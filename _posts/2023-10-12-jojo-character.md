---
toc: true
comments: false
layout: post
title: Jonathan Animation
description: this is our jonathan anitmation sprite character.
type: hacks
courses: { compsci: {week: 6} }
---

<body>
    <div id="controls"> <!--basic radio buttons which can be used to check whether each individual animaiton works -->
        <button id="toggleCanvasEffect">Invert</button>
        <input type="radio" name="animation" id="walkRight" checked>
        <label for="walkRight">Walk Right</label>
        <input type="radio" name="animation" id="walkLeft">
        <label for="walkLeft">Punch</label>
        <input type="radio" name="animation" id="jumpRight">
        <label for="jumpRight">Sword Fight</label>
        <input type="radio" name="animation" id="jumpLeft">
        <label for="jumpLeft">Fire Punch</label>
    </div>
    <div>
        <canvas id="spriteContainer"> <!-- Within the base div is a canvas. An HTML canvas is used only for graphics. It allows the user to access some basic functions related to the image created on the canvas (including animation) -->
            <img id="Jonathan Joes Sprite" src="{{site.baseurl}}/images/Jonathan_Joes_sprite_sheet.png"> // change sprite here
        </canvas>

    </div>
</body>

<script>
    const canvas = document.getElementById('spriteContainer');
    const ctx = canvas.getContext('2d');
    /* Toggle "canvas filter propery" 
    * look in _sass/minima/dark-mode.scss
    */
   canvas.style.filter = "none";
    /*var isFilterEnabled = true;
    const defaultFilter = getComputedStyle(document.documentElement).getPropertyValue('--default-canvas-filter');
    toggleCanvasEffect.addEventListener("click", function () {
        if (isFilterEnabled) {
            canvas.style.filter = "none";  // remove filter
        } else {
            canvas.style.filter = defaultFilter; // Apply the default filter value
        }

        isFilterEnabled = !isFilterEnabled;  // switch boolean value
    });*/
    // start on page load
    window.addEventListener('load', function () {
        const SPRITE_WIDTH = 55;  // matches sprite pixel width
        const SPRITE_HEIGHT = 75; // matches sprite pixel height
        const FRAME_LIMIT = 5;  // matches number of frames per sprite row, this code assume each row is same

        const SCALE_FACTOR = 2;  // control size of sprite on canvas
        canvas.width = SPRITE_WIDTH * SCALE_FACTOR;
        canvas.height = SPRITE_HEIGHT * SCALE_FACTOR;

        // a class to store the differences in the animations to make it clear what the animation changes are for
        class AnimationType{
            constructor(initFrameX = 0, frameY = 0, maxFrame = FRAME_LIMIT, animationDelay = 68, spriteWidth = SPRITE_WIDTH , spriteHeight= SPRITE_HEIGHT, overrides = []){
                this.maxFrame = maxFrame;
                this.initFrameX = initFrameX;
                this.frameY = frameY;
                this.animationDelay = animationDelay;
                this.spriteWidth = spriteWidth;
                this.spriteHeight = spriteHeight;
                this.overrides = overrides;
            }
        }

        class Jonathan {
            constructor() {
                this.image = document.getElementById("Jonathan Joes Sprite");
                this.x = 0;
                this.y = 0;
                this.minFrame = 0;
                this.animationType = new AnimationType();
                this.frameX = 0;
                this.frameY = 0;
            }

            // draw Monkey object
            draw(context) {
                let spriteWidth = this.animationType.spriteWidth;
                for (let override of this.animationType.overrides) {
                    if (override.x === this.frameX) {
                        spriteWidth = override.width;
                    }
                }

                context.drawImage(
                    this.image,
                    this.frameX * this.animationType.spriteWidth,
                    this.animationType.frameY,
                    spriteWidth,
                    this.animationType.spriteHeight,
                    this.x,
                    this.y,
                    canvas.width,
                    canvas.height
                );
            }

            // update frameX of object
            update() {
                // this sprite sheet uses a variable number of frames for different animations
                if (this.frameX < this.animationType.maxFrame) {
                    this.frameX++;
                } else {
                    // the start frame of the animations changes in this sprite sheet
                    this.frameX = this.animationType.initFrameX;
                }
            }
        }

        // Monkey object
        const jonathan = new Jonathan();

        // Make the animations that deviate from the default use a class 
        // to make it more readible what the changes are doing
        //const jumpRightAnimation = new AnimationType(undefined, 7, undefined); // frames go from 0 to 7 but everything else is the same
        //const jumpLeftAnimation = new AnimationType(8, undefined, undefined); // frames go from 8 to 14 but everything else is the same
        const punchAnimation = new AnimationType(undefined, 110,12, undefined, 60,65);

        const swordAnimation = new AnimationType(undefined,170,11,
        undefined,65,95);

        const firePunchAnimation = new AnimationType(undefined, 285,8, undefined, 64,64, [{x:4,width:80},{x:5,width:80}]);
        // update frameY of monkey object, action from radio controls
        const controls = document.getElementById('controls');
        controls.addEventListener('click', function (event) {
            if (event.target.tagName === 'INPUT') {
                const selectedAnimation = event.target.id;
                switch (selectedAnimation) {
                    case 'walkRight':
                        jonathan.frameY = 0;
                        jonathan.animationType = new AnimationType();
                        break;
                    case 'walkLeft':
                        jonathan.frameY = 1;
                        jonathan.animationType = punchAnimation;
                        break;
                    case 'jumpRight':
                        jonathan.frameY = 2;
                        jonathan.animationType = swordAnimation;
                        break;
                    case 'jumpLeft':
                        jonathan.frameY = 3;
                        jonathan.animationType = firePunchAnimation;
                    default:
                        break;
                }
            }
        });
             
        // Animation recursive control function
        function animate() {
            // Clears the canvas to remove the previous frame.
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Draws the current frame of the sprite.
            jonathan.draw(ctx);

            // Updates the `frameX` property to prepare for the next frame in the sprite sheet.
            jonathan.update();

            // Uses `requestAnimationFrame` to synchronize the animation loop with the display's refresh rate,
            // ensuring smooth visuals.
            requestAnimationFrame(function() {
                setTimeout(animate, jonathan.animationType.animationDelay); // Adjust the delay (in milliseconds) to control the frame rate.
            });
        }

        // run 1st animate
        animate();
    });
</script>