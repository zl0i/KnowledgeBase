## Инструкция для подстройки интерфейса под разные плотности пикселей в Qt/qml.

Существуют несколько типов плотности экрана:
1. ldpi - 120 dpi

2. mdpi - 160 dpi

3. hdpi - 240 dpi

4. xhdpi - 320 dpi

5. xxhdpi - 480 dpi

Стандартом является mdpi, поэтому перерасчет пикселей производится именно через это количество dpi. Google предлагает использовать dip или dp (Density-independent Pixels) - независимый от плотности пиксель. Для этого дизайн приложения должен рисоваться в mdpi, а при верстке необходимо использовать формулу px = (mdpi_px * dpi) / 160. В этой формуле мы пересчитываем пиксели для разных dpi. Также следует учесть, что для десктопов (которые обычно ldpi) перерасчет не производится. Также в macOS и IOS операционная система сама подстраивает плотность пикселей на основе логического разрешения.

Инструкция для ручной подстройки в qml:

1. Узнаем dpi экрана командой Screen.pixelDensity*25.4

2. Записываем в переменную: property var dpi: Screen.pixelDensity*25.4

3. Создаем функцию для пересчета: 

```js
function dp(px) {
    if(Qt.platform.os === "windows") {
    	return px;
    }
    return (px * dpi)/160
}
```

4. При создании элемента используем функцию. Пример:

```js
Rectangle {
    width: dp(40)  // например для dpi = 240(hdpi), будет 60 
    height: dp(80) // например для dpi = 240(hdpi), будет 120
}
```


Для автоматической подстройки необходимо в main.cpp указать атрибут:  

```c++
	QCoreApplication::setAttribute(Qt::AA_EnableHighDpiScaling);
```

однако это не проверялось на 320 и 480 dpi.

