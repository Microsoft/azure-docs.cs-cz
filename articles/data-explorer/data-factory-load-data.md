---
title: Kopírování dat z Azure Data Factory do Průzkumníka dat Azure
description: V tomto článku se dozvíte, jak ingestovat (načíst) data do Průzkumníka dat Azure pomocí nástroje pro kopírování Azure Data Factory.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71300600"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Kopírování dat do Azure Data Exploreru pomocí Azure Data Factory 

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat. Nabízí analýzu v reálném čase na velkých objemech dat, která streamují z mnoha zdrojů, jako jsou aplikace, weby a zařízení IoT. S Azure Data Explorer můžete iterativně zkoumat data a identifikovat vzory a anomálie za účelem zlepšení produktů, zlepšení zkušeností zákazníků, monitorování zařízení a zvýšení provozu. Pomáhá vám prozkoumat nové otázky a získat odpovědi během několika minut. 

Azure Data Factory je plně spravovaná cloudová služba pro integraci dat. Můžete ji použít k naplnění databáze Průzkumníka dat Azure dat y daty z vašeho stávajícího systému. Může vám pomoci ušetřit čas při vytváření analytických řešení.

Při načítání dat do Průzkumníka dat Azure, Data Factory poskytuje následující výhody:

* **Snadné nastavení:** Získejte intuitivního pětistupňového průvodce bez nutnosti skriptování.
* **Bohatá podpora úložiště dat:** Získejte integrovanou podporu pro bohatou sadu místních a cloudových úložišť dat. Podrobný seznam naleznete v tabulce [Podporovaná úložiště dat](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats).
* **Zabezpečené a kompatibilní**: Data se přenášejí přes PROTOKOL HTTPS nebo Azure ExpressRoute. Globální přítomnost služby zajišťuje, že vaše data nikdy neopustí geografickou hranici.
* **Vysoký výkon**: Rychlost načítání dat je až 1 gigabajt za sekundu (BPS) do Průzkumníka dat Azure. Další informace naleznete v [tématu Kopírování výkonu aktivity](/azure/data-factory/copy-activity-performance).

V tomto článku použijete nástroj Data Factory Copy Data k načtení dat ze služby Amazon Simple Storage Service (S3) do Průzkumníka dat Azure. Můžete sledovat podobný proces kopírovat data z jiných úložišť dat, například:
* [Úložiště objektů blob Azure](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [Systém souborů](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Cluster a databáze Průzkumníka dat Azure](create-cluster-database-portal.md).
* Zdroj dat.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Přihlaste se k [portálu Azure](https://ms.portal.azure.com).

1. V levém podokně vyberte Vytvořit**datovou továrnu****analytics** >  **zdrojů** > .

   ![Vytvoření datové továrny na webu Azure Portal](media/data-factory-load-data/create-adf.png)

1. V podokně **Nová továrna dat** zadejte hodnoty pro pole v následující tabulce:

   ![Podokno Nová továrna na data](media/data-factory-load-data/my-new-data-factory.png)  

   | Nastavení  | Hodnota, která má být zadávána  |
   |---|---|
   | **Název** | Do pole zadejte globálně jedinečný název datové továrny. Pokud se zobrazí chyba, *název \"datové továrny LoadADXDemo\" není k dispozici*, zadejte jiný název pro datové továrny. Pravidla týkající se pojmenování artefaktů datové továrny naleznete v tématu [Pravidla pojmenování datové továrny](/azure/data-factory/naming-rules).|
   | **Předplatné** | V rozevíracím seznamu vyberte předplatné Azure, ve kterém chcete vytvořit datové továrny. |
   | **Skupina prostředků** | Vyberte **Vytvořit nový**a zadejte název nové skupiny prostředků. Pokud již skupinu prostředků máte, vyberte **Použít existující**. |
   | **Verze** | V rozevíracím seznamu vyberte **V2**. |  
   | **Umístění** | V rozevíracím seznamu vyberte umístění pro datovou továrnu. V seznamu jsou zobrazena pouze podporovaná umístění. Úložiště dat, která používá továrna na data, mohou existovat v jiných umístěních nebo oblastech. |

1. Vyberte **Vytvořit**.

1. Chcete-li sledovat proces vytváření, vyberte na panelu nástrojů **možnost Oznámení.** Po vytvoření datové továrny ji vyberte.
   
   Otevře se podokno **Data Factory.**

   ![Podokno Data Factory](media/data-factory-load-data/data-factory-home-page.png)

1. Chcete-li aplikaci otevřít v samostatném podokně, vyberte dlaždici **Author & Monitor.**

## <a name="load-data-into-azure-data-explorer"></a>Načítání dat do Průzkumníka dat Azure

Do Průzkumníka dat Azure můžete načíst data z mnoha typů [úložišť dat.](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) Tento článek popisuje, jak načíst data z Amazon S3.

Data můžete načíst jedním z následujících způsobů:

* V uživatelském rozhraní Azure Data Factory v levém podokně vyberte ikonu **Autor,** jak je znázorněno v části Vytvořit datovou továrnu v části [Vytvořit datovou továrnu pomocí uživatelského rozhraní Azure Data Factory](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory).
* V nástroji Azure Data Factory Copy Data, jak je znázorněno v [nástroji Kopírovat data pomocí nástroje Kopírovat data ke kopírování dat](/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

### <a name="copy-data-from-amazon-s3-source"></a>Kopírování dat z Amazon S3 (zdroj)

1. V podokně **Začínáme** otevřete nástroj Kopírovat data výběrem **možnosti Kopírovat data**.

   ![Tlačítko Kopírovat data](media/data-factory-load-data/copy-data-tool-tile.png)

1. V podokně **Vlastnosti** zadejte do pole **Název úkolu** název a pak vyberte **Další**.

    ![Podokno Kopírovat vlastnosti dat](media/data-factory-load-data/copy-from-source.png)

1. V podokně **Úložiště zdrojových dat** vyberte **Vytvořit nové připojení**.

    ![Podokno Kopírovat data "Úložiště zdrojových dat"](media/data-factory-load-data/source-create-connection.png)

1. Vyberte **Amazon S3**a pak vyberte **Pokračovat**.

    ![Podokno Nová propojená služba](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. V podokně **Nová propojená služba (Amazon S3)** postupujte takto:

    ![Zadejte amazons3 propojené služby](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. Do pole **Název** zadejte název nové propojené služby.

    b. V rozevíracím seznamu **Připojit pomocí integračního běhu** vyberte hodnotu.

    c. Do pole **ID přístupového klíče** zadejte hodnotu.
    
    > [!NOTE]
    > Chcete-li v aplikaci Amazon S3 vyhledat přístupový klíč, vyberte uživatelské jméno uživatele amazonky na navigačním panelu a pak vyberte **moje bezpečnostní pověření**.
    
    d. Do pole **Tajný přístupový klíč** zadejte hodnotu.

    e. Chcete-li otestovat připojení propojené služby, které jste vytvořili, vyberte **možnost Testovat připojení**.

    f. Vyberte **Finish** (Dokončit).
    
      Podokno **úložiště zdrojových dat** zobrazuje nové připojení AmazonS31. 

1. Vyberte **další**.

   ![Vytvořeno připojení úložiště zdrojových dat](media/data-factory-load-data/source-data-store-created-connection.png)

1. V **podokně Vybrat vstupní soubor nebo složku** postupujte takto:

    a. Přejděte k souboru nebo složce, kterou chcete zkopírovat, a vyberte ji.

    b. Vyberte požadované chování kopírování. Ujistěte se, že binární **kopie** zaškrtnutí políčka není zaškrtnuto.

    c. Vyberte **další**.

    ![Zvolte vstupní soubor nebo složku](media/data-factory-load-data/source-choose-input-file.png)

1. V podokně **Nastavení formátu souboru** vyberte příslušná nastavení souboru. a pak vyberte **Další**.

   ![Podokno Nastavení formátu souboru](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Kopírování dat do Průzkumníka dat Azure (cíl)

Nová propojená služba Azure Data Explorer se vytvoří pro kopírování dat do cílové tabulky (jímky) aplikace Azure Data Explorer, která je určena v této části.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Vytvoření propojené služby Azure Data Explorer

Chcete-li vytvořit propojenou službu Azure Data Explorer, postupujte takto.

1. Chcete-li použít existující připojení k úložišti dat nebo zadat nové úložiště dat, vyberte v podokně **Cílové úložiště dat** možnost Vytvořit nové **připojení**.

    ![Podokno úložiště cílových dat](media/data-factory-load-data/destination-create-connection.png)

1. V podokně **Nová propojená služba** vyberte **Průzkumník dat Azure**a pak vyberte **Pokračovat**.

    ![Podokno Nové propojené služby](media/data-factory-load-data/adx-select-new-linked-service.png)

1. V podokně **Nová propojená služba (Průzkumník dat Azure)** postupujte takto:

    ![Podokno Nové propojené služby Průzkumníka dat Azure](media/data-factory-load-data/adx-new-linked-service.png)

   a. Do pole **Název** zadejte název propojené služby Azure Data Explorer.

   b. V části **Metoda výběru účtu**proveďte jednu z následujících akcí: 

    * Vyberte **Z předplatného Azure** a pak v rozevíracích seznamech vyberte předplatné **Azure** a **cluster**. 

        > [!NOTE]
        > Ovládací **prvek clusteru** obsahuje pouze clustery, které jsou přidruženy k vašemu předplatnému.

    * Vyberte **Zadat ručně**a zadejte **koncový bod**.

   c. Do pole **Tenant** zadejte název klienta.

   d. Do pole **ID instančního objektu** zadejte ID instančního objektu.

   e. Vyberte **hlavní povinný klíč servisu** a pak do pole **klíč instanční ho** zadejte hodnotu klíče.

   f. V rozevíracím seznamu **Databáze** vyberte název databáze. Případně zaškrtněte políčko **Upravit** a zadejte název databáze.

   g. Chcete-li otestovat připojení propojené služby, které jste vytvořili, vyberte **možnost Testovat připojení**. Pokud se můžete připojit k propojené službě, zobrazí se v podokně zelená značka zaškrtnutí a zpráva **O připojení byla úspěšná.**

   h. Vyberte **Dokončit,** chcete-li dokončit vytvoření propojené služby.

    > [!NOTE]
    > Instanční objekt služby používá Azure Data Factory pro přístup ke službě Azure Data Explorer. Pokud chcete vytvořit instanční objekt, přejděte k [vytvoření instančního objektu služby Azure Active Directory (Azure AD).](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal) Nepoužívejte metodu Azure Key Vault.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Konfigurace datového připojení Azure Data Explorer

Po vytvoření připojeného připojení služby se otevře podokno **Cílové úložiště dat** a vytvořené připojení bude k dispozici pro použití. Chcete-li připojení nakonfigurovat, postupujte takto:

1. Vyberte **další**.

    ![Podokno "Cílové úložiště dat" v Průzkumníku dat Azure](media/data-factory-load-data/destination-data-store.png)

1. V podokně **mapování tabulky** nastavte název cílové tabulky a pak vyberte **Další**.

    ![Cílové podokno mapování dat](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. V podokně **mapování sloupců** probíhá následující mapování:

    a. První mapování provádí Azure Data Factory podle [mapování schématu Azure Data Factory](/azure/data-factory/copy-activity-schema-and-type-mapping). Udělejte toto:

    * Nastavte **mapování sloupců** pro cílovou tabulku Azure Data Factory. Výchozí mapování se zobrazí ze zdroje do cílové tabulky Azure Data Factory.

    * Zrušte výběr sloupců, které není nutné definovat mapování sloupců.

    b. Druhé mapování nastane, když se tato tabulková data ingestuje do Průzkumníka dat Azure. Mapování se provádí podle [pravidel mapování CSV](/azure/kusto/management/mappings#csv-mapping). I v případě, že zdrojová data nejsou ve formátu CSV, Azure Data Factory převede data do tabulkového formátu. Mapování CSV je proto pouze relevantní mapování v této fázi. Udělejte toto:

    * (Nepovinné) V části **Vlastnosti jímky Průzkumníka dat Azure (Kusto)** přidejte příslušný **název mapování ingestování,** aby bylo možné použít mapování sloupců.

    * Pokud není zadán **název mapování ingestování,** bude použito pořadí mapování *podle názvu,* které je definováno v části **Mapování sloupců.** Pokud se mapování *názvů* nezdaří, Azure Data Explorer se pokusí ingestovat data v pořadí pozice *podle sloupce* (to znamená, že mapuje podle pozice jako výchozí).

    * Vyberte **další**.

    ![Cílové podokno mapování sloupců](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. V podokně **Nastavení** postupujte takto:

    a. V části **Nastavení odolnosti proti chybám**zadejte příslušná nastavení.

    b. V části **Nastavení výkonu**se nepoužije **možnost Povolit pracovní** nastavení a **Upřesnit nastavení** zahrnuje aspekty nákladů. Pokud nemáte žádné konkrétní požadavky, ponechte tato nastavení tak, jak jsou.

    c. Vyberte **další**.

    ![Podokno Kopírovat data "Nastavení"](media/data-factory-load-data/copy-data-settings.png)

1. V podokně **Souhrn** zkontrolujte nastavení a pak vyberte **Další**.

    ![Podokno Kopírovat data "Souhrn"](media/data-factory-load-data/copy-data-summary.png)

1. V podokně **Dokončení nasazení** postupujte takto:

    a. Pokud chcete přepnout na kartu **Monitor** a zobrazit stav kanálu (tj. průběh, chyby a tok dat), vyberte **Sledovat**.

    b. Pokud chcete upravit propojené služby, datové sady a kanály, vyberte **Upravit kanál**.

    c. Chcete-li dokončit úlohu kopírování dat, vyberte **Dokončit.**

    ![Podokno Nasazení dokončeno](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [konektoru Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer) v Azure Data Factory.
* Další informace o úpravách propojených služeb, datových sad a kanálů najdete v [ui. datové továrně](/azure/data-factory/quickstart-create-data-factory-portal).
* Přečtěte si o [dotazech Azure Data Explorer](/azure/data-explorer/web-query-data) pro dotazování dat.
