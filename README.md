# textAnime.js

This plugin helps to animate texts using a typewriting pattern. 

### Installation

This plugin can be included into your project file by adding the script as shown below 

```js
<script src="textAnime.js" />
```

> Once we have added the script, we can proceed to initialize the plugin. 

### Initilizing

This plugin is usually initiliazed with a query selector of the field whose text is expected to be animated. Assuming we have an html field as shown below

```html
<div class="animate"> This is an animation </div>
```

> We can instantiate the _textAnime_ plugin on this element as shown below

```js
let textAnime = TextAnime('.animate')
```

> Once we have selected the field to be animated as shown above, we can continue to animate the text using the _typewrite_ method.

```js 
textAnime.typeWrite({})
```

### Typewrite Options 

There are several supported options and methods which can be applied on the animation. These are listed below and will be discussed one after the other. 

   + color
   + texts
   + speed
   + beats
   + alternate
   + each()
   + done()

   ##### color 

   This option is used to set a default color for all animated texts. This will overide any default color set.

   ```js 
   textAnime.typeWrite({
    color: red
   })
   ```

   ##### text 

   This option is used to set a list of texts or words that is expected to be typewrited.

   ```js 
   textAnime.typeWrite({
    texts: ['first text', 'second text', 'third text']
   })
   ```
   
   > Note that if the selected field contains a text, this text will be pushed to the first of the animated texts. This behaviour makes it easier to set default texts in selected fields.

   ##### speed 

   This option determines the speed at which each texts character is typewrited out.

   ```js 
   textAnime.typeWrite({
    text: ['first text', 'second text', 'third text'],
    speed: 500,
   })
   ```
   
   > The above sets the typewriter at a speed of 500ms per each character displayed

   ##### beats 

   This option sets the interval at which each text is displayed before character animation kicks in.

   ```js 
   textAnime.typeWrite({
    text: ['first text', 'second text', 'third text'],
    beats: 1000
   })
   ```
   
   > In the code above, after the first text has been animated, it will wait for 1 seconds before the second text is rendered. 

   ##### alternate 

   This option takes a boolean value which determines if the looped set of predefined animated texts should stop or continue after reaching the end of the text list.

   ```js 
   textAnime.typeWrite({
    text: ['first text', 'second text', 'third text'],
    alternate: true
   })
   ```
   
   > The above defines that when the last text 'third text' is reached, then the animation will start again from the beginning.

   ##### method:done 

   This method is used to define a callback function for when each text character animation is completed before the next animation is initializated or started.

   ```js 
   textAnime.typeWrite({
    text: ['first text', 'second text', 'third text'],
    beats: 1000,
    done: function(){
        //run this after animation
    }
   })
   ```
   
   > When using the _done()_ callback function, the amount of time required for the function to run should never be greater than the _beats_ which determines when the next text animation is initialized. Hence, using setTimeout should be with care while setIntervals should be avoided. 

   ##### method:each 

   This method is used to define how each text character should be rendered. This is usually being used to add effects to each text character typed out. This method helps to extend the animation capacity of the typewriter. For example we can create dynamically generated colorful texts or text animations as shown below:

   ```js 
   textAnime.typeWrite({
    text: ['first text', 'second text', 'third text'],
    beats: 1000,
    each: function(character, index){
        let space = '', hue, xclass, color;

        hue = Math.floor(Math.random() * 361);
        xclass = ((index % 2) === 0)? " blade" : "";
        color = `hsl(${hue}, 50%, 80%)`;

        if(character === ' '){
            character = '&nbsp;';
        }

        return `<span style='color:${color}' class="si${xclass}">${character}</span>`
    }
   })
   ```
   
   > In the _each()_ method above, the first argument _"character"_ defines the current character expected to be displayed while the _"index"_ argument defines the character position. Taking advantage of this, We defined a dynamic color hue for the animated characters. When the _each()_ method is used, it required that the currently modified character should be returned which overides the default character. In this way, in the above, we were able to return the character as a span having its own class and style attribute. It is important to note that the animation may not handle spaces well, in order to fix this, each space is handled as a character and replaced with the _&nbsp;_ html special code entity.

### Managing Events 

The typewriter function provides extended functionality to manage the typewriter animation. The available methods are 

   + pause : Used for pausing the state of the animation
   + play  : Used for resuming or restarting the state of the animation
   + previous : Used for going back to the previous animation
   + previous : Used for going back to the next animation
   + reboot : Used for restarting the animation

The example below provides a control button that can be used to control the animation state. 

   > Assuming we have an html control buttons as shown below: 

   ```html
   <div class="text-field">some default text</div> <br>

   <div class="controls">
       <button id="pause">Pause</button>
       <button id="play">Play</button>
       <button id="reboot">reboot</button>
       <button id="previous">previous</button>
       <button id="next">next</button>
   </div>
   ```
   
   > We can easily set up the typewriter as shown below

   ```js
   let textAnime, typewriter;

   textAnime = new TextAnime('.text-field');

   typewriter = textAnime.typeWrite({
       text: ['first text', 'second text', 'third text'],
       beats: 1000,
       each: function(character, index){
           let space = '', hue, xclass, color;

           hue = Math.floor(Math.random() * 361);
           xclass = ((index % 2) === 0)? " blade" : "";
           color = `hsl(${hue}, 50%, 80%)`;

           if(character === ' '){
               character = '&nbsp;';
           }

           return `<span style='color:${color}' class="si${xclass}">${character}</span>`
      }
   });
   ```

   > Taking advantage of the typewriter control system, we can attach the controller events to the html buttons earlier

   ```js
   document.querySelector('#pause').addEventListener('click', function(){
      typewriter.pause(function(controls){
         console.log('paused!')
      });
    })

   document.querySelector('#play').addEventListener('click', function(){
       typewriter.play(function(controls){
           console.log('played')
       });
   })

   document.querySelector('#reboot').addEventListener('click', function(){
       typewriter.reboot();
   })

   document.querySelector('#previous').addEventListener('click', function(){
       typewriter.previous(function(controls){
           console.log(controls)
       });
   })

   document.querySelector('#next').addEventListener('click', function(){
       typewriter.next();
   })
   ```

   > Each of the typewriter controller methods can be supplied with a callback function which in turn has access to the controls system. For example, we can easily overide the default speed when a control is called. 

   ```js 
   document.querySelector('#next').addEventListener('click', function(){
       typewriter.next(function(control){

            let settings = control.anime.settings;

            settings.speed = 100;

       });
   })  
   ```

   > The code above will overide the default speed after the next text is called. This is the default behaviour of the callback function. However in the case when the _reboot()_ method is called, the callback will run before the reboot function is executed.

   ```js 
   document.querySelector('#reboot').addEventListener('click', function(){
       typewriter.reboot(function(){

            console.log('animation was rebooted');

       });
   })  
   ```

### Dependencies 

Although this class works with the normal querySelectors, the _[selector.js](https://github.com/teymzz/selector.js)_ plugin can increase the selection range.
















) plugin can help to improve the selection range of this plugin