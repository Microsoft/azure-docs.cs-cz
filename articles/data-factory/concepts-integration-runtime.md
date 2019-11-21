---
title: Prostředí Integration Runtime v Azure Data Factory
description: Seznamte se s prostředím Integration Runtime v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: abnarain
ms.openlocfilehash: 0b137edbfb5ca439d4ba15614225ec0973511763
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218818"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Prostředí Integration Runtime v Azure Data Factory
Prostředí Integration Runtime (IR) je výpočetní infrastruktura, kterou Azure Data Factory používá k poskytování následujících funkcí integrace dat v různých síťových prostředích:

- **Data Flow**: Execute a [Data Flow](concepts-data-flow-overview.md) in managed Azure compute environment.  
- **Data movement**: Copy data across data stores in public network and data stores in private network (on-premises or virtual private network). Poskytuje podporu vestavěných konektorů, převodu formátu, mapování sloupců a výkonného a škálovatelného přenosu dat.
- **Activity dispatch**:  Dispatch and monitor transformation activities running on a variety of compute services such as Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server, and more.
- **Spouštění balíčků služby SSIS**: nativní spouštění balíčků služby SQL Server Integration Services (SSIS) ve spravovaném výpočetním prostředí Azure.

Aktivita ve službě Data Factory určuje akci, která se má provést. Propojená služba určuje cílové úložiště dat nebo výpočetní službu. Prostředí Integration Runtime představuje spojení mezi aktivitou a propojenými službami.  It is referenced by the linked service or activity, and provides the compute environment where the activity either runs on or gets dispatched from. Tímto způsobem se dá aktivita co nejefektivněji provést v oblasti, která je nejblíž cílovému úložišti dat nebo výpočetní službě, a zároveň vyhovět potřebám zabezpečení a dodržování předpisů.

## <a name="integration-runtime-types"></a>Typy prostředí Integration Runtime
Služba Data Factory nabízí tři typy prostředí Integration Runtime. Z nich si můžete vybrat ten, který nejlépe vyhovuje hledaným funkcím integrace dat a potřebám síťového prostředí.  Jedná se o tyto tři typy:

- Azure
- V místním prostředí
- Azure-SSIS

Následující tabulka obsahuje informace o podpoře funkcí a sítí pro každý typ prostředí Integration Runtime:

Typ prostředí IR | Veřejná síť | Privátní síť
------- | -------------- | ---------------
Azure | Tok dat<br/>Pohyb dat<br/>Odesílání aktivit | &nbsp;
V místním prostředí | Pohyb dat<br/>Odesílání aktivit | Pohyb dat<br/>Odesílání aktivit
Azure-SSIS | Spouštění balíčků služby SSIS | Spouštění balíčků služby SSIS

Následující diagram znázorňuje, jak se dají různá prostředí Integration Runtime používat v kombinaci, aby nabízela bohaté funkce integrace dat a podporu sítí:

![Různé typy prostředí Integration Runtime](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Azure Integration Runtime
Prostředí Azure Integration Runtime nabízí tyto funkce:

- Running Data Flows in Azure 
- Spouštění aktivity kopírování mezi úložišti dat v cloudu.
- Dispatching the following transform activities in public network: Databricks Notebook/ Jar/ Python activity, HDInsight Hive activity, HDInsight Pig activity, HDInsight MapReduce activity, HDInsight Spark activity, HDInsight Streaming activity, Machine Learning Batch Execution activity, Machine Learning Update Resource activities, Stored Procedure activity, Data Lake Analytics U-SQL activity, .NET custom activity, Web activity, Lookup activity, and Get Metadata activity.

### <a name="azure-ir-network-environment"></a>Síťové prostředí Azure IR
Azure Integration Runtime supports connecting to data stores and compute services with public accessible endpoints. Pro prostředí Azure Virtual Network používejte prostředí Integration Runtime v místním prostředí.

### <a name="azure-ir-compute-resource-and-scaling"></a>Výpočetní prostředky a škálování prostředí Azure IR
Prostředí Azure Integration Runtime poskytuje v rámci Azure plně spravované výpočetní prostředí bez serveru.  Nemusíte si dělat starosti se zřizováním infrastruktury, instalací softwaru, opravami ani škálováním kapacity.  Kromě toho platíte jenom po dobu, kdy prostředí opravdu používáte.

Prostředí Azure Integration Runtime poskytuje nativní výpočetní prostředí pro zabezpečené, spolehlivé a vysoce výkonné přesuny dat mezi cloudovými úložišti dat.  Můžete nastavit počet jednotek integrace dat, který se má použít v aktivitě kopírování, a velikost výpočetních prostředků Azure IR se elasticky vertikálně navýší, aniž byste museli velikost prostředí Azure Integration Runtime explicitně upravovat. 

Odesílání aktivit je nenáročná operace, která zajišťuje směrování aktivity do cílové výpočetní služby, takže pro tento scénář není nutné vertikálně navyšovat velikost výpočetních prostředků.

Informace o vytváření a konfiguraci prostředí najdete v návodu k vytvoření a konfiguraci prostředí Azure IR mezi příručkami s postupy. 

> [!NOTE] 
> Azure Integration runtime has properties related to Data Flow runtime, which defines the underlying compute infrastructure that would be used to run the data flows on. 

## <a name="self-hosted-integration-runtime"></a>Místní prostředí Integration Runtime
Prostředí IR v místním prostředí nabízí tyto funkce:

- Spouštění aktivity kopírování mezi úložišti dat v cloudu a úložištěm dat v privátní síti.
- Dispatching the following transform activities against compute resources in on-premises or Azure Virtual Network: HDInsight Hive activity (BYOC-Bring Your Own Cluster), HDInsight Pig activity (BYOC), HDInsight MapReduce activity (BYOC), HDInsight Spark activity (BYOC), HDInsight Streaming activity (BYOC), Machine Learning Batch Execution activity, Machine Learning Update Resource activities, Stored Procedure activity, Data Lake Analytics U-SQL activity, Custom activity (runs on Azure Batch), Lookup activity, and Get Metadata activity.

> [!NOTE] 
> Use self-hosted integration runtime to support data stores that requires bring-your-own driver such as SAP Hana, MySQL, etc.  For more information, see [supported data stores](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE) is a dependency of Self Hosted IR. Please make sure you have JRE installed on the same host.

### <a name="self-hosted-ir-network-environment"></a>Síťové prostředí místního prostředí IR
Pokud chcete v prostředí privátní sítě, které nenabízí přímý přístup z prostředí veřejného cloudu, provádět zabezpečenou integraci dat, můžete nainstalovat prostředí IR do místního prostředí za podnikovou bránu firewall nebo v rámci virtuální privátní sítě.  Prostředí Integration Runtime v místním prostředí navazuje jenom odchozí připojení HTTP do otevřeného internetu.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Výpočetní prostředky a škálování místního prostředí IR
Prostředí IR v místním prostředí je potřeba nainstalovat do místního počítače nebo virtuálního počítače v rámci privátní sítě. V současné době podporujeme spouštění prostředí IR v místním prostředí jenom v operačním systému Windows.  

Za účelem vysoké dostupnosti a škálovatelnosti můžete horizontálně navýšit kapacitu prostředí IR v místním prostředí tak, že logickou instanci přidružíte k víc místním počítačům v režimu aktivní-aktivní.  For more information, see how to [create and configure self-hosted IR](create-self-hosted-integration-runtime.md) article under how to guides for details.

## <a name="azure-ssis-integration-runtime"></a>Prostředí Azure-SSIS Integration Runtime
Pokud chcete navýšit a přesunout stávající úlohy služby SSIS, můžete vytvořit prostředí Azure SSIS IR pro nativní spouštění balíčků služby SSIS.

### <a name="azure-ssis-ir-network-environment"></a>Síťové prostředí Azure-SSIS IR
Prostředí Azure-SSIS IR se dá zřídit ve veřejné síti nebo privátní síti.  Připojením prostředí Azure-SSIS IR k virtuální síti připojené k vaší místní síti se dá získat podpora přístupu k místním datům.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Výpočetní prostředky a škálování prostředí Azure-SSIS IR
Prostředí Azure-SSIS IR je plně spravovaný cluster virtuálních počítačů Azure vyhrazených ke spouštění balíčků služby SSIS. You can bring your own Azure SQL Database or Managed Instance server to host the catalog of SSIS projects/packages (SSISDB) that is going to be attached to it. Můžete vertikálně navýšit výkon výpočetního prostředí tím, že určíte velikost uzlu a pak určíte počet uzlů v clusteru. Prostředí Azure-SSIS Integration Runtime můžete podle libosti zastavovat a spouštět, takže můžete mít pod kontrolou související náklady.

Další informace najdete v článku o vytváření a konfiguraci prostředí Azure-SSIS IR mezi příručkami s postupy.  Po vytvoření můžete existující balíčky služby SSIS nasazovat a spravovat s minimem změn pomocí známých nástrojů, jako jsou SQL Server Data Tools (SSDT) a SQL Server Management Studio (SSMS), stejně jako kdybyste službu SSIS používali místně.

Další informace o modulu runtime Azure-SSIS najdete v následujících článcích: 

- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny pro vytvoření Azure-SSIS IR a využívá databázi Azure SQL k hostování katalogu SSIS. 
- [Postup: Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) This article expands on the tutorial and provides instructions on using Azure SQL Database Managed Instance and joining the IR to a virtual network. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst informace o Azure-SSIS IR a popisy stavů ve vrácených informacích. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Tento článek ukazuje, jak zastavit, spustit nebo odebrat Azure-SSIS IR. Ukazuje také postup horizontálního navýšení kapacity Azure-SSIS IR přidáním více uzlů. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek obsahuje koncepční informace o připojení Azure-SSIS IR k virtuální síti Azure. Poskytuje také kroky pro využití webu Azure Portal ke konfiguraci virtuální sítě, aby se k ní prostředí Azure-SSIS IR mohlo připojit. 

## <a name="integration-runtime-location"></a>Umístění prostředí Integration Runtime
Do umístění služby Data Factory se ukládají metadata datové továrny a inicializují se z něj spouštění kanálu. Datová továrna mezitím může přistupovat k úložištím dat a výpočetním službám v jiných oblastech Azure za účelem přesouvání dat mezi úložišti dat nebo zpracování dat pomocí výpočetních služeb. Toto chování probíhá prostřednictvím [globálně dostupného prostředí IR](https://azure.microsoft.com/global-infrastructure/services/), aby se zajistilo dodržování předpisů pro data, efektivita a nižší náklady na celkový výstup sítě.

Umístění prostředí IR určuje umístění výpočetního prostředí back-end, tedy v podstatě umístění, kde se provádí přesun dat, odesílání aktivit a spouštění balíčku SSIS. Umístění prostředí IR se může lišit od umístění, do kterého patří datová továrna. 

### <a name="azure-ir-location"></a>Umístění prostředí Azure IR
Pro prostředí Azure IR můžete nastavit určité umístění a v tom případě bude přesun dat nebo odesílání aktivit probíhat v této konkrétní oblasti. 

If you choose to use the **auto-resolve Azure IR** which is the default, 

- V případě aktivity kopírování se služba ADF pokusí automaticky zjistit vaše úložiště dat jímky a zdrojové úložiště dat a zvolí nejlepší umístění buď ve stejné oblasti, pokud je dostupné nebo nejbližší na stejném území, nebo pokud nejsou zjistitelné, použije jako alternativu oblast datové továrny.

- For Lookup/GetMetadata/Delete activity execution (also known as Pipeline activities), transformation activity dispatching (also known as External activities), and authoring operations (test connection, browse folder list and table list, preview data), ADF will use the IR in the data factory region.

- For Data Flow, ADF will use the IR in the data factory region. 

  > [!TIP] 
  > A good practice would be to ensure Data flow runs in the same region as your corresponding data stores (if possible). You can either achieve this by auto-resolve Azure IR (if data store location is same as Data Factory location), or by creating a new Azure IR instance in the same region as your data stores and then execute the data flow on it. 

V zobrazení monitorování aktivit kanálu v uživatelském rozhraní nebo datové části monitorování aktivit můžete monitorovat, které umístění prostředí IR se při provádění aktivit použije.

>[!TIP]
>Pokud máte přísné požadavky na dodržování předpisů pro data a potřebujete zajistit, aby data neopustila určité území, můžete prostředí Azure IR explicitně vytvořit v určité oblasti a odkázat propojenou službu na toto prostředí IR pomocí vlastnosti ConnectVia. Například pokud chcete kopírovat data z objektu blob v oblasti Velká Británie – jih do služby SQL Data Warehouse v oblasti Velká Británie – jih a chcete zajistit, aby data neopustila Velkou Británii, vytvořte prostředí Azure IR v oblasti Velká Británie – jih a připojte obě propojené služby k tomuto prostředí IR.

### <a name="self-hosted-ir-location"></a>Umístění místního prostředí IR
Prostředí IR v místním prostředí má logickou registraci ve službě Data Factory a výpočetní prostředí používané k podpoře jeho funkcí poskytujete vy. Proto pro prostředí IR v místním prostředí neexistuje žádná explicitní vlastnost umístění. 

Pokud se prostředí IR v místním prostředí používá k provádění přesunu dat, extrahuje data ze zdroje a zapisuje je do cíle.

### <a name="azure-ssis-ir-location"></a>Umístění prostředí Azure-SSIS IR
Pro vysoký výkon pracovních postupů extrakce, transformace a načítání (ETL) je velmi důležitý výběr správného umístění prostředí Azure-SSIS IR.

- The location of your Azure-SSIS IR does not need be the same as the location of your data factory, but it should be the same as the location of your own Azure SQL Database/Managed Instance server where SSISDB is to be hosted. Díky tomu může prostředí Azure-SSIS Integration Runtime snadno získat přístup ke službě SSISDB bez nadměrných přenosů dat mezi různými umístěními.
- If you do not have an existing Azure SQL Database/Managed Instance server to host SSISDB, but you have on-premises data sources/destinations, you should create a new Azure SQL Database/Managed Instance server in the same location of a virtual network connected to your on-premises network.  This way, you can create your Azure-SSIS IR using the new Azure SQL Database/Managed Instance server and joining that virtual network, all in the same location, effectively minimizing data movements across different locations.
- If the location of your existing Azure SQL Database/Managed Instance server where SSISDB is hosted is not the same as the location of a virtual network connected to your on-premises network, first create your Azure-SSIS IR using an existing Azure SQL Database/Managed Instance server and joining another virtual network in the same location, and then configure a virtual network to virtual network connection between different locations.

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

### <a name="data-flow-activity"></a>Data Flow activity

Data Flow activity is executed on the integration runtime associated to it. 

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Create Azure integration runtime](create-azure-integration-runtime.md)
- [Vytvoření prostředí Integration Runtime v místním prostředí](create-self-hosted-integration-runtime.md)
- [Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). This article expands on the tutorial and provides instructions on using Azure SQL Database Managed Instance and joining the IR to a virtual network. 
