---
title: Monitorování prostředí Integration runtime v Azure Data Factory | Microsoft Docs
description: Naučte se monitorovat různé typy prostředí Integration runtime v Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/25/2018
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: b84ea0b9859e69eaf45cd9a89b2443cf42949f79
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141087"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitorování prostředí Integration runtime v Azure Data Factory  
**Integration runtime** je výpočetní infrastruktura, kterou používá Azure Data Factory k zajištění různých možností integrace dat napříč různými síťovými prostředími. Existují tři typy prostředí Integration runtime, které nabízí Data Factory:

- Prostředí Azure Integration Runtime
- Prostředí Integration Runtime v místním prostředí
- Prostředí Azure-SSIS Integration Runtime

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li získat stav instance prostředí Integration runtime (IR), spusťte následující příkaz prostředí PowerShell: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Rutina vrátí různé informace pro různé typy prostředí Integration runtime. Tento článek vysvětluje vlastnosti a stavy pro jednotlivé typy prostředí Integration runtime.  

## <a name="azure-integration-runtime"></a>Prostředí Azure Integration Runtime
Výpočetní prostředek pro Azure Integration runtime je plně spravovaný elastickě v Azure. Následující tabulka uvádí popisy vlastností vrácených příkazem **Get-AzDataFactoryV2IntegrationRuntime** :

### <a name="properties"></a>Vlastnosti
Následující tabulka uvádí popis vlastností vrácených rutinou pro prostředí Azure Integration Runtime:

| Vlastnost | Description |
-------- | ------------- | 
| Name | Název prostředí Azure Integration runtime. |  
| State | Stav prostředí Azure Integration runtime. | 
| Location | Umístění prostředí Azure Integration runtime. Podrobnosti o umístění prostředí Azure Integration runtime najdete v tématu [Úvod do prostředí Integration runtime](concepts-integration-runtime.md). |
| DataFactoryName | Název objektu pro vytváření dat, ke kterému patří Azure Integration runtime | 
| ResourceGroupName | Název skupiny prostředků, do které patří objekt pro vytváření dat.  |
| Description | Popis prostředí Integration runtime.  |

### <a name="status"></a>Stav
Následující tabulka nabízí možné stavy prostředí Azure Integration Runtime:

| Stav | Komentáře a scénáře | 
| ------ | ------------------ |
| Online | Prostředí Azure Integration runtime je online a připravené k použití. | 
| Offline | Prostředí Azure Integration runtime je offline kvůli vnitřní chybě. |

## <a name="self-hosted-integration-runtime"></a>Prostředí Integration Runtime v místním prostředí
V této části najdete popis vlastností vrácených pomocí rutiny Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> Vrácené vlastnosti a stav obsahují informace o celkovém prostředí Integration runtime a všech uzlech modulu runtime.  

### <a name="properties"></a>Vlastnosti

Následující tabulka uvádí popisy vlastností monitorování pro **každý uzel**:

| Vlastnost | Description | 
| -------- | ----------- | 
| Name | Název místního prostředí Integration runtime a uzlů, které jsou k němu přidružené. Uzel je místní počítač s Windows, na kterém je nainstalovaný modul Integration runtime v místním prostředí. |  
| Stav | Stav celkového místního prostředí Integration runtime a každého uzlu. Příklad: Online/offline/omezené/atd. Informace o těchto stavech najdete v další části. | 
| Version | Verze místního prostředí Integration runtime a každého uzlu. Verze prostředí Integration runtime v místním prostředí je určena na základě verze většiny uzlů ve skupině. Pokud v místním prostředí Integration runtime hostují uzly s různými verzemi, budou správně fungovat pouze uzly se stejným číslem verze jako logická funkce místního prostředí Integration runtime. Ostatní jsou v omezeném režimu a je potřeba je ručně aktualizovat (jenom v případě, že se automatická aktualizace nezdařila). | 
| Dostupná paměť | Dostupná paměť v uzlu Integration runtime v místním prostředí. Tato hodnota je snímkem téměř v reálném čase. | 
| Využití procesoru | Využití procesoru uzlu Integration runtime v místním prostředí. Tato hodnota je snímkem téměř v reálném čase. |
| Sítě (za sekundu) | Využití sítě uzlu Integration runtime v místním prostředí. Tato hodnota je snímkem téměř v reálném čase. | 
| Souběžné úlohy (spuštěné/omezení) | **Spuštěno**. Počet úloh nebo úloh, které jsou spuštěny na jednotlivých uzlech. Tato hodnota je snímkem téměř v reálném čase. <br/><br/>**Omezení**. Limit označuje maximální počet souběžných úloh pro každý uzel. Tato hodnota je definovaná na základě velikosti počítače. Tento limit můžete zvýšit tak, aby se při provádění úloh v pokročilých scénářích narůstat na horizontální navýšení kapacity, a to i v případě, že je procesor, paměť nebo síť přetížená. Tato funkce je také k dispozici v místním prostředí Integration runtime s jedním uzlem. |
| Role | Existují dva typy rolí v prostředí Integration runtime ve více uzlech – dispečer a pracovní proces. Všechny uzly jsou pracovní procesy, což znamená, že je můžete použít ke spouštění úloh. K dispozici je jen jeden uzel dispečera, který se používá k vyžádání úkolů nebo úloh z cloudových služeb a jejich odeslání do různých pracovních uzlů. Uzel dispečera je také pracovním uzlem. |

Některá nastavení vlastností jsou smysluplnější, když v místním prostředí Integration runtime (to znamená ve scénáři horizontálního navýšení kapacity) jsou dva nebo více uzlů.

#### <a name="concurrent-jobs-limit"></a>Omezení souběžných úloh

Výchozí hodnota limitu souběžných úloh je nastavená na základě velikosti počítače. Faktory použité k výpočtu této hodnoty závisí na velikosti paměti RAM a počtu PROCESORových jader počítače. Čím více jader a čím více paměti, tím vyšší je výchozí limit souběžných úloh.

Horizontální navýšení kapacity můžete zvýšit zvýšením počtu uzlů. Pokud zvýšíte počet uzlů, limit souběžných úloh je součtem hodnot souběžných úloh limitu všech dostupných uzlů.  Například pokud jeden uzel umožňuje spustit maximálně dvanáct souběžných úloh, pak přidání tří podobných uzlů vám umožní spustit maximálně 48 souběžných úloh (tj. 4 x 12). Maximální počet souběžných úloh doporučujeme zvýšit jenom v případě, že se na každém uzlu zobrazí nízké využití prostředků s výchozími hodnotami.

Vypočtenou výchozí hodnotu můžete přepsat v Azure Portal. Vyberte vytvořit > připojení > prostředí Integration runtime > upravte > uzly > změňte hodnotu souběžných úloh na uzel. Můžete použít také příkaz PowerShell [Update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) .
  
### <a name="status-per-node"></a>Stav (na jeden uzel)
Následující tabulka uvádí možné stavy uzlu Integration runtime v místním prostředí:

| Stav | Description |
| ------ | ------------------ | 
| Online | Uzel je připojen ke službě Data Factory. |
| Offline | Uzel je offline. |
| Upgrade | Uzel se automaticky aktualizuje. |
| Omezené | Kvůli problému s připojením. Může to být kvůli problému s portem HTTP 8050, potížím s připojením k Service Bus nebo problémům s synchronizací přihlašovacích údajů. |
| Neaktivní | Uzel je v konfiguraci odlišnou od konfigurace jiných majoritní uzlů. |

Uzel může být neaktivní, pokud se nemůže připojit k jiným uzlům.

### <a name="status-overall-self-hosted-integration-runtime"></a>Stav (celkový místní prostředí Integration Runtime)
Následující tabulka uvádí možné stavy prostředí Integration runtime v místním prostředí. Tento stav závisí na stavech všech uzlů, které patří do modulu runtime. 

| Stav | Description |
| ------ | ----------- | 
| Vyžaduje registraci | Do tohoto místního prostředí Integration runtime ještě není zaregistrovaný žádný uzel. |
| Online | Všechny uzly jsou online. |
| Offline | Žádný uzel není online. |
| Omezené | Ne všechny uzly v tomto místním prostředí Integration runtime jsou v dobrém stavu. Tento stav je upozorněním, že některé uzly mohou být mimo provoz. Důvodem může být problém synchronizace přihlašovacích údajů v uzlu Dispatcher nebo Worker. |

Pomocí rutiny **Get-AzDataFactoryV2IntegrationRuntimeMetric** načtěte datovou část JSON obsahující podrobné vlastnosti místního prostředí Integration runtime a jejich hodnoty snímku v době spuštění rutiny.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Ukázkový výstup (předpokládá, že jsou k tomuto prostředí Integration runtime v místním prostředí přidruženy dva uzly):

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
Prostředí Azure-SSIS Integration runtime je plně spravovaný cluster virtuálních počítačů Azure (neboli uzlů) vyhrazených ke spouštění balíčků SSIS. Nespouští žádné další aktivity Azure Data Factory. Po zřízení můžete zadat dotaz na jeho vlastnosti a monitorovat jejich celkový stav specifický pro uzel.

### <a name="properties"></a>Vlastnosti

| Vlastnost/stav | Description |
| --------------- | ----------- |
| CreateTime | Čas UTC, kdy se vytvořil prostředí Azure-SSIS Integration runtime. |
| Uzly | Přidělené a dostupné uzly prostředí Azure-SSIS Integration runtime se stavem specifickým pro uzel (spuštění/k dispozici/recyklace/nedostupné) a chybami, které lze provést. |
| OtherErrors | Chyby, které nejsou specifické pro uzel v prostředí Azure-SSIS Integration runtime. |
| LastOperation | Výsledek poslední operace spuštění/zastavení v prostředí Azure-SSIS Integration runtime s chybami, pokud se nezdařila. |
| State | Celkový stav (počáteční/spuštění/spuštění/zastavení/zastavení/zastavení) prostředí Azure-SSIS Integration runtime. |
| Location | Umístění prostředí Azure-SSIS Integration runtime. |
| NodeSize | Velikost každého uzlu prostředí Azure-SSIS Integration runtime. |
| NodeCount | Počet uzlů v prostředí Azure-SSIS Integration runtime. |
| MaxParallelExecutionsPerNode | Počet paralelních spuštění na uzel v prostředí Azure-SSIS Integration runtime. |
| CatalogServerEndpoint | Koncový bod stávajícího serveru Azure SQL Database/spravované instance pro hostování SSISDB. |
| CatalogAdminUserName | Uživatelské jméno správce stávajícího serveru Azure SQL Database/spravované instance. Služba Data Factory používá tyto informace k přípravě a správě SSISDB vaším jménem. |
| CatalogAdminPassword | Heslo správce stávajícího serveru Azure SQL Database/spravované instance. |
| CatalogPricingTier | Cenová úroveň pro SSISDB, která je hostovaná vaším stávajícím Azure SQL Database serverem.  Nedá se použít pro Azure SQL Database spravovanou instanci hostující SSISDB. |
| VNetId | ID prostředku virtuální sítě pro prostředí Azure-SSIS Integration runtime, které se má připojit. |
| Subnet | Název podsítě pro prostředí Azure-SSIS Integration runtime, které se má připojit. |
| id | ID prostředku prostředí Azure-SSIS Integration runtime. |
| type | Typ (spravované nebo místní hostování) vašeho prostředí Azure-SSIS Integration runtime. |
| ResourceGroupName | Název skupiny prostředků Azure, ve které se vytvořila vaše Datová továrna a prostředí Azure-SSIS Integration runtime. |
| DataFactoryName | Název vaší datové továrny Azure. |
| Name | Název prostředí Azure-SSIS Integration runtime. |
| Description | Popis prostředí Azure-SSIS Integration runtime. |

  
### <a name="status-per-node"></a>Stav (na jeden uzel)

| Stav | Description |
| ------ | ----------- | 
| Spouštění | Připravuje se tento uzel. |
| K dispozici | Tento uzel je připravený na nasazení/spouštění balíčků SSIS. |
| Recyklován | Probíhá oprava/restartování tohoto uzlu. |
| Není dostupný | Tento uzel není připravený na nasazení/spouštění balíčků SSIS a má možné chyby nebo problémy, které můžete vyřešit. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Stav (celkový prostředí Azure-SSIS Integration Runtime)

| Celkový stav | Description | 
| -------------- | ----------- | 
| Počáteční | Uzly prostředí Azure-SSIS Integration runtime nebyly přiděleny nebo připraveny. | 
| Spouštění | Uzly prostředí Azure-SSIS Integration runtime se přidělují/připravují a účtují se fakturace. |
| Spuštěno | Uzly prostředí Azure-SSIS Integration runtime byly přiděleny nebo připraveny a jsou připraveny k nasazení/spuštění balíčků SSIS. |
| Zastavování  | Uvolňují se uzly prostředí Azure-SSIS Integration runtime. |
| Zastaveno | Uzly prostředí Azure-SSIS Integration runtime byly vydány a fakturace se zastavila. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Monitorování prostředí Azure-SSIS Integration runtime v Azure Portal

Následující snímky obrazovky ukazují, jak vybrat Azure-SSIS IR pro monitorování a zadat příklad zobrazených informací.

![Vyberte prostředí Azure-SSIS Integration runtime, které chcete monitorovat.](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Zobrazit informace o prostředí Azure-SSIS Integration runtime](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Monitorování prostředí Azure-SSIS Integration runtime pomocí PowerShellu

Ke kontrole stavu Azure-SSIS IR použijte skript podobný následujícímu příkladu.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Další informace o prostředí Azure-SSIS Integration runtime

Další informace o prostředí Azure-SSIS Integration runtime najdete v následujících článcích:

- [Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje koncepční informace o integračních modulech Integration obecně včetně Azure-SSIS IR. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny pro vytvoření Azure-SSIS IR a využívá databázi Azure SQL k hostování katalogu SSIS. 
- [Postup: Vytvoření prostředí Azure-SSIS Integration](create-azure-ssis-integration-runtime.md)runtime. Tento článek se rozbalí v tomto kurzu a poskytne pokyny k používání Azure SQL Database spravované instance a připojení IR k virtuální síti. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Tento článek ukazuje, jak zastavit, spustit nebo odebrat Azure-SSIS IR. Ukazuje také postup horizontálního navýšení kapacity Azure-SSIS IR přidáním více uzlů. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek obsahuje koncepční informace o připojení Azure-SSIS IR k virtuální síti Azure. Poskytuje také postup Azure Portal pro konfiguraci virtuální sítě, aby se Azure-SSIS IR mohl připojit k virtuální síti. 

## <a name="next-steps"></a>Další kroky
Projděte si následující články pro monitorování kanálů různými způsoby: 

- [Rychlý Start: vytvoření datové továrny](quickstart-create-data-factory-dot-net.md)
- [Monitorování kanálů Data Factory pomocí Azure Monitor](monitor-using-azure-monitor.md)
