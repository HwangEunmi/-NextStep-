**프로젝트 소개**
-------

Google Developer Training 1.1 을 보다 AndroidManifest.xml과 Gradle에 관한 내용이 나왔습니다.

그래서 두 내용을 정리해보았습니다.

먼저 AndroidManifest.xml에 관한 내용입니다.

내용중에 AndroidManifest.xml과 상관없는 내용이 있을 수도 있습니다. 그런 내용은 제가 공부하면서 궁금한 점이 꼬리에 꼬리를 물었기때문에 같이 정리해서 그렇습니다.
그런 내용은 Skip 하셔도 됩니다!

프로젝트는 지속적으로 추가 될 예정입니다. 상단의 Star, Watching 버튼을 클릭하시면 구독 알림을 받으실 수 있습니다 :)

----------

[Google Developer Training 1.1 URL] 

https://google-developer-training.github.io/android-developer-fundamentals-course-concepts-v2/unit-1-get-started/lesson-1-build-your-first-app/1-1-c-your-first-android-app/1-1-c-your-first-android-app.html

https://codelabs.developers.google.com/codelabs/android-training-hello-world/index.html?index=..%2F..%2Fandroid-training#2

----------


**목차**
--

 - [프로젝트 소개](#프로젝트-소개)
 - [목차](#목차)
 - [전체 플로우](#전체-플로우)
  - [AndroidManifest.xml 란?](#안AndroidManifest.xml-란?)
     - [01.앱의 패키지 이름](#01.앱의-패키지-이름)
     - [02.앱의 구성요소](#02.앱의-구성요소)
        - [번외) Intent 란?](#)
        - [번외) 명시적 인텐트](#)
        - [번외) 암시적 인텐트](#)
     - [03.앱이 다른 부분에 액세스하기 위해 필요한 권한](#03.앱이-다른-부분에-액세스하기-위해-필요한-권한)
     - [04.앱에 필요한 하드웨어/소프트웨어 기능 알림](#04.앱에-필요한-하드웨어/소프트웨어-기능-알림)
        - [uses-feature](#uses-feature)
  

----------

**전체 플로우**
----------


**AndroidManifest.xml 란?**
------------------------------------------


----------

AndroidManifest 은 앱에 관한 필수 정보를 가지고 있는 파일이다. 

```java
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.test.emhwang"
    android:versionCode="1"
    android:versionName="1.0" >
    
    ...
    
</manifest>
```

매니페스트 파일은 여러가지 정보를 가지고 있지만, 특히 다음과 같은 내용들을 가지고 있어야 한다.

1) 앱의 패키지 이름 

2) 앱의 구성요소 (Ex. Activity, Service, Broadcast Receiver, Content Provider 등..)

3) 앱이 다른 부분에 액세스하기 위해 필요한 권한

4) 앱에 필요한 하드웨어/소프트웨어 기능 알림



**01.앱의 패키지 이름**
-------- 

먼저 앱의 패키지 이름에 대해서 살펴본다.


```java
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.test.emhwang"
    android:versionCode="1"
    android:versionName="1.0" >
    
   <activity android:name=".MainActivity">    
   
</manifest>
```

앱을 APK로 빌드할 때, Android 빌드도구는 package 특성값을 R.java 클래스의 네임스페이스로 이 이름을 사용한다.

즉, 이 이름을 앱 리소스에 액세스하는데 사용하며, 이때 매니페스트 파일에 선언되어 있는 클래스 이름을 함께 확인한다.

예를들어 위처럼 선언된 액티비티가 com.example.emhwang.MainActivity 인 것으로 인식된다는 것이다.


따라서 package 특성의 이름은 프로젝트의 기본 패키지 이름과 항상 일치해야 한다. 

그러나 APK가 컴파일되고 나면 이제 package 특성은 고유한 애플리케이션 ID를 나타내는데 쓰인다. 

즉, 빌드 도구가 위의 작업을 수행하고 나면, 프로젝트의 build.gradle 파일에 있는 applicationId 속성에 지정된 값으로 package 값을 대체한다. 
(build.gradle의 applicationId가 더 상위 버전)

그러므로 package값과 build.gradle 파일의 applicationId값을 동일하게 맞춰주는 것이 좋다. 


**02.앱의 구성요소**
-------- 

다음은 앱의 구성요소이다.

```java
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.test.emhwang"
    android:versionCode="1"
    android:versionName="1.0" >
    
   <activity android:name=".MainActivity">    
   
</manifest>
```

모든 Activity, Service, BroadcastReceiver, ContentProvider 들은 매니페스트에 해당하는 XML 요소를 선언해야 한다. 

이때 해당 컴포넌트들은 Intent로 활성화된다. 


**번외) Intent 란?**
-------- 

Intent는 메시징 객체로 구성요소 사이의 통신을 이루어주는 역할을 한다. 

앱이 Intent를 시스템에 발행하면 시스템은 각 앱의 매니페스트 파일에 선언된 Intent Filter를 참조하여 Intent를 처리할 수 있는 앱의 구성요소를 찾는다. 

그 후 일치하는 구성요소의 인스턴스를 시작하고, 해당 구성 요소에 Intent 객체를 전달한다. 

만약 두개 이상의 앱이 해당 Intent를 처리할 수 있는 경우, 사용자는 어떤 앱을 사용할지 선택할 수 있다. 

앱 구성요소는 Intent Filter를 몇개든 가질 수 있으며, 각 Intent Filter는 해당 구성 요소의 각 기능을 설명한다. 

```java
<activity android:name="ShareActivity">
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="text/plain"/>
    </intent-filter>
</activity
```

위의 예제는 ACTION_SEND 인텐트를 수신하여 작업할 수 있는 ShareActivity 클래스를 보여준다.

각 Intent Filter는 Intent의 작업과 데이터 및 카테고리를 기반으로 어느 유형의 Intent를 작업할 수 있는지 지정한다. 보통 암시적 인텐트에 쓰인다. 

Intent는 명시적 인텐트와 암시적 인텐트 두 종류가 있다. 


**번외) 명시적 인텐트**
-------- 

앱 내의 특정 액티비티나 서비스 등 특정한 앱 구성요소를 시작하는데 사용한다. 

일반적으로 앱 안에서 구성 요소를 시작할때 쓴다. 

(시작하고자 하는 구성요소의 클래스 이름을 알고 있기 때문에)

```java
Intent intent = new Intent(this, DownloadService.class);
intent.setData(Uri.parse(fileUrl));
startService(intent);
```

**번외) 암시적 인텐트**
-------- 

특정 구성 요소의 이름을 적진 않지만, 대신 수행할 작업을 선언하여 다른 앱의 구성 요소가 이를 처리할 수 있도록 해준다. 

```java
Intent intent = new Intent();
intent.setAction(Intent.ACTION_SEND);
intent.putExtra(Intent.EXTRA_TEXT, textMessage);
intent.setType("text/plain");

if (intent.resolveActivity(getPackageManager()) != null) {
    startActivity(intent);
}
```

stattActivity를 호출하면 시스템이 설치된 앱을 모두 살펴보고, 이런 종류의 인텐트를 처리할 수 있는 앱이 어떤것인지 알아본다.

이것을 처리할 수 있는 앱이 하나뿐이면 해당 앱이 즉시 열리고 이 앱에 인텐트가 주어지지만

인텐트를 허용하는 액티비티가 여러 개인 경우 시스템은 대화상자를 표시하여 사용자에게 앱을 선택하게 한다.


**03.앱이 다른 부분에 액세스하기 위해 필요한 권한**
-------- 

앱은 민감한 사용자 데이터 또는 특정 시스템 기능에 접근하기 위한 권한을 요청해야 한다.

```java
<manifest ... >
    <uses-permission android:name="android.permission.SEND_SMS"/>
    ...
</manifest>
```

예를들어 SMS 메시지를 보내야 하는 앱은 매니페스트에 위의 예제처럼 추가해야한다.

권한이 부여되면 앱은 해당 기능을 사용할 수 있다.

참고로 Android 6.0(API 레벨 23)부터 사용자는 런타임에서 일부 앱 권한을 승인하거나 거절할 수 있게 된다.


**04.앱에 필요한 하드웨어/소프트웨어 기능 알림**
-------- 

**uses-feature**
-------- 

마지막으로 기기 호환성이다. 

```java
<manifest ... >
    <uses-feature android:name="android.hardware.sensor.compass"
                  android:required="true" />
    ...
</manifest>
```

매니페스트 파일에선 <uses-feature> 를 사용하여 앱에 필요한 하드웨어 및 소프트웨어 기능을 선언할 수 있다. 

<uses-feature>는 등록한 권한 외에도 앱이 해당 기능을 사용한다는 것을 미리 알려서, 해당 기능이 없는 장비에 앱이 설치되는 것을 방지하기 위해 사용된다. 

예를들어 나침반 센서가 없는 기기에서 앱이 기능을 실행할 수 없는 경우, 위의 예제와 같이 <uses-feature> 태그를 사용하여 나침반 센서를 선언할 수 있다.



```java
<manifest ... >
   <uses-permission android:name="android.permission.CAMERA" />
   <uses-permission android:name="android.permission.FLASHLIGHT" />
   ...
</manifest>
```

또 이런식으로 사용할 수 있다. 다른 예를 들어보자.

앱에서 카메라의 플래시 라이트를 사용하기 위해 해당 권한을 추가한다면 실제 마켓에서 앱을 검색할때 하드웨어 기능을 기준으로 필터링이 걸리게 된다. 

즉, 카메라나 플래시라이트가 없는 단말은 앱을 설치조차 할 수 없게 되는 것이다. 

그렇다고 uses-permission을 빼버릴 수도 없는 노릇이다.  이럴때 <uses-feature> 요소를 사용할 수 있다. 


```java
<manifest ... >
   <uses-permission android:name="android.permission.CAMERA" />
   <uses-permission android:name="android.permission.FLASHLIGHT" />

   <uses-feature android:name="android.hardware.camera" android:required="false" />
   <uses-feature android:name="android.hardware.camera.flash" android:required="false" />
   ...
</manifest>
```

위와 같이 <uses-feature> 요소를 추가하면서 android:required="false" 속성을 주게 되면

해당 하드웨어에 대한 권한은 요청하지만, 하드웨어 기능이 실제 기기에 존재하지 않더라도 설치는 가능하게 된다. 

이후엔 실제 코드에서 카메라의 존재 여부에 따라 동작하는 코드를 만들면 된다. 

반대로 android:required="true"는 해당 요소가 앱에 꼭 있어야 한다는 뜻이다. 

-------- 

이렇게 AndroidManifest.xml 에 대해서 살펴보았다. 

AndroidManifest에서도 minSdkVersion이나 targetSdkVersion 값을 선언할 수 있지만, 이 속성값들은 build.gradle 파일의 속성으로 재정의된다는 것을 기억해야 한다. 

