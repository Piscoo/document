# 一些实用的CSS技巧

1. 折角

- 关键实现：线性渐变
```
div {
  position: relative;
  width: 200px;
  height: 100px;
  background: linear-gradient(-150deg, transparent 1.5em, cyan 0);
  border-radius: .5em;
}
div:after {
  content: '';
  position: absolute;
  right: 0;
  width: 1.73em;
  height: 3em;
  background: linear-gradient(to left bottom, transparent 50%, red 0);
  transform: translateY(-1.3em) rotate(-30deg);
  transform-origin: bottom right;
  border-bottom-left-radius: inherit; 
}
```