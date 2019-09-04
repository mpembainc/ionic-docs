---
previousText: ''
previousUrl: ''
nextText: 'Config'
nextUrl: '/docs/utilities/config'
---

# Animations

## Overview

Ionic Animations is a utility that allows developers to build complex animations in a platform agnostic manner. Developers do not need to be using a particular framework such as React or Angular, nor do they even need to be building an Ionic app! As long as developers include `@ionic/core` in their app's `package.json` file, they should have access to all of Ionic Animations.

Building efficient animations can be tricky. Developers are often limited by the libraries available to them as well as the hardware that their apps run on. On top of that, many animation libraries use a JavaScript-driven approach to running animations where they handle the calculation of your animation's values at every step in a `requestAnimationFrame` loop. This reduces the scalability of your animations as the library is constantly computing values and using up CPU time.

Ionic Animations uses the [Web Animations API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API) to build and run your animations. In doing this, we offload all work required to compute and run your animations to the browser. As a result, this allows the browser to make any optimizations it needs and ensures your animations run as smoothly as possible. While most browsers support a basic implementation of Web Animations, we fallback to [CSS Animations](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations/Using_CSS_animations) for browsers that do not support Web Animations. The performance difference in switching between these two should typically be negligible.

## Basic Animations

### Usage

```javascript
createAnimation()
  .addElement(document.querySelector('.square'))
  .duration(3000)
  .easing('linear')
  .fill('both')
  .iterations(Infinity)
  .fromTo('transform', 'translateX(100px)')
  .fromTo('opacity', 1, 0.5);
```

In the example above, an animation that changes the transform and opacity on the `.square` element has been created.

CODEPEN WITH LIVE DEMO SHOULD GO HERE

## Keyframe Animations

Ionic Animations allow you to control the intermediate steps in an animation using keyframes. Any valid CSS property can be used here, and you can even use CSS Variables as values!

### Usage

```javascript
createAnimation()
  .addElement(document.querySelector('.square'))
  .duration(3000)
  .easing('linear')
  .fill('both')
  .iterations(Infinity)
  .keyframes([
    { offset: 0, background: 'red' },
    { offset: 0.72, background: 'var(--background)' },
    { offset: 1, background: 'green' }
  ]);
```

In the example above, the `.square` element will transition from a red background color, to a background color defined by the `--background` variable, and then transition on a green background color.

Each keyframe object contains an `offset` property. `offset` is a value between 0 and 1 that defined the keyframe step.

CODEPEN WITH LIVE DEMO SHOULD GO HERE

## Grouped Animations

Multiple elements can be animated at the same time and controlled via a single parent animation object. Child animations inherit properties such as duration, easing, or iterations unless otherwise specified. A parent animation's `onFinish` callback will not be called until all child animations have completed.

### Usage

```javascript
const squareA = createAnimation()
  .addElement(document.querySelector('.square-a'))
  .keyframes([
    { offset: 0, transform: 'scale(1) rotate(0)' },
    { offset: 0.5, transform: 'scale(1.2) rotate(45deg)' },
    { offset: 1, transform: 'scale(1), rotate(45deg) '}
  ]);
  
const squareB = createAnimation()
  .addElement(document.querySelector('.square-b'))
  .keyframes([
    { offset: 0, transform: 'scale(1))', opacity: 1 },
    { offset: 0.5, transform: 'scale(1.2)', opacity: 0.3 },
    { offset: 1, transform: 'scale(1)', opacity: 1 }
  ]);
  
const squareC = createAnimation()
  .addElement(document.querySelector('.square-c'))
  .duration(5000)
  .keyframes([
    { offset: 0, transform: 'scale(1))', opacity: 0.5 },
    { offset: 0.5, transform: 'scale(0.8)', opacity: 1 },
    { offset: 1, transform: 'scale(1)', opacity: 0.5 }
  ]);

const parent = createAnimation()
  .duration(2000)
  .iterations(Infinity)
  .easing('linear')
  .addAnimation([squareA, squareB, squareC])
  .play();
```

This example shows 3 child animations controlled by a single parent animation. Animations `squareA` and `squareB` inherit the parent animation's duration of 2000ms, but animation `squareC` has a duration of 5000ms since it was explicitly set.

CODEPEN WITH LIVE DEMO SHOULD GO HERE

## Before and After Hooks

Ionic Animations provides hooks that let you alter an element before and animation runs and after an animation completes. These hooks can be used to perform DOM reads and writes and add or removes classes or inline styles.

### Usage

```javascript
createAnimation()
  .addElement(document.querySelector('.square'))
  .duration(2000)
  .easing('linear')
  .beforeStyles({
    opacity: 0.2
  })
  .afterStyles({
    background: 'rgba(0, 255, 0, 0.5)'
  })
  .afterClearStyles(['opacity'])
  .keyframes([
    { offset: 0, transform: 'scale(1)' },
    { offset: 0.5, transform: 'scale(1.5)' },
    { offset: 1, transform: 'scale(1)' }
  ])
```

In this example, an inline opacity of 0.2 is set on the `.square` element prior to the animation starting. Once the animation finishes, the background color of the element is set to `rgba(0, 255, 0, 0.5)` and the inline opacity is cleared.

See [Methods](#methods) for a complete list of hooks.

CODEPEN WITH LIVE DEMO SHOULD GO HERE

## Chained Animations

Animations can be chained to run one after the other. The recommended approach is to use the `playAsync` method, but chaining via an `onFinish` callback can be used as well.

### Usage

```javascript
const squareA = createAnimation()
  .addElement(document.querySelector('.square-a'))
  .duration(1000)
  .keyframes([
    { offset: 0, transform: 'scale(1) rotate(0)' },
    { offset: 0.5, transform: 'scale(1.2) rotate(45deg)' },
    { offset: 1, transform: 'scale(1) rotate(0) '}
  ]);
  
const squareB = createAnimation()
  .addElement(document.querySelector('.square-b'))
  .duration(1000)
  .keyframes([
    { offset: 0, transform: 'scale(1)', opacity: 1 },
    { offset: 0.5, transform: 'scale(1.2)', opacity: 0.3 },
    { offset: 1, transform: 'scale(1)', opacity: 1 }
  ]);
  
const squareC = createAnimation()
  .addElement(document.querySelector('.square-c'))
  .duration(1000)
  .keyframes([
    { offset: 0, transform: 'scale(1)', opacity: 0.5 },
    { offset: 0.5, transform: 'scale(0.8)', opacity: 1 },
    { offset: 1, transform: 'scale(1)', opacity: 0.5 }
  ]);

await squareA.playAsync();
await squareB.playAsync();
await squareC.playAsync();
```

The example above plays animation `squareA` first, then plays animation `squareB`, and finally plays animation `squareC`. An alternative way of writing this would be to use the `onFinish` method as follows:

```javascript
squareA.onFinish(() => squareB.play());
squareB.onFinish(() => squareC.play());

squareA.play();
```

CODEPEN WITH LIVE DEMO SHOULD GO HERE

## Gesture Animations

Info on how to create a gesture based animation using `createGesture` (maybe link to a Gesture utility doc?)

### Usage


## Performance Considerations

Info on performance considerations

## Types

```typescript
AnimationDirection = 'normal' | 'reverse' | 'alternate' | 'alternate-reverse';
AnimationFill = 'auto' | 'none' | 'forwards' | 'backwards' | 'both';
```

## Properties

#### `childAnimations: Animation[]`

All child animations of a given parent animation.

#### `elements: HTMLElement[]`

All elements attached to an animation.


#### `parentAnimation: Animation | undefined`

The parent animation of a given animation object.

## Methods

#### `addAnimation(animationToAdd: Animation | Animation[] | undefined | null) => Animation`

Group one or more animations together to be controlled by a parent animation.

#### `addElement(el: Element | Element[] | Node | Node[] | NodeList | undefined | null) => Animation`

Add one or more elements to the animation.

#### `afterAddClass(className: string | string[] | undefined) => Animation`

Add a class or array of classes to be added to all elements in an animation after the animation ends.

#### `afterAddRead(readFn: () => void) => Animation`

Add a function that performs a DOM read to be run after the animation ends.

#### `afterAddWrite(writeFn: () => void) => Animation`

Add a function that performs a DOM write to be run after the animation ends.

#### `afterClearStyles(propertyNames: string[]) => Animation`

Add an array of property names to be cleared from the inline styles on all elements in an animation after the animation ends.

#### `afterRemoveClass(className: string | string[] | undefined) => Animation`

Add a class or an array of classes to be removed from all elements in an animation after the animation ends.

#### `afterStyles(styles: { [property: string]: any }) => Animation`

Add an object of styles to be applied to all elements in an animation after the animation ends.

#### `animationFinish() => Animation`

Need docs for this.

#### `beforeAddClass(className: string | string[] | undefined) => Animation`

Add a class or array of classes to be added to all elements in an animation befpre the animation starts.

#### `beforeAddRead(readFn: () => void) => Animation`

Add a function that performs a DOM read to be run before the animation starts.

#### `beforeAddWrite(writeFn: () => void) => Animation`

Add a function that performs a DOM write to be run before the animation starts.

#### `beforeClearStyles(propertyNames: string[]) => Animation`

Add an array of property names to be cleared from the inline styles on all elements in an animation before the animation starts.

#### `beforeRemoveClass(className: string | string[] | undefined) => Animation`

Add a class or an array of classes to be removed from all elements in an animation before the animation starts.

#### `beforeStyles(styles: { [property: string]: any }) => Animation`

Add an object of styles to be applied to all elements in an animation before the animation starts.

#### `direction(direction: AnimationDirection | undefined) => Animation`

Set the direction the animation should play in.

#### `delay() => Animation`

Set the delay for the start of the animation in milliseconds.

#### `destroy() => Animation`

Destroy the animation and clear all elements, child animations, and keyframes.

#### `duration(duration: number) => Animation`

Set the duration of the animation in milliseconds.

#### `easing(easing: string) => Animation`

Set the easing of the animation in milliseconds. See [Easing Effects](https://developer.mozilla.org/en-US/docs/Web/API/EffectTiming/easing#Value) for a list of accepted easing values.

#### `pause() => Animation`

Pause the animation.

#### `play() => Animation`

Play the animation.

#### `playAsync() => Promise<Animation>`

Play the animation asynchronously. Returns a promise that resolves when the animation is done.

#### `playSync() => Animation`

Play the animation synchronously. This is the equivalent of playing an animation with a duration of 0.

#### `stop() => Animation`

Stop the animation and reset all elements to their initial state.
