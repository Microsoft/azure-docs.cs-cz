---
title: Nejčastější dotazy – Azure Disk Encryption pro virtuální počítače s Windows
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Microsoft Azureho šifrování disku pro virtuální počítače s Windows IaaS.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: 6371a6bd0c4b8151c97a892ec70789429b4bda75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562193"
---
# <a name="azure-disk-encryption-for-windows-virtual-machines-faq"></a>Nejčastější dotazy k Azure Disk Encryption pro virtuální počítače s Windows

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure Disk Encryption virtuálních počítačů s Windows. Další informace o této službě najdete v tématu [přehled Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-windows-vms"></a>Co je Azure Disk Encryption pro virtuální počítače s Windows?

Azure Disk Encryption pro virtuální počítače s Windows používá funkci BitLocker systému Windows k zajištění úplného šifrování disku disku s operačním systémem a datových disků. Kromě toho poskytuje šifrování dočasného disku, pokud [je VolumeType parametr](disk-encryption-windows.md#enable-encryption-on-a-newly-added-data-disk).  Tok obsahu se zašifruje z virtuálního počítače do back-endu úložiště. A díky tomu poskytuje komplexní šifrování pomocí klíče spravovaného zákazníkem.
 
Viz [podporované virtuální počítače a operační systémy](disk-encryption-overview.md#supported-vms-and-operating-systems).
 
## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Kde je Azure Disk Encryption při obecné dostupnosti (GA)?

Azure Disk Encryption je všeobecně dostupná ve všech veřejných oblastech Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Jaké uživatelské prostředí jsou k dispozici v Azure Disk Encryption?

Azure Disk Encryption GA podporuje Azure Resource Manager šablony, Azure PowerShell a Azure CLI. Různé uživatelské prostředí nabízí flexibilitu. Máte tři různé možnosti, jak povolit šifrování disku pro vaše virtuální počítače. Další informace o uživatelském prostředí a podrobné pokyny dostupné v Azure Disk Encryption najdete v tématu [Azure disk Encryptionch scénářích pro Windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Kolik se Azure Disk Encryption náklady?

Za šifrování disků virtuálních počítačů s Azure Disk Encryptionem se neúčtují žádné poplatky, ale jsou spojené s používáním Azure Key Vault. Další informace o Azure Key Vaultch nákladech najdete na stránce s [cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) .

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Jak můžu začít používat Azure Disk Encryption?

Pokud chcete začít, přečtěte si [přehled Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Jaké velikosti a operační systémy pro virtuální počítače podporují Azure Disk Encryption?

V článku [přehled Azure Disk Encryption](disk-encryption-overview.md) jsou uvedeny [velikosti virtuálních počítačů](disk-encryption-overview.md#supported-vms) a [operační systémy virtuálních počítačů](disk-encryption-overview.md#supported-operating-systems) , které podporují Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Můžu pomocí Azure Disk Encryption šifrovat spouštěcí i datové svazky?

Můžete šifrovat spouštěcí i datové svazky, ale nemůžete data šifrovat, aniž byste nejdřív našifroval svazek operačního systému.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Můžu pomocí Azure Disk Encryption zašifrovat nepřipojený svazek?

Ne, Azure Disk Encryption pouze šifruje připojené svazky.

## <a name="what-is-storage-server-side-encryption"></a>Co je šifrování na straně serveru úložiště?

Šifrování na straně serveru šifruje spravované disky Azure v Azure Storage. Spravované disky se ve výchozím nastavení šifrují pomocí šifrování na straně serveru s klíčem spravovaným platformou (od 10. června 2017). Šifrování spravovaných disků můžete spravovat vlastními klíči zadáním klíče spravovaného zákazníkem. Další informace najdete v tématu [šifrování Azure Managed disks na straně serveru](../disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Jak se Azure Disk Encryption liší od šifrování na straně serveru pomocí klíče spravovaného zákazníkem a kdy použít každé řešení?

Azure Disk Encryption poskytuje komplexní šifrování pro disk s operačním systémem, datové disky a dočasný disk s klíčem spravovaným zákazníkem.

- Pokud vaše požadavky zahrnují šifrování všech výše uvedených a kompletních šifrování, použijte Azure Disk Encryption. 
- Pokud vaše požadavky zahrnují šifrování jenom uložených dat s klíčem spravovaným zákazníkem, pak použijte [šifrování na straně serveru pomocí klíčů spravovaných zákazníkem](../disk-encryption.md). Pomocí klíčů spravovaných zákazníkem nelze zašifrovat disk s použitím Azure Disk Encryption i úložiště na straně serveru.
- Pokud používáte scénář s názvem v [nepodporovaných scénářích pro Windows](disk-encryption-windows.md#unsupported-scenarios), zvažte použití [šifrování na straně serveru pomocí klíčů spravovaných zákazníkem](../disk-encryption.md). 
- Pokud zásady vaší organizace umožňují šifrování obsahu v klidovém stavu pomocí klíče spravovaného službou Azure, není nutné provádět žádnou akci – ve výchozím nastavení je obsah zašifrovaný. V případě spravovaných disků je obsah uvnitř úložiště ve výchozím nastavení zašifrovaný pomocí šifrování na straně serveru s klíčem spravovaným platformou. Klíč spravuje služba Azure Storage. 

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Návody přetočit tajné klíče a šifrovací klíče?

K otočení tajných kódů stačí zavolat stejný příkaz, který jste použili k povolení šifrování disku, a zadat jiný Key Vault. Pokud chcete otočit klíč šifrovacího klíče, zavolejte stejný příkaz, který jste použili k povolení šifrování disku, a určete nové šifrování klíče. 

>[!WARNING]
> - Pokud jste dřív používali [Azure Disk Encryption s aplikací Azure AD](disk-encryption-windows-aad.md) zadáním přihlašovacích údajů Azure AD k zašifrování tohoto virtuálního počítače, budete muset tuto možnost použít k ZAšifrování virtuálního počítače. V tomto šifrovaném virtuálním počítači nemůžete použít Azure Disk Encryption, protože se nejedná o podporovaný scénář, což znamená, že pro tento zašifrovaný virtuální počítač se zatím nepodporuje přepínání z aplikace AAD.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Návody přidat nebo odebrat klíč šifrování klíče, pokud jsem ho původně nepoužíval?

Chcete-li přidat klíč šifrování klíče, zavolejte příkaz Enable znovu předáním parametru šifrovacího klíče klíče. Chcete-li odebrat klíč šifrovacího klíče, zavolejte příkaz Enable znovu bez parametru šifrovacího klíče klíče.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Umožňuje vám Azure Disk Encryption použít vlastní klíč (BYOK)?

Ano, můžete dodat vlastní klíče šifrování klíčů. Tyto klíče jsou chráněny v Azure Key Vault, což je úložiště klíčů pro Azure Disk Encryption. Další informace o scénářích podpory klíčových šifrovacích klíčů najdete v článku [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Můžu použít šifrovací klíč klíče vytvořený v Azure?

Ano, pomocí Azure Key Vault můžete vygenerovat klíč šifrování klíče pro použití Azure Disk Encryption. Tyto klíče jsou chráněny v Azure Key Vault, což je úložiště klíčů pro Azure Disk Encryption. Další informace o klíčovém šifrovacím klíči najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Můžu k ochraně šifrovacích klíčů použít místní službu správy klíčů nebo modul HARDWAROVÉho zabezpečení?

K ochraně šifrovacích klíčů pomocí Azure Disk Encryption nemůžete použít místní službu správy klíčů ani modul HARDWAROVÉho zabezpečení. K ochraně šifrovacích klíčů můžete použít jenom službu Azure Key Vault. Další informace o scénářích podpory klíčového šifrovacího klíče najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Jaké jsou požadavky pro konfiguraci Azure Disk Encryption?

Existují předpoklady pro Azure Disk Encryption. Další informace o vytvoření nového trezoru klíčů najdete v článku [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md) , nebo nastavení stávajícího trezoru klíčů pro přístup k šifrování disku, povolení šifrování a zabezpečení tajných kódů a klíčů. Další informace o scénářích podpory klíčového šifrovacího klíče najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Jaké jsou požadavky na konfiguraci Azure Disk Encryption pomocí aplikace Azure AD (předchozí verze)?

Existují předpoklady pro Azure Disk Encryption. Pokud chcete vytvořit aplikaci Azure Active Directory, vytvořit nový trezor klíčů nebo nastavit existující Trezor klíčů pro přístup k šifrování disku, abyste mohli povolit šifrování a chránit tajné klíče a klíče, přečtěte si téma [Azure Disk Encryption s obsahem Azure AD](disk-encryption-windows-aad.md) . Další informace o scénářích podpory klíčového šifrovacího klíče najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Je Azure Disk Encryption používání aplikace Azure AD (předchozí verze) stále podporovaná?
Ano. Šifrování disku pomocí aplikace Azure AD se pořád podporuje. Při šifrování nových virtuálních počítačů se ale doporučuje použít místo šifrování v aplikaci Azure AD novou metodu. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Můžu migrovat virtuální počítače, které byly zašifrované pomocí aplikace Azure AD pro šifrování bez aplikace Azure AD?
  V současné době není k dispozici přímá migrační cesta pro počítače, které byly zašifrované pomocí aplikace Azure AD pro šifrování bez aplikace Azure AD. Kromě toho neexistuje Přímá cesta ze šifrování bez aplikace služby Azure AD pro šifrování pomocí aplikace AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Jakou verzi Azure PowerShell Azure Disk Encryption podporuje?

K nakonfigurování Azure Disk Encryption použijte nejnovější verzi sady Azure PowerShell SDK. Stáhněte si nejnovější verzi [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption Azure SDK verze *1.1.0 nepodporuje.*

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Jaký je disk "klíče bek Volume" nebo "/mnt/azure_bek_disk"?

"Klíče bek Volume" je místní datový svazek, který bezpečně ukládá šifrovací klíče pro šifrované virtuální počítače Azure.

> [!NOTE]
> Neodstraňujte ani neupravujte žádný obsah tohoto disku. Tento disk neodstraňujte, protože přítomnost šifrovacího klíče je potřebná pro jakékoli operace šifrování na virtuálním počítači s IaaS.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Jakou metodu šifrování používá Azure Disk Encryption?

Azure Disk Encryption vybere metodu šifrování v BitLockeru na základě verze Windows následujícím způsobem:

| Verze systému Windows                 | Verze | Metoda šifrování        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 nebo novější  | >= 1511 |XTS-AES 256 – bit           |
| Windows Server 2012, Windows 8, 8,1, 10 | < 1511 |AES 256 bit *              |
| 2008 R2 Windows serveru            |        |AES 256 bit s difuzorem |

\* V systému Windows 2012 a novějších není podporován bit AES 256 s difuzorem.

Pokud chcete zjistit verzi operačního systému Windows, spusťte na virtuálním počítači nástroj winver.

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Můžu zálohovat a obnovit zašifrovaný virtuální počítač? 

Azure Backup poskytuje mechanismus pro zálohování a obnovení šifrovaného virtuálního počítače v rámci stejného předplatného a oblasti.  Pokyny najdete v tématu [zálohování a obnovení šifrovaných virtuálních počítačů pomocí Azure Backup](../../backup/backup-azure-vms-encryption.md).  Obnovení šifrovaného virtuálního počítače do jiné oblasti se v tuto chvíli nepodporuje.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Kde můžu přejít na dotazy nebo poskytnout zpětnou vazbu?

Můžete klást otázky nebo poskytovat zpětnou vazbu na [stránce s dotazem&Microsoft Q pro Azure Disk Encryption](/answers/topics/azure-disk-encryption.html).

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli víc o nejčastějších dotazech souvisejících s Azure Disk Encryption. Další informace o této službě najdete v následujících článcích:

- [Přehled služby Azure Disk Encryption](disk-encryption-overview.md)
- [Použít šifrování disku v Azure Security Center](../../security-center/asset-inventory.md)
- [Šifrování neaktivních uložených dat v Azure](../../security/fundamentals/encryption-atrest.md)