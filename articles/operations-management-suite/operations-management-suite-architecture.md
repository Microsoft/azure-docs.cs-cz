---
title: Architektura Operations Management Suite (OMS) | Dokumentace Microsoftu
description: Microsoft Operations Management Suite (OMS) je cloudové řešení společnosti Microsoft pro správu IT, které pomáhá se správou a ochranou místních a cloudových infrastruktur.  Tento článek identifikuje různé služby zahrnuté v OMS a poskytuje odkazy na podrobné informace.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 40e41686-7e35-4d85-bbe8-edbcb295a534
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: fb4e9150c1069d48399fb217f865b294ccd317dc
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2017
ms.locfileid: "26683261"
---
# <a name="oms-architecture"></a>Architektura OMS
[Operations Management Suite (OMS)](https://azure.microsoft.com/documentation/services/operations-management-suite/) je kolekce cloudových služeb pro správu vašich místních a cloudových prostředí.  Tento článek popisuje různé místní a cloudové komponenty OMS a jejich základní cloudovou výpočetní architekturu.  Další podrobnosti najdete v dokumentaci k jednotlivým službám.

## <a name="log-analytics"></a>Log Analytics
Všechna data shromážděná službou [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) se ukládají v úložišti OMS hostovaném v Azure.  Připojené zdroje generují data, která se shromažďují v úložišti OMS.  V současné době jsou podporované tři typy připojených zdrojů.

* Agent nainstalovaný na počítači s [Windows](../log-analytics/log-analytics-windows-agent.md) nebo [Linuxem](../log-analytics/log-analytics-linux-agents.md), který je připojený přímo k OMS.
* Skupina pro správu nástroje System Center Operations Manager (SCOM), která je [připojená k Log Analytics](../log-analytics/log-analytics-om-agents.md).  Agenti nástroje SCOM nadále komunikují se servery pro správu, které přeposílají události a data o výkonu do Log Analytics.
* [Účet služby Azure Storage](../log-analytics/log-analytics-azure-storage.md), který shromažďuje data [Diagnostiky Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) z role pracovního procesu, webové role nebo virtuálního počítače v Azure.

Zdroje dat definují data, která Log Analytics shromažďuje z připojených zdrojů, včetně protokolů událostí a čítačů výkonu.  Řešení přidávají funkce do OMS a snadno je můžete do svého pracovního prostoru přidat z [Galerie řešení OMS](../log-analytics/log-analytics-add-solutions.md).  Některá řešení mohou vyžadovat přímé připojení agentů nástroje SCOM k Log Analytics, zatímco jiné mohou vyžadovat instalaci dalšího agenta.

Log Analytics obsahuje webový portál, pomocí kterého můžete spravovat prostředky OMS, přidávat a konfigurovat řešení OMS a zobrazit a analyzovat data v úložišti OMS.

![Základní architektura služby Log Analytics](media/operations-management-suite-architecture/log-analytics.png)

## <a name="azure-automation"></a>Azure Automation
[Runbooky služby Azure Automation](http://azure.microsoft.com/documentation/services/automation) se provádějí v cloudu Azure a mohou přistupovat k prostředkům v Azure, v dalších cloudových službách nebo k prostředkům, které jsou přístupné z veřejného internetu.  Můžete také pomocí procesu [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md) určit místní počítače ve svém místním datovém centru a umožnit tak runbookům přístup k místním prostředkům.

[Konfigurace DSC](../automation/automation-dsc-overview.md) uložené ve službě Azure Automation lze použít přímo na virtuálních počítačích Azure.  Ostatní fyzické a virtuální počítače mohou žádat o konfigurace ze serveru vyžádané replikace Azure Automation DSC.

Azure Automation obsahuje řešení OMS, které pro všechny operace zobrazuje statistiku a odkaz na otevření webu Azure Portal.

![Základní architektura služby Azure Automation](media/operations-management-suite-architecture/automation.png)

## <a name="azure-backup"></a>Azure Backup
Chráněná data ve službě [Azure Backup](http://azure.microsoft.com/documentation/services/backup) se ukládají do trezoru záloh umístěného v konkrétní geografické oblasti.  Data jsou replikována v rámci stejné oblasti a v závislosti na typu trezoru mohou být replikována také do jiné oblasti pro zajištění další redundance.

Azure Backup obsahuje tři základní scénáře.

* Počítač s Windows a agentem služby Azure Backup.  Tento scénář vám umožňuje zálohovat soubory a složky z jakéhokoli klienta nebo serveru s Windows přímo do svého trezoru záloh Azure.  
* Server System Center Data Protection Manageru (DPM) nebo server Microsoft Azure Backup. Tento scénář vám umožňuje využít server DPM nebo Microsoft Azure Backup Server kromě zálohování úloh aplikací, jako jsou SQL a SharePoint, i k zálohování souborů a složek do místního úložiště a jejich následné replikaci do vašeho trezoru záloh Azure.
* Rozšíření virtuálního počítače Azure.  Tento scénář vám umožňuje zálohovat virtuální počítače Azure do svého trezoru záloh Azure.

Azure Backup obsahuje řešení OMS, které pro všechny operace zobrazuje statistiku a odkaz na otevření webu Azure Portal.

![Základní architektura služby Azure Backup](media/operations-management-suite-architecture/backup.png)

## <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení u virtuálních počítačů a fyzických serverů. Replikovaná data se přemisťují mezi hostiteli Hyper-V, hypervisory VMware a fyzickými servery v primárním a sekundárním datovém centru, nebo mezi datovým centrem a úložištěm Azure.  Site Recovery ukládá metadata do trezorů umístěných v konkrétní oblasti Azure. V samotné službě Site Recovery se replikovaná data neukládají.

Azure Site Recovery obsahuje tři základní scénáře replikace.

**Replikace virtuálních počítačů Hyper-V**

* Pokud jsou virtuální počítače Hyper-V spravovány v cloudech VMM, je možná replikace do sekundárního datového centra nebo do úložiště Azure.  Replikace do Azure probíhá přes zabezpečené internetové připojení.  Replikace do sekundárního datového centra probíhá přes síť LAN.
* Pokud virtuální počítače Hyper-V nejsou spravovány ve VMM, je možná pouze replikace do úložiště Azure.  Replikace do Azure probíhá přes zabezpečené internetové připojení.

**Replikace virtuálních počítačů VMware**

* Virtuální počítače VMware můžete replikovat do sekundárního datového centra, na kterém běží VMware, nebo do úložiště Azure.  Replikace do Azure může probíhat přes síť VPN typu Site-to-Site nebo Azure ExpressRoute nebo přes zabezpečené internetové připojení. Replikace do sekundárního datového centra probíhá přes kanál nástroje InMage Scout.

**Replikace fyzických serverů s Windows nebo Linuxem** 

* Fyzické servery můžete replikovat do sekundárního datového centra nebo do úložiště Azure. Replikace do Azure může probíhat přes síť VPN typu Site-to-Site nebo Azure ExpressRoute nebo přes zabezpečené internetové připojení. Replikace do sekundárního datového centra probíhá přes kanál nástroje InMage Scout.  Azure Site Recovery obsahuje řešení OMS, které zobrazuje omezenou statistiku, ale pro všechny operace je nutné použít Azure Portal.

![Základní architektura služby Azure Site Recovery](media/operations-management-suite-architecture/site-recovery.png)

## <a name="next-steps"></a>Další kroky
* Další informace o [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Další informace o [Azure Automation](https://azure.microsoft.com/documentation/services/automation).
* Další informace o [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* Další informace o [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).

