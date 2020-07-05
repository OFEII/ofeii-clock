<template>
  <div id="app">
    <router-view  />
  </div>
</template>

<style lang="scss" >
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  /* fixed blue rect in android */
  body {
    background-color: #f5f6f7;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 1rem 3rem;
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
  }

  /* fixed blue rect in ios/wechat  */
  a:focus,
  input:focus,
  p:focus,
  div:focus {
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
    -webkit-user-modify: read-write-plaintext-only;
  }

</style>
