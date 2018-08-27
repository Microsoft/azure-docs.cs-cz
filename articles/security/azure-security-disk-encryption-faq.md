---
title: Azure Disk Encryption – nejčastější dotazy | Dokumentace Microsoftu
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Microsoft Azure Disk Encryption pro Windows a virtuálních počítačů IaaS s Linuxem.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2018
ms.author: mstewart
ms.openlocfilehash: b93ad4d6d4637b3060d92fb5e17747294c7e6211
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42885988"
---
# <a name="azure-disk-encryption-faq"></a>Nejčastější dotazy k Azure Disk Encryption

Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) o Azure Disk Encryption pro Windows a virtuálních počítačů IaaS s Linuxem. Další informace o této službě najdete v tématu [Azure Disk Encryption pro Windows a virtuálních počítačů IaaS s Linuxem](azure-security-disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Kde je Azure Disk Encryption v obecné dostupnosti (GA)?

Azure Disk Encryption pro Windows a virtuálních počítačů IaaS s Linuxem jsou obecně dostupné ve všech veřejných oblastech Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Jaké uživatelské prostředí jsou k dispozici s Azure Disk Encryption?

Azure Disk Encryption GA podporuje šablony Azure Resource Manageru, Azure Powershellu a rozhraní příkazového řádku Azure. Více uživatelských prostředí poskytují flexibilitu. Budete mít tři různé možnosti pro povolení šifrování disků pro virtuální počítače IaaS. Další informace o uživatelské prostředí a podrobné pokyny k dispozici v Azure Disk Encryption najdete v tématu [povolit Azure Disk Encryption pro Windows](azure-security-disk-encryption-windows.md) a [povolit Azure Disk Encryption pro Linux](azure-security-disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Kolik stojí Azure Disk Encryption

Neplatí žádné poplatky pro šifrování disků virtuálních počítačů pomocí Azure Disk Encryption, ale existují poplatky související s používáním služby Azure Key Vault. Další informace o Azure Key Vault náklady, najdete v článku [cenách služby Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) stránky.


## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Jaké úrovně virtuálních počítačů Azure Disk Encryption podporuje?

Azure Disk Encryption je k dispozici na virtuálních počítačích úrovně standard, včetně [A, D, DS, G, GS a F](https://azure.microsoft.com/pricing/details/virtual-machines/) řady virtuálních počítačů IaaS. Je také k dispozici pro virtuální počítače s premium storage. Není k dispozici na úrovni basic virtuálních počítačů.

## <a name="bkmk_LinuxOSSupport"></a> Jaké distribuce Linuxu podporuje Azure Disk Encryption?

Azure Disk Encryption je podporováno v následujících Linuxových distribucích serveru a verze:

| Linuxové distribuce | Verze | Typ svazku podporovaný pro šifrování|
| --- | --- |--- |
| Ubuntu | 16.04. KAŽDÝ DEN LTS | Disk operačního systému a dat |
| Ubuntu | 14.04.5-DAILY-LTS | Disk operačního systému a dat |
| RHEL | 7.5 | Datový disk * |
| RHEL | 7.4 | Datový disk * |
| RHEL | 7.3 | Datový disk * |
| RHEL | 7.2 | Datový disk * |
| RHEL | 6.8 | Datový disk * |
| RHEL | 6.7 | Datový disk * |
| CentOS | 7.4 | Disk operačního systému a dat |
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
| SLES | Priorita: 12 – aktualizace SP1 | Datový disk |
| SLES | PROSTŘEDÍ HPC 12 | Datový disk |
| SLES | Priorita: 11-SP4 | Datový disk |
| SLES | 11 SP4 | Datový disk |


*__ADE je podporována pro RHEL pro datový disk. Aktuální implementace ADE funguje pro disk s operačním systémem, ale aktuálně společně se nepodporuje. Microsoft a Red Hat pracujete společně podporované řešení. Prozatím můžete odkazovat [Azure Disk Encryption pro Linux](azure-security-disk-encryption-linux.md) článku.__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Jak můžu začít používat Azure Disk Encryption?

Abyste mohli začít, přečtěte si [přehled Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Můžete šifrovat spouštěcích a datových svazků s Azure Disk Encryption?

Ano, můžete šifrovat spouštěcích a datových svazků pro Windows a virtuálních počítačů IaaS s Linuxem. Pro virtuální počítače s Windows nejde šifrovat data bez první šifrování svazku operačního systému. Pro virtuální počítače s Linuxem je možné šifrovat objem dat bez nutnosti nejprve šifrování svazku operačního systému. Zakázáním šifrování svazku operačního systému pro virtuální počítače IaaS s Linuxem není podporována po zašifrujete svazek s operačním systémem Linux.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok-capability"></a>Umožňuje Azure Disk Encryption přineste vlastní klíč (BYOK) funkce?

Ano, můžete zadat vlastní klíče šifrovací klíče. Tyto klíče jsou chráněné ve službě Azure Key Vault, což je úložiště klíčů pro Azure Disk Encryption. Další informace o klíčových šifrovací klíče podporují scénáře, naleznete v tématu [požadavky Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Můžete použít Azure vytvořen šifrovací klíč klíče?

Ano, můžete použít Azure Key Vault generovat šifrovací klíč klíče pro použití Azure disk encryption. Tyto klíče jsou chráněné ve službě Azure Key Vault, což je úložiště klíčů pro Azure Disk Encryption. Další informace o šifrovací klíč klíče najdete v tématu [požadavky Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Můžete použít místní službu správy klíčů nebo modulu hardwarového zabezpečení k ochraně šifrovacích klíčů?

Služba správy klíčů v místním nebo modulu hardwarového zabezpečení nelze použít k ochraně šifrovacích klíčů s Azure Disk Encryption. Služba Azure Key Vault můžete použít pouze na trhu při ochraně šifrovacích klíčů. Další informace o šifrování klíče klíčové scénáře, naleznete v tématu [požadavky Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Jaké jsou požadavky na konfiguraci Azure Disk Encryption?

Existují požadavky pro Azure Disk Encryption. Zobrazit [požadavky Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) článku vytvořte nový trezor klíčů nebo nastavení existujícího trezoru klíčů pro přístup k šifrování disku povolit šifrování a ochrana tajných kódů a klíčů. Další informace o šifrování klíče klíčové scénáře, naleznete v tématu [přehled Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Jaké jsou požadavky pro Azure Disk Encryption nakonfigurovat aplikaci služby Azure AD (předchozí verze)?

Existují požadavky pro Azure Disk Encryption. Zobrazit [požadavky Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) článek k vytvoření aplikace Azure Active Directory, vytvořte nový trezor klíčů, nebo nastavení existujícího trezoru klíčů pro přístup k šifrování disku povolit šifrování a zabezpečit tajné kódy a klíče. Další informace o šifrování klíče klíčové scénáře, naleznete v tématu [přehled Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Azure Disk Encryption používá aplikace Azure AD (předchozí verzi) stále podporovány?
Ano. Šifrování disku pomocí aplikace Azure AD je nadále podporován. Ale při šifrování nových virtuálních počítačů se doporučuje použít nové metody, nikoli pomocí aplikace Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Můžete migrovat virtuální počítače, které byly šifrované pomocí aplikace Azure AD k šifrování bez aplikace Azure AD?
V současné době není cesta přímé migrace pro počítače, které byly šifrované pomocí aplikace Azure AD k šifrování bez aplikace Azure AD. Kromě toho není také přímou cestu ze šifrování bez aplikace Azure AD pro šifrování s aplikací AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Jakou verzi prostředí Azure PowerShell podporuje Azure Disk Encryption?

Použijte nejnovější verzi sady SDK Azure Powershellu ke konfiguraci Azure Disk Encryption. Stáhněte si nejnovější verzi [prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption je *není* podporuje sadu Azure SDK verze 1.1.0.

> [!NOTE]
> Ve verzi preview rozšíření Linux Azure disk encryption je zastaralý. Podrobnosti najdete v tématu [rozšíření ukončení podpory pro Azure disk encryption ve verzi preview pro virtuální počítače IaaS s Linuxem](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Můžete použít Azure Disk Encryption na své vlastní image Linuxu?

Azure Disk Encryption nelze použít na vlastní image Linuxu. Jsou podporovány pouze Linuxové Image Galerie pro podporované distribuce vyznačeny dříve. Vlastní Image Linuxu se aktuálně nepodporují.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Můžete použít aktualizace pro Red Hat virtuálního počítače s Linuxem, který používá aktualizace yum

Ano, můžete provést aktualizaci nebo opravu virtuálního počítače s Red Hat Linux. Další informace najdete v tématu [použití aktualizací na šifrovaných IaaS Red Hat virtuálního počítače Azure s využitím yumu aktualizace](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Co je pracovní postup doporučené Azure disk encryption pro Linux?

Následující pracovní postup se doporučuje mít nejlepších výsledků v Linuxu:
* Spustit z image bez úprav uložených Galerie odpovídající požadované distribuce operačního systému a verze
* Proveďte zálohu připojené jednotky, na kterých budou zašifrovaná.  To umožňuje obnovení Pokud dojde k selhání, třeba pokud je virtuální počítač restartovat předtím, než byla dokončena šifrování.
* Šifrování (může trvat několik hodin nebo dokonce dnů v závislosti na vlastnosti virtuálního počítače a velikost jakýchkoliv připojených datových disků)
* Přizpůsobení a podle potřeby přidejte softwaru na bitovou kopii.

Pokud tento pracovní postup není možné, spoléhat na [šifrování služby Storage](../storage/common/storage-service-encryption.md) (SSE) vrstvy účtu úložiště platformy mohou být o alternativu k šifrování celého disku pomocí dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>Co je na disku "Svazek klíče Bek" nebo "/ mnt/azure_bek_disk"?

"Klíče Bek" pro Windows nebo "/ mnt/azure_bek_disk" pro Linux je svazek místní data, která bezpečně ukládá šifrovací klíče pro šifrované virtuální počítače Azure IaaS.
> [!NOTE]
> Odstranit nebo upravit obsah tohoto disku. Protože přítomnost klíče šifrování je potřeba pro všechny operace šifrování na virtuálním počítači IaaS není odpojit disk.

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Kam se můžu obrátit klást otázky nebo připomínky?

Můžete klást otázky nebo poskytnout zpětnou vazbu ohledně [fórum pro Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste se dozvěděli informace o nejčastější dotazy týkající se Azure Disk Encryption. Další informace o této službě a její možnosti najdete v následujících článcích:

- [Přehled Azure Disk Encryption](azure-security-disk-encryption-overview.md)
- [Použít šifrování disku ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure data šifrování v klidovém stavu](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
