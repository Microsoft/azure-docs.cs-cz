---
title: Připojení k Excelu online – Azure Logic Apps
description: Správa dat pomocí rozhraní REST API Excelu online a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: d3c6395f7fd66e1b89fc49536e228249bc4ed2a9
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859251"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Správa dat v Excelu online pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru Excelu online můžete vytvářet automatizované úkoly a pracovní postupy na základě vašich dat v Excelu online pro firmy nebo OneDrive. Tento konektor poskytuje akce, které vám pomůžou pracovat s daty a spravovat tabulky, například:

* Vytvořte nové listy a tabulky.
* Získat a spravovat listy, tabulky a řádky.
* Přidejte jednotlivé řádky a klíčové sloupce.

Pak můžete použít výstupy z těchto akcí s akcemi pro jiné služby. Pokud například použijete akci, která vytvoří listy každý týden, můžete použít jinou akci, která odešle potvrzovací e-mail pomocí konektoru Office 365 Outlook.

Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> [Aplikace Excel Online pro firmy](/connectors/excelonlinebusiness/) a [Excel Online pro](/connectors/excelonline/) konektory OneDrive funguje s Azure Logic Apps a liší se od [konektoru aplikace Excel pro PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet Office 365](https://www.office.com/) pro váš pracovní účet nebo osobní účet Microsoft

  Data Excelu můžou existovat jako textový soubor s oddělovači (CSV) ve složce úložiště, například na OneDrivu. 
  Můžete také použít stejný soubor CSV s konektorem s [plochými soubory](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete získat přístup k datům Excelu online Tento konektor nabízí pouze akce, takže můžete spustit aplikaci logiky, vybrat samostatnou aktivační událost, například Trigger **opakování** .

## <a name="add-excel-action"></a>Přidat akci aplikace Excel

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky, pokud ještě není otevřená.

1. V části Trigger vyberte **Nový krok**.

1. Do vyhledávacího pole zadejte jako filtr slovo "Excel". V seznamu akce vyberte akci, kterou chcete.

   > [!NOTE]
   > Návrhář aplikace logiky nemůže načíst tabulky, které mají 100 nebo více sloupců. Pokud je to možné, omezte počet sloupců ve vybrané tabulce tak, aby mohl Návrhář načíst tabulku.

1. Pokud se zobrazí výzva, přihlaste se ke svému účtu Office 365.

   Vaše přihlašovací údaje autorizují vaši aplikaci logiky, aby vytvořila připojení k Excelu online a měla přístup k vašim datům.

1. Pokračujte zadáním potřebných podrobností pro vybranou akci a vytvořením pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Podrobnosti o technických podrobnostech, jako jsou triggery, akce a omezení, jak je popsáno v souborech OpenAPI konektoru (dříve Swagger), najdete v těchto referenčních stránkách konektoru:

* [Excel Online pro firmy](/connectors/excelonlinebusiness/)
* [Excel Online pro OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
