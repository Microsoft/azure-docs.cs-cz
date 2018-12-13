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
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 7602524675edbf0e3ca96c74a2aba2eac48c417b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084069"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Průvodce laděním a výkonem aktivity kopírování
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-copy-activity-performance.md)
> * [Aktuální verze](copy-activity-performance.md)


Azure Data Factory za kopírování přináší prvotřídní zabezpečené, spolehlivé a vysoce výkonné datové načítání řešení. To vám umožňuje desítky kopii terabajty dat každý den bohaté nejrůznějších cloudových a místních úložišť dat. Neuvěřitelně rychlá data výkon při načítání je klíč, aby se mohli soustředit na základní problém "anglicky big data": sestavovat Pokročilá analytická řešení a získat podrobné informace z všechna tato data.

Azure poskytuje sadu podniková řešení datových skladů úložiště a data, a aktivitu kopírování, která nabízí vysoce optimalizovanému data načítání prostředí, které usnadňují konfiguraci a nastavení. Pouze jednu aktivitu kopírování, která můžete dosáhnout:

* Načtení dat do **Azure SQL Data Warehouse** na **1,2 GB/s**.
* Načtení dat do **úložiště objektů Blob v Azure** na **1,0 GB/s**
* Načtení dat do **Azure Data Lake Store** na **1,0 GB/s**

Tento článek popisuje:

* [Výkon referenční čísla](#performance-reference) pro podporované zdroje a jímky úložišť dat, které vám pomohou při plánování vašeho projektu.
* Funkce, které může zvýšit propustnost kopírování v různých scénářích, včetně [jednotky integrace dat](#data-integration-units), [paralelní kopírování](#parallel-copy), a [fázovaného kopírování](#staged-copy);
* [Průvodce laděním výkonu](#performance-tuning-steps) o tom, jak optimalizovat výkon a klíčové faktory, které mohou ovlivnit výkon kopírování.

> [!NOTE]
> Pokud nejste obeznámeni s aktivitou kopírování obecně platí, přečtěte si téma [přehled aktivit kopírování](copy-activity-overview.md) před čtením tohoto článku.
>

## <a name="performance-reference"></a>Referenční dokumentace výkonu

Referenci naleznete níže uvedená tabulka zobrazuje počet propustnost kopírování **v MB/s** pro dané zdroje a jímky dvojice **v aktivitě kopírování jednoho spuštění** na základě interní testování. Pro porovnání, také ukazuje, jak různé nastavení [jednotky integrace dat](#data-integration-units) nebo [modul Integration Runtime škálovatelnost](concepts-integration-runtime.md#self-hosted-integration-runtime) (více uzlů) může pomoct výkon kopírování.

![Přehled výkonu](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> Při provádění aktivity kopírování v prostředí Azure Integration Runtime je minimální povolený jednotky integrace dat, (dříve označované jako jednotky přesunu dat) dvě. Pokud není zadán, najdete v článku výchozí Data integrace jednotky se používají ve [jednotky integrace dat](#data-integration-units).

Odkazuje na mějte na paměti:

* Vypočítá se propustnost s použitím následujícího vzorce: [objem dat pro čtení ze zdroje] / [doba trvání běhu aktivity kopírování].
* Výkon referenční čísla v tabulce se měří pomocí [TPC-H](http://www.tpc.org/tpch/) datovou sadu v aktivitě kopírování jednoho spuštění. Testovací soubory pro souborové úložiště se víc souborů s 10GB velikosti.
* V úložištích dat Azure zdroj a jímka mají ve stejné oblasti Azure.
* Pro hybridní kopírování mezi místním a cloudovým úložištěm dat a každý uzel modul Integration Runtime byl spuštěn na počítači, který byl nezávisle na úložišti dat s níže specifikace. Pokud byla spuštěna jedna aktivita, operace kopírování spotřebovány pouze malou část testovací počítač procesor, paměť nebo šířky pásma sítě.
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
        <td>Internetové rozhraní: 10 GB/s; rozhraní sítě intranet: 40 GB/s</td>
    </tr>
    </table>


> [!TIP]
> Vyšší propustnosti můžete dosáhnout pomocí více dat integrace jednotek (DIÚ). Například s 100 DIUs, které můžete dosáhnout kopírování dat z objektů Blob v Azure do Azure Data Lake Store v **1.0GBps**. Zobrazit [jednotky integrace dat](#data-integration-units) podrobné informace o této funkci a podporované scénáře. 

## <a name="data-integration-units"></a>Jednotky integrace dat

A **částí integrace dat (DIÚ)** (dříve označované jako jednotka pohybu dat v cloudu nebo DMU) je míru, která představuje výkon (kombinaci procesoru, paměti a přidělení prostředků sítě) jedné jednotky ve službě Data Factory. **DIÚ platí jenom pro [prostředí Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)**, ale ne [modul Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

**Minimální jednotky integrace dat pro spuštění aktivity kopírování jsou dvě.** Pokud není zadán, následující tabulka uvádí výchozí DIUs používá ve scénářích různé kopie:

| Kopírování | Výchozí DIUs určené služby |
|:--- |:--- |
| Kopírovat data mezi úložišti souborů | Mezi 4 a 32 v závislosti na počtu a velikosti souborů. |
| Dalších scénářů kopírování | 4 |

Chcete-li přepsat toto výchozí nastavení, zadejte hodnotu **dataIntegrationUnits** vlastnost následujícím způsobem. **Povolené hodnoty** pro **dataIntegrationUnits** vlastnost **až 256**. **Skutečný počet DIUs** , že operace kopírování používá za běhu je rovna nebo menší než nakonfigurovaná hodnota, v závislosti na vašich dat vzor. Informace o úrovni výkonu, může se zobrazit při konfiguraci víc jednotek pro konkrétní kopírovat zdroje a jímky, najdete v článku [výkonu](#performance-reference).

Zobrazí se skutečně využité jednotky integrace dat pro každou kopii spustit v aktivitě kopírování výstup při spuštění aktivity monitorování. Další podrobnosti o [zkopírujte monitorování aktivit](copy-activity-overview.md#monitoring).

> [!NOTE]
> Nastavení DIUs **větší než 4** aktuálně funguje pouze tehdy, když jste **zkopírovat víc souborů z objektu Blob úložiště a Data Lake Storage/Amazon S3/Cloudová FTP/Cloudová SFTP pro všechna ostatní data cloudové úložiště.**.
>

**Příklad:**

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

Má **důležité** pamatovat, že se vám účtuje podle celkové doby trvání operace kopírování. Celková doba trvání, které se účtují pro přesun dat je celková doba trvání napříč DIUs. Pokud úloha kopírování používá k trvat jednu hodinu se dvěma jednotkami cloudu a teď trvá 15 minut s osm jednotek v cloudu, bude celkové vyúčtování skoro stejné zůstane.

## <a name="parallel-copy"></a>Paralelní kopírování

Můžete použít **parallelCopies** vlastnost umožňující označit, které chcete aktivitou kopírování pomocí paralelismu. Tato vlastnost si můžete představit jako maximální počet vláken v aktivitě kopírování, který může číst ze zdroje nebo zapisovat do svá úložiště dat jímky paralelně.

Pro každé spuštění aktivity kopírování Data Factory určuje počet paralelních kopií pro použití ke kopírování dat ze zdroje dat, ukládání a cílového datového úložiště. Výchozí počet paralelních kopie, které používá závisí na typu zdroje a jímky, kterou používáte:

| Kopírování | Výchozí počet paralelních kopií určené služby |
| --- | --- |
| Kopírovat data mezi úložišti souborů |Závisí na velikosti souborů a počet jednotek integrace dat (DIUs) používá ke kopírování dat mezi dvěma cloudovými úložišti dat nebo konfigurace fyzického počítače modul Integration Runtime. |
| Kopírování dat z jakékoli zdrojové úložiště dat do služby Azure Table storage |4 |
| Dalších scénářů kopírování |1 |

[!TIP]
> Při kopírování dat mezi úložišti souborů, výchozí chování (automatické určit) obvykle poskytují nejlepší propustnost. 

K řízení zatížení na počítačích, které jsou hostiteli vaše data ukládá nebo pro optimalizaci výkonu kopírování, můžete výchozí hodnotu přepsat a zadat hodnotu **parallelCopies** vlastnost. Hodnota musí být celé číslo větší než nebo rovno 1. V době běhu pro zajištění nejlepšího výkonu, aktivita kopírování používá hodnotu, která je menší než nebo rovna hodnotě, kterou jste nastavili.

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

Odkazuje na mějte na paměti:

* Při kopírování dat mezi souborové úložiště **parallelCopies** určit paralelismu na úrovni souboru. Bloků v rámci jednoho souboru by se stalo pod automaticky a transparentně a je navržena k načtení dat v paralelní aplikace a kolmě parallelCopies pomocí ideální velikost bloku vhodný typ daného zdroje dat úložiště. Skutečný počet paralelních kopie služba pro přesun dat se používá pro operaci kopírování v době běhu je delší než počet souborů, které máte. Pokud je chování kopírování nastavené **mergeFile**, aktivita kopírování nemůže využívají paralelismus úrovni souboru.
* Pokud zadáte hodnotu **parallelCopies** vlastnost, zvažte zvýšení zátěže na svá úložiště dat zdroje a jímky a modul Integration Runtime, pokud aktivita kopírování je možnost jím například pro hybridní kopírování. K tomu dojde, zejména pokud máte více souběžných spuštění stejného aktivit, které běží na stejné úložiště dat nebo aktivity. Pokud si všimnete, že úložiště dat nebo modul Integration Runtime je zahlcen zatížení, snížit **parallelCopies** hodnota, která má-li snížit zatížení.
* Při kopírování dat z úložiště, které nejsou na základě souboru do úložišť, které jsou založené na souboru ignoruje služba pro přesun dat **parallelCopies** vlastnost. I v případě, že je zadán paralelismu, není použita v tomto případě.
* **parallelCopies** je ortogonální k **dataIntegrationUnits**. Předchozí se počítá přes všechny jednotky integrace Data.

## <a name="staged-copy"></a>Kopírování dvoufázové instalace

Při kopírování dat ze zdrojového úložiště dat do úložiště dat jímky, můžete zvolit použití Blob storage jako dočasné pracovní úložiště. Pracovní je zvláště užitečná v následujících případech:

- **Chcete ingestovat data z různých zdrojů dat do SQL Data Warehouse pomocí PolyBase**. SQL Data Warehouse používá k načtení velkých objemů dat do SQL Data Warehouse PolyBase jako vhodný mechanismus vysokou propustnost. Však musí být zdroj dat v Blob storage nebo Azure Data Lake Store a musí splňovat další kritéria. Při načítání dat z úložiště dat než Blob storage nebo Azure Data Lake Store můžete aktivovat kopírování prostřednictvím Blob storage dočasné pracovní data. V takovém případě služby Data Factory provádí transformace požadovaná data k zajištění, že splňují požadavky PolyBase. Potom použije PolyBase k načtení dat do SQL Data Warehouse efektivně. Další informace najdete v tématu [použití PolyBase k načítání dat do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Někdy trvá nějakou provést přesun hybridních dat (to znamená, zkopírujte z místního úložiště dat do cloudového úložiště dat) přes pomalé síťové připojení**. Kvůli zvýšení výkonu se vám pomůže dvoufázové instalace kopírování komprimovat data dostupná místně tak, že vyžaduje méně času k přesunu dat do pracovní úložiště dat v cloudu a dekomprimaci dat v úložišti pracovní před načtením do cílového úložiště dat.
- **Nechcete otevřít porty než 80 a port 443 v bráně firewall kvůli podnikovým zásadám IT**. Například při kopírování dat z do místního úložiště dat jímky Azure SQL Database nebo Azure SQL Data Warehouse jímky, budete muset aktivovat odchozí komunikaci TCP na portu 1433 pro bránu Windows firewall a váš podnikový firewall. V tomto scénáři můžete dvoufázové instalace kopírování využít modul Integration Runtime nejprve zkopírovat data do úložiště objektů Blob pracovní instance prostřednictvím protokolu HTTP nebo HTTPS na portu 443 a pak načíst data do SQL Database nebo SQL Data Warehouse z pracovního objektu Blob úložiště. V tomto toku není nutné povolit port 1433.

### <a name="how-staged-copy-works"></a>Kopírování funguje jak dvoufázové instalace

Při aktivaci pracovní funkce data se zkopíruje ze zdrojového úložiště dat do přípravného úložiště objektů Blob (funkce přineste si vlastní). V dalším kroku data zkopírována z pracovní úložiště dat do úložiště dat jímky. Data Factory automaticky spravuje tok dvoufázová za vás. Data Factory také vyčistí dočasná data z přípravného úložiště po dokončení přesunu dat.

![Kopírování dvoufázové instalace](media/copy-activity-performance/staged-copy.png)

Když aktivujete přesun dat pomocí přípravné úložiště, můžete určit, jestli mají data, která mají komprimovat ještě před přesunem dat ze zdrojového úložiště dat do úložiště dočasné nebo pracovní data a potom dekomprimovat před přesouvá data z průběžného nebo přípravu dat Uložit do úložiště dat jímky.

V současné době nelze kopírovat data mezi dvěma v místním úložišti dat pomocí přípravné úložiště.

### <a name="configuration"></a>Konfigurace

Konfigurace **enableStaging** nastavení k určení, jestli chcete data budou umístěné v úložišti objektů Blob, než je načtete do cílového úložiště dat v aktivitě kopírování. Pokud nastavíte **enableStaging** k `TRUE`, zadat další vlastnosti uvedené v následující tabulce. Pokud ho nemáte, budete potřebovat k vytvoření služby Azure Storage nebo úložiště sdíleného přístupu podpis propojené služby.

| Vlastnost | Popis | Výchozí hodnota | Požaduje se |
| --- | --- | --- | --- |
| **enableStaging** |Určete, jestli chcete kopírovat data prostřednictvím jako dočasné pracovní úložiště. |False |Ne |
| **linkedServiceName** |Zadejte název [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) propojenou službu, která odkazuje na instanci úložiště, které můžete použít jako dočasné pracovní úložiště. <br/><br/> Úložiště pomocí sdíleného přístupového podpisu nelze použít k načtení dat do SQL Data Warehouse pomocí PolyBase. Můžete ji použít v jiných scénářích. |neuvedeno |Ano, pokud **enableStaging** nastavena na hodnotu TRUE |
| **Cesta** |Zadejte cestu úložiště objektů Blob, který chcete s daty, dvoufázové instalace. Pokud nezadáte cestu, služba vytvoří kontejner pro uložení dočasných dat. <br/><br/> Zadejte cestu, pouze v případě, že používáte úložiště pomocí sdíleného přístupového podpisu nebo vyžadujete dočasných dat v konkrétním umístění. |neuvedeno |Ne |
| **enableCompression** |Určuje, zda data je nutné zkomprimovat. předtím, než je zkopírovat do cíle. Toto nastavení omezuje objem dat přenášených. |False |Ne |

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

Bude se vám účtovat na základě dva kroky: doba kopírování a zkopírujte typu.

* Při použití přípravy během cloudové kopírování (kopírování dat z cloudového úložiště dat do jiného cloudového úložiště dat, obě fáze možnost pomocí prostředí Azure Integration Runtime), bude se vám účtovat [Součet doba kopírování pro kroky 1 a 2] x [cloudové kopie Jednotková cena].
* Při použití přípravy při kopírování hybridní (kopírování dat z úložiště dat v místním cloudovým úložištěm dat jedné fáze pověřený modul Integration Runtime), bude vám účtována [doba kopírování hybridní] x [hybridní kopírování Jednotková cena] + [cloudu doba kopírování] x [cloudu kopírování Jednotková cena].

## <a name="performance-tuning-steps"></a>Postup optimalizace výkonu

Doporučujeme vám, že je provést tyto kroky pro optimalizaci výkonu služby Data Factory s aktivitou kopírování:

1. **Stanovení základní úrovně**. Během fáze vývoje Otestujte svůj kanál pomocí aktivity kopírování proti ukázku reprezentativní data. Shromažďovat podrobnosti provádění a výkonové charakteristiky následující [zkopírujte monitorování aktivit](copy-activity-overview.md#monitoring).

2. **Diagnostika a optimalizace výkonu**. Pokud, které můžete sledovat výkon nesplňuje vaše očekávání, budete muset identifikovat kritické body výkonu. Potom optimalizace výkonu můžete odebrat nebo snižují dopad kritické body. Úplný popis Diagnostika výkonu je nad rámec tohoto článku, ale tady jsou některé běžné aspekty:

   * Funkce výkonu:
     * [Paralelní kopírování](#parallel-copy)
     * [Jednotky integrace dat](#data-integration-units)
     * [Kopírování dvoufázové instalace](#staged-copy)
     * [Škálovatelnost v místním prostředí Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Místní prostředí Integration Runtime](#considerations-for-self-hosted-integration-runtime)
   * [Zdroj](#considerations-for-the-source)
   * [jímka](#considerations-for-the-sink)
   * [Serializace a deserializace](#considerations-for-serialization-and-deserialization)
   * [Komprese](#considerations-for-compression)
   * [Mapování sloupce](#considerations-for-column-mapping)
   * [Další aspekty](#other-considerations)

3. **Rozbalte položku konfigurace pro celou datovou sadu**. Jakmile budete spokojeni s výsledky spuštění a výkonem, můžete rozbalit definice a kanál pro celou datovou sadu.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Důležité informace týkající se místní prostředí Integration Runtime

Pokud vaše aktivita kopírování provádí na modul Integration Runtime, vezměte na vědomí následující:

**Instalační program**: doporučujeme použít vyhrazený počítač na hostitele Integration Runtime. Zobrazit [důležité informace týkající se postará modul Integration Runtime pomocí](concepts-integration-runtime.md).

**Horizontální navýšení kapacity**: jeden logický modul Integration Runtime pomocí jednoho nebo více uzlů může sloužit několika spustí aktivita kopírování ve stejnou dobu současně. Pokud nemáte náročné požadavky na přesun hybridních dat s velkým počtem souběžných kopírování spuštění aktivit nebo pomocí velkého objemu dat ke kopírování, zvažte pro [horizontální navýšení kapacity modul Integration Runtime](create-self-hosted-integration-runtime.md#high-availability-and-scalability) tak, aby zřídit další zdroj Umožněte kopírování.

## <a name="considerations-for-the-source"></a>Důležité informace pro zdroj

### <a name="general"></a>Obecné

Ujistěte se, že základní úložiště dat není zahlcen jiné úlohy, které jsou spuštěny na nebo před ním.

Microsoft úložišť dat, naleznete v tématu [monitorování a optimalizace témata](#performance-reference) , které jsou specifické pro úložiště dat a pomohou vám zjistit data ukládat výkonové charakteristiky, minimalizovat dobu odezvy a maximalizuje propustnost.

* Pokud zkopírujete data **ze služby Blob storage do SQL Data Warehouse**, zvažte použití **PolyBase** pro zvýšení výkonu. Zobrazit [použití PolyBase k načítání dat do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) podrobnosti.
* Pokud zkopírujete data **z HDFS do Azure Blob nebo Azure Data Lake Store**, zvažte použití **DistCp** pro zvýšení výkonu. Zobrazit [použití DistCp ke kopírování dat z HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) podrobnosti.
* Pokud zkopírujete data **z Redshift do Azure SQL Data Warehouse/Azure BLob nebo Azure Data Lake Store**, zvažte použití **uvolnění** pro zvýšení výkonu. Zobrazit [uvolnění použít ke zkopírování dat z Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) podrobnosti.

### <a name="file-based-data-stores"></a>Úložiště dat na základě souboru

* **Průměrná velikost souboru a počet souborů**: aktivitu kopírování, která přenáší data jeden soubor současně. Pomocí stejné množství dat k přesunutí je nižší, pokud data se skládá z mnoha malých souborů spíše než několik velkých souborů z důvodu spuštění fáze pro každý soubor celkovou propustnost. Pokud je to možné, sloučit proto malých souborů do větší soubory získáte vyšší propustnost.
* **Soubor formátů a komprese**: Další způsoby, jak vylepšit výkon, najdete v článku [důležité informace k serializaci a deserializaci](#considerations-for-serialization-and-deserialization) a [důležité informace týkající se komprese](#considerations-for-compression) oddíly.

### <a name="relational-data-stores"></a>Úložiště relačních dat

* **Vzorek dat**: kopírování propustnost má vliv na vaše schéma tabulky. Velký řádek velikosti poskytuje lepší výkon než velikost malých řádku ke zkopírování stejné množství dat. Důvodem je, že databáze můžete efektivněji načíst menší počet dávek dat, které obsahují menší počet řádků.
* **Dotaz nebo uloženou proceduru**: optimalizace logiku dotazu nebo uložené procedury, které jste zadali v zdroje aktivity kopírování se načíst data efektivněji.

## <a name="considerations-for-the-sink"></a>Důležité informace pro jímku

### <a name="general"></a>Obecné

Ujistěte se, že základní úložiště dat není zahlcen jiné úlohy, které jsou spuštěny na nebo před ním.

Microsoft úložišť dat, najdete v tématu [monitorování a optimalizace témata](#performance-reference) , které jsou specifické pro úložiště dat. Tato témata můžete pochopit vlastnosti výkonu úložiště dat a jak minimalizovat dobu odezvy a maximalizuje propustnost.

* Pokud zkopírujete data **ze služby Blob storage do SQL Data Warehouse**, zvažte použití **PolyBase** pro zvýšení výkonu. Zobrazit [použití PolyBase k načítání dat do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) podrobnosti.
* Pokud zkopírujete data **z HDFS do Azure Blob nebo Azure Data Lake Store**, zvažte použití **DistCp** pro zvýšení výkonu. Zobrazit [použití DistCp ke kopírování dat z HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) podrobnosti.
* Pokud zkopírujete data **z Redshift do Azure SQL Data Warehouse/Azure BLob nebo Azure Data Lake Store**, zvažte použití **uvolnění** pro zvýšení výkonu. Zobrazit [uvolnění použít ke zkopírování dat z Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) podrobnosti.

### <a name="file-based-data-stores"></a>Úložiště dat na základě souboru

* **Zkopírujte chování**: Při kopírování dat z různých datových souborové úložiště, aktivita kopírování má tři možnosti prostřednictvím **copyBehavior** vlastnost. Zachová hierarchie, sloučí hierarchie nebo sloučí soubory. Zachování nebo sloučení hierarchie má žádné nebo téměř žádné nároky na výkon, ale slučování souborů způsobí, že chcete zvýšit nároky na výkon.
* **Soubor formátů a komprese**: najdete v článku [důležité informace k serializaci a deserializaci](#considerations-for-serialization-and-deserialization) a [důležité informace týkající se komprese](#considerations-for-compression) oddíly pro další způsoby, jak zlepšit výkon.

### <a name="relational-data-stores"></a>Úložiště relačních dat

* **Zkopírujte chování**: v závislosti na vlastnosti, které jste nastavili pro **sqlSink**, aktivitu kopírování, která zapisuje data do cílové databáze různými způsoby.
  * Ve výchozím nastavení připojí data přesun služba používá rozhraní API hromadného kopírování k vložení dat v režimu, který poskytuje nejlepší výkon.
  * Při konfiguraci uloženou proceduru v jímce, platí databázi jednoho řádku dat v době místo jako hromadné načtení. Výkon dochází k výraznému snížení. Pokud vaši datovou sadu je velká, pokud se dá použít, zvažte možnost použití **preCopyScript** vlastnost.
  * Pokud nakonfigurujete **preCopyScript** spustit vlastností pro každou aktivitu kopírování, služba spustí skript a potom pomocí rozhraní API hromadného kopírování vložte data. Například pokud chcete přepsat celou tabulku s nejnovější data, můžete určit skript, který nejprve odstranit všechny záznamy před hromadného načtení nová data ze zdroje.
* **Velikost dat vzor a batch**:
  * Schéma tabulky ovlivňuje kopírování propustnost. Ke zkopírování stejné množství dat, velký řádek velikosti umožňuje lepší výkon než velikost malých řádku, protože databáze můžete efektivněji menší počet dávek dat potvrzení změn.
  * Aktivitu kopírování, která vloží data z řady dávky. Můžete nastavit počet řádků v dávce pomocí **writeBatchSize** vlastnost. Pokud data obsahují malé řádků, můžete nastavit **writeBatchSize** vlastnost s vyšší hodnota můžou využívat výhody nižší režijní náklady na služby batch a vyšší propustnost. Je-li velikost řádku vašich dat je velká, dejte pozor, když zvýšíte **writeBatchSize**. Vysoká hodnota může vést k kopírování chybu způsobenou přetížení databáze.

### <a name="nosql-stores"></a>Úložišť typu NoSQL

* Pro **Table storage**:
  * **Oddíl**: zápis dat do oddílů prokládané výrazně snižuje výkon. Zdrojová data řadit klíč oddílu tak, aby vložení dat efektivně do jednoho oddílu po druhé, nebo upravte logiku k zápisu dat do jednoho oddílu.

## <a name="considerations-for-serialization-and-deserialization"></a>Důležité informace týkající se serializace a deserializace

Serializace a deserializace může dojít, pokud vstupní datové sady nebo výstupní datové sady je soubor. Zobrazit [podporované formáty souborů a komprese](supported-file-formats-and-compression-codecs.md) s podrobnostmi o podporovaných formátech souborů pomocí aktivity kopírování.

**Zkopírujte chování**:

* Kopírování souborů mezi úložišti dat na základě souboru:
  * Když vstupní a výstupní datové sady obě mají stejné nebo žádné nastavení formátu souboru, služba pro přesun dat provádí **binární kopie** bez jakýchkoli serializace nebo deserializace. Zobrazí větší propustnost v porovnání s scénář, ve které se liší od sebe navzájem nastavení formátu souboru zdroje a jímky.
  * Pokud vstupní a výstupní datové sady obě jsou ve formátu textu a pouze kódování typ se liší, služba pro přesun dat se jenom převod kódování. Neprovádí žádné serializace a deserializace, což způsobí, že některé výkonu režie ve srovnání s binární kopie.
  * Pokud vstupní a výstupní datové sady i mají různé formáty souborů nebo různé konfigurace, jako je oddělovače, služba pro přesun dat deserializuje zdroje dat do datového proudu, transformaci a pak ho serializovat do výstupní formát, který jste určili. Výsledkem této operace mnohem více významné výkonnostní režii ve srovnání s další scénáře.
* Při kopírování souborů do a z úložiště dat, které nejsou založené na souboru (například z úložiště založené na souborech do relačního úložiště) serializaci nebo deserializaci krok je povinný. Tento krok vede významné výkonnostní režii.

**Formát souboru**: formát souboru zvolíte může ovlivnit výkon kopírování. Například je Avro kompaktní binární formát, který ukládá metadata s daty. Má širokou podporu v ekosystému Hadoop pro zpracování a dotazování na ně. Je však dražší, serializace a deserializace, což vede k nižší propustnost kopírování ve srovnání s textový formát Avro. Ujistěte se, podle vašeho výběru formátu v průběhu zpracování toku komplexně. Začněte s co tvoří data uložená v úložišti zdroje dat nebo extrahovat z externích systémů: nejlepší formát pro úložiště, analytické zpracování a dotazování; a v jakém formátu data exportují do datového tržiště pro nástroje pro vytváření sestav a vizualizace. Někdy formát souboru, který je neoptimální pro čtení a zápisu může být dobrou volbou, pokud byste zvážit celkové analytické procesu.

## <a name="considerations-for-compression"></a>Důležité informace pro kompresi

Pokud vstupní nebo výstupní datové sady je soubor, můžete nastavit aktivitu kopírování k provedení kompresi nebo dekompresi jako zapíše data do cíle. Při výběru komprese provedete kompromis mezi vstupně výstupní (I/O) a procesoru. Komprese dat příplatek ve výpočetních prostředcích. Ale na oplátku omezuje v / v sítě a úložiště. V závislosti na vašich dat může se zobrazit boost v celkovou propustnost kopírování.

**Kodek**: každý kompresní kodek má své výhody. Například bzip2 má nejnižší kopírování propustnost, ale získat nejlepší výkon dotazů Hive pomocí bzip2, protože je možné rozdělit ke zpracování. GZIP je nepoužít možnost vyvážená nejvíce a se nejčastěji používá. Zvolte kodek, který nejlépe vyhovuje vaší situaci začátku do konce.

**Úroveň**: můžete vybrat ze dvou možností pro každý kompresní kodek: nejrychlejší komprimované a optimálně komprimované. Nejrychlejší komprimované možnost co nejrychleji komprimuje data i v případě, že výsledný soubor není komprimována optimálně. Optimálně komprimovaný možnost tráví víc času na komprese a vrací minimální nároky na data. Obě možnosti zobrazíte, která poskytuje lepší výkon ve vašem případě můžete otestovat.

**Potřeba**: kopírování velkých objemů dat mezi místním úložištěm a cloudem, zvažte použití [fázovaného kopírování](#staged-copy) pomocí komprese zapnuta. Použití dočasné úložiště je užitečné, když omezujícím faktorem je šířka pásma podnikové sítě a služby Azure a chcete, aby vstupní datové sady i výstupní datové sady v nekomprimovaných formuláře.

## <a name="considerations-for-column-mapping"></a>Důležité informace týkající se mapování sloupce

Můžete nastavit **columnMappings** vlastnost v aktivitě kopírování do mapy všechny, nebo jen některé ze sloupců vstupní a výstupní sloupce. Poté, co služba pro přesun dat načte data ze zdroje, je potřeba provést mapování sloupců s daty před zapisuje data do jímky. Tato další zpracování snižuje kopírování propustnost.

Pokud zdrojové úložiště dat je zadávat dotazy, pokud je relačního úložiště, jako je SQL Database nebo SQL Server, nebo pokud je úložiště typu NoSQL, jako je Table storage nebo Azure Cosmos DB, představte si třeba doručením (push) sloupce filtrování a změna uspořádání logiku pro **dotazu** vlastnosti namísto použití mapování sloupců. Tímto způsobem projekce nastane, když služba pro přesun dat čte data ze zdrojového úložiště dat, kde je mnohem efektivnější.

Další informace z [mapování schématu aktivity kopírování](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Další důležité informace

Pokud je velká velikost dat, který chcete zkopírovat, můžete upravit vaši obchodní logiku pro další oddíl data a naplánovat aktivitu kopírování, která spouštět častěji ke snížení velikosti dat pro každé spuštění aktivity kopírování.

Buďte opatrní počet datových sad a kopie aktivity vyžadující připojení do stejného úložiště dat ve stejnou dobu služby Data Factory. Mnoho souběžných kopírování úloh může omezit úložiště dat a vést ke snížení výkonu, opakování interní úlohu kopírování a v některých případech se selhání spuštění.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Ukázkový scénář: kopírování z místního SQL serveru do úložiště objektů Blob

**Scénář**: kanál je určený pro kopírování dat z místních SQL serveru do úložiště objektů Blob ve formátu CSV. Chcete-li úlohu kopírování rychleji, by měl zkomprimují soubory CSV do formátu bzip2.

**Testování a analýzy**: propustnost aktivitu kopírování, která je menší než 2 MB/s, což je mnohem pomalejší než srovnávacího testu výkonu.

**Analýza výkonu a ladění**: řešení potíží s výkonem, Podívejme se na způsobu zpracování a přesunout data.

1. **Čtení dat**: modul runtime integrace otevře připojení k SQL serveru a odešle tento dotaz. SQL Server odpoví odesláním datový proud prostředí integration runtime prostřednictvím intranetu.
2. **Serializace a komprese dat**: modul runtime integrace serializuje datový proud do formátu CSV a komprimuje data do datového proudu bzip2.
3. **Zápis dat**: modul runtime integrace odešle datový proud bzip2 do úložiště objektů Blob přes Internet.

Jak je vidět, data se zpracování a streamování sekvenčním způsobem přesunout: systému SQL Server > LAN > modul runtime integrace > síť WAN > úložiště objektů Blob. **Celkový výkon je chráněný branami minimální propustnost přes kanál**.

![Tok dat](./media/copy-activity-performance/case-study-pic-1.png)

Nejméně jednu z těchto faktorů může způsobit snížení výkonu:

* **Zdroj**: samotného SQL serveru je Nízká propustnost kvůli velkým zatížením.
* **Modul Runtime integrace v místním prostředí**:
  * **LAN**: modul runtime integrace je umístěn daleko od počítači s SQL serverem a mají připojení s malou šířkou pásma.
  * **Prostředí Integration runtime**: modul runtime integrace bylo dosaženo omezení zatížení provádět následující operace:
    * **Serializace**: serializace datový proud do formátu CSV má pomalé propustnost.
    * **Komprese**: zvolili pomalé kompresní kodek (například bzip2, což je 2.8 MB/s s Core i7).
  * **Síť WAN**: šířku pásma mezi podnikovou sítí a služby Azure je nízká (například T1 = 1,544 kB/s; T2 = 6,312 kb/s).
* **Jímka**: Blob storage je Nízká propustnost. (Tento scénář je nepravděpodobné, že by jeho SLA zaručuje minimálně 60 MB/s.)

V takovém případě může být komprese dat bzip2 zpomalení celého kanálu. Přepnutí na kompresní kodek gzip může zmírnit tyto potíže.

## <a name="reference"></a>Referenční informace

Tady je pro sledování výkonu a ladění pro některé z úložišť dat podporovaných odkazů:

* Azure Storage (včetně úložiště objektů Blob a Table storage): [cíle škálovatelnosti služby Azure Storage](../storage/common/storage-scalability-targets.md) a [kontrolní seznam výkonu a škálovatelnosti služby Azure Storage](../storage/common/storage-performance-checklist.md)
* Azure SQL Database: Můžete [sledovat výkon](../sql-database/sql-database-single-database-monitor.md) a zkontrolovat procento databáze transakce jednotek (DTU)
* Azure SQL Data Warehouse: Jeho funkce se měří v jednotkách datového skladu (Dwu); Zobrazit [spravovat výpočetní výkon v Azure SQL Data Warehouse (přehled)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [úrovní výkonu ve službě Azure Cosmos DB](../cosmos-db/performance-levels.md)
* V místním SQL serveru: [monitorování a ladění výkonu](https://msdn.microsoft.com/library/ms189081.aspx)
* Místní souborový server: [optimalizace výkonu pro souborové servery](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>Další postup
Zobrazit další články o aktivitě kopírování:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Mapování schématu aktivity kopírování](copy-activity-schema-and-type-mapping.md)
- [Zkopírovat aktivitu odolnost proti chybám](copy-activity-fault-tolerance.md)
