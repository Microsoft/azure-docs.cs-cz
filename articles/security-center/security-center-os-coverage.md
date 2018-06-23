---
title: Podporované platformy v Azure Security Center | Microsoft Docs
description: Tento dokument obsahuje seznam operatings systémy Windows a Linux podporované v Azure Security Center.
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
ms.date: 06/22/2018
ms.author: terrylan
ms.openlocfilehash: 1bb0b61fe8a74edd35bb096fd295aedc67f6efb3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335886"
---
# <a name="supported-platforms-in-azure-security-center"></a>Podporované platformy v Azure Security Center
Sledování stavu zabezpečení a doporučení jsou k dispozici pro virtuální počítače (VM), vytvořené pomocí klasického a modelech nasazení Resource Manager a počítačů.

> [!NOTE]
> Další informace o [classic a modelech nasazení Resource Manager](../azure-classic-rm.md) pro prostředky Azure.
>
>

## <a name="supported-platforms-for-windows-computers-and-vms"></a>Podporované platformy pro počítače se systémem Windows a virtuálních počítačů
Podporované operační systémy Windows:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


## <a name="supported-platforms-for-linux-computers-and-vms"></a>Podporované platformy pro počítače se systémem Linux a virtuální počítače
Podporované operační systémy Linux:

* Verze Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS
* Debian verze 6, 7, 8, 9
* CentOS verze 5, 6, 7
* Red Hat Enterprise Linux (RHEL) verze 5, 6, 7
* SUSE Linux Enterprise Server (SLES) verze 11, 12
* Oracle Linux verze 5, 6, 7
* Linux Amazon 2012.09 prostřednictvím 2017
* OpenSSL 1.1.0 je podporována pouze na platformách x86_64 (64 bitů)

> [!NOTE]
> Pro vypracování analýzy chování virtuálního počítače ještě nejsou k dispozici pro operační systémy Linux.
>
>

## <a name="vms-and-cloud-services"></a>Virtuální počítače a cloudové služby
Virtuální počítače spuštěné v rámci cloudové služby jsou také podporovány. Pouze cloudových služeb webové a pracovní role spuštění v produkčním prostředí, které jsou monitorovány sloty. Další informace o cloudovou službu, najdete v části [cloudové služby přehled](../cloud-services/cloud-services-choose-me.md).

## <a name="next-steps"></a>Další postup

- [Průvodce Azure Security Center plánováním a provozem](security-center-planning-and-operations-guide.md) – zjistěte, jak naplánovat a pochopit aspekty návrhu přijmout službu Azure Security Center
- [Výstrahy zabezpečení podle typu v Azure Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis) – Další informace o analýzy chování virtuálního počítače a havárií analýza výpisu paměti v Centru zabezpečení
- [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
- [Blog o bezpečnosti Azure](http://blogs.msdn.com/b/azuresecurity/) – přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů
