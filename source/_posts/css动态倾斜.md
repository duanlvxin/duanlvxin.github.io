---
title: css动态倾斜
date: 2026-07-18 22:10:09
tags:
  - css
categories:
  - css
---


<div class="arknights-demo-container">
<div class="arknights-demo">
  <div class="whitebg"></div>
  <div class="charimgwrapper">
      <div class="circlepoint"></div>
  </div>
  <div class="charimgwrapper" style="overflow: inherit;">
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<div class="charimg" style="left: 0px; top: 0px; filter: none;"><img loading="lazy" src="https://media.prts.wiki/a/a6/%E5%8D%8A%E8%BA%AB%E5%83%8F_%E7%BB%B4%E4%BB%80%E6%88%B4%E5%B0%94_skin2.png?v=2qajwssxtkj4hhqfwfshondld2uet3c" width="120px"></div>
  </div>
  <div class="skinlogowrapper" style="overflow: hidden; left: 0px; top: 0px; transform: translateZ(0px) scale(1); filter: none;">
      <div class="skinlogo" style="left: 0px; top: 0px;">
              <div class="logo-game"><img src="https://media.prts.wiki/d/d7/Skin_brand_%E6%88%90%E5%B0%B1%E4%B9%8B%E6%98%9F.png?v=mllhe8qwa191viunsp9jriofj54le84" width="100%"></div>
      </div>
  </div>
&nbsp; &nbsp; &nbsp; &nbsp;<div class="skintag" style="">特典获得</div>
  <div class="charnameEn">绝对主角</div>
  <div class="skinname">ACHIEVEMENT STAR</div> 
</div>
</div>


<style>
.arknights-demo-container {
  position: relative;
}
.arknights-demo {
  width: 120px;
  height: 276px;
  background-color: white;
  position: relative;
  margin: 0px;
  font-size: 0px;
  display: inline-block;
  z-index: 0;
  filter: drop-shadow(0px 5px 5px rgba(0, 0, 0, .5));
  cursor: pointer;
  transition: all .2s ease;
  margin-bottom: 16px;
}
.arknights-demo .whitebg {
  position: absolute;
  top: 0px;
  left: 0px;
  background-color: white;
  width: 120px;
  height: 240px;
  z-index: 0;
}
.arknights-demo .charimgwrapper {
  position: absolute;
  bottom: 36px;
  left: 0px;
  width: 120px;
  height: 240px;
  z-index: 1;
  overflow: hidden;
}
.arknights-demo .skinlogowrapper {
  position: absolute;
  top: 0px;
  width: 120px;
  height: 240px;
  overflow: hidden;
  z-index: 0;
  opacity: 1;
  transition: all .4s ease;
  transform-origin: left top;
}
.arknights-demo .skintag {
  width: 66px;
  height: 20px;
  background-color: #0224ff;
  position: absolute;
  top: 5px;
  right: -7px;
  color: white;
  font-size: 12px;
  line-height: 20px;
  text-align: center;
  box-sizing: border-box;
  z-index: 2;
}
.arknights-demo .charnameEn {
  position: absolute;
  top: 244px;
  left: 4px;
  color: #5b5b5b;
  font-weight: bold;
  font-size: 13px;
  line-height: 12px;
}
.arknights-demo .skinname {
  position: absolute;
  bottom: 4px;
  left: 4px;
  color: #9d9d9d;
  font-size: 16px;
  line-height: 12px;
  white-space: nowrap;
  font-weight: bold;
  transform: scale(.5);
  transform-origin: left;
}
</style>

<script>
  const container = document.querySelector('.arknights-demo');
  container.addEventListener('mouseenter', (e) => {
    // 如果是在左下
    if(e.offsetX < container.offsetWidth / 2 && e.offsetY > container.offsetHeight / 2) {
      container.style = 'transform: translateZ(0px) perspective(1000px) rotateY(-20deg) rotateX(-13.3333deg); filter: brightness(0.95); box-shadow: rgba(0, 0, 20, 0.25) 20px -20px 10px 0px;';
    }
    // 如果是右下角
    if(e.offsetX > container.offsetWidth / 2 && e.offsetY > container.offsetHeight / 2) {
      container.style = 'transform: translateZ(0px) perspective(1000px) rotateY(20deg) rotateX(-13.3333deg); filter: brightness(0.95); box-shadow: rgba(0, 0, 20, 0.25) -20px -20px 10px 0px;'
    }
    // 如果是右上角
    if(e.offsetX > container.offsetWidth / 2 && e.offsetY < container.offsetHeight / 2) {
      container.style = 'transform: translateZ(0px) perspective(1000px) rotateY(20deg) rotateX(13.3333deg); filter: brightness(1.05); box-shadow: rgba(0, 0, 20, 0.25) -20px 20px 10px 0px;'
    }
    // 如果是左上角
    if(e.offsetX < container.offsetWidth / 2 && e.offsetY < container.offsetHeight / 2) {
      container.style = 'transform: translateZ(0px) perspective(1000px) rotateY(-20deg) rotateX(13.3333deg); filter: brightness(1.05); box-shadow: rgba(0, 0, 20, 0.25) 20px 20px 10px 0px;'
    }
  });
  container.addEventListener('mouseleave', () => {
    container.style = '';
  });
</script>