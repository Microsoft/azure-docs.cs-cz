---
title: Integrace dat pomocí Azure Data Factory a Azure Data Share
description: Kopírování, transformace a sdílení dat pomocí Azure Data Factory a Azure Data Share
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 7d453b2724c308e48366d653a51d9e6aa8e82c96
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415929"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Integrace dat pomocí Azure Data Factory a Azure Data Share

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Když se zákazníci pustí do svých moderních projektů datového skladu a analýzy, vyžadují nejen více dat, ale také větší přehled o svých datech v celém datovém kombi. Tento workshop se zabývá tím, jak vylepšení Azure Data Factory a Azure Data Share zjednodušují integraci a správu dat v Azure. 

Od povolení etl/elt bez kódu až po vytvoření komplexního zobrazení vašich dat, vylepšení v Azure Data Factory umožní vašim datovým inženýrům s jistotou přinést do vašeho podniku více dat, a tím i větší hodnotu. Azure Data Share vám umožní podnikat s obchodním sdílením řízeným způsobem.

V tomto workshopu použijete Azure Data Factory (ADF) k ingestování dat z databáze Azure SQL (SQL DB) do gen2 Azure Data Lake Storage (ADLS gen2). Jakmile data přistanete v jezeře, transformujete je pomocí toků mapových dat, nativní transformační služby datové továrny a ponoříte je do Azure Synapse Analytics (dříve SQL DW). Pak budete sdílet tabulku s transformovanými daty spolu s některými dalšími daty pomocí služby Azure Data Share. 

Data použitá v této laboratoři jsou newyorská taxidata. Chcete-li jej importovat do databáze Azure SQL, stáhněte si [soubor bacpac taxi-data](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac).

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

* **Azure SQL Database:** Pokud nemáte SQL DB, přečtěte si, jak [vytvořit účet SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)

* **Účet úložiště Azure Data Lake Storage Gen2**: Pokud nemáte účet úložiště ADLS Gen2, přečtěte si, jak [vytvořit účet úložiště ADLS Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

* **Azure Synapse Analytics (dříve SQL DW):** Pokud nemáte Azure Synapse Analytics (dříve SQL DW), přečtěte si, jak [vytvořit instanci Azure Synapse Analytics](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal).

* **Azure Data Factory**: Pokud jste nevytvořili továrnu na data, podívejte se, jak [vytvořit datovou továrnu](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).

* **Azure Data Share**: Pokud jste nevytvořili sdílenou složku dat, podívejte se, jak [vytvořit sdílenou složku dat](https://docs.microsoft.com/azure/data-share/share-your-data#create-a-data-share-account).

## <a name="set-up-your-azure-data-factory-environment"></a>Nastavení prostředí Azure Data Factory

V této části se dozvíte, jak získat přístup k uživatelskému prostředí Azure Data Factory (ADF UX) z webu Azure Portal. Jakmile budete v ADF UX, nakonfigurujete tři propojené služby pro každé úložiště dat, které používáme: Azure SQL DB, ADLS Gen2 a Azure Synapse Analytics.

V Azure Data Factory propojené služby definovat informace o připojení k externím prostředkům. Azure Data Factory v současné době podporuje více než 85 konektorů.

### <a name="open-the-azure-data-factory-ux"></a>Otevření uživatelského nastavení Azure Data Factory

1. Otevřete [portál Azure](https://portal.azure.com) v Microsoft Edge nebo Google Chrome.
1. Pomocí vyhledávacího panelu v horní části stránky vyhledejte možnost "Datové továrny"

    ![Portál](media/lab-data-flow-data-share/portal1.png)
1. Kliknutím na zdroj z továrny na data otevřete okno zdroje.

    ![Portál](media/lab-data-flow-data-share/portal2.png)
1. Kliknutím na **Author and Monitor** otevřete ADF UX. ADF UX lze také přistupovat na adf.azure.com.

    ![Portál](media/lab-data-flow-data-share/portal3.png)
1. Budete přesměrováni na domovskou stránku adf ux. Tato stránka obsahuje rychlé spuštění, instruktážní videa a odkazy na výukové programy, které se učí koncepty datových továrně. Chcete-li začít vytvářet, klikněte na ikonu tužky v levém bočním panelu.

    ![Portál](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL databáze

1. Stránka pro vytváření je místo, kde můžete vytvářet prostředky továrny na data, jako jsou kanály, datové sady, toky dat, aktivační události a propojené služby. Chcete-li vytvořit propojenou službu, klikněte na tlačítko **Připojení** v pravém dolním rohu.

    ![Portál](media/lab-data-flow-data-share/configure2.png)
1. Na kartě Připojení klikněte na **Nový** a přidejte novou propojenou službu.

    ![Portál](media/lab-data-flow-data-share/configure3.png)
1. První propojená služba, kterou nakonfigurujete, je Azure SQL DB. Vyhledávací panel můžete použít k filtrování seznamu úložiště dat. Klikněte na dlaždici **Azure SQL Database** a klikněte na pokračovat.

    ![Portál](media/lab-data-flow-data-share/configure4.png)
1. V konfiguračním podokně SQL DB zadejte jako název propojené služby příkaz SQLDB. Zadejte své přihlašovací údaje, aby se data factory mohla připojit k databázi. Pokud používáte ověřování SQL, zadejte název serveru, databázi, uživatelské jméno a heslo. Kliknutím na **tlačítko Testovat připojení**můžete ověřit správnost informací o připojení . Po dokončení klikněte na **Vytvořit.**

    ![Portál](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Vytvoření propojené služby Azure Synapse Analytics

1. Stejným postupem opakujte a přidejte propojenou službu Azure Synapse Analytics. Na kartě Připojení klikněte na **Nový**. Vyberte dlaždici **Azure Synapse Analytics (dříve SQL DW)** a klikněte na pokračovat.

    ![Portál](media/lab-data-flow-data-share/configure6.png)
1. V podokně konfigurace propojené služby zadejte jako název propojené služby název propojené služby ."SQLDW" jako název propojené služby. Zadejte své přihlašovací údaje, aby se data factory mohla připojit k databázi. Pokud používáte ověřování SQL, zadejte název serveru, databázi, uživatelské jméno a heslo. Kliknutím na **tlačítko Testovat připojení**můžete ověřit správnost informací o připojení . Po dokončení klikněte na **Vytvořit.**

    ![Portál](media/lab-data-flow-data-share/configure7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Vytvoření propojené služby Azure Data Lake Storage Gen2

1. Poslední propojená služba potřebná pro toto testovací prostředí je gen2 úložiště datového jezera Azure.  Na kartě Připojení klikněte na **Nový**. Vyberte dlaždici **Azure Data Lake Storage Gen2** a klikněte na pokračovat.

    ![Portál](media/lab-data-flow-data-share/configure8.png)
1. V podokně konfigurace propojené služby zadejte jako název propojené služby název propojené služby "ADLSGen2". Pokud používáte ověřování pomocí klíče účtu, vyberte svůj účet úložiště adls gen2 z rozevíracího přehledu **Název účtu úložiště.** Kliknutím na **tlačítko Testovat připojení**můžete ověřit správnost informací o připojení . Po dokončení klikněte na **Vytvořit.**

    ![Portál](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Zapnutí režimu ladění toku dat

V části *Transformace dat pomocí toku dat mapování*budete vytvářet toky mapových dat. Osvědčeným postupem před vytvářením mapování datových toků je zapnout režim ladění, který umožňuje testovat logiku transformace během několika sekund v aktivním clusteru spark.

Chcete-li ladění zapnout, klikněte na jezdec **ladění toku dat** v horní části řádku výroby. Při potvrzovacím dialogu klikněte na ok. Spuštění clusteru bude trvat asi 5-7 minut. Pokračujte na *ingestování dat z Azure SQL DB do ADLS gen2 pomocí aktivity kopírování* při inicializaci.

![Portál](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Ingestování dat pomocí aktivity kopírování

V této části vytvoříte kanál s aktivitou kopírování, která ingestuje jednu tabulku z Azure SQL DB do účtu úložiště ADLS gen2. Dozvíte se, jak přidat kanál, nakonfigurovat datovou sadu a ladit kanál prostřednictvím adf UX. Konfigurační vzor použitý v této části lze použít pro kopírování z úložiště relačních dat do úložiště dat založeného na souborech.

V Azure Data Factory kanálu je logické seskupení aktivit, které společně provést úkol. Aktivita definuje operaci, která má být s daty provedena. Datová sada odkazuje na data, která chcete použít v propojené službě.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Vytvoření kanálu s aktivitou kopírování

1. V podokně výrobních zdrojů otevřete novou nabídku prostředků kliknutím na ikonu plus. Vyberte **možnost Kanál**.

    ![Portál](media/lab-data-flow-data-share/copy1.png)
1. Na kartě **Obecné** na plátně kanálu pojmenujte svůj kanál něco popisného, například "IngestAndTransformTaxiData".

    ![Portál](media/lab-data-flow-data-share/copy2.png)
1. V podokně aktivit plátna kanálu otevřete akordeon **Move and Transform** a **přetáhněte** aktivitu Kopírovat data na plátno. Zadejte aktivitu kopírování popisným názvem, například "IngestIntoADLS".

    ![Portál](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Konfigurace zdrojové datové sady Azure SQL DB

1. Klikněte na kartu **Zdroj** aktivity kopírování. Chcete-li vytvořit novou datovou sadu, klepněte na tlačítko **Nový**. Váš zdroj bude tabulka 'dbo. TripData' umístěné v propojené službě 'SQLDB' nakonfigurované dříve.

    ![Portál](media/lab-data-flow-data-share/copy4.png)
1. Vyhledejte **Azure SQL Database** a klikněte na pokračovat.

    ![Portál](media/lab-data-flow-data-share/copy5.png)
1. Zavolejte do své datové sady "TripData". Jako propojenou službu vyberte "SQLDB". Vyberte název tabulky 'dbo. TripData' z rozbalovací tabulky s názvem. Import schématu **z připojení nebo úložiště**. Po dokončení klepněte na tlačítko OK.

    ![Portál](media/lab-data-flow-data-share/copy6.png)

Úspěšně jste vytvořili zdrojovou datovou sadu. Ujistěte se, že ve zdrojovém nastavení je v poli použít dotaz vybrána výchozí hodnota **Tabulka.**

### <a name="configure-adls-gen-2-sink-dataset"></a>Konfigurace datové sady jímky ADLS Gen 2

1. Klikněte na kartu **Dřez** aktivity kopírování. Chcete-li vytvořit novou datovou sadu, klepněte na tlačítko **Nový**.

    ![Portál](media/lab-data-flow-data-share/copy7.png)
1. Vyhledejte **Azure Data Lake Storage Gen2** a klikněte na pokračovat.

    ![Portál](media/lab-data-flow-data-share/copy8.png)
1. V podokně vybraného formátu vyberte při zápisu do souboru CSV text **Oddělovaný.** Klikněte na pokračovat.

    ![Portál](media/lab-data-flow-data-share/copy9.png)
1. Pojmenujte datovou sadu jímky "TripDataCSV". Jako propojenou službu vyberte "ADLSGen2". Zadejte, kam chcete soubor csv napsat. Můžete například zapsat data `trip-data.csv` do `staging-container`souboru v kontejneru . Nastavte **první řádek jako záhlaví** na hodnotu true, protože chcete, aby výstupní data měla záhlaví. Vzhledem k tomu, že v cílovém umístění dosud neexistuje žádný soubor, nastavte **schéma importu** na **žádný**. Po dokončení klepněte na tlačítko OK.

    ![Portál](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>Otestujte aktivitu kopírování pomocí spuštění ladění kanálu

1. Chcete-li ověřit, zda vaše aktivita kopírování funguje správně, klepněte na **tlačítko Ladění** v horní části plátna kanálu a spusťte spuštění ladění. Spuštění ladění umožňuje otestovat kanál buď end-to-end nebo až do zarážky před publikováním do služby datové továrny.

    ![Portál](media/lab-data-flow-data-share/copy11.png)
1. Chcete-li sledovat spuštění ladění, přejděte na kartu **Výstup** na plátně kanálu. Monitorovací obrazovka se automaticky aktualizuje každých 20 sekund nebo po ručním klepnutí na tlačítko obnovit. Aktivita kopírování má speciální zobrazení monitorování, ke kterému lze získat přístup kliknutím na ikonu brýlí ve sloupci **Akce.**

    ![Portál](media/lab-data-flow-data-share/copy12.png)
1. Zobrazení monitorování kopie poskytuje podrobnosti o spuštění aktivity a charakteristiky výkonu. Můžete zobrazit informace, jako jsou data číst / psát, řádky číst / zapisovat, soubory číst / zapisovat, a propustnost. Pokud jste nakonfigurovali vše správně, měli byste vidět 49 999 řádků zapsaných do jednoho souboru v jímce ADLS.

    ![Portál](media/lab-data-flow-data-share/copy13.png)
1. Než přejdete k další části, doporučujeme publikovat změny ve službě datové továrny kliknutím na **Publikovat vše** v horní části panelu výroby. I když není zahrnuta v této laboratoři, Azure Data Factory podporuje plnou git integrace. Integrace Gitu umožňuje správu verzí, iterativní ukládání v úložišti a spolupráci v datové továrně. Další informace najdete v tématu [správa zdrojového kódu v Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control#troubleshooting-git-integration).

    ![Portál](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Transformace dat s využitím toků dat mapování

Teď, když jste úspěšně zkopírovali data do Azure Data Lake Storage, je čas spojit a agregovat tato data do datového skladu. Budeme používat mapování toku dat, Azure Data Factory vizuálně navržené transformační služby. Mapování datových toků umožňuje uživatelům vyvíjet transformační logiku bez kódu a spouštět je na podněskovacích clusterech spravovaných službou ADF.

Tok dat vytvořený v tomto kroku vnitřní spojuje datovou sadu "TripDataCSV" vytvořenou v předchozí části s tabulkou 'dbo. TripFares' uložené v "SQLDB" na základě čtyř klíčových sloupců. Pak se data agregují na základě sloupce `payment_type` pro výpočet průměru určitých polí a zapsaných v tabulce Azure Synapse Analytics.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Přidání aktivity toku dat do kanálu

1. V podokně aktivit plátna kanálu otevřete akordeon **Move and Transform** a přetáhněte aktivitu **toku dat** na plátno.

    ![Portál](media/lab-data-flow-data-share/dataflow1.png)
1. V bočním podokně, které se otevře, vyberte **Vytvořit nový tok dat** a zvolte Mapování **toku dat**. Klikněte na tlačítko **OK**.

    ![Portál](media/lab-data-flow-data-share/dataflow2.png)
1. Budete přesměrováni na plátno toku dat, kde budete vytvářet logiku transformace. Na kartě obecné pojmenujte tok dat "JoinAndAggregateData".

    ![Portál](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Konfigurace zdroje csv dat o cestě

1. První věc, kterou chcete udělat, je nakonfigurovat dvě zdrojové transformace. První zdroj bude ukazovat na 'TripDataCSV' DelimitedText datové sady. Chcete-li přidat zdrojovou transformaci, klikněte na pole **Přidat zdroj** na plátně.

    ![Portál](media/lab-data-flow-data-share/dataflow4.png)
1. Pojmenujte svůj zdroj "TripDataCSV" a vyberte datovou sadu "TripDataCSV" z rozbalovacího pole zdroje. Pokud si pamatujete, neimportovali jste schéma zpočátku při vytváření této datové sady, protože tam nebyla žádná data. Vzhledem k tomu, `trip-data.csv` že nyní existuje, klikněte na **Upravit** a přejděte na kartu Nastavení datové sady.

    ![Portál](media/lab-data-flow-data-share/dataflow5.png)
1. Přejděte na schéma **tabulátoru** a klepněte na **položku Importovat schéma**. Vyberte **Z připojení nebo úložiště** importovat přímo z úložiště souborů. Mělo by se zobrazit 14 sloupců řetězce typu.

    ![Portál](media/lab-data-flow-data-share/dataflow6.png)
1. Vraťte se k toku dat "JoinAndAggregateData". Pokud byl váš ladicí cluster spuštěn (označen zeleným kruhem vedle posuvníku ladění), můžete získat snímek dat na kartě **Náhled dat.** Kliknutím na **Aktualizovat** načtete náhled dat.

    ![Portál](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> Náhled dat nezapisuje data.

### <a name="configure-your-trip-fares-sql-db-source"></a>Konfigurace jízdného ve vaší jízdě zdroj SQL DB

1. Druhý zdroj, který přidáváte, bude ukazovat na tabulku SQL DB 'dbo. TripFares'. Pod zdrojem "TripDataCSV" bude další pole **Přidat zdroj.** Kliknutím na něj přidáte novou zdrojovou transformaci.

    ![Portál](media/lab-data-flow-data-share/dataflow8.png)
1. Pojmenujte tento zdroj 'TripFaresSQL'. Chcete-li vytvořit novou datovou sadu SQL DB, klepněte na tlačítko **Nový** vedle pole zdrojové datové sady.

    ![Portál](media/lab-data-flow-data-share/dataflow9.png)
1. Vyberte dlaždici **Azure SQL Database** a klikněte na pokračovat. *Poznámka: Můžete si všimnout, mnoho konektorů v datové toku nejsou podporovány v mapování toku dat. Chcete-li transformovat data z jednoho z těchto zdrojů, svatit do podporovaného zdroje pomocí aktivity kopírování*.

    ![Portál](media/lab-data-flow-data-share/dataflow10.png)
1. Zavolejte do své datové sady "TripFares". Jako propojenou službu vyberte "SQLDB". Vyberte název tabulky 'dbo. TripFares' z rozbalovací tabulky. Import schématu **z připojení nebo úložiště**. Po dokončení klepněte na tlačítko OK.

    ![Portál](media/lab-data-flow-data-share/dataflow11.png)
1. Chcete-li ověřit data, načtete náhled dat na kartě **Náhled dat.**

    ![Portál](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Vnitřní spojení S TripDataCSV a TripFaresSQL

1. Chcete-li přidat novou transformaci, klikněte na ikonu plus v pravém dolním rohu "TripDataCSV". V části **Více vstupů/výstupů**vyberte **Připojit .**

    ![Portál](media/lab-data-flow-data-share/join1.png)
1. Pojmenujte transformaci spojení 'InnerJoinWithTripFares'. V rozbalovací části "TripFaresSQL" vyberte rozbalovací položku pravého datového proudu. Jako typ spojení vyberte **Vnitřní.** Další informace o různých typech spojení v toku dat mapování najdete v [tématu typy spojení](https://docs.microsoft.com/azure/data-factory/data-flow-join#join-types).

    V yberte sloupce, ve kterých chcete z každého datového proudu odpovídat, pomocí rozevíracího seznamu **Podmínky spojení.** Chcete-li přidat další podmínku spojení, klikněte na ikonu plus vedle existující podmínky. Ve výchozím nastavení jsou všechny podmínky spojení kombinovány s operátorem AND, což znamená, že musí být splněny všechny podmínky pro shodu. V této laboratoři chceme, `medallion`aby `hack_license` `vendor_id`odpovídaly na sloupce , , , a`pickup_datetime`

    ![Portál](media/lab-data-flow-data-share/join2.png)
1. Ověřte, zda jste úspěšně připojili 25 sloupců spolu s náhledem dat.

    ![Portál](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Agregovat podle payment_type

1. Po dokončení transformace spojení přidejte agregační transformaci kliknutím na ikonu plus vedle "InnerJoinWithTripFares. V části **Modifikátor Schémata**zvolte **Agregace** .

    ![Portál](media/lab-data-flow-data-share/agg1.png)
1. Pojmenujte agregotní transformaci AggregateByPaymentType. Vyberte `payment_type` jako skupinu po sloupci.

    ![Portál](media/lab-data-flow-data-share/agg2.png)
1. Přejděte na kartu **Agregace.** Zde zadáte dvě agregace:
    * Průměrný tarif seskupený podle typu platby
    * Celková vzdálenost cesty seskupená podle typu platby

    Nejprve vytvoříte výraz průměrného tarifu. Do textového pole s **popiskem Přidat nebo vybrat sloupec**zadejte "average_fare".

    ![Portál](media/lab-data-flow-data-share/agg3.png)
1. Chcete-li zadat výraz agregace, klepněte na modré pole označené **Enter expression**. Tím se otevře tvůrce výrazů toku dat, nástroj používaný k vizuálnímu vytváření výrazů toku dat pomocí vstupního schématu, vestavěných funkcí a operací a uživatelem definovaných parametrů. Další informace o možnostech tvůrce výrazů naleznete v [dokumentaci tvůrce výrazů](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder).

    Chcete-li získat průměrné `avg()` jízdné, použijte funkci agregace agregovat `total_amount` `toInteger()`přetypování sloupce na celé číslo s . V jazyce výrazu toku `avg(toInteger(total_amount))`dat je to definováno jako . Až budete hotovi, klikněte na **Uložit a dokončit.**

    ![Portál](media/lab-data-flow-data-share/agg4.png)
1. Chcete-li přidat další výraz agregace, klikněte na ikonu plus vedle položky `average_fare`. Vyberte **Přidat sloupec**.

    ![Portál](media/lab-data-flow-data-share/agg5.png)
1. V textovém poli s **popiskem Přidat nebo vybrat sloupec**zadejte "total_trip_distance". Stejně jako v posledním kroku otevřete tvůrce výrazů, který chcete zadat do výrazu.

    Chcete-li získat celkovou `sum()` vzdálenost cesty, použijte funkci `trip_distance` agregace k `toInteger()`agregaci přetypování sloupce do celého čísla s . V jazyce výrazu toku `sum(toInteger(trip_distance))`dat je to definováno jako . Až budete hotovi, klikněte na **Uložit a dokončit.**

    ![Portál](media/lab-data-flow-data-share/agg6.png)
1. Otestujte logiku transformace na kartě **Náhled dat.** Jak můžete vidět, existuje výrazně méně řádků a sloupců než dříve. Pouze tři sloupce skupiny podle a agregace definované v této transformaci pokračovat po proudu. Vzhledem k tomu, že v ukázce je pouze pět skupin typu platby, je výstup pouze pět řádků.

    ![Portál](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Konfigurace dřezu Azure Synapse Analytics

1. Teď, když jsme dokončili naši transformační logiku, jsme připraveni uvrhnout naše data v tabulce Azure Synapse Analytics. Přidejte transformaci jímky do části **Cíl.**

    ![Portál](media/lab-data-flow-data-share/sink1.png)
1. Pojmenujte dřez 'SQLDWSink'. Kliknutím na **Nový** vedle pole datové sady jímky vytvořte novou datovou sadu Azure Synapse Analytics.

    ![Portál](media/lab-data-flow-data-share/sink2.png)

1. Vyberte dlaždici **Azure Synapse Analytics (dříve SQL DW)** a klikněte na pokračovat.

    ![Portál](media/lab-data-flow-data-share/sink3.png)
1. Zavolejte do své datové sady "AggregatedTaxiData". Jako propojenou službu vyberte "SQLDW". Vyberte **Vytvořit novou tabulku** a pojmenujte novou tabulku dbo. AggregateTaxiData. Po dokončení klikněte na OK.

    ![Portál](media/lab-data-flow-data-share/sink4.png)
1. Přejděte na kartu **Nastavení** umyvadla. Vzhledem k tomu, že vytváříme novou tabulku, musíme v akci tabulky vybrat **Znovu vytvořit tabulku.** Zrušte **odznačte povolit pracovní pracovní**nastavení , které přepíná bez ohledu na to, zda vkládáme řádek po řádku nebo do dávky.

    ![Portál](media/lab-data-flow-data-share/sink5.png)

Úspěšně jste vytvořili tok dat. Nyní je čas spustit v aktivitě kanálu.

### <a name="debug-your-pipeline-end-to-end"></a>Ladění kanálu od konce ke konci

1. Vraťte se na kartu pro kanál **IngestAndTransformData.** Všimněte si zeleného pole na aktivitě kopírování "IngestIntoADLS". Přetáhněte ji do aktivity toku dat JoinAndAggregateData. To vytvoří "na úspěch", který způsobí, že aktivita toku dat spustit pouze v případě, že kopie je úspěšná.

    ![Portál](media/lab-data-flow-data-share/pipeline1.png)
1. Stejně jako jsme to udělali pro aktivitu kopírování, klepněte na tlačítko **Ladění** spustit ladění spustit. Pro spuštění ladění bude aktivita toku dat používat aktivní ladicí cluster namísto střídání nového clusteru. Spuštění tohoto kanálu bude trvat něco málo přes minutu.

    ![Portál](media/lab-data-flow-data-share/pipeline2.png)
1. Stejně jako aktivita kopírování má tok dat speciální zobrazení monitorování přístupné ikonou brýlí po dokončení aktivity.

    ![Portál](media/lab-data-flow-data-share/pipeline3.png)
1. V zobrazení monitorování můžete zobrazit zjednodušený graf toku dat spolu s časy spuštění a řádky v každé fázi spuštění. Pokud se provádí správně, měli byste mít agregované 49 999 řádků do pěti řádků v této aktivitě.

    ![Portál](media/lab-data-flow-data-share/pipeline4.png)
1. Kliknutím na transformaci získáte další podrobnosti o jejím spuštění, jako jsou informace o rozdělení a nové/aktualizované/vynechané sloupce.

    ![Portál](media/lab-data-flow-data-share/pipeline5.png)

Nyní jste dokončili část datové továrny tohoto testovacího prostředí. Publikujte své prostředky, pokud je chcete zprovoznit pomocí aktivačních událostí. Úspěšně jste spustili kanál, který ingestoval data z Azure SQL Database do Azure Data Lake Storage pomocí aktivity kopírování a pak agregovali tato data do Azure Synapse Analytics. Můžete ověřit, že data byla úspěšně zapsána při pohledu na samotný sql server.

## <a name="share-data-using-azure-data-share"></a>Sdílení dat pomocí služby Azure Data Share

V této části se dozvíte, jak nastavit novou sdílenou složku dat pomocí webu Azure Portal. To bude zahrnovat vytvoření nové sdílené datové složky, která bude obsahovat datové sady z Azure Data Lake Store Gen2 a Azure SQL Data Warehouse. Poté nakonfigurujete plán snímků, který spotřebitelům dat poskytne možnost automaticky aktualizovat data, která jsou s nimi sdílena. Potom pozvete příjemce do sdílené složky dat. 

Po vytvoření sdílené složky dat pak přepnete klobouky a stanete se *příjemcem dat*. Jako příjemce dat budete procházet tok přijetí mašity ke sdílení dat, konfigurovat, kde chcete data přijímat, a mapovat datové sady do různých umístění úložiště. Pak spustíte snímek, který zkopíruje data sdílená s vámi do zadaného cíle. 

### <a name="sharing-data-data-provider-flow"></a>Sdílení dat (tok zprostředkovatele dat)

1. Otevřete portál Azure v Microsoft Edge nebo Google Chrome.

1. Pomocí vyhledávacího panelu v horní části stránky vyhledejte **položky Sdílení dat**

    ![Portál](media/lab-data-flow-data-share/portal-ads.png)

1. V názvu vyberte účet sdílení dat s názvem Zprostředkovatel. Například **DataProvider0102**. 

1. Vyberte **Zahájit sdílení dat.**

    ![Zahájit sdílení](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Výběrem možnosti **+Vytvořit** zahájíte konfiguraci nové sdílené složky dat. 

1. V části **Název sdílené**položky zadejte název, který si vyberete. Toto je název sdílené položky, který uvidí váš spotřebitel dat, proto jej nezapomeňte uvést popisným názvem, například TaxiData.

1. V části **Popis**vložte do věty, která popisuje obsah sdílené složky dat. Sdílení dat bude obsahovat celosvětové taxi data, která jsou uložená v řadě úložišť, včetně Azure SQL Data Warehouse a Azure Data Lake Store. 

1. V **části Podmínky použití**zadejte sadu podmínek, které má váš datový spotřebitel dodržovat. Některé příklady zahrnují "Nedistribuovat tato data mimo vaši organizaci" nebo "Odkazovat na právní smlouvu". 

    ![Sdílet podrobnosti](media/lab-data-flow-data-share/ads-details.png)

1. Vyberte **Pokračovat**. 

1. Vybrat **přidat datové sady** 

    ![Přidat datovou sadu](media/lab-data-flow-data-share/add-dataset.png)

1. Vyberte **Azure SQL Data Warehouse** a vyberte tabulku z datového skladu Azure SQL, ve které se vaše transformace ADF vykládaly.

    ![Přidat datovou sadu](media/lab-data-flow-data-share/add-dataset-sql.png)

> [!NOTE]
> Azure SQL Data Warehouse se teď označuje jako Azure Synapse Analytics.

1. Než budete moci pokračovat, budete mít ke spuštění skript. Poskytnutý skript vytvoří uživatele v databázi SQL, aby msi sdílení dat Azure mohl ověřit jeho jménem. 

> [!IMPORTANT]
> Před spuštěním skriptu se musíte nastavit jako správce služby Active Directory pro sql server. 

1. Otevřete novou kartu a přejděte na portál Azure. Zkopírujte skript zadaný a vytvořte uživatele v databázi, ze které chcete sdílet data. To provést přihlášením do databáze EDW pomocí Průzkumníka dotazů (preview) pomocí ověřování AAD. 

    Budete muset upravit skript tak, aby vytvořený uživatel byl obsažen v závorkách. Např:
    
    vytvořit uživatele [dataprovider-xxxx] z externího přihlášení;  exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. Přepněte zpět do služby Azure Data Share, kde jste do sdílené datové složky přidávali datové sady. 

1. Vyberte **EDW** pro sql datový sklad a vyberte **AggregatedTaxiData** pro tabulku. 

1. Vybrat **přidat datovou sadu**

    Nyní máme tabulku SQL, která je součástí naší datové sady. Dále přidáme další datové sady z Azure Data Lake Store. 

1. Vyberte **Přidat datovou sadu** a vyberte **Azure Data Lake Store Gen2**

    ![Přidat datovou sadu](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Vybrat **další**

1. Rozbalte *wwtaxidata*. Rozbalte *Boston Taxi Data*. Všimněte si, že můžete sdílet až na úroveň souboru. 

1. Vyberte složku *Boston Taxi Data* a přidejte celou složku do sdílené složky dat. 

1. Vybrat **přidat datové sady**

1. Zkontrolujte datové sady, které byly přidány. Měli byste mít tabulku SQL a složku ADLSGen2 přidándo sdílené složky dat. 

1. Vyberte **Pokračovat**

1. Na této obrazovce můžete přidat příjemce do sdílené složky dat. Příjemci, které přidáte, obdrží pozvánky ke sdílené složce dat. Pro účely tohoto testovacího prostředí je nutné přidat 2 e-mailové adresy:

    1. E-mailová adresa předplatného Azure, ve které se najdete. 

        ![Přidání příjemců](media/lab-data-flow-data-share/add-recipients.png)

    1. Přidejte fiktivní příjemce *janedoe@fabrikam.com*dat s názvem .

1. Na této obrazovce můžete nakonfigurovat nastavení snímku pro vašeho příjemce dat. To jim umožní dostávat pravidelné aktualizace vašich dat v intervalu, který jste definovali. 

1. Zkontrolujte **plán snímků** a nakonfigurujte hodinovou aktualizaci dat pomocí rozevíracího seznamu *Opakování.*  

1. Vyberte **Vytvořit**.

    Nyní máte aktivní sdílenou složku dat. Umožňuje zkontrolovat, co můžete vidět jako zprostředkovatel dat při vytváření sdílené složky dat. 

1. Vyberte sdílenou složku dat, kterou jste vytvořili, s názvem **DataProvider**. Na něj můžete přejít výběrem **možnosti Odeslané sdílené složky** ve **sdílené složce dat**. 

1. Klikněte na plán snímků. Pokud se rozhodnete, můžete zakázat plán snímků. 

1. Dále vyberte kartu **Datové sady.** Do této sdílené složky dat můžete po vytvoření přidat další datové sady. 

1. Vyberte kartu **Sdílet odběry.** Odběry sdílení zatím neexistují, protože váš příjemce dat ještě nepřijal vaši pozvánku.

1. Přejděte na kartu **Pozvánky.** Zde se zobrazí seznam nevyřízených pozvánek. 

    ![Nevyřízené pozvánky](media/lab-data-flow-data-share/pending-invites.png)

1. Vyberte pozvánku do . *janedoe@fabrikam.com* Vyberte Odstranit. Pokud příjemce pozvánku ještě nepřijal, nebude to moci učinit. 

1. Vyberte kartu **Historie.** Zatím se nic nezobrazí, protože váš příjemce dat ještě nepřijal vaši pozvánku a spustil snímek. 

### <a name="receiving-data-data-consumer-flow"></a>Příjem dat (tok spotřebitele dat)

Nyní, když jsme zkontrolovali náš podíl dat, jsme připraveni přepínat kontext a nosit náš klobouk pro spotřebitele dat. 

Teď byste měli mít pozvánku ke sdílené službě Azure ve složce doručené pošty z Microsoft Azure. Spusťte Outlook Web Access (outlook.com) a přihlaste se pomocí přihlašovacích údajů dodaných pro vaše předplatné Azure.

V e-mailu, který jste měli obdržet, klikněte na "Zobrazit pozvánku >". V tomto okamžiku budete simulovat prostředí pro spotřebitele dat při přijímání pozvání poskytovatelů dat do jejich sdílení dat. 

![E-mailová pozvánka](media/lab-data-flow-data-share/email-invite.png)

Můžete být vyzváni k výběru předplatného. Ujistěte se, že jste vybrali předplatné, ve kterém jste pracovali pro toto testovací prostředí. 

1. Klikněte na pozvánku s názvem *DataProvider*. 

1. Na této obrazovce pozvánky si všimnete různých podrobností o sdílené složce dat, kterou jste dříve nakonfigurovali jako zprostředkovatele dat. Přečtěte si podrobnosti a přijměte podmínky použití, pokud jsou uvedeny.

1. Vyberte předplatným a skupinou prostředků, která již pro vaše testovací prostředí existuje. 

1. V **položce Účet sdílení dat**vyberte **položku DataConsumer**. Můžete také vytvořit nový účet sdílení dat. 

1. Vedle **položky Přijatý název sdílené složky**zjistíte, že výchozí název sdílené položky je název, který byl určen poskytovatelem dat. Pojmenujte sdílenou složku popisnou názvem popisující data, která se chystáte přijmout, například **TaxiDataShare**.

    ![Pozvánka přijímá](media/lab-data-flow-data-share/consumer-accept.png)

1. Můžete zvolit **přijetí a konfiguraci nyní** nebo **Přijmout a nakonfigurovat později**. Pokud se rozhodnete přijmout a nakonfigurovat nyní, zadáte účet úložiště, kde by měla být zkopírována všechna data. Pokud se rozhodnete přijmout a nakonfigurovat později, datové sady ve sdílené složce budou nenamapované a budete je muset ručně mapovat. Pro to se rozhodneme později. 

1. Vyberte **Přijmout a nakonfigurovat později**. 

    Při konfiguraci této možnosti je vytvořeno sdílení odběr, ale není nikde pro data k zemi, protože žádný cíl byl namapován. 

    Dále nakonfigurujeme mapování datových sad pro sdílenou složku dat. 

1. Vyberte přijatou sdílenou složku (název, který jste zadali v kroku 5).

    **Snímek aktivační události** je zašedlý, ale sdílená složky jsou aktivní. 

1. Vyberte kartu **Datové sady.** Všimněte si, že každá datová sada je Unmapapped, což znamená, že nemá žádný cíl pro kopírování dat. 

    ![nenamapované datové sady](media/lab-data-flow-data-share/unmapped.png)

1. Vyberte tabulku datového skladu SQL a pak vyberte **+ Mapovat na cíl**.

1. Na pravé straně obrazovky vyberte rozevírací rozbalovací položku **Cílový datový typ.** 

    Data SQL můžete namapovat do široké škály úložišť dat. V takovém případě budeme mapování na Azure SQL Database.

    ![mapování](media/lab-data-flow-data-share/mapping-options.png)
    
    (Nepovinné) Jako cílový datový typ vyberte **Azure Data Lake Store Gen2.** 
    
    (Nepovinné) Vyberte účet Předplatné, Skupina prostředků a Úložiště, se kterými pracujete. 
    
    (Nepovinné) Můžete si vybrat, zda chcete přijímat data do datového jezera buď ve formátu csv nebo parket. 

1. Vedle **položky Cílový datový typ**vyberte Azure SQL Database. 

1. Vyberte účet Předplatné, Skupina prostředků a Úložiště, se kterými pracujete. 

    ![mapovat na SQL](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Než budete moci pokračovat, budete muset vytvořit nového uživatele v SQL Server spuštěním skriptu k dispozici. Nejprve zkopírujte skript dodaný do schránky. 

1. Otevřete novou kartu portálu Azure. Nezavírejte stávající kartu, protože se k ní budete muset za chvíli vrátit. 

1. Na nové kartě, kterou jste otevřeli, přejděte do **databází SQL**.

1. Vyberte databázi SQL (v předplatném by měla být pouze jedna). Dávejte pozor, abyste nevybrali datový sklad SQL. 

1. Vybrat **editor dotazů (náhled)**

1. Pomocí ověřování AAD se přihlaste do editoru dotazů. 

1. Spusťte dotaz poskytnutý ve sdílené složce dat (zkopírován do schránky v kroku 14). 

    Tento příkaz umožňuje službě Azure Data Share používat spravované identity pro služby Azure k ověření na SQL Serveru, aby do něj bylo možné kopírovat data. 

1. Vraťte se na původní kartu a vyberte **Mapovat cíl**.

1. Dále vyberte složku Azure Data Lake Gen2, která je součástí datové sady a namapujte ji na účet azure blob storage. 

    ![úložiště](media/lab-data-flow-data-share/storage-map.png)

    Při namapování všech datových sad jste nyní připraveni začít přijímat data od poskytovatele dat. 

    ![Mapovány](media/lab-data-flow-data-share/all-mapped.png)
    
1. Vyberte **Podrobnosti**. 

    Všimněte si, že **snímek Trigger** již není zašedlý, protože sdílená data má nyní cíle, do kterých je třeba kopírovat.

1. Vyberte Aktivovat snímek -> úplnou kopii. 

    ![Aktivační událost](media/lab-data-flow-data-share/trigger-full.png)

    Tím začnete kopírovat data do nového účtu sdílení dat. V reálném scénáři by tato data pocházela od třetí strany. 

    Bude trvat přibližně 3-5 minut, než se data narazí. Průběh můžete sledovat kliknutím na kartu **Historie.** 

    Zatímco čekáte, přejděte na původní sdílení dat (DataProvider) a zobrazte stav karty **Sdílené odběry** a **historie.** 

1. Přejděte zpět na sdílenou složku dat příjemce dat. Jakmile je stav aktivační události úspěšný, přejděte do cílové databáze SQL a datového jezera a zjistěte, že data přistála v příslušných úložištích. 

Gratulujeme, dokončili jste laboratoř!


