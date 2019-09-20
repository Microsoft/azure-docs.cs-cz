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
ms.openlocfilehash: 60d4957f65c124de683c6d156561de3b52aaec94
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71163940"
---
Tento článek obsahuje doporučení zabezpečení pro Azure Virtual Machines. Implementace těchto doporučení vám pomůže splnit vaše povinnosti zabezpečení, jak je popsáno v našem sdíleném modelu zodpovědnosti, a zvýší celkové zabezpečení vašich řešení webových aplikací. Další informace o tom, co společnost Microsoft splní zodpovědností poskytovatele služeb, najdete v článku [sdílené odpovědnosti pro cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

Některá doporučení obsažená v tomto článku můžete automaticky monitorovat pomocí Azure Security Center. Azure Security Center je první linií obrany při ochraně vašich prostředků v Azure. Pravidelně analyzuje stav zabezpečení vašich prostředků Azure, aby identifikovala potenciální ohrožení zabezpečení. Pak vám poskytne doporučení, jak je řešit.

- Další informace o Azure Security Center doporučeních najdete v tématu [doporučení zabezpečení v Azure Security Center](../articles/security-center/security-center-recommendations.md).
- Informace o Azure Security Center najdete v [Azure Security Center?](../articles/security-center/security-center-intro.md)

## <a name="recommendations"></a>Doporučení

| Category | Doporučení | Komentáře | Security Center |
|-|-|----|--|
| Obecné | Při sestavování vlastních imagí virtuálních počítačů použijte nejnovější aktualizace. | Před vytvořením imagí zajistěte, aby byly nainstalované všechny nejnovější aktualizace pro operační systém a všechny aplikace, které budou součástí vaší image.  | - |
| Obecné | Zachování aktuálnosti virtuálních počítačů | Řešení [Update Management](../articles/automation/automation-update-management.md) v Azure Automation můžete použít ke správě aktualizací operačního systému pro počítače se systémem Windows a Linux v Azure. | [Ano](../articles/security-center/security-center-apply-system-updates.md) |
| Obecné | Zálohování virtuálních počítačů | [Azure Backup](../articles/backup/backup-overview.md) pomáhá chránit data aplikací s minimálními provozními náklady. Chyby aplikací můžou poškodit vaše data a lidské omyly zase můžou způsobit chyby v aplikacích. Díky Azure Backup jsou virtuální počítače se systémem Windows a Linux chráněny. | - |
| Obecné | Použití více virtuálních počítačů pro zajištění vyšší odolnosti a dostupnosti | Pokud váš virtuální počítač spouští aplikace, které potřebují vysokou dostupnost, měli byste mít několik virtuálních počítačů nebo [skupin dostupnosti](../articles/virtual-machines/windows/manage-availability.md) . | - |
| Obecné | Zajištění strategie pro provozní kontinuitu a zotavení po havárii (BCDR) | Azure Site Recovery vám umožní vybírat z různých možností, které jsou navržené tak, aby podporovaly potřeby provozní kontinuity. Podporuje různé scénáře replikace a převzetí služeb při selhání. Další informace najdete v tématu [o Site Recovery](../articles/site-recovery/site-recovery-overview.md) . | - |
| Správa identit a přístupu | Centralizace ověřování virtuálních počítačů | Ověřování virtuálních počítačů s Windows a Linuxem můžete centralizovat pomocí [ověřování Azure AD](../articles/active-directory/develop/authentication-scenarios.md). | - |
| Zabezpečení dat | Šifrování disků operačního systému | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) vám pomůže s šifrováním disků virtuálních počítačů s Windows a Linux IaaS. Při šifrování disků se obsah nedá číst bez nutnosti použití potřebných klíčů. Šifrování disku chrání uložená data před neoprávněným přístupem, které by jinak bylo možné při kopírování disku.| [Ano](../articles/security-center/security-center-apply-disk-encryption.md) |
| Zabezpečení dat | Šifrování datových disků | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) vám pomůže s šifrováním disků virtuálních počítačů s Windows a Linux IaaS. Při šifrování disků se obsah nedá číst bez nutnosti použití potřebných klíčů. Šifrování disku chrání uložená data před neoprávněným přístupem, které by jinak bylo možné při kopírování disku.| -  |
| Zabezpečení dat | Omezení nainstalovaného softwaru | Omezení nainstalovaného softwaru na to, co je potřeba k úspěšné implementaci vašeho řešení, pomáhá omezit plochu útoku na vaše řešení. | - |
| Zabezpečení dat | Použití antivirového/antimalwaru | V Azure můžete použít antimalwarový software od dodavatelů zabezpečení, jako jsou Microsoft, Symantec, Trend Micro a Kaspersky. Tento software pomáhá chránit vaše virtuální počítače před škodlivými soubory, adwarem a dalšími hrozbami. Ochranu proti malwaru společnosti Microsoft můžete nasadit v závislosti na úlohách vaší aplikace, a to buď se základní zabezpečenou nebo pokročilou vlastní konfigurací. Další informace o programu Microsoft Antimalware pro Azure najdete v tématu [Microsoft Antimalware pro azure Cloud Services a Virtual Machines](../articles/security/azure-security-antimalware.md) | - |
| Zabezpečení dat | Bezpečné ukládání klíčů a tajných kódů | Zjednodušení správy tajných kódů a klíčů poskytnutím vlastníků vaší aplikace pomocí zabezpečené centrálně spravované možnosti můžete snížit riziko náhodného ohrožení nebo nevracení. Azure Key Vault můžou bezpečně ukládat klíče v modulech hardwarového zabezpečení (HSM) s certifikací na úrovni FIPS 140-2 úrovně 2. Pokud potřebujete ukládat klíče a tajné klíče pomocí standardu FIPs 140,2 Level 3, můžete použít [vyhrazený modul HSM Azure](../articles/dedicated-hsm/overview.md) . | - |
| Sítě | Omezení přístupu k portům pro správu | Útočníci prochází rozsahy IP adres veřejných cloudu pro otevřené porty pro správu a pokoušejí "jednoduché" útoky, jako jsou běžná hesla a známá slabá místa v opravených chybách. [Přístup k virtuálnímu počítači za běhu (just-in-time)](../articles/security-center/security-center-just-in-time.md) se dá použít k uzamknutí příchozího provozu do virtuálních počítačů Azure. tím se sníží riziko útoků na útoky a zároveň se vám umožní snadný přístup k virtuálním počítačům v případě potřeby. | - |
| Sítě | Omezení přístupu k síti | Skupiny zabezpečení sítě umožňují omezit přístup k síti a řídit počet vystavených koncových bodů. Další informace najdete v tématu [Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](../articles/virtual-network/manage-network-security-group.md) . | - |
| Monitorování | Monitorování virtuálních počítačů | Pomocí [služby Azure monitor pro](../articles/azure-monitor/insights/vminsights-overview.md) virtuální počítače můžete monitorovat stav virtuálních počítačů Azure a sady škálování virtuálních počítačů. Problémy s výkonem virtuálního počítače mohou způsobit přerušení služby, což porušuje princip zabezpečení dostupnosti. | - |

## <a name="next-steps"></a>Další kroky

Obraťte se na poskytovatele aplikace a zjistěte, zda jsou k dispozici další požadavky na zabezpečení. Další informace o vývoji zabezpečených aplikací najdete v [dokumentaci k zabezpečení vývoje](../articles/security/fundamentals/abstract-develop-secure-apps.md).