# 移动端响应式布局实践：基于 rem 的适配方案

## 一、背景介绍

在移动端开发中，我们经常需要面对不同尺寸的设备屏幕。为了让页面在各种设备上都能呈现出良好的显示效果，响应式布局变得尤为重要。本文将基于一个实际项目中的 `flexible.ts` 代码，详细介绍一种基于 rem 的响应式布局方案。

## 二、核心实现原理

### 2.1 基准设定

```typescript
const UI_WIDTH = 375;  // 设计稿基准宽度
const baseFontSize = 100;  // 基准字体大小
```

这里设定了两个重要的基准值：
- 设计稿基准宽度
- 基准字体大小：采用 100px 作为基准值，便于计算

### 2.2 响应式计算核心

```typescript
const MAX_WIDTH = 750;

function setRootFontSize() {
  const width = document.documentElement.clientWidth;
  const maxWidth = Math.min(parseFloat(width.toString()), MAX_WIDTH);
  const fontSize = ((maxWidth / UI_WIDTH) * baseFontSize).toFixed(4);
  document.documentElement.style.fontSize = fontSize + 'px';
}
```

核心计算逻辑：
1. 获取当前设备宽度
2. 限制最大宽度为 750px，避免在大屏设备上过度放大
3. 根据当前宽度与设计稿宽度的比例，计算根元素字体大小
4. 设置根元素字体大小，作为 rem 的基准值

### 2.4 设备兼容性处理

```typescript
const nowFontSize = parseFloat(getComputedStyle(document.documentElement, false)['font-size']);
if (nowFontSize + '' !== fontSize) {
  const fixedFontSize = (fontSize * fontSize) / nowFontSize;
  document.documentElement.style.fontSize = fixedFontSize + 'px';
}
```

针对某些设备对字体大小进行额外处理的情况，增加了修正逻辑，确保实际效果与预期一致。

## 三、工具函数

### 3.1 px 转 rem

```typescript
export function px2rem(px: number) {
  return round(px / baseFontSize, 4);
}
```

将设计稿中的 px 值转换为 rem 值，便于在样式中使用。

### 3.2 px 转实际像素

```typescript
export function px2px(px: number) {
  return round(px2rem(px) * parseFloat(window.__ROOT_FONT_SIZE__).toFixed(4), 1);
}
```

将设计稿中的 px 值转换为实际显示的像素值，用于特殊场景下的精确计算。

### 3.3 等比例转换

```typescript
export function pxTransform(size: number) {
  const result = size * (DeviceWidth / UI_WIDTH);
  return round(result);
}
```

根据当前设备宽度进行等比例转换，保持与设计稿的比例一致。

## 四、实践建议

1. **初始化时机**
   ```typescript
   export const flexible = () => {
     setRootFontSize();
     window.addEventListener('resize', handler);
     setTimeout(setRootFontSize, 1000);
   };
   ```
   - 页面加载时初始化
   - 监听 resize 事件，使用防抖处理
   - 延迟重新计算，处理某些设备的初始化问题

2. **CSS 变量的使用**
   ```typescript
   document.documentElement.style.setProperty('--gpx', '0.01rem');
   ```
   设置全局 CSS 变量，方便在样式中使用响应式单位

3. **最大宽度限制**
   ```typescript
   const maxWidth = Math.min(parseFloat(width.toString()), 750);
   ```
   设置最大宽度限制，避免在平板等大屏设备上页面过度放大

## 五、总结

这套响应式布局方案的优点：
1. 实现简单，容易理解和维护
2. 兼容性好，处理了各种设备适配问题
3. 提供了完整的工具函数，方便开发使用
4. 性能优化，使用防抖处理 resize 事件

建议在实际项目中：
1. 根据设计稿尺寸调整基准宽度
2. 合理使用工具函数进行单位转换
3. 注意设置合适的最大宽度限制
4. 在必要时增加设备特殊适配

通过这套方案，我们可以更好地处理移动端的响应式布局问题，提供更好的用户体验。