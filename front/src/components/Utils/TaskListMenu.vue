<template>
  <div class="text-h6">
    <q-btn-dropdown stretch flat round>
      <template #label>
        <div class="row q-gutter-md items-center">
          <div>{{ title }}</div>
          <div class="col">
            <q-badge v-if="task" :style="colorHash(task.category)">
              {{ task.category }}
            </q-badge>
          </div>
        </div>
      </template>
      <template #default>
        <q-list>
          <q-item
            v-for="t of sortedTasks"
            :key="t.nodeId"
            v-close-popup
            clickable
            :to="taskLink(t)"
          >
            <q-item-section>
              <q-item-label>
                <div class="row" style="max-width: 200px">
                  <div class="col">
                    <div class="ellipsis">
                      {{ t.title }}
                    </div>
                  </div>
                  <div v-show="t.solved" class="col col-auto q-ml-xs">
                    <q-badge icon="flag" color="green" rounded>
                      <q-icon name="flag" />
                    </q-badge>
                  </div>
                </div>
              </q-item-label>
            </q-item-section>
            <q-item-section side>
              <q-chip
                class="text-white"
                style="max-width: 90px"
                :style="colorHash(t.category)"
              >
                <div class="ellipsis">
                  {{ t.category }}
                </div>
              </q-chip>
            </q-item-section>
          </q-item>
        </q-list>
      </template>
    </q-btn-dropdown>
  </div>
</template>

<script lang="ts">
import { Ctf, Task } from 'src/ctfnote/models';
import ctfnote from 'src/ctfnote';
import { defineComponent } from 'vue';

export default defineComponent({
  components: {},
  props: {
    ctf: { type: Object as () => Ctf, required: true },
    taskId: { type: Number, default: null },
  },
  computed: {
    task() {
      return this.ctf.tasks.find((t) => t.id == this.taskId);
    },
    title() {
      if (this.task) {
        return this.task.title;
      }
      return 'Open task';
    },
    sortedTasks() {
      return this.ctf.tasks
        .slice()
        .sort((a, b) => {
          const acat = (a.category ?? '').toLowerCase();
          const bcat = (b.category ?? '').toLowerCase();
          if (acat == bcat) {
            const atitle = a.title.toLowerCase();
            const btitle = a.title.toLowerCase();
            return atitle == btitle ? 0 : atitle < btitle ? -1 : 1;
          }
          return acat < bcat ? -1 : 1;
        })
        .sort((a) => {
          return a.solved ? 1 : -1;
        });
    },
  },
  methods: {
    taskLink(task: Task | null = null) {
      if (!task) {
        return null;
      }
      return {
        name: 'task',
        params: {
          ctfId: this.ctf.id,
          ctfSlug: this.ctf.slug,
          taskId: task.id,
          taskSlug: task.slug,
        },
      };
    },

    colorHash(s: string) {
      return { backgroundColor: ctfnote.utils.colorHash(s) };
    },
  },
});
</script>

<style scoped></style>
