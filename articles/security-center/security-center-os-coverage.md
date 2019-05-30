---
title: Funkce a platformách podporovaných službou Azure Security Center | Dokumentace Microsoftu
description: Tento dokument obsahuje seznam funkcí a platformách podporovaných službou Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/27/2019
ms.author: monhaber
ms.openlocfilehash: 807bde76bb6bb50490ee599768273a59c49d5e45
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258691"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Platformy a funkcí podporovaných službou Azure Security Center

Monitorování stavu zabezpečení a doporučení jsou k dispozici pro virtuální počítače (VM) vytvořené pomocí modelu classic a modely nasazení Resource Manager i počítače.

> [!NOTE]
> Další informace o [klasické modely nasazení a modely nasazení Resource Manager](../azure-classic-rm.md) pro prostředky Azure.
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Platformy, které podporují agent pro shromažďování dat. 

Tato část uvádí platformy, na kterých agent Azure Security Center můžete spustit a odkud mohla shromažďovat data.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Podporované platformy pro Windows, počítačů a virtuálních počítačů
Následující operační systémy Windows se podporují:

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> Integrace s ochrany ATP v programu Windows Defender podporuje pouze systém Windows Server 2012 R2 a Windows serveru 2016.

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Podporované platformy pro virtuální počítače a počítače s Linuxem

Jsou podporovány následující operační systémy Linux:

> [!NOTE]
> Vzhledem k tomu, že seznam podporovaných operačních systémech Linux se neustále mění, pokud dáváte přednost, klikněte na tlačítko [tady](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) zobrazíte aktuální seznam podporovaných verzí, v případě, že byly provedeny změny od posledního publikování v tomto tématu.

64 bitů
* CentOS 6 a 7
* Amazon Linux 2017.09
* Oracle Linux 6 a 7
* Red Hat Enterprise Linux Server 6 a 7
* Debian GNU/Linux 8 a 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS a 18.04 LTS
* SUSE Linux Enterprise Server 12

32-bit
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 a 9
* Ubuntu Linux 14.04 LTS a 16.04 LTS

## <a name="vms-and-cloud-services"></a>Virtuální počítače a cloudové služby
Podporují se také virtuální počítače, na kterých běží v cloudové službě. Jsou monitorovány pouze cloud services webové a pracovní role, které běží v slot na produkční. Další informace o službách cloud services, najdete v článku [přehled o Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Podporované funkce IaaS

> [!div class="mx-tableFixed"]
> 

|Server|Windows||Linux||||Ceny|
|----|----|----|----|----|----|----|----|
|**Prostředí**|**Azure**||**Non-Azure**|**Azure**||**Non-Azure**||
||**Virtuální počítač**|**Škálovací sada virtuálních počítačů**||**Virtuální počítač**|**Škálovací sada virtuálních počítačů**|
|Výstrahy detekce hrozeb VMBA|✔|✔|✔|✔ (o podporovaných verzích)|✔ (o podporovaných verzích)|✔|Detekce hrozeb (zdarma) doporučení (Standard)|
|Výstrahy detekce hrozeb založené na síti|✔|✔|X|✔|✔|X|Standard|
|Integrace ochrany ATP v programu Windows Defender|✔ (o podporovaných verzích)|✔ (o podporovaných verzích)|✔|X|X|X|Standard|
|Chybějící opravy|✔|✔|✔|✔|✔|✔|Free|
|Konfigurace zabezpečení|✔|✔|✔|✔|✔|✔|Free|
|Posouzení ochrany koncového bodu|✔|✔|✔|X|X|X|Free|
|Přístup k virtuálnímu počítači podle potřeby|✔|X|X|✔|X|X|Standard|
|Adaptivní řízení aplikací|✔|X|✔|✔|X|✔|Standard|
|FIM|✔|✔|✔|✔|✔|✔|Standard|
|Vyhodnocování šifrování disku.|✔|✔|X|✔|✔|X|Free|
|Nasazení třetích stran|✔|X|X|✔|X|X|Free|
|Posouzení skupin zabezpečení sítě|✔|✔|X|✔|✔|X|Free|
|Detekce hrozeb fileless|✔|✔|✔|X|X|X|Standard|
|Mapování sítě|✔|✔|X|✔|✔|X|Standard|
|Ovládací prvky pro adaptivní síť|✔|✔|X|✔|✔|X|Standard|
|Dodržování legislativních řídicího panelu pro dodržování předpisů a sestavy|✔|✔|✔|✔|✔|✔|Standard|
|Doporučení a detekce hrozeb v kontejnerech Dockeru hostovaných IaaS|X|X|X|✔|✔|✔|Standard|

### <a name="supported-endpoint-protection-solutions"></a>Řešení ochrany koncových bodů podporované

Následující tabulka obsahuje matici:
 - Určuje, zda můžete použít Azure Security Center k instalaci jednotlivá řešení za vás.
 - Můžete zjistit, které endpoint protection řešení Security Center. Pokud je jedno z těchto řešení endpoint protection zjistí, nebude Security Center doporučit instalace.

Informace o po vygenerování doporučení pro všechny tyto ochrany najdete v tématu [Endpoint Protection posouzení a doporučení](security-center-endpoint-protection.md).

| Endpoint Protection (Ochrana koncových bodů)| Platformy | Instalace Security Center | Zjišťování Security Center |
|------|------|-----|-----|
| Windows Defender (antimalware Microsoftu)| Windows Server 2016| Ne, součást operačního systému| Ano |
| Ochrana koncových bodů System Center (antimalware Microsoftu) | Windows Server 2012 R2, 2012, 2008 R2 (viz poznámka níže) | Prostřednictvím rozšíření | Ano |
| Trend Micro – všechny verze | Řada Windows Serverů  | Ne | Ano |
| Symantec v12.1.1100+| Řada Windows Serverů  | Ne | Ano |
| McAfee v10+ | Řada Windows Serverů  | Ne | Ano |
| Kaspersky| Řada Windows Serverů  | Ne | Ne  |
| Sophos| Řada Windows Serverů  | Ne | Ne  |

> [!NOTE]
> - Zjišťování nástroje System Center Endpoint Protection (SCEP) na virtuálním počítači s Windows serverem 2008 R2 vyžaduje SCEP k instalaci prostředí PowerShell 3.0 (nebo horním verze).

## <a name="supported-paas-features"></a>Podporované funkce PaaS


|Služba|Doporučení (zdarma)|Detekce hrozeb (Standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Azure Blob storage účty *|✔| ✔|
|Aplikační služby|✔| ✔|
|Cloudové služby|✔| X|
|Virtuální sítě|✔| Není k dispozici|
|Podsítě|✔| Není k dispozici|
|Síťové karty|✔| Není k dispozici|
|Skupiny NSG|✔| Není k dispozici|
|Předplatné|✔ **| ✔|
|App Service|✔| Není k dispozici|
|Batch|✔| Není k dispozici|
|Service Fabric|✔| Není k dispozici|
|Účet Automation|✔| Není k dispozici|
|Nástroj pro vyrovnávání zatížení|✔| Není k dispozici|
|Search|✔| Není k dispozici|
|Service Bus|✔| Není k dispozici|
|Stream Analytics|✔| Není k dispozici|
|Centrum událostí|✔| Není k dispozici|
|Logické aplikace|✔| Není k dispozici|
|Podsíť|✔| Není k dispozici|
|Vnet|✔| Není k dispozici|
|Účet úložiště|✔| Není k dispozici|
|Redis|✔| Není k dispozici|
|SQL|✔| Není k dispozici|
|Data lake analytics|✔| Není k dispozici|
|Účet úložiště|✔| Není k dispozici|
|Předplatné|✔| Není k dispozici|
|Trezor klíčů|✔| Není k dispozici|




\* Tyto funkce jsou aktuálně podporované ve verzi public preview.

\*\* AAD doporučení jsou dostupné jenom pro standardní předplatná



## <a name="next-steps"></a>Další postup

- Zjistěte, jak [naplánovat a pochopit aspekty návrhu, abyste mohli přejít na Azure Security Center](security-center-planning-and-operations-guide.md).
- Další informace o [analýza chování virtuálního počítače a při selhání výpis stavu paměti analýzy ve službě Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Najít [– nejčastější dotazy o použití Azure Security Center](security-center-faq.md).
- Najít [příspěvky o zabezpečení Azure a dodržování předpisů](https://blogs.msdn.com/b/azuresecurity/).
