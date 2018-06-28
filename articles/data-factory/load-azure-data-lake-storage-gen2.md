---
title: Načtení dat do Azure Data Lake Storage Gen2 (Náhled) s Azure Data Factory
description: Pomocí Azure Data Factory ke zkopírování dat do Azure Data Lake Storage Gen2 (Preview)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: 961c8dea4dbb6b6600d10b75e84a9a84c34c329b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035868"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-preview-with-azure-data-factory"></a>Načtení dat do Azure Data Lake Storage Gen2 Preview s Azure Data Factory

[Azure Data Lake Storage Gen2 Preview](../storage/data-lake-storage/introduction.md) Přidá protokol s funkcí obor názvů a zabezpečení systému hierarchické souboru do Azure Blob Storage a usnadňuje tak připojit analytics rozhraní do vrstvy odolné úložiště. V Data Lake Storage Gen2 (Preview), zůstávají všechny vlastnosti objektu úložiště při přidávání výhody rozhraní systému souborů.

Azure Data Factory je plně spravovaná Cloudová datová služba integrace. Službu můžete použít k naplnění lake s daty z širokou škálu místní a Cloudová datová ukládá a ušetřit čas při vytváření vlastních analytických řešení. Podrobný seznam podporované konektory, najdete v tabulce [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory nabízí škálovatelnou, řešení přesun spravovaných dat oproti AzCopy, příkazový řádek dat přenosu nástroj. Z důvodu škálovatelnou architekturu ADF ho ingestování data na vysoké propustnosti. Podrobnosti najdete v tématu [výkonu kopie aktivity](copy-activity-performance.md).

V tomto článku se dozvíte, jak používat nástroj Data Factory kopírovat Data načíst data z _služby Amazon Web Services S3_ do _Azure Data Lake Storage Gen2_. Můžete provést podobné kroky ke zkopírování dat od ostatních typů dat úložiště.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/) před zahájením.
* Účet služby Azure Storage s Data Lake Storage Gen2 povoleno: Pokud nemáte účet úložiště, klikněte na tlačítko [sem](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) k jeho vytvoření.
* Účet AWS s sady S3, který obsahuje data: Tento článek ukazuje, jak zkopírovat data z Amazonu S3. Jiným úložištím dat můžete pomocí následujících podobným způsobem.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **nový** > **Data + analýzy** > **Data Factory**:
   
   ![Vytvoření nové datové továrny](./media/load-azure-data-lake-storage-gen2/new-azure-data-factory-menu.png)
2. V **nový objekt pro vytváření dat** stránky, zadejte hodnoty pro pole, která jsou zobrazená na následujícím obrázku: 
      
   ![Stránka Nová datová továrna](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Název**: Zadejte globálně jedinečný název objektu pro vytváření dat Azure. Pokud se zobrazí chyba "název objektu pro vytváření dat \"LoadADLSDemo\" není k dispozici," Zadejte jiný název služby data Factory. Například můžete použít název  _**jméno**_**ADFTutorialDataFactory**. Zkuste vytvořit objekt pro vytváření dat znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné**: Vyberte předplatné Azure, ve kterém se má vytvořit datová továrna. 
    * **Skupina prostředků**: Vyberte existující skupinu prostředků z rozevíracího seznamu, nebo **vytvořit nový** možnost a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Verze**: vyberte **V2(Preview)**.
    * **Umístění**: Vyberte umístění služby data Factory. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, které jsou používány pro vytváření dat může být v jiných umístěních a oblastech. 

3. Vyberte **Vytvořit**.
4. Po dokončení vytvoření přejděte do data factory. Zobrazí **Data Factory** domovskou stránku, jak je znázorněno na následujícím obrázku: 
   
   ![Domovská stránka objektu pro vytváření dat](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Vyberte **Autor & monitorování** dlaždici spuštění aplikace integraci dat na samostatné kartě.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Načtení dat do Azure Data Lake Storage Gen2

1. V **Začínáme** vyberte **kopírovat Data** dlaždici spustíte nástroj pro kopírování dat: 

   ![Dlaždice nástroje pro kopírování dat](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. V **vlastnosti** zadejte **CopyFromAmazonS3ToADLS** pro **název úlohy** pole a vyberte možnost **Další**:

    ![Stránka Vlastnosti](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. V **zdrojového úložiště dat** klikněte na tlačítko **+ vytvořit nové připojení**:

    ![Stránka Zdrojové úložiště dat](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Vyberte **Amazon S3** z Galerie konektoru a vyberte **pokračovat**
    
    ![Stránka s3 úložiště zdroje dat](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. V **připojení zadejte Amazon S3** proveďte následující kroky:
   1. Zadejte **Access Key ID** hodnotu.
   2. Zadejte **tajný přístupový klíč** hodnotu.
   3. Klikněte na tlačítko **testovací připojení** Pokud chcete ověřit nastavení, pak vyberte **Dokončit**.
   
   ![Zadejte účet Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   
   4. Uvidíte, že se vytvoří nové připojení. Vyberte **Next** (Další).
   
5. V **zvolte vstupní soubor nebo složku** stránky, přejděte na složku a soubor, který chcete zkopírovat přes. Vyberte složka či soubor, vyberte **zvolte**:

    ![Zvolte vstupní soubor nebo složku](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Určete chování, kopie kontrolou **kopírování souborů rekurzivně** a **binární kopie** možnosti. Vyberte **Další**:

    ![Zadejte výstupní složky](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. V **cílového úložiště dat** klikněte na tlačítko **+ vytvořit nové připojení**a potom vyberte **Azure Data Lake Storage Gen2 (Preview)** a vyberte **pokračovat** :

    ![Stránka Cílové úložiště dat](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. V **zadejte Azure Data Lake Storage připojení** proveďte následující kroky:

   1. Vyberte vaše Gen2 úložiště Data Lake podporující účet z "název pro účet úložiště" rozevíracím seznamu.
   2. Vyberte **Next** (Další).
   
   ![Zadejte účet Azure Data Lake Store](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. V **zvolit výstupní soubor nebo složku** zadejte **copyfroms3** jako výstup název složky a vyberte **Další**: 

    ![Zadejte výstupní složky](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. V **nastavení** vyberte **Další** použít výchozí nastavení:

    ![Stránka Nastavení](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. V **Souhrn** , zkontrolujte nastavení a vyberte **Další**:

    ![Stránka souhrnu](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. V **stránka nasazení**, vyberte **monitorování** k monitorování kanálu:

    ![Stránka Nasazení](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. **Akce** sloupec obsahuje odkazy, chcete-li zobrazit podrobnosti o spuštění aktivity a znovu spusťte kanál:

    ![Monitorování spuštění kanálu](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Chcete-li zobrazit běh aktivit, které jsou spojené s kanálu spustit, vyberte **zobrazení aktivity spouští** odkaz v **akce** sloupce. Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Chcete-li přepnout zpět do kanálu spustí zobrazení, vyberte **kanály** odkaz v horní části. Seznam můžete aktualizovat výběrem možnosti **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Ke sledování provádění podrobnosti pro každou aktivitu kopírování, vyberte **podrobnosti** odkaz (brýlí obrázek) v části **akce** v aktivitě zobrazení monitorování. Dokáže monitorovat podrobné údaje, jako objem dat zkopíruje ze zdroje jímka, propustnost dat, provádění kroky s odpovídající doba trvání a použít konfigurace:

    ![Podrobnosti o spuštění aktivity monitorování](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Ověřte, že data budou zkopírována do účtu Data Lake Storage Gen2.

## <a name="best-practice"></a>Osvědčený postup

Při kopírování velkého objemu dat z úložiště dat na základě souborů, byly navrženy pro:

- Rozdělit na oddíly na 20TB použití soubory do 10TB.
- Nespouštějí příliš mnoho souběžných kopie spustí předejdete omezení ze zdroje nebo podřízený datová úložiště. Můžete začít s jeden kopie spuštění a monitorování propustnost a pak postupně přidat další podle potřeby.

## <a name="next-steps"></a>Další postup

* [Kopie aktivity – přehled](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2 konektoru](connector-azure-data-lake-storage.md)