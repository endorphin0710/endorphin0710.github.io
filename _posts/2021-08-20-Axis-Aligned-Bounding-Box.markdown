---
layout: post
title:  "AABB(Axis Aligned Bounding Box)"
date:   2021-08-20 02:16:00 +0900
categories: algorithm
---

AABB는 Axis-Aligned Bounding Box의 줄임말로, 객체 간의 충돌 여부를 탐지하기 위해 사용되는 알고리즘입니다.<br/>
Axis-Aligned 된 환경에서의 충돌 탐지가 빈번하게 필요한 게임 개발을 할 때 자주 사용되고 있습니다.


Box가 Axis-Aligned 되었다는 뜻은 회전하지 않고 물체가 놓여진 축에 일렬로 고정되어 움직인다고 이해하면 될 것 같습니다.
* 2차원의 경우 회전 없이 X축과 Y축 이동
* 3차원의 경우 회전 없이 X, Y, Z축 이동 

AABB는 2차원과 3차원에서 모두 사용할 수 있으며 이 글은 2차원에 한정하여 정리해보려 합니다.<br/>
2차원의 경우 원과 직사각형을 이용하여 충돌 여부를 탐지합니다.<br/>
간단한 게임의 경우 원과 직사각형을 통해서 만족할만한 수준의 충돌 여부를 감지할 수 있게 됩니다.<br/>


<img src="https://i.ytimg.com/vi/qlMJLH-GQU4/maxresdefault.jpg"
  width="500" height="250"/>

<h3>구현</h3>
<h4>직사각형</h4>
직사각형 객체끼리의 충돌 감지는 각 면 사이의 공간이 있는지 체크하는 방식으로 이루어집니다.<br/>
한 군데라도 공간이 있게 된다면 충돌이 없는 것으로 보면 됩니다.

{% highlight javascript %}
var rect1 = {x: 0, y: 0, width: 50, height: 50}
var rect2 = {x: 20, y: 20, width: 10, height: 10}

if (rect1.x < rect2.x + rect2.width &&
   rect1.x + rect1.width > rect2.x &&
   rect1.y < rect2.y + rect2.height &&
   rect1.y + rect1.height > rect2.y) {
    // collision detected!
}else{
  //no collision detected!
}
{% endhighlight %}



<canvas id="canvasRect" width="300" height="300" style="border: thin solid crimson; background-color: white;"></canvas>
방향키를 이용해 사각형을 움직여보세요. 충돌이 감지될 경우 색이 변경됩니다.

<h4>원형</h4>
원형 객체끼리의 충돌 감지는 중심과 반지름을 이용해 거리를 계산합니다.<br/>
중심끼리의 거리가 반지름의 합보다 작다면 충돌한 것으로 확인합니다.

{% highlight javascript %}
var circle1 = {radius: 20, x: 5, y: 5};
var circle2 = {radius: 12, x: 10, y: 5};

var dx = circle1.x - circle2.x;
var dy = circle1.y - circle2.y;
var distance = Math.sqrt(dx * dx + dy * dy);

if (distance < circle1.radius + circle2.radius) {
    // collision detected!
}else{
  //no collision detected!
}
{% endhighlight %}

<canvas id="canvasCircle" width="300" height="300" style="border: thin solid crimson; background-color: white;"></canvas>
방향키를 이용해 원을 움직여보세요. 충돌이 감지될 경우 색이 변경됩니다.

<br/><br/><br/>
이 글은 mozilla docs를 참고하여 작성하였습니다.<br/>
<a href="https://developer.mozilla.org/en-US/docs/Games/Techniques/2D_collision_detection">https://developer.mozilla.org/en-US/docs/Games/Techniques/2D_collision_detection</a>

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/

<script>
  var me = {
    x: 0,
    y: 0,
    width: 40,
    height: 40,
    speed: 20,
    collide: false,
    color: 'green'
  };

  var obj = {
    x: 120,
    y: 120,
    width: 80,
    height: 80,
    color: 'royalblue'
  };

  var obj2 = {
    x: 260,
    y: 260,
    width: 25,
    height: 50,
    color: 'pink'
  };

  var obj3 = {
    x: 0,
    y: 50,
    width: 260,
    height: 20,
    color: 'yellow'
  };

  var me2 = {
    x: 100,
    y: 100,
    radius: 30,
    speed: 20,
    collide: false,
    color: 'green'
  };

  var circle = {
    x: 160,
    y: 100,
    radius: 40,
    color: 'black'
  };

  var circle2 = {
    x: 220,
    y: 220,
    radius: 20,
    color: 'orange'
  };

  var circle3 = {
    x: 30,
    y: 160,
    radius: 30,
    color: 'blue'
  };

  var objs = [obj, obj2, obj3];
  var circles = [circle, circle2, circle3];

  const canvasRect = document.getElementById('canvasRect');
  const canvasCircle = document.getElementById('canvasCircle');
  const ctxRect = canvasRect.getContext('2d');
  const ctxCircle = canvasCircle.getContext('2d');
  detectRectCollision()
  detectCircleCollision()

  window.addEventListener("keydown", move, false);
  function move(e){
    e.preventDefault();
    switch(e.keyCode){
      case 37:
        if(me.x-me.speed >= 0){
          ctxRect.clearRect(me.x, me.y, me.width, me.height);
          me.x -= me.speed;
          detectRectCollision();
        }else{
          ctxRect.clearRect(me.x, me.y, me.width, me.height);
          me.x = 0;
          detectRectCollision();
        }

        if(me2.x-me2.radius-me2.speed >= 0){
          me2.x -= me2.speed;
          detectCircleCollision()
        }else{
          me2.x = me2.radius;
          detectCircleCollision()
        }
        break;
      case 38:
        if(me.y-me.speed >= 0){
          ctxRect.clearRect(me.x, me.y, me.width, me.height);
          me.y -= me.speed;
          detectRectCollision();
        }else{
          ctxRect.clearRect(me.x, me.y, me.width, me.height);
          me.y = 0;
          detectRectCollision();
        }

        if(me2.y-me2.radius-me2.speed >= 0){
          me2.y -= me2.speed;
          detectCircleCollision()
        }else{
          me2.y = me2.radius;
          detectCircleCollision()
        }
        break;
      case 39:
        if(me.x+me.width+me.speed <= canvasRect.width){
          ctxRect.clearRect(me.x, me.y, me.width, me.height);
          me.x += me.speed;
          detectRectCollision();
        }else{
          ctxRect.clearRect(me.x, me.y, me.width, me.height);
          me.x = canvasRect.width-me.width;
          detectRectCollision();
        }

        if(me2.x+me2.radius+me2.speed <= canvasCircle.width){
          me2.x += me2.speed;
          detectCircleCollision()
        }else{
          me2.x = canvasCircle.width-me2.radius;
          detectCircleCollision()
        }
        break;
      case 40:
        if(me.y+me.height+me.speed <= canvasRect.height){
          ctxRect.clearRect(me.x, me.y, me.width, me.height);
          me.y += me.speed;
          detectRectCollision();
        }else{
          ctxRect.clearRect(me.x, me.y, me.width, me.height);
          me.y = canvasRect.height-me.height;
          detectRectCollision();
        }

        if(me2.y+me2.radius+me2.speed <= canvasCircle.height){
          me2.y += me2.speed;
          detectCircleCollision()
        }else{
          me2.y = canvasCircle.height-me2.radius;
          detectCircleCollision()
        }
        break;
    }
  };

  function detectRectCollision(){
    var collide = false;
    for(var i = 0; i < objs.length; i++){
      var you = objs[i];
      if (me.x < you.x + you.width &&
          me.x + me.width > you.x &&
          me.y < you.y + you.height &&
          me.y + me.height > you.y) {
            collide = true;
            break;
      }
    }

    if(collide){
      me.collide = true;
      me.color = 'crimson';
    }else{
      me.collide = false;
      me.color = 'green';
    }
    drawRects();
  }

  function detectCircleCollision(){
    var collide = false;
    for(var i = 0; i < circles.length; i++){
      var you = circles[i];
      var dx = me2.x - you.x;
      var dy = me2.y - you.y;
      var distance = Math.sqrt(dx * dx + dy * dy);

      if (distance < me2.radius + you.radius) {
        collide = true;
            break;
      }
    }

    if(collide){
      me2.collide = true;
      me2.color = 'crimson';
    }else{
      me2.collide = false;
      me2.color = 'green';
    }
    drawCircles();
  }

  function drawRectangle(me){
    ctxRect.fillStyle = me.color;
    ctxRect.fillRect(me.x, me.y, me.width, me.height);
  }

  function drawCircle(me){
    ctxCircle.clearRect(me.x, me.y, me.width, me.height);
    ctxCircle.fillStyle = me.color;
    ctxCircle.beginPath();
    ctxCircle.arc(me.x, me.y, me.radius, 0, 2 * Math.PI);
    ctxCircle.fill();
  }

  function drawRects(){
    drawRectangle(obj);
    drawRectangle(obj2);
    drawRectangle(obj3);
    drawRectangle(me);
  }

  function drawCircles(){
    ctxCircle.clearRect(0, 0, canvasCircle.width, canvasCircle.height);
    drawCircle(circle);
    drawCircle(circle2);
    drawCircle(circle3);
    drawCircle(me2);
  }

</script>
