---
title: Vytvořit tabulkový model pomocí návrháře Azure Analysis Services – webové | Microsoft Docs
description: Naučte se vytvořit Azure Analysis Services tabulkový model pomocí návrháře webové na portálu Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4b1d4cffc3571297f2b74674156cb7f3bad7c2c8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-model-in-azure-portal"></a>Vytvoření modelu na portálu Azure

Funkce Azure Analysis Services webová návrháře (preview) na portálu Azure poskytuje rychlý a snadný způsob, jak vytvořit a upravit tabulkové modely a dotazování modelu dat vpravo v prohlížeči. 

Mějte na paměti, Návrhář webu je **preview**. Funkce je omezena. Pro pokročilejší model vývoje a testování je nejvhodnější použít Visual Studio (SSDT) a SQL Server Management Studio (SSMS).

## <a name="before-you-begin"></a>Než začnete

- Serveru Azure Analysis Services na úroveň Standard nebo Developer. Nové modelů vytvořených pomocí webové návrháře jsou DirectQuery, podporuje pouze tyto vrstvy.
- Databáze SQL Azure, Azure SQL Data Warehouse nebo souboru Power BI Desktop (.pbix) jako zdroj dat. Nové modely vytvořené pomocí Power BI Desktop soubory podpory Azure SQL Database a Azure SQL Data Warehouse.
- Účet systému SQL Server a heslo pro připojení ke zdrojům dat databáze SQL Azure nebo Azure SQL Data Warehouse.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="to-create-a-new-tabular-model"></a>Chcete-li vytvořit nový tabulkový model.

1. Na vašem serveru **přehled** > **Návrhář**, klikněte na tlačítko **otevřete**.

    ![Vytvoření modelu na portálu Azure](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. V **Návrhář** > **modely**, klikněte na tlačítko **+ přidat**.

    ![Vytvoření modelu na portálu Azure](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. V **nový model**, zadejte název modelu a potom vyberte zdroj dat.

    ![Dialogové okno Nový model na portálu Azure](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. V **Connect**, zadejte vlastnosti připojení. Uživatelské jméno a heslo musí být účet systému SQL Server.

     ![Připojit dialogové okno na portálu Azure](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. V **tabulek a zobrazení**, vyberte tabulky, které chcete zahrnout do modelu a potom klikněte na **vytvořit**. Jsou automaticky vytvářet relace mezi tabulkami s pár klíčů.

     ![Vyberte tabulky a zobrazení](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

Nový model se zobrazí v prohlížeči. Tady můžete:   

- Dotaz na data modelu přetažením polí do Návrháře dotazů a přidáte filtry.
- Vytvořte nové míry v tabulkách.
- Úpravy metadat modelu s použitím json editor.
- Visual Studio (SSDT), Power BI Desktop nebo aplikace Excel otevřete model.

![Vyberte tabulky a zobrazení](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> Když budete upravovat metadata modelu nebo vytvářet nové míry v prohlížeči, ukládáte tyto změny modelu v Azure. Také při práci na modelu SSDT, Power BI Desktop nebo aplikace Excel, můžete získat váš model synchronizován.


## <a name="next-steps"></a>Další postup 
[Spravovat role databáze a uživatele](analysis-services-database-users.md)  
[Propojení s Excelem](analysis-services-connect-excel.md)  


