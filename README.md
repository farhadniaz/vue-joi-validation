# vue-joi-validation
A validation libraery for vuejs and ssr support which dosen't have any conlifct with nuxti18n and vue-i18n.

> Schema-based validation for vuejs using joi.

## Links

- 📘 [Joi documentation] (https://github.com/hapijs/joi/blob/v14.3.0/API.md)

## Features

- Model based
- Decoupled from templates
- Schema based
- Built upon Joi
- Minimalistic
- Support for collection validations
- Support for nested models
- Contextified validators
- Easy to use with custom validators (e.g. Moment.js)
- Support for function composition
- Validates different data sources: Vuex getters, computed values, etc.

## Installation

```bash
yarn add vue-joi-validation
```

```bash
npm install vue-joi-validation --save
```


You can import the library and use as a Vue plugin to enable the functionality globally on all components containing validation configuration.

```javascript
import Vue from 'vue'
import vueJoiValidation from 'vue-joi-validation'
Vue.use(vueJoiValidation)
```
## Custom Validation

For creating custom validation rules you have to folow [Joi extend](https://github.com/hapijs/joi/blob/v14.3.0/API.md#extendextension).

extend options for vue-joi-vallidation can be a Function, an Array of rule objects, or a rule object.

```javascript
import Vue from 'vue';
import vueJoiValidation, {Joi} from 'vue-joi-validation';
const options = {
    extend: {
        base: Joi.string(),
        name: 'string',
        language: {
            justalpha: 'just alphabetic allowed ',
        },
        rules: [{
            name: 'justalpha',
            validate(params, value, contextState, options) {
            
                let regPattern = new RegExp('^[a-zA-Z]*$');
                if (!regPattern.test(value)) {
                    return this.createError('string.justalpha', {}, contextState, options);
                }
                return value; // Everything is OK
            }
        }]
    }
};
Vue.use(vueJoiValidation,options);
```
## In Components
 To use vue-joi-validation in components you have to provide a computed property with `joiValidationSchemaObject`. The vue-joi-validation will automatically consider joivalidationSchemaObject keys as component data. This means that your validation schema is based on your component data keys.

 ```javascript
 data() {
        return {                                            
            nationalId: '',
            name: '',
            lastName: '',
            birthDay: '',
            birthCountry: '',
            passportCountry: '',
            passportNumber: '',
            passportExpireDate: '',
            birthDate: '',                        
            birthYear: '',
            birthMonth: '',                                                
            passportExpireYear: '',
            passportExpireMonth: '',
            passportExpireDay: '',            
        };
    },
  computed:{
        joiValidationSchemaObject() {
            return   this.$joi.object({                           
                nationalId          : this.$joi.id().nationalId(),
                name                : this.$joi.string().alphanum().required(),
                lastName            : this.$joi.string().alphanum().required(),
                birthDay            : this.$joi.number().integer().min(1).max(31),
                birthCountry        : this.$joi.string().required(),
                passportCountry     : this.$joi.string().required(),
                passportNumber      : this.$joi.number().required(),
                passportExpireDate  : this.$joi.date().required(),
                birthDate           : this.$joi.date().required(),
                birthYear           : this.$joi.required(),            
                birthMonth          : this.$joi.number().min(1).max(12).required(),
                passportExpireYear  : this.$joi.required(),
                passportExpireMonth : this.$joi.number().min(1).max(12).required(),
                passportExpireDay   : this.$joi.number().min(1).max(31).required()    
                }).with('name','lastName')
                 .without('password');
        }
    }
 ```



## Validation in Components

Whenever a data key which is declared in `joiValidationSchemaObject` changes `vue-joi-validation` validates it . You can get all validation errors in component using the `joiValidationErrors` property.

In components `joiValidate` is a function which performs validation programmatically, 
- validate all schema 
```
$ let validationResult = this.joiValidate()
```
- validate a key in data
```
$ let KeyNameValidationResult=this.joiValidate(keyName)
```

- All errors are availabe in template, you just have to check for the key that you want.
`joiValidationErrors` properties are an array of Joi erros.

```html
<div class="form-group" :class="{'has-error' : joiValidationErrors.lastName? true: false }">                    
    <input type="text"                                         
            name="lastName"                                    
            class="form-control"                
            v-model="lastName" />
    <template v-if="joiValidationErrors.lastName">
        <template  v-for="(validationError, index) in joiValidationErrors.lastName " >
                <span :key="index" v-if="validationError.type=='string.justalpha' " class="error-block">
                    just use alphabetic
                </span>                        
                <span :key="index" v-if="validationError.type=='any.empty' " class="error-block">
                    this filed is required 
                </span>
        </template>                                                
    </template>
</div>
```
or with `has function` 

```html
<div class="form-group" :class="{'has-error' : joiValidationErrors.lastName? true: false }">                    
    <input type="text"                                         
            name="lastName"                                    
            class="form-control"                
            v-model="lastName" />
    <template v-if="joiValidationErrors.lastName">
                <span :key="index" v-if="joiValidationErrors.lastName.has('string.justalpha')" class="error-block">
                    just use alphabetic
                </span>                        
                <span :key="index" v-if="joiValidationErrors.lastName.has('any.empty')" class="error-block">
                    this filed is required 
                </span>                                              
    </template>
</div>
```

## Nuxt usage 

`/plugins/vue-joi-validation.js`
```javascript
import Vue from 'vue';
import vueJoiValidation from 'vue-joi-validation';
const options={};
Vue.use(vueJoiValidation,options);
```

add to `nuxt.config.js` 
```javascript
   // Plugins
    plugins: [
        .
        .
        .     
        { src: '~/plugins/vue-joi-validation' }
    ]
```

For using Joi in server side or other purpose you can do more :
```javascript
import {Joi} from 'vue-joi-validation';

Joi.any().date();
const schema = {
    a: Joi.number()
};

const value = {
    a: '123'
};

Joi.validate(value, schema, (err, value) => { });
// err -> null
// value.a -> 123 (number, not string)
```
### Joi API
See the detailed [API Reference](https://github.com/hapijs/joi/blob/v14.3.0/API.md).


### Ioi extensions and useful libraries ==>[all joies](https://www.npmjs.com/search?q=joi)
[joi-date-extensions](https://www.npmjs.com/package/joi-date-extensions)
[joi-multiaddr](https://www.npmjs.com/package/joi-multiaddr)
[joi-phone-number](https://www.npmjs.com/package/joi-phone-number)
[enjoi](https://www.npmjs.com/package/enjoi)
[waterline-joi](https://www.npmjs.com/package/waterline-joi)
[joi-currency-code](https://www.npmjs.com/package/joi-currency-code)
[joi18nz](https://www.npmjs.com/package/joi18nz)
[restify-joi-middleware](https://www.npmjs.com/package/restify-joi-middleware)
[joi-timezone](https://www.npmjs.com/package/joi-timezone)
[joi-enums-extension](https://www.npmjs.com/package/joi-enums-extension)
[joi-x-i18n](https://www.npmjs.com/package/joi-x-i18n)
[vue-joi-validation](https://www.npmjs.com/package/vue-joi-validation)

