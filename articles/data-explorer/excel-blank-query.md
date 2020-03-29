---
title: Vizualizace dat pomocí dotazu Azure Data Explorer Kusto importovaného do Microsoft Excelu
description: V tomto článku se dozvíte, jak importovat dotaz Azure Data Explorer Kusto do Microsoft Excelu.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849084"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Vizualizace dat pomocí dotazu Azure Data Explorer Kusto importovaného do Microsoft Excelu

Azure Data Explorer poskytuje dvě možnosti pro připojení k datům v Excelu: použijte nativní konektor nebo importujte dotaz z Azure Data Explorer. Tento článek ukazuje, jak importovat dotaz z Průzkumníka dat Azure do Excelu pro vizualizaci dat. Přidejte dotaz Kusto jako zdroj dat aplikace Excel a proveďte další výpočty nebo vizualizace dat.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* E-mailový účet organizace, který je členem adresáře Azure Active Directory, takže se můžete připojit k [clusteru nápovědy Azure Data Exploreru](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>– nebo –</br>
* Vytvořte [testovací cluster a databázi](create-cluster-database-portal.md) a přihlaste se k [webové aplikaci Azure Data Explorer](https://dataexplorer.azure.com/).

## <a name="define-kusto-query-as-an-excel-data-source"></a>Definování dotazu Kusto jako zdroje dat aplikace Excel

1. Ve [webovém uživatelském rozhraní Průzkumníka dat Azure](https://dataexplorer.azure.com/clusters/help/databases/Samples)spusťte dotaz a zkontrolujte výsledky.

1. Vyberte kartu **Sdílet** a vyberte **Dotaz na Power BI**.

    ![Dotaz webového uživatelského uživatelského portálu pro Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Zobrazí se okno s následujícím oznámením:

    ![export dotazu do schránky](media/excel-blank-query/query-exported-to-clipboard.png)

1. Sem **můžete otevřít aplikaci Microsoft Excel**.

1. Na kartě **Data** vyberte **Nasávat data** > **z prázdných dotazů z jiných zdrojů** > **Blank Query**.

    ![Získání dat a výběr prázdného dotazu](media/excel-blank-query/get-data-blank-query.png)

1. Otevře se okno **Power Query Editor.** V okně vyberte **Rozšířený editor**.

    ![Okno editoru power query](media/excel-blank-query/power-query-editor.png)

1. V okně **Rozšířený editor** vložte dotaz, který jste exportovali, do schránky a vyberte **Hotovo**.

    ![Dotaz rozšířeného editoru](media/excel-blank-query/advanced-editor-query.png)    

1. Chcete-li se ověřit, vyberte **možnost Upravit pověření**.

    ![Upravit přihlašovací údaje](media/excel-blank-query/edit-credentials.png)

1. Vyberte **účet organizace** a **přihlásit se**. Dokončete proces přihlášení a pak vyberte **Připojit**.

    ![Úplné přihlášení](media/excel-blank-query/complete-sign-in.png)

    Opakováním předchozích kroků přidejte další dotazy. Dotazy můžete přejmenovat na smysluplnější názvy.

1. Chcete-li data dostat do Excelu, vyberte tlačítko **Zavřít & načíst.**

    ![Vybrat zavřít a načíst](media/excel-blank-query/close-and-load.png)

1. Nyní jsou vaše data v aplikaci Excel. Chcete-li aktualizovat dotaz, vyberte tlačítko **Aktualizovat.**

    ![Zobrazení dat v excelu](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>Další kroky

[Vizualizace dat pomocí konektoru Azure Data Explorer pro Excel](excel-connector.md)