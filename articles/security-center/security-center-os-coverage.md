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
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: f9b948714f72ba02a100d9941721f073953bf22a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473233"
---
# <a name="supported-platforms"></a>Podporované platformy 

## <a name="virtual-machines--servers"></a>Virtuální stroje / servery<a name="vm-server"></a>

Security Center podporuje virtuální počítače / servery na různých typech hybridních prostředí:

* Jenom Azure
* Azure a místní
* Azure a další cloudy
* Azure, další cloudy a místní

Pro prostředí Azure aktivované na předplatné Azure Azure Centrum zabezpečení automaticky zjišťuje prostředky IaaS, které jsou nasazené v rámci předplatného.

> [!NOTE]
> Chcete-li získat úplnou sadu funkcí zabezpečení, musíte mít [agenta analýzy protokolů](../azure-monitor/platform/agents-overview.md#log-analytics-agent), který používá Azure Security Center, nainstalovaný a [správně nakonfigurovaný k odesílání dat do Centra zabezpečení Azure](security-center-enable-data-collection.md#manual-agent).

V následujících částech jsou uvedeny podporované operační systémy pro servery, na kterých může spustit [agent analýzy protokolů](../azure-monitor/platform/agents-overview.md#log-analytics-agent), který používá Azure Security Center.

### <a name="windows-server-operating-systems"></a>Operační systémy Windows pro servery<a name="os-windows"></a>

|Operační systém|Podporované Azure Security Center|Podpora integrace s programem Microsoft Defender ATP|
|:---|:-:|:-:|
|Windows Server 2019|✔|×|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Další informace o podporovaných funkcích operačních systémů Windows, které jsou uvedeny výše, naleznete v [tématu Funkce podporované virtuálním počítačem a serverem](security-center-services.md#vm-server-features).

### <a name="windows-operating-systems"></a>Operační systémy Windows<a name="os-windows (non-server)"></a>

Azure Security Center se integruje se službami Azure a monitoruje a chrání vaše virtuální počítače s Windows.

### <a name="linux-operating-systems"></a>Operační systémy Linux<a name="os-linux"></a>

64bitová

* CentOS 6 a 7
* Amazon Linux 2017.09
* Oracle Linux 6 a Oracle Linux 7
* Red Hat Enterprise Linux Server 6 a 7
* Debian GNU/Linux 8 a 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS a 18.04 LTS
* SUSE Linux Enterprise Server 12

32bitová
* Centos 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 a 9
* Ubuntu Linux 14.04 LTS a 16.04 LTS

> [!NOTE]
> Vzhledem k tomu, že seznam podporovaných operačních systémů Linux se neustále mění, pokud dáváte přednost, klikněte [zde](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) pro zobrazení nejaktuálnějšího seznamu podporovaných verzí, v případě, že došlo ke změnám od posledního publikování tohoto tématu.

Další informace o podporovaných funkcích operačních systémů Linux, které jsou uvedeny výše, naleznete v [tématu Funkce podporované virtuálním počítačem a serverem](security-center-services.md#vm-server-features).

### <a name="managed-virtual-machine-services"></a>Spravované služby virtuálních strojů<a name="virtual-machine"></a>

Virtuální počítače se taky vytvářejí v zákaznickém předplatném jako součást některých spravovaných služeb Azure, jako jsou Azure Kubernetes (AKS), Azure Databricks a další. Tyto virtuální počítače jsou také zjištěny azure security center a agent analýzy protokolů lze nainstalovat a nakonfigurovat podle [podporovaných operačních systémů Windows/Linux](#os-windows), které jsou uvedeny výše.

### <a name="cloud-services"></a>Cloudové služby<a name="cloud-services"></a>

Virtuální počítače, které běží v cloudové službě jsou také podporovány. Monitorují se pouze webové a pracovní role cloudových služeb, které běží v produkčních slotech. Další informace o cloudových službách najdete v [tématu Přehled cloudových služeb Azure](../cloud-services/cloud-services-choose-me.md).

Ochrana virtuálních počítačů s bydlištěm v Azure Stack je také podporována. Další informace o integraci Centra zabezpečení s Azure Stack najdete [v tématu Onboard your Azure Stack virtual machines to Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [Security Center shromažďuje data a agent a analýzy protokolů](security-center-enable-data-collection.md).
- Zjistěte, jak [Security Center spravuje a zabezpečuje data](security-center-data-security.md).
- Přečtěte si, jak [naplánovat a pochopit aspekty návrhu a přijmout Azure Security Center](security-center-planning-and-operations-guide.md).
- Informace o [funkcích dostupných pro různá cloudová prostředí](security-center-services.md).
- Další informace o [ochraně před hrozbami pro počítače s Windows a Linuxem v Azure Security Center](threat-protection.md#windows-machines).
