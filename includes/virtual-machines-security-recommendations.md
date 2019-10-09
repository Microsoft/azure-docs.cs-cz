---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/19/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: 795b9beedabd119df98487a0bc00160bcf21507f
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174177"
---
Tento článek obsahuje doporučení zabezpečení pro Azure Virtual Machines. Tato doporučení vám pomůžou splnit bezpečnostní povinnosti popsané v našem modelu pro sdílenou zodpovědnost. Doporučení vám taky pomůžou zlepšit celkové zabezpečení vašich řešení webových aplikací. Další informace o tom, co společnost Microsoft potřebuje k plnění zodpovědností poskytovatele služeb, najdete v tématu [sdílené odpovědnosti pro cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

Některá z doporučení tohoto článku můžete automaticky řešit pomocí Azure Security Center. Azure Security Center je první linií ochrany vašich prostředků v Azure. Pravidelně analyzuje stav zabezpečení vašich prostředků Azure, aby identifikovala potenciální ohrožení zabezpečení. Pak doporučí řešení chyb zabezpečení. Další informace najdete v tématu [doporučení zabezpečení v Azure Security Center](../articles/security-center/security-center-recommendations.md).

Obecné informace o Azure Security Center najdete v tématu [co je Azure Security Center?](../articles/security-center/security-center-intro.md).

## <a name="recommendations"></a>Doporučit

| Kategorie | Základě | Komentáře | Security Center |
|-|-|----|--|
| Obecné | Při vytváření vlastních imagí virtuálních počítačů použijte nejnovější aktualizace. | Před vytvořením imagí nainstalujte nejnovější aktualizace pro operační systém a pro všechny aplikace, které budou součástí vaší image.  | - |
| Obecné | Udržujte své virtuální počítače v aktuálním prostředí. | Řešení [Update Management](../articles/automation/automation-update-management.md) v Azure Automation můžete použít ke správě aktualizací operačního systému pro počítače se systémem Windows a Linux v Azure. | [Ano](../articles/security-center/security-center-apply-system-updates.md) |
| Obecné | Zálohujte své virtuální počítače. | [Azure Backup](../articles/backup/backup-overview.md) pomáhá chránit data aplikací a má minimální provozní náklady. Chyby aplikací můžou poškodit vaše data a lidské chyby můžou do vašich aplikací zavádět chyby. Azure Backup chrání vaše virtuální počítače se systémem Windows a Linux. | - |
| Obecné | Pro zajištění vyšší odolnosti a dostupnosti použijte více virtuálních počítačů. | Pokud váš virtuální počítač spouští aplikace, které musí být vysoce dostupné, použijte několik virtuálních počítačů nebo [skupin dostupnosti](../articles/virtual-machines/windows/manage-availability.md). | - |
| Obecné | Proveďte strategii pro provozní kontinuitu a zotavení po havárii (BCDR). | Azure Site Recovery vám umožní vybírat z různých možností, které jsou navržené pro podporu kontinuity podnikových služeb. Podporuje různé scénáře replikace a převzetí služeb při selhání. Další informace najdete v tématu [o Site Recovery](../articles/site-recovery/site-recovery-overview.md). | - |
| Správa identit a přístupu | Centralizace ověřování virtuálních počítačů. | Ověřování virtuálních počítačů s Windows a Linuxem můžete centralizovat pomocí [Azure Active Directory ověřování](../articles/active-directory/develop/authentication-scenarios.md). | - |
| Zabezpečení dat | Zašifrujte disky operačního systému. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) vám pomůže s šifrováním disků virtuálních počítačů s Windows a Linux IaaS. Bez nezbytných klíčů nelze obsah šifrovaných disků číst. Šifrování disku chrání uložená data před neoprávněným přístupem, které by jinak bylo možné při kopírování disku.| [Ano](../articles/security-center/security-center-apply-disk-encryption.md) |
| Zabezpečení dat | Šifrování datových disků. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) vám pomůže s šifrováním disků virtuálních počítačů s Windows a Linux IaaS. Bez nezbytných klíčů nelze obsah šifrovaných disků číst. Šifrování disku chrání uložená data před neoprávněným přístupem, které by jinak bylo možné při kopírování disku.| -  |
| Zabezpečení dat | Omezte nainstalovaný software. | Omezte nainstalovaný software na to, co je potřeba k úspěšnému nasazení vašeho řešení. Tyto zásady pomáhají omezit plochu útoku na vaše řešení. | - |
| Zabezpečení dat | Používejte antivirový program nebo antimalwarový software. | V Azure můžete použít antimalwarový software od dodavatelů zabezpečení, jako jsou Microsoft, Symantec, Trend Micro a Kaspersky. Tento software pomáhá chránit vaše virtuální počítače před škodlivými soubory, adwarem a dalšími hrozbami. Microsoft Antimalware můžete nasadit na základě úloh vaší aplikace. Použijte buď základní zabezpečení, nebo pokročilou vlastní konfiguraci. Další informace najdete v tématu [Microsoft Antimalware pro Azure Cloud Services a Virtual Machines](../articles/security/azure-security-antimalware.md). | - |
| Zabezpečení dat | Bezpečné ukládání klíčů a tajných kódů | Zjednodušte správu tajných kódů a klíčů tím, že svým vlastníkům vaší aplikace poskytnete zabezpečenou, centrálně spravovanou možnost. Tato Správa snižuje riziko náhodného narušení nebo nevracení. Azure Key Vault můžou bezpečně ukládat klíče v modulech hardwarového zabezpečení (HSM), které jsou certifikované pro FIPS 140-2 úrovně 2. Pokud k ukládání klíčů a tajných kódů potřebujete použít standard FIPs 140,2 úrovně 3, můžete použít [vyhrazený modul HSM Azure](../articles/dedicated-hsm/overview.md). | - |
| Síťové služby | Omezte přístup k portům pro správu. | Útočníci prochází rozsahy IP adres veřejných cloudu pro otevřené porty pro správu a pokoušejí "jednoduché" útoky, jako jsou běžná hesla a známá slabá místa v opravených chybách. [Přístup k virtuálnímu počítači](../articles/security-center/security-center-just-in-time.md) za běhu můžete použít k uzamknutí příchozího provozu do virtuálních počítačů Azure. tím se sníží riziko útoků na útoky a při jejich vyžádání se snadno připojovat k virtuálním počítačům. | - |
| Síťové služby | Omezte přístup k síti. | Skupiny zabezpečení sítě umožňují omezit přístup k síti a řídit počet vystavených koncových bodů. Další informace najdete v tématu [Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](../articles/virtual-network/manage-network-security-group.md). | - |
| Sledovaný | Monitorujte své virtuální počítače. | Pomocí [Azure monitor pro virtuální počítače](../articles/azure-monitor/insights/vminsights-overview.md) můžete monitorovat stav virtuálních počítačů Azure a sady škálování virtuálních počítačů. Problémy s výkonem virtuálního počítače mohou způsobit přerušení služby, což porušuje princip zabezpečení dostupnosti. | - |

## <a name="next-steps"></a>Další kroky

Další informace o požadavcích na zabezpečení najdete u svého poskytovatele aplikace. Další informace o vývoji zabezpečených aplikací najdete v [dokumentaci k zabezpečení vývoje](../articles/security/fundamentals/abstract-develop-secure-apps.md).
