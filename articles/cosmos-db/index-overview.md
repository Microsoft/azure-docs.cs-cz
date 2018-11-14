---
title: Indexování ve službě Azure Cosmos DB
description: Zjistěte, jak funguje indexování ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: c0525cfba16fb61f8388ae4d6a693be3bb71674c
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628929"
---
# <a name="indexing-in-azure-cosmos-db"></a>Indexování ve službě Azure Cosmos DB

Azure Cosmos DB je nezávislý na schématu databáze a umožňuje rychle iterovat aplikace bez nutnosti schéma nebo správu indexů. Ve výchozím nastavení služby Azure Cosmos DB automaticky indexuje všechny položky ve vašem kontejneru bez potřeby schématu nebo sekundárních indexů, od vývojářů.

## <a name="items-as-trees"></a>Položky jako stromové struktury

Promítnutí položky v kontejneru jako dokumenty JSON a představující jako stromové struktury, služby Azure Cosmos DB sjednotí strukturu a hodnoty instancí napříč položky na sjednocení konceptu **dynamicky kódovaný strukturu cesty** . Každý popisek v dokumentu JSON, který obsahuje názvy vlastností a jejich hodnoty v této reprezentaci stane uzlu stromu. Listy stromu obsahovat skutečných hodnot a zprostředkujících uzly obsahují informace o schématu. Na následujícím obrázku představuje stromů vytvořené pro dvě položky (1 a 2) v kontejneru:

![Strom reprezentaci pro dvě různé položky v kontejneru Azure Cosmos](./media/index-overview/indexing-as-tree.png)

Kořenový uzel pseudo se vytvoří jako nadřazený pro skutečné uzly odpovídající popisky v dokumentu pod. Vnořené datové struktury jednotka hierarchii ve stromové struktuře. Zprostředkující umělé uzly s popiskem s číselnými hodnotami (například 0, 1,...) se použijí pro zastoupení výčtů a indexy pole.

## <a name="index-paths"></a>Index cesty

Azure Cosmos DB projekty položky jako dokumenty JSON a indexu jako stromové struktury. Optimalizujte zásad u cest v rámci stromu. Můžete zahrnout nebo vyloučit cesty ze indexování. To můžete nabízet zápisu Vylepšený výkon a snížit index úložiště pro scénáře, ve kterém jsou dopředu známé vzory dotazů. Další informace najdete v tématu [Index cesty](index-paths.md).

## <a name="indexing-under-the-hood"></a>Indexování: pod pokličkou

Databáze Azure Cosmos se týká automatického indexování dat, ve kterém je každá cesta ve stromové struktuře indexovat, pokud nakonfigurujete tak, aby vylučovat určité cesty.

Azure Cosmos, který využívá databázi obrácený index datová struktura k ukládání informací o každé položky a k usnadnění efektivnější reprezentaci pro dotazování. Strom indexu je dokument, který je vytvořený pomocí spojení všech stromů představující jednotlivé položky v kontejneru. Index strom roste v čase jako nové položky se přidají nebo aktualizují existující položky v kontejneru. Na rozdíl od indexování relační databáze Azure Cosmos DB nerestartuje, indexování od nuly zavedeném nová pole nové položky jsou přidány do stávající struktury. 

Každý uzel stromu. index je index záznam obsahující hodnoty popisek a pozice volá termín a ID položek, volá se, příspěvky. Příspěvky ve složených závorkách (například {1,2}) odpovídají obrácenou index obrázku položky jako Dokument1 a Document2 obsahující hodnotu daného popisku. Důležité nepřímo rovnoměrně zpracovávání popisků schémat i hodnoty instance je, že všechno, co je zabalena uvnitř velký index. Hodnotu instance, která se stále listy neopakuje, může být v různých rolích napříč položky popisky jiné schéma, ale je stejná jako hodnota. Následující obrázek ukazuje obrácenou indexování pro různé položky:

![Indexování pod pokličkou, obrácený indexu](./media/index-overview/inverted-index.png)

> [!NOTE]
> Obráceným indexu může vypadat podobně jako indexování strukturám používaným v vyhledávacího webu v načítání informací o doméně. Pomocí této metody služby Azure Cosmos DB umožňuje vyhledávat databáze pro libovolné položky bez ohledu na jeho strukturu schématu.

Normalizovaná cesta kóduje index dopředné cesta od kořenové hodnotě spolu s informací o typu hodnoty. Cesta a hodnota jsou kódovány poskytnout různé typy indexování – například rozsah prostorových typů. Hodnota kódování je navržené pro poskytování jedinečných hodnot nebo složení cesty.

## <a name="querying-with-indexes"></a>Dotazování s indexy

Obráceným index umožňuje identifikovat dokumenty, které odpovídají predikátu dotazu rychle dotazu. Díky tomu schéma a instance hodnoty rovnoměrně z hlediska cesty, že obrácenou index je také stromu. Proto index a výsledky můžete být serializován na platný dokument JSON a vrátit jako samotných dokumentech, jako jsou vráceny ve stromové struktuře. Tato metoda umožňuje recursing přes výsledky pro další dotazování. Následující obrázek ukazuje příklad indexování v dotazu bodu:  

![Příklad dotazu bodu](./media/index-overview/index-point-query.png)

Rozsah dotazu je GermanTax uživatelem definovanou funkci spouštěny jako součást zpracování dotazů. Uživatelem definované funkce je všechny registrované, funkce JavaScriptu, která dokáže poskytovat bohaté programovací logiky integrované do dotazu. Následující obrázek ukazuje příklad indexování v dotazu na rozsah:

![Příklad dotazu rozsahu](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>Další postup

Další informace o indexování v následujících článcích:

- [Zásady indexování](index-policy.md)
- [Index typy](index-types.md)
- [Index cesty](index-paths.md)
- [Jak spravovat zásady indexování](how-to-manage-indexing-policy.md)