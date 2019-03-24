

**프로젝트 소개**
-------

안드로이드에서의 빌드 툴은 Gradle입니다.
안드로이드 스튜디오를 사용하면서 Gradle에 대해 잘 알지 못하는 경우가 많은데요.
그래서 정리해보았습니다.

내용중에 Gradle과 상관없는 내용이 있을 수도 있습니다. 그런 내용은 제가 공부하면서 궁금한 점이 꼬리에 꼬리를 물었기때문에 같이 정리해서 그렇습니다.
그런 내용은 Skip 하셔도 됩니다!

프로젝트는 지속적으로 추가 될 예정입니다. 상단의 Star, Watching 버튼을 클릭하시면 구독 알림을 받으실 수 있습니다 :)


----------


**목차**
--

 - [프로젝트 소개](#프로젝트-소개)
 - [목차](#목차)
 - [전체 플로우](#전체-플로우)
  - [프로젝트](#프로젝트-내에서-Gradle-관련-파일)
     - [1) settings.gradle 파일](#settings.gradle-파일)
     - [2) 루트 프로젝트 디렉토리의 build.gradle 파일 (최상위 빌드 파일)](#2)
     - [3) 모듈내에서의 Gradle 관련 파일](#ㄱ)
  - [JCenter vs Maven Central](#JCenter-vs-Maven-Central)
  - [새로운 라이브러리를 추가하는 방법](#새로운-라이브러리를-추가하는-방법)
     - [1) jar파일일 경우 (간단한 방법)](#ㄱ)
     - [2) jar나 다른 라이브러리일 경우](#ㄱ)
  - [.jar형 라이브러리를 앱에 적용할 때 새로운 라이브러리를 gradle에 추가했는데
Cause: unable to find valid certification path to requested target 에러가 나는 경우](#ㄱ)


----------

**전체 플로우**
----------

**프로젝트 내에서 Gradle 관련 파일**
------

**1) settings.gradle 파일**

앱을 빌드할때 어떤 모듈을 포함할지 Gradle에 알려준다.

```java
include ':app'
```

**2) 루트 프로젝트 디렉토리의 build.gradle 파일 (최상위 빌트 파일)**

프로젝트의 모든 모듈에 적용되는 빌드 구성을 정의한다.


```java
buildscript {
      repositories {
           jcenter()
      }

      dependencies {
           classpath 'com.android.tools.build:gradle:3.3.0'
      }
  }

  allprojects {
    repositories {
        jcenter()
    }
 }
```

**2-1. buildscript{} 구역 :** 프로젝트의 모든 모듈에 공통되는 Gradle 레파지토리와(라이브러리 땡겨오는)

종속성을 정의하기 위한 블록, 구역이다.

프로젝트의 모든 모듈에 공통되는 구역이기 때문에 모듈의(ex. app) 개인 dependencies는 이 곳에 정의될 수 없다.

**2-2. repositories{} 구역 :** dependencies 구역에 쓰여있는 라이브러리들을 찾거나 다운로드 하기 위한 Gradle 레파지토리를 정의한다.

Gradle은 JCenter나 Maven Central, Ivy를 Gradle 레파지토리로 지원하고 있다.

또한 로컬 레파지토리나 자신의 소유의 서버 레파지토리도 정의할 수 있다.


**2-3. dependencies{} 구역 :** 프로젝트가 빌드하기 위해 필요한 Gradle 정보를 넣는 구역이다.

참고로 classpath 'com.android.tools.build:gradle:3.3.0'은 버전 3.3.0의 그래들을 위한 안드로이드 플러그인이다.

(플러그인 : 일반적인 소프트웨어의 일부로, 외부 기술의 일종이다. 즉, 쉽게 설치되고 사용될 수 있는 프로그램을 말한다.)


**2-4. allprojects{} 구역 :** 프로젝트 내의 모든 모듈의 dependencies와 저장소들을 사용하기 위해 정의하는 구역이다.

디폴트 저장소는 jCenter이다.

----------


**3) 모듈내에서의 Gradle 관련 파일**

이 파일(build.gradle)이 위치하는 특정 모듈의 빌드 설정을 구성할 수 있으며, AndroidManifest 또는 최상위 build.gradle 파일에 있는 설정을 재정의할 수 있다.


```java
apply plugin: 'com.android.application' 

      android {
          compileSdkVersion 28
          buildToolsVersion "28.0.3"

      defaultConfig {
        applicationId 'com.example.myapp'
        minSdkVersion 15
        targetSdkVersion 28
        versionCode 1
        versionName "1.0"
     }

      buildTypes {
         release {
             minifyEnabled true // Enables code shrinking for the release build type.
             proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
         }
      }

      productFlavors {
        free {
          applicationId 'com.example.myapp.free'
        }
        
        paid {
          applicationId 'com.example.myapp.paid'
       }
     }
     
     splits {
        density {
           enable false
           exclude "ldpi", "tvdpi", "xxxhdpi", "400dpi", "560dpi"
        }
      }
    }

    dependencies {
         compile project(":lib")
         compile 'com.android.support:appcompat-v7:28.0.0'
         compile fileTree(dir: 'libs', include: ['*.jar'])
    }
```


**3-1. apply plugin :** Android 플러그인을 적용한다.

Gradle은 이것을 빌드하고 android{} 구역을 이용할 수 있도록 만든다.

**3-2. android{}구역 :** 모듈의 Android 관련 설정을 구성하는 공간이다.

**3-3. compileSdkVersion :** Gradle이 앱을 컴파일 할 때 사용해야 할 Android API 레벨을 지정한다.

이 말인 즉슨, 앱은 이 API 레벨과 해당 API 레벨보다 낮은 API의 특징을 사용할 수 있다는 것이다.

**3-4. buildToolsVersion :** Gradle이 앱을 빌드할 때 사용해야 하는 컴파일러의 버전 및 SDK 빌드 도구를 지정한다.

SDK관리자를 사용하여 빌드 도구를 다운로드 할 수 있다.

(SDK : 안드로이드 소프트웨어를 개발하기 위한 도구를 모아놓은 키트)

(JDK : 자바 소프트웨어를 개발하기 위한 도구를 모아놓은 키트)

(=> 안드로이드를 개발하려면 SDK와 JDK 둘다 필요하다. (안드로이드는 자바 언어로 개발 되므로))

buildToolsVersion은 컴파일러의 버전, compileSdkVersion은 컴파일러가 컴파일 할 때의 버전이므로 compileSdkVersion과 buildToolsVersion은 서로 비슷하게 맞춰주는 것이 좋다.

----------
**3-5. defaultConfig{} 구역 :** 디폴트 셋팅과 모든 빌드 변수의 값들을 캡슐화 해놓은 구역이다.

또한 이 구역은 AndroidManifest.xml에 있는 몇몇 속성들을 오버라이드 할 수 있다. (빌드 시스템에 의해서 동적으로 오버라이드 된다.)

**3-5-1. applicationId :** 앱을 출시하기 위하여, 자신을 나타낼 수 있는 유니크한 값이다.

**3-5-2. minSdkVersion :** 앱을 구동하기 위해 필요한 최소 API 레벨이다.

**3-5-3. targetSdkVersion :** 앱을 테스트했었던 API 레벨이다. (기준)

**3-5-4. versionCode :** 버전 코드

**3-5-5. versionName :** 버전명 (사용자들이 친숙하게 느낄 수 있는 버전 코드)
----------
**3-6. buildTypes{} 구역 :** 빌드 타입을 구성할 수 있는 구역이다.

기본적으로 빌드 시스템은 debug와 release 두가지의 빌드 타입을 정의한다.




----------

**JCenter vs Maven Central**
------
Bintray와 Maven은 다양한 라이브러리 저장소를 운영하는 서비스이다.

그리고 JCenter는 Bintray에, Maven Central은 Maven에 속하는 자바 레파지토리 저장소이다.

jcenter()는 mavenCentral()의 상위 개념이라고 볼 수 있는데, 왜냐하면 안드로이드 스튜디오 이전 버전은 mavenCentral()을 사용했었지만

현재는 jcenter()를 사용하고 있다.

이는 jcenter()가 mavenCentral()보다 성능 및 메모리 사용면에서 우수하기 때문이다.

jcenter()는 https프로토콜을 사용하므로 높은 보안을 유지하는데, mavenCentral()은 http프로토콜을 사용하여 상대적으로 낮은 보안을 유지한다.



**새로운 라이브러리를 추가하는 방법*
------

**1) jar파일일 경우 (간단한 방법)**

libs 폴더에 jar파일을 넣는다 -> 우클릭해서 Add As library를 누른다.

**2) jar나 다른 라이브러리일 경우**

File > Project Structure를 누른다 -> Dependencies 탭을 누른 후, + 버튼을 누른다.

**2-1. jcenter에 있는 라이브러리인 경우**

Library dependency를 누르고 검색창에 해당 라이브러리 명을 검색한 후 ok를 눌러서 Gradle에 추가한다.

**2-2. jar파일인 경우**

Jar dependency를 누르고 해당 경로를 선택한 후 추가한다. (이 방법보다 보통 간단한 방법을 사용한다.)


이클립스에서 안드로이드 스튜디오로 넘어오면서 라이브러리 추가를 Gradle을 이용하여 jcenter에 있는 라이브러리를 불러와 사용하게 되었다.

경로 : https://bintray.com/bintray/jcenter

기존에는 위 사이트에서 필요한 라이브러리를 검색한 후 Gradle 파일에 입력하곤 했는데, 안드로이드 스튜디오에선 File > Project Structure > Library dependency를 이용하여 편하게 라이브러리를 추가할 수 있다.




**.jar형 라이브러리를 앱에 적용할 때**
------

Project 단위에서 app 밑에 있는 libs 폴더안에 jar 파일을 넣고, (libs폴더가 없으면 생성하기)

오른쪽 버튼으로 Add as 시킨다.

그러면 app/build.gradle에 있는 dependencies에 

implementation files('libs/SDK_ANDROID_APMS_11888.jar') 이런 형태로 적용이 된다.

그런데 이렇게 하나하나 적용하면 끝이 없으니까

implementation fileTree(include: ['*.jar'], dir: 'libs') // libs 폴더안에 있는 모든 .jar 파일 적용

이런 식으로 하나로 통합해 주는 것이 좋다.





**새로운 라이브러리를 gradle에 추가했는데

Cause: unable to find valid certification path to requested target 에러가 나는 경우**
------

유효한 인증서가 없기 때문이다.

이럴 때는 Configure build를 눌러서 에러로그를 자세히 킨 다음

Caused by : org.gradle.api.resource.ResourceException : Could not get resource '...'라고 하이퍼링크가 있을 것이다.

(ex. Caused by: org.gradle.api.resources.ResourceException: Could not get resource 'https://maven.fabric.io/public/io/fabric/tools/gradle/1.25.1/gradle-1.25.1.pom'.) // Firebase Crashlytics 추가하려다 발생

이럴 땐 이 하이퍼링크를 눌러서 그 사이트로 이동한다. (Chrome을 사용한다.)

(ex. https://maven.fabric.io/public/io/fabric/tools/gradle/1.25.1/gradle-1.25.1.pom)

창이 뜨면 주소창 맨 왼쪽에 '자물쇠' 모양이 있는데 그것을 누른다. 그러면 '인증서 : (유효)'가 있는데 이것을 누른다.

또 창이 뜨면 '자세히 탭'을 누르고 '파일에 복사'를 누른다.

그 다음 인증서 내보내기 마법사 창이 뜨는데, 다음과 다음을 누른다.

내보낼 파일 창에서 파일 이름을 지정해줘야 하는데 마음대로 지정해준다. (ex. mavenFabric.cer)

여기서 확장자는 무조건 .cer이어야 한다.

다시 다음을 누르고 종료시킨다.

그러면 아까 파일 이름을 지정해줄때 지정해놓았던 경로에 인증서가 저장되어 있을 것이다.


이제 안드로이드 스튜디오로 돌아와서 File > Settings 를 눌러서 셋팅창을 켠 후, 

Tools > Server Certificates 또는, 검색창에 Certificates라고 검색한다.

여기서 상단에 Accepted certificates라는 구역이 있는데, 여기서 '+ 버튼'을 누른다. 


(Accepted certificates 구역은 이 구역에 있는 인증서들이 현재 안드로이드 스튜디오에서 유효한 상태에 있는 인증서들이다.)

'+ 버튼'을 눌러서 뜬 창에서 아까 전의 인증서 경로를 찾은 후, apply 시키고 ok 버튼을 누른다.

이를 통해서 성공적으로 유효한 인증서를 등록할 수 있다.








