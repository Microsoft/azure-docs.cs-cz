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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: bdb0322083f842922359f0c91da77f5df81613cb
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675815"
---
# <a name="supported-platforms"></a>Podporované platformy 

## Virtuální počítače a servery<a name="vm-server"></a>

Security Center podporuje virtuální počítače a servery v různých typech hybridních prostředí:

* Jenom Azure
* Azure a místní
* Azure a další cloudy
* Azure, ostatní cloudy a místní

Pro prostředí Azure aktivované v rámci předplatného Azure Azure Security Center automaticky zjišťovat prostředky IaaS nasazené v rámci předplatného.

> [!NOTE]
> Chcete-li získat kompletní sadu funkcí zabezpečení, je nutné mít [agenta Log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent), který je používán Azure Security Center, nainstalován a [správně nakonfigurován k odesílání dat do Azure Security Center](security-center-enable-data-collection.md#manual-agent).


V následujících oddílech jsou uvedeny podporované serverové operační systémy, na kterých je možné spustit [agenta Log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent), který používá služba Azure Security Center.

### Operační systémy Windows Server<a name="os-windows"></a>

[!div class="mx-tableFixed"]
|OS|Podporováno aplikací Azure Security Center|Podpora integrace s Microsoft Defender ATP|
|---|---|---|
|Windows Server 2019|✔|×|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2012|✔|✔|
|Windows Server 2008 R2|✔|✔|
|Windows Server 2008|✔|✔|

Další informace o podporovaných funkcích pro operační systémy Windows, které jsou uvedené výše, najdete v tématu [funkce podporované virtuálním počítačem/serverem](security-center-services.md##vm-server-features).

### Operační systémy Linux<a name="os-linux"></a>

64 – bit

* CentOS 6 a 7
* Amazon Linux 2017,09
* Oracle Linux 6 a 7
* Red Hat Enterprise Linux Server 6 a 7
* Debian GNU/Linux 8 a 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS a 18,04 LTS
* SUSE Linux Enterprise Server 12

32 – bit
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 a 9
* Ubuntu Linux 14,04 LTS a 16,04 LTS

> [!NOTE]
> Vzhledem k tomu, že se seznam podporovaných operačních systémů Linux neustále mění, pokud chcete, kliknutím [sem](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) zobrazíte nejaktuálnější seznam podporovaných verzí, v případě, že došlo ke změnám od posledního publikování tohoto tématu.

Další informace o podporovaných funkcích pro operační systémy Linux, které jsou uvedené výše, najdete v tématu [funkce podporované virtuálním počítačem/serverem](security-center-services.md##vm-server-features).

### Spravované služby virtuálních počítačů<a name="virtual-machine"></a>

Virtuální počítače se také vytvoří v rámci zákaznického předplatného jako součást některých spravovaných služeb Azure, jako je například Azure Kubernetes (AKS), Azure Databricks a další. Tyto virtuální počítače se taky zjišťují pomocí Azure Security Center a Agent Log Analytics se dá nainstalovat a nakonfigurovat podle podporovaných [operačních systémů Windows/Linux](#os-windows)uvedených výše.

### Cloud Services<a name="cloud-services"></a>

Podporují se také virtuální počítače, které běží v cloudové službě. Monitorují se jenom webové role a role pracovních procesů Cloud Services, které běží v produkčních slotech. Další informace o cloudových službách najdete v tématu [Přehled Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

## Služby PaaS<a name="paas-services"></a>

Azure Security Center podporují následující prostředky Azure PaaS:

* SQL
* PostGreSQL
* MySQL
* CosmosDB
* Účet úložiště
* App Service
* Funkce
* Cloudová služba
* VNet
* Podsíť
* NIC
* NSG
* Účet Batch
* Účet Service Fabric
* Účet Automation
* Nástroj pro vyrovnávání zatížení
* Hledat
* Obor názvů služby Service Bus
* Stream Analytics
* Obor názvů centra událostí
* Logické aplikace
* Redis
* Analýza Data Lake
* Data Lake Store
* Key Vault

Další informace o podporovaných funkcích pro výše uvedený seznam prostředků PaaS najdete v tématu [funkce podporované službami PaaS](security-center-services.md#paas-services).

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [Security Center shromažďuje data a agenta Log Analytics](security-center-enable-data-collection.md).
- Přečtěte si [, jak Security Center spravuje a chrání data](security-center-data-security.md).
- Naučte se [plánovat a porozumět hlediskům návrhu, které je potřeba přijmout Azure Security Center](security-center-planning-and-operations-guide.md).
- Seznamte [se s funkcemi dostupnými pro různá cloudová prostředí](security-center-services.md).
- Přečtěte si další informace o [detekci hrozeb pro virtuální počítače & serverech v Azure Security Center](security-center-alerts-iaas.md).
- Přečtěte si [Nejčastější dotazy týkající se použití Azure Security Center](security-center-faq.md).
- Přečtěte si [blogové příspěvky o zabezpečení Azure a dodržování předpisů](https://blogs.msdn.com/b/azuresecurity/).
