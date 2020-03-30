---
title: zahrnout soubor
description: zahrnout soubor
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 73d4d4e1a5f148dce6099b2d747ee1c290bcf7c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "67175378"
---
## <a name="run-the-web-application"></a>Spuštění webové aplikace

1. Abychom vám to usnadnili, je v GitHubu hostovaná ukázková jednostránková webová aplikace. Otevřete prohlížeč [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/)na .

    > [!NOTE]
    > Zdroj souboru HTML je umístěn na [adrese /docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html).

1. Když se zobrazí výzva k zadání základní adresy URL aplikace funkcí, zadejte `http://localhost:7071`.

1. Při zobrazení výzvy zadejte uživatelské jméno.

1. Webová aplikace volá funkci *GetSignalRInfo* v aplikaci funkcí, aby načetla informace o připojení ke službě Azure SignalR Service. Po dokončení připojení se zobrazí vstupní pole zprávy chatu.

1. Zadejte zprávu a stiskněte klávesu Enter. Aplikace odešle zprávu funkci *SendMessage* v aplikaci funkcí Azure, která pak pomocí výstupní vazby služby SignalR odešle zprávu všem připojeným klientům. Pokud všechno funguje správně, měla by se tato zpráva v aplikaci zobrazit.

    ![Spuštění aplikace](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Otevřete další instanci webové aplikace v jiném okně prohlížeče. Uvidíte, že všechny odeslané zprávy se zobrazují ve všech instancích aplikace.

> [!IMPORTANT]
> Vzhledem k tomu, že stránka HTML se zobrazuje pomocí protokolu HTTPS, ale místní modul runtime Azure Functions ve výchozím nastavení používá protokol HTTP, může váš prohlížeč (například Firefox) vynutit zásady smíšeného obsahu, které blokují požadavky z webové stránky na vaše funkce. Chcete-li tento problém vyřešit, použijte prohlížeč, který nemá toto omezení, nebo spusťte místní http server, například [http-server](https://www.npmjs.com/package/http-server) v adresáři */docs/demo/chat-v2.* Ujistěte se, že `CORS` počátek je přidán do nastavení v *local.settings.json*.