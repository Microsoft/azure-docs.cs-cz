---
title: Azure Storage šifrování dat v klidovém umístění | Microsoft Docs
description: Azure Storage chrání vaše data tím, že je před trvalým nasazením do cloudu automaticky šifruje. Všechna data v Azure Storage jsou šifrovaná a dešifrována transparentně pomocí 256 šifrování AES a jsou kompatibilní se standardem FIPS 140-2.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2abaa994bf507c3ffb65199af9ac609ece138ea4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673048"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage šifrování dat v klidovém umístění

Azure Storage automaticky šifruje vaše data při jejich uchování do cloudu. Šifrování chrání vaše data a usnadňuje splnění závazků týkajících se zabezpečení a dodržování předpisů v organizaci. Data v Azure Storage jsou šifrována a dešifrována transparentně pomocí 256 [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), je k dispozici jedna z nejúčinnějších šifrovacích šifr a je kompatibilní se standardem FIPS 140-2. Šifrování Azure Storage se v systému Windows podobá šifrování BitLockeru.

Pro všechny nové a existující účty úložiště je povolené šifrování Azure Storage a nedá se zakázat. Vzhledem k tomu, že vaše data jsou zabezpečená ve výchozím nastavení, nemusíte upravovat kód ani aplikace, abyste mohli využívat Azure Storage šifrování. 

Účty úložiště se šifrují bez ohledu na jejich úroveň výkonu (Standard nebo Premium) nebo modelu nasazení (Azure Resource Manager nebo Classic). Všechny možnosti redundance Azure Storage podporují šifrování a všechny kopie účtu úložiště jsou zašifrované. Všechny prostředky Azure Storage jsou zašifrované, včetně objektů blob, disků, souborů, front a tabulek. Všechna metadata objektů jsou také šifrována.

Šifrování nemá vliv na Azure Storage výkon. Azure Storage šifrování se neúčtují žádné další náklady.

Další informace o kryptografických modulech podkladových Azure Storage šifrování najdete v tématu [Cryptography API: Další generace](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="key-management"></a>Správa klíčů

Pro šifrování účtu úložiště můžete spoléhat na klíče spravované Microsoftem, nebo můžete spravovat šifrování pomocí vlastních klíčů společně s Azure Key Vault.

### <a name="microsoft-managed-keys"></a>Klíče spravované společností Microsoft

Ve výchozím nastavení váš účet úložiště používá šifrovací klíče spravované Microsoftem. Nastavení šifrování pro účet úložiště můžete zobrazit v části **šifrování** [Azure Portal](https://portal.azure.com), jak je znázorněno na následujícím obrázku.

![Zobrazit účet zašifrovaný pomocí klíčů spravovaných Microsoftem](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Klíče spravované zákazníkem

Azure Storage šifrování můžete spravovat pomocí klíčů spravovaných zákazníkem. Klíče spravované zákazníkem poskytují větší flexibilitu při vytváření, střídání, zakázání a odvolávání řízení přístupu. Můžete také auditovat šifrovací klíče používané k ochraně vašich dat. 

Pomocí Azure Key Vault můžete spravovat klíče a auditovat používání klíčů. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Účet úložiště a trezor klíčů musí být ve stejné oblasti, ale můžou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/key-vault-overview.md).

Pokud chcete odvolat přístup k klíčům spravovaným zákazníkem, přečtěte si téma [Azure Key Vault PowerShellu](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) a [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Odvolání přístupu efektivně zablokuje přístup ke všem datům v účtu úložiště, protože šifrovací klíč je nepřístupný Azure Storage.

Další informace o použití klíčů spravovaných zákazníkem s Azure Storage najdete v jednom z těchto článků:

- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage na webu Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage v PowerShellu](storage-encryption-keys-powershell.md)
- [Použití klíčů spravovaných zákazníkem s šifrováním Azure Storage z Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity prostředků Azure, což je funkce Azure Active Directory (Azure AD). Když přenášíte předplatné z jednoho adresáře služby Azure AD do jiného, spravované identity se neaktualizují a klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu **přenos předplatného mezi adresáři služby Azure AD** v [nejčastějších dotazech a známých potížích se spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

> [!NOTE]  
> Klíče spravované zákazníkem nejsou podporované pro [Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage šifrování oproti šifrování disku

Při šifrování Azure Storage jsou všechny účty Azure Storage a prostředky, které obsahují, zašifrované, včetně objektů blob stránky, které se nacházejí na discích virtuálních počítačů Azure. Kromě toho můžou být disky virtuálních počítačů Azure zašifrované pomocí [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption používá standardní [Nástroj BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) ve Windows a [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) v systému Linux, který poskytuje řešení šifrování založená na operačním systému, která jsou integrovaná s Azure Key Vault.

## <a name="next-steps"></a>Další kroky

- [Co je Azure Key Vault?](../../key-vault/key-vault-overview.md)
