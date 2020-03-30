---
title: Sledování běhu integrace v Azure Data Factory
description: Zjistěte, jak monitorovat různé typy integračního běhu v Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/25/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 2399849b87e44c5cb70d2db987ae18d8d2d9c552
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261135"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitorování prostředí Integration Runtime ve službě Azure Data Factory  
**Integrační runtime** je výpočetní infrastruktura používaná Azure Data Factory k poskytování různých možností integrace dat v různých síťových prostředích. Data Factory nabízí tři typy integračních runů:

- Prostředí Azure Integration Runtime
- Prostředí Integration Runtime v místním prostředí
- Prostředí Azure-SSIS Integration Runtime

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li získat stav instance integračního běhu (IR), spusťte následující příkaz prostředí PowerShell: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Rutina vrátí různé informace pro různé typy integrace runtime. Tento článek vysvětluje vlastnosti a stavy pro každý typ integrace runtime.  

## <a name="azure-integration-runtime"></a>Prostředí Azure Integration Runtime
Výpočetní prostředek pro modul runtime integrace Azure je plně spravovaný elasticky v Azure. V následující tabulce jsou uvedeny popisy vlastností vrácených příkazem **Get-AzDataFactoryV2IntegrationRuntime:**

### <a name="properties"></a>Vlastnosti
Následující tabulka obsahuje popis vlastností vrácených rutinou pro prostředí integrace Azure:

| Vlastnost | Popis |
-------- | ------------- | 
| Name (Název) | Název runtime integrace Azure. |  
| Stav | Stav runtime integrace Azure. | 
| Umístění | Umístění runtime integrace Azure. Podrobnosti o umístění runtime integrace Azure najdete [v tématu Úvod do integrace runtime](concepts-integration-runtime.md). |
| Název_datafactory | Název datové továrny, do které patří prostředí Integrace Azure. | 
| ResourceGroupName | Název skupiny prostředků, do které patří datová továrna.  |
| Popis | Popis integračního běhu.  |

### <a name="status"></a>Status
Následující tabulka obsahuje možné stavy runtime integrace Azure:

| Status | Komentáře/scénáře | 
| ------ | ------------------ |
| Online | Runtime integrace Azure je online a připravený k použití. | 
| Offline | Runtime integrace Azure je offline z důvodu vnitřní chyby. |

## <a name="self-hosted-integration-runtime"></a>Prostředí Integration Runtime v místním prostředí
Tato část obsahuje popis vlastností vrácených rutinou Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> Vrácené vlastnosti a stav obsahují informace o celkovém prostředí runtime integrace s vlastním hostitelem a o každém uzlu v běhu.  

### <a name="properties"></a>Vlastnosti

V následující tabulce jsou uvedeny popisy vlastností monitorování pro **každý uzel**:

| Vlastnost | Popis | 
| -------- | ----------- | 
| Name (Název) | Název prostředí runtime integrace s vlastním hostitelem a uzlů, které jsou s ním spojené. Node je místní počítač se systémem Windows, který má na sobě nainstalovaný runtime integrace s vlastním hostitelem. |  
| Status | Stav celkového prostředí runtime integrace s vlastním hostitelem a každého uzlu. Příklad: Online/Offline/Limited/etc. Informace o těchto stavech naleznete v další části. | 
| Version | Verze runtime integrace s vlastním hostitelem a každý uzel. Verze runtime integrace s vlastním hostitelem je určena na základě verze většiny uzlů ve skupině. Pokud existují uzly s různými verzemi v nastavení runtime integrace s vlastním hostitelem, pouze uzly se stejným číslem verze jako logické vlastní hostované integrace runtime fungovat správně. Ostatní jsou v omezeném režimu a je třeba je ručně aktualizovat (pouze v případě, že se automatická aktualizace nezdaří). | 
| Dostupná paměť | Dostupná paměť v samostatném hostovaném uzlu runtime integrace. Tato hodnota je snímek téměř v reálném čase. | 
| Využití procesoru | Využití procesoru samoobslužného integračního runtime uzlu. Tato hodnota je snímek téměř v reálném čase. |
| Síťové (příchozí/odchozí) | Využití síťového runtime uzlu integrace s vlastním hostitelem. Tato hodnota je snímek téměř v reálném čase. | 
| Souběžné úlohy (spuštěné/limitové) | **Spuštěno**. Počet úloh nebo úloh spuštěných v každém uzlu. Tato hodnota je snímek téměř v reálném čase. <br/><br/>**Limit**. Limit označuje maximální souběžné úlohy pro každý uzel. Tato hodnota je definována na základě velikosti stroje. Můžete zvýšit limit vertikálně navýšit souběžné provádění úloh v pokročilých scénářích, kdy jsou aktivity vypršení časového limitu i v případě, že procesor, paměť nebo síť je nedostatečně využívána. Tato funkce je také k dispozici s jedním uzlem vlastní hostované integrace runtime. |
| Role | Existují dva typy rolí v runtime integrace s více uzly – dispečer a pracovník. Všechny uzly jsou pracovníci, což znamená, že všechny mohou být použity k provádění úloh. Existuje pouze jeden uzel dispečera, který se používá k vytahování úloh/úloh z cloudových služeb a jejich odeslání do různých pracovních uzlů. Uzel dispečera je také pracovní uzel. |

Některá nastavení vlastností dávají větší smysl, pokud jsou dva nebo více uzlů v prostředí runtime integrace s vlastním hostitelem (to znamená ve scénáři horizontálního navýšení kapacity).

#### <a name="concurrent-jobs-limit"></a>Limit souběžných úloh

Výchozí hodnota limitu souběžných úloh je nastavena na základě velikosti počítače. Faktory použité k výpočtu této hodnoty závisí na množství paměti RAM a počtu jader procesoru v počítači. Takže čím více jader a čím více paměti, tím vyšší je výchozí limit souběžných úloh.

Horizontální navýšení kapacity zvýšením počtu uzlů. Když zvýšíte počet uzlů, limit souběžných úloh je součtem souběžných limitních hodnot úloh všech dostupných uzlů.  Například pokud jeden uzel umožňuje spustit maximálně dvanáct souběžných úloh, pak přidání dalších tří podobných uzlů umožňuje spustit maximálně 48 souběžných úloh (to znamená 4 x 12). Doporučujeme zvýšit limit souběžných úloh pouze v případě, že se zobrazí nízké využití prostředků s výchozími hodnotami na každém uzlu.

Vypočítanou výchozí hodnotu můžete přepsat na webu Azure Portal. Vyberte možnost Author > Connections > Integration Runtimes > Edit > uzly > Změnit hodnotu souběžné úlohy na uzel. Můžete také použít powershell [update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) příkaz.
  
### <a name="status-per-node"></a>Stav (na uzel)
Následující tabulka obsahuje možné stavy vlastního hostovaného integračního runtime uzlu:

| Status | Popis |
| ------ | ------------------ | 
| Online | Uzel je připojen ke službě Data Factory. |
| Offline | Uzel je offline. |
| Inovace | Uzel je automaticky aktualizován. |
| Omezeně | Kvůli problému s připojením. Může být způsobeno problémem s portem HTTP 8050, problémem s připojením sběrnice nebo problémem se synchronizací pověření. |
| Inactive | Uzel je v konfiguraci odlišné od konfigurace jiných většinových uzlů. |

Uzel může být neaktivní, pokud se nemůže připojit k jiným uzlům.

### <a name="status-overall-self-hosted-integration-runtime"></a>Stav (celkový runtime integrace s vlastním hostitelem)
Následující tabulka obsahuje možné stavy prostředí runtime integrace s vlastním hostitelem. Tento stav závisí na stavech všech uzlů, které patří do běhu. 

| Status | Popis |
| ------ | ----------- | 
| Potřebujete registraci | Na tento vlastní hostovaný integrační runtime ještě není registrován žádný uzel. |
| Online | Všechny uzly jsou online. |
| Offline | Žádný uzel není online. |
| Omezeně | Ne všechny uzly v tomto prostředí integrace s vlastním hostitelem jsou v pořádku stavu. Tento stav je upozornění, že některé uzly mohou být vypnuty. Tento stav může být způsoben problémem synchronizace pověření v uzlu dispečera/pracovníka. |

Pomocí rutiny **Get-AzDataFactoryV2IntegrationRuntimeMetric** načtěte datovou část JSON obsahující podrobné vlastní hostované integrační runtime vlastnosti a jejich hodnoty snímku během doby provádění rutiny.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Ukázkový výstup (předpokládá, že existují dva uzly přidružené k tomuto prostředí runtime integrace s vlastním hostitelem):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Prostředí Azure-SSIS Integration Runtime
Modul runtime integrace Azure-SSIS je plně spravovaný cluster virtuálních počítačů Azure (nebo uzlů) určených ke spuštění balíčků SSIS. Nespouštět žádné jiné aktivity Azure Data Factory. Po zřízení můžete zadat dotaz na jeho vlastnosti a sledovat jeho celkové stavy specifické pro uzel.

### <a name="properties"></a>Vlastnosti

| Vlastnost/stav | Popis |
| --------------- | ----------- |
| Vytvořit čas | Čas UTC, kdy byl vytvořen váš runtime integrace Azure-SSIS. |
| Uzly | Přidělené/dostupné uzly vašeho runtime integrace Azure-SSIS se stavy specifickými pro uzel (spuštění/dostupnost/recyklace/nedostupnost) a žalovatelnými chybami. |
| OtherErrors | Chyby, které nelze akcetovat bez uzlu v době runtime integrace Azure-SSIS. |
| LastOperation | Výsledek posledního spuštění a zastavení operace na vašem azure-SSIS integrace runtime s žalovatelné chyby, pokud se nezdařilo. |
| Stav | Celkový stav (počáteční/spuštění/spuštění/zastavení/zastavení) vašeho runtime integrace Azure-SSIS. |
| Umístění | Umístění vašeho runtime integrace Azure-SSIS. |
| Velikost uzlu | Velikost každého uzlu vašeho runtime integrace Azure-SSIS. |
| NodeCount | Počet uzlů v prostředí integrace Azure-SSIS. |
| MaxParallelExecutionsPerNode | Počet paralelních spuštění na uzel v době runtime integrace Azure-SSIS. |
| CatalogServerEndpoint | Koncový bod existujícího serveru Azure SQL Database/Managed Instance pro hostování SSISDB. |
| Název_uživatele CatalogAdmin | Uživatelské jméno správce stávajícího serveru Azure SQL Database/Managed Instance. Služba Data Factory používá tyto informace k přípravě a správě SSISDB vaším jménem. |
| KatalogAdminPassword | Heslo správce stávajícího serveru Azure SQL Database/Managed Instance. |
| KatalogPricingTier | Cenová úroveň pro SSISDB hostovaná stávajícím serverem Azure SQL Database.  Neplatí pro Azure SQL Database Spravované instance hostování SSISDB. |
| Virtuální rozhraní | ID prostředku virtuální sítě pro váš runtime integrace Azure-SSIS pro připojení. |
| Podsíť | Název podsítě pro váš runtime integrace Azure-SSIS pro připojení. |
| ID | ID prostředku vašeho runtime integrace Azure-SSIS. |
| Typ | Typ (spravované/hostované samostatně) vašeho runtime integrace Azure-SSIS. |
| ResourceGroupName | Název skupiny prostředků Azure, ve kterém byly vytvořeny vaše továrna dat a runtime integrace Azure-SSIS. |
| Název_datafactory | Název vaší datové továrny Azure. |
| Name (Název) | Název runtime integrace Azure-SSIS. |
| Popis | Popis runtime integrace Azure-SSIS. |

  
### <a name="status-per-node"></a>Stav (na uzel)

| Status | Popis |
| ------ | ----------- | 
| Spouštění | Tento uzel se připravuje. |
| K dispozici. | Tento uzel je připraven k nasazení a spuštění balíčků SSIS. |
| Recyklace | Tento uzel je opravován nebo restartován. |
| Neaktivní | Tento uzel není připraven k nasazení a spuštění balíčků SSIS a má žalovatelné chyby/problémy, které byste mohli vyřešit. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Stav (celkový běh integrace Azure-SSIS)

| Celkový stav | Popis | 
| -------------- | ----------- | 
| Počáteční | Uzly vašeho runtime integrace Azure-SSIS nebyly přiděleny nebo připraveny. | 
| Spouštění | Uzly vašeho runtime integrace Azure-SSIS jsou přidělovány nebo připravovány a fakturace byla spuštěna. |
| Bylo zahájeno | Uzly vašeho runtime integrace Azure-SSIS byly přiděleny nebo připraveny a jsou připraveny k nasazení a spuštění balíčků SSIS. |
| Zastavování  | Uzly vašeho runtime integrace Azure-SSIS jsou uvolněny. |
| Zastaveno | Uzly vašeho runtime integrace Azure-SSIS byly uvolněny a fakturace byla zastavena. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Monitorování runtime integrace Azure-SSIS na webu Azure Portal

Následující snímky obrazovky ukazují, jak vybrat Azure-SSIS IR ke sledování a poskytnout příklad informací, které se zobrazí.

![Vyberte runtime integrace Azure-SSIS, který chcete monitorovat](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Zobrazení informací o době integrace Azure-SSIS](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Monitorování runtime integrace Azure-SSIS pomocí PowerShellu

Ke kontrole stavu infračerveného provozu Azure-SSIS použijte skript, jako je následující příklad.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Další informace o runtime integrace Azure-SSIS

Další informace o runtime integrace Azure-SSIS najdete v následujících článcích:

- [Azure-SSIS Integrace Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje rámcové informace o integračních časech runtimes obecně, včetně Azure-SSIS IR. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny pro vytvoření Azure-SSIS IR a využívá databázi Azure SQL k hostování katalogu SSIS. 
- [Postup: Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) Tento článek rozšiřuje kurz a obsahuje pokyny k použití Azure SQL Database Spravované instance a připojení infračerveného počítače k virtuální síti. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Tento článek ukazuje, jak zastavit, spustit nebo odebrat Azure-SSIS IR. Ukazuje také postup horizontálního navýšení kapacity Azure-SSIS IR přidáním více uzlů. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek obsahuje koncepční informace o připojení Azure-SSIS IR k virtuální síti Azure. Poskytuje také kroky pro použití portálu Azure ke konfiguraci virtuální sítě tak, aby se infračervená infračervená síť Azure-SSIS mohla připojit k virtuální síti. 

## <a name="next-steps"></a>Další kroky
V následujících článcích pro monitorování potrubí různými způsoby: 

- [Úvodní příručka: vytvoření datové továrny](quickstart-create-data-factory-dot-net.md).
- [Monitorování kanálů Datové továrny pomocí Azure Monitoru](monitor-using-azure-monitor.md)
