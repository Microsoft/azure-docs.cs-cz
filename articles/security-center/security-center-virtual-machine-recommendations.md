---
title: Ochrana virtuálních počítačů v Azure Security Center | Microsoft Docs
description: Tento dokument adresy doporučení v Azure Security Center, které vám pomůžou chránit virtuální počítače a zůstat souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 54375f6f98b4989a7af8bcde649d967f77c6c862
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2018
ms.locfileid: "27623494"
---
# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>Ochrana virtuálních počítačů v Azure Security Center
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří se doporučení, která vás provede procesem konfigurace potřebných kontrol.  Doporučení platí pro typy prostředků Azure: virtuální počítače (VM), sítě, SQL a aplikace.

Tento článek se zaměřuje na doporučení, která se týkají virtuálních počítačů.  Virtuální počítač doporučení center kolem shromažďování dat, použití aktualizací systému, zřizování antimalwaru, šifrování vaše disky virtuálního počítače a další.  Použijte v následující tabulce vám pomohou pochopit dostupné doporučení pro virtuální počítače a co každé z nich bude dělat, když ho použijete jako odkaz.

## <a name="available-vm-recommendations"></a>K dispozici doporučení pro virtuální počítače
| Doporučení | Popis |
| --- | --- |
| [Povolení shromažďování dat pro předplatná](security-center-enable-data-collection.md) |Doporučuje, abyste zapnuli shromažďování dat v zásadách zabezpečení pro každé ze svých předplatných a všechny virtuální počítače ve svých předplatných. |
| [Povolit šifrování pro účet úložiště Azure](security-center-enable-encryption-for-storage-account.md) | Doporučuje povolit šifrování služby úložiště Azure pro data v klidovém stavu. Šifrování služby úložiště (SSE) funguje tak, že šifrování dat při je zapsán do úložiště Azure a dešifruje před načtení. SSE aktuálně nejsou k dispozici pouze pro službu Azure Blob lze použít pro objekty BLOB bloku, objektů BLOB stránky a doplňovacích objektů BLOB. Další informace najdete v tématu [šifrování služby úložiště pro data v klidovém stavu](../storage/common/storage-service-encryption.md).</br>SSE je podporována pouze na účty úložiště Resource Manager. Klasické účty úložiště nejsou aktuálně podporovány. Abyste pochopili, classic a modelech nasazení Resource Manager, najdete v části [modelech nasazení Azure](../azure-classic-rm.md). |
| [Opravit konfigurace zabezpečení](security-center-remediate-os-vulnerabilities.md) |Doporučuje se, že je zarovnat vaše konfigurace operačního systému s pravidla zabezpečení doporučená konfigurace, například neumožňují ukládání hesel. |
| [Instalace aktualizací systému](security-center-apply-system-updates.md) |Doporučuje nasazení chybějících aktualizací zabezpečení systému a kritických aktualizací do virtuálních počítačů. |
| [Použít pouze v době provedená sítě řízení přístupu](security-center-just-in-time.md) | Doporučuje se použít jenom v přístup k časovému virtuálních počítačů. Právě v čase je funkce ve verzi preview a je k dispozici ve standardní vrstvě služby Security Center. V tématu [cenová](security-center-pricing.md) Další informace o službě Security Center je cenové úrovně. |
| [Restartování po aktualizacích systému](security-center-apply-system-updates.md#reboot-after-system-updates) |Doporučuje, abyste restartovali virtuální počítač k dokončení procesu instalace aktualizací systému. |
| [Instalace Endpoint Protection](security-center-install-endpoint-protection.md) |Doporučuje, abyste do virtuálních počítačů nainstalovali antimalwarové programy (platí pouze pro virtuální počítače s Windows). |
| [Povolení agenta virtuálního počítače](security-center-enable-vm-agent.md) |Umožňuje vám zobrazit, které virtuální počítače vyžadují agenta virtuálního počítače. Agent virtuálního počítače musí být nainstalovaný na virtuálních počítačích, aby mohl poskytovat vyhledávání oprav, vyhledávání směrných plánů a antimalwarové programy. Agent virtuálního počítače je ve výchozím nastavení nainstalován na virtuálních počítačích nasazených z Azure Marketplace. V článku [Agenti a rozšíření virtuálních počítačů – Část 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) najdete informace o tom, jak agenta virtuálního počítače nainstalovat. |
| [Použití šifrování disku](security-center-apply-disk-encryption.md) |Doporučuje, abyste disky svých virtuálních počítačů zašifrovali pomocí služby Azure Disk Encryption (platí pro virtuální počítače s Windows a Linuxem). Na virtuálním počítači se doporučuje šifrování svazku operačního systému i svazku s daty. |
| [Aktualizace verze operačního systému](security-center-update-os-version.md) |Doporučuje aktualizovat na verzi operačního systému (OS) pro cloudové služby na nejnovější dostupnou verzi pro operačních systémů.  Další informace o službách Cloud Services najdete v tématu [cloudové služby přehled](../cloud-services/cloud-services-choose-me.md). |
| [Není nainstalováno posouzení ohrožení zabezpečení](security-center-vulnerability-assessment-recommendations.md) |Doporučuje, abyste na vašem virtuálním počítači nainstalovali řešení posouzení ohrožení zabezpečení. |
| [Náprava ohrožení zabezpečení](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Umožňuje vám zobrazit ohrožení zabezpečení systému a aplikací zjištěná řešením posouzení ohrožení zabezpečení nainstalovaným na vašem virtuálním počítači. |

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:

* [Ochrana aplikací v Azure Security Center](security-center-application-recommendations.md)
* [Ochrana sítě v Azure Security Center](security-center-network-recommendations.md)
* [Ochrana služby Azure SQL v Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
