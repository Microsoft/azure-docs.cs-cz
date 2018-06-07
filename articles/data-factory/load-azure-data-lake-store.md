---
title: Načtení dat do Azure Data Lake Store pomocí Azure Data Factory | Microsoft Docs
description: Použití Azure Data Factory ke zkopírování dat do Azure Data Lake Store
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
ms.openlocfilehash: fdfb35b0e1c52ad2aad164a38ae308f9142880a6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619622"
---
# <a name="load-data-into-azure-data-lake-store-by-using-azure-data-factory"></a>Načtení dat do Azure Data Lake Store pomocí Azure Data Factory

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) je jako podniková velkého rozsahu úložiště pro analytické úlohy s velkými objemy dat. Azure Data Lake umožňuje zaznamenávat data libovolné velikosti, typ a rychlost přijímání. Zachycení na jednom místě pro provozní a zjišťovací analýzy.

Azure Data Factory je plně spravovaná Cloudová datová služba integrace. Službu můžete použít k naplnění lake s daty z vašeho stávajícího systému a ušetřit čas při vytváření vlastních analytických řešení.

Azure Data Factory nabízí následující výhody pro načítání dat do Azure Data Lake Store:

* **Snadno nastavit**: intuitivní průvodce krok 5 žádné vyžaduje skriptování.
* **Podpora úložiště bohaté dat**: integrovanou podporu pro bohatou sadu místní a Cloudová datová úložiště. Podrobný seznam najdete v tabulce [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
* **Zabezpečení a dodržování**: Data se přenáší prostřednictvím protokolu HTTPS nebo ExpressRoute. Přítomnosti globální služby zajišťuje vaše data nikdy neopustí hranice zeměpisné.
* **Vysoký výkon**: až 1-GB/s dat rychlost načítání do Azure Data Lake Store. Podrobnosti najdete v tématu [výkonu kopie aktivity](copy-activity-performance.md).

Tento článek ukazuje, jak používat nástroj Data Factory kopírovat Data k _načtení dat z Amazon S3 do Azure Data Lake Store_. Můžete provést podobné kroky ke zkopírování dat od ostatních typů dat úložiště.

> [!NOTE]
> Další informace najdete v tématu [zkopírovat data do nebo z Azure Data Lake Store pomocí Azure Data Factory](connector-azure-data-lake-store.md).
>
> Tento článek se týká verze 2 služby Azure Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [aktivitu kopírování v Azure Data Factory verze 1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/) před zahájením.
* Azure Data Lake Store: Pokud nemáte účet Data Lake Store, postupujte podle pokynů v [vytvoření účtu Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).
* Amazon S3: Tento článek ukazuje, jak zkopírovat data z Amazonu S3. Jiným úložištím dat můžete pomocí následujících podobným způsobem.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **nový** > **Data + analýzy** > **Data Factory**:
   
   ![Vytvoření nové datové továrny](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. V **nový objekt pro vytváření dat** stránky, zadejte hodnoty pro pole, která jsou zobrazená na následujícím obrázku: 
      
   ![Stránka Nová datová továrna](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Název**: Zadejte globálně jedinečný název objektu pro vytváření dat Azure. Pokud se zobrazí chyba "název objektu pro vytváření dat \"LoadADLSDemo\" není k dispozici," Zadejte jiný název služby data Factory. Například můžete použít název  _**jméno**_**ADFTutorialDataFactory**. Zkuste vytvořit objekt pro vytváření dat znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné**: Vyberte předplatné Azure, ve kterém se má vytvořit datová továrna. 
    * **Skupina prostředků**: Vyberte existující skupinu prostředků z rozevíracího seznamu, nebo **vytvořit nový** možnost a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Verze**: vyberte **V2 (Preview)**.
    * **Umístění**: Vyberte umístění služby data Factory. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, které jsou používány pro vytváření dat může být v jiných umístěních a oblastech. Tyto úložiště dat patří Azure Data Lake Store, Azure Storage, Azure SQL Database a tak dále.

3. Vyberte **Vytvořit**.
4. Po dokončení vytvoření přejděte do data factory. Zobrazí **Data Factory** domovskou stránku, jak je znázorněno na následujícím obrázku: 
   
   ![Domovská stránka objektu pro vytváření dat](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Vyberte **Autor & monitorování** dlaždici spuštění aplikace integraci dat na samostatné kartě.

## <a name="load-data-into-azure-data-lake-store"></a>Načtení dat do Azure Data Lake Store

1. V **Začínáme** vyberte **kopírovat Data** dlaždici spustíte nástroj pro kopírování dat: 

   ![Dlaždice nástroje pro kopírování dat](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. V **vlastnosti** zadejte **CopyFromAmazonS3ToADLS** pro **název úlohy** pole a vyberte možnost **Další**:

    ![Stránka Vlastnosti](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. V **zdrojového úložiště dat** vyberte **Amazon S3**a vyberte **Další**:

    ![Stránka Zdrojové úložiště dat](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. V **připojení zadejte Amazon S3** proveďte následující kroky: 
   1. Zadejte **Access Key ID** hodnotu.
   2. Zadejte **tajný přístupový klíč** hodnotu.
   3. Vyberte **Next** (Další).
   
   ![Zadejte účet Amazon S3](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. V **zvolte vstupní soubor nebo složku** stránky, přejděte na složku a soubor, který chcete zkopírovat přes. Vyberte složka či soubor, vyberte **zvolte**a potom vyberte **Další**:

    ![Zvolte vstupní soubor nebo složku](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. V **cílového úložiště dat** vyberte **Azure Data Lake Store**a vyberte **Další**:

    ![Stránka Cílové úložiště dat](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. Vyberte chování kopie výběrem **kopírování souborů rekurzivně** a **binární kopie** (kopírovat soubory jako-je) možnosti. Vyberte **Další**:

    ![Zadejte výstupní složky](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. V **zadejte Data Lake Store připojení** proveďte následující kroky: 

   1. Vyberte pro Data Lake Store **název účtu Data Lake Store**.
   2. Zadejte informace o objektu služby: **klienta**, **služby ID objektu zabezpečení**, a **hlavní klíč služby**.
   3. Vyberte **Next** (Další).
   
   > [!IMPORTANT]
   > V tomto návodu můžete použít _instanční objekt_ k ověření Data Lake Store. Ujistěte se, zda jste instanční objekt udělit příslušná oprávnění v Azure Data Lake Store pomocí následujícího [tyto pokyny](connector-azure-data-lake-store.md#using-service-principal-authentication).
   
   ![Zadejte účet Azure Data Lake Store](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. V **zvolit výstupní soubor nebo složku** zadejte **copyfroms3** jako výstup název složky a vyberte **Další**: 

    ![Zadejte výstupní složky](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. V **nastavení** vyberte **Další**:

    ![Stránka Nastavení](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. V **Souhrn** , zkontrolujte nastavení a vyberte **Další**:

    ![Stránka souhrnu](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. V **stránka nasazení**, vyberte **monitorování** k monitorování kanálu (úlohy):

    ![Stránka Nasazení](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. **Akce** sloupec obsahuje odkazy, chcete-li zobrazit podrobnosti o spuštění aktivity a znovu spusťte kanál:

    ![Monitorování spuštění kanálu](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Chcete-li zobrazit běh aktivit, které jsou spojené s kanálu spustit, vyberte **zobrazení aktivity spouští** odkaz v **akce** sloupce. Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Chcete-li přepnout zpět do kanálu spustí zobrazení, vyberte **kanály** odkaz v horní části. Seznam můžete aktualizovat výběrem možnosti **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Ke sledování provádění podrobnosti pro každou aktivitu kopírování, vyberte **podrobnosti** v části **akce** v aktivitě zobrazení monitorování. Dokáže monitorovat podrobné údaje, jako objem dat zkopíruje ze zdroje jímka, propustnost dat, provádění kroky s odpovídající doba trvání a použít konfigurace:

    ![Podrobnosti o spuštění aktivity monitorování](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Ověřte, že data budou zkopírována do Azure Data Lake Store: 

    ![Zkontrolujte výstup Data Lake Store](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Další postup

Přechodu na v následujícím článku na další informace o podpoře Azure Data Lake Store: 

> [!div class="nextstepaction"]
>[Konektor Azure Data Lake Store](connector-azure-data-lake-store.md)
