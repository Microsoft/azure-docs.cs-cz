---
title: Průvodce laděním a výkonem aktivity kopírování v Azure Data Factory | Microsoft Docs
description: Přečtěte si o klíčových faktorech, které ovlivňují výkon přesunu dat v Azure Data Factory při použití aktivity kopírování.
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
ms.openlocfilehash: d8ce0a4f6bacdd1c8c858d474e6f3957a23c6357
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967358"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Průvodce laděním a výkonem aktivity kopírování
> [!div class="op_single_selector" title1="Vyberte verzi Azure Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-activity-performance.md)
> * [Aktuální verze](copy-activity-performance.md)


Aktivita kopírování Azure Data Factory poskytuje prvotřídní řešení pro bezpečné, spolehlivé a vysoce výkonné datové zatížení. Můžete ji použít ke kopírování desítkových terabajtů dat každý den napříč širokou škálou cloudových a místních úložišť dat. Vysoký výkon při načítání dat je klíč, který zajistí, že se můžete soustředit na základní problém s velkými objemy dat: sestavování pokročilých analytických řešení a získání podrobných přehledů ze všech těchto dat.

Azure poskytuje sadu řešení pro datové úložiště a datové sklady na podnikové úrovni. Aktivita kopírování nabízí vysoce optimalizované prostředí pro načítání dat, které se dá snadno konfigurovat a nastavit. S jednou aktivitou kopírování můžete načíst data do:

* Azure SQL Data Warehouse v 1,2 GB/s.
* Úložiště objektů BLOB v Azure v 1,0 GB/s.
* Azure Data Lake Store v 1,0 GB/s.

Tento článek popisuje:

* [Referenční počty výkonu](#performance-reference) pro podporovaná úložiště dat zdroje a jímky, která vám pomůžou plánovat projekt.
* Funkce, které mohou zvýšit propustnost kopírování v různých scénářích, které zahrnují [jednotky pro integraci dat](#data-integration-units) (DIUs), [paralelní kopírování](#parallel-copy)a [dvoufázové kopírování](#staged-copy).
* [Pokyny k ladění výkonu](#performance-tuning-steps) , jak vyladit výkon a klíčové faktory, které mohou ovlivnit výkon kopírování.

> [!NOTE]
> Pokud nejste obeznámeni s aktivitou kopírování obecně, přečtěte si článek [Přehled aktivity kopírování](copy-activity-overview.md) .
>

## <a name="performance-reference"></a>Referenční dokumentace výkonu

V následující tabulce je uvedeno číslo propustnosti kopírování v MB/s pro daný zdroj a páry jímky v rámci jedné aktivity kopírování na základě interního testování. Pro porovnání také ukazuje, jakým způsobem může při kopírování dopomáhat různá nastavení [jednotek pro integraci dat](#data-integration-units) nebo [škálovatelnost modulu runtime integrace](concepts-integration-runtime.md#self-hosted-integration-runtime) v místním prostředí (více uzlů).

![Přehled výkonu](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> Když aktivita kopírování běží v prostředí Azure Integration runtime, minimální jednotky povolené integrace dat (dříve označované jako jednotky přesunu dat) jsou dvě. Pokud tento parametr nezadáte, přečtěte si téma výchozí jednotky integrace dat, které se používají v [jednotkách pro integraci dat](#data-integration-units).

**Ukazuje na poznámku:**

* Propustnost se počítá pomocí následujícího vzorce: [velikost čtených dat ze zdroje]/[doba trvání spuštění aktivity kopírování].
* Referenční údaje o výkonu v tabulce byly měřeny pomocí datové sady [TPC-H](http://www.tpc.org/tpch/) v jednom spuštění aktivity kopírování. Soubory testů pro úložiště na základě souborů jsou více soubory s velikostí 10 GB.
* V úložištích dat Azure zdroj a jímka mají ve stejné oblasti Azure.
* Pro hybridní kopírování mezi místními a cloudovým úložištěm dat byl každý uzel Integration runtime v místním prostředí spuštěný na počítači, který byl oddělený od úložiště dat s následující specifikací. Pokud byla spuštěna jedna aktivita, operace kopírování spotřebovány pouze malou část testovací počítač procesor, paměť nebo šířky pásma sítě.
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
        <td>Internetové rozhraní: 10 GB/s; intranetové rozhraní: 40 GB/s</td>
    </tr>
    </table>


> [!TIP]
> Vyšší propustnost můžete dosáhnout pomocí více DIUs. Například s 100 DIUs můžete kopírovat data ze služby Azure Blob Storage do Azure Data Lake Store na 1,0 GB/s. Další informace o této funkci a podporovaném scénáři najdete v části [jednotky pro integraci dat](#data-integration-units) . 

## <a name="data-integration-units"></a>Jednotky pro integraci dat

Jednotka Integration data je míra, která představuje napájení (kombinace procesoru, paměti a přidělení síťových prostředků) jedné jednotky v Azure Data Factory. Jednotka pro integraci dat platí jenom pro [prostředí Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime), ale ne pro místní [prostředí Integration runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

Minimální DIUs k tomu, aby mohl běžet aktivita kopírování, je dvě. Pokud není zadán, následující tabulka uvádí výchozí DIUs používá ve scénářích různé kopie:

| Kopírování | Výchozí DIUs určené služby |
|:--- |:--- |
| Kopírovat data mezi úložišti souborů | Mezi 4 a 32 v závislosti na počtu a velikosti souborů |
| Kopírovat data do Azure SQL Database nebo Azure Cosmos DB |Mezi 4 a 16 v závislosti na úrovni jímky Azure SQL Database nebo Cosmos DB (počet DTU/ru) |
| Všechny ostatní scénáře kopírování | 4 |

Chcete-li přepsat toto výchozí nastavení, zadejte hodnotu **dataIntegrationUnits** vlastnost následujícím způsobem. *Povolené hodnoty* pro vlastnost **dataIntegrationUnits** jsou až 256. *Skutečný počet DIUs* , že operace kopírování používá za běhu je rovna nebo menší než nakonfigurovaná hodnota, v závislosti na vašich dat vzor. Informace o úrovni výkonu, může se zobrazit při konfiguraci víc jednotek pro konkrétní kopírovat zdroje a jímky, najdete v článku [výkonu](#performance-reference).

Při monitorování spuštění aktivit můžete zobrazit DIUs, která se používá pro každé spuštění kopírování v výstupu aktivity kopírování. Další informace najdete v tématu [monitorování aktivit kopírování](copy-activity-overview.md#monitoring).

> [!NOTE]
> Nastavení DIUs větší než 4 se v současné době týká jenom při kopírování více souborů z Azure Storage, Azure Data Lake Storage, Amazon S3, Google Cloud Storage, cloudového FTP FTP nebo cloudu SFTP do dalších cloudových úložišť dat.
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

Pamatujte na to, že se vám účtuje celková doba operace kopírování. Celková doba, kterou se vám účtuje za přesun dat, je součet doby trvání napříč DIUs. Pokud úloha kopírování používá k trvat jednu hodinu se dvěma jednotkami cloudu a teď trvá 15 minut s osm jednotek v cloudu, bude celkové vyúčtování skoro stejné zůstane.

## <a name="parallel-copy"></a>Paralelní kopírování

Vlastnost **parallelCopies** můžete použít k označení paralelismu, které má aktivita kopírování použít. Tuto vlastnost si můžete představit jako maximální počet vláken v rámci aktivity kopírování, kterou si můžete přečíst ze zdroje nebo zapisovat do úložiště dat jímky paralelně.

Pro každou spuštění aktivity kopírování Azure Data Factory určuje počet paralelních kopií, které se mají použít ke kopírování dat ze zdrojového úložiště dat a do cílového úložiště dat. Výchozí počet paralelních kopií, které používá, závisí na typu zdroje a jímky, které používáte.

| Kopírování | Výchozí počet paralelních kopií určené služby |
| --- | --- |
| Kopírovat data mezi úložišti souborů |Závisí na velikosti souborů a na počtu DIUs používaných ke kopírování dat mezi dvěma úložišti dat cloudu nebo na fyzické konfiguraci počítače prostředí Integration runtime v místním prostředí. |
| Kopírování dat z libovolného zdrojového úložiště do Azure Table Storage |4 |
| Dalších scénářů kopírování |1 |

> [!TIP]
> Když kopírujete data mezi úložišti na základě souborů, výchozí chování obvykle poskytuje nejlepší propustnost. Výchozí chování je automaticky určováno na základě vzoru zdrojového souboru.

Pro řízení zatížení počítačů, které hostují vaše úložiště dat nebo pro optimalizaci výkonu kopírování, můžete přepsat výchozí hodnotu a zadat hodnotu pro vlastnost **parallelCopies** . Hodnota musí být celé číslo větší než nebo rovno 1. V době běhu používá aktivita kopírování hodnotu, která je menší nebo rovna hodnotě, kterou jste nastavili.

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

**Ukazuje na poznámku:**

* Při kopírování dat mezi úložišti založenými na souborech Určuje **parallelCopies** paralelismus na úrovni souboru. Blokování v rámci jednoho souboru probíhá automaticky a transparentně. Je navržená tak, aby používala nejvhodnější velikost bloku dat pro daný typ zdrojového úložiště dat, aby se data načetla paralelně a kolmo k **parallelCopies**. Skutečný počet paralelních kopie služba pro přesun dat se používá pro operaci kopírování v době běhu je delší než počet souborů, které máte. Pokud je chování kopírování **mergeFile**, aktivita kopírování nemůže využít paralelismus na úrovni souborů.
* Při kopírování dat z úložišť, která nejsou založená na souborech (s výjimkou [Oracle](connector-oracle.md#oracle-as-source), [Teradata](connector-teradata.md#teradata-as-source), [tabulka SAP](connector-sap-table.md#sap-table-as-source)a konektoru [SAP Open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) jako zdroje s povoleným vytvářením oddílů dat), do úložišť, která jsou založená na souborech, služba pro přesun dat ignoruje vlastnost **parallelCopies** . I v případě, že je zadán paralelismu, není použita v tomto případě.
* Vlastnost **parallelCopies** je kolmá na **dataIntegrationUnits**. Předchozí se počítá přes všechny jednotky integrace Data.
* Když zadáte hodnotu vlastnosti **parallelCopies** , zvažte zvýšení zátěže ve zdrojovém a úložišti dat jímky. Zvažte také zvýšení zatížení v místním prostředí Integration runtime, pokud je aktivita kopírování oprávněná, například pro hybridní kopírování. Toto zvýšení zatížení nastane hlavně v případě, že máte více aktivit nebo souběžných spuštění stejných aktivit, které se spouštějí ve stejném úložišti dat. Pokud si všimnete, že úložiště dat nebo místní prostředí Integration runtime je zahlcené zatížením, snižte hodnotu **parallelCopies** k uvolnění zátěže.

## <a name="staged-copy"></a>Kopírování dvoufázové instalace

Při kopírování dat ze zdrojového úložiště dat do úložiště dat jímky, můžete zvolit použití Blob storage jako dočasné pracovní úložiště. Pracovní je zvláště užitečná v následujících případech:

- **Chcete ingestovat data z různých úložišť dat do SQL Data Warehouse prostřednictvím základny.** SQL Data Warehouse používá k načtení velkých objemů dat do SQL Data Warehouse PolyBase jako vhodný mechanismus vysokou propustnost. Zdrojová data musí být ve službě BLOB Storage nebo Azure Data Lake Store a musí splňovat další kritéria. Při načítání dat z úložiště dat než Blob storage nebo Azure Data Lake Store můžete aktivovat kopírování prostřednictvím Blob storage dočasné pracovní data. V takovém případě Azure Data Factory provádí požadované transformace dat, aby se zajistilo, že splňuje požadavky základny. Potom použije PolyBase k načtení dat do SQL Data Warehouse efektivně. Další informace najdete v tématu [použití PolyBase k načítání dat do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **V některých případech trvá i v průběhu provádění hybridního přesunu dat (tedy kopírování z místního úložiště dat do cloudového úložiště dat) prostřednictvím pomalého síťového připojení.** Za účelem zvýšení výkonu můžete pomocí připravené kopie komprimovat data v místním prostředí, aby při přesunu dat do pracovního úložiště dat v cloudu trvalo méně času. Pak můžete data v pracovním úložišti dekomprimovat ještě předtím, než se načtou do cílového úložiště dat.
- **Nechcete v bráně firewall otevírat jiné porty než port 80 a port 443 kvůli podnikovým zásadám IT.** Například při kopírování dat z do místního úložiště dat jímky Azure SQL Database nebo Azure SQL Data Warehouse jímky, budete muset aktivovat odchozí komunikaci TCP na portu 1433 pro bránu Windows firewall a váš podnikový firewall. V tomto scénáři může připravené kopírování využít výhod místního prostředí Integration runtime k prvnímu kopírování dat do pracovní instance úložiště objektů BLOB přes protokol HTTP nebo HTTPS na portu 443. Pak může data načíst do SQL Database nebo SQL Data Warehouse z přípravy úložiště objektů BLOB. V tomto toku není nutné povolit port 1433.

### <a name="how-staged-copy-works"></a>Kopírování funguje jak dvoufázové instalace

Při aktivaci pracovní funkce data se zkopíruje ze zdrojového úložiště dat do přípravného úložiště objektů Blob (funkce přineste si vlastní). V dalším kroku data zkopírována z pracovní úložiště dat do úložiště dat jímky. Azure Data Factory pro vás automaticky spravuje tok se dvěma fázemi. Po dokončení přesunu dat Azure Data Factory taky vyčistit dočasná data z pracovního úložiště.

![Kopírování dvoufázové instalace](media/copy-activity-performance/staged-copy.png)

Když provedete přesun dat pomocí pracovního úložiště, můžete určit, jestli chcete data před přesunem dat ze zdrojového úložiště dat do dočasného nebo přípravného úložiště dat a pak je dekomprimovat, před přesunem dat z dočasného nebo přípravného dat. úložiště dat jímky.

V současné době nemůžete kopírovat data mezi dvěma datovými úložišti, která jsou propojena prostřednictvím jiného samoobslužného úřadu pro hostování, ani bez dvoufázové kopie. V takovém případě můžete nakonfigurovat dvě explicitně zřetězené aktivity kopírování ke kopírování ze zdroje do přípravy, a to z přípravy do jímky.

### <a name="configuration"></a>Konfiguraci

Nakonfigurujte nastavení **enableStaging** v aktivitě kopírování, abyste určili, jestli chcete data připravit v úložišti objektů blob, než je načtete do cílového úložiště dat. Při nastavování **enableStaging** na `TRUE`zadejte další vlastnosti uvedené v následující tabulce. Je také potřeba vytvořit sdílenou službu Azure Storage nebo sdílený přístupový podpis s úložištěm pro přípravu, pokud ji ještě nemáte.

| Vlastnost | Popis | Výchozí hodnota | Požadováno |
| --- | --- | --- | --- |
| enableStaging |Určete, jestli chcete kopírovat data prostřednictvím jako dočasné pracovní úložiště. |False |Ne |
| linkedServiceName |Zadejte název [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) propojenou službu, která odkazuje na instanci úložiště, které můžete použít jako dočasné pracovní úložiště. <br/><br/> Úložiště se sdíleným přístupovým podpisem nelze použít k načtení dat do SQL Data Warehouse prostřednictvím základny. Můžete ji použít v jiných scénářích. |neuvedeno |Ano, pokud **enableStaging** nastavena na hodnotu TRUE |
| path |Zadejte cestu úložiště objektů Blob, který chcete s daty, dvoufázové instalace. Pokud cestu nezadáte, služba vytvoří kontejner, do kterého budou ukládat dočasná data. <br/><br/> Zadejte cestu, pouze v případě, že používáte úložiště pomocí sdíleného přístupového podpisu nebo vyžadujete dočasných dat v konkrétním umístění. |neuvedeno |Ne |
| Hodnotou EnableCompression |Určuje, zda mají být data před kopírováním do cíle komprimována. Toto nastavení omezuje objem dat přenášených. |False |Ne |

>[!NOTE]
> Pokud použijete připravené kopírování s povolenou kompresí, instanční objekt nebo ověřování MSI pro propojenou službu pracovního objektu BLOB se nepodporuje.

Tady je ukázková definice aktivity kopírování s vlastnostmi popsanými v předchozí tabulce:

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

Účtují se vám poplatky podle dvou kroků: doba kopírování a typ kopírování.

* Při použití přípravy během kopírování do cloudu, který kopíruje data z cloudového úložiště dat do jiného cloudového úložiště dat, se v obou fázích, které zmocňuje prostředí Azure Integration runtime, účtují [součet doby kopírování pro krok 1 a krok 2] x [cena za jednotku cloudového kopírování].
* Při použití přípravy během hybridní kopie, která kopíruje data z místního úložiště dat do cloudového úložiště dat, je jedna fáze, kterou má samoobslužný modul runtime integrace v místním prostředí, účtována za [doba trvání hybridního kopírování] × [cena za jednotku Hybrid Copy] + [doba kopírování v cloudu] x [Jednotková cena za kopii v cloudu].

## <a name="performance-tuning-steps"></a>Postup optimalizace výkonu

Provedením těchto kroků vyoptimalizujete výkon služby Azure Data Factory s aktivitou kopírování.

1. **Vytvořte směrný plán.** Během fáze vývoje otestujte svůj kanál pomocí aktivity kopírování v reprezentativní ukázce dat. Shromažďování podrobností o spuštění a charakteristik výkonu po [monitorování aktivity kopírování](copy-activity-overview.md#monitoring)

2. **Diagnostikujte a Optimalizujte výkon.** Pokud výkon, který sledujete, nesplňuje vaše očekávání, identifikujte kritická místa výkonu. Potom optimalizace výkonu můžete odebrat nebo snižují dopad kritické body.

    V některých případech se při spuštění aktivity kopírování v Azure Data Factory zobrazí zpráva "Tipy pro ladění výkonu" v horní části [stránky monitorování aktivity kopírování](copy-activity-overview.md#monitor-visually), jak je znázorněno v následujícím příkladu. Zpráva vás upozorní na kritické body, které se identifikovaly pro daný běh kopírování. Také vám pomůže s tím, co se dá změnit, aby se zvýšila propustnost kopírování. Tipy pro ladění výkonu aktuálně poskytují návrhy jako:

    - Při kopírování dat do Azure SQL Data Warehouse použít základ
    - Zvyšte Azure Cosmos DB jednotky žádostí nebo Azure SQL Database DTU (jednotky propustnosti databáze), když je prostředek na straně úložiště dat kritický.
    - Odeberte nepotřebnou fázi kopírování.

    Pravidla optimalizace výkonu se postupně rozšiřují.

    **Příklad: Kopírování do Azure SQL Database s využitím tipů pro ladění výkonu**

    V této ukázce se při spuštění kopírování Azure Data Factory oznámení, že Azure SQL Database jímka dosáhne vysokého využití DTU, což zpomaluje operace zápisu. Návrhem je zvýšení Azure SQL Database úrovně o více DTU. 

    ![Sledování kopírování pomocí tipů pro ladění výkonu](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    Kromě toho jsou zde některé běžné požadavky. Úplný popis nástroje pro diagnostiku výkonu překračuje rozsah tohoto článku.

   * Funkce výkonu:
     * [Paralelní kopírování](#parallel-copy)
     * [Jednotky integrace dat](#data-integration-units)
     * [Kopírování dvoufázové instalace](#staged-copy)
     * [Škálovatelnost prostředí Integration runtime v místním prostředí](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Modul runtime integrace v místním prostředí](#considerations-for-self-hosted-integration-runtime)
   * [Zdroj](#considerations-for-the-source)
   * [jímka](#considerations-for-the-sink)
   * [Serializace a deserializace](#considerations-for-serialization-and-deserialization)
   * [Komprese](#considerations-for-compression)
   * [Mapování sloupce](#considerations-for-column-mapping)
   * [Další aspekty](#other-considerations)

3. **Rozšiřte konfiguraci na celou datovou sadu.** Až budete spokojeni s výsledky a výkonem spuštění, můžete rozšířit definici a kanál tak, aby pokryly celou datovou sadu.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Pokyny pro prostředí Integration runtime v místním prostředí

Pokud vaše aktivita kopírování běží v místním prostředí Integration runtime, pamatujte na následující:

**Nastavení**: Pro hostování prostředí Integration runtime doporučujeme použít vyhrazený počítač. Viz [předpoklady pro použití prostředí Integration runtime](concepts-integration-runtime.md)v místním prostředí.

**Horizontální**navýšení kapacity: Jeden logický modul runtime integrace v místním prostředí s jedním nebo více uzly může obsluhovat více souběžně spuštěných aktivit kopírování. Pokud máte těžkou potřebu při přesunu hybridních dat, ať už se jedná o velký počet souběžných spuštění aktivit kopírování, nebo s velkým objemem dat ke kopírování, zvažte horizontální navýšení kapacity [prostředí Integration runtime](create-self-hosted-integration-runtime.md#high-availability-and-scalability) pro zajištění dalších prostředků, které umožňují kopírování.

## <a name="considerations-for-the-source"></a>Důležité informace pro zdroj

### <a name="general"></a>Obecné

Ujistěte se, že základní úložiště dat není zahlcené jinými úlohami, které běží na nebo proti němu.

Informace o úložištích dat Microsoftu najdete v [tématech monitorování a ladění](#performance-reference) , která jsou specifická pro úložiště dat. Tato témata můžete pochopit vlastnosti výkonu úložiště dat a jak minimalizovat dobu odezvy a maximalizuje propustnost.

* Pokud kopírujete data ze služby Blob Storage do SQL Data Warehouse, zvažte použití základny pro zvýšení výkonu. Další informace najdete v tématu [použití PolyBase k načítání dat do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Pokud kopírujete data ze HDFS do služby Azure Blob Storage nebo Azure Data Lake Store, zvažte použití DistCp ke zvýšení výkonu. Další informace najdete v tématu [použití DistCp ke kopírování dat ze HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Pokud kopírujete data z RedShift do Azure SQL Data Warehouse, úložiště objektů BLob v Azure nebo Azure Data Lake Store, zvažte použití uvolnění pro zvýšení výkonu. Další informace najdete v tématu [použití uvolnění ke kopírování dat z Amazon RedShift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Úložiště dat na základě souboru

* **Průměrná velikost souboru a počet souborů**: Aktivita kopírování přenáší data v jednom souboru. Pomocí stejné množství dat k přesunutí je nižší, pokud data se skládá z mnoha malých souborů spíše než několik velkých souborů z důvodu spuštění fáze pro každý soubor celkovou propustnost. Pokud je to možné, zkombinujte malé soubory do větších souborů, abyste získali vyšší propustnost.
* **Formát souboru a komprese**: Další způsoby, jak zvýšit výkon, naleznete v části [požadavky na serializaci a](#considerations-for-serialization-and-deserialization) deserializaci a [požadavky pro kompresní](#considerations-for-compression) oddíly.

### <a name="relational-data-stores"></a>Úložiště relačních dat

* **Datový vzor**: Schéma tabulky ovlivňuje kopírování propustnost. Velikost velkého řádku poskytuje lepší výkon než velikost malého řádku pro kopírování stejného množství dat. Důvodem je, že databáze můžete efektivněji načíst menší počet dávek dat, které obsahují menší počet řádků.
* **Dotaz nebo uložená procedura**: Optimalizujte logiku dotazu nebo uložené procedury, kterou zadáte ve zdroji aktivity kopírování, aby se data načetla efektivněji.

## <a name="considerations-for-the-sink"></a>Důležité informace pro jímku

### <a name="general"></a>Obecné

Ujistěte se, že základní úložiště dat není zahlcené jinými úlohami, které běží na nebo proti němu.

Informace o úložištích dat Microsoftu najdete v [tématech monitorování a ladění](#performance-reference) , která jsou specifická pro úložiště dat. Tato témata můžete pochopit vlastnosti výkonu úložiště dat a jak minimalizovat dobu odezvy a maximalizuje propustnost.

* Pokud kopírujete data z libovolného úložiště dat do Azure SQL Data Warehouse, zvažte použití základny pro zvýšení výkonu. Další informace najdete v tématu [použití PolyBase k načítání dat do Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Pokud kopírujete data ze HDFS do služby Azure Blob Storage nebo Azure Data Lake Store, zvažte použití DistCp ke zvýšení výkonu. Další informace najdete v tématu [použití DistCp ke kopírování dat ze HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Pokud kopírujete data z RedShift do Azure SQL Data Warehouse, úložiště objektů BLOB v Azure nebo Azure Data Lake Store, zvažte použití uvolnění pro zvýšení výkonu. Další informace najdete v tématu [použití uvolnění ke kopírování dat z Amazon RedShift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Úložiště dat na základě souboru

* **Zkopírujte chování**: Pokud kopírujete data z jiného úložiště dat založeného na souborech, aktivita kopírování má tři možnosti prostřednictvím vlastnosti **copyBehavior** . Zachová hierarchie, sloučí hierarchie nebo sloučí soubory. Zachování nebo sloučení hierarchie má žádné nebo téměř žádné nároky na výkon, ale slučování souborů způsobí, že chcete zvýšit nároky na výkon.
* **Formát souboru a komprese**: Další způsoby, jak zvýšit výkon, naleznete v části [požadavky na serializaci a](#considerations-for-serialization-and-deserialization) deserializaci a [požadavky pro kompresní](#considerations-for-compression) oddíly.

### <a name="relational-data-stores"></a>Úložiště relačních dat

* **Chování při kopírování a vynásobení výkonu**: Existují různé způsoby, jak zapisovat data do jímky SQL. Další informace z [osvědčeného postupu pro načítání dat do Azure SQL Database](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database).

* **Velikost dat vzor a batch**:
  * Schéma tabulky ovlivňuje kopírování propustnost. Ke zkopírování stejné množství dat, velký řádek velikosti umožňuje lepší výkon než velikost malých řádku, protože databáze můžete efektivněji menší počet dávek dat potvrzení změn.
  * Aktivita kopírování vloží data do řady dávek. Můžete nastavit počet řádků v dávce pomocí **writeBatchSize** vlastnost. Pokud data obsahují malé řádků, můžete nastavit **writeBatchSize** vlastnost s vyšší hodnota můžou využívat výhody nižší režijní náklady na služby batch a vyšší propustnost. Je-li velikost řádku vašich dat je velká, dejte pozor, když zvýšíte **writeBatchSize**. Vysoká hodnota může vést k kopírování chybu způsobenou přetížení databáze.

### <a name="nosql-stores"></a>Úložišť typu NoSQL

* Pro **Table storage**:
  * **Oddíl**: Zápis dat do prokládaných oddílů výrazně snižuje výkon. Seřaďte zdrojová data podle klíče oddílu, aby byla data do jednoho oddílu po druhém vložena. Nebo můžete upravit logiku pro zápis dat do jednoho oddílu.

## <a name="considerations-for-serialization-and-deserialization"></a>Důležité informace týkající se serializace a deserializace

Serializace a deserializace může nastat, pokud je vstupní datová sada nebo výstupní datová sada soubor. Další informace o podporovaných formátech souborů podle aktivity kopírování najdete v tématu [podporované formáty souborů a komprese](supported-file-formats-and-compression-codecs.md).

**Zkopírujte chování**:

* Kopírování souborů mezi úložišti dat na základě souboru:
  * Pokud vstupní a výstupní datové sady mají stejné nastavení jako formát souboru, služba přesunu dat provede *binární kopii* bez jakékoli serializace nebo deserializace. Zobrazí větší propustnost v porovnání s scénář, ve které se liší od sebe navzájem nastavení formátu souboru zdroje a jímky.
  * V případě, že vstupní a výstupní datové sady jsou v textovém formátu a pouze typ kódování je jiný než stejný typ kódování, převádí služba přesunu dat pouze převod kódování. Neprovádí žádné serializace a deserializace, což způsobí, že některé výkonu režie ve srovnání s binární kopie.
  * Pokud vstupní a výstupní datové sady mají jiné formáty souborů nebo různé konfigurace, jako jsou oddělovače, služba přesunu dat deserializace zdrojová data do datového proudu, transformuje a pak je zaznamená do formátu výstupu, který jste určili. Výsledkem této operace mnohem více významné výkonnostní režii ve srovnání s další scénáře.
* Při kopírování souborů do nebo z úložiště dat, které není založené na souboru, například z úložiště založeného na souborech do relačního úložiště, je nutné určit serializaci nebo deserializaci krok. Tento krok vede významné výkonnostní režii.

**Formát souboru**: Formát souboru, který zvolíte, může ovlivnit výkon kopírování. Například je Avro kompaktní binární formát, který ukládá metadata s daty. Má širokou podporu v ekosystému Hadoop pro zpracování a dotazování na ně. Avro je dražší pro serializaci a deserializaci, což vede k nižší propustnosti kopírování v porovnání s formátem textu. 

Ujistěte se, podle vašeho výběru formátu v průběhu zpracování toku komplexně. Začít s:

- Jaká data jsou uložená v, zdrojová úložiště dat nebo se mají extrahovat z externích systémů.
- Nejlepší formát pro úložiště, analytické zpracování a dotazování.
- V jakém formátu mají být data exportována do tržiště dat pro vytváření sestav a nástrojů vizualizace.

Někdy formát souboru, který je neoptimální pro čtení a zápisu může být dobrou volbou, pokud byste zvážit celkové analytické procesu.

## <a name="considerations-for-compression"></a>Důležité informace pro kompresi

Když je vstupní nebo výstupní datová sada soubor, můžete nastavit aktivitu kopírování, aby se při zápisu dat do cíle prováděla komprese nebo dekomprese. Při výběru komprese provedete kompromis mezi vstupně výstupní (I/O) a procesoru. Komprese dat příplatek ve výpočetních prostředcích. Ale na oplátku omezuje v / v sítě a úložiště. V závislosti na vašich datech se může zobrazit zvýšení celkové propustnosti kopírování.

**Kodek**: Každý Kompresní kodek má výhody. Například bzip2 má nejnižší kopírování propustnost, ale získat nejlepší výkon dotazů Hive pomocí bzip2, protože je možné rozdělit ke zpracování. Nástroj gzip je nejvyváženější možnost a používá se nejčastěji. Zvolte kodek, který nejlépe vyhovuje vaší situaci začátku do konce.

**Úroveň**: Pro každý Kompresní kodek můžete zvolit jednu ze dvou možností: nejrychlejší komprimovaný a optimálně komprimovaný. Možnost nejrychlejší komprese komprimuje data co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován. Optimálně komprimovaný možnost tráví víc času na komprese a vrací minimální nároky na data. Obě možnosti zobrazíte, která poskytuje lepší výkon ve vašem případě můžete otestovat.

**Zvážení**: Pokud chcete kopírovat velké množství dat mezi místním úložištěm a cloudem, zvažte použití [připravené kopie](#staged-copy) s povolenou kompresí. Použití dočasného úložiště je užitečné v případě, že je šířka pásma vaší podnikové sítě a služeb Azure omezující faktor a vy chcete vstupní datovou sadu a výstupní datovou sadu v nekomprimované podobě.

## <a name="considerations-for-column-mapping"></a>Důležité informace týkající se mapování sloupce

Vlastnost ColumnMappings v aktivitě kopírování můžete nastavit tak, aby se namapovala vše nebo podmnožina vstupních sloupců na výstupní sloupce. Poté, co služba pro přesun dat načte data ze zdroje, je potřeba provést mapování sloupců s daty před zapisuje data do jímky. Tato další zpracování snižuje kopírování propustnost.

Pokud zdrojové úložiště dat je zadávat dotazy, pokud je relačního úložiště, jako je SQL Database nebo SQL Server, nebo pokud je úložiště typu NoSQL, jako je Table storage nebo Azure Cosmos DB, představte si třeba doručením (push) sloupce filtrování a změna uspořádání logiku pro **dotazu** vlastnosti namísto použití mapování sloupců. Tímto způsobem se projekce objeví, zatímco služba pro přesun dat načítá data ze zdrojového úložiště dat, kde je mnohem efektivnější.

Další informace najdete v [mapování schématu aktivity kopírování](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Další důležité informace

Pokud je velikost dat, která chcete zkopírovat, Velká, můžete upravit obchodní logiku a dále rozdělit data. Aktivitu kopírování můžete naplánovat tak, aby se častěji spouštěla, aby se snížila velikost dat pro každou aktivitu kopírování, která běží.

Buďte opatrní na počet datových sad a aktivit kopírování, které vyžadují, aby se Azure Data Factory připojovaly ke stejnému úložišti dat ve stejnou dobu. Mnoho souběžných kopírování úloh může omezit úložiště dat a vést ke snížení výkonu, opakování interní úlohu kopírování a v některých případech se selhání spuštění.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Vzorový scénář: Kopírování z místního SQL serveru do úložiště objektů BLOB

**Scénář**: Kanál je vytvořený tak, aby kopíroval data z místního SQL serveru do úložiště objektů BLOB ve formátu CSV. Chcete-li úlohu kopírování rychleji, by měl zkomprimují soubory CSV do formátu bzip2.

**Testování a analýza**: Propustnost aktivity kopírování je menší než 2 MB/s, což je mnohem pomalejší než srovnávací test výkonnosti.

**Analýza a optimalizace výkonu**: Pokud chcete řešit potíže s výkonem, Podívejme se na to, jak se data zpracovávají a přesunují.

- **Číst data**: Modul runtime integrace otevře připojení k SQL Server a odešle dotaz. SQL Server odpoví odesláním datového proudu do prostředí Integration runtime prostřednictvím intranetu.
- **Serializovat a komprimovat data**: Modul runtime integrace serializace datový proud do formátu CSV a komprimuje data do datového proudu bzip2.
- **Zapisovat data**: Prostředí Integration runtime nahrává datový proud bzip2 do úložiště objektů BLOB prostřednictvím Internetu.

Jak vidíte, data se zpracují a přesunou se sekvenčním způsobem streamování: SQL Server > LAN > Integration runtime > WAN > BLOB Storage. Celkový výkon je ověřovaný minimální propustností v rámci kanálu.

![Tok dat](./media/copy-activity-performance/case-study-pic-1.png)

Nejméně jednu z těchto faktorů může způsobit snížení výkonu:

* **Zdroj**: SQL Server má při velkém zatížení nízkou propustnost.
* Místní **prostředí Integration runtime**:
  * **SÍŤ LAN**: Integration runtime je umístěný daleko z SQL Server počítače a má připojení s malou šířkou pásma.
  * **Prostředí Integration runtime**: Prostředí Integration runtime dosáhlo svých omezení zatížení k provedení následujících operací:
    * **Serializace**: Serializace datového streamu do formátu CSV má pomalou propustnost.
    * **Komprese**: Zvolili jste pomalu Kompresní kodek, například bzip2, což je 2,8 MB/s Core i7.
  * **SÍŤ WAN**: Šířka pásma mezi podnikovou sítí a službami Azure je nízká, například T1 = 1 544 KB/s; T2 = 6 312 kb/s.
* **Jímka**: Úložiště objektů BLOB má nízkou propustnost. Tento scénář je nepravděpodobný, protože smlouva SLA na úrovni služeb zaručuje minimálně 60 MB/s.

V takovém případě může být komprese dat bzip2 zpomalení celého kanálu. Přepnutí na kompresní kodek gzip může zmírnit tyto potíže.

## <a name="references"></a>Odkazy

Tady jsou odkazy na sledování výkonu a ladění pro některá z podporovaných úložišť dat:

* Azure Storage, což zahrnuje úložiště objektů BLOB a úložiště tabulek: [Azure Storage cíle škálovatelnosti](../storage/common/storage-scalability-targets.md) a [Kontrolní seznam pro výkon a škálovatelnost Azure Storage](../storage/common/storage-performance-checklist.md).
* Azure SQL Database: Můžete [monitorovat výkon](../sql-database/sql-database-single-database-monitor.md) a kontrolovat procento transakční jednotky (DTU).
* Azure SQL Data Warehouse: Jeho schopnost se měří v jednotkách datového skladu (DWU). Viz [Správa výpočetního výkonu v Azure SQL Data Warehouse (přehled)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Úrovně výkonu v Azure Cosmos DB](../cosmos-db/performance-levels.md).
* Místní SQL Server: [Monitorujte a Optimalizujte výkon](https://msdn.microsoft.com/library/ms189081.aspx).
* Místní souborový server: [Ladění výkonu pro souborové servery](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Další kroky
Další články o aktivitě kopírování najdete v článcích:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Mapování schématu aktivity kopírování](copy-activity-schema-and-type-mapping.md)
- [Zkopírovat aktivitu odolnost proti chybám](copy-activity-fault-tolerance.md)
