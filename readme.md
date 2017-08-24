## Alloy ListItem Selection Re-usable Widget (with "Other" optional support)

This is a Axway Titanium Alloy Widget wrapping some standard components in order
to create a re-usable Widget for a single choice selection within a ListView with
a support for a "Other" entry.

Here we are using one Ti.UI.ListView, one Ti.UI.ListSection and multiple Ti.UI.ListItems.

We've tried to make the API as simple and intuitive as possible but we're opened for
pull requests too.

At the moment we only support a single selection mode but we could work on a multiple
selection mode in the future.

![screencast](https://cdn-pro.droplr.net/files/acc_244709/qacXm2)

## Alloy implementation

```xml
<Alloy>
    <Widget id="myelement" src="uk.co.cyber-duck.select"
        options="Foo|Bar|Lorem|Ipsum"
        onOptionSelected="myOptionSelectedHandler"
        hasOther="true|false" otherTitle="Another thing" onOtherSelected="myOtherSelectedHandler" />
</Alloy>
```

`hasOther` has to be defined upon Widget instanciation but `options` and `otherTitle` can be defined at both XML View level or JS Controller level:

```js
$.myelement.setOptions(["Foo","Bar","Lorem","Ipsum"]);
$.myelement.setOtherTitle("Another thing");
```

```xml
<Alloy>
    <Widget src="uk.co.cyber-duck.select" options="Foo|Bar|Lorem|Ipsum" otherTitle="Another thing" ... />
</Alloy>
```

## Public Methods

### Setters

* `$.myelement.setOptions(options: Array<String>)`
* `$.myelement.setOtherTitle(title: String)`
* `$.myelement.setOtherSubtitle(subtitle: String)`

### Getters

* `$.myelement.getValue()`
* `$.myelement.getOtherValue()`
* `$.myelement.getSelectedOption()`

### Widget Functions

* `$.myelement.selectOptionAtIndex(index: Integer)`
* `$.myelement.unselectAllOptions()`

## Public Events

### `optionSelected(e)`

Triggered when any of the option is selected.

#### Context

Defaults [from the SDK](https://docs.appcelerator.com/platform/latest/#!/api/Titanium.UI.ListView-event-itemclick) `itemclick` event:

* `e.source` --> Ti.UI.ListView
* `e.section` --> Ti.UI.ListSection
* `e.sectionIndex`
* `e.itemIndex`
* `e.itemId`
* `e.bindId`

Added context variables from our Widget:

* `e.selectedOption` --> Ti.UI.ListItem
* `e.value` --> String

### `otherSelected(e)`

Triggered when the `hasOther` parameter is set to `true` and when the last "Other" option is selected.

#### Context

Defaults [from the SDK](https://docs.appcelerator.com/platform/latest/#!/api/Titanium.UI.ListView-event-itemclick) `itemclick` event:

* `e.source` --> Ti.UI.ListView
* `e.section` --> Ti.UI.ListSection
* `e.sectionIndex`
* `e.itemIndex`
* `e.itemId`
* `e.bindId`

Added context variables from our Widget:

* `e.otherTitle` --> String
* `e.otherSubtitle` --> String

## Bonus

A good implementation of `otherSelected` could be to open a new `Ti.UI.Window` in order to ask the
user more details about that "Other" entry:

Assuming you have a `controllers/textarea`:

```xml
<Alloy>
    <Window title="Please specify">
        <LeftNavButton platform="ios">
            <View>
                <Button title="Back" onClick="doBack" />
            </View>
        </LeftNavButton>
        <TextArea id="textareaElement" />
    </Window>
</Alloy>
```

```js
var args = $.args;

if (args.value) {
    $.textareaElement.setValue(args.value);
}

function doBack() {
    $.trigger("closed", {
        "value": $.textareaElement.getValue()
    });
    $.getView().close();
}
```

You could have:

```xml
<Alloy>
    <Widget id="myelement" src="uk.co.cyber-duck.select"
        hasOther="true" onOtherSelected="otherSelected" />
</Alloy>
```

```js
function otherSelected(e) {
    var controller = Alloy.createController("textarea", { "value": "Some initial copy" });
    controller.addEventListener("closed", function(param){
        $.myelement.setOtherSubtitle(param.value);
    });
    myCurrentWindow.openWindow(controller.getView());
}
```

You get the idea ;)

## License

```
Copyright 2017 Cyber-Duck Ltd

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
