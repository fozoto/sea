# quasar打包android apk

```shell
cd /app/workspaces/quasar/quasar-hello
npm install -g cordova
export ANDROID_HOME="$HOME/Android/Sdk"
export PATH=$PATH:$ANDROID_HOME/tools; 
export PATH=$PATH:$ANDROID_HOME/platform-tools
quasar mode add cordova
```

> vim src-cordova/platforms/android/gradle/wrapper/gradle-wrapper.properties

```shell
distributionUrl=file:/soft/gradle-4.10.3-bin.zip
```

> vim vim src-cordova/platforms/android/build.gradle

```shell
buildscript {
    repositories {
        maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' }
        maven{ url 'http://maven.aliyun.com/nexus/content/repositories/jcenter'}
        google()
        jcenter()
    }
}
allprojects {
    repositories {
        maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' }
        maven{ url 'http://maven.aliyun.com/nexus/content/repositories/jcenter'}
        google()
        jcenter()
    }
}
```

打包apk

```shell
quasar build -m andorid
keytool -genkey -v -keystore release.keystore -alias release -keyalg RSA -keysize 2048 -validity 20000
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore release.keystore /app/workspaces/quasar/quasar-hello/dist/cordova/android/apk/release/app-release-unsigned.apk release
```
