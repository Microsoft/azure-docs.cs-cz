---
title: Shromažďují se požadované parametry.
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Parametry pro všechny kontejnery Cognitive Services
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2019
ms.author: dapine
ms.openlocfilehash: bf4e8f06642a4a764794a75e2b49f15958796c61
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "73465938"
---
## <a name="gathering-required-parameters"></a>Shromažďují se požadované parametry.

Existují tři primární parametry pro všechny požadované kontejnery Cognitive Services. Licenční smlouvu s koncovým uživatelem (EULA) se musí nacházet s hodnotou `accept`. Kromě toho je potřeba adresa URL koncového bodu i klíč rozhraní API.

### <a name="endpoint-uri-endpoint_uri"></a>`{ENDPOINT_URI}` identifikátoru URI koncového bodu

Hodnota identifikátoru URI **koncového bodu** je k dispozici na stránce *Přehled* Azure Portal odpovídajícího prostředku služby pro rozpoznávání. Přejděte na stránku *Přehled* , najeďte myší na koncový bod a zobrazí se <span class="docon docon-edit-copy x-hidden-focus"></span> ikona `Copy to clipboard`. Zkopírujte a použijte tam, kde je to potřeba.

![Shromáždit identifikátor URI koncového bodu pro pozdější použití](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>`{API_KEY}` klíčů

Tento klíč se používá ke spuštění kontejneru a je k dispozici na stránce klíčů Azure Portal odpovídajícího prostředku služby pro rozpoznávání. Přejděte na stránku *klíče* a klikněte na ikonu `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> .

![Získat jeden ze dvou klíčů pro pozdější použití](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Tyto klíče předplatného se používají pro přístup ke službě rozpoznávání rozhraní API. Nesdílejte své klíče. Bezpečně je uložte, například pomocí Azure Key Vault. Tyto klíče doporučujeme také pravidelně obnovovat. K volání rozhraní API je potřeba jenom jeden klíč. Při opětovném generování prvního klíče můžete použít druhý klíč pro pokračování přístupu ke službě.