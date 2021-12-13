<template>
  <div class="about">
    <h1>This is an about page</h1>
    <div class="container">
      <h1 class="title">Polygons</h1>
      <ul class="list">
        <li class="lists--odd">
          <h2>3</h2>
          <div class="tryangle">
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
          </div>
        </li>
        <li class="lists">
          <h2>4</h2>
          <div class="square">
            <div class="rect"></div>
            <div class="rect"></div>
          </div>
        </li>
        <li class="lists--odd">
          <h2>5</h2>
          <div class="pentagon">
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
          </div>
        </li>
        <li class="lists">
          <h2>6</h2>
          <div class="hexagon">
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
          </div>
        </li>
        <li class="lists">
          <h2>8</h2>
          <div class="octagon">
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
          </div>
        </li>
        <li class="lists--odd">
          <h2>9</h2>
          <div class="nonagon">
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
          </div>
        </li>
        <li class="lists">
          <h2>10</h2>
          <div class="decagon">
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
          </div>
        </li>
        <li class="lists">
          <h2>12</h2>
          <div class="dodecagon">
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
            <div class="rect"></div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>
<style lang="scss">
@import url(https://fonts.googleapis.com/css?family=Quattrocento+Sans);

$size: 200px;
$bk-color: #f5f5f5;
$border-color: #333;
$rect-even-color: #fff;
$rect-odd-color: transparent;
$text-even-color: #fff;
$text-odd-color: #000;

@mixin center {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  margin: auto;
}

@mixin center-tf {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);
}

@mixin border {
  box-sizing: border-box;
  border: 1px solid $border-color;
  border-left: none;
  border-right: none;
}

body {
  width: 100%;
  height: 100%;
  background: $bk-color;
}

.container {
  position: relative;
  width: 100%;
  height: 100%;
  min-height: 100vh;
  margin: 0 auto;
  .title {
    position: relative;
    box-sizing: border-box;
    width: 200px;
    height: 40px;
    line-height: 40px;
    margin: 40px auto 0;
    padding-left: 10px;
    font-size: 11px;
    text-transform: uppercase;
    text-align: center;
    letter-spacing: 10px;
    font-family: 'Quattrocento Sans';
    &:before,
    &:after {
      content: '';
      position: absolute;
      top: calc(50% - 1px);
      width: 10px;
      height: 1px;
      background: #333;
    }
    &:before {
      left: 0;
    }
    &:after {
      right: 0;
    }
  }
}

.list {
  text-align: center;
}

.lists,
.lists--odd {
  position: relative;
  display: inline-block;
  width: 10%;
  min-width: $size * 1.5;
  height: $size * 1.5;
  h2 {
    font-size: 12px;
    color: $text-even-color;
    font-family: 'Quattrocento Sans';
    z-index: 1;
    @include center-tf;
  }
}

.lists--odd {
  h2 {
    color: $text-odd-color;
  }
}
    
.tryangle,
.square,
.pentagon,
.hexagon,
.dodecagon,
.octagon,
.nonagon,
.decagon,
.pentadecagon,
.hexadecagon,
.octadecagon,
.icosagon {
  width: $size;
  height: $size;
  @include center;
  .rect {
    background: $rect-even-color;
    transform-origin: center center;
    @include border;
    @include center;
  }
}

.lists--odd {
  .rect {
    background: $rect-odd-color;
  }
}
    

.tryangle{
    border:solid 1px black;
    border-radius:100%;
  .rect {
    width: 86.60254037844386%;// r(√3)
    height: 50%;
    border-top: none;
    @for $i from 0 through 2 {
      &:nth-of-type(#{$i + 1}) {
        transform: rotate(120deg * $i);
      }
    }
  }
}
    
.square{
    border:solid 1px black;
    border-radius:100%;
  .rect {
    width: 70.710678118655%;// √(5-2√5)
    height: 70.710678118655%;
    border:solid 1px black;
    transform: rotate(45deg);
  }
}
    
.pentagon {
    border:solid 1px black;
    border-radius:100%;
  .rect {
    width: 58.778525229247%;// √(5-2√5)
    height: 80.901699437495%;
    border-top: none;
    @for $i from 0 through 4 {
      &:nth-of-type(#{$i + 1}) {
        transform: rotate(72deg * $i);
      }
    }
  }
}

.hexagon {
    border:solid 1px black;
    border-radius:100%;
  .rect {
    width: 50%;// (50/√3)*2
    height: 86.602540378444%;
    @for $i from 0 through 2 {
      &:nth-of-type(#{$i + 1}) {
        transform: rotate(60deg * $i);
      }
    }
  }
}

.octagon {
  .rect {
    width: 41.421356237%;// √2-1
    height: 100%;
    @for $i from 0 through 3 {
      &:nth-of-type(#{$i + 1}) {
        transform: rotate(45deg * $i);
      }
    }
  }
}

.nonagon {
    border:solid 1px black;
    border-radius:100%;
  .rect {
    width: 36.397023426%;// tan(π/9)
    height: 100%;
    border-top: none;
    @for $i from 0 through 8 {
      &:nth-of-type(#{$i + 1}) {
        transform: rotate(40deg * $i);
      }
    }
  }
}

.decagon {
  .rect {
    width: 32.491969623%;// √(25-10√5)/5
    height: 100%;
    @for $i from 0 through 4 {
      &:nth-of-type(#{$i + 1}) {
        transform: rotate(36deg * $i);
      }
    }
  }
}

.dodecagon {
  .rect {
    width: 26.794919243%;// 2-√3 円に外接する正多角形
    height: 100%;
    @for $i from 0 through 5 {
      &:nth-of-type(#{$i + 1}) {
        transform: rotate(30deg * $i);
      }
    }
  }
}

.pentadecagon {
  .rect {
    width: 21.255656167%;// (-√(50-22√5)-√15+3√3)/2
    height: 100%;
    border-top: none;
    @for $i from 0 through 14 {
      &:nth-of-type(#{$i + 1}) {
        transform: rotate(24deg * $i);
      }
    }
  }
}

.hexadecagon {
  .rect {
    width: 19.891236738%;// tan(π/16)
    height: 100%;
    @for $i from 0 through 7 {
      &:nth-of-type(#{$i + 1}) {
        transform: rotate(22.5deg * $i);
      }
    }
  }
}

.octadecagon {
  .rect {
    width: 17.63269807%;// tan(π/18)
    height: 100%;
    @for $i from 0 through 8 {
      &:nth-of-type(#{$i + 1}) {
        transform: rotate(20deg * $i);
      }
    }
  }
}

.icosagon {
  .rect {
    width: 15.838444032%;// -√(5+2√5)+√5+1
    height: 100%;
    @for $i from 0 through 9 {
      &:nth-of-type(#{$i + 1}) {
        transform: rotate(18deg * $i);
      }
    }
  }
}


</style>