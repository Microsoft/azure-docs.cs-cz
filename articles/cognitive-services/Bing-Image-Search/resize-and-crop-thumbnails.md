---
title: Změna velikosti a oříznout miniatury Bingu | Dokumentace Microsoftu
description: Ukazuje, jak změnit velikost a oříznout miniatury zahrnutý v odpovědi Bingu.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: F4FFAE91-A003-4F7C-8E60-83A142485E28
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 98c4caa50ca5e861f4276e26983ef501d17bd349
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "41987526"
---
# <a name="resizing-and-cropping-thumbnail-images"></a>Změna velikosti a oříznutí obrázků miniatur

Při zpracování vyhledávací dotaz Bingu vygenerují miniatury informace pro všechny Image v jeho [odpovědi](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#bing-image-search-response-format). Tyto informace slouží k zobrazení všech nebo podmnožinu vrácené miniatury. Pokud zobrazíte dílčí poskytují možnost, chcete-li zobrazit zbývající bitové kopie. 


<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Pokud uživatel na miniaturu klikne, můžete mu s použitím hodnoty [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) zobrazit obrázek v plné velikosti. Nezapomeňte obrázku přiřadit atribut.

Pokud je hodnota `shoppingSourcesCount` nebo `recipeSourcesCount` větší než 0, přidejte k miniatuře odznáček, například nákupního košíku, který bude značit, že pro objekt na obrázku je k dispozici možnost nákupu nebo zobrazení receptů.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

Pokud chcete získat informace o obrázku, například webové stránky, které obrázek obsahují, nebo osoby rozpoznané na obrázku, použijte hodnotu [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken). Podrobnosti najdete v tématu [Přehledy obrázků](image-insights.md).

## <a name="resizing-and-cropping-thumbnails"></a>Změna velikosti a oříznutí miniatury

Můžete také změnit velikost a rozbalte miniatury, například když najede uživatele kurzoru nad ním. 
> [!NOTE]
> Pokud obrázek zvětšíte, nezapomeňte mu přiřadit atribut. Můžete například extrahovat název hostitele z hodnoty [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) a zobrazit ho pod obrázkem. 

[!INCLUDE [cognitive-services-bing-resize-crop-thumbnails](../../../includes/cognitive-services-bing-resize-crop-thumbnails.md)]