# v-icon-picker

## Preview

![ex](https://user-images.githubusercontent.com/84322691/120912065-5f4a8980-c6b6-11eb-87dc-2ba744677316.gif)

## Installation
```
npm i v-icon-picker
```
## Install Material Design Icons & Bootstrap
```
npm install @mdi/font bootstrap -D
```

### Usage
```
import "bootstrap-vue/dist/bootstrap-vue.css"; // required for css for v-icon-picker
import '@mdi/font/css/materialdesignicons.css' //required for get list mdi Icon
import VueIconPicker from "v-icon-picker";

Vue.use(VueIconPicker);
```
```
<v-icon-picker v-model="icon" ></v-icon-picker>
```

### Props
* v-model: Currently selected icon
* text: Text of button
* type: Color of button and list icon inside
["primary","success","secondary","danger","warning","info","light", "dark","default"]
Default: "dark"
* direction: direction show popup select icon
["down", "up", "left", "right"]
Default: down
* outline: isButton Outline. Default "true"
* rowsize: Number rows of table icon. Default 4
* colsize: Number cols of table icon. Default 4
* @select: Select icon event
