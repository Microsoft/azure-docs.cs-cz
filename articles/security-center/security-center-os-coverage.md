---
title: Platformy podporované aplikací Azure Security Center | Microsoft Docs
description: Tento dokument poskytuje seznam platforem podporovaných nástrojem Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 4cc8130d34b5f51728fb2e7fb47c1fb0717d2138
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595128"
---
# <a name="supported-platforms"></a>Podporované platformy 

Tato stránka zobrazuje platformy a prostředí, které podporuje Azure Security Center.

## <a name="combinations-of-environments"></a>Kombinace prostředí <a name="vm-server"></a>

Azure Security Center podporuje virtuální počítače a servery v různých typech hybridních prostředí:

* Jenom Azure
* Azure a místní
* Azure a další cloudy
* Azure, ostatní cloudy a místní

Pro prostředí Azure aktivované v rámci předplatného Azure Azure Security Center automaticky zjišťovat prostředky IaaS nasazené v rámci předplatného.

## <a name="supported-operating-systems"></a>Podporované operační systémy

Security Center závisí na [agentu Log Analytics](../azure-monitor/agents/agents-overview.md#log-analytics-agent). Zajistěte, aby počítače používaly jeden z podporovaných operačních systémů pro tohoto agenta, jak je popsáno na následujících stránkách:

* [Agent Log Analytics pro podporované operační systémy Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)
* [Agent Log Analytics pro podporované operační systémy Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

Ujistěte se také, že je váš agent Log Analytics [správně nakonfigurovaný tak, aby odesílal data Security Center](security-center-enable-data-collection.md#manual-agent)

Další informace o konkrétních funkcích Security Center dostupných v systému Windows a Linux najdete v tématu věnovaném [pokrytí funkcí pro počítače](security-center-services.md).

> [!NOTE]
> I když je Azure Defender navržený tak, aby chránil servery, většina možností **Azure Defenderu pro servery** je podporovaná pro počítače s Windows 10. Jednou funkcí, která není aktuálně podporovaná, je [Security Center integrované řešení EDR: Microsoft Defender pro koncové body](security-center-wdatp.md).

## <a name="managed-virtual-machine-services"></a>Spravované služby virtuálních počítačů <a name="virtual-machine"></a>

Virtuální počítače se také vytvoří v rámci zákaznického předplatného jako součást některých služeb spravovaných Azure, jako je například Azure Kubernetes (AKS), Azure Databricks a další. Security Center zjistí také tyto virtuální počítače a Agent Log Analytics lze nainstalovat a nakonfigurovat, pokud je k dispozici podporovaný operační systém.

## <a name="cloud-services"></a>Cloud Services <a name="cloud-services"></a>

Podporují se také virtuální počítače, které běží v cloudové službě. Monitorují se jenom webové role a role pracovních procesů Cloud Services, které běží v produkčních slotech. Další informace o cloudových službách najdete v tématu [Přehled Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

Podporuje se i ochrana pro virtuální počítače, které jsou umístěné v Azure Stack. Další informace o integraci Security Center s Azure Stack najdete v tématu připojení [virtuálních počítačů s Azure Stack k Security Center](quickstart-onboard-machines.md). 

## <a name="next-steps"></a>Další kroky

- Přečtěte si [, jak Security Center shromažďuje data pomocí agenta Log Analytics](security-center-enable-data-collection.md).
- Přečtěte si [, jak Security Center spravuje a chrání data](security-center-data-security.md).
- Naučte se [plánovat a porozumět hlediskům návrhu, které je potřeba přijmout Azure Security Center](security-center-planning-and-operations-guide.md).