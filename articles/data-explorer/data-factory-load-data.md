---
title: Kopírování dat z Azure Data Factory do Azure Průzkumník dat
description: V tomto článku se dozvíte, jak pomocí nástroje pro kopírování Azure Data Factory ingestovat (načítat) data do Azure Průzkumník dat.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300600"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Kopírování dat do Azure Průzkumník dat pomocí Azure Data Factory 

Azure Průzkumník dat je rychlá, plně spravovaná služba pro analýzu dat. Nabízí analýzu v reálném čase u velkých objemů dat, která se streamují z mnoha zdrojů, jako jsou aplikace, weby a zařízení IoT. Pomocí Azure Průzkumník dat můžete iterativní zkoumání dat a identifikovat vzory a anomálie při vylepšování produktů, vylepšit prostředí zákazníků, monitorovat zařízení a zvyšovat operace. Pomůže vám prozkoumat nové otázky a získat odpovědi v řádu minut. 

Azure Data Factory je plně spravovaná cloudová služba pro integraci dat. Můžete ji použít k naplnění databáze služby Azure Průzkumník dat daty ze stávajícího systému. Může vám ušetřit čas při vytváření analytických řešení.

Při načítání dat do služby Azure Průzkumník dat Data Factory nabízí následující výhody:

* **Snadné nastavení**: Získejte intuitivního a pátého průvodce bez nutnosti skriptování.
* **Podpora úložiště s bohatými daty**: Získejte integrovanou podporu pro bohatou sadu místních a cloudových úložišť dat. Podrobný seznam najdete v tabulce [podporovaných úložišť dat](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Zabezpečení a dodržování předpisů**: Data se přenáší přes protokol HTTPS nebo Azure ExpressRoute. Přítomnost globální služby zajišťuje, že vaše data nikdy neopustí zeměpisnou hranici.
* **Vysoký výkon**: Rychlost načítání dat je až 1 gigabajt za sekundu (GB/s) do Azure Průzkumník dat. Další informace najdete v tématu [výkon aktivity kopírování](/azure/data-factory/copy-activity-performance).

V tomto článku pomocí nástroje Data Factory Kopírování dat načtete data ze služby Amazon Simple Storage Service (S3) do Azure Průzkumník dat. Můžete postupovat podle stejného procesu ke kopírování dat z jiných úložišť dat, jako například:
* [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [Systém souborů](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Cluster a databáze Azure Průzkumník dat](create-cluster-database-portal.md).
* Zdroj dat.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Přihlaste se k webu [Azure Portal](https://ms.portal.azure.com).

1. V levém podokně vyberte vytvořit**Data Factory** **analýzy** >  **prostředků** > .

   ![Vytvoření datové továrny v Azure Portal](media/data-factory-load-data/create-adf.png)

1. V podokně **Nová datová továrna** zadejte hodnoty pro pole v následující tabulce:

   ![Podokno nová Datová továrna](media/data-factory-load-data/my-new-data-factory.png)  

   | Nastavení  | Hodnota, která se má zadat  |
   |---|---|
   | **Název** | Do pole zadejte globálně jedinečný název pro datovou továrnu. Pokud se zobrazí chyba, *název \"objektu pro vytváření dat\" LoadADXDemo není k dispozici*, zadejte jiný název pro objekt pro vytváření dat. Pravidla týkající se pojmenování artefaktů Data Factory naleznete v tématu [Data Factory pravidla pro pojmenování](/azure/data-factory/naming-rules).|
   | **Předplatné** | V rozevíracím seznamu vyberte předplatné Azure, ve kterém chcete vytvořit datovou továrnu. |
   | **Skupina prostředků** | Vyberte **vytvořit novou**a potom zadejte název nové skupiny prostředků. Pokud již máte skupinu prostředků, vyberte **použít existující**. |
   | **Verze** | V rozevíracím seznamu vyberte **v2**. |  
   | **Location** | V rozevíracím seznamu vyberte umístění pro datovou továrnu. V seznamu se zobrazí pouze podporovaná umístění. Úložiště dat používaná datovou továrnou mohou existovat v jiných umístěních nebo oblastech. |

1. Vyberte **Vytvořit**.

1. Chcete-li monitorovat proces vytváření, vyberte **oznámení** na panelu nástrojů. Po vytvoření datové továrny ji vyberte.
   
   Otevře se podokno **Data Factory** .

   ![Podokno Data Factory](media/data-factory-load-data/data-factory-home-page.png)

1. Pokud chcete aplikaci otevřít v samostatném podokně, vyberte dlaždici **Author & monitor** .

## <a name="load-data-into-azure-data-explorer"></a>Načtení dat do Azure Průzkumník dat

Data z mnoha typů [úložišť dat](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) můžete načíst do Azure Průzkumník dat. Tento článek popisuje, jak načíst data z Amazon S3.

Data můžete načíst jedním z následujících způsobů:

* V uživatelském rozhraní Azure Data Factory v levém podokně vyberte ikonu **Autor** , jak je znázorněno v části Vytvoření datové továrny v tématu [Vytvoření datové továrny pomocí uživatelského rozhraní Azure Data Factory](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory).
* V nástroji Azure Data Factory Kopírování dat, jak je popsáno v části [použití nástroje kopírování dat ke kopírování dat](/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

### <a name="copy-data-from-amazon-s3-source"></a>Kopírovat data z Amazon S3 (zdroj)

1. V podokně **Začínáme** otevřete nástroj kopírování dat tak, že vyberete možnost **kopírování dat**.

   ![Tlačítko nástroje Kopírování dat](media/data-factory-load-data/copy-data-tool-tile.png)

1. V podokně **vlastnosti** zadejte do pole **název úlohy** název a pak vyberte **Další**.

    ![Podokno vlastností Kopírování dat](media/data-factory-load-data/copy-from-source.png)

1. V podokně **zdrojové úložiště dat** vyberte **vytvořit nové připojení**.

    ![Podokno Kopírování dat zdrojové úložiště dat](media/data-factory-load-data/source-create-connection.png)

1. Vyberte **Amazon S3**a pak vyberte **pokračovat**.

    ![Podokno Nová propojená služba](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. V podokně **Nová propojená služba (Amazon S3)** udělejte toto:

    ![Zadat propojenou službu Amazon S3](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. Do pole **název** zadejte název nové propojené služby.

    b. V rozevíracím seznamu **připojit přes prostředí Integration runtime** vyberte hodnotu.

    c. Do pole **ID přístupového klíče** zadejte hodnotu.
    
    > [!NOTE]
    > V části Amazon S3 Najděte přístupový klíč, na navigačním panelu vyberte své uživatelské jméno Amazon a pak vyberte **Moje přihlašovací údaje zabezpečení**.
    
    d. Do pole **tajný přístupový klíč** zadejte hodnotu.

    e. Chcete-li otestovat připojení propojené služby, které jste vytvořili, vyberte možnost **Test připojení**.

    f. Vyberte **Finish** (Dokončit).
    
      V podokně **zdrojové úložiště dat** se zobrazí vaše nové připojení AmazonS31. 

1. Vyberte **Další**.

   ![Zdrojové úložiště dat vytvořené připojení](media/data-factory-load-data/source-data-store-created-connection.png)

1. V podokně **zvolit vstupní soubor nebo složku** proveďte následující kroky:

    a. Vyhledejte soubor nebo složku, které chcete zkopírovat, a pak ji vyberte.

    b. Vyberte chování kopírování, které chcete. Ujistěte se, že není zaškrtnuto políčko **binární kopírování** .

    c. Vyberte **Další**.

    ![Zvolte vstupní soubor nebo složku](media/data-factory-load-data/source-choose-input-file.png)

1. V podokně **Nastavení formátu souboru** vyberte odpovídající nastavení souboru. a pak vyberte **Další**.

   ![Podokno "nastavení formátu souboru"](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Kopírování dat do Azure Průzkumník dat (cíl)

Vytvoří se nová propojená služba Azure Průzkumník dat pro kopírování dat do cílové tabulky Azure Průzkumník dat (jímka), která je uvedená v této části.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Vytvoření propojené služby Azure Průzkumník dat

Pokud chcete vytvořit propojenou službu Azure Průzkumník dat, udělejte toto:

1. Chcete-li použít existující připojení úložiště dat nebo zadat nové úložiště dat, vyberte v podokně **cílové úložiště dat** možnost **vytvořit nové připojení**.

    ![Podokno cílové úložiště dat](media/data-factory-load-data/destination-create-connection.png)

1. V podokně **Nová propojená služba** vyberte **Azure Průzkumník dat**a pak vyberte **pokračovat**.

    ![Podokno Nová propojená služba](media/data-factory-load-data/adx-select-new-linked-service.png)

1. V podokně **Nová propojená služba (Azure Průzkumník dat)** udělejte toto:

    ![Podokno nové propojené služby Azure Průzkumník dat](media/data-factory-load-data/adx-new-linked-service.png)

   a. Do pole **název** zadejte název propojené služby Azure Průzkumník dat.

   b. V části **metoda výběru účtu**proveďte jednu z následujících akcí: 

    * Vyberte **z předplatného Azure** a potom v rozevíracích seznamech vyberte **předplatné Azure** a váš **cluster**. 

        > [!NOTE]
        > Rozevírací ovládací prvek **clusteru** obsahuje jenom clustery, které jsou přidružené k vašemu předplatnému.

    * Vyberte **zadat ručně**a potom zadejte svůj **koncový bod**.

   c. Do pole **tenant (tenant** ) zadejte název tenanta.

   d. Do pole **ID instančního objektu** zadejte ID instančního objektu.

   e. Vyberte **klíč instančního objektu** a potom do pole **klíč instančního objektu** zadejte hodnotu pro klíč.

   f. V rozevíracím seznamu **databáze** vyberte název databáze. Případně zaškrtněte políčko **Upravit** a zadejte název databáze.

   g. Chcete-li otestovat připojení propojené služby, které jste vytvořili, vyberte možnost **Test připojení**. Pokud se můžete připojit k propojené službě, v podokně se zobrazí zelená značka zaškrtnutí a zpráva o **úspěšném připojení** .

   h. Kliknutím na **Dokončit** dokončete vytvoření propojené služby.

    > [!NOTE]
    > Objekt služby se používá Azure Data Factory pro přístup ke službě Azure Průzkumník dat. Instanční objekt vytvoříte tak, že přejdete na [vytvořit objekt služby Azure Active Directory (Azure AD)](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Nepoužívejte metodu Azure Key Vault.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Konfigurace datového připojení Azure Průzkumník dat

Po vytvoření připojení propojené služby se otevře podokno **cílové úložiště dat** a připojení, které jste vytvořili, je k dispozici k použití. Chcete-li konfigurovat připojení, udělejte toto:

1. Vyberte **Další**.

    ![Podokno "cílové úložiště dat" Azure Průzkumník dat](media/data-factory-load-data/destination-data-store.png)

1. V podokně **mapování tabulky** nastavte název cílové tabulky a pak vyberte **Další**.

    ![Podokno cílová datová sada "mapování tabulky"](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. V podokně **mapování sloupců** proběhne následující mapování:

    a. První mapování provádí Azure Data Factory podle [mapování schématu Azure Data Factory](/azure/data-factory/copy-activity-schema-and-type-mapping). Udělejte toto:

    * Nastavte **mapování sloupců** pro cílovou tabulku Azure Data Factory. Výchozí mapování se zobrazí ze zdroje do cílové tabulky Azure Data Factory.

    * Zruší výběr sloupců, které nemusíte definovat mapování sloupce.

    b. Druhé mapování nastane, když se tato tabulková data ingestují do služby Azure Průzkumník dat. Mapování se provádí podle [pravidel mapování sdílených svazků clusteru](/azure/kusto/management/mappings#csv-mapping). I když zdrojová data nejsou ve formátu CSV, Azure Data Factory převádí data do tabulkového formátu. Proto je v této fázi pouze mapování sdílených svazků clusteru pouze relevantní. Udělejte toto:

    * Volitelné V části **vlastnosti jímky služby Azure Průzkumník dat (Kusto)** přidejte odpovídající **název mapování pro přijímání** , aby bylo možné použít mapování sloupce.

    * Pokud není zadán **název mapování** ingestování, použije se pořadí mapování *podle názvu* definované v oddílu **mapování sloupců** . Pokud mapování *podle názvu* selhává, Azure Průzkumník dat se pokusí ingestovat data v pořadí *podle sloupce* (to znamená, že se mapuje podle pozice jako výchozí).

    * Vyberte **Další**.

    ![Podokno "mapování sloupce cílové datové sady"](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. V podokně **Nastavení** proveďte následující akce:

    a. V části **Nastavení odolnosti proti chybám**zadejte relevantní nastavení.

    b. V části **Nastavení výkonu**se **možnost Povolit** neuplatní fázování a **Rozšířené nastavení** zahrnuje i náklady. Pokud nemáte žádné konkrétní požadavky, ponechte tato nastavení tak, jak je.

    c. Vyberte **Další**.

    ![Podokno nastavení kopírování dat](media/data-factory-load-data/copy-data-settings.png)

1. V podokně **Souhrn** zkontrolujte nastavení a pak vyberte **Další**.

    ![Podokno Souhrn dat kopírování](media/data-factory-load-data/copy-data-summary.png)

1. V podokně **dokončení nasazení** proveďte následující:

    a. Chcete-li přepnout na kartu **monitorování** a zobrazit stav kanálu (tj. průběh, chyby a tok dat), vyberte možnost **monitor**.

    b. Pokud chcete upravit propojené služby, datové sady a kanály, vyberte **Upravit kanál**.

    c. Kliknutím na **Dokončit** dokončete úlohu kopírování dat.

    ![Podokno nasazení je dokončené.](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si informace o [konektoru Azure Průzkumník dat](/azure/data-factory/connector-azure-data-explorer) v Azure Data Factory.
* Přečtěte si další informace o úpravách propojených služeb, datových sad a kanálů v [uživatelském rozhraní Data Factory](/azure/data-factory/quickstart-create-data-factory-portal).
* Přečtěte si o dotazech na [Azure Průzkumník dat](/azure/data-explorer/web-query-data) pro dotazování na data.
