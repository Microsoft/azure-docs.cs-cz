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
ms.date: 07/14/2020
ms.openlocfilehash: b899485589f19a5b74d6d22b4e5dae5fbf3ff604
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827533"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Prostředí Integration Runtime v Azure Data Factory 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Prostředí Integration Runtime (IR) je výpočetní infrastruktura, kterou Azure Data Factory používá k poskytování následujících funkcí integrace dat v různých síťových prostředích:

- **Tok dat**: spuštění [toku dat](concepts-data-flow-overview.md) ve spravovaném prostředí Azure Compute.  
- **Přesun dat**: kopírování dat napříč úložišti dat ve veřejné síti a úložištích dat v privátní síti (místní nebo virtuální privátní síť). Poskytuje podporu vestavěných konektorů, převodu formátu, mapování sloupců a výkonného a škálovatelného přenosu dat.
- **Odesílání aktivit**: odesílání a sledování transformačních aktivit běžících na různých výpočetních službách, jako je Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server a další.
- **Spouštění balíčků služby SSIS**: nativní spouštění balíčků služby SQL Server Integration Services (SSIS) ve spravovaném výpočetním prostředí Azure.

Aktivita ve službě Data Factory určuje akci, která se má provést. Propojená služba určuje cílové úložiště dat nebo výpočetní službu. Prostředí Integration Runtime představuje spojení mezi aktivitou a propojenými službami.  Na ni odkazuje propojená služba nebo aktivita a poskytuje výpočetní prostředí, ve kterém se aktivita buď spustí, nebo se z něj odešle. Tímto způsobem se dá aktivita co nejefektivněji provést v oblasti, která je nejblíž cílovému úložišti dat nebo výpočetní službě, a zároveň vyhovět potřebám zabezpečení a dodržování předpisů.

Prostředí Integration runtime se dají vytvořit v Azure Data Factorym uživatelském rozhraní prostřednictvím [centra pro správu](author-management-hub.md) a všech aktivit, datových sad nebo toků dat, které na ně odkazují.

## <a name="integration-runtime-types"></a>Typy prostředí Integration Runtime

Data Factory nabízí tři typy Integration Runtime (IR) a měli byste zvolit typ, který nejlépe obsluhuje možnosti integrace dat a síťové prostředí, které hledáte.  Jedná se o tyto tři typy:

- Azure
- V místním prostředí
- Azure-SSIS

Následující tabulka obsahuje informace o podpoře funkcí a sítí pro každý typ prostředí Integration Runtime:

Typ prostředí IR | Veřejná síť | Privátní síť
------- | -------------- | ---------------
Azure | Data Flow<br/>Přesuny dat<br/>Odesílání aktivit | Data Flow<br/>Přesuny dat<br/>Odesílání aktivit
V místním prostředí | Přesuny dat<br/>Odesílání aktivit | Přesuny dat<br/>Odesílání aktivit
Azure-SSIS | Spouštění balíčků služby SSIS | Spouštění balíčků služby SSIS


## <a name="azure-integration-runtime"></a>Prostředí Azure Integration Runtime

Prostředí Azure Integration runtime může:

- Spouštění toků dat v Azure 
- Spustit aktivitu kopírování mezi úložišti cloudových dat
- Odeslání následujících transformačních aktivit ve veřejné síti: datacihly/aktivita jar/jar/Python, aktivita v rámci prostředí HDInsight, aktivita prasete HDInsight, aktivita pro MapReduce HDInsight, aktivita HDInsight Spark, aktivita pro streamování HDInsight, aktivita streamování ve službě HDInsight Machine Learning, aktivita Machine Learning spouštění v prostředí .NET, vlastní aktivita aktivity Data Lake Analytics prostředků, aktivita pro hledání dat

### <a name="azure-ir-network-environment"></a>Síťové prostředí Azure IR

Azure Integration Runtime podporuje připojení k úložištím dat a výpočetní služby s veřejnými dostupnými koncovými body. Povolení spravovaných Virtual Network Azure Integration Runtime podporuje připojení k úložištím dat pomocí služby privátního propojení v prostředí privátní sítě.

### <a name="azure-ir-compute-resource-and-scaling"></a>Výpočetní prostředky a škálování prostředí Azure IR
Prostředí Azure Integration Runtime poskytuje v rámci Azure plně spravované výpočetní prostředí bez serveru.  Nemusíte si dělat starosti se zřizováním infrastruktury, instalací softwaru, opravami nebo škálováním kapacity.  Kromě toho platíte jenom po dobu, kdy prostředí opravdu používáte.

Prostředí Azure Integration Runtime poskytuje nativní výpočetní prostředí pro zabezpečené, spolehlivé a vysoce výkonné přesuny dat mezi cloudovými úložišti dat.  Můžete nastavit počet jednotek integrace dat, který se má použít v aktivitě kopírování, a velikost výpočetních prostředků Azure IR se elasticky vertikálně navýší, aniž byste museli velikost prostředí Azure Integration Runtime explicitně upravovat. 

Odeslání aktivity je odlehčená operace pro směrování aktivity do cílové výpočetní služby, takže nemusíte škálovat výpočetní velikost pro tento scénář.

Informace o vytváření a konfiguraci Azure IR najdete v tématu [jak vytvořit a nakonfigurovat Azure Integration runtime](create-azure-integration-runtime.md). 

> [!NOTE] 
> Prostředí Azure Integration runtime má vlastnosti související s modulem runtime toku dat, který definuje základní výpočetní infrastrukturu, která by se použila ke spouštění toků dat. 

## <a name="self-hosted-integration-runtime"></a>Prostředí Integration Runtime v místním prostředí

Prostředí IR v místním prostředí nabízí tyto funkce:

- Spouštění aktivity kopírování mezi úložišti dat v cloudu a úložištěm dat v privátní síti.
- Odesílání následujících transformačních aktivit pro výpočetní prostředky v místním prostředí nebo v Azure Virtual Network: aktivita v rámci prostředí HDInsight (BYOC-Přineste si vlastní cluster), aktivita prasete služby HDInsight (BYOC), aktivita MapReduce služby HDInsight (BYOC), aktivita HDInsight Spark (BYOC), aktivita streamování HDInsight (BYOC), aktivita spuštění služby Batch, Machine Learning aktualizovat aktivity prostředků, aktivity uložených procedur, Data Lake Analytics Machine Learning aktivity U-SQL, vlastní aktivita (spouští se v Azure Batch) , Aktivity vyhledávání a získání aktivity metadat.

> [!NOTE] 
> Pomocí místního prostředí Integration runtime můžete podporovat úložiště dat, která vyžadují vlastní ovladač, jako je SAP HANA, MySQL atd.  Další informace najdete v tématu [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE) je závislost samostatně hostovaného IR. Ujistěte se prosím, že máte JRE nainstalované na stejném hostiteli.

### <a name="self-hosted-ir-network-environment"></a>Síťové prostředí místního prostředí IR

Pokud chcete integrovat data bezpečně v prostředí privátní sítě, které nemá přímý dohled nad veřejným cloudovým prostředím, můžete nainstalovat místní prostředí IR v místním prostředí za vaší firemní bránou firewall nebo do virtuální privátní sítě.  Prostředí Integration Runtime v místním prostředí navazuje jenom odchozí připojení HTTP do otevřeného internetu.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Výpočetní prostředky a škálování místního prostředí IR

Instalace prostředí IR v místním prostředí v místním počítači nebo virtuálním počítači v privátní síti. V současné době podporujeme spouštění prostředí IR v místním prostředí jenom v operačním systému Windows.  

Za účelem vysoké dostupnosti a škálovatelnosti můžete horizontálně navýšit kapacitu prostředí IR v místním prostředí tak, že logickou instanci přidružíte k víc místním počítačům v režimu aktivní-aktivní.  Další informace najdete v tématu Postup [Vytvoření a konfigurace místního hostovaného](create-self-hosted-integration-runtime.md) článku v části Návod k zobrazení podrobností.

## <a name="azure-ssis-integration-runtime"></a>Prostředí Azure-SSIS Integration Runtime

Pokud chcete navýšit a přesunout stávající úlohy služby SSIS, můžete vytvořit prostředí Azure SSIS IR pro nativní spouštění balíčků služby SSIS.

### <a name="azure-ssis-ir-network-environment"></a>Síťové prostředí Azure-SSIS IR

Prostředí Azure-SSIS IR se dá zřídit ve veřejné síti nebo privátní síti.  Připojením prostředí Azure-SSIS IR k virtuální síti připojené k vaší místní síti se dá získat podpora přístupu k místním datům.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Výpočetní prostředky a škálování prostředí Azure-SSIS IR

Prostředí Azure-SSIS IR je plně spravovaný cluster virtuálních počítačů Azure vyhrazených ke spouštění balíčků služby SSIS. Můžete převést vlastní Azure SQL Database nebo spravovanou instanci SQL pro katalog projektů a balíčků SSIS (SSISDB). Můžete vertikálně navýšit výkon výpočetního prostředí tím, že určíte velikost uzlu a pak určíte počet uzlů v clusteru. Prostředí Azure-SSIS Integration Runtime můžete podle libosti zastavovat a spouštět, takže můžete mít pod kontrolou související náklady.

Další informace najdete v článku o vytváření a konfiguraci prostředí Azure-SSIS IR mezi příručkami s postupy.  Po vytvoření můžete existující balíčky služby SSIS nasazovat a spravovat s minimem změn pomocí známých nástrojů, jako jsou SQL Server Data Tools (SSDT) a SQL Server Management Studio (SSMS), stejně jako kdybyste službu SSIS používali místně.

Další informace o modulu runtime Azure-SSIS najdete v následujících článcích: 

- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny k vytvoření Azure-SSIS IR a používá Azure SQL Database k hostování katalogu SSIS. 
- [Postup: Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) Tento článek se rozbalí v tomto kurzu a poskytne pokyny k použití spravované instance SQL a připojení IR k virtuální síti. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst informace o Azure-SSIS IR a popisy stavů ve vrácených informacích. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Tento článek ukazuje, jak zastavit, spustit nebo odebrat Azure-SSIS IR. Ukazuje také postup horizontálního navýšení kapacity Azure-SSIS IR přidáním více uzlů. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek obsahuje koncepční informace o připojení Azure-SSIS IR k virtuální síti Azure. Poskytuje také kroky pro využití webu Azure Portal ke konfiguraci virtuální sítě, aby se k ní prostředí Azure-SSIS IR mohlo připojit. 

## <a name="integration-runtime-location"></a>Umístění prostředí Integration Runtime

### <a name="relationship-between-factory-location-and-ir-location"></a>Vztah mezi umístěním továrny a umístěním IR

Když zákazník vytvoří instanci objektu pro vytváření dat, musí určit umístění pro datovou továrnu. Do umístění služby Data Factory se ukládají metadata datové továrny a inicializují se z něj spouštění kanálu. Metadata pro objekt pro vytváření se ukládají jenom v oblasti výběru zákazníka a neukládají se do jiných oblastí.

Datová továrna mezitím může přistupovat k úložištím dat a výpočetním službám v jiných oblastech Azure za účelem přesouvání dat mezi úložišti dat nebo zpracování dat pomocí výpočetních služeb. Toto chování probíhá prostřednictvím [globálně dostupného prostředí IR](https://azure.microsoft.com/global-infrastructure/services/), aby se zajistilo dodržování předpisů pro data, efektivita a nižší náklady na celkový výstup sítě.

Umístění prostředí IR určuje umístění výpočetního prostředí back-end, tedy v podstatě umístění, kde se provádí přesun dat, odesílání aktivit a spouštění balíčku SSIS. Umístění prostředí IR se může lišit od umístění, do kterého patří datová továrna. 

### <a name="azure-ir-location"></a>Umístění prostředí Azure IR

Můžete nastavit určité umístění Azure IR. v takovém případě se spuštění nebo odeslání aktivity provede v konkrétní oblasti.

Pokud se rozhodnete použít Azure IR automatického řešení ve veřejné síti, což je výchozí nastavení,

- V případě aktivity kopírování bude ADF vysílat automatické rozpoznávání umístění úložiště dat jímky a pak bude používat IR v obou oblastech, pokud je k dispozici, nebo co nejblíže ve stejné geografické oblasti. Pokud oblast úložiště dat jímky není zjistitelná, použije se jako alternativa možnost IR v oblasti Datové továrny.

  Například máte vytvořenou továrnu v Východní USA, 
  
  - Při kopírování dat do objektu blob Azure v Západní USA, pokud se ADF úspěšně zjistilo, že je objekt BLOB v Západní USA, aktivita kopírování se spustí v prostředí IR v Západní USA; Pokud se detekce oblasti nezdařila, aktivita kopírování se spustí v prostředí IR v Východní USA.
  - Při kopírování dat do Salesforce, u kterých není oblast zjistitelná, se aktivita kopírování spustí na IR v Východní USA.

  >[!TIP] 
  >Pokud máte přísné požadavky na dodržování předpisů pro data a potřebujete zajistit, aby data neopustila určité území, můžete prostředí Azure IR explicitně vytvořit v určité oblasti a odkázat propojenou službu na toto prostředí IR pomocí vlastnosti ConnectVia. Pokud třeba chcete kopírovat data z objektu BLOB v Velká Británie – jih do služby Azure synapse Analytics v Velká Británie – jih a chcete zajistit, aby data nezůstala Velká Británie, vytvořte v Velká Británie – jih Azure IR a propojte obě propojené služby s tímto IR.

- Pro provádění aktivit vyhledávání/GetMetadata/Delete (označuje se také jako aktivity kanálu), deoprava transformačních aktivit (označované také jako externí aktivity) a vytváření operací (testovací připojení, seznam složek a seznam tabulek, náhled dat), ADF používá v oblasti Datové továrny IR.

- V případě toku dat používá ADF v oblasti Datové továrny IR. 

  > [!TIP] 
  > Dobrým postupem je zajistit, aby tok dat běžel ve stejné oblasti jako vaše odpovídající úložiště dat (Pokud je to možné). Toho můžete dosáhnout buď automatickým přeložením Azure IR (Pokud je umístění úložiště dat stejné jako Data Factory umístění), nebo vytvořením nové instance Azure IR ve stejné oblasti jako úložiště dat a následným spuštěním toku dat. 

Pokud povolíte možnost spravované Virtual Network pro automatické řešení Azure IR, ADF používá v oblasti Datové továrny IR. 

V zobrazení monitorování aktivit kanálu v uživatelském rozhraní nebo datové části monitorování aktivit můžete monitorovat, které umístění prostředí IR se při provádění aktivit použije.

### <a name="self-hosted-ir-location"></a>Umístění místního prostředí IR

Prostředí IR v místním prostředí má logickou registraci ve službě Data Factory a výpočetní prostředí používané k podpoře jeho funkcí poskytujete vy. Proto pro prostředí IR v místním prostředí neexistuje žádná explicitní vlastnost umístění. 

Pokud se prostředí IR v místním prostředí používá k provádění přesunu dat, extrahuje data ze zdroje a zapisuje je do cíle.

### <a name="azure-ssis-ir-location"></a>Umístění prostředí Azure-SSIS IR

Pro vysoký výkon pracovních postupů extrakce, transformace a načítání (ETL) je velmi důležitý výběr správného umístění prostředí Azure-SSIS IR.

- Umístění vašeho Azure-SSIS IR nemusí být stejné jako umístění vaší datové továrny, ale mělo by to být stejné jako umístění vaší vlastní Azure SQL Database nebo spravované instance SQL, kde SSISDB. Díky tomu může prostředí Azure-SSIS Integration Runtime snadno získat přístup ke službě SSISDB bez nadměrných přenosů dat mezi různými umístěními.
- Pokud nemáte existující SQL Database nebo spravovanou instanci SQL, ale máte místní zdroje dat nebo cíle, měli byste vytvořit novou Azure SQL Database nebo spravovanou instanci SQL ve stejném umístění jako virtuální síť připojenou k vaší místní síti.  Tímto způsobem můžete vytvořit Azure-SSIS IR pomocí nové Azure SQL Database nebo spravované instance SQL a připojit se k této virtuální síti, a to všechno ve stejném umístění a efektivně tak minimalizovat přesuny dat mezi různými umístěními.
- Pokud umístění vaší existující Azure SQL Database nebo spravované instance SQL není stejné jako umístění virtuální sítě připojené k vaší místní síti, vytvořte nejprve Azure-SSIS IR pomocí existující Azure SQL Database nebo spravované instance SQL a připojení k jiné virtuální síti ve stejném umístění a pak nakonfigurujte virtuální síť na připojení k virtuální síti mezi různými umístěními.

Následující diagram znázorňuje nastavení umístění služby Data Factory a jejích prostředí Integration Runtime:

![Umístění prostředí Integration Runtime](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Určení toho, které prostředí IR používat

### <a name="copy-activity"></a>Aktivita kopírování

V případě aktivity kopírování jsou zapotřebí služby propojené se zdrojem a jímkou, které určují směr toku dat. Pomocí následující logiky se dá určit, která instance prostředí Integration Runtime se ke kopírování používá: 

- **Kopírování mezi dvěma datovými zdroji cloudu**: Pokud se používá zdrojová i Odkazovaná služba Azure IR, ADF používá místní Azure IR, pokud jste určili, nebo automatické určení umístění Azure IR, pokud zvolíte možnost automaticky vyřešit IR (výchozí), jak je popsáno v části [umístění prostředí Integration runtime](#integration-runtime-location) .
- **Kopírování mezi cloudovým zdrojem dat a zdrojem dat v privátní síti**: Pokud služba propojená se zdrojem nebo jímkou odkazuje na prostředí IR v místním prostředí, aktivita kopírování se provádí v prostředí Integration Runtime v místním prostředí.
- **Kopírování mezi dvěma zdroji dat v privátní síti**: zdrojová i propojená služba musí odkazovat na stejnou instanci prostředí Integration runtime a k provedení aktivity kopírování se používá modul runtime integrace.

### <a name="lookup-and-getmetadata-activity"></a>Aktivity Lookup a GetMetadata

Aktivity Lookup a GetMetadata se provádí v prostředí Integration Runtime přidruženém k propojené službě úložiště dat.

### <a name="external-transformation-activity"></a>Externí aktivita transformace

Každá externí aktivita transformace, která využívá externí výpočetní modul, má cílovou výpočetní propojenou službu, která odkazuje na prostředí Integration runtime. Tato instance prostředí Integration runtime určuje umístění, ze kterého je odeslána aktivita převodu na externí ruku.

### <a name="data-flow-activity"></a>Aktivita toku dat

Aktivity toku dat se spouštějí v prostředí Azure Integration runtime, které je k němu přidružené. Výpočetní výkon využívaný pomocí datových toků závisí na vlastnostech toku dat ve vašem Azure Integration Runtime a jsou plně spravovány pomocí ADF.

## <a name="next-steps"></a>Další kroky

Viz následující články:

- [Vytvoření prostředí Azure Integration runtime](create-azure-integration-runtime.md)
- [Vytvoření prostředí Integration Runtime v místním prostředí](create-self-hosted-integration-runtime.md)
- [Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Tento článek se rozbalí v tomto kurzu a poskytne pokyny k použití spravované instance SQL a připojení IR k virtuální síti. 
