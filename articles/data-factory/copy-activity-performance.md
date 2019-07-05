---
title: Průvodce laděním a výkonem aktivity kopírování ve službě Azure Data Factory | Dokumentace Microsoftu
description: Další informace o klíčových faktorů, které mají vliv na výkon přesouvání dat ve službě Azure Data Factory použijete aktivitu kopírování.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: face3719f32ccb44e7479150e94417496141f90b
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509563"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Průvodce laděním a výkonem aktivity kopírování
> [!div class="op_single_selector" title1="Vyberte verzi služby Azure Data Factory, který používáte:"]
> * [Verze 1](v1/data-factory-copy-activity-performance.md)
> * [Aktuální verze](copy-activity-performance.md)


Aktivita kopírování objektu pro vytváření dat Azure poskytuje prvotřídní zabezpečené, spolehlivé a vysoce výkonné datové načítání řešení. Slouží ke zkopírování desítky terabajty dat každý den v nejrůznějších cloudových a místních úložišť dat. Rychlé načítání dat výkonu je klíčem k zajištění, že se můžete soustředit na základní problém velké objemy dat: sestavovat Pokročilá analytická řešení a získat podrobné informace z všechna tato data.

Azure poskytuje sadu podniková řešení datových skladů úložiště a data. Aktivita kopírování nabízí vysoce optimalizovanému data načítání prostředí, které usnadňují konfiguraci a nastavení. Pomocí aktivity kopírování jeden můžete načíst data do:

* Azure SQL Data Warehouse při 1,2 GB/s.
* Azure Blob storage rychlostí 1,0 GB/s.
* Azure Data Lake Store v 1,0 GB/s.

Tento článek popisuje:

* [Výkon referenční čísla](#performance-reference) pro podporované zdroje a jímky úložišť dat, které vám pomohou při plánování projektu.
* Funkce, které může zvýšit propustnost kopírování v různých scénářích, která zahrnuje [jednotky integrace dat](#data-integration-units) (DIUs) [paralelní kopírování](#parallel-copy), a [fázovaného kopírování](#staged-copy).
* [Průvodce laděním výkonu](#performance-tuning-steps) o tom, jak optimalizovat výkon a klíčové faktory, které mohou ovlivnit výkon kopírování.

> [!NOTE]
> Pokud nejste obeznámeni s aktivitou kopírování obecně platí, najdete v článku [přehled aktivit kopírování](copy-activity-overview.md) předtím, než v tomto článku.
>

## <a name="performance-reference"></a>Referenční dokumentace výkonu

Jako odkaz následující tabulka uvádí číslo kopírování propustnost v MB/s pro daný zdroj a dvojice jímky v aktivitě kopírování jednoho spuštění na základě interní testování. Pro porovnání, také ukazuje, jak různé nastavení [jednotky integrace dat](#data-integration-units) nebo [škálovatelnost modulu runtime integrace v místním prostředí](concepts-integration-runtime.md#self-hosted-integration-runtime) (více uzlů) může pomoct výkon kopírování.

![Přehled výkonu](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> Při spuštění aktivity kopírování v prostředí Azure integration runtime je minimální povolený jednotky integrace dat, (dříve označované jako jednotky přesunu dat) dvě. Pokud není zadán, najdete v článku výchozí jednotky integrace dat se používají ve [jednotky integrace dat](#data-integration-units).

**Odkazuje na mějte na paměti:**

* Vypočítá se propustnost s použitím následujícího vzorce: [objem dat pro čtení ze zdroje] / [aktivita doba trvání běhu kopírování].
* Výkon referenční čísla v tabulce se měří pomocí [TPC-H](http://www.tpc.org/tpch/) datovou sadu v aktivitě kopírování jednoho spuštění. Testovací soubory pro souborové úložiště se víc souborů s 10 GB velikosti.
* V úložištích dat Azure zdroj a jímka mají ve stejné oblasti Azure.
* Pro hybridní kopírování mezi místním a cloudovým úložištěm dat a každý uzel v místním prostředí integration runtime byl spuštěn na počítači, který byl nezávisle na úložišti dat pomocí specifikace. Pokud byla spuštěna jedna aktivita, operace kopírování spotřebovány pouze malou část testovací počítač procesor, paměť nebo šířky pásma sítě.
    <table>
    <tr>
        <td>Procesor</td>
        <td>32 jader 2,20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Memory (Paměť)</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Síť</td>
        <td>Internetové rozhraní: 10 GB/s; rozhraní sítě intranet: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> Pomocí více DIUs můžete dosáhnout vyšší propustnost. Například s 100 DIUs, můžete zkopírovat data z úložiště objektů Blob Azure do Azure Data Lake Store v 1,0 GB/s. Další informace o této funkce a scénáře podporované, najdete v článku [jednotky integrace dat](#data-integration-units) oddílu. 

## <a name="data-integration-units"></a>Jednotky integrace dat

Jednotka integrace dat je míru, která představuje výkon (kombinaci procesoru, paměti a přidělení prostředků sítě) jedné jednotky ve službě Azure Data Factory. Jednotka integrace dat se vztahuje pouze na [prostředí Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime), ale ne [modulu runtime integrace v místním prostředí](concepts-integration-runtime.md#self-hosted-integration-runtime).

Minimální DIUs pro spuštění aktivity kopírování jsou dvě. Pokud není zadán, následující tabulka uvádí výchozí DIUs používá ve scénářích různé kopie:

| Kopírování | Výchozí DIUs určené služby |
|:--- |:--- |
| Kopírovat data mezi úložišti souborů | Mezi 4 a 32 v závislosti na počtu a velikosti souborů |
| Kopírování dat do Azure SQL Database nebo Azure Cosmos DB |Mezi 4 a 16 v závislosti na jímce úrovni Azure SQL Database nebo Cosmos DB (počet Dtu/ru) |
| Všech dalších scénářů kopírování | 4 |

Chcete-li přepsat toto výchozí nastavení, zadejte hodnotu **dataIntegrationUnits** vlastnost následujícím způsobem. *Povolené hodnoty* pro **dataIntegrationUnits** vlastnost je až 256. *Skutečný počet DIUs* , že operace kopírování používá za běhu je rovna nebo menší než nakonfigurovaná hodnota, v závislosti na vašich dat vzor. Informace o úrovni výkonu, může se zobrazit při konfiguraci víc jednotek pro konkrétní kopírovat zdroje a jímky, najdete v článku [výkonu](#performance-reference).

Zobrazí se DIUs použít pro každou kopii spustit ve výstupu aktivity kopírování, když monitorování spuštění aktivit. Další informace najdete v tématu [zkopírujte monitorování aktivit](copy-activity-overview.md#monitoring).

> [!NOTE]
> Nastavení DIUs větší než čtyři aktuálně platí jenom v případě, že zkopírovat víc souborů z Azure Storage, Azure Data Lake Storage, Amazon S3, Google Cloud Storage, cloudovým FTP nebo SFTP do všech ostatních cloudových úložišť dat v cloudu.
>

**Příklad**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="data-integration-units-billing-impact"></a>Fakturační dopad jednotky integrace dat

Mějte na paměti, že vám budou účtovány na základě na celkové doby trvání operace kopírování. Celková doba, kterou bude se vám účtovat pro přesun dat je celková doba trvání mezi DIUs. Pokud úloha kopírování používá k trvat jednu hodinu se dvěma jednotkami cloudu a teď trvá 15 minut s osm jednotek v cloudu, bude celkové vyúčtování skoro stejné zůstane.

## <a name="parallel-copy"></a>Paralelní kopírování

Můžete použít **parallelCopies** vlastnost umožňující označit, které chcete aktivitou kopírování pomocí paralelismu. Tuto vlastnost můžete představit jako maximální počet vláken v aktivitě kopírování, který může číst ze zdroje nebo zapisovat do svá úložiště dat jímky paralelně.

Pro každé spuštění aktivity kopírování Azure Data Factory určuje počet paralelních kopií pro použití ke kopírování dat ze zdroje dat, ukládání a cílového datového úložiště. Výchozí počet paralelních kopie, které používá závisí na typu zdroje a jímky, který používáte.

| Kopírování | Výchozí počet paralelních kopií určené služby |
| --- | --- |
| Kopírovat data mezi úložišti souborů |Závisí na velikosti souborů a počet DIUs používá ke kopírování dat mezi dvěma cloudovými úložišti dat nebo konfigurace fyzického počítače místní prostředí integration runtime. |
| Kopírování dat z jakékoli zdrojové úložiště Azure Table Storage |4 |
| Dalších scénářů kopírování |1 |

> [!TIP]
> Při kopírování dat mezi úložišti souborové výchozí chování obvykle poskytuje nejlepší propustnost. Výchozí chování je určen automaticky založena na vzoru váš zdrojový soubor.

Řízení zatížení na počítačích, které jsou hostiteli vaše data ukládá, nebo pro optimalizaci výkonu kopírování, můžete přepsat výchozí hodnotu a zadat hodnotu **parallelCopies** vlastnost. Hodnota musí být celé číslo větší než nebo rovno 1. V době běhu pro zajištění nejlepšího výkonu, aktivita kopírování používá hodnotu, která je menší než nebo rovna hodnotě, kterou jste nastavili.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

**Odkazuje na mějte na paměti:**

* Při kopírování dat mezi úložišti souborové **parallelCopies** určuje paralelismu na úrovni souboru. Bloků v rámci jednoho souboru dojde pod automaticky a transparentně. Je navržena pro použití nejlépe vhodný bloků velikosti pro typ úložiště daného zdroje dat pro načtení dat v paralelní a kolmě **parallelCopies**. Skutečný počet paralelních kopie služba pro přesun dat se používá pro operaci kopírování v době běhu je delší než počet souborů, které máte. Pokud je chování kopírování nastavené **mergeFile**, aktivita kopírování nemůže využívají paralelismus úrovni souboru.
* Při kopírování dat z úložiště, která nejsou na základě souboru (s výjimkou jako zdroj s povoleno dělení dat Oracle database) do úložišť, které jsou založené na souboru ignoruje služba pro přesun dat **parallelCopies** vlastnost. I v případě, že je zadán paralelismu, není použita v tomto případě.
* **ParallelCopies** vlastnost je ortogonální k **dataIntegrationUnits**. Předchozí se počítá přes všechny jednotky integrace Data.
* Pokud zadáte hodnotu **parallelCopies** vlastnost, zvažte zvýšení zátěže na zdroji a úložiště dat jímky. Také zvažte zvýšení zátěže do místního prostředí integration runtime, pokud aktivita kopírování je možnost, například pro hybridní kopírování. Toto zvýšení zátěže se stane, zejména pokud máte více souběžných spuštění stejného aktivit, které běží na stejné úložiště dat nebo aktivity. Pokud si všimnete, že v úložišti dat nebo místní prostředí integration runtime je zahlcen zatížení, snížit **parallelCopies** hodnota, která má-li snížit zatížení.

## <a name="staged-copy"></a>Kopírování dvoufázové instalace

Při kopírování dat ze zdrojového úložiště dat do úložiště dat jímky, můžete zvolit použití Blob storage jako dočasné pracovní úložiště. Pracovní je zvláště užitečná v následujících případech:

- **Chcete ingestovat data z různých zdrojů dat do SQL Data Warehouse pomocí PolyBase.** SQL Data Warehouse používá k načtení velkých objemů dat do SQL Data Warehouse PolyBase jako vhodný mechanismus vysokou propustnost. Zdroj dat musí být ve službě Blob storage nebo Azure Data Lake Store a musí splňovat další kritéria. Při načítání dat z úložiště dat než Blob storage nebo Azure Data Lake Store můžete aktivovat kopírování prostřednictvím Blob storage dočasné pracovní data. V takovém případě služby Azure Data Factory provádí transformace požadovaná data k zajištění, že splňují požadavky PolyBase. Potom použije PolyBase k načtení dat do SQL Data Warehouse efektivně. Další informace najdete v tématu [použití PolyBase k načítání dat do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Někdy trvá nějakou provést přesun hybridních dat (to znamená, zkopírujte z místního úložiště dat do cloudového úložiště dat) přes pomalé síťové připojení.** Kvůli zvýšení výkonu se vám pomůže dvoufázové instalace kopírování komprimovat data dostupná místně tak, že bude trvat kratší dobu pro přesun dat do pracovní úložiště dat v cloudu. Před načtením do cílového úložiště dat, pak můžete dekomprimaci dat v úložišti pracovní.
- **Nechcete otevřít jiné porty než port 80 a 443 v bráně firewall kvůli podnikovým zásadám IT.** Například při kopírování dat z do místního úložiště dat jímky Azure SQL Database nebo Azure SQL Data Warehouse jímky, budete muset aktivovat odchozí komunikaci TCP na portu 1433 pro bránu Windows firewall a váš podnikový firewall. V tomto scénáři dvoufázové instalace kopírování můžete využít místní prostředí integration runtime nejprve zkopírovat data do úložiště objektů Blob pracovní instance prostřednictvím protokolu HTTP nebo HTTPS na portu 443. Pak je načíst data do SQL Database nebo SQL Data Warehouse z pracovního objektu Blob úložiště. V tomto toku není nutné povolit port 1433.

### <a name="how-staged-copy-works"></a>Kopírování funguje jak dvoufázové instalace

Při aktivaci pracovní funkce data se zkopíruje ze zdrojového úložiště dat do přípravného úložiště objektů Blob (funkce přineste si vlastní). V dalším kroku data zkopírována z pracovní úložiště dat do úložiště dat jímky. Azure Data Factory automaticky spravuje tok dvoufázová za vás. Po dokončení přesunu dat, vyčistí Azure Data Factory také dočasná data z přípravného úložiště.

![Kopírování dvoufázové instalace](media/copy-activity-performance/staged-copy.png)

Při aktivaci přesun dat pomocí přípravné úložiště můžete určit, zda chcete úložištěm dat, aby se komprimoval před přesun dat ze zdrojových dat a jako dočasné nebo úložišti pracovní data a pak dekomprimovat před přesun dat z dočasné nebo pracovních dat úložiště na úložiště dat jímky.

V současné době nelze kopírovat data mezi dvě úložiště dat, které jsou připojené přes různé IRs místním s ani bez dvoufázové instalace kopírování. Pro takové scénáře můžete nakonfigurovat dvě aktivity explicitně zřetězené kopírování ke kopírování ze zdroje do přípravného prostředí a z pracovního do jímky.

### <a name="configuration"></a>Konfigurace

Konfigurace **enableStaging** nastavení k určení, jestli chcete data budou umístěné v úložišti objektů Blob, než je načtete do cílového úložiště dat v aktivitě kopírování. Pokud nastavíte **enableStaging** k `TRUE`, zadat další vlastnosti uvedené v následující tabulce. Budete potřebovat k vytvoření služby Azure Storage nebo úložiště sdíleného přístupu podpis propojená služba pro přípravu, pokud ho nemáte.

| Vlastnost | Popis | Výchozí hodnota | Požaduje se |
| --- | --- | --- | --- |
| enableStaging |Určete, jestli chcete kopírovat data prostřednictvím jako dočasné pracovní úložiště. |False |Ne |
| linkedServiceName |Zadejte název [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) propojenou službu, která odkazuje na instanci úložiště, které můžete použít jako dočasné pracovní úložiště. <br/><br/> Úložiště pomocí sdíleného přístupového podpisu nelze použít k načtení dat do SQL Data Warehouse pomocí PolyBase. Můžete ji použít v jiných scénářích. |neuvedeno |Ano, pokud **enableStaging** nastavena na hodnotu TRUE |
| path |Zadejte cestu úložiště objektů Blob, který chcete s daty, dvoufázové instalace. Pokud nezadáte cestu, služba vytvoří kontejner pro uložení dočasných dat. <br/><br/> Zadejte cestu, pouze v případě, že používáte úložiště pomocí sdíleného přístupového podpisu nebo vyžadujete dočasných dat v konkrétním umístění. |neuvedeno |Ne |
| enableCompression |Určuje, zda data je nutné zkomprimovat. předtím, než je zkopírovat do cíle. Toto nastavení omezuje objem dat přenášených. |False |Ne |

>[!NOTE]
> Pokud používáte dvoufázové instalace kopírování s komprese zapnuta, instanční objekt nebo ověřováním MSI pro pracovního objektu blob propojená služba se nepodporuje.

Tady je ukázková definice aktivity kopírování s vlastnostmi, které jsou popsány v předchozí tabulce:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Fázovaného kopírování fakturace dopad

Vám budou účtovány na základě dva kroky: doba kopírování a zkopírujte typu.

* Při použití přípravy během kopírování cloud, který kopíruje data z cloudového úložiště dat do jiného cloudového úložiště dat, jak fáze možnost pomocí prostředí Azure integration runtime, vám budou účtovány [Součet doba kopírování pro kroky 1 a 2] x [cloudové kopie Jednotková cena].
* Při použití přípravy během hybridní kopírování, která je kopírování dat z úložiště dat v místním cloudovým úložištěm dat, jedné fáze pověřený místní prostředí integration runtime, vám budeme účtovat [doba kopírování hybridní] x [hybridní kopírování Jednotková cena] + [cloudu doba kopírování] x [cloudové kopie Jednotková cena].

## <a name="performance-tuning-steps"></a>Postup optimalizace výkonu

Proveďte tyto kroky pro optimalizaci výkonu vaší služby Azure Data Factory s aktivitou kopírování.

1. **Stanovení základní úrovně.** Ve fázi vývoje testování svůj kanál pomocí aktivity kopírování proti ukázku reprezentativní data. Shromažďovat podrobnosti provádění a výkonové charakteristiky následující [zkopírujte monitorování aktivit](copy-activity-overview.md#monitoring).

2. **Diagnostika a optimalizace výkonu.** Pokud, které můžete sledovat výkon nesplňuje vaše očekávání, identifikujte kritické body výkonu. Potom optimalizace výkonu můžete odebrat nebo snižují dopad kritické body.

    V některých případech se při spuštění aktivity kopírování ve službě Azure Data Factory, se zobrazí zpráva "Tipy pro optimalizaci výkonu" v horní části [monitorování stránku aktivita kopírování](copy-activity-overview.md#monitor-visually), jak je znázorněno v následujícím příkladu. Zpráva informuje kritickým bodem, který byl identifikován spuštění dané kopie. Také provede vás o tom, jak změnit propustnost kopírování boost. Tipy pro ladění výkonu aktuálně poskytují návrhy, jako jsou:

    - Při kopírování dat do Azure SQL Data Warehouse pomocí PolyBase.
    - Zvýšit počet jednotek žádostí Azure Cosmos DB nebo Dtu databáze SQL Azure (jednotky propustnosti databáze) Pokud je prostředek na straně úložiště dat problémové místo.
    - Odeberte nepotřebné dvoufázové instalace kopii.

    Výkon ladění pravidel bude postupně rozšiřují i.

    **Příklad: Zkopírujte do Azure SQL Database s tipy pro ladění výkonu**

    V této ukázce během kopírování spustit, oznámení Azure Data Factory jímky, které Azure SQL Database dosáhne vysokého využití DTU, což zpomalí operace zápisu. Návrh je zvýšit na úrovni Azure SQL Database s více Dtu. 

    ![Zkopírujte monitorování s tipy pro optimalizaci výkonu](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    Kromě toho Následují některé společné aspekty. Úplný popis Diagnostika výkonu je nad rámec tohoto článku.

   * Funkce výkonu:
     * [Paralelní kopírování](#parallel-copy)
     * [Jednotky integrace dat](#data-integration-units)
     * [Kopírování dvoufázové instalace](#staged-copy)
     * [Škálovatelnost modulu runtime integrace v místním prostředí](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Modul runtime integrace v místním prostředí](#considerations-for-self-hosted-integration-runtime)
   * [Zdroj](#considerations-for-the-source)
   * [jímka](#considerations-for-the-sink)
   * [Serializace a deserializace](#considerations-for-serialization-and-deserialization)
   * [Komprese](#considerations-for-compression)
   * [Mapování sloupce](#considerations-for-column-mapping)
   * [Další aspekty](#other-considerations)

3. **Rozbalte položku konfigurace pro celou datovou sadu.** Jakmile budete spokojeni s výsledky spuštění a výkonem, můžete rozbalit definice a kanál pro celou datovou sadu.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Důležité informace týkající se místní prostředí integration runtime

Pokud vaše aktivita kopírování používá na místní prostředí integration runtime, vezměte na vědomí následující:

**Instalační program**: Doporučujeme použít vyhrazený počítač na hostitele integration runtime. Zobrazit [předpoklady pro použití v místním prostředí integration runtime](concepts-integration-runtime.md).

**Horizontální navýšení kapacity**: Jeden logický místní prostředí integration runtime pomocí jednoho nebo více uzlů může sloužit několika spuštění aktivity kopírování ve stejnou dobu současně. Pokud nemáte náročné požadavky na přesun hybridních dat s velkým počtem souběžných kopírování spuštění aktivit nebo pomocí velkého objemu dat ke kopírování, vezměte v úvahu [horizontální navýšení kapacity pro místní prostředí integration runtime](create-self-hosted-integration-runtime.md#high-availability-and-scalability) zřídit další prostředky Umožněte kopírování.

## <a name="considerations-for-the-source"></a>Důležité informace pro zdroj

### <a name="general"></a>Obecné

Ujistěte se, že základní úložiště dat není zahlcen jiné úlohy, které jsou spuštěny na nebo před ním.

Microsoft úložišť dat, naleznete v tématu [monitorování a optimalizace témata](#performance-reference) , které jsou specifické pro úložiště dat. Tato témata můžete pochopit vlastnosti výkonu úložiště dat a jak minimalizovat dobu odezvy a maximalizuje propustnost.

* Kopírování dat z Blob storage do SQL Data Warehouse, vezměte v úvahu při použití technologie PolyBase pro zvýšení výkonu. Další informace najdete v tématu [použití PolyBase k načítání dat do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Pokud zkopírujete data z HDFS do Azure Blob storage nebo Azure Data Lake Store, zvažte použití DistCp ke zvýšení výkonu. Další informace najdete v tématu [použití DistCp ke kopírování dat z HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Pokud se kopírování dat z Redshift do Azure SQL Data Warehouse, Azure BLob storage nebo Azure Data Lake Store, zvažte použití uvolnění z paměti pro zvýšení výkonu. Další informace najdete v tématu [uvolnění použít ke zkopírování dat z Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Úložiště dat na základě souboru

* **Průměrná velikost souboru a počet souborů**: Aktivita kopírování přenese jeden soubor dat najednou. Pomocí stejné množství dat k přesunutí je nižší, pokud data se skládá z mnoha malých souborů spíše než několik velkých souborů z důvodu spuštění fáze pro každý soubor celkovou propustnost. Pokud je to možné sloučit malých souborů do větší soubory získáte vyšší propustnost.
* **Soubor formátů a komprese**: Další způsoby, jak vylepšit výkon, najdete v článku [důležité informace k serializaci a deserializaci](#considerations-for-serialization-and-deserialization) a [důležité informace týkající se komprese](#considerations-for-compression) oddíly.

### <a name="relational-data-stores"></a>Úložiště relačních dat

* **Vzorek dat**: Schéma tabulky ovlivňuje kopírování propustnost. Velký řádek velikosti poskytuje lepší výkon než velikost malých řádku ke zkopírování stejné množství dat. Důvodem je, že databáze můžete efektivněji načíst menší počet dávek dat, které obsahují menší počet řádků.
* **Dotaz nebo uloženou proceduru**: Optimalizujte logiku dotazu nebo uložené procedury, které zadáte v zdroje aktivity kopírování se načíst data efektivněji.

## <a name="considerations-for-the-sink"></a>Důležité informace pro jímku

### <a name="general"></a>Obecné

Ujistěte se, že základní úložiště dat není zahlcen jiné úlohy, které jsou spuštěny na nebo před ním.

Microsoft úložišť dat, naleznete v tématu [monitorování a optimalizace témata](#performance-reference) , které jsou specifické pro úložiště dat. Tato témata můžete pochopit vlastnosti výkonu úložiště dat a jak minimalizovat dobu odezvy a maximalizuje propustnost.

* Kopírování dat z jakékoli úložiště dat do Azure SQL Data Warehouse, vezměte v úvahu při použití technologie PolyBase pro zvýšení výkonu. Další informace najdete v tématu [použití PolyBase k načítání dat do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Pokud zkopírujete data z HDFS do Azure Blob storage nebo Azure Data Lake Store, zvažte použití DistCp ke zvýšení výkonu. Další informace najdete v tématu [použití DistCp ke kopírování dat z HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Pokud se kopírování dat z Redshift do Azure SQL Data Warehouse, Azure Blob storage nebo Azure Data Lake Store, zvažte použití uvolnění z paměti pro zvýšení výkonu. Další informace najdete v tématu [uvolnění použít ke zkopírování dat z Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Úložiště dat na základě souboru

* **Zkopírujte chování**: Pokud se kopírování dat z různých datových souborové úložiště aktivity kopírování obsahuje tři možnosti prostřednictvím **copyBehavior** vlastnost. Zachová hierarchie, sloučí hierarchie nebo sloučí soubory. Zachování nebo sloučení hierarchie má žádné nebo téměř žádné nároky na výkon, ale slučování souborů způsobí, že chcete zvýšit nároky na výkon.
* **Soubor formátů a komprese**: Další způsoby, jak vylepšit výkon, najdete v článku [důležité informace k serializaci a deserializaci](#considerations-for-serialization-and-deserialization) a [důležité informace týkající se komprese](#considerations-for-compression) oddíly.

### <a name="relational-data-stores"></a>Úložiště relačních dat

* **Zkopírujte chování a výkon nepřímo**: Existují různé způsoby zápisu dat do SQL jímky. Další informace z [osvědčený postup pro načítání dat do služby Azure SQL Database](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database).

* **Velikost dat vzor a batch**:
  * Schéma tabulky ovlivňuje kopírování propustnost. Ke zkopírování stejné množství dat, velký řádek velikosti umožňuje lepší výkon než velikost malých řádku, protože databáze můžete efektivněji menší počet dávek dat potvrzení změn.
  * Aktivita kopírování vloží data z řady dávky. Můžete nastavit počet řádků v dávce pomocí **writeBatchSize** vlastnost. Pokud data obsahují malé řádků, můžete nastavit **writeBatchSize** vlastnost s vyšší hodnota můžou využívat výhody nižší režijní náklady na služby batch a vyšší propustnost. Je-li velikost řádku vašich dat je velká, dejte pozor, když zvýšíte **writeBatchSize**. Vysoká hodnota může vést k kopírování chybu způsobenou přetížení databáze.

### <a name="nosql-stores"></a>Úložišť typu NoSQL

* Pro **Table storage**:
  * **oddíl**: Zápis dat do oddílů prokládané výrazně snižuje výkon. Seřaďte svá zdrojová data pomocí klíče oddílu, vložení dat efektivně do jednoho oddílu po druhé. Nebo můžete upravit logiku k zápisu dat do jednoho oddílu.

## <a name="considerations-for-serialization-and-deserialization"></a>Důležité informace týkající se serializace a deserializace

Serializace a deserializace může dojít, pokud vstupní datová sada nebo výstupní datová sada je soubor. Další informace o podporovaných formátech souborů pomocí aktivity kopírování najdete v tématu [podporované formáty souborů a komprese](supported-file-formats-and-compression-codecs.md).

**Zkopírujte chování**:

* Kopírování souborů mezi úložišti dat na základě souboru:
  * Pokud vstupní a výstupní datovou sadu obě mají stejné nebo žádné nastavení formátu souboru, služba pro přesun dat provádí *binární kopie* bez jakýchkoli serializace nebo deserializace. Zobrazí větší propustnost v porovnání s scénář, ve které se liší od sebe navzájem nastavení formátu souboru zdroje a jímky.
  * Pokud vstupní a výstupní datové sady obě jsou ve formátu textu a pouze kódování typ se liší, služba pro přesun dat se jenom převod kódování. Neprovádí žádné serializace a deserializace, což způsobí, že některé výkonu režie ve srovnání s binární kopie.
  * Pokud vstupní a výstupní datovou sadu obě mají různé formáty souborů nebo různé konfigurace, jako je oddělovače, služba pro přesun dat deserializuje zdroje dat do datového proudu, transformaci a pak ho serializovat do výstupní formát, který jste určili. Výsledkem této operace mnohem více významné výkonnostní režii ve srovnání s další scénáře.
* Při kopírování souborů do nebo z úložiště dat, která není soubor na základě, například z úložiště založené na souborech do relačního úložiště serializaci nebo deserializaci krok je povinný. Tento krok vede významné výkonnostní režii.

**Formát souboru**: Formát souboru, který zvolíte může ovlivnit výkon kopírování. Například je Avro kompaktní binární formát, který ukládá metadata s daty. Má širokou podporu v ekosystému Hadoop pro zpracování a dotazování na ně. Avro je nákladnější k serializaci a deserializaci, což vede k nižší propustnost kopírování ve srovnání s textovém formátu. 

Ujistěte se, podle vašeho výběru formátu v průběhu zpracování toku komplexně. Začněte s:

- Co tvoří data uložená v úložištích dat zdroje nebo extrahovat z externích systémů.
- Nejlepší formát pro úložiště, analytické zpracování a dotazu.
- V jakém formátu data mají být exportovány do datových tržišť nástrojů pro vytváření sestav a vizualizační nástroje.

Někdy formát souboru, který je neoptimální pro čtení a zápisu může být dobrou volbou, pokud byste zvážit celkové analytické procesu.

## <a name="considerations-for-compression"></a>Důležité informace pro kompresi

Pokud vstupní nebo výstupní datová sada je soubor, můžete nastavit aktivitu kopírování k provedení kompresi nebo dekompresi jako zapíše data do cíle. Při výběru komprese provedete kompromis mezi vstupně výstupní (I/O) a procesoru. Komprese dat příplatek ve výpočetních prostředcích. Ale na oplátku omezuje v / v sítě a úložiště. V závislosti na vašich dat může se zobrazit boost v celkovou propustnost kopírování.

**Kodek**: Každý kompresní kodek má výhody. Například bzip2 má nejnižší kopírování propustnost, ale získat nejlepší výkon dotazů Hive pomocí bzip2, protože je možné rozdělit ke zpracování. GZIP je nepoužít možnost vyvážená nejvíce a se často používá nejčastěji. Zvolte kodek, který nejlépe vyhovuje vaší situaci začátku do konce.

**Úroveň**: Můžete zvolit ze dvou možností pro každý kompresní kodek: nejrychlejší komprimované a optimálně komprimované. Nejrychlejší komprimované možnost co nejrychleji komprimuje data i v případě, že výsledný soubor není komprimována optimálně. Optimálně komprimovaný možnost tráví víc času na komprese a vrací minimální nároky na data. Obě možnosti zobrazíte, která poskytuje lepší výkon ve vašem případě můžete otestovat.

**Potřeba**: Kopírování velkých objemů dat mezi místním úložištěm a cloudem, zvažte použití [fázovaného kopírování](#staged-copy) pomocí komprese zapnuta. Použití dočasné úložiště je užitečné, když šířku pásma podnikové sítě a služby Azure je omezujícím faktorem a má vstupní datové sady a výstupní datovou sadu i v nekomprimovaných formuláře.

## <a name="considerations-for-column-mapping"></a>Důležité informace týkající se mapování sloupce

Můžete nastavit **columnMappings** vlastnost v aktivitě kopírování do mapy všechny, nebo jen některé ze sloupců vstupní a výstupní sloupce. Poté, co služba pro přesun dat načte data ze zdroje, je potřeba provést mapování sloupců s daty před zapisuje data do jímky. Tato další zpracování snižuje kopírování propustnost.

Pokud zdrojové úložiště dat je zadávat dotazy, pokud je relačního úložiště, jako je SQL Database nebo SQL Server, nebo pokud je úložiště typu NoSQL, jako je Table storage nebo Azure Cosmos DB, představte si třeba doručením (push) sloupce filtrování a změna uspořádání logiku pro **dotazu** vlastnosti namísto použití mapování sloupců. Tímto způsobem projekce nastane, když služba pro přesun dat čte data ze zdrojového úložiště dat, kde je mnohem efektivnější.

Další informace z [zkopírujte mapování schématu aktivity](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Další důležité informace

Při velké množství dat, který chcete zkopírovat můžete upravit vaši obchodní logiku do oddílu Další data. Můžete naplánovat aktivitu kopírování ke spuštění více často ke snížení velikosti dat pro každou aktivitu kopírování, na kterém běží.

Buďte opatrní počet datových sad a zkopírujte aktivity, které vyžadují služby Azure Data Factory k připojení do stejného úložiště dat ve stejnou dobu. Mnoho souběžných kopírování úloh může omezit úložiště dat a vést ke snížení výkonu, opakování interní úlohu kopírování a v některých případech se selhání spuštění.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Ukázkový scénář: Kopírování z místního SQL serveru do úložiště objektů Blob

**Scénář**: Kanál je určený pro kopírování dat z místních SQL serveru do úložiště objektů Blob ve formátu CSV. Chcete-li úlohu kopírování rychleji, by měl zkomprimují soubory CSV do formátu bzip2.

**Testování a analýzy**: Propustnost aktivity kopírování je menší než 2 MB/s, což je mnohem pomalejší než srovnávacího testu výkonu.

**Analýza výkonu a ladění**: Řešení potíží s výkonem, Podívejme se na způsobu zpracování a přesunout data.

- **Čtení dat**: Prostředí integration runtime se otevře připojení k SQL serveru a odešle tento dotaz. SQL Server odpoví odesílání datového proudu do modulu integration runtime prostřednictvím intranetu.
- **Serializace a komprese dat**: Prostředí integration runtime serializuje datový proud do formátu CSV a komprimuje data do datového proudu bzip2.
- **Zápis dat**: Prostředí integration runtime odešle datový proud bzip2 do úložiště objektů Blob přes internet.

Jak je vidět zpracování a přesunout streamování sekvenčním způsobem: SQL Server > LAN > modul runtime integrace > síť WAN > úložiště objektů Blob. Celkový výkon je chráněný branami minimální propustnost v kanálu.

![Tok dat](./media/copy-activity-performance/case-study-pic-1.png)

Nejméně jednu z těchto faktorů může způsobit snížení výkonu:

* **Zdroj**: Samotný Server SQL je Nízká propustnost kvůli velkým zatížením.
* **Modul runtime integrace v místním prostředí**:
  * **LAN**: Prostředí Integration runtime je umístěn daleko od počítači s SQL serverem a mají připojení s malou šířkou pásma.
  * **Prostředí Integration runtime**: Modul runtime integrace bylo dosaženo omezení zatížení provádět následující operace:
    * **Serializace**: Serializace datový proud do formátu CSV má pomalé propustnost.
    * **Komprese**: Jste zvolili pomalé kompresní kodek, například bzip2, což je 2.8 MB/s s Core i7.
  * **SÍŤ WAN**: Šířka pásma mezi podnikovou sítí a služby Azure je nízké, třeba T1 = 1,544 kB/s; T2 = 6,312 kb/s.
* **Jímka**: Úložiště objektů blob je Nízká propustnost. Tento scénář je nepravděpodobné, že by jeho smlouvu o úrovni služeb (SLA) zaručuje minimálně 60 MB/s.

V takovém případě může být komprese dat bzip2 zpomalení celého kanálu. Přepnutí na kompresní kodek gzip může zmírnit tyto potíže.

## <a name="references"></a>Odkazy

Tady je pro sledování výkonu a ladění pro některé z úložišť dat podporovaných odkazů:

* Azure Storage, která zahrnuje úložiště objektů Blob a Table storage: [Azure cíle škálovatelnosti úložiště](../storage/common/storage-scalability-targets.md) a [kontrolní seznam výkonu a škálovatelnosti služby Azure Storage](../storage/common/storage-performance-checklist.md).
* Azure SQL Database: Je možné [sledovat výkon](../sql-database/sql-database-single-database-monitor.md) a zkontrolovat procento jednotky transakcí databáze (DTU).
* Azure SQL Data Warehouse: Jeho funkce se měří v jednotkách datového skladu (Dwu). Zobrazit [spravovat výpočetní výkon v Azure SQL Data Warehouse (přehled)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Úrovně výkonu ve službě Azure Cosmos DB](../cosmos-db/performance-levels.md).
* V místním SQL serveru: [Monitorování a vyladění výkonu](https://msdn.microsoft.com/library/ms189081.aspx).
* S místními souborového serveru: [Optimalizace výkonu pro souborové servery](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Další postup
Najdete v dalších článcích aktivity kopírování:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Mapování schématu aktivity kopírování](copy-activity-schema-and-type-mapping.md)
- [Zkopírovat aktivitu odolnost proti chybám](copy-activity-fault-tolerance.md)
