---
title: Podporované platformy ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument obsahuje seznam operatings systémů Windows a Linux podporované ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2018
ms.author: terrylan
ms.openlocfilehash: b82b152ae4f44e475b5c3e9bda9d604f405503e0
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123727"
---
# <a name="supported-platforms-in-azure-security-center"></a>Podporované platformy ve službě Azure Security Center
Monitorování stavu zabezpečení a doporučení jsou k dispozici pro virtuální počítače (VM) vytvořené pomocí modelu classic a modely nasazení Resource Manager a počítače.

> [!NOTE]
> Další informace o [klasické modely nasazení a modely nasazení Resource Manager](../azure-classic-rm.md) pro prostředky Azure.
>
>

## <a name="supported-platforms-for-windows-computers-and-vms"></a>Podporované platformy pro Windows, počítačů a virtuálních počítačů
Podporované operační systémy Windows:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


## <a name="supported-platforms-for-linux-computers-and-vms"></a>Podporované platformy pro virtuální počítače a počítače s Linuxem
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

## <a name="next-steps"></a>Další postup

- [Průvodce Azure Security Center plánováním a provozem](security-center-planning-and-operations-guide.md) – zjistěte, jak naplánovat a pochopit aspekty návrhu, abyste mohli přejít na Azure Security Center
- [Výstrahy zabezpečení podle typu ve službě Azure Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis) – Další informace o analýza chování virtuálního počítače a k chybě při analýze výpisu paměti ve službě Security Center
- [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
- [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/) – přečtěte si blog příspěvky o zabezpečení Azure a dodržování předpisů
