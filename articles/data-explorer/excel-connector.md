---
title: Vizualizace dat pomocí konektoru Azure Data Explorer pro Microsoft Excel
description: V tomto článku se dozvíte, jak používat konektor Excelu pro Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849050"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Vizualizace dat pomocí konektoru Azure Data Explorer pro Excel

Azure Data Explorer poskytuje dvě možnosti pro připojení k datům v Excelu: použijte nativní konektor nebo importujte dotaz z Azure Data Explorer. Tento článek ukazuje, jak používat nativní konektor v Excelu a připojit se ke clusteru Azure Data Explorer získat a vizualizovat data.

Nativní konektor Excelu průzkumníka Dat Azure nabízí možnost exportovat výsledky dotazu do Excelu. Můžete také přidat dotaz KQL jako zdroj dat aplikace Excel pro další výpočty nebo vizualizace.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Definování dotazu Kusto jako zdroje dat aplikace Excel a načtení dat do Excelu

1. Sem **můžete otevřít aplikaci Microsoft Excel**.
1. Na kartě **Data** vyberte **Získat data** > **z Azure** > **z Azure Data Exploreru**.

    ![Získání dat z Průzkumníka dat Azure](media/excel-connector/get-data-from-adx.png)

1. V okně **Průzkumníka dat Azure (Kusto)** vyplňte následující pole a vyberte **OK**.

    ![Okno Průzkumníka dat Azure (Kusto)](media/excel-connector/adx-connection-window.png)
    
    |Pole   |Popis |
    |---------|---------|
    |**Clusteru**   |   Název clusteru (povinný)      |    
    |**Databáze**     |    Název databáze      |    
    |**Název tabulky nebo dotaz Průzkumníka dat Azure**    |     Název tabulky nebo dotazu Průzkumníka dat Azure    | 
    
    **Rozšířené možnosti:**

     |Pole   |Popis |
    |---------|---------|
    |**Omezit číslo záznamu výsledku dotazu**     |     Omezení počtu záznamů načtených do aplikace Excel  |    
    |**Omezit velikost dat výsledků dotazu (bajty)**    |    Omezení velikosti dat      |   
    |**Zakázat zkrácení sady výsledků**    |         |      
    |**Příkazy další sady (oddělené středníky)**    |    Přidání `set` příkazů, které se mají použít ke zdroji dat     |   

1.  V podokně **Navigátor** přejděte na správnou tabulku. V podokně náhledu tabulky vyberte **Transformovat data,** chcete-li provést změny dat, nebo vyberte **Načíst,** chcete-li je načíst do Excelu.

![Okno náhledu tabulky](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Pokud jsou již **zadány název databáze** nebo nebo **tabulky nebo dotaz Průzkumníka dat Azure,** otevře se automaticky správné podokno náhledu tabulky. 

## <a name="analyze-and-visualize-data-in-excel"></a>Analýza a vizualizace dat v Excelu

Jakmile se data načtou do excelu a budou k dispozici v excelovém listu, můžete data analyzovat, sumarizovat a vizualizovat vytvořením relací a vizuálů. 

1.  Na kartě **Návrh tabulky** vyberte **Sumarizovat pomocí kontingenční tabulky**. V okně **Vytvořit kontingenční tabulku** vyberte příslušnou tabulku a vyberte **OK**.

    ![Vytvoření kontingenční tabulky](media/excel-connector/create-pivot-table.png)

1. V podokně **Pole kontingenční tabulky** vyberte příslušné sloupce tabulky a vytvořte souhrnné tabulky. V příkladu níže **eventid** a **stav** jsou vybrány.
    
    ![Výběr polí kontingenční tabulky](media/excel-connector/pivot-table-pick-fields.png)

1. Na kartě **Analýza kontingenční tabulky** vyberte **Kontingenční graf,** abyste vytvořili vizuály založené na tabulce. 

    ![Kontingenční graf](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. V níže uvedeném příkladu použijte **ID události**, **StartTime**a **EventType** k zobrazení dalších informací o událostech počasí.

    ![Vizualizace dat](media/excel-connector/visualize-excel-data.png)

1. Vytvořte úplné řídicí panely pro sledování dat.

## <a name="next-steps"></a>Další kroky

[Vizualizace dat pomocí dotazu Azure Data Explorer Kusto importovaného do Microsoft Excelu](excel-blank-query.md)