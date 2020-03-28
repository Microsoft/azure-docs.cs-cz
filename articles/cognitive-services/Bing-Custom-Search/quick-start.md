---
title: 'Rychlý start: Vytvoření první instance vlastního vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Tento rychlý start slouží k vytvoření vlastní instance Bingu, která může vyhledávat domény a webové stránky, které definujete.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: b8287250df4e278d4904e31121ed7d2df208e1c9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238857"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Rychlý start: Vytvoření první instance vlastního vyhledávání Bingu

Pokud chcete použít Vlastní vyhledávání Bingu, musíte vytvořit instanci vlastního vyhledávání, která definuje vaše zobrazení nebo řez webu. Tato instance obsahuje veřejné domény, weby a webové stránky, které chcete prohledávat, spolu s případnými úpravami hodnocení. 

Chcete-li vytvořit instanci, použijte [portál vlastního vyhledávání Bingu](https://customsearch.ai). 

![Obrázek portálu vlastního vyhledávání Bing](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Vytvoření instance vlastního vyhledávání

Vytvoření instance vlastního vyhledávání Bingu:

1. Na webové stránce [Portálu vlastního vyhledávání Bingu](https://customsearch.ai) klikněte na **Začínáme** a přihlaste se pomocí svého účtu Microsoft.

2. Klikněte na **Nová instance**a zadejte popisný název. Název instance můžete kdykoli změnit.
 
3. Na kartě **Aktivní** v části **Vyhledávací prostředí** zadejte adresy URL webů, které chcete do vyhledávání zahrnout. 

    > [!NOTE]
    > Instance vlastního vyhledávání Bingvrátí výsledky pouze pro domény a webové stránky, které jsou veřejné a byly indexovány službou Bing.

4. Na pravé straně portálu vlastního vyhledávání Bingu můžete zadat dotaz a prozkoumat výsledky hledání vrácené instancí hledání. Pokud nejsou vráceny žádné výsledky, zkuste zadat jinou adresu URL.  

5. Kliknutím na **Publikovat** publikujte změny v produkčním prostředí a aktualizujte koncové body instance.

6.  Klikněte na kartu **Výroba** v části **Koncové body**a zkopírujte **vlastní ID konfigurace**. Toto ID potřebujete k volání rozhraní API pro `customconfig=` vlastní vyhledávání připojením k parametru dotazu ve vašich voláních.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Úvodní příručka: Volání ke konečnému bodu vlastního vyhledávání Bingu](./call-endpoint-csharp.md)
