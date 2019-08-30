---
title: Vizualizace dat pomocí konektoru Azure Průzkumník dat pro Microsoft Excel
description: V tomto článku se dozvíte, jak používat excelový konektor pro Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 18c6499afe450a0387b0d9f0f13ee4378e0a21a9
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173830"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Vizualizace dat pomocí konektoru Azure Průzkumník dat pro Excel

Azure Průzkumník dat poskytuje dvě možnosti pro připojení k datům v Excelu: použijte nativní konektor nebo naimportujte dotaz z Azure Průzkumník dat. V tomto článku se dozvíte, jak používat nativní konektor v aplikaci Excel a připojit se ke clusteru Azure Průzkumník dat k získání a vizualizaci dat.

Azure Průzkumník dat Excel Native Connector nabízí možnost Exportovat výsledky dotazu do Excelu. Můžete také přidat dotaz KQL jako zdroj dat aplikace Excel pro další výpočty nebo vizualizace.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Definujte dotaz Kusto jako zdroj dat aplikace Excel a načtěte data do Excelu.

1. Otevřete **aplikaci Microsoft Excel**.
1. Na kartě **data** vyberte z Azure**Průzkumník dat** **získat data** > **z Azure** > .

    ![Získat data z Azure Průzkumník dat](media/excel-connector/get-data-from-adx.png)

1. V okně **Azure Průzkumník dat (Kusto)** vyplňte následující pole a vyberte **OK**.

    ![Okno Azure Průzkumník dat (Kusto)](media/excel-connector/adx-connection-window.png)
    
    |Pole   |Popis |
    |---------|---------|
    |**Služby**   |   Název clusteru (povinné)      |    
    |**Database**     |    Název databáze      |    
    |**Název tabulky nebo dotaz na Azure Průzkumník dat**    |     Název tabulky nebo dotazu Azure Průzkumník dat    | 
    
    **Rozšířené možnosti:**

     |Pole   |Popis |
    |---------|---------|
    |**Omezit číslo záznamu výsledku dotazu**     |     Omezení počtu záznamů načtených do aplikace Excel  |    
    |**Omezit velikost dat výsledků dotazu (bajty)**    |    Omezení velikosti dat      |   
    |**Zakázat zkracování sady výsledků**    |         |      
    |**Další příkazy set (oddělené středníky)**    |    Přidat `set` příkazy, které se použijí na zdroj dat     |   

1.  V podokně **navigátor** přejděte do správné tabulky. V podokně náhledu tabulky vyberte transformovaná **data** , abyste mohli provádět změny dat, nebo vyberte **načíst** a načtěte ho do Excelu.

![Okno náhledu tabulky](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Pokud už je zadaný název **databáze** **nebo tabulky nebo dotaz Azure Průzkumník dat** , automaticky se otevře správné podokno náhledu tabulky. 

## <a name="analyze-and-visualize-data-in-excel"></a>Analýza a vizualizace dat v Excelu

Po načtení dat do Excelu a dostupnosti v excelovém listu můžete analyzovat, shrnout a vizualizovat data vytvořením relací a vizuálů. 

1.  Na kartě **návrh tabulky** vyberte **Souhrn s kontingenční tabulkou**. V okně **vytvořit kontingenční** tabulku vyberte příslušnou tabulku a vyberte **OK**.

    ![Vytvořit kontingenční tabulku](media/excel-connector/create-pivot-table.png)

1. V podokně **pole kontingenční tabulky** vyberte příslušné sloupce tabulky a vytvořte tak souhrnné tabulky. V následujícím příkladu jsou vybrány **ID události** a **State** .
    
    ![Výběr polí kontingenční tabulky](media/excel-connector/pivot-table-pick-fields.png)

1. Na kartě **analyzovat kontingenční tabulku** vyberte **kontingenční graf** pro vytvoření vizuálů založených na tabulce. 

    ![Kontingenční graf](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. V následujícím příkladu použijte **ID události**, **čas_spuštění**a **EventType** k zobrazení dalších informací o počasí událostí.

    ![Vizualizace dat](media/excel-connector/visualize-excel-data.png)

1. Vytvořením úplných řídicích panelů můžete monitorovat vaše data.

