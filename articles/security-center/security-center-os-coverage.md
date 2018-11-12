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
ms.openlocfilehash: f4bc90b2d1a80125ae88b4b5c4c11e42a34a985a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240422"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Platformy a funkcí podporovaných službou Azure Security Center

Monitorování stavu zabezpečení a doporučení jsou k dispozici pro virtuální počítače (VM) vytvořené pomocí modelu classic a modely nasazení Resource Manager a počítače.

> [!NOTE]
> Další informace o [klasické modely nasazení a modely nasazení Resource Manager](../azure-classic-rm.md) pro prostředky Azure.
>
>

## <a name="supported-platforms"></a>Podporované platformy 

Tato část uvádí platformy, na kterých agent Azure Security Center můžete spustit a odkud mohla shromažďovat data.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Podporované platformy pro Windows, počítačů a virtuálních počítačů
Podporované operační systémy Windows:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


### <a name="supported-platforms-for-linux-computers-and-vms"></a>Podporované platformy pro virtuální počítače a počítače s Linuxem
Podporované operační systémy Linux:

* Verze Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS
* Debian verze 6, 7, 8, 9
* CentOS verze 5, 6, 7
* Red Hat Enterprise Linux (RHEL) verze 5, 6, 7
* SUSE Linux Enterprise Server (SLES) verze 11, 12
* Oracle Linux verze 5, 6, 7
* Linux Amazon 2012.09 2017
* OpenSSL 1.1.0 je podporována pouze na platformách x86_64 (64 bitů)

> [!NOTE]
> Pro vypracování analýzy chování virtuálního počítače nejsou k dispozici pro operační systémy a Linux.
>
>

## <a name="vms-and-cloud-services"></a>Virtuální počítače a cloudové služby
Podporují se také virtuální počítače spuštěné v cloudové službě. Pouze cloud services – webové a pracovní role běžících v produkčním prostředí, které jsou monitorovány sloty. Další informace o cloudové služby, najdete v článku [Přehled služby Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Podporované funkce IaaS

> [!div class="mx-tableFixed"]
> 

|Server|Windows||Linux||
|----|----|----|----|----|
|Prostředí|Azure|Mimo službu Azure|Azure|Mimo službu Azure|
|Výstrahy detekce hrozeb VMBA|✔|✔|✔ (o podporovaných verzích)|✔|
|Výstrahy detekce hrozeb na základě sítě|✔|X|✔|X|
|Ochrana ATP v programu Windows Defender integrace *|✔ (o podporovaných verzích)|✔|X|X|
|Chybějící opravy|✔|✔|✔|✔|
|Konfigurace zabezpečení|✔|✔|✔|✔|
|Anti-malware|✔|✔|X|X|
|Přístup k virtuálnímu počítači podle potřeby|✔|X|✔|X|
|Adaptivní řízení aplikací|✔|X|X|X|
|FIM|✔|✔|✔|✔|
|Šifrování disku|✔|X|✔|X|
|Nasazení třetích stran|✔|X|✔|X|
|Skupiny NSG|✔|X|✔|X|
|Detekce hrozeb Filess|✔|✔|X|X|
|Mapování sítě|✔|X|✔|X|
|Ovládací prvky pro adaptivní síť|✔|X|✔|X|

\* Tyto funkce jsou aktuálně podporované ve verzi public preview.


## <a name="supported-paas-features"></a>Podporované funkce PaaS


|Služba|Doporučení|Detekce hrozeb|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL *|✔| ✔|
|MySQL *|✔| ✔|
|Účty úložiště BLOB *|✔| ✔|
|Aplikační služby|✔| ✔|
|Cloud Services|✔| X|
|Virtuální sítě|✔| Není k dispozici|
|Podsítě|✔| Není k dispozici|
|Síťové karty|✔| ✔|
|Skupiny NSG|✔| Není k dispozici|
|Předplatné|✔| ✔|

\* Tyto funkce jsou aktuálně podporované ve verzi public preview.

## <a name="next-steps"></a>Další postup

- [Průvodce Azure Security Center plánováním a provozem](security-center-planning-and-operations-guide.md) – zjistěte, jak naplánovat a pochopit aspekty návrhu, abyste mohli přejít na Azure Security Center
- [Výstrahy zabezpečení podle typu ve službě Azure Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis) – Další informace o analýza chování virtuálního počítače a k chybě při analýze výpisu paměti ve službě Security Center
- [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
- [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – přečtěte si blog příspěvky o zabezpečení Azure a dodržování předpisů
