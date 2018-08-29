---
title: Monitorování prostředí integration runtime ve službě Azure Data Factory | Dokumentace Microsoftu
description: Další informace o monitorování různé typy prostředí integration runtime ve službě Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: douglasl
ms.openlocfilehash: 8ca7ce2586513373c58552bb1f56b94715f15076
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128445"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitorování prostředí integration runtime ve službě Azure Data Factory  
**Prostředí Integration runtime** je výpočetní infrastruktura, službou Azure Data Factory používá k poskytování různé možnosti integrace dat v různých síťových prostředích. Existují tři typy prostředí integration runtime nabízí datové továrny:

- Prostředí Azure Integration Runtime
- Prostředí Integration Runtime v místním prostředí
- Prostředí Azure-SSIS Integration Runtime

Pokud chcete získat stav instance prostředí integration runtime (IR), spusťte následující příkaz Powershellu: 

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Rutina vrátí různé informace pro různé typy prostředí integration runtime. Tento článek vysvětluje, vlastnosti a stavů pro každý typ prostředí integration runtime.  

## <a name="azure-integration-runtime"></a>Prostředí Azure Integration Runtime
Výpočetní prostředek pro prostředí Azure integration runtime je plně spravovaná Elasticky v Azure. Následující tabulka obsahuje popis vlastností vrácené **Get-AzureRmDataFactoryV2IntegrationRuntime** příkaz:

### <a name="properties"></a>Vlastnosti
Následující tabulka obsahuje popisy vlastností vrací rutina pro prostředí Azure integration runtime:

| Vlastnost | Popis |
-------- | ------------- | 
| Název | Název modulu runtime integrace se službou Azure. |  
| Stav | Stav prostředí Azure integration runtime. | 
| Umístění | Umístění prostředí Azure integration runtime. Podrobnosti o umístění prostředí Azure integration runtime najdete v tématu [Úvod do prostředí integration runtime](concepts-integration-runtime.md). |
| DataFactoryName | Název objektu pro vytváření dat, které patří prostředí Azure integration runtime. | 
| ResourceGroupName | Název skupiny prostředků, které patří služby data factory.  |
| Popis | Popis prostředí integration runtime.  |

### <a name="status"></a>Status
Následující tabulka uvádí možné stavy prostředí Azure integration runtime:

| Status | Komentáře a scénáře | 
| ------ | ------------------ |
| Online | Prostředí Azure integration runtime je online a připravená k použití. | 
| Offline | Prostředí Azure integration runtime je offline z důvodu vnitřní chyby. |

## <a name="self-hosted-integration-runtime"></a>Prostředí Integration Runtime v místním prostředí
Tato část obsahuje popis vlastností vrací Rutina Get-AzureRmDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> Vrácený vlastností a stavu obsahují informace o celkové místní prostředí integration runtime a každý uzel v modulu runtime.  

### <a name="properties"></a>Vlastnosti

Následující tabulka obsahuje popis vlastností pro monitorování **každý uzel**:

| Vlastnost | Popis | 
| -------- | ----------- | 
| Název | Název místního prostředí integration runtime a uzlů s ním spojená. Uzel je místní počítač Windows, který má místní prostředí integration runtime na něm nainstalován. |  
| Status | Stav celkové místní prostředí integration runtime a každý uzel. Příklad: Online/Offline/Limited/atd. Informace o tyto stavy najdete v další části. | 
| Verze | Verze modulu runtime integrace v místním prostředí a každý uzel. Verzi modulu runtime integrace v místním prostředí je určen na základě verze Většina uzlů ve skupině. Pokud jsou uzly s různými verzemi v nastavení místního prostředí integration runtime, pouze uzly se stejným číslem verze jako logický v místním prostředí integration runtime funkce správně. Ostatní jsou v režimu omezen a potřeba ručně aktualizovat (pouze v případě automatických aktualizací selže). | 
| Dostupná paměť | Dostupná paměť na uzel v místním prostředí integration runtime. Tato hodnota je snímek téměř v reálném čase. | 
| Využití procesoru | Využití procesoru uzlu místního prostředí integration runtime. Tato hodnota je snímek téměř v reálném čase. |
| Síť (vstup/výstup) | Využití sítě uzel v místním prostředí integration runtime. Tato hodnota je snímek téměř v reálném čase. | 
| Souběžné úlohy (spuštění / Limit) | **Spuštění**. Počet úloh nebo úlohy spuštěné na každém uzlu. Tato hodnota je snímek téměř v reálném čase. <br/><br/>**Limit**. Limit označuje maximální počet souběžných úloh pro každý uzel. Tato hodnota je definována v závislosti na velikosti počítačů. Můžete zvýšit limit vertikální navýšení kapacity provádění souběžné úlohy v pokročilých scénářích, když se nestíhají dokončit i v případě procesor, paměť nebo síť je nevyužitých aktivity. Tato možnost je také dostupné s jedním uzlem místní prostředí integration runtime. |
| Role | Existují dva typy rolí v několika uzly místního prostředí integration runtime – dispečer a pracovního procesu. Všechny uzly jsou pracovních procesů, což znamená, že jsou všechny slouží ke spuštění úlohy. Existuje pouze jeden uzel dispečer, který slouží k vyžádání úlohy nebo úlohy ze služby cloud services a jejich vypravování do různých pracovních uzlů. Dispečer uzel je také pracovního uzlu. |

Některá nastavení vlastnosti dávat větší smysl, pokud existují dva nebo více uzlů v místním prostředí integration runtime (to znamená, že v s horizontálním navýšením kapacity scénář).

#### <a name="concurrent-jobs-limit"></a>Limit souběžných úloh

Výchozí hodnota je nastavený limit souběžných úloh podle velikosti počítačů. Faktory, které slouží k výpočtu tato hodnota závisí na množství paměti RAM a počet jader procesoru počítače. Takže více jader a větší množství paměti, tím vyšší výchozí omezení souběžných úloh.

Horizontální navýšení kapacity zvýšením počtu uzlů. Pokud zvýšíte počet uzlů, limit souběžných úloh je součet hodnot limitu souběžných úloh všech dostupných uzlů.  Například pokud se jeden uzel umožňuje spouštět až 12 souběžných úloh, přidáte tři uzly podobné vám umožní spustit určitý počet souběžných úloh 48 (4 x 12). Doporučujeme, abyste zvýšili limit souběžných úloh pouze v případě, že se zobrazí využití prostředků nízké výchozí hodnoty na každém uzlu.

Můžete přepsat počítané výchozí hodnotu na webu Azure Portal. Vyberte Vytvořit > připojení > prostředí Integration runtime > Upravit > uzlů > změnit hodnotu souběžných úloh na uzlu. Můžete také použít rutinu prostředí PowerShell [aktualizace azurermdatafactoryv2integrationruntimenode](https://docs.microsoft.com/en-us/powershell/module/azurerm.datafactoryv2/update-azurermdatafactoryv2integrationruntimenode?view=azurermps-6.4.0#examples) příkazu.
  
### <a name="status-per-node"></a>Stav (na uzel)
Následující tabulka uvádí možné stavy uzel v místním prostředí integration runtime:

| Status | Popis |
| ------ | ------------------ | 
| Online | Uzel je připojen ke službě Data Factory. |
| Offline | Uzel je offline. |
| Upgrade | Uzel je automaticky aktualizuje. |
| Omezená | Kvůli problému připojením. Může být způsobeno problém 8050 portu HTTP, problém se službou Service bus připojení nebo problém synchronizace s přihlašovacími údaji. |
| Neaktivní | Uzel je v konfiguraci se liší od konfigurace dalších většinou uzlů. |

Uzlem může být neaktivní, pokud se nemůže připojit k ostatním uzlům.

### <a name="status-overall-self-hosted-integration-runtime"></a>Stav (celkové místní prostředí integration runtime)
Následující tabulka obsahuje možné stavy z místního prostředí integration runtime. Tento stav závisí na stavy všechny uzly, které patří do modulu runtime. 

| Status | Popis |
| ------ | ----------- | 
| Třeba registrace | Žádný uzel ještě zaregistrován do tohoto modulu runtime integrace v místním prostředí. |
| Online | Všechny uzly jsou online. |
| Offline | Žádný uzel je online. |
| Omezená | Ne všechny uzly v tomto modulu runtime integrace v místním prostředí jsou v dobrém stavu. Tento stav se upozornění, že některé uzly jsou pravděpodobně mimo provoz. Tento stav může být způsobeno problém synchronizace s přihlašovacími údaji na dispečerský/pracovní uzel. |

Použití **Get-AzureRmDataFactoryV2IntegrationRuntimeMetric** rutina pro načtení datové části JSON obsahující na podrobné v místním prostředí integration runtime vlastnosti a hodnoty jejich snímku během doby provádění rutiny.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Ukázkový výstup (předpokládá, že existují dva uzly, které jsou přidružené k tento modul runtime integrace v místním prostředí):

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
Prostředí Azure-SSIS integration runtime je plně spravovaný cluster Azure virtual machines (nebo uzlů) vyhrazených ke spouštění balíčků služby SSIS. Nejde spustit všechny ostatní aktivity služby Azure Data Factory. Po zřízení, se můžete dotazovat její vlastnosti a sledovat její konkrétní uzel celkové/stavy.

### <a name="properties"></a>Vlastnosti

| Vlastnost/Status | Popis |
| --------------- | ----------- |
| CreateTime | Čas UTC vytvoření prostředí Azure-SSIS integration runtime. |
| Uzly | Přidělené dostupné uzly vašeho prostředí Azure-SSIS integration runtime se stavy daného uzlu (počáteční/k dispozici/recyklace/není k dispozici) a užitečné chyby. |
| OtherErrors | Uzel konkrétních užitečné chyb v prostředí Azure-SSIS integration runtime. |
| LastOperation | Výsledek poslední operace spustit/zastavit na prostředí Azure-SSIS integration runtime s chybami užitečné, pokud se něco nepovedlo. |
| Stav | Celkový stav (počáteční/počáteční/spuštění/zastavení/zastavená) z prostředí Azure-SSIS integration runtime. |
| Umístění | Umístění prostředí Azure-SSIS integration runtime. |
| NodeSize | Velikost každého uzlu vašeho prostředí Azure-SSIS integration runtime. |
| NodeCount | Počet uzlů v prostředí Azure-SSIS integration runtime. |
| MaxParallelExecutionsPerNode | Počet paralelních úloh na uzlu v prostředí Azure-SSIS integration runtime. |
| CatalogServerEndpoint | Koncový bod vašeho existujícího serveru Azure SQL Database/spravované Instance (Preview) pro hostování služby SSISDB. |
| CatalogAdminUserName | Uživatelské jméno správce vašeho existujícího serveru Azure SQL Database/spravované Instance (Preview). Služba data Factory používá tyto informace můžete připravit a spravovat databáze SSISDB vaším jménem. |
| CatalogAdminPassword | Heslo správce vašeho existujícího serveru Azure SQL Database/spravované Instance (Preview). |
| CatalogPricingTier | Cenovou úroveň pro databází SSISDB hostovanou ve vaší stávající server Azure SQL Database.  Neplatí pro Azure SQL Managed Instance (Preview) hostování databáze SSISDB. |
| VNetId | Virtuální síť ID prostředku pro prostředí Azure-SSIS integration runtime připojit. |
| Podsíť | Název podsítě pro vaše prostředí Azure-SSIS integration runtime připojit. |
| ID | ID prostředku vašeho prostředí Azure-SSIS integration runtime. |
| Typ | Typ (spravované/samoobslužné-Hosted) vašeho prostředí Azure-SSIS integration runtime. |
| ResourceGroupName | Název vaší skupiny prostředků Azure, ve kterém byly vytvořeny služby data factory a Azure-SSIS integration runtime. |
| DataFactoryName | Název služby Azure data factory. |
| Název | Název prostředí Azure-SSIS integration runtime. |
| Popis | Popis prostředí Azure-SSIS integration runtime. |

  
### <a name="status-per-node"></a>Stav (na uzel)

| Status | Popis |
| ------ | ----------- | 
| Spouštění | Připravuje se tento uzel. |
| K dispozici. | Tento uzel je připravená k nasazení a spouštění balíčků služby SSIS. |
| Recyklace | Je tento uzel se opravit nebo restartování. |
| Není dostupný | Tento uzel není připravená k nasazení a spouštění balíčků služby SSIS a obsahuje užitečné chyby nebo problémy, které by se dala přeložit. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Stav (celkové prostředí Azure-SSIS integration runtime)

| Celkový stav | Popis | 
| -------------- | ----------- | 
| Počáteční | Uzly prostředí Azure-SSIS integration runtime se přidělené a připravené. | 
| Spouštění | Uzly prostředí Azure-SSIS integration runtime se přidělené a připravené a fakturace byla spuštěna. |
| Spuštěno | Uzly prostředí Azure-SSIS integration runtime byly přidělené a připravené a jsou připravená k nasazení a spouštění balíčků služby SSIS. |
| Zastavování  | Uzly prostředí Azure-SSIS integration runtime se vydávají. |
| Zastaveno | Byly vydány uzlů v prostředí Azure-SSIS integration runtime a účtování se ukončí. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Monitorování prostředí Azure-SSIS integration runtime na webu Azure Portal

Na následujících snímcích obrazovky ukazují, jak vybrat Azure-SSIS IR k monitorování a uveďte příklad informace, které se zobrazí.

![Vyberte monitorování prostředí Azure-SSIS integration runtime](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Zobrazení informací o prostředí Azure-SSIS integration runtime](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Monitorování prostředí Azure-SSIS integration runtime pomocí Powershellu

Použít skript jako v následujícím příkladu a zkontrolujte stav Azure-SSIS IR.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Další informace o Azure-SSIS integration runtime

Naleznete v následujících článcích se dozvíte další informace o Azure-SSIS integration runtime:

- [Prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje koncepční informace o prostředí integration runtime obecně včetně Azure-SSIS IR. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny pro vytvoření Azure-SSIS IR a využívá databázi Azure SQL k hostování katalogu SSIS. 
- [Postup: Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) Tento článek dále navazuje na tento kurz a obsahuje pokyny k použití spravované instance SQL Azure (Preview) a připojení IR k virtuální síti. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Tento článek ukazuje, jak zastavit, spustit nebo odebrat Azure-SSIS IR. Ukazuje také postup horizontálního navýšení kapacity Azure-SSIS IR přidáním více uzlů. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek obsahuje koncepční informace o připojení Azure-SSIS IR k virtuální síti Azure. Poskytuje také kroky konfigurace virtuální sítě tak, aby prostředí Azure-SSIS IR se může připojit k virtuální síti pomocí webu Azure portal. 

## <a name="next-steps"></a>Další postup
Naleznete v následujících článcích pro monitorování kanálů různými způsoby: 

- [Rychlý start: vytvoření datové továrny](quickstart-create-data-factory-dot-net.md).
- [Použití Azure Monitor k monitorování kanálů Data Factory](monitor-using-azure-monitor.md)
