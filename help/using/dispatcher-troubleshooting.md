---
title: Felsökning av Dispatcher-problem
seo-title: Troubleshooting AEM Dispatcher Problems
description: Lär dig att felsöka Dispatcher-problem.
seo-description: Learn to troubleshoot AEM Dispatcher issues.
uuid: 9c109a48-d921-4b6e-9626-1158cebc41e7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: a612e745-f1e6-43de-b25a-9adcaadab5cf
exl-id: 29f338ab-5d25-48a4-9309-058e0cc94cff
source-git-commit: 26c8edbb142297830c7c8bd068502263c9f0e7eb
workflow-type: tm+mt
source-wordcount: '560'
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
>Kontrollera [Dispatcher Knowledge Base](https://helpx.adobe.com/experience-manager/kb/index/dispatcher.html), [Felsökning av problem med att tömma dispatcher](https://experienceleague.adobe.com/search.html?lang=en#q=troubleshooting%20dispatcher%20flushing%20issues&amp;sort=relevancy&amp;f:el_product=[Experience%20Manager]) och [Vanliga frågor och svar om Dispatcher](dispatcher-faq.md) för ytterligare information.

## Kontrollera den grundläggande konfigurationen {#check-the-basic-configuration}

Som alltid är de första stegen att kontrollera grunderna:

* [Bekräfta grundläggande åtgärd](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Kontrollera alla loggfiler för webbservern och Dispatcher. Öka `loglevel` används för Dispatcher [loggning](/help/using/dispatcher-configuration.md#logging).

* [Kontrollera konfigurationen](/help/using/dispatcher-configuration.md):

   * Har du flera utskickare?

      * Har du fastställt vilken Dispatcher som hanterar webbplatsen/sidan som du undersöker?
   * Har du implementerat filter?

      * Påverkar de här filtren det du undersöker?


## Diagnostikverktyg för IIS {#iis-diagnostic-tools}

IIS innehåller olika spårningsverktyg, beroende på den faktiska versionen:

* IIS 6 - IIS-diagnostikverktygen kan hämtas och konfigureras
* IIS 7 - kalkeringen är helt integrerad

De här verktygen kan hjälpa dig att övervaka aktiviteten.

## IIS och 404 hittades inte {#iis-and-not-found}

När du använder IIS kan du uppleva `404 Not Found` returneras i olika scenarier. Om så är fallet, se följande artiklar i kunskapsbasen.

* [IIS 6/7: POSTEN returnerar 404](https://helpx.adobe.com/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6: Begäranden till URL:er som innehåller bassökvägen `/bin` returnera en `404 Not Found`](https://helpx.adobe.com/experience-manager/kb/RequestsToBinDirectoryFailInIIS6.html)

Kontrollera också att Dispatcher-cacheroten och IIS-dokumentroten är inställda på samma katalog.

## Problem med att ta bort arbetsflödesmodeller {#problems-deleting-workflow-models}

**Symtom**

Problem med att ta bort arbetsflödesmodeller när en AEM författarinstans öppnas via Dispatcher.

**Steg som ska återskapas:**

1. Logga in på din författarinstans (bekräfta att begäranden dirigeras via Dispatcher).
1. Skapa ett arbetsflöde, till exempel med Title inställd på workflowToDelete.
1. Bekräfta att arbetsflödet har skapats.
1. Markera och högerklicka på arbetsflödet och klicka sedan på **Ta bort**.

1. Klicka **Ja** för att bekräfta.
1. En felmeddelanderuta med följande information visas:\
   &quot; `ERROR 'Could not delete workflow model!!`&quot;.

**Upplösning**

Lägg till följande rubriker i `/clientheaders` i `dispatcher.any` fil:

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

Den här processen beskriver hur Dispatcher interagerar med `mod_dir` inuti Apache-webbservern eftersom den kan ge upphov till olika, potentiellt oväntade effekter:

### Apache 1.3 {#apache}

I Apache 1.3 `mod_dir` hanterar varje begäran där URL:en mappar till en katalog i filsystemet.

Den kommer antingen att

* omdirigera begäran till en befintlig `index.html` fil
* generera en kataloglista

När Dispatcher är aktiverad bearbetar den sådana förfrågningar genom att registrera sig själv som en hanterare för innehållstypen `httpd/unix-directory`.

### Apache 2.x {#apache-x}

I Apache 2.x är det annorlunda. En modul kan hantera olika faser av begäran, till exempel URL-korrigering. The `mod_dir` hanterar det här steget genom att dirigera om en begäran (när URL:en mappar till en katalog) till URL:en med en `/` tillagd.

Dispatcher fångar inte `mod_dir` korrigering, men hanterar begäran fullständigt till den omdirigerade URL:en (det vill säga med `/` tillagd). Den här processen kan utgöra ett problem om fjärrservern (till exempel AEM) hanterar begäranden till `/a_path` skiljer sig från förfrågningar till `/a_path/` (när `/a_path` mappar till en befintlig katalog).

Om detta händer måste du antingen:

* disable `mod_dir` för `Directory` eller `Location` underträd som hanteras av Dispatcher

* use `DirectorySlash Off` för att konfigurera `mod_dir` inte att lägga till `/`
