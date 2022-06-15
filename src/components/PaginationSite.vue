<template>
  <div class="pagination">
    <button 
      class="pagination-button" 
      btn_type="back" 
      @click="changeNumberPage"
    >
      &lsaquo;
    </button>
    <p>{{ page }}</p>
    <button 
      class="pagination-button" 
      btn_type="next" 
      @click="changeNumberPage"
    >
      &rsaquo;
    </button>
  </div>
</template>

<script>
  export default {
    props: {
      page: Number,
      changeNumberPage: Function,
    },
  }
</script>

<style>
  .pagination {
    display: flex;
    width: fit-content;
    gap: 30px;
    margin: 30px auto;
    align-items: center;
  }

  .pagination-button {
    padding: 10px;
    font-size: 30px;
    background-color: transparent;
    cursor: pointer;
    border: none;
    transition: all 0.5s;
  }

  .pagination-button:hover {
    color: goldenrod;
  }

  .pagination p {
    font-size: 30px;
  }

</style>