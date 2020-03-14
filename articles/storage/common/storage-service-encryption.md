---
title: Azure Storage šifrování dat v klidovém umístění
description: Azure Storage chrání vaše data tím, že je před trvalým nasazením do cloudu automaticky šifruje. Pro šifrování účtu úložiště můžete spoléhat na klíče spravované Microsoftem, nebo můžete spravovat šifrování pomocí vlastních klíčů.
services: storage
author: tamram
ms.service: storage
ms.date: 03/09/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 028b186964643a08a4370741a3f1ff2ba33a4e85
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370300"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage šifrování dat v klidovém umístění

Azure Storage automaticky šifruje vaše data při jejich trvalém uložení do cloudu. Azure Storage šifrování chrání vaše data a umožňuje vám plnit závazky zabezpečení vaší organizace a dodržování předpisů.

## <a name="about-azure-storage-encryption"></a>O šifrování Azure Storage

Data v Azure Storage jsou šifrována a dešifrována transparentně pomocí 256 [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), je k dispozici jedna z nejúčinnějších šifrovacích šifr a je kompatibilní se standardem FIPS 140-2. Šifrování Azure Storage se v systému Windows podobá šifrování BitLockeru.

Azure Storage šifrování je povolené pro všechny nové účty úložiště, včetně účtů úložiště Správce prostředků a Classic. Šifrování Azure Storage nelze zakázat. Vzhledem k tomu, že vaše data jsou zabezpečená ve výchozím nastavení, nemusíte upravovat kód ani aplikace, abyste mohli využívat Azure Storage šifrování.

Účty úložiště se šifrují bez ohledu na jejich úroveň výkonu (Standard nebo Premium) nebo modelu nasazení (Azure Resource Manager nebo Classic). Všechny možnosti redundance Azure Storage podporují šifrování a všechny kopie účtu úložiště jsou zašifrované. Všechny prostředky Azure Storage jsou zašifrované, včetně objektů blob, disků, souborů, front a tabulek. Všechna metadata objektů jsou také šifrována.

Šifrování nemá vliv na Azure Storage výkon. Azure Storage šifrování se neúčtují žádné další náklady.

Všechny objekty blob bloku, doplňovací objekty blob nebo objekty blob stránky, které byly zapsány do Azure Storage po 20. října 2017, jsou zašifrovány. Objekty blob vytvořené před tímto datem budou i nadále zašifrovány procesem na pozadí. Pokud chcete vynutit šifrování objektu blob, který se vytvořil před 20. října 2017, můžete objekt BLOB přepsat. Informace o tom, jak zjistit stav šifrování objektu blob, najdete v tématu [ověření stavu šifrování objektu BLOB](../blobs/storage-blob-encryption-status.md).

Další informace o kryptografických modulech podkladových Azure Storage šifrování najdete v tématu [kryptografické rozhraní API: další generace](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Pro šifrování účtu úložiště můžete spoléhat na klíče spravované Microsoftem, nebo můžete spravovat šifrování pomocí vlastních klíčů. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, máte dvě možnosti:

- Můžete zadat *klíč spravovaný zákazníkem* Azure Key Vault, který se má použít pro šifrování a dešifrování dat v úložišti objektů BLOB a ve službě soubory Azure. <sup>1, 2</sup>
- Můžete zadat *klíč poskytnutý zákazníkem* pro operace BLOB Storage. Klient, který vytváří požadavek na čtení nebo zápis proti úložišti objektů blob, může do žádosti přidat šifrovací klíč a získat tak podrobné řízení způsobu, jakým se data objektů BLOB šifrují a dešifrují.

Následující tabulka porovnává možnosti správy klíčů pro Azure Storage šifrování.

|                                        |    Klíče spravované společností Microsoft                             |    Klíče spravované zákazníkem                                                                                                                        |    Klíče poskytované zákazníky                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operace šifrování a dešifrování    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Podporované služby Azure Storage Services    |    Vše                                                |    BLOB Storage, soubory Azure<sup>1, 2</sup>                                                                                                               |    Blob Storage                                                                  |
|    Úložiště klíčů                         |    Úložiště klíčů Microsoftu    |    Azure Key Vault                                                                                                                              |    Azure Key Vault nebo jakékoli jiné úložiště klíčů                                                                 |
|    Zodpovědnost za střídání klíčů         |    Microsoft                                          |    Zákazníka                                                                                                                                     |    Zákazníka                                                                      |
|    Použití klíče                           |    Microsoft                                          |    Azure Portal, poskytovatel prostředků úložiště REST API, Azure Storage knihovny pro správu, PowerShell, rozhraní příkazového řádku        |    Azure Storage REST API (BLOB Storage), Azure Storage klientských knihoven    |
|    Přístup ke klíči                          |    Jenom Microsoft                                     |    Microsoft, zákazník                                                                                                                    |    Jenom zákazník                                                                 |

<sup>1</sup> informace o vytvoření účtu, který podporuje použití klíčů spravovaných zákazníkem s úložištěm Queue, najdete v tématu [Vytvoření účtu, který podporuje klíče spravované zákazníkem pro fronty](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> informace o vytvoření účtu, který podporuje použití klíčů spravovaných zákazníkem s tabulkovým úložištěm, najdete v tématu [Vytvoření účtu, který podporuje klíče spravované zákazníkem pro tabulky](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

V následujících částech jsou podrobněji popsány všechny možnosti správy klíčů.

## <a name="microsoft-managed-keys"></a>Klíče spravované společností Microsoft

Ve výchozím nastavení váš účet úložiště používá šifrovací klíče spravované Microsoftem. Nastavení šifrování pro účet úložiště můžete zobrazit v části **šifrování** [Azure Portal](https://portal.azure.com), jak je znázorněno na následujícím obrázku.

![Zobrazit účet zašifrovaný pomocí klíčů spravovaných Microsoftem](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys-with-azure-key-vault"></a>Klíče spravované zákazníkem s Azure Key Vault

Azure Storage šifrování na úrovni účtu úložiště můžete spravovat pomocí vlastních klíčů. Když zadáte klíč spravovaný zákazníkem na úrovni účtu úložiště, tento klíč se používá k ochraně a řízení přístupu ke kořenovému šifrovacímu klíči pro účet úložiště, který se zase používá k šifrování a dešifrování všech objektů BLOB a souborů. Klíče spravované zákazníkem nabízejí větší flexibilitu při správě řízení přístupu. Můžete také auditovat šifrovací klíče používané k ochraně vašich dat.

K ukládání klíčů spravovaných zákazníkem je nutné použít Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Účet úložiště a trezor klíčů musí být ve stejné oblasti a v rámci stejného Azure Active Directory klienta (Azure AD), ale můžou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/key-vault-overview.md).

Tento diagram ukazuje, jak Azure Storage používá Azure Active Directory a Azure Key Vault k tomu, aby vyžádal použití klíče spravovaného zákazníkem:

![Diagram znázorňující, jak jsou klíče spravované zákazníkem v Azure Storage fungovat](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

V následujícím seznamu najdete popis očíslovaných kroků v diagramu:

1. Správce Azure Key Vault uděluje oprávnění šifrovacím klíčům ke spravované identitě, která je přidružená k účtu úložiště.
2. Správce Azure Storage nakonfiguruje šifrování pomocí klíče spravovaného zákazníkem pro účet úložiště.
3. Azure Storage používá spravovanou identitu, která je přidružená k účtu úložiště pro ověřování přístupu k Azure Key Vault prostřednictvím Azure Active Directory.
4. Azure Storage zabalí šifrovací klíč účtu s klíčem zákazníka v Azure Key Vault.
5. Pro operace čtení a zápisu Azure Storage posílá žádosti, aby Azure Key Vault zabalit a rozbalí šifrovací klíč účtu, aby prováděl operace šifrování a dešifrování.

### <a name="enable-customer-managed-keys-for-a-storage-account"></a>Povolení klíčů spravovaných zákazníkem pro účet úložiště

Když pro účet úložiště povolíte šifrování pomocí klíčů spravovaných zákazníkem, Azure Storage zabalí šifrovací klíč účtu s klíčem spravovaným zákazníkem v přidruženém trezoru klíčů. Povolení klíčů spravovaných zákazníkem nemá vliv na výkon a účet je zašifrovaný pomocí nového klíče hned bez jakékoliv prodlevy.

Nový účet úložiště je vždycky zašifrovaný pomocí klíčů spravovaných Microsoftem. V okamžiku vytvoření účtu není možné povolit klíče spravované zákazníkem. Klíče spravované zákazníkem jsou uložené v Azure Key Vault a trezor klíčů musí být zřízený pomocí zásad přístupu, které udělí klíčová oprávnění ke spravované identitě, která je přidružená k účtu úložiště. Spravovaná identita je k dispozici až po vytvoření účtu úložiště.

Když upravíte klíč, který se používá pro Azure Storage šifrování, povolením nebo zakázáním klíčů spravovaných zákazníkem, aktualizací verze klíče nebo zadáním jiného klíče, pak se změní šifrování kořenového klíče, ale data v účtu Azure Storage je nutné znovu zašifrovat.

Další informace o použití klíčů spravovaných zákazníkem s Azure Key Vault pro šifrování Azure Storage najdete v jednom z těchto článků:

- [Konfigurace klíčů spravovaných zákazníkem pomocí Key Vault pro Azure Storage šifrování z Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurace klíčů spravovaných zákazníkem pomocí Key Vault pro Azure Storage šifrování z PowerShellu](storage-encryption-keys-powershell.md)
- [Konfigurace klíčů spravovaných zákazníkem pomocí Key Vault pro šifrování Azure Storage z Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity prostředků Azure, což je funkce služby Azure AD. Spravované identity v současné době nepodporují scénáře pro více adresářů. Když v Azure Portal konfigurujete klíče spravované zákazníkem, automaticky se k vašemu účtu úložiště přiřadí spravovaná identita v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo účet úložiště z jednoho adresáře služby Azure AD do jiného, nepřesune se do nového tenanta spravovaná identita přidružená k účtu úložiště, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu **přenos předplatného mezi adresáři služby Azure AD** v [nejčastějších dotazech a známých potížích se spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ukládání klíčů spravovaných zákazníkem v Azure Key Vault

Pokud chcete povolit klíče spravované zákazníkem v účtu úložiště, musíte použít Azure Key Vault k uložení klíčů. V trezoru klíčů musíte povolit jak **obnovitelné odstranění** , tak i **Nemazat** vlastnosti.

Azure Storage šifrování podporují pouze klíče RSA o velikosti 2048. Další informace o klíčích najdete v tématu **Key Vault Keys** v tématu [informace o Azure Key Vaultch klíčích, tajných klíčích a certifikátech](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Otočit klíče spravované zákazníkem

Klíč spravovaný zákazníkem můžete v Azure Key Vault otočit podle vašich zásad dodržování předpisů. Při otočení klíče musíte aktualizovat účet úložiště, aby používal nový identifikátor URI klíče. Informace o tom, jak aktualizovat účet úložiště pro použití nové verze klíče v Azure Portal, najdete v části s názvem **aktualizace verze klíče** v tématu [konfigurace klíčů spravovaných zákazníkem pro Azure Storage pomocí Azure Portal](storage-encryption-keys-portal.md).

Otáčení klíče neaktivuje opětovné šifrování dat v účtu úložiště. Od uživatele není vyžadována žádná další akce.

### <a name="revoke-access-to-customer-managed-keys"></a>Odvolat přístup k klíčům spravovaným zákazníkem

Přístup účtu úložiště můžete kdykoli odvolat do klíče spravovaného zákazníkem. Po odvolání přístupu k klíčům spravovaným zákazníkem nebo po zakázání nebo odstranění klíče nemohou klienti volat operace, které čtou nebo zapisují do objektu BLOB nebo jeho metadat. Pokus o volání některé z následujících operací selže s kódem chyby 403 (zakázáno) pro všechny uživatele:

- [Vypsat objekty blob](/rest/api/storageservices/list-blobs)při volání s parametrem `include=metadata` v identifikátoru URI žádosti
- [Získat objekt BLOB](/rest/api/storageservices/get-blob)
- [Získat vlastnosti objektu BLOB](/rest/api/storageservices/get-blob-properties)
- [Získat metadata objektu BLOB](/rest/api/storageservices/get-blob-metadata)
- [Nastavení metadat objektu BLOB](/rest/api/storageservices/set-blob-metadata)
- [Objekt BLOB snímku](/rest/api/storageservices/snapshot-blob)při volání s hlavičkou žádosti `x-ms-meta-name`
- [Kopírovat objekt BLOB](/rest/api/storageservices/copy-blob)
- [Kopírovat objekt BLOB z adresy URL](/rest/api/storageservices/copy-blob-from-url)
- [Nastavit úroveň objektu BLOB](/rest/api/storageservices/set-blob-tier)
- [Blok vložení](/rest/api/storageservices/put-block)
- [Vložit blok z adresy URL](/rest/api/storageservices/put-block-from-url)
- [Připojit blok](/rest/api/storageservices/append-block)
- [Připojit blok z adresy URL](/rest/api/storageservices/append-block-from-url)
- [Vložení objektu BLOB](/rest/api/storageservices/put-blob)
- [Vložit stránku](/rest/api/storageservices/put-page)
- [Vložit stránku z adresy URL](/rest/api/storageservices/put-page-from-url)
- [Objekt BLOB přírůstkového kopírování](/rest/api/storageservices/incremental-copy-blob)

Chcete-li tyto operace volat znovu, obnovte přístup k klíči spravovanému zákazníkem.

Všechny operace s daty, které nejsou uvedené v této části, můžou pokračovat až po odvolání klíčů spravovaných zákazníkem nebo zakázání nebo odstranění klíče.

K odvolání přístupu ke klíčům spravovaným zákazníkem použijte [PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) nebo [Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys).

### <a name="customer-managed-keys-for-azure-managed-disks-preview"></a>Klíče spravované zákazníkem pro Azure Managed Disks (Preview)

Klíče spravované zákazníkem jsou také k dispozici pro správu šifrování služby Azure Managed Disks (Preview). Klíče spravované zákazníkem se chovají jinak než u spravovaných disků než u Azure Storagech prostředků. Další informace najdete v tématu [šifrování Azure Managed disks na straně serveru](../../virtual-machines/windows/disk-encryption.md) pro Windows nebo [šifrování na straně serveru Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) pro Linux.

## <a name="customer-provided-keys-preview"></a>Klíče poskytované zákazníky (Preview)

Klienti, kteří provádějí požadavky na úložiště objektů BLOB v Azure, mají možnost zadat šifrovací klíč pro jednotlivé požadavky. Zahrnutí šifrovacího klíče na žádost poskytuje podrobnou kontrolu nad nastavením šifrování pro operace BLOB Storage. Klíče poskytované zákazníky (Preview) je možné uložit v Azure Key Vault nebo v jiném úložišti klíčů.

Příklad, který ukazuje, jak zadat klíč poskytovaný zákazníkem pro požadavek na úložiště objektů blob, najdete v tématu [určení klíče poskytovaného zákazníkem na žádost do úložiště objektů BLOB v rozhraní .NET](../blobs/storage-blob-customer-provided-key.md).

### <a name="encrypting-read-and-write-operations"></a>Šifrování operací čtení a zápisu

Když klientská aplikace poskytuje šifrovací klíč na žádost, Azure Storage provádí šifrování a dešifrování transparentně při čtení a zápisu dat objektů BLOB. Azure Storage zapíše hodnotu hash SHA-256 šifrovacího klíče spolu s obsahem objektu BLOB. Hodnota hash se používá k ověření, že všechny následné operace s objektem BLOB používají stejný šifrovací klíč.

Azure Storage neukládá ani nespravuje šifrovací klíč, který klient s požadavkem odesílá. Klíč se bezpečně zahodí ihned po dokončení procesu šifrování nebo dešifrování.

Když klient vytvoří nebo aktualizuje objekt BLOB pomocí klíče poskytnutého zákazníkem, musí klíč zadat i následné žádosti o čtení a zápis tohoto objektu BLOB. Pokud klíč není k dispozici u žádosti o objekt blob, který již byl zašifrován pomocí klíče poskytnutého zákazníkem, požadavek se nezdařil s kódem chyby 409 (konflikt).

Pokud klientská aplikace odešle šifrovací klíč na žádost a účet úložiště je také zašifrovaný pomocí klíče spravovaného společností Microsoft nebo klíče spravovaného zákazníkem, Azure Storage použije klíč poskytovaný na žádost o šifrování a dešifrování.

Aby mohl klient v rámci žádosti odeslat šifrovací klíč, musí navázat zabezpečené připojení k Azure Storage pomocí protokolu HTTPS.

Každý snímek objektu BLOB může mít vlastní šifrovací klíč.

### <a name="request-headers-for-specifying-customer-provided-keys"></a>Hlavičky požadavku na zadání klíčů poskytovaných zákazníkem

V případě volání REST můžou klienti pomocí následujících hlaviček bezpečně předat informace o šifrovacím klíči na žádost do úložiště objektů BLOB:

|Hlavička žádosti | Popis |
|---------------|-------------|
|`x-ms-encryption-key` |Vyžaduje se pro požadavky zápisu i čtení. Hodnota šifrovacího klíče AES-256 kódovaná v kódování Base64. |
|`x-ms-encryption-key-sha256`| Vyžaduje se pro požadavky zápisu i čtení. SHA256 šifrovacího klíče zakódovaného ve formátu base64. |
|`x-ms-encryption-algorithm` | Vyžadováno pro požadavky na zápis, volitelné pro požadavky na čtení. Určuje algoritmus, který se použije při šifrování dat pomocí daného klíče. Musí být AES256. |

Zadání šifrovacích klíčů v žádosti je volitelné. Pokud však zadáte jednu z výše uvedených hlaviček pro operaci zápisu, je nutné zadat všechny z nich.

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operace BLOB Storage podporující klíče poskytované zákazníky

Následující operace úložiště BLOB podporují odesílání šifrovacích klíčů poskytovaných zákazníkem na žádost:

- [Vložení objektu BLOB](/rest/api/storageservices/put-blob)
- [Seznam blokovaných umístění](/rest/api/storageservices/put-block-list)
- [Blok vložení](/rest/api/storageservices/put-block)
- [Vložit blok z adresy URL](/rest/api/storageservices/put-block-from-url)
- [Vložit stránku](/rest/api/storageservices/put-page)
- [Vložit stránku z adresy URL](/rest/api/storageservices/put-page-from-url)
- [Připojit blok](/rest/api/storageservices/append-block)
- [Nastavení vlastností objektu BLOB](/rest/api/storageservices/set-blob-properties)
- [Nastavení metadat objektu BLOB](/rest/api/storageservices/set-blob-metadata)
- [Získat objekt BLOB](/rest/api/storageservices/get-blob)
- [Získat vlastnosti objektu BLOB](/rest/api/storageservices/get-blob-properties)
- [Získat metadata objektu BLOB](/rest/api/storageservices/get-blob-metadata)
- [Objekt BLOB snímku](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>Otočit klíče poskytované zákazníky

Pokud chcete otočit šifrovací klíč předaný na žádost, Stáhněte objekt BLOB a znovu ho nahrajte pomocí nového šifrovacího klíče.

> [!IMPORTANT]
> Azure Portal nelze použít ke čtení nebo zápisu do kontejneru nebo objektu blob, který je zašifrovaný pomocí klíče, který je k dispozici na žádosti.
>
> Nezapomeňte chránit šifrovací klíč, který zadáte na žádost pro úložiště objektů BLOB v zabezpečeném úložišti klíčů, jako je Azure Key Vault. Pokud se pokusíte o operaci zápisu na kontejneru nebo objektu BLOB bez šifrovacího klíče, operace se nezdaří a ztratíte přístup k objektu.

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage šifrování oproti šifrování disku

Azure Storage šifrování šifruje objekty blob stránky, které se nacházejí na discích virtuálních počítačů Azure. Kromě toho mohou být všechny disky virtuálních počítačů Azure, včetně místních dočasných disků, šifrovány pomocí [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption používá standardní [Nástroj BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) ve Windows a [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) v systému Linux, který poskytuje řešení šifrování založená na operačním systému, která jsou integrovaná s Azure Key Vault.

## <a name="next-steps"></a>Další kroky

- [Co je Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage na webu Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage v PowerShellu](storage-encryption-keys-powershell.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage v Azure CLI](storage-encryption-keys-cli.md)
