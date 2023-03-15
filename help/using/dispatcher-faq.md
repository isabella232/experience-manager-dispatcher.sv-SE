---
title: Skicka de vanligaste frågorna
seo-title: Top issues for AEM Dispatcher
description: De vanligaste frågorna för AEM Dispatcher
seo-description: Top issues for Adobe AEM Dispatcher
exl-id: 4dcc7318-aba5-4b17-8cf4-190ffefbba75
source-git-commit: 26c8edbb142297830c7c8bd068502263c9f0e7eb
workflow-type: tm+mt
source-wordcount: '1578'
ht-degree: 0%

---

# Vanliga frågor och svar om AEM Dispatcher

![Konfigurera Dispatcher](assets/CQDispatcher_workflow_v2.png)

## Introduktion

### Vad är Dispatcher?

Dispatcher är ett Adobe Experience Manager verktyg för cachelagring och/eller belastningsutjämning som gör att du kan skapa en snabb och dynamisk webbmiljö. För cachelagring fungerar Dispatcher som en del av en HTTP-server, till exempel Apache. Syftet är att lagra (eller&quot;cachelagra&quot;) så mycket av det statiska webbplatsinnehållet som möjligt och att så sällan som möjligt få åtkomst till webbplatsens layoutmotor. I en lastbalanserande roll distribuerar Dispatcher användarförfrågningar (inläsning) över olika AEM instanser (återgivningar).

För cachelagring använder modulen Dispatcher webbserverns förmåga att hantera statiskt innehåll. Dispatcher placerar de cachelagrade dokumenten i dokumentroten på webbservern.

### Hur utför Dispatcher cachelagring?

Dispatcher använder webbserverns förmåga att hantera statiskt innehåll. Dispatcher lagrar cachelagrade dokument i webbserverns dokumentrot. Dispatcher har två primära metoder för att uppdatera cacheinnehållet när ändringar görs på webbplatsen.

* **Innehållsuppdateringar** ta bort sidor som har ändrats och filer som är direkt kopplade till dem.
* **Automatisk invalidering** gör automatiskt de delar av cachen som kan vara inaktuella efter en uppdatering blir ogiltiga. Det flaggar till exempel att relevanta sidor är inaktuella, utan att något tas bort.

### Vilka är fördelarna med lastbalansering?

Belastningsutjämning distribuerar användarförfrågningar (inläsning) över flera AEM instanser. I följande lista beskrivs fördelarna med belastningsutjämning:

* **Ökad processorkraft**: I praktiken innebär den här metoden att Dispatcher delar dokumentbegäranden mellan flera instanser av AEM. Eftersom varje instans har färre dokument att behandla har du snabbare svarstider. Dispatcher sparar intern statistik för varje dokumentkategori så att den kan beräkna inläsningen och distribuera frågorna effektivt.
* **Ökad felsäker täckning**: Om Dispatcher inte tar emot svar från en instans vidarebefordrar den automatiskt begäranden till en av de andra instanserna. Om en instans blir otillgänglig är den enda effekten en nedgång av webbplatsen, som står i proportion till den förlorade datorkraften.

>[!NOTE]
>
>Mer information finns i [Dispatcher - översikt](dispatcher.md)

## Installera och konfigurera

### Var hämtar jag Dispatcher-modulen från?

Du kan hämta den senaste Dispatcher-modulen från [Dispatcher Release Notes](release-notes.md) sida.

### Hur installerar jag modulen Dispatcher?

Se [Installerar Dispatcher](dispatcher-install.md) page

### Hur konfigurerar jag modulen Dispatcher?

Se [Konfigurera Dispatcher](dispatcher-configuration.md) sida.

### Hur konfigurerar jag Dispatcher för författarinstansen?

Se [Använda Dispatcher med en författarinstans](dispatcher.md#using-a-dispatcher-with-an-author-server) för de detaljerade stegen.

### Hur konfigurerar jag Dispatcher med flera domäner?

Du kan konfigurera CQ Dispatcher med flera domäner, förutsatt att domänerna uppfyller följande villkor:

* Webbinnehållet för båda domänerna lagras i en enda AEM
* Filerna i Dispatcher-cachen kan ogiltigförklaras separat för varje domän

Läs [Använda Dispatcher med flera domäner](dispatcher-domains.md) för mer information.

### Hur konfigurerar jag Dispatcher så att alla begäranden från en användare dirigeras till samma Publish-instans?

Du kan använda [klisterlappande anslutningar](dispatcher-configuration.md#identifying-a-sticky-connection-folder-stickyconnectionsfor) som ser till att alla dokument för en användare bearbetas i samma instans av AEM. Den här funktionen är viktig om du använder personaliserade sidor och sessionsdata. Data lagras på instansen. Därför måste efterföljande begäranden från samma användare returnera till den instansen, annars går data förlorade.

Eftersom häftiga anslutningar begränsar Dispatcher möjlighet att optimera begäranden bör du bara använda den här metoden när det behövs. Du kan ange den mapp som innehåller de&quot;klisterlappande&quot; dokumenten, så att alla dokument i mappen behandlas på samma plats för en användare.

### Kan jag använda kladdiga anslutningar och cachelagring tillsammans?

För de flesta sidor där klisterlappande anslutningar används bör du inaktivera cachelagring. I annat fall visas samma instans av sidan för alla användare, oavsett sessionsinnehållet.

I vissa program kan du använda både fast anslutning och cachelagring. Om du till exempel visar ett formulär som skriver data till en session, kan du använda kladdiga anslutningar och cachelagring tillsammans.

### Kan en Dispatcher och en AEM Publish-instans finnas på samma fysiska dator?

Ja, om maskinen är tillräckligt kraftfull. Du bör dock konfigurera Dispatcher och AEM Publish-instansen på olika datorer.

Vanligtvis finns Publish-instansen inuti brandväggen och Dispatcher finns i DMZ. Om du väljer att ha både Publish-instansen och Dispatcher på samma fysiska dator måste du se till att brandväggsinställningarna förhindrar direktåtkomst till Publish-instansen från externa nätverk.

### Kan jag bara cachelagra filer med specifika tillägg?

Ja. Om du till exempel bara vill cachelagra GIF-filer anger du *.gif i cache-avsnittet i dispatcher.any-konfigurationsfilen.

### Hur tar jag bort filer från cachen?

Du kan ta bort filer från cachen genom att använda en HTTP-begäran. När HTTP-begäran tas emot tar Dispatcher bort filerna från cachen. Dispatcher cachelagrar filerna igen endast när de tar emot en klientbegäran för sidan. Att ta bort cachelagrade filer på det här sättet är lämpligt för webbplatser som sannolikt inte tar emot samtidiga begäranden för samma sida.

HTTP-begäran har följande syntax:

```
POST /dispatcher/invalidate.cache HTTP/1.1
CQ-Action: Activate
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher tar bort de cachelagrade filer och mappar som har namn som matchar värdet i CQ-Handle-huvudet. En CQ-Handle med `/content/geomtrixx-outdoors/en` matchar följande objekt:

Alla filer (oavsett filtillägg) med namnet en i katalogen geometrixx-outdoor.
Alla kataloger med namn `_jcr_content` nedanför katalogen en (som, om den finns, innehåller cachelagrade återgivningar av sidans undernoder).
Katalogen `en` tas bara bort om `CQ-Action` är `Delete` eller `Deactivate`.

Mer information om detta avsnitt finns på [Invalidera Dispatcher-cachen manuellt](page-invalidate.md).

### Hur implementerar jag behörighetskänslig cachelagring?

Se [Cachelagra säkert innehåll](permissions-cache.md) sida.

### Hur skyddar jag kommunikationen mellan Dispatcher- och CQ-instanserna?

Se [Dispatcher Security Checklist](security-checklist.md) och [AEM](https://experienceleague.adobe.com/docs/experience-manager-64/administering/security/security-checklist.html?lang=en) sidor.

### Skickaproblem `jcr:content` ändrat till `jcr%3acontent`

**Fråga**: Företaget har nyligen stött på ett problem på Dispatcher-nivå. En av AJAX samtal som fick data från CQ-databasen hade `jcr:content` i den. Det blev kodat till `jcr%3acontent` vilket resulterar i fel resultatmängd.

**Svar**: Använd `ResourceResolver.map()` metod för att hämta en Friendly-URL som ska användas/utfärdas, hämta begäranden från och även för att lösa cachelagringsproblemet med Dispatcher. Metoden map() kodar `:` kolon till understreck och metoden resolve() avkodar dem till SLING JCR-läsbart format. Använd metoden map() för att generera den URL som används i Ajax-anropet.

Läs mer: [https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling](https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling)

## Rensa Dispatcher

### Hur konfigurerar jag push-agenter för Dispatcher i en Publish-instans?

Se [Replikering](https://experienceleague.adobe.com/docs/experience-manager-64/deploying/configuring/replication.html?lang=en#configuring-your-replication-agents) sida.

### Hur felsöker jag problem med att tömma Dispatcher?

[Se de här felsökningsartiklarna](https://experienceleague.adobe.com/search.html?lang=en#q=troubleshooting%20dispatcher%20flushing%20issues&amp;sort=relevancy&amp;f:el_product=[Experience%20Manager]).

Om åtgärderna Ta bort gör att Dispatcher rensas [använd lösningen i det här blogginlägget från Sensei Martin](https://mkalugin-cq.blogspot.com/2012/04/i-have-been-working-on-following.html).

### Hur tömmer jag DAM-resurser från Dispatcher-cachen?

Du kan använda funktionen &quot;kedjereplikering&quot;. När den här funktionen är aktiverad skickar Dispatcher flush-agenten en tömningsbegäran när en replikering tas emot från författaren.

Så här aktiverar du den:

1. [Följ stegen här](page-invalidate.md#invalidating-dispatcher-cache-from-a-publishing-instance) för att skapa tömningsagenter vid publicering
1. Gå till varje agentes konfiguration och på **Utlösare** -fliken, kontrollera **Vid mottagning** box.

## Övrigt

Hur avgör Dispatcher om ett dokument är aktuellt?
För att avgöra om ett dokument är uppdaterat utför Dispatcher följande åtgärder:

Den kontrollerar om dokumentet kan ogiltigförklaras automatiskt. Annars betraktas dokumentet som uppdaterat.
Om dokumentet har konfigurerats för automatisk ogiltigförklaring kontrollerar Dispatcher om det är äldre eller nyare än den senaste tillgängliga ändringen. Om den är äldre begär Dispatcher den aktuella versionen från AEM och ersätter versionen i cachen.

### Hur returnerar Dispatcher dokument?

Du kan definiera om Dispatcher ska cachelagra ett dokument med [Dispatcher-konfiguration](dispatcher-configuration.md) fil, `dispatcher.any`. Dispatcher kontrollerar begäran mot listan med cachelagrade dokument. Om dokumentet inte finns med i den här listan begär Dispatcher dokumentet från AEM.

The `/rules` anger vilka dokument som cachelagras enligt dokumentets sökväg. Oavsett `/rules` egenskapen, Dispatcher cachelagrar aldrig ett dokument under följande omständigheter:

* URI för begäran innehåller en `(?)` frågetecken.
* Det indikerar en dynamisk sida, till exempel ett sökresultat som inte behöver cachas.
* Filtillägget saknas.
* Webbservern behöver tillägget för att kunna avgöra dokumenttypen (MIME-typen).
* Autentiseringshuvudet är inställt (konfigurerbart).
* Om AEM svarar med följande rubriker:
   * no-cache
   * no store
   * must-revalidate

Dispatcher lagrar cachelagrade filer på webbservern som om de var en del av en statisk webbplats. Om en användare begär ett cachelagrat dokument kontrollerar Dispatcher om dokumentet finns i webbserverns filsystem. I så fall returnerar Dispatcher dokumenten. Annars begär Dispatcher dokumentet från AEM.

>[!NOTE]
>
>Metoderna GET eller HEAD (för HTTP-huvudet) kan nås av Dispatcher. Mer information om cachelagring av svarshuvuden finns i [Cachelagra HTTP-svarshuvuden](dispatcher-configuration.md#caching-http-response-headers) -avsnitt.

### Kan jag implementera flera utskickare i en konfiguration?

Ja. I så fall måste du se till att båda utskickarna har direktåtkomst till den AEM webbplatsen. En Dispatcher kan inte hantera begäranden från en annan Dispatcher.
