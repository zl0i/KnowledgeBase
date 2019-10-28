Атрибуты объектов QML должны быть всегда структурированы в следующем порядке:
	id
	property declarations
	signal declarations
	JavaScript functions
	object properties
	child objects
	states
	transitions

Для лучшей читаемости необходимо разделять эти части пустой строкой.	

```js
Rectangle {
    id: photo  // id on the first line makes it easy to find an object
    property bool thumbnail: false     // property declarations
    property alias image: photoImage.source

    signal clicked                   // signal declarations

    function doSomething(x)        // javascript functions
    {
        return x + photoImage.width
    }

    color: "gray"          // object properties
    x: 20                 // try to group related properties together
    y: 20
    height: 150
    width: {             // large bindings
        if (photoImage.width > 200) {
            photoImage.width;
        } else {
            200;
        }
    }

    Rectangle {          // child objects
        id: border
        anchors.centerIn: parent; color: "white"

        Image {
            id: photoImage
            anchors.centerIn: parent
        }
    }

    states: State {                // states
        name: "selected"
        PropertyChanges { target: border; color: "red" }
    }

    transitions: Transition {       // transitions
        from: ""
        to: "selected"
        ColorAnimation { target: border; duration: 200 }
    }
}
```

Групповое свойство:	

```js
Rectangle {
    anchors { left: parent.left; top: parent.top; right: parent.right; leftMargin: 20 }
}
```

```js
Text {
	text: "hello"
	font { bold: true; italic: true; pixelSize: 20; capitalization: Font.AllUppercase }
}
```

Если список содержит только один элемент, необходимо опускать квадратные скобки.

```js
states: State {
    name: "open"
    PropertyChanges { target: container; width: 200 }
}
```

Если сценарий представляет собой одно выражение, рекомендуется писать его встроенным:

```js
Rectangle { color: "blue"; width: parent.width / 3 }
```

Если скрипт имеет длину более пары строк или может использоваться различными объектами, рекомендуется создать функцию и вызвать ее следующим образом:

```js
function calculateWidth(object)
{
	    var w = object.width / 3
	    // ...
	    // more javascript code
	    // ...
	    console.debug(w)
	    return w
}

Rectangle { color: "blue"; width: calculateWidth(parent) }
```

Для длинных скриптов необходимо помещать функции в их собственный файл JavaScript и импортировать следующим образом:	

```js
import "myscript.js" as Script

Rectangle { color: "blue"; width: Script.calculateWidth(parent) }
```