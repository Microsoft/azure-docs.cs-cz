---
title: Co je rozhraní API pro vizuální vyhledávání Bingu?
titleSuffix: Azure Cognitive Services
description: Vizuální vyhledávání Bingu poskytuje podrobnosti nebo přehledy o obrázku, například podobné obrázky nebo nákupní zdroje.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 03/27/2019
ms.author: scottwhi
ms.openlocfilehash: bd93166cb878fbd961795492ed9a035c919429a8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "64916629"
---
# <a name="what-is-the-bing-visual-search-api"></a>Co je rozhraní API pro vizuální vyhledávání Bingu?

Bing API pro vizuální vyhledávání vrací přehledy o určité imagi. Můžete nahrát obrázek nebo zadejte adresu URL do jedné. Přehledy jsou vizuálně podobné obrázky, nákupní zdrojů, webové stránky, které zahrnují image a další. Jsou podobné těch, které jsou zobrazené na Bing.com/images přehledy vrácená rozhraním API vizuální vyhledávání Bingu.

Pokud používáte [API pro vyhledávání obrázků Bingu](../bing-image-search/overview.md), můžete používat tokeny přehledy z výsledků hledání toto rozhraní API pro vizuální vyhledávání Bingu místo nahrání obrázku.

> [!IMPORTANT]
> Pokud se zobrazí obrázek přehledy s využitím rozhraní API Bingu pro vyhledávání obrázků, zvažte možnost API Bingu pro vizuální vyhledávání, které poskytuje komplexnější Přehled.

## <a name="insights"></a>Insights

Je možné zjistit tyto přehledy s využitím vizuálního vyhledávání Bingu:

| Přehled                              | Popis |
|--------------------------------------|-------------|
| Vizuálně podobné obrázky              | Seznam imagí, které jsou vizuálně podobné vstupního obrázku. |
| Vizuálně podobné produkty            | Produkty, které jsou vizuálně podobné produkt zobrazí.            |
| Nákupní zdroje                     | Místa, kde si můžete koupit položky zobrazené v vstupního obrázku.            |
| Související hledání                     | Související hledání od ostatních nebo které jsou na základě obsahu obrázku.            |
| Webové stránky, které zahrnují image     | Webové stránky, které zahrnují vstupního obrázku.            |
| Recepty                              | Webové stránky, které obsahují recepty vznesení pokrmu vstupního obrázku.            |

Kromě přehledu o pro vizuální vyhledávání Bingu vrátí různé podmínky (značky) odvozené ze vstupního obrázku. Značky umožňují uživatelům Seznamte se s koncepty, které jsou součástí image. Pokud vstupního obrázku je známý athlete, jedna značka může být název sportovce, jiné značky může být sportu. Nebo pokud vstupního obrázku je apple výsečové, značky může být Apple výsečového, Pies a My jsme zvolili zákusky.

Výsledky pro vizuální vyhledávání Bingu také obsahovat ohraničující polí pro oblasti zájmu v bitové kopii. Například pokud image obsahuje několik celebrit, výsledky mohou zahrnovat ohraničující políčka pro každou rozpoznaný celebrit. Nebo pokud Bingu rozpozná produktu nebo oblečení v bitové kopii, výsledek může zahrnovat rámeček ohraničující konkrétní rozpoznaných položek.

## <a name="workflow"></a>Pracovní postup

Bing API pro vizuální vyhledávání je RESTful webová služba, což usnadňuje volat z libovolného programovacího jazyka, který může vytvářet požadavky HTTP a parsování formátu JSON. Rozhraní REST API nebo sady SDK můžete použít pro službu.

1. Vytvoření [účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přístup k rozhraní API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si [zdarma vytvořit účet](https://azure.microsoft.com/free/). Můžete získat klíč předplatného z [webu Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#access-your-resource) po vytvoření účtu, nebo [web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis) až po dokončení aktivace bezplatné zkušební verze.
2. Odeslat požadavek na rozhraní API pomocí platný vyhledávacího dotazu.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další postup

Nejprve zkuste Visual API Bingu pro vyhledávání [interaktivní ukázka](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/).
Ukázka ukazuje, jak můžete rychle přizpůsobení vyhledávací dotaz a projeďte obrázky na webu.

Abyste mohli rychle začít s vaší první žádost, najdete v článku postupů rychlý start: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Další informace najdete v tématech

* [Image - vizuálního vyhledávání](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) referenční popisuje definice a informace o koncových bodech, požádat o hlavičky odpovědi a výsledky hledání parametry dotazu, které slouží k požadavku na základě bitové kopie.

* [Pomocí rozhraní API Bingu pro vyhledávání a zobrazit požadavky](../bing-web-search/use-display-requirements.md) zadejte přijatelné použití obsahu a informací získaných z rozhraní API pro vyhledávání Bingu.
