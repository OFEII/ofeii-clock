<template>
  <div id="app">
    <!-- <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div> -->
    <router-view/>
  </div>
</template>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body{
  background-color: #f5f6f7;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 1rem 3rem;
  //fixed blue rect in android
  -webkit-tap-highlight-color:rgba(0,0,0,0); 
}

//fixed blue rect in ios/wechat 
a:focus,
input:focus,
p:focus,
div:focus{
-webkit-tap-highlight-color:rgba(0,0,0,0);
-webkit-user-modify:read-write-plaintext-only;
}
</style>
