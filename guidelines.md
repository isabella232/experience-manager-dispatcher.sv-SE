---
source-git-commit: 053f90d9a0574c7d470cec6794498919e3340713
translation-type: tm+mt

---
# Riktlinjer för att bidra till Adobe Experience Manager-dokumentationen

## Dokumentationsfilosofi

Vi vet att Adobe Experience Manager-användare arbetar i mycket konkurrensutsatta miljöer och strävar efter att skapa digitala upplevelser som skiljer dem från deras konkurrenter. Därför är det viktigt att när Adobe levererar avancerade nya verktyg i AEM, dessa verktyg kompletteras med korrekt och tydlig dokumentation som gör det möjligt för kunden att omedelbart utnyttja sin AEM-investering och maximera avkastningen.

Målet med AEM-dokumentationen är att skicka dokumentation till AEM-användarna så snart som möjligt. Därför prioriterar vi korrekt, användbar dokumentation och strävar efter att kontinuerligt uppdatera och förbättra den.

## Dokumentationsbidrag

För att kontinuerligt förbättra AEM-dokumentationen är det välkommet att alla AEM-användare bidrar till dokumentationen. Vare sig det gäller förfrågningar eller frågor kan förbättringar av dokumentationen vara korrigeringar, förtydliganden, tillägg och ytterligare exempel.

## Dokumentationsstandarder

Även om vi välkomnar bidrag till vår dokumentation bör alla bidrag till AEM-dokumentationen, antingen i form av en begäran om att få lämna in en ansökan eller ett problem, överensstämma med våra standarder för bidrag och dokumentation.

Bidrag som inte uppfyller dessa standarder kan avvisas.

### Vi dokumenterar standardanvändningsexempel.

AEM-dokumentationen täcker standardanvändningsfall. Användningsfall som inte omfattas av standardinstallation och -användning ingår inte i AEM-dokumentationen.

### Vi dokumenterar vanligtvis inte buggar eller deras tillfälliga lösningar.

AEM-dokumentationen täcker standardanvändningsfall. Av den anledningen är buggar, effekter orsakade av buggar och tillfälliga lösningar för buggar i allmänhet inte dokumenterade.

Undantag från den här regeln gäller versionsinformationen där kända problem kan listas med möjliga lösningar som har godkänts av AEM Product Management.

### Dokumentationsbidragen är inte till för att besvara tekniska frågor.

Alla idéer du kan behöva för att förbättra AEM-dokumentationen är välkomna som bidrag. Kommentarer, frågor och förfrågningar är dock endast avsedda för *bidrag* . De är inte avsedda att användas för att besvara dina frågor om hur du använder AEM, implementerar ditt AEM-projekt eller löser tekniska problem.

Frågor om hur du använder AEM eller tekniska fel som du kan ha gjort ska rapporteras via den normala supportprocessen via [Experience Cloud Enterprise Support-portalen](https://helpx.adobe.com/contact/enterprise-support.ec.html) eller diskuteras i [Experience Manager-communityn](https://forums.adobe.com/community/experience-cloud/marketing-cloud/experience-manager).

***AEM-dokumentationsbidragen ersätter inte Adobes kundtjänst*** och eventuella bidrag som söker svar på supportrelaterade frågor kommer att refuseras.

### Bidragen ska tydligt hänvisa till berörda dokumentationssidor.

Om du skapar ett problem som kan föreslå förbättringar av dokumentationen måste du inkludera länkar till de sidor som påverkas. Om du skapar ett ärende genom att använda länken **Redigera den här sidan** på en dokumentationssida skapas ärendet automatiskt med en länk till sidan.

Detta gäller inte för pull-begäranden eftersom pull-begäranden till sin natur refererar till den eller de berörda sidorna.

## Riktlinjer för dokumentation

Vi ber att eventuella bidrag till vår dokumentation följer vissa riktlinjer för format.

Genom att följa dessa riktlinjer blir det enklare att granska ditt bidrag och det går därför snabbare att integrera det i vår dokumentation.

### Språk och format

#### Språk:

* AEM-dokumentationen skrivs och underhålls på amerikansk engelska.
* Håll meningar så enkla som möjligt.
* Se till att språket är klart och koncist.

Kom ihåg att läsare av AEM-dokumentation finns i hela världen och inte kan förväntas vara inbyggda eller flytande engelska högtalare. Undvik kollokvialism och håll språket så tydligt och enkelt som möjligt.

#### Följ Microsoft-formathandboken

[Microsoft Manual of Style](https://docs.microsoft.com/en-us/style-guide/welcome/) är en kostnadsfri handbok för dokumentationsformat som fokuserar på programvarudokumentation och AEM-dokumentation följer den här handboken när det är möjligt.

### Formatering

| Objekt | Format |
|---|---|
| Element eller alternativ i användargränssnittet | **fet** |
| Filnamn, sökväg, användarindata, parametervärden | `monospaced` |
| Kod, kommandorad | ```Code Block``` |

### Skärmbilder

Skärmbilder ska användas med omdöme och endast när en textbeskrivning är otillräcklig.

Markörer eller andra anteckningar i skärmbilder (som röda ramar, pilar eller text) bör inte användas. På så sätt är skärmbilderna enklare att återanvända eller att replikera i lokaliserade versioner av dokumentationen.

### Versionsspecifika referenser

Undvik om möjligt direkta referenser till en viss version i dokumentationsinnehållet. Detta gör dokumentationen mer flexibel och utbyggbar för framtida versioner.

### Användning av Day, AEM, CQ, CRX

Produkten ska alltid kallas för sitt fullständiga namn **Adobe Experience Manager** för första gången i en artikel och kan därefter kallas för **AEM**.

Day, Day Software, CQ och CRX bör inte användas utom när det är oundvikligt, t.ex. i klassnamn eller med hänvisning till AEM-historiken.