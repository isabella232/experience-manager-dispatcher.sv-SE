---
title: Checklistan för Dispatcher-säkerhet
seo-title: The Dispatcher Security Checklist
description: En checklista för säkerhet som ska slutföras innan produktionen påbörjas.
seo-description: A security checklist that should be completed before going on production.
uuid: 7bfa3202-03f6-48e9-8d2e-2a40e137ecbe
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: fbfafa55-c029-4ed7-ab3e-1bebfde18248
jcr-lastmodifiedby: remove-legacypath-6-1
index: y
internal: n
snippet: y
exl-id: 49009810-b5bf-41fd-b544-19dd0c06b013
source-git-commit: a21b527700b3a5c3a11eadaefc729e754ba2b37b
workflow-type: tm+mt
source-wordcount: '638'
ht-degree: 0%

---

# Checklistan för Dispatcher-säkerhet{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

Adobe rekommenderar att du slutför följande checklista innan du börjar producera.

>[!CAUTION]
>
>Du måste också fylla i checklistan för dokumentskydd för din version av AEM innan du publicerar. Se motsvarande [Adobe Experience Manager-dokumentation](https://helpx.adobe.com/experience-manager/6-5/sites/administering/using/security-checklist.html).

## Använd den senaste versionen av Dispatcher {#use-the-latest-version-of-dispatcher}

Du bör installera den senaste tillgängliga versionen för din plattform. Du bör uppgradera din Dispatcher-instans för att använda den senaste versionen och dra nytta av produkt- och säkerhetsförbättringarna. Se [Installerar Dispatcher](dispatcher-install.md).

>[!NOTE]
>
>Du kan kontrollera den aktuella versionen av din dispatcherinstallation genom att titta i dispatcherloggfilen.
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>Om du vill hitta loggfilen kontrollerar du dispatcherkonfigurationen i din `httpd.conf`.

## Begränsa klienter som kan tömma cachen {#restrict-clients-that-can-flush-your-cache}

Adobe rekommenderar att du [begränsa vilka klienter som kan tömma cachen.](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)

## Aktivera HTTPS för transportlagersäkerhet {#enable-https-for-transport-layer-security}

Adobe rekommenderar att du aktiverar HTTPS-transportlager på både författare- och publiceringsinstanser.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:41:28.841-0400

<p>Recommended to have SSL termination, front end SSL.</p> 
<p>Question is do we want to have SSL communication between dispatcher and AEM instances (publish and/or author).</p> 
<p>We might want to have two items:</p> 
<ul> 
 <li>MUST HTTPS clients -&gt; dispatcher / load balancer</li> 
 <li>NICE load balancer -&gt; dispatcher<br /> </li> 
 <li>NICE dispatcher -&gt; instances if sensitive information such as credit cards / or infrastructure requirements such as DMZ</li> 
</ul>

 -->

## Begränsa åtkomst {#restrict-access}

När du konfigurerar Dispatcher bör du begränsa den externa åtkomsten så mycket som möjligt. Se [Exempel/filteravsnitt](dispatcher-configuration.md#main-pars_184_1_title) i Dispatcher-dokumentationen.

## Kontrollera att åtkomst till administrativa URL:er nekas {#make-sure-access-to-administrative-urls-is-denied}

Se till att du använder filter för att blockera extern åtkomst till administrativa URL:er, t.ex. webbkonsolen.

Se [Testar Dispatcher Security](dispatcher-configuration.md#testing-dispatcher-security) för en lista över URL:er som måste blockeras.

## Använd Tillåtelselista i stället för Blockeringslista {#use-allowlists-instead-of-blocklists}

Tillåtelselista är ett bättre sätt att tillhandahålla åtkomstkontroll eftersom de till sin natur antar att alla åtkomstbegäranden ska nekas såvida de inte uttryckligen ingår i tillåtelselista. Den här modellen ger mer restriktiv kontroll över nya begäranden som kanske inte har granskats än eller beaktats under en viss konfigurationsfas.

## Kör Dispatcher med en dedikerad systemanvändare {#run-dispatcher-with-a-dedicated-system-user}

När du konfigurerar Dispatcher bör du se till att webbservern körs av en dedikerad användare med minst behörighet. Vi rekommenderar att du bara ger skrivåtkomst till cachemappen för dispatchern.

IIS-användare måste dessutom konfigurera sin webbplats på följande sätt:

1. I inställningen för fysisk sökväg för din webbplats väljer du **Anslut som specifik användare**.
1. Ange användaren.

## Förhindra DoS-attacker (Denial of Service) {#prevent-denial-of-service-dos-attacks}

En denial of service-attack (DoS) är ett försök att göra en datorresurs otillgänglig för de avsedda användarna.

På dispatchernivå finns det två metoder för att konfigurera för att förhindra DoS-attacker: [](https://docs.adobe.com/content/docs/en/dispatcher.html#/filter (Filter))

* Använd modulen mod_rewrite (till exempel) [Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html)) för att utföra URL-valideringar (om URL-mönsterreglerna inte är för komplexa).

* Förhindra att avsändaren cachelagrar URL:er med falska tillägg genom att använda [filter](dispatcher-configuration.md#configuring-access-to-conten-tfilter).\
   Ändra till exempel cachningsreglerna för att begränsa cachning till de förväntade MIME-typerna, som:

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`

   En exempelkonfigurationsfil finns för [begränsa extern åtkomst](#restrict-access)innehåller detta begränsningar för MIME-typer.

Om du vill aktivera alla funktioner för publiceringsinstanserna på ett säkert sätt konfigurerar du filter så att de inte får åtkomst till följande noder:

* `/etc/`
* `/libs/`

Konfigurera sedan filter för att ge åtkomst till följande nodsökvägar:

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*` (JS, CSS och JSON)
* `/libs/cq/security/userinfo.json` (CQ-användarinformation)
* `/libs/granite/security/currentuser.json` (**data får inte cachelagras**)

* `/libs/cq/i18n/*` (Internalisering)

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:38:17.016-0400

<p>We need to highlight whether a path applies to all versions or specific ones.<br /> </p>

 -->

## Konfigurera Dispatcher för att förhindra CSRF-attacker {#configure-dispatcher-to-prevent-csrf-attacks}

AEM tillhandahåller en [ramverk](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps) som syftar till att förhindra attacker av typen cross-site Request. För att du ska kunna använda det här ramverket måste du tillåtslista CSRF-tokenstöd i dispatchern. Du kan göra detta genom att:

1. Skapa ett filter som tillåter `/libs/granite/csrf/token.json` sökväg,
1. Lägg till `CSRF-Token` sidhuvud till `clientheaders` i Dispatcher-konfigurationen.

## Förhindra clickjacking {#prevent-clickjacking}

För att förhindra clickjacking rekommenderar vi att du konfigurerar webbservern så att den `X-FRAME-OPTIONS` HTTP-huvudet är inställt på `SAMEORIGIN`.

Mer [information om clickjacking finns på OWASP-webbplatsen](https://www.owasp.org/index.php/Clickjacking).

## Utför ett penetrationstest {#perform-a-penetration-test}

Adobe rekommenderar starkt att du utför ett penetrationstest av din AEM infrastruktur innan du börjar producera.
