<template>
  <col :style="style" />
</template>

<script>
export default {
  name: 'DraggableTreeTableColumn',
  props: {
    width: Number,
    align: String,
  },
  computed: {
    style() {
      const { width, align } = this;

      return {
        width: width ? width + 'px' : undefined,
        textAlign: align ? align : 'center',
      };
    },
  },
};
</script>

<style></style>
