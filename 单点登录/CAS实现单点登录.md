# 1 简述

SSO(Single Sign On)使得在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统。CAS(Central Authentication Service)是一款不错的针对 Web 应用的单点登录框架。

## 1.1 CAS原理与协议

CAS 包含两个部分：CAS Server和CAS Client。
CAS Server 需要独立部署，主要负责对用户的认证工作；
CAS Client 负责处理对客户端受保护资源的访问请求，需要登录时，重定向到 CAS Server。下图是 CAS 最基本的协议过程：
![基本的协议过程](http://www.ibm.com/developerworks/cn/opensource/os-cn-cas/images/image001.jpg)

# 2 实现步骤

1.  准备工作:下载tomcat，下载cas server和client。
2.  部署cas server。
3.  扩展认证接口。

        *   扩展AuthenticationHandler。
    *   JDBC认证。

4.  扩展CAS Server界面。
5.  部署客户端应用

        *   与CAS Server建立信任
    *   配置CAS Filter。

## 2.1 准备工作

下载tomcat，本例使用tomcat7。
下载CAS Server和client。本例使用：cas-client-3.2.1-release.zip、cas-server-3.4.2.1-release.zip。

## 2.2 部署CAS Server

### 2.2.1 配置Tomcat使用HTTPS协议

*   为服务器生成证书：

    D:<span class="hljs-command">\develop</span><span class="hljs-command">\soft</span><span class="hljs-command">\Java</span><span class="hljs-command">\JDK</span><span class="hljs-command">\bin</span>>keytool -genkey -v -alias tomcat -keyalg RSA -keystore D:<span class="hljs-command">\develop</span><span class="hljs-command">\soft</span><span class="hljs-command">\Java</span><span class="hljs-command">\tomcat</span>.keystore -validity 36500
    输入密钥库口令:makun2016
    再次输入新口令:makun2016
    您的名字与姓氏是什么?
      <span class="hljs-special">[</span>Unknown<span class="hljs-special">]</span>:  makun
    您的组织单位名称是什么?
      <span class="hljs-special">[</span>Unknown<span class="hljs-special">]</span>:  TianShengWoCai
    您的组织名称是什么?
      <span class="hljs-special">[</span>Unknown<span class="hljs-special">]</span>:  TianShengWoCai
    您所在的城市或区域名称是什么?
      <span class="hljs-special">[</span>Unknown<span class="hljs-special">]</span>:  BeiJing
    您所在的省/市/自治区名称是什么?
      <span class="hljs-special">[</span>Unknown<span class="hljs-special">]</span>:  BeiJing
    该单位的双字母国家/地区代码是什么?
      <span class="hljs-special">[</span>Unknown<span class="hljs-special">]</span>:  CN
    CN=makun, OU=TianShengWoCai, O=TianShengWoCai, L=BeiJing, ST=BeiJing, C=CN是否正确?  <span class="hljs-special">[</span>否<span class="hljs-special">]</span>:  y

    正在为以下对象生成 2,048 位RSA密钥对和自签名证书 (SHA256withRSA) (有效期为 36,500 天):
    CN=makun, OU=TianShengWoCai, O=TianShengWoCai, L=BeiJing, ST=BeiJing, C=CN
    输入 <tomcat> 的密钥口令
            (如果和密钥库口令相同, 按回车):
    <span class="hljs-special">[</span>正在存储D:<span class="hljs-command">\develop</span><span class="hljs-command">\soft</span><span class="hljs-command">\Java</span><span class="hljs-command">\tomcat</span>.keystore<span class="hljs-special">]</span>
    `</pre>

*   为客户端生成证书
    `keytool -genkey -v -alias mykey -keyalg RSA -store type PKCS12 -keystore D:\develop\soft\Java\mykey.p12`
    操作同上，密钥同上，其他信息可不填。此处未填。
    <pre>`D:<span class="hljs-command">\develop</span><span class="hljs-command">\soft</span><span class="hljs-command">\Java</span><span class="hljs-command">\JDK</span><span class="hljs-command">\bin</span>> keytool -genkey -v -alias mykey -keyalg RSA -store
    type PKCS12 -keystore D:<span class="hljs-command">\develop</span><span class="hljs-command">\soft</span><span class="hljs-command">\Java</span><span class="hljs-command">\mykey</span>.p12
    输入密钥库口令:makun2016
    再次输入新口令:makun2016
    您的名字与姓氏是什么?   <span class="hljs-special">[</span>Unknown<span class="hljs-special">]</span>:
    您的组织单位名称是什么? <span class="hljs-special">[</span>Unknown<span class="hljs-special">]</span>:
    您的组织名称是什么?  <span class="hljs-special">[</span>Unknown<span class="hljs-special">]</span>:
    您所在的城市或区域名称是什么?  <span class="hljs-special">[</span>Unknown<span class="hljs-special">]</span>:
    您所在的省/市/自治区名称是什么?  <span class="hljs-special">[</span>Unknown<span class="hljs-special">]</span>:
    该单位的双字母国家/地区代码是什么?  <span class="hljs-special">[</span>Unknown<span class="hljs-special">]</span>:
    CN=Unknown, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=Unknown是否正确?  <span class="hljs-special">[</span>否<span class="hljs-special">]</span>:  y

    正在为以下对象生成 2,048 位RSA密钥对和自签名证书 (SHA256withRSA) (有效期为 90 天):
             CN=Unknown, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=Unknown
    <span class="hljs-special">[</span>正在存储D:<span class="hljs-command">\develop</span><span class="hljs-command">\soft</span><span class="hljs-command">\Java</span><span class="hljs-command">\mykey</span>.p12<span class="hljs-special">]</span>
    `</pre>

*   让服务器信任客户端证书    由于是双向SSL认证，服务器必须要信任客户端证书，因此，必须把客户端证书添加为服务器的信任认证。由于不能直接将PKCS12格式的证书库导入，必须先把客户端证书导出为一个单独的CER文件
    <pre>`<span class="hljs-string">D:</span>\develop\soft\Java\JDK\bin>keytool -export -alias mykey -keystore <span class="hljs-string">D:</span>\develop\
    oft\Java\mykey.p12 -storetype PKCS12 -storepass makun2016 -rfc -file <span class="hljs-string">D:</span>\develop
    soft\Java\mykey.cer
    存储在文件 <<span class="hljs-string">D:</span>\develop\soft\Java\mykey.cer> 中的证书
    <span class="hljs-javadoc">/** --------将客户端证书导出到mykey.cer文件 ----------- **/</span>
    <span class="hljs-label">
    D:</span>\develop\soft\Java\JDK\bin>keytool -<span class="hljs-keyword">import</span> -v -file <span class="hljs-string">D:</span>\develop\soft\Java\mykey
    .cer -keystore <span class="hljs-string">D:</span>\develop\soft\Java\tomcat.keystore
    输入密钥库口令: makun2016
    所有者: CN=Unknown, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=Unknown
    发布者: CN=Unknown, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=Unknown
    序列号: <span class="hljs-number">3</span>c58f07
    有效期开始日期: Thu Mar <span class="hljs-number">03</span> <span class="hljs-number">11</span>:<span class="hljs-number">45</span>:<span class="hljs-number">43</span> CST <span class="hljs-number">2016</span>, 截止日期: Wed Jun <span class="hljs-number">01</span> <span class="hljs-number">11</span>:<span class="hljs-number">45</span>:<span class="hljs-number">43</span> CST
    <span class="hljs-number">2016</span>
    证书指纹:
    <span class="hljs-string">MD5:</span><span class="hljs-number">11</span>:<span class="hljs-number">52</span>:<span class="hljs-string">AE:</span><span class="hljs-number">83</span>:<span class="hljs-number">04</span>:<span class="hljs-string">F2:</span><span class="hljs-number">57</span>:<span class="hljs-string">DC:</span><span class="hljs-number">22</span>:<span class="hljs-number">48</span>:<span class="hljs-number">03</span>:<span class="hljs-number">21</span>:<span class="hljs-string">C3:</span><span class="hljs-string">ED:</span><span class="hljs-number">90</span>:<span class="hljs-number">1</span>D
    <span class="hljs-string">SHA1:</span> <span class="hljs-number">4</span><span class="hljs-string">B:</span><span class="hljs-string">A4:</span><span class="hljs-number">8</span><span class="hljs-string">D:</span><span class="hljs-number">94</span>:<span class="hljs-number">17</span>:<span class="hljs-string">B6:</span><span class="hljs-number">4</span><span class="hljs-string">E:</span><span class="hljs-string">F9:</span><span class="hljs-string">F1:</span><span class="hljs-number">48</span>:<span class="hljs-string">D7:</span><span class="hljs-string">DD:</span><span class="hljs-string">E7:</span><span class="hljs-string">B1:</span><span class="hljs-string">C0:</span><span class="hljs-string">F7:</span><span class="hljs-number">89</span>:<span class="hljs-string">A3:</span><span class="hljs-string">FB:</span><span class="hljs-number">24</span>
    <span class="hljs-string">SHA256:</span> <span class="hljs-number">96</span>:<span class="hljs-number">8</span><span class="hljs-string">D:</span><span class="hljs-string">AC:</span><span class="hljs-number">2</span><span class="hljs-string">C:</span><span class="hljs-number">47</span>:<span class="hljs-number">46</span>:<span class="hljs-string">E5:</span><span class="hljs-string">B8:</span><span class="hljs-number">8</span><span class="hljs-string">F:</span><span class="hljs-number">4</span><span class="hljs-string">F:</span><span class="hljs-number">75</span>:<span class="hljs-string">C4:</span><span class="hljs-string">B9:</span><span class="hljs-number">8</span><span class="hljs-string">B:</span><span class="hljs-string">B1:</span><span class="hljs-number">10</span>:<span class="hljs-number">56</span>:<span class="hljs-number">67</span>:<span class="hljs-string">BE:</span><span class="hljs-string">D4:</span><span class="hljs-number">1</span><span class="hljs-string">A:</span><span class="hljs-number">23</span>:<span class="hljs-string">E5:</span><span class="hljs-number">6</span><span class="hljs-string">E:</span><span class="hljs-number">17</span>:<span class="hljs-string">C5:</span><span class="hljs-number">67</span>:<span class="hljs-number">90</span>:<span class="hljs-string">A7:</span><span class="hljs-number">5</span><span class="hljs-string">F:</span><span class="hljs-number">83</span>:<span class="hljs-number">41</span>
    签名算法名称: SHA256withRSA
    版本: <span class="hljs-number">3</span>
    扩展:
    #<span class="hljs-number">1</span>:<span class="hljs-string">ObjectId:</span> <span class="hljs-number">2.5</span><span class="hljs-number">.29</span><span class="hljs-number">.14</span> Criticality=<span class="hljs-literal">false</span>
    SubjectKeyIdentifier [
    KeyIdentifier [
    <span class="hljs-number">0000</span>: D3 <span class="hljs-number">46</span> <span class="hljs-number">42</span> EB <span class="hljs-number">9</span>B <span class="hljs-number">1</span>A <span class="hljs-number">41</span> EE   F8 <span class="hljs-number">02</span> C4 <span class="hljs-number">3</span>A <span class="hljs-number">45</span> DE <span class="hljs-number">72</span> <span class="hljs-number">11</span>  .FB...A....:E.r<span class="hljs-number">.0010</span>: BB <span class="hljs-number">70</span> DA <span class="hljs-number">29</span> .p.)]]

    是否信任此证书? [否]:  y
    证书已添加到密钥库中
    [正在存储<span class="hljs-string">D:</span>\develop\soft\Java\tomcat.keystore]
    <span class="hljs-comment">/* ---导入到服务器的证书库，添加为一个信任证书 ---*/</span>
    <span class="hljs-label">
    D:</span>\develop\soft\Java\JDK\bin>keytool -list -keystore <span class="hljs-string">D:</span>\develop\soft\Java\tomcat
    .keystore
    输入密钥库口令:makun2016

    密钥库类型: JKS
    密钥库提供方: SUN

    您的密钥库包含 <span class="hljs-number">2</span> 个条目

    tomcat, <span class="hljs-number">2016</span>-<span class="hljs-number">3</span>-<span class="hljs-number">3</span>, PrivateKeyEntry,
    证书指纹 (SHA1): <span class="hljs-string">CA:</span><span class="hljs-number">37</span>:<span class="hljs-number">09</span>:<span class="hljs-number">4</span><span class="hljs-string">D:</span><span class="hljs-number">1</span><span class="hljs-string">C:</span><span class="hljs-number">60</span>:<span class="hljs-number">37</span>:<span class="hljs-number">30</span>:<span class="hljs-number">0</span><span class="hljs-string">A:</span><span class="hljs-string">E2:</span><span class="hljs-number">72</span>:<span class="hljs-number">5</span><span class="hljs-string">E:</span><span class="hljs-string">A7:</span><span class="hljs-string">AD:</span><span class="hljs-number">3</span><span class="hljs-string">F:</span><span class="hljs-number">21</span>:<span class="hljs-string">CE:</span><span class="hljs-string">BC:</span><span class="hljs-number">4</span><span class="hljs-string">E:</span><span class="hljs-number">38</span>
    mykey, <span class="hljs-number">2016</span>-<span class="hljs-number">3</span>-<span class="hljs-number">3</span>, trustedCertEntry,
    证书指纹 (SHA1): <span class="hljs-number">4</span><span class="hljs-string">B:</span><span class="hljs-string">A4:</span><span class="hljs-number">8</span><span class="hljs-string">D:</span><span class="hljs-number">94</span>:<span class="hljs-number">17</span>:<span class="hljs-string">B6:</span><span class="hljs-number">4</span><span class="hljs-string">E:</span><span class="hljs-string">F9:</span><span class="hljs-string">F1:</span><span class="hljs-number">48</span>:<span class="hljs-string">D7:</span><span class="hljs-string">DD:</span><span class="hljs-string">E7:</span><span class="hljs-string">B1:</span><span class="hljs-string">C0:</span><span class="hljs-string">F7:</span><span class="hljs-number">89</span>:<span class="hljs-string">A3:</span><span class="hljs-string">FB:</span><span class="hljs-number">24</span>
    <span class="hljs-comment">/* -- list命令查看服务器的证书库 --- */</span>

# 3 参考资料

[http://www.ibm.com/developerworks/cn/opensource/os-cn-cas/index.html](http://www.ibm.com/developerworks/cn/opensource/os-cn-cas/index.html)