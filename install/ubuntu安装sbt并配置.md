# ubuntu安装sbt并配置

版本: 1.3.4

## 下载

```shell
https://sbt-downloads.cdnedge.bluemix.net/releases/v1.3.4/sbt-1.3.4.tgz
```

## 安装

```shell
cp sbt-1.3.4.tgz /soft
cd /soft
tar -zxvf sbt-1.3.4.tgz
echo "export SBT_HOME=/soft/sbt" >> /etc/profile
echo "PATH=$PATH:$SBT_HOME/bin" >> /etc/profile
```

## 配置仓库地址以及阿里云

vim ~/.sbt/repositories

```shell
[repositories]
  local
  local-maven: file:///file/repository/maven
  aliyun-nexus: https://maven.aliyun.com/nexus/content/groups/public/
  aliyun-nexus2: https://maven.aliyun.com/nexus/content/groups/public/,[organization]/[module]/(scala_[scalaVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext], bootOnly
  maven-central

[ivy]
  ivy-home: /file/repository/sbt
```

## idea配置sbt

idea的sbt的VM参数:

```shell
-Dsbt.override.build.repos=true
-Dsbt.repository.config=~/.sbt/repositories
```

## 修改启动jar

修改/soft/sbt/bin/sbt-launch.jar包的/sbt/sbt.boot.properties

```shell
[scala]
  version: ${sbt.scala.version-auto}

[app]
  org: ${sbt.organization-org.scala-sbt}
  name: sbt
  version: ${sbt.version-read(sbt.version)[1.3.4]}
  class: ${sbt.main.class-sbt.xMain}
  components: xsbti,extra
  cross-versioned: ${sbt.cross.versioned-false}
  resources: ${sbt.extraClasspath-}

[repositories]
  local
  local-maven: file:/file/repository/maven
  local-preloaded-ivy: file:///${sbt.preloaded-${sbt.global.base-${user.home}/.sbt}/preloaded/}, [organization]/[module]/[revision]/[type]s/[artifact](-[classifier]).[ext]
  local-preloaded: file:///${sbt.preloaded-${sbt.global.base-${user.home}/.sbt}/preloaded/}
  aliyun-nexus: http://maven.aliyun.com/nexus/content/groups/public/
  aliyun-nexus2: https://maven.aliyun.com/nexus/content/groups/public/,[organization]/[module]/(scala_[scalaVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext], bootOnly
  maven-central
  sbt-maven-releases: https://repo.scala-sbt.org/scalasbt/maven-releases/, bootOnly
  sbt-maven-snapshots: https://repo.scala-sbt.org/scalasbt/maven-snapshots/, bootOnly
  typesafe-ivy-releases: https://repo.typesafe.com/typesafe/ivy-releases/, [organization]/[module]/[revision]/[type]s/[artifact](-[classifier]).[ext], bootOnly
  sbt-ivy-snapshots: https://repo.scala-sbt.org/scalasbt/ivy-snapshots/, [organization]/[module]/[revision]/[type]s/[artifact](-[classifier]).[ext], bootOnly

[boot]
  directory: ${sbt.boot.directory-${sbt.global.base-${user.home}/.sbt}/boot/}
  lock: ${sbt.boot.lock-true}

[ivy]
  ivy-home: /file/repository/sbt
  checksums: ${sbt.checksums-sha1,md5}
  override-build-repos: ${sbt.override.build.repos-false}
  repository-config: ${sbt.repository.config-${sbt.global.base-${user.home}/.sbt}/repositories}
```
