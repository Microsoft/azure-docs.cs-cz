---
title: Kopírování dat pomocí nástroje Azure Kopírování dat
description: Vytvořte Azure Data Factory a pak pomocí nástroje Kopírování dat zkopírujte data z jednoho umístění v úložišti objektů BLOB v Azure do jiného umístění.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 282917a6bbf7edb962a87ad87810adde56206d97
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564714"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Rychlý Start: použití nástroje Kopírování dat ke kopírování dat

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuální verze](quickstart-create-data-factory-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto rychlém startu použijete Azure Portal k vytvoření datové továrny. Pak pomocí nástroje pro kopírování dat vytvoříte kanál, který kopíruje data z jedné složky v úložišti objektů blob v Azure do jiné. 

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md), než s tímto rychlým startem začnete. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. Přejděte na web [Azure Portal](https://portal.azure.com). 
1. V nabídce Azure Portal vyberte vytvořit data Factory **pro**  >  **integraci** prostředků  >  **Data Factory** :

    ![Vytvoření nové datové továrny](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Na stránce **Nová datová továrna** jako **Název** zadejte **ADFTutorialDataFactory**. 
 
   Název Azure Data Factory musí být *globálně jedinečný*. Pokud se zobrazí následující chyba, změňte název datové továrny (například na **&lt; &gt; ADFTutorialDataFactory** ) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
   ![Chyba: název není k dispozici](./media/doc-common-process/name-not-available-error.png)
1. Jako **Předplatné** vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit. 
1. Pro položku **Skupina prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a ze seznamu vyberte existující skupinu prostředků. 
   - Vyberte **vytvořit novou** a zadejte název skupiny prostředků.   
         
   Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
1. V poli **Verze** vyberte **V2**.
1. V poli **Umístění** vyberte umístění pro datovou továrnu.

   Seznam obsahuje jenom umístění podporovaná službou Data Factory, do kterých se budou ukládat vaše metadata Azure Data Factory. Přidružená úložiště dat (například Azure Storage a Azure SQL Database) a výpočetní prostředí (například Azure HDInsight), které Data Factory používá, mohou běžet v jiných oblastech.

1. Vyberte **Vytvořit**.

1. Po dokončení vytváření se zobrazí stránka **Data Factory** . Kliknutím na dlaždici **Vytvořit a monitorovat** spusťte na samostatné kartě aplikaci uživatelského rozhraní služby Azure Data Factory.
   
   ![Domovská stránka datové továrny s dlaždici Author & Monitor (Vytvořit a sledovat)](./media/doc-common-process/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Spuštění nástroje pro kopírování dat

1. Na stránce **Let‘s get started** (Začínáme) vyberte dlaždici **Copy Data** (Kopírovat data). Spustí se nástroj pro kopírování dat. 

   ![Dlaždice Copy Data (Kopírovat data)](./media/doc-common-process/get-started-page.png)

1. Na stránce **Properties** (Vlastnosti) nástroje Kopírování dat můžete zadat název kanálu a jeho popis. Pak vyberte **Next** (Další). 

   ![Stránka Properties (Vlastnosti)](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. Na stránce **Source data store** (Zdrojové úložiště dat) proveďte následující kroky:

    a. Kliknutím na **+ vytvořit nové připojení** přidejte připojení.

    b. Vyberte typ propojené služby, který chcete vytvořit pro zdrojové připojení. V tomto kurzu používáme **Azure Blob Storage**. Vyberte ho z galerie a pak vyberte **pokračovat**.
    
    ![Vybrat objekt BLOB](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. Na stránce **Nová propojená služba (Azure Blob Storage)** zadejte název propojené služby. V seznamu **název účtu úložiště** vyberte svůj účet úložiště, otestujte připojení a pak vyberte **vytvořit**. 

    ![Konfigurace účtu služby Azure Blob Storage](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    d. Vyberte jako zdroj nově vytvořenou propojenou službu a pak klikněte na **Další**.


1. Na stránce **Choose the input file or folder** (Zvolit vstupní soubor nebo složku) proveďte následující kroky:

   a. Klikněte na **Procházet** a přejděte do složky **adftutorial/Input** , vyberte soubor **emp.txt** a pak klikněte na **zvolit**. 

   d. Zaškrtněte políčko **binární kopírování** pro zkopírování souboru tak, jak je, a pak vyberte **Další**. 

   ![Stránka Choose the input file or folder (Zvolit vstupní soubor nebo složku)](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. Na stránce **cílové úložiště dat** vyberte propojenou službu **Azure Blob Storage** , kterou jste vytvořili, a pak vyberte **Další**. 

1. Na stránce **zvolte výstupní soubor nebo složku** zadejte **adftutorial/Output** pro cestu ke složce a pak vyberte **Další**. 

   ![Stránka Choose the output file or folder (Zvolit výstupní soubor nebo složku)](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. Na stránce **Settings** (Nastavení) vyberte **Next** (Další), aby se použila výchozí konfigurace. 

1. Na stránce **Summary** (Souhrn) zkontrolujte všechna nastavení a vyberte **Next** (Další). 

1. Na stránce **Deployment complete** (Nasazení dokončeno) vyberte **Monitor** (Sledovat) a začněte sledovat vytvořený kanál. 

    ![Stránka Deployment complete (Nasazení dokončeno)](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. Aplikace se přepne na kartu **monitorování** . Na této kartě se zobrazí stav kanálu. Vyberte **aktualizovat** , aby se seznam aktualizoval. Kliknutím na odkaz **název kanálu** zobrazíte podrobnosti o spuštění aktivit nebo znovu spustíte kanál. 
   
    ![Aktualizovat kanál](./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png)

1. Na stránce spuštění aktivit vyberte odkaz **Podrobnosti** (ikona brýlí) ve sloupci **název aktivity** , kde najdete další podrobnosti o operaci kopírování. Podrobnosti o vlastnostech najdete v tématu [Přehled aktivity kopírování](copy-activity-overview.md). 

1. Pokud se chcete vrátit do zobrazení spuštění kanálu, vyberte odkaz **všechny spuštění kanálu** v nabídce s popisem cesty. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**. 

1. Ověřte, že se ve složce **output** kontejneru **adftutorial** vytvořil soubor **emp.txt**. Pokud výstupní složka neexistuje, Služba Data Factory ji automaticky vytvoří. 

1. Nad kartou **Monitorování** v levém panelu přepněte na kartu **Vytvořit** , kde můžete upravovat propojené služby, datové sady a kanály. Další informace o jejich úpravách v uživatelském rozhraní služby Data Factory najdete v tématu [Vytvoření datové továrny pomocí webu Azure Portal](quickstart-create-data-factory-portal.md).

    ![Vybrat kartu autor](./media/quickstart-create-data-factory-copy-data-tool/select-author.png)

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob v Azure. Pokud se chcete naučit používat službu Data Factory ve více scénářích, projděte si příslušné [kurzy](tutorial-copy-data-portal.md). 
