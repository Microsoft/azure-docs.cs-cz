---
title: Funkce a platformách podporovaných službou Azure Security Center | Dokumentace Microsoftu
description: Tento dokument obsahuje seznam funkcí a platformách podporovaných službou Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 4108355415d1230f98db36a4f83497de2fa848f7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185575"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Platformy a funkcí podporovaných službou Azure Security Center

Monitorování stavu zabezpečení a doporučení jsou k dispozici pro virtuální počítače (VM) vytvořené pomocí modelu classic a modely nasazení Resource Manager i počítače.

> [!NOTE]
> Další informace o [klasické modely nasazení a modely nasazení Resource Manager](../azure-classic-rm.md) pro prostředky Azure.
>
>

## <a name="supported-platforms"></a>Podporované platformy 

Tato část uvádí platformy, na kterých agent Azure Security Center můžete spustit a odkud mohla shromažďovat data.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Podporované platformy pro Windows, počítačů a virtuálních počítačů
Následující operační systémy Windows se podporují:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


### <a name="supported-platforms-for-linux-computers-and-vms"></a>Podporované platformy pro virtuální počítače a počítače s Linuxem
Jsou podporovány následující operační systémy Linux:

* Verze Ubuntu 12.04 LTS, 14.04 LTS a 16.04 LTS.
* Debian verze 6, 7, 8 a 9.
* CentOS verze 5, 6 a 7.
* Red Hat Enterprise Linux (RHEL) verze 5, 6 a 7.
* SUSE Linux Enterprise Server (SLES) verze 11 a 12.
* Oracle Linux verze 5, 6 a 7.
* Linux Amazon 2012.09 2017.
* OpenSSL 1.1.0 je podporována pouze na platformách x86_64, 64 bitů.

> [!NOTE]
> Pro vypracování analýzy chování virtuálního počítače zatím nejsou k dispozici pro operační systémy a Linux.
>
>

## <a name="vms-and-cloud-services"></a>Virtuální počítače a cloudové služby
Podporují se také virtuální počítače, na kterých běží v cloudové službě. Jsou monitorovány pouze cloud services webové a pracovní role, které běží v slot na produkční. Další informace o službách cloud services, najdete v článku [přehled o Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Podporované funkce IaaS

> [!div class="mx-tableFixed"]
> 

|Server|Windows||Linux||
|----|----|----|----|----|
|Prostředí|Azure|Mimo službu Azure|Azure|Mimo službu Azure|
|Výstrahy detekce hrozeb VMBA|✔|✔|✔ (o podporovaných verzích)|✔|
|Výstrahy detekce hrozeb založené na síti|✔|X|✔|X|
|Ochrana ATP v programu Windows Defender integrace *|✔ (o podporovaných verzích)|✔|X|X|
|Chybějící opravy|✔|✔|✔|✔|
|Konfigurace zabezpečení|✔|✔|✔|✔|
|Antimalwarové programy|✔|✔|X|X|
|Přístup k virtuálnímu počítači podle potřeby|✔|X|✔|X|
|Adaptivní řízení aplikací|✔|X|X|X|
|FIM|✔|✔|✔|✔|
|Šifrování disku|✔|X|✔|X|
|Nasazení třetích stran|✔|X|✔|X|
|Skupiny NSG|✔|X|✔|X|
|Detekce hrozeb fileless|✔|✔|X|X|
|Mapování sítě|✔|X|✔|X|
|Ovládací prvky pro adaptivní síť|✔|X|✔|X|

\* Tyto funkce jsou aktuálně podporované ve verzi public preview.


## <a name="supported-paas-features"></a>Podporované funkce PaaS 


|Služba|Doporučení|Detekce hrozeb|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL *|✔| ✔|
|MySQL *|✔| ✔|
|Azure Blob storage účty *|✔| ✔|
|Aplikační služby|✔| ✔|
|Cloud Services|✔| X|
|Virtuální sítě|✔| Není k dispozici|
|Podsítě|✔| Není k dispozici|
|Síťové karty|✔| ✔|
|Skupiny NSG|✔| Není k dispozici|
|Předplatné|✔| ✔|

\* Tyto funkce jsou aktuálně podporované ve verzi public preview. 

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [naplánovat a pochopit aspekty návrhu, abyste mohli přejít na Azure Security Center](security-center-planning-and-operations-guide.md).
- Další informace o [analýza chování virtuálního počítače a při selhání výpis stavu paměti analýzy ve službě Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Najít [– nejčastější dotazy o použití Azure Security Center](security-center-faq.md).
- Najít [příspěvky o zabezpečení Azure a dodržování předpisů](https://blogs.msdn.com/b/azuresecurity/).
