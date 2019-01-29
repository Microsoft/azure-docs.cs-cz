---
title: Co je Vizuální vyhledávání Bingu?
titleSuffix: Azure Cognitive Services
description: Vizuální vyhledávání Bingu poskytuje podrobnosti nebo přehledy o obrázku, například podobné obrázky nebo nákupní zdroje.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 61a851b0efbcc4fdb55308e47447d218014ef9e0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154350"
---
# <a name="what-is-the-bing-visual-search-api"></a>Co je rozhraní API pro vizuální vyhledávání Bingu?

Rozhraní API pro vizuální vyhledávání Bingu poskytuje podobné informace o obrázcích zobrazeným jako Bing.com/images. Nahrávání obrázku nebo poskytnutím adresy URL do jednoho, toto rozhraní API identifikovat celou řadu podrobnosti, včetně vizuálně podobné obrázky, nákupní zdrojů, webové stránky, které zahrnují image a další. Pokud používáte [API pro vyhledávání obrázků Bingu](../bing-image-search/overview.md), můžete používat tokeny insight připojený k rozhraní API výsledky hledání namísto odesílání bitovou kopii.

## <a name="insights"></a>Insights

Tady jsou přehledy, můžete pro vizuální vyhledávání můžete zjistit:

| Přehled                              | Popis |
|--------------------------------------|-------------|
| Vizuálně podobné obrázky              | Seznam imagí, které jsou vizuálně podobné vstupního obrázku. |
| Vizuálně podobné produkty            | Produkty, které jsou vizuálně podobné produkt zobrazí.            |
| Nákupní zdroje                     | Místa, kde si můžete koupit položky zobrazené v vstupního obrázku.            |
| Související hledání                     | Související hledání od ostatních nebo které jsou na základě obsahu obrázku.            |
| Webové stránky, které obsahují image     | Webové stránky, které zahrnují vstupního obrázku.            |
| Recepty                              | Webové stránky, které obsahují recepty vznesení pokrmu vstupního obrázku            |

Kromě těchto přehledů vizuální vyhledávání vrátí také rozmanitou sadu termínů (značek) odvozených od zadaného obrázku. Tyto značky umožňují uživatelům prozkoumat koncepty, které jsou součástí obrázku. Pokud je třeba na zadaném obrázku známý sportovec, může být jednou značkou jméno sportovce a další značkou může být Sport. Nebo pokud je na zadaném obrázku jablečný koláč, můžou být značky Jablečný koláč, Koláče, Dezerty, takže uživatelé můžou prozkoumat související koncepty.

Výsledky vizuálního vyhledávání také zahrnují ohraničující rámečky pro oblasti zájmu na obrázku. Pokud třeba obrázek obsahuje několik celebrit, můžou výsledky zahrnovat ohraničující rámečky pro každou rozpoznanou celebritu na obrázku. Nebo pokud Bing rozpozná produkt nebo oblečení na obrázku, může výsledek zahrnovat ohraničující rámeček pro rozpoznaný produkt nebo kus oblečení.

> [!IMPORTANT]
> Pokud se zobrazí obrázek přehledy s využitím rozhraní API Bingu pro vyhledávání obrázků, zvažte možnost API Bingu pro vizuální vyhledávání, které poskytuje komplexnější Přehled.

## <a name="workflow"></a>Pracovní postup

Bing API pro vizuální vyhledávání je RESTful webová služba, což usnadňuje volat z libovolného programovacího jazyka, který může vytvářet požadavky HTTP a parsování formátu JSON. Můžete použít službu pomocí rozhraní REST API nebo sady SDK.

1. Vytvoření účtu rozhraní API služeb Cognitive Services s přístupem k rozhraní API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete zdarma vytvořit účet.
2. Odeslat požadavek na rozhraní API pomocí platný vyhledávacího dotazu.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.


## <a name="next-steps"></a>Další postup

Nejprve vyzkoušejte [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/) rozhraní API Bingu pro vyhledávání obrázků.
Tato ukázka znázorňuje, jak můžete rychle přizpůsobit vyhledávací dotaz a procházet obrázky na webu.

Jakmile budete připraveni volat rozhraní API, vytvořte si [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Abyste mohli rychle začít s vaší první žádost, najdete v článku postupů rychlý start: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).


## <a name="see-also"></a>Další informace najdete v tématech

* [Odkaz pro vizuální vyhledávání Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) dokumentace obsahuje definice a informace o koncových bodech, záhlaví, odpovědi rozhraní API a parametry dotazu, které slouží k vyžádání výsledky hledání založené na bitové kopii.

* [Požadavky Bingu na zobrazení a použití](./use-and-display-requirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.
