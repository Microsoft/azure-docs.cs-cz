---
title: Azure Storage šifrování dat v klidovém umístění | Microsoft Docs
description: Azure Storage chrání vaše data tím, že je před trvalým nasazením do cloudu automaticky šifruje. Pro šifrování účtu úložiště můžete spoléhat na klíče spravované Microsoftem, nebo můžete spravovat šifrování pomocí vlastních klíčů.
services: storage
author: tamram
ms.service: storage
ms.date: 10/02/2019
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: cfac7fdbbdbf06ae74385fbc33e61d11cb99ff87
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74066317"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage šifrování dat v klidovém umístění

Azure Storage automaticky šifruje vaše data při jejich uchování do cloudu. Šifrování chrání vaše data a usnadňuje splnění závazků týkajících se zabezpečení a dodržování předpisů v organizaci. Data v Azure Storage jsou šifrována a dešifrována transparentně pomocí 256 [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), je k dispozici jedna z nejúčinnějších šifrovacích šifr a je kompatibilní se standardem FIPS 140-2. Šifrování Azure Storage se v systému Windows podobá šifrování BitLockeru.

Pro všechny nové účty úložiště je povolené šifrování Azure Storage a nedá se zakázat. Vzhledem k tomu, že vaše data jsou zabezpečená ve výchozím nastavení, nemusíte upravovat kód ani aplikace, abyste mohli využívat Azure Storage šifrování.

Účty úložiště se šifrují bez ohledu na jejich úroveň výkonu (Standard nebo Premium) nebo modelu nasazení (Azure Resource Manager nebo Classic). Všechny možnosti redundance Azure Storage podporují šifrování a všechny kopie účtu úložiště jsou zašifrované. Všechny prostředky Azure Storage jsou zašifrované, včetně objektů blob, disků, souborů, front a tabulek. Všechna metadata objektů jsou také šifrována.

Šifrování nemá vliv na Azure Storage výkon. Azure Storage šifrování se neúčtují žádné další náklady.

Další informace o kryptografických modulech podkladových Azure Storage šifrování najdete v tématu [kryptografické rozhraní API: další generace](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Pro šifrování účtu úložiště můžete spoléhat na klíče spravované Microsoftem, nebo můžete spravovat šifrování pomocí vlastních klíčů. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, máte dvě možnosti:

- Můžete zadat *klíč spravovaný zákazníkem* , který se použije pro šifrování a dešifrování všech dat v účtu úložiště. Klíč spravovaný zákazníkem slouží k šifrování všech dat ve všech službách v účtu úložiště.
- Můžete zadat *klíč poskytnutý zákazníkem* pro operace BLOB Storage. Klient, který vytváří požadavek na čtení nebo zápis proti úložišti objektů blob, může do žádosti přidat šifrovací klíč a získat tak podrobné řízení způsobu, jakým se data objektů BLOB šifrují a dešifrují.

Následující tabulka porovnává možnosti správy klíčů pro Azure Storage šifrování.

|                                        |    Klíče spravované společností Microsoft                             |    Klíče spravované zákazníkem                                                                                                                        |    Klíče poskytované zákazníky                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operace šifrování a dešifrování    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Podporované služby Azure Storage Services    |    Vše                                                |    BLOB Storage, soubory Azure                                                                                                               |    Blob Storage                                                                  |
|    Úložiště klíčů                         |    Úložiště klíčů Microsoftu    |    Azure Key Vault                                                                                                                              |    Azure Key Vault nebo jakékoli jiné úložiště klíčů                                                                 |
|    Zodpovědnost za střídání klíčů         |    Microsoft                                          |    Zákazník                                                                                                                                     |    Zákazník                                                                      |
|    Použití klíče                           |    Microsoft                                          |    Azure Portal, poskytovatel prostředků úložiště REST API, Azure Storage knihovny pro správu, PowerShell, rozhraní příkazového řádku        |    Azure Storage REST API (BLOB Storage), Azure Storage klientských knihoven    |
|    Přístup ke klíči                          |    Jenom Microsoft                                     |    Microsoft, zákazník                                                                                                                    |    Jenom zákazník                                                                 |

V následujících částech jsou podrobněji popsány všechny možnosti správy klíčů.

## <a name="microsoft-managed-keys"></a>Klíče spravované společností Microsoft

Ve výchozím nastavení váš účet úložiště používá šifrovací klíče spravované Microsoftem. Nastavení šifrování pro účet úložiště můžete zobrazit v části **šifrování** [Azure Portal](https://portal.azure.com), jak je znázorněno na následujícím obrázku.

![Zobrazit účet zašifrovaný pomocí klíčů spravovaných Microsoftem](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys"></a>Klíče spravované zákazníkem

Můžete se rozhodnout, že budete Azure Storage šifrování na úrovni účtu úložiště spravovat pomocí vlastních klíčů. Když na úrovni účtu úložiště zadáte klíč spravovaný zákazníkem, použije se tento klíč k šifrování a dešifrování všech dat v účtu úložiště, včetně objektů blob, front, souborů a dat tabulek.  Klíče spravované zákazníkem nabízejí větší flexibilitu při vytváření, střídání, zakázání a odvolávání řízení přístupu. Můžete také auditovat šifrovací klíče používané k ochraně vašich dat.

K ukládání klíčů spravovaných zákazníkem je nutné použít Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Účet úložiště a trezor klíčů musí být ve stejné oblasti, ale můžou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/key-vault-overview.md).

Tento diagram ukazuje, jak Azure Storage používá Azure Active Directory a Azure Key Vault k tomu, aby vyžádal použití klíče spravovaného zákazníkem:

![Diagram znázorňující, jak jsou klíče spravované zákazníkem v Azure Storage fungovat](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

V následujícím seznamu najdete popis očíslovaných kroků v diagramu:

1. Správce Azure Key Vault uděluje oprávnění šifrovacím klíčům ke spravované identitě, která je přidružená k účtu úložiště.
2. Správce Azure Storage nakonfiguruje šifrování pomocí klíče spravovaného zákazníkem pro účet úložiště.
3. Azure Storage používá spravovanou identitu, která je přidružená k účtu úložiště pro ověřování přístupu k Azure Key Vault prostřednictvím Azure Active Directory.
4. Azure Storage zabalí šifrovací klíč účtu s klíčem zákazníka v Azure Key Vault.
5. Pro operace čtení a zápisu Azure Storage posílá žádosti, aby Azure Key Vault zabalit a rozbalí šifrovací klíč účtu, aby prováděl operace šifrování a dešifrování.

Pokud chcete odvolat přístup k klíčům spravovaným zákazníkem v účtu úložiště, přečtěte si téma [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) a [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Odvolání přístupu efektivně zablokuje přístup ke všem datům v účtu úložiště, protože šifrovací klíč je nepřístupný Azure Storage.

Klíče spravované zákazníkem jsou k dispozici také pro služby Azure Managed disks jako verze Public Preview. klíče spravované zákazníkem fungují jinak než u spravovaných disků než do zbytku úložiště. Podrobnosti najdete v našem [článku na předmětu](../../virtual-machines/linux/disk-encryption.md#customer-managed-keys-public-preview).

Další informace o použití klíčů spravovaných zákazníkem s Azure Storage najdete v jednom z těchto článků:

- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage na webu Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage v PowerShellu](storage-encryption-keys-powershell.md)
- [Použití klíčů spravovaných zákazníkem s šifrováním Azure Storage z Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity prostředků Azure, což je funkce Azure Active Directory (Azure AD). Když v Azure Portal konfigurujete klíče spravované zákazníkem, automaticky se k vašemu účtu úložiště přiřadí spravovaná identita v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo účet úložiště z jednoho adresáře služby Azure AD do jiného, nepřesune se do nového tenanta spravovaná identita přidružená k účtu úložiště, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu **přenos předplatného mezi adresáři služby Azure AD** v [nejčastějších dotazech a známých potížích se spravovanými identitami pro prostředky Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="customer-provided-keys-preview"></a>Klíče poskytované zákazníky (Preview)

Klienti, kteří provádějí požadavky na úložiště objektů BLOB v Azure, mají možnost zadat šifrovací klíč pro jednotlivé požadavky. Zahrnutí šifrovacího klíče na žádost poskytuje podrobnou kontrolu nad nastavením šifrování pro operace BLOB Storage. Klíče poskytované zákazníky (Preview) je možné uložit v Azure Key Vault nebo v jiném úložišti klíčů.

### <a name="encrypting-read-and-write-operations"></a>Šifrování operací čtení a zápisu

Když klientská aplikace poskytuje šifrovací klíč na žádost, Azure Storage provádí šifrování a dešifrování transparentně při čtení a zápisu dat objektů BLOB. Algoritmus SHA-256 šifrovacího klíče se zapisuje společně s obsahem objektu BLOB a slouží k ověření, že všechny následné operace s objektem BLOB používají stejný šifrovací klíč. Azure Storage neukládá ani nespravuje šifrovací klíč, který klient s požadavkem odesílá. Klíč se bezpečně zahodí ihned po dokončení procesu šifrování nebo dešifrování.

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

### <a name="example-use-a-customer-provided-key-to-upload-a-blob-in-net"></a>Příklad: použití klíčového zákazníka k nahrání objektu BLOB v .NET

Následující příklad vytvoří klíč poskytnutý zákazníkem a použije tento klíč k nahrání objektu BLOB. Kód nahraje blok a pak potvrdí seznam blokování, aby napsal objekt blob do Azure Storage. Klíč je k dispozici na objektu [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) nastavením vlastnosti [CustomerProvidedKey](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.customerprovidedkey) .

Klíč je vytvořen pomocí třídy [AesCryptoServiceProvider](/dotnet/api/system.security.cryptography.aescryptoserviceprovider) . Chcete-li vytvořit instanci této třídy v kódu, přidejte příkaz `using`, který odkazuje na obor názvů `System.Security.Cryptography`:

```csharp
public static void UploadBlobWithClientKey(CloudBlobContainer container)
{
    // Create a new key using the Advanced Encryption Standard (AES) algorithm.
    AesCryptoServiceProvider keyAes = new AesCryptoServiceProvider();

    // Specify the key as an option on the request.
    BlobCustomerProvidedKey customerProvidedKey = new BlobCustomerProvidedKey(keyAes.Key);
    var options = new BlobRequestOptions
    {
        CustomerProvidedKey = customerProvidedKey
    };

    string blobName = "sample-blob-" + Guid.NewGuid();
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);

    try
    {
        // Create an array of random bytes.
        byte[] buffer = new byte[1024];
        Random rnd = new Random();
        rnd.NextBytes(buffer);

        using (MemoryStream sourceStream = new MemoryStream(buffer))
        {
            // Write the array of random bytes to a block.
            int blockNumber = 1;
            string blockId = Convert.ToBase64String(Encoding.ASCII.GetBytes(string.Format("BlockId{0}",
                blockNumber.ToString("0000000"))));

            // Write the block to Azure Storage.
            blockBlob.PutBlock(blockId, sourceStream, null, null, options, null);

            // Commit the block list to write the blob.
            blockBlob.PutBlockList(new List<string>() { blockId }, null, options, null);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage šifrování oproti šifrování disku

Azure Storage šifrování šifruje objekty blob stránky, které se nacházejí na discích virtuálních počítačů Azure. Kromě toho mohou být všechny disky virtuálních počítačů Azure, včetně místních dočasných disků, šifrovány pomocí [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption používá standardní [Nástroj BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) ve Windows a [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) v systému Linux, který poskytuje řešení šifrování založená na operačním systému, která jsou integrovaná s Azure Key Vault.

## <a name="next-steps"></a>Další kroky

- [Co je Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage na webu Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage v PowerShellu](storage-encryption-keys-powershell.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage v Azure CLI](storage-encryption-keys-cli.md)
