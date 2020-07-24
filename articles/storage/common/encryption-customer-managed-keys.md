---
title: Použití klíčů spravovaných zákazníkem se Azure Key Vault ke správě šifrování účtů
titleSuffix: Azure Storage
description: K ochraně dat ve vašem účtu úložiště můžete použít vlastní šifrovací klíč. Když zadáte klíč spravovaný zákazníkem, použije se tento klíč k ochraně a řízení přístupu ke klíči, který vaše data šifruje. Klíče spravované zákazníkem nabízejí větší flexibilitu při správě řízení přístupu.
services: storage
author: tamram
ms.service: storage
ms.date: 07/20/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d53818c91d32bc7435d1328c2ae73a8eb3172cd4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029786"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Použití klíčů spravovaných zákazníkem se Azure Key Vault ke správě šifrování Azure Storage

K ochraně dat ve vašem účtu úložiště můžete použít vlastní šifrovací klíč. Když zadáte klíč spravovaný zákazníkem, použije se tento klíč k ochraně a řízení přístupu ke klíči, který vaše data šifruje. Klíče spravované zákazníkem nabízejí větší flexibilitu při správě řízení přístupu.

K ukládání klíčů spravovaných zákazníkem je nutné použít Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Účet úložiště a trezor klíčů musí být ve stejné oblasti a v rámci stejného Azure Active Directory klienta (Azure AD), ale můžou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/general/overview.md).

## <a name="about-customer-managed-keys"></a>O klíčích spravovaných zákazníkem

Následující diagram ukazuje, jak Azure Storage používá Azure Active Directory a Azure Key Vault k vytváření požadavků pomocí klíče spravovaného zákazníkem:

![Diagram znázorňující, jak jsou klíče spravované zákazníkem v Azure Storage fungovat](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

V následujícím seznamu najdete popis očíslovaných kroků v diagramu:

1. Správce Azure Key Vault uděluje oprávnění šifrovacím klíčům ke spravované identitě, která je přidružená k účtu úložiště.
2. Správce Azure Storage nakonfiguruje šifrování pomocí klíče spravovaného zákazníkem pro účet úložiště.
3. Azure Storage používá spravovanou identitu, která je přidružená k účtu úložiště pro ověřování přístupu k Azure Key Vault prostřednictvím Azure Active Directory.
4. Azure Storage zabalí šifrovací klíč účtu s klíčem zákazníka v Azure Key Vault.
5. Pro operace čtení a zápisu Azure Storage posílá žádosti, aby Azure Key Vault rozbalí šifrovací klíč účtu, aby prováděl operace šifrování a dešifrování.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Vytvoření účtu, který podporuje klíče spravované zákazníkem pro fronty a tabulky

Data uložená ve frontě a službě Table Services automaticky chrání klíč spravovaný zákazníkem, pokud jsou pro účet úložiště povoleny klíče spravované zákazníkem. Volitelně můžete tyto služby nakonfigurovat v okamžiku, kdy vytvoříte účet úložiště, který chcete zahrnout do této ochrany.

Další informace o tom, jak vytvořit účet úložiště, který podporuje klíče spravované zákazníky pro fronty a tabulky, najdete v tématu [Vytvoření účtu, který podporuje klíče spravované zákazníkem pro tabulky a fronty](account-encryption-key-create.md).

Data v objektech blob a souborových službách jsou vždycky chráněná pomocí klíčů spravovaných zákazníkem, když jsou pro účet úložiště nakonfigurované klíče spravované zákazníkem.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Povolení klíčů spravovaných zákazníkem pro účet úložiště

Když nakonfigurujete klíč spravovaný zákazníkem, Azure Storage zabalí kořenový šifrovací klíč dat pro účet s klíčem spravovaným zákazníkem v přidruženém trezoru klíčů. Povolení klíčů spravovaných zákazníkem nemá vliv na výkon a projeví se okamžitě.

Když povolíte nebo zakážete spravované klíče zákazníka nebo upravíte klíč nebo verzi klíče, změní se ochrana kořenového šifrovacího klíče, ale data v Azure Storagem účtu nemusí být znovu zašifrovaná.

Klíče spravované zákazníkem se můžou povolit jenom u stávajících účtů úložiště. U trezoru klíčů je potřeba nakonfigurovat zásady přístupu, které udělují oprávnění ke spravované identitě, která je přidružená k účtu úložiště. Spravovaná identita je k dispozici až po vytvoření účtu úložiště.

Mezi uživatelsky spravovanými klíči a klíči spravovanými Microsoftem můžete kdykoli přepínat. Další informace o klíčích spravovaných Microsoftem najdete v tématu [o správě šifrovacích klíčů](storage-service-encryption.md#about-encryption-key-management).

Další informace o použití klíčů spravovaných zákazníkem s Azure Key Vault pro šifrování Azure Storage najdete v jednom z těchto článků:

- [Konfigurace klíčů spravovaných zákazníkem pomocí Key Vault pro Azure Storage šifrování z Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurace klíčů spravovaných zákazníkem pomocí Key Vault pro Azure Storage šifrování z PowerShellu](storage-encryption-keys-powershell.md)
- [Konfigurace klíčů spravovaných zákazníkem pomocí Key Vault pro šifrování Azure Storage z Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity prostředků Azure, což je funkce služby Azure AD. Spravované identity v současné době nepodporují scénáře pro více adresářů. Když v Azure Portal konfigurujete klíče spravované zákazníkem, automaticky se k vašemu účtu úložiště přiřadí spravovaná identita v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo účet úložiště z jednoho adresáře služby Azure AD do jiného, nepřesune se do nového tenanta spravovaná identita přidružená k účtu úložiště, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu **přenos předplatného mezi adresáři služby Azure AD** v [nejčastějších dotazech a známých potížích se spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ukládání klíčů spravovaných zákazníkem v Azure Key Vault

Pokud chcete povolit klíče spravované zákazníkem v účtu úložiště, musíte k uložení klíčů použít Trezor klíčů Azure. V trezoru klíčů musíte povolit jak **obnovitelné odstranění** , tak i **Nemazat** vlastnosti.

Šifrování úložiště Azure podporuje klíče RSA a RSA-HSM velikostí 2048, 3072 a 4096. Další informace o klíčích najdete v tématu **Key Vault Keys** v tématu [informace o Azure Key Vaultch klíčích, tajných klíčích a certifikátech](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

Použití Azure Key Vault má přidružené náklady. Další informace najdete v tématu [Key Vault ceny](/pricing/details/key-vault/).

## <a name="rotate-customer-managed-keys"></a>Otočit klíče spravované zákazníkem

Klíč spravovaný zákazníkem můžete v Azure Key Vault otočit podle vašich zásad dodržování předpisů. Máte dvě možnosti, jak otočit klíč spravovaný zákazníkem:

- **Automatické otočení:** Pokud chcete konfigurovat automatické rotaci klíčů spravovaných zákazníkem, vynechejte verzi klíče, pokud povolíte šifrování s použitím klíčů spravovaných zákazníkem pro účet úložiště. Pokud je verze klíče vynechaná, Azure Storage každý den kontroluje, jestli je ve službě Azure Key Vault k dispozici nová verze klíče spravovaného zákazníkem. Pokud je k dispozici nová verze klíče, Azure Storage automaticky použije nejnovější verzi klíče.
- **Ruční rotace:** Pokud chcete pro Azure Storage šifrování použít určitou verzi klíče, zadejte ji, když povolíte šifrování pomocí klíčů spravovaných zákazníkem pro účet úložiště. Pokud zadáte verzi klíče, pak Azure Storage používá tuto verzi pro šifrování, dokud neaktualizujete ručně verzi klíče.

    Když se klíč ručně otočí, musíte aktualizovat účet úložiště, aby používal nový identifikátor URI verze klíče. Informace o tom, jak aktualizovat účet úložiště pro použití nové verze klíče v Azure Portal, najdete v tématu [Ruční aktualizace verze klíče](storage-encryption-keys-portal.md#manually-update-the-key-version).

Otáčení klíče spravovaného zákazníkem neaktivuje opětovné šifrování dat v účtu úložiště. Od uživatele není vyžadována žádná další akce.

## <a name="revoke-access-to-customer-managed-keys"></a>Odvolat přístup k klíčům spravovaným zákazníkem

Přístup účtu úložiště můžete kdykoli odvolat do klíče spravovaného zákazníkem. Po odvolání přístupu k klíčům spravovaným zákazníkem nebo po zakázání nebo odstranění klíče nemohou klienti volat operace, které čtou nebo zapisují do objektu BLOB nebo jeho metadat. Pokus o volání některé z následujících operací selže s kódem chyby 403 (zakázáno) pro všechny uživatele:

- [Vypsat objekty blob](/rest/api/storageservices/list-blobs)při volání s `include=metadata` parametrem v identifikátoru URI žádosti
- [Získat objekt BLOB](/rest/api/storageservices/get-blob)
- [Získání vlastností objektu blob](/rest/api/storageservices/get-blob-properties)
- [Získat metadata objektu BLOB](/rest/api/storageservices/get-blob-metadata)
- [Nastavení metadat objektu BLOB](/rest/api/storageservices/set-blob-metadata)
- [Objekt BLOB snímku](/rest/api/storageservices/snapshot-blob)při volání s `x-ms-meta-name` hlavičkou požadavku
- [Zkopírování objektu blob](/rest/api/storageservices/copy-blob)
- [Kopírovat objekt BLOB z adresy URL](/rest/api/storageservices/copy-blob-from-url)
- [Nastavení úrovně objektu blob](/rest/api/storageservices/set-blob-tier)
- [Blok vložení](/rest/api/storageservices/put-block)
- [Vložit blok z adresy URL](/rest/api/storageservices/put-block-from-url)
- [Připojit blok](/rest/api/storageservices/append-block)
- [Připojit blok z adresy URL](/rest/api/storageservices/append-block-from-url)
- [Vložení objektu blob](/rest/api/storageservices/put-blob)
- [Vložit stránku](/rest/api/storageservices/put-page)
- [Vložit stránku z adresy URL](/rest/api/storageservices/put-page-from-url)
- [Objekt BLOB přírůstkového kopírování](/rest/api/storageservices/incremental-copy-blob)

Chcete-li tyto operace volat znovu, obnovte přístup k klíči spravovanému zákazníkem.

Všechny operace s daty, které nejsou uvedené v této části, můžou pokračovat až po odvolání klíčů spravovaných zákazníkem nebo zakázání nebo odstranění klíče.

K odvolání přístupu ke klíčům spravovaným zákazníkem použijte [PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) nebo [Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Klíče spravované zákazníkem pro Azure Managed disks

Klíče spravované zákazníkem jsou také k dispozici pro správu šifrování služby Azure Managed disks. Klíče spravované zákazníkem se chovají jinak než u spravovaných disků než u Azure Storagech prostředků. Další informace najdete v tématu [šifrování Azure Managed disks na straně serveru](../../virtual-machines/windows/disk-encryption.md) pro Windows nebo [šifrování na straně serveru Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) pro Linux.

## <a name="next-steps"></a>Další kroky

- [Konfigurace klíčů spravovaných zákazníkem pomocí Key Vault pro Azure Storage šifrování z Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurace klíčů spravovaných zákazníkem pomocí Key Vault pro Azure Storage šifrování z PowerShellu](storage-encryption-keys-powershell.md)
- [Konfigurace klíčů spravovaných zákazníkem pomocí Key Vault pro šifrování Azure Storage z Azure CLI](storage-encryption-keys-cli.md)
- [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md)
