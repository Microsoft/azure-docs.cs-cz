---
title: Načítání dat do azure data lake storage gen1
description: Kopírování dat do Azure Data Lake Storage Gen1 pomocí Azure Data Factory
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/17/2018
ms.openlocfilehash: 1b1b19814709451bdbbea97462c459149484e71f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415849"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Načítání dat do Azure Data Lake Storage Gen1 pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (dříve známé jako Azure Data Lake Store) je celopodnikové úložiště v měřítku velkých objemů dat pro analytické úlohy velkých objemů dat. Data Lake Storage Gen1 umožňuje zachytit data libovolné velikosti, typu a rychlosti přijím. Data jsou zachycena na jednom místě pro provozní a průzkumnou analýzu.

Azure Data Factory je plně spravovaná cloudová služba pro integraci dat. Pomocí služby můžete naplnit jezero daty ze stávajícího systému a ušetřit čas při vytváření analytických řešení.

Azure Data Factory nabízí následující výhody pro načítání dat do data Lake Storage Gen1:

* **Snadné nastavení**: Intuitivní pětistupňový průvodce bez nutnosti skriptování.
* **Bohatá podpora úložiště dat:** Integrovaná podpora pro bohatou sadu místních a cloudových úložišť dat. Podrobný seznam naleznete v tabulce [Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
* **Zabezpečené a kompatibilní**: Data se přenášejí přes HTTPS nebo ExpressRoute. Globální přítomnost služby zajišťuje, že vaše data nikdy neopustí geografickou hranici.
* **Vysoký výkon:** Rychlost načítání dat až 1 GB/s do data Lake Storage Gen1. Podrobnosti naleznete v [tématu Kopírování výkonu aktivity](copy-activity-performance.md).

Tento článek ukazuje, jak pomocí nástroje Data Factory Copy Data tool _načíst data z Amazon S3 do data Lake Storage Gen1_. Můžete postupovat podle podobných kroků ke kopírování dat z jiných typů úložišť dat.

> [!NOTE]
> Další informace najdete [v tématu Kopírování dat do nebo z Data Lake Storage Gen1 pomocí Azure Data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* Účet Data Lake Storage Gen1: Pokud nemáte účet Data Lake Storage Gen1, přečtěte si pokyny v [tématu Vytvoření účtu Gen1 úložiště datového jezera](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3: Tento článek ukazuje, jak kopírovat data z Amazon S3. Jiná úložiště dat můžete použít podle podobných kroků.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V levé nabídce vyberte Vytvořit**datovou továrnu****Analytics** >  **:** > 
   
   ![Výběr datové továrny v podokně Nový](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stránce **Nová továrna dat** zadejte hodnoty pro pole, která jsou zobrazena na následujícím obrázku: 
      
   ![Stránka Nová datová továrna](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Název:** Zadejte globálně jedinečný název pro vaši datovou továrnu Azure. Pokud se zobrazí chyba "Název \"datové továrny\" LoadADLSG1Demo není k dispozici", zadejte jiný název pro datové továrny. Můžete například použít název _**yourname**_**ADFTutorialDataFactory**. Zkuste znovu vytvořit datovou továrnu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné:** Vyberte předplatné Azure, ve kterém chcete vytvořit datové továrny. 
    * **Skupina prostředků**: V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte možnost **Vytvořit nový** a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
    * **Verze**: Vyberte **V2**.
    * **Umístění**: Vyberte umístění pro datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, které jsou používány factory může být v jiných umístěních a oblastech. Tato úložiště dat zahrnují Azure Data Lake Storage Gen1, Azure Storage, Azure SQL Database a tak dále.

3. Vyberte **Vytvořit**.
4. Po dokončení vytváření přejděte do datové továrny. Zobrazí se domovská stránka **Data Factory,** jak je znázorněno na následujícím obrázku: 
   
   ![Domovská stránka objektu pro vytváření dat](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Výběrem dlaždice **Author & Monitor** (Vytvořit a monitorovat) otevřete na samostatné kartě aplikaci pro integraci dat.

## <a name="load-data-into-data-lake-storage-gen1"></a>Načítání dat do úložiště datového jezera Gen1

1. Na stránce **Začínáme** vyberte dlaždici **Kopírovat data** a spusťte nástroj Kopírovat data: 

   ![Dlaždice nástroje pro kopírování dat](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Na stránce **Vlastnosti** zadejte **příkaz CopyFromAmazonS3ToADLS** pro pole **Název úkolu** a vyberte **Další**:

    ![Stránka Vlastnosti](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Na stránce **Zdrojové úložiště dat** klikněte na **+ Vytvořit nové připojení**:

    ![Stránka Zdrojové úložiště dat](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Vyberte **Amazon S3**a vyberte **Pokračovat**
    
    ![Stránka úložiště zdrojových dat s3](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Na stránce **Určení připojení Amazon S3** proveďte následující kroky: 
   1. Zadejte hodnotu **ID přístupového klíče.**
   2. Zadejte hodnotu **klíče tajného přístupu.**
   3. Vyberte **Finish** (Dokončit).
   
      ![Zadejte účet Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Uvidíte nové připojení. Vyberte **Další**.
   
   ![Zadejte účet Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. Na stránce pro **volbu vstupního souboru nebo složky** přejděte ke složce a souboru, který chcete zkopírovat. Vyberte složku nebo soubor, vyberte **Vybrat**a pak vyberte **Další**:

    ![Zvolte vstupní soubor nebo složku](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Zvolte chování kopírování výběrem možností **Kopírovat soubory rekurzivně** a **Binární kopírování** (kopírování souborů tak, jak jsou). Vyberte **další**:

    ![Určení výstupní složky](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. Na stránce **Cílové úložiště dat** klikněte na + Vytvořit nové **připojení**a pak vyberte Azure Data Lake **Storage Gen1**a vyberte **Pokračovat**:

    ![Stránka Cílové úložiště dat](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Na stránce **New Linked Service (Azure Data Lake Storage Gen1)** proveďte následující kroky: 

   1. Vyberte svůj účet Data Lake Storage Gen1 pro **název účtu Data Lake Store**.
   2. Zadejte **tenanta**a vyberte Dokončit.
   3. Vyberte **Další**.
   
   > [!IMPORTANT]
   > V tomto návodu použijete spravovanou identitu pro prostředky Azure k ověření účtu Data Lake Storage Gen1. Nezapomeňte udělit MSI správná oprávnění v Data Lake Storage Gen1 podle [následujících pokynů](connector-azure-data-lake-store.md#managed-identity).
   
   ![Určení účtu Data Lake Storage Gen1](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Na stránce **Vybrat výstupní soubor nebo složku** zadejte **copyfroms3** jako název výstupní složky a vyberte **Další**: 

    ![Určení výstupní složky](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Na stránce **Nastavení** vyberte **Další**:

    ![Stránka Nastavení](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Na stránce **Souhrn** zkontrolujte nastavení a vyberte **Další**:

    ![Stránka souhrnu](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Na **stránce Nasazení**vyberte **Sledovat,** chcete-li sledovat kanál (úlohu):

    ![Stránka Nasazení](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Sloupec **Akce** obsahuje odkazy na zobrazení podrobností o spuštění aktivity a opětovné spuštění kanálu:

    ![Monitorování spuštění kanálu](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Chcete-li zobrazit spuštění aktivity, které jsou přidruženy ke spuštění kanálu, vyberte odkaz **Zobrazit spuštění aktivity** ve sloupci **Akce.** Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Chcete-li přepnout zpět do zobrazení spuštění kanálu, vyberte odkaz **Potrubí** v horní části. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Chcete-li sledovat podrobnosti spuštění pro každou aktivitu kopírování, vyberte odkaz **Podrobnosti** v části **Akce** v zobrazení sledování aktivity. Můžete sledovat podrobnosti, jako je objem dat zkopírovaných ze zdroje do jímky, propustnost dat, kroky spuštění s odpovídající dobou trvání a použité konfigurace:

    ![Podrobnosti spuštění aktivity monitoru](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Ověřte, zda jsou data zkopírována do vašeho účtu Data Lake Storage Gen1: 

    ![Ověření výstupu Data Lake Storage Gen1](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Další kroky

Přejdete k následujícímu článku, kde se dozvíte o podpoře Data Lake Storage Gen1: 

> [!div class="nextstepaction"]
>[Konektor Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
