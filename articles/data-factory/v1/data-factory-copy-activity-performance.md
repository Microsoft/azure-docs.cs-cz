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
ms.openlocfilehash: 5910b94dba03f105197a94cf1ea1805f45249f3f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451348"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Průvodce laděním a výkonem aktivity kopírování

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-copy-activity-performance.md)
> * [Verze 2 (aktuální verze)](../copy-activity-performance.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [o výkonu a Průvodci optimalizací aktivity kopírování pro Data Factory](../copy-activity-performance.md).

Aktivita kopírování Azure Data Factory poskytuje prvotřídní řešení pro bezpečné, spolehlivé a vysoce výkonné datové zatížení. Umožňuje kopírovat desítky terabajtů dat každý den napříč širokou škálou cloudových a místních úložišť dat. Neuvěřitelně – rychlý výkon načítání dat je klíč, který zajistí, že se můžete soustředit na základní problém s velkým objemem dat: vytváření pokročilých analytických řešení a získávání podrobných přehledů ze všech těchto dat.

Azure poskytuje sadu řešení pro datové úložiště a datové sklady na podnikové úrovni a aktivita kopírování nabízí vysoce optimalizované prostředí pro načítání dat, které se dá snadno nakonfigurovat a nastavit. Jenom s jednou aktivitou kopírování můžete dosáhnout těchto akcí:

* Načítání dat do služby **Azure synapse Analytics** při **1,2 GB/** s. Návod s případem použití najdete v tématu [načtení 1 TB do služby Azure synapse Analytics za 15 minut s Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Načtení dat do **úložiště objektů BLOB v Azure** v **1,0 GB/** s
* Načtení dat do **Azure Data Lake Store** při **1,0 GB/** s

Tento článek popisuje:

* [Referenční počty výkonu](#performance-reference) pro podporovaná úložiště dat zdroje a jímky, která vám pomůžou plánovat projekt;
* Funkce, které mohou zvýšit propustnost kopírování v různých scénářích, včetně [jednotek přesunu dat cloudu](#cloud-data-movement-units), [paralelní kopírování](#parallel-copy)a [připraveného kopírování](#staged-copy);
* [Pokyny k ladění výkonu](#performance-tuning-steps) , jak vyladit výkon a klíčové faktory, které mohou ovlivnit výkon kopírování.

> [!NOTE]
> Pokud obecně neznáte aktivitu kopírování, přečtěte si článek [přesunutí dat pomocí aktivity kopírování](data-factory-data-movement-activities.md) .
>

## <a name="performance-reference"></a>Referenční informace o výkonu

Jako referenční informace zobrazuje následující tabulka číslo propustnosti kopírování v MB/s pro danou dvojici zdroje a jímky na základě interního testování. Pro porovnání také ukazuje, jakým způsobem může pomáhat s výkonem kopírování různých nastavení [jednotek pro pohyb dat cloudu](#cloud-data-movement-units) nebo [škálovatelnost Správa dat brány](data-factory-data-management-gateway-high-availability-scalability.md) (více uzlů brány).

![Matice výkonu](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>V Azure Data Factory verze 1 jsou minimální jednotky přesunu cloudových dat pro kopii Cloud-cloudu dva. Pokud tento parametr nezadáte, přečtěte si téma výchozí jednotky přesunu dat používané v [jednotkách přesunu dat v cloudu](#cloud-data-movement-units).

**Ukazuje na poznámku:**
* Propustnost se počítá pomocí následujícího vzorce: [velikost čtených dat ze zdroje]/[doba trvání spuštění aktivity kopírování].
* Referenční údaje o výkonu v tabulce byly měřeny pomocí [TPC-H](http://www.tpc.org/tpch/) datové sady v jednom spuštění aktivity kopírování.
* Zdroj a jímka v Azure Data Stores jsou ve stejné oblasti Azure.
* Pro hybridní kopírování mezi místními a cloudovým úložištěm dat byl každý uzel brány spuštěný na počítači, který byl oddělený od místního úložiště dat s níže uvedeným specifikací. Když v bráně běžela jedna aktivita, operace kopírování spotřebuje jenom malou část procesoru testovacího počítače, paměti nebo šířky pásma sítě. Přečtěte si další informace [o Správa dat bráně](#considerations-for-data-management-gateway).
    <table>
    <tr>
        <td>Procesor</td>
        <td>32 jader 2,20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Paměť</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Síť</td>
        <td>Internetové rozhraní: 10 GB/s; intranetové rozhraní: 40 GB/s</td>
    </tr>
    </table>


> [!TIP]
> Vyšší propustnost můžete dosáhnout využitím více jednotek přesunu dat (DMUs) než výchozí maximální DMUs, což je 32 pro spuštění aktivity kopírování z cloudu do cloudu. Například s 100 DMUs můžete dosáhnout kopírování dat z objektu blob Azure do Azure Data Lake Store při **1,0 GB/** s. Podrobnosti o této funkci a podporovaném scénáři najdete v části [jednotky pro přesun dat v cloudu](#cloud-data-movement-units) . Kontaktujte [podporu Azure](https://azure.microsoft.com/support/) a požádejte o další DMUs.

## <a name="parallel-copy"></a>Paralelní kopírování
Můžete číst data ze zdroje nebo zapisovat data do cíle **paralelně v rámci spuštění aktivity kopírování**. Tato funkce zlepšuje propustnost operace kopírování a zkracuje dobu potřebnou k přesunu dat.

Toto nastavení se liší od vlastnosti **souběžnosti** v definici aktivity. Vlastnost **Concurrency** určuje, kolik **souběžných aktivit kopírování se spustí** pro zpracování dat z různých oken aktivit (od 1 do 2 dop, 2 am a 3 dop. 3 dop. a tak dále). Tato funkce je užitečná při provádění historických zátěží. Možnost paralelní kopírování se vztahuje na **spuštění jedné aktivity**.

Pojďme se podívat na vzorový scénář. V následujícím příkladu je třeba zpracovat více řezů z minulosti. Data Factory spustí instanci aktivity kopírování (spuštění aktivity) pro každý řez:

* Datový řez z prvního okna aktivity (1d až 2 dop) = => spuštění aktivit 1
* Datový řez z druhého okna aktivity (2 až 3 dop.) = => spuštění aktivit 2
* Datový řez z druhého okna aktivity (od 3 do 4 dop) = => spuštění aktivit 3

A tak dále.

V tomto příkladu, když je hodnota **souběžnosti** nastavená na 2, **spuštění aktivit 1** a **aktivita 2** kopírování dat ze dvou oken aktivit **současně** za účelem zlepšení výkonu přesunu dat. Pokud je však ke spuštění aktivity 1 přidruženo více souborů, služba přesunu dat kopíruje soubory ze zdrojového do cílového umístění v jednom okamžiku.

### <a name="cloud-data-movement-units"></a>Jednotky pro pohyb dat v cloudu
**Jednotka pro pohyb dat v cloudu (DMU)** je míra, která představuje výkon jedné jednotky v Data Factory (kombinace procesoru, paměti a přidělení síťových prostředků). DMU se vztahuje na operace kopírování z cloudu do cloudu, ale ne v hybridní kopii.

**Minimální jednotky přesunu dat cloudu, které umožňují spuštění aktivit kopírování, jsou dvě.** Pokud není zadaný, v následující tabulce jsou uvedeny výchozí DMUs používané v různých scénářích kopírování:

| Scénář kopírování | Výchozí DMUs určuje služba |
|:--- |:--- |
| Kopírování dat mezi úložišti na základě souborů | Mezi 4 a 16 v závislosti na počtu a velikosti souborů. |
| Všechny ostatní scénáře kopírování | 4 |

Chcete-li přepsat toto výchozí nastavení, zadejte hodnotu vlastnosti **cloudDataMovementUnits** následujícím způsobem. **Povolené hodnoty** pro vlastnost **cloudDataMovementUnits** jsou 2, 4, 8, 16, 32. **Skutečný počet cloudových DMUs** , které operace kopírování používá v době běhu, se rovná nebo je menší než nakonfigurovaná hodnota v závislosti na datovém vzoru. Informace o úrovni zvýšení výkonu, které můžete získat, když nakonfigurujete více jednotek pro konkrétní zdroj kopírování a jímku, najdete v části [referenční informace o výkonu](#performance-reference).

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
> Pokud potřebujete více cloudových DMUs pro vyšší propustnost, obraťte se na [podporu Azure](https://azure.microsoft.com/support/). Nastavení 8 a výše teď funguje jenom v případě, že **zkopírujete víc souborů z BLOB Storage/Data Lake Store/Amazon S3/Cloud FTP/cloudu SFTP do BLOB Storage/Data Lake Store/Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies
Pomocí vlastnosti **parallelCopies** můžete označit paralelismus, které má aktivita kopírování použít. Tuto vlastnost si můžete představit jako maximální počet vláken v rámci aktivity kopírování, která se dá číst ze zdroje, nebo zapisovat do úložiště dat jímky paralelně.

Pro každou spuštění aktivity kopírování Data Factory určuje počet paralelních kopií, které se mají použít ke kopírování dat ze zdrojového úložiště dat a do cílového úložiště dat. Výchozí počet paralelních kopií, které používá, závisí na typu zdroje a jímky, které používáte.

| Zdroj a jímka | Výchozí počet paralelně kopírovaných kopií stanovený službou |
| --- | --- |
| Kopírování dat mezi úložišti založenými na souborech (úložiště objektů BLOB; Data Lake Store; Amazon S3; místní systém souborů; místní HDFS |Mezi 1 a 32. Závisí na velikosti souborů a počtu jednotek pohybu cloudových dat (DMUs), které se používají ke kopírování dat mezi dvěma úložišti cloudových dat, nebo fyzické konfiguraci počítače brány používaného pro hybridní kopírování (kopírování dat do nebo z místního úložiště dat). |
| Kopírování dat z **libovolného zdrojového úložiště dat do úložiště tabulek Azure** |4 |
| Všechny ostatní páry zdrojů a jímky |1 |

Výchozí chování by mělo být obvykle vám poskytne nejlepší propustnost. Pro řízení zátěže na počítačích, které hostují vaše úložiště dat nebo pro optimalizaci výkonu kopírování, se můžete rozhodnout přepsat výchozí hodnotu a zadat hodnotu pro vlastnost **parallelCopies** . Hodnota musí být v rozmezí od 1 do 32 (včetně). V době běhu používá aktivita kopírování pro nejlepší výkon hodnotu, která je menší nebo rovna hodnotě, kterou jste nastavili.

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
Ukazuje na poznámku:

* Při kopírování dat mezi úložišti založeném na souborech Určuje **parallelCopies** paralelismus na úrovni souboru. K zablokování v rámci jednoho souboru dojde automaticky a transparentně a je navržena tak, aby používala nejvhodnější velikost bloku dat pro daný typ zdrojového úložiště dat, aby se data načetla paralelně a kolmo k parallelCopies. Skutečný počet paralelních kopií, které služba přesunu dat používá pro operaci kopírování v době běhu, není vyšší než počet souborů, které máte. Pokud je chování kopírování **mergeFile**, aktivita kopírování nemůže využít paralelismus na úrovni souborů.
* Když zadáte hodnotu vlastnosti **parallelCopies** , zvažte zvýšení zátěže v úložišti dat zdroje a jímky a na bránu, pokud se jedná o hybridní kopii. K tomu dochází hlavně v případě, že máte více aktivit nebo souběžných spuštění stejných aktivit, které se spouštějí ve stejném úložišti dat. Pokud si všimnete, že úložiště dat nebo brána jsou zahlcené zatížením, snižte hodnotu **parallelCopies** k uvolnění zátěže.
* Když kopírujete data z úložišť, která nejsou založená na souborech pro úložiště, která jsou založená na souborech, služba přesunu dat ignoruje vlastnost **parallelCopies** . I v případě, že je zadán paralelismu, v tomto případě se nepoužije.

> [!NOTE]
> Pokud chcete používat funkci **parallelCopies** při hybridním kopírování, musíte použít bránu Správa dat verze 1,11 nebo novější.
>
>

K lepšímu používání těchto dvou vlastností a k vylepšení propustnosti přesunu dat použijte příklady případů použití. Nemusíte konfigurovat **parallelCopies** , abyste mohli využívat výchozí chování. Pokud nakonfigurujete a **parallelCopies** je příliš malá, možná nebude možné plně využít více cloudových DMUs.

### <a name="billing-impact"></a>Dopad fakturace
Je **důležité** si uvědomit, že se vám budou účtovat poplatky podle celkové doby operace kopírování. Pokud úloha kopírování použila jednu hodinu s jednou cloudovou jednotkou a teď trvá 15 minut a čtyři cloudové jednotky, bude celková faktura skoro stejná. Například použijete čtyři cloudové jednotky. První cloudová jednotka tráví 10 minut, druhý druhý, 10 minut, třetí 1, 5 minut a čtvrtou dobu 5 minut, a to vše v jedné aktivitě kopírování. Účtuje se vám celková doba kopírování (přesunu dat), což je 10 + 10 + 5 + 5 = 30 minut. Použití **parallelCopies** nemá vliv na fakturaci.

## <a name="staged-copy"></a>Připravené kopírování
Když kopírujete data ze zdrojového úložiště dat do úložiště dat jímky, můžete použít úložiště objektů BLOB jako dočasné pracovní úložiště. Příprava je užitečná hlavně v následujících případech:

1. Chcete ingestovat **data z různých úložišť dat do služby Azure synapse Analytics prostřednictvím základu**. Azure synapse Analytics používá základ jako mechanismus vysoké propustnosti k načtení velkého množství dat do Azure synapse Analytics. Zdrojová data ale musí být v úložišti objektů BLOB a musí splňovat další kritéria. Při načítání dat z jiného úložiště dat než do úložiště objektů blob můžete aktivovat kopírování dat prostřednictvím dočasného pracovního úložiště objektů BLOB. V takovém případě Data Factory provádí požadované transformace dat, aby se zajistilo, že splňuje požadavky základny. Pak použije základnu k načtení dat do služby Azure synapse Analytics. Další podrobnosti najdete v tématu [použití základny k načtení dat do služby Azure synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics). Návod s případem použití najdete v tématu [načtení 1 TB do služby Azure synapse Analytics za 15 minut s Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Někdy nějakou dobu trvá, než se provedou hybridní přesun dat (tj. kopírování mezi místním úložištěm dat a cloudovým úložištěm dat) prostřednictvím pomalého síťového připojení**. Aby bylo možné zvýšit výkon, můžete data místně zkomprimovat, aby bylo přesouvání dat do pracovního úložiště dat v cloudu trvat kratší dobu. Pak můžete data z přípravného úložiště dekomprimovat předtím, než je načtete do cílového úložiště dat.
3. **Z důvodu podnikových zásad IT nechcete v bráně firewall otevírat jiné porty než port 80 a port 443**. Když například kopírujete data z místního úložiště dat do jímky Azure SQL Database nebo do jímky služby Azure synapse Analytics, musíte aktivovat odchozí komunikaci TCP na portu 1433 pro bránu Windows Firewall i firemní bránu firewall. V tomto scénáři můžete bránu využít k prvnímu kopírování dat do pracovní instance úložiště objektů BLOB přes protokol HTTP nebo HTTPS na portu 443. Pak načtěte data do SQL Database nebo Azure synapse Analytics z přípravy úložiště objektů BLOB. V tomto toku nemusíte povolit port 1433.

### <a name="how-staged-copy-works"></a>Jak funguje dvoufázové kopírování
Když aktivujete pracovní funkci, nejdřív se data zkopírují ze zdrojového úložiště dat do pracovního úložiště dat (Přineste si vlastní). V dalším kroku se data zkopírují z pracovního úložiště dat do úložiště dat jímky. Data Factory pro vás automaticky spravuje tok se dvěma fázemi. Po dokončení přesunu dat Data Factory taky vyčistit dočasná data z pracovního úložiště.

V případě cloudového kopírování (úložiště dat zdrojového kódu i jímky jsou v cloudu) se brána nepoužívá. Služba Data Factory provádí operace kopírování.

![Dvoufázové kopírování: scénář cloudu](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

V případě hybridního kopírování (zdroj je místní a jímka je v cloudu) brána přesouvá data ze zdrojového úložiště dat do pracovního úložiště dat. Služba Data Factory přesouvá data z pracovního úložiště dat do úložiště dat jímky. Kopírování dat z cloudového úložiště dat do místního úložiště dat prostřednictvím přípravy je podporované i s obráceným tokem.

![Dvoufázové kopírování: hybridní scénář](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Když provedete přesun dat pomocí pracovního úložiště, můžete určit, jestli chcete data před přesunutím dat ze zdrojového úložiště dat do dočasného nebo přípravného úložiště dat zkomprimovat, a pak je dekomprimovat před přesunem dat z dočasného nebo přípravného úložiště dat do úložiště dat jímky.

V současné době nemůžete kopírovat data mezi dvěma místními úložišti dat pomocí přípravného úložiště. Očekáváme, že tato možnost bude brzy k dispozici.

### <a name="configuration"></a>Konfigurace
Nakonfigurujte nastavení **enableStaging** v aktivitě kopírování a určete, jestli se mají data v úložišti objektů BLOB připravit, než je načtete do cílového úložiště dat. Pokud nastavíte **enableStaging** na hodnotu true, určete další vlastnosti uvedené v následující tabulce. Pokud ho ještě nemáte, musíte vytvořit propojenou službu Azure Storage nebo sdílený přístupový podpis s úložištěm pro přípravu.

| Vlastnost | Popis | Výchozí hodnota | Vyžadováno |
| --- | --- | --- | --- |
| **enableStaging** |Určete, zda chcete kopírovat data prostřednictvím dočasného přípravného úložiště. |Nepravda |No |
| **linkedServiceName** |Zadejte název propojené služby [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) nebo [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) , která odkazuje na instanci úložiště, kterou používáte jako dočasné pracovní úložiště. <br/><br/> Úložiště se sdíleným přístupovým podpisem se nedá použít k načtení dat do služby Azure synapse Analytics prostřednictvím základu. Můžete ho použít ve všech ostatních scénářích. |Není k dispozici |Ano, pokud je **enableStaging** nastavené na true |
| **dílčí** |Zadejte cestu k úložišti objektů blob, kterou chcete, aby obsahovala zpracovaná data. Pokud cestu nezadáte, služba vytvoří kontejner pro ukládání dočasných dat. <br/><br/> Zadejte cestu pouze v případě, že používáte úložiště se sdíleným přístupovým podpisem, nebo pokud chcete, aby byla dočasná data v určitém umístění. |Není k dispozici |No |
| **Hodnotou EnableCompression** |Určuje, zda mají být data před zkopírováním do cíle komprimována. Toto nastavení snižuje objem přenášených dat. |Nepravda |No |

Tady je ukázková definice aktivity kopírování s vlastnostmi popsanými v předchozí tabulce:

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
Účtují se vám poplatky podle dvou kroků: doba kopírování a typ kopírování.

* Při použití přípravy během kopírování do cloudu (kopírování dat z cloudového úložiště dat do jiného cloudového úložiště dat) se vám účtuje [součet doby kopírování pro krok 1 a krok 2] x [cena za jednotku kopie v cloudu].
* Při použití přípravy během hybridní kopie (kopírování dat z místního úložiště dat do cloudového úložiště dat) se vám bude účtovat [doba trvání hybridního kopírování] x [běžná kopie jednotky ceny] + [doba kopírování v cloudu] x [cena za jednotku cloudové kopie].

## <a name="performance-tuning-steps"></a>Postup ladění výkonu
Doporučujeme, abyste provedli následující kroky, abyste mohli vyladit výkon služby Data Factory s aktivitou kopírování:

1. **Vytvořte směrný plán**. Během fáze vývoje otestujte kanál pomocí aktivity kopírování na reprezentativní ukázce dat. K omezení množství dat, se kterými pracujete, můžete použít [model průřezu](data-factory-scheduling-and-execution.md) Data Factory.

   Shromažďovat dobu provádění a výkonnostní charakteristiky pomocí aplikace pro **monitorování a správu**. Na domovské stránce Data Factory vyberte **Monitor & spravovat** . Ve stromovém zobrazení vyberte **výstupní datovou sadu**. V seznamu **okna aktivit** vyberte spuštění aktivity kopírování. **Okna aktivity** zobrazují dobu trvání aktivity kopírování a velikost kopírovaných dat. Propustnost je uvedena v **Průzkumníkovi okna aktivity**. Další informace o aplikaci najdete v tématu [monitorování a Správa kanálů Azure Data Factory pomocí aplikace pro monitorování a správu](data-factory-monitor-manage-app.md).

   ![Podrobnosti o spuštění aktivit](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Později v tomto článku můžete porovnat výkon a konfiguraci scénáře, abyste mohli kopírovat [odkaz na výkon](#performance-reference) aktivity z našich testů.
2. **Diagnostikujte a Optimalizujte výkon**. Pokud výkon, který sledujete, nesplňuje vaše očekávání, je nutné určit kritické body výkonu. Pak Optimalizujte výkon, abyste odebrali nebo snížili vliv kritických míst. Úplný popis nástroje pro diagnostiku výkonu překračuje rámec tohoto článku, ale tady jsou některé běžné požadavky:

   * Funkce výkonu:
     * [Paralelní kopírování](#parallel-copy)
     * [Jednotky pro pohyb dat v cloudu](#cloud-data-movement-units)
     * [Připravené kopírování](#staged-copy)
     * [Škálovatelnost Správa dat brány](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Brána správy dat](#considerations-for-data-management-gateway)
   * [Zdroj](#considerations-for-the-source)
   * [Jímka](#considerations-for-the-sink)
   * [Serializace a deserializace](#considerations-for-serialization-and-deserialization)
   * [Komprese](#considerations-for-compression)
   * [Mapování sloupců](#considerations-for-column-mapping)
   * [Další důležité informace](#other-considerations)
3. **Rozšiřte konfiguraci na celou datovou sadu**. Až budete spokojeni s výsledky spuštění a výkonem, můžete rozšířit aktivní období definice a kanálu, abyste pokryli celou datovou sadu.

## <a name="considerations-for-data-management-gateway"></a>Předpoklady pro Správa dat bránu
**Nastavení brány**: Doporučujeme, abyste pro hostování Správa dat brány použili vyhrazený počítač. Podívejte se na téma informace týkající se [použití Správa dat brány](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Monitorování brány a horizontální navýšení kapacity**: jedna logická brána s jedním nebo více uzly brány může obsluhovat více souběžně spuštěných aktivit kopírování. Můžete zobrazit snímek využití prostředků téměř v reálném čase (CPU, paměť, síť (v/v) atd.) na počítači brány a také počet souběžných úloh, které běží v Azure Portal, a to v tématu [monitorování brány na portálu](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Pokud máte těžkou potřebu při přesunu hybridních dat, ať už s velkým počtem souběžných spuštění aktivit kopírování nebo s velkým objemem dat, která se mají kopírovat, zvažte možnost [horizontálního navýšení kapacity nebo horizontálního navýšení](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) kapacity, aby bylo možné lépe využívat svůj prostředek, nebo zřídit více prostředků pro zajištění kopie

## <a name="considerations-for-the-source"></a>Požadavky na zdroj
### <a name="general"></a>Obecné
Ujistěte se, že základní úložiště dat není zahlcené jinými úlohami, které běží na nebo proti němu.

V případě úložišť dat Microsoftu si přečtěte témata týkající se [monitorování a ladění](#performance-reference) , která jsou specifická pro úložiště dat, a pomohou vám pochopit charakteristiky výkonu úložiště dat, minimalizovat dobu odezvy a maximalizovat propustnost.

Pokud kopírujete data z úložiště objektů blob do služby Azure synapse Analytics, zvažte použití **základny** pro zvýšení výkonu. Podrobnosti najdete v tématu [použití základu k načtení dat do služby Azure synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) . Návod s případem použití najdete v tématu [načtení 1 TB do služby Azure synapse Analytics za 15 minut s Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Úložiště dat na základě souborů
*(Zahrnuje úložiště objektů blob, Data Lake Store, Amazon S3, místní souborové systémy a místní HDFS)*

* **Průměrná velikost souboru a počet souborů**: aktivita kopírování přenáší data v jednom souboru. U stejného množství dat, která se mají přesunout, je celková propustnost nižší, pokud se data skládají z mnoha malých souborů namísto několika velkých souborů z důvodu fáze Bootstrap pro každý soubor. Proto pokud je to možné, zkombinujte malé soubory do větších souborů, abyste získali vyšší propustnost.
* **Formát souboru a komprese**: Další způsoby, jak zvýšit výkon, naleznete v části [požadavky na serializaci a deserializaci](#considerations-for-serialization-and-deserialization) a [požadavky pro kompresní](#considerations-for-compression) oddíly.
* Pro **místní scénář souborového systému** , ve kterém se **Správa dat brána** vyžaduje, si přečtěte část [požadavky na Správa dat Gateway](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Relační úložiště dat
*(Zahrnuje SQL Database; Azure synapse Analytics; Amazon RedShift; SQL Server databází; a databáze Oracle, MySQL, DB2, Teradata, Sybase a PostgreSQL atd.)*

* **Datový vzor**: vaše schéma tabulky ovlivňuje propustnost kopírování. Velikost velkého řádku nabízí lepší výkon než velikost malého řádku, aby bylo možné zkopírovat stejné množství dat. Důvodem je, že databáze může efektivněji načíst méně dávkových dat, která obsahují méně řádků.
* **Dotaz nebo uložená procedura**: Optimalizujte logiku dotazu nebo uložené procedury, kterou zadáte ve zdroji aktivity kopírování, aby se data načetla efektivněji.
* U **místních relačních databází**, jako jsou SQL Server a Oracle, které vyžadují použití **brány Správa dat**, si přečtěte část požadavky na Správa dat Gateway.

## <a name="considerations-for-the-sink"></a>Předpoklady pro jímku
### <a name="general"></a>Obecné
Ujistěte se, že základní úložiště dat není zahlcené jinými úlohami, které běží na nebo proti němu.

V případě úložišť dat Microsoft najdete témata týkající se [monitorování a ladění](#performance-reference) , která jsou specifická pro úložiště dat. Tato témata vám pomůžou pochopit charakteristiky výkonu úložiště dat a minimalizovat dobu odezvy a maximalizovat propustnost.

Pokud kopírujete data z **úložiště objektů BLOB** do služby **Azure synapse Analytics**, zvažte použití **základny** pro zvýšení výkonu. Podrobnosti najdete v tématu [použití základu k načtení dat do služby Azure synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) . Návod s případem použití najdete v tématu [načtení 1 TB do služby Azure synapse Analytics za 15 minut s Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Úložiště dat na základě souborů
*(Zahrnuje úložiště objektů blob, Data Lake Store, Amazon S3, místní souborové systémy a místní HDFS)*

* **Chování při kopírování**: Pokud kopírujete data z jiného souborového úložiště dat, aktivita kopírování má tři možnosti prostřednictvím vlastnosti **copyBehavior** . Zachovává hierarchii, sloučení hierarchie nebo sloučení souborů. Buď se zachováním nebo plochou vyrovnávání hierarchie dochází pouze ke zvýšení výkonu, ale sloučení souborů způsobí zvýšení režie výkonu.
* **Formát a komprese souborů**: Další způsoby, jak zvýšit výkon, najdete v částech [požadavky na serializaci a deserializaci](#considerations-for-serialization-and-deserialization) a [předpoklady pro komprimaci](#considerations-for-compression) .
* **Úložiště objektů BLOB**: úložiště objektů BLOB v současné době podporuje pro optimalizaci přenosu a propustnosti dat pouze objekty blob bloku.
* V případě scénářů pro **místní souborové systémy** , které vyžadují použití **brány Správa dat**, přečtěte si část [požadavky na Správa dat bránu](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Relační úložiště dat
*(Zahrnuje SQL Database, analýzy Azure synapse, databáze SQL Server a databáze Oracle)*

* **Chování při kopírování**: v závislosti na vlastnostech, které jste nastavili pro **sqlSink**, aktivita kopírování zapisuje data do cílové databáze různými způsoby.
  * Ve výchozím nastavení používá služba přesunu dat rozhraní API hromadného kopírování k vkládání dat v režimu připojení, který poskytuje nejlepší výkon.
  * Pokud nakonfigurujete uloženou proceduru v jímky, databáze použije data v jednom řádku, nikoli jako hromadné zatížení. Výkon se výrazně sníží. Pokud je vaše datová sada velká (Pokud je k dispozici), zvažte přechod na použití vlastnosti **sqlWriterCleanupScript** .
  * Pokud nakonfigurujete vlastnost **sqlWriterCleanupScript** pro každé spuštění aktivity kopírování, služba spustí skript a potom k vložení dat použijeme rozhraní API pro hromadné kopírování. Pokud například chcete přepsat celou tabulku nejnovějšími daty, můžete před hromadnou načtením nových dat ze zdroje zadat skript, který nejprve odstraní všechny záznamy.
* **Velikost datového vzoru a dávky**:
  * Vaše schéma tabulky ovlivňuje propustnost kopírování. Chcete-li zkopírovat stejné množství dat, velikost velkého řádku vám poskytne lepší výkon než velikost malého řádku, protože databáze může efektivněji potvrzovat méně dávkám dat.
  * Aktivita kopírování vloží data do řady dávek. Počet řádků v dávce můžete nastavit pomocí vlastnosti **writeBatchSize** . Pokud vaše data obsahují malé řádky, můžete nastavit vlastnost **writeBatchSize** s vyšší hodnotou, abyste využili nižší nároky na dávku a vyšší propustnost. Pokud je velikost řádku dat velká, buďte při zvýšení **writeBatchSize** opatrní. Vysoká hodnota může vést k selhání kopírování způsobenému přetížením databáze.
* U **místních relačních databází** , jako jsou SQL Server a Oracle, které vyžadují použití **brány Správa dat**, si přečtěte část [požadavky na Správa dat Gateway](#considerations-for-data-management-gateway) .

### <a name="nosql-stores"></a>NoSQL obchody
*(Zahrnuje úložiště tabulek a Azure Cosmos DB)*

* Pro **úložiště tabulek**:
  * **Oddíl**: zápis dat do prokládaných oddílů výrazně snižuje výkon. Seřaďte zdrojová data podle klíče oddílu, aby byla data do jednoho oddílu po druhém vložena, nebo upravte logiku tak, aby data zapisovala do jednoho oddílu.
* Pro **Azure Cosmos DB**:
  * **Velikost dávky**: vlastnost **writeBatchSize** nastaví počet paralelních požadavků na službu Azure Cosmos DB k vytváření dokumentů. Při zvýšení **writeBatchSize** můžete očekávat lepší výkon, protože se do Azure Cosmos DB odesílají další paralelní požadavky. Sledujte ale omezení při psaní do Azure Cosmos DB (chybová zpráva je "frekvence požadavků je velká"). Různé faktory můžou způsobit omezování, včetně velikosti dokumentu, počtu podmínek v dokumentech a zásad indexování cílových kolekcí. Abyste dosáhli vyšší propustnosti kopírování, zvažte použití lepší kolekce, například S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Předpoklady pro serializaci a deserializaci
Serializace a deserializace může nastat, pokud je vstupní datová sada nebo výstupní datová sada soubor. Viz [podporované formáty souborů a komprese](data-factory-supported-file-and-compression-formats.md) s podrobnostmi o podporovaných formátech souborů podle aktivity kopírování.

**Chování při kopírování**:

* Kopírování souborů mezi úložišti dat založených na souborech:
  * Pokud vstupní a výstupní sady dat mají stejnou hodnotu nebo nastavení formátu souboru, služba přesunu dat provede binární kopii bez jakékoli serializace nebo deserializace. Ve srovnání s scénářem se zobrazí vyšší propustnost, při které se nastavení formátu zdrojových souborů a souborů jímky liší od sebe.
  * Pokud vstupní a výstupní datové sady jsou v textovém formátu a pouze typ kódování je jiný než převod, služba přesunu dat provádí převod kódování. Neprovede žádné serializace a deserializace, což způsobí, že se některé nároky na výkon v porovnání s binární kopií.
  * Pokud vstupní a výstupní sady dat mají různé formáty souborů nebo různé konfigurace, jako jsou oddělovače, služba přesunu dat deserializace zdrojová data do streamu, transformuje a pak je zavolá do formátu výstupu, který jste určili. Výsledkem této operace je mnohem výraznější režie na výkon ve srovnání s jinými scénáři.
* Pokud kopírujete soubory do nebo z úložiště dat, které není založené na souborech (například z úložiště založeného na souborech do relačního úložiště), je nutný krok serializace nebo deserializace. Výsledkem tohoto kroku je výrazné režie na výkon.

**Formát souboru**: formát souboru, který zvolíte, může ovlivnit výkon kopírování. Například Avro je kompaktní binární formát, který ukládá metadata s daty. Má širokou podporu ekosystému Hadoop pro zpracování a dotazování. Avro je však dražší pro serializaci a deserializaci, což vede k nižší propustnosti kopírování v porovnání s formátem textu. Formát souboru si vyberete v komplexní toku zpracování. Začněte s tím, na jakém formuláři jsou data uložená, zdrojová úložiště dat nebo se mají extrahovat z externích systémů. nejlepší formát pro úložiště, analytické zpracování a dotazování; a v jakém formátu mají být data exportována do tržiště dat pro vytváření sestav a nástrojů pro vizualizaci. Někdy může být formát souboru, který je v optimálním formátu pro čtení a výkon zápisu, při zvážení celého procesu analýzy dobrý volbou.

## <a name="considerations-for-compression"></a>Předpoklady pro kompresi
Když je vstupní nebo výstupní sada dat soubor, můžete nastavit aktivitu kopírování, která provádí kompresi nebo dekompresi při zápisu dat do cíle. Když zvolíte kompresi, provedete si kompromis mezi vstupem a výstupem (v/v) a CPU. Komprese nákladů na data extra ve výpočetních prostředcích. V takovém případě se ale zkracuje I síťové vstupně-výstupní operace a úložiště. V závislosti na vašich datech se může zobrazit zvýšení celkové propustnosti kopírování.

**Kodek**: aktivita kopírování podporuje typy komprese GZip, bzip2 a deflate. Azure HDInsight může pro zpracování využívat všechny tři typy. Každý Kompresní kodek má výhody. BZIP2 má například nejnižší propustnost kopírování, ale získáte nejlepší výkon dotazů na podregistr s bzip2, protože ho můžete rozdělit ke zpracování. Nástroj gzip je nejvyváženější možnost a používá se nejčastěji. Vyberte kodek, který nejlépe vyhovuje vašemu kompletnímu scénáři.

**Úroveň**: můžete si vybrat ze dvou možností pro každý Kompresní kodek: nejrychlejší komprimovaný a optimálně komprimovaný. Nejrychlejší komprimovaná možnost komprimuje data co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován. Optimálně komprimovaná možnost stráví více času komprimace a poskytuje minimální množství dat. Můžete otestovat obě možnosti, abyste viděli, která poskytuje lepší celkový výkon pro váš případ.

**Zvážení**: Pokud chcete kopírovat velké množství dat mezi místním úložištěm a cloudem, zvažte použití dočasného úložiště BLOB s kompresí. Použití dočasného úložiště je užitečné v případě, že je šířka pásma vaší podnikové sítě a služeb Azure omezující faktor a vy chcete, aby byla vstupní datová sada a výstupní sada dat v nekomprimované podobě. Konkrétně je možné rozdělit jednu aktivitu kopírování na dvě aktivity kopírování. První kopie aktivity kopírování ze zdroje do dočasného nebo přípravného objektu BLOB v komprimované podobě. Druhá aktivita kopírování kopíruje komprimovaná data z přípravy a pak je dekomprimuje během zápisu do jímky.

## <a name="considerations-for-column-mapping"></a>Pokyny pro mapování sloupců
Vlastnost **ColumnMappings** v aktivitě kopírování můžete nastavit tak, aby se namapovala vše nebo podmnožina vstupních sloupců na výstupní sloupce. Poté, co služba přesunu dat přečte data ze zdroje, musí před zápisem dat do jímky provést mapování sloupce na data. Toto dodatečné zpracování omezuje propustnost kopírování.

Pokud je zdrojové úložiště dat Queryable, například pokud se jedná o relační úložiště, jako je SQL Database nebo SQL Server, nebo pokud se jedná o NoSQL úložiště, jako je například úložiště tabulky nebo Azure Cosmos DB, zvažte vložení filtrování sloupce a změnu pořadí na vlastnost **dotazu** namísto použití mapování sloupce. Tímto způsobem se projekce objeví, zatímco služba pro přesun dat načítá data ze zdrojového úložiště dat, kde je mnohem efektivnější.

## <a name="other-considerations"></a>Další důležité informace
Pokud je velikost dat, která chcete zkopírovat, Velká, můžete upravit obchodní logiku a dále rozdělit data pomocí mechanismu řezů v Data Factory. Pak Naplánujte aktivitu kopírování tak, aby se spouštěla častěji, aby se snížila velikost dat pro každý běh aktivity kopírování.

Buďte opatrní na počet datových sad a aktivit kopírování, které vyžadují, Data Factory, aby se současně konektor do stejného úložiště dat. Mnoho souběžných úloh kopírování může omezit úložiště dat a vést ke snížení výkonu, provádění interních opakovaných pokusů úloh kopírování a v některých případech na selhání.

## <a name="sample-scenario-copy-from-a-sql-server-database-to-blob-storage"></a>Ukázkový scénář: kopírování z databáze SQL Server do úložiště objektů BLOB
**Scénář**: kanál je sestavený tak, aby kopíroval data z databáze SQL Server do úložiště objektů BLOB ve formátu CSV. Aby bylo možné úlohu kopírování urychlit, soubory CSV by se měly zkomprimovat do formátu bzip2.

**Testování a analýza**: propustnost aktivity kopírování je menší než 2 MB/s, což je mnohem pomalejší než srovnávací test výkonnosti.

**Analýza a optimalizace výkonu**: Pokud chcete vyřešit potíže s výkonem, Podívejme se na to, jak se data zpracovávají a přesunují.

1. **Čtení dat**: Brána otevře připojení k SQL Server a odešle dotaz. SQL Server odpoví odesláním datového proudu do brány prostřednictvím intranetu.
2. **Serializace a komprimace dat**: Brána serializovat datový proud do formátu CSV a komprimuje data do datového proudu bzip2.
3. **Zápis dat**: Brána nahrává datový proud bzip2 do úložiště objektů BLOB prostřednictvím Internetu.

Jak vidíte, data se zpracovávají a přesunují v sekvenčním režimu streamování: SQL Server > LAN > bráně > síti WAN > BLOB Storage. **Celkový výkon je ověřovaný minimální propustností v rámci kanálu**.

![Tok dat](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Problém s výkonem může způsobovat jeden nebo více následujících faktorů:

* **Zdroj**: SQL Server sám o sobě má nízkou propustnost z důvodu vysokého zatížení.
* **Brána Správa dat**:
  * **LAN**: Brána se nachází daleko z SQL Server počítače a má připojení s malou šířkou pásma.
  * **Brána**: Brána dosáhla svých omezení zatížení k provedení následujících operací:
    * **Serializace**: serializace datového proudu do formátu CSV má pomalou propustnost.
    * **Komprese**: zvolili jste pomalu Kompresní kodek (například bzip2, což je 2,8 MB/s Core i7).
  * **WAN**: šířka pásma mezi podnikovou sítí a službami Azure je nízká (například T1 = 1 544 KB/s). T2 = 6 312 kb/s).
* **Jímka**: úložiště objektů BLOB má nízkou propustnost. (Tento scénář je nepravděpodobný, protože smlouva SLA zaručuje minimálně 60 MB/s.)

V takovém případě může komprese dat bzip2 zpomalit celý kanál. Přepnutí do kompresního kodeku gzip může být tímto kritickým bodem velmi jednoduché.

## <a name="sample-scenarios-use-parallel-copy"></a>Ukázkové scénáře: použití paralelního kopírování
**Scénář I:** Zkopírujte soubory o velikosti 1 000 1 MB z místního systému souborů do úložiště objektů BLOB.

**Analýza a optimalizace výkonu**: Pokud jste například nainstalovali bránu na čtyřjádrový počítač, Data Factory používá 16 paralelních kopií k přesouvání souborů ze systému souborů do úložiště objektů BLOB souběžně. Toto paralelní provádění by mělo mít za následek vysokou propustnost. Můžete také explicitně zadat počet souběžných kopií. Když kopírujete mnoho malých souborů, paralelní kopie významně pomůžou snížit propustnost pomocí prostředků efektivněji.

![Scénář 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scénář II**: Zkopírujte 20 objektů BLOB o velikosti 500 MB z úložiště objektů blob do služby Data Lake Store Analytics a pak Optimalizujte výkon.

**Analýza a optimalizace výkonu**: v tomto scénáři data Factory kopírují data z úložiště objektů Blob do data Lake Store pomocí jedné kopie (**parallelCopies** set to 1) a jednotek pro přesun dat s jedním cloudem. Propustnost, kterou sledujete, se blíží k tomuto, která je popsána v [části referenční informace o výkonu](#performance-reference).

![Scénář 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scénář III**: velikost jednotlivých souborů je větší než desítka MB a celková velikost svazku je velká.

**Analýza a zvýšení výkonu**: zvyšování **parallelCopies** nemá za následek lepší výkon kopírování v důsledku omezení prostředků v rámci jedné cloudové DMU. Místo toho byste měli zadat více cloudových DMUs a získat další prostředky k přesunu dat. Nezadávejte hodnotu vlastnosti **parallelCopies** . Data Factory zpracovává paralelismus za vás. V takovém případě, pokud nastavíte **cloudDataMovementUnits** na 4, dojde k propustnosti přibližně čtyřikrát.

![Scénář 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referenční informace
Tady jsou odkazy na sledování výkonu a ladění pro některá z podporovaných úložišť dat:

* Azure Blob Storage: [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../../storage/blobs/scalability-targets.md) a [Kontrolní seznam výkonu a škálovatelnosti pro úložiště objektů BLOB](../../storage/blobs/storage-performance-checklist.md).
* Azure Table Storage: [škálovatelnost a výkonnostní cíle pro úložiště tabulek](../../storage/tables/scalability-targets.md) a pro [Kontrolní seznam výkonu a škálovatelnosti pro úložiště tabulek](../../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: můžete [monitorovat výkon](../../azure-sql/database/monitor-tune-overview.md) a kontrolovat procento transakčních jednotek databáze (DTU).
* Azure synapse Analytics: jeho schopnost se měří v jednotkách datového skladu (DWU); viz [Správa výpočetního výkonu ve službě Azure synapse Analytics (přehled)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md) .
* Azure Cosmos DB: [úrovně výkonu v Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* Místní SQL Server: [monitorování a optimalizace výkonu](/sql/relational-databases/performance/monitor-and-tune-for-performance)
* Místní souborový server: [optimalizace výkonu pro souborové servery](/previous-versions//dn567661(v=vs.85))