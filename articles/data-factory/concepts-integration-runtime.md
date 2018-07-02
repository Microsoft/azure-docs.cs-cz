---
title: Prostředí Integration Runtime v Azure Data Factory | Microsoft Docs
description: Seznamte se s prostředím Integration Runtime v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/14/2018
ms.author: jingwang
ms.openlocfilehash: cd2964d0a579b903ddebfd19c90d2ce38d2374bc
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050416"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Prostředí Integration Runtime v Azure Data Factory
Prostředí Integration Runtime (IR) je výpočetní infrastruktura, kterou Azure Data Factory používá k poskytování následujících funkcí integrace dat v různých síťových prostředích:

- **Přesuny dat**: přesouvání dat mezi úložišti dat ve veřejné síti a úložišti dat v privátní síti (místní nebo virtuální privátní síť). Poskytuje podporu vestavěných konektorů, převodu formátu, mapování sloupců a výkonného a škálovatelného přenosu dat.
- **Odesílání aktivit**: odesílání a monitorování aktivit transformace spuštěných v různých výpočetních službách, jako je Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server a další.
- **Spouštění balíčků služby SSIS**: nativní spouštění balíčků služby SQL Server Integration Services (SSIS) ve spravovaném výpočetním prostředí Azure.

Aktivita ve službě Data Factory určuje akci, která se má provést. Propojená služba určuje cílové úložiště dat nebo výpočetní službu. Prostředí Integration Runtime představuje spojení mezi aktivitou a propojenými službami.  Na toto prostředí se odkazuje z propojené služby. Poskytuje výpočetní prostředí, ve kterém se příslušná aktivita buď spustí, nebo se z něj odešle.  Tímto způsobem se dá aktivita co nejefektivněji provést v oblasti, která je nejblíž cílovému úložišti dat nebo výpočetní službě, a zároveň vyhovět potřebám zabezpečení a dodržování předpisů.

## <a name="integration-runtime-types"></a>Typy prostředí Integration Runtime
Služba Data Factory nabízí tři typy prostředí Integration Runtime. Z nich si můžete vybrat ten, který nejlépe vyhovuje hledaným funkcím integrace dat a potřebám síťového prostředí.  Jedná se o tyto tři typy:

- Azure
- V místním prostředí
- Azure-SSIS

Následující tabulka obsahuje informace o podpoře funkcí a sítí pro každý typ prostředí Integration Runtime:

Typ prostředí IR | Veřejná síť | Privátní síť
------- | -------------- | ---------------
Azure | Přesuny dat<br/>Odesílání aktivit | &nbsp;
V místním prostředí | Přesuny dat<br/>Odesílání aktivit | Přesuny dat<br/>Odesílání aktivit
Azure-SSIS | Spouštění balíčků služby SSIS | Spouštění balíčků služby SSIS

Následující diagram znázorňuje, jak se dají různá prostředí Integration Runtime používat v kombinaci, aby nabízela bohaté funkce integrace dat a podporu sítí:

![Různé typy prostředí Integration Runtime](media\concepts-integration-runtime\different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Prostředí Azure Integration Runtime
Prostředí Azure Integration Runtime nabízí tyto funkce:

- Spouštění aktivity kopírování mezi úložišti dat v cloudu.
- Odesílání následujících aktivit transformace ve veřejné síti: aktivita Hive služby HDInsight, aktivita Pig služby HDInsight, aktivita MapReduce služby HDInsight, aktivita Spark služby HDInsight, aktivita streamování služby HDInsight, aktivita provedení dávky služby Machine Learning, aktivity aktualizace prostředku služby Machine Learning, aktivita uložené procedury, aktivita U-SQL služby Data Lake Analytics, vlastní aktivita rozhraní .Net, webová aktivita, aktivita vyhledávání a aktivita načtení metadat.

### <a name="azure-ir-network-environment"></a>Síťové prostředí Azure IR
Prostředí Azure Integration Runtime podporuje připojení k úložištím dat a výpočetním službám ve veřejné síti s veřejně přístupnými koncovými body. Pro prostředí Azure Virtual Network používejte prostředí Integration Runtime v místním prostředí.

### <a name="azure-ir-compute-resource-and-scaling"></a>Výpočetní prostředky a škálování prostředí Azure IR
Prostředí Azure Integration Runtime poskytuje v rámci Azure plně spravované výpočetní prostředí bez serveru.  Nemusíte si dělat starosti se zřizováním infrastruktury, instalací softwaru, opravami ani škálováním kapacity.  Kromě toho platíte jenom po dobu, kdy prostředí opravdu používáte.

Prostředí Azure Integration Runtime poskytuje nativní výpočetní prostředí pro zabezpečené, spolehlivé a vysoce výkonné přesuny dat mezi cloudovými úložišti dat.  Můžete nastavit počet jednotek integrace dat, který se má použít v aktivitě kopírování, a velikost výpočetního prostředí Azure IR se elasticky vertikálně navýší, aniž byste museli velikost prostředí Azure Integration Runtime explicitně upravovat.

Odesílání aktivit je nenáročná operace, která zajišťuje směrování aktivity do cílové výpočetní služby, takže pro tento scénář není nutné vertikálně navyšovat kapacitu výpočetního prostředí.

Informace o vytváření a konfiguraci prostředí najdete v návodu k vytvoření a konfiguraci prostředí Azure IR mezi příručkami s postupy. 

## <a name="self-hosted-integration-runtime"></a>Prostředí Integration Runtime v místním prostředí
Prostředí IR v místním prostředí nabízí tyto funkce:

- Spouštění aktivity kopírování mezi úložišti dat v cloudu a úložištěm dat v privátní síti.
- Odesílání následujících aktivit transformace ve výpočetních prostředcích v místní virtuální síti nebo virtuální síti Azure: aktivita Hive služby HDInsight (BYOC), aktivita Pig služby HDInsight (BYOC), aktivita MapReduce služby HDInsight (BYOC), aktivita Spark služby HDInsight (BYOC), aktivita streamování služby HDInsight (BYOC), aktivita provedení dávky služby Machine Learning, aktivity aktualizace prostředku služby Machine Learning, aktivita uložené procedury, aktivita U-SQL služby Data Lake Analytics, vlastní aktivita rozhraní .Net, aktivita vyhledávání a aktivita načtení metadat.

> [!NOTE] 
> Prostředí Integration Runtime v místním prostředí umožňuje podporu úložišť dat, která vyžadují vlastní ovladač, jako je SAP Hana, MySQL atd.  Další informace najdete v tabulce [podporovaných úložišť dat](copy-activity-overview.md#supported-data-stores-and-formats).

### <a name="self-hosted-ir-network-environment"></a>Síťové prostředí místního prostředí IR
Pokud chcete v prostředí privátní sítě, které nenabízí přímý přístup z prostředí veřejného cloudu, provádět zabezpečenou integraci dat, můžete nainstalovat prostředí IR do místního prostředí za podnikovou bránu firewall nebo v rámci virtuální privátní sítě.  Prostředí Integration Runtime v místním prostředí navazuje jenom odchozí připojení HTTP do otevřeného internetu.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Výpočetní prostředky a škálování místního prostředí IR
Prostředí IR v místním prostředí je potřeba nainstalovat do místního počítače nebo virtuálního počítače v rámci privátní sítě. V současné době podporujeme spouštění prostředí IR v místním prostředí jenom v operačním systému Windows.  

Za účelem vysoké dostupnosti a škálovatelnosti můžete horizontálně navýšit kapacitu prostředí IR v místním prostředí tak, že logickou instanci přidružíte k víc místním počítačům v režimu aktivní-aktivní.  Další informace najdete v článku o vytváření a konfiguraci místního prostředí IR mezi příručkami s postupy.

## <a name="azure-ssis-integration-runtime"></a>Prostředí Azure-SSIS Integration Runtime
Pokud chcete navýšit a přesunout stávající úlohy služby SSIS, můžete vytvořit prostředí Azure SSIS IR pro nativní spouštění balíčků služby SSIS.

### <a name="azure-ssis-ir-network-environment"></a>Síťové prostředí Azure-SSIS IR
Prostředí Azure-SSIS IR se dá zřídit ve veřejné síti nebo privátní síti.  Připojením prostředí Azure-SSIS IR k virtuální síti připojené k vaší místní síti se dá získat podpora přístupu k místním datům.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Výpočetní prostředky a škálování prostředí Azure-SSIS IR
Prostředí Azure-SSIS IR je plně spravovaný cluster virtuálních počítačů Azure vyhrazených ke spouštění balíčků služby SSIS. K hostování katalogu projektů nebo balíčků služby SSIS (SSISDB), který k němu bude připojený, můžete použít vlastní server Azure SQL Database nebo spravované instance (Preview). Můžete vertikálně navýšit výkon výpočetního prostředí tím, že určíte velikost uzlu a pak určíte počet uzlů v clusteru. Prostředí Azure-SSIS Integration Runtime můžete podle libosti zastavovat a spouštět, takže můžete mít pod kontrolou související náklady.

Další informace najdete v článku o vytváření a konfiguraci prostředí Azure-SSIS IR mezi příručkami s postupy.  Po vytvoření můžete existující balíčky služby SSIS nasazovat a spravovat s minimem změn pomocí známých nástrojů, jako jsou SQL Server Data Tools (SSDT) a SQL Server Management Studio (SSMS), stejně jako kdybyste službu SSIS používali místně.

Další informace o modulu runtime Azure-SSIS najdete v následujících článcích: 

- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny pro vytvoření Azure-SSIS IR a využívá databázi Azure SQL k hostování katalogu SSIS. 
- [Postup: Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) Tento článek dále navazuje na tento kurz a obsahuje pokyny k použití spravované instance SQL Azure (Preview) a připojení IR k virtuální síti. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst informace o Azure-SSIS IR a popisy stavů ve vrácených informacích. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Tento článek ukazuje, jak zastavit, spustit nebo odebrat Azure-SSIS IR. Ukazuje také postup horizontálního navýšení kapacity Azure-SSIS IR přidáním více uzlů. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek obsahuje koncepční informace o připojení Azure-SSIS IR k virtuální síti Azure. Poskytuje také kroky pro využití webu Azure Portal ke konfiguraci virtuální sítě, aby se k ní prostředí Azure-SSIS IR mohlo připojit. 

## <a name="integration-runtime-location"></a>Umístění prostředí Integration Runtime
Do umístění služby Data Factory se ukládají metadata datové továrny a inicializují se z něj spouštění kanálu. Datová továrna mezitím může přistupovat k úložištím dat a výpočetním službám v jiných oblastech Azure za účelem přesouvání dat mezi úložišti dat nebo zpracování dat pomocí výpočetních služeb. Toto chování probíhá prostřednictvím [globálně dostupného prostředí IR](https://azure.microsoft.com/global-infrastructure/services/), aby se zajistilo dodržování předpisů pro data, efektivita a nižší náklady na celkový výstup sítě.

Umístění prostředí IR určuje umístění výpočetního prostředí back-end, tedy v podstatě umístění, kde se provádí přesun dat, odesílání aktivit a spouštění balíčku SSIS. Umístění prostředí IR se může lišit od umístění, do kterého patří datová továrna. 

### <a name="azure-ir-location"></a>Umístění prostředí Azure IR
Pro prostředí Azure IR můžete nastavit určité umístění a v tom případě bude přesun dat nebo odesílání aktivit probíhat v této konkrétní oblasti. 

Pokud se rozhodnete použít automatický překlad umístění prostředí Azure IR, což je výchozí nastavení: 

- V případě aktivity kopírování se služba ADF pokusí automaticky zjistit vaše úložiště dat jímky a zdrojové úložiště dat a zvolí nejlepší umístění buď ve stejné oblasti, pokud je dostupné nebo nejbližší na stejném území, nebo pokud nejsou zjistitelné, použije jako alternativu oblast datové továrny.
- V případě provádění aktivity Lookup a GetMetadata a odesílání aktivity transformace služba ADF použije prostředí IR v oblasti datové továrny.

V zobrazení monitorování aktivit kanálu v uživatelském rozhraní nebo datové části monitorování aktivit můžete monitorovat, které umístění prostředí IR se při provádění aktivit použije.

>[!TIP]
>Pokud máte přísné požadavky na dodržování předpisů pro data a potřebujete zajistit, aby data neopustila určité území, můžete prostředí Azure IR explicitně vytvořit v určité oblasti a odkázat propojenou službu na toto prostředí IR pomocí vlastnosti ConnectVia. Například pokud chcete kopírovat data z objektu blob v oblasti Velká Británie – jih do služby SQL Data Warehouse v oblasti Velká Británie – jih a chcete zajistit, aby data neopustila Velkou Británii, vytvořte prostředí Azure IR v oblasti Velká Británie – jih a připojte obě propojené služby k tomuto prostředí IR.

### <a name="self-hosted-ir-location"></a>Umístění místního prostředí IR
Prostředí IR v místním prostředí má logickou registraci ve službě Data Factory a výpočetní prostředí používané k podpoře jeho funkcí poskytujete vy. Proto pro prostředí IR v místním prostředí neexistuje žádná explicitní vlastnost umístění. 

Pokud se prostředí IR v místním prostředí používá k provádění přesunu dat, extrahuje data ze zdroje a zapisuje je do cíle.

### <a name="azure-ssis-ir-location"></a>Umístění prostředí Azure-SSIS IR
Pro vysoký výkon pracovních postupů extrakce, transformace a načítání (ETL) je velmi důležitý výběr správného umístění prostředí Azure-SSIS IR.

- Umístění vašeho prostředí Azure-SSIS IR nemusí být stejné jako umístění datové továrny, ale mělo by odpovídat umístění vašeho vlastního serveru Azure SQL Database nebo spravované instance (Preview), na kterém chcete službu SSISDB hostovat. Díky tomu může prostředí Azure-SSIS Integration Runtime snadno získat přístup ke službě SSISDB bez nadměrných přenosů dat mezi různými umístěními.
- Pokud nemáte stávající server Azure SQL Database nebo spravované instance (Preview) pro hostování služby SSISDB, ale máte místní zdroje/cíle dat, měli byste vytvořit nový server Azure SQL Database nebo spravované instance (Preview) ve stejném umístění jako virtuální síť připojenou k vaší místní síti.  Můžete tak vytvořit prostředí Azure-SSIS IR s použitím nového serveru Azure SQL Database nebo spravované instance (Preview) a připojit se k této virtuální síti ve stejném umístění, čímž účinně omezíte přesuny dat mezi různými umístěními.
- Pokud se umístění existujícího serveru Azure SQL Database nebo spravované instance (Preview), který je hostitelem služby SSISDB, neshoduje s umístěním virtuální sítě připojené k vaší místní síti, vytvořte nejprve prostředí Azure-SSIS IR s použitím existujícího serveru Azure SQL Database nebo spravované instance (Preview), připojte se k jiné virtuální síti ve stejném umístění a pak nakonfigurujte připojení virtuální sítě k virtuální síti mezi různými umístěními.

Následující diagram znázorňuje nastavení umístění služby Data Factory a jejích prostředí Integration Runtime:

![Umístění prostředí Integration Runtime](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Určení toho, které prostředí IR používat

### <a name="copy-activity"></a>Aktivita kopírování

V případě aktivity kopírování jsou zapotřebí služby propojené se zdrojem a jímkou, které určují směr toku dat. Pomocí následující logiky se dá určit, která instance prostředí Integration Runtime se ke kopírování používá: 

- **Kopírování mezi dvěma cloudovými zdroji dat:** Pokud prostředí Azure IR používají služby propojené se zdrojem i jímkou, služba ADF použije regionální prostředí Azure IR, pokud jste ho určili, nebo automaticky určí umístění prostředí Azure IR, pokud jste zvolili automatický překlad umístění prostředí IR (výchozí nastavení), jak je popsáno v části [Umístění prostředí Integration Runtime](#integration-runtime-location).
- **Kopírování mezi cloudovým zdrojem dat a zdrojem dat v privátní síti**: Pokud služba propojená se zdrojem nebo jímkou odkazuje na prostředí IR v místním prostředí, aktivita kopírování se provádí v prostředí Integration Runtime v místním prostředí.
- **Kopírování mezi dvěma zdroji dat v privátní síti:** Služba propojená se zdrojem i jímkou musí odkazovat na stejnou instanci prostředí Integration Runtime a k provedení aktivity kopírování se použije toto prostředí Integration Runtime.

### <a name="lookup-and-getmetadata-activity"></a>Aktivity Lookup a GetMetadata

Aktivity Lookup a GetMetadata se provádí v prostředí Integration Runtime přidruženém k propojené službě úložiště dat.

### <a name="transformation-activity"></a>Aktivita transformace

Každá aktivita transformace má cílovou výpočetní propojenou službu, která odkazuje na určité prostředí Integration Runtime. Z této instance prostředí Integration Runtime se aktivita transformace odesílá.

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Vytvoření prostředí Integration Runtime v místním prostředí](create-self-hosted-integration-runtime.md)
- [Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Tento článek dále navazuje na tento kurz a obsahuje pokyny k použití spravované instance SQL Azure (Preview) a připojení IR k virtuální síti. 
