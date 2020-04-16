---
title: Prostředí Integration Runtime
description: Seznamte se s prostředím Integration Runtime v Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/26/2020
ms.openlocfilehash: ffa348c796a4d9d4e3bdb8e7ce18ba0eb82e17ad
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418374"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Prostředí Integration Runtime v Azure Data Factory 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Prostředí Integration Runtime (IR) je výpočetní infrastruktura, kterou Azure Data Factory používá k poskytování následujících funkcí integrace dat v různých síťových prostředích:

- **Tok dat:** Spusťte [tok dat](concepts-data-flow-overview.md) ve spravovaném výpočetním prostředí Azure.  
- **Přesun dat**: Kopírování dat napříč úložišti dat ve veřejných sítích a úložišti dat v privátní síti (místní nebo virtuální privátní sítě). Poskytuje podporu vestavěných konektorů, převodu formátu, mapování sloupců a výkonného a škálovatelného přenosu dat.
- **Odeslání aktivity:** Odeslání a monitorování transformačních aktivit spuštěných na různých výpočetních službách, jako jsou Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server a další.
- **Spouštění balíčků služby SSIS**: nativní spouštění balíčků služby SQL Server Integration Services (SSIS) ve spravovaném výpočetním prostředí Azure.

Aktivita ve službě Data Factory určuje akci, která se má provést. Propojená služba určuje cílové úložiště dat nebo výpočetní službu. Prostředí Integration Runtime představuje spojení mezi aktivitou a propojenými službami.  Odkazuje propojená služba nebo aktivita a poskytuje výpočetní prostředí, kde aktivita buď běží na nebo získá odeslány z. Tímto způsobem se dá aktivita co nejefektivněji provést v oblasti, která je nejblíž cílovému úložišti dat nebo výpočetní službě, a zároveň vyhovět potřebám zabezpečení a dodržování předpisů.

## <a name="integration-runtime-types"></a>Typy prostředí Integration Runtime

Data Factory nabízí tři typy integrace Runtime (IR) a měli byste zvolit typ, který nejlépe slouží možnosti integrace dat a potřeby síťového prostředí, které hledáte.  Jedná se o tyto tři typy:

- Azure
- V místním prostředí
- Azure-SSIS

Následující tabulka obsahuje informace o podpoře funkcí a sítí pro každý typ prostředí Integration Runtime:

Typ prostředí IR | Veřejná síť | Privátní síť
------- | -------------- | ---------------
Azure | Data Flow<br/>Přesuny dat<br/>Odesílání aktivit | &nbsp;
V místním prostředí | Přesuny dat<br/>Odesílání aktivit | Přesuny dat<br/>Odesílání aktivit
Azure-SSIS | Spouštění balíčků služby SSIS | Spouštění balíčků služby SSIS

Následující diagram znázorňuje, jak se dají různá prostředí Integration Runtime používat v kombinaci, aby nabízela bohaté funkce integrace dat a podporu sítí:

![Různé typy prostředí Integration Runtime](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Prostředí Azure Integration Runtime

Runtime integrace Azure může:

- Běh toků dat v Azure 
- Spouštění aktivity kopírování mezi úložišti dat v cloudu.
- Odeslání následujících aktivit transformace ve veřejné síti: Databricks Notebook / Jar/ Python aktivity, HDInsight Hive aktivity, HDInsight Pig aktivita, HDInsight MapReduce aktivity, HDInsight Spark činnost, HDInsight Streaming aktivity, Machine Learning Batch Execution aktivity, Machine Learning Update Resource aktivity, uložené procedury aktivity, Data Lake Analytics U-SQL aktivity, .NET vlastní aktivity, Web aktivity, Vyhledávání aktivity a Získat metadata aktivity.

### <a name="azure-ir-network-environment"></a>Síťové prostředí Azure IR

Azure Integration Runtime podporuje připojení k úložištím dat a vypočítává služby s veřejně přístupnými koncovými body. Pro prostředí Azure Virtual Network používejte prostředí Integration Runtime v místním prostředí.

### <a name="azure-ir-compute-resource-and-scaling"></a>Výpočetní prostředky a škálování prostředí Azure IR
Prostředí Azure Integration Runtime poskytuje v rámci Azure plně spravované výpočetní prostředí bez serveru.  Nemusíte se starat o poskytování infrastruktury, instalaci softwaru, opravy nebo škálování kapacity.  Kromě toho platíte jenom po dobu, kdy prostředí opravdu používáte.

Prostředí Azure Integration Runtime poskytuje nativní výpočetní prostředí pro zabezpečené, spolehlivé a vysoce výkonné přesuny dat mezi cloudovými úložišti dat.  Můžete nastavit počet jednotek integrace dat, který se má použít v aktivitě kopírování, a velikost výpočetních prostředků Azure IR se elasticky vertikálně navýší, aniž byste museli velikost prostředí Azure Integration Runtime explicitně upravovat. 

Odeslání aktivity je zjednodušená operace pro směrování aktivity do cílové výpočetní služby, takže není nutné vertikálně navýšit velikost výpočetního prostředí pro tento scénář.

Informace o vytváření a konfiguraci Azure IR, odkazovat, jak vytvořit a nakonfigurovat Azure IR v rámci návodu. 

> [!NOTE] 
> Runtime integrace Azure má vlastnosti související s runtime toku dat, který definuje základní výpočetní infrastrukturu, která by se použila ke spuštění toků dat. 

## <a name="self-hosted-integration-runtime"></a>Prostředí Integration Runtime v místním prostředí

Prostředí IR v místním prostředí nabízí tyto funkce:

- Spouštění aktivity kopírování mezi úložišti dat v cloudu a úložištěm dat v privátní síti.
- Odeslání následujících aktivit transformace proti výpočetním prostředkům v místním prostředí nebo virtuální síti Azure: aktivita HDInsight Hive (BYOC-Bring Your Own Cluster), HDInsight Pig activity (BYOC), HDInsight MapReduce activity (BYOC), HDInsight Spark activity (BYOC), HDInsight Streaming activity (BYOC), Machine Learning Batch Execution activity, Machine Learning Update Resource activities, Stored Procedure Activity, Data Lake Analytics U-SQL activity, Custom activity (runs on Azure Batch) , aktivita vyhledávání a aktivita získat metadata.

> [!NOTE] 
> Použijte modul runtime integrace s vlastním hostitelem pro podporu datových úložišť, která vyžadují vlastní ovladač, jako je SAP Hana, MySQL atd.  Další informace naleznete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE) je závislost vlastní hostované infračerveného přenosového prostředí. Ujistěte se, že máte jre nainstalován na stejném hostiteli.

### <a name="self-hosted-ir-network-environment"></a>Síťové prostředí místního prostředí IR

Pokud chcete bezpečně provádět integraci dat v prostředí privátní sítě, které nemá přímý přímý výhled z prostředí veřejného cloudu, můžete nainstalovat infračervený přenos s vlastním hostitelem v místním prostředí za podnikovou bránou firewall nebo uvnitř virtuální privátní sítě.  Prostředí Integration Runtime v místním prostředí navazuje jenom odchozí připojení HTTP do otevřeného internetu.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Výpočetní prostředky a škálování místního prostředí IR

Nainstalujte infračervený přenos s vlastním hostitelem v místním počítači nebo virtuálním počítači v privátní síti. V současné době podporujeme spouštění prostředí IR v místním prostředí jenom v operačním systému Windows.  

Za účelem vysoké dostupnosti a škálovatelnosti můžete horizontálně navýšit kapacitu prostředí IR v místním prostředí tak, že logickou instanci přidružíte k víc místním počítačům v režimu aktivní-aktivní.  Další informace naleznete v tématu [jak vytvořit a nakonfigurovat článek infračerveného serveru](create-self-hosted-integration-runtime.md) s vlastním hostitelem v části Návody k podrobnostem.

## <a name="azure-ssis-integration-runtime"></a>Prostředí Azure-SSIS Integration Runtime

Pokud chcete navýšit a přesunout stávající úlohy služby SSIS, můžete vytvořit prostředí Azure SSIS IR pro nativní spouštění balíčků služby SSIS.

### <a name="azure-ssis-ir-network-environment"></a>Síťové prostředí Azure-SSIS IR

Prostředí Azure-SSIS IR se dá zřídit ve veřejné síti nebo privátní síti.  Připojením prostředí Azure-SSIS IR k virtuální síti připojené k vaší místní síti se dá získat podpora přístupu k místním datům.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Výpočetní prostředky a škálování prostředí Azure-SSIS IR

Prostředí Azure-SSIS IR je plně spravovaný cluster virtuálních počítačů Azure vyhrazených ke spouštění balíčků služby SSIS. Můžete si přinést vlastní Azure SQL Database nebo server spravované instance pro hostování katalogu projektů/balíčků SSIS (SSISDB), který k němu bude připojen. Můžete vertikálně navýšit výkon výpočetního prostředí tím, že určíte velikost uzlu a pak určíte počet uzlů v clusteru. Prostředí Azure-SSIS Integration Runtime můžete podle libosti zastavovat a spouštět, takže můžete mít pod kontrolou související náklady.

Další informace najdete v článku o vytváření a konfiguraci prostředí Azure-SSIS IR mezi příručkami s postupy.  Po vytvoření můžete existující balíčky služby SSIS nasazovat a spravovat s minimem změn pomocí známých nástrojů, jako jsou SQL Server Data Tools (SSDT) a SQL Server Management Studio (SSMS), stejně jako kdybyste službu SSIS používali místně.

Další informace o modulu runtime Azure-SSIS najdete v následujících článcích: 

- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny k vytvoření infračerveného serveru Azure-SSIS inizrcadla a používá Azure SQL Database k hostování katalogu SSIS. 
- [Postup: Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) Tento článek rozšiřuje kurz a obsahuje pokyny k použití Azure SQL Database Spravované instance a připojení infračerveného počítače k virtuální síti. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst informace o Azure-SSIS IR a popisy stavů ve vrácených informacích. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Tento článek ukazuje, jak zastavit, spustit nebo odebrat Azure-SSIS IR. Ukazuje také postup horizontálního navýšení kapacity Azure-SSIS IR přidáním více uzlů. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek obsahuje koncepční informace o připojení Azure-SSIS IR k virtuální síti Azure. Poskytuje také kroky pro využití webu Azure Portal ke konfiguraci virtuální sítě, aby se k ní prostředí Azure-SSIS IR mohlo připojit. 

## <a name="integration-runtime-location"></a>Umístění prostředí Integration Runtime

Do umístění služby Data Factory se ukládají metadata datové továrny a inicializují se z něj spouštění kanálu. Datová továrna mezitím může přistupovat k úložištím dat a výpočetním službám v jiných oblastech Azure za účelem přesouvání dat mezi úložišti dat nebo zpracování dat pomocí výpočetních služeb. Toto chování probíhá prostřednictvím [globálně dostupného prostředí IR](https://azure.microsoft.com/global-infrastructure/services/), aby se zajistilo dodržování předpisů pro data, efektivita a nižší náklady na celkový výstup sítě.

Umístění prostředí IR určuje umístění výpočetního prostředí back-end, tedy v podstatě umístění, kde se provádí přesun dat, odesílání aktivit a spouštění balíčku SSIS. Umístění prostředí IR se může lišit od umístění, do kterého patří datová továrna. 

### <a name="azure-ir-location"></a>Umístění prostředí Azure IR

- Pro aktivitu kopírování adf vynaloží maximální úsilí k automatickému rozpoznání umístění úložiště dat jímky a poté použije infračervený přenos ve stejné oblasti, pokud je k dispozici, nebo v nejbližší oblasti ve stejné zeměpisné oblasti; Pokud oblast úložiště dat jímky není zjistitelná, infračervený přenos v oblasti factory dat jako alternativa se používá.

  Například máte továrnu vytvořenou ve východní USA, 
  
  - Při kopírování dat do objektu Blob Azure v západní USA, pokud ADF úspěšně zjistil, že objekt blob je v západní USA, kopírování aktivita se spustí na infračervený přenos v západní USA; Pokud se detekce oblasti nezdaří, aktivita kopírování se spustí na infračerveného vyhledávání v USA – východ.
  - Při kopírování dat do Salesforce, jehož oblast není zjistitelná, je aktivita kopírování spuštěna na infračerveném odhlučnění v USA ve východním USA.

- Pro aktivitu kopírování adf vynakládá maximální úsilí k automatickému rozpoznání jímky a zdrojové úložiště dat zvolit nejlepší umístění, buď ve stejné oblasti (pokud je k dispozici), nebo nejbližší ve stejné zeměpisné oblasti, nebo pokud není zjistitelné použít oblast factory dat jako alternativu.

- Pro vyhledávání/GetMetadata/Odstranění provádění aktivit (označované také jako aktivity kanálu), expedice aktivity transformace (označované také jako Externí aktivity) a operace vytváření (testovací připojení, procházení seznamu složek a seznam tabulek, data náhledu) používá adf infračervený přenos v oblasti továrny na data.

- Pro tok dat adf používá infračervený přenos v oblasti factory dat. 

  > [!TIP] 
  > Osvědčeným postupem by bylo zajistit, aby tok dat běží ve stejné oblasti jako vaše odpovídající úložiště dat (pokud je to možné). Toho můžete dosáhnout buď automatickým překladem azure ir (pokud umístění úložiště dat je stejné jako umístění datové továrny), nebo vytvořením nové instance Azure IR ve stejné oblasti jako úložiště dat a pak spustit tok dat na něm. 

V zobrazení monitorování aktivit kanálu v uživatelském rozhraní nebo datové části monitorování aktivit můžete monitorovat, které umístění prostředí IR se při provádění aktivit použije.

### <a name="self-hosted-ir-location"></a>Umístění místního prostředí IR

Prostředí IR v místním prostředí má logickou registraci ve službě Data Factory a výpočetní prostředí používané k podpoře jeho funkcí poskytujete vy. Proto pro prostředí IR v místním prostředí neexistuje žádná explicitní vlastnost umístění. 

Pokud se prostředí IR v místním prostředí používá k provádění přesunu dat, extrahuje data ze zdroje a zapisuje je do cíle.

### <a name="azure-ssis-ir-location"></a>Umístění prostředí Azure-SSIS IR

Pro vysoký výkon pracovních postupů extrakce, transformace a načítání (ETL) je velmi důležitý výběr správného umístění prostředí Azure-SSIS IR.

- Umístění zařízení Azure-SSIS Ir nemusí být stejné jako umístění vaší datové továrny, ale mělo by být stejné jako umístění vlastní databáze Azure SQL Database nebo serveru spravované instance, kde má být hostována SSISDB. Díky tomu může prostředí Azure-SSIS Integration Runtime snadno získat přístup ke službě SSISDB bez nadměrných přenosů dat mezi různými umístěními.
- Pokud nemáte existující Azure SQL Database nebo server spravované instance pro hostování SSISDB, ale máte místní zdroje dat nebo cíle, měli byste vytvořit novou Azure SQL Database nebo server spravované instance ve stejném umístění virtuální sítě připojené k místní síti.  Tímto způsobem můžete vytvořit Azure-SSIS IR pomocí nové azure sql databáze nebo spravované instance serveru a připojení k této virtuální síti, to vše ve stejném umístění, efektivně minimalizuje přesuny dat v různých umístěních.
- Pokud umístění vašeho stávajícího serveru Azure SQL Database nebo Managed Instance, kde je sisisdb hostováno, není stejné jako umístění virtuální sítě připojené k vaší místní síti, nejprve vytvořte zařízení Azure-SSIS IR pomocí existující databáze Azure NEBO serveru spravované instance a připojte se k jiné virtuální síti ve stejném umístění a pak nakonfigurujte virtuální síť na připojení virtuální sítě mezi různými umístěními.

Následující diagram znázorňuje nastavení umístění služby Data Factory a jejích prostředí Integration Runtime:

![Umístění prostředí Integration Runtime](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Určení toho, které prostředí IR používat

### <a name="copy-activity"></a>Aktivita kopírování

V případě aktivity kopírování jsou zapotřebí služby propojené se zdrojem a jímkou, které určují směr toku dat. Pomocí následující logiky se dá určit, která instance prostředí Integration Runtime se ke kopírování používá: 

- **Kopírování mezi dvěma zdroji cloudových dat**: když zdrojové i jímací propojené služby používají Azure IR, ADF používá místní Azure IR, pokud jste zadali, nebo automaticky určit umístění Azure IR, pokud zvolíte automatické řešení IR (výchozí), jak je popsáno v části [umístění za běhu integrace.](#integration-runtime-location)
- **Kopírování mezi cloudovým zdrojem dat a zdrojem dat v privátní síti**: Pokud služba propojená se zdrojem nebo jímkou odkazuje na prostředí IR v místním prostředí, aktivita kopírování se provádí v prostředí Integration Runtime v místním prostředí.
- **Kopírování mezi dvěma zdroji dat v privátní síti**: zdrojová i jímka propojená služba musí ukazovat na stejnou instanci integračního běhu a že integrační runtime se používá ke spuštění aktivity kopírování.

### <a name="lookup-and-getmetadata-activity"></a>Aktivity Lookup a GetMetadata

Aktivity Lookup a GetMetadata se provádí v prostředí Integration Runtime přidruženém k propojené službě úložiště dat.

### <a name="external-transformation-activity"></a>Externí transformační činnost

Každá externí transformační aktivita, která využívá externí výpočetní stroj, má cílovou výpočetní propojenou službu, která odkazuje na modul runtime integrace. Tato instancí prostředí runtime integrace určuje umístění, odkud je odeslána tato externí ručně kódovaná transformační aktivita.

### <a name="data-flow-activity"></a>Aktivita toku dat

Aktivity toku dat se spouštějí v době integration Azure, která je k němu přidružená. Výpočetní prostředky Spark používané datovými toky jsou určeny vlastnostmi toku dat ve vašem prostředí Azure Integration Runtime a jsou plně spravované ADF.

## <a name="next-steps"></a>Další kroky

Viz následující články:

- [Vytvoření runtime integrace Azure](create-azure-integration-runtime.md)
- [Vytvoření prostředí Integration Runtime v místním prostředí](create-self-hosted-integration-runtime.md)
- [Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Tento článek rozšiřuje kurz a obsahuje pokyny k použití Azure SQL Database Spravované instance a připojení infračerveného počítače k virtuální síti. 
