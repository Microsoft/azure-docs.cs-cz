---
title: Zkopírujte aktivity výkonu a vyladění průvodce v Azure Data Factory | Microsoft Docs
description: Další informace o klíčových faktorů, které ovlivňují výkon přesun dat v Azure Data Factory při použití aktivity kopírování.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: jingwang
ms.openlocfilehash: c43973a7e5070676fc0f32a4c8923d57a479f884
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Zkopírujte aktivity výkonu a vyladění Průvodce
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-copy-activity-performance.md)
> * [Verze 2 – Preview](copy-activity-performance.md)


Aktivita kopírování Azure Data Factory nabízí prvotřídní dat zabezpečeným, spolehlivým a vysoce výkonné načítání řešení. Ji budete kopie desítkami terabajtů dat pro každý den s bohatou různých cloudové a místní úložiště dat. Výkon při načítání dat svěží fast je klíč k zajištění, můžete se zaměřit na problém "velkých objemů dat" základní: vytváření řešení pro pokročilou analýzu a získávání hlubšímu porozumění z všechno, co data.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [výkonu kopie aktivity v datové továrně verze 1](v1/data-factory-copy-activity-performance.md).

Azure poskytuje sadu podnikové úrovni řešení pro úložiště a datového skladu dat a aktivity kopírování nabízí vysoce optimalizovaného data načítání prostředí, které se snadno konfiguraci a nastavení. Jenom jedna kopie aktivity můžete dosáhnout:

* Načítání dat do **Azure SQL Data Warehouse** v **1,2 GB/s**.
* Načítání dat do **úložiště objektů Azure Blob** v **1.0 GB/s**
* Načítání dat do **Azure Data Lake Store** v **1.0 GB/s**

Tento článek popisuje:

* [Výkon referenční čísla](#performance-reference) podporováno zdroj a jímka úložiště dat, které vám pomohou naplánovat projektu;
* Funkce, které může zvýšit propustnost kopírování v různých scénářích, včetně [jednotky přesun dat v cloudu](#cloud-data-movement-units), [paralelní kopie](#parallel-copy), a [připravený kopie](#staged-copy);
* [Ladění pokyny výkonu](#performance-tuning-steps) o tom, jak optimalizovat výkon a klíčové faktory, které může ovlivnit výkon kopírování.

> [!NOTE]
> Pokud nejste obeznámeni s aktivitou kopírování obecně, přečtěte si téma [kopie aktivity přehled](copy-activity-overview.md) před přečtení tohoto článku.
>

## <a name="performance-reference"></a>Referenční dokumentace výkonu

Jako odkaz, níže uvedená tabulka zobrazuje číslo propustnost kopie **v MB/s** pro daný zdroj a jímka dvojice **v aktivitě sady jedna kopie spustit** založené na interní testování. Pro porovnání, také ukazuje, jak budou různí nastavení [jednotky přesun dat v cloudu](#cloud-data-movement-units) nebo [Self-hosted integrace Runtime škálovatelnost](concepts-integration-runtime.md#self-hosted-integration-runtime) (více uzlů) může pomoct na výkon kopírování.

![Matice výkonu](./media/copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>V Azure Data Factory verze 2 při aktivitě kopírování se spustí na modulu Runtime integrace Azure, jednotky přesun dat minimální povolené cloudu je dva. Pokud není zadaný, najdete v části jednotky přesun dat výchozí použitá v [jednotky přesun dat v cloudu](#cloud-data-movement-units).

Všimněte si body:

* Propustnost je vypočítána pomocí následujícího vzorce: [velikost dat číst ze zdroje] / [spustit doba trvání aktivity kopírování].
* Referenční čísla výkonu v tabulce se měří pomocí [TPC-H](http://www.tpc.org/tpch/) datovou sadu v jedna kopie aktivity při spuštění.
* V úložištích dat Azure zdroj a jímka mají ve stejné oblasti Azure.
* Pro hybridní kopírování mezi místními a cloudovými úložiště dat, každý uzel Self-hosted integrace Runtime byla spuštěna na počítači, který byl samostatné z úložiště dat s níže specifikace. Pokud byla spuštěna jediné aktivity, operace kopírování spotřebováno pouze malou část CPU, paměť či šířku pásma sítě testovací počítač.
    <table>
    <tr>
        <td>CPU</td>
        <td>32 jader 2,20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Paměť</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Síť</td>
        <td>Internetové rozhraní: 10 GB/s; rozhraní sítě intranet: 40 GB/s</td>
    </tr>
    </table>


> [!TIP]
> Vyšší propustnost můžete dosáhnout pomocí jednotky další přesun dat (DMUs) než výchozí, povolená maximální DMUs, které jsou 32 pro spuštění aktivity kopírování cloudu do cloudu. Například s 100 DMUs, můžete dosáhnout kopírování dat z objektu Blob Azure do Azure Data Lake Store v **1.0GBps**. Najdete v článku [jednotky přesun dat v cloudu](#cloud-data-movement-units) část Podrobnosti o této funkci a podporovaném scénáři. Obraťte se na [podporu Azure](https://azure.microsoft.com/support/) požádat o další DMUs.

## <a name="cloud-data-movement-units"></a>Jednotky přesun dat cloudu

A **jednotky přesun dat cloudu (DMU)** je míra, která reprezentuje výkon (kombinaci procesoru, paměti a přidělení prostředků sítě) v objektu pro vytváření dat na jednu jednotku. **DMU se vztahuje pouze na [Runtime integrace Azure](concepts-integration-runtime.md#azure-integration-runtime)**, ale ne [Self-hosted integrace Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

**Jednotky přesun dat minimální cloudu na základě kterých kopie aktivity při spuštění je dva.** Pokud není zadaný, následující tabulka uvádí výchozí DMUs, používá ve scénářích různé kopie:

| Kopírování | Výchozí DMUs určit službou |
|:--- |:--- |
| Kopírování dat mezi úložišti na základě souborů | Mezi 4 a 32 v závislosti na počtu a velikosti souborů. |
| Všechny ostatní kopie scénáře | 4 |

Pokud chcete přepsat toto výchozí nastavení, zadejte hodnotu **cloudDataMovementUnits** vlastnost následujícím způsobem. **Povolené hodnoty** pro **cloudDataMovementUnits** vlastnost je **až 256**. **Skutečný počet cloudu DMUs** že kopírování se používá v době běhu je rovna nebo menší než nakonfigurovaná hodnota, v závislosti na vaší vzorek dat. Informace o úrovni výkonnější se mohou objevit, když konfigurujete další jednotky pro konkrétní kopie zdroj a jímka najdete v tématu [referenční dokumentace výkonu](#performance-reference).

Zobrazí se ve skutečnosti použít cloudové jednotky přesun dat pro každou kopii spustit v aktivitě kopírování výstup při spuštění aktivity monitorování. Další informace z podrobností o [kopírovat, pokud chcete monitorování aktivit](copy-activity-overview.md#monitoring).

> [!NOTE]
> Pokud potřebujete další cloudu DMUs pro vyšší propustnost, obraťte se na [podporu Azure](https://azure.microsoft.com/support/). Nastavení 8 a vyšší aktuálně funguje pouze tehdy, když jste **zkopírovat soubory z objektu Blob úložiště nebo Data Lake Store nebo Amazon S3 nebo cloudem FTP nebo cloudem SFTP žádné jiným úložištím dat cloudu**.
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
            "cloudDataMovementUnits": 32
        }
    }
]
```

### <a name="cloud-data-movement-units-billing-impact"></a>Jednotky přesunu dat cloudové fakturace dopad

Má **důležité** pamatovat, že budou účtovat na základě celkové doby operace kopírování. Celková doba trvání, které se účtují pro přesun dat je celková doba trvání mezi DMUs. Pokud úlohu kopírování se používá k trvat hodinu s dvě jednotky cloudu a teď bude trvat 15 minut u osm jednotek cloudu, zůstane celkové faktury téměř stejný.

## <a name="parallel-copy"></a>Paralelní kopie

Můžete použít **parallelCopies** vlastnost označující paralelismus, který chcete použít aktivitu kopírování. Tato vlastnost si můžete představit jako maximální počet vláken v rámci aktivitu kopírování, která můžou číst ze zdroje nebo zapisovat do vašeho úložiště dat podřízený paralelně.

Objekt pro vytváření dat pro každou aktivitu kopírování, spuštění, určuje počet paralelních kopií, které chcete použít ke zkopírování dat ze zdroje dat, ukládání a k datům cílového úložiště. Výchozí počet paralelní kopie, které používá závisí na typu zdroj a jímka, kterou používáte:

| Kopírování | Výchozí paralelní kopie počet určit službou |
| --- | --- |
| Kopírování dat mezi úložišti na základě souborů |Závisí na velikosti souborů a počet cloudu jednotek přesun dat (DMUs) používat ke kopírování dat mezi dvěma cloudové úložiště dat nebo fyzické konfigurace počítače Self-hosted integrace Runtime. |
| Kopírování dat z jakékoli zdrojového úložiště dat do úložiště Azure Table |4 |
| Všechny ostatní kopie scénáře |1 |

[!TIP]
> Při kopírování dat mezi úložišti na základě souborů, použije se výchozí chování (automaticky zjistit) obvykle poskytují nejlepší propustnost. 

K řízení zatížení na počítačích, které hostují vaše data ukládá, nebo k ladění výkonu kopie, můžete se rozhodnout přepsat výchozí hodnotu a zadejte hodnotu **parallelCopies** vlastnost. Hodnota musí být celé číslo větší než nebo rovno 1. Za běhu pro nejlepší výkon, používá aktivitu kopírování hodnotu, která je menší než nebo rovna hodnotě, který nastavíte.

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

Všimněte si body:

* Při kopírování dat mezi úložišti na základě souborů **parallelCopies** určení stupně paralelního zpracování na úrovni souborů. Rozdělování v rámci jednoho souboru by se stalo pod automaticky a transparentně a je určený používat velikost bloku vhodné doporučené pro zadaná zdrojová datový typ úložiště pro načtení dat do paralelní a ortogonální k parallelCopies. Skutečný počet kopií paralelní služba pro přesun dat používá pro operace kopírování v době běhu je více než počet souborů, které máte. Pokud je kopie chování **mergeFile**, aktivity kopírování nemohou využívat paralelismus úrovni souborů.
* Pokud zadáte hodnotu **parallelCopies** vlastnost, je-li aktivitě kopírování je oprávněný tímto například pro hybridní kopírování, zvažte zvýšení zatížení na zdroj a jímka datová úložiště a do Self-Hosted integrace Runtime. K tomu dojde, zejména pokud máte více souběžných spustí stejný aktivity, které spouštění stejné úložiště dat nebo aktivity. Pokud si všimnete, že úložiště dat nebo Self-hosted integrace Runtime je zahlcen zatížení, snížit **parallelCopies** hodnota, která má-li snížit zatížení.
* Při kopírování dat z úložiště, které nejsou na základě souborů do úložiště, které jsou na základě souborů, služba pro přesun dat ignoruje **parallelCopies** vlastnost. I když je zadán paralelismus, není použita v tomto případě.
* **parallelCopies** je ortogonální k **cloudDataMovementUnits**. První se počítá mezi všechny jednotky přesun cloudu data.

## <a name="staged-copy"></a>Kopírování dvoufázové instalace

Při kopírování dat ze zdrojového úložiště dat do úložiště dat jímka, je možné používat úložiště objektů Blob jako dočasné pracovní úložiště. Pracovní je zvlášť užitečné v následujících případech:

- **Chcete pro načítání dat z různých úložišť dat do SQL Data Warehouse pomocí PolyBase**. SQL Data Warehouse PolyBase používá jako mechanismus vysokou propustností načíst velké množství dat do SQL Data Warehouse. Ale zdrojová data musí být v Blob storage nebo Azure Data Lake Store a musí splňovat další kritéria. Při načítání dat z úložiště dat než úložiště objektů Blob nebo Azure Data Lake Store, můžete aktivovat data kopírování prostřednictvím dočasné pracovní úložiště objektů Blob. V takovém případě objekt pro vytváření dat provádí transformace požadovaná data, chcete-li zajistit, aby splňoval požadavky PolyBase. Potom pomocí PolyBase efektivně načtení dat do SQL Data Warehouse. Další informace najdete v tématu [PolyBase používá k načtení dat do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Někdy trvá, než se přesouvat data hybridní (to znamená, zkopírujte z místního úložiště dat do úložiště dat cloudu) přes pomalé síťové připojení**. Pokud chcete zvýšit výkon, můžete ke kompresi dat na místě, aby trvá méně času k přesuňte data do pracovní úložiště dat v cloudu a dekomprimaci dat v úložišti pracovní před načtením do cílového úložiště dat kopie dvoufázové instalace.
- **Nechcete-li otevřít jiné porty než port 80 a portu 443 v bráně firewall kvůli podnikové zásady IT**. Například při kopírování dat z úložiště dat místní jímky Azure SQL Database nebo jímky Azure SQL Data Warehouse, musíte aktivovat odchozí komunikaci TCP na portu 1433 pro bránu Windows firewall a váš podnikový firewall. V tomto scénáři můžete dvoufázové instalace kopie využít výhod integrace modulu Runtime Self-hosted nejprve zkopírovat data do úložiště objektů Blob pracovní instance protokol HTTP nebo HTTPS na portu 443 a pak načíst data do SQL Database nebo SQL Data Warehouse z pracovní databáze úložiště objektů Blob. V tomto toku nemusíte povolit port 1433.

### <a name="how-staged-copy-works"></a>Kopírování funguje jak dvoufázové instalace

Když aktivujete pracovní funkce, nejdřív data budou zkopírována z zdrojového úložiště dat do pracovní úložiště objektů Blob (přineste si vlastní). V dalším kroku data zkopírována z pracovní úložiště dat do úložiště dat jímky. Objekt pro vytváření dat automaticky spravuje toku dvoufázová za vás. Po dokončení přesunu dat, data Factory dočasná data z pracovní úložiště také vyčistí.

![Kopírování dvoufázové instalace](media/copy-activity-performance/staged-copy.png)

Když aktivujete přesun dat s použitím pracovní úložiště, můžete zadat, zda chcete data, která mají být před přesunutím dat ze zdrojového úložiště dat k úložišti dat dočasné nebo pracovní zkomprimovat a pak dekomprimovat před přesouvání dat od jako dočasné nebo přípravu úložiště dat pro úložiště dat podřízený.

V současné době nelze kopírovat data mezi dvěma místní úložišti dat pomocí pracovní úložiště.

### <a name="configuration"></a>Konfigurace

Konfigurace **enableStaging** nastavení k určení, zda chcete data, která mají být dvoufázové instalace v úložišti objektů Blob před načtením do cílového úložiště dat v aktivitě kopírování. Když nastavíte **enableStaging** k `TRUE`, zadejte další vlastnosti, které jsou uvedené v další tabulce. Pokud nemáte, je také potřeba vytvořit Azure Storage nebo úložiště sdíleného přístupu podpis propojené služby pro přípravu.

| Vlastnost | Popis | Výchozí hodnota | Požadováno |
| --- | --- | --- | --- |
| **enableStaging** |Zadejte, zda chcete zkopírovat data prostřednictvím jako dočasné pracovní úložiště. |False |Ne |
| **linkedServiceName** |Zadejte název [azurestorage](connector-azure-blob-storage.md#linked-service-properties) propojené služby, která odkazuje na instanci úložiště, který používáte jako dočasné pracovní úložiště. <br/><br/> Úložiště nelze použít s sdílený přístupový podpis načíst data do SQL Data Warehouse pomocí PolyBase. Můžete ji v jiných scénářích. |Nevztahuje se. |Ano, když **enableStaging** nastaven na hodnotu TRUE |
| **path** |Zadejte cestu úložiště objektů Blob, která má obsahovat pracovních dat. Pokud nezadáte cestu, služba vytvoří kontejner pro uložení dočasná data. <br/><br/> Zadejte cestu, pouze v případě, že používáte úložiště s sdílený přístupový podpis, nebo vyžadujete dočasná data v konkrétní umístění. |Nevztahuje se. |Ne |
| **enableCompression** |Určuje, zda data by měl být komprimují předtím, než je zkopírován do cílové. Toto nastavení snižuje objem přenášených dat. |False |Ne |

Zde je ukázka definice aktivitou kopírování pomocí vlastnosti, které jsou popsané v předchozí tabulce:

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

### <a name="staged-copy-billing-impact"></a>Kopírování fakturace dopad dvoufázové instalace

Budou se vám účtovat na základě dva kroky: kopírování doba trvání a zkopírujte typu.

* Při použití pracovní během kopírování cloudu (kopírování dat z jiného úložiště dat cloudu do jiného úložiště dat cloudu, oba fázích pověřený Runtime integrace Azure), vám budou účtovat [Součet kopie doba kroky 1 a 2] x [cloudové kopírování jednotkové ceny].
* Při použití pracovní během hybridní kopírování (kopírování dat z místního úložiště dat do cloudového úložiště dat, jeden fáze pověřený Self-hosted integrace Runtime), budeme vám účtovat [kopírování hybridní doba trvání] x [cena za jednotku hybridního kopie] + [cloudu kopie trvání] x [cloudu kopírování jednotkové ceny].

## <a name="performance-tuning-steps"></a>Postup ladění výkonu

Doporučujeme, aby je provést tyto kroky pro optimalizaci výkonu služby Data Factory s aktivitou kopírování:

1. **Stanovení základní úrovně**. Během fáze vývoje Otestujte svůj kanál pomocí aktivity kopírování proti data reprezentativní ukázka. Shromažďovat podrobnosti o provádění a výkonové charakteristiky následující [kopírovat, pokud chcete monitorování aktivit](copy-activity-overview.md#monitoring).

2. **Diagnostika a optimalizace výkonu**. Pokud zjistíte výkonu nevyhovuje vašim požadavkům, musíte určit kritické body. Potom optimalizace výkonu můžete odebrat nebo snížil dopad kritická místa. Úplný popis diagnostiku výkonu je nad rámec tohoto článku, ale tady jsou některé společné aspekty:

   * Funkce výkonu:
     * [Paralelní kopie](#parallel-copy)
     * [Jednotky přesun dat cloudu](#cloud-data-movement-units)
     * [Kopírování dvoufázové instalace](#staged-copy)
     * [Škálovatelnost vlastním hostováním integrace modulu Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Integrace s vlastním hostováním Runtime](#considerations-for-self-hosted-integration-runtime)
   * [Zdroj](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Serializace a deserializace](#considerations-for-serialization-and-deserialization)
   * [Komprese](#considerations-for-compression)
   * [Mapování sloupce](#considerations-for-column-mapping)
   * [Další aspekty](#other-considerations)

3. **Rozbalte položku konfigurace pro celou sadu dat**. Až budete spokojeni s výsledky spuštění a výkon, můžete rozbalit definice a kanál tak, aby pokrývalo celé datové sady.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Důležité informace týkající se integrace s vlastním hostováním Runtime

Pokud vaše aktivita kopírování proveden v modulu Runtime Self-hosted integrace, pamatujte na následující:

**Instalační program**: doporučujeme použít vyhrazený počítač na hostitele integrace modulu Runtime. V tématu [důležité informace týkající se použití Self-hosted integrace Runtime](concepts-integration-runtime.md).

**Horizontální navýšení kapacity**: jedné logické integrace Runtime Self-hosted s jeden nebo více uzlů může sloužit více aktivity kopírování spustí ve stejnou dobu současně. Pokud máte třeba velkou na přesun dat hybridní buď velký počet souběžných kopie aktivity spustí nebo s velkým množstvím data ke zkopírování, zvažte pro [škálování Self-hosted integrace Runtime](create-self-hosted-integration-runtime.md#high-availability-and-scalability) tak, aby zřídit další prostředek umožnit kopírování.

## <a name="considerations-for-the-source"></a>Důležité informace pro zdroj

### <a name="general"></a>Obecné

Ujistěte se, že příslušné úložiště dat není zahlcen jiné úlohy, které jsou spuštěné na nebo před ním.

Pro úložiště dat společnosti Microsoft, najdete v části [sledování a ladění témata](#performance-reference) , které jsou specifické pro úložiště dat a pomáhá pochopit data ukládání výkonové charakteristiky, minimalizovat dobu odezvy a maximalizovat propustnost.

* Při kopírování dat **z úložiště objektů Blob do SQL Data Warehouse**, zvažte použití **PolyBase** pro zvýšení výkonu. V tématu [PolyBase používá k načtení dat do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) podrobnosti.
* Při kopírování dat **z HDFS do Azure Blob nebo Azure Data Lake Store**, zvažte použití **DistCp** pro zvýšení výkonu. V tématu [použití DistCp zkopírovat data z HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) podrobnosti.
* Při kopírování dat **z Redshift do Azure SQL Data Warehouse nebo Azure BLob nebo Azure Data Lake Store**, zvažte použití **uvolnění** pro zvýšení výkonu. V tématu [uvolnění použít ke zkopírování dat z Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) podrobnosti.

### <a name="file-based-data-stores"></a>Úložiště dat na základě souborů

* **Průměrná velikost souboru a počet souborů**: aktivita kopírování přenosy dat jeden soubor současně. S stejné množství dat, se přesune je nižší, pokud dat obsahuje mnoho malých souborů než několik velkých souborů z důvodu fázi zavedení pro každý soubor celkovou propustnost. Pokud je to možné, kombinovat proto malé soubory do větší souborů k získání vyšší propustnost.
* **Soubor formátu a komprese**: Další způsoby, jak zvýšit výkon, najdete v článku [důležité informace týkající se serializace a deserializace](#considerations-for-serialization-and-deserialization) a [důležité informace týkající se komprese](#considerations-for-compression) oddíly.

### <a name="relational-data-stores"></a>Relační datové úložiště

* **Vzorek dat**: schéma tabulky ovlivňuje kopie propustnost. Velikost velké řádku získáte lepší výkon než velikost řádku malé, zkopírujte stejný objem dat. Důvodem je, že databázi můžete načíst efektivněji méně dávek dat, která obsahují menší počet řádků.
* **Dotazu nebo uložené proceduře**: optimalizace logiku dotazu nebo uložené procedury, zadejte ve zdroji aktivity kopírování efektivněji načíst data.

## <a name="considerations-for-the-sink"></a>Důležité informace týkající se jímka

### <a name="general"></a>Obecné

Ujistěte se, že příslušné úložiště dat není zahlcen jiné úlohy, které jsou spuštěné na nebo před ním.

Úložiště dat společnosti Microsoft, najdete v části [sledování a ladění témata](#performance-reference) , které jsou specifické pro datová úložiště. Tato témata vám může pomoct pochopit vlastnosti výkonu úložiště dat a jak minimalizovat dobu odezvy a maximalizovat propustnost.

* Při kopírování dat **z úložiště objektů Blob do SQL Data Warehouse**, zvažte použití **PolyBase** pro zvýšení výkonu. V tématu [PolyBase používá k načtení dat do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) podrobnosti.
* Při kopírování dat **z HDFS do Azure Blob nebo Azure Data Lake Store**, zvažte použití **DistCp** pro zvýšení výkonu. V tématu [použití DistCp zkopírovat data z HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) podrobnosti.
* Při kopírování dat **z Redshift do Azure SQL Data Warehouse nebo Azure BLob nebo Azure Data Lake Store**, zvažte použití **uvolnění** pro zvýšení výkonu. V tématu [uvolnění použít ke zkopírování dat z Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) podrobnosti.

### <a name="file-based-data-stores"></a>Úložiště dat na základě souborů

* **Zkopírujte chování**: Při kopírování dat z jiné na základě souborů datové úložiště, aktivity kopírování má tři možnosti prostřednictvím **copyBehavior** vlastnost. Zachovává hierarchie, vyrovná hierarchie nebo sloučí soubory. Buď se zachováním nebo vyrovnání hierarchie má žádné nebo téměř žádné nároky na výkon, ale sloučení souborů způsobí, že chcete zvýšit nároky na výkon.
* **Soubor formátu a komprese**: najdete v článku [důležité informace týkající se serializace a deserializace](#considerations-for-serialization-and-deserialization) a [důležité informace týkající se komprese](#considerations-for-compression) oddíly pro další způsoby, jak zlepšit výkon.

### <a name="relational-data-stores"></a>Relační datové úložiště

* **Zkopírujte chování**: v závislosti na vlastnosti, které jste nastavili pro **sqlSink**, aktivity kopírování zapisuje data do cílové databáze různými způsoby.
  * Ve výchozím nastavení připojí data přesun služba používá rozhraní hromadné kopírování API vložit data v režimu, který poskytuje nejlepší výkon.
  * Pokud nakonfigurujete uložené procedury v jímce, platí databázi jednoho řádku dat najednou místo jako hromadného načtení. Výkon výrazně zahodí. Pokud sadu dat velká, v případě potřeby, zvažte možnost použití **preCopyScript** vlastnost.
  * Pokud nakonfigurujete **preCopyScript** spouštět vlastnosti pro každou aktivitu kopírování, služba spustí skript a potom pomocí rozhraní API hromadné kopírování vložte data. Pokud chcete přepsat celou tabulku s nejnovější data, například můžete zadat skript, který nejprve odstranit všechny záznamy před hromadného načtení nová data ze zdroje.
* **Velikost dat vzor a batch**:
  * Schéma tabulky ovlivňuje kopie propustnost. Ke zkopírování stejné množství dat, velikost velké řádku umožňuje lepší výkon než velikost malých řádku, protože databáze můžete potvrdit efektivněji méně dávky data.
  * Aktivita kopírování vkládá data v řadě dávek. Můžete nastavit počet řádků v dávce pomocí **writeBatchSize** vlastnost. Pokud data obsahují malé řádky, můžete nastavit **writeBatchSize** vlastnost s vyšší hodnotou, abyste mohli využívat výhod nižší režijní náklady na dávky a vyšší propustnost. Pokud velikost řádku data velká, dávejte pozor, když zvýšíte **writeBatchSize**. Vysoká hodnota může vést k selhání kopírování způsobené přetížení databáze.

### <a name="nosql-stores"></a>NoSQL úložiště

* Pro **tabulky úložiště**:
  * **Oddíl**: zápis dat do oddílů prokládaná výrazně snižuje výkon. Zdrojová data seřadit klíč oddílu tak, aby, vloží se data efektivně do oddílu jeden po druhém, nebo upravte logiku zapsat data do jediného oddílu.

## <a name="considerations-for-serialization-and-deserialization"></a>Důležité informace týkající se serializace a deserializace

Serializace a deserializace může dojít, když vaše vstupní datové sady nebo výstupní datové sady není soubor. V tématu [podporované formáty souborů a komprese](supported-file-formats-and-compression-codecs.md) s informace o podporovaných formátech souborů pomocí aktivity kopírování.

**Zkopírujte chování**:

* Kopírování souborů mezi úložišti dat na základě souborů:
  * Pokud vstupní a výstupní datové sady oba mají stejné nebo žádné nastavení formátu souboru, služba pro přesun dat provádí **binární kopie** bez jakýchkoli serializaci nebo deserializaci. Zobrazí vyšší propustnost ve srovnání s scénář, ve které se liší od sebe navzájem zdroj a jímka nastavení formátu souboru.
  * Pokud vstupní a výstupní datové sady obě jsou ve formátu textu a pouze kódování typu je jiné, služba pro přesun dat pouze nepodporuje kódování převod. Neprovádí žádné serializace a deserializace, což způsobí, že některé výkonu režie ve srovnání s binární kopie.
  * Pokud vstupní a výstupní datové sady oba mají jiné formáty souborů nebo různé konfigurace, jako oddělovače, služba pro přesun dat deserializuje zdroje dat do datového proudu, transformace a pak ho serializaci do formátu výstup, kterou jste uvedli. Tato operace výsledkem mnohem větších výkonu režie ve srovnání s dalšími scénáři.
* Při kopírování souborů z úložiště dat, který není na základě souborů (například z úložiště založené na souborech do relační úložiště), krok serializaci nebo deserializaci je povinný. Tento krok vede režijní náklady na významně zvýšit výkon.

**Formát souboru**: formát souboru zvolíte může ovlivnit výkon kopírování. Například je Avro kompaktní binární formát, který ukládá metadata s daty. Rozsáhlá podpora má v ekosystému Hadoop pro zpracování a dotazování. Je však nákladnější k serializaci a deserializaci, což vede k nižší propustnost kopie ve srovnání s textový formát Avro. Zkontrolujte výběr formátu souboru v rámci toku zpracování komplexní. Začněte s co tvoří dat je uložená v úložištích zdroj dat nebo extrahovat z externích systémů; nejlepší formát pro úložiště, analytického zpracování a dotazování; a jaký formát data mají být exportovány do datových tržišť nástrojů pro vytváření sestav a vizualizace nástroje. Někdy formát souboru, který je zhoršené pro čtení a zápisu výkon může být dobrou volbou, pokud byste zvážit celkové analytical proces.

## <a name="considerations-for-compression"></a>Důležité informace pro kompresi

Pokud vstupní nebo výstupní datové sady je soubor, můžete nastavit aktivitu kopírování ke kompresi nebo dekompresi jako zapíše data do cílového umístění. Když zvolíte komprese, můžete udělat kompromis mezi vstupně výstupní (I/O) a procesoru. Komprese náklady na data, která je navíc v výpočetní prostředky. Ale na oplátku omezuje sítě vstupně-výstupních operací a úložiště. V závislosti na vašich dat se může zobrazit nárůst v celkovou propustnost kopírování.

**Kodeků**: každý komprese kodek obsahuje výhody. Například bzip2 má nejnižší kopie propustnost, ale získáte nejlepší výkon dotazů Hive s bzip2, protože je možné rozdělit pro zpracování. GZIP je nejvíce vyrovnáváním možnost, a tím nejčastěji používají. Zvolte kodeků, který nejlépe vyhovuje danému typu klient server.

**Úroveň**: můžete vybrat z dvě možnosti pro každý komprese kodeků: nejrychlejších komprimované a optimálně komprimované. Nejrychlejších možnost komprimovaný komprimaci dat, co nejrychleji i v případě, že bude výsledný soubor není komprimována optimálně. Možnost optimálně komprimované stráví více času na komprese a výsledkem minimální množství dat. Obě možnosti zobrazíte, který poskytuje lepší celkový výkon ve vašem případě můžete otestovat.

**Zabývat**: Chcete-li kopírovat velké množství dat mezi místnímu úložišti a cloudu, zvažte použití [připravený kopie](#staged-copy) s komprese zapnuta. Použití dočasné úložiště je užitečné, když šířku pásma sítě a služeb Azure je omezující faktor, který chcete sadu dat vstupní i výstupní datové sady v nekomprimované formě.

## <a name="considerations-for-column-mapping"></a>Důležité informace týkající se mapování sloupců

Můžete nastavit **columnMappings** vlastnosti v aktivitě kopírování mapy všechny nebo jen některé vstupní sloupce pro výstupní sloupce. Poté, co služba pro přesun dat načte data ze zdroje, musí se provést mapování sloupců na data před zapíše data do jímky. Další zpracování snižuje kopie propustnost.

Pokud zdrojového úložiště dat dotazovatelné, například pokud je relační úložiště, jako je SQL Database nebo SQL Server, nebo pokud je úložiště typu NoSQL jako Table storage nebo Azure Cosmos DB, zvažte vkládání sloupec filtrování a změna logiku **dotazu** vlastnost místo použití mapování sloupců. Tímto způsobem projekce provádí služba pro přesun dat čte data ze zdrojového úložiště dat, kde je mnohem efektivnější.

Další informace z [aktivity kopírování schéma mapování](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Další důležité informace

Pokud je velká velikost dat, kterou chcete zkopírovat, můžete upravit obchodní logiky další oddíl data a naplánovat aktivitu kopírování ke spuštění častěji ke snížení velikosti dat pro každou aktivitu kopírování spustit.

Buďte opatrní počet datových sad a kopie aktivity nutnosti objekt pro vytváření dat pro připojení k úložišti dat stejné ve stejnou dobu. Mnoho souběžných kopírování úlohy může omezit úložiště dat a vést ke snížení výkonu, kopie úlohy interní opakovaných pokusů a v některých případech selhání spuštění.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Vzorový scénář: kopírování z místního SQL serveru do úložiště objektů Blob

**Scénář**: kanál vychází ke zkopírování dat z místního SQL serveru do úložiště objektů Blob ve formátu CSV. Chcete-li úlohu kopírování rychleji, by do formátu bzip2 komprimovat soubory CSV.

**Test a analýzy**: propustnost aktivity kopírování je menší než 2 MB/s, což je mnohem nižší než srovnávacího testu výkonu.

**Analýza výkonu a ladění**: Chcete-li vyřešit problémy s výkonem, podíváme se na způsobu zpracování a přesunout data.

1. **Čtení dat**: integrace runtime otevře připojení k systému SQL Server a odešle dotaz. SQL Server odpoví odesláním datový proud do integrace runtime prostřednictvím intranetu.
2. **Serializuje a komprese dat**: integrace runtime serializuje datový proud do formátu CSV a komprimaci dat do datového proudu bzip2.
3. **Zápis dat**: integrace runtime odešle datový proud bzip2 do úložiště objektů Blob přes Internet.

Jak můžete vidět, data se zpracovat a přesunout způsobem streamování sekvenční: SQL Server > LAN > integrace runtime > WAN > úložiště objektů Blob. **Celkový výkon jsou závislé na minimální propustnost přes kanál**.

![Tok dat](./media/copy-activity-performance/case-study-pic-1.png)

Jeden nebo více následujících faktorů může způsobit kritická místa výkonu:

* **Zdroj**: samotný SQL Server má nízkou propustnost kvůli velkým zatížením.
* **Hostovanou na vlastním integrace Runtime**:
  * **LAN**: integrace runtime nachází daleko od počítače systému SQL Server a má připojení s malou šířkou pásma.
  * **Integrace runtime**: integrace runtime bylo dosaženo jeho omezení zatížení provádět následující operace:
    * **Serializace**: serializaci do formátu CSV datový proud má pomalé propustnost.
    * **Komprese**: jste zvolili pomalé kompresní kodek (například bzip2, což je 2,8 MB/s s i7 jádra).
  * **WAN**: šířku pásma mezi podnikovou sítí a služeb Azure je nízká (například T1 = 1,544 kbps; T2 = 6,312 kb/s).
* **Jímky**: úložiště objektů Blob má nízkou propustnost. (Tento scénář nepravděpodobné, protože jeho SLA zaručuje minimálně 60 MB/s.)

V takovém případě může komprese dat bzip2 zpomalení celého kanálu. Přepnutí na kodek pro kompresi gzip může usnadňují tyto potíže.

## <a name="reference"></a>Referenční informace

Tady je výkonu sledování a ladění odkazy pro některé podporované datové úložiště:

* Úložiště Azure (včetně úložiště objektů Blob a Table storage): [cíle škálovatelnosti Azure Storage](../storage/common/storage-scalability-targets.md) a [kontrolní seznam výkonu a škálovatelnosti Azure Storage](../storage/common/storage-performance-checklist.md)
* Azure SQL Database: Můžete [sledovat výkon](../sql-database/sql-database-single-database-monitor.md) a zkontrolujte procento databáze transakce jednotky (DTU)
* Azure SQL Data Warehouse: Jeho schopnosti se měří v jednotky datového skladu (Dwu); v tématu [spravovat výpočetní výkon v Azure SQL Data Warehouse (přehled)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [úrovně výkonu v Azure Cosmos DB](../cosmos-db/performance-levels.md)
* Místní SQL Server: [monitorování a optimalizace výkonu](https://msdn.microsoft.com/library/ms189081.aspx)
* Místní souborový server: [optimalizace výkonu pro souborové servery](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>Další postup
Najdete v aktivitě kopírování článcích:

- [Kopie aktivity – přehled](copy-activity-overview.md)
- [Kopie aktivity schéma mapování](copy-activity-schema-and-type-mapping.md)
- [Zkopírujte aktivity odolnost proti chybám](copy-activity-fault-tolerance.md)
