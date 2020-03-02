---
title: Nejčastější dotazy – Azure Disk Encryption pro virtuální počítače se systémem Linux
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Microsoft Azureho šifrování disků pro virtuální počítače s IaaS Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 45febcffed747f75b6c8b78c2e4b93a047f81b0a
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205870"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>Nejčastější dotazy k Azure Disk Encryption pro virtuální počítače s IaaS

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure Disk Encryption pro virtuální počítače se systémem Linux. Další informace o této službě najdete v tématu [přehled Azure Disk Encryption](disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Kde je Azure Disk Encryption v obecné dostupnosti (GA)?

Azure Disk Encryption pro virtuální počítače se systémem Linux je všeobecně dostupná ve všech veřejných oblastech Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Jaké uživatelské prostředí jsou k dispozici s Azure Disk Encryption?

Azure Disk Encryption GA podporuje šablony Azure Resource Manageru, Azure Powershellu a rozhraní příkazového řádku Azure. Různé uživatelské prostředí poskytují flexibilitu. Máte tři různé možnosti, jak povolit šifrování disku pro vaše virtuální počítače. Další informace o uživatelském prostředí a podrobné pokyny dostupné v Azure Disk Encryption najdete v tématu [Azure disk Encryptionch scénářích pro Linux](disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Kolik stojí Azure Disk Encryption

Za šifrování disků virtuálních počítačů s Azure Disk Encryptionem se neúčtují žádné poplatky, ale jsou spojené s používáním Azure Key Vault. Další informace o Azure Key Vaultch nákladech najdete na stránce s [cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) .

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Jak můžu začít používat Azure Disk Encryption?

Pokud chcete začít, přečtěte si [přehled Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Jaké velikosti a operační systémy pro virtuální počítače podporují Azure Disk Encryption?

V článku [přehled Azure Disk Encryption](disk-encryption-overview.md) jsou uvedeny [velikosti virtuálních počítačů](disk-encryption-overview.md#supported-vm-sizes) a [operační systémy virtuálních počítačů](disk-encryption-overview.md#supported-operating-systems) , které podporují Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Můžete šifrovat spouštěcích a datových svazků s Azure Disk Encryption?

Ano, můžete šifrovat spouštěcí i datové svazky, nebo můžete datový svazek šifrovat, aniž byste nejdřív museli zašifrovat svazek operačního systému. 

Po zašifrování svazku operačního systému se zakazování šifrování na svazku operačního systému nepodporuje. Pro virtuální počítače se systémem Linux v sadě škálování je možné šifrovat pouze datový svazek.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Můžu pomocí Azure Disk Encryption zašifrovat nepřipojený svazek?

Ne, Azure Disk Encryption pouze šifruje připojené svazky.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Návody přetočit tajné klíče a šifrovací klíče?

K otočení tajných kódů stačí zavolat stejný příkaz, který jste použili k povolení šifrování disku, a zadat jiný Key Vault. Pokud chcete otočit klíč šifrovacího klíče, zavolejte stejný příkaz, který jste použili k povolení šifrování disku, a určete nové šifrování klíče. 

>[!WARNING]
> - Pokud jste dřív používali [Azure Disk Encryption s aplikací Azure AD](disk-encryption-linux-aad.md) zadáním přihlašovacích údajů Azure AD k zašifrování tohoto virtuálního počítače, budete muset tuto možnost použít k ZAšifrování virtuálního počítače. V tomto šifrovaném virtuálním počítači nemůžete použít Azure Disk Encryption, protože se nejedná o podporovaný scénář, což znamená, že pro tento zašifrovaný virtuální počítač se zatím nepodporuje přepínání z aplikace AAD.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Návody přidat nebo odebrat klíč šifrování klíče, pokud jsem ho původně nepoužíval?

Chcete-li přidat klíč šifrování klíče, zavolejte příkaz Enable znovu předáním parametru šifrovacího klíče klíče. Chcete-li odebrat klíč šifrovacího klíče, zavolejte příkaz Enable znovu bez parametru šifrovacího klíče klíče.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure Disk Encryption umožňuje přinést si vlastní klíč (BYOK)?

Ano, můžete zadat vlastní klíče šifrovací klíče. Tyto klíče jsou chráněné ve službě Azure Key Vault, což je úložiště klíčů pro Azure Disk Encryption. Další informace o scénářích podpory klíčových šifrovacích klíčů najdete v článku [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Můžete použít Azure vytvořen šifrovací klíč klíče?

Ano, můžete použít Azure Key Vault generovat šifrovací klíč klíče pro použití Azure disk encryption. Tyto klíče jsou chráněné ve službě Azure Key Vault, což je úložiště klíčů pro Azure Disk Encryption. Další informace o klíčovém šifrovacím klíči najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Můžete použít místní službu správy klíčů nebo modulu hardwarového zabezpečení k ochraně šifrovacích klíčů?

Služba správy klíčů v místním nebo modulu hardwarového zabezpečení nelze použít k ochraně šifrovacích klíčů s Azure Disk Encryption. Služba Azure Key Vault můžete použít pouze na trhu při ochraně šifrovacích klíčů. Další informace o scénářích podpory klíčového šifrovacího klíče najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Jaké jsou požadavky na konfiguraci Azure Disk Encryption?

Existují požadavky pro Azure Disk Encryption. Další informace o vytvoření nového trezoru klíčů najdete v článku [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md) , nebo nastavení stávajícího trezoru klíčů pro přístup k šifrování disku, povolení šifrování a zabezpečení tajných kódů a klíčů. Další informace o scénářích podpory klíčového šifrovacího klíče najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Jaké jsou požadavky pro Azure Disk Encryption nakonfigurovat aplikaci služby Azure AD (předchozí verze)?

Existují požadavky pro Azure Disk Encryption. Pokud chcete vytvořit aplikaci Azure Active Directory, vytvořit nový trezor klíčů nebo nastavit existující Trezor klíčů pro přístup k šifrování disku, abyste mohli povolit šifrování a chránit tajné klíče a klíče, přečtěte si téma [Azure Disk Encryption s obsahem Azure AD](disk-encryption-linux-aad.md) . Další informace o scénářích podpory klíčového šifrovacího klíče najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Azure Disk Encryption používá aplikace Azure AD (předchozí verzi) stále podporovány?
Ano. Šifrování disku pomocí aplikace Azure AD je nadále podporován. Ale při šifrování nových virtuálních počítačů se doporučuje použít nové metody, nikoli pomocí aplikace Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Můžete migrovat virtuální počítače, které byly šifrované pomocí aplikace Azure AD k šifrování bez aplikace Azure AD?
  V současné době není cesta přímé migrace pro počítače, které byly šifrované pomocí aplikace Azure AD k šifrování bez aplikace Azure AD. Kromě toho není přímou cestu ze šifrování bez aplikace Azure AD pro šifrování s aplikací AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Jakou verzi prostředí Azure PowerShell podporuje Azure Disk Encryption?

Použijte nejnovější verzi sady SDK Azure Powershellu ke konfiguraci Azure Disk Encryption. Stáhněte si nejnovější verzi [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption Azure SDK verze *1.1.0 nepodporuje.*

> [!NOTE]
> Rozšíření Microsoft. OSTCExtension. AzureDiskEncryptionForLinux ve verzi Preview pro Linux Azure Disk Encryption je zastaralé. Toto rozšíření bylo publikováno pro verzi Preview služby Azure Disk Encryption. V testovacím nebo produkčním nasazení byste neměli používat verzi Preview rozšíření.

> U scénářů nasazení, jako je například Azure Resource Manager (ARM), kde potřebujete nasadit rozšíření Azure Disk Encryption pro virtuální počítač se systémem Linux, aby bylo možné na svém VIRTUÁLNÍm počítači se systémem Linux IaaS povolit šifrování, je nutné použít rozšíření podporované provozním prostředím Azure Disk Encryption. Microsoft. Azure. Security. AzureDiskEncryptionForLinux ".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Můžete použít Azure Disk Encryption na své vlastní image Linuxu?

Azure Disk Encryption nelze použít na vlastní image Linuxu. Jsou podporovány pouze Linuxové Image Galerie pro podporované distribuce vyznačeny dříve. Vlastní Image Linuxu se aktuálně nepodporují.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Můžete použít aktualizace pro Red Hat virtuálního počítače s Linuxem, který používá aktualizace yum

Ano, na virtuálním počítači se systémem Red Hat Linux můžete provést aktualizaci Yumu.  Další informace najdete v tématu [Azure Disk Encryption v izolované síti](disk-encryption-isolated-network.md).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Co je pracovní postup doporučené Azure disk encryption pro Linux?

Následující pracovní postup se doporučuje mít nejlepších výsledků v Linuxu:
* Spustit z image bez úprav uložených Galerie odpovídající potřeby distribuce operačního systému a verze
* Proveďte zálohu připojené jednotky, na kterých budou zašifrovaná.  Zpět nahoru díky tomu pro obnovení Pokud dojde k selhání, třeba pokud je virtuální počítač restartovat předtím, než byla dokončena šifrování.
* Šifrování (může trvat několik hodin nebo dokonce dnů v závislosti na vlastnosti virtuálního počítače a velikost jakýchkoliv připojených datových disků)
* Přizpůsobení a podle potřeby přidejte softwaru na bitovou kopii.

Pokud tento pracovní postup není možný, spoléhá na [šifrování služby Storage](../../storage/common/storage-service-encryption.md) (SSE) na úrovni účtu úložiště platformy může být alternativou k šifrování celého disku pomocí dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Co je na disku "Svazek klíče Bek" nebo "/ mnt/azure_bek_disk"?

"Klíče bek Volume" je místní datový svazek, který bezpečně ukládá šifrovací klíče pro šifrované virtuální počítače Azure.
> [!NOTE]
> Odstranit nebo upravit obsah tohoto disku. Protože přítomnost klíče šifrování je potřeba pro všechny operace šifrování na virtuálním počítači IaaS není odpojit disk.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Jakou metodu šifrování Azure Disk Encryption používá?

Azure Disk Encryption používá dešifrovací výchozí hodnotu AES-XTS-plain64 s 256 bitovým hlavním klíčem svazku.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Je-li použít encryptformatall šifrovaně a určete všechny typy svazku vymaže jeho data na datové jednotky, které jsme už zašifrovali?
Ne, smazání dat z datové jednotky, které už jsou šifrované pomocí Azure Disk Encryption. Podobně jako u jak encryptformatall šifrovaně nebyl znovu zašifrovat jednotky operačního systému, ho nebude znovu zašifrovat jednotka už zašifrovaná data. Další informace najdete v tématu [kritéria pro EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>Je podporováno XFS systémem souborů?
XFS svazky se podporují jenom pro šifrování datových disků s EncryptFormatAll. Tím se svazek znovu naformátuje a mazání všech dat tam už existuje. Další informace najdete v tématu [kritéria pro EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Můžu zálohovat a obnovit zašifrovaný virtuální počítač? 

Azure Backup poskytuje mechanismus pro zálohování a obnovení šifrovaného virtuálního počítače v rámci stejného předplatného a oblasti.  Pokyny najdete v tématu [zálohování a obnovení šifrovaných virtuálních počítačů pomocí Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).  Obnovení šifrovaného virtuálního počítače do jiné oblasti se v tuto chvíli nepodporuje.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Kam se můžu obrátit klást otázky nebo připomínky?

Můžete klást otázky nebo sdělit svůj názor na [Azure Disk Encryption Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli informace o nejčastější dotazy týkající se Azure Disk Encryption. Další informace o této službě najdete v následujících článcích:

- [Přehled služby Azure Disk Encryption](disk-encryption-overview.md)
- [Použít šifrování disku v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Šifrování dat Azure v klidovém umístění](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
