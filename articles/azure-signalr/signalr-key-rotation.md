---
title: Obměna klíčů přístup pro služby Azure SignalR
description: Obsahuje přehled proč zákazník potřebuje pravidelně obměna přístupových klíčů a jak to provést s grafickým uživatelským rozhraním webu Azure portal a rozhraní příkazového řádku Azure.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 04321e62ea41b58e9ee4314b600c77e6c39b7ade
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065830"
---
# <a name="access-key-rotation-for-azure-signalr-service"></a>Obměna klíčů přístup pro služby Azure SignalR

Každá instance služby Azure SignalR nemá dvojici přístupové klíče volá primárního a sekundárního klíče. Slouží k ověřování klientů SignalR při žádostí do služby. Klíče jsou spojeny s adresu url koncového bodu instance. Zabezpečení vašich klíčů a je pravidelně otočení. Jste, pokud se dva přístupové klíče, takže můžete udržovat připojení pomocí jeden klíč druhého.

## <a name="why-rotate-access-keys"></a>Proč otočit přístupové klíče?

Z důvodů zabezpečení a požadavky na dodržování předpisů pravidelně otočit přístupové klíče.

## <a name="regenerate-access-keys"></a>Opětovné vygenerování přístupových klíčů

1. Přejděte [webu Azure portal](https://portal.azure.com/)a přihlaste se pomocí svých přihlašovacích údajů.

1. Najít **klíče** části v instanci služby Azure SignalR s klíči, které chcete znovu vygenerovat.

1. Vyberte **klíče** v navigační nabídce.

1. Vyberte **znovu vygenerovat primární klíč** nebo **znovu vygenerovat sekundární klíč**.

   Nový klíč a odpovídající připojovací řetězec se vytvoří a zobrazí.

   ![Obnovit klíče](media/signalr-key-rotation/regenerate-keys.png)

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
> [Integrace s ASP.NET core identity](./signalr-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Sestavení aplikace bez serveru v reálném čase s ověřováním](./signalr-authenticate-azure-functions.md)