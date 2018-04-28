---
title: Azure Disk Encryption – nejčastější dotazy | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy k Microsoft Azure Disk Encryption pro systém Windows a virtuálních počítačů IaaS Linux.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2018
ms.author: barclayn
ms.openlocfilehash: 47ccf91a64653c928cc4da01bc98535c97440d37
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-disk-encryption-faq"></a>Nejčastější dotazy k Azure Disk Encryption

Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) o Azure Disk Encryption pro systém Windows a virtuálních počítačů IaaS Linux. Další informace o této službě najdete v tématu [Azure Disk Encryption pro systém Windows a virtuálních počítačů IaaS Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Je-li Azure Disk Encryption obecné dostupnosti (GA)?

Azure Disk Encryption pro Windows a virtuálních počítačů IaaS Linux je obecně dostupnosti ve všech oblastech Azure veřejné.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Vyskytne uživatele, jsou k dispozici s Azure Disk Encryption?

Azure Disk Encryption GA podporuje šablony Azure Resource Manager, prostředí Azure PowerShell a rozhraní příkazového řádku Azure. To vám přináší značnou flexibilitu. Máte tři různé možnosti pro povolení šifrování disku pro virtuální počítače IaaS. Další informace o uživatelské rozhraní a podrobné pokyny k dispozici v Azure Disk Encryption najdete v tématu [Azure Disk Encryption scénáře nasazení a prostředí](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Kolik Azure Disk Encryption stojí?

Je bezplatná pro šifrování disky virtuálních počítačů s Azure Disk Encryption, ale existují poplatky související s používáním Azure Key Vault. Další informace o Azure Key Vault naleznete náklady [Key Vault ceny](https://azure.microsoft.com/pricing/details/key-vault/) stránky.

## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Které vrstvy virtuálního počítače Azure Disk Encryption podporuje?

Je k dispozici na úrovni standard virtuálních počítačích, včetně Azure Disk Encryption [A, D, DS, G, GS a F](https://azure.microsoft.com/pricing/details/virtual-machines/) řady virtuální počítače IaaS. Je k dispozici pro virtuální počítače s storage úrovně premium. Není k dispozici na úroveň basic virtuálních počítačů.

## <a name="what-linux-distributions-does-azure-disk-encryption-support"></a>Jaké Linuxových distribucích podporuje Azure Disk Encryption?

Azure Disk Encryption je podporováno v následujících Linuxových distribucích serveru a verze:

| Distribuce systému Linux | Verze | Typ svazku podporovaný pro šifrování|
| --- | --- |--- |
| Ubuntu | 16.04. DENNĚ LTS | Disk operačního systému a dat |
| Ubuntu | 14.04.5-DAILY-LTS | Disk operačního systému a dat |
| RHEL | 7.4 | Datový disk * |
| RHEL | 7.3 | Datový disk * |
| RHEL | 7.2 | Datový disk * |
| RHEL | 6.8 | Datový disk * |
| RHEL | 6.7 | Datový disk * |
| CentOS | 7.3 | Disk operačního systému a dat |
| CentOS | 7.2N | Disk operačního systému a dat |
| CentOS | 6.8 | Disk operačního systému a dat |
| CentOS | 7.1 | Datový disk |
| CentOS | 7.0 | Datový disk |
| CentOS | 6.7 | Datový disk |
| CentOS | 6.6 | Datový disk |
| CentOS | 6.5 | Datový disk |
| openSUSE | 13.2 | Datový disk |
| SLES | 12 SP1 | Datový disk |
| SLES | Priorita: 12-SP1 | Datový disk |
| SLES | HPC 12 | Datový disk |
| SLES | Priorita: 11-SP4 | Datový disk |
| SLES | 11 SP4 | Datový disk |

*__ADE se podporuje pro RHEL pro datový disk. Aktuální implementace ADE funguje pro disk operačního systému, ale nepodporuje aktuálně společně. Společnosti Microsoft a Red Hat práce na společně podporované řešení. Do té doby můžete odkazovat v dokumentu White Paper ADE šifrování disku operačního systému Linux [zde](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Jak můžete začít používat Azure Disk Encryption?

Chcete-li začít, přečtěte si [Azure Disk Encryption pro systém Windows a virtuálních počítačů IaaS Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) dokumentu white paper.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Můžete šifrovat spouštěcí a datové svazky s Azure Disk Encryption?

Ano, můžete šifrovat spouštěcí a datové svazky pro systém Windows a virtuálních počítačů IaaS Linux. U virtuálních počítačů Windows nelze zašifrovat data bez první šifrování svazku operačního systému. Pro virtuální počítače s Linuxem můžete šifrovat datový svazek bez nutnosti nejprve šifrování svazku operačního systému. Poté, co jste zašifrovali svazku operačního systému Linux, zakázáním šifrování na svazku operačního systému pro virtuální počítače IaaS Linux není podporována.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok-capability"></a>Umožňuje Azure Disk Encryption přineste si vlastní klíč (BYOK) funkce?

Ano, můžete zadat vlastní klíče šifrovací klíče. Tyto klíče jsou chráněné v Azure Key Vault, což je úložiště klíčů pro Azure Disk Encryption. Další informace o klíčových šifrovací klíče podporu scénářů najdete v tématu [Azure Disk Encryption scénáře nasazení a prostředí](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Můžete použít Azure vytvořit klíče šifrovací klíč?

Ano, můžete použít Azure Key Vault ke generování klíče šifrovací klíč pro použití Azure disk encryption. Tyto klíče jsou chráněné v Azure Key Vault, což je úložiště klíčů pro Azure Disk Encryption. Další informace o scénářích klíče podpora šifrování pomocí klíče najdete v tématu [Azure Disk Encryption scénáře nasazení a prostředí](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Můžete použít místní službu správy klíčů nebo modulu hardwarového zabezpečení k ochraně šifrovacích klíčů?

Aby se předešlo šifrovacích klíčů s Azure Disk Encryption nemůžete použít místní službu správy klíčů nebo HSM. Aby se předešlo šifrovací klíče můžete použít pouze do služby Azure Key Vault. Další informace o scénářích klíče podpora šifrování pomocí klíče najdete v tématu [Azure Disk Encryption scénáře nasazení a prostředí](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Jaké jsou požadavky pro konfiguraci Azure Disk Encryption?

Je skript prostředí PowerShell požadovaných součástí. Pomocí tohoto skriptu můžete vytvořit aplikaci Azure Active Directory, vytvoření nového trezoru klíčů nebo nastavit existující trezor klíčů pro přístup k šifrování disku povolit šifrování a chránit tajných klíčů a klíče. Další informace o scénářích klíče podpora šifrování pomocí klíče najdete v tématu [Azure Disk Encryption požadavky a scénáře nasazení a prostředí](azure-security-disk-encryption.md#prerequisites).

## <a name="where-can-i-get-more-information-on-how-to-use-powershell-for-configuring-azure-disk-encryption"></a>Kde je můžete získat další informace o tom, jak pomocí prostředí PowerShell pro konfiguraci Azure Disk Encryption?

Máme některé skvělé články v tom, jak můžete provádět základní úlohy Azure Disk Encryption, jakož i pokročilejší scénáře. Základní úlohách najdete v tématu [prozkoumat Azure Disk Encryption s prostředím Azure PowerShell – část 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/). Pro pokročilejší scénáře, najdete v části [prozkoumat Azure Disk Encryption s prostředím Azure PowerShell – část 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/).

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Jakou verzi prostředí Azure PowerShell Azure Disk Encryption podporuje?

Použijte nejnovější verzi sady Azure PowerShell SDK ke konfiguraci Azure Disk Encryption. Stáhněte si nejnovější verzi [prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption je *není* nepodporuje verze 1.1.0 sady Azure SDK.

> [!NOTE]
> Rozšíření Linux Azure šifrování disku preview je zastaralý. Podrobnosti najdete v tématu [místo začne Azure disk encryption preview rozšíření pro virtuální počítače IaaS Linux](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Můžete použít Azure Disk Encryption na mé vlastní bitovou kopii systému Linux?

Azure Disk Encryption nelze použít na vaše vlastní bitovou kopii systému Linux. Pouze obrázky Linux Galerie jsme podporu pro podporované distribuce vyznačeny dříve. Vlastní Image Linux aktuálně nepodporujeme.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Můžete použít aktualizace pro Red Hat virtuálního počítače s Linuxem používající yum aktualizace?

Ano, můžete provést aktualizace nebo opravy virtuálního počítače s Linuxem Red Hat. Další informace najdete v tématu [použití aktualizací na šifrovaný IaaS Red Hat virtuálního počítače Azure pomocí aktualizace yum](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Co je pracovní postup šifrování disku doporučený Azure pro Linux?

Následující pracovní postup se doporučuje mít nejlepších výsledků v systému Linux:
* Spuštění z bitové kopie beze změny uložených Galerie odpovídající požadované distro operačního systému a verze
* Zálohujte všechny připojené jednotky, které se budou šifrovat.  To umožňuje obnovení v případě selhání, například pokud virtuální počítač je restartovat předtím, než byla dokončena šifrování.
* Šifrování (může trvat několik hodiny nebo i dny v závislosti na vlastnosti virtuálního počítače a velikost všech připojených datových disků)
* Přizpůsobit a přidat softwaru na bitovou kopii podle potřeby.

Pokud tento pracovní postup není možné, spoléhat na [šifrování služby úložiště](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE) na úložiště platformy vrstvy účet může být alternativu k použití dm-crypt šifrování celého disku.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>Co je na disku "Svazek Bek" nebo "/ mnt/azure_bek_disk"?

Pro systém Windows "Bek svazek" nebo "/ mnt/azure_bek_disk" pro Linux je místní data svazek, který bezpečně ukládá šifrovací klíče pro šifrovaná virtuálních počítačů Azure IaaS.
> [!NOTE]
> Odstranit nebo upravit všechny obsah v tomto disku. Vzhledem k tomu, že šifrovací klíče přítomnosti je potřeba pro všechny operace šifrování pro virtuální počítač IaaS není odpojit disk.

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Kde můžete přejít k nim máte nějaké otázky nebo poskytnout zpětnou vazbu?

Můžete klást otázky nebo na svůj názor [fórum pro Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, informace o nejčastější dotazy týkající se Azure Disk Encryption. Další informace o této služby a jeho funkce najdete v následujících článcích:

- [Použít šifrování disku v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Šifrování virtuálního počítače Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Šifrování dat Azure v klidovém stavu](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
