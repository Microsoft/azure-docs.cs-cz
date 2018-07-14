---
title: Načtení dat do Azure Data Lake Storage Gen1 pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Použití Azure Data Factory pro kopírování dat do Azure Data Lake Storage Gen1
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: e401508fc5ffc1de666f727ffbb7790005384fc1
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003793"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Načtení dat do Azure Data Lake Storage Gen1 pomocí služby Azure Data Factory

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (dříve označované jako Azure Data Lake Store) je podnikové úrovni hyperškálovatelné úložiště pro analytické úlohy s velkými objemy dat. Azure Data Lake umožňuje zaznamenávat data libovolné velikosti, typu a rychlosti příjmu. Data jsou zachyceny na jednom místě pro provozní a zjišťovací analýzy.

Azure Data Factory je služba pro integraci plně spravovaný cloudový datový. Služby můžete použít k naplnění lake s daty ze stávajícího systému a ušetřit čas při vytváření vlastních analytických řešení.

Azure Data Factory nabízí následující výhody pro načítání dat do služby Azure Data Lake Store:

* **Snadné nastavení**: intuitivní Průvodce kroku 5 s žádné požadované skriptování.
* **Podpora store velké množství dat**: integrovanou podporu pro bohatou sadu místní a cloudové datové úložiště. Podrobný seznam najdete v tabulce [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
* **Zabezpečení a dodržování**: Data se přenáší prostřednictvím protokolu HTTPS nebo ExpressRoute. Přítomnost globální služba zajišťuje, že vaše data nikdy neopustí hranice zeměpisné.
* **Vysoký výkon**: až 1 GB/s dat rychlost načítání do služby Azure Data Lake Store. Podrobnosti najdete v tématu [výkonu aktivity kopírování](copy-activity-performance.md).

Tento článek ukazuje, jak použít nástroje pro kopírování dat objekt pro vytváření dat do _načíst data z Amazonu S3 do Azure Data Lake Store_. Můžete použít podobným způsobem kopírovat data z jiných typů úložišť dat.

> [!NOTE]
> Další informace najdete v tématu [kopírování dat do nebo z Azure Data Lake Store pomocí Azure Data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/) předtím, než začnete.
* Azure Data Lake Store: Pokud nemáte účet Data Lake Store, přečtěte si pokyny v [vytvoření účtu Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).
* Amazon S3: Tento článek popisuje, jak kopírovat data z Amazonu S3. Podle podobných kroků, můžete použít dalšími datovými úložišti.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **nový** > **Data a analýzy** > **služby Data Factory**:
   
   ![Vytvoření nové datové továrny](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. V **nová datová továrna** zadejte hodnoty pro pole, která jsou zobrazena na následujícím obrázku: 
      
   ![Stránka Nová datová továrna](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Název**: Zadejte globálně jedinečný název pro službu Azure data factory. Pokud se zobrazí chyba "název objektu pro vytváření dat \"LoadADLSDemo\" není k dispozici," Zadejte jiný název datové továrny. Můžete například použít název  _**vaše_jméno**_**ADFTutorialDataFactory**. Zkuste znovu vytvořit datovou továrnu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné**: vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit. 
    * **Skupina prostředků**: z rozevíracího seznamu vyberte existující skupinu prostředků, nebo **vytvořit nový** možnost a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Verze**: vyberte **V2**.
    * **Umístění**: Vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, které jsou používané datovou továrnou můžou být v jiných umístěních a oblastech. Úložiště těchto dat patří Azure Data Lake Store, Azure Storage, Azure SQL Database a tak dále.

3. Vyberte **Vytvořit**.
4. Po vytvoření se, přejděte do služby data factory. Zobrazí **služby Data Factory** domovskou stránku, jak je znázorněno na následujícím obrázku: 
   
   ![Domovská stránka datové továrny](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Vyberte **vytvořit a monitorovat** dlaždice aplikace pro integraci dat v na samostatné kartě.

## <a name="load-data-into-azure-data-lake-store"></a>Načtení dat do Azure Data Lake Store

1. V **Začínáme** stránky, vyberte **kopírování dat** dlaždice nástroje pro kopírování dat: 

   ![Dlaždice nástroje pro kopírování dat](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. V **vlastnosti** určete, **CopyFromAmazonS3ToADLS** pro **název úkolu** pole a vyberte **Další**:

    ![Stránka Vlastnosti](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. V **zdrojového úložiště dat** klikněte na **+ vytvořit nové připojení**:

    ![Stránka Zdrojové úložiště dat](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Vyberte **Amazon S3**a vyberte **pokračovat**
    
    ![Stránka zdrojové úložiště dat s3](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. V **připojení zadejte Amazon S3** stránce, proveďte následující kroky: 
   1. Zadejte **Access Key ID** hodnotu.
   2. Zadejte **tajný přístupový klíč** hodnotu.
   3. Vyberte **Finish** (Dokončit).
   
   ![Zadání účtu Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Zobrazí se nové připojení. Vyberte **Další**.
   
   ![Zadání účtu Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. V **zvolte vstupní soubor nebo složku** stránce, přejděte do složky a souboru, který chcete zkopírovat. Vyberte složky nebo souboru, vyberte **zvolit**a pak vyberte **Další**:

    ![Zvolte vstupní soubor nebo složku](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Zvolte chování kopírování tak, že vyberete **kopírovat soubory rekurzivně** a **binární kopie** (kopírování souborů jako-je) možnosti. Vyberte **Další**:

    ![Zadejte výstupní složka](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. V **cílového úložiště dat** klikněte na **+ vytvořit nové připojení**a pak vyberte **Azure Data Lake Store**a vyberte **pokračovat**:

    ![Stránka Cílové úložiště dat](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. V **připojení zadejte Data Lake Store** stránce, proveďte následující kroky: 

   1. Vyberte vaše Data Lake Store pro **název účtu Data Lake Store**.
   2. Zadejte **Tenanta**a klepnutím na tlačítko Dokončit.
   3. Vyberte **Další**.
   
   > [!IMPORTANT]
   > V tomto názorném postupu použijete _se identita spravované služby_ ověřit vaše Data Lake Store. Je potřeba udělit příslušná oprávnění v Azure Data Lake Store MSI pomocí následujícího [tyto pokyny](connector-azure-data-lake-store.md#using-managed-service-identity-authentication).
   
   ![Zadejte účet Azure Data Lake Store](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. V **zvolte výstupní soubor nebo složku** zadejte **copyfroms3** jako název složky výstupu a vyberte **Další**: 

    ![Zadejte výstupní složka](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. V **nastavení** stránce **Další**:

    ![Stránka Nastavení](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. V **Souhrn** stránky, zkontrolujte nastavení a vyberte **Další**:

    ![Stránka souhrnu](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. V **stránku nasazení**vyberte **monitorování** a začněte monitorovat kanál (úlohu):

    ![Stránka Nasazení](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. **Akce** sloupec obsahuje odkazy, chcete-li zobrazit podrobnosti o spuštění aktivit a opětovné spuštění kanálu:

    ![Monitorování spuštění kanálu](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Pokud chcete zobrazit spuštění aktivit, které jsou spojeny se spuštěním kanálu, vyberte **zobrazit spuštění aktivit** odkaz v **akce** sloupce. Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Pokud chcete přepnout zpět na zobrazení spuštění kanálu, vyberte **kanály** odkazu v horní části. Seznam můžete aktualizovat výběrem možnosti **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Pokud chcete monitorovat spuštění podrobnosti o každé aktivitě kopírování, vyberte **podrobnosti** odkaz v části **akce** v aktivitě zobrazení monitorování. Podobně jako objem dat zkopírovanou ze zdroje do jímky, propustnost dat, provádění kroků s určitou dobu a použít konfigurace, můžete sledovat informace:

    ![Podrobnosti o spuštění aktivit monitorování](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Ověřte, že se data kopírují do váš Azure Data Lake Store: 

    ![Ověření výstupu Data Lake Store](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Další postup

Přejděte k další informace o podpoře Azure Data Lake Store v následujícím článku: 

> [!div class="nextstepaction"]
>[Konektor Azure Data Lake Store](connector-azure-data-lake-store.md)
