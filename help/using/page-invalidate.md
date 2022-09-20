---
title: Invaliderar cachelagrade sidor från AEM
seo-title: Invalidating Cached Pages From Adobe AEM
description: Lär dig hur du konfigurerar interaktionen mellan Dispatcher och AEM för att säkerställa effektiv cachehantering.
seo-description: Learn how to configure the interaction between Adobe AEM Dispatcher and AEM to ensure effective cache management.
uuid: 66533299-55c0-4864-9beb-77e281af9359
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: 79cd94be-a6bc-4d34-bfe9-393b4107925c
exl-id: 90eb6a78-e867-456d-b1cf-f62f49c91851
source-git-commit: f255701f23a628ba0d8b6cd91228462e1b552ffa
workflow-type: tm+mt
source-wordcount: '1421'
ht-degree: 0%

---

# Invaliderar cachelagrade sidor från AEM {#invalidating-cached-pages-from-aem}

När du använder Dispatcher med AEM måste interaktionen konfigureras för att säkerställa effektiv cachehantering. Beroende på din miljö kan konfigurationen även öka prestandan.

## Konfigurera AEM användarkonton {#setting-up-aem-user-accounts}

Standardvärdet `admin` användarkontot används för att autentisera de replikeringsagenter som är installerade som standard. Du bör skapa ett dedikerat användarkonto som kan användas med replikeringsagenter.

Mer information finns i [Konfigurera replikerings- och transportanvändare](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps) i AEM Security Checklist.

## Dispatcher Cache har inte verifierats från redigeringsmiljön {#invalidating-dispatcher-cache-from-the-authoring-environment}

En replikeringsagent på AEM författarinstans skickar en cacheogiltigförklaring till Dispatcher när en sida publiceras. Begäran gör att Dispatcher så småningom uppdaterar filen i cachen när nytt innehåll publiceras.

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-0436B4A35714BFF67F000101@AdobeID)
Last Modified Date: 2017-05-25T10:37:23.679-0400

<p>Hiding this information due to <a href="https://jira.corp.adobe.com/browse/CQDOC-5805">CQDOC-5805</a>.</p>

 -->

Använd följande procedur för att konfigurera en replikeringsagent på AEM författarinstans för att göra Dispatcher-cachen ogiltig vid sidaktivering:

1. Öppna AEM verktygskonsol. (`https://localhost:4502/miscadmin#/etc`)
1. Öppna den nödvändiga replikeringsagenten under Verktyg/replikering/Agenter på författare. Du kan använda agenten Dispatcher Flush som är installerad som standard.
1. Klicka på Redigera och kontrollera att **Aktiverad** är markerat.

1. (valfritt) Markera alternativet **Aliasuppdatering** alternativ.
1. På fliken Transport anger du den URI som behövs för att få åtkomst till Dispatcher.\
   Om du använder standardagenten Dispatcher Flush måste du antagligen uppdatera värdnamnet och porten; till exempel https://&lt;*dispatcherHost*>:&lt;*portApache*>/dispatcher/invalidate.cache

   **Obs!** För Dispatcher Flush-agenter används URI-egenskapen endast om du använder sökvägsbaserade virtualhost-poster för att skilja mellan grupper. Du använder det här fältet för att ange gruppen som ogiltig. Servergrupp #1 har till exempel en virtuell värd för `www.mysite.com/path1/*` och grupp 2 har en virtuell värd för `www.mysite.com/path2/*`. Du kan använda en URL med `/path1/invalidate.cache` för att rikta in sig på den första gården och `/path2/invalidate.cache` för den andra gruppen. Mer information finns i [Använda Dispatcher med flera domäner](dispatcher-domains.md).

1. Konfigurera andra parametrar efter behov.
1. Klicka på OK för att aktivera agenten.

Du kan även få åtkomst till och konfigurera agenten för borttagning av patcher från [Gränssnitt för AEM Touch](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/configuring/replication.html#configuring-a-dispatcher-flush-agent).

Mer information om hur du aktiverar åtkomst till mål-URL:er finns i [Aktivera åtkomst till Vanity-URL:er](dispatcher-configuration.md#enabling-access-to-vanity-urls-vanity-urls).

>[!NOTE]
>
>Agenten för tömning av dispatchercache behöver inte ha ett användarnamn och lösenord, men om den är konfigurerad skickas de med grundläggande autentisering.

Det finns två möjliga problem med detta tillvägagångssätt:

* Dispatcher måste kunna nås från utvecklingsinstansen. Om ditt nätverk (t.ex. brandväggen) är konfigurerat så att åtkomsten mellan de båda är begränsad, är detta inte fallet.

* Publicering och ogiltigförklaring av cache sker samtidigt. Beroende på tidpunkten kan en användare begära en sida precis efter att den tagits bort från cachen, och precis innan den nya sidan publiceras. AEM returnerar nu den gamla sidan och Dispatcher cachelagrar den igen. Detta är mer av ett problem för stora webbplatser.

## Invaliderar Dispatcher Cache från en publiceringsinstans {#invalidating-dispatcher-cache-from-a-publishing-instance}

Under vissa omständigheter kan du göra prestandavinster genom att överföra cachehantering från redigeringsmiljön till en publiceringsinstans. Det är då publiceringsmiljön (inte AEM redigeringsmiljö) som skickar en cacheogiltigförklaring till Dispatcher när en publicerad sida tas emot.

Dessa omständigheter omfattar

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

* Förhindra möjliga timingkonflikter mellan Dispatcher och publiceringsinstansen (se [Dispatcher-cachen från redigeringsmiljön har inte verifierats](#invalidating-dispatcher-cache-from-the-authoring-environment)).
* Systemet innehåller flera publiceringsinstanser som finns på servrar med höga prestanda och endast en redigeringsinstans.

>[!NOTE]
>
>Beslutet att använda denna metod bör fattas av en erfaren AEM.

Flush-åtgärden för dispatcher styrs av en replikeringsagent som körs på publiceringsinstansen. Konfigurationen görs emellertid i redigeringsmiljön och överförs sedan genom att agenten aktiveras:

1. Öppna AEM verktygskonsol.
1. Öppna den nödvändiga replikeringsagenten under Verktyg/replikering/Agenter vid publicering. Du kan använda agenten Dispatcher Flush som är installerad som standard.
1. Klicka på Redigera och kontrollera att **Aktiverad** är markerat.
1. (valfritt) Markera alternativet **Aliasuppdatering** alternativ.
1. På fliken Transport anger du den URI som behövs för att få åtkomst till Dispatcher.\
   Om du använder standardagenten Dispatcher Flush måste du antagligen uppdatera värdnamnet och porten; till exempel `http://<dispatcherHost>:<portApache>/dispatcher/invalidate.cache`

   **Obs!** För Dispatcher Flush-agenter används URI-egenskapen endast om du använder sökvägsbaserade virtualhost-poster för att skilja mellan grupper. Du använder det här fältet för att ange gruppen som ogiltig. Servergrupp #1 har till exempel en virtuell värd för `www.mysite.com/path1/*` och grupp 2 har en virtuell värd för `www.mysite.com/path2/*`. Du kan använda en URL med `/path1/invalidate.cache` för att rikta in sig på den första gården och `/path2/invalidate.cache` för den andra gruppen. Mer information finns i [Använda Dispatcher med flera domäner](dispatcher-domains.md).

1. Konfigurera andra parametrar efter behov.
1. Logga in i publiceringsinstansen och validera justeringsagentens konfiguration. Kontrollera även att det är aktiverat.
1. Upprepa för varje publiceringsinstans som påverkas.

När du har konfigurerat och aktiverar en sida från författaren till publiceringen initierar den här agenten en standardreplikering. Loggen innehåller meddelanden som anger begäranden från din publiceringsserver, som i följande exempel:

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## Invalidera Dispatcher-cachen manuellt {#manually-invalidating-the-dispatcher-cache}

Om du vill göra Dispatcher-cachen ogiltig (eller tömma) utan att aktivera en sida kan du skicka en HTTP-begäran till dispatchern. Du kan till exempel skapa ett AEM program som gör att administratörer eller andra program kan tömma cachen.

HTTP-begäran gör att Dispatcher tar bort specifika filer från cachen. Dispatcher kan sedan uppdatera cachen med en ny kopia.

### Ta bort cachelagrade filer {#delete-cached-files}

Utfärda en HTTP-begäran som får Dispatcher att ta bort filer från cachen. Dispatcher cachelagrar filerna igen endast när de tar emot en klientbegäran för sidan. Att ta bort cachelagrade filer på det här sättet är lämpligt för webbplatser som sannolikt inte tar emot samtidiga begäranden för samma sida.

HTTP-begäran har följande format:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher tömmer (tar bort) de cachelagrade filer och mappar som har namn som matchar värdet i `CQ-Handler` header. Till exempel en `CQ-Handle` av `/content/geomtrixx-outdoors/en` matchar följande objekt:

* Alla filer (med valfritt filtillägg) namngivna `en` i `geometrixx-outdoors` katalog

* Alla kataloger med namnet &quot; `_jcr_content`&quot; nedanför katalogen en (som, om den finns, innehåller cachelagrade återgivningar av sidans undernoder)

Alla andra filer i dispatchercachen (eller upp till en viss nivå, beroende på `/statfileslevel` inställning) ogiltigförklaras genom att `.stat` -fil. Filens senaste ändringsdatum jämförs med det senaste ändringsdatumet för ett cachelagrat dokument och dokumentet hämtas igen om `.stat` filen är nyare. Se [Ogiltiga filer per mappnivå](dispatcher-configuration.md#main-pars_title_26) för mer information.

Invalidering (dvs. beröring av .stat-filer) kan förhindras genom att en extra rubrik skickas `CQ-Action-Scope: ResourceOnly`. Detta kan användas för att tömma vissa resurser utan att göra andra delar av cachen ogiltiga, som JSON-data som skapas dynamiskt och som kräver regelbunden tömning oberoende av cachen (t.ex. som representerar data som hämtas från ett tredjepartssystem för att visa nyheter, börsknappar osv.).

### Ta bort och cachelagra filer {#delete-and-recache-files}

Skicka en HTTP-begäran som får Dispatcher att ta bort cachelagrade filer och omedelbart hämta och cacha filen. Ta bort och cachelagra omedelbart om filer när det är troligt att webbplatser tar emot samtidiga klientbegäranden för samma sida. Omedelbar cachelagring säkerställer att Dispatcher bara hämtar och cachelagrar sidan en gång, i stället för en gång för varje samtidig klientbegäran.

**Obs!** Borttagning och cachelagring av filer bör endast utföras på publiceringsinstansen. När det utförs från författarinstansen inträffar konkurrensförhållanden när försök att hämta resurser görs innan de har publicerats.

HTTP-begäran har följande format:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate   
`Content-Type: text/plain  
CQ-Handle: path-pattern  
Content-Length: numchars in bodypage_path0

page_path1 
...  
page_pathn
```

Sidsökvägarna till direkt cacheminne visas på separata rader i meddelandetexten. Värdet för `CQ-Handle` är sökvägen till en sida som gör sidorna ogiltiga. (Se `/statfileslevel` parametern för [Cache](dispatcher-configuration.md#main-pars_146_44_0010) konfigurationsobjekt.) Följande exempel på HTTP-begäran tar bort och cachelagrar `/content/geometrixx-outdoors/en.html page`:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html
```

### Exempel på tömningsservett {#example-flush-servlet}

I följande kod implementeras en servlet som skickar en ogiltig begäran till Dispatcher. Servern tar emot ett begärandemeddelande som innehåller `handle` och `page` parametrar. Dessa parametrar anger värdet för `CQ-Handle` sidhuvud och sidans sökväg till cachelagring. Servern använder värdena för att konstruera HTTP-begäran för Dispatcher.

När servern distribueras till publiceringsinstansen gör följande URL att Dispatcher tar bort /content/geometrixx-outdoors/en.html och sedan cachelagrar en ny kopia.

`10.36.79.223:4503/bin/flushcache/html?page=/content/geometrixx-outdoors/en.html&handle=/content/geometrixx-outdoors/en/men.html`

>[!NOTE]
>
>Den här exempelservern är inte säker och visar bara hur HTTP Post-begärandemeddelandet används. Lösningen bör skydda åtkomsten till serverutrymmet.

```java
package com.adobe.example;

import org.apache.felix.scr.annotations.Component;
import org.apache.felix.scr.annotations.Service;
import org.apache.felix.scr.annotations.Property;

import org.apache.sling.api.SlingHttpServletRequest;
import org.apache.sling.api.SlingHttpServletResponse;
import org.apache.sling.api.servlets.SlingSafeMethodsServlet;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.apache.commons.httpclient.*;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.httpclient.methods.StringRequestEntity;

@Component(metatype=true)
@Service
public class Flushcache extends SlingSafeMethodsServlet {

 @Property(value="/bin/flushcache")
 static final String SERVLET_PATH="sling.servlet.paths";

 private Logger logger = LoggerFactory.getLogger(this.getClass());

 public void doGet(SlingHttpServletRequest request, SlingHttpServletResponse response) {
  try{ 
      //retrieve the request parameters
      String handle = request.getParameter("handle");
      String page = request.getParameter("page");

      //hard-coding connection properties is a bad practice, but is done here to simplify the example
      String server = "localhost"; 
      String uri = "/dispatcher/invalidate.cache";

      HttpClient client = new HttpClient();

      PostMethod post = new PostMethod("https://"+host+uri);
      post.setRequestHeader("CQ-Action", "Activate");
      post.setRequestHeader("CQ-Handle",handle);
   
      StringRequestEntity body = new StringRequestEntity(page,null,null);
      post.setRequestEntity(body);
      post.setRequestHeader("Content-length", String.valueOf(body.getContentLength()));
      client.executeMethod(post);
      post.releaseConnection();
      //log the results
      logger.info("result: " + post.getResponseBodyAsString());
      }
  }catch(Exception e){
      logger.error("Flushcache servlet exception: " + e.getMessage());
  }
 }
}
```
