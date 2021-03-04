---
title: Monitorujte své aplikace bez změn kódu – automatické instrumentace pro Azure Monitor Application Insights | Microsoft Docs
description: Přehled automatické instrumentace pro Azure Monitor správu výkonu aplikací NES kódováním Application Insights
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 9ead123338a410daf53569ff577dfc8c728a8ddf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708488"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Co je automatické instrumentace nebo připojení s nekódováním Azure Monitor Application Insights?

Automatické instrumentace nebo připojení bez kódu umožňuje povolit monitorování aplikací pomocí Application Insights beze změny kódu.  

Application Insights je integrován s různými poskytovateli prostředků a pracuje v různých prostředích. V podstatě je to vše, co musíte udělat, a to v některých případech – nakonfigurujte agenta, který bude automaticky shromažďovat telemetrii. V takovém případě uvidíte metriky, data a závislosti v prostředku Application Insights, což vám umožní vymezit zdroj potenciálních problémů a analyzovat hlavní příčinu pomocí zobrazení koncových transakcí.

## <a name="supported-environments-languages-and-resource-providers"></a>Podporovaná prostředí, jazyky a poskytovatelé prostředků

Po přidání dalších integrací se matice schopností automatické instrumentace stane složitou. Následující tabulka ukazuje aktuální stav otázky, pokud je to podpora různých poskytovatelů prostředků, jazyků a prostředí.

|Prostředí/poskytovatel prostředků          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service ve Windows           | GA, OnBD *       | GA, výslovný souhlas      | Rozpracované     | Rozpracované     | Nepodporováno   |
|Azure App Service v systému Linux             | –             | Nepodporováno   | Rozpracované     | Public Preview  | Nepodporováno   |
|Azure App Service na AKS               | –             | V návrhu       | V návrhu       | V návrhu       | Nepodporováno   |
|Azure Functions – Basic                | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       |
|Azure Functions závislosti Windows | Nepodporováno   | Nepodporováno   | Public Preview  | Nepodporováno   | Nepodporováno   |
|Azure Kubernetes Service               | –             | V návrhu       | Prostřednictvím agenta   | V návrhu       | Nepodporováno   |
|Okna virtuálních počítačů Azure                      | Public Preview  | Nepodporováno   | Nepodporováno   | Nepodporováno   | Nepodporováno   |
|Okna místních virtuálních počítačů                | GA, výslovný souhlas      | Nepodporováno   | Prostřednictvím agenta   | Nepodporováno   | Nepodporováno   |
|Samostatný agent – libovolný env.            | Nepodporováno   | Nepodporováno   | GA              | Nepodporováno   | Nepodporováno   |

* OnBD ve výchozím nastavení je krátké – Application Insights se po nasazení aplikace do podporovaných prostředí automaticky povolí. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

#### <a name="net"></a>.NET
Monitorování aplikací na Azure App Service ve Windows je dostupné pro .NET pro [aplikace](./azure-web-apps.md?tabs=net) .NET a ve výchozím nastavení je povolené.

#### <a name="netcore"></a>. NETCore
Monitorování pro [. NETCore aplikace](./azure-web-apps.md?tabs=netcore) lze povolit jediným kliknutím.

#### <a name="java"></a>Java
Integrace portálu pro monitorování aplikací Java v App Service ve Windows není momentálně k dispozici. do své aplikace ale můžete přidat Application Insights [samostatného agenta java 3,0](./java-in-process-agent.md) , aniž byste museli změny kódu nasadit do App Service. Agent Application Insights Java 3,0 je všeobecně dostupný.

#### <a name="nodejs"></a>Node.js
Monitorování pro Node.js aplikace ve Windows se momentálně nedá na portálu povolit. K monitorování aplikací Node.js použijte [sadu SDK](./nodejs.md).

### <a name="linux"></a>Linux

#### <a name="netcore"></a>. NETCore
K monitorování. NETCore aplikace spuštěné v systému Linux, použijte [sadu SDK](./asp-net-core.md).

#### <a name="java"></a>Java 
Povolení monitorování aplikací Java pro App Service v systému Linux z portálu není k dispozici, ale před nasazením aplikací do App Service můžete do aplikace přidat [Application Insights agenta java 3,0](./java-in-process-agent.md) . Agent Application Insights Java 3,0 je všeobecně dostupný.

#### <a name="nodejs"></a>Node.js
[Monitorování Node.js aplikací v App Service v systému Linux](./azure-web-apps.md?tabs=nodejs) je ve verzi Public Preview a je možné je povolit v Azure Portal, které jsou k dispozici ve všech oblastech. 

#### <a name="python"></a>Python
Použití sady SDK k [monitorování aplikace v Pythonu](./opencensus-python.md) 

## <a name="azure-functions"></a>Azure Functions

Základní monitorování pro Azure Functions je ve výchozím nastavení povolené pro shromažďování protokolů, výkonu, chybových dat a požadavků HTTP. Pro aplikace Java můžete povolit rozšířené monitorování s distribuovaným trasováním a získat podrobnosti o kompletní transakci. Tato funkce pro jazyk Java je ve verzi Public Preview a můžete [ji povolit v Azure Portal](./monitor-functions.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Instrumentace služby Azure Kubernetes bez kódu je teď k dispozici pro aplikace v jazyce Java prostřednictvím [samostatného agenta](./java-in-process-agent.md). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Virtuální počítače Azure s Windows a sada škálování virtuálních počítačů

Pro [.NET](./azure-vm-vmss-apps.md) a [Java](./java-in-process-agent.md)jsou k dispozici automatické instrumentace pro virtuální počítače Azure a sadu škálování virtuálního počítače.  

## <a name="on-premises-servers"></a>Místní servery
Můžete snadno povolit monitorování pro [místní Windows servery pro aplikace .NET](./status-monitor-v2-overview.md) a pro [aplikace v jazyce Java](./java-in-process-agent.md).

## <a name="other-environments"></a>Další prostředí
Univerzální samostatný agent Java funguje v jakémkoli prostředí, není potřeba instrumentovat kód. [Postupujte podle pokynů v průvodci](./java-in-process-agent.md) a zapněte si Application Insights a přečtěte si o úžasnéch funkcích agenta Java. Agent je ve verzi Public Preview a dostupný ve všech oblastech. 

## <a name="next-steps"></a>Další kroky

* [Přehled Application Insights](./app-insights-overview.md)
* [Mapa aplikace](./app-map.md)
* [Monitorování výkonu na konci](../app/tutorial-performance.md)