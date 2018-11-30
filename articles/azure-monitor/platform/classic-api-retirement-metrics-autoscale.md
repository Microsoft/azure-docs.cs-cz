---
title: Azure Monitor vyřazení z modelu nasazení classic rozhraní API pro metriky a automatické škálování
description: Metriky a automatické škálování klasického rozhraní API, označovaný taky jako Azure Service Management (ASM) nebo model nasazení RDFE vyřazuje
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: robb
ms.component: ''
ms.openlocfilehash: 450eac3613df18997c5b0f9189555555da20cfcf
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585516"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor vyřazení z modelu nasazení classic rozhraní API pro metriky a automatické škálování

Azure Monitor (dříve Azure Insights při prvním vydání) v současné době nabízí možnost vytvářet a spravovat nastavení automatického škálování a využívat metriky z klasické virtuální počítače a cloudové služby classic. Původní sada bude rozhraní API založená na modelu nasazení classic **vyřazeno po 30. června 2019** ve všech veřejných a privátních cloudů Azure ve všech oblastech.   

Stejné operace jsou podporovány pomocí sady Azure Resource Manageru na základě rozhraní API pro průběhu roku. Na webu Azure portal používá nové rozhraní REST API pro automatické škálování a metriky. Nové sady SDK, prostředí PowerShell a rozhraní příkazového řádku založené na těchto rozhraní API Resource Manageru jsou také k dispozici. Ke sledování služby našich partnerů využívat nové rozhraní REST API ve službě Azure Monitor využívající Resource Manager.  

## <a name="who-is-not-affected"></a>Kdo to se týká

Pokud spravujete automatické škálování prostřednictvím webu Azure portal, [novou sadu SDK Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), šablon Resource Manageru, rozhraní příkazového řádku nebo Powershellu, není nutná žádná akce.  

Pokud jsou využívání metriky přes Azure portal nebo prostřednictvím různých [monitorování partnerské služby](../../monitoring-and-diagnostics/monitoring-partners.md), není nutná žádná akce. Microsoft ve spolupráci s partnery, aby migrovali na nové rozhraní API pro monitorování.

## <a name="who-is-affected"></a>Kdo to se týká

Tento článek se týká, pokud použijete následující komponenty:

- **Klasický Azure Insights SDK** – Pokud používáte [classic Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), přejít k používání nové monitorování Azure SDK for [.NET](https://github.com/azure/azure-libraries-for-net#download) nebo [Java](https://github.com/azure/azure-libraries-for-java#download). Stáhněte si [balíček NuGet sady SDK služby Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Klasické automatického škálování** – Pokud voláte [nastavení automatického škálování klasického rozhraní API](https://msdn.microsoft.com/library/azure/mt348562.aspx) ze svých nástrojů vlastními silami sestavených nebo pomocí [classic Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), byste měli přejít na použití [ Rozhraní REST API služby Správce prostředků Azure monitoru](https://docs.microsoft.com/rest/api/monitor/autoscalesettings).

- **Klasické metriky** – Pokud jste využívání pomocí metrik [klasické rozhraní REST API](https://msdn.microsoft.com/library/azure/dn510374.aspx) nebo [classic Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) z vlastními silami sestavených nástroje byste měli přejít k použití [ Rozhraní REST API služby Správce prostředků Azure monitoru](https://docs.microsoft.com/rest/api/monitor/autoscalesettings). 

Pokud si nejste jisti, zda jsou klasické rozhraní API volání kódu nebo vlastních nástrojů, podívejte se na následující:

- Zkontrolujte identifikátor URI odkazovat v kódu nebo nástroj. Klasické rozhraní API použijte identifikátor URI https://management.core.windows.net. Byste měli použít novější identifikátor URI pro správce prostředků založené na rozhraní API začíná https://management.azure.com/.

- Porovnání názvu sestavení na svém počítači. Starší classic sestavení je v https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/.

- Pokud používáte ověřování pomocí certifikátu pro přístup k metriky nebo automatické škálování rozhraní API, musíte používat klasické koncového bodu a knihovny. Novější rozhraní API Resource Manageru vyžaduje ověřování Azure Active Directory prostřednictvím objektu zabezpečení uživatele nebo instanční objekt.

- Pokud používáte volání, které jsou uvedeny v dokumentaci na libovolné z následujících odkazů, používáte starší klasické rozhraní API.

  - [Knihovna tříd Windows.Azure.Management.Monitoring](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [Monitorování .NET (classic)](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations rozhraní](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Proč byste měli přejít

Všechny stávající možnosti automatického škálování a metriky se budou nadále fungovat prostřednictvím nových rozhraní API.  

Migrace přes na novější funkce založené na Resource Manageru, jako třeba podporu pro konzistentní vzhledem k aplikacím Role-Based Access Control (RBAC) napříč všemi službami monitorování součástí rozhraní API. Získáte také další funkce pro metriky: 

- Podpora pro dimenze
- konzistentní členitosti metriky 1 minuty napříč všemi službami 
- lepší dotazování
- vyšší uchovávání dat (93 dní metrik vs. 30 dní) 

Celkové, stejně jako všechny ostatní služby v Azure Resource Manageru, na základě Azure Monitor, rozhraní API jsou součástí lepší výkon, škálovatelnost a spolehlivost. 

## <a name="what-happens-if-you-do-not-migrate"></a>Co se stane, pokud neprovedete migraci

### <a name="before-retirement"></a>Před vyřazení z provozu

Nebude existovat žádný přímý vliv na služby Azure nebo jejich úloh.  

### <a name="after-retirement"></a>Po vyřazení

Všechna volání do klasického rozhraní API uvedené dříve selže a vrátí chybové zprávy podobné následující dotazy:

Pro automatické škálování: *toto rozhraní API je zastaralá. Správa nastavení automatického škálování pomocí webu Azure portal, sady SDK monitorování Azure, Powershellu, rozhraní příkazového řádku, šablon Resource Manageru nebo*.  

Pro metriky: *toto rozhraní API je zastaralá. Použití webu Azure portal, sady SDK monitorování Azure, Powershellu, rozhraní příkazového řádku k dotazování pro metriky*.

## <a name="email-notifications"></a>E-mailová oznámení

Oznámení o vyřazení byla odeslána na e-mailové adresy pro tyto role účet: 

- Správci účtu a služby
- Spolusprávci  

Pokud máte nějaké dotazy, kontaktujte nás na adrese MonitorClassicAPIhelp@microsoft.com.  

## <a name="references"></a>Odkazy

- [Novější rozhraní REST API pro Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) 
- [Azure Monitor novější sada SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
