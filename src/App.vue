<template>
    <div class="checklist-container" v-if="showChecklist">
        <form @submit.prevent="revise">
            <h1>JSON  >>>  Checklist</h1>
            <Button type="submit">Revise</Button>
            <div v-html="list" class="checklist" @click="todoclick"></div>
        </form>
    </div>
    <div class="editor" v-else>
        <form @submit.prevent="handleSubmit">
            <h1>JSON  >>>  Checklist</h1>
            <Button type="submit">Create Checklist</Button>
            <TextAreaInput
                label=''
                v-bind:editView="editView"
                v-model="model.value"
            />
        </form>
    </div>
</template>
<script>
import TextAreaInput from "move-ui-vue/src/components/base/TextAreaInput.vue";
import Button from "move-ui-vue/src/components/base/Button.vue";

export default {
    name: 'App',
    components: {
        TextAreaInput,
        Button
    },
    data() {
        return {
            model: {
                value: `{"employee": {"name":"Bono","salary":56000,"married":true}}`
            },
            editView: true,
            savable: true,
            showChecklist: false,
            list: ""
        }
    },

    methods: {
        jsonToHtmlList(json){
            return this.objToHtmlList(JSON.parse(json));
        },

        objToHtmlList(obj){
            var self = this;
            if (obj instanceof Array) {
                var ol = document.createElement('ol');
                for (var child in obj) {
                    var li = document.createElement('li');
                    li.appendChild(self.objToHtmlList(obj[child]));
                    if (( typeof obj[child] == 'string' ) || (typeof obj[child] == 'number' ) ){
                      li.classList.add('link')
                    }
                    ol.appendChild(li);
                }
                return ol;
            }
            else if (obj instanceof Object && !(obj instanceof String)) {
                var ul = document.createElement('ul');
                for (var child in obj) {
                  var li = document.createElement('li');
                  if (obj[child] instanceof Object){
                    li.appendChild(document.createTextNode(child + ": "));
                    li.appendChild(self.objToHtmlList(obj[child]));
                  } else {
                    var span = document.createElement('span');
                    span.appendChild(document.createTextNode(child + ": " + obj[child]));
                    li.classList.add('link')
                    li.appendChild(span);
                  }
                  ul.appendChild(li);
                }
                return ul;
            }
            else {
              var span = document.createElement('span');
              span.appendChild(document.createTextNode(obj));
              return span;
            }
        },

        handleSubmit(event){
            var list = this.jsonToHtmlList(this.model.value)
            this.list = list.outerHTML;
            this.showChecklist = true;
        },

        revise(event){
            this.showChecklist = false;
        },
        todoclick(event){
          event.target.classList.toggle('done');
        }
    }
}
</script>

<style lang="scss">
/* Box sizing rules */
*,
*::before,
*::after {
  box-sizing: border-box;
}

/* Remove default margin */
body,
h1,
h2,
h3,
h4,
p,
figure,
blockquote,
dl,
dd {
  margin: 0;
}

/* Remove list styles on ul, ol elements with a list role, which suggests default styling will be removed */
ul[role='list'],
ol[role='list'] {
  list-style: none;
}

/* Set core root defaults */
html:focus-within {
  scroll-behavior: smooth;
}

/* Set core body defaults */
body {
  min-height: 100vh;
  text-rendering: optimizeSpeed;
  line-height: 1.5;
}

/* A elements that don't have a class get default styles */
a:not([class]) {
  text-decoration-skip-ink: auto;
}

/* Make images easier to work with */
img,
picture {
  max-width: 100%;
  display: block;
}

/* Inherit fonts for inputs and buttons */
input,
button,
textarea,
select {
  font: inherit;
}

/* Remove all animations, transitions and smooth scroll for people that prefer not to see them */
@media (prefers-reduced-motion: reduce) {
  html:focus-within {
   scroll-behavior: auto;
  }

  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
</style>
<style lang="scss">
@import "move-ui-vue/dist/lib/move-ui-vue.css";
@import "move-ui-vue/src/assets/styles/styles.scss";
/* Layout */
#app > div {
  display: grid;
  grid-template-columns: 1fr;
  grid-template-rows: 60px 1fr;
  width: 80%;
  height: 90vh;
  margin: 1em 8% 0 12%;
  .checklist-container,
  form {
    display: grid;
    grid-template-columns: 2fr 200px;
    grid-row: 1;
    h1 {
      grid-row: 1;
      grid-column: 1;
    }
    button {
      grid-row: 1;
      grid-column: 2;
    }
    .checklist,
    .field {
      grid-row: 2;
      grid-column:1 /span 2;
      margin-top: 2em;
      margin-left: 1em;
      textarea {
        grid-column: 1/span 5;
        min-height: 80vh;
      }
    }
  }
}

/* Layout */
#app {
  h1 {
    text-align: left;
    letter-spacing: -0.08em;
  }
  textarea {
    font-family: monospace !important;
    font-size: 1em !important;
    text-align: left !important;
    white-space: pre !important;
    word-spacing: normal !important;
    word-break: normal !important;
    word-wrap: normal !important;
    line-height: 1em !important;
  }
  .checklist {
    list-style:	disc outside none;
    ul,
    ol {
      list-style-type: circle;
      list-style-position: inside;
      margin-left: 15px;
      li.link:first-child {
        margin-top: 1em;
        border-top: 1px solid $black;
      }
      li.link:last-child {
        margin-bottom: 1em;
      }
      li.link {
        list-style: none;
        border-left: 1px solid $black;
        border-right: 1px solid $black;
        border-bottom: 1px solid $black;
        padding: 12px 6px 12px 12px;
        &:before {
          display: inline;
          padding: 4px 14px;
          text-align: center;
          content: ' ';
          border: 1px solid $black;
          transform: translateY(10px);
          pointer-events: none;
        }
        &:hover {
          background-color: $gray4;
          cursor: pointer;
        }
        span {
          padding: 10px;
          pointer-events: none;
        }
        &.done {
          background-color: rgba($green, 40%);
          span {
            text-decoration: line-through !important;
          }
          &:before {
            content: '✓';
            padding: 4px 8px;
          }
        }
      }
    }
  }
}
</style>
