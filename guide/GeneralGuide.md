# TurboWarp Custom Extensions Guide

## Introduction

This guide explains how to create custom JavaScript extensions for TurboWarp, from the basics to advanced unsandboxed features.

Topics include:

- Extension structure
- Creating blocks
- Inputs and arguments
- Reporter / command / boolean blocks
- Menus
- Runtime APIs
- Sprite data access
- Variables and lists
- Renderer access
- File handling
- Debugging
- Best practices
- Security
- Performance
- Packaging and distribution

---

# 1. What Is A TurboWarp Extension?

A TurboWarp extension is a JavaScript file loaded into TurboWarp that adds new blocks.

Example:

```js
class MyExtension {
    getInfo() {
        return {
            id: 'myext',
            name: 'My Extension',
            blocks: [
                {
                    opcode: 'hello',
                    blockType: Scratch.BlockType.REPORTER,
                    text: 'hello'
                }
            ]
        };
    }

    hello() {
        return 'Hello world';
    }
}

Scratch.extensions.register(new MyExtension());
```

This creates a reporter block called:

```scratch
hello
```

---

# 2. Sandboxed vs Unsandboxed Extensions

## Sandboxed

- Safer
- Restricted access
- Cannot access browser internals directly
- Cannot access runtime internals deeply

## Unsandboxed

- Full JavaScript access
- Access to TurboWarp runtime
- Access to browser APIs
- Can manipulate renderer/runtime directly

Unsandboxed extensions are extremely powerful.

---

# 3. Loading Extensions

In TurboWarp:

1. Open Extensions
2. Custom Extension
3. Load extension from URL

You can:
- host extensions online
- drag-and-drop local files
- use file:// paths

Example:

```txt
file:///C:/Users/You/Desktop/ext.js
```

---

# 4. Extension Structure

Basic structure:

```js
class MyExtension {
    getInfo() {
        return {
            id: 'myext',
            name: 'My Extension',
            blocks: []
        };
    }
}

Scratch.extensions.register(new MyExtension());
```

---

# 5. Block Types

## Command Block

Performs an action.

```js
{
    opcode: 'sayHello',
    blockType: Scratch.BlockType.COMMAND,
    text: 'say hello'
}
```

---

## Reporter Block

Returns a value.

```js
{
    opcode: 'getNumber',
    blockType: Scratch.BlockType.REPORTER,
    text: 'number'
}
```

---

## Boolean Block

Returns true/false.

```js
{
    opcode: 'isBig',
    blockType: Scratch.BlockType.BOOLEAN,
    text: '[A] > [B]'
}
```

---

## Hat Block

Triggers scripts.

```js
{
    opcode: 'whenSomething',
    blockType: Scratch.BlockType.HAT,
    text: 'when something happens'
}
```

---

# 6. Arguments / Inputs

Arguments are defined inside `arguments`.

Example:

```js
{
    opcode: 'add',
    blockType: Scratch.BlockType.REPORTER,
    text: '[A] + [B]',
    arguments: {
        A: {
            type: Scratch.ArgumentType.NUMBER,
            defaultValue: 1
        },
        B: {
            type: Scratch.ArgumentType.NUMBER,
            defaultValue: 2
        }
    }
}
```

Function:

```js
add(args) {
    return args.A + args.B;
}
```

---

# 7. Argument Types

Available types:

```js
Scratch.ArgumentType.NUMBER
Scratch.ArgumentType.STRING
Scratch.ArgumentType.BOOLEAN
Scratch.ArgumentType.COLOR
Scratch.ArgumentType.ANGLE
Scratch.ArgumentType.NOTE
Scratch.ArgumentType.MATRIX
```

---

# 8. Menus

Menus create dropdowns.

```js
menus: {
    axisMenu: {
        items: ['x', 'y', 'z']
    }
}
```

Usage:

```js
AXIS: {
    type: Scratch.ArgumentType.STRING,
    menu: 'axisMenu'
}
```

---

# 9. Dynamic Menus

Menus can be generated dynamically.

```js
menus: {
    spriteMenu: {
        acceptReporters: true,
        items: 'getSprites'
    }
}
```

Function:

```js
getSprites() {
    return Scratch.vm.runtime.targets.map(t => t.getName());
}
```

---

# 10. Working With Math

JavaScript math uses:

```js
Math.function()
```

Examples:

```js
Math.sin()
Math.cos()
Math.tan()
Math.sqrt()
Math.abs()
Math.floor()
Math.ceil()
Math.round()
Math.random()
Math.PI
```

---

# 11. Degrees vs Radians

JavaScript trig functions use radians.

Convert degrees:

```js
const radians = degrees * Math.PI / 180;
```

Example:

```js
const result = Math.cos(angle * Math.PI / 180);
```

---

# 12. Floating Point Precision

Computers cannot represent many decimal numbers exactly.

Example:

```js
Math.cos(Math.PI / 2)
```

returns:

```txt
6.123233995736766e-17
```

Use epsilon checks:

```js
if (Math.abs(value) < 1e-10) {
    value = 0;
}
```

---

# 13. Accessing The Runtime

Unsandboxed extensions can access:

```js
Scratch.vm
```

Main areas:

```js
Scratch.vm.runtime
Scratch.vm.renderer
Scratch.vm.extensionManager
```

---

# 14. Accessing Sprites

Get a sprite:

```js
const target =
    Scratch.vm.runtime.getSpriteTargetByName("Player");
```

Properties:

```js
target.x
target.y
target.direction
target.visible
target.size
```

---

# 15. Variables

Variables are stored in:

```js
target.variables
```

Example:

```js
for (const id in target.variables) {
    const variable = target.variables[id];

    console.log(variable.name);
    console.log(variable.value);
}
```

---

# 16. Lists

Lists are variables with type `"list"`.

Example:

```js
if (variable.type === "list") {
    console.log(variable.value);
}
```

---

# 17. Costumes

Current costume:

```js
target.currentCostume
```

All costumes:

```js
target.sprite.costumes
```

Example:

```js
const costume =
    target.sprite.costumes[target.currentCostume];

console.log(costume.name);
```

---

# 18. Clones

All runtime targets:

```js
Scratch.vm.runtime.targets
```

Detect clones:

```js
target.isOriginal
```

---

# 19. Renderer Access

Renderer:

```js
const renderer = Scratch.vm.renderer;
```

Drawable ID:

```js
target.drawableID
```

Drawable:

```js
renderer._allDrawables[target.drawableID]
```

---

# 20. Extension Manager

Extensions are managed through:

```js
Scratch.vm.extensionManager
```

Loaded extensions:

```js
Scratch.vm.extensionManager._loadedExtensions
```

Services:

```js
Scratch.vm.extensionManager.services
```

---

# 21. Debugging

## console.log

```js
console.log(value);
```

## console.warn

```js
console.warn("warning");
```

## console.error

```js
console.error("error");
```

---

# 22. Opening DevTools

## Browser

Press:

```txt
F12
```

or:

```txt
Ctrl + Shift + I
```

Go to Console tab.

---

## TurboWarp Desktop

Usually:

```txt
Ctrl + Shift + I
```

or:

```txt
F12
```

---

# 23. Useful Debugging Tricks

Readable JSON:

```js
console.log(JSON.stringify(obj, null, 2));
```

Tables:

```js
console.table(array);
```

Pause execution:

```js
debugger;
```

---

# 24. Browser Storage

## localStorage

Save:

```js
localStorage.setItem("save", JSON.stringify(data));
```

Load:

```js
const data =
    JSON.parse(localStorage.getItem("save"));
```

---

# 25. Downloading Files

Example:

```js
const blob = new Blob(
    [JSON.stringify(data)],
    {type: "application/json"}
);

const a = document.createElement("a");

a.href = URL.createObjectURL(blob);

a.download = "save.json";

a.click();
```

---

# 26. Uploading Files

Example:

```js
const input = document.createElement("input");

input.type = "file";

input.onchange = async e => {
    const file = e.target.files[0];

    const text = await file.text();

    console.log(text);
};

input.click();
```

---

# 27. HTML Overlays

Unsandboxed extensions can create HTML.

Example:

```js
const div = document.createElement("div");

div.style.position = "fixed";
div.style.left = "0";
div.style.top = "0";

document.body.appendChild(div);
```

Useful for:
- debugging
- FPS counters
- custom interfaces
- editors

---

# 28. Events

Keyboard input:

```js
window.addEventListener("keydown", e => {
    console.log(e.key);
});
```

Mouse input:

```js
window.addEventListener("mousemove", e => {
    console.log(e.clientX);
});
```

---

# 29. Async Functions

Extensions can use async code.

Example:

```js
async fetchData() {
    const response =
        await fetch("https://example.com");

    return await response.text();
}
```

---

# 30. Performance Tips

## Avoid Heavy Loops

Bad:

```js
while (true) {}
```

## Cache Expensive Values

Bad:

```js
Math.sqrt(x)
Math.sqrt(x)
Math.sqrt(x)
```

Good:

```js
const root = Math.sqrt(x);
```

---

# 31. Security

Unsandboxed extensions can:
- access runtime internals
- access browser APIs
- sometimes access filesystem APIs
- communicate with servers

Never load untrusted extensions.

---

# 32. Recommended Learning Path

## JavaScript Basics

Learn:
- variables
- functions
- arrays
- objects
- loops
- classes

## Then Learn

- vectors
- trigonometry
- collision math
- rendering
- async programming

---

# 33. Best Practices

## Keep Functions Small

Bad:

```js
hugeFunctionDoingEverything()
```

Good:

```js
updatePhysics()
drawObjects()
checkCollisions()
```

---

## Use Constants

```js
const EPSILON = 1e-10;
```

---

## Use Comments

```js
// Convert degrees to radians
```

---

# 34. Common Mistakes

## Forgetting `this`

## Forgetting radians conversion

## Blocking the main thread

## Modifying runtime internals carelessly

## Infinite loops

---

# 35. Example Complete Extension

```js
class ExampleExtension {
    getInfo() {
        return {
            id: 'example',
            name: 'Example',

            blocks: [
                {
                    opcode: 'add',
                    blockType: Scratch.BlockType.REPORTER,
                    text: '[A] + [B]',

                    arguments: {
                        A: {
                            type: Scratch.ArgumentType.NUMBER,
                            defaultValue: 1
                        },

                        B: {
                            type: Scratch.ArgumentType.NUMBER,
                            defaultValue: 2
                        }
                    }
                }
            ]
        };
    }

    add(args) {
        return args.A + args.B;
    }
}

Scratch.extensions.register(new ExampleExtension());
```

---

# 36. Recommended Resources

Official docs:

https://docs.turbowarp.org/development/extensions/introduction

Extensions gallery:

https://extensions.turbowarp.org/

TurboWarp GitHub:

https://github.com/TurboWarp/extensions

MDN JavaScript docs:

https://developer.mozilla.org/

---

# Conclusion

TurboWarp extensions range from simple utility blocks to advanced runtime modifications and game engine systems.

Once you understand:
- blocks
- arguments
- runtime access
- debugging
- browser APIs

you can build:
- physics systems
- renderers
- networking APIs
- custom editors
- save systems
- debugging tools
- advanced game mechanics

The most important skill is experimentation and inspection:
- use console.log
- inspect runtime objects
- read existing extension code
- test small systems incrementally
