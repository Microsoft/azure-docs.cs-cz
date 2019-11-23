---
title: Vizualizace dat pomocí dotazu Azure Průzkumník dat Kusto naimportovaného do aplikace Microsoft Excel
description: V tomto článku se dozvíte, jak importovat dotaz Azure Průzkumník dat Kusto do aplikace Microsoft Excel.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 55198e0c38c2922d69b68d9ce62e16ea25e9cc44
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173726"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Vizualizace dat pomocí dotazu Azure Průzkumník dat Kusto naimportovaného do aplikace Microsoft Excel

Azure Průzkumník dat poskytuje dvě možnosti pro připojení k datům v Excelu: použijte nativní konektor nebo naimportujte dotaz z Azure Průzkumník dat. V tomto článku se dozvíte, jak naimportovat dotaz z Azure Průzkumník dat do Excelu pro vizualizaci dat. Přidejte dotaz Kusto jako zdroj dat Excelu, abyste mohli provádět další výpočty nebo vizualizace dat.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* E-mailový účet organizace, který je členem Azure Active Directory, abyste se mohli připojit ke [clusteru azure Průzkumník dat help](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>nebo</br>
* Vytvořte [testovací cluster a databázi](create-cluster-database-portal.md) a přihlaste se k [aplikaci webového uživatelského rozhraní Azure Průzkumník dat](https://dataexplorer.azure.com/).

## <a name="define-kusto-query-as-an-excel-data-source"></a>Definování dotazu Kusto jako zdroje dat aplikace Excel

1. V [Azure Průzkumník dat webové uživatelské rozhraní](https://dataexplorer.azure.com/clusters/help/databases/Samples)spusťte dotaz a podívejte se na výsledky.

1. Vyberte kartu **sdílení** a vyberte **dotaz pro Power BI**.

    ![Dotaz webového uživatelského rozhraní na Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Zobrazí se okno s následujícím oznámením:

    ![exportovat dotaz do schránky](media/excel-blank-query/query-exported-to-clipboard.png)

1. Otevřete **aplikaci Microsoft Excel**.

1. Na kartě **data** vyberte **získat data** > **z jiných zdrojů** > **prázdný dotaz**.

    ![Získat data a vybrat prázdný dotaz](media/excel-blank-query/get-data-blank-query.png)

1. Otevře se okno **Editor Power Query** . V okně vyberte **Rozšířený editor**.

    ![Okno editoru Power Query](media/excel-blank-query/power-query-editor.png)

1. V okně **Rozšířený editor** vložte dotaz, který jste exportovali do schránky, a vyberte **Hotovo**.

    ![Rozšířený editor dotazu](media/excel-blank-query/advanced-editor-query.png)    

1. Pokud se chcete ověřit, vyberte **Upravit přihlašovací údaje**.

    ![Upravit přihlašovací údaje](media/excel-blank-query/edit-credentials.png)

1. Vyberte **účet organizace** a **Přihlaste se**. Dokončete proces přihlašování a pak vyberte **připojit**.

    ![Dokončit přihlášení](media/excel-blank-query/complete-sign-in.png)

    Zopakováním předchozích kroků přidejte další dotazy. Dotazy můžete přejmenovat na smysluplné názvy.

1. Kliknutím na tlačítko **zavřít & načíst** načtěte data do Excelu.

    ![Vybrat zavřít a načíst](media/excel-blank-query/close-and-load.png)

1. Data jsou teď v Excelu. Kliknutím na tlačítko **aktualizovat** aktualizujte dotaz.

    ![Zobrazit data v Excelu](media/excel-blank-query/data-in-excel.png)