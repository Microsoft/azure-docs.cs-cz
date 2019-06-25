---
title: Šifrování služby Azure Storage pro neaktivní uložená data | Dokumentace Microsoftu
description: Úložiště Azure chrání vaše data tím, že automaticky šifruje před uložením do cloudu. Všechna data ve službě Azure Storage je šifrovaný a dešifrovat transparentně pomocí 256bitového šifrování AES a je kompatibilní s FIPS 140-2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1e95adbd1a564fb34d3f0506ac1cc25bc5a63c62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65790055"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Šifrování služby Azure Storage pro neaktivní uložená data

Azure Storage automaticky šifruje vaše data při zachování do cloudu. Šifrování chrání vaše data a také jí pomohou zvládnout organizační závazky zabezpečení a dodržování předpisů. Data ve službě Azure Storage je šifrovaný a dešifrovat transparentně pomocí 256bitového [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jedna z nejsilnějších bloku šifer k dispozici a je kompatibilní s FIPS 140-2. Šifrování Azure Storage je podobný šifrování nástroje BitLocker v Windows.

Šifrování Azure Storage je povolená pro všechny účty nová a existující úložiště a nejde zakázat. Vzhledem k tomu, že ve výchozím nastavení jsou vaše data zabezpečená, není nutné upravovat kód vaší aplikace a chcete využít výhod šifrování služby Azure Storage. 

Účty úložiště se zašifrují bez ohledu na jejich úroveň výkonu (standard nebo premium) nebo model nasazení (Azure Resource Manager nebo classic). Všechny možnosti redundance Azure Storage podporují šifrování, a zašifrují veškeré kopie tohoto účtu úložiště. Všechny prostředky služby Azure Storage jsou zašifrovaná, včetně objektů BLOB, disky, soubory, fronty a tabulky. Všechna metadata objektu se šifrují taky.

Šifrování nemá vliv na výkon služby Azure Storage. Se neúčtují žádné další poplatky pro šifrování Azure Storage.

Další informace o kryptografických modulů základní šifrovací služby Azure Storage najdete v tématu [rozhraní API kryptografických služeb: Další generace](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="key-management"></a>Správa klíčů

Můžete se spolehnout na spravovaných microsoftem klíče pro šifrování vašeho účtu úložiště, nebo je můžete spravovat šifrování vlastní klíče, společně s Azure Key Vault.

### <a name="microsoft-managed-keys"></a>Klíče spravované zákazníkem společnosti Microsoft

Ve výchozím nastavení používá řízených šifrovací klíče účtu úložiště. Zobrazí se nastavení šifrování pro účet úložiště v **šifrování** část [webu Azure portal](https://portal.azure.com), jak je znázorněno na následujícím obrázku.

![Zobrazit účet zašifrovaný pomocí klíčů spravovaných microsoftem](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Klíče spravované zákazníkem

Správa šifrování služby Azure Storage pomocí klíčů spravovaných zákazníkem. Klíče spravované zákazníkem získáte větší flexibilitu při vytvoření, otáčení, zakázat a odvolat přístup k ovládacím prvkům. Šifrovací klíče použité k ochraně vašich dat můžete také auditovat. 

Spravovat klíče a auditovat využití klíčů pomocí Azure Key Vault. Můžete vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo rozhraní API služby Azure Key Vault můžete použít ke generování klíčů. Účet úložiště a trezoru klíčů musí být ve stejné oblasti, ale mohou být v různých předplatných. Další informace o službě Azure Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/key-vault-overview.md).

Odvolání přístupu k klíčů spravovaných zákazníkem najdete v článku [Azure Key Vault prostředí PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) a [příkazového řádku Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). Odvolání přístupu efektivně blokuje přístup ke všem datům v účtu úložiště, jako šifrovací klíč je pravděpodobně nepřístupný pomocí služby Azure Storage.

Zjistěte, jak používat klíče spravované zákazníkem služby Azure Storage, najdete v některém z těchto článků:

- [Konfigurovat klíče spravované zákazníkem pro šifrování Azure Storage na webu Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurovat klíče spravované zákazníkem pro šifrování Azure Storage z prostředí PowerShell](storage-encryption-keys-powershell.md)
- [Používat klíče spravované zákazníkem pomocí šifrování úložiště Azure z příkazového řádku Azure](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Klíče spravované zákazníkem závisí na spravovaných identit pro prostředky Azure, která je součástí Azure Active Directory (Azure AD). Při přenosu předplatného z jednoho adresáře služby Azure AD na jiný, spravované identity nejsou aktualizovány a klíče spravované zákazníkem už nemusí fungovat. Další informace najdete v tématu **převodu předplatného mezi adresáři Azure AD** v [nejčastější dotazy a známé problémy s spravovaných identit pro prostředky Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

> [!NOTE]  
> Klíče spravované zákazníkem nejsou podporovány pro [Azure managed disks](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Šifrování služby Azure Storage a šifrování disku

Pomocí šifrování služby Azure Storage, všechny účty úložiště Azure a prostředky, které obsahují se šifrují, včetně objektů BLOB stránky, které zálohují disky virtuálních počítačů Azure. Kromě toho může šifrovat disky virtuálních počítačů Azure s [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption používá standardní [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) na Windows a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) na platformě Linux k zajištění řešení šifrování podle operačního systému, které jsou integrované s Azure Key Vault.

## <a name="next-steps"></a>Další postup

- [Co je Azure Key Vault?](../../key-vault/key-vault-overview.md)
