---
title: Konfigurera Dispatcher för att förhindra CSRF-attacker
seo-title: Configuring Adobe AEM Dispatcher to Prevent CSRF Attacks
description: Lär dig hur du konfigurerar AEM Dispatcher för att förhindra attacker som leder till cross-site request-attacker.
seo-description: Learn how to configure Adobe AEM Dispatcher to prevent Cross-Site Request Forgery attacks.
uuid: f290bdeb-54e2-4649-b0fc-6257b422af2d
topic-tags: dispatcher
content-type: reference
discoiquuid: d61d021e-b338-4a1d-91ee-55427557e931
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
source-git-commit: 3a0e237278079a3885e527d7f86989f8ac91e09d
workflow-type: tm+mt
source-wordcount: '225'
ht-degree: 0%

---

# Konfigurera Dispatcher för att förhindra CSRF-attacker{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM tillhandahåller ett ramverk som syftar till att förhindra attacker av typen cross-site Request. För att du ska kunna använda detta ramverk på rätt sätt måste du göra följande ändringar i din dispatcherkonfiguration:

>[!NOTE]
>
>Var noga med att uppdatera regelnumren i exemplen nedan baserat på din befintliga konfiguration. Kom ihåg att avsändare kommer att använda den senaste matchande regeln för att bevilja eller neka tillstånd, så placera reglerna längst ned i den befintliga listan.

1. I `/clientheaders` i din författargrupp.any och publish-farm.any, lägg till följande post längst ned i listan:\
   `CSRF-Token`
1. I avsnittet /filters i `author-farm.any` och `publish-farm.any` eller `publish-filters.any` fil, lägg till följande rad för att tillåta begäranden för `/libs/granite/csrf/token.json` genom dispatchern.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. Under `/cache /rules` i `publish-farm.any`lägger du till en regel som blockerar dispatchern från att cachelagra `token.json` -fil. Vanligtvis åsidosätter författare cachelagring, så du behöver inte lägga till regeln i `author-farm.any`.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Kontrollera att konfigurationen fungerar genom att titta på dispatcher.log i DEBUG-läge för att kontrollera att filen token.json inte cachelagras och inte blockeras av filter. Du bör se meddelanden som liknar:\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Du kan också validera att begäranden lyckas i din apache `access_log`. Begäranden för&quot;/libs/granite/csrf/token.json ska returnera en HTTP 200-statuskod.
