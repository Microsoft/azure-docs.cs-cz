---
title: Shromažďují se požadované parametry.
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Parametry pro všechny kontejnery Cognitive Services
ms.service: cognitive-services
ms.topic: include
ms.date: 7/24/2019
ms.author: dapine
ms.openlocfilehash: 636a41fde345a08db1549e53626522962f9cf74f
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488786"
---
## <a name="gathering-required-parameters"></a>Shromažďují se požadované parametry.

Existují tři primární parametry pro všechny požadované kontejnery Cognitive Services. Podmínky licenční smlouvy s koncovým uživatelem (**EULA**) musí být k dispozici s `accept`hodnotou. Kromě toho je potřeba adresa URL koncového bodu i klíč rozhraní API.

### <a name="endpoint-uri-endpointuri"></a>Identifikátor URI koncového bodu`{Endpoint_URI}`

Hodnota identifikátoru URI **koncového bodu** je k dispozici na stránce *Přehled* Azure Portal odpovídajícího prostředku služby pro rozpoznávání. Přejděte na stránku *Přehled* , najeďte myší na koncový bod a `Copy to clipboard` zobrazí se <span class="docon docon-edit-copy x-hidden-focus"></span> ikona. Zkopírujte a použijte tam, kde je to potřeba.

![Shromáždit identifikátor URI koncového bodu pro pozdější použití](../media/overview-endpoint-uri.png)

### <a name="keys-apikey"></a>Klíče`{API_Key}`

Tento klíč se používá ke spuštění kontejneru a je k dispozici na stránce klíčů Azure Portal odpovídajícího prostředku služby pro rozpoznávání. Přejděte na stránku *klíče* a klikněte `Copy to clipboard` na <span class="docon docon-edit-copy x-hidden-focus"></span> ikonu.

![Získat jeden ze dvou klíčů pro pozdější použití](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Tyto klíče předplatného se používají pro přístup ke službě rozpoznávání rozhraní API. Nesdílejte své klíče. Bezpečně je uložte, například pomocí Azure Key Vault. Tyto klíče doporučujeme také pravidelně obnovovat. K volání rozhraní API je potřeba jenom jeden klíč. Při opětovném generování prvního klíče můžete použít druhý klíč pro pokračování přístupu ke službě.