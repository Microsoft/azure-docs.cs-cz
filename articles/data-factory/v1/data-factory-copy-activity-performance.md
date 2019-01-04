---
title: Průvodce laděním a výkonem aktivity kopírování | Dokumentace Microsoftu
description: Další informace o klíčových faktorů, které mají vliv na výkon přesouvání dat ve službě Azure Data Factory použijete aktivitu kopírování.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 632e605a6f7c9885f3854ca1f7b69ed337a1eacc
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025874"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Průvodce laděním a výkonem aktivity kopírování

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-copy-activity-performance.md)
> * [Verze 2 (aktuální verze)](../copy-activity-performance.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Průvodce pro službu Data Factory laděním a výkonem aktivity kopírování](../copy-activity-performance.md).

Azure Data Factory za kopírování přináší prvotřídní zabezpečené, spolehlivé a vysoce výkonné datové načítání řešení. To vám umožňuje desítky kopii terabajty dat každý den bohaté nejrůznějších cloudových a místních úložišť dat. Neuvěřitelně rychlá data výkon při načítání je klíč, aby se mohli soustředit na základní problém "anglicky big data": sestavovat Pokročilá analytická řešení a získat podrobné informace z všechna tato data.

Azure poskytuje sadu podniková řešení datových skladů úložiště a data, a aktivitu kopírování, která nabízí vysoce optimalizovanému data načítání prostředí, které usnadňují konfiguraci a nastavení. Pouze jednu aktivitu kopírování, která můžete dosáhnout:

* Načtení dat do **Azure SQL Data Warehouse** na **1,2 GB/s**. Návod s případu použití, naleznete v tématu [načtení 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v oblasti 15 minut](data-factory-load-sql-data-warehouse.md).
* Načtení dat do **úložiště objektů Blob v Azure** na **1,0 GB/s**
* Načtení dat do **Azure Data Lake Store** na **1,0 GB/s**

Tento článek popisuje:

* [Výkon referenční čísla](#performance-reference) pro podporované zdroje a jímky úložišť dat, které vám pomohou při plánování vašeho projektu.
* Funkce, které může zvýšit propustnost kopírování v různých scénářích, včetně [jednotek pohybu dat v cloudu](#cloud-data-movement-units), [paralelní kopírování](#parallel-copy), a [fázovaného kopírování](#staged-copy);
* [Průvodce laděním výkonu](#performance-tuning-steps) o tom, jak optimalizovat výkon a klíčové faktory, které mohou ovlivnit výkon kopírování.

> [!NOTE]
> Pokud nejste obeznámeni s aktivitou kopírování obecně platí, přečtěte si téma [přesun dat pomocí aktivity kopírování](data-factory-data-movement-activities.md) před čtením tohoto článku.
>

## <a name="performance-reference"></a>Referenční dokumentace výkonu

Jako odkaz níže uvedená tabulka zobrazuje číslo kopírování propustnost v MB/s pro dané zdroje a jímky dvojice na základě interní testování. Pro porovnání, také ukazuje, jak různé nastavení [jednotek pohybu dat v cloudu](#cloud-data-movement-units) nebo [škálovatelnost brány správy dat](data-factory-data-management-gateway-high-availability-scalability.md) (více uzlů brány) může pomoct výkon kopírování.

![Přehled výkonu](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>Ve službě Azure Data Factory verze 1 minimální cloudu jednotek přesunu dat pro cloud cloudové kopírování je dva. Pokud není zadán, najdete v článku se používají ve jednotek přesunu dat výchozí [jednotek pohybu dat v cloudu](#cloud-data-movement-units).

**Odkazuje na mějte na paměti:**
* Vypočítá se propustnost s použitím následujícího vzorce: [objem dat pro čtení ze zdroje] / [doba trvání běhu aktivity kopírování].
* Výkon referenční čísla v tabulce se měří pomocí [TPC-H](http://www.tpc.org/tpch/) datové sady v aktivitě kopírování jednoho spuštění.
* V úložištích dat Azure zdroj a jímka mají ve stejné oblasti Azure.
* Pro hybridní kopírování mezi místním a cloudovým úložištěm dat a každý uzel brány byla spuštěna na počítači, který byl odděleně od místního úložiště dat s níže specifikace. Při běhu jedné aktivity na bráně, operace kopírování využívat pouze malou část testovací počítač procesor, paměť nebo šířky pásma sítě. Další informace z [pečlivě zvážit u brány správy dat](#considerations-for-data-management-gateway).
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
> Můžete dosáhnout vyšší propustnost s využitím více jednotek pohybu dat v (DMUs) než je výchozí maximální DMUs, což je 32 pro spuštění aktivity kopírování cloud-to-cloud. Například s 100 DMUs, které můžete dosáhnout kopírování dat z objektů Blob v Azure do Azure Data Lake Store v **1.0GBps**. Zobrazit [jednotek pohybu dat v cloudu](#cloud-data-movement-units) podrobné informace o této funkci a podporované scénáře. Kontakt [podpory Azure](https://azure.microsoft.com/support/) požádat o další DMUs.

## <a name="parallel-copy"></a>Paralelní kopírování
Může číst data ze zdroje nebo zápis dat do cílového umístění **paralelní v rámci spuštění aktivity kopírování**. Tato funkce zvyšuje propustnost operace kopírování a snižuje čas potřebný k přesunutí dat.

Toto nastavení se liší od **souběžnosti** vlastnost v definici aktivity. **Souběžnosti** vlastnost určuje počet **spuštění souběžných aktivit kopírování** se zpracováním dat ze systému windows jiné aktivitě (1: 00 do 2: 00, 2 AM 3 AM, AM 3 na 4 AM a tak dále). Tato možnost je užitečná při provádění historických zatížení. Paralelní kopírování funkce se vztahuje na **jedno spuštění aktivit**.

Podívejme se na ukázkový scénář. V následujícím příkladu třeba zpracovat více řezy z minulosti. Objekt pro vytváření dat běží instance aktivity kopírování (aktivita spustit) pro každý řez:

* Datový řez ze první okno aktivity (1: 00 do 2: 00) == > 1 spuštění aktivit
* Datový řez z okna druhá aktivita (2: 00 do 3: 00) == > 2 spuštění aktivit
* Datový řez ze okně druhé aktivity (3: 00 do 4: 00) == > 3 spuštění aktivit

A tak dále.

V tomto příkladu při **souběžnosti** je hodnota nastavena na 2, **aktivita spuštěna 1** a **aktivity spuštění 2** kopírovat data ze dvou aktivit systému windows **souběžně** ke zlepšení výkonu při přesunu dat. Ale pokud několik souborů jsou přidruženy k 1 spuštění aktivit, služba pro přesun dat zkopíruje soubory ze zdroje do jednoho souboru cílové najednou.

### <a name="cloud-data-movement-units"></a>Jednotek pohybu dat v cloudu
A **jednotka pohybu dat v cloudu (DMU)** je míru, která představuje výkon (kombinaci procesoru, paměti a přidělení prostředků sítě) jedné jednotky ve službě Data Factory. DMU platí pro operace kopírování cloudu do cloudu, ale ne v hybridní kopírování.

**Minimální cloudu jednotek přesunu dat pro spuštění aktivity kopírování jsou dvě.** Pokud není zadán, následující tabulka uvádí výchozí DMUs používá ve scénářích různé kopie:

| Kopírování | Výchozí DMUs určené služby |
|:--- |:--- |
| Kopírovat data mezi úložišti souborů | Mezi 4 a 16 v závislosti na počtu a velikosti souborů. |
| Dalších scénářů kopírování | 4 |

Chcete-li přepsat toto výchozí nastavení, zadejte hodnotu **cloudDataMovementUnits** vlastnost následujícím způsobem. **Povolené hodnoty** pro **cloudDataMovementUnits** vlastnosti jsou 2, 4, 8, 16, 32. **Skutečný počet cloudu DMUs** , že operace kopírování používá za běhu je rovna nebo menší než nakonfigurovaná hodnota, v závislosti na vašich dat vzor. Informace o úrovni výkonu, může se zobrazit při konfiguraci víc jednotek pro konkrétní kopírovat zdroje a jímky, najdete v článku [výkonu](#performance-reference).

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
> Pokud potřebujete další cloudu DMUs vyšší propustnost, obraťte se na [podpory Azure](https://azure.microsoft.com/support/). Nastavení 8 a novějším v současné době používá pouze tehdy, když jste **zkopírovat víc souborů z objektu Blob úložiště/Data Lake Store/Amazon S3/Cloudová FTP/Cloudová SFTP do Blob storage nebo Data Lake Store nebo Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies
Můžete použít **parallelCopies** vlastnost umožňující označit, které chcete aktivitou kopírování pomocí paralelismu. Tato vlastnost si můžete představit jako maximální počet vláken v aktivitě kopírování, který může číst ze zdroje nebo zapisovat do svá úložiště dat jímky paralelně.

Pro každé spuštění aktivity kopírování Data Factory určuje počet paralelních kopií pro použití ke kopírování dat ze zdroje dat, ukládání a cílového datového úložiště. Výchozí počet paralelních kopie, které používá závisí na typu zdroje a jímky, kterou používáte.  

| Zdroje a jímky | Výchozí počet paralelních kopií určené služby |
| --- | --- |
| Kopírovat data mezi souborových úložišť (úložiště objektů Blob; Data Lake Store; Amazon S3; v místním systému souborů. místní HDFS) |Od 1 do 32. Závisí na velikosti souborů a počet jednotek pohybu dat v cloudu (DMUs) používá ke kopírování dat mezi dvěma cloudovými úložišti dat nebo konfigurace fyzického počítače brány pro hybridní kopírování (pro kopírování dat do nebo z úložiště dat v místním prostředí). |
| Kopírování dat z **jakékoli zdrojového úložiště dat do služby Azure Table storage** |4 |
| Všechny ostatní zdroje a jímky kombinace |1 |

Obvykle výchozí chování by vám měl dát nejlepší propustnost. Však řídit zatížení na počítačích, které jsou hostiteli vaše data ukládá, nebo pro optimalizaci výkonu kopírování, můžete výchozí hodnotu přepsat a zadat hodnotu **parallelCopies** vlastnost. Hodnota musí být mezi 1 a 32 (obojí včetně). V době běhu pro zajištění nejlepšího výkonu, aktivita kopírování používá hodnotu, která je menší než nebo rovna hodnotě, kterou jste nastavili.

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
Odkazuje na mějte na paměti:

* Při kopírování dat mezi souborové úložiště **parallelCopies** určit paralelismu na úrovni souboru. Bloků v rámci jednoho souboru by se stalo pod automaticky a transparentně a je navržena k načtení dat v paralelní aplikace a kolmě parallelCopies pomocí ideální velikost bloku vhodný typ daného zdroje dat úložiště. Skutečný počet paralelních kopie služba pro přesun dat se používá pro operaci kopírování v době běhu je delší než počet souborů, které máte. Pokud je chování kopírování nastavené **mergeFile**, aktivita kopírování nemůže využívají paralelismus úrovni souboru.
* Pokud zadáte hodnotu **parallelCopies** vlastnost, zvažte zvýšení zátěže na svá úložiště dat zdroje a jímky a k bráně, pokud jde o kopii hybridní. K tomu dojde, zejména pokud máte více souběžných spuštění stejného aktivit, které běží na stejné úložiště dat nebo aktivity. Pokud si všimnete, že úložiště dat nebo brány je zahlcen zatížení, snížit **parallelCopies** hodnota, která má-li snížit zatížení.
* Při kopírování dat z úložiště, které nejsou na základě souboru do úložišť, které jsou založené na souboru ignoruje služba pro přesun dat **parallelCopies** vlastnost. I v případě, že je zadán paralelismu, není použita v tomto případě.

> [!NOTE]
> Brána správy dat verze 1.11 nebo novější je nutné použít k použití **parallelCopies** při kopírování hybridní funkce.
>
>

Pro lepší využití tyto dvě vlastnosti a vylepšit propustnost přesunu dat najdete v tématu [vzorové případy použití](#case-study-use-parallel-copy). Není nutné konfigurovat **parallelCopies** využít výchozí chování. Pokud nakonfigurujete a **parallelCopies** je příliš malá, více cloudu DMUs nemusí plně využít.  

### <a name="billing-impact"></a>Dopad fakturace
Má **důležité** pamatovat, že se vám účtuje podle celkové doby trvání operace kopírování. Pokud úloha kopírování používá k trvat jednu hodinu jednotku na jeden cloud a teď trvá 15 minut s čtyři jednotky cloudu, bude celkové vyúčtování skoro stejné zůstane. Například použití čtyř jednotek v cloudu. První Cloudová jednotka stráví 10 minut, je druhý řádek 10 minut, třetí příkaz 5 minut a čtvrtý, ten 5 minut, poběží v jednou aktivitou kopírování. Bude vám účtována čas celkový kopírování (přesunu dat), což je 10 + 10 + 5 + 5 = 30 minut. Pomocí **parallelCopies** nemá vliv na fakturaci.

## <a name="staged-copy"></a>Kopírování dvoufázové instalace
Při kopírování dat ze zdrojového úložiště dat do úložiště dat jímky, můžete zvolit použití Blob storage jako dočasné pracovní úložiště. Pracovní je zvláště užitečná v následujících případech:

1. **Chcete ingestovat data z různých zdrojů dat do SQL Data Warehouse pomocí PolyBase**. SQL Data Warehouse používá k načtení velkých objemů dat do SQL Data Warehouse PolyBase jako vhodný mechanismus vysokou propustnost. Ale zdroj dat musí být v úložišti objektů Blob a musí splňovat další kritéria. Při načítání dat z úložiště dat než úložiště objektů Blob, můžete aktivovat kopírování prostřednictvím Blob storage dočasné pracovní data. V takovém případě služby Data Factory provádí transformace požadovaná data k zajištění, že splňují požadavky PolyBase. Potom použije PolyBase k načtení dat do SQL Data Warehouse. Další podrobnosti najdete v tématu [použití PolyBase k načítání dat do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). Návod s případu použití, naleznete v tématu [načtení 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v oblasti 15 minut](data-factory-load-sql-data-warehouse.md).
2. **Někdy trvá nějakou provést přesun hybridních dat (to znamená pro kopírování dat mezi místními daty úložiště a dat v cloudu úložiště) přes pomalé síťové připojení**. Kvůli zvýšení výkonu se dokáže komprimovat data dostupná místně tak, že bude trvat kratší dobu pro přesun dat do pracovní úložiště dat v cloudu. Předtím, než je načtete do cílového úložiště dat, pak můžete dekomprimaci dat v úložišti pracovní.
3. **Nechcete otevřít porty než 80 a port 443 v bráně firewall kvůli podnikovým zásadám IT**. Například při kopírování dat z do místního úložiště dat jímky Azure SQL Database nebo Azure SQL Data Warehouse jímky, budete muset aktivovat odchozí komunikaci TCP na portu 1433 pro bránu Windows firewall a váš podnikový firewall. V tomto scénáři využijte brány prvního kopírování dat na instanci pracovní úložiště objektů Blob prostřednictvím protokolu HTTP nebo HTTPS na portu 443. Pak načtete data do SQL Database nebo SQL Data Warehouse z pracovního objektu Blob úložiště. V tomto toku není nutné povolit port 1433.

### <a name="how-staged-copy-works"></a>Kopírování funguje jak dvoufázové instalace
Při aktivaci pracovní funkce, data se zkopíruje ze zdrojového úložiště dat do přípravného úložiště dat (funkce přineste si vlastní). V dalším kroku data zkopírována z pracovní úložiště dat do úložiště dat jímky. Data Factory automaticky spravuje tok dvoufázová za vás. Data Factory také vyčistí dočasná data z přípravného úložiště po dokončení přesunu dat.

Ve scénáři cloudové kopie (zdroje a jímky dat, které obchody jsou v cloudu) se nepoužívá bránu. Služba Data Factory provádí operace kopírování.

![Fázované kopírování: Scénář cloudu](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

V hybridním scénáři kopírování (zdroj je místní a jímkou je v cloudu), brány přesouvá data ze zdrojového úložiště dat do přípravného úložiště dat. Služba data Factory přesouvá data z pracovní úložiště dat do úložiště dat jímky. Kopírování dat z cloudového úložiště dat v místním úložišti dat prostřednictvím přípravy také podporuje obrácený toku.

![Fázované kopírování: Hybridní scénář](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Když aktivujete přesun dat pomocí přípravné úložiště, můžete určit, jestli mají data, která mají komprimovat ještě před přesunem dat ze zdrojového úložiště dat do úložiště dočasné nebo pracovní data a potom dekomprimovat před přesouvá data z průběžného nebo přípravu dat Uložit do úložiště dat jímky.

V současné době nelze kopírovat data mezi dvěma v místním úložišti dat pomocí přípravné úložiště. Očekáváme, že tato možnost bude brzy dostupná.

### <a name="configuration"></a>Konfigurace
Konfigurace **enableStaging** nastavení k určení, jestli chcete data budou umístěné v úložišti objektů Blob, než je načtete do cílového úložiště dat v aktivitě kopírování. Pokud nastavíte **enableStaging** nastavena hodnota TRUE a zadat další vlastnosti uvedené v následující tabulce. Pokud ho nemáte, budete potřebovat k vytvoření služby Azure Storage nebo úložiště sdíleného přístupu podpis propojené služby.

| Vlastnost | Popis | Výchozí hodnota | Požaduje se |
| --- | --- | --- | --- |
| **enableStaging** |Určete, jestli chcete kopírovat data prostřednictvím jako dočasné pracovní úložiště. |False |Ne |
| **linkedServiceName** |Zadejte název [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) nebo [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) propojenou službu, která odkazuje na instanci úložiště, které můžete použít jako dočasné pracovní úložiště. <br/><br/> Úložiště pomocí sdíleného přístupového podpisu nelze použít k načtení dat do SQL Data Warehouse pomocí PolyBase. Můžete ji použít v jiných scénářích. |neuvedeno |Ano, pokud **enableStaging** nastavena na hodnotu TRUE |
| **Cesta** |Zadejte cestu úložiště objektů Blob, který chcete s daty, dvoufázové instalace. Pokud nezadáte cestu, služba vytvoří kontejner pro uložení dočasných dat. <br/><br/> Zadejte cestu, pouze v případě, že používáte úložiště pomocí sdíleného přístupového podpisu nebo vyžadujete dočasných dat v konkrétním umístění. |neuvedeno |Ne |
| **enableCompression** |Určuje, zda data je nutné zkomprimovat. předtím, než je zkopírovat do cíle. Toto nastavení omezuje objem dat přenášených. |False |Ne |

Tady je ukázková definice aktivity kopírování s vlastnostmi, které jsou popsány v předchozí tabulce:

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
Bude se vám účtovat na základě dva kroky: doba kopírování a zkopírujte typu.

* Při použití přípravy během cloudové kopírování (kopírování dat z cloudového úložiště dat do jiného cloudového úložiště dat), bude se vám účtovat [Součet doba kopírování pro kroky 1 a 2] x [cloudové kopie Jednotková cena].
* Při použití přípravy při kopírování hybridní (kopírování dat z úložiště dat v místním cloudovým úložištěm dat), bude vám účtována [doba kopírování hybridní] x [hybridní kopírování Jednotková cena] + [cloudu doba kopírování] x [cloudové kopie Jednotková cena].

## <a name="performance-tuning-steps"></a>Postup optimalizace výkonu
Doporučujeme vám, že je provést tyto kroky pro optimalizaci výkonu služby Data Factory s aktivitou kopírování:

1. **Stanovení základní úrovně**. Během fáze vývoje Otestujte svůj kanál pomocí aktivity kopírování proti ukázku reprezentativní data. Data Factory můžete využít [model dělení časového](data-factory-scheduling-and-execution.md) a omezit tak množství dat, pracovat s.

   Shromažďování času spuštění a výkonové charakteristiky pomocí **monitorování a správu aplikací**. Zvolte **monitorování a správa** na domovskou stránku služby Data Factory. Ve stromovém zobrazení, zvolte **výstupní datovou sadu**. V **aktivity Windows** klikněte na položku spuštění aktivity kopírování. **Aktivity Windows** uvádí doba trvání aktivity kopírování a množství dat, který se zkopíruje. Propustnost je uveden v **Průzkumníku okna aktivity**. Další informace o aplikaci najdete v tématu [monitorování a Správa kanálů Azure Data Factory pomocí monitorování a správu aplikací](data-factory-monitor-manage-app.md).

   ![Podrobnosti o spuštění aktivit](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Později v tomto článku můžete porovnat výkon a konfigurace vašeho scénáře aktivitou kopírování [výkonu](#performance-reference) z našich testů.
2. **Diagnostika a optimalizace výkonu**. Pokud, které můžete sledovat výkon nesplňuje vaše očekávání, budete muset identifikovat kritické body výkonu. Potom optimalizace výkonu můžete odebrat nebo snižují dopad kritické body. Úplný popis Diagnostika výkonu je nad rámec tohoto článku, ale tady jsou některé běžné aspekty:

   * Funkce výkonu:
     * [Paralelní kopírování](#parallel-copy)
     * [Jednotek pohybu dat v cloudu](#cloud-data-movement-units)
     * [Kopírování dvoufázové instalace](#staged-copy)
     * [Škálovatelnost brány správy dat](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Brána správy dat](#considerations-for-data-management-gateway)
   * [Zdroj](#considerations-for-the-source)
   * [jímka](#considerations-for-the-sink)
   * [Serializace a deserializace](#considerations-for-serialization-and-deserialization)
   * [Komprese](#considerations-for-compression)
   * [Mapování sloupce](#considerations-for-column-mapping)
   * [Další aspekty](#other-considerations)
3. **Rozbalte položku konfigurace pro celou datovou sadu**. Jakmile budete spokojeni s výsledky spuštění a výkonem, můžete rozbalit definice a aktivní období kanálu pro celou datovou sadu.

## <a name="considerations-for-data-management-gateway"></a>Důležité informace týkající se brány správy dat
**Instalační program brány**: Doporučujeme použít vyhrazený počítač na hostitele brány správy dat. Zobrazit [důležité informace týkající se pomocí brány správy dat](data-factory-data-management-gateway.md#considerations-for-using-gateway).  

**Monitorování brány a nahoru/Škálováním**: Jedné logické brány pomocí jednoho nebo více uzlů brány může sloužit několika spustí aktivita kopírování ve stejnou dobu současně. Téměř v reálném čase snímek využití prostředků (procesoru, paměti, network(in/out) atd.) můžete zobrazit na počítači brány a počet souběžných úloh spuštěných oproti limit na webu Azure Portal, najdete v článku [monitorování brány na portálu](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Pokud nemáte náročné požadavky na přesun hybridních dat s velkým počtem souběžných kopírování spuštění aktivit nebo pomocí velkého objemu dat ke kopírování, zvažte pro [vertikálně nebo horizontálně navýšit kapacitu brány](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) tak, aby lépe využít váš prostředek nebo ke zřízení Další zdroje pro kopírování. 

## <a name="considerations-for-the-source"></a>Důležité informace pro zdroj
### <a name="general"></a>Obecné
Ujistěte se, že základní úložiště dat není zahlcen jiné úlohy, které jsou spuštěny na nebo před ním.

Microsoft úložišť dat, naleznete v tématu [monitorování a optimalizace témata](#performance-reference) , které jsou specifické pro úložiště dat a pomohou vám zjistit data ukládat výkonové charakteristiky, minimalizovat dobu odezvy a maximalizuje propustnost.

Pokud se kopírování dat z Blob storage do SQL Data Warehouse, zvažte použití **PolyBase** pro zvýšení výkonu. Zobrazit [použití PolyBase k načítání dat do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) podrobnosti. Návod s případu použití, naleznete v tématu [načtení 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v oblasti 15 minut](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Úložiště dat na základě souboru
*(Včetně úložiště objektů Blob, Data Lake Store, Amazon S3, s místními systémy souborů a místní HDFS)*

* **Průměrná velikost souboru a počet souborů**: Aktivita kopírování přenese jeden soubor dat najednou. Pomocí stejné množství dat k přesunutí je nižší, pokud data se skládá z mnoha malých souborů spíše než několik velkých souborů z důvodu spuštění fáze pro každý soubor celkovou propustnost. Pokud je to možné, sloučit proto malých souborů do větší soubory získáte vyšší propustnost.
* **Soubor formátů a komprese**: Další způsoby, jak vylepšit výkon, najdete v článku [důležité informace k serializaci a deserializaci](#considerations-for-serialization-and-deserialization) a [důležité informace týkající se komprese](#considerations-for-compression) oddíly.
* Pro **v místním systému souborů** scénář, ve kterém **brána správy dat** je potřeba, najdete v článku [důležité informace týkající se Brána pro správu dat](#considerations-for-data-management-gateway) části.

### <a name="relational-data-stores"></a>Úložiště relačních dat
*(Včetně SQL databázi. SQL Data Warehouse; Amazon Redshift; Databáze systému SQL Server; a Oracle, MySQL, DB2, Teradata, Sybase a PostgreSQL databázím atd.)*

* **Vzorek dat**: Schéma tabulky ovlivňuje kopírování propustnost. Velký řádek velikosti poskytuje lepší výkon než velikost malých řádku ke zkopírování stejné množství dat. Důvodem je, že databáze můžete efektivněji načíst menší počet dávek dat, které obsahují menší počet řádků.
* **Dotaz nebo uloženou proceduru**: Optimalizujte logiku dotazu nebo uložené procedury, které zadáte v zdroje aktivity kopírování se načíst data efektivněji.
* Pro **místních relačních databází**, jako je například SQL Server a Oracle, které vyžadují použití **brána správy dat**, najdete v článku [důležité informace týkající se Brána pro správu dat](#considerations-on-data-management-gateway) oddílu.

## <a name="considerations-for-the-sink"></a>Důležité informace pro jímku
### <a name="general"></a>Obecné
Ujistěte se, že základní úložiště dat není zahlcen jiné úlohy, které jsou spuštěny na nebo před ním.

Microsoft úložišť dat, najdete v tématu [monitorování a optimalizace témata](#performance-reference) , které jsou specifické pro úložiště dat. Tato témata můžete pochopit vlastnosti výkonu úložiště dat a jak minimalizovat dobu odezvy a maximalizuje propustnost.

Pokud se kopírování dat z **úložiště objektů Blob** k **SQL Data Warehouse**, zvažte použití **PolyBase** pro zvýšení výkonu. Zobrazit [použití PolyBase k načítání dat do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) podrobnosti. Návod s případu použití, naleznete v tématu [načtení 1 TB do Azure SQL Data Warehouse pomocí Azure Data Factory v oblasti 15 minut](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Úložiště dat na základě souboru
*(Včetně úložiště objektů Blob, Data Lake Store, Amazon S3, s místními systémy souborů a místní HDFS)*

* **Zkopírujte chování**: Pokud se kopírování dat z různých datových souborové úložiště aktivitu kopírování, která obsahuje tři možnosti prostřednictvím **copyBehavior** vlastnost. Zachová hierarchie, sloučí hierarchie nebo sloučí soubory. Zachování nebo sloučení hierarchie má žádné nebo téměř žádné nároky na výkon, ale slučování souborů způsobí, že chcete zvýšit nároky na výkon.
* **Soubor formátů a komprese**: Najdete v článku [důležité informace k serializaci a deserializaci](#considerations-for-serialization-and-deserialization) a [důležité informace týkající se komprese](#considerations-for-compression) oddíly pro další způsoby, jak zlepšit výkon.
* **Úložiště objektů blob**: Úložiště objektů Blob v současné době podporuje jenom objekty BLOB bloku pro přenos dat Optimalizovaná a propustnost.
* Pro **místních systémů souborů** scénáře, které vyžadují použití **brána správy dat**, najdete v článku [důležité informace týkající se Brána pro správu dat](#considerations-for-data-management-gateway) oddílu.

### <a name="relational-data-stores"></a>Úložiště relačních dat
*(Včetně SQL Database, SQL Data Warehouse, databáze SQL serveru a databáze Oracle)*

* **Zkopírujte chování**: V závislosti na vlastnostech jste nastavili pro **sqlSink**, aktivitu kopírování, která zapisuje data do cílové databáze různými způsoby.
  * Ve výchozím nastavení připojí data přesun služba používá rozhraní API hromadného kopírování k vložení dat v režimu, který poskytuje nejlepší výkon.
  * Při konfiguraci uloženou proceduru v jímce, platí databázi jednoho řádku dat v době místo jako hromadné načtení. Výkon dochází k výraznému snížení. Pokud vaši datovou sadu je velká, pokud se dá použít, zvažte možnost použití **sqlWriterCleanupScript** vlastnost.
  * Pokud nakonfigurujete **sqlWriterCleanupScript** spustit vlastností pro každou aktivitu kopírování, služba spustí skript a potom pomocí rozhraní API hromadného kopírování vložte data. Například pokud chcete přepsat celou tabulku s nejnovější data, můžete určit skript, který nejprve odstranit všechny záznamy před hromadného načtení nová data ze zdroje.
* **Velikost dat vzor a batch**:
  * Schéma tabulky ovlivňuje kopírování propustnost. Ke zkopírování stejné množství dat, velký řádek velikosti umožňuje lepší výkon než velikost malých řádku, protože databáze můžete efektivněji menší počet dávek dat potvrzení změn.
  * Aktivitu kopírování, která vloží data z řady dávky. Můžete nastavit počet řádků v dávce pomocí **writeBatchSize** vlastnost. Pokud data obsahují malé řádků, můžete nastavit **writeBatchSize** vlastnost s vyšší hodnota můžou využívat výhody nižší režijní náklady na služby batch a vyšší propustnost. Je-li velikost řádku vašich dat je velká, dejte pozor, když zvýšíte **writeBatchSize**. Vysoká hodnota může vést k kopírování chybu způsobenou přetížení databáze.
* Pro **místních relačních databází** , jako je SQL Server a Oracle, které vyžadují použití **brána správy dat**, najdete v článku [důležité informace týkající se Brána pro správu dat](#considerations-for-data-management-gateway)oddílu.

### <a name="nosql-stores"></a>Úložišť typu NoSQL
*(Včetně Table storage a Azure Cosmos DB)*

* Pro **Table storage**:
  * **oddíl**: Zápis dat do oddílů prokládané výrazně snižuje výkon. Zdrojová data řadit klíč oddílu tak, aby vložení dat efektivně do jednoho oddílu po druhé, nebo upravte logiku k zápisu dat do jednoho oddílu.
* Pro **služby Azure Cosmos DB**:
  * **Velikost dávky**: **WriteBatchSize** vlastnost nastaví počet paralelní požadavky na služby Azure Cosmos DB k vytváření dokumentů. Lepšího výkonu můžete očekávat, když zvýšíte **writeBatchSize** protože další paralelní požadavky se odesílají do služby Azure Cosmos DB. Podívejte se ale pro omezení při zápisu do služby Azure Cosmos DB (chybová zpráva "je frekvence požadavků velkých"). Použitím různých faktorů může způsobit omezení velikosti dokumentu, včetně počtu podmínky v dokumentech a zásady indexování cílové kolekce. K dosažení vyšší propustnost kopie, vezměte v úvahu pomocí lepší kolekce, například S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Důležité informace týkající se serializace a deserializace
Serializace a deserializace může dojít, pokud vstupní datové sady nebo výstupní datové sady je soubor. Zobrazit [podporované formáty souborů a komprese](data-factory-supported-file-and-compression-formats.md) s podrobnostmi o podporovaných formátech souborů pomocí aktivity kopírování.

**Zkopírujte chování**:

* Kopírování souborů mezi úložišti dat na základě souboru:
  * Když vstupní a výstupní datové sady obě mají stejné nebo žádné nastavení formátu souboru, služba pro přesun dat provádí binární kopie bez jakýchkoli serializace nebo deserializace. Zobrazí větší propustnost v porovnání s scénář, ve které se liší od sebe navzájem nastavení formátu souboru zdroje a jímky.
  * Pokud vstupní a výstupní datové sady obě jsou ve formátu textu a pouze kódování typ se liší, služba pro přesun dat se jenom převod kódování. Neprovádí žádné serializace a deserializace, což způsobí, že některé výkonu režie ve srovnání s binární kopie.
  * Pokud vstupní a výstupní datové sady i mají různé formáty souborů nebo různé konfigurace, jako je oddělovače, služba pro přesun dat deserializuje zdroje dat do datového proudu, transformaci a pak ho serializovat do výstupní formát, který jste určili. Výsledkem této operace mnohem více významné výkonnostní režii ve srovnání s další scénáře.
* Při kopírování souborů do a z úložiště dat, které nejsou založené na souboru (například z úložiště založené na souborech do relačního úložiště) serializaci nebo deserializaci krok je povinný. Tento krok vede významné výkonnostní režii.

**Formát souboru**: Formát souboru, který zvolíte může ovlivnit výkon kopírování. Například je Avro kompaktní binární formát, který ukládá metadata s daty. Má širokou podporu v ekosystému Hadoop pro zpracování a dotazování na ně. Je však dražší, serializace a deserializace, což vede k nižší propustnost kopírování ve srovnání s textový formát Avro. Ujistěte se, podle vašeho výběru formátu v průběhu zpracování toku komplexně. Začněte s co tvoří data uložená v úložišti zdroje dat nebo extrahovat z externích systémů: nejlepší formát pro úložiště, analytické zpracování a dotazování; a v jakém formátu data exportují do datového tržiště pro nástroje pro vytváření sestav a vizualizace. Někdy formát souboru, který je neoptimální pro čtení a zápisu může být dobrou volbou, pokud byste zvážit celkové analytické procesu.

## <a name="considerations-for-compression"></a>Důležité informace pro kompresi
Pokud vstupní nebo výstupní datové sady je soubor, můžete nastavit aktivitu kopírování k provedení kompresi nebo dekompresi jako zapíše data do cíle. Při výběru komprese provedete kompromis mezi vstupně výstupní (I/O) a procesoru. Komprese dat příplatek ve výpočetních prostředcích. Ale na oplátku omezuje v / v sítě a úložiště. V závislosti na vašich dat může se zobrazit boost v celkovou propustnost kopírování.

**Kodek**: Aktivitu kopírování, která podporuje typy komprese Deflate, bzip2 a gzip. Azure HDInsight mohou využívat všechny tři typy pro zpracování. Každý kompresní kodek má výhody. Například bzip2 má nejnižší kopírování propustnost, ale získat nejlepší výkon dotazů Hive pomocí bzip2, protože je možné rozdělit ke zpracování. GZIP je nepoužít možnost vyvážená nejvíce a se nejčastěji používá. Zvolte kodek, který nejlépe vyhovuje vaší situaci začátku do konce.

**Úroveň**: Můžete zvolit ze dvou možností pro každý kompresní kodek: nejrychlejší komprimované a optimálně komprimované. Nejrychlejší komprimované možnost co nejrychleji komprimuje data i v případě, že výsledný soubor není komprimována optimálně. Optimálně komprimovaný možnost tráví víc času na komprese a vrací minimální nároky na data. Obě možnosti zobrazíte, která poskytuje lepší výkon ve vašem případě můžete otestovat.

**Potřeba**: Kopírování velkých objemů dat mezi místním úložištěm a cloudem, zvažte použití úložiště objektů blob v přechodném komprese. Použití dočasné úložiště je užitečné, když omezujícím faktorem je šířka pásma podnikové sítě a služby Azure a chcete, aby vstupní datové sady i výstupní datové sady v nekomprimovaných formuláře. Jedna kopie aktivity můžete přesněji řečeno, rozdělit na dvě kopie aktivity. První aktivita kopírování zkopíruje ze zdroje do objektu blob dočasné nebo pracovní v komprimované formě. Druhou aktivitu kopírování kopíruje komprimovaných dat z pracovní a potom dekomprimuje zatímco zapisuje do jímky.

## <a name="considerations-for-column-mapping"></a>Důležité informace týkající se mapování sloupce
Můžete nastavit **columnMappings** vlastnost v aktivitě kopírování do mapy všechny, nebo jen některé ze sloupců vstupní a výstupní sloupce. Poté, co služba pro přesun dat načte data ze zdroje, je potřeba provést mapování sloupců s daty před zapisuje data do jímky. Tato další zpracování snižuje kopírování propustnost.

Pokud zdrojové úložiště dat je zadávat dotazy, pokud je relačního úložiště, jako je SQL Database nebo SQL Server, nebo pokud je úložiště typu NoSQL, jako je Table storage nebo Azure Cosmos DB, představte si třeba doručením (push) sloupce filtrování a změna uspořádání logiku pro **dotazu** vlastnosti namísto použití mapování sloupců. Tímto způsobem projekce nastane, když služba pro přesun dat čte data ze zdrojového úložiště dat, kde je mnohem efektivnější.

## <a name="other-considerations"></a>Další důležité informace
Při velké množství dat, který chcete zkopírovat můžete upravit vaši obchodní logiku na další oddíl data pomocí mechanismu řezů ve službě Data Factory. Naplánujte aktivitu kopírování, která spouštět častěji ke snížení velikosti dat pro každé spuštění aktivity kopírování.

Buďte opatrní počet datových sad a kopie aktivity vyžadující služby Data Factory ke konektoru do stejného úložiště dat ve stejnou dobu. Mnoho souběžných kopírování úloh může omezit úložiště dat a vést ke snížení výkonu, opakování interní úlohu kopírování a v některých případech se selhání spuštění.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Ukázkový scénář: Kopírování z místního SQL serveru do úložiště objektů Blob
**Scénář**: Kanál je určený pro kopírování dat z místních SQL serveru do úložiště objektů Blob ve formátu CSV. Chcete-li úlohu kopírování rychleji, by měl zkomprimují soubory CSV do formátu bzip2.

**Testování a analýzy**: Propustnost aktivitu kopírování, která je menší než 2 MB/s, což je mnohem pomalejší než srovnávacího testu výkonu.

**Analýza výkonu a ladění**: Řešení potíží s výkonem, Podívejme se na způsobu zpracování a přesunout data.

1. **Čtení dat**: Brána otevře připojení k SQL serveru a odešle dotaz. SQL Server odpoví odesílání datového proudu k bráně prostřednictvím sítě intranet.
2. **Serializace a komprese dat**: Brána serializuje datový proud do formátu CSV a komprimuje data do datového proudu bzip2.
3. **Zápis dat**: Brána odešle datový proud bzip2 do úložiště objektů Blob přes Internet.

Jak je vidět, data se zpracování a streamování sekvenčním způsobem přesunout: SQL Server > LAN > brána > síť WAN > úložiště objektů Blob. **Celkový výkon je chráněný branami minimální propustnost přes kanál**.

![Tok dat](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Nejméně jednu z těchto faktorů může způsobit snížení výkonu:

* **Zdroj**: Samotný Server SQL je Nízká propustnost kvůli velkým zatížením.
* **Brána správy dat**:
  * **LAN**: Brána se nachází daleko od počítači s SQL serverem a mají připojení s malou šířkou pásma.
  * **Brána**: Brány dosáhl omezení zatížení provádět následující operace:
    * **Serializace**: Serializace datový proud do formátu CSV má pomalé propustnost.
    * **Komprese**: Rozhodli jste pomalé kompresní kodek (například bzip2, což je 2.8 MB/s s Core i7).
  * **SÍŤ WAN**: Je s nízkou šířkou pásma mezi podnikovou sítí a služby Azure (například T1 = 1,544 kB/s; T2 = 6,312 kb/s).
* **Jímka**: Úložiště objektů blob je Nízká propustnost. (Tento scénář je nepravděpodobné, že by jeho SLA zaručuje minimálně 60 MB/s.)

V takovém případě může být komprese dat bzip2 zpomalení celého kanálu. Přepnutí na kompresní kodek gzip může zmírnit tyto potíže.

## <a name="sample-scenarios-use-parallel-copy"></a>Ukázkové scénáře: Použít paralelní kopírování
**Scénář I:** Zkopírujte soubory 1 000 1 MB v místním systému souborů do úložiště objektů Blob.

**Analýza a optimalizace výkonu**: Příklad Pokud jste nainstalovali bránu na počítači čtyřjádrový, Data Factory používá 16 paralelní kopie pro přesun souborů ze systému souborů do úložiště objektů Blob současně. Toto paralelní spuštění by měl vést vysokou propustnost. Také můžete explicitně určit počet paralelních kopie. Při kopírování mnoha malých souborů paralelní kopie značně pomáhají propustnost s použitím prostředků efektivněji.

![Scénář 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scénář II**: Zkopírujte 20 objektů BLOB 500 MB z úložiště objektů Blob do Data Lake Store Analytics a pak ladění výkonu.

**Analýza a optimalizace výkonu**: V tomto scénáři objektu pro vytváření dat zkopíruje data z úložiště objektů Blob do Data Lake Store s použitím jedné kopie (**parallelCopies** nastavena na hodnotu 1) a jednotky přesunu dat jeden cloud. Propustnost můžete sledovat blízko, který najdete v [výkonu informační části](#performance-reference).   

![Scénář 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scénář III**: Velikost jednotlivých souborů je větší než desítky MB a celkový objem je velká.

**Analýzy a výkonu měnící**: Zvýšení **parallelCopies** nevede k lepší výkon kopírování z důvodu omezení prostředků DMU jeden cloud. Místo toho je třeba zadat další cloudu DMUs, chcete-li získat více prostředků k provádění přesunu dat. Nezadávejte hodnotu **parallelCopies** vlastnost. Data Factory zajišťuje paralelismus za vás. V tomto případě, pokud jste nastavili **cloudDataMovementUnits** 4, propustnost o čtyřikrát vyvolá.

![Scénář 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referenční informace
Tady je pro sledování výkonu a ladění pro některé z úložišť dat podporovaných odkazů:

* Azure Storage (včetně úložiště objektů Blob a Table storage): [Azure cíle škálovatelnosti úložiště](../../storage/common/storage-scalability-targets.md) a [kontrolní seznam výkonu a škálovatelnosti služby Azure Storage](../../storage/common/storage-performance-checklist.md)
* Azure SQL Database: Je možné [sledovat výkon](../../sql-database/sql-database-single-database-monitor.md) a zkontrolovat procento databáze transakce jednotek (DTU)
* Azure SQL Data Warehouse: Jeho funkce se měří v jednotkách datového skladu (Dwu); Zobrazit [spravovat výpočetní výkon v Azure SQL Data Warehouse (přehled)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [Úrovně výkonu ve službě Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* V místním SQL serveru: [Monitorování a optimalizace výkonu](https://msdn.microsoft.com/library/ms189081.aspx)
* S místními souborového serveru: [Ladění výkonů u souborových serverů](https://msdn.microsoft.com/library/dn567661.aspx)
