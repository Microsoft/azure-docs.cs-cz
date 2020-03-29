---
title: Obměna přístupového klíče pro službu Azure SignalR Service
description: Přehled o tom, proč zákazník potřebuje pravidelně otáčet přístupové klíče a jak to udělat pomocí grafického uživatelského rozhraní portálu Azure a rozhraní příkazového příkazového příkazu Konfrontací kláves Azure.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 133edc64ac2f858a397a4a184c24497dae8af333
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67565731"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Obměna přístupového klíče pro službu Azure SignalR Service

Každá instance služby Azure SignalR má dvojici přístupových klíčů s názvem Primární a sekundární klíče. Používají se k ověření klientů SignalR při požadavku na službu. Klíče jsou přidruženy k adrese url koncového bodu instance. Udržujte své klíče v bezpečí a pravidelně je otáčejte. Máte k dispozici dva přístupové klávesy, takže můžete udržovat připojení pomocí jednoho klíče při regeneraci druhého.

## <a name="why-rotate-access-keys"></a>Proč otáčet přístupové klávesy?

Z bezpečnostních důvodů a požadavků na dodržování předpisů pravidelně otáčejte přístupovými klíči.

## <a name="regenerate-access-keys"></a>Obnovení přístupových klíčů

1. Přejděte na [portál Azure](https://portal.azure.com/)a přihlaste se pomocí svých přihlašovacích údajů.

1. Najděte oddíl **Klíče** v instanci služby Azure SignalR s klíči, které chcete znovu vygenerovat.

1. V navigační nabídce vyberte **Klávesy.**

1. Vyberte **obnovit primární klíč** nebo obnovit sekundární **klíč**.

   Vytvoří se a zobrazí nový klíč a odpovídající připojovací řetězec.

   ![Obnovit klíče](media/signalr-howto-key-rotation/regenerate-keys.png)

Klíče můžete také znovu vygenerovat pomocí [příkazového příkazového příkazu k webu Azure](/cli/azure/signalr/key?view=azure-cli-latest#az-signalr-key-renew).

## <a name="update-configurations-with-new-connection-strings"></a>Aktualizace konfigurací pomocí nových připojovacích řetězců

1. Zkopírujte nově generovaný připojovací řetězec.

1. Aktualizujte všechny konfigurace, aby bylo použití nového připojovacího řetězce.

1. Podle potřeby restartujte aplikaci.

## <a name="forced-access-key-regeneration"></a>Obnovení vynucených přístupových klíčů

Služba Azure SignalR může vynutit obnovení povinného přístupového klíče v určitých situacích. Služba upozorní zákazníky prostřednictvím e-mailu a portálu oznámení. Pokud obdržíte tuto komunikaci nebo dojde k selhání služby z důvodu přístupového klíče, otočte klíče podle pokynů v této příručce.

## <a name="next-steps"></a>Další kroky

Pravidelně otáčejte přístupovými klávesami jako osvědčený postup zabezpečení.

V této příručce jste se naučili, jak znovu vygenerovat přístupové klíče. Pokračujte k dalším kurzům o ověřování pomocí OAuth nebo pomocí funkcí Azure.

> [!div class="nextstepaction"]
> [Integrace s ASP.NET základní identitou](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Vytvoření bezserverové aplikace v reálném čase s ověřováním](./signalr-tutorial-authenticate-azure-functions.md)