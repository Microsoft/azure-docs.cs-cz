---
title: "Přidejte konektor Azure SQL Database ve vašich Logic Apps | Microsoft Docs"
description: "Přehled konektoru Azure SQL Database s parametry rozhraní REST API"
services: 
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 4313ead0c31ab2e72238701d58dc2f321f116fa6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-azure-sql-database-connector"></a>Začínáme s Azure SQL Database konektoru
Pomocí konektoru Azure SQL Database, vytvořte pracovní postupy pro vaši organizaci, které spravovat data v tabulkách. 

S databází SQL můžete:

* Vytvořte pracovní postup přidáním nového zákazníka k databázi zákazníků nebo aktualizaci pořadí, v databázi objednávky.
* Pomocí akcí pro získání řádku dat, vložit nový řádek a i odstranění. Například v aplikaci Dynamics CRM Online (aktivační událost) je vytvořen záznam, pak vložte řádek v Azure SQL Database (akce). 

Tento článek ukazuje, jak k používání konektoru databáze SQL v aplikaci logiky a také uvádí seznam akcí.

Další informace o Logic Apps najdete v tématu [co jsou logic apps](../logic-apps/logic-apps-overview.md) a [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-azure-sql-database"></a>Připojení k databázi Azure SQL
Než se aplikace logiky k jakékoli služby, nejprve vytvořit *připojení* ke službě. Připojení poskytuje připojení mezi aplikace logiky a jiné služby. Například pro připojení k databázi SQL, je nejprve vytvořit databázi SQL *připojení*. Chcete-li vytvořit připojení, zadejte přihlašovacích údajů, které standardně používáte k přístupu ke službě, ke kterému se připojujete. Ano v databázi SQL, zadejte přihlašovací údaje databáze SQL k vytvoření připojení. 

#### <a name="create-the-connection"></a>Vytvoření připojení
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>Použít aktivační událost
Tento konektor nemá žádné aktivační události. Spusťte aplikaci logiky, jako jsou aktivační událost opakování, aktivační události Webhooku protokolu HTTP, aktivační události, které jsou k dispozici ostatní konektory a další pomocí jiných aktivační události. [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) poskytuje příklad.

## <a name="use-an-action"></a>Použít akci.
Akce je operace prováděné definované v aplikaci logiky pracovního postupu. [Další informace o akcích](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Vyberte znaménko plus. Zobrazí několik možností: **přidat akci**, **přidat podmínku**, nebo jeden z **Další** možnosti.
   
    ![](./media/connectors-create-api-sqlazure/add-action.png)
2. Zvolte **přidat akci**.
3. Do textového pole zadejte "sql" získat seznam všechny dostupné akce.
   
    ![](./media/connectors-create-api-sqlazure/sql-1.png) 
4. V tomto příkladu vyberte **systému SQL Server - Get řádek**. Pokud připojení již existuje, pak vyberte **název tabulky** z rozevíracího seznamu a zadejte **ID řádku** chcete vrátit.
   
    ![](./media/connectors-create-api-sqlazure/sample-table.png)
   
    Pokud se zobrazí výzva pro informace o připojení, zadejte podrobnosti k vytvoření připojení. [Vytvoření připojení](connectors-create-api-sqlazure.md#create-the-connection) v tomto článku popisuje tyto vlastnosti. 
   
   > [!NOTE]
   > V tomto příkladu jsme vrátí řádek z tabulky. Chcete-li zobrazit data na tomto řádku, přidejte další akci, která vytvoří soubor pomocí pole z tabulky. Například přidejte OneDrive akci, která používá pole FirstName a LastName k vytvoření nového souboru v účtu úložiště cloudu. 
   > 
   > 
5. **Uložit** změny (levém horním rohu panelu nástrojů). Aplikace logiky je uloženo a automaticky povolí.

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/sql/). 

## <a name="next-steps"></a>Další postup
[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Prozkoumejte dalších dostupných konektorů v Logic Apps v [rozhraní API seznamu](apis-list.md).

