---
title: Azure Service Fabric události agregace pomocí diagnostiky Azure pro Linux | Dokumentace Microsoftu
description: Další informace o agregaci a shromažďování událostí pomocí LAD pro monitorování a diagnostiku clustery Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: c7eb98eb2dbff05e67b6a60c413932ba51fdfdf7
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45573752"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Agregace událostí a kolekce pomocí diagnostiky Azure Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Když používáte cluster Azure Service Fabric, je vhodné pro shromažďování protokolů ze všech uzlů v centrálním umístění. S protokoly v centrálním umístění vám pomáhají analyzovat a řešit problémy ve vašem clusteru nebo problémy v aplikace a služby běžící v tomto clusteru.

Jeden způsob, jak nahrát a shromažďovat protokoly je použít rozšíření diagnostiky Azure Linux (LAD), nahraje protokoly do služby Azure Storage, který má také možnost odeslat protokoly do služby Azure Application Insights nebo Center událostí. Externí proces lze také použít ke čtení události ze služby storage a umístit je do o produkt poskytovaný analýzy platformy, jako [Log Analytics](../log-analytics/log-analytics-service-fabric.md) nebo jiné řešení analýzy protokolů.

## <a name="log-and-event-sources"></a>Zdroje protokolů a událostí

### <a name="service-fabric-platform-events"></a>Události platformy Service Fabric
Service Fabric vysílá několik protokolů out-of-the-box prostřednictvím [LTTng](http://lttng.org), včetně provozní události nebo běhové události. Tyto protokoly se ukládají v umístění, které určuje šablony Resource Manageru clusteru. Pro získání nebo nastavení podrobností účtu úložiště, vyhledá značku **AzureTableWinFabETWQueryable** a hledejte **StoreConnectionString**.

### <a name="application-events"></a>Události aplikace
 Události generované z kódu vašich aplikací a služeb, jako jste je uvedli v při instrumentaci vašeho softwaru. Můžete použít žádné protokolování řešení, která zapisuje soubory založený na textu protokolu – například LTTng. Další informace najdete v dokumentaci LTTng na trasování pro tuto aplikaci.

[Monitorování a Diagnostika služeb v instalačním programu místním počítači vývoje](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Nasazení rozšíření diagnostiky
Prvním krokem při shromažďování protokolů je k nasazení rozšíření diagnostiky na všech virtuálních počítačů v clusteru Service Fabric. Rozšíření diagnostiky shromažďuje protokoly na každém virtuálním počítači a odesílá je do účtu úložiště, který zadáte. 

Chcete-li nasadit rozšíření diagnostiky pro virtuální počítače v clusteru jako součást vytváření clusteru, nastavte **diagnostiky** k **na**. Po vytvoření clusteru nelze toto nastavení změnit pomocí portálu, takže budete muset provést potřebné změny v šabloně Resource Manageru.

Tím se nakonfiguruje LAD agenta monitorování zadané soubory protokolu. Pokaždé, když se nový řádek je připojená k souboru, vytvoří záznam syslog, který je odeslán do služby storage (tabulky), který jste zadali.


## <a name="next-steps"></a>Další postup

1. Chcete-li podrobněji pochopit, jaké události byste měli zkontrolovat při řešení potíží, přečtěte si téma [LTTng dokumentaci](http://lttng.org/docs) a [pomocí LAD](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux).
2. [Nastavení agenta Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) pomůže shromažďovat metriky, monitorování kontejnerů v clusteru a vizualizovat vaše protokoly 
