---
title: Správa dat, listů a tabulek v Excelu online
description: Správa dat v listech a tabulkách v Excelu online pro firmy nebo Excel Online pro OneDrive pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 097db6683127b410e713be53e6de838cf7734ddc
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400719"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Správa dat v Excelu online pomocí Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a konektoru [Excel Online pro firmy](/connectors/excelonlinebusiness/) nebo [Excelu online pro konektor OneDrive](/connectors/excelonline/) můžete vytvářet automatizované úkoly a pracovní postupy na základě vašich dat v Excelu online pro firmy nebo OneDrive. Tento konektor poskytuje akce, které vám pomůžou pracovat s daty a spravovat tabulky, například:

* Vytvořte nové listy a tabulky.
* Získat a spravovat listy, tabulky a řádky.
* Přidejte jednotlivé řádky a klíčové sloupce.

Pak můžete použít výstupy z těchto akcí s akcemi pro jiné služby. Pokud například použijete akci, která vytvoří listy každý týden, můžete použít jinou akci, která odešle potvrzovací e-mail pomocí konektoru Office 365 Outlook.

Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> [Aplikace Excel Online pro firmy](/connectors/excelonlinebusiness/) a [Excel Online pro](/connectors/excelonline/) konektory OneDrive funguje s Azure Logic Apps a liší se od [konektoru aplikace Excel pro PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Pracovní nebo školní účet](https://www.office.com/) pro svůj pracovní účet nebo osobní účet Microsoft

  Data Excelu můžou existovat jako textový soubor s oddělovači (CSV) ve složce úložiště, například na OneDrivu. 
  Můžete také použít stejný soubor CSV s [konektorem s plochými soubory](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete získat přístup k datům Excelu online Tento konektor nabízí pouze akce, takže můžete spustit aplikaci logiky, vybrat samostatnou aktivační událost, například Trigger **opakování** .

## <a name="add-excel-action"></a>Přidat akci aplikace Excel

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky, pokud ještě není otevřená.

1. V části Trigger vyberte **Nový krok**.

1. Do vyhledávacího pole zadejte jako filtr slovo "Excel". V seznamu akce vyberte akci, kterou chcete.

   > [!NOTE]
   > Návrhář aplikace logiky nemůže načíst tabulky, které mají 100 nebo více sloupců. Pokud je to možné, omezte počet sloupců ve vybrané tabulce tak, aby mohl Návrhář načíst tabulku.

1. Pokud se zobrazí výzva, přihlaste se ke svému pracovnímu nebo školnímu účtu.

   Vaše přihlašovací údaje autorizují vaši aplikaci logiky, aby vytvořila připojení k Excelu online a měla přístup k vašim datům.

1. Pokračujte zadáním potřebných podrobností pro vybranou akci a vytvořením pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Podrobnosti o technických podrobnostech, jako jsou triggery, akce a omezení, jak je popsáno v souborech OpenAPI konektoru (dříve Swagger), najdete v těchto referenčních stránkách konektoru:

* [Excel Online pro firmy](/connectors/excelonlinebusiness/)
* [Excel Online pro OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
