<template>
  <modal
    :name="name"
    :resizable="true"
    width="80%"
    height="80%"
    @closed="modalClosed()"
    classes="dashy-modal"
    :style="{ left: modalLeft + 'px', top: modalTop + 'px' }"
  >
    <div
      slot="top-right"
      @click="hide()"
      @mousedown="startDrag"
      class="drag-handle"
    >
      Close
    </div>
    <a @click="hide()" class="close-button" title="Close">x</a>
    <iframe
      v-if="url"
      :src="url"
      @keydown.esc="close"
      class="frame"
      allow="fullscreen; clipboard-write"
    />
    <div v-else class="no-url">No URL Specified</div>
  </modal>
</template>

<script>
import Keys from '@/utils/StoreMutations';

export default {
  name: 'IframeModal',
  props: {
    name: String,
  },
  data() {
    return {
      url: '#',
      isDragging: false,
      modalLeft: 0,
      modalTop: 0,
      initialX: 0,
      initialY: 0,
      xOffset: 0,
      yOffset: 0,
    };
  },
  methods: {
    show(url) {
      this.url = url;
      this.$modal.show(this.name);
      this.$store.commit(Keys.SET_MODAL_OPEN, true);
    },
    hide() {
      this.$modal.hide(this.name);
    },
    modalClosed() {
      this.$store.commit(Keys.SET_MODAL_OPEN, false);
    },
    startDrag(event) {
      this.initialX = event.clientX - this.modalLeft;
      this.initialY = event.clientY - this.modalTop;
      this.isDragging = true;
      document.addEventListener('mousemove', this.dragModal);
      document.addEventListener('mouseup', this.stopDrag);
    },
    dragModal(event) {
      if (this.isDragging) {
        event.preventDefault();
        this.modalLeft = event.clientX - this.initialX;
        this.modalTop = event.clientY - this.initialY;
      }
    },
    stopDrag() {
      this.isDragging = false;
      document.removeEventListener('mousemove', this.dragModal);
      document.removeEventListener('mouseup', this.stopDrag);
    },
  },
};
</script>

<style lang="scss">
.frame {
  width: 100%;
  height: 100%;
  border: none;
}

.no-url {
  margin: 70rem auto;
  width: fit-content;
  font-size: 2rem;
  padding: 0.5rem;
  border: 1px dashed #ff0000;
  border-radius: 3px;
  background: #f4f2f2;
}

.close-button {
  position: absolute;
  right: 0;
  padding: 0.5rem;
  border: 0;
  border-radius: 0 0 0 10px;
  background: var(--primary);
  color: var(--background);
  border-left: 1px solid var(--primary);
  border-bottom: 1px solid var(--primary);
  cursor: pointer;
  &:hover {
    background: var(--background);
    color: var(--primary);
  }
}

.drag-handle {
  cursor: move;
  background-color: #ccc; /* Asegúrate de que sea visible */
  padding: 5px;
}
</style>
