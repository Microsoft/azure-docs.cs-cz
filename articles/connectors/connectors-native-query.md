---
title: Přidání akce dotazu ve službě logic apps | Dokumentace Microsoftu
description: Přehled akce dotazu pro provádění akcí, jako je filtrování pole.
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
ms.openlocfilehash: 2a82afe396039857e5b9ad6b8a6d0e710573037f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60538214"
---
# <a name="get-started-with-the-query-action"></a>Začínáme s akce dotazu
Pomocí akce dotazu můžete pracovat s listy a pole k provedení pracovní postupy pro:

* Vytvořte úlohu pro všechny záznamy s vysokou prioritou z databáze.
* Uloží všechny přílohy PDF pro e-mailů do objektu blob Azure.

Abyste mohli začít používat akce dotazu v aplikaci logiky, najdete v článku [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Pomocí akce dotazu
Akce je operace, která provádí pracovní postup, který je definován v aplikaci logiky. 
[Další informace o akcích](../connectors/apis-list.md).  

Akce dotazu právě jedna operace, s názvem filtrování pole, která je vystavena v návrháři. To umožňuje zadat dotaz na pole a vrátí sadu filtrované výsledky.

Zde je, jak můžete přidat v aplikaci logiky:

1. Vyberte **nový krok** tlačítko.
2. Zvolte **přidat akci**.
3. Zadejte do vyhledávacího pole Akce **filtr** do seznamu **filtrování pole** akce.
   
    ![Výběr akce dotazu](./media/connectors-native-query/using-action-1.png)
4. Vyberte pole pro filtrování. (Na následujícím snímku obrazovky se zobrazí pole výsledky vyhledávání na Twitteru.)
5. Vytvořte podmínku, která chcete vyhodnotit pro každou položku. (Na následujícím snímku obrazovky filtry tweety od uživatelů, kteří mají více než 100 sledujícími.)
   
    ![Dokončení akce dotazu](./media/connectors-native-query/using-action-2.png)
   
    Nové pole obsahující pouze výsledky, které splňuje požadavky filtru bude výstup akce.
6. Klikněte levém horním rohu panelu nástrojů uložte a vaše aplikace logiky se jak uložit a publikovat (aktivovat).

\* Pokud jste volání koncového bodu HTTP a příjem odpovědi JSON, použijte _Parsovat JSON_ akce parsovat JSON odpovědi. Bez provedení tohoto kroku _filtrování pole_ zobrazit pouze text, který se není srozumitelný struktura datovou část JSON.

## <a name="query-action"></a>Akce dotazu
Tady jsou uvedené podrobnosti pro akci, která podporuje tento konektor. Konektor má jednu akci je to možné.

| Akce | Popis |
| --- | --- |
| Filtrovat pole |Vyhodnocuje podmínku pro každou položku v poli a vrátí výsledky |

## <a name="action-details"></a>Detaily akce
Akce dotazu obsahuje jednu akci je to možné. Následující tabulky popisují požadované a volitelné vstupní pole pro akce a odpovídající podrobnosti výstupu, které jsou spojené s použitím akce.

### <a name="filter-array"></a>Filtrovat pole
Níže jsou vstupní pole pro akce, která umožňuje odchozí požadavek HTTP.
A * znamená, že je povinné pole.

| Zobrazované jméno | Název vlastnosti | Popis |
| --- | --- | --- |
| Z * |od |Pole k filtrování |
| Podmínka * |kde |Podmínku, která má vyhodnotit pro každou položku |

<br>

### <a name="output-details"></a>Podrobnosti výstupu
Toto jsou podrobnosti výstupu pro odpověď HTTP.

| Název vlastnosti | Typ dat | Popis |
| --- | --- | --- |
| Filtrované pole |pole |Pole, které obsahuje objekt pro každý filtrovaných výsledků |

## <a name="next-steps"></a>Další postup
Nyní, vyzkoušejte si platformu a [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Další dostupné konektory v logic apps můžete prozkoumat podle našich [rozhraní API seznamu](apis-list.md).

