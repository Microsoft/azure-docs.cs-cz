---
title: Obměna přístupový klíč pro služby Azure SignalR
description: Obsahuje přehled proč zákazník potřebuje pravidelně obměna přístupových klíčů a jak to provést s grafickým uživatelským rozhraním webu Azure portal a rozhraní příkazového řádku Azure.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 15b0ff0bbb96e5fa96d81cfa265e83abf749cf85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60688887"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Obměna přístupový klíč pro služby Azure SignalR

Každá instance služby Azure SignalR nemá dvojici přístupové klíče volá primárního a sekundárního klíče. Slouží k ověřování klientů SignalR při žádostí do služby. Klíče jsou spojeny s adresu url koncového bodu instance. Zabezpečení vašich klíčů a je pravidelně otočení. Jste, pokud se dva přístupové klíče, takže můžete udržovat připojení pomocí jeden klíč druhého.

## <a name="why-rotate-access-keys"></a>Proč otočit přístupové klíče?

Z důvodů zabezpečení a požadavky na dodržování předpisů pravidelně otočit přístupové klíče.

## <a name="regenerate-access-keys"></a>Opětovné vygenerování přístupových klíčů

1. Přejděte [webu Azure portal](https://portal.azure.com/)a přihlaste se pomocí svých přihlašovacích údajů.

1. Najít **klíče** části v instanci služby Azure SignalR s klíči, které chcete znovu vygenerovat.

1. Vyberte **klíče** v navigační nabídce.

1. Vyberte **znovu vygenerovat primární klíč** nebo **znovu vygenerovat sekundární klíč**.

   Nový klíč a odpovídající připojovací řetězec se vytvoří a zobrazí.

   ![Znovu vygenerovat klíče](media/signalr-howto-key-rotation/regenerate-keys.png)

Můžete také může znovu vygenerovat klíče s použitím [rozhraní příkazového řádku Azure](/cli/azure/ext/signalr/signalr/key?view=azure-cli-latest#ext-signalr-az-signalr-key-renew).

## <a name="update-configurations-with-new-connection-strings"></a>Aktualizace konfigurace se nové připojovací řetězce

1. Zkopírování nově vygenerovaný připojovacího řetězce.

1. Aktualizujte všechny konfigurace používat nový připojovací řetězec.

1. Restartování aplikace podle potřeby.

## <a name="forced-access-key-regeneration"></a>Opětovné generování vynucené přístupového klíče

Službě Azure SignalR může vynutit povinná opětovné generování přístupového klíče v některých situacích. Služba upozorní zákazníky prostřednictvím e-mailu a portálu oznámení. Pokud přijímáte tuto komunikaci nebo dojde k selhání služby z důvodu přístupový klíč, otočení klíče podle pokynů v této příručce.

## <a name="next-steps"></a>Další postup

Otočit přístupové klíče pravidelně z hlediska zabezpečení.

V této příručce jste zjistili, jak znovu vygenerovat přístupové klíče. Pokračujte na další kurzy týkající se ověřování pomocí OAuth, nebo s využitím Azure Functions.

> [!div class="nextstepaction"]
> [Integrace s ASP.NET core identity](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Sestavení aplikace bez serveru v reálném čase s ověřováním](./signalr-tutorial-authenticate-azure-functions.md)