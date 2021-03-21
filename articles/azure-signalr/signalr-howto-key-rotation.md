---
title: Obměna přístupového klíče pro službu Azure SignalR Service
description: Přehled o tom, proč zákazník potřebuje k rutinnímu otočení přístupových klíčů a jak to udělat pomocí grafického uživatelského rozhraní Azure Portal a rozhraní příkazového řádku Azure CLI
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 1f80742d48fc46c8c5e337794100ca5901818c88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184385"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Obměna přístupového klíče pro službu Azure SignalR Service

Každá instance služby signalizace Azure má dvojici přístupových klíčů nazývaných primární a sekundární klíče. Slouží k ověřování klientů služby Signal, když se do služby zadávají požadavky. Klíče jsou přidružené k adrese URL koncového bodu instance. Udržujte klíče v bezpečí a pravidelně je otáčejte. Máte k dispozici dva přístupové klíče, takže můžete spravovat připojení pomocí jednoho klíče a zároveň ho znovu vygenerovat.

## <a name="why-rotate-access-keys"></a>Proč střídat přístupové klíče?

Z bezpečnostních důvodů a požadavků na dodržování předpisů rutinním otočením přístupových klíčů.

## <a name="regenerate-access-keys"></a>Znovu vygenerovat přístupové klíče

1. Přejít na [Azure Portal](https://portal.azure.com/)a přihlaste se pomocí svých přihlašovacích údajů.

1. V instanci služby signalizace Azure Najděte oddíl **Keys** s klíči, které chcete znovu vygenerovat.

1. V navigační nabídce vyberte **klíče** .

1. Vyberte **znovu vygenerovat primární klíč** nebo znovu **vygenerovat sekundární klíč**.

   Vytvoří a zobrazí se nový klíč a odpovídající připojovací řetězec.

   ![Znovu vygenerovat klíče](media/signalr-howto-key-rotation/regenerate-keys.png)

Můžete také znovu vygenerovat klíče pomocí [Azure CLI](/cli/azure/signalr/key#az-signalr-key-renew).

## <a name="update-configurations-with-new-connection-strings"></a>Aktualizace konfigurací novými řetězci připojení

1. Zkopírujte nově vygenerovaný připojovací řetězec.

1. Aktualizujte všechny konfigurace tak, aby používaly nový připojovací řetězec.

1. V případě potřeby restartujte aplikaci.

## <a name="forced-access-key-regeneration"></a>Vygenerování vynuceného přístupového klíče

Služba Azure Signaler může vynutila vygenerování povinného přístupového klíče za určitých okolností. Služba upozorňuje zákazníky prostřednictvím e-mailu a oznámení na portálu. Pokud obdržíte tuto komunikaci nebo dojde k chybě služby z důvodu přístupové klávesy, pomocí pokynů v této příručce tyto klíče přeotáčejte.

## <a name="next-steps"></a>Další kroky

Pravidelně otáčejte přístupové klíče jako dobrý bezpečnostní postup.

V této příručce jste zjistili, jak znovu vygenerovat přístupové klíče. Přejděte k dalším kurzům o ověřování pomocí protokolu OAuth nebo s Azure Functions.

> [!div class="nextstepaction"]
> [Integrace s identitou ASP.NET Core](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Vytvoření aplikace v reálném čase bez serveru s ověřováním](./signalr-tutorial-authenticate-azure-functions.md)