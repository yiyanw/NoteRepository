# Vue 2

## Keywords 

1. data binding - {{}}

   * v-once - only update variable once, including all children tags

2. data receiving 

   * save input to variable name
     * `<input type="text" v-model="name"/>`
   * type casting 
     * V-model.number
   * Lazy updating
     * v-model.lazy - only update the variable when losing focus

3. v-bind - for parameter text binding

4. v-on - for function parameter binding

   * Click - v-on:click 
     * if do not mention any params, it will have a js native param "event"
       * v-on:click="\<functionName>"
       * \<functionName>: function() { ... }
     * can pass customised param
       * v-on:click="\<functionName>($event, \<customized params>)"
     * V-on:click.stop - click event will not propagate further
     * V-on:click.once - only trigger the method once
     * V-on:click.prevent - prevent default function

   * keyup.\<keyName>.\<otherKeyName>
   * modifier
     * Trigger function when press modifier and target key at same time

5. v-html - render html text 

   * be careful for this tag, always check the input before using 

6. condition control

   * ```html
     <div>
       <p v-if="<condition1>">...</p>
       <p v-else-if="<condition2>">...</p>
       <p v-else>...</p>
     </div>
     ```

   * the tags that do not meet the condition will be directly removed by dom

   * also can use template to implement condition control, so we can get 

7. Template

8. v-cloack - the element is invisible until the data is loaded

9. v-for

   * `<tag v-for='obj in iterableObj'>`
   * `<tag v-for='(obj, index) in iterableObj'>`
   * `<tag v-for='(obj, propertyName) in iterableObj'>`
   * `<tag v-for='(obj, propertyName, index) in iterableObj'>`
   * loop number range
     * `<tag v-for="n in 10">` - 1,2,3,4,5,6,7,8,9,10
     * `<tag v-for="(n, index) in 10">`

10. Vue.set()/this.$set() - for updating array/obj and vue can detect the change

    * PS - must use this to initialize the variable, otherwise vue will not establish built-in setter and getter to detect changes

      * ```js
        // this is the correct way, initial all key-value pairs in advance
        // therefore, vue can create builtin getters and setters for each key-value pair
        data: {
          slotsOpen: {"SlotId1": false, "SlotId2": false, ... }
        }
        
        
        // this is the other correct way
        OnSlotClick(slot) {      
        			if (this.slotsOpen[slot.slotId] === undefined) {
                this.$set(this.slotsOpen, slot.slotId, false);
              }
          		// as long as using correct initializaiton, both two ways of updating works
              // this.$set(this.slotsOpen, slot.slotId, !this.slotsOpen[slot.slotId]);
              this.slotsOpen[slot.slotId] = !this.slotsOpen[slot.slotId];
        }
        
        // this is the wrong way
        OnSlotClick(slot) {
          		// this initialization does not works 
              if (this.slotsOpen[slot.slotId] === undefined) {
                this.slotsOpen[slot.slotId] = false;
              }
          		// althought it uses set function later, vue cannot detect changes
              this.$set(this.slotsOpen, slot.slotId, !this.slotsOpen[slot.slotId]);
        }
        
        ```

11. computed properties

    * write as a function - only be called when target properties changes, otherwise using cache

    * use as a property

    * `computed: { ... }`

    * getter and setter

      * ```js
        computed: {
          var1: {
           
            set: function(newVar) {
              ...
            }
          }
        }，
        methods: {
          test: function() {
            let test = this.var1; // invoking getter
            this.var1 = "new text"; // invoking setter
          }
        }
        ```
        
      * 

12. Watcher

    * the function will be automatically trigger when the target property changes

    * `watch: { ... }`

    * example

      * ```js
        new Vue({
          ...,
          data: {
          	var1: ''
        	},
          watch: {
          	var1: function(newVar) {
          		...
        		}      
          }      
         
        })
        ```

13. Filter - can be used as part of pipeline processing

    * `filters: { ... }`

    * ```js
      filters: {
        filter1: function(value, ...) { // first param is filled by pipeline
          ...
          return ...
        }
      }
      ```

    * ```html
      <div>
        {{ message | filter1(...) }} 
        <!-- message will always be the first param, we need to fill rest customised params -->
      </div>
      ```

14. inline CSS style

    * vue can combine native style and v-bind:style

      * `<div style="width: 200px; height: 200px" v-bind:style="styles"/>` - this `styles` is property defined in data

    * v-bind can get multi style properties as array

      * `<div v-bind:style="[style1, style2]"`
      * if `style1` and `style2` has same feature, the second one will cover the first one

15. CSS class

    * format

      * `v-bind:class='{css1:true}'` - `css1` is the class defined in CSS files 

      * `v-bind:class='[css2, { css1: true }]'` - can combine the css classes in array

16. Shorthands

    * `v-on:click -> @click`
    * `v-bind:style -> @style`

## Deep Dive: The Vue Instance

* todo

## Setting up a development Env

* we can use JSFiddle at first, but we need a more formal development env for the read work
* setting Env with Webpack and Vue CLI
  * `npm install -g vue-cli` - install VueCLI
  * `vue init webpack-simple <projectName>` - initial a project with simple webpack template
  * `cd` into project directory
  * `npm install`
  * `npm run dev` - deploy on local device
* structure of project
  * Package.json
    * `babel` - transpiler for handling compatibility of browsers
  * webpack.config.js
    * Entry - the entry point of whole project
  * main.js
    * `render: h => h(App)`
      * equivalent to `render: function(createElement){ createElement(App); }`
* building for production
  * `npm run build` - build project
    * it will create a directory with file named `build.js`

## Components

* syntax - (global components)

  * ```html
    <div id='app1'>
      <contact-us></contact-us>
      <contact-us></contact-us>
    </div>
    ```

  * ```js
    Vue.component('contact-us', { // matching the tag name
    	data: function() { // data should be a function, instead of object
      	return {
        	email: 'info@mycompany.com'
        }
      },
      template: `
      	<div>
        	<h1>Contact Us</h1>
          <p>Please send an e-mail to: {{ email }}</p>
        </div>
      `
    });
    
    new Vue({
      el: '#app1'
    })
    ```

* global and local components

  * Local components

    * ```html
      <div id='app1'>
        <contact-us></contact-us>
      </div>
      
      <div id='app2'>
        <contact-us></contact-us> <!-- won't be rendered, because contact-us tag is only declared in app1 -->
      </div>
      ```

    * ```js
      let contactUs = { // matching the tag name
      	data: function() { // data should be a function, instead of object
        	return {
          	email: 'info@mycompany.com'
          }
        },
        template: `
        	<div>
          	<h1>Contact Us</h1>
            <p>Please send an e-mail to: {{ email }}</p>
          </div>
        `
      }
      
      new Vue({
        el: '#app1'
        components: {
        	'contact-us': contactUs
      	}
      })
      
      new Vue({
        el: '#app2'
      })
      ```

* naming

  * camel case - thisIsName
  * kebab case - this-is-name
  * pascal case - ThisIsName
  * Convention of naming: 
    * using camel in js
    * using kebab in html

* for real project

  * Html and js will be written in same file with suffix `*.vue`

  * ```vue
    <template>
      <div class="alert alert-info">
      {{ announcement }}
      </div>
    </template>
    
    <script>
    export default {
      data() {
        return {
          announcement: "The site will go down for maintenance soon!"
        }
      }
    }
    </script>
    ```

* scoped style

  * `<style scope> ... </style>` - only applying on current and child components

* passing properties from parent to child components

  * ```vue
    <!-- child component named Announcement -->
    <template>
      <div class="alert alert-info">
      {{ announcement }}
      </div>
    </template>
    
    <script>
    export default {
      props: ['announcement']
    }
    </script>
    ```

  * ```vue
    <!-- parent component -->
    <template>
    	<announcement :announcement="announcement"/>
    </template>
    
    <script>
    import Announcement from './Announcement.vue';
    export default {
      components: {
        announcement: Announcement
      }
      data() {
        return {
          announcement: "The site will go down for maintenance soon!"
        }
      }
    }
    </script>
    ```

  * it will pass the reference type to child component, so child component will update when properties change.

* validating props

  * props can be a object type to customized features of props

  * ```vue
    ...
    props: {
    	<propertyName>: {
      	type: <type of property>,
        required: <boolean>,
        default: <default value, need a function if not the primitive type () => {}>
      },
      ...
    }
    ...
    ```

* event - passing data from child to parent

  * customized event - (event is the trigger to invoking further action, like `keydown`, `click`)
  * syntax
    * `this.$emit('<eventName>', {<arg1>, <arg2>, ...})` - emit event
    * `v-on:<eventName>="..."` - bind event


  * eventbus - communicate between two irrelated components
    * Define a empty vue component in `main.js` named `EventBus`
      * `export const EventBus = new Vue();`
    * sending component invoke `EventBus.$emit('<eventName>', {...})`
    * receving component invoke `EventBus.$on('<eventName>', <callbackFunc>)` 

* Slots - placeholder for the content between start tag and end tag

  * it will be replaced by the content

    * ```html
      <template>
      	<slot>it can have some default contents, such as text or tags</slot>
      </template>
      
      
      <!-- when using it -->
      <customizedTag>
      This text will replace the slot tag
      </customizedTag>
      ```

  * Named slots

    * ```html
      <template>
      	<slot name="s1">it can have some default contents, such as text or tags</slot>
        <slot name="s2">it can have some default contents, such as text or tags</slot>
        <slot>this is the default slot</slot>
      </template>
      
      
      <!-- when using it -->
      <customizedTag>
      <p slot="s1">
      	This text will replace the slot tag with name "s1"
      </p>
        
      <p>
      	This text will replace the slot tag without name
      </p>
       
      </customizedTag>
      ```

* dynamic components

  * use tag `<component :is="<componentName>"></component>`
  * using `<keep-alive><component/></keep-alive>` to keep component alive, instead of destroying every time it switches
    * two additional life cycle hooks
      * Activated()
      * Deactivated()


## Mixins & Filters

* a way to add common functionalities into component.
* personally opinion: do not use it if possible

## Form

* project is using vuetify

* Skip this part

* basic example

  * ```vue
    <template>
        <div class="container">
            <div class="row">
                <div class="col-xs-12">
                    <h1>Add Blog Post</h1>
    
                    <form @submit.prevent="isSubmitted = true">
                        <div class="form-group">
                            <label for="title">Title</label>
                            <input type="text" class="form-control" id="title" v-model.trim="post.title">
                        </div>
    
                        <div class="form-group">
                            <label for="content">Content</label>
                            <textarea class="form-control" id="content" v-model.lazy.trim="post.content" cols="30" rows="10"></textarea>
                        </div>
    
                        <div class="form-group">
                            <input type="checkbox" id="publish-immediately" v-model="post.publishImmediately">
                            <label for="publish-immediately">Publish immediately</label>
                        </div>
    
                        <div class="form-group">
                            <label>Share on</label>
    
                            <div v-for="media in formData.socialMedia">
                                <input type="checkbox" :id="media" :value="media" v-model="post.shareOn">
                                <label :for="media">{{ media }}</label>
                            </div>
                        </div>
    
                        <div class="form-group">
                            <label>Category</label>
    
                            <div v-for="category in formData.categories">
                                <input type="radio" :id="category" :value="category" v-model="post.category">
                                <label :for="category">{{ category }}</label>
                            </div>
                        </div>
    
                        <div class="form-group">
                            <label for="select-series">Series</label>
    
                            <select class="form-control" id="select-series" v-model="post.series">
                                <option value="">Choose Series</option>
                                <option v-for="series in formData.series" :value="series">{{ series }}</option>
                            </select>
                        </div>
    
                        <input type="submit" class="btn btn-primary" value="Publish">
                    </form>
    
                    <hr>
    
                    <table class="table table-striped" v-if="isSubmitted">
                        <thead>
                            <tr>
                                <td class="col-xs-6"><strong>Field</strong></td>
                                <td class="col-xs-6"><strong>Value</strong></td>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td>Title</td>
                                <td>{{ post.title }}</td>
                            </tr>
                            <tr>
                                <td>Content</td>
                                <td style="white-space: pre;">{{ post.content }}</td>
                            </tr>
                            <tr>
                                <td>Publish immediately</td>
                                <td>{{ post.publishImmediately }}</td>
                            </tr>
                            <tr>
                                <td>Share on</td>
                                <td>
                                    <ul>
                                        <li v-for="media in post.shareOn">{{ media }}</li>
                                    </ul>
                                </td>
                            </tr>
                            <tr>
                                <td>Category</td>
                                <td>{{ post.category }}</td>
                            </tr>
                            <tr>
                                <td>Series</td>
                                <td>{{ post.series }}</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </template>
    
    <script>
        export default {
            data() {
                return {
                    isSubmitted: false,
                    post: {
                        title: '',
                        content: '',
                        publishImmediately: true,
                        shareOn: ['Facebook'],
                        category: 'Backend',
                        series: 'Complete Guide to Elasticsearch'
                    },
                    formData: {
                        socialMedia: ['Facebook', 'Twitter'],
                        categories: ['Frontend', 'Backend'],
                        series: [
                            'Vue.js: From Beginner to Professional',
                            'Complete Guide to Elasticsearch'
                        ]
                    }
                };
            }
        }
    </script>
    
    <style>
        input[type="radio"] + label,
        input[type="checkbox"] + label {
            font-weight: normal;
        }
    </style>
    ```

  * 

## SPA - Single Page Application

* external url is constant

  * avoiding user's unexpected accesses ( <u>todo: this is not correct)</u>

* install vue-router

  * `npm install vue-router`

* import vue-router

  * `import VueRouter from 'vue-router'`

* Enable vue-router

  * `Vue.use(VueRouter)`

* demo

  * ```js
    // main.js
    import Vue from 'vue'
    import VueRouter from 'vue-router';
    import App from './App.vue'
    import { routes } from './routes'; // routing file "routes.js" with all required information
    
    Vue.use(VueRouter); // enable router
    
    const router = new VueRouter({
        routes: routes, // add routing information
        mode: 'history' // history mode will eliminate "/#" at the end of url, localhost:8080/#/... -> localhost:8080/...
    });
    
    new Vue({
        el: '#app',
        render: h => h(App),
        router: router // add router as an argument
    })
    ```

  * ```js
    // routes.js
    import Component1 from './Component1.vue'; 
    import Component2 from './Component2.vue';
    
    export const routes = [
        { path: '', component: Component1, name: "component-1" }, // can use path or name to identify
        { path: '/component2/:param1', component: Component2 }, // can specify param at the end
        { path: '*', component: { template: '<h1>Page Not Found!</h1>' } }
    ];
    ```

  * ```vue
    <!-- App.vue -->
    <template>
      <!-- router-link tag is used as button to nevigate -->
      <router-link to="/"><strong>Home Page</strong></router-link>
    	<!-- that is an other way to identify routing tag with name -->
    	<router-link to="{ name: 'component-1', params: {...}}">Component 1</router-link></li>
    	<router-link to="/comp2">Component 2</router-link></li>
    	<!-- router-view tag is used to display the component with the matched url -->
      <router-view></router-view>
    </template>
    
    <script>
        export default {
        }
    </script>
    
    <style>
    ...
    </style>
    ```

* `exact` - kay word

  * only exactly-matching url will be rendered

* retrive url params

  * `this.$route.params.<paramName>`
    * Drawbacks: highly coupling between components
  * also can retrive by props
    * customized routes info `{ path: '/component2/:param1', props: true, component: Component2 }` - set props to true
    * then this `param1` can be access in props
      * `export default { props: { param1: {...}}}`

* nevigation programmtically

  * `this.$router.push("<path>")`
  * `this.$router.push({ name: '<route name>', params: {...}})`
  * `this.$router.replace(..)` -  same as push but won't add in historystack

* move forward or backward

  * `this.$router.go()` - positive number for forwarding, negative number for back warding

* redirecting

  * `{path: '/legacy/path', redirect: <path|{name: '..', ...}>`

* alias

  * `{path: '/path', alias: '/other/path'>`

* nested routes - todo

* `query` - query parameters

  * can be added at the end of url `xxx?<paramName>=<paramVal>`

  * also can use query key word to add it

    * ```js
      {
        ...
        query: {
          <paramName>: <paramVal>
        }
        ...
      }
      ```

  * Retrieve query param:

    * `this.$route.query.discount`

* hash fragments

  * route information has key word `hash="#<componentId>"` to select target component with id
  * `{ path: '', component: Component1, name: "component-1", hash: '#componentId' }`

* controlling the scroll behaviour - router function

  * ```javascript
    const router = new VueRouter({
        routes: ...,
        mode: ...,
      	scrollBehavior(to, from, savedPosition) { // it will be in invoked when nevigation happens
      		if (to.hash) {
            return {
              selector: to.hash;
            }
          }
      
          if (savedPosition) {
    				return savedPosition;
          }
      
      		return { x: 0, y: 0 }; // scrolling to the top left cornor
    		}
    });
    ```

* named views - give `<router-view name="<viewName>"` a name feature to target

  * without name tag, the default name is "default"
  * change route info to `{ path: '', components: { default: component1, <viewName>:<otherComponent>, ...}}`

* Transition & animation

  * move `router-view` into `<transition>`tag

  * ```html
    <transition
                enter-active-class="animated fadeInRight ..."
                leave-active-class="animated fadeOutLeft ..."
                mode="out-in"
                :duration="1000"
                >
    	<router-view>...</router-view>
    </transition>
    ```

* route guards - route function `beforeEnter`

  * ```js
    { path: '...',
      ...,
      beforeEnter(to, from, next) {
        if (!authService.isLoggedIn) {
          alert("You must be logged in!");
          return next(false); // return key word can avoid beforeEnter function being invoked again
        }
        
        next(); // nevigate to next url and component
      }
    }
    ```

* Component guards - 

  * component function `beforeRouteEnter`
    * same as `beforeEnter` in route 
  * component function `beforeRouteUpdate`
    * will be invoked when url changes, including query params
  * `beforeRouteLeave`
    * different lifecycle

* Global guards & meta fields

  * can directly apply `beforeEnter` function on router
    * `router.beforeEach(...)`
    * it will be applied to all route nevigation operations
  * Meta fields - extra information attched to route
    * `{path: '...', meta: {isAuthRequired: true}}`
    * Retrieve meta feature `route.meta.isAuthRequired`

* `afterEach` - router function

  * ```js
    router.afterEach((to, from) => { // it does not have "next, because the nevigation is finished"
    	...  
    })
    ```

## Connecting to Servers through HTTP

* todo

## Vuex

* installation

  * `npm install vuex --save`

* Simple store 

  * ```js
    import Vuex from 'vuex';
    Vue.use(Vuex);
    const store = new Vuex.Store({
      state: {
        // write initial states here
      },
      getters: {
        <getterName>: (state, getters) => { // state and getters will be automatically passed by Vuex
      		...
    		},
        <getter2Name>: (...) => { // getter with customized params
      		return function(param1, ...) => ...;
    		}
      },
      mutations: {
      	<SetterName>(state) {
          ...
        },
        <Setter2Name>(state, payload) { // payload will be the parameter when invoking the setter
          // do not invoke getter inside setter
          // we can use action, which will be shown in following lecture
          
        }
      }
    })
    
    new Vue({
      ...,
      store, store
    })
    ```

* retrieve state

  * `this.$store.state.<stateName>`

  * better put it into computed state:

    * ```js
      ...
      computed: {
        <propName>: {
          get: () => {this.$store.state.<stateName>},
            ... // can declare set function later
        }
      }
      ```

  * use predefined getter in store

    * `this.$store.getters.<getterName>`
    
    * Use `mapGetters` 
    
      * ```js
        import { mapGetters } from 'vuex';
        ...
        computed: {
          ...mapGetters([
            '<stateName>',
            <newStateName>: '<stateName>' // also can change name, usually for solving name conflict
            ...
          ])
        }
      

* Change state

  * object or any reference type can be directly update - **not encouraged**

  * primitive type need setter function - **still not encouraged**

    * ```js
      ...
      computed: {
        <propName>: {
          get: () => {this.$store.state.<stateName>},
          set: (value) => {this.$store.state.<stateName> = value;}
        } 
      }
      ```

  * otherwise using mutations

    * `this.$store.commit('<setterName>', { <property1>: ..., <property2>: ..., ...})` - second param will be stored in `payload`
    * use  `mapMutations`
    
      * ```js
        import { mapMutations } from 'vuex';
        ...
        methods: {
          ...mapMutations(['<setterName>', ...])
        }

* good way to improve the structure of code in mutations implementations - easy to track, avoid typo problems

  * use constants for mutations types

    * Define constant name in the separate file:

      * `export const <SETTER_NAME> = '<setterName>'`

    * ```js
      import { <SETTER_NAME> } from '...';
      
      ...
      
      mutations: {
        [<SETTER_NAME>]: (...) => { ... }
      }
      ```

    * when invoking it

      * ```js
        import { <SETTER_NAME> } from '...';
        
        ...
        this.$store.commit(<SETTER_NAME>, { ... });
        ```

* actions

  * Usually, we won't add async code in mutations, because it will make the code hard to monitor and mantain

  * we use action to handel async codes

    * ```js
      
      const store = new Vuex.Store({
      ...  
      actions: {
        <actionName>(context, payload) { // can have same name with mutation
        	... // do some async operations
        	context.commit(<mutationName>, payload)
      	} 
      }
      ...
      ```

    * Invoking - `this.$store.dispatch('<actionName>', {...})`

    * also has mapping function - `mapActions` - put it in methods part

    * Suggestion: always use action whatever it is sync or async function

* module

  * Dividing store into several substore, with different purposes and namespaces

  * states with same name in different substorm can be invoked at the same time (if you want)

    * because all names in modules will be added to global namespace

  * demo

    * ```js
      // store.js file
      
      import ModuleA from '...';
      import ModuleB from '...';
      
      const store = new Vuex.Store({
        modules: {
          moduleA: ModuleA,
          moduleB: ModuleB
        },
        state: { // so this state becomes to "root state"
          ...
        },
        ...
      })
      ```

    * ```js
      // ModuleA.js file
      
      export default {
        namespaced: <true|
        >, // enable it to avoid the name confliction
        state: { // this will match to "state" param of getters and mutations in this file
          ...
        },
        getters: {
          <getterName>(state, getters, rootState, rootGetters) { 
            // rootState will match to state in store.js file
            // rootGetters will match to getters in store.js file
            ...
          }
        },
        actions: {
          <actionName>({state, commit, rootState, rootGetters}) {
            ...
            commit('<mutationName>', {...}) // can directly access mutations in this file
            commit('<mutationName>', {...}, {root: true}) // access root mutations (similar to actions)
          }
        },
        mutations: {
          <mutationName>(state) { // cannot directly access rootstate in mutations
            ...
          }
        }
      }
      ```

  * Namespaced

    * enable it to avoid the name confliction

      * if not enabled, all functions will be directly injected into global namespace
        * can directly access by
          * `...mapXXX([ '<getterName>', ... ])... `
          * `this.$store.getters.<getterName>`
          * `this.$store.commit('<mutationName>', {...})`
          * `this.$store.dispatch('<dispatchName>', {...})`

      * if enabled, 
        * `...mapXXX([ '<moduleName>/<getterName>', ... ])...`
          * alternative way 1 - `...mapXXX('<moduleName>', {'<newName>': '<getterName>', ... })...`


* 







