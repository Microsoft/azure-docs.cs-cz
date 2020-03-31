---
title: Průvodce laděním a výkonem aktivity kopírování
description: Přečtěte si o klíčových faktorech, které ovlivňují výkon přesunu dat v Azure Data Factory při použití aktivity kopírování.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c4ca328aa0ddc61d86a435b93fe775f294287b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527380"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Průvodce laděním a výkonem aktivity kopírování

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-copy-activity-performance.md)
> * [Verze 2 (aktuální verze)](../copy-activity-performance.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Kopírování výkonu aktivity a průvodce laděním pro data factory](../copy-activity-performance.md).

Aktivita kopírování Azure Data Factory poskytuje prvotřídní zabezpečené, spolehlivé a vysoce výkonné řešení načítání dat. Umožňuje kopírovat desítky terabajtů dat každý den v široké škále cloudových a místních úložišť dat. Klíčem k jemu je, abyste se mohli soustředit na základní problém "velkých objemů dat": vytvářet pokročilá analytická řešení a získat z nich podrobné přehledy.

Azure poskytuje sadu řešení pro ukládání dat a datový sklad na podnikové úrovni a aktivita kopírování nabízí vysoce optimalizované prostředí pro načítání dat, které se snadno nastavuje a nastavuje. S jedinou aktivitou kopírování můžete dosáhnout:

* Načítání dat do **Azure SQL Data Warehouse** na **1,2 bps**. Návod k případu použití najdete v [tématu načtení 1 TB do Azure SQL Data Warehouse do 15 minut s Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Načítání dat do **úložiště objektů blob Azure** na **1,0 BPS**
* Načítání dat do **úložiště datových jezer Azure** s **1,0 bps**

Tento článek popisuje:

* [Referenční čísla výkonu](#performance-reference) pro podporovaná úložiště dat zdroje a jímky, která vám pomohou naplánovat projekt.
* Funkce, které mohou zvýšit propustnost kopírování v různých scénářích, včetně [jednotek přesunu dat cloudu](#cloud-data-movement-units), [paralelní kopie](#parallel-copy)a [fázované kopie](#staged-copy);
* [Pokyny pro ladění výkonu,](#performance-tuning-steps) jak optimalizovat výkon a klíčové faktory, které mohou ovlivnit výkon kopírování.

> [!NOTE]
> Pokud nejste obeznámeni s kopírovat aktivity obecně, [přečtěte si téma Přesunutí dat pomocí kopírovat aktivity](data-factory-data-movement-activities.md) před čtením tohoto článku.
>

## <a name="performance-reference"></a>Referenční výkonnost

Jako odkaz níže uvedená tabulka ukazuje číslo propustnost kopie v MB/s pro daný zdroj a dvojice jímky na základě interního testování. Pro srovnání také ukazuje, jak různé nastavení [jednotek pohybu dat v cloudu](#cloud-data-movement-units) nebo [škálovatelnost brány pro správu dat](data-factory-data-management-gateway-high-availability-scalability.md) (více uzlů brány) může pomoci při výkonu kopírování.

![Výkonnostní matice](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>Ve službě Azure Data Factory verze 1 jsou minimální jednotky pro přesun y cloudových dat dvě. Pokud není zadán, viz výchozí jednotky pohybu dat používané v [jednotkách pohybu dat v cloudu](#cloud-data-movement-units).

**Body k poznámce:**
* Propustnost se vypočítá pomocí následujícího vzorce: [velikost dat čtených ze zdroje]/[Doba trvání spuštění aktivity kopírování].
* Referenční čísla výkonu v tabulce byla měřena pomocí datové sady [TPC-H](http://www.tpc.org/tpch/) v jednom spuštění aktivity kopírování.
* V úložištích dat Azure jsou zdroj a jímky ve stejné oblasti Azure.
* Pro hybridní kopírování mezi místními a cloudovými úložišti dat byl každý uzel brány spuštěn v počítači, který byl oddělený od místního úložiště dat s níže uvedenou specifikací. Když byla na bráně spuštěna jedna aktivita, operace kopírování spotřebovává pouze malou část procesoru, paměti nebo šířky pásma sítě testovacího počítače. Další informace najdete v [úvahách o bráně pro správu dat](#considerations-for-data-management-gateway).
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
        <td>Network (Síť)</td>
        <td>Internetové rozhraní: 10 Gb/s; intranetové rozhraní: 40 Gb/s</td>
    </tr>
    </table>


> [!TIP]
> Vyšší propustnost můžete dosáhnout využitím více jednotek přesunu dat (DMU) než výchozí maximální jednotky DMU, což je 32 pro spuštění aktivity kopírování z cloudu na cloud. Například s 100 DMU, můžete dosáhnout kopírování dat z objektů Blob Azure do Azure Data Lake Store na **1.0GBps**. Podrobnosti o této funkci a podporovaném scénáři najdete v části [Jednotky přesunu dat v cloudu.](#cloud-data-movement-units) Obraťte se na [podporu Azure](https://azure.microsoft.com/support/) a požádejte o další jednotky DMU.

## <a name="parallel-copy"></a>Paralelní kopírování
Můžete číst data ze zdroje nebo zapisovat data do cíle **paralelně v rámci spuštění aktivity kopírování**. Tato funkce zvyšuje propustnost operace kopírování a zkracuje dobu potřebný k přesunutí dat.

Toto nastavení se liší od vlastnosti **souběžnosti** v definici aktivity. Vlastnost **souběžnosti** určuje počet **souběžných spuštění aktivity kopírování** pro zpracování dat z různých oken aktivit (1:00 až 2:00, 2:00 až 3:00, 3:00 až 4:00 a tak dále). Tato funkce je užitečná při provádění historické zatížení. Funkce paralelního kopírování platí pro **spuštění jedné aktivity**.

Podívejme se na ukázkový scénář. V následujícím příkladu je třeba zpracovat více řezů z minulosti. Data Factory spustí instanci aktivity kopírování (spuštění aktivity) pro každý řez:

* Řez dat z prvního okna aktivity (1:00 až 2:00) ==> Aktivita spuštěna 1
* Řez dat z druhého okna aktivity (2:00 až 3:00) ==> Aktivita spustit 2
* Řez dat z druhého okna aktivity (3:00 až 4:00) ==> Aktivita spustit 3

A tak dále.

V tomto příkladu, když je hodnota **souběžnosti** nastavena na 2, **aktivita spustit 1** a **aktivita spustit 2** kopírovat data ze dvou oken **aktivity současně** ke zlepšení výkonu pohybu dat. Pokud je však ke spuštění aktivity 1 přidruženo více souborů, služba přesunu dat zkopíruje soubory ze zdroje do cílového souboru současně.

### <a name="cloud-data-movement-units"></a>Jednotky pro přesun y cloudových dat
Cloudová **jednotka pro přesun dat (DMU)** je míra, která představuje výkon (kombinaci procesoru, paměti a přidělení síťových prostředků) jedné jednotky v datové továrně. DMU je použitelná pro operace kopírování cloud-cloud, ale ne v hybridní kopii.

**Minimální jednotky pohybu dat v cloudu, které zmocňují spuštění aktivity kopírování, jsou dvě.** Pokud není zadán, v následující tabulce jsou uvedeny výchozí dmu používané v různých scénářích kopírování:

| Kopírovat scénář | Výchozí dmu určené službou |
|:--- |:--- |
| Kopírování dat mezi úložišti založenými na souborech | Mezi 4 a 16 v závislosti na počtu a velikosti souborů. |
| Všechny ostatní scénáře kopírování | 4 |

Chcete-li přepsat toto výchozí nastavení, zadejte hodnotu vlastnosti **cloudDataMovementUnits** následujícím způsobem. **Povolené hodnoty** pro **vlastnost cloudDataMovementUnits** jsou 2, 4, 8, 16, 32. **Skutečný počet cloudových dmu,** které operace kopírování používá za běhu, je v závislosti na datovém vzoru roven nebo menší než nakonfigurovaná hodnota. Informace o úrovni zvýšení výkonu, které můžete získat při konfiguraci více jednotek pro konkrétní zdroj kopie a jímky, naleznete v [odkazu na výkon](#performance-reference).

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
> Pokud potřebujete další cloudové DMU pro vyšší propustnost, obraťte se na [podporu Azure](https://azure.microsoft.com/support/). Nastavení 8 a vyšší aktuálně funguje pouze v případě, že **zkopírujete více souborů z úložiště blob/Úložiště datových jezer/Amazon S3/cloud FTP/cloud SFTP do úložiště blob/Data Lake Store/Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies
Vlastnost **parallelCopies** můžete použít k označení paralelismu, který chcete použít aktivitu kopírování. Tuto vlastnost si můžete myslet jako maximální počet podprocesů v rámci aktivity kopírování, které lze číst ze zdroje nebo zapisovat do úložiště dat jímky paralelně.

Pro každé spuštění aktivity kopírování určuje data factory počet paralelních kopií, které mají být zkopírovány data z úložiště zdrojových dat a do cílového úložiště dat. Výchozí počet paralelních kopií, které používá, závisí na typu zdroje a jímky, které používáte.

| Zdroj a umyvadlo | Výchozí počet paralelních kopií určený službou |
| --- | --- |
| Kopírování dat mezi úložišti založenými na souborech (úložiště objektů Blob; Úložiště datových jezer; Amazon S3; místní systém souborů; místní HDFS) |Mezi 1 a 32. Závisí na velikosti souborů a počtu jednotek cloudového přesunu dat (DMU) používaných ke kopírování dat mezi dvěma úložišti cloudových dat nebo na fyzické konfiguraci počítače Gateway používaného pro hybridní kopírování (ke kopírování dat do nebo z místního úložiště dat). |
| Kopírování dat z **libovolného zdrojového úložiště dat do úložiště Azure Table** |4 |
| Všechny ostatní páry zdrojů a jímek |1 |

Výchozí chování by obvykle mělo poskytnout nejlepší propustnost. Chcete-li však řídit zatížení počítačů, které hostují úložiště dat, nebo optimalizovat výkon kopírování, můžete přehlasovat výchozí hodnotu a zadat hodnotu vlastnosti **parallelCopies.** Hodnota musí být mezi 1 a 32 (oba včetně). Za běhu pro nejlepší výkon funkce Kopírovat aktivitu používá hodnotu, která je menší nebo rovna hodnotě, kterou nastavíte.

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
Body k poznámce:

* Při kopírování dat mezi úložišť založených na **souborech, parallelCopies** určit paralelismus na úrovni souboru. Bloků v rámci jednoho souboru by dojít pod automaticky a transparentně a je navržen tak, aby použití nejvhodnější velikost bloku pro daný typ úložiště zdrojových dat načíst data paralelně a ortogonu na paralelní kopie. Skutečný počet paralelních kopií, které služba přesunu dat používá pro operaci kopírování za běhu, není větší než počet souborů, které máte. Pokud je chování kopírování **mergeFile**, aktivita kopírování nemůže využít paralelismus na úrovni souboru.
* Když zadáte hodnotu pro **vlastnost parallelCopies,** zvažte zvýšení zatížení na zdrojové úložiště a umístění dat jímky a bránu, pokud se jedná o hybridní kopii. K tomu dochází zejména v případě, že máte více aktivit nebo souběžných spuštění stejné aktivity, které běží proti stejnému úložišti dat. Pokud zjistíte, že úložiště dat nebo gateway je zahlceni zatížení, snížit **hodnotu parallelCopies** zmírnit zatížení.
* Při kopírování dat z úložišť, které nejsou založeny na souboru do úložišť, které jsou založeny na souboru, služba přesundat ignoruje **vlastnost parallelCopies.** I v případě, že paralelismus je zadán, není použita v tomto případě.

> [!NOTE]
> Chcete-li použít funkci **parallelCopies** při hybridní kopii, je nutné použít bránu pro správu dat verze 1.11 nebo novější.
>
>

Chcete-li lépe použít tyto dvě vlastnosti a zvýšit propustnost přesunu dat, podívejte se na ukázkové případy použití. Není nutné konfigurovat **paralelní kopie** využít výchozí chování. Pokud nakonfigurujete a **paralelní kopie** je příliš malý, více cloudových DMU nemusí být plně využita.

### <a name="billing-impact"></a>Dopad na fakturaci
Je **důležité** si uvědomit, že vám budou účtovány poplatky na základě celkového času operace kopírování. Pokud úloha kopírování, která se používá k jedné hodině s jednou cloudovou jednotkou, trvá 15 minut se čtyřmi cloudovými jednotkami, celkový účet zůstane téměř stejný. Například použít čtyři cloudové jednotky. První cloudová jednotka stráví 10 minut, druhá, 10 minut, třetí, 5 minut a čtvrtá, 5 minut, vše v jednom spuštění aktivity kopírování. Účtuje se vám celkový čas kopírování (přesun dat), což je 10 + 10 + 5 + 5 = 30 minut. Použití **parallelCopies** nemá vliv na fakturaci.

## <a name="staged-copy"></a>Fázovaná kopie
Když zkopírujete data ze zdrojového úložiště dat do úložiště dat jímky, můžete použít úložiště objektů blob jako dočasné pracovní úložiště. Pracovní je zvláště užitečné v následujících případech:

1. **Chcete ingestovat data z různých datových úložišť do datového skladu SQL přes PolyBase**. SQL Data Warehouse používá PolyBase jako mechanismus vysoké propustnosti k načtení velkého množství dat do datového skladu SQL. Zdrojová data však musí být v úložišti objektů Blob a musí splňovat další kritéria. Když načtete data z jiného úložiště dat než úložiště objektů blob, můžete aktivovat kopírování dat prostřednictvím dočasného pracovního úložiště objektů blob. V takovém případě Data Factory provede požadované transformace dat k zajištění, že splňuje požadavky PolyBase. Pak používá PolyBase k načtení dat do datového skladu SQL. Další podrobnosti najdete v článku [Použití PolyBase k načtení dat do datového skladu Azure SQL](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). Návod k případu použití najdete v [tématu načtení 1 TB do Azure SQL Data Warehouse do 15 minut s Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Někdy trvá nějakou dobu provést hybridní přesun dat (to znamená kopírovat mezi místním úložištěm dat a úložištěm cloudových dat) přes pomalé síťové připojení**. Chcete-li zvýšit výkon, můžete komprimovat data v místním tak, aby trvalo méně času přesunout data do pracovníúložiště dat v cloudu. Potom můžete dekomprimovat data v pracovním úložišti před načtením do cílového úložiště dat.
3. **Nechcete otevírat jiné porty než port 80 a port 443 v bráně firewall z důvodu podnikových zásad IT**. Pokud například zkopírujete data z místního úložiště dat do jímky databáze Azure SQL nebo jímky datového skladu Azure SQL, musíte aktivovat odchozí komunikaci TCP na portu 1433 pro bránu firewall systému Windows i podnikovou bránu firewall. V tomto scénáři využijte výhod brány k první kopírování dat do pracovní instance úložiště objektů Blob přes HTTP nebo HTTPS na portu 443. Potom načtěte data do databáze SQL nebo datového skladu SQL z pracovní hodu úložiště objektů blob. V tomto toku není nutné povolit port 1433.

### <a name="how-staged-copy-works"></a>Jak fázované kopie funguje
Když aktivujete pracovní funkci, nejprve se data zkopírují ze zdrojového úložiště dat do pracovního úložiště dat (přineste si vlastní). Dále se data zkopírují z pracovníúložiště dat do úložiště dat jímky. Data Factory automaticky spravuje dvoustupňový tok za vás. Data Factory také vyčistí dočasná data z pracovního úložiště po dokončení přesunu dat.

Ve scénáři kopírování cloudu (úložiště dat zdroje i jímky jsou v cloudu), brána se nepoužívá. Služba Data Factory provádí operace kopírování.

![Inscenovaná kopie: Scénář cloudu](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

Ve scénáři hybridní kopie (zdroj je místní a jímka je v cloudu), brána přesune data z úložiště zdrojových dat do pracovníúložiště dat. Služba Data Factory přesune data z pracovního úložiště dat do úložiště dat jímky. Kopírování dat z úložiště cloudových dat do místního úložiště dat prostřednictvím pracovní je také podporováno s obráceným tokem.

![Fázovaná kopie: Hybridní scénář](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Když aktivujete přesun dat pomocí pracovního úložiště, můžete určit, zda mají být data komprimována před přesunutím dat ze zdrojového úložiště dat do dočasného nebo pracovního úložiště dat a poté dekomprimována před přesunutím dat z mezidobí nebo pracovních dat. do úložiště dat jímky.

V současné době nelze kopírovat data mezi dvěma místními úložišti dat pomocí pracovního úložiště. Očekáváme, že tato možnost bude brzy k dispozici.

### <a name="configuration"></a>Konfigurace
Nakonfigurujte nastavení **enableStaging** v aktivitě kopírování a určete, zda mají být data připravena k přípravě v úložišti objektů Blob před jejich načtením do cílového úložiště dat. Když nastavíte **funkci enableStaging** na HODNOTU TRUE, zadejte další vlastnosti uvedené v následující tabulce. Pokud ho nemáte, musíte taky vytvořit službu azure storage nebo storage pro vytvoření podpisu propojené s podpisem.

| Vlastnost | Popis | Výchozí hodnota | Požaduje se |
| --- | --- | --- | --- |
| **enableStaging** |Určete, zda chcete kopírovat data prostřednictvím dočasného pracovního úložiště. |False |Ne |
| **linkedServiceName** |Zadejte název propojené služby [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) nebo [AzureStorageSas,](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) která odkazuje na instanci úložiště, které používáte jako dočasné pracovní úložiště. <br/><br/> Úložiště se sdíleným přístupovým podpisem nelze použít k načtení dat do datového skladu SQL prostřednictvím služby PolyBase. Můžete jej použít ve všech ostatních scénářích. |Není dostupné. |Ano, pokud je **enableStaging** nastaven na hodnotu TRUE |
| **Cestu** |Zadejte cestu úložiště objektů blob, kterou chcete obsahovat fázovaná data. Pokud nezadáte cestu, služba vytvoří kontejner pro ukládání dočasných dat. <br/><br/> Cestu zadejte pouze v případě, že používáte úložiště se sdíleným přístupovým podpisem nebo požadujete, aby dočasná data byla v určitém umístění. |Není dostupné. |Ne |
| **povolitKomprese** |Určuje, zda mají být data před zkopírováním do cílového umístění komprimována. Toto nastavení snižuje objem přenášených dat. |False |Ne |

Zde je ukázková definice aktivity kopírování s vlastnostmi popsanými v předchozí tabulce:

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

### <a name="billing-impact"></a>Dopad na fakturaci
Poplatky vám budou účtovány na základě dvou kroků: doba trvání kopírování a typ kopie.

* Když používáte pracovní během kopírování v cloudu (kopírování dat z úložiště dat v cloudu do jiného úložiště dat v cloudu), bude vám účtována [součet doby trvání kopírování pro krok 1 a krok 2] x [cena jednotky kopírování cloudu].
* Při použití pracovní během hybridní kopie (kopírování dat z místního úložiště dat do úložiště cloudových dat) se vám bude účtovat [doba trvání hybridní kopie] x [hybridní cena kopie jednotky] + [doba trvání kopie cloudu] x [cena jednotky cloudcopy].

## <a name="performance-tuning-steps"></a>Kroky ladění výkonu
Doporučujeme provést tyto kroky k vyladění výkonu služby Data Factory pomocí aktivity kopírování:

1. **Vytvořte směrný plán**. Během fáze vývoje otestujte kanál pomocí aktivity kopírování proti reprezentativnímu vzorku dat. [Model krájení](data-factory-scheduling-and-execution.md) data factory můžete omezit množství dat, se kterými pracujete.

   Shromážděte čas spuštění a charakteristiky výkonu pomocí **aplikace monitorování a správa**. Na domovské stránce datové továrny zvolte **Sledovat & Spravovat.** Ve stromovém zobrazení zvolte **výstupní datovou sadu**. V seznamu **Aktivita windows** zvolte spustit aktivitu kopírování. **Program Windows pro aktivity** uvádí dobu trvání aktivity kopírování a velikost zkopírovaných dat. Propustnost je uvedena v **Průzkumníku oken aktivit**. Další informace o aplikaci najdete v [tématu Sledování a správa kanálů Azure Data Factory pomocí aplikace Monitorování a správa](data-factory-monitor-manage-app.md).

   ![Podrobnosti o spuštění aktivit](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Dále v článku můžete porovnat výkon a konfiguraci vašeho scénáře na odkaz [na výkon](#performance-reference) aktivity z našich testů.
2. **Diagnostikujte a optimalizujte výkon**. Pokud výkon, který sledujete, nesplňuje vaše očekávání, je třeba identifikovat kritická místa výkonu. Potom optimalizujte výkon, abyste odstranili nebo snížili účinek kritických míst. Úplný popis diagnostiky výkonu je nad rámec tohoto článku, ale zde jsou některé běžné aspekty:

   * Funkce výkonu:
     * [Paralelní kopírování](#parallel-copy)
     * [Jednotky pro přesun y cloudových dat](#cloud-data-movement-units)
     * [Fázovaná kopie](#staged-copy)
     * [Škálovatelnost brány pro správu dat](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Brána správy dat](#considerations-for-data-management-gateway)
   * [Zdroj](#considerations-for-the-source)
   * [Jímka](#considerations-for-the-sink)
   * [Serializace a deserializace](#considerations-for-serialization-and-deserialization)
   * [Komprese](#considerations-for-compression)
   * [Mapování sloupců](#considerations-for-column-mapping)
   * [Další úvahy](#other-considerations)
3. **Rozbalte konfiguraci na celou sadu dat**. Když jste spokojeni s výsledky spuštění a výkon, můžete rozšířit definici a potrubí aktivní období pokrýt celou sadu dat.

## <a name="considerations-for-data-management-gateway"></a>Důležité informace pro bránu pro správu dat
**Nastavení brány**: Doporučujeme použít vyhrazený počítač pro hostování brány pro správu dat. Informace [o používání brány pro správu dat](data-factory-data-management-gateway.md#considerations-for-using-gateway)naleznete v tématu Důležité informace o používání brány pro správu dat .

**Monitorování brány a škálování nahoru/out**: Jedna logická brána s jedním nebo více uzly brány může současně obsluhovat více spuštění aktivity kopírování současně. Můžete zobrazit téměř v reálném čase snímek využití prostředků (CPU, paměť, síť (dovnitř / ven), atd.) na počítači brány, stejně jako počet souběžných úloh spuštěných versus limit na portálu Azure, viz [Monitorování brány na portálu](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Pokud máte velké potřeby na hybridní přesun dat buď s velkým počtem souběžných spuštění aktivity kopírování nebo s velkým objemem dat ke kopírování, zvažte [vertikálně navýšit nebo vertikálně navýšit kapacitu brány](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) tak, aby lépe využít prostředek nebo zřídit více prostředků pro posílení kopírování.

## <a name="considerations-for-the-source"></a>Důležité informace o zdroji
### <a name="general"></a>Obecné
Ujistěte se, že základní úložiště dat není zahlceni jinými úlohami, které jsou spuštěny na nebo proti němu.

Úložiště dat Microsoftu najdete v [tématech monitorování a ladění témat,](#performance-reference) která jsou specifická pro úložiště dat, a pomáhávám pochopit charakteristiky výkonu úložiště dat, minimalizovat dobu odezvy a maximalizovat propustnost.

Pokud zkopírujete data z úložiště objektů Blob do datového skladu SQL, zvažte použití **PolyBase** ke zvýšení výkonu. Podrobnosti najdete [v článku Použití PolyBase k načtení dat do datového skladu Azure SQL.](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) Návod k případu použití najdete v [tématu načtení 1 TB do Azure SQL Data Warehouse do 15 minut s Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Úložiště dat založená na souborech
*(Zahrnuje úložiště objektů Blob, úložiště Data Lake Store, Amazon S3, místní souborové systémy a místní HDFS)*

* **Průměrná velikost souboru a počet souborů**: Aktivita kopírování přenáší data po jednom souboru. Se stejným množstvím dat, které mají být přesunuty, je celková propustnost nižší, pokud se data skládají z mnoha malých souborů, nikoli z několika velkých souborů kvůli fázi zaváděcí fáze pro každý soubor. Proto, pokud je to možné, kombinovat malé soubory do větších souborů získat vyšší propustnost.
* **Formát a komprese souborů**: Další způsoby, jak zlepšit výkon, naleznete v [části Důležité informace o serializaci a deserializaci](#considerations-for-serialization-and-deserialization) a [Důležité informace pro oddíly komprese.](#considerations-for-compression)
* Scénář **místního systému souborů,** ve kterém je **vyžadována brána pro správu dat,** najdete v části [Důležité informace pro bránu pro správu dat.](#considerations-for-data-management-gateway)

### <a name="relational-data-stores"></a>Relační úložiště dat
*(Zahrnuje databázi SQL; Datový sklad SQL; Amazon Redshift; Databáze serveru SQL Server. databáze Oracle, MySQL, DB2, Teradata, Sybase a PostgreSQL atd.)*

* **Datový vzor**: Schéma tabulky ovlivňuje propustnost kopírování. Velká velikost řádku poskytuje lepší výkon než malá velikost řádku, chcete-li zkopírovat stejné množství dat. Důvodem je, že databáze může efektivněji načíst méně dávek dat, které obsahují méně řádků.
* **Dotaz nebo uložená procedura**: Optimalizace logiky dotazu nebo uložené procedury zadané ve zdroji Aktivita kopírování pro efektivnější načítání dat.
* Místní **relační databáze**, jako je SQL Server a Oracle, které vyžadují použití **brány pro správu dat**, naleznete v části Důležité informace pro bránu pro správu dat.

## <a name="considerations-for-the-sink"></a>Důležité informace pro dřez
### <a name="general"></a>Obecné
Ujistěte se, že základní úložiště dat není zahlceni jinými úlohami, které jsou spuštěny na nebo proti němu.

Úložiště dat společnosti Microsoft naleznete [v tématech monitorování a ladění,](#performance-reference) která jsou specifická pro úložiště dat. Tato témata vám mohou pomoci pochopit charakteristiky výkonu úložiště dat a jak minimalizovat dobu odezvy a maximalizovat propustnost.

Pokud kopírujete data z **úložiště objektů Blob** do **datového skladu SQL**, zvažte použití **PolyBase** ke zvýšení výkonu. Podrobnosti najdete [v článku Použití PolyBase k načtení dat do datového skladu Azure SQL.](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) Návod k případu použití najdete v [tématu načtení 1 TB do Azure SQL Data Warehouse do 15 minut s Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Úložiště dat založená na souborech
*(Zahrnuje úložiště objektů Blob, úložiště Data Lake Store, Amazon S3, místní souborové systémy a místní HDFS)*

* **Chování kopírování**: Pokud zkopírujete data z jiného úložiště dat založeného na souboru, aktivita kopírování má tři možnosti prostřednictvím vlastnosti **copyBehavior.** Zachová hierarchii, sloučí hierarchii nebo sloučí soubory. Zachování nebo sloučení hierarchie má malou nebo žádnou režii výkonu, ale slučování souborů způsobí zvýšení režie výkonu.
* **Formát a komprese souborů**: Další způsoby, jak zlepšit výkon, naleznete v [části Důležité informace o serializaci a deserializaci](#considerations-for-serialization-and-deserialization) a [Důležité informace pro oddíly komprese.](#considerations-for-compression)
* **Úložiště objektů blob**: Úložiště objektů Blob v současné době podporuje jenom objekty BLOB pro optimalizovaný přenos dat a propustnost.
* Scénáře **místních systémů souborů,** které vyžadují použití **brány pro správu dat**, naleznete v části Důležité informace pro [bránu pro správu dat.](#considerations-for-data-management-gateway)

### <a name="relational-data-stores"></a>Relační úložiště dat
*(Zahrnuje databázi SQL, datový sklad SQL, databáze SERVERU SQL a databáze Oracle)*

* **Chování kopírování**: V závislosti na vlastnostech, které jste nastavili pro **sqlSink**, zkopírovat aktivitu zapisuje data do cílové databáze různými způsoby.
  * Ve výchozím nastavení služba přesunu dat používá rozhraní API pro hromadné kopírování k vkládání dat do režimu připojení, který poskytuje nejlepší výkon.
  * Pokud nakonfigurujete uloženou proceduru v jímce, databáze použije data po jednom řádku namísto jako hromadné zatížení. Výkon výrazně klesá. Pokud je vaše datová sada velká, pokud je to možné, zvažte přepnutí na vlastnost **sqlWriterCleanupScript.**
  * Pokud nakonfigurujete vlastnost **sqlWriterCleanupScript** pro každé spuštění aktivity kopírování, služba spustí skript a potom k vložení dat použijete rozhraní API pro hromadné kopírování. Chcete-li například přepsat celou tabulku nejnovějšími daty, můžete zadat skript, který nejprve odstraní všechny záznamy před hromadným načtením nových dat ze zdroje.
* **Datový vzor a velikost dávky**:
  * Schéma tabulky ovlivňuje propustnost kopírování. Chcete-li zkopírovat stejné množství dat, velká velikost řádku poskytuje lepší výkon než velikost malého řádku, protože databáze může efektivněji potvrdit méně dávek dat.
  * Aktivita kopírování vloží data do řady dávek. Můžete nastavit počet řádků v dávce pomocí **writeBatchSize** vlastnost. Pokud vaše data mají malé řádky, můžete nastavit **writeBatchSize** vlastnost s vyšší hodnotou těžit z nižší dávkové režie a vyšší propustnost. Pokud je velikost řádku dat velká, buďte opatrní při zvýšení **writeBatchSize**. Vysoká hodnota může vést k selhání kopírování způsobené přetížení databáze.
* Místní **relační databáze,** jako je SQL Server a Oracle, které vyžadují použití **brány pro správu dat**, naleznete v části Důležité informace pro [bránu pro správu dat.](#considerations-for-data-management-gateway)

### <a name="nosql-stores"></a>NoSQL obchody
*(Zahrnuje úložiště tabulek a Azure Cosmos DB)*

* Pro **ukládání stolů**:
  * **Oddíl**: Zápis dat do prokládání oddílů výrazně snižuje výkon. Seřaďte zdrojová data podle klíče oddílu tak, aby byla data efektivně vložena do jednoho oddílu za druhým, nebo upravte logiku pro zápis dat do jednoho oddílu.
* Pro **Azure Cosmos DB**:
  * **Velikost dávky**: Vlastnost **writeBatchSize** nastaví počet paralelních požadavků na službu Azure Cosmos DB k vytvoření dokumentů. Můžete očekávat lepší výkon při zvýšení **writeBatchSize** protože další paralelní požadavky jsou odesílány do Azure Cosmos DB. Však pozor na omezení při zápisu do Azure Cosmos DB (chybová zpráva je "Míra požadavků je velký"). Různé faktory mohou způsobit omezení, včetně velikosti dokumentu, počet podmínek v dokumentech a zásady indexování cílové kolekce. Chcete-li dosáhnout vyšší propustnost kopírování, zvažte použití lepší kolekce, například S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Důležité informace pro serializaci a deserializaci
Serializace a deserializace může dojít, pokud je sada vstupních dat nebo výstupní sada dat soubor. Viz [Podporované formáty souborů a komprese](data-factory-supported-file-and-compression-formats.md) s podrobnostmi o podporovaných formátech souborů podle aktivity kopírování.

**Chování kopírování**:

* Kopírování souborů mezi úložišti dat založených na souborech:
  * Pokud mají sady vstupních i výstupních dat stejné nebo žádné nastavení formátu souboru, spustí služba přesunu dat binární kopii bez serializace nebo deserializace. Zobrazí se vyšší propustnost ve srovnání se scénářem, ve kterém se nastavení formátu zdrojového souboru a jímky liší od sebe navzájem.
  * Pokud jsou sady vstupních i výstupních dat v textovém formátu a pouze typ kódování se liší, služba přesunu dat provádí pouze převod kódování. Neprovádí žádnou serializaci a deserializaci, což způsobuje určitou režii výkonu ve srovnání s binární kopií.
  * Pokud sady vstupních i výstupních dat mají různé formáty souborů nebo různé konfigurace, jako jsou oddělovače, služba přesunu dat deserializuje zdrojová data pro streamování, transformaci a jejich serializaci do výstupního formátu, který jste uvedli. Tato operace má za následek mnohem významnější režii výkonu ve srovnání s jinými scénáři.
* Při kopírování souborů do nebo z úložiště dat, které není založeno na souborech (například z úložiště založeného na souborech do relačního úložiště), je vyžadován krok serializace nebo deserializace. Tento krok má za následek významné režie výkonu.

**Formát souboru**: Formát souboru, který zvolíte, může ovlivnit výkon kopírování. Například Avro je kompaktní binární formát, který ukládá metadata s daty. Má širokou podporu v ekosystému Hadoop pro zpracování a dotazování. Avro je však dražší pro serializaci a deserializaci, což má za následek nižší propustnost kopírování ve srovnání s textovým formátem. Vyberte si formát souboru v celém toku zpracování holisticky. Začněte s tím, v jaké formě jsou data uložena, ukládají se zdrojová data nebo mají být extrahována z externích systémů; nejlepší formát pro ukládání, analytické zpracování a dotazování; a v jakém formátu by měla být data exportována do datových tržišť pro nástroje pro vytváření sestav a vizualizaci. Někdy formát souboru, který je neoptimální pro čtení a zápis výkonu může být dobrou volbou, když vezmete v úvahu celkový analytický proces.

## <a name="considerations-for-compression"></a>Důležité informace pro kompresi
Pokud je vstupní nebo výstupní datová sada souborem, můžete nastavit aktivitu kopírování tak, aby prováděla kompresi nebo dekompresi při zápisu dat do cíle. Zvolíte-li kompresi, provedete kompromis mezi vstupem a výstupem (I/O) a procesorem. Komprese dat stojí navíc ve výpočetních prostředcích. Ale na oplátku snižuje síťové vstupně-va a úložiště. V závislosti na vašich datech se může zobrazit zvýšení celkové propustnosti kopírování.

**Kodek**: Aktivita kopírování podporuje typy komprese gzip, bzip2 a Deflate. Azure HDInsight můžou využívat všechny tři typy ke zpracování. Každý kompresní kodek má své výhody. Například bzip2 má nejnižší propustnost kopírování, ale získáte nejlepší výkon dotazu Hive s bzip2, protože jej můžete rozdělit pro zpracování. Gzip je nejvyváženější volbou a používá se nejčastěji. Vyberte kodek, který nejlépe vyhovuje vašemu scénáři od konce.

**Úroveň**: Můžete si vybrat ze dvou možností pro každý kompresní kodek: nejrychlejší komprimované a optimálně komprimované. Nejrychlejší komprimovaná možnost komprimuje data co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován. Optimálně komprimovaná možnost stráví více času na kompresi a poskytuje minimální množství dat. Můžete otestovat obě možnosti a zjistit, který poskytuje lepší celkový výkon ve vašem případě.

**Zvažování**: Chcete-li zkopírovat velké množství dat mezi místním úložištěm a cloudem, zvažte použití dočasného úložiště objektů blob s kompresí. Použití dočasného úložiště je užitečné, když šířka pásma vaší podnikové sítě a služeb Azure je limitujícím faktorem a chcete, aby vstupní sada dat a výstupní sada dat byly v nekomprimované podobě. Přesněji řečeno, můžete rozdělit jednu aktivitu kopírování na dvě aktivity kopírování. První aktivita kopírování se zkopíruje ze zdroje do dočasného nebo pracovního objektu blob v komprimované podobě. Druhá aktivita kopírování zkopíruje komprimovaná data z pracovní a potom dekomprimuje, zatímco zapíše do jímky.

## <a name="considerations-for-column-mapping"></a>Důležité informace pro mapování sloupců
Vlastnost **columnMappings** v části Aktivita kopírování můžete namapovat všechny nebo podmnožinu vstupních sloupců na výstupní sloupce. Poté, co služba přesunu dat přečte data ze zdroje, musí provést mapování sloupců na data před zápisem dat do jímky. Toto další zpracování snižuje propustnost kopírování.

Pokud je vaše zdrojové úložiště dat dotazovatelné, například pokud se jedná o relační úložiště, jako je SQL Database nebo SQL Server, nebo pokud se jedná o úložiště NoSQL, jako je table storage nebo Azure Cosmos DB, zvažte odeslání logiky filtrování sloupců a změn pořadí na vlastnost **dotazu** namísto použití mapování sloupců. Tímto způsobem dojde k projekci, zatímco služba přesunu dat čte data ze zdrojového úložiště dat, kde je mnohem efektivnější.

## <a name="other-considerations"></a>Další aspekty
Pokud je velikost dat, která chcete kopírovat, velká, můžete upravit obchodní logiku pro další rozdělení dat pomocí mechanismu krájení v datové továrně. Potom naplánujte, aby se aktivita kopírování spouštěla častěji, aby se zmenšila velikost dat pro každé spuštění aktivity kopírování.

Buďte opatrní ohledně počtu sad dat a kopírování aktivit, které vyžadují Data Factory pro konektor ke stejnému úložišti dat ve stejnou dobu. Mnoho souběžných úloh kopírování může omezit úložiště dat a vést ke snížení výkonu, kopírování interních opakování úlohy a v některých případech selhání spuštění.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Ukázkový scénář: Kopírování z místního úložiště SQL Server do úložiště objektů Blob
**Scénář**: Kanál je vytvořen ke kopírování dat z místního úložiště SQL Server do úložiště objektů Blob ve formátu CSV. Chcete-li zpracovat kopírování, soubory CSV by měly být komprimovány do formátu bzip2.

**Testování a analýza**: Propustnost aktivity kopírování je menší než 2 Mb/s, což je mnohem pomalejší než benchmark výkonu.

**Analýza a ladění výkonu**: Chcete-li vyřešit problém s výkonem, podívejme se na to, jak jsou data zpracovávána a přesouvána.

1. **Číst data**: Brána otevře připojení k serveru SQL Server a odešle dotaz. SQL Server reaguje odesláním datového proudu do brány prostřednictvím intranetu.
2. **Serializovat a komprimovat data**: Brána serializuje datový proud do formátu CSV a komprimuje data do datového proudu bzip2.
3. **Zapsat data**: Gateway nahraje datový proud bzip2 do úložiště objektů Blob přes internet.

Jak můžete vidět, data jsou zpracovávána a přesouvána postupně: SQL Server > LAN > Gateway > wan > úložiště objektů blob. **Celkový výkon je brána minimální propustnost přes kanál**.

![Tok dat](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Problémový bod výkonu může způsobit jeden nebo více následujících faktorů:

* **Zdroj**: SQL Server sám má nízkou propustnost z důvodu těžkých zatížení.
* **Brána pro správu dat**:
  * **LAN**: Brána je umístěna daleko od počítače SQL Server a má připojení s malou šířkou pásma.
  * **Brána**: Brána dosáhla omezení zatížení k provedení následujících operací:
    * **Serializace**: Serializace datového proudu do formátu CSV má pomalou propustnost.
    * Komprese : **Zvolili**jste pomalý kompresní kodek (například bzip2, což je 2,8 MB/s s jádrem i7).
  * **WAN**: Šířka pásma mezi podnikovou sítí a službami Azure je nízká (například T1 = 1 544 kbps; T2 = 6 312 kbps).
* **Jímka**: Úložiště objektů blob má nízkou propustnost. (Tento scénář je nepravděpodobné, protože jeho SLA zaručuje minimálně 60 MB/s.)

V tomto případě komprese dat bzip2 může zpomalovat celý kanál. Přepnutí na kompresní kodek gzip může zmírnit toto kritické místo.

## <a name="sample-scenarios-use-parallel-copy"></a>Ukázkové scénáře: Použití paralelní kopie
**Scénář I:** Zkopírujte 1 000 souborů o velikosti 1 MB z místního systému souborů do úložiště objektů Blob.

**Analýza a optimalizace výkonu**: Pokud jste nainstalovali bránu do čtyřjádrového počítače, používá Data Factory 16 paralelních kopií k současnému přesunu souborů ze systému souborů do úložiště objektů Blob. Toto paralelní spuštění by mělo mít za následek vysokou propustnost. Můžete také explicitně zadat počet paralelních kopií. Při kopírování mnoha malých souborů, paralelní kopie výrazně pomoci propustnost pomocí prostředků efektivněji.

![Scénář 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scénář II**: Zkopírujte 20 objektů BLOB po 500 MB z úložiště objektů Blob do služby Data Lake Store Analytics a pak vyladěte výkon.

**Analýza a optimalizace výkonu**: V tomto scénáři Data Factory zkopíruje data z úložiště objektů Blob do úložiště Data Lake Store pomocí jedné kopie **(parallelCopies** nastavit na 1) a jednotky pro přesun dat s jedním cloudem. Propustnost, kterou pozorujete, bude blízká propustku popsanému v [části reference výkonu](#performance-reference).

![Scénář 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scénář III**: Velikost jednotlivých souborů je větší než desítky mbů a celkový objem je velký.

**Analýza a soustružení výkonu**: Zvýšení **paralelníkopie** nemá za následek lepší výkon kopírování z důvodu omezení prostředků dmu s jedním cloudem. Místo toho byste měli zadat více cloudových dmu, abyste získali další prostředky k provedení přesunu dat. Nezadávejte hodnotu pro **vlastnost parallelCopies.** Data Factory zpracovává paralelismus za vás. V tomto případě pokud nastavíte **cloudDataMovementUnits** na 4, dojde k propustnosti asi čtyřikrát.

![Scénář 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Odkaz
Zde jsou odkazy na monitorování a ladění výkonu pro některá podporovaná úložiště dat:

* Úložiště objektů blob Azure: [Škálovatelnost a cíle výkonu pro úložiště objektů blob](../../storage/blobs/scalability-targets.md) a [kontrolní seznam výkonu a škálovatelnosti pro úložiště objektů blob](../../storage/blobs/storage-performance-checklist.md).
* Azure Table storage: [Škálovatelnost a výkonnostní cíle pro úložiště tabulek](../../storage/tables/scalability-targets.md) a [kontrolní seznam výkonu a škálovatelnosti pro table storage](../../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: Můžete [sledovat výkon](../../sql-database/sql-database-single-database-monitor.md) a zkontrolovat procento jednotky transakce databáze (DTU)
* Azure SQL Data Warehouse: Jeho schopnost se měří v jednotkách datového skladu (DWU); viz [Správa výpočetního výkonu v Azure SQL Data Warehouse (přehled)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [Úrovně výkonu v Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* Místní SQL Server: [Sledování a ladění výkonu](https://msdn.microsoft.com/library/ms189081.aspx)
* Místní souborový server: [Optimalizace výkonu pro souborové servery](https://msdn.microsoft.com/library/dn567661.aspx)
