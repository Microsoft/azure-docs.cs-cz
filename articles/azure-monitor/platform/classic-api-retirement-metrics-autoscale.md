---
title: Vyřadit rozhraní API pro nasazení Azure Monitor metrik a automatického škálování
description: Vyřazení metrik a automatické škálování klasických rozhraní API, označovaných také jako Azure Service Management (ASM) nebo model nasazení RDFE
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 38a30f2adc0de7ccb9a9a3a4ba7ed53fd5fda3f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87317378"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor vyřazení rozhraní API modelu nasazení Classic pro metriky a automatické škálování

Azure Monitor (dřív vydaná služba Azure Insights) v současné době nabízí možnost vytvářet a spravovat nastavení automatického škálování a využívat metriky z klasických virtuálních počítačů a klasických Cloud Services. Původní sada rozhraní API založených na modelu nasazení Classic bude vycházet z **30. června 2019** ve všech veřejných a privátních cloudech Azure ve všech oblastech.   

Stejné operace byly podporovány prostřednictvím sady rozhraní API založených na Azure Resource Manager po dobu v roce. Azure Portal používá nová rozhraní REST API pro automatické škálování a metriky. K dispozici jsou také nové sady SDK, PowerShell a rozhraní příkazového řádku založené na těchto Správce prostředků API. Naše partnerské služby pro monitorování využívají v Azure Monitor nová rozhraní REST API založená na Správce prostředků.  

## <a name="who-is-not-affected"></a>Kdo to nemá vliv

Pokud spravujete automatické škálování prostřednictvím Azure Portal, [nové šablony Azure monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), PowerShellu, CLI nebo správce prostředků nemusíte dělat nic.  

Pokud používáte metriky prostřednictvím Azure Portal nebo prostřednictvím různých [partnerských monitorovacích služeb](./partners.md), není nutná žádná akce. Microsoft pracuje s monitorováním partnerů k migraci na nová rozhraní API.

## <a name="who-is-affected"></a>Kdo je ovlivněn

Tento článek se týká, pokud používáte následující součásti:

- **Klasická sada Azure Insights SDK** – Pokud používáte [klasickou sadu Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), přepněte se na použití nové sady Azure monitor SDK pro [.NET](https://github.com/azure/azure-libraries-for-net#download) nebo [Java](https://github.com/azure/azure-libraries-for-java#download). Stáhněte [balíček NuGet sady Azure monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Klasické automatické škálování** – Pokud voláte [rozhraní API klasického nastavení automatického škálování](/previous-versions/azure/reference/mt348562(v=azure.100)) ze svých vlastních nástrojů nebo pomocí [klasické sady Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), měli byste přepnout na použití [Správce prostředků Azure monitor REST API](/rest/api/monitor/autoscalesettings).

- **Klasické metriky** – Pokud pracujete s metrikami pomocí [klasických rozhraní REST API](/previous-versions/azure/reference/dn510374(v=azure.100)) nebo  [sady Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) od vlastních nástrojů, měli byste přepnout na použití [Správce prostředků Azure monitor REST API](/rest/api/monitor/autoscalesettings). 

Pokud si nejste jistí, jestli váš kód nebo vlastní nástroje volají rozhraní API Classic, podívejte se na následující:

- Zkontrolujte identifikátor URI, na který se odkazuje v kódu nebo nástroji. Rozhraní API Classic používají identifikátor URI https://management.core.windows.net . Měli byste používat novější identifikátor URI pro rozhraní API založená na Správce prostředků začíná na `https://management.azure.com/` .

- Porovnejte název sestavení v počítači. Starší sestavení Classic je na  https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/ .

- Pokud k přístupu k metrikám nebo rozhraním API automatického škálování používáte ověřování pomocí certifikátů, používáte klasický koncový bod a knihovnu. Novější rozhraní Správce prostředků API vyžadují Azure Active Directory ověřování prostřednictvím instančního objektu nebo objektu zabezpečení uživatele.

- Pokud používáte volání odkazovaná v dokumentaci na jakémkoli z následujících odkazů, používáte starší rozhraní API Classic.

  - [Knihovna tříd Windows. Azure. Management. Monitoring](/previous-versions/azure/dn510414(v=azure.100))

  - [Monitorování (klasické rozhraní) .NET](/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [Rozhraní IMetricOperations](/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Proč byste měli přepnout

Všechny existující funkce pro automatické škálování a metriky budou i nadále fungovat prostřednictvím nových rozhraní API.  

Migrace přes do novějších rozhraní API přináší funkce založené na Správce prostředků, jako je podpora konzistentních Role-Based Access Control (RBAC) napříč všemi vašimi monitorovacími službami. Získáte také další funkce pro metriky: 

- Podpora dimenzí
- konzistentní členitost metriky o úrovni 1 minuty napříč všemi službami 
- lepší dotazování
- vyšší Doba uchovávání dat (93 dní metriky vs. 30 dní) 

Stejně jako u všech ostatních služeb v Azure přináší Správce prostředků rozhraní API pro Azure Monitor vycházející s vyšším výkonem, škálovatelností a spolehlivostí. 

## <a name="what-happens-if-you-do-not-migrate"></a>Co se stane, když nemigrujete

### <a name="before-retirement"></a>Před vyřazením

Vaše služby Azure ani jejich úlohy nebudou mít přímý vliv.  

### <a name="after-retirement"></a>Po vyřazení

Všechna volání klasických rozhraní API uvedených výše selžou a vrátí chybové zprávy podobné následujícím:

Pro automatické škálování: *Toto rozhraní API je zastaralé. Pomocí Azure Portal, Azure Monitor SDK, PowerShellu, rozhraní příkazového řádku nebo šablon Správce prostředků můžete spravovat nastavení automatického škálování*.  

Pro metriky: *Toto rozhraní API je zastaralé. K dotazování na metriky použijte Azure Portal, Azure Monitor SDK, PowerShell, rozhraní příkazového řádku*.

## <a name="email-notifications"></a>E-mailová oznámení

Oznámení o vyřazení bylo odesláno na e-mailové adresy následujících rolí účtu: 

- Správci účtů a služeb
- Spolusprávci  

Pokud máte nějaké dotazy, kontaktujte nás na adrese MonitorClassicAPIhelp@microsoft.com .  

## <a name="references"></a>Reference

- [Novější rozhraní REST API pro Azure Monitor](/rest/api/monitor/) 
- [Novější sada Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)

