---
title: Obměna klíčů přístup pro služby Azure SignalR
description: Obsahuje přehled proč zákazník potřebuje pravidelně obměna přístupových klíčů a jak to provést v portálu grafického uživatelského rozhraní a rozhraní příkazového řádku.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 2c0f60b0ef3a90372fc4a095c830f39bc148f354
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53636153"
---
# <a name="access-key-rotation-for-azure-signalr-service"></a>Obměna klíčů přístup pro služby Azure SignalR

Každá instance služby Azure SignalR nemá dvojici přístupové klíče: Primární a sekundární klíče. Používají se k ověřování klientů SignalR při zasílání požadavků na službu. Klíče jsou spojeny s adresu url koncového bodu instance. Zabezpečení vašich klíčů a je pravidelně otočení. Dva přístupové klíče, jsou k dispozici, můžete udržovat připojení pomocí jednoho klíče druhého.

## <a name="why-rotate-access-keys"></a>Proč otočit přístupové klíče?

Při požadavku na důvod a dodržování předpisů zabezpečení vývojáři doporučují pravidelně obměna přístupových klíčů.

## <a name="how-to-regenerate-access-keys"></a>Jak znovu vygenerovat přístupové klíče?

1. Přejděte na web [Azure Portal](https://portal.azure.com/) a přihlaste se pomocí svých přihlašovacích údajů.

1. Najít **klíče** část z instance služby Azure SignalR, který chcete znovu vygenerovat klíče.

1. Klikněte na tlačítko **klíče** v navigační nabídce.

1. Klikněte na tlačítko **znovu vygenerovat primární klíč** nebo **znovu vygenerovat sekundární klíč**.

Bude vytvořena a zobrazí nový klíč a odpovídající připojovací řetězec.

 ![Obnovit klíče](media/signalr-key-rotation/regenerate-keys.png)

Můžete také znovu vygenerovat klíče pomocí [rozhraní příkazového řádku Azure](/cli/azure/ext/signalr/signalr/key?view=azure-cli-latest#ext-signalr-az-signalr-key-renew).

## <a name="update-configurations-with-new-connection-strings"></a>Aktualizace konfigurace se nové připojovací řetězce

1. Zkopírování nově vygenerovaný připojovacího řetězce.

1. Aktualizujte všechny konfigurace používat nový připojovací řetězec.

1. Restartování aplikace podle potřeby.

## <a name="forced-access-key-regeneration"></a>Opětovné generování vynucené přístupového klíče

Službě Azure SignalR může vynutit povinná opětovné generování přístupového klíče v rámci určité situace. Služba upozorní zákazníky prostřednictvím e-mailu a portálu oznámení. Pokud přijímáte tuto komunikaci nebo dojde k selhání služby z důvodu přístupový klíč, otočení klíčů podle této příručky.

## <a name="next-steps"></a>Další postup

Doporučujeme pravidelně otáčení přístupové klíče z hlediska zabezpečení.

V této příručce jste se dozvěděli o tom, jak znovu vygenerovat přístupové klíče. Pokračujte na další kurzy týkající se ověřování pomocí OAuth, nebo s využitím Azure Functions.

> [!div class="nextstepaction"]
> [Integrace s ASP.NET Core Identity](./signalr-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Vytvoření aplikace bez serveru v reálném čase s ověřováním](./signalr-authenticate-azure-functions.md)