---
title: Indexování ve službě Azure Cosmos DB
description: Zjistěte, jak funguje indexování ve službě Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: ecf53251020ce1b639a5bf8da65f5d31ff699db9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59265691"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexování ve službě Azure Cosmos DB – přehled

Azure Cosmos DB je nezávislý na schématu databáze a umožňuje rychle iterovat aplikace bez nutnosti schéma nebo správu indexů. Ve výchozím nastavení služby Azure Cosmos DB automaticky indexuje všechny položky ve vašem kontejneru bez potřeby schématu nebo sekundárních indexů, od vývojářů.

## <a name="items-as-trees"></a>Položky jako stromové struktury

Promítnutí položky v kontejneru jako dokumenty JSON a představující jako stromové struktury, služby Azure Cosmos DB sjednotí strukturu a hodnoty instancí napříč položky na sjednocení konceptu **dynamicky kódovaný strukturu cesty** . V této reprezentaci každého popisku v dokumentu JSON, který obsahuje názvy vlastností a jejich hodnoty, bude uzel stromu. Listy stromu obsahovat skutečných hodnot a zprostředkujících uzly obsahují informace o schématu. Na následujícím obrázku představuje stromů vytvořené pro dvě položky (1 a 2) v kontejneru Azure Cosmos:

![Strom reprezentaci pro dvě různé položky v kontejneru Azure Cosmos](./media/index-overview/indexing-as-tree.png)

Kořenový uzel pseudo se vytvoří jako nadřazený pro skutečné uzly odpovídající popisky v dokumentu JSON pod. Vnořené datové struktury jednotka hierarchii ve stromové struktuře. Zprostředkující umělé uzly s popiskem s číselnými hodnotami (například 0, 1,...) se použijí pro zastoupení výčtů a indexy pole.

## <a name="index-paths"></a>Cesty indexů

Azure Cosmos DB projekty položky v kontejneru Azure Cosmos jako dokumenty JSON a indexu jako stromové struktury. Pak můžete ladit zásady index u cest v rámci stromu. Můžete zahrnout nebo vyloučit cesty ze indexování. To můžete nabízet zápisu Vylepšený výkon a snížit index úložiště pro scénáře, ve kterém jsou dopředu známé vzory dotazů. Další informace najdete v tématu [Index cesty](index-paths.md).

## <a name="indexing-under-the-hood"></a>Indexování: Pohled pod kapotu

Databáze Azure Cosmos se vztahuje *automatického indexování* k datům, kde každá cesta ve stromové struktuře indexována, pokud nakonfigurujete tak, aby vylučovat určité cesty.

Azure Cosmos, který využívá databázi obrácený index datová struktura k ukládání informací o každé položky a k usnadnění efektivnější reprezentaci pro dotazování. Strom indexu je dokument, který je vytvořený pomocí spojení všech stromů představujících jednotlivé položky v kontejneru. Index strom roste v průběhu času jako nové položky se přidají nebo aktualizují existující položky v kontejneru. Na rozdíl od indexování relační databáze Azure Cosmos DB nerestartuje indexování od nuly, jako jsou zavedeny nové pole. Nové položky jsou přidány do stávající struktury indexu. 

Každý uzel stromu. index je index záznam obsahující hodnoty popisek a umístění, volá se, *termín*a ID položek, volá se, *příspěvky*. Příspěvky ve složených závorkách (například {1,2}) obrácenou index obrázku odpovídají položkám, jako *Dokument1* a *Document2* obsahující hodnotu daného popisku. Důležité nepřímo rovnoměrně zpracovávání popisků schémat a hodnoty instance je, že všechno, co je zabalena uvnitř velký index. Hodnotu instance, která se stále listy neopakuje, může být v různých rolích napříč položky popisky jiné schéma, ale je stejná jako hodnota. Následující obrázek ukazuje obrácenou indexování pro dvě různé položky:

![Indexování pod pokličkou, obrácený indexu](./media/index-overview/inverted-index.png)

> [!NOTE]
> Obráceným indexu může vypadat podobně jako indexování strukturám používaným v vyhledávacího webu v načítání informací o doméně. Pomocí této metody služby Azure Cosmos DB umožňuje vyhledávat databáze pro libovolné položky bez ohledu na jeho strukturu schématu.

Normalizovaná cesta kóduje index dopředné cesta od kořenové hodnotě spolu s informací o typu hodnoty. Cesta a hodnota jsou kódovány poskytnout různé typy indexovat jako je oblast, prostorová atd. Hodnota kódování je navržené pro poskytování jedinečných hodnot nebo složení cesty.

## <a name="querying-with-indexes"></a>Dotazování s indexy

Obráceným index umožňuje identifikovat dokumenty, které odpovídají predikátu dotazu rychle dotazu. Zpracováním schéma a hodnoty instance rovnoměrně z hlediska cesty obrácenou index je také stromu. Proto index a výsledky můžete být serializován na platný dokument JSON a vrátit jako samotných dokumentech, jako jsou vráceny ve stromové struktuře. Tato metoda umožňuje recursing přes výsledky pro další dotazování. Následující obrázek ukazuje příklad indexování v dotazu bodu:  

![Příklad dotazu bodu](./media/index-overview/index-point-query.png)

Rozsah dotazu *GermanTax* je [uživatelem definovanou funkci](stored-procedures-triggers-udfs.md#udfs) spouštěny jako součást zpracování dotazů. Uživatelem definované funkce je všechny registrované, funkce JavaScriptu, která dokáže poskytovat bohaté programovací logiky integrované do dotazu. Následující obrázek ukazuje příklad indexování v dotazu na rozsah:

![Příklad dotazu rozsahu](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>Další postup

Další informace o indexování v následujících článcích:

- [Zásady indexování](index-policy.md)
- [Index typy](index-types.md)
- [Index cesty](index-paths.md)
- [Jak spravovat zásady indexování](how-to-manage-indexing-policy.md)
