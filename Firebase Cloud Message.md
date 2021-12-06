# Добавление Firebase Cloud Message в Qt android

Идем на сайт https://console.firebase.google.com/u/0/ 
Регистрируемся там и создаем проект, вводим название проекта (любое), рекомендуется подключить аналитику, выбираем аккаунт для аналитики и нажимаем создать проект, ждем.

Далее щелкаем на значок Android (создание приложения) и вводим имя пакета (очень важно, имя пакета должно совпадать во всем приложении), по желанию также вводим псевдоним приложения (название). Щелкаем далее, скачиваем файл конфигурации google-serivce.json, нажимаем далее. Необходимо добавить SDK, создаем приложение Qt для android, создаем шаблон проекта (папка android), перемещаем google-service.json в папку andorid.открываем build.gradle и прописываем:
	1) в buildsript в разделе dependencies:

```java
classpath 'com.google.gms:google-services:4.0.1'
```

2) в android 		

```java
defaultConfig {
	applicationId "com.firebase.test" //ВАЖНО!!! имя пакета которое писали при создании приложения
	vectorDrawables.useSupportLibrary = true
	multiDexEnabled = true
} 
```

3) в конце файла добавляем:

```java
dependencies {
   implementation fileTree(dir: 'src', include: ['*.jar'])
   implementation 'com.google.android.gms:play-services-base:15.0.1' //google-play сервисы
   implementation 'com.google.firebase:firebase-core:16.0.1' // ядро firebase
   implementation 'com.google.firebase:firebase-messaging:17.1.0' // собственно для доставки уведомлений
   implementation 'com.google.firebase:firebase-analytics:17.2.0' //подключенная аналитика ( если не включали, можно не подключать)
}
apply plugin: 'com.google.gms.google-services'
```

Далее идем в AndroidManifest.xml и меняем там имя пакета на наше.
После чего можно собирать приложение и запускать.
Пока собирается приложении, на сайте Firebase нажимаем далее, сайт будет искать только что созданное запущенное приложение. Как только приложение запуститься сайт должен увидеть и написать что подключение произошло. После этого уже можно отправлять уведомления, но если приложение запущено, уведомления отображаться не будут.

Для того что бы уведомления приходили во время работы приложения, необходимо добавить сервис в AndroidManifest.xml:

```xml
<service android:name="com.firebase.test.Notification" android:exported="true"> //ВАЖНО!!! com.firebase.test - имя пакета, Notification - java класс
    <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
	</service>
```

в папке android создаем папку src и в ней папки согласно названию пакета, пример (если пакет com.firebase.test)
	-android
	    -src
	        -com
		    -firebase
			-test
			    Notification.java

в классе прописываем 

```java
package сom.firebase.test; // ВАЖНО!!! имя нашего пакета

import android.app.NotificationChannel;
import android.app.NotificationManager;

import android.support.v4.app.NotificationCompat;
import android.os.Build;
import android.content.Context;
import android.content.Intent;
import android.media.RingtoneManager;
import android.net.Uri;

import android.util.Log;

import com.google.firebase.messaging.FirebaseMessagingService;
import com.google.firebase.messaging.RemoteMessage;

import сom.firebase.test.R; //ВАЖНО!!! путь к ресурсам

public class Notification  extends FirebaseMessagingService {
    private static final String TAG = "FirebaseMsgService";
    
    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {               
         sendNotification(remoteMessage.getNotification().getBody());
    }

    @Override
    public void onNewToken(String mToken) {
        super.onNewToken(mToken);        
        Log.e(TAG + " TOKEN", mToken);
    }   

    private void sendNotification(String messageBody) { //функция для создания и отображения уведомления
        final String channelId = getString(R.string.default_notification_channel_id); // должен быть создать файл string.xml в папке android/res/values 
        Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);        

        NotificationCompat.Builder notificationBuilder =
                new NotificationCompat.Builder(this, channelId)
                        .setSmallIcon(R.drawable.ic_stat_ic_notification)
                        .setContentTitle("TestFirebase")
                        .setContentText(messageBody)
                        .setSound(defaultSoundUri);
                         NotificationManager notificationManager =
                            (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            NotificationChannel channel = new NotificationChannel(channelId,
                    "Channel human readable title",
                    NotificationManager.IMPORTANCE_DEFAULT);
            notificationManager.createNotificationChannel(channel);
        }
        notificationManager.notify(0,  notificationBuilder.build());
        }   

}   
```

Описание функций:

​	onNewToken - при первом запуске приложения будет создаваться токен, по нему идентифицируется пользователь
​	onMessageReceived - всегда когда приложение запущено и приходит уведомление идет обращение к этой функции
​	sendNotification - функция для создания и отображения уведомления
​		

Более подробно:
	https://firebase.google.com/docs/cloud-messaging/android/client?authuser=0 - подключение firebase cloud message к android 
	https://developer.android.com/guide/topics/ui/notifiers/notifications - создание уведомлений в android 
	https://github.com/zl0i/QtFirebase - готовый проект на GitHub

# Отправка уведомлений 

Отправка уведомлений может осуществляться несколькими способами:

1) через консоль firebase:
	Идем в Cloud Message - Send first Message - Новое уведомление. Здесь создаем и настраиваем уведомление.

2) через post запрос (legacy):
	url: https://fcm.googleapis.com/fcm/send
	header:
		Content-Type: application/json
		Authorization: key=(old key server) //берётся из настроек проекта, вкладка cloud message
	body:	

```json
{
    "name": "", //имя сообщение
    "data": {  //внутренние данные (любые поля)
        string: string,
    },
    "notification": { // тело уведомления
        "title": "aaaaa2", //заголовок
        "body": "aaaaaa3", //текст
        "image": "https://s...00.png" //картинка
    },
    "to": "eE9f258CM_A:A.....3uVaTx" //токен пользователя
}
```

3) через post запрос (new):
	Необходима авторизация через OAuth 2.0  

url: https://fcm.googleapis.com/v1/projects/{project-name}/messages:send

header:
	Content-Type: application/json
	Authorization: Bearer  (new key server) //берётся из настроек проекта, вкладка cloud message

body:

```json
{
"name": string,
"data": {
	string: string,  
},
"notification": {
 	object (Notification)
},
"android": {
 	object (AndroidConfig)
},
"webpush": {
	object (WebpushConfig)
},
"apns": {
 	object (ApnsConfig)
},
"fcm_options": {
	object (FcmOptions)
},
// Union field target can be only one of the following:
"token": string,
"topic": string,
"condition": string
// End of list of possible types for union field target.
}
```
4) через XMPP (legacy)
5) через SDK (доступно для Node.js, Java, Go, C#, Python)

Более подробно:
	https://firebase.google.com/docs/cloud-messaging/server?authuser=0 - Server environment
	https://firebase.google.com/docs/reference/fcm/rest/v1/projects.messages?authuser=0 - rest api