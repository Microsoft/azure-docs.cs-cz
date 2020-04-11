---
title: Vyřazení api nasazení pro metriky Azure Monitoru a automatické škálování
description: Metriky a automatické škálování klasických api, označované také jako Azure Service Management (ASM) nebo RDFE model nasazení vyřazené
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: d9b6118bc165a9b0a78ae9c4da895719249b22b7
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114227"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor vyřazení klasických typů API modelu nasazení pro metriky a automatické škálování

Azure Monitor (dříve Azure Insights při prvním vydání) v současné době nabízí možnost vytvářet a spravovat nastavení automatického škálování pro a využívat metriky z klasických virtuálních počítačů a klasických cloudových služeb. Původní sada klasických řešení API založených na modelu nasazení bude **vyřazena po 30 června 2019** ve všech veřejných a privátních cloudech Azure ve všech oblastech.   

Stejné operace byly podporovány prostřednictvím sady azure resource manager na základě API pro více než rok. Portál Azure používá nová api REST pro automatické škálování i metriky. K dispozici je také nová sada SDK, prostředí PowerShell a rozhraní příkazového příkazového příkazu na základě těchto rozhraní API správce prostředků. Naše partnerské služby pro monitorování spotřebovávají nová řešení REST API založená na Správci prostředků ve službě Azure Monitor.  

## <a name="who-is-not-affected"></a>Kdo není ovlivněn

Pokud spravujete automatické škálování prostřednictvím portálu Azure, nové azure [monitor sdk](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), PowerShell, CLI nebo Resource Manager šablony, žádná akce je nutné.  

Pokud spotřebováváte metriky prostřednictvím portálu Azure nebo prostřednictvím různých [služeb partnerů pro monitorování](../../azure-monitor/platform/partners.md), není nutná žádná akce. Společnost Microsoft spolupracuje s partnery pro monitorování na migraci do nových rozhraní API.

## <a name="who-is-affected"></a>Kdo je ovlivněn

Tento článek se vás týká, pokud používáte následující součásti:

- **Klasická sada Azure Insights SDK** – pokud používáte [klasickou sadu Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), přepněte na použití nové sady Azure Monitor SDK pro [.NET](https://github.com/azure/azure-libraries-for-net#download) nebo [Javu](https://github.com/azure/azure-libraries-for-java#download). Stáhněte si [balíček Azure Monitor SDK NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Klasické automatické škálování** – Pokud voláte [rozhraní API pro klasická nastavení automatického škálování](https://msdn.microsoft.com/library/azure/mt348562.aspx) z vlastních nástrojů nebo používáte klasickou sadu [Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), měli byste přepnout na použití [rozhraní API REST služby Resource Manager Azure Monitor](https://docs.microsoft.com/rest/api/monitor/autoscalesettings).

- **Klasické metriky** – pokud spotřebováváte metriky pomocí [klasických rozhraní REST API](https://msdn.microsoft.com/library/azure/dn510374.aspx) nebo [klasické sady Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) z vlastních nástrojů, měli byste přepnout na použití [rozhraní RESOURCE Manager Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings). 

Pokud si nejste jisti, jestli váš kód nebo vlastní nástroje volají klasická api, podívejte se na následující:

- Zkontrolujte identifikátor URI, na který odkazuje váš kód nebo nástroj. Klasická api používají https://management.core.windows.netidentifikátor URI . Měli byste používat novější identifikátor URI pro řešení API `https://management.azure.com/`založené na Správci prostředků, které začínají na .

- Porovnejte název sestavy v počítači. Starší klasická sestava https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/je na .

- Pokud používáte ověřování certifikátů pro přístup k metrikám nebo automatickým škálování api, používáte klasický koncový bod a knihovnu. Novější rozhraní API Správce prostředků vyžadují ověřování Azure Active Directory prostřednictvím instančního objektu nebo uživatelského objektu uživatele.

- Pokud používáte volání odkazovaná v dokumentaci na některý z následujících odkazů, používáte starší klasická api.

  - [Knihovna tříd Windows.Azure.Management.Monitoring](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [Monitorování (klasické) .NET](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [Rozhraní ImetricOperations](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Proč byste měli přepnout

Všechny existující funkce pro automatické škálování a metriky budou nadále fungovat prostřednictvím nových api.  

Migrace na novější api jsou dodávána s funkcemi založenými na Správci prostředků, jako je například podpora konzistentního řízení přístupu na základě rolí (RBAC) ve všech monitorovacích službách. Získáte také další funkce pro metriky: 

- podpora rozměrů
- konzistentní 1-minutové metriky rozlišovací schopnost ve všech službách 
- lepší dotazování
- vyšší uchovávání dat (93 dní metrik vs. 30 dní) 

Celkově, stejně jako všechny ostatní služby v Azure, azure monitor na základě Správce prostředků api na bázi dat přijít s lepší výkon, škálovatelnost a spolehlivost. 

## <a name="what-happens-if-you-do-not-migrate"></a>Co se stane, pokud nemigrujete

### <a name="before-retirement"></a>Před odchodem do důchodu

Nebude mít žádný přímý dopad na vaše služby Azure nebo jejich úlohy.  

### <a name="after-retirement"></a>Po odchodu do důchodu

Všechna volání klasických rozhraní API uvedených dříve se nezdaří a vrátí chybové zprávy podobné následující:

Pro automatické škálování: *Toto rozhraní API bylo zastaralé. Ke správě nastavení automatického škálování použijte šablony Azure Portal, Azure Monitor SDK, PowerShell, CLI nebo Resource Manager*.  

Pro metriky: *Toto rozhraní API bylo zastaralé. Použijte portál Azure, Azure Monitor SDK, PowerShell, CLI k dotazování na metriky*.

## <a name="email-notifications"></a>E-mailová oznámení

Na e-mailové adresy pro následující role účtu bylo odesláno oznámení o vyřazení: 

- Správci účtů a služeb
- Spolusprávci  

Máte-li jakékoli dotazy, MonitorClassicAPIhelp@microsoft.comkontaktujte nás na adrese .  

## <a name="references"></a>Odkazy

- [Novější api rest pro Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) 
- [Novější sada Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
