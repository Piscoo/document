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

2. 渐变文字加阴影

原因： text-shadow 和 -webkit-text-fill-color、-webkit-background-clip会有冲突

```
<h1 class="title" :data-content="title">{{title}}</h1>

// css
.title {
	color: transparent;
	text-shadow: 0px 1px 0px rgba(255, 255, 255, 0.6), 0px 22px 18px rgba(182, 157, 255, 0.4);
	position: relative;
	&::after {
		content: attr(data-content);
		display: block;
		position: absolute;
		color: #fff;
		left: 0;
		top: 0;
		z-index: 2;
		letter-spacing: 6px;
		font-weight: 400;
		white-space: nowrap;
		background: linear-gradient(90deg, #4884FF 0%, #E7A0FE 100%);
		-webkit-background-clip: text;
		-webkit-text-fill-color: transparent;
		text-shadow: none;
	}
}
```