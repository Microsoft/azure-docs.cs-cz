---
title: 'Rychlý start: Vytvoření první instance vlastního vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete vytvořit vlastní instanci Bingu, která bude prohledávat domény a webové stránky, které definujete.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 726a60d611abc392bc1a4629c5088ca4c6b703d9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338329"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Rychlý start: Vytvoření první instance vlastního vyhledávání Bingu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Pokud chcete použít Vlastní vyhledávání Bingu, musíte vytvořit instanci vlastního vyhledávání, která definuje vaše zobrazení nebo řez webu. Tato instance obsahuje veřejné domény, weby a webové stránky, které chcete vyhledat, spolu s případnými úpravami řazení, která budete chtít. 

Chcete-li vytvořit instanci, použijte [portál vlastní vyhledávání Bingu](https://customsearch.ai). 

![Obrázek Vlastní vyhledávání Binguového portálu](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Vytvoření instance vlastního vyhledávání

Vytvoření instance vlastního vyhledávání Bingu:

1. Klikněte **na tlačítko Začínáme na** webové stránce [portálu vlastní vyhledávání Bingu](https://customsearch.ai) a přihlaste se pomocí účet Microsoft.

2. Klikněte na **Nová instance** a zadejte popisný název. Název instance můžete kdykoli změnit.
 
3. Na kartě **Aktivní** v části **Vyhledávací prostředí** zadejte adresy URL webů, které chcete do vyhledávání zahrnout. 

    > [!NOTE]
    > Instance Vlastní vyhledávání Bingu budou vracet pouze výsledky pro domény a webové stránky, které jsou veřejné a indexovány pomocí Bingu.

4. K zadání dotazu a kontrole výsledků hledání vrácených instancí hledání můžete použít pravou stranu portálu Vlastní vyhledávání Bingu. Pokud se nevrátí žádné výsledky, zkuste zadat jinou adresu URL.  

5. Kliknutím na **publikovat** publikujte změny do provozního prostředí a aktualizujte koncové body instance.

6.  V části **koncové body** klikněte na kartu **Výroba** a zkopírujte **ID vlastní konfigurace**. Toto ID budete potřebovat pro volání rozhraní API pro vlastní vyhledávání tím, že ho připojíte k `customconfig=` parametru dotazu ve vašich voláních.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rychlý Start: volání koncového bodu Vlastní vyhledávání Bingu](./call-endpoint-csharp.md)