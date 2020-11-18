---
title: Monitorujte své aplikace bez změn kódu – automatické instrumentace pro Azure Monitor Application Insights | Microsoft Docs
description: Přehled automatické instrumentace pro Azure Monitor správu výkonu aplikací NES kódováním Application Insights
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: aa5c2a0070ea0c3a0963f97fc1a04670eeaa4827
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701887"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Co je automatické instrumentace nebo připojení s nekódováním Azure Monitor Application Insights?

Automatické instrumentace nebo připojení bez kódu umožňuje povolit monitorování aplikací pomocí Application Insights beze změny kódu.  

Application Insights je integrován s různými poskytovateli prostředků a pracuje v různých prostředích. V podstatě je to vše, co musíte udělat, a to v některých případech – nakonfigurujte agenta, který bude shromažďovat telemetrii ze seznamu. V takovém případě uvidíte metriky, data a závislosti v prostředku Application Insights, což vám umožní vymezit zdroj potenciálních problémů a analyzovat hlavní příčinu pomocí zobrazení koncových transakcí.

## <a name="supported-environments-languages-and-resource-providers"></a>Podporovaná prostředí, jazyky a poskytovatelé prostředků

Po přidání dalších a dalších integrací se matice schopností automatické instrumentace stane složitou. Následující tabulka ukazuje aktuální stav otázky, pokud je to podpora různých poskytovatelů prostředků, jazyků a prostředí.

|Prostředí/poskytovatel prostředků          | .NET            | .NET Core       | Java            | Node.js         |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service ve Windows           | GA, OnBD *       | GA, výslovný souhlas      | Privátní verze Preview | Privátní verze Preview |
|Azure App Service v systému Linux             | –             | Nepodporováno   | Privátní verze Preview | Public Preview  |
|Azure App Service na AKS               | –             | V návrhu       | V návrhu       | V návrhu       |
|Azure Functions – Basic                | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       |
|Azure Functions závislosti Windows | Nepodporováno   | Nepodporováno   | Public Preview  | Nepodporováno   |
|Azure Kubernetes Service               | –             | V návrhu       | Prostřednictvím agenta   | V návrhu       |
|Okna virtuálních počítačů Azure                      | Public Preview  | Nepodporováno   | Nepodporováno   | Nepodporováno   |
|Okna místních virtuálních počítačů                | GA, výslovný souhlas      | Nepodporováno   | Prostřednictvím agenta   | Nepodporováno   |
|Samostatný agent – libovolný env.            | Nepodporováno   | Nepodporováno   | GA              | Nepodporováno   |

* OnBD ve výchozím nastavení je krátké – Application Insights se po nasazení aplikace do podporovaných prostředí automaticky povolí. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

[Monitorování aplikací v Azure App Service](./azure-web-apps.md?tabs=net) je k dispozici pro aplikaci .NET a je ve výchozím nastavení povoleno, rozhraní .NET Core lze povolit jediným kliknutím a jazyky Java a Node.js jsou v privátní verzi Preview.

### <a name="linux"></a>Linux 

Monitorování aplikací Java a Node.js v App Service je ve verzi Public Preview a je možné je povolit v Azure Portal, které jsou k dispozici ve všech oblastech.

## <a name="azure-functions"></a>Azure Functions

Základní monitorování pro Azure Functions je ve výchozím nastavení povolené pro shromažďování protokolů, výkonu, chybových dat a požadavků HTTP. Pro aplikace Java můžete povolit rozšířené monitorování s distribuovaným trasováním a získat podrobnosti o kompletní transakci. Tato funkce pro jazyk Java je ve verzi Public Preview a můžete [ji povolit v Azure Portal](./monitor-functions.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Instrumentace služby Azure Kubernetes bez kódu je teď k dispozici pro aplikace v jazyce Java prostřednictvím [samostatného agenta](./java-in-process-agent.md). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Virtuální počítače Azure s Windows a sada škálování virtuálních počítačů

Pro aplikace .NET je k dispozici [Automatické instrumentace pro virtuální počítače Azure a sadu škálování virtuálního počítače](./azure-vm-vmss-apps.md) . 

## <a name="on-premises-servers"></a>Místní servery
Můžete snadno povolit monitorování pro [místní Windows servery pro aplikace .NET](./status-monitor-v2-overview.md) a pro [aplikace v jazyce Java](./java-in-process-agent.md).

## <a name="other-environments"></a>Další prostředí
Univerzální samostatný agent Java funguje v jakémkoli prostředí, není potřeba instrumentovat kód. [Postupujte podle pokynů v průvodci](./java-in-process-agent.md) a zapněte si Application Insights a přečtěte si o úžasnéch funkcích agenta Java. Agent je ve verzi Public Preview a dostupný ve všech oblastech. 

## <a name="next-steps"></a>Další kroky

* [Přehled Application Insights](./app-insights-overview.md)
* [Mapa aplikace](./app-map.md)
* [Monitorování výkonu na konci](../learn/tutorial-performance.md)

