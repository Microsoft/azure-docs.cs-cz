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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58051972"
---
## <a name="run-the-web-application"></a>Spuštění webové aplikace

1. Abychom vám to usnadnili, je v GitHubu hostovaná ukázková jednostránková webová aplikace. Otevřete prohlížeč na adrese [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/).

    > [!NOTE]
    > Zdrojový soubor HTML se nachází v [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html).

1. Po zobrazení výzvy pro funkce aplikace základní adresu URL zadejte `http://localhost:7071`.

1. Při zobrazení výzvy zadejte uživatelské jméno.

1. Webová aplikace volá funkci *GetSignalRInfo* v aplikaci funkcí, aby načetla informace o připojení ke službě Azure SignalR Service. Po dokončení připojení se zobrazí vstupní pole zprávy chatu.

1. Zadejte zprávu a stiskněte klávesu Enter. Aplikace odešle zprávu funkci *SendMessage* v aplikaci funkcí Azure, která pak pomocí výstupní vazby služby SignalR odešle zprávu všem připojeným klientům. Pokud všechno funguje správně, měla by se tato zpráva v aplikaci zobrazit.

    ![Spuštění aplikace](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Otevřete další instanci webové aplikace v jiném okně prohlížeče. Uvidíte, že všechny odeslané zprávy se zobrazují ve všech instancích aplikace.

> [!IMPORTANT]
> Protože obsluhuje stránku HTML pomocí protokolu HTTPS, ale místní modul runtime Azure Functions je ve výchozím nastavení pomocí protokolu HTTP, prohlížeči (jako je například Firefox) může vynutit zásady smíšený obsah které blokují požadavky z webové stránky na vaše funkce. Tento problém vyřešit, použijte prohlížeč, který neobsahuje toto omezení nebo spustit místní server HTTP, jako [http server](https://www.npmjs.com/package/http-server) v */docs/demo/chat-v2* adresáře. Ujistěte se původ je přidán do `CORS` nastavení *local.settings.json*.