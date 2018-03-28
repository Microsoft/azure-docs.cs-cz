---
title: Kopírování dat pomocí nástroje pro kopírování dat Azure | Dokumentace Microsoftu
description: Vytvořte datovou továrnu Azure a pak pomocí nástroje Kopírování dat zkopírujte data z jednoho umístění v úložišti objektů blob v Azure do jiného.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: d09422f31a2dda5e14fb891fa07f65fdcceb72c1
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="use-the-copy-data-tool-to-copy-data"></a>Kopírování dat pomocí nástroje pro kopírování dat 
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Verze 1 – GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Verze 2 – Preview](quickstart-create-data-factory-copy-data-tool.md)

V tomto rychlém startu použijete Azure Portal k vytvoření datové továrny. Pak pomocí nástroje pro kopírování dat vytvoříte kanál, který kopíruje data z jedné složky v úložišti objektů blob v Azure do jiné. 

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](data-factory-introduction.md), než s tímto rychlým startem začnete. 
>
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby, která je všeobecně dostupná (GA), prostudujte si [úvod do služby Data Factory verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Vyberte **Nový** v nabídce vlevo, vyberte **Data a analýzy** a pak vyberte **Datová továrna**. 
   
   ![Výběr datové továrny v podokně Nový](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
2. Na stránce **Nová datová továrna** jako **Název** zadejte **ADFTutorialDataFactory**. 
      
   ![Stránka Nová datová továrna](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se zobrazí následující chyba, změňte název datové továrny (třeba na **&lt;váš_název_&gt;ADFTutorialDataFactory**) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
   ![Chyba: název není k dispozici](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Jako **Předplatné** vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit. 
4. Pro položku **Skupina prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a ze seznamu vyberte existující skupinu prostředků. 
   - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
   Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
4. Jako **Verze** vyberte **V2 (Preview)**.
5. V poli **Umístění** vyberte umístění pro datovou továrnu. 

   V seznamu se zobrazují pouze podporovaná umístění. Úložiště dat (např. Azure Storage nebo Azure SQL Database) a výpočetní prostředí (např. Azure HDInsight) používaná službou Data Factory můžou být v jiných umístěních nebo oblastech.

6. Zaškrtněte **Připnout na řídicí panel**.     
7. Vyberte **Vytvořit**.
8. Na řídicím panelu se zobrazí následující dlaždice se stavem **Nasazování datové továrny**: 

    ![Dlaždice Nasazování datové továrny](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**. Kliknutím na dlaždici **Vytvořit a monitorovat** spusťte na samostatné kartě aplikaci uživatelského rozhraní služby Azure Data Factory.
   
   ![Domovská stránka datové továrny s dlaždici Author & Monitor (Vytvořit a sledovat)](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Spuštění nástroje pro kopírování dat

1. Na stránce **Let‘s get started** (Začínáme) vyberte dlaždici **Copy Data** (Kopírovat data). Spustí se nástroj pro kopírování dat. 

   ![Dlaždice Copy Data (Kopírovat data)](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)
2. Na stránce **Properties** (Vlastnosti) nástroje pro kopírování dat vyberte **Next** (Další). Na této stránce můžete zadat název a popis kanálu. 

   ![Stránka Properties (Vlastnosti)](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. Na stránce **Source data store** (Zdrojové úložiště dat) vyberte **Azure Blob Storage** a pak vyberte **Next** (Další).

   ![Stránka Source data store (Zdrojové úložiště dat)](./media/quickstart-create-data-factory-copy-data-tool/source-data-store-page.png)
4. Na stránce **Specify the Azure Blob storage account** (Zadat účet služby Azure Blob Storage) vyberte ze seznamu **Storage account name** (Název účtu úložiště) svůj účet úložiště a pak vyberte **Next** (Další). 

   ![Stránka Specify the Azure Blob storage account (Zadat účet služby Azure Blob Storage)](./media/quickstart-create-data-factory-copy-data-tool/specify-blob-storage-account.png)
5. Na stránce **Choose the input file or folder** (Zvolit vstupní soubor nebo složku) proveďte následující kroky:

   a. Přejděte do složky **adftutorial/input**.

   b. Vyberte soubor **emp.txt**.

   c. Vyberte **Choose** (Zvolit). Tento krok můžete přeskočit dvojím kliknutím na soubor **emp.txt**.

   d. Vyberte **Next** (Další). 

   ![Stránka Choose the input file or folder (Zvolit vstupní soubor nebo složku)](./media/quickstart-create-data-factory-copy-data-tool/choose-input-file-folder.png)
6. Na stránce **File format settings** (Nastavení formátu souboru) si všimněte, že nástroj automaticky rozpoznává oddělovače sloupců a řádků, a vyberte **Next** (Další). Na této stránce si můžete také prohlédnout náhled dat a schémata vstupních dat. 

   ![Stránka File format settings (Nastavení formátu souboru)](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-page.png)
7. Na stránce **Destination data store** (Cílové úložiště dat) vyberte **Azure Blob Storage** a pak vyberte **Next** (Další). 

   ![Stránka Destination data store (Cílové úložiště dat)](./media/quickstart-create-data-factory-copy-data-tool/destination-data-store-page.png)    
8. Na stránce **Specify the Azure Blob storage account** (Zadat účet služby Azure Blob Storage) vyberte svůj účet služby Azure Blob Storage a pak vyberte **Next** (Další). 

   ![Stránka Specify the Azure Blob storage account (Zadat účet služby Azure Blob Storage)](./media/quickstart-create-data-factory-copy-data-tool/specify-sink-blob-storage-account.png)
9. Na stránce **Choose the output file or folder** (Zvolit výstupní soubor nebo složku) proveďte následující kroky: 

   a. Jako cestu ke složce zadejte **adftutorial/output**.

   b. Jako název souboru zadejte **emp.txt**.

   c. Vyberte **Next** (Další). 

   ![Stránka Choose the output file or folder (Zvolit výstupní soubor nebo složku)](./media/quickstart-create-data-factory-copy-data-tool/choose-output-file-folder.png) 
10. Na stránce **File format settings** (Nastavení formátu souboru) vyberte **Next** (Další). 

    ![Stránka File format settings (Nastavení formátu souboru)](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-output-page.png)
11. Na stránce **Settings** (Nastavení) vyberte **Next** (Další). 

    ![Stránka Settings (Nastavení)](./media/quickstart-create-data-factory-copy-data-tool/advanced-settings-page.png)
12. Na stránce **Summary** (Souhrn) zkontrolujte všechna nastavení a vyberte **Next** (Další). 

    ![Stránka Summary (Souhrn)](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)
13. Na stránce **Deployment complete** (Nasazení dokončeno) vyberte **Monitor** (Sledovat) a začněte sledovat vytvořený kanál. 

    ![Stránka Deployment complete (Nasazení dokončeno)](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)
14. Aplikace se přepne na kartu **Monitorování**. Na této kartě se zobrazí stav kanálu. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. 
    
    ![Karta pro sledování spuštění kanálu s tlačítkem Aktualizovat](./media/quickstart-create-data-factory-copy-data-tool/monitor-pipeline-runs-page.png)
15. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Kanál má pouze jednu aktivitu typu **Kopírování**. 

    ![Seznam spuštění aktivit](./media/quickstart-create-data-factory-copy-data-tool/activity-runs.png)
16. Pokud chcete zobrazit podrobnosti o operaci kopírování, vyberte odkaz **Podrobnosti** (obrázek brýlí) ve sloupci **Akce**. Podrobnosti o vlastnostech najdete v tématu [Přehled aktivity kopírování](copy-activity-overview.md). 

    ![Podrobnosti o aktivitě kopírování](./media/quickstart-create-data-factory-copy-data-tool/copy-operation-details.png)
17. Ověřte, že se ve složce **output** kontejneru **adftutorial** vytvořil soubor **emp.txt**. Pokud výstupní složka neexistuje, služba Data Factory ji automaticky vytvoří. 
18. Přepněte na kartu **Upravit**, kde můžete upravit propojené služby, datové sady a kanály. Další informace o jejich úpravách v uživatelském rozhraní služby Data Factory najdete v tématu [Vytvoření datové továrny pomocí webu Azure Portal](quickstart-create-data-factory-portal.md).

    ![Karta Upravit](./media/quickstart-create-data-factory-copy-data-tool/edit-tab.png)

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob v Azure. Pokud se chcete naučit používat službu Data Factory ve více scénářích, projděte si příslušné [kurzy](tutorial-copy-data-portal.md). 