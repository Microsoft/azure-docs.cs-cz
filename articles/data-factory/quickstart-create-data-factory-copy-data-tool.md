---
title: Kopírování dat pomocí nástroje pro kopírování dat Azure | Dokumentace Microsoftu
description: Vytvořte datovou továrnu Azure a pak pomocí nástroje Kopírování dat zkopírujte data z jednoho umístění v úložišti objektů blob v Azure do jiného.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: a4e41408a3af2e6bb68c14f2e34bf1141bf349c1
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017803"
---
# <a name="use-the-copy-data-tool-to-copy-data"></a>Kopírování dat pomocí nástroje pro kopírování dat 
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuální verze](quickstart-create-data-factory-copy-data-tool.md)

V tomto rychlém startu použijete Azure Portal k vytvoření datové továrny. Pak pomocí nástroje pro kopírování dat vytvoříte kanál, který kopíruje data z jedné složky v úložišti objektů blob v Azure do jiné. 

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](data-factory-introduction.md), než s tímto rychlým startem začnete. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Vyberte **Nový** v nabídce vlevo, vyberte **Data a analýzy** a pak vyberte **Datová továrna**. 
   
   ![Výběr datové továrny v podokně Nový](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
1. Na stránce **Nová datová továrna** jako **Název** zadejte **ADFTutorialDataFactory**. 
      
   ![Stránka Nová datová továrna](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   Název datové továrny Azure musí být *globálně jedinečný*. Pokud se zobrazí následující chyba, změňte název datové továrny (třeba na **&lt;váš_název_&gt;ADFTutorialDataFactory**) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
   ![Chyba: název není k dispozici](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
1. Jako **Předplatné** vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit. 
1. Pro položku **Skupina prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a ze seznamu vyberte existující skupinu prostředků. 
   - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
   Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
1. Jako **Verzi** vyberte **V2**.
1. Jako **Umístění** vyberte umístění datové továrny. 

   Seznam obsahuje jenom umístění podporovaná službou Data Factory, do kterých se budou ukládat vaše metadata Azure Data Factory. Upozorňujeme, že související úložiště dat (třeba Azure Storage a Azure SQL Database) a výpočetní prostředí (jako Azure HDInsight) používaná službou Data Factory můžou běžet v jiných oblastech.

1. Vyberte **Vytvořit**.
1. Po vytvoření se zobrazí stránka **Datová továrna**. Kliknutím na dlaždici **Vytvořit a monitorovat** spusťte na samostatné kartě aplikaci uživatelského rozhraní služby Azure Data Factory.
   
   ![Domovská stránka datové továrny s dlaždici Author & Monitor (Vytvořit a sledovat)](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Spuštění nástroje pro kopírování dat

1. Na stránce **Let‘s get started** (Začínáme) vyberte dlaždici **Copy Data** (Kopírovat data). Spustí se nástroj pro kopírování dat. 

   ![Dlaždice Copy Data (Kopírovat data)](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)

1. Na stránce **Properties** (Vlastnosti) nástroje Kopírování dat můžete zadat název kanálu a jeho popis. Pak vyberte **Next** (Další). 

   ![Stránka Properties (Vlastnosti)](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. Na stránce **Source data store** (Zdrojové úložiště dat) proveďte následující kroky:

    a. Kliknutím na **+ Create new connection** (+ Vytvořit nové připojení) přidejte připojení.

    ![Stránka Source data store (Zdrojové úložiště dat)](./media/quickstart-create-data-factory-copy-data-tool/new-source-linked-service.png)

    b. Z galerie vyberte **Azure Blob Storage** a pak vyberte **Next** (Další).

    ![Výběr služby Blob Storage z galerie](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. Na stránce **Specify the Azure Blob storage account** (Zadejte účet Azure Blob Storage) vyberte ze seznamu **Storage account name** (Název účtu úložiště) účet úložiště a pak vyberte **Finish** (Dokončit). 

   ![Konfigurace účtu služby Azure Blob Storage](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

   d. Vyberte nově vytvořenou propojenou službu jako zdroj a pak klikněte na **Next** (Další).

   ![Výběr zdrojové propojené služby](./media/quickstart-create-data-factory-copy-data-tool/select-source-linked-service.png)


1. Na stránce **Choose the input file or folder** (Zvolit vstupní soubor nebo složku) proveďte následující kroky:

   a. Klikněte na **Browse** (Procházet), přejděte do složky **adftutorial/input**, vyberte soubor **emp.txt** a pak klikněte na **Choose** (Zvolit). 

   ![Stránka Choose the input file or folder (Zvolit vstupní soubor nebo složku)](./media/quickstart-create-data-factory-copy-data-tool/configure-source-path.png)

   d. Zaškrtněte možnost **Binary copy** (Binární kopie), aby se soubor zkopíroval tak, jak je, a pak vyberte **Next** (Další). 

   ![Stránka Choose the input file or folder (Zvolit vstupní soubor nebo složku)](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. Na stránce **Destination data store** (Cílové úložiště dat) vyberte propojenou službu **Azure Blob Storage**, kterou jste právě vytvořili, a pak vyberte **Next** (Další). 

   ![Stránka Destination data store (Cílové úložiště dat)](./media/quickstart-create-data-factory-copy-data-tool/select-sink-linked-service.png)

1. Na stránce **Choose the output file or folder** (Zvolit výstupní soubor nebo složku) jako cestu ke složce zadejte **adftutorial/output** a pak vyberte **Next** (Další). 

   ![Stránka Choose the output file or folder (Zvolit výstupní soubor nebo složku)](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. Na stránce **Settings** (Nastavení) vyberte **Next** (Další), aby se použila výchozí konfigurace. 

1. Na stránce **Summary** (Souhrn) zkontrolujte všechna nastavení a vyberte **Next** (Další). 

    ![Stránka Summary (Souhrn)](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)

1. Na stránce **Deployment complete** (Nasazení dokončeno) vyberte **Monitor** (Sledovat) a začněte sledovat vytvořený kanál. 

    ![Stránka Deployment complete (Nasazení dokončeno)](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. Aplikace se přepne na kartu **Monitorování**. Na této kartě se zobrazí stav kanálu. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. 
    
    ![Monitorování spuštění kanálu](./media/quickstart-create-data-factory-copy-data-tool/pipeline-monitoring.png)

1. Klikněte na odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. Kanál má pouze jednu aktivitu typu **Kopírování**. 

    ![Monitorování spuštění aktivit](./media/quickstart-create-data-factory-copy-data-tool/activity-monitoring.png)
    
1. Pokud chcete zobrazit podrobnosti o operaci kopírování, vyberte odkaz **Podrobnosti** (obrázek brýlí) ve sloupci **Akce**. Podrobnosti o vlastnostech najdete v tématu [Přehled aktivity kopírování](copy-activity-overview.md).

    ![Podrobnosti o aktivitě kopírování](./media/quickstart-create-data-factory-copy-data-tool/activity-execution-details.png)

1. Ověřte, že se ve složce **output** kontejneru **adftutorial** vytvořil soubor **emp.txt**. Pokud výstupní složka neexistuje, služba Data Factory ji automaticky vytvoří. 

1. Nad kartou **Monitorování** v levém panelu přepněte na kartu **Vytvořit**, kde můžete upravovat propojené služby, datové sady a kanály. Další informace o jejich úpravách v uživatelském rozhraní služby Data Factory najdete v tématu [Vytvoření datové továrny pomocí webu Azure Portal](quickstart-create-data-factory-portal.md).

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob v Azure. Pokud se chcete naučit používat službu Data Factory ve více scénářích, projděte si příslušné [kurzy](tutorial-copy-data-portal.md). 
