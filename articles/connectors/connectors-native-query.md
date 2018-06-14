---
title: Přidání akce dotazu v aplikace logiky | Microsoft Docs
description: Přehled akce dotazu pro provádění akcí, jako je pole filtru.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 05dd4ae3c4ee439d66401a3f5595f9104051f8ee
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27962641"
---
# <a name="get-started-with-the-query-action"></a>Začínáme s akce dotazu
Pomocí akce dotazu, můžete pracovat se dávek a aby provést pracovní postupy pro pole:

* Vytvořte úlohu pro všechny záznamy s vysokou prioritou z databáze.
* Uložte všechny přílohy PDF pro e-mailů do objektu blob Azure.

Chcete-li začít používat akce dotazu v aplikaci logiky, přečtěte si téma [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Pomocí akce dotazu
Akce je operace, která se provádí v pracovním postupu, který je definován v aplikaci logiky. [Další informace o akcích](connectors-overview.md).  

Akce dotazu má aktuálně jednu operaci, názvem pole filtru, který je zveřejněný v návrháři. To umožňuje dotazování pole a vrátit sadu filtrované výsledky.

Zde je, jak můžete přidat logiku aplikace:

1. Vyberte **nový krok** tlačítko.
2. Zvolte **přidat akci**.
3. Zadejte do vyhledávacího pole Akce **filtru** do seznamu **pole filtru** akce.
   
    ![Vyberte akci, která dotazu](./media/connectors-native-query/using-action-1.png)
4. Vyberte pole filtrovat. (Následující snímek obrazovky ukazuje pole výsledky vyhledávání na Twitteru.)
5. Vytvořte podmínku, kterou chcete vyhodnotit na každou položku. (Následující snímek obrazovky filtruje tweetů od uživatelů, kteří mají více než 100 délky.)
   
    ![Dokončení akce dotazu](./media/connectors-native-query/using-action-2.png)
   
    Akce bude výstup nové pole, které obsahuje pouze výsledky, které splnění požadavků filtru.
6. Klikněte levém horním panelu nástrojů na Uložit a aplikace logiky jak uložíte a publikovat (aktivovat).

\*Pokud jste volání koncový bod HTTP a příjem odpověď JSON, použijte _analyzovat JSON_ akce analyzovat odpověď JSON. Bez nutnosti převádět tento krok _pole filtru_ bude zobrazit pouze text a není srozumitelný strukturu datové části JSON.

## <a name="query-action"></a>Akce dotazu
Tady jsou uvedené podrobnosti pro akci, která tento konektor podporuje. Konektor je možné jednu akci.

| Akce | Popis |
| --- | --- |
| Filtrovat pole |Vyhodnocuje podmínku pro každou položku v pole a vrátí výsledky |

## <a name="action-details"></a>Detaily akce
Akce dotazu obsahuje jednu možné akci. Následující tabulky popisují požadované a volitelné vstupních polí pro akce a odpovídající výstup podrobnosti, které jsou spojené s použitím akce.

### <a name="filter-array"></a>Filtrovat pole
Níže jsou uvedeny vstupních polí pro akce, která umožňuje odchozí požadavku HTTP.
A * znamená, že je povinné pole.

| Zobrazované jméno | Název vlastnosti | Popis |
| --- | --- | --- |
| Z * |od |Pole pro filtrování |
| Podmínka * |kde |Podmínku, která má vyhodnotit pro každou položku |

<br>

### <a name="output-details"></a>Podrobnosti výstupu
Níže jsou uvedeny podrobnosti výstup pro odpověď HTTP.

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Filtrované pole |pole |Pole, které obsahuje objekt pro každý filtrované výsledek |

## <a name="next-steps"></a>Další postup
Teď vyzkoušet platformu a [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ostatní konektory k dispozici v aplikace logiky můžete prozkoumat pohledem na našem [rozhraní API seznamu](apis-list.md).

