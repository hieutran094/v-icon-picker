# v-icon-picker

## Installation
```
npm i v-icon-picker
```

### Usage
```
import VueIconPicker from "v-icon-picker";
and then:
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