---
title: Kopírování dat z Azure Data Factory do Azure Průzkumník dat
description: V tomto tématu se naučíte přijímat (načítat) data do Azure Průzkumník dat pomocí nástroje Azure Data Factory Copy.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 5eb05df7ed97839ef80798a752565234d180f0e2
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268821"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Kopírování dat do Azure Průzkumník dat pomocí Azure Data Factory 

Azure Průzkumník dat je rychlá a plně spravovaná služba analýzy dat pro analýzu velkých objemů datových proudů dat z mnoha zdrojů, jako jsou aplikace, weby a zařízení IoT, a to v reálném čase. Iterativní prozkoumávání dat a identifikaci vzorců a anomálií pro zlepšení produktů, vylepšení zkušeností uživatelů, monitorování zařízení a zvyšování provozu. Prozkoumejte nové dotazy a získejte odpovědi v řádu minut. Azure Data Factory je plně spravovaná cloudová služba pro integraci dat. Službu můžete použít k naplnění databáze služby Azure Průzkumník dat daty ze stávajícího systému a ušetřit čas při vytváření analytických řešení.

Azure Data Factory nabízí následující výhody pro načítání dat do Azure Průzkumník dat:

* **Snadné nastavení**: Intuitivní průvodce pěti kroky bez nutnosti skriptování.
* **Podpora úložiště s bohatými daty**: Integrovaná podpora pro bohatou sadu místních a cloudových úložišť dat. Podrobný seznam najdete v tabulce [podporovaných úložišť dat](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Zabezpečení a dodržování předpisů**: Data se přenáší přes protokol HTTPS nebo ExpressRoute. Přítomnost globální služby zajišťuje, že vaše data nikdy neopustí zeměpisnou hranici.
* **Vysoký výkon**: Až do Azure Průzkumník dat rychlost načítání dat až 1 GB/s. Podrobnosti najdete v tématu o [výkonu aktivity kopírování](/azure/data-factory/copy-activity-performance).

V tomto článku se dozvíte, jak pomocí nástroje Data Factory Kopírování dat načíst data z Amazon S3 do Azure Průzkumník dat. Můžete postupovat podle podobných kroků a kopírovat data z jiných úložišť dat, jako jsou [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage), [Azure SQL Database](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle)a [systém souborů](/azure/data-factory/connector-file-system).

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Cluster a databáze Azure Průzkumník dat](create-cluster-database-portal.md)
* Zdroj dat

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Vyberte tlačítko **vytvořit prostředek** (+) v levém horním rohu portálu > **Analytics** > **Data Factory**.

   ![Vytvoření nové datové továrny](media/data-factory-load-data/create-adf.png)

1. Na stránce **Nová datová továrna** zadejte hodnoty pro následující pole a pak vyberte **vytvořit**.

    ![Stránka Nová datová továrna](media/data-factory-load-data/my-new-data-factory.png)

    **Nastavení**  | **Popis pole**
    |---|---|
    | **Název** | Zadejte globálně jedinečný název pro datovou továrnu. Pokud se zobrazí chyba *" \"název objektu pro vytváření dat\" LoadADXDemo není k dispozici"* , zadejte jiný název pro datovou továrnu. Pravidla pojmenování artefaktů Data Factory naleznete v tématu [Data Factory pravidla pro pojmenování](/azure/data-factory/naming-rules).|
    | **Předplatné** | Vyberte své předplatné Azure, ve kterém chcete vytvořit datovou továrnu. |
    | **Skupina prostředků** | Vyberte **vytvořit novou** a zadejte název nové skupiny prostředků. Pokud máte existující skupinu prostředků, vyberte **použít existující**. |
    | **Verze** | Vybrat **v2** |
    | **Location** | Vyberte umístění pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, která služba Data Factory používá, můžou být v jiných umístěních nebo oblastech. |
    | | |

1. Na panelu nástrojů vyberte oznámení pro monitorování procesu vytváření. Až se vytváření dokončí, přejdete na objekt pro vytváření dat, který jste vytvořili. Otevře se Domovská stránka **Data Factory** .

   ![Domovská stránka datové továrny](media/data-factory-load-data/data-factory-home-page.png)

1. Výběrem dlaždice **autora & monitorování** spusťte aplikaci na samostatné kartě.

## <a name="load-data-into-azure-data-explorer"></a>Načtení dat do Azure Průzkumník dat

Data je možné načíst z mnoha typů [úložišť dat](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) do Azure Průzkumník dat. Toto téma podrobně popisuje načtení dat z Amazon S3.

Existují dva způsoby, jak do Azure Průzkumník dat načíst data pomocí Azure Data Factory:

* Azure Data Factory uživatelské rozhraní – [karta **Autor**](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Nástroj Azure Data Factory **kopírování dat** ](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) , který se používá v tomto článku.

### <a name="copy-data-from-amazon-s3-source"></a>Kopírovat data z Amazon S3 (zdroj)

1. Na stránce **Začínáme** vyberte dlaždici **kopírování dat** pro spuštění nástroje kopírování dat.

   ![Dlaždice nástroje pro kopírování dat](media/data-factory-load-data/copy-data-tool-tile.png)

1. Na stránce **vlastnosti** zadejte **název úlohy** a vyberte **Další**.

    ![Kopírovat ze zdrojových vlastností](media/data-factory-load-data/copy-from-source.png)

1. Na stránce **zdrojové úložiště dat** klikněte na **+ vytvořit nové připojení**.

    ![Zdrojová data – vytvořit připojení](media/data-factory-load-data/source-create-connection.png)

1. Vyberte **Amazon S3**a pak vyberte **pokračovat** .

    ![Nová propojená služba](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Na stránce **Nová propojená služba (Amazon S3)** proveďte následující kroky:

    ![Zadat propojenou službu Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Zadejte **název** nové propojené služby.
    * V rozevíracím seznamu vyberte **připojit pomocí hodnoty prostředí Integration runtime** .
    * Zadejte hodnotu **ID přístupového klíče** .
    * Zadejte hodnotu **tajného přístupového klíče** .
    * Vyberte **Test připojení** a otestujte připojení propojené služby, které jste vytvořili.
    * Vyberte **Finish** (Dokončit).
    
    > [!NOTE]
    > V Amazon S3 vyberte v navigačním panelu své uživatelské jméno Amazon a pak vyberte **Moje přihlašovací údaje zabezpečení** a vyhledejte **přístupový klíč**. 

1. Na stránce **zdrojové úložiště dat** se zobrazí vaše nové připojení AmazonS31. Vyberte **Další**.

   ![Zdrojové úložiště dat vytvořené připojení](media/data-factory-load-data/source-data-store-created-connection.png)

1. Na stránce **zvolit vstupní soubor nebo složku** :

    1. Přejděte do složky nebo souboru, který chcete zkopírovat. Vyberte složku nebo soubor.
    1. Vyberte chování při kopírování podle potřeby. Nechat **binární kopii** nezaškrtnutou.
    1. Vyberte **Další**.

    ![Zvolte vstupní soubor nebo složku](media/data-factory-load-data/source-choose-input-file.png)

1. Na stránce **Nastavení formátů souborů** vyberte odpovídající nastavení souboru a klikněte na **Další**.

   ![Zvolte vstupní soubor nebo složku](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Kopírování dat do Azure Průzkumník dat (cíl)

Služba Azure Průzkumník dat vytvoří novou propojenou službu pro kopírování dat do cílové tabulky Azure Průzkumník dat (jímka), která je uvedená níže.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Vytvoření propojené služby Azure Průzkumník dat

1. Na stránce **cílové úložiště dat** můžete použít existující připojení úložiště dat nebo zadat nové úložiště dat kliknutím na **+ vytvořit nové připojení**.

    ![Stránka Cílové úložiště dat](media/data-factory-load-data/destination-create-connection.png)

1. Na stránce **Nová propojená služba** vyberte **Azure Průzkumník dat**a pak vyberte **pokračovat** .

    ![Výběr služby Azure Průzkumník dat – Nová propojená služba](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Na stránce **Nová propojená služba (Azure Průzkumník dat)** proveďte následující kroky:

    ![Nová propojená služba ADX](media/data-factory-load-data/adx-new-linked-service.png)

   * Vyberte **název** propojené služby Azure Průzkumník dat.
   * V **metodě výběru účtu**: 
        * Vyberte přepínač **z předplatného Azure** a vyberte svůj účet **předplatného Azure** . Pak vyberte svůj **cluster**. Poznámka: rozevírací seznam bude zobrazovat jenom clustery, které patří k uživateli.
        * Případně vyberte možnost **zadat ručně** přepínací tlačítko a zadejte **koncový bod**.
    * Zadejte **tenanta**.
    * Zadejte **ID instančního objektu**.
    * Vyberte tlačítko **klíč instančního objektu** a zadejte **klíč instančního objektu služby**.
    * Z rozevírací nabídky vyberte svou **databázi** . Případně můžete vybrat zaškrtávací políčko **Upravit** a zadat název databáze.
    * Vyberte **Test připojení** a otestujte připojení propojené služby, které jste vytvořili. Pokud se můžete připojit k instalaci, **zobrazí se zelený symbol zaškrtnutí.**
    * Vytvoření propojené služby dokončíte kliknutím na **Dokončit** .

    > [!NOTE]
    > Objekt služby se používá Azure Data Factory pro přístup ke službě Azure Průzkumník dat. V případě instančního objektu [Vytvořte objekt služby Azure Active Directory (Azure AD)](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Nepoužívejte metodu **Azure Key Vault** .

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Konfigurace datového připojení Azure Průzkumník dat

1. Otevře se **cílové úložiště dat** . Datové připojení Azure Průzkumník dat, které jste vytvořili, je k dispozici k použití. Pro konfiguraci připojení vyberte **Další** .

    ![Cílové úložiště dat ADX](media/data-factory-load-data/destination-data-store.png)

1. V části **mapování tabulky**nastavte název cílové tabulky a vyberte **Další**.

    ![Mapování tabulky cílové datové sady](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Na stránce **mapování sloupců** :
    * První mapování provádí pomocí ADF podle [mapování schématu ADF](/azure/data-factory/copy-activity-schema-and-type-mapping) .
        * Nastavte **mapování sloupců** pro cílovou tabulku Azure Data Factory. Výchozí mapování se zobrazí ze zdroje do cílové tabulky ADF.
        * Zrušte výběr sloupců, které nemusíte definovat mapování sloupce.
    * Druhé mapování nastane, když se tato tabulková data ingestují do služby Azure Průzkumník dat. Mapování se provádí podle [pravidel mapování sdílených svazků clusteru](/azure/kusto/management/mappings#csv-mapping). Mějte na paměti, že i když zdrojová data nejsou ve formátu CSV, služba ADF data převedla do tabulkového formátu, proto mapování sdílených svazků clusteru je v této fázi jediným relevantním mapováním.
        * V části **vlastnosti jímky služby Azure Průzkumník dat (Kusto)** přidejte odpovídající **název mapování** ingestování (volitelné), aby bylo možné použít mapování sloupce.
        * Pokud není zadán **název mapování pro přijímání** , dojde k definování pořadí mapování "podle názvu" definovaného v oddílu **mapování sloupců** . Pokud mapování "podle názvu" selže, služba Azure Průzkumník dat se pokusí ingestovat data v pořadí podle sloupce (mapy podle umístění jako výchozí).
    * Vyberte **Další**.

    ![Mapování sloupce cílové datové sady](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Na stránce **Nastavení**:
    * Nastavte odpovídající nastavení odolnosti **proti chybám**.
    * **Nastavení výkonu**: Možnost Povolit přípravu není k dispozici. **Rozšířené nastavení** zahrnuje náklady na náklady. Nechejte tak, jak je, pokud žádné konkrétní potřeby nepotřebujete.
    * Vyberte **Další**.

    ![Kopírovat nastavení dat](media/data-factory-load-data/copy-data-settings.png)

1. V části **Souhrn**zkontrolujte nastavení a vyberte **Další**.

    ![Kopírovat souhrn dat](media/data-factory-load-data/copy-data-summary.png)

1. Na **stránce nasazení**:
    * Vyberte **monitorování** a přepněte na kartu **monitorování** a podívejte se na stav kanálu (průběh, chyby a tok dat).
    * Vyberte **Upravit kanál** a upravte propojené služby, datové sady a kanály.
    * Úloha kopírování dat kliknutím na **Dokončit** dokončete

    ![Stránka Nasazení](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si informace o [konektoru Azure Průzkumník dat](/azure/data-factory/connector-azure-data-explorer) v Azure Data Factory.

* Přečtěte si další informace o úpravách propojených služeb, datových sad a kanálů v [uživatelském rozhraní Data Factory](/azure/data-factory/quickstart-create-data-factory-portal).

* Přečtěte si o dotazech na [Azure Průzkumník dat](/azure/data-explorer/web-query-data) pro dotazování na data.
