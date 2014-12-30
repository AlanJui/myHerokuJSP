# 指引 #

本文件目的，在於提供使用本容器產出，所需之「作業指引」：安裝、編譯、執行、佈署及驗證。


### 本容器用途 ###

提供 Java Web 開發專案，所需使用的「專案模版」。

#### 適用情境：

* 執行環境： Heroku
* 開發環境： Tomcat ＋MySQL
* 開發工具：IntelliJ IDEA CE + Maven

#### 規格明細：

* 作業系統：OS X V.10.10.1
* Java 開發工具：JDK 7u71
* Java EE Web 伺服器：Tomcat 7 V7.0.57
* Build工具：Maven 3.2.3
* 版本控管工具：git 2.2
* 程式開發工具：Intellij IDEA 14 CE

## 開發環境安裝及設定作業

為能正常運作，使用者須先完成如下之相關「安裝、設定」作業。

作業之程序步驟：

1. 備妥基礎軟體
2. 設定環境變數
3. 安裝及設定 Tomcat
4. 安裝及設定 Maven

### 備妥基礎軟體

先完成下列軟體之安裝：

* Java開發工具：JDK 7u71
* 版本控管工具：git 2.2
* 程式開發工具：Intellij IDEA V14 CE
* 純文字編輯器：atom V0.165.0 (可依個人喜好，替換此編輯器)

### 設定環境變數

程式碼之編輯、編譯及執行，需要下列「環境變數」設定：

* JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.7.0_71.jdk/Contents/Home

配合以上之需求，可在 ~/.bash_profile 加入如下內容，以便「終端機」啟動時，能進行環境變數的自動設定工作：

```
source ~/.profile

# JDK
export JAVA_HOME=$(/usr/libexec/java_home -v 1.7)
```




### 設定 Tomcat 作業

作業之程序步驟：

1. 下載 Tomcat 7
2. 安裝 Tomcat 7
3. 設定 server Port: 9090
4. 設定佈署使用者之帳號及角色（權限）
5. 啟動 Tomcat 7

#### 下載 Tomcat 7

自官網「http://tomcat.apache.org/download-70.cgi 」，下載版本編號為：「7.0.57」的「tar.gz」壓縮檔案。

#### 安裝 Tomcat 7

將下載之壓縮檔案：「apache-tomcat-7.0.57.tar.gz」，先行「解壓縮」。然後將整個資料夾放入 Applications

#### 設定 server Port: 9090

編輯設定檔 server.xml
```
$ atom /Applications/apache-tomcat-7.0.57/conf/server.xml
```

加入如下設定：
```
<server ...>
  ......
  <Connector port="9090" protocol="HTTP/1.1"
    connectionTimeout="20000"
    redirectPort="8443" />
  ......
</server>
```

#### 設定佈署使用者之帳號及角色（權限）

編輯設定檔 tomcat-users.xml
```
$ atom /Applications/apache-tomcat-7.0.57/conf/tomcat-users.xml
```

加入如下設定：
```
<tomcat-users>
  <role rolename="manager-gui"/>
  <role rolename="manager-script"/>
  <user username="tomcat" password="tomcat" roles="manager-gui, manager-script"/>
</tomcat-users>
```

#### 啟動 Tomcat 7

執行啟動指令
```
$ sh /Applications/apache-tomcat-7.0.57/bin/startup.sh

Using CATALINA_BASE:   /Applications/apache-tomcat-7.0.57
Using CATALINA_HOME:   /Applications/apache-tomcat-7.0.57
Using CATALINA_TMPDIR: /Applications/apache-tomcat-7.0.57/temp
Using JRE_HOME:        /Library/Java/JavaVirtualMachines/jdk1.7.0_71.jdk/Contents/Home
Using CLASSPATH:       /Applications/apache-tomcat-7.0.57/bin/bootstrap.jar:/Applications/apache-tomcat-7.0.57/bin/tomcat-juli.jar
Tomcat started.
```



### 設定 Maven 作業

作業之程序步驟：

1. 下載及安裝 Maven
2. 設定 Global Setting
3. 設定環境變數

#### 下載及安裝 Maven

```
$ brew install maven

==> Downloading http://www.apache.org/dyn/closer.cgi?path=maven/maven-3/3.2.3/bi
==> Best Mirror http://apache.petsads.us/maven/maven-3/3.2.3/binaries/apache-mav
######################################################################## 100.0%
🍺  /usr/local/Cellar/maven/3.2.3: 76 files, 8.0M, built in 113 seconds
```

#### 設定 Global Setting

編輯設定檔 settings.xml
```
$ sudo atom /usr/local/Cellar/maven/3.2.3/libexec/conf/settings.xml
```

加入如下設定：
```
<servers>
  <server>
    <id>Tomcat7</id>
    <username>tomcat</username>
    <password>tomcat</password>
  </server>
</servers>
```

#### 設定環境變數

編輯設定檔 .bash_profile
```
$ vim ~/.bash_profile
```

加入如下設定：
```
export M2_HOME=/usr/local/Cellar/maven/3.2.3/libexec
export M2=$M2_HOME/bin
export PATH=$M2:$PATH
```

#### 【附註】：
完整的 .bash_profile 內容：
```
source ~/.profile

# JDK
export JAVA_HOME=$(/usr/libexec/java_home -v 1.7)

# Mavern
export M2_HOME=/usr/local/Cellar/maven/3.2.3/libexec
export M2=$M2_HOME/bin
export PATH=$M2:$PATH
```

執行環境變數設定：
```
$ . ~/.bash_profile
```

## 編譯、執行及佈署作業

作業之程序步驟：

1. 使用 IntelliJ 建立 Maven Project 。
2. 修訂 Maven Script 設定檔。
3. 包裝成 war 檔案。
4. 佈署到 Local 端的 Tomcat 伺服器。
5. 佈署到雲端的 Heroku 伺服器。


### 1. 使用 IntelliJ 建立 Maven Project

在 IntelliJ ，執行「New Project」。

Create from archetype: org.apache.maven.archetypes:maven-archetype-webapp

![2014-12-30_07-12-42.png](https://lh3.googleusercontent.com/-f7hZreXKZv4/VKHkZrJtPiI/AAAAAAAAeww/wupM-a767CI/w661-h549-no/2014-12-30_07-12-42.png)

### 2. 修訂 Maven Script 設定檔。

修訂 pom.xml 檔案：

* properties - project.build.sourceEncoding=UTF-8
* dependency - javax.servlet V3.0.1
* plugin - org.apache.maven.plugins:maven-compiler-plugin V3.2
* plugin - org.apache.tomcat7.maven:tomcat7-maven-plugin V2.2

### 3. 包裝成 war 檔案。

執行 Maven 指令，包裝成 war 檔。
```
＄ mvn package
```

### 4. 佈署到 Local 端的 Tomcat 伺服器。

執行 Maven 指令，佈署到 Local 端的 Tomcat：
```
＄ mvn tomcat7:deploy
```

### 5. 佈署到雲端的 Heroku 伺服器。

此程序步驟可再細分如下之操作步驟：

1. 在 Heroku 網站建立 App 。
2. 在「終端機」登入 Heroku 。
3. 在「終端機」安裝 Heroku Plugin 。
4. 在「終端機」佈署到 Heroku 。
5. 在「終端機」啟動 Heroku App。


#### 在 Heroku 網站建立 App

此程序步驟可再細分如下之操作步驟：

1. 登入 Heroku 網站，進入個人專屬之「Dashboard網頁」 。
2. 在「Dashboard網頁」，執行「New App」。


##### 1. 登入 Heroku 網站，進入個人專屬之「Dashboard網頁」 。
![Fig-2](https://lh6.googleusercontent.com/-U1BdQ_2_UQ0/VKHmsG4IOWI/AAAAAAAAexA/fGwOfkX5zaI/w949-h553-no/2014-12-30_07-36-55.png)

##### 2. 在「Dashboard網頁」，執行「New App」，以建立新的 App。

完成 App 建立之後，可自「Dashboard網頁」進入，看到如下之 App 管理畫面。

![Fig-3](https://lh6.googleusercontent.com/-GpmyZMq6nu8/VKHmtLoahDI/AAAAAAAAexI/Qs9YlnziFPc/w949-h553-no/2014-12-30_07-37-16.png)


#### 在「終端機」登入 Heroku

```
$ heroku login

Enter your Heroku credentials.
Email: alanjui.1960@gmail.com
Password (typing will be hidden):
Authentication successful.
```

#### 在「終端機」安裝 Heroku Plugin

```
$ heroku plugins:install https://github.com/heroku/heroku-deploy

Installing heroku-deploy... done
```

#### 在「終端機」佈署到 Heroku

```
$ heroku deploy:war --war target/myWebApp01.war --app my-heroku-jsp

Uploading target/myWebApp01.war.....done
Deploying to my-heroku-jsp.................done
Created release v6
```

#### 在「終端機」啟動 Heroku App

在「終端機」輸入下列指令，要求作業系統啟動 Web 瀏覽器，進入 Heroku 所佈署的網站。
```
$ heroku open --app my-heroku-jsp

Opening my-heroku-jsp... done
```

【註】：此步驟之程序可省去，直接啟動瀏覽器軟體，輸入網址：「https://my-heroku-jsp.herokuapp.com/ 」，透過網頁的輸出，驗證佈署的結果，是否成功。




## 驗證作業

本驗證作業設定之目標：

* 在瀏覽器可見到「首頁（Home Page）」輸出的網頁畫面
* 在瀏覽器輸入「網址」，負責對應的 Servlet ，能完成網頁輸出

### 在瀏覽器可見到「首頁（Home Page）」輸出的網頁畫面

1. 啟動瀏覽器。

2. 輸入網址：https://my-heroku-jsp.herokuapp.com 。

3. 瀏覽器輸出的畫面，可見到：「Hello World」文字。

### 在瀏覽器輸入「網址」，負責對應的 Servlet ，能完成網頁輸出

#### 【案例一】HelloServlet 處理「get /hello」之 HTTP Request

1. 啟動瀏覽器。

2. 輸入網址：https://my-heroku-jsp.herokuapp.com/hello 。

3. 瀏覽器輸出的畫面，可見到：「Hello Servlet!!」文字。

#### 【案例二】HiServlet 處理「get /hi」之 HTTP Request

1. 啟動瀏覽器。

2. 輸入網址：https://my-heroku-jsp.herokuapp.com/hi 。

3. 瀏覽器輸出的畫面，可見到：「Hi every body, I am HiServlet!!」文字。
