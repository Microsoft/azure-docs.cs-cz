---
title: zahrnout soubor
description: zahrnout soubor
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 2af9c1dfd13d3aeafa7cf1ac76537117ecc15aff
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "84317642"
---
## <a name="run-the-web-application"></a>Spuštění webové aplikace

1. Chcete-li zjednodušit testování klientů, otevřete prohlížeč v naší ukázkové webové aplikaci s jednou stránkou [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/) . 

    > [!NOTE]
    > Zdroj souboru HTML je umístěný na adrese [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html). A pokud chcete kód HTML hostovat sami, spusťte prosím v adresáři */docs/demo/chat-v2* místní server http, například [http-Server](https://www.npmjs.com/package/http-server) . Zajistěte, aby byl počátek přidán do `CORS` nastavení v *local.settings.js* podobný ukázce.
    > 
    > ```javascript
    > "Host": {
    >  "LocalHttpPort": 7071,
    >  "CORS": "http://localhost:8080,https://azure-samples.github.io",
    >  "CORSCredentials": true
    > }
    >
    > ```

1. Když se zobrazí výzva k zadání základní adresy URL aplikace funkcí, zadejte `http://localhost:7071`.

1. Při zobrazení výzvy zadejte uživatelské jméno.

1. Webová aplikace volá funkci *GetSignalRInfo* v aplikaci funkcí, aby načetla informace o připojení ke službě Azure SignalR Service. Po dokončení připojení se zobrazí vstupní pole zprávy chatu.

1. Zadejte zprávu a stiskněte klávesu Enter. Aplikace odešle zprávu funkci *SendMessage* v aplikaci funkcí Azure, která pak pomocí výstupní vazby služby SignalR odešle zprávu všem připojeným klientům. Pokud všechno funguje správně, měla by se tato zpráva v aplikaci zobrazit.

    ![Spuštění aplikace](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Otevřete další instanci webové aplikace v jiném okně prohlížeče. Uvidíte, že všechny odeslané zprávy se zobrazují ve všech instancích aplikace.
