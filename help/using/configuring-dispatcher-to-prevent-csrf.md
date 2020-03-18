---
title: Konfigurera Dispatcher för att förhindra CSRF-attacker
seo-title: Konfigurera Adobe AEM Dispatcher för att förhindra CSRF-attacker
description: Lär dig hur du konfigurerar AEM Dispatcher för att förhindra attacker som leder till cross-site request-attacker.
seo-description: Lär dig hur du konfigurerar Adobe AEM Dispatcher för att förhindra attacker som leder till cross-site request-attacker.
uuid: f290bdeb-54e2-4649-b0fc-6257b422af2d
topic-tags: dispatcher
content-type: reference
discoiquuid: d61d021e-b338-4a1d-91ee-55427557e931
translation-type: tm+mt
source-git-commit: 69edbe7608b46c93d238515e4223606eadad0ac4

---


# Konfigurera Dispatcher för att förhindra CSRF-attacker{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM tillhandahåller ett ramverk som syftar till att förhindra attacker med förfalskade begäranden på olika webbplatser. För att du ska kunna använda detta ramverk på rätt sätt måste du göra följande ändringar i din dispatcherkonfiguration:

>[!NOTE]
>
>Var noga med att uppdatera regelnumren i exemplen nedan baserat på din befintliga konfiguration. Kom ihåg att avsändare kommer att använda den senaste matchande regeln för att bevilja eller neka tillstånd, så placera reglerna längst ned i den befintliga listan.

1. I delen `/clientheaders` av din författargrupp.any och publish-farm.any lägger du till följande post längst ned i listan:\
   `CSRF-Token`
1. Lägg till följande rad i avsnittet /filters i din `author-farm.any` och `publish-farm.any` eller `publish-filters.any` din fil för att tillåta begäranden `/libs/granite/csrf/token.json` via dispatchern.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. Lägg till en regel under `/cache /rules` avsnittet i `publish-farm.any`filen som hindrar dispatchern från att cachelagra `token.json` filen. Vanligtvis åsidosätter författare cachelagring, så du behöver inte lägga till regeln i dina `author-farm.any`.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Kontrollera att konfigurationen fungerar genom att titta på dispatcher.log i DEBUG-läge för att kontrollera att filen token.json inte cachelagras och inte blockeras av filter. Du bör se meddelanden som liknar:\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Du kan också validera att begäranden lyckas i din apache `access_log`. Begäranden för&quot;/libs/granite/csrf/token.json ska returnera en HTTP 200-statuskod.
