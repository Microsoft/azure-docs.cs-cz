---
title: Správa šifrování účtu pomocí klíčů spravovaných zákazníky pomocí služby Azure Key Vault
titleSuffix: Azure Storage
description: K ochraně dat v účtu úložiště můžete použít vlastní šifrovací klíč. Když zadáte klíč spravovaný zákazníkem, tento klíč se používá k ochraně a řízení přístupu ke klíči, který šifruje vaše data. Klíče spravované zákazníkem nabízejí větší flexibilitu při správě řízení přístupu.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 6a3447a88aea1087c7ec327a956044ea94e793e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79410035"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Správa šifrování Azure Storage pomocí azure key vaultu pomocí klíčů Azure Key Vault

K ochraně dat v účtu úložiště můžete použít vlastní šifrovací klíč. Když zadáte klíč spravovaný zákazníkem, tento klíč se používá k ochraně a řízení přístupu ke klíči, který šifruje vaše data. Klíče spravované zákazníkem nabízejí větší flexibilitu při správě řízení přístupu.

K ukládání klíčů spravovaných zákazníky je nutné použít azure key vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít Azure Key Vault API ke generování klíčů. Účet úložiště a trezor klíčů musí být ve stejné oblasti a ve stejném tenantovi Služby Azure Active Directory (Azure AD), ale mohou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault?](../../key-vault/key-vault-overview.md).

## <a name="about-customer-managed-keys"></a>Klíče spravované zákazníkem

Následující diagram znázorňuje, jak Azure Storage používá Azure Active Directory a Azure Key Vault k vytváření požadavků pomocí klíče spravovaného zákazníkem:

![Diagram znázorňující, jak fungují klíče spravované zákazníkem v Azure Storage](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

Následující seznam vysvětluje očíslované kroky v diagramu:

1. Správce úložiště klíčů Azure uděluje oprávnění k šifrovacím klíčům spravované identity, která je přidružená k účtu úložiště.
2. Správce Azure Storage konfiguruje šifrování pomocí klíče spravovaného zákazníkem pro účet úložiště.
3. Azure Storage používá spravovanou identitu, která je přidružená k účtu úložiště, k ověření přístupu k Azure Key Vault prostřednictvím Služby Azure Active Directory.
4. Azure Storage zabalí šifrovací klíč účtu pomocí klíče zákazníka v Azure Key Vault.
5. Pro operace pro čtení a zápis Azure Storage odesílá požadavky do služby Azure Key Vault rozbalit šifrovací klíč účtu k provádění operací šifrování a dešifrování.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Vytvoření účtu, který podporuje klíče spravované zákazníkem pro fronty a tabulky

Data uložená ve službách Fronty a Tabulka nejsou automaticky chráněna klíčem spravovaným zákazníkem, pokud jsou pro účet úložiště povoleny klíče spravované zákazníkem. Volitelně můžete nakonfigurovat tyto služby v době, kdy vytvoříte účet úložiště, které mají být zahrnuty do této ochrany.

Další informace o tom, jak vytvořit účet úložiště, který podporuje klíče spravované zákazníkem pro fronty a tabulky, naleznete [v tématu Vytvoření účtu, který podporuje klíče spravované zákazníkem pro tabulky a fronty](account-encryption-key-create.md).

Data ve službách objektů Blob a soubor je vždy chráněna klíči spravovanými zákazníky, když jsou klíče spravované zákazníkem nakonfigurovány pro účet úložiště.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Povolení klíčů spravovaných zákazníkem pro účet úložiště

Klíče spravované zákazníkem lze povolit pouze u existujících účtů úložiště. Trezor klíčů musí být zřízen zásadami přístupu, které udělují oprávnění klíče spravované identitě, která je přidružena k účtu úložiště. Spravovaná identita je k dispozici pouze po vytvoření účtu úložiště.

Když nakonfigurujete klíč spravovaný zákazníkem, Azure Storage zabalí kořenový klíč šifrování dat pro účet s klíčem spravovaným zákazníkem v přidruženém trezoru klíčů. Povolení klíčů spravovaných zákazníkem nemá vliv na výkon a projeví se okamžitě.

Když upravíte klíč používaný pro šifrování Azure Storage povolením nebo zakázáním klíčů spravovaných zákazníkem, aktualizací verze klíče nebo určením jiného klíče, pak se změní šifrování kořenového klíče, ale data ve vašem účtu Azure Storage nechybě je třeba znovu zašifrovat.

Když povolíte nebo zakážete klíče spravované zákazníkem nebo když upravíte klíč nebo verzi klíče, změní se ochrana kořenového šifrovacího klíče, ale data ve vašem účtu Služby Azure Storage nemusí být znovu šifrována.

Informace o tom, jak používat klíče spravované zákazníky s šifrováním Azure Key Vault pro Azure Storage, najdete v jednom z těchto článků:

- [Konfigurace klíčů spravovaných zákazníkem pomocí trezoru klíčů pro šifrování Azure Storage z webu Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurace klíčů spravovaných zákazníkem pomocí trezoru klíčů pro šifrování Azure Storage z PowerShellu](storage-encryption-keys-powershell.md)
- [Konfigurace klíčů spravovaných zákazníkem pomocí trezoru klíčů pro šifrování Azure Storage z rozhraní příkazového příkazového příkazu Azure](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity pro prostředky Azure, což je funkce Azure AD. Spravované identity aktuálně nepodporují scénáře mezi adresáři. Když nakonfigurujete klíče spravované zákazníky na webu Azure Portal, spravovaná identita se automaticky přiřadí k vašemu účtu úložiště pod kryty. Pokud následně přesunete předplatné, skupinu prostředků nebo účet úložiště z jednoho adresáře Azure AD do jiného, spravovaná identita přidružená k účtu úložiště se nepřenese do nového klienta, takže klíče spravované zákazníkem už nemusí fungovat. Další informace najdete **v tématu Přenos předplatného mezi adresáři Azure AD** v [častých dotazech a známými problémy se spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ukládání klíčů spravovaných zákazníky v trezoru klíčů Azure

Chcete-li povolit klíče spravované zákazníkem na účtu úložiště, musíte použít Trezor klíčů Azure k uložení klíčů. Je nutné povolit vlastnosti **Obnovitelné odstranění** a **Nevyčistit** v trezoru klíčů.

Šifrováním úložiště Azure jsou podporované jenom klíče RSA velikosti 2048. Další informace o klíčích najdete v **tématu Klíče trezoru klíčů** v [tématu O klíčích, tajných klíčích a certifikátech trezoru klíčů Azure](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="rotate-customer-managed-keys"></a>Otočení klíčů spravovaných zákazníkem

Klíč spravovaný zákazníkem můžete v trezoru klíčů Azure v souladu s vašimi zásadami dodržování předpisů otočit. Při otočení klíče je nutné aktualizovat účet úložiště tak, aby používal identifikátor URI nové verze klíče. Informace o tom, jak aktualizovat účet úložiště tak, aby používal novou verzi klíče na webu Azure Portal, najdete v části **Aktualizace verze klíče** klíče v [tématu Konfigurace klíčů spravovaných zákazníky pro Azure Storage pomocí portálu Azure](storage-encryption-keys-portal.md).

Otočením klíče neaktivujete opětovné šifrování dat v účtu úložiště. Neexistuje žádná další akce vyžaduje od uživatele.

## <a name="revoke-access-to-customer-managed-keys"></a>Odvolání přístupu ke klíčům spravovaným zákazníkem

Přístup účtu úložiště ke klíči spravovanému zákazníkem můžete kdykoli odvolat. Po odvolání přístupu ke klíčům spravovaným zákazníkem nebo po zakázání nebo odstranění klíče nemohou klienti volat operace, které přičtou z objektu blob nebo jeho metadata nebo do něj zapisují. Pokusy o volání některé z následujících operací se nezdaří s kódem chyby 403 (Zakázáno) pro všechny uživatele:

- [Seznam objektů BLOB](/rest/api/storageservices/list-blobs), `include=metadata` při volání s parametrem na požadavku URI
- [Získat blob](/rest/api/storageservices/get-blob)
- [Získání vlastností objektu blob](/rest/api/storageservices/get-blob-properties)
- [Získání metadat objektů blob](/rest/api/storageservices/get-blob-metadata)
- [Nastavení metadat objektu blob](/rest/api/storageservices/set-blob-metadata)
- [Snímek objektu blob](/rest/api/storageservices/snapshot-blob), při volání s hlavičkou `x-ms-meta-name` požadavku
- [Zkopírování objektu blob](/rest/api/storageservices/copy-blob)
- [Kopírovat objekt blob z adresy URL](/rest/api/storageservices/copy-blob-from-url)
- [Nastavení úrovně objektu blob](/rest/api/storageservices/set-blob-tier)
- [Umístit blok](/rest/api/storageservices/put-block)
- [Umístit blok z adresy URL](/rest/api/storageservices/put-block-from-url)
- [Připojit blok](/rest/api/storageservices/append-block)
- [Připojit blok od adresy URL](/rest/api/storageservices/append-block-from-url)
- [Vložení objektu blob](/rest/api/storageservices/put-blob)
- [Umístit stránku](/rest/api/storageservices/put-page)
- [Umístit stránku z adresy URL](/rest/api/storageservices/put-page-from-url)
- [Objekt blob přírůstkové kopie](/rest/api/storageservices/incremental-copy-blob)

Chcete-li tyto operace znovu volat, obnovte přístup ke klíči spravovanému zákazníkem.

Všechny datové operace, které nejsou uvedeny v této části může pokračovat po klientspravované klíče jsou odvolány nebo klíč je zakázán nebo odstraněn.

Chcete-li odvolat přístup ke klíčům spravovaným zákazníkem, použijte [PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) nebo [Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Klíče spravované zákazníkem pro spravované disky Azure

Klíče spravované zákazníkem jsou taky dostupné pro správu šifrování spravovaných disků Azure. Klíče spravované zákazníkem se chovají jinak pro spravované disky než pro prostředky Azure Storage. Další informace najdete v tématu Šifrování na [straně serveru spravovaných disků Azure](../../virtual-machines/windows/disk-encryption.md) pro šifrování [spravovaných disků Azure](../../virtual-machines/linux/disk-encryption.md) pro Linux na straně Windows nebo Server.

## <a name="next-steps"></a>Další kroky

- [Konfigurace klíčů spravovaných zákazníkem pomocí trezoru klíčů pro šifrování Azure Storage z webu Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurace klíčů spravovaných zákazníkem pomocí trezoru klíčů pro šifrování Azure Storage z PowerShellu](storage-encryption-keys-powershell.md)
- [Konfigurace klíčů spravovaných zákazníkem pomocí trezoru klíčů pro šifrování Azure Storage z rozhraní příkazového příkazového příkazu Azure](storage-encryption-keys-cli.md)
- [Šifrování Azure Storage pro data v klidovém stavu](storage-service-encryption.md)
