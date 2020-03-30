---
title: Správa dat, listů a tabulek v Excelu Online
description: Správa dat v listech a tabulkách v Excelu Online pro firmy nebo Excelu Online na OneDrivu pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: b1df1219590aa94331a4404b1315eb7231c59699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445885"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Správa dat Excelu Online pomocí aplikací Azure Logic Apps

S [Logic Apps Azure](../logic-apps/logic-apps-overview.md) a konektorem [Excelonline pro firmy](/connectors/excelonlinebusiness/) nebo [konektorem Excelu Online pro OneDrive](/connectors/excelonline/) můžete vytvářet automatizované úkoly a pracovní postupy na základě vašich dat v Excelu Online pro firmy nebo na OneDrivu. Tento konektor obsahuje akce, které vám pomohou pracovat s daty a spravovat tabulky, například:

* Vytvořte nové listy a tabulky.
* Získejte a spravujte listy, tabulky a řádky.
* Přidejte jednotlivé řádky a klíčové sloupce.

Výstupy z těchto akcí pak můžete použít s akcemi pro jiné služby. Pokud například použijete akci, která každý týden vytváří listy, můžete použít jinou akci, která odešle potvrzovací e-mail pomocí konektoru Outlooku Office 365.

Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Konektory [Excelu Online pro firmy](/connectors/excelonlinebusiness/) a [Excelu Online pro OneDrive](/connectors/excelonline/) fungují s Logic Apps Azure a liší se od [excelového konektoru pro PowerApps.](/connectors/excel/)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet Office 365](https://www.office.com/) pro váš pracovní nebo osobní účet Microsoft

  Data aplikace Excel mohou existovat jako soubor csv (oddělený čárkami) ve složce úložiště, například na OneDrivu. 
  Můžete také použít stejný soubor CSV s [plochým konektorem](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete získat přístup k datům Excelu Online. Tento konektor poskytuje pouze akce, takže ke spuštění aplikace logiky vyberte samostatnou aktivační událost, například aktivační událost **opakování.**

## <a name="add-excel-action"></a>Přidat akci Excelu

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. Pod aktivační událostí zvolte **Nový krok**.

1. Do vyhledávacího pole zadejte jako filtr "excel". V seznamu akcí vyberte požadovanou akci.

   > [!NOTE]
   > Návrhář aplikace logiky nelze načíst tabulky, které mají 100 nebo více sloupců. Pokud je to možné, snižte počet sloupců ve vybrané tabulce, aby návrhář mohl tabulku načíst.

1. Pokud se zobrazí výzva, přihlaste se ke svému účtu Office 365.

   Vaše přihlašovací údaje autorizují vaši aplikaci logiky k vytvoření připojení k Excelu Online a přístupu k vašim datům.

1. Pokračujte v poskytování potřebných podrobností pro vybranou akci a vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou aktivační události, akce a omezení, jak je popsáno v souborech OpenAPI (dříve Swagger) konektoru, naleznete v těchto referenčních stránkách konektoru:

* [Excel Online pro firmy](/connectors/excelonlinebusiness/)
* [Excel Online pro OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
