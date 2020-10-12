---
title: Azure Monitor sešitů – transformují data JSON pomocí JSONPath
description: Použití JSONPath v sešitech Azure Monitor k transformaci výsledků dat JSON vrácených pomocí dotazovaného koncového bodu do formátu, který si přejete.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: efa54933ac7d57ec0dcff9ae11b6fb5c2c87a897
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081385"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>Jak používat JSONPath k transformaci dat JSON v sešitech

Sešity se mohou dotazovat na data z mnoha zdrojů. Některé koncové body, například [Azure Resource Manager](../../azure-resource-manager/management/overview.md) nebo vlastní koncový bod, mohou vracet výsledky ve formátu JSON. Pokud data JSON vrácená koncovým bodem nejsou nakonfigurovaná ve formátu, který si přejete, můžete k transformaci výsledků použít JSONPath.

JSONPath je dotazovací jazyk pro JSON, který se podobá XPath pro XML. Podobně jako XPath JSONPath umožňuje extrakci a filtraci dat ze struktury JSON.

Pomocí transformace JSONPath mohou autoři sešitu převést JSON na strukturu tabulky. Tabulka se pak dá použít k vykreslení [vizualizací sešitu](workbooks-visualizations.md).

## <a name="using-jsonpath"></a>Použití JSONPath

1. Kliknutím na položku panelu nástrojů pro *Úpravy* přepněte sešit do režimu úprav.
2. K *Add*  >  Přidání ovládacího prvku dotazu do sešitu použijte odkaz Přidat*dotaz* přidat.
3. Vyberte zdroj dat jako *JSON*.
4. Pomocí editoru JSON zadejte následující fragment kódu JSON.
    ```json
    { "store": {
        "books": [ 
          { "category": "reference",
            "author": "Nigel Rees",
            "title": "Sayings of the Century",
            "price": 8.95
          },
          { "category": "fiction",
            "author": "Evelyn Waugh",
            "title": "Sword of Honour",
            "price": 12.99
          },
          { "category": "fiction",
            "author": "Herman Melville",
            "title": "Moby Dick",
            "isbn": "0-553-21311-3",
            "price": 8.99
          },
          { "category": "fiction",
            "author": "J. R. R. Tolkien",
            "title": "The Lord of the Rings",
            "isbn": "0-395-19395-8",
            "price": 22.99
          }
        ],
        "bicycle": {
          "color": "red",
          "price": 19.95
        }
      }
    }
    ```  

Předpokládejme, že se vám jako reprezentace inventáře obchodu přidáváme výše uvedený objekt JSON. Naším úkolem je vytvořit tabulku dostupných knih obchodu, a to zadáním jejich názvů, autorů a cen.

1. Vyberte kartu *nastavení výsledků* a přepněte formát výsledku na *cestu JSON*.
2. Použijte následující nastavení cesty JSON:

    Tabulka cesty JSON: `$.store.books` . Toto pole představuje cestu k kořenovému adresáři tabulky. V tomto případě se zajímáme o inventář knih v obchodě. Cesta k tabulce filtruje kód JSON s informacemi o knize.

   | ID sloupců | Cesta JSON sloupce |
   |:-----------|:-----------------|
   | Nadpis      | `$.title`        |
   | Autor     | `$.author`       |
   | Cena      | `$.price`        |

    ID sloupců budou záhlaví sloupců. Pole cesty JSON sloupce označují cestu z kořene tabulky k hodnotě sloupce.

1. Výše uvedená nastavení použijte kliknutím na tlačítko *Spustit dotaz* .

![ Úprava položky dotazu se zdrojem dat JSON a formátem výsledku cesty JSON](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>Další kroky
- [Přehled sešitů](workbooks-overview.md)
- [Skupiny v Azure Monitor sešitech](workbooks-groups.md)
