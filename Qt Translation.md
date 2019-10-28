Для создания переводов в Qt необходимо оборачивать каждую строчку в  tr() - для Qt виджетов и qsTr() - для Qml виджетов. 

Далее для создания файлов перевода (.ts) необходимо использовать программу lupdate.exe поставляемую вместе с Qt.
Разберем на примере с qml файлами (актуально и для cpp).
В командной строке переходим в папку проекта и прописываем команду, пример: 	

```
G:\Qt\5.12.4\mingw73_64\bin\lupdate.exe main.qml -ts main_en.ts
```

команда создаст файл перевода (main_en.ts) для файла main.qml
Также можно указывать несколько файлов перевода сразу, пример:

```
G:\Qt\5.12.4\mingw73_64\bin\lupdate.exe main.qml -ts main_en.ts main_ru.ts main_fr.ts 
```

Также можно файл ресурсов с qml файлами, пример:

```
G:\Qt\5.12.4\mingw73_64\bin\lupdate.exe qml.qrc -ts main_en.ts main_ru.ts main_fr.ts
```

команда создаст файл перевода (main_en.ts) для файла main.qml
Также можно указывать несколько файлов перевода сразу, пример:	

```
G:\Qt\5.12.4\mingw73_64\bin\lupdate.exe main.qml -ts main_en.ts main_ru.ts main_fr.ts 
```

Также можно файл ресурсов с qml файлами, пример:	

```
G:\Qt\5.12.4\mingw73_64\bin\lupdate.exe qml.qrc -ts main_en.ts main_ru.ts main_fr.ts
```

После создания файлов перевода необходимо открыть программу linguist.exe (путь: G:\Qt\5.12.4\mingw73_64\bin)
В программе октрываем созданные ранее .ts файлы переводим каждую строку. После чего необходимо скомпилировать готовые файлы перевода (Файл-> Скомпилировать все)
Полученные файлы (.qm) необходимо добавить в файл ресурсов в проект.
Далее можно изменять переводы командами:	

```c++
QTranslator translator;
translator.load(":translation/main_de");
app.installTranslator(&translator);
```

Для Qt виджетов перевод изменится сразу. Для qml необходимо подать специальный сигнал, вызвав функцию engine.retranslate(); (данная функция была добавлена в Qt 5.10 и 
обновляет все привязки в qml слое (не только строки), в будущем разработчик обещает оптимизировать данную функцию).


Более подробная информация: https://doc.qt.io/QT-5/qtlinguist-index.html

Популярные страны и их сокращения:
Россия - ru
США - us
Германия - de
Франция - fr
Украина - ua
Казахстан - kz
Беларусь - by
Греция - gr
Испания - es
Египет - eg
Индия - in
Китай - cn
Великобритания - gb
Япония - jp
Австралия - au
Бразилия - br