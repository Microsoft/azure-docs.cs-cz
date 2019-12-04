---
title: Přidat akci dotazu do aplikace Logic Apps
description: Přehled akce dotazu pro provádění akcí, jako je pole filtru
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/20/2016
tags: connectors
ms.openlocfilehash: 2e5c9d371a280b776699b2e10d3e8e94b5f41f6f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74787128"
---
# <a name="get-started-with-the-query-action"></a>Začněte s akcí dotazu

Pomocí akce dotazu můžete pracovat s dávkami a poli a provádět pracovní postupy:

* Vytvoří úkol pro všechny záznamy s vysokou prioritou z databáze.
* Uložte všechny přílohy PDF pro e-maily do objektu blob Azure.

Chcete-li začít používat akci dotazu v aplikaci logiky, přečtěte si téma [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Použít akci dotazu

Akce je operace, kterou provede pracovní postup, který je definovaný v aplikaci logiky. 
[Přečtěte si další informace o akcích](../connectors/apis-list.md).  

Akce dotazu nyní obsahuje jednu operaci s názvem pole filtru, která je vystavena v návrháři. To vám umožní zadat dotaz na pole a vrátit sadu filtrovaných výsledků.

Tady je postup, jak ho můžete přidat do aplikace logiky:

1. Vyberte tlačítko **Nový krok** .
2. Vyberte **přidat akci**.
3. Do vyhledávacího pole Akce zadejte **Filter** a vypište akci **filtru pole** .
   
    ![Vyberte akci dotazu.](./media/connectors-native-query/using-action-1.png)
4. Vyberte pole, které chcete filtrovat. (Na následujícím snímku obrazovky vidíte pole výsledků hledání v Twitteru.)
5. Vytvořte podmínku pro vyhodnocení každé položky. (Následující filtry snímků obrazovky se tweety na uživatele, kteří mají více než 100 sledujících.)
   
    ![Dokončení akce dotazu](./media/connectors-native-query/using-action-2.png)
   
    Akce vytvoří výstup nového pole, které obsahuje pouze výsledky, které splňují požadavky filtru.
6. Kliknutím na levý horní roh panelu nástrojů ho uložíte a vaše aplikace logiky bude ukládat i publikovat (aktivovat).

\* Pokud voláte koncový bod HTTP a dostanete odpověď JSON, použijte akci _analyzovat JSON_ k analýze odpovědi JSON. Bez provedení tohoto kroku uvidí _pole Filter_ pouze tělo a nebude rozumět struktuře datové části JSON.

## <a name="query-action"></a>Akce dotazu
Zde jsou uvedeny podrobnosti o akci, kterou tento konektor podporuje. Konektor má jednu možnou akci.

| Akce | Popis |
| --- | --- |
| Filtrovat pole |Vyhodnotí podmínku pro každou položku v poli a vrátí výsledky. |

## <a name="action-details"></a>Podrobnosti akce
Akce dotazu se nachází v jedné možné akci. Následující tabulky popisují požadovaná a volitelná vstupní pole pro akci a odpovídající výstupy, které jsou přidruženy k použití akce.

### <a name="filter-array"></a>Filtrovat pole
Níže jsou vstupní pole pro akci, která vytváří odchozí požadavek HTTP.
Znak * znamená, že se jedná o povinné pole.

| Zobrazované jméno | Název vlastnosti | Popis |
| --- | --- | --- |
| Výsledkem |Výsledkem |Pole, které se má filtrovat |
| Pomocné |, |Podmínka, která se má vyhodnotit pro každou položku |

<br>

### <a name="output-details"></a>Podrobnosti výstupu
Níže jsou uvedeny podrobnosti výstupu pro odpověď HTTP.

| Název vlastnosti | Data type | Popis |
| --- | --- | --- |
| Filtrované pole |pole |Pole, které obsahuje objekt pro každý filtrovaný výsledek |

## <a name="next-steps"></a>Další kroky
Teď si vyzkoušejte platformu a [vytvořte aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Další dostupné konektory můžete prozkoumat v části Logic Apps, a to tak, že si prohlédněte náš [seznam rozhraní API](apis-list.md).

