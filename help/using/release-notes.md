---
title: AEM Dispatcher Release Notes
seo-title: AEM Dispatcher Release Notes
description: Versionsinformation om Adobe Experience Manager Dispatcher
seo-description: Release notes specific to Adobe Experience Manager Dispatcher
uuid: ae3ccf62-0514-4c03-a3b9-71799a482cbd
topic-tags: release-notes
content-type: reference
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9-fa896393aac5
exl-id: b55c7a34-d57b-4d45-bd83-29890f1524de
source-git-commit: 377a104f7e8e506e2f61002d90851a38657e8fe5
workflow-type: tm+mt
source-wordcount: '977'
ht-degree: 2%

---

# AEM Dispatcher Release Notes{#aem-dispatcher-release-notes}

## Versionsinformation {#release-information}

|  |  |
|--- |--- |
| Produkter | Adobe Experience Manager (AEM) Dispatcher |
| Version | 4.3.5 |
| Typ | Mindre release |
| Date | 4 april 2022 |
| Hämta URL | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services (IIS)](release-notes.md#iis)</li></ul> |
| Kompatibilitet | AEM 6.1 eller senare |

## Systemkrav och krav {#system-requirements-and-prerequisites}

Se [Plattformar som stöds](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/technical-requirements.html)för mer information om krav och krav.

Adobe rekommenderar starkt att du använder den senaste versionen av AEM Dispatcher för att få tillgång till den senaste funktionen, de senaste felkorrigeringarna och bästa möjliga prestanda.

## Installationsanvisningar {#installation-instructions}

Detaljerade anvisningar finns i [Installerar Dispatcher](dispatcher-install.md).

## Versionshistorik {#release-history}

### Version 4.3.5 (2022-Apr-04) {#apr}

**Förbättringar**:

* DISP-954 - Supportogiltigförklaring även om utgångsdatum inte har passerats
* DISP-949 - Dispatcher returnerar 200 i stället för 404, även om POSTEN blockeras av filter

### Version 4.3.4 (2021-Nov-29) {#nov}

**Felkorrigeringar**:

* DISP-833 - X-Forwarded-Host headers kan innehålla en lista med kommaavgränsade värdnamn
* DISP-835 - DispatcherUseForwardedHost-värdhuvud om det kommer sist

**Förbättringar**:

* DISP-874 - Skapar en dispatcherkonfiguration som aktiverar eller inaktiverar implementering av DISP-818 via en flagga `DispatcherRestrictUncacheableContent`. Standardvärdet är Av. När På tas alla cachelagringshuvuden som anges med mod bort för innehåll som inte kan cachelagras. Detta skiljer sig från beteendet i version 4.3.3 (där standardinställningen var På) men det är samma som i tidigare versioner än 4.3.3 (där standardinställningen var Av). Keeping `DispatcherRestrictUncacheableContent`Vi rekommenderar att webbläsarens cacheminne är avstängt som standard, vilket ger större flexibilitet. Om du vid uppgradering från version 4.3.3 till 4.3.4 vill behålla samma beteende som i version 4.3.3 måste du uttryckligen ange `DispatcherRestrictUncacheableContent` till På.
* DISP-841 - Dispatcher respekterar inte /serverStaleOnError för 504-svarskod
* DISP-874 - Skapa en dispatcherkonfiguration som aktiverar eller inaktiverar implementeringen av DISP-818
* DISP-883 - Spåra som visar URL-begäran, disposition i Dispatcher
* DISP-944 - anvä-url före inläsning

### Version 4.3.3 (2019-Oct-18) {#october}

**Felkorrigeringar**:

* DISP-739 - LogLevel-dispatcher: **nivå** fungerar inte
* DISP-749 - Alpine Linux-dispatchern kraschar med spårloggsnivå

**Förbättringar**:

* DISP-813 - Stöd i Dispatcher för openssl 1.1.x
* DISP-814 - Apache 40x-fel under cachetömningar
* DISP-818 - mod_expirres lägger till Cache-Control-rubriker för innehåll som inte kan cache-lagras
* DISP-821 - Lagra inte loggkontext i socket
* DISP-822 - Dispatcher ska använda pell i stället för pselect
* DISP-824 - Secure DispatcherUseForwardedHost
* DISP-825 - Logga specialmeddelanden när det inte finns mer utrymme på disken
* DISP-826 - Stöd för uppdatering av URI:er med en frågesträng

**Nya funktioner**:

* DISP-703 - Träffgrad för servergruppsspecifik cache
* DISP-827 - lokal server för testning
* DISP-828 - Skapa testdockarbild för dispatcher

### Version 4.3.2 (2019-Jan-31) {#jan}

**Felkorrigeringar**:

* DISP-734 - Dispatcher orsakar krasch i insert_output_filter om det inte anges som hanterare
* DISP-735 - REs fungerar inte i Alpine Linux
* DISP-740 - Inläsning av dispatcher i macOS Mojave är inaktiverat som standard
* DISP-742 - Blockerade begäranden kan läcka information för att autentisera skyddade resurser

**Förbättringar**:

* DISP-746 - Omärkta strängar i dispatcher.any bör generera en varning

**Ny funktion**:

* DISP-747 - Ange begärandeinformation i Apache-miljön

### Version 4.3.1 (2018-Oct-16) {#oct}

**Felkorrigeringar**:

* DISP-656 - Dispatcher returnerar fel ETag Header
* DISP-694 - Utelämna varningar när aktiva anslutningar förblir oförändrade
* DISP-714 - Cookie-baserad sessionshantering fungerar inte i IIS
* DISP-715 - Säker flagga för återgiven cookie
* DISP-720 - Temporära filer som inte stängs kan leda till att filen blir slut (för många öppna filer)
* DISP-721 - Dispatcher avbryter poll() när Apache startar om underordnat
* DISP-722 - Cachefiler skapas med oktalt läge 0600
* DISP-723 - implicit 10-minuters timeout (och försök igen) när återgivningens tidsgräns är 0
* DISP-725 - Efterföljande tecken efter strängar konverteras tyst till namnlöst värde
* DISP-726 - Logga en varning när ingen servergrupp faktiskt matchar den inkommande värden
* DISP-727 - Dispatcher kontrollerar innehållslängden för begäranden om tomma cachefiler
* DISP-730-404 vid försök att komma åt rubrikfil via dispatcher
* DISP-731 - Dispatcher är sårbar för Log Injection
* DISP-732 - Dispatcher ska ta bort efterföljande&quot;/&quot; i URL:en
* DISP-733 - Dispatcher ska ange (beräkna) en åldersrubrik

**Förbättringar**:

* DISP-656 - Dispatcher returnerar fel ETag Header
* DISP-694 - Utelämna varningar när aktiva anslutningar förblir oförändrade
* DISP-715 - Säker flagga för återgiven cookie
* DISP-722 - Cachefiler skapas med oktalt läge 0600
* DISP-726 - Logga en varning när ingen servergrupp faktiskt matchar den inkommande värden

### Version 4.3.0 (2018-Jun-13) {#jun}

**Felkorrigeringar**:

* DISP-682 - Numerisk loggnivå används felaktigt
* DISP-685 - 32-bitars Solaris SPARC-binärfiler har en odefinierad referens till __divdi3
* DISP-688 - Dispatcher returnerar inte X-Cache-Info-huvudet på 404-svar
* DISP-690 - Rubriken Senast ändrad är inte tillgänglig
* DISP-691 - åtkomstfel i w3wp.exe
* DISP-693 - Behöver uppdatera arkitekturinformation för solarisservrar på hämtningssidan för dispatcher
* DISP-695 - Problem med DispatcherLog-nivån i Dispatcher-modulen 4.2.3
* DISP-698 - Dispatcher TTL måste ha stöd för s-maxage och privata direktiv
* DISP-700 - Modulen fungerar inte korrekt i Alpine Linux
* DISP-704 - Webbläsarbegäranden som innehåller %2b skickas till utgivaren utan kodning
* DISP-705 - Dispatcher kraschar på grund av att programmet är dubbelt ledigt eller skadat (snabbast)
* DISP-706 - Vid ogiltigförklaring följer dispatchern bakåtreferenssymboler som kan orsaka en oändlig slinga
* DISP-709 - Blockera vissa tillägg för innehålls-URL
* DISP-710 - Bygger för Linux som inte kan användas i Cent OS 6

**Förbättringar**:

* DISP-652 - Dispatcher visar fel datumhuvud

## Användbara resurser {#helpful-resources}

* [AEM Dispatcher - översikt](dispatcher.md)

## Nedladdningar {#downloads}

### Apache 2.4 {#apache}

| Plattform | Arkitektur | Stöd för OpenSSL | Hämta |
|---|---|---|---|
| Linux | i686 (32-bitars) | Inget | [dispatcher-apache2.4-linux-i686-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.5.tar.gz) |
| Linux | i686 (32-bitars) | 1.0 | [dispatcher-apache2.4-linux-i686-ssl1.0-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.5.tar.gz) |
| Linux | i686 (32-bitars) | 1.1 | [dispatcher-apache2.4-linux-i686-ssl1.1-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.5.tar.gz) |
| Linux | x86_64 (64-bitars) | Inget | [dispatcher-apache2.4-linux-x86_64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.5.tar.gz) |
| Linux | x86_64 (64-bitars) | 1.0 | [dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.5.tar.gz) |
| Linux | x86_64 (64-bitars) | 1.1 | [dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.5.tar.gz) |
| macOS | x86_64 (64-bitars) | Inget | [dispatcher-apache2.4-darwin-x86_64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.5.tar.gz) |

### IIS {#iis}

| Plattform | Arkitektur | Stöd för OpenSSL | Hämta |
|---|---|---|---|
| Windows | x86 (32-bitars) | Inget | [dispatcher-iis-windows-x86-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.5.zip) |
| Windows | x86 (32-bitars) | 1.0 | [dispatcher-iis-windows-x86-ssl1.0-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.5.zip) |
| Windows | x86 (32-bitars) | 1.1 | [dispatcher-iis-windows-x86-ssl1.1-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.5.zip) |
| Windows | x64 (64-bitars) | Inget | [dispatcher-iis-windows-x64-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.5.zip) |
| Windows | x64 (64-bitars) | 1.0 | [dispatcher-iis-windows-x64-ssl1.0-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.5.zip) |
| Windows | x64 (64-bitars) | 1.1 | [dispatcher-iis-windows-x64-ssl1.1-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.5.zip) |
