---
title: Monitorujte své aplikace bez změn kódu – automatické instrumentace pro Azure Monitor Application Insights | Microsoft Docs
description: Přehled automatické instrumentace pro Azure Monitor správu výkonu aplikací NES kódováním Application Insights
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 87342dcd316b0364522baa01e632b704665c998e
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2020
ms.locfileid: "85363753"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Co je automatické instrumentace nebo připojení s nekódováním Azure Monitor Application Insights?

Automatické instrumentace nebo připojení bez kódu umožňuje povolit monitorování aplikací pomocí Application Insights beze změny kódu.  

Application Insights je integrován s různými poskytovateli prostředků a pracuje v různých prostředích. V podstatě je to vše, co musíte udělat, a to v některých případech – nakonfigurujte agenta, který bude shromažďovat telemetrii ze seznamu. V takovém případě uvidíte metriky, data a závislosti v prostředku Application Insights, což vám umožní vymezit zdroj potenciálních problémů a analyzovat hlavní příčinu pomocí zobrazení koncových transakcí.

## <a name="supported-environments-languages-and-resource-providers"></a>Podporovaná prostředí, jazyky a poskytovatelé prostředků

Po přidání dalších a dalších integrací se matice schopností automatické instrumentace stane složitou. Následující tabulka ukazuje aktuální stav otázky, pokud je to podpora různých poskytovatelů prostředků, jazyků a prostředí.

|Prostředí/poskytovatel prostředků | .NET            | .NET Core       | Java            | Node.js         |
|------------------------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service ve Windows  | GA, OnBD *       | GA, výslovný souhlas      | Privátní verze Preview | Privátní verze Preview |
|Azure App Service v systému Linux    | –             | Nepodporuje se   | Public Preview  | Public Preview  |
|Azure App Service na AKS      | –             | V návrhu       | V návrhu       | V návrhu       |
|Azure Functions – Basic       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       |
|Azure Functions – závislosti| Nepodporuje se   | Nepodporuje se   | Public Preview  | Nepodporuje se   |
|Azure Kubernetes Service      | –             | V návrhu       | Prostřednictvím agenta   | V návrhu       |
|Okna virtuálních počítačů Azure             | Public Preview  | Nepodporuje se   | Nepodporuje se   | Nepodporuje se   |
|Okna místních virtuálních počítačů       | GA, výslovný souhlas      | Nepodporuje se   | Prostřednictvím agenta   | Nepodporuje se   |
|Samostatný agent – libovolný env.   | Nepodporuje se   | Nepodporuje se   | Public Preview  | Nepodporuje se   |

* OnBD ve výchozím nastavení je krátké – Application Insights se po nasazení aplikace do podporovaných prostředí automaticky povolí. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

[Monitorování aplikací v Azure App Service](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=net) je k dispozici pro aplikaci .NET a je ve výchozím nastavení povoleno, rozhraní .NET Core lze povolit jediným kliknutím a jazyky Java a Node.js jsou v privátní verzi Preview.

### <a name="linux"></a>Linux 

Monitorování aplikací Java a Node.js v App Service je ve verzi Public Preview a je možné je povolit v Azure Portal, které jsou k dispozici ve všech oblastech.

## <a name="azure-functions"></a>Azure Functions

Základní monitorování pro Azure Functions je ve výchozím nastavení povolené pro shromažďování protokolů, výkonu, chybových dat a požadavků HTTP. Pro aplikace Java můžete povolit rozšířené monitorování s distribuovaným trasováním a získat podrobnosti o kompletní transakci. Tato funkce pro jazyk Java je ve verzi Public Preview a můžete [ji povolit v Azure Portal](https://docs.microsoft.com/azure/azure-monitor/app/monitor-functions).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Instrumentace služby Azure Kubernetes bez kódu je teď k dispozici pro aplikace v jazyce Java prostřednictvím [samostatného agenta](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Virtuální počítače Azure s Windows a sada škálování virtuálních počítačů

Pro aplikace .NET je k dispozici [Automatické instrumentace pro virtuální počítače Azure a sadu škálování virtuálního počítače](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps) . 

## <a name="on-premises-servers"></a>Místní servery
Můžete snadno povolit monitorování pro [místní Windows servery pro aplikace .NET](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) a pro [aplikace v jazyce Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent).

## <a name="other-environments"></a>Další prostředí
Univerzální samostatný agent Java funguje v jakémkoli prostředí, není potřeba instrumentovat kód. [Postupujte podle pokynů v průvodci](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) a zapněte si Application Insights a přečtěte si o úžasnéch funkcích agenta Java. Agent je ve verzi Public Preview a dostupný ve všech oblastech. 

## <a name="next-steps"></a>Další kroky

* [Přehled Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* [Mapa aplikace](./../../azure-monitor/app/app-map.md)
* [Monitorování výkonu na konci](./../../azure-monitor/learn/tutorial-performance.md)
