---
title: Rychlý Start – vytvoření a Správa přístupových tokenů
titleSuffix: An Azure Communication Services quickstart
description: Naučte se spravovat identity a přístupové tokeny pomocí klientské knihovny pro správu služby Azure Communication Services.
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: a76000ecacdf78196ec1b80a60940484f6421641
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946961"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Rychlý Start: vytváření a Správa přístupových tokenů

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Začněte s komunikačními službami Azure pomocí klientské knihovny pro správu komunikačních služeb ke zřízení a správě přístupových tokenů. Přístupové tokeny umožňují konverzaci a volání klientských knihoven přímo s komunikačními službami Azure. Tyto tokeny se generují ve službě zřizování tokenů na straně serveru, kterou implementujete. Pak se používají k inicializaci klientských knihoven komunikačních služeb na klientských zařízeních.

Všimněte si, že všechny ceny, které se v tomto kurzu zobrazují v rámci tohoto kurzu, jsou třeba jenom pro účely.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

Výstup aplikace popisuje každou akci, která je dokončena:
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Issued a access token with 'voip' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Issued a access token with 'chat' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Successfully deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050

Deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](./create-communication-resource.md#clean-up-resources).


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

> [!div class="checklist"]
> * Správa identit
> * Vystavení přístupových tokenů
> * Použití klientské knihovny pro správu komunikačních služeb


> [!div class="nextstepaction"]
> [Přidání hlasového volání do aplikace](./voice-video-calling/getting-started-with-calling.md)

Můžete také chtít:

 - [Další informace o ověřování](../concepts/authentication.md)
 - [Přidání chatu do aplikace](./chat/get-started.md)
 - [Informace o architektuře klientů a serverů](../concepts/client-and-server-architecture.md)
