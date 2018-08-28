---
title: Připojení k Excelu Online – Azure Logic Apps | Dokumentace Microsoftu
description: Správa dat pomocí aplikace Excel Online rozhraní REST API a Azure Logic Apps
ms.service: logic-apps
services: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: ceef6c5f32372bb69f6ce789e755bc540cb12ba1
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051945"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Spravovat data aplikace Excel Online s Azure Logic Apps

S Azure Logic Apps a konektoru Excelu Online můžete vytvořit automatizovaných úloh a pracovní postupy založené na vašich dat v Excelu Online pro firmy nebo OneDrive. Tento konektor zajišťuje akce, které vám pomohou pracovat s vašimi daty a spravovat tabulky, například: 

* Vytvoření nových listů a tabulky.
* Získání a správě listy, tabulky a řádky.
* Přidáte jednoho řádky a sloupce klíče.

Pak můžete výstup z těchto akcí s akcemi pro ostatní služby. Například pokud použijete akci, která se vytvoří každý týden listy, můžete použít jinou akci, která odešle e-mail s potvrzením pomocí konektoru Office 365 Outlook.

Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> [Excelu Online pro firmy](/connectors/excelonlinebusiness/) a [Excelu Online, Onedrivu](/connectors/excelonline/) konektory fungují s Azure Logic Apps a se liší od [konektor aplikace Excel pro PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* [Účet Office 365](https://www.office.com/) pro váš pracovní účet nebo osobní účet Microsoft 

  Data aplikace Excel může existovat jako soubor hodnot oddělených čárkami (CSV) ve složce úložiště, třeba ve Onedrivu. 
  Můžete také použít stejný soubor CSV s [plochého souboru konektoru](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přistupovat k datům v Excelu Online. Tento konektor zajišťuje jenom akce, takže ke spuštění aplikace logiky vyberte samostatné aktivační událost, například, **opakování** aktivační události.

## <a name="add-excel-action"></a>Přidání akce aplikace Excel

1. V [webu Azure portal](https://portal.azure.com), otevření aplikace logiky v návrháři aplikace logiky, pokud není otevřen.

1. Pod triggerem zvolte **nový krok**.

1. Do vyhledávacího pole zadejte jako filtr "excel". V seznamu akcí vyberte požadovanou akci.

1. Pokud se zobrazí výzva k přihlášení ke svému účtu Office 365, zvolte **přihlášení**. 

   Vaše přihlašovací údaje autorizaci aplikace logiky k vytvoření připojení k Excelu Online a přístup k vašim datům.

1. Pokračujte v poskytování nezbytné podrobnosti pro vybranou akci a vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako je například akcí a omezení, popsaného souborům Swagger konektory najdete v těchto stránky s referenčními informacemi konektoru:

* [Excel Online pro firmy](/connectors/excelonlinebusiness/) 
* [Excel Online pro OneDrive](/connectors/excelonline/) 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
