---
title: Platformy podporované Azure Security Center | Dokumenty společnosti Microsoft
description: Tento dokument obsahuje seznam platforem podporovaných Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 3c8bf69b745f5dba8c08556908df4d4ae5b5769f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521918"
---
# <a name="supported-platforms"></a>Podporované platformy 

Na této stránce jsou zobrazeny platformy a prostředí podporovaná Azure Security Center.

## <a name="combinations-of-environments"></a>Kombinace prostředí<a name="vm-server"></a>

Azure Security Center podporuje virtuální počítače a servery v různých typech hybridních prostředí:

* Jenom Azure
* Azure a místní
* Azure a další cloudy
* Azure, další cloudy a místní

Pro prostředí Azure aktivované na předplatné Azure Azure Centrum zabezpečení automaticky zjišťuje prostředky IaaS, které jsou nasazené v rámci předplatného.

## <a name="supported-operating-systems"></a>Podporované operační systémy

Centrum zabezpečení závisí na [agentovi analýzy protokolů](../azure-monitor/platform/agents-overview.md#log-analytics-agent). Ujistěte se, že vaše počítače používají jeden z podporovaných operačních systémů pro tohoto agenta, jak je popsáno na následujících stránkách:

* [Agent Log Analytics pro operační systémy podporované systémem Windows](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
* [Agent Log Analytics pro operační systémy podporované Linuxem](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

Také se ujistěte, že váš agent Log Analytics je [správně nakonfigurován pro odesílání dat do Security Center](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> Další informace o konkrétních funkcích Centra zabezpečení dostupných ve Windows a Linuxu najdete v [tématu Pokrytí funkcí pro počítače](security-center-services.md).

## <a name="managed-virtual-machine-services"></a>Spravované služby virtuálních strojů<a name="virtual-machine"></a>

Virtuální počítače se taky vytvářejí v zákaznickém předplatném jako součást některých služeb spravovaných Azure, jako jsou Azure Kubernetes (AKS), Azure Databricks a další. Security Center zjišťuje tyto virtuální počítače příliš a agent Alog Analytics lze nainstalovat a nakonfigurovat, pokud je k dispozici podporovaný operační systém.

## <a name="cloud-services"></a>Cloudové služby<a name="cloud-services"></a>

Virtuální počítače, které běží v cloudové službě jsou také podporovány. Monitorují se pouze webové a pracovní role cloudových služeb, které běží v produkčních slotech. Další informace o cloudových službách najdete v [tématu Přehled cloudových služeb Azure](../cloud-services/cloud-services-choose-me.md).

Ochrana pro virtuální počítače s bydlištěm v Azure Stack je také podporována. Další informace o integraci Centra zabezpečení s Azure Stack najdete [v tématu Onboard your Azure Stack virtual machines to Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [Security Center shromažďuje data pomocí agenta analýzy protokolů](security-center-enable-data-collection.md).
- Zjistěte, jak [Security Center spravuje a zabezpečuje data](security-center-data-security.md).
- Přečtěte si, jak [naplánovat a pochopit aspekty návrhu a přijmout Azure Security Center](security-center-planning-and-operations-guide.md).