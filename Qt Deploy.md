Чтобы создать папку с необходимыми для запуска приложения библиотеками необходимо выполнить следующие действия:
1. Собираем релизный проект, копируем exe файл релиза в предварительно созданную папку.

2. Запускаем консоль Qt (Пуск - все программы -  Qt - Qt 5.11 for Desktop)

3. Заходим в этой консоли в ранее созданную папку (где лежит наш exe-файл)

4. Прописываем команду:
	```
G:\Qt\5.11.3\mingw53_32\bin\windeployqt.exe . //точка обязательна
	```
	
	
	Эта команда скопирует все необходимые библиотеки для Qt проекта. После чего программу можно использовать отдельно от Qt.

Описание аргументов windeployqt.exe:
	https://doc.qt.io/QT-5/windows-deployment.html

Для автоматического деплоя после сборки проекта необходимо в .pro файл добавить

```
DESTDIR = ../Deploy
QMAKE_POST_LINK += $$(QTDIR)/bin/windeployqt $$DESTDIR
```

если проект использует qml, необходимо указать аргумент 

```
--qmldir $$OUT_PWD/../$$QMAKE_PROJECT_NAME
```


Для создания инсталлятора необходимо выполнить следующие действия (предварительно должен быть установлен QtInstallerFramework): 
1. Создаем папку config с файлом config.xml (необходимо взять из примера)

2. Создаем папку packages c подпапками компонентов (com.vendor.example) в каждой из которой есть папки data и meta 
  (в папку data необходимо скопировать деплой проекта (или его составляющие), в папке meta должен быть файл  package.xml 
  с описание компонента (также могут быть файл скрипта, ui-файл и лицензия)

3. Запускаем консоль Qt (Пуск - все программы - Qt - Qt 5.11 for Desktop)

4. Прописываем команду:
	
	```
	G:\Qt\Tools\QtInstallerFramework\3.0\bin\binarycreator.exe -c config\config.xml -p packages NameInstaller.exe
	```
	
5. Для сборки оффлайн установщика необходимо прописать команду -f

6. Для сборки онлайн установщика необходимо создать папку repository и в консоли выполнить команду
	```
G:\Qt\Tools\QtInstallerFramework\3.0\bin\repogen.exe -p packages repository
	```
	
	
	и затем заново пересобрать инсталлер
	
7. После обновления компонентов необходимо изменить номер версии в package.xml и выполнить в консоли команду 
	
	```
	G:\Qt\Tools\QtInstallerFramework\3.0\bin\repogen.exe --update -p packages repository
	```
	
	Описание аргументов binarycreator.exe и repogen.exe:
		https://doc.qt.io/qtinstallerframework/ifw-tools.html

Описание тэгов config.xml:
	https://doc.qt.io/qtinstallerframework/ifw-globalconfig.html

Описание тэгов package.xml:
	https://doc.qt.io/qtinstallerframework/ifw-component-description.html

Структура папок:
	config
		-config.xml - файл настроек установщика
	packages 
		-com.vendor.componentA - папка с компонентов в формате обратного 
			-data - папка с данными самого компонента
			-meta - папка с метоинформацией
				-package.xml - файл настройки компонента, обязательный
				-page.ui - файл доп. странички в установщике
				-script.js - скрипт, выполнябщий доп. операции
				-lisence.txt - лицензия
		-com.vendor.componentA.subcomponentA - подкомпонент A
			-data
			-meta
		-com.vendor.componentA.subcomponentB - подкомпонент B
			-data
			-meta
	repository
		-Updates.xml - файл с метаданными о каждом компоненте
		-com.vendor.componentA - папка с архивом(архивами) компонентов
		-com.vendor.componentA.subcomponentA - папка с архивом(архивами) компонентов
		-com.vendor.componentA.subcomponentB - папка с архивом(архивами) компонентов
		
	