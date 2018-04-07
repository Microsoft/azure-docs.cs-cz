---
title: Azure Service Fabric událostí agregace pomocí diagnostiky Linux Azure | Microsoft Docs
description: Další informace o agregaci a shromažďování událostí pomocí LAD pro monitorování a Diagnostika Azure Service Fabric clusterů.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 9ad63a67d3b51cad1376eb9e97e68fb9102013e3
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Seskupení událostí a kolekce pomocí diagnostiky Azure Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Když používáte cluster služby Azure Service Fabric, je vhodné shromažďovat protokoly ze všech uzlů v centrálním umístění. S protokoly v centrálním umístění, vám pomáhají analyzovat a vyřešit problémy v clusteru nebo problémy v aplikací a služeb spuštěných v daném clusteru.

Jeden způsob, jak nahrát a shromažďování protokolů je použití přípony Linux Azure Diagnostics (LAD), který odešle protokoly do služby Azure Storage a má také možnost odeslat protokoly služby Azure Application Insights nebo Event Hubs. Externího procesu můžete také použít ke čtení události z úložiště a umístit je o analýzy platformy produkt, například [analýzy protokolů](../log-analytics/log-analytics-service-fabric.md) nebo jiné řešení pro analýzu protokolu.

## <a name="log-and-event-sources"></a>Protokol a událostí zdroje

### <a name="service-fabric-platform-events"></a>Události platformy Service Fabric
Service Fabric vysílá několik protokolů se na pole prostřednictvím [LTTng](http://lttng.org), včetně provozní události nebo modul runtime události. Tyto protokoly se ukládají do umístění, které určuje šablony Resource Manageru do clusteru. Pro získání nebo nastavení podrobnosti o účtu úložiště, vyhledá značku **AzureTableWinFabETWQueryable** a vyhledejte **StoreConnectionString**.

### <a name="application-events"></a>Události aplikace
 Události vygenerované z vašich aplikací a služeb kódu podle specifikace můžete při instrumentaci váš software. Můžete použít řešení protokolování, který zapíše soubory založený na textu protokolu – například LTTng. Další informace najdete v dokumentaci LTTng na trasování vaší aplikace.

[Monitorování a Diagnostika služby v instalačním programu místním počítači vývoj](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Nasazení rozšíření diagnostiky
Prvním krokem při shromažďování protokolů je k nasazení rozšíření diagnostiky na všech virtuálních počítačích v clusteru Service Fabric. Rozšíření diagnostiky shromažďuje protokoly na každý virtuální počítač a odesílá je do účtu úložiště, který určíte. 

Chcete-li nasadit rozšíření diagnostiky pro virtuální počítače v clusteru jako součást vytváření clusteru, nastavte **diagnostiky** k **na**. Po vytvoření clusteru, nelze toto nastavení změnit pomocí portálu, takže budete muset provést potřebné změny v šabloně Resource Manager.

Tím se nakonfiguruje LAD agenta monitorování zadané soubory protokolu. Vždy, když nový řádek je připojen k souboru, vytvoří záznam syslog, která je odeslána do úložiště (tabulky), které jste zadali.


## <a name="next-steps"></a>Další postup

1. Chcete-li podrobněji pochopit, jaké události byste měli zkontrolovat při odstraňování problémů, přečtěte si téma [LTTng dokumentace](http://lttng.org/docs) a [pomocí LAD](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).
2. [Nastavení agenta analýzy protokolů](service-fabric-diagnostics-event-analysis-oms.md) ke shromažďování metrik monitorovat kontejnery nasazené v clusteru a vizualizovat svoje protokoly 