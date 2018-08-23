---
title: Vytváření tabelárního modelu pomocí Azure Analysis Services – webové návrháře | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit tabulkový model služby Azure Analysis Services pomocí webový Návrhář na webu Azure portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dcfcfb24d2b47a8272c576856fc3accc547f354a
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42060754"
---
# <a name="create-a-model-in-azure-portal"></a>Vytvoření modelu na webu Azure portal

Funkce návrháře (preview) webové služby Azure Analysis Services na webu Azure portal poskytuje rychlý a snadný způsob, jak vytvořit a upravit tabulkové modely a dotazovat data modelu přímo v prohlížeči. 

Mějte na paměti, je webový Návrhář **ve verzi preview**. Funkce je omezena. Pro pokročilejší model vývoje a testování je nejvhodnější použít Visual Studio (SSDT) a SQL Server Management Studio (SSMS).

## <a name="before-you-begin"></a>Než začnete

- Váš server Azure Analysis Services musí být na úrovni Standard a Developer. Nové modely vytvořené pomocí webový Návrhář se DirectQuery, podporuje pouze tyto úrovně.
- Azure SQL Database, Azure SQL Data Warehouse nebo soubor Power BI Desktopu (.pbix) jako zdroj. Nové modely vytvořené z Power BI Desktopu soubory podpory Azure SQL Database a Azure SQL Data Warehouse.
- Účet serveru SQL a heslo pro připojení ke zdrojům dat Azure SQL Database nebo Azure SQL Data Warehouse.
- Musíte mít oprávnění správce serveru a vytvořte nový model. Upravit a dotazování pomocí návrháře modelu jsou požadována oprávnění správce databáze.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="to-create-a-new-tabular-model"></a>Chcete-li vytvořit nový tabulkový model

1. Na vašem serveru **přehled** > **webový Návrhář**, klikněte na tlačítko **otevřít**.

    ![Vytvoření modelu na webu Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. V **webový Návrhář** > **modely**, klikněte na tlačítko **+ přidat**.

    ![Vytvoření modelu na webu Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. V **nový model**, zadejte název modelu a pak vyberte zdroj dat.

    ![Dialogové okno Nový model na webu Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. V **připojit**, zadejte vlastnosti připojení. Uživatelské jméno a heslo musí být účet systému SQL Server.

     ![Připojit dialogového okna na webu Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. V **tabulek a zobrazení**, vyberte tabulky, které chcete zahrnout do modelu a pak klikněte na tlačítko **vytvořit**. Relace se automaticky vytvoří mezi tabulkami pomocí páru klíčů.

     ![Výběr tabulek a zobrazení](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

Nový model se zobrazí v prohlížeči. Odsud můžete:   

- Dotaz modelu na data přetažením polí do aplikace Návrhář dotazů a přidáním filtrů.
- Nové míry můžete vytvořte v tabulkách.
- Úprava metadat modelu pomocí editoru json.
- Otevřete model v sadě Visual Studio (SSDT), Power BI Desktopu nebo Excelu.

![Výběr tabulek a zobrazení](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> Když upravit metadata modelu nebo vytvořte nové míry v prohlížeči, které ukládáte tyto změny na váš model v Azure. Také při práci na modelu v sadě SSDT, Power BI Desktopu nebo Excelu, můžete získat model synchronizovaný.


## <a name="next-steps"></a>Další postup 
[Spravovat databázové role a uživatele](analysis-services-database-users.md)  
[Propojení s Excelem](analysis-services-connect-excel.md)  


