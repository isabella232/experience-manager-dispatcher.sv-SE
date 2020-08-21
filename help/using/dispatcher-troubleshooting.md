---
title: Felsökning av Dispatcher-problem
seo-title: Felsökning AEM utskicksproblem
description: Lär dig att felsöka Dispatcher-problem.
seo-description: Lär dig att felsöka AEM Dispatcher-problem.
uuid: 9c109a48-d921-4b6e-9626-1158cebc41e7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: a612e745-f1e6-43de-b25a-9adcaadab5cf
translation-type: tm+mt
source-git-commit: 5734e601379fda9a62eda46bded493b8dbd49a4c
workflow-type: tm+mt
source-wordcount: '553'
ht-degree: 0%

---


# Felsökning av Dispatcher-problem {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Dispatcher-versionerna är oberoende av AEM, men Dispatcher-dokumentationen är inbäddad i AEM. Använd alltid Dispatcher-dokumentationen som är inbäddad i dokumentationen för den senaste versionen av AEM.
>
>Du kan ha omdirigerats till den här sidan om du har följt en länk till Dispatcher-dokumentationen som är inbäddad i dokumentationen för en tidigare version av AEM.

>[!NOTE]
>
>Mer information finns även i [Dispatcher Knowledge Base](https://helpx.adobe.com/cq/kb/index/dispatcher.html), [Troubleshooting Dispatcher Dispatcher Flushing Issues](https://helpx.adobe.com/adobe-cq/kb/troubleshooting-dispatcher-flushing-issues.html) och i Vanliga frågor [om](dispatcher-faq.md) Dispatcher.

## Kontrollera den grundläggande konfigurationen {#check-the-basic-configuration}

Som alltid är de första stegen att kontrollera grunderna:

* [Bekräfta grundläggande åtgärd](#ConfirmBasicOperation)
* Kontrollera alla loggfiler för webbservern och dispatchern. Om det behövs kan du öka `loglevel` antalet som används för [loggning](#Logging)av avsändare.

* [Kontrollera konfigurationen](#ConfiguringtheDispatcher):

   * Har du flera utskickare?

      * Har du fastställt vilken Dispatcher som hanterar den webbplats/sida som du undersöker?
   * Har du implementerat filter?

      * Påverkar detta ditt ärende?


## Diagnostikverktyg för IIS {#iis-diagnostic-tools}

IIS innehåller olika spårningsverktyg, beroende på den faktiska versionen:

* IIS 6 - IIS-diagnostikverktygen kan hämtas och konfigureras
* IIS 7 - kalkeringen är helt integrerad

Dessa kan hjälpa dig att övervaka aktiviteten.

## IIS och 404 hittades inte {#iis-and-not-found}

När du använder IIS kanske du får en `404 Not Found` returnerad version i olika scenarier. Om så är fallet, se följande artiklar i kunskapsbasen.

* [IIS 6/7: POSTEN returnerar 404](https://helpx.adobe.com/dispatcher/kb/IIS6IsapiFilters.html)
* [IIS 6: Begäranden till URL:er som innehåller `/bin` bassökvägen `404 Not Found`](https://helpx.adobe.com/dispatcher/kb/RequestsToBinDirectoryFailInIIS6.html)

Du bör också kontrollera att dispatcherns cacherot och IIS-dokumentroten är inställda på samma katalog.

## Problem med att ta bort arbetsflödesmodeller {#problems-deleting-workflow-models}

**Symtom**

Problem med att ta bort arbetsflödesmodeller när en AEM författarinstans öppnas via Dispatcher.

**Steg som ska återskapas:**

1. Logga in på din författarinstans (bekräfta att begäranden dirigeras via dispatchern).
1. Skapa ett nytt arbetsflöde, till exempel med Title inställd på workflowToDelete.
1. Bekräfta att arbetsflödet har skapats.
1. Högerklicka på arbetsflödet och klicka sedan på **Ta bort**.

1. Bekräfta genom att klicka på **Ja** .
1. Ett felmeddelande visas:\
   &quot; `ERROR 'Could not delete workflow model!!`&quot;.

**Upplösning**

Lägg till följande rubriker i `/clientheaders` avsnittet i `dispatcher.any` filen:

* `x-http-method-override`
* `x-requested-with`

```
{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}
```

## Interferens med mod_dir (Apache) {#interference-with-mod-dir-apache}

Detta beskriver hur dispatchern interagerar med `mod_dir` inuti webbservern Apache, eftersom detta kan leda till olika, potentiellt oväntade effekter:

### Apache 1.3 {#apache}

I Apache 1.3 `mod_dir` hanteras varje begäran där URL:en mappas till en katalog i filsystemet.

Den kommer antingen att

* omdirigera begäran till en befintlig `index.html` fil
* generera en kataloglista

När dispatchern är aktiverad bearbetar den sådana förfrågningar genom att registrera sig själv som en hanterare för innehållstypen `httpd/unix-directory`.

### Apache 2.x {#apache-x}

I Apache 2.x är det annorlunda. En modul kan hantera olika faser av begäran, till exempel URL-korrigering. `mod_dir` hanterar det här steget genom att dirigera om en begäran (när URL:en mappar till en katalog) till URL:en med ett `/` tillägg.

Dispatcher fångar inte upp `mod_dir` korrigeringen, men hanterar begäran fullständigt till den omdirigerade URL:en (d.v.s. med `/` tillägg). Detta kan utgöra ett problem om fjärrservern (t.ex. AEM) hanterar begäranden som `/a_path` skiljer sig från begäranden till `/a_path/` (när `/a_path` mappar till en befintlig katalog).

Om detta händer måste du antingen:

* inaktivera `mod_dir` för det `Directory` eller `Location` underträd som hanteras av dispatchern

* använd `DirectorySlash Off` för att konfigurera `mod_dir` att inte lägga till `/`
