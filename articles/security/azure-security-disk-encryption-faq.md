---
title: Časté otázky – Azure Disk Encryption pro virtuální počítače IaaS | Dokumentace Microsoftu
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Microsoft Azure Disk Encryption pro Windows a virtuálních počítačů IaaS s Linuxem.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 4f2a34e63a870814c8d2a3ffe24c60083c9d7bb2
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781110"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>Nejčastější dotazy k Azure Disk Encryption pro virtuální počítače s IaaS

Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) o Azure Disk Encryption pro Windows a virtuálních počítačů IaaS s Linuxem. Další informace o této službě najdete v tématu [Azure Disk Encryption pro Windows a virtuálních počítačů IaaS s Linuxem](azure-security-disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Kde je Azure Disk Encryption v obecné dostupnosti (GA)?

Azure Disk Encryption pro Windows a virtuálních počítačů IaaS s Linuxem jsou obecně dostupné ve všech veřejných oblastech Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Jaké uživatelské prostředí jsou k dispozici s Azure Disk Encryption?

Azure Disk Encryption GA podporuje šablony Azure Resource Manageru, Azure Powershellu a rozhraní příkazového řádku Azure. Různé uživatelské prostředí poskytují flexibilitu. Budete mít tři různé možnosti pro povolení šifrování disků pro virtuální počítače IaaS. Další informace o uživatelské prostředí a podrobné pokyny k dispozici v Azure Disk Encryption najdete v tématu [povolit Azure Disk Encryption pro Windows](azure-security-disk-encryption-windows.md) a [povolit Azure Disk Encryption pro Linux](azure-security-disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Kolik stojí Azure Disk Encryption

Neplatí žádné poplatky pro šifrování disků virtuálních počítačů pomocí Azure Disk Encryption, ale existují poplatky související s používáním služby Azure Key Vault. Další informace o Azure Key Vault náklady, najdete v článku [cenách služby Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) stránky.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Jak můžu začít používat Azure Disk Encryption?

Abyste mohli začít, přečtěte si [přehled Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Jaké velikosti a operační systémy pro virtuální počítače podporují Azure Disk Encryption?

V článku [Azure Disk Encryption předpoklady](azure-security-disk-encryption-prerequisites.md) jsou uvedené [velikosti virtuálních počítačů](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes) a [operační systémy virtuálních počítačů](azure-security-disk-encryption-prerequisites.md#supported-operating-systems) , které podporují Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Můžete šifrovat spouštěcích a datových svazků s Azure Disk Encryption?

Ano, můžete šifrovat spouštěcích a datových svazků pro Windows a virtuálních počítačů IaaS s Linuxem. Pro virtuální počítače s Windows nejde šifrovat data bez první šifrování svazku operačního systému. Pro virtuální počítače s Linuxem je možné šifrovat objem dat bez nutnosti nejprve šifrování svazku operačního systému. Zakázáním šifrování svazku operačního systému pro virtuální počítače IaaS s Linuxem není podporována po zašifrujete svazek s operačním systémem Linux. Pro virtuální počítače se systémem Linux v sadě škálování je možné šifrovat pouze datový svazek.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Můžu pomocí Azure Disk Encryption zašifrovat nepřipojený svazek?

Ne, Azure Disk Encryption pouze šifruje připojené svazky.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Návody přetočit tajné klíče a šifrovací klíče?

K otočení tajných kódů stačí zavolat stejný příkaz, který jste použili k povolení šifrování disku, a zadat jiný Key Vault. Pokud chcete otočit klíč šifrovacího klíče, zavolejte stejný příkaz, který jste použili k povolení šifrování disku, a určete nové šifrování klíče. 

>[!WARNING]
> - Pokud jste dřív používali [Azure Disk Encryption s aplikací Azure AD](azure-security-disk-encryption-prerequisites-aad.md) zadáním přihlašovacích údajů Azure AD k zašifrování tohoto virtuálního počítače, budete muset tuto možnost použít k ZAšifrování virtuálního počítače. V tomto šifrovaném virtuálním počítači nemůžete použít [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) , protože se nejedná o podporovaný scénář, což znamená, že pro tento zašifrovaný virtuální počítač se zatím nepodporuje přepínání z aplikace AAD.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Návody přidat nebo odebrat klíč šifrování klíče, pokud jsem ho původně nepoužíval?

Chcete-li přidat klíč šifrování klíče, zavolejte příkaz Enable znovu předáním parametru šifrovacího klíče klíče. Chcete-li odebrat klíč šifrovacího klíče, zavolejte příkaz Enable znovu bez parametru šifrovacího klíče klíče.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure Disk Encryption umožňuje přinést si vlastní klíč (BYOK)?

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
  V současné době není cesta přímé migrace pro počítače, které byly šifrované pomocí aplikace Azure AD k šifrování bez aplikace Azure AD. Kromě toho není přímou cestu ze šifrování bez aplikace Azure AD pro šifrování s aplikací AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Jakou verzi prostředí Azure PowerShell podporuje Azure Disk Encryption?

Použijte nejnovější verzi sady SDK Azure Powershellu ke konfiguraci Azure Disk Encryption. Stáhněte si nejnovější verzi [prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption je *není* podporuje sadu Azure SDK verze 1.1.0.

> [!NOTE]
> Rozšíření Microsoft. OSTCExtension. AzureDiskEncryptionForLinux ve verzi Preview pro Linux Azure Disk Encryption je zastaralé. Toto rozšíření bylo publikováno pro verzi Preview služby Azure Disk Encryption. V testovacím nebo produkčním nasazení byste neměli používat verzi Preview rozšíření.

> U scénářů nasazení, jako je například Azure Resource Manager (ARM), kde potřebujete nasadit rozšíření Azure Disk Encryption pro virtuální počítač se systémem Linux, aby bylo možné na svém VIRTUÁLNÍm počítači se systémem Linux IaaS povolit šifrování, je nutné použít rozšíření podporované provozním prostředím Azure Disk Encryption. Microsoft. Azure. Security. AzureDiskEncryptionForLinux ".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Můžete použít Azure Disk Encryption na své vlastní image Linuxu?

Azure Disk Encryption nelze použít na vlastní image Linuxu. Jsou podporovány pouze Linuxové Image Galerie pro podporované distribuce vyznačeny dříve. Vlastní Image Linuxu se aktuálně nepodporují.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Můžete použít aktualizace pro Red Hat virtuálního počítače s Linuxem, který používá aktualizace yum

Ano, na virtuálním počítači se systémem Red Hat Linux můžete provést aktualizaci Yumu.  Další informace najdete v tématu [Správa balíčků pro Linux za bránou firewall](azure-security-disk-encryption-tsg.md#linux-package-management-behind-a-firewall).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Co je pracovní postup doporučené Azure disk encryption pro Linux?

Následující pracovní postup se doporučuje mít nejlepších výsledků v Linuxu:
* Spustit z image bez úprav uložených Galerie odpovídající potřeby distribuce operačního systému a verze
* Proveďte zálohu připojené jednotky, na kterých budou zašifrovaná.  Zpět nahoru díky tomu pro obnovení Pokud dojde k selhání, třeba pokud je virtuální počítač restartovat předtím, než byla dokončena šifrování.
* Šifrování (může trvat několik hodin nebo dokonce dnů v závislosti na vlastnosti virtuálního počítače a velikost jakýchkoliv připojených datových disků)
* Přizpůsobení a podle potřeby přidejte softwaru na bitovou kopii.

Pokud tento pracovní postup není možné, spoléhat na [šifrování služby Storage](../storage/common/storage-service-encryption.md) (SSE) vrstvy účtu úložiště platformy mohou být o alternativu k šifrování celého disku pomocí dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Co je na disku "Svazek klíče Bek" nebo "/ mnt/azure_bek_disk"?

"Klíče Bek" pro Windows nebo "/ mnt/azure_bek_disk" pro Linux je svazek místní data, která bezpečně ukládá šifrovací klíče pro šifrované virtuální počítače Azure IaaS.
> [!NOTE]
> Odstranit nebo upravit obsah tohoto disku. Protože přítomnost klíče šifrování je potřeba pro všechny operace šifrování na virtuálním počítači IaaS není odpojit disk.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Jakou metodu šifrování Azure Disk Encryption používá?

V systému Windows používá ADE šifrovací metodu AES256 BitLocker (AES256WithDiffuser ve verzích starších než Windows Server 2012). V systému Linux používá ADE výchozí dešifrovací systém AES-XTS-plain64 s 256 bitovým hlavním klíčem svazku.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Je-li použít encryptformatall šifrovaně a určete všechny typy svazku vymaže jeho data na datové jednotky, které jsme už zašifrovali?
Ne, smazání dat z datové jednotky, které už jsou šifrované pomocí Azure Disk Encryption. Podobně jako u jak encryptformatall šifrovaně nebyl znovu zašifrovat jednotky operačního systému, ho nebude znovu zašifrovat jednotka už zašifrovaná data. Další informace najdete v tématu [encryptformatall šifrovaně kritéria](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).        

## <a name="is-xfs-filesystem-supported"></a>Je podporováno XFS systémem souborů?
XFS svazky se podporují jenom pro šifrování datových disků s EncryptFormatAll. Tím se svazek znovu naformátuje a mazání všech dat tam už existuje. Další informace najdete v tématu [encryptformatall šifrovaně kritéria](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Můžu zálohovat a obnovit zašifrovaný virtuální počítač? 

Azure Backup poskytuje mechanismus pro zálohování a obnovení šifrovaného virtuálního počítače v rámci stejného předplatného a oblasti.  Pokyny najdete v tématu [zálohování a obnovení šifrovaných virtuálních počítačů pomocí Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).  Obnovení šifrovaného virtuálního počítače do jiné oblasti se v tuto chvíli nepodporuje.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Kam se můžu obrátit klást otázky nebo připomínky?

Můžete klást otázky nebo poskytnout zpětnou vazbu ohledně [fórum pro Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste se dozvěděli informace o nejčastější dotazy týkající se Azure Disk Encryption. Další informace o této službě najdete v následujících článcích:

- [Přehled Azure Disk Encryption](azure-security-disk-encryption-overview.md)
- [Použít šifrování disku ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure data šifrování v klidovém stavu](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
