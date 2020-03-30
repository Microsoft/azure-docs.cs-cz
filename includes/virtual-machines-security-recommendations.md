---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/13/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: d0ede337f2ae17f28cd02df7ed35919bd9955e7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048572"
---
Tento článek obsahuje doporučení zabezpečení pro virtuální počítače Azure. Postupujte podle těchto doporučení, abyste pomohli splnit bezpečnostní povinnosti popsané v našem modelu sdílené odpovědnosti. Doporučení vám také pomohou zlepšit celkové zabezpečení řešení webových aplikací. Další informace o tom, co Společnost Microsoft dělá pro plnění povinností poskytovatele služeb, naleznete [v tématu Sdílené odpovědnosti za cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Některá doporučení tohoto článku můžete automaticky řešit Azure Security Center. Azure Security Center je první obranná linie pro vaše prostředky v Azure. Pravidelně analyzuje stav zabezpečení prostředků Azure k identifikaci potenciálních chyb zabezpečení. Poté doporučuje, jak řešit chyby zabezpečení. Další informace najdete [v tématu Doporučení zabezpečení v Azure Security Center](../articles/security-center/security-center-recommendations.md).

Obecné informace o Azure Security Center najdete v tématu [Co je Azure Security Center?](../articles/security-center/security-center-intro.md).

## <a name="general"></a>Obecné

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Při vytváření vlastních image virtuálních aplikací použijte nejnovější aktualizace. | Před vytvořením bitových kopií nainstalujte nejnovější aktualizace operačního systému a pro všechny aplikace, které budou součástí bitové kopie.  | - |
| Udržujte své virtuální počítače aktuální. | Řešení správy [aktualizací](../articles/automation/automation-update-management.md) v Azure Automation můžete použít ke správě aktualizací operačního systému pro počítače s Windows a Linuxem v Azure. | [Ano](../articles/security-center/security-center-apply-system-updates.md) |
| Zálohujte virtuální počítače. | [Azure Backup](../articles/backup/backup-overview.md) pomáhá chránit data aplikací a má minimální provozní náklady. Chyby aplikace mohou poškodit vaše data a lidské chyby mohou zavádět chyby do vašich aplikací. Azure Backup chrání vaše virtuální počítače, které běží Windows a Linux. | - |
| Použijte více virtuálních počítačů pro větší odolnost a dostupnost. | Pokud váš virtuální počítač spouští aplikace, které musí být vysoce dostupné, použijte více virtuálních počítačů nebo [sad dostupnosti](../articles/virtual-machines/windows/manage-availability.md). | - |
| Přijměte strategii kontinuity provozu a zotavení po havárii (BCDR). | Azure Site Recovery umožňuje vybrat si z různých možností navržených pro podporu kontinuity podnikání. Podporuje různé scénáře replikace a převzetí služeb při selhání. Další informace naleznete v tématu [O obnovení webu](../articles/site-recovery/site-recovery-overview.md). | - |

## <a name="data-security"></a>Zabezpečení dat

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Šifrování disků operačního systému. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) vám pomůže šifrovat vaše disky virtuálního počítače Windows a Linux IaaS. Bez potřebných klíčů je obsah šifrovaných disků nečitelný. Šifrování disku chrání uložená data před neoprávněným přístupem, která by jinak byla možná, pokud by byl disk zkopírován.| [Ano](../articles/security-center/security-center-apply-disk-encryption.md) |
| Šifrovat datové disky. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) vám pomůže šifrovat vaše disky virtuálního počítače Windows a Linux IaaS. Bez potřebných klíčů je obsah šifrovaných disků nečitelný. Šifrování disku chrání uložená data před neoprávněným přístupem, která by jinak byla možná, pokud by byl disk zkopírován.| -  |
| Omezte nainstalovaný software. | Omezte nainstalovaný software na to, co je nutné k úspěšnému použití vašeho řešení. Tato směrnice pomáhá snížit prostor pro útok vašeho řešení. | - |
| Používejte antivirový program nebo antimalwar. | V Azure můžete používat antimalwarový software od dodavatelů zabezpečení, jako jsou Microsoft, Symantec, Trend Micro a Kaspersky. Tento software pomáhá chránit virtuální počítače před škodlivými soubory, adwarem a dalšími hrozbami. Antimalwaru společnosti Microsoft můžete nasadit na základě úloh vaší aplikace. Použijte základní zabezpečenou výchozí nebo upřesňující vlastní konfiguraci. Další informace najdete [v tématu Microsoft Antimalware pro Azure Cloud Services and Virtual Machines](../articles/security/azure-security-antimalware.md). | - |
| Bezpečně uklápěte klíče a tajné klíče. | Zjednodušte správu tajných kódů a klíčů tím, že majitelům aplikací poskytnete zabezpečenou, centrálně spravovanou možnost. Tato správa snižuje riziko náhodného ohrožení nebo úniku. Azure Key Vault můžete bezpečně ukládat klíče v modulech zabezpečení hardwaru (HSMs), které jsou certifikované na FIPS 140-2 Level 2. Pokud potřebujete k ukládání klíčů a tajných kódů použít fip 140.2 úroveň 3, můžete použít [azure dedicated hsm](../articles/dedicated-hsm/overview.md). | - |

## <a name="identity-and-access-management"></a>Správa identit a přístupu 

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Centralizovat ověřování virtuálních můejů. | Ověřování virtuálních zařízení s Windows a Linuxem můžete centralizovat pomocí [ověřování Azure Active Directory](../articles/active-directory/develop/authentication-scenarios.md). | - |

## <a name="monitoring"></a>Monitorování

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Sledujte své virtuální počítače. | [Azure Monitor pro virtuální počítače](../articles/azure-monitor/insights/vminsights-overview.md) můžete použít ke sledování stavu virtuálních počítačů Azure a škálovacích sad virtuálních strojů. Problémy s výkonem s virtuálním virtuálním ms může vést k přerušení služby, což porušuje princip zabezpečení dostupnosti. | - |

## <a name="networking"></a>Síťové služby

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Omezte přístup k portům pro správu. | Útočníci skenují rozsahy IP adres veřejného cloudu, kde hledají otevřené porty pro správu, a pokoušejí se o "snadné" útoky, jako jsou běžná hesla a známé neopravené chyby zabezpečení. Přístup k virtuálním počítačům [za chvíli (JIT)](../articles/security-center/security-center-just-in-time.md) můžete použít k uzamčení příchozího provozu na virtuálních počítačích Azure, což snižuje vystavení útokům a zároveň poskytuje snadné připojení k virtuálním počítačům, když jsou potřeba. | - |
| Omezte přístup k síti. | Skupiny zabezpečení sítě umožňují omezit přístup k síti a řídit počet exponovaných koncových bodů. Další informace naleznete v [tématu Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](../articles/virtual-network/manage-network-security-group.md). | - |

## <a name="next-steps"></a>Další kroky

Informace o dalších požadavcích na zabezpečení vám poskytne poskytovatel aplikací. Další informace o vývoji zabezpečených aplikací naleznete v [dokumentaci k bezpečnému vývoji](../articles/security/fundamentals/abstract-develop-secure-apps.md).
