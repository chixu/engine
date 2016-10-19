## Key tags
* [include](#include)
* if
* for
* template
* placeholder

## config
* the processor will first read _commons/config.json
* the process will then read `game`/config.json and the value will be overridden.

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
## context
the following infomation will be token in the processor
* lang (en,cs,jp...)
* target (dev, uat, prod, staging, webuat, webprod, webstaging)
* platform (web, mobile)

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

## placeholder

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
