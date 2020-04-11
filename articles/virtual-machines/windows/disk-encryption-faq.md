---
title: Nejčastější dotazy – šifrování disku Azure pro virtuální počítače s Windows
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se šifrování disku Microsoft Azure pro virtuální počítače Windows IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: 8ef791759a33d08aaff068b0dd2634e5f21bb1b6
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114996"
---
# <a name="azure-disk-encryption-for-windows-virtual-machines-faq"></a>Nejčastější dotazy k šifrování disku Azure pro virtuální počítače s Windows

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se šifrování disku Azure pro virtuální počítače s Windows. Další informace o této službě naleznete v [tématu Přehled šifrování disku Azure](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-windows-vms"></a>Co je šifrování disku Azure pro virtuální počítače s Windows?

Azure Disk Encryption pro virtuální počítače s Windows používá funkci Bitlocker systému Windows k zajištění úplného šifrování disku operačního systému a datových disků. Navíc poskytuje šifrování disku dočasného prostředku, pokud [je parametr VolumeType All](disk-encryption-windows.md#enable-encryption-on-a-newly-added-data-disk).  Obsah toky šifrované z virtuálního virtuálního soudu do back-endu úložiště. Tím poskytuje tešifrování mezi koncovými body s klíčem spravovaným zákazníkem.
 
Viz [Podporované virtuální počítače a operační systémy](disk-encryption-overview.md#supported-vms-and-operating-systems).
 
## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Kde je šifrování disku Azure v obecné dostupnosti (GA)?

Azure Disk Encryption je obecně k dispozici ve všech veřejných oblastech Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Jaká uživatelská prostředí jsou dostupná v Azure Disk Encryption?

Azure Disk Encryption GA podporuje šablony Azure Resource Manager, Azure PowerShell a Azure CLI. Různé uživatelské prostředí poskytují flexibilitu. Máte tři různé možnosti pro povolení šifrování disku pro vaše virtuální počítače. Další informace o uživatelském prostředí a podrobných pokynech dostupných v Azure Disk Encryption najdete v tématu [Azure Disk Encryption scenarios for Windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Kolik stojí Azure Disk Encryption?

Šifrování disků virtuálních počítačů pomocí Azure Disk Encryption se neplatí, ale s použitím služby Azure Key Vault jsou spojeny poplatky. Další informace o nákladech služby Azure Key Vault najdete na stránce [s cenami trezoru klíčů.](https://azure.microsoft.com/pricing/details/key-vault/)

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Jak můžu začít používat Azure Disk Encryption?

Chcete-li začít, přečtěte si [přehled šifrování disku Azure](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Jaké velikosti virtuálních počítačů a operační systémy podporují Azure Disk Encryption?

V článku [přehled azure diskové šifrování](disk-encryption-overview.md) uvádí [velikosti virtuálních počítačů](disk-encryption-overview.md#supported-vms) a operační systémy [virtuálních počítačů,](disk-encryption-overview.md#supported-operating-systems) které podporují azure disk encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Můžu šifrovat spouštěcí i datové svazky pomocí Azure Disk Encryption?

Můžete šifrovat spouštěcí i datové svazky, ale nemůžete šifrovat data bez předchozího šifrování svazku operačního systému.

Po zašifrování svazku operačního systému není zakázání šifrování na svazku operačního systému podporováno.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Můžu šifrovat nepřipojený svazek pomocí Azure Disk Encryption?

Ne, Azure Disk Encryption šifruje jenom připojené svazky.

## <a name="what-is-storage-server-side-encryption"></a>Co je šifrování na straně serveru úložiště?

Šifrování na straně serveru úložiště šifruje spravované disky Azure ve službě Azure Storage. Spravované disky jsou ve výchozím nastavení šifrovány pomocí šifrování na straně serveru pomocí klíče spravovaného platformou (k 10. červnu 2017). Šifrování spravovaných disků pomocí vlastních klíčů můžete spravovat zadáním klíče spravovaného zákazníkem. Další informace najdete [v tématu Šifrování na straně serveru disků spravovaných Azure](disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Jak se Azure Disk Encryption liší od šifrování na straně serveru úložiště pomocí klíče spravovaného zákazníkem a kdy mám použít každé řešení?

Azure Disk Encryption poskytuje end-to-end šifrování pro disk operačního systému, datové disky a dočasný disk prostředků s klíčem správou zákazníka.

- Pokud vaše požadavky zahrnují šifrování všech výše uvedených a koncových šifrování, použijte Azure Disk Encryption. 
- Pokud vaše požadavky zahrnují šifrování pouze dat v klidovém stavu pomocí klíče spravovaného zákazníkem, použijte [šifrování na straně serveru pomocí klíčů spravovaných zákazníkem](disk-encryption.md). Disk nelze šifrovat pomocí šifrování na webu Azure Disk i šifrování na straně serveru úložiště pomocí klíčů spravovaných zákazníkem.
- Pokud používáte scénář vyvolaný v [nepodporovaných scénářích pro systém Windows](disk-encryption-windows.md#unsupported-scenarios), zvažte [šifrování na straně serveru pomocí klíčů spravovaných zákazníkem](disk-encryption.md). 
- Pokud zásady vaší organizace umožňují šifrovat obsah v klidovém stavu pomocí klíče spravovaného Azure, není potřeba žádná akce – obsah je ve výchozím nastavení zašifrovaný. U spravovaných disků je obsah uvnitř úložiště ve výchozím nastavení šifrován šifrováním na straně serveru pomocí klíče spravovaného platformou. Klíč spravuje služba Azure Storage. 

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Jak lze otočit tajné kódy nebo šifrovací klíče?

Chcete-li střídat tajné klíče, stačí zavolat stejný příkaz, který jste původně použili k povolení šifrování disku, a zadat jiný trezor klíčů. Chcete-li otočit šifrovací klíč klíče, zavolejte stejný příkaz, který jste původně použili k povolení šifrování disku, a zadejte nové šifrování klíče. 

>[!WARNING]
> - Pokud jste dříve používali [Azure Disk Encryption s aplikací Azure AD](disk-encryption-windows-aad.md) zadáním přihlašovacích údajů Azure AD k šifrování tohoto virtuálního počítače, budete muset pokračovat v použití této možnosti k šifrování virtuálního počítače. Na tomto šifrovaném virtuálním počítači nelze použít Azure Disk Encryption, protože se nejedná o podporovaný scénář, což znamená, že přepnutí z aplikace AAD pro tento šifrovaný virtuální počítač ještě není podporované.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Jak přidám nebo odeberu šifrovací klíč, když jsem ho původně nepoužil?

Chcete-li přidat šifrovací klíč klíče, zavolejte příkaz enable znovu předat parametr klíče šifrování. Chcete-li odebrat šifrovací klíč klíče, zavolejte příkaz enable znovu bez parametru klíče šifrování.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Umožňuje azure diskencryption přinést vlastní klíč (BYOK)?

Ano, můžete zadat vlastní šifrovací klíče klíče. Tyto klíče jsou zabezpečené v Azure Key Vault, což je úložiště klíčů pro Azure Disk Encryption. Další informace o scénářích podpory klíčů najdete v [tématu Vytvoření a konfigurace trezoru klíčů pro azure disk encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Můžu použít šifrovací klíč vytvořený Azure?

Ano, azure key vault můžete použít ke generování klíče šifrovací klíč pro použití šifrování disku Azure. Tyto klíče jsou zabezpečené v Azure Key Vault, což je úložiště klíčů pro Azure Disk Encryption. Další informace o šifrovacím klíči klíče najdete v [tématu Vytvoření a konfigurace trezoru klíčů pro azure disk encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Můžu k zabezpečení šifrovacích klíčů použít místní službu správy klíčů nebo hardwarový server?

Nelze použít místní službu správy klíčů nebo modul hardwarového zabezpečení k zabezpečení šifrovacích klíčů pomocí azure diskového šifrování. Službu Azure Key Vault můžete použít pouze k zabezpečení šifrovacích klíčů. Další informace o scénářích podpory klíče pro šifrování klíčů naleznete v [tématu Vytvoření a konfigurace trezoru klíčů pro azure disk encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Jaké jsou předpoklady pro konfiguraci azure disk encryption?

Existují předpoklady pro azure disk šifrování. V článku [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md) vytvořte nový trezor klíčů nebo nastavte existující trezor klíčů pro přístup k šifrování disku, který umožní šifrování a ochranu tajných kódů a klíčů. Další informace o scénářích podpory klíče pro šifrování klíčů naleznete v [tématu Vytvoření a konfigurace trezoru klíčů pro azure disk encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Jaké jsou předpoklady pro konfiguraci azure šifrování disku s aplikací Azure AD (předchozí verze)?

Existují předpoklady pro azure disk šifrování. Podívejte se na Azure Disk Encryption s obsahem [Azure AD](disk-encryption-windows-aad.md) vytvořit aplikaci Azure Active Directory, vytvořit nový trezor klíčů nebo nastavit existující trezor klíčů pro přístup k šifrování disku pro povolení šifrování a ochranu tajných kódů a klíčů. Další informace o scénářích podpory klíče pro šifrování klíčů najdete v [tématu Vytvoření a konfigurace trezoru klíčů pro šifrování disku Azure pomocí Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Je azure disk šifrování pomocí aplikace Azure AD (předchozí verze) stále podporována?
Ano. Šifrování disku pomocí aplikace Azure AD je stále podporováno. Ale při šifrování nových virtuálních počítačů se doporučuje použít novou metodu, nikoli šifrování pomocí aplikace Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Můžu migrovat virtuální počítače, které byly zašifrované pomocí aplikace Azure AD na šifrování bez aplikace Azure AD?
  V současné době neexistuje cesta přímé migrace pro počítače, které byly šifrované pomocí aplikace Azure AD k šifrování bez aplikace Azure AD. Navíc neexistuje přímá cesta od šifrování bez aplikace Azure AD k šifrování pomocí aplikace AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Jakou verzi Azure PowerShellu podporuje Azure Disk Encryption?

Ke konfiguraci Azure Disk Encryption použijte nejnovější verzi sady Azure PowerShell SDK. Stáhněte si nejnovější verzi [Azure PowerShellu](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption *není* podporovaná sadou Azure SDK verze 1.1.0.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Co je disk "Bek Volume" nebo "/mnt/azure_bek_disk"?

"Svazek Bek" je místní datový svazek, který bezpečně ukládá šifrovací klíče pro šifrované virtuální počítače Azure.

> [!NOTE]
> Neodstraňujte ani neupravujte žádný obsah na tomto disku. Neodpojujte disk, protože přítomnost šifrovacího klíče je potřebná pro všechny operace šifrování na virtuálním počítači IaaS.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Jakou metodu šifrování používá šifrování disku Azure?

Azure Disk Encryption vybere metodu šifrování v nástroji BitLocker na základě verze systému Windows následujícím způsobem:

| Verze systému Windows                 | Version | Metoda šifrování        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 nebo vyšší  | >=1511 |XTS-AES 256 bitů           |
| Windows Server 2012, Windows 8, 8.1, 10 | < 1511 |AES 256 bitů *              |
| Systém Windows Server 2008R2            |        |AES 256 bit s difuzorem |

\*AES 256 bit s difuzorem není podporovánv systému Windows 2012 a novějších.

Chcete-li zjistit verzi operačního systému Windows, spusťte nástroj winver ve virtuálním počítači.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Pokud použiji EncryptFormatAll a zadám všechny typy svazků, vymaže data na datových jednotkách, které jsme již zašifrovali?
Ne, data se nesmažou z datových jednotek, které už jsou šifrované pomocí Azure Disk Encryption. Podobně jako EncryptFormatAll nebyl re-šifrovat jednotku operačního systému, nebude znovu šifrovat již šifrované datové jednotky. 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Můžu zálohovat a obnovovat šifrovaný virtuální účet? 

Azure Backup poskytuje mechanismus zálohování a obnovení šifrovaných virtuálních počítačích v rámci stejného předplatného a oblasti.  Pokyny najdete v [tématu Zálohování a obnovení šifrovaných virtuálních počítačů pomocí azure backup](../../backup/backup-azure-vms-encryption.md).  Obnovení šifrovaného virtuálního virtuálního aplikace do jiné oblasti není aktuálně podporováno.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Kde mohu jít klást otázky nebo poskytnout zpětnou vazbu?

Můžete klást otázky nebo poskytnout zpětnou vazbu na [fóru Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli více o nejčastějších otázkách týkajících se Azure Disk Encryption. Další informace o této službě naleznete v následujících článcích:

- [Přehled služby Azure Disk Encryption](disk-encryption-overview.md)
- [Použití šifrování disku v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Šifrování dat Azure v klidovém stavu](../../security/fundamentals/encryption-atrest.md)
