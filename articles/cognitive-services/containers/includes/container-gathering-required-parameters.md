---
title: Shromažďování požadovaných parametrů
services: cognitive-services
author: aahill
manager: nitinme
description: Parametry pro všechny kontejnery cognitive services
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875029"
---
## <a name="gathering-required-parameters"></a>Shromažďování požadovaných parametrů

Existují tři primární parametry pro všechny kontejnery cognitive services, které jsou požadovány. Licenční smlouva s koncovým uživatelem (EULA) `accept`musí být přítomna s hodnotou . Kromě toho jsou potřeba adresu URL koncového bodu a klíč rozhraní API.

### <a name="endpoint-uri-endpoint_uri"></a>Identifikátor URI koncového bodu`{ENDPOINT_URI}`

Hodnota IDENTIFIKÁTORU URI **koncového bodu** je k dispozici na stránce *Přehled* portálu Azure odpovídajícího prostředku služby Cognitive Service. Přejděte na stránku *Přehled,* najeďte `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> na koncový bod a zobrazí se ikona. V případě potřeby kopírujte a používejte.

![Shromáždit identifikátor uri koncového bodu pro pozdější použití](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Klíče`{API_KEY}`

Tento klíč se používá ke spuštění kontejneru a je k dispozici na stránce klíče portálu Azure odpovídající prostředek kognitivní služby. Přejděte na stránku *Klíče* `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> a klikněte na ikonu.

![Získejte jeden ze dvou klíčů pro pozdější použití](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Tyto klíče předplatného se používají pro přístup k rozhraní API služby Cognitive Service. Nesdílejte své klíče. Uklápěte je bezpečně, například pomocí azure key vaultu. Doporučujeme také pravidelně regenerovat tyto klíče. K volání rozhraní API je nutný pouze jeden klíč. Při obnově prvního klíče můžete použít druhý klíč pro trvalý přístup ke službě.