---
title: Monitorování integrace runtime v Azure Data Factory | Microsoft Docs
description: Naučte se monitorovat různé typy integrace runtime v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: ''
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2017
ms.author: douglasl
ms.openlocfilehash: 956bd89a993a019d7a5a4dd28efec4116b174334
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297253"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitorování modulu runtime integrace v Azure Data Factory  
**Integrace runtime** je na výpočetní infrastruktuře používá k zajištění různé funkce integrace dat různých prostředích sítě Azure Data Factory. Existují tři typy integrační moduly runtime nabízené službou Data Factory:

- Prostředí Azure Integration Runtime
- Prostředí Integration Runtime v místním prostředí
- Prostředí Azure-SSIS Integration Runtime

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), prostudujte si [dokumentaci služby Data Factory verze 1](v1/data-factory-introduction.md).

Chcete-li získat stav instance integrace runtime (IR), spusťte následující příkaz prostředí PowerShell: 

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Rutina vrátí různé informace pro různé typy integrace modulu runtime. Tento článek vysvětluje vlastnosti a stavy, které jsou pro každý typ integrace modulu runtime.  

## <a name="azure-integration-runtime"></a>Prostředí Azure Integration Runtime
Výpočetní prostředek pro modulu runtime integrace se službou Azure je plně spravovaná pružně v Azure. Následující tabulka obsahuje popis vlastností vrácený **Get-AzureRmDataFactoryV2IntegrationRuntime** příkaz:

### <a name="properties"></a>Vlastnosti
Následující tabulka obsahuje popis vlastností vrácených pro modulu runtime integrace se službou Azure rutinu:

| Vlastnost | Popis |
-------- | ------------- | 
| Název | Název modulu runtime integrace se službou Azure. |  
| Stav | Stav modulu runtime integrace se službou Azure. | 
| Umístění | Umístění modulu runtime integrace se službou Azure. Podrobnosti o umístění modulu runtime integrace se službou Azure najdete v tématu [Úvod do integrace runtime](concepts-integration-runtime.md). |
| DataFactoryName | Název objektu pro vytváření dat, který je součástí modulu runtime integrace se službou Azure. | 
| ResourceGroupName | Název objektu pro vytváření dat patří do skupiny prostředků.  |
| Popis | Popis integrace modulu runtime.  |

### <a name="status"></a>Status
V následující tabulce jsou uvedené možné stavy modulu runtime integrace se službou Azure:

| Status | Komentáře nebo scénáře | 
| ------ | ------------------ |
| Online | Modul runtime integrace se službou Azure je online a připravená k použití. | 
| Offline | Modul runtime integrace se službou Azure je offline z důvodu vnitřní chyby. |

## <a name="self-hosted-integration-runtime"></a>Prostředí Integration Runtime v místním prostředí
Tato část obsahuje popis vlastností vrátila Rutina Get-AzureRmDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> Vrácených vlastností a stav obsahují informace o vlastním hostováním celkovou integraci runtime a každý uzel v modulu runtime.  

### <a name="properties"></a>Vlastnosti

Následující tabulka obsahuje popis vlastností pro monitorování **každý uzel**:

| Vlastnost | Popis | 
| -------- | ----------- | 
| Název | Název modulu runtime vlastním hostováním integrace a uzly s ním spojená. Uzel je místní počítač systému Windows, s vlastním hostováním integrační modul runtime na něm nainstalován. |  
| Status | Stav modulu runtime celkovou vlastním hostováním integraci a každý uzel. Příklad: Online nebo Offline nebo Limited/atd. Informace o tyto stavy najdete v další části. | 
| Verze | Verze runtime vlastním hostováním integrace a každý uzel. Verze runtime vlastním hostováním integrace je určen na základě verze Většina uzlů ve skupině. Pokud jsou uzly s různými verzemi v nastavení vlastním hostováním integrace modulu runtime, pouze uzly se číslo verze jako logické samoobslužné hostované funkce integrace modulu runtime správně. Ostatní jsou v režimu omezené a je nutné ručně aktualizovat (pouze v případě automatické aktualizace nezdaří). | 
| Dostupná paměť | Dostupná paměť na uzlu vlastním hostováním integrace modulu runtime. Tato hodnota je snímku near v reálném čase. | 
| Využití procesoru | Využití procesoru uzlu vlastním hostováním integrace modulu runtime. Tato hodnota je snímku near v reálném čase. |
| Sítě (vstup/výstup) | Využití sítě uzlu vlastním hostováním integrace modulu runtime. Tato hodnota je snímku near v reálném čase. | 
| Souběžné úlohy (spuštění / omezit) | Počet úlohy nebo úlohy na jednotlivých uzlech spuštěné. Tato hodnota je snímku near v reálném čase. Limit označuje, že maximální souběžných úloh pro každý uzel. Tato hodnota je definována v závislosti na velikosti počítače. Můžete zvýšit limit škálování provádění souběžné úlohy v pokročilých scénářích, kde procesoru a paměti nebo síťových je v části využívat, ale aktivity jsou vypršení časového limitu. Tato funkce je také dostupná s vlastním hostováním integrace jedním uzlem runtime. |
| Role | Existují dva typy rolí v několika uzly vlastním hostováním integrace runtime – dispečera a pracovního procesu. Všechny uzly jsou pracovníci, což znamená, že se všechny slouží k provedení úlohy. Je pouze jeden uzel dispečera, který se používá k úlohy nebo úlohy z cloudové služby pro vyžádání obsahu a jejich odesílání na jiný pracovní uzly. Dispečer uzel je také pracovního uzlu. |

Některá nastavení vlastnosti dávat větší smysl, pokud jsou dostupné dva nebo víc uzlů (škálování scénář) v modulu runtime vlastním hostováním integrace. 
  
### <a name="status-per-node"></a>Stav (podle uzlu)
Následující tabulka obsahuje možné stavy modulu runtime uzlu vlastním hostováním integrace:

| Status | Popis |
| ------ | ------------------ | 
| Online | Uzel je připojený ke službě Data Factory. |
| Offline | Uzel je offline. |
| Upgrade | Uzel, která má být automaticky aktualizován. |
| Omezená | Problém s připojením. Může být kvůli problému 8050 portu HTTP, problém s připojením service bus nebo problém s přihlašovacími údaji synchronizace. |
| Neaktivní | Uzel je v konfiguraci se liší od konfigurace jiných Většina uzlů. |

Uzlem může být neaktivní, když se nemůže připojit k jiné uzly.

### <a name="status-overall-self-hosted-integration-runtime"></a>Stav (celkovou integraci vlastním hostováním runtime)
Následující tabulka obsahuje možné stavy modulu runtime vlastním hostováním integrace. Tento stav závisí na stavy, které jsou všechny uzly, které patří do modulu runtime. 

| Status | Popis |
| ------ | ----------- | 
| Třeba registrace | Tento modul runtime vlastním hostováním integrace je zaregistrovaný žádný uzel ještě. |
| Online | Všechny uzly jsou online. |
| Offline | Žádný uzel je online. |
| Omezená | Ne všechny uzly v tento modul runtime vlastním hostováním integrace jsou v dobrém stavu. Tento stav se upozornění, že některé uzly mohou být mimo provoz. Tento stav může být způsobeno problémem synchronizace přihlašovacích údajů na dispečera nebo pracovního uzlu. |

Použití **Get-AzureRmDataFactoryV2IntegrationRuntimeMetric** rutiny načíst datové části JSON obsahující v podrobné samoobslužně hostovaná integrace modulu runtime vlastnosti a hodnoty jejich snímku v době, provádění rutiny.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Ukázkový výstup (předpokládá, že existují dva uzly, které jsou přidružené k této integrace s vlastním hostováním runtime):

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
Modul runtime integrace Azure SSIS je plně spravovaný cluster Azure virtuální počítače (nebo uzlech) vyhrazený pro spuštění vaší balíčky SSIS. Nejde spustit všechny ostatní aktivity Azure Data Factory. Po zřízení, můžete dotazovat na vlastnosti a sledovat jeho stavy, které jsou specifické pro danou celkový/uzlu.

### <a name="properties"></a>Vlastnosti

| Vlastnost nebo stav | Popis |
| --------------- | ----------- |
| CreateTime | Čas UTC vytvoření vaší runtime integrace Azure SSIS. |
| Uzly | Přidělené dostupné uzly vaše integrace Azure SSIS modulu runtime s stavy daného uzlu (spuštění nebo k dispozici nebo recyklace nebo není k dispozici) a níž lze provést akci chyby. |
| OtherErrors | Řešitelné chyby uzlu konkrétní na vaše runtime integrace Azure SSIS. |
| LastOperation | Výsledek operace poslední spuštění a zastavení na vaše integrace Azure SSIS modulu runtime s řešitelné chybám, pokud se nezdařilo. |
| Stav | Celkový stav (počáteční nebo spuštění nebo spuštění nebo zastavení nebo zastaveno) vaší modulu runtime integrace Azure SSIS. |
| Umístění | Umístění vaší runtime integrace Azure SSIS. |
| NodeSize | Velikost vašeho modulu runtime integrace Azure SSIS každého uzlu. |
| NodeCount | Počet uzlů v vaší runtime integrace Azure SSIS. |
| MaxParallelExecutionsPerNode | Počet paralelní spuštění na každém uzlu v vaší runtime integrace Azure SSIS. |
| CatalogServerEndpoint | Koncový bod existujícího serveru Azure SQL Database nebo spravované Instance (Preview) na hostitele SSISDB. |
| CatalogAdminUserName | Uživatelské jméno správce serveru existující databáze nebo spravované Instance SQL Azure (Preview). Služba data Factory používá tyto informace k přípravě a spravovat SSISDB vaším jménem. |
| CatalogAdminPassword | Heslo správce existujícího serveru Azure SQL Database nebo spravované Instance (Preview). |
| CatalogPricingTier | Cenovou úroveň pro SSISDB hostované existujícího serveru Azure SQL Database.  Neplatí pro Azure spravované Instance SQL (Preview) hostování SSISDB. |
| VNetId | Virtuální síť ID prostředku pro vaše runtime integrace Azure SSIS pro připojení. |
| Podsíť | Název podsítě pro vaše integrace Azure SSIS modulu runtime pro připojení. |
| ID | ID prostředku vaší runtime integrace Azure SSIS. |
| Typ | Typ (spravované nebo samoobslužných-Hosted) vaší runtime integrace Azure SSIS. |
| ResourceGroupName | Název vaší skupiny prostředků Azure, ve kterém byly vytvořeny pro vytváření dat a integrace Azure SSIS modulu runtime. |
| DataFactoryName | Název vaší služby Azure data factory. |
| Název | Název vašeho runtime integrace Azure SSIS. |
| Popis | Popis vaší runtime integrace Azure SSIS. |

  
### <a name="status-per-node"></a>Stav (podle uzlu)

| Status | Popis |
| ------ | ----------- | 
| Spouštění | Připravuje se tento uzel. |
| K dispozici. | Tento uzel je připraven k nasazení a spouštění balíčků SSIS. |
| Recyklace | Tento uzel je právě opravit nebo restartování. |
| Není k dispozici | Tento uzel není připraven k nasazení a spouštění balíčků SSIS a je možné použít chyby nebo problémy, které by mohly vyřešit. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Stav (celkové Azure SSIS integrace runtime)

| Celkový stav | Popis | 
| -------------- | ----------- | 
| Počáteční | Uzly vaší runtime integrace Azure SSIS nebyly přidělené připravený. | 
| Spouštění | Uzly vaší runtime integrace Azure SSIS probíhá přidělené připravené a fakturace bylo zahájeno. |
| Spuštěno | Uzly vaší runtime integrace Azure SSIS byly přiděleny připravené a jsou připraveny k nasazení a spouštění balíčků SSIS. |
| Zastavování  | Uzly vaší runtime integrace Azure SSIS vydávají. |
| Zastaveno | Uzly vaší runtime integrace Azure SSIS byly vydány a fakturace byla zastavena. |

Najdete další informace o běhu integrace Azure SSIS v následujících článcích:

- [Modul Runtime integrace Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje koncepční informace o integraci runtimes obecně včetně infračerveného signálu Azure SSIS. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny pro vytvoření Azure-SSIS IR a využívá databázi Azure SQL k hostování katalogu SSIS. 
- [Postup: Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) Tento článek rozšíří na kurz a poskytuje pokyny o používání spravované Instance Azure SQL (Preview) a připojení k virtuální síti reakcí na Incidenty. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Tento článek ukazuje, jak zastavit, spustit nebo odebrat Azure-SSIS IR. Ukazuje také postup horizontálního navýšení kapacity Azure-SSIS IR přidáním více uzlů. 
- [Připojení Azure SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek obsahuje koncepční informace o připojení Azure SSIS Reakcí do virtuální sítě Azure. Poskytuje také kroky konfigurace virtuální sítě, aby IR Azure SSIS možné připojit virtuální sítě pomocí portálu Azure. 

## <a name="next-steps"></a>Další postup
Najdete v následujících článcích pro monitorování kanály různými způsoby: 

- [Rychlý úvod: Vytvořte objekt pro vytváření dat](quickstart-create-data-factory-dot-net.md).
- [Použijte nástroj Sledování Azure monitorování služby Data Factory kanálů](monitor-using-azure-monitor.md)
