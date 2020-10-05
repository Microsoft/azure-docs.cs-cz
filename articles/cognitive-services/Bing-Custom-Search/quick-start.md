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
ms.openlocfilehash: b8287250df4e278d4904e31121ed7d2df208e1c9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "80238857"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Rychlý start: Vytvoření první instance vlastního vyhledávání Bingu

Pokud chcete použít Vlastní vyhledávání Bingu, musíte vytvořit instanci vlastního vyhledávání, která definuje vaše zobrazení nebo řez webu. Tato instance obsahuje veřejné domény, weby a webové stránky, které chcete vyhledat, spolu s případnými úpravami řazení, která budete chtít. 

Chcete-li vytvořit instanci, použijte [portál vlastní vyhledávání Bingu](https://customsearch.ai). 

![Obrázek Vlastní vyhledávání Binguového portálu](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Vytvoření instance vlastního vyhledávání

Vytvoření instance vlastního vyhledávání Bingu:

1. Klikněte **na tlačítko Začínáme na** webové stránce [portálu vlastní vyhledávání Bingu](https://customsearch.ai) a přihlaste se pomocí účet Microsoft.

2. Klikněte na **Nová instance**a zadejte popisný název. Název instance můžete kdykoli změnit.
 
3. Na kartě **Aktivní** v části **Vyhledávací prostředí** zadejte adresy URL webů, které chcete do vyhledávání zahrnout. 

    > [!NOTE]
    > Instance Vlastní vyhledávání Bingu budou vracet pouze výsledky pro domény a webové stránky, které jsou veřejné a indexovány pomocí Bingu.

4. K zadání dotazu a kontrole výsledků hledání vrácených instancí hledání můžete použít pravou stranu portálu Vlastní vyhledávání Bingu. Pokud se nevrátí žádné výsledky, zkuste zadat jinou adresu URL.  

5. Kliknutím na **publikovat** publikujte změny do provozního prostředí a aktualizujte koncové body instance.

6.  V části **koncové body**klikněte na kartu **Výroba** a zkopírujte **ID vlastní konfigurace**. Toto ID budete potřebovat pro volání rozhraní API pro vlastní vyhledávání tím, že ho připojíte k `customconfig=` parametru dotazu ve vašich voláních.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rychlý Start: volání koncového bodu Vlastní vyhledávání Bingu](./call-endpoint-csharp.md)
