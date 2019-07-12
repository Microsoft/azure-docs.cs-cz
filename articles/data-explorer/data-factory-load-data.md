---
title: Kopírování dat z Azure Data Factory do Průzkumníku dat Azure
description: V tomto tématu zjistíte, jak ingestovat data (načíst) do Průzkumníku dat Azure pomocí Azure Data Factory nástroj pro kopírování
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 2142fbf03daa6667b20db43f9212a2b5e6d7dd44
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657535"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Kopírování dat do Průzkumníku dat Azure pomocí Azure Data Factory 

Průzkumník služby Azure Data je rychlé a plně spravované datové služby analytics pro analýzu v reálném čase na velké objemy dat streamované z mnoha zdrojů, jako je například aplikace, weby a zařízení IoT. Opakovaně zkoumat data a identifikovat vzory a anomálie k vylepšení produktů, vylepšení zkušeností zákazníků, monitorovat zařízení a zvýšit operace. Prozkoumejte nové dotazy a získejte odpovědi v řádu minut. Azure Data Factory je služba pro integraci plně spravovaný cloudový datový. Služby můžete použít k naplnění databáze Průzkumník dat Azure s daty ze stávajícího systému a ušetřit čas při vytváření vlastních analytických řešení.

Azure Data Factory nabízí následující výhody pro načítání dat do Průzkumníku dat Azure:

* **Snadné nastavení**: Intuitivní Průvodce pěti krocích pomocí bez skriptů, vyžaduje.
* **Podpora podrobná data store**: Integrovanou podporu pro bohatou sadu místní a cloudové datové úložiště. Podrobný seznam najdete v tabulce [podporovanými úložišti dat](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Zabezpečení a dodržování**: Data se přenáší prostřednictvím protokolu HTTPS nebo ExpressRoute. Přítomnost globální služba zajišťuje, že vaše data nikdy neopustí hranice zeměpisné.
* **Vysoký výkon**: Až 1 GB/s dat načítací rychlosti do Průzkumníku dat Azure. Podrobnosti najdete v tématu [výkonu aktivity kopírování](/azure/data-factory/copy-activity-performance).

V tomto článku se dozvíte, jak načíst data z Amazonu S3 do Průzkumníku dat Azure pomocí nástroje pro kopírování dat Data Factory. Můžete postupovat podle podobných kroků ke kopírování dat z jiných úložišť dat, jako [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage), [Azure SQL Database](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle), a [systém souborů](/azure/data-factory/connector-file-system).

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Průzkumník dat Azure clusteru a databáze služby](create-cluster-database-portal.md)
* Zdroj dat

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Vyberte **vytvořit prostředek** tlačítko (+) v levém horním rohu portálu > **Analytics** > **služby Data Factory**.

   ![Vytvoření nové datové továrny](media/data-factory-load-data/create-adf.png)

1. V **nová datová továrna** stránky, zadejte hodnoty pro následující pole a pak vyberte **vytvořit**.

    ![Stránka Nová datová továrna](media/data-factory-load-data/my-new-data-factory.png)

    **Nastavení**  | **Popis pole**
    |---|---|
    | **Název** | Zadejte globálně jedinečný název datové továrny. Pokud se zobrazí chyba *"název objektu pro vytváření dat \"LoadADXDemo\" není k dispozici"* , zadejte jiný název datové továrny. Pravidla pojmenování artefaktů služby Data Factory, naleznete v tématu [Data Factory – pravidla pojmenování](/azure/data-factory/naming-rules).|
    | **Předplatné** | Vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit. |
    | **Skupina prostředků** | Vyberte **vytvořit nový** a zadejte název nové skupiny prostředků. Vyberte **použít existující**, pokud máte existující skupinu prostředků. |
    | **Verze** | Vyberte **V2** |
    | **Location** | Vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložišť dat, které jsou používané datovou továrnou můžou být v jiných umístěních nebo oblastech. |
    | | |

1. Vyberte oznámení na panelu nástrojů můžete sledovat v procesu vytváření. Po vytvoření se, přejděte do služby data factory, který jste vytvořili. **Služby Data Factory** otevře domovskou stránku.

   ![Domovská stránka datové továrny](media/data-factory-load-data/data-factory-home-page.png)

1. Vyberte **vytvořit a monitorovat** dlaždici ke spuštění aplikace na samostatné kartě.

## <a name="load-data-into-azure-data-explorer"></a>Načtení dat do Průzkumníku dat Azure

Data je možné načíst z řady typů [úložišť dat](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) do Průzkumníku dat Azure. Toto téma obsahuje podrobnosti o načítají se data z Amazonu S3.

Existují dva způsoby, jak načíst data do Průzkumníku dat Azure pomocí Azure Data Factory:

* Uživatelské rozhraní Azure Data Factory - [ **Autor** kartu](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Azure Data Factory **kopírování dat** nástroj](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) použité v tomto článku.

### <a name="copy-data-from-amazon-s3-source"></a>Kopírovat data z Amazonu S3 (zdroj)

1. V **pusťme se do práce** stránky, vyberte **kopírování dat** dlaždice nástroje pro kopírování dat.

   ![Dlaždice nástroj pro kopírování dat](media/data-factory-load-data/copy-data-tool-tile.png)

1. V **vlastnosti** určete, **název úkolu** a vyberte **Další**.

    ![Zkopírujte z vlastnosti zdroje](media/data-factory-load-data/copy-from-source.png)

1. V **zdrojového úložiště dat** klikněte na **+ vytvořit nové připojení**.

    ![Vytvoření připojení zdroje dat](media/data-factory-load-data/source-create-connection.png)

1. Vyberte **Amazon S3**a pak vyberte **pokračovat**

    ![Nová propojená služba](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. V **Nová propojená služba (Amazon S3)** stránce, proveďte následující kroky:

    ![Zadání Amazon S3 propojené služby](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Zadejte **název** z nové propojené služby.
    * Vyberte **připojit prostřednictvím prostředí integration runtime** hodnotu z rozevíracího seznamu.
    * Zadejte **Access Key ID** hodnotu.
    * Zadejte **tajný přístupový klíč** hodnotu.
    * Vyberte **Test připojení** chcete otestovat připojení propojenou službu jste vytvořili.
    * Vyberte **Finish** (Dokončit).

1. V **zdrojového úložiště dat** stránky, zobrazí se nové připojení AmazonS31. Vyberte **Další**.

   ![Úložiště vytvořit připojení ke zdroji dat](media/data-factory-load-data/source-data-store-created-connection.png)

1. V **zvolte vstupní soubor nebo složku** stránky:

    1. Přejděte do složky nebo souboru, který chcete zkopírovat. Vyberte složky nebo souboru.
    1. Vyberte chování kopírování podle potřeby. Zachovat **binární kopie** nezaškrtnuté.
    1. Vyberte **Další**.

    ![Zvolte vstupní soubor nebo složku](media/data-factory-load-data/source-choose-input-file.png)

1. V **formáty souborů nastavení** stránky vyberte odpovídající nastavení pro soubor a klikněte na tlačítko **Další**.

   ![Zvolte vstupní soubor nebo složku](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Kopírování dat do Průzkumníku dat Azure (cíl)

Průzkumník dat Nová propojená služba Azure se vytvoří pro kopírování dat do cílové tabulky Průzkumník dat Azure (jímka) uvedené níže.

1. V **cílového úložiště dat** stránky, můžete použít existující data uložit připojení nebo zadejte nové úložiště dat kliknutím **+ vytvořit nové připojení**.

    ![Stránka Cílové úložiště dat](media/data-factory-load-data/destination-create-connection.png)

1. V **Nová propojená služba** stránce **Průzkumník dat Azure**a pak vyberte **pokračovat**

    ![Vyberte Azure Průzkumník dat – Nová propojená služba](media/data-factory-load-data/adx-select-new-linked-service.png)

1. V **Nová propojená služba (Průzkumník dat Azure)** stránce, proveďte následující kroky:

    ![Nová propojená služba ADX](media/data-factory-load-data/adx-new-linked-service.png)

   * Vyberte **název** Průzkumník dat Azure propojenou službu.
   * V **metoda výběru účtu**: 
        * Vyberte **předplatné Azure z** přepínací tlačítka a vyberte váš **předplatného Azure** účtu. Vyberte vaše **clusteru**. Všimněte si rozevíracího seznamu bude pouze výpis clusterů, které patří uživateli.
        * Můžete také vybrat **zadat ručně** přepínač a zadejte vaše **koncový bod**.
    * Zadejte **Tenanta**.
    * Zadejte **ID instančního objektu služby**.
    * Vyberte **klíč instančního objektu** tlačítko a zadejte **klíč objektu služby**.
    * Vyberte vaše **databáze** z rozevírací nabídky. Můžete také vybrat **upravit** zaškrtávací políčko a zadejte název databáze.
    * Vyberte **Test připojení** chcete otestovat připojení propojenou službu jste vytvořili. Pokud se můžete připojit k vaší instalace, zelenou značku zaškrtnutí **úspěšné připojení** se zobrazí.
    * Vyberte **Dokončit** nezbytných k dokončení vytvoření propojené služby.

    > [!NOTE]
    > Instanční objekt se používá službou Azure Data Factory pro přístup ke službě Průzkumník dat Azure. Pro instanční objekt [vytvořit instanční objekt služby Azure Active Directory (Azure AD)](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Nepoužívejte **Azure Key Vault** metody.

1. **Cílového úložiště dat** otevře. Není k dispozici pro použití Průzkumníka služby Azure Data datové připojení, které jste vytvořili. Vyberte **Další** ke konfiguraci připojení.

    ![ADX cílového úložiště dat.](media/data-factory-load-data/destination-data-store.png)

1. V **mapování tabulky**, nastavte název cílové tabulky a vyberte **Další**.

    ![Mapování tabulek cílový dataset](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. V **mapování sloupců** stránky:
    * První mapování se provádí pomocí ADF podle [ADF mapování schématu](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * Nastavte **mapování sloupců** pro cílové tabulky objektu pro vytváření dat Azure. Výchozí mapování se zobrazí ze zdroje do cílové tabulky ADF.
        * Zrušit výběr sloupce, které není nutné definovat mapování sloupců.
    * Druhý mapování nastane, pokud tento tabulková data ingestují do Průzkumníku dat Azure. Mapování se provádí podle [pravidla mapování sdíleného svazku clusteru](/azure/kusto/management/mappings#csv-mapping). Všimněte si, že i v případě, že zdroj dat nebyl ve formátu CSV, ADF má data převedli do formátu tabulky, proto je mapování sdíleného svazku clusteru pouze relevantní mapování v této fázi.
        * V části **Průzkumník dat Azure (Kusto) jímky vlastnosti** přidejte příslušné **Ingestování mapování názvu** (volitelné) proto mapování sloupce je možné.
        * Pokud **Ingestování mapování názvu** není zadán, podle pořadí mapování "by-name" **mapování sloupců** dojde k oddílu. Pokud mapování "by-name" selže, Průzkumník dat Azure se pokusí ingestovat data v pořadí "podle sloupce pozice" (mapy podle pozice jako výchozí).
    * Vyberte **Další**.

    ![Mapování sloupce cílové datové sady](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Na stránce **Nastavení**:
    * Nastavit příslušné **odolnost proti chybám nastavení**.
    * **Nastavení výkonu**: Povolit pracovní nelze použít. **Upřesňující nastavení** zahrnují náklady na aspekty. Nechte, jak je, pokud žádné konkrétní potřebuje.
    * Vyberte **Další**.

    ![Kopírovat nastavení dat](media/data-factory-load-data/copy-data-settings.png)

1. V **Souhrn**, zkontrolujte nastavení a vyberte **Další**.

    ![Kopírování dat souhrnného](media/data-factory-load-data/copy-data-summary.png)

1. V **stránku nasazení**:
    * Vyberte **monitorování** přepnout na **monitorování** kartu a zobrazit stav kanálu (průběh, chyby a tok dat).
    * Vyberte **upravit kanál** můžete upravit propojené služby, datové sady a kanály.
    * Vyberte **Dokončit** úplnou kopii dat úkolu

    ![Stránka Nasazení](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Další postup

* Další informace o [Průzkumník dat Azure konektor](/azure/data-factory/connector-azure-data-explorer) ve službě Azure Data Factory.

* Další informace o úpravách propojené služby, datové sady a kanály ve službě [uživatelské rozhraní služby Data Factory](/azure/data-factory/quickstart-create-data-factory-portal).

* Další informace o [Průzkumník dat Azure dotazy](/azure/data-explorer/web-query-data) pro dotazování na data.
