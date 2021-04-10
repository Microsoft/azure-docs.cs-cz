---
title: Doporučení zabezpečení pro virtuální počítače v Azure
description: Tato doporučení můžete použít pro virtuální počítače v Azure, které vám pomůžou splnit bezpečnostní povinnosti popsané v modelu sdílené zodpovědnosti a zlepšit celkové zabezpečení vašich nasazení.
author: msmbaldwin
manager: RKarlin
ms.service: virtual-machines
ms.subservice: security
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.reviewer: cynthn
ms.openlocfilehash: 622ebfed8294dbcb441aa509fc4f6ba75114f28d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499352"
---
# <a name="security-recommendations-for-virtual-machines-in-azure"></a>Doporučení zabezpečení pro virtuální počítače v Azure


Tento článek obsahuje doporučení zabezpečení pro Azure Virtual Machines. Tato doporučení vám pomůžou splnit bezpečnostní povinnosti popsané v našem modelu pro sdílenou zodpovědnost. Doporučení vám taky pomůžou zlepšit celkové zabezpečení vašich řešení webových aplikací. Další informace o tom, co společnost Microsoft potřebuje k plnění zodpovědností poskytovatele služeb, najdete v tématu [sdílené odpovědnosti pro cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Některá z doporučení tohoto článku můžete automaticky řešit pomocí Azure Security Center. Azure Security Center je první linií ochrany vašich prostředků v Azure. Pravidelně analyzuje stav zabezpečení vašich prostředků Azure, aby identifikovala potenciální ohrožení zabezpečení. Pak doporučí řešení chyb zabezpečení. Další informace najdete v tématu [doporučení zabezpečení v Azure Security Center](../security-center/security-center-recommendations.md).

Obecné informace o Azure Security Center najdete v tématu [co je Azure Security Center?](../security-center/security-center-introduction.md).

## <a name="general"></a>Obecné

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Při vytváření vlastních imagí virtuálních počítačů použijte nejnovější aktualizace. | Před vytvořením imagí nainstalujte nejnovější aktualizace pro operační systém a pro všechny aplikace, které budou součástí vaší image.  | - |
| Udržujte své virtuální počítače v aktuálním prostředí. | Řešení [Update Management](../automation/update-management/overview.md) v Azure Automation můžete použít ke správě aktualizací operačního systému pro počítače se systémem Windows a Linux v Azure. | [Ano](../security-center/asset-inventory.md) |
| Zálohujte své virtuální počítače. | [Azure Backup](../backup/backup-overview.md) pomáhá chránit data aplikací a má minimální provozní náklady. Chyby aplikací můžou poškodit vaše data a lidské chyby můžou do vašich aplikací zavádět chyby. Azure Backup chrání vaše virtuální počítače se systémem Windows a Linux. | - |
| Pro zajištění vyšší odolnosti a dostupnosti použijte více virtuálních počítačů. | Pokud váš virtuální počítač spouští aplikace, které musí být vysoce dostupné, použijte několik virtuálních počítačů nebo [skupin dostupnosti](./availability.md). | - |
| Proveďte strategii pro provozní kontinuitu a zotavení po havárii (BCDR). | Azure Site Recovery vám umožní vybírat z různých možností, které jsou navržené pro podporu kontinuity podnikových služeb. Podporuje různé scénáře replikace a převzetí služeb při selhání. Další informace najdete v tématu  [o Site Recovery](../site-recovery/site-recovery-overview.md). | - |

## <a name="data-security"></a>Zabezpečení dat

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Zašifrujte disky operačního systému. | [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) vám pomůže s šifrováním disků virtuálních počítačů s Windows a Linux IaaS. Bez nezbytných klíčů nelze obsah šifrovaných disků číst. Šifrování disku chrání uložená data před neoprávněným přístupem, které by jinak bylo možné při kopírování disku.| [Ano](../security-center/asset-inventory.md) |
| Šifrování datových disků. | [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) vám pomůže s šifrováním disků virtuálních počítačů s Windows a Linux IaaS. Bez nezbytných klíčů nelze obsah šifrovaných disků číst. Šifrování disku chrání uložená data před neoprávněným přístupem, které by jinak bylo možné při kopírování disku.| -  |
| Omezte nainstalovaný software. | Omezte nainstalovaný software na to, co je potřeba k úspěšnému nasazení vašeho řešení. Tyto zásady pomáhají omezit plochu útoku na vaše řešení. | - |
| Používejte antivirový program nebo antimalwarový software. | V Azure můžete použít antimalwarový software od dodavatelů zabezpečení, jako jsou Microsoft, Symantec, Trend Micro a Kaspersky. Tento software pomáhá chránit vaše virtuální počítače před škodlivými soubory, adwarem a dalšími hrozbami. Microsoft Antimalware můžete nasadit na základě úloh vaší aplikace. Microsoft Antimalware je k dispozici jenom pro počítače s Windows. Použijte buď základní zabezpečení, nebo pokročilou vlastní konfiguraci. Další informace najdete v tématu [Microsoft Antimalware pro Azure Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md). | - |
| Bezpečné ukládání klíčů a tajných kódů | Zjednodušte správu tajných kódů a klíčů tím, že svým vlastníkům vaší aplikace poskytnete zabezpečenou, centrálně spravovanou možnost. Tato Správa snižuje riziko náhodného narušení nebo nevracení. Azure Key Vault můžou bezpečně ukládat klíče v modulech hardwarového zabezpečení (HSM), které jsou certifikované pro FIPS 140-2 úrovně 2. Pokud k ukládání klíčů a tajných kódů potřebujete použít standard FIPs 140,2 úrovně 3, můžete použít [vyhrazený modul HSM Azure](../dedicated-hsm/overview.md). | - |

## <a name="identity-and-access-management"></a>Správa identit a přístupu 

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Centralizace ověřování virtuálních počítačů. | Ověřování virtuálních počítačů s Windows a Linuxem můžete centralizovat pomocí [Azure Active Directory ověřování](../active-directory/develop/authentication-vs-authorization.md). | - |

## <a name="monitoring"></a>Monitorování

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Monitorujte své virtuální počítače. | Pomocí [Azure monitor pro virtuální počítače](../azure-monitor/vm/vminsights-overview.md) můžete monitorovat stav virtuálních počítačů Azure a sady škálování virtuálních počítačů. Problémy s výkonem virtuálního počítače mohou způsobit přerušení služby, což porušuje princip zabezpečení dostupnosti. | - |

## <a name="networking"></a>Sítě

| Doporučení | Komentáře | Security Center |
|-|----|--|
| Omezte přístup k portům pro správu. | Útočníci prochází rozsahy IP adres veřejných cloudu pro otevřené porty pro správu a pokoušejí "jednoduché" útoky, jako jsou běžná hesla a známá slabá místa v opravených chybách. [Přístup k virtuálnímu počítači](../security-center/security-center-just-in-time.md) za běhu můžete použít k uzamknutí příchozího provozu do virtuálních počítačů Azure. tím se sníží riziko útoků na útoky a při jejich vyžádání se snadno připojovat k virtuálním počítačům. | - |
| Omezte přístup k síti. | Skupiny zabezpečení sítě umožňují omezit přístup k síti a řídit počet vystavených koncových bodů. Další informace najdete v tématu [Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md). | - |

## <a name="next-steps"></a>Další kroky

Další informace o požadavcích na zabezpečení najdete u svého poskytovatele aplikace. Další informace o vývoji zabezpečených aplikací najdete v [dokumentaci k zabezpečení vývoje](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).