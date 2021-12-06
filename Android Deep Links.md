**Android Deep Links**

Android Deep Links позволяют обрабатывать ссылки через приложение. Один из самых распространённых сценариев, когда пользователь открывает ссылку (через другое приложение или считывает QR-код) и это приводит не к открытию сайта, а к открытию приложения на определенной странице.

Ссылки бывают трех типов:

1. Ссылка на приложение (открываться **только** в приложении и нигде больше):

> В этом случае в AndroidManifest.xml в блок activity необходимо добавить Intent-filter:
>
> ```xml
><intent-filter>
>  <data android:scheme="com.qt.example.test"/>  // схема по которой приложение будет понимать что идет запрос к нему
>   <action android:name="android.intent.action.VIEW"/> // запускать View
>     <category android:name="android.intent.category.DEFAULT"/>
>    	<category android:name="android.intent.category.BROWSABLE"/> //приложение видно браузеру
>    </intent-filter>
> ```
> 
> Далее необходимо добавить в html файл:
>
> ```html
><!DOCTYPE html>
> <html>
> <script>
> 	window.location.replace(`com.qt.example.test://${window.location}`); //происходит переход к приложению
> 	setTimeout(() => {
>    		window.location.replace('market://details?id=com.qt.example.test'); //если не было перехода, то открывается Play Store
>   	}, 500)
>    </script>
>   </html>
> ```
> 
> В случае когда несколько приложений обрабатывают данную схему, пользователю будет предложено выбрать приложение.

2. Универсальная ссылка (открываться в браузере, либо если установлено приложение, то в нем):

   > В этом случае в AndroidManifest.xml в блок activity необходимо добавить Intent-filter:
   >
   > ```xml
   > <intent-filter android:autoVerify="true"> //необходимо для подтверждения домена
   > 	<data android:scheme="https://" android:host="example.com" android:path="/example"/>  
   > 	<action android:name="android.intent.action.VIEW"/> 
   > 	<category android:name="android.intent.category.DEFAULT"/>
   > 	<category android:name="android.intent.category.BROWSABLE"/>
   > </intent-filter>
   > ```
   >
   > Далее необходимо сгенерировать JSON файл подписи, это делается в Android Studio Tools->App Links Assistant и залить на собственный домен по пути https://example.com/.well-known/assetlinks.json
   >
   > После чего, при переходе по установленной ранее ссылки, пользователю будет предлагаться открыть ее в приложении.
   
3. Веб-ссылка (открывается только в браузере)

Deep Link можно получить в классе Activity:

   ```java
   Intent intent = getIntent();
   Uri appLinkData = intent.getData();
   if (appLinkData != null){
   	Log.d(TAG, appLinkData.toString());
   }
   ```

Стоит отметить, что Deep Links можно получить после установки приложения.

Существует специальный сервис Firebase Dynamic Links. Он позволяет создавать и тонко настраивать Deep Links, а также просматривать аналитику по кликам на ссылку. Еще Firebase Dynamic Links может генерировать ссылки, это может быть необходимо для получения информации откуда пользователь узнал о приложении. Запросы Firebase Dynamic Links ограничены 5 запросами на IP-адрес в секунду и 200 000 запросов в день. 

Полезные ссылки:

1. Подробнее об intents-filters - https://developer.android.com/guide/components/intents-filters
2. Подробнее об intents-filters в AndroidManifest.xml - https://developer.android.com/guide/topics/manifest/intent-filter-element
3.  Firebase Dynamic Links  - https://firebase.google.com/docs/dynamic-links
4. Firebase Analytics Dynamic Links - https://firebase.google.com/docs/dynamic-links/analytics
