---
title: Klíče spravované zákazníkem pro šifrování účtů
titleSuffix: Azure Storage
description: K ochraně dat ve vašem účtu úložiště můžete použít vlastní šifrovací klíč. Když zadáte klíč spravovaný zákazníkem, použije se tento klíč k ochraně a řízení přístupu ke klíči, který šifruje vaše data. Klíče spravované zákazníkem nabízejí větší flexibilitu při správě řízení přístupu.
services: storage
author: tamram
ms.service: storage
ms.date: 03/09/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 358be21a1f7f13c0c0ce1bf0e57dda53174216b2
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517527"
---
# <a name="customer-managed-keys-for-azure-storage-encryption"></a>Klíče spravované zákazníkem pro šifrování Azure Storage

K ochraně dat ve vašem účtu úložiště můžete použít vlastní šifrovací klíč. Když zadáte klíč spravovaný zákazníkem, použije se tento klíč k ochraně a řízení přístupu ke klíči, který šifruje vaše data. Klíče spravované zákazníkem nabízejí větší flexibilitu při správě řízení přístupu.

K ukládání klíčů spravovaných zákazníkem je nutné použít buď Azure Key Vault, nebo Azure Key Vault spravovaný modul hardwarového zabezpečení (HSM) (Preview). Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů nebo spravovaného modulu HSM, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Účet úložiště a trezor klíčů nebo spravovaný modul HSM musí být ve stejné oblasti a v rámci stejného Azure Active Directory klienta (Azure AD), ale můžou být v různých předplatných.

Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/general/overview.md).

> [!IMPORTANT]
>
> Šifrování pomocí klíčů spravovaných zákazníkem, které jsou uložené v Azure Key Vault spravovaný modul HSM, je momentálně ve **verzi Preview**. Přečtěte si další [podmínky použití Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview pro právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.
>
> Azure Key Vault a Azure Key Vault spravovaný modul HSM podporuje pro konfiguraci stejné rozhraní API a rozhraní pro správu.

## <a name="about-customer-managed-keys"></a>O klíčích spravovaných zákazníkem

Následující diagram ukazuje, jak Azure Storage používá Azure Active Directory a trezor klíčů nebo spravovaný modul HSM k vytváření požadavků pomocí klíče spravovaného zákazníkem:

![Diagram znázorňující, jak jsou klíče spravované zákazníkem v Azure Storage fungovat](media/customer-managed-keys-overview/encryption-customer-managed-keys-diagram.png)

V následujícím seznamu najdete popis očíslovaných kroků v diagramu:

1. Správce Azure Key Vault uděluje oprávnění šifrovacím klíčům ke spravované identitě, která je přidružená k účtu úložiště.
2. Správce Azure Storage nakonfiguruje šifrování pomocí klíče spravovaného zákazníkem pro účet úložiště.
3. Azure Storage používá spravovanou identitu, která je přidružená k účtu úložiště pro ověřování přístupu k Azure Key Vault prostřednictvím Azure Active Directory.
4. Azure Storage zabalí šifrovací klíč účtu s klíčem zákazníka v Azure Key Vault.
5. Pro operace čtení a zápisu Azure Storage posílá žádosti, aby Azure Key Vault rozbalí šifrovací klíč účtu, aby prováděl operace šifrování a dešifrování.

## <a name="customer-managed-keys-for-queues-and-tables"></a>Klíče spravované zákazníkem pro fronty a tabulky

Pokud jsou pro účet úložiště povolené klíče spravované zákazníkem, data uložená ve frontě a úložišti tabulek se automaticky nechrání klíčem spravovaným zákazníkem. Volitelně můžete tyto služby nakonfigurovat tak, aby byly zahrnuty do této ochrany v době, kdy vytváříte účet úložiště.

Další informace o tom, jak vytvořit účet úložiště, který podporuje klíče spravované zákazníky pro fronty a tabulky, najdete v tématu [Vytvoření účtu, který podporuje klíče spravované zákazníkem pro tabulky a fronty](account-encryption-key-create.md).

Data v úložišti objektů BLOB a v souborech Azure jsou vždycky chráněná pomocí zákaznických klíčů, když jsou pro účet úložiště nakonfigurované klíče spravované zákazníkem.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Povolení klíčů spravovaných zákazníkem pro účet úložiště

Když nakonfigurujete klíč spravovaný zákazníkem, Azure Storage zabalí kořenový šifrovací klíč dat pro účet s klíčem spravovaným zákazníkem v přidruženém trezoru klíčů nebo spravovaném HSM. Povolení klíčů spravovaných zákazníkem nemá vliv na výkon a projeví se okamžitě.

Když povolíte nebo zakážete spravované klíče zákazníka nebo upravíte klíč nebo verzi klíče, změní se ochrana kořenového šifrovacího klíče, ale data v Azure Storagem účtu nemusí být znovu zašifrovaná.

Klíče spravované zákazníkem se můžou povolit jenom u stávajících účtů úložiště. Trezor klíčů nebo spravovaný modul HSM musí být nakonfigurovaný tak, aby udělil oprávnění spravované identitě, která je přidružená k účtu úložiště. Spravovaná identita je k dispozici až po vytvoření účtu úložiště.

Mezi uživatelsky spravovanými klíči a klíči spravovanými Microsoftem můžete kdykoli přepínat. Další informace o klíčích spravovaných Microsoftem najdete v tématu [o správě šifrovacích klíčů](storage-service-encryption.md#about-encryption-key-management).

Informace o tom, jak nakonfigurovat šifrování Azure Storage pomocí klíčů spravovaných zákazníkem v trezoru klíčů, najdete v tématu [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem uložených v Azure Key Vault](customer-managed-keys-configure-key-vault.md). Informace o konfiguraci klíčů spravovaných zákazníkem ve spravovaném modulu HSM najdete v tématu [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem, které jsou uložené v Azure Key Vault spravované HSM (Preview)](customer-managed-keys-configure-key-vault-hsm.md).

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity prostředků Azure, což je funkce služby Azure AD. Spravované identity v současné době nepodporují scénáře pro více adresářů. Když v Azure Portal konfigurujete klíče spravované zákazníkem, automaticky se k vašemu účtu úložiště přiřadí spravovaná identita v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo účet úložiště z jednoho adresáře služby Azure AD do jiného, nepřesune se do nového tenanta spravovaná identita přidružená k účtu úložiště, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu **přenos předplatného mezi adresáři služby Azure AD** v [nejčastějších dotazech a známých potížích se spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

Šifrování úložiště Azure podporuje klíče RSA a RSA-HSM velikostí 2048, 3072 a 4096. Další informace o klíčích najdete v tématu [o klíčích](../../key-vault/keys/about-keys.md).

Pomocí trezoru klíčů nebo spravovaného modulu HARDWAROVÉho zabezpečení jsou přiřazeny náklady. Další informace najdete v tématu [Key Vault ceny](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="update-the-key-version"></a>Aktualizace verze klíče

Když konfigurujete šifrování s použitím klíčů spravovaných zákazníkem, máte dvě možnosti aktualizace verze klíče:

- **Automaticky aktualizovat verzi klíče:** Pokud chcete automaticky aktualizovat klíč spravovaný zákazníkem, když je k dispozici nová verze, vynechejte verzi klíče, pokud povolíte šifrování pomocí klíčů spravovaných zákazníkem pro účet úložiště. Pokud je verze klíče vynechána, Azure Storage zkontroluje Trezor klíčů nebo spravovaný modul HSM denně pro novou verzi klíče spravovaného zákazníkem. Azure Storage automaticky používá nejnovější verzi klíče.
- **Ruční aktualizace verze klíče:** Pokud chcete pro Azure Storage šifrování použít určitou verzi klíče, zadejte ji, když povolíte šifrování pomocí klíčů spravovaných zákazníkem pro účet úložiště. Pokud zadáte verzi klíče, pak Azure Storage používá tuto verzi pro šifrování, dokud neaktualizujete ručně verzi klíče.

    Pokud je verze klíče explicitně zadaná, musíte ručně aktualizovat účet úložiště, aby při vytvoření nové verze používal nový identifikátor URI verze klíče. Informace o tom, jak aktualizovat účet úložiště pro použití nové verze klíče, najdete v tématu [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem uložených v Azure Key Vault](customer-managed-keys-configure-key-vault.md) nebo [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem, které jsou uložené v Azure Key Vault Managed HSM (Preview)](customer-managed-keys-configure-key-vault-hsm.md).

Když aktualizujete verzi klíče, změní se ochrana kořenového šifrovacího klíče, ale data v Azure Storagem účtu se znovu nešifrují. Od uživatele není vyžadována žádná další akce.

> [!NOTE]
> Pokud chcete otočit klíč, vytvořte novou verzi klíče v trezoru klíčů nebo spravovaném HSM podle vašich zásad dodržování předpisů. Svůj klíč můžete otočit ručně nebo vytvořit funkci, která ho otočí podle plánu.

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

K odvolání přístupu ke klíčům spravovaným zákazníkem použijte [PowerShell](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys) nebo [Azure CLI](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Klíče spravované zákazníkem pro Azure Managed disks

Klíče spravované zákazníkem jsou také k dispozici pro správu šifrování služby Azure Managed disks. Klíče spravované zákazníkem se chovají jinak než u spravovaných disků než u Azure Storagech prostředků. Další informace najdete v tématu [šifrování Azure Managed disks na straně serveru](../../virtual-machines/disk-encryption.md) pro Windows nebo [šifrování na straně serveru Azure Managed disks](../../virtual-machines/disk-encryption.md) pro Linux.

## <a name="next-steps"></a>Další kroky

- [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md)
- [Konfigurace šifrování s použitím klíčů spravovaných zákazníkem uložených v Azure Key Vault](customer-managed-keys-configure-key-vault.md)
- [Konfigurace šifrování pomocí klíčů spravovaných zákazníkem uložených v Azure Key Vault spravovaném HSM (Preview)](customer-managed-keys-configure-key-vault-hsm.md)
