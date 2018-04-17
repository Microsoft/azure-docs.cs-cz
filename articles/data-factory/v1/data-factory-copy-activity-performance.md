---
title: Zkopírujte aktivity výkonu a vyladění Průvodce | Microsoft Docs
description: Další informace o klíčových faktorů, které ovlivňují výkon přesun dat v Azure Data Factory při použití aktivity kopírování.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b54138c5197d1c5870eed6fd4782e47c6a8b0300
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Zkopírujte aktivity výkonu a vyladění Průvodce

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-copy-activity-performance.md)
> * [Verze 2 – Preview](../copy-activity-performance.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [zkopírujte vyladění Průvodce pro vytváření dat verze 2 a výkonu aktivity](../copy-activity-performance.md).

Aktivita kopírování Azure Data Factory nabízí prvotřídní dat zabezpečeným, spolehlivým a vysoce výkonné načítání řešení. Ji budete kopie desítkami terabajtů dat pro každý den s bohatou různých cloudové a místní úložiště dat. Výkon při načítání dat svěží fast je klíč k zajištění, můžete se zaměřit na problém "velkých objemů dat" základní: vytváření řešení pro pokročilou analýzu a získávání hlubšímu porozumění z všechno, co data.

Azure poskytuje sadu podnikové úrovni řešení pro úložiště a datového skladu dat a aktivity kopírování nabízí vysoce optimalizovaného data načítání prostředí, které se snadno konfiguraci a nastavení. Jenom jedna kopie aktivity můžete dosáhnout:

* Načítání dat do **Azure SQL Data Warehouse** v **1,2 GB/s**. Návod s případu použití najdete v tématu [načíst 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v části 15 minut](data-factory-load-sql-data-warehouse.md).
* Načítání dat do **úložiště objektů Azure Blob** v **1.0 GB/s**
* Načítání dat do **Azure Data Lake Store** v **1.0 GB/s**

Tento článek popisuje:

* [Výkon referenční čísla](#performance-reference) podporováno zdroj a jímka úložiště dat, které vám pomohou naplánovat projektu;
* Funkce, které může zvýšit propustnost kopírování v různých scénářích, včetně [jednotky přesun dat v cloudu](#cloud-data-movement-units), [paralelní kopie](#parallel-copy), a [připravený kopie](#staged-copy);
* [Ladění pokyny výkonu](#performance-tuning-steps) o tom, jak optimalizovat výkon a klíčové faktory, které může ovlivnit výkon kopírování.

> [!NOTE]
> Pokud nejste obeznámeni s aktivitou kopírování obecně, přečtěte si téma [přesun dat pomocí aktivity kopírování](data-factory-data-movement-activities.md) před přečtení tohoto článku.
>

## <a name="performance-reference"></a>Referenční dokumentace výkonu

Jako odkaz níže uvedená tabulka zobrazuje číslo kopie propustnost v MB/s pro daný zdroj a jímka dvojice založené na interní testování. Pro porovnání, také ukazuje, jak budou různí nastavení [jednotky přesun dat v cloudu](#cloud-data-movement-units) nebo [Brána pro správu dat škálovatelnost](data-factory-data-management-gateway-high-availability-scalability.md) (více uzlů brány) může pomoct na výkon kopírování.

![Matice výkonu](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>V Azure Data Factory verze 1 je minimální cloudu jednotky přesun dat pro cloud cloudové kopírování dva. Pokud není zadaný, najdete v části jednotky přesun dat výchozí použitá v [jednotky přesun dat v cloudu](#cloud-data-movement-units).

**Všimněte si body:**
* Propustnost je vypočítána pomocí následujícího vzorce: [velikost dat číst ze zdroje] / [spustit doba trvání aktivity kopírování].
* Referenční čísla výkonu v tabulce se měří pomocí [TPC-H](http://www.tpc.org/tpch/) datové sady v jedna kopie aktivity při spuštění.
* V úložištích dat Azure zdroj a jímka mají ve stejné oblasti Azure.
* Pro hybridní kopírování mezi místními a cloudovými úložiště dat, každý uzel brány byla spuštěna na počítači, který byl samostatné z místního úložiště dat s níže specifikace. Při spuštění jediné aktivity v brány, operace kopírování spotřebováno pouze malou část CPU, paměť či šířku pásma sítě testovací počítač. Další informace z [aspekt Brána pro správu dat](#considerations-for-data-management-gateway).
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
> Vyšší propustnost můžete dosáhnout využitím jednotky další přesun dat (DMUs) než výchozí maximální DMUs, které je 32 pro spuštění aktivity kopírování cloudu cloud. Například s 100 DMUs, můžete dosáhnout kopírování dat z objektu Blob Azure do Azure Data Lake Store v **1.0GBps**. Najdete v článku [jednotky přesun dat v cloudu](#cloud-data-movement-units) část Podrobnosti o této funkci a podporovaném scénáři. Obraťte se na [podporu Azure](https://azure.microsoft.com/support/) požádat o další DMUs.

## <a name="parallel-copy"></a>Paralelní kopie
Můžete číst data ze zdroje nebo zapsat data do cílové **paralelně v rámci aktivity kopírování spustit**. Tato funkce vylepšuje propustnost operace kopírování a snižuje dobu potřebnou k přesunutí dat.

Toto nastavení se liší od **souběžnosti** vlastnost v definici aktivity. **Souběžnosti** vlastnost určuje počet **spouští souběžné aktivity kopírování** zpracováním dat ze systému windows jiné aktivitě (1: 00 do 2: 00, 2 AM 3 AM, AM 3 a 4 AM a tak dále). Tato možnost je užitečná při provádění historických zatížení. Paralelní kopie funkce se vztahuje na **jednotné aktivity při spuštění**.

Podívejme se na vzorový scénář. V následujícím příkladu třeba zpracovat více řezů v minulosti. Objekt pro vytváření dat běží instance aktivity kopírování (aktivita spustit) pro každý řez:

* Datový řez z okna první aktivitu (1: 00 do 2: 00) == > aktivita běžet 1
* Datový řez z okna druhý aktivity (2: 00 do 3: 00) == > aktivita běžet 2
* Datový řez z okna druhý aktivity (3: 00 do 4: 00) == > aktivita běžet 3

A tak dále.

V tomto příkladu při **souběžnosti** hodnota nastavena na 2, **aktivita běžet 1** a **aktivita běžet 2** kopírování dat z okna dvě aktivity **souběžně** ke zlepšení výkonu přesun dat. Ale pokud více souborů jsou spojené s aktivity při spuštění 1, služba pro přesun dat zkopíruje soubory ze zdroje cílový jeden soubor současně.

### <a name="cloud-data-movement-units"></a>Jednotky přesun dat cloudu
A **jednotky přesun dat cloudu (DMU)** je míra, která reprezentuje výkon (kombinaci procesoru, paměti a přidělení prostředků sítě) v objektu pro vytváření dat na jednu jednotku. DMU lze použít pro operace kopírování cloudu do cloudu, ale není v hybridní kopírování.

**Jednotky přesun dat minimální cloudu na základě kterých kopie aktivity při spuštění je dva.** Pokud není zadaný, následující tabulka uvádí výchozí DMUs, používá ve scénářích různé kopie:

| Kopírování | Výchozí DMUs určit službou |
|:--- |:--- |
| Kopírování dat mezi úložišti na základě souborů | Mezi 2 a 16 v závislosti na počtu a velikosti souborů. |
| Všechny ostatní kopie scénáře | 2 |

Pokud chcete přepsat toto výchozí nastavení, zadejte hodnotu **cloudDataMovementUnits** vlastnost následujícím způsobem. **Povolené hodnoty** pro **cloudDataMovementUnits** vlastnost jsou 2, 4, 8, 16, 32. **Skutečný počet cloudu DMUs** že kopírování se používá v době běhu je rovna nebo menší než nakonfigurovaná hodnota, v závislosti na vaší vzorek dat. Informace o úrovni výkonnější se mohou objevit, když konfigurujete další jednotky pro konkrétní kopie zdroj a jímka najdete v tématu [referenční dokumentace výkonu](#performance-reference).

```json
"activities":[  
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
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

> [!NOTE]
> Pokud potřebujete další cloudu DMUs pro vyšší propustnost, obraťte se na [podporu Azure](https://azure.microsoft.com/support/). Nastavení 8 a vyšší aktuálně funguje pouze tehdy, když jste **zkopírovat soubory z objektu Blob úložiště nebo Data Lake Store nebo Amazon S3 nebo cloudem FTP nebo cloudem SFTP do objektu Blob úložiště nebo Data Lake Store nebo Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies
Můžete použít **parallelCopies** vlastnost označující paralelismus, který chcete použít aktivitu kopírování. Tato vlastnost si můžete představit jako maximální počet vláken v rámci aktivitu kopírování, která můžou číst ze zdroje nebo zapisovat do vašeho úložiště dat podřízený paralelně.

Objekt pro vytváření dat pro každou aktivitu kopírování, spuštění, určuje počet paralelních kopií, které chcete použít ke zkopírování dat ze zdroje dat, ukládání a k datům cílového úložiště. Výchozí počet paralelní kopie, které používá závisí na typu zdroj a jímka, který používáte.  

| Zdroj a jímka | Výchozí paralelní kopie počet určit službou |
| --- | --- |
| Kopírovat data mezi souborové úložiště (úložiště objektů Blob; Data Lake Store; Amazon S3; systému souborů na místě. místní službě HDFS) |Mezi 1 a 32. Závisí na velikosti souborů a počet jednotek přesun dat cloudu (DMUs) používat ke kopírování dat mezi dvěma cloudové úložiště dat nebo fyzické konfigurace počítače brány, použít pro hybridní kopírování (pro kopírování dat do nebo z místnímu úložišti dat). |
| Kopírování dat z **úložiště všechny zdroje dat do úložiště Azure Table** |4 |
| Všechny ostatní zdroj a jímka páry |1 |

Obvykle použije se výchozí chování měl dát nejlepší propustnost. Však k řízení zatížení na počítačích, které hostují vaše data ukládá, nebo k ladění výkonu kopie, můžete se rozhodnout přepsat výchozí hodnotu a zadejte hodnotu **parallelCopies** vlastnost. Hodnota musí být mezi 1 a 32 (obě včetně). Za běhu pro nejlepší výkon, používá aktivitu kopírování hodnotu, která je menší než nebo rovna hodnotě, který nastavíte.

```json
"activities":[  
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
Všimněte si body:

* Při kopírování dat mezi úložišti na základě souborů **parallelCopies** určení stupně paralelního zpracování na úrovni souborů. Rozdělování v rámci jednoho souboru by se stalo pod automaticky a transparentně a je určený používat velikost bloku vhodné doporučené pro zadaná zdrojová datový typ úložiště pro načtení dat do paralelní a ortogonální k parallelCopies. Skutečný počet kopií paralelní služba pro přesun dat používá pro operace kopírování v době běhu je více než počet souborů, které máte. Pokud je kopie chování **mergeFile**, aktivity kopírování nemohou využívat paralelismus úrovni souborů.
* Pokud zadáte hodnotu **parallelCopies** vlastnost, zvažte zvýšení zatížení na zdroj a jímka datová úložiště a pro bránu, pokud je hybridní kopírování. K tomu dojde, zejména pokud máte více souběžných spustí stejný aktivity, které spouštění stejné úložiště dat nebo aktivity. Pokud si všimnete, že úložiště dat nebo brány je zahlcen zatížení, snížit **parallelCopies** hodnota, která má-li snížit zatížení.
* Při kopírování dat z úložiště, které nejsou na základě souborů do úložiště, které jsou na základě souborů, služba pro přesun dat ignoruje **parallelCopies** vlastnost. I když je zadán paralelismus, není použita v tomto případě.

> [!NOTE]
> Můžete použít používá Brána pro správu dat. verze 1.11 nebo novější **parallelCopies** funkce při hybridní kopírování.
>
>

Pro lepší využití tyto dvě vlastnosti a k vylepšení vašeho propustnost přesun dat, najdete v článku [ukázkové případy použití](#case-study-use-parallel-copy). Nemusíte konfigurovat **parallelCopies** využít výchozí chování. Pokud nakonfigurujete a **parallelCopies** je příliš malá, více cloudu DMUs nemusí budou plně využívat.  

### <a name="billing-impact"></a>Dopad fakturace
Má **důležité** pamatovat, že budou účtovat na základě celkové doby operace kopírování. Pokud úlohu kopírování se používá k trvat hodinu s jednotkou k jednomu cloudu a teď bude trvat 15 minut s čtyři jednotky cloudu, zůstane celkové faktury téměř stejný. Například můžete použít čtyři jednotky cloudu. První jednotky cloudu stráví 10 minut, druhý, 10 minut, třetí jeden, 5 minut a čtvrtý jeden, 5 minut, všechny v jedné kopie aktivity spustit. Budou se vám účtovat dobu celkový kopírování (přesun dat), což je 10 + 10 + 5 + 5 = 30 minut. Pomocí **parallelCopies** nemá vliv na fakturace.

## <a name="staged-copy"></a>Kopírování dvoufázové instalace
Při kopírování dat ze zdrojového úložiště dat do úložiště dat jímka, je možné používat úložiště objektů Blob jako dočasné pracovní úložiště. Pracovní je zvlášť užitečné v následujících případech:

1. **Chcete pro načítání dat z různých úložišť dat do SQL Data Warehouse pomocí PolyBase**. SQL Data Warehouse PolyBase používá jako mechanismus vysokou propustností načíst velké množství dat do SQL Data Warehouse. Ale zdrojová data musí být v úložišti objektů Blob a musí splňovat další kritéria. Při načítání dat z úložiště dat než úložiště objektů Blob, můžete aktivovat data kopírování prostřednictvím dočasné pracovní úložiště objektů Blob. V takovém případě objekt pro vytváření dat provádí transformace požadovaná data, chcete-li zajistit, aby splňoval požadavky PolyBase. Pak PolyBase používá k načtení dat do SQL Data Warehouse. Další podrobnosti najdete v tématu [PolyBase používá k načtení dat do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). Návod s případu použití najdete v tématu [načíst 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v části 15 minut](data-factory-load-sql-data-warehouse.md).
2. **Někdy trvá, než se přesouvat data hybridní (tedy kopírovat mezi místní data úložiště a Cloudová data uložit) přes pomalé síťové připojení**. Chcete-li zvýšit výkon, dokáže komprimovat data na místní tak, aby trvá méně času pro přesun dat do pracovní úložiště dat v cloudu. Před načtením do cílového úložiště dat, potom můžete dekomprimaci dat v úložišti pracovní.
3. **Nechcete-li otevřít jiné porty než port 80 a portu 443 v bráně firewall kvůli podnikové zásady IT**. Například při kopírování dat z úložiště dat místní jímky Azure SQL Database nebo jímky Azure SQL Data Warehouse, musíte aktivovat odchozí komunikaci TCP na portu 1433 pro bránu Windows firewall a váš podnikový firewall. V tomto scénáři využít výhod bránu a první kopírovat data do instance pracovní úložiště objektů Blob přes protokol HTTP nebo HTTPS na portu 443. Potom načtete data do SQL Database nebo SQL Data Warehouse z pracovní databáze úložiště objektů Blob. V tomto toku nemusíte povolit port 1433.

### <a name="how-staged-copy-works"></a>Kopírování funguje jak dvoufázové instalace
Při aktivaci pracovní funkce, nejprve dat je zkopírován ze zdrojového úložiště dat do pracovní úložiště dat (funkce přineste si vlastní). V dalším kroku data zkopírována z pracovní úložiště dat do úložiště dat jímky. Objekt pro vytváření dat automaticky spravuje toku dvoufázová za vás. Po dokončení přesunu dat, data Factory dočasná data z pracovní úložiště také vyčistí.

Ve scénáři kopie cloudu (zdroj a jímka data, která jsou úložiště v cloudu) se nepoužije brány. Služba Data Factory provádí operace kopírování.

![Dvoufázové instalace kopírování: scénář cloudu](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

V hybridním scénáři kopírování (je místní zdroj a jímka je v cloudu), brány přesune data ze zdrojového úložiště dat do úložiště dat, které pracovní. Služba data Factory přesouvá data z pracovní úložiště dat do úložiště dat jímky. Kopírování dat z jiného úložiště dat cloudu k úložišti dat místní prostřednictvím přípravy je podporováno také s odstínech toku.

![Dvoufázové instalace kopírování: hybridní scénář](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Když aktivujete přesun dat s použitím pracovní úložiště, můžete zadat, zda chcete data, která mají být před přesunutím dat ze zdrojového úložiště dat k úložišti dat dočasné nebo pracovní zkomprimovat a pak dekomprimovat před přesouvání dat od jako dočasné nebo přípravu úložiště dat pro úložiště dat podřízený.

V současné době nelze kopírovat data mezi dvěma místní úložišti dat pomocí pracovní úložiště. Očekáváme, že tuto možnost, chcete-li být brzy dostupná.

### <a name="configuration"></a>Konfigurace
Konfigurace **enableStaging** nastavení k určení, zda chcete data, která mají být dvoufázové instalace v úložišti objektů Blob před načtením do cílového úložiště dat v aktivitě kopírování. Když nastavíte **enableStaging** na hodnotu TRUE, zadejte další vlastnosti, které jsou uvedené v další tabulce. Pokud nemáte, je také potřeba vytvořit Azure Storage nebo úložiště sdíleného přístupu podpis propojené služby pro přípravu.

| Vlastnost | Popis | Výchozí hodnota | Požaduje se |
| --- | --- | --- | --- |
| **enableStaging** |Zadejte, zda chcete zkopírovat data prostřednictvím jako dočasné pracovní úložiště. |False |Ne |
| **linkedServiceName** |Zadejte název [azurestorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) nebo [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) propojené služby, která odkazuje na instanci úložiště, který používáte jako dočasné pracovní úložiště. <br/><br/> Úložiště nelze použít s sdílený přístupový podpis načíst data do SQL Data Warehouse pomocí PolyBase. Můžete ji v jiných scénářích. |neuvedeno |Ano, když **enableStaging** nastaven na hodnotu TRUE |
| **Cesta** |Zadejte cestu úložiště objektů Blob, která má obsahovat pracovních dat. Pokud nezadáte cestu, služba vytvoří kontejner pro uložení dočasná data. <br/><br/> Zadejte cestu, pouze v případě, že používáte úložiště s sdílený přístupový podpis, nebo vyžadujete dočasná data v konkrétní umístění. |neuvedeno |Ne |
| **enableCompression** |Určuje, zda data by měl být komprimují předtím, než je zkopírován do cílové. Toto nastavení snižuje objem přenášených dat. |False |Ne |

Zde je ukázka definice aktivitou kopírování pomocí vlastnosti, které jsou popsané v předchozí tabulce:

```json
"activities":[  
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>Dopad fakturace
Budou se vám účtovat na základě dva kroky: kopírování doba trvání a zkopírujte typu.

* Při použití pracovní během kopírování cloudu (kopírování dat z jiného úložiště dat cloudu do jiného úložiště dat v cloudu), vám budou účtovat [Součet kopie doba kroky 1 a 2] x [cloudové kopírování jednotkové ceny].
* Při použití pracovní během hybridní kopírování (kopírování dat z místního úložiště dat do úložiště dat cloudu), budeme vám účtovat [kopírování hybridní doba trvání] x [cena za jednotku hybridního kopie] + [cloudu kopie trvání] x [cloudové kopírování jednotkové ceny].

## <a name="performance-tuning-steps"></a>Postup ladění výkonu
Doporučujeme, aby je provést tyto kroky pro optimalizaci výkonu služby Data Factory s aktivitou kopírování:

1. **Stanovení základní úrovně**. Během fáze vývoje Otestujte svůj kanál pomocí aktivity kopírování proti data reprezentativní ukázka. Data Factory můžete využít [řezů modelu](data-factory-scheduling-and-execution.md) omezit množství dat, můžete pracovat.

   Shromažďovat dobu provádění a vlastnosti výkonu pomocí **monitorování a správu aplikací**. Zvolte **monitorování a správa** na domovské stránce objektu pro vytváření dat. Ve stromovém zobrazení, vyberte **výstupní datovou sadu**. V **aktivity Windows** vyberte kopie aktivity při spuštění. **Aktivity Windows** uvádí dobu trvání aktivity kopírování a velikost dat, která se zkopírují. Propustnost je uvedena v **aktivity okno Průzkumníka**. Další informace o aplikaci najdete v tématu [monitorování a Správa kanálů služby Azure Data Factory pomocí monitorování a správu aplikací](data-factory-monitor-manage-app.md).

   ![Podrobnosti o spuštění aktivit](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Později v článku, můžete porovnat výkon a konfiguraci váš scénář aktivity kopírování [referenční dokumentace výkonu](#performance-reference) z našich testů.
2. **Diagnostika a optimalizace výkonu**. Pokud zjistíte výkonu nevyhovuje vašim požadavkům, musíte určit kritické body. Potom optimalizace výkonu můžete odebrat nebo snížil dopad kritická místa. Úplný popis diagnostiku výkonu je nad rámec tohoto článku, ale tady jsou některé společné aspekty:

   * Funkce výkonu:
     * [Paralelní kopie](#parallel-copy)
     * [Jednotky přesun dat cloudu](#cloud-data-movement-units)
     * [Kopírování dvoufázové instalace](#staged-copy)
     * [Škálovatelnost Brána pro správu dat](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Brána správy dat](#considerations-for-data-management-gateway)
   * [Zdroj](#considerations-for-the-source)
   * [podřízený](#considerations-for-the-sink)
   * [Serializace a deserializace](#considerations-for-serialization-and-deserialization)
   * [Komprese](#considerations-for-compression)
   * [Mapování sloupce](#considerations-for-column-mapping)
   * [Další aspekty](#other-considerations)
3. **Rozbalte položku konfigurace pro celou sadu dat**. Až budete spokojeni s výsledky spuštění a výkon, můžete rozbalit definice a aktivní období kanálu, nepokrývají celou sadu dat.

## <a name="considerations-for-data-management-gateway"></a>Důležité informace týkající se Brána pro správu dat
**Instalační program brány**: doporučujeme použít vyhrazený počítač na hostitele brány pro správu dat. V tématu [předpoklady pro použití brány pro správu dat](data-factory-data-management-gateway.md#considerations-for-using-gateway).  

**Monitorování brány a nahoru nebo škálování**: jedné logické brány se jeden nebo více uzlů brány může sloužit více aktivity kopírování spustí ve stejnou dobu současně. Skoro v reálném čase snímek využití prostředků (procesor, paměť, network(in/out) atd.) můžete zobrazit na počítači brány i počet souběžných úloh spuštěných versus limit na portálu Azure najdete v části [monitorování brány na portálu](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Pokud máte třeba velkou na přesun dat hybridní buď velký počet souběžných kopie aktivity spustí nebo s velkým množstvím data ke zkopírování, zvažte pro [vertikální navýšení kapacity nebo škálovat. brána](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) tak, aby lépe využívat prostředek nebo pro zřízení více prostředků na základě kterých kopírování. 

## <a name="considerations-for-the-source"></a>Důležité informace pro zdroj
### <a name="general"></a>Obecné
Ujistěte se, že příslušné úložiště dat není zahlcen jiné úlohy, které jsou spuštěné na nebo před ním.

Pro úložiště dat společnosti Microsoft, najdete v části [sledování a ladění témata](#performance-reference) , které jsou specifické pro úložiště dat a pomáhá pochopit data ukládání výkonové charakteristiky, minimalizovat dobu odezvy a maximalizovat propustnost.

Pokud zkopírujete data z Blob storage do SQL Data Warehouse, zvažte použití **PolyBase** pro zvýšení výkonu. V tématu [PolyBase používá k načtení dat do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) podrobnosti. Návod s případu použití najdete v tématu [načíst 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v části 15 minut](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Úložiště dat na základě souborů
*(Včetně úložiště objektů Blob, Data Lake Store, Amazon S3, systémy souborů na místě a místní HDFS)*

* **Průměrná velikost souboru a počet souborů**: aktivita kopírování přenosy dat jeden soubor současně. S stejné množství dat, se přesune je nižší, pokud dat obsahuje mnoho malých souborů než několik velkých souborů z důvodu fázi zavedení pro každý soubor celkovou propustnost. Pokud je to možné, kombinovat proto malé soubory do větší souborů k získání vyšší propustnost.
* **Soubor formátu a komprese**: Další způsoby, jak zvýšit výkon, najdete v článku [důležité informace týkající se serializace a deserializace](#considerations-for-serialization-and-deserialization) a [důležité informace týkající se komprese](#considerations-for-compression) oddíly.
* Pro **místní systém souborů** scénář, ve kterém **Brána pro správu dat** je potřeba, najdete v článku [důležité informace týkající se Brána pro správu dat](#considerations-for-data-management-gateway) části.

### <a name="relational-data-stores"></a>Relační datové úložiště
*(Zahrnuje SQL Database; SQL Data Warehouse; Amazon Redshift; Databáze systému SQL Server; a Oracle, MySQL, DB2, Teradata, Sybase a PostgreSQL databáze atd.)*

* **Vzorek dat**: schéma tabulky ovlivňuje kopie propustnost. Velikost velké řádku získáte lepší výkon než velikost řádku malé, zkopírujte stejný objem dat. Důvodem je, že databázi můžete načíst efektivněji méně dávek dat, která obsahují menší počet řádků.
* **Dotazu nebo uložené proceduře**: optimalizace logiku dotazu nebo uložené procedury, zadejte ve zdroji aktivity kopírování efektivněji načíst data.
* Pro **místní relační databáze**, jako je SQL Server a Oracle, které vyžadují použití **Brána pro správu dat**, najdete v článku [důležité informace týkající se Brána pro správu dat](#considerations-on-data-management-gateway) části.

## <a name="considerations-for-the-sink"></a>Důležité informace týkající se jímka
### <a name="general"></a>Obecné
Ujistěte se, že příslušné úložiště dat není zahlcen jiné úlohy, které jsou spuštěné na nebo před ním.

Úložiště dat společnosti Microsoft, najdete v části [sledování a ladění témata](#performance-reference) , které jsou specifické pro datová úložiště. Tato témata vám může pomoct pochopit vlastnosti výkonu úložiště dat a jak minimalizovat dobu odezvy a maximalizovat propustnost.

Pokud chcete kopírovat data z **úložiště objektů Blob** k **SQL Data Warehouse**, zvažte použití **PolyBase** pro zvýšení výkonu. V tématu [PolyBase používá k načtení dat do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) podrobnosti. Návod s případu použití najdete v tématu [načíst 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v části 15 minut](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Úložiště dat na základě souborů
*(Včetně úložiště objektů Blob, Data Lake Store, Amazon S3, systémy souborů na místě a místní HDFS)*

* **Zkopírujte chování**: Při kopírování dat z jiné na základě souborů datové úložiště, aktivity kopírování má tři možnosti prostřednictvím **copyBehavior** vlastnost. Zachovává hierarchie, vyrovná hierarchie nebo sloučí soubory. Buď se zachováním nebo vyrovnání hierarchie má žádné nebo téměř žádné nároky na výkon, ale sloučení souborů způsobí, že chcete zvýšit nároky na výkon.
* **Soubor formátu a komprese**: najdete v článku [důležité informace týkající se serializace a deserializace](#considerations-for-serialization-and-deserialization) a [důležité informace týkající se komprese](#considerations-for-compression) oddíly pro další způsoby, jak zlepšit výkon.
* **Úložiště objektů blob**: v současné době podporuje úložiště objektů Blob blokovat jenom objekty BLOB pro přenos dat optimalizované a propustnosti.
* Pro **systémy souborů místní** scénáře, které vyžadují použití **Brána pro správu dat**, najdete v článku [důležité informace týkající se Brána pro správu dat](#considerations-for-data-management-gateway) části.

### <a name="relational-data-stores"></a>Relační datové úložiště
*(Zahrnuje SQL Database, SQL datového skladu, databáze systému SQL Server a Oracle – databáze)*

* **Zkopírujte chování**: v závislosti na vlastnosti, které jste nastavili pro **sqlSink**, aktivity kopírování zapisuje data do cílové databáze různými způsoby.
  * Ve výchozím nastavení připojí data přesun služba používá rozhraní hromadné kopírování API vložit data v režimu, který poskytuje nejlepší výkon.
  * Pokud nakonfigurujete uložené procedury v jímce, platí databázi jednoho řádku dat najednou místo jako hromadného načtení. Výkon výrazně zahodí. Pokud sadu dat velká, v případě potřeby, zvažte možnost použití **sqlWriterCleanupScript** vlastnost.
  * Pokud nakonfigurujete **sqlWriterCleanupScript** spouštět vlastnosti pro každou aktivitu kopírování, služba spustí skript a potom pomocí rozhraní API hromadné kopírování vložte data. Pokud chcete přepsat celou tabulku s nejnovější data, například můžete zadat skript, který nejprve odstranit všechny záznamy před hromadného načtení nová data ze zdroje.
* **Velikost dat vzor a batch**:
  * Schéma tabulky ovlivňuje kopie propustnost. Ke zkopírování stejné množství dat, velikost velké řádku umožňuje lepší výkon než velikost malých řádku, protože databáze můžete potvrdit efektivněji méně dávky data.
  * Aktivita kopírování vkládá data v řadě dávek. Můžete nastavit počet řádků v dávce pomocí **writeBatchSize** vlastnost. Pokud data obsahují malé řádky, můžete nastavit **writeBatchSize** vlastnost s vyšší hodnotou, abyste mohli využívat výhod nižší režijní náklady na dávky a vyšší propustnost. Pokud velikost řádku data velká, dávejte pozor, když zvýšíte **writeBatchSize**. Vysoká hodnota může vést k selhání kopírování způsobené přetížení databáze.
* Pro **místní relační databáze** , jako třeba SQL Server a Oracle, které vyžadují použití **Brána pro správu dat**, najdete v článku [důležité informace týkající se Brána pro správu dat](#considerations-for-data-management-gateway) části.

### <a name="nosql-stores"></a>NoSQL úložiště
*(Zahrnuje Table storage a Azure Cosmos DB)*

* Pro **tabulky úložiště**:
  * **Oddíl**: zápis dat do oddílů prokládaná výrazně snižuje výkon. Zdrojová data seřadit klíč oddílu tak, aby, vloží se data efektivně do oddílu jeden po druhém, nebo upravte logiku zapsat data do jediného oddílu.
* Pro **Azure Cosmos DB**:
  * **Velikost dávky**: **writeBatchSize** vlastnost nastaví počet paralelní požadavků ve službě Azure Cosmos DB vytvářet dokumenty. Lepšího výkonu můžete očekávat, když zvýšíte **writeBatchSize** protože další paralelní žádosti se odesílají do Azure Cosmos DB. Podívejte se však omezení při zápisu Cosmos Azure DB (je chybová zpráva "rychlost požadavků je velká"). Různé faktory může způsobit omezení velikosti dokumentu, včetně počtu podmínky v dokumenty a cílovou kolekci zásady indexování. K dosažení vyšší propustnost kopírování, zvažte použití lepší kolekci, například S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Důležité informace týkající se serializace a deserializace
Serializace a deserializace může dojít, když vaše vstupní datové sady nebo výstupní datové sady není soubor. V tématu [podporované formáty souborů a komprese](data-factory-supported-file-and-compression-formats.md) s informace o podporovaných formátech souborů pomocí aktivity kopírování.

**Zkopírujte chování**:

* Kopírování souborů mezi úložišti dat na základě souborů:
  * Pokud vstupní a výstupní datové sady oba mají stejné nebo žádné nastavení formátu souboru, služba pro přesun dat provede binární kopie bez jakýchkoli serializaci nebo deserializaci. Zobrazí vyšší propustnost ve srovnání s scénář, ve které se liší od sebe navzájem zdroj a jímka nastavení formátu souboru.
  * Pokud vstupní a výstupní datové sady obě jsou ve formátu textu a pouze kódování typu je jiné, služba pro přesun dat pouze nepodporuje kódování převod. Neprovádí žádné serializace a deserializace, což způsobí, že některé výkonu režie ve srovnání s binární kopie.
  * Pokud vstupní a výstupní datové sady oba mají jiné formáty souborů nebo různé konfigurace, jako oddělovače, služba pro přesun dat deserializuje zdroje dat do datového proudu, transformace a pak ho serializaci do formátu výstup, kterou jste uvedli. Tato operace výsledkem mnohem větších výkonu režie ve srovnání s dalšími scénáři.
* Při kopírování souborů z úložiště dat, který není na základě souborů (například z úložiště založené na souborech do relační úložiště), krok serializaci nebo deserializaci je povinný. Tento krok vede režijní náklady na významně zvýšit výkon.

**Formát souboru**: formát souboru zvolíte může ovlivnit výkon kopírování. Například je Avro kompaktní binární formát, který ukládá metadata s daty. Rozsáhlá podpora má v ekosystému Hadoop pro zpracování a dotazování. Je však nákladnější k serializaci a deserializaci, což vede k nižší propustnost kopie ve srovnání s textový formát Avro. Zkontrolujte výběr formátu souboru v rámci toku zpracování komplexní. Začněte s co tvoří dat je uložená v úložištích zdroj dat nebo extrahovat z externích systémů; nejlepší formát pro úložiště, analytického zpracování a dotazování; a jaký formát data mají být exportovány do datových tržišť nástrojů pro vytváření sestav a vizualizace nástroje. Někdy formát souboru, který je zhoršené pro čtení a zápisu výkon může být dobrou volbou, pokud byste zvážit celkové analytical proces.

## <a name="considerations-for-compression"></a>Důležité informace pro kompresi
Pokud vstupní nebo výstupní datové sady je soubor, můžete nastavit aktivitu kopírování ke kompresi nebo dekompresi jako zapíše data do cílového umístění. Když zvolíte komprese, můžete udělat kompromis mezi vstupně výstupní (I/O) a procesoru. Komprese náklady na data, která je navíc v výpočetní prostředky. Ale na oplátku omezuje sítě vstupně-výstupních operací a úložiště. V závislosti na vašich dat se může zobrazit nárůst v celkovou propustnost kopírování.

**Kodeků**: aktivita kopírování podporuje typy kompresi Deflate, bzip2 a gzip. Azure HDInsight můžete využívat všechny tři typy zpracování. Každý komprese kodek obsahuje výhody. Například bzip2 má nejnižší kopie propustnost, ale získáte nejlepší výkon dotazů Hive s bzip2, protože je možné rozdělit pro zpracování. GZIP je nejvíce vyrovnáváním možnost, a tím nejčastěji používají. Zvolte kodeků, který nejlépe vyhovuje danému typu klient server.

**Úroveň**: můžete vybrat z dvě možnosti pro každý komprese kodeků: nejrychlejších komprimované a optimálně komprimované. Nejrychlejších možnost komprimovaný komprimaci dat, co nejrychleji i v případě, že bude výsledný soubor není komprimována optimálně. Možnost optimálně komprimované stráví více času na komprese a výsledkem minimální množství dat. Obě možnosti zobrazíte, který poskytuje lepší celkový výkon ve vašem případě můžete otestovat.

**Zabývat**: Chcete-li kopírovat velké množství dat mezi místnímu úložišti a cloudu, zvažte použití úložiště objektů blob v mezičase s kompresí. Použití dočasné úložiště je užitečné, když šířku pásma sítě a služeb Azure je omezující faktor, který chcete sadu dat vstupní i výstupní datové sady v nekomprimované formě. Jedna kopie aktivity přesněji řečeno, lze rozdělit na dvě kopie aktivity. První aktivitu kopírování zkopíruje ze zdroje do objektu blob dočasné nebo pracovní v komprimované formě. Druhý aktivitě kopírování zkopíruje komprimovaná data z pracovní databáze a pak dekomprimuje při zapíše do jímky.

## <a name="considerations-for-column-mapping"></a>Důležité informace týkající se mapování sloupců
Můžete nastavit **columnMappings** vlastnosti v aktivitě kopírování mapy všechny nebo jen některé vstupní sloupce pro výstupní sloupce. Poté, co služba pro přesun dat načte data ze zdroje, musí se provést mapování sloupců na data před zapíše data do jímky. Další zpracování snižuje kopie propustnost.

Pokud zdrojového úložiště dat dotazovatelné, například pokud je relační úložiště, jako je SQL Database nebo SQL Server, nebo pokud je úložiště typu NoSQL jako Table storage nebo Azure Cosmos DB, zvažte vkládání sloupec filtrování a změna logiku **dotazu** vlastnost místo použití mapování sloupců. Tímto způsobem projekce provádí služba pro přesun dat čte data ze zdrojového úložiště dat, kde je mnohem efektivnější.

## <a name="other-considerations"></a>Další důležité informace
Pokud je velká velikost dat, kterou chcete zkopírovat, můžete upravit obchodní logiky na další oddíl data pomocí řezů mechanismu v datové továrně. Poté naplánujte aktivitu kopírování ke spuštění častěji ke snížení velikosti dat pro každou aktivitu kopírování spustit.

Buďte opatrní počet datových sad a kopie aktivity nutnosti Data Factory ke konektoru k úložišti dat stejné ve stejnou dobu. Mnoho souběžných kopírování úlohy může omezit úložiště dat a vést ke snížení výkonu, kopie úlohy interní opakovaných pokusů a v některých případech selhání spuštění.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Vzorový scénář: kopírování z místního SQL serveru do úložiště objektů Blob
**Scénář**: kanál vychází ke zkopírování dat z místního SQL serveru do úložiště objektů Blob ve formátu CSV. Chcete-li úlohu kopírování rychleji, by do formátu bzip2 komprimovat soubory CSV.

**Test a analýzy**: propustnost aktivity kopírování je menší než 2 MB/s, což je mnohem nižší než srovnávacího testu výkonu.

**Analýza výkonu a ladění**: Chcete-li vyřešit problémy s výkonem, podíváme se na způsobu zpracování a přesunout data.

1. **Čtení dat**: Brána otevře připojení k systému SQL Server a odešle dotaz. SQL Server reaguje odesílání datového proudu k bráně prostřednictvím intranetu.
2. **Serializuje a komprese dat**: Brána serializuje datový proud do formátu CSV a komprimaci dat do datového proudu bzip2.
3. **Zápis dat**: brány odešle datový proud bzip2 do úložiště objektů Blob přes Internet.

Jak vidíte, data se zpracovat a přesunout způsobem streamování sekvenční: SQL Server > LAN > brány > WAN > úložiště objektů Blob. **Celkový výkon jsou závislé na minimální propustnost přes kanál**.

![Tok dat](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Jeden nebo více následujících faktorů může způsobit kritická místa výkonu:

* **Zdroj**: samotný SQL Server má nízkou propustnost kvůli velkým zatížením.
* **Brána pro správu dat**:
  * **LAN**: brány se nachází daleko od počítače systému SQL Server a má připojení s malou šířkou pásma.
  * **Brána**: brány bylo dosaženo jeho omezení zatížení provádět následující operace:
    * **Serializace**: serializaci do formátu CSV datový proud má pomalé propustnost.
    * **Komprese**: jste zvolili pomalé kompresní kodek (například bzip2, což je 2,8 MB/s s i7 jádra).
  * **WAN**: šířku pásma mezi podnikovou sítí a služeb Azure je nízká (například T1 = 1,544 kbps; T2 = 6,312 kb/s).
* **Jímky**: úložiště objektů Blob má nízkou propustnost. (Tento scénář nepravděpodobné, protože jeho SLA zaručuje minimálně 60 MB/s.)

V takovém případě může komprese dat bzip2 zpomalení celého kanálu. Přepnutí na kodek pro kompresi gzip může usnadňují tyto potíže.

## <a name="sample-scenarios-use-parallel-copy"></a>Ukázkové scénáře: použití paralelních kopie
**Scénář I:** kopírovat 1 000 1 MB soubory z místní systém do úložiště objektů Blob souborů.

**Analýzy a optimalizace výkonu**: pro příklad, pokud jste nainstalovali bránu na počítači čtyřjádrový, objekt pro vytváření dat používá 16 paralelní kopie k přesunutí souborů ze systému souborů do úložiště objektů Blob současně. Paralelní spuštění tohoto by měl mít za následek vysoké propustnosti. Také lze explicitně zadat počet paralelní kopie. Při kopírování mnoho malých souborů paralelní kopie výrazně pomoct propustnost pomocí prostředků efektivněji.

![Scénář 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scénář II**: kopírování 20 objekty BLOB 500 MB z úložiště objektů Blob do Data Lake Store Analytics a optimalizujte výkon.

**Analýzy a optimalizace výkonu**: V tomto scénáři pro vytváření dat zkopíruje data z úložiště objektů Blob do Data Lake Store pomocí jedné kopie (**parallelCopies** nastavena na hodnotu 1) a data v cloudu jedním přesun jednotek. Propustnost zjistíte blízko, najdete v [výkonu referenčním oddílu](#performance-reference).   

![Scénář 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scénář III**: velikost jednotlivých souborů je větší než desítek MB a celkového objemu velký.

**Analýzy a výkonu měnící**: zvýšení **parallelCopies** nevede k lepší výkon kopírování kvůli omezení prostředků DMU jeden cloud. Místo toho musíte zadat další cloudu DMUs získat další zdroje pro přesun dat provádění. Nezadávejte hodnotu **parallelCopies** vlastnost. Objekt pro vytváření dat zpracovává paralelismus za vás. V tomto případě, pokud jste nastavili **cloudDataMovementUnits** 4, propustnost o čtyřikrát dojde.

![Scénář 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referenční informace
Zde jsou výkonu sledování a ladění odkazy pro některé podporované datové úložiště:

* Úložiště Azure (včetně úložiště objektů Blob a Table storage): [cíle škálovatelnosti Azure Storage](../../storage/common/storage-scalability-targets.md) a [kontrolní seznam výkonu a škálovatelnosti Azure Storage](../../storage/common/storage-performance-checklist.md)
* Azure SQL Database: Můžete [sledovat výkon](../../sql-database/sql-database-single-database-monitor.md) a zkontrolujte procento databáze transakce jednotky (DTU)
* Azure SQL Data Warehouse: Jeho schopnosti se měří v jednotky datového skladu (Dwu); v tématu [spravovat výpočetní výkon v Azure SQL Data Warehouse (přehled)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [úrovně výkonu v Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* Místní SQL Server: [monitorování a optimalizace výkonu](https://msdn.microsoft.com/library/ms189081.aspx)
* Místní souborový server: [optimalizace výkonu pro souborové servery](https://msdn.microsoft.com/library/dn567661.aspx)
