---
title: Integrace dat pomocí Azure Data Factory a sdílení dat Azure
description: Kopírování, transformace a sdílení dat pomocí Azure Data Factory a sdílení dat Azure
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: fa424f7e1f5e1f885dd433b8abc8aae1dc1bc206
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97006139"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Integrace dat pomocí Azure Data Factory a sdílení dat Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V případě, že se zákazníci nastoupili na jejich moderní projekty datového skladu a analýzy, vyžadují nejen více dat, ale také lepší viditelnost svých dat napříč jejich datovými podstatami. Tato dílna komentáře, jak Azure Data Factory a sdílení dat Azure zjednodušuje integraci a správu dat v Azure. 

Aby bylo možné vytvořit komplexní pohled na vaše data, díky vylepšením, která jsou v Azure Data Factory, umožníte vašim inženýrům dat spolehlivě přinášet do podniku více dat a tím i větší hodnotu. Sdílená složka Azure vám umožní provádět sdílení firmy způsobem, který se řídí.

V této dílně použijete Azure Data Factory (ADF) k ingestování dat z Azure SQL Database do Azure Data Lake Storage Gen2 (ADLS Gen2). Jakmile budete data nakládat do jezera, budete je transformovat prostřednictvím mapování toků dat, nativní transformace služby Data Factory a zajímka do Azure synapse Analytics. Pak nasdílíte tabulku s transformovanými daty spolu s dalšími daty pomocí Azure Data Share. 

Data použitá v tomto testovacím prostředí jsou v New Yorku City taxislužby data. Pokud ho chcete importovat do databáze v SQL Database, Stáhněte si [soubor taxislužby-data BacPac](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac).

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

* **Azure SQL Database**: Pokud nemáte databázi SQL, přečtěte si, jak [vytvořit účet databáze SQL](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal) .

* **Azure Data Lake Storage Gen2 účet úložiště**: Pokud nemáte účet úložiště adls Gen2, přečtěte si, jak [vytvořit účet úložiště adls Gen2](../storage/common/storage-account-create.md).

* **Azure synapse Analytics**: Pokud nemáte Azure synapse Analytics, přečtěte si, jak [vytvořit instanci Azure synapse Analytics](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md).

* **Azure Data Factory**: Pokud jste ještě nevytvořili datovou továrnu, přečtěte si téma Jak [vytvořit datovou továrnu](./quickstart-create-data-factory-portal.md).

* **Sdílená složka Azure**: Pokud jste nevytvořili sdílenou složku, přečtěte si téma Jak [vytvořit sdílenou složku dat](../data-share/share-your-data.md#create-a-data-share-account).

## <a name="set-up-your-azure-data-factory-environment"></a>Nastavení Azure Data Factoryho prostředí

V této části se dozvíte, jak získat přístup k Azure Data Factory uživatelského prostředí (uživatelského rozhraní ADF) z Azure Portal. Jednou v uživatelském prostředí ADF nakonfigurujete pro každé úložiště dat, které používáme: Azure SQL DB, ADLS Gen2 a Azure synapse Analytics tři propojené služby.

V Azure Data Factory propojené služby definují informace o připojení k externím prostředkům. Azure Data Factory aktuálně podporuje více než 85 konektorů.

### <a name="open-the-azure-data-factory-ux"></a>Otevřete Azure Data Factory UX

1. Otevřete [Azure Portal](https://portal.azure.com) v Microsoft Edge nebo Google Chrome.
1. Pomocí panelu hledání v horní části stránky vyhledejte datové továrny.

    ![Portál 1](media/lab-data-flow-data-share/portal1.png)
1. Kliknutím na prostředek služby Data Factory otevřete okno prostředku.

    ![Portál 2](media/lab-data-flow-data-share/portal2.png)
1. Kliknutím na **vytvořit a monitorovat** otevřete uživatelské prostředí ADF. K UŽIVATELSKÉmu prostředí ADF se dá dostat i na adf.azure.com.

    ![Portál 3](media/lab-data-flow-data-share/portal3.png)
1. Budete přesměrováni na domovskou stránku uživatelského prostředí ADF. Tato stránka obsahuje seznam rychlých a výukových videí a odkazy na kurzy, které vám pomají přehled o konceptech Data Factory. Chcete-li začít s vytvářením obsahu, klikněte na ikonu tužky v levém bočním panelu.

    ![Konfigurace portálu](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database

1. Stránka pro vytváření obsahu je místo, kde můžete vytvářet prostředky služby Data Factory, jako jsou kanály, datové sady, toky dat, triggery a propojené služby. Pokud chcete vytvořit propojenou službu, klikněte na tlačítko **připojení** v pravém dolním rohu.

    ![Portál – konfigurace 2](media/lab-data-flow-data-share/configure2.png)
1. Na kartě připojení kliknutím na **Nový** přidejte novou propojenou službu.

    ![Portál – konfigurace 3](media/lab-data-flow-data-share/configure3.png)
1. První propojená služba, kterou nakonfigurujete, je databáze SQL Azure. Seznam úložišť dat můžete filtrovat pomocí panelu hledání. Klikněte na dlaždici **Azure SQL Database** a potom klikněte na pokračovat.

    ![Portál – konfigurace 4](media/lab-data-flow-data-share/configure-4.png)
1. V podokně Konfigurace databáze SQL zadejte jako název propojené služby položku ' SQLDB '. Zadejte přihlašovací údaje, aby se data Factory mohla připojit k vaší databázi. Pokud používáte ověřování SQL, zadejte název serveru, databázi, uživatelské jméno a heslo. Kliknutím na **Testovat připojení** můžete ověřit, jestli jsou informace o připojení správné. Po dokončení klikněte na **vytvořit** .

    ![Portál – konfigurace 5](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Vytvoření propojené služby Azure synapse Analytics

1. Pokud chcete přidat propojenou službu Azure synapse Analytics, opakujte stejný postup. Na kartě připojení klikněte na **Nový**. Vyberte dlaždici **Azure synapse Analytics** a klikněte na pokračovat.

    ![Portál – konfigurace 6](media/lab-data-flow-data-share/configure-6.png)
1. V podokně Konfigurace propojené služby zadejte jako název propojené služby ' SQLDW '. Zadejte přihlašovací údaje, aby se data Factory mohla připojit k vaší databázi. Pokud používáte ověřování SQL, zadejte název serveru, databázi, uživatelské jméno a heslo. Kliknutím na **Testovat připojení** můžete ověřit, jestli jsou informace o připojení správné. Po dokončení klikněte na **vytvořit** .

    ![Portál – konfigurace 7](media/lab-data-flow-data-share/configure-7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Vytvoření propojené služby Azure Data Lake Storage Gen2

1. Poslední propojená služba potřebná pro toto testovací prostředí je Azure Data Lake Storage Gen2.  Na kartě připojení klikněte na **Nový**. Vyberte dlaždici **Azure Data Lake Storage Gen2** a klikněte na pokračovat.

    ![Portál – konfigurace 8](media/lab-data-flow-data-share/configure8.png)
1. V podokně Konfigurace propojené služby zadejte jako název propojené služby ' ADLSGen2 '. Pokud používáte ověřování pomocí klíče účtu, vyberte účet úložiště ADLS Gen2 v rozevíracím seznamu **název účtu úložiště** . Kliknutím na **Testovat připojení** můžete ověřit, jestli jsou informace o připojení správné. Po dokončení klikněte na **vytvořit** .

    ![Portál – konfigurace 9](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Zapnout režim ladění toku dat

V části *transformuje data pomocí mapování toku dat* budete sestavovat toky dat s mapováním. Osvědčeným postupem při sestavování toků mapování dat je zapnout režim ladění, který vám umožní testovat logiku transformace v sekundách v aktivním clusteru Spark.

Pokud chcete zapnout ladění, klikněte na posuvník pro **ladění toku dat** na horním panelu pro vytváření. Po zobrazení dialogového okna pro potvrzení klikněte na OK. Spuštění clusteru bude trvat přibližně 5-7 minut. Pokračujte k ingestování *dat ze služby Azure SQL DB do adls Gen2 pomocí aktivity kopírování* během inicializace.

![Portál konfigurovat 10](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Ingestování dat pomocí aktivity kopírování

V této části vytvoříte kanál s aktivitou kopírování, která ingestuje jednu tabulku z databáze SQL Azure do účtu úložiště ADLS Gen2. Naučíte se, jak přidat kanál, nakonfigurovat datovou sadu a ladit kanál prostřednictvím uživatelského rozhraní ADF. Konfigurační vzor použitý v této části lze použít pro kopírování z relačního úložiště dat do úložiště dat založeného na souborech.

V Azure Data Factory kanál je logické seskupení aktivit, které dohromady provádějí úlohu. Aktivita definuje operaci, která se má provést na vašich datech. Datová sada odkazuje na data, která chcete použít v propojené službě.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Vytvoření kanálu s aktivitou kopírování

1. V podokně prostředky továrny kliknutím na ikonu Plus otevřete nabídku nový prostředek. Vyberte **kanál**.

    ![Kopie na portálu 1](media/lab-data-flow-data-share/copy1.png)
1. Na kartě **Obecné** na plátně kanálu pojmenujte svůj kanál s popisným názvem, jako je například "IngestAndTransformTaxiData".

    ![Kopie 2 portálu](media/lab-data-flow-data-share/copy2.png)
1. V podokně aktivity plátna kanálu otevřete možnost **přesunutí a transformace** a přetáhněte aktivitu **Kopírovat data** na plátno. Zadejte popisný název aktivity kopírování, například ' IngestIntoADLS '.

    ![Portál – kopie 3](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Konfigurace zdrojové datové sady Azure SQL DB

1. Klikněte na kartu **zdroj** aktivity kopírování. Chcete-li vytvořit novou datovou sadu, klikněte na tlačítko **Nový**. Zdroj bude tabulka dbo. TripData ' umístěný v ' SQLDB ' propojené službě ' ' byl nakonfigurován dříve.

    ![Kopie portálu 4](media/lab-data-flow-data-share/copy4.png)
1. Vyhledejte **Azure SQL Database** a klikněte na pokračovat.

    ![Kopie portálu 5](media/lab-data-flow-data-share/copy-5.png)
1. Zavolejte svou datovou sadu ' TripData '. Jako propojená služba vyberte SQLDB. Vyberte název tabulky dbo. TripData ' v rozevíracím seznamu název tabulky. Importujte schéma **z připojení/úložiště**. Po dokončení klikněte na OK.

    ![Kopie portálu 6](media/lab-data-flow-data-share/copy6.png)

Úspěšně jste vytvořili zdrojovou datovou sadu. Ujistěte se, že je v nastavení zdroje vybraná **tabulka** výchozí hodnota v poli použít dotaz.

### <a name="configure-adls-gen2-sink-dataset"></a>Konfigurovat datovou sadu jímky ADLS Gen2

1. Klikněte na kartu **jímka** aktivity kopírování. Chcete-li vytvořit novou datovou sadu, klikněte na tlačítko **Nový**.

    ![Kopie portálu 7](media/lab-data-flow-data-share/copy7.png)
1. Vyhledejte **Azure Data Lake Storage Gen2** a klikněte na pokračovat.

    ![Portál – kopie 8](media/lab-data-flow-data-share/copy8.png)
1. V podokně vybrat formát vyberte **DelimitedText** při zápisu do souboru CSV. Klikněte na pokračovat.

    ![Portál – kopie 9](media/lab-data-flow-data-share/copy9.png)
1. Pojmenujte datovou sadu jímky ' TripDataCSV '. Jako propojená služba vyberte ADLSGen2. Zadejte, kam chcete zapisovat do souboru CSV. Můžete například zapsat data do souboru `trip-data.csv` v kontejneru `staging-container` . Nastavte **první řádek jako záhlaví** na true, protože chcete, aby výstupní data měla záhlaví. Vzhledem k tomu, že v cíli ještě neexistuje žádný soubor, nastavte **schéma pro import** na **none**. Po dokončení klikněte na OK.

    ![Kopie na portálu 10](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>Testování aktivity kopírování pomocí ladicího běhu kanálu

1. Pokud chcete ověřit, jestli vaše aktivita kopírování funguje správně, klikněte na **ladit** v horní části plátna kanálu a spusťte ladění. Pomocí ladicího programu můžete svůj kanál před publikováním do služby Data Factory otestovat buď na konci, nebo do zarážky.

    ![Kopie portálu 11](media/lab-data-flow-data-share/copy11.png)
1. Pokud chcete monitorovat svůj ladicí běh, na plátně kanálu klikněte na kartu **výstup** . Obrazovka monitorování se znovu aktualizuje každých 20 sekund nebo ručně kliknutím na tlačítko Aktualizovat. Aktivita kopírování má speciální zobrazení monitorování, které může mít přístup kliknutím na ikonu oka-brýle ve sloupci **Akce** .

    ![Kopie portálu 12](media/lab-data-flow-data-share/copy12.png)
1. Zobrazení pro monitorování kopírování poskytuje podrobnosti o spuštění aktivity a výkonnostní charakteristiky. Můžete zobrazit informace, jako je čtení a zápis, řádky pro čtení/zápis, čtení a zapisování souborů a propustnost. Pokud jste všechno nakonfigurovali správně, měli byste vidět 49 999 řádků zapsaných do jednoho souboru v ADLS jímky.

    ![Kopie portálu 13](media/lab-data-flow-data-share/copy13.png)
1. Než přejdete k další části, doporučujeme publikovat změny do služby Data Factory kliknutím na **publikovat vše** v horním panelu továrny. I když se v tomto testovacím prostředí nezabývá, Azure Data Factory podporuje úplnou integraci Gitu. Integrace Git umožňuje správu verzí, iterativní ukládání do úložiště a spolupráci na datové továrně. Další informace naleznete v tématu [Správa zdrojového kódu v Azure Data Factory](./source-control.md#troubleshooting-git-integration).

    ![Publikování portálu 1](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Transformace dat s využitím toků dat mapování

Teď, když jste úspěšně zkopírovali data do Azure Data Lake Storage, je čas připojit a agregovat tato data do datového skladu. Použijeme mapování toku dat Azure Data Factory vizuálně navržený transformační službu. Mapování toků dat umožňuje uživatelům vyvíjet bez kódu transformaci a provádět je v clusterech Spark spravovaných službou ADF.

Tok dat, který byl vytvořen v tomto kroku, se doplní do datové sady ' TripDataCSV ' vytvořené v předchozí části s tabulkou ' dbo. TripFares ' uložený v ' SQLDB ' na základě čtyř klíčových sloupců. Pak se data sloučí na základě sloupce `payment_type` a vypočítá průměr určitých polí a napsaných v tabulce Azure synapse Analytics.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Přidání aktivity toku dat do kanálu

1. V podokně aktivity plátna kanálu otevřete možnost **přesunutí a transformace** a přetáhněte aktivitu **toku dat** na plátno.

    ![Tok dat portálu 1](media/lab-data-flow-data-share/dataflow1.png)
1. V bočním podokně, které se otevře, vyberte **vytvořit nový tok dat** a zvolte **mapování toku dat**. Klikněte na **OK**.

    ![Datový tok portálu 2](media/lab-data-flow-data-share/dataflow2.png)
1. Budete přesměrováni na plátno toku dat, kde budete vytvářet logiku transformace. Na kartě Obecné pojmenujte datový tok ' JoinAndAggregateData '.

    ![Datový tok portálu 3](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Konfigurace zdroje CSV pro data cest

1. První věc, kterou chcete udělat, je nakonfigurovat vaše dvě zdrojové transformace. První zdroj bude ukazovat na DelimitedText datovou sadu TripDataCSV. Pokud chcete přidat transformaci zdroje, klikněte na pole **Přidat zdroj** na plátně.

    ![Tok dat portálu 4](media/lab-data-flow-data-share/dataflow4.png)
1. Pojmenujte zdroj "TripDataCSV" a z rozevíracího seznamu zdroj vyberte datovou sadu "TripDataCSV". Pokud si pamatujete, při vytváření této datové sady jste neimportovali schéma, protože zde nejsou žádná data. Vzhledem `trip-data.csv` k tomu, že teď existuje, klikněte na **Upravit** a přejděte na kartu nastavení datové sady.

    ![Tok dat portálu 5](media/lab-data-flow-data-share/dataflow5.png)
1. Přejděte ke **schématu** tabulátoru a klikněte na **importovat schéma**. Vyberte možnost **připojení/obchod** pro import přímo z úložiště souborů. mělo by se zobrazit 14 sloupců typu řetězec.

    ![Tok dat portálu 6](media/lab-data-flow-data-share/dataflow6.png)
1. Vraťte se zpět do toku dat ' JoinAndAggregateData '. Pokud byl váš ladicí cluster spuštěn (označen zeleným kroužkem vedle posuvníku ladění), můžete získat snímek dat na kartě **Náhled dat** . Kliknutím na **aktualizovat** načtete náhled dat.

    ![Datový tok portálu 7](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> Data ve verzi Preview nezápisují data.

### <a name="configure-your-trip-fares-sql-db-source"></a>Konfigurace zdroje databáze SQL DB pro cestovní tarify

1. Druhý zdroj, který přidáváte, bude odkazovat na tabulku dbo databáze SQL. TripFares'. V rámci zdroje TripDataCSV bude k dispozici další pole **Přidat zdroj** . Kliknutím na něj přidejte novou zdrojovou transformaci.

    ![Tok dat portálu 8](media/lab-data-flow-data-share/dataflow8.png)
1. Pojmenujte tento zdroj ' TripFaresSQL '. Kliknutím na **Nový** vedle pole zdrojová datová sada vytvořte novou datovou sadu SQL DB.

    ![Datový tok portálu 9](media/lab-data-flow-data-share/dataflow9.png)
1. Vyberte dlaždici **Azure SQL Database** a klikněte na pokračovat. *Poznámka: Možná si všimnete, že mnoho konektorů ve službě Data Factory není v toku dat mapování podporováno. Pokud chcete transformovat data z jednoho z těchto zdrojů, ingestujte je do podporovaného zdroje pomocí aktivity kopírování*.

    ![Tok dat portálu 10](media/lab-data-flow-data-share/dataflow-10.png)
1. Zavolejte svou datovou sadu ' TripFares '. Jako propojená služba vyberte SQLDB. Vyberte název tabulky dbo. TripFares ' v rozevíracím seznamu název tabulky. Importujte schéma **z připojení/úložiště**. Po dokončení klikněte na OK.

    ![Datový tok portálu 11](media/lab-data-flow-data-share/dataflow11.png)
1. Chcete-li ověřit data, načtěte náhled dat na kartě **Náhled dat** .

    ![Tok dat portálu 12](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Vnitřní spojení TripDataCSV a TripFaresSQL

1. Chcete-li přidat novou transformaci, klikněte na ikonu Plus v pravém dolním rohu ' TripDataCSV '. V části **více vstupů/výstupů** vyberte **připojit**.

    ![Spojení s portálem 1](media/lab-data-flow-data-share/join1.png)
1. Pojmenujte transformaci JOIN ' InnerJoinWithTripFares '. V rozevíracím seznamu pravý datový proud vyberte ' TripFaresSQL '. Jako typ spojení vyberte **vnitřní** . Další informace o různých typech spojení v mapování toku dat najdete v tématu [typy spojení](./data-flow-join.md#join-types).

    V rozevíracím seznamu **podmínky připojení** vyberte, na kterých sloupcích si chcete u každého streamu souhlasit. Chcete-li přidat další podmínku spojení, klikněte na ikonu plus vedle existující podmínky. Ve výchozím nastavení jsou všechny podmínky spojení kombinovány s operátorem AND, což znamená, že všechny podmínky musí být splněny pro shodu. V tomto testovacím prostředí chceme odpovídat na sloupce `medallion` ,, `hack_license` `vendor_id` a. `pickup_datetime`

    ![Spojení s portálem 2](media/lab-data-flow-data-share/join2.png)
1. Ověřte, že jste úspěšně připojili 25 sloupců spolu s náhledem dat.

    ![Spojení s portálem 3](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Agregovat podle payment_type

1. Po dokončení transformace spojení přidejte agregovanou transformaci kliknutím na ikonu plus vedle položky ' InnerJoinWithTripFares. V části **Modifikátor schématu** vyberte **agregovat** .

    ![Portál AGG 1](media/lab-data-flow-data-share/agg1.png)
1. Pojmenujte svou agregovanou transformaci ' AggregateByPaymentType '. `payment_type`Jako sloupec Group by vyberte.

    ![Portál AGG 2](media/lab-data-flow-data-share/agg2.png)
1. Přejít na kartu **agregace** . Tady zadáte dvě agregace:
    * Průměrná tarify seskupené podle typu platby
    * Celková vzdálenost cest seskupená podle typu platby

    Nejprve vytvoříte výraz Průměrná tarify. Do textového pole s názvem **Přidat nebo vybrat sloupec** zadejte ' average_fare '.

    ![Portál AGG 3](media/lab-data-flow-data-share/agg3.png)
1. Chcete-li zadat agregační výraz, klikněte na modrý rámeček označený **výrazem ENTER**. Tím se otevře Tvůrce výrazů toku dat, který slouží k vizuálnímu vytváření výrazů toku dat pomocí vstupního schématu, integrovaných funkcí a operací a uživatelsky definovaných parametrů. Další informace o možnostech Tvůrce výrazů naleznete v [dokumentaci k tvůrci výrazů](./concepts-data-flow-expression-builder.md).

    K získání průměrného tarifu použijte agregační `avg()` funkci pro agregaci `total_amount` přetypování sloupce na celé číslo pomocí `toInteger()` . V jazyce výrazu toku dat je tato definice definována jako `avg(toInteger(total_amount))` . Až budete hotovi, klikněte na **Uložit a dokončit** .

    ![Portál AGG 4](media/lab-data-flow-data-share/agg4.png)
1. Chcete-li přidat další agregační výraz, klikněte na ikonu plus vedle pole `average_fare` . Vyberte **Přidat sloupec**.

    ![AGG na portálu 5](media/lab-data-flow-data-share/agg5.png)
1. Do textového pole s názvem **Přidat nebo vybrat sloupec** zadejte ' total_trip_distance '. Jako v posledním kroku otevřete Tvůrce výrazů, který zadáte do výrazu.

    K získání celkové vzdálenosti cest použijte `sum()` agregační funkci pro agregaci `trip_distance` přetypování sloupce na celé číslo pomocí `toInteger()` . V jazyce výrazu toku dat je tato definice definována jako `sum(toInteger(trip_distance))` . Až budete hotovi, klikněte na **Uložit a dokončit** .

    ![Portál AGG 6](media/lab-data-flow-data-share/agg6.png)
1. Otestujte logiku transformace na kartě **Náhled dat** . Jak vidíte, existuje mnohem méně řádků a sloupců než dříve. Pouze tři skupiny podle a agregované sloupce definované v této transformaci budou pokračovat pro příjem dat. Jak je v ukázce jenom pět skupin typů plateb, vycházejí jenom pět řádků.

    ![Portál AGG 7](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Konfigurace jímky Azure synapse Analytics

1. Teď, když jsme dokončili naši logiku transformace, jsme připraveni data zajímka do tabulky Azure synapse Analytics. Přidejte transformaci jímky do **cílového** oddílu.

    ![Jímka portálu 1](media/lab-data-flow-data-share/sink1.png)
1. Pojmenujte svou jímku ' SQLDWSink '. Klikněte na **Nový** vedle pole datová sada jímky a vytvořte novou datovou sadu Azure synapse Analytics.

    ![Jímka portálu 2](media/lab-data-flow-data-share/sink2.png)

1. Vyberte dlaždici **Azure synapse Analytics** a klikněte na pokračovat.

    ![Jímka portálu 3](media/lab-data-flow-data-share/sink-3.png)
1. Zavolejte svou datovou sadu ' AggregatedTaxiData '. Jako propojená služba vyberte SQLDW. Vyberte **vytvořit novou tabulku** a pojmenujte novou tabulku dbo. AggregateTaxiData. Po dokončení klikněte na OK.

    ![Jímka portálu 4](media/lab-data-flow-data-share/sink4.png)
1. Přejít na kartu **Nastavení** jímky. Vzhledem k tomu, že vytváříme novou tabulku, musíme v rámci akce tabulky vybrat možnost **znovu vytvořit tabulku** . Zrušte výběr možnosti **Povolit přípravu**, která přepíná, zda vkládáme řádek po řádku nebo v dávce.

    ![Jímka portálu 5](media/lab-data-flow-data-share/sink5.png)

Úspěšně jste vytvořili tok dat. Nyní je čas ho spustit v aktivitě kanálu.

### <a name="debug-your-pipeline-end-to-end"></a>Ladění kanálu od začátku do konce

1. Vraťte se na kartu pro kanál **IngestAndTransformData** . Všimněte si zeleného pole aktivity kopírování IngestIntoADLS. Přetáhněte ji na aktivitu toku dat ' JoinAndAggregateData '. Tím se vytvoří úspěch, který způsobí, že se aktivita toku dat spustí jenom v případě, že je kopie úspěšná.

    ![Kanál portálu 1](media/lab-data-flow-data-share/pipeline1.png)
1. Stejně jako u aktivity kopírování klikněte na **ladit** a spusťte ladění. Pro spuštění ladění bude aktivita toku dat používat aktivní ladicí cluster namísto rozběhu nového clusteru. Spuštění tohoto kanálu bude trvat trochu déle než minutu.

    ![Kanál portálu 2](media/lab-data-flow-data-share/pipeline2.png)
1. Podobně jako u aktivity kopírování má tok dat speciální zobrazení monitorování, ke kterému přistupovala ikona brýlí po dokončení aktivity.

    ![Kanál portálu 3](media/lab-data-flow-data-share/pipeline3.png)
1. V zobrazení monitorování můžete zobrazit zjednodušený graf toku dat spolu s časy spuštění a řádky v jednotlivých fázích spuštění. V případě správného provedení byste měli v této aktivitě agregovat řádky 49 999 na pět řádků.

    ![Kanál portálu 4](media/lab-data-flow-data-share/pipeline4.png)
1. Kliknutím na transformaci můžete získat další podrobnosti o jejím spuštění, jako jsou informace o dělení a nové/aktualizované/vyřazené sloupce.

    ![Kanál portálu 5](media/lab-data-flow-data-share/pipeline5.png)

Právě jste dokončili část Data Factory tohoto testovacího prostředí. Pokud je chcete zprovoznění s triggery, publikujte prostředky. Úspěšně jste spustili kanál, který ingestoval data z Azure SQL Database, aby Azure Data Lake Storage pomocí aktivity kopírování a pak agreguje tato data do analýzy Azure synapse. Data byla úspěšně zapsána tak, že si prohlížíte SQL Server sebe sama.

## <a name="share-data-using-azure-data-share"></a>Sdílení dat s využitím Azure Data Share

V této části se dozvíte, jak nastavit novou sdílenou složku pomocí Azure Portal. Tato akce zahrnuje vytvoření nové sdílené složky, která bude obsahovat datové sady z Azure Data Lake Store Gen2 a Azure synapse Analytics. Potom nakonfigurujete plán snímků, který poskytne spotřebitelům dat možnost automaticky aktualizovat data, která jsou s nimi sdílená. Potom budete pozvat příjemce do sdílené složky dat. 

Jakmile vytvoříte sdílenou složku dat, přepnete Hats a stane se *příjemcem dat*. Jako příjemce dat provedete postup přijetí pozvánky ke sdílení dat a nakonfigurujete, kam chcete data přijímat a mapování datových sad na jiná umístění úložiště. Potom budete aktivovat snímek, který bude kopírovat data, která s vámi sdílí, do zadaného cíle. 

### <a name="sharing-data-data-provider-flow"></a>Sdílení dat (tok Zprostředkovatel dat)

1. Otevřete Azure Portal v Microsoft Edge nebo Google Chrome.

1. Pomocí panelu hledání v horní části stránky vyhledejte **sdílené složky dat** .

    ![Inzeráty na portál](media/lab-data-flow-data-share/portal-ads.png)

1. V názvu vyberte účet pro sdílení dat se zprostředkovatelem. Například **DataProvider0102**. 

1. Vyberte možnost **začít sdílet data** .

    ![Zahájit sdílení](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Výběrem **+ vytvořit** spusťte konfiguraci nového sdílení dat. 

1. V části **název sdílené složky** zadejte název dle svého výběru. Jedná se o název sdílené složky, který uvidí váš příjemce dat, a nezapomeňte mu dát popisný název, třeba TaxiData.

1. V části **Popis** zadejte větu, která popisuje obsah sdílené složky dat. Sdílená data budou obsahovat taxislužby data o cestách, která jsou uložená v několika obchodech, včetně Azure synapse Analytics a Azure Data Lake Store. 

1. V části **podmínky použití** zadejte sadu podmínek, na které se má váš příjemce dat řídit. Mezi příklady patří "Nedistribuovat tato data mimo vaši organizaci" nebo "odkazování na právní smlouvu". 

    ![Sdílet podrobnosti](media/lab-data-flow-data-share/ads-details.png)

1. Vyberte **Pokračovat**. 

1. Vyberte **přidat datové sady** . 

    ![Přidat datovou sadu 1](media/lab-data-flow-data-share/add-dataset.png)

1. Vyberte **Azure synapse Analytics** a vyberte tabulku z Azure synapse Analytics, na kterou se proložily vaše transformace ADF.

    ![Přidat datovou sadu SQL](media/lab-data-flow-data-share/add-dataset-sql.png)


1. Abyste mohli pokračovat, budete mít k skript, který se má spustit. Zadaný skript vytvoří uživatele v databázi SQL, který umožní vašemu jménem sdílet službu Azure Data Shares MSI. 

> [!IMPORTANT]
> Před spuštěním skriptu je nutné nastavit sebe jako správce služby Active Directory pro SQL Server. 

1. Otevřete novou kartu a přejděte na Azure Portal. Zkopírujte zadaný skript pro vytvoření uživatele v databázi, ze které chcete sdílet data. Provedete to tak, že se přihlásíte do databáze podnikového pomocí aplikace Query Explorer (Preview) pomocí ověřování AAD. 

    Budete muset skript upravit tak, aby vytvořený uživatel byl obsažený v závorkách. EG
    
    vytvořit uživatele [DataProvider-xxxx] z externího přihlášení;  Exec sp_addrolemember db_owner, [DataProvider-xxxx];
    
1. Přepněte zpátky na sdílenou složku Azure, do které jste do své sdílené složky přidali datové sady. 

1. Vyberte **podnikového** a pak pro tabulku vyberte **AggregatedTaxiData** . 

1. Vybrat možnost **Přidat datovou sadu**

    Teď máme tabulku SQL, která je součástí naší datové sady. V dalším kroku přidáme další datové sady z Azure Data Lake Store. 

1. Vyberte **Přidat datovou sadu** a vyberte **Azure Data Lake Store Gen2**

    ![Přidat datovou sadu adls](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Vyberte **Další**.

1. Rozbalte *wwtaxidata*. Rozbalte položku *Boston taxislužby data*. Všimněte si, že můžete sdílet až na úrovni souboru. 

1. Výběrem složky *dat Boston taxislužby* přidejte celou složku do sdílené složky dat. 

1. Vyberte **přidat datové sady** .

1. Zkontrolujte datové sady, které byly přidány. Do sdílené složky dat byste měli mít přidanou tabulku SQL a složku ADLS Gen2. 

1. Vyberte **Pokračovat**.

1. Na této obrazovce můžete přidat příjemce do sdílené složky dat. Příjemci, které přidáte, budou dostávat pozvánky do sdílené složky dat. Pro účely tohoto testovacího prostředí musíte přidat dvě e-mailové adresy:

    1. E-mailová adresa předplatného Azure, na které jste. 

        ![Přidání příjemců](media/lab-data-flow-data-share/add-recipients.png)

    1. Přidejte do fiktivního příjemce dat s názvem *janedoe@fabrikam.com* .

1. Na této obrazovce můžete nakonfigurovat nastavení snímku pro vašeho příjemce dat. To jim umožní získávat pravidelné aktualizace vašich dat v intervalu, který jste definovali. 

1. Pomocí rozevíracího seznamu *opakování* proveďte kontrolu **plánu snímků** a nakonfigurujte hodinovou aktualizaci dat.  

1. Vyberte **Vytvořit**.

    Teď máte aktivní sdílení dat. Umožňuje zkontrolovat, co můžete vidět jako poskytovatele dat při vytváření sdílené složky dat. 

1. Vyberte sdílenou složku dat, kterou jste vytvořili, s názvem **DataProvider**. Můžete na ni přejít tak, že vyberete **odeslané sdílené složky** ve **sdílení dat**. 

1. Klikněte na plán snímku. Plán snímků můžete zakázat, pokud zvolíte. 

1. Potom vyberte kartu **datové sady** . Po vytvoření můžete do této sdílené složky přidat další datové sady. 

1. Vyberte kartu **sdílet odběry** . Zatím neexistují žádná předplatná sdílení, protože váš příjemce dat ještě vaši pozvánku nepřijal.

1. Přejděte na kartu **pozvánky** . Tady se zobrazí seznam nevyřízených pozvání. 

    ![Nevyřízené pozvánky](media/lab-data-flow-data-share/pending-invites.png)

1. Vyberte pozvánku na *janedoe@fabrikam.com* . Vyberte Odstranit. Pokud váš příjemce tuto pozvánku ještě nepřijal, už je nebude moct dělat. 

1. Vyberte kartu **Historie** . Nic se nezobrazuje, protože váš příjemce dat ještě nepřijal vaši pozvánku a aktivoval snímek. 

### <a name="receiving-data-data-consumer-flow"></a>Příjem dat (tok data Consumer)

Teď, když jsme zkontrolovali naši sdílenou složku dat, jsme připraveni přepnout kontext a nosit náš datový klobouk. 

Ve vaší doručené poště byste teď měli mít k dispozici pozvánku ke sdílení dat Azure z Microsoft Azure. Spusťte Outlook Web Access (outlook.com) a přihlaste se pomocí přihlašovacích údajů zadaných pro vaše předplatné Azure.

V e-mailu, který byste měli přijmout, klikněte na Zobrazit > pozvánky. V tuto chvíli budete simulovat uživatelské prostředí dat při přijímání pozvánky zprostředkovatelů dat ke sdílení dat. 

![E-mailová pozvánka](media/lab-data-flow-data-share/email-invite.png)

Může se zobrazit výzva k výběru předplatného. Ujistěte se, že jste vybrali předplatné, ve kterém jste v tomto testovacím prostředí pracovali. 

1. Klikněte na pozvánku s názvem *DataProvider*. 

1. Na této obrazovce pozvánky si všimnete různých podrobností o sdílené složce dat, kterou jste nakonfigurovali dříve jako poskytovatel dat. Pokud je k dispozici, přečtěte si podrobnosti a přijměte podmínky použití.

1. Vyberte předplatné a skupinu prostředků, které už pro testovací prostředí existují. 

1. V případě **účtu sdílení dat** vyberte **DataConsumer**. Můžete také vytvořit nový účet pro sdílení dat. 

1. Vedle **položky přijatý název sdílené složky** si všimnete, že výchozí název sdílené složky je název, který byl zadán poskytovatelem dat. Poskytněte sdílené složce popisný název, který popisuje data, která se chystáte přijmout, například **TaxiDataShare**.

    ![Pozvánka je přijímána](media/lab-data-flow-data-share/consumer-accept.png)

1. Můžete zvolit, že se má **přijmout a nakonfigurovat** , nebo **přijmout a nakonfigurovat později**. Pokud se rozhodnete přijmout a nakonfigurovat teď, zadejte účet úložiště, do kterého se mají kopírovat všechna data. Pokud se rozhodnete přijmout a nakonfigurovat později, datové sady ve sdílené složce se odmapují a budete je muset ručně namapovat. Později vám to vyžádáme. 

1. Vyberte **přijmout a konfigurovat později**. 

    Při konfiguraci této možnosti se vytvoří předplatné Shared, ale pro data, která se mají namapovat, se nikde. 

    Dále nakonfigurujeme mapování datových sad pro sdílenou složku. 

1. Vyberte přijatou sdílenou složku (název, který jste zadali v kroku 5).

    **Snímek triggeru** je šedý, ale sdílená složka je aktivní. 

1. Vyberte kartu **datové sady** . Všimněte si, že každá datová sada je nemapovaná, což znamená, že nemá žádné místo určení pro kopírování dat. 

    ![nemapované datové sady](media/lab-data-flow-data-share/unmapped.png)

1. Vyberte tabulku Azure synapse Analytics a pak vyberte **+ Mapa k cíli**.

1. Na pravé straně obrazovky vyberte rozevírací seznam **cílový datový typ** . 

    Data SQL můžete mapovat na široké spektrum úložišť dat. V tomto případě budeme mapování na Azure SQL Database.

    ![mapování](media/lab-data-flow-data-share/mapping-options.png)
    
    Volitelné Jako cílový datový typ vyberte **Azure Data Lake Store Gen2** . 
    
    Volitelné Vyberte předplatné, skupinu prostředků a účet úložiště, ve kterém jste pracovali. 
    
    Volitelné Data můžete do data Lake přijmout buď v souboru CSV, nebo ve formátu Parquet. 

1. V poli **cílový datový typ** vyberte Azure SQL Database. 

1. Vyberte předplatné, skupinu prostředků a účet úložiště, ve kterém jste pracovali. 

    ![mapovat na SQL](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Než budete moct pokračovat, budete muset vytvořit nového uživatele v SQL Server spuštěním zadaného skriptu. Nejdřív zkopírujte skript, který jste zadali do schránky. 

1. Otevřete novou kartu Azure Portal. Nezavírejte stávající kartu, protože se k ní budete muset později vrátit. 

1. Na nové kartě, kterou jste otevřeli, přejděte na **databáze SQL**.

1. Vyberte databázi SQL (v předplatném by měla být jen jedna). Dejte pozor, abyste nevybrali datový sklad. 

1. Výběr **Editoru dotazů (Preview)**

1. K přihlášení do editoru dotazů použijte ověřování AAD. 

1. Spusťte dotaz, který jste zadali ve sdílené složce dat (zkopírované do schránky v kroku 14). 

    Tento příkaz umožňuje službě sdílení dat Azure používat spravované identity pro služby Azure k ověření SQL Server, aby bylo možné do ní kopírovat data. 

1. Vraťte se na původní kartu a vyberte **mapovat na cíl**.

1. Potom vyberte složku Azure Data Lake Gen2, která je součástí datové sady, a namapujte ji na účet Azure Blob Storage. 

    ![úložiště](media/lab-data-flow-data-share/storage-map.png)

    U všech datových sad, které jste namapovali, jste teď připraveni začít přijímat data od poskytovatele dat. 

    ![mapovaný](media/lab-data-flow-data-share/all-mapped.png)
    
1. Vyberte **Podrobnosti**. 

    Všimněte si, že **snímek triggeru** už není šedý, protože sdílená složka má teď cíle na kopírování.

1. Vyberte aktivační snímek > úplné kopírování. 

    ![signálu](media/lab-data-flow-data-share/trigger-full.png)

    Tím se začnou kopírovat data do nového účtu pro sdílení dat. Ve scénáři reálného světa budou tato data přijít od třetí strany. 

    Bude trvat přibližně 3-5 minut, než se data dostanou napříč. Průběh můžete sledovat kliknutím na kartu **Historie** . 

    Až budete čekat, přejděte do původní sdílené složky (DataProvider) a zobrazte stav karty **sdílet odběry** a **historii** . Všimněte si, že teď existuje aktivní předplatné a jako poskytovatel dat můžete monitorovat také, kdy uživatel dat začal přijímat data, která s nimi sdílí. 

1. Přejděte zpátky do sdílené složky dat příjemce dat. Jakmile je stav triggeru úspěšný, přejděte do cílové databáze SQL a data Lake, abyste viděli, že se data vyložila v příslušných úložištích. 

Blahopřejeme, dokončili jste testovací prostředí.