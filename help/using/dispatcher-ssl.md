---
title: Använda SSL med Dispatcher
seo-title: Using SSL with Dispatcher
description: Lär dig hur du konfigurerar Dispatcher att kommunicera med AEM via SSL-anslutningar.
seo-description: Learn how to configure Dispatcher to communicate with AEM using SSL connections.
uuid: 1a8f448c-d3d8-4798-a5cb-9579171171ed
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: 771cfd85-6c26-4ff2-a3fe-dff8d8f7920b
index: y
internal: n
snippet: y
exl-id: ec378409-ddb7-4917-981d-dbf2198aca98
source-git-commit: e87af532ee3268f0a45679e20031c3febc02de58
workflow-type: tm+mt
source-wordcount: '1355'
ht-degree: 0%

---

# Använda SSL med Dispatcher {#using-ssl-with-dispatcher}

Använd SSL-anslutningar mellan Dispatcher och återgivningsdatorn:

* [Envägs SSL](#use-ssl-when-dispatcher-connects-to-aem)
* [Ömsesidig SSL](#configuring-mutual-ssl-between-dispatcher-and-aem)

>[!NOTE]
>
>Åtgärder som rör SSL-certifikat är bundna till produkter från tredje part. De omfattas inte av Adobe Platinum Maintenance and Support-avtalet.

## Använd SSL när Dispatcher ansluts till AEM {#use-ssl-when-dispatcher-connects-to-aem}

Konfigurera Dispatcher för att kommunicera med AEM eller CQ-återgivningsinstansen med SSL-anslutningar.

Innan du konfigurerar Dispatcher måste du konfigurera AEM eller CQ så att SSL används:

* AEM 6.2: [Aktivera HTTP över SSL](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=en)
* AEM 6.1: [Aktivera HTTP över SSL](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=en)
* Äldre AEM: se [den här sidan](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=en).

### SSL-relaterade begärandehuvuden {#ssl-related-request-headers}

När Dispatcher tar emot en HTTPS-begäran inkluderar Dispatcher följande huvuden i den efterföljande begäran som skickas till AEM eller CQ:

* `X-Forwarded-SSL`
* `X-Forwarded-SSL-Cipher`
* `X-Forwarded-SSL-Keysize`
* `X-Forwarded-SSL-Session-ID`

En begäran via Apache-2.4 med `mod_ssl` innehåller rubriker som liknar följande exempel:

```shell
X-Forwarded-SSL: on
X-Forwarded-SSL-Cipher: DHE-RSA-AES256-SHA
X-Forwarded-SSL-Session-ID: 814825E8CD055B4C166C2EF6D75E1D0FE786FFB29DEB6DE1E239D5C771CB5B4D
```

### Konfigurera Dispatcher att använda SSL {#configuring-dispatcher-to-use-ssl}

Om du vill konfigurera Dispatcher att ansluta till AEM eller CQ via SSL, kan du [dispatcher.any](dispatcher-configuration.md) filen kräver följande egenskaper:

* En virtuell värd som hanterar HTTPS-begäranden.
* The `renders` -avsnittet i den virtuella värden innehåller ett objekt som identifierar värdnamnet och porten för CQ- eller AEM-instansen som använder HTTPS.
* The `renders` objektet innehåller en egenskap med namnet `secure` värde `1`.

Obs! Skapa en annan virtuell värd för att hantera HTTP-begäranden, om det behövs.

Följande exempel `dispatcher.any` filen visar egenskapsvärden för anslutning med HTTPS till en CQ-instans som körs på värden `localhost` och port `8443`:

```
/farms
{
   /secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTPS requests
         "https://*"
      }
      /renders
      {
      /0001
         {
            # hostname or IP of the render
            /hostname "localhost"
            # port of the render
            /port "8443"
            # connect via HTTPS
            /secure "1"
         }
      }
     # the rest of the properties are omitted
   }

   /non-secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTP requests
         "http://*"
      }
      /renders
      {
         /0001
      {
         # hostname or IP of the render
         /hostname "localhost"
         # port of the render
         /port "4503"
      }
   }
    # the rest of the properties are omitted
}
```

## Konfigurera ömsesidig SSL mellan Dispatcher och AEM {#configuring-mutual-ssl-between-dispatcher-and-aem}

Konfigurera anslutningarna mellan Dispatcher och återgivningsdatorn (vanligtvis en AEM- eller CQ-publiceringsinstans) om du vill använda ömsesidig SSL:

* Dispatcher ansluter till återgivningsinstansen via SSL.
* Återgivningsinstansen verifierar giltigheten för Dispatcher-certifikatet.
* Dispatcher verifierar att certifikatutfärdaren för återgivningsinstansens certifikat är betrodd.
* (Valfritt) Dispatcher verifierar att återgivningsinstansens certifikat matchar återgivningsinstansens serveradress.

Om du vill konfigurera gemensam SSL måste du ha certifikat som är signerade av en betrodd certifikatutfärdare (CA). Självsignerade certifikat räcker inte. Du kan antingen agera som certifikatutfärdare eller använda tjänsterna från en tredjepartscertifikatutfärdare för att signera dina certifikat. Om du vill konfigurera gemensam SSL behöver du följande objekt:

* Signerade certifikat för återgivningsinstansen och Dispatcher
* Certifikatutfärdarcertifikatet (om du agerar som certifikatutfärdare)
* OpenSSL-bibliotek för generering av certifikatutfärdare, certifikat och certifikatbegäranden.

Så här konfigurerar du gemensam SSL:

1. [Installera](dispatcher-install.md) den senaste versionen av Dispatcher för din plattform. Använd en Dispatcher-binär som stöder SSL (SSL finns i filnamnet, till exempel dispatcher-apache2.4-linux-x86-64-ssl10-4.1.7.tar).
1. [Skapa eller hämta CA-signerat certifikat](dispatcher-ssl.md#main-pars-title-3) för Dispatcher och återgivningsinstansen.
1. [Skapa en nyckelbehållare som innehåller återgivningscertifikat](dispatcher-ssl.md#main-pars-title-6) och konfigurera återgivningens HTTP-tjänst.
1. [Konfigurera webbservermodulen Dispatcher](dispatcher-ssl.md#main-pars-title-4) för gemensam SSL.

### Skapa eller hämta CA-signerade certifikat {#creating-or-obtaining-ca-signed-certificates}

Skapa eller hämta de CA-signerade certifikat som autentiserar publiceringsinstansen och Dispatcher.

#### Skapar din certifikatutfärdare {#creating-your-ca}

Om du agerar som certifikatutfärdare ska du använda [OpenSSL](https://www.openssl.org/) för att skapa certifikatutfärdaren som signerar servern och klientcertifikaten. (Du måste ha OpenSSL-biblioteken installerade.) Utför inte den här proceduren om du använder en tredjeparts certifikatutfärdare.

1. Öppna en terminal och ändra den aktuella katalogen till katalogen som innehåller `CA.sh` fil, som `/usr/local/ssl/misc`.
1. Om du vill skapa certifikatutfärdaren anger du följande kommando och anger sedan värden när du uppmanas till det:

   ```shell
   ./CA.sh -newca
   ```

   >[!NOTE]
   >
   >Flera egenskaper i `openssl.cnf` -filen styr beteendet för skriptet CA.sh. Redigera den här filen efter behov innan du skapar din certifikatutfärdare.

#### Skapa certifikat {#creating-the-certificates}

Använd OpenSSL för att skapa de certifikatbegäranden som ska skickas till certifikatutfärdaren från tredje part eller för att signera med din certifikatutfärdare.

När du skapar ett certifikat använder OpenSSL egenskapen Gemensamt namn för att identifiera certifikatinnehavaren. För återgivningsinstansens certifikat använder du instansdatorns värdnamn som gemensamt namn om du konfigurerar Dispatcher att acceptera certifikatet, och bara om det matchar värdnamnet för Publish-instansen. (Se [DispatcherCheckPeerCN](dispatcher-ssl.md#main-pars-title-11) egenskap.)

1. Öppna en terminal och ändra den aktuella katalogen till den katalog som innehåller CH.sh-filen för dina OpenSSL-bibliotek.
1. Ange följande kommando och ange värden när du uppmanas till det. Använd vid behov värdnamnet för publiceringsinstansen som gemensamt namn. Värdnamnet är DNS-matchningsbart namn för återgivningens IP-adress:

   ```shell
   ./CA.sh -newreq
   ```

   Om du använder en tredjepartscertifikatutfärdare skickar du filen newreq.pem till certifikatutfärdaren för signering. Om du agerar som certifikatutfärdare fortsätter du till steg 3.

1. Om du vill signera certifikatet med din certifikatutfärdares certifikat anger du följande kommando:

   ```shell
   ./CA.sh -sign
   ```

   Två filer namngivna `newcert.pem` och `newkey.pem` skapas i den katalog som innehåller dina CA-hanteringsfiler. Dessa två filer är det offentliga certifikatet och den privata nyckeln för återgivningsdatorn.

1. Byt namn `newcert.pem` till `rendercert.pem`och byta namn `newkey.pem` till `renderkey.pem`.
1. Upprepa steg 2 och 3 för att skapa ett certifikat och en offentlig nyckel för Dispatcher-modulen. Se till att du använder ett gemensamt namn som är specifikt för Dispatcher-instansen.
1. Byt namn `newcert.pem` till `dispcert.pem`och byta namn `newkey.pem` till `dispkey.pem`.

### Konfigurera SSL på återgivningsdatorn {#configuring-ssl-on-the-render-computer}

Konfigurera SSL på återgivningsinstansen med `rendercert.pem` och `renderkey.pem` filer.

#### Konvertera återgivningscertifikatet till JKS-format (Java™ KeyStore) {#converting-the-render-certificate-to-jks-format}

Använd följande kommando för att konvertera återgivningscertifikatet, som är en PEM-fil, till en PKCS#12-fil. Inkludera även certifikatet för den certifikatutfärdare som signerade återgivningscertifikatet:

1. I ett terminalfönster ändrar du den aktuella katalogen till platsen för återgivningscertifikatet och den privata nyckeln.
1. Om du vill konvertera återgivningscertifikatet, som är en PEM-fil, till en PKCS#12-fil, anger du följande kommando. Inkludera även certifikatet för den certifikatutfärdare som signerade återgivningscertifikatet:

   ```shell
   openssl pkcs12 -export -in rendercert.pem -inkey renderkey.pem  -certfile demoCA/cacert.pem -out rendercert.p12
   ```

1. Om du vill konvertera PKCS#12-fil till Java™ KeyStore-format (JKS) anger du följande kommando:

   ```shell
   keytool -importkeystore -srckeystore servercert.p12 -srcstoretype pkcs12 -destkeystore render.keystore
   ```

1. Java™-nyckelbehållaren skapas med ett standardalias. Ändra aliaset om du vill:

   ```shell
   keytool -changealias -alias 1 -destalias jettyhttp -keystore render.keystore
   ```

#### Lägga till certifikatutfärdarcertifikatet i återgivningens förtroendelager {#adding-the-ca-cert-to-the-render-s-truststore}

Om du fungerar som certifikatutfärdare importerar du ditt certifikatutfärdarcertifikat till en nyckelbehållare. Konfigurera sedan den JVM som kör återgivningsinstansen så att nyckelbehållaren är tillförlitlig.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2014-08-12T13:11:21.401-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The jetty http service has properties to specify trusted CA certificates for mutual SSL for 6.0. Whether they are operable is undetetermined. See https://issues.adobe.com/browse/DOC-4738.</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;"> </p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For 5.6.1, you would specify the system property javax.net.ssl.trustStore, using the path to cacerts as value.</p>

 -->

1. Använd en textredigerare för att öppna filen cacert.pem och ta bort all text som föregår följande rad:

   `-----BEGIN CERTIFICATE-----`

1. Använd följande kommando för att importera certifikatet till en nyckelbehållare:

   ```shell
   keytool -import -keystore cacerts.keystore -alias myca -storepass password -file cacert.pem
   ```

1. Använd följande systemegenskap för att konfigurera den JVM som kör din renderingsinstans så att nyckelbehållaren är tillförlitlig:

   ```shell
   -Djavax.net.ssl.trustStore=<location of cacerts.keystore>
   ```

   Om du till exempel använder skriptet crx-quickstart/bin/quickstart för att starta din publiceringsinstans kan du ändra egenskapen CQ_JVM_OPTS:

   ```shell
   CQ_JVM_OPTS='-server -Xmx2048m -XX:MaxPermSize=512M -Djavax.net.ssl.trustStore=/usr/lib/cq6.0/publish/ssl/cacerts.keystore'
   ```

#### Konfigurera återgivningsinstansen {#configuring-the-render-instance}

Om du vill konfigurera HTTP-tjänsten för återgivningsinstansen så att den använder SSL använder du återgivningscertifikatet med instruktionerna i *Aktivera SSL på publiceringsinstansen* avsnitt:

* AEM 6.2: [Aktivera HTTP över SSL](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=en)
* AEM 6.1: [Aktivera HTTP över SSL](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=en)
* Äldre AEM: se [den här sidan.](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=en)

### Konfigurera SSL för Dispatcher Module {#configuring-ssl-for-the-dispatcher-module}

Om du vill konfigurera Dispatcher så att ömsesidig SSL används, förbereder du Dispatcher-certifikatet och konfigurerar sedan webbservermodulen.

### Skapa ett enhetligt utskickscertifikat {#creating-a-unified-dispatcher-certificate}

Kombinera Dispatcher-certifikatet och den okrypterade privata nyckeln till en enda PEM-fil. Använda en textredigerare eller `cat` för att skapa en fil som liknar följande exempel:

1. Öppna en terminal och ändra den aktuella katalogen till platsen för filen diskey.pem.
1. Om du vill dekryptera den privata nyckeln anger du följande kommando:

   ```shell
   openssl rsa -in dispkey.pem -out dispkey_unencrypted.pem
   ```

1. Använda en textredigerare eller `cat` om du vill kombinera den okrypterade privata nyckeln och certifikatet i en enda fil som liknar följande exempel:

   ```xml
   -----BEGIN RSA PRIVATE KEY-----
   MIICxjBABgkqhkiG9w0B...
   ...M2HWhDn5ywJsX
   -----END RSA PRIVATE KEY-----
   -----BEGIN CERTIFICATE-----
   MIIC3TCCAk...
   ...roZAs=
   -----END CERTIFICATE-----
   ```

### Ange vilket certifikat som ska användas för Dispatcher {#specifying-the-certificate-to-use-for-dispatcher}

Lägg till följande egenskaper i [Dispatcher-modulens konfiguration](dispatcher-install.md#main-pars-55-35-1022) (in `httpd.conf`):

* `DispatcherCertificateFile`: Sökvägen till den enhetliga certifikatfilen för Dispatcher, som innehåller det offentliga certifikatet och den okrypterade privata nyckeln. Den här filen används när SSL-servern begär Dispatcher-klientcertifikatet.
* `DispatcherCACertificateFile`: Sökvägen till certifikatutfärdarens certifikatfil, som används om SSL-servern visar en certifikatutfärdare som inte är betrodd av en rotutfärdare.
* `DispatcherCheckPeerCN`: Om du vill aktivera ( `On`) eller inaktivera ( `Off`) värdnamnskontroll för fjärrservercertifikat.

Följande kod är en exempelkonfiguration:

```xml
<IfModule disp_apache2.c>
  DispatcherConfig conf/dispatcher.any
  DispatcherLog    logs/dispatcher.log
  DispatcherLogLevel 3
  DispatcherNoServerHeader 0
  DispatcherDeclineRoot 0
  DispatcherUseProcessedURL 0
  DispatcherPassError 0
  DispatcherCertificateFile disp_unified.pem
  DispatcherCACertificateFile cacert.pem
  DispatcherCheckPeerCN On
</IfModule>
```
