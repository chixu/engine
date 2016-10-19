# Layout Preprocessor

* [config](#config)
* [Keys](#Keys)
* [context](#context)
* [if tag](#if tag)
* [config](#config)

## config
* the processor will first read _commons/config.json
* the processor will then read `game`/config.json and the value will be overridden.

if we have a commons config.json
``` javascript
{
    "deployment":{
        "default":{
            "uiVersion":"1.0",
            "layoutVersion":"1.0",
            "defaultLoginURL":"http://www.[%%DOMAINLINK%%]/Login/v3.aspx",
            "apiName":"Mobile"
        }
    }
}
```

add a game config.json
``` javascript
{
    "deployment":{
        "default":{
            "uiVersion":"2.0",
            "layoutVersion":"2.0"
        }
    }
}
```
the processor will get
``` javascript
{
    "deployment":{
        "default":{
            "uiVersion":"2.0",
            "layoutVersion":"2.0",
            "defaultLoginURL":"http://www.[%%DOMAINLINK%%]/Login/v3.aspx",
            "apiName":"Mobile"
        }
    }
}
```
## Keys
* Keys in the xml can be written as {%key%}, the key will be placed by values in the preprocessor.
* Key value pair is read from the [config](#config) `deployment` value
* the default value will be overriden by the environment value

sample `config.json`
``` javascript
{
    "deployment":{
        "default":{
            "uiVersion":"1.0",
            "url":"defaulturl"
        },
        "uat":{
            "url":"uaturl"
        },
        "prod":{
            "url":"produrl"
        }
    }
}
```
in uat we will have

``` javascript
"uiVersion":"1.0"
"url":"uaturl"
```
therefore, if we have `{%url%}` in our `layout.xml`, it will be replaced by `uaturl`


## context
the following infomation will be token in the processor
* `lang` (en,cs,jp...)
* `target` (dev, uat, prod, staging, webuat, webprod, webstaging)
* `platform` (web, mobile)

## if tag
generate the content by if condition
* check if value is equal `<if name="lang" value="en" />`
* check if value is in `<if name="lang" in="en,cs" />`

if our lang is `en` and we have
``` xml
<if name="lang" value="cs">
  <label>cs</label>
</if>
<if name="lang" value="en">
  <label>en</label>
</if>
<if name="lang" in="en,cs">
  <label>en or cs</label>
</if>
```
we will get
``` xml
<label>en</label>
<label>en or cs</label>
```
## for tag
generate a series of its content
* key attribute: `from`, `to`, `step`
* key tag: `content` (looping content), `arguments` (for replace parameters)
* {%index%} will be replaced by the interator.

if we have
``` xml
<for from="1" to="3" step="1">
  <content>
    <label id="{%index%}">component{%index%}</label>
    <label x="{%x%}" y="{%y%}"/>
  </content>
  <arguments> 
    <argument id="default" x="100" y="10"/>
    <argument id="3" x="33" y="333"/>
  </arguments>
</for>
```
after compile we will get
```xml
<label id="1">component1</label>
<label x="100" y="10"/>
<label id="2">component2</label>
<label x="100" y="10"/>
<label id="3">component3</label>
<label x="33" y="333"/>
```

## include tag

* uses `<include ref="other.xml" />` tag
* uses custom properties, 
if we have a entry file:

```xml
<app>
  <include ref="mainScene.xml" id="main" background="0xfff" />
</app>
```

and other xml name `mainScene.xml`

```xml
<scene>
  <view>
    <img src="bg.png" />
  </view>
</scene>
```

after compile will get:

```xml
<app>
  <scene id="main" background="0xfff">
    <view>
      <img src="bg.png" />
    </view>
  </scene>
</app>
```
## template tag
we use template tag when we have many duplicate content, but only a few different attribute value is different(normally for multi lang image adjustment)
* key attribute in source xml: `id`, `ref` (template file path)
* other attribute in source xml: replace the key values in the template
* key attribute in template xml: `id`(since one template file may have more than one template tag)
* other attribute in template xml: default values in the template

if we have a `main.xml`
``` xml
<template ref="./template.xml" id="template1" x="1111" src="customized.png"/>
```
and a `template.xml` on the same directory
``` xml
<templates>
  <template id="template1" x="55" y="44" src="template1.png">
    <label value="template1"/>
    <layer>
      <img src="{%src%}" x="{%x%}" y="{%y%}"/>
    </layer>
  </template>
  <template id="template2" x="100">
    <label id="label" value="myTemplate"></label>
    <img src="myTemplate.png" x="{%x%}"/>
  </template>
</templates>
```
after compile, we will get
``` xml
<label value="template1"/>
<layer>
  <img src="customized.png" x="1111" y="44"/>
</layer>
```

## placeholder tag

*uses `placeholder` we can easily to replace the content in template xml which we uses `include` tag to include to our xml file.
* if the placeholder does not match to any of the placeholer in the destination file. the content of it will be taken. 

if we have a `main.xml`
```xml
<app>
  <include ref="./tmpl.xml">
    <placeholder id="myPlaceHolder1">
      <label>111111</label>
    </placeholder>
    <placeholder id="myPlaceHolder2">
      <label>222222</label>
    </placeholder>
  </include>
</app>
```

and we have a `tmpl.xml`
```xml
<view>
  <placeholder id="myPlaceHolder1"></placeholder>
  <label>View</label>
  <placeholder id="myPlaceHolder2"></placeholder>
  <placeholder id="defaultPlaceHolder">
    <label>defaultPlaceHolder</label>
  </placeholder>
</view>
```

after compile we will get
```xml
<app>
  <view>
    <label>111111</label>
    <label>View</label>
    <label>222222</label>
    <label>defaultPlaceHolder</label>
  </view>
</app>
```
