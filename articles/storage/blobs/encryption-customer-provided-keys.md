---
title: Zadání šifrovacího klíče pro požadavek na úložiště objektů BLOB
titleSuffix: Azure Storage
description: Klienti, kteří provádějí požadavky na úložiště objektů BLOB v Azure, mají možnost zadat šifrovací klíč na základě jednotlivých požadavků. Zahrnutí šifrovacího klíče na žádost poskytuje podrobnou kontrolu nad nastavením šifrování pro operace BLOB Storage.
services: storage
author: tamram
ms.service: storage
ms.date: 12/14/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcc5c02c4a37e205622470260d3c620ad76d07d8
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694693"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage"></a>Zadání šifrovacího klíče pro požadavek na úložiště objektů BLOB

Klienti, kteří provádějí požadavky na úložiště objektů BLOB v Azure, mají možnost zadat šifrovací klíč AES-256 na základě jednotlivých požadavků. Zahrnutí šifrovacího klíče na žádost poskytuje podrobnou kontrolu nad nastavením šifrování pro operace BLOB Storage. Klíče poskytované zákazníkem mohou být uloženy v Azure Key Vault nebo v jiném úložišti klíčů.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="encrypting-read-and-write-operations"></a>Šifrování operací čtení a zápisu

Když klientská aplikace poskytuje šifrovací klíč na žádost, Azure Storage provádí šifrování a dešifrování transparentně při čtení a zápisu dat objektů BLOB. Azure Storage zapíše hodnotu hash SHA-256 šifrovacího klíče spolu s obsahem objektu BLOB. Hodnota hash se používá k ověření, že všechny následné operace s objektem BLOB používají stejný šifrovací klíč.

Azure Storage neukládá ani nespravuje šifrovací klíč, který klient s požadavkem odesílá. Klíč se okamžitě po dokončení procesu šifrování nebo dešifrování bezpečně odstraní.

Když klient vytvoří nebo aktualizuje objekt BLOB pomocí klíče dodaného zákazníkem v žádosti, musí klíč zadat i následné žádosti o čtení a zápis tohoto objektu BLOB. Pokud klíč není k dispozici u žádosti o objekt blob, který již byl zašifrován pomocí klíče poskytnutého zákazníkem, požadavek se nezdařil s kódem chyby 409 (konflikt).

Pokud klientská aplikace odešle šifrovací klíč na žádost a účet úložiště je také zašifrovaný pomocí klíče spravovaného společností Microsoft nebo klíče spravovaného zákazníkem, Azure Storage použije klíč poskytovaný na žádost o šifrování a dešifrování.

Aby mohl klient v rámci žádosti odeslat šifrovací klíč, musí navázat zabezpečené připojení k Azure Storage pomocí protokolu HTTPS.

Každý snímek objektu BLOB může mít vlastní šifrovací klíč.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Hlavičky požadavku na zadání klíčů poskytovaných zákazníkem

V případě volání REST můžou klienti pomocí následujících hlaviček bezpečně předat informace o šifrovacím klíči na žádost do úložiště objektů BLOB:

|Hlavička požadavku | Popis |
|---------------|-------------|
|`x-ms-encryption-key` |Vyžaduje se pro požadavky zápisu i čtení. Hodnota šifrovacího klíče AES-256 kódovaná v kódování Base64. |
|`x-ms-encryption-key-sha256`| Vyžaduje se pro požadavky zápisu i čtení. SHA256 šifrovacího klíče zakódovaného ve formátu base64. |
|`x-ms-encryption-algorithm` | Vyžadováno pro požadavky na zápis, volitelné pro požadavky na čtení. Určuje algoritmus, který se použije při šifrování dat pomocí daného klíče.  Hodnota této hlavičky musí být `AES256` . |

Zadání šifrovacích klíčů v žádosti je volitelné. Pokud však zadáte jednu z výše uvedených hlaviček pro operaci zápisu, je nutné zadat všechny z nich.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operace BLOB Storage podporující klíče poskytované zákazníky

Následující operace úložiště BLOB podporují odesílání šifrovacích klíčů poskytovaných zákazníkem na žádost:

- [Vložení objektu blob](/rest/api/storageservices/put-blob)
- [Seznam blokovaných umístění](/rest/api/storageservices/put-block-list)
- [Blok vložení](/rest/api/storageservices/put-block)
- [Vložit blok z adresy URL](/rest/api/storageservices/put-block-from-url)
- [Vložit stránku](/rest/api/storageservices/put-page)
- [Vložit stránku z adresy URL](/rest/api/storageservices/put-page-from-url)
- [Připojit blok](/rest/api/storageservices/append-block)
- [Nastavení vlastností objektu BLOB](/rest/api/storageservices/set-blob-properties)
- [Nastavení metadat objektu BLOB](/rest/api/storageservices/set-blob-metadata)
- [Získat objekt BLOB](/rest/api/storageservices/get-blob)
- [Získání vlastností objektu blob](/rest/api/storageservices/get-blob-properties)
- [Získat metadata objektu BLOB](/rest/api/storageservices/get-blob-metadata)
- [Pořízení snímku objektu blob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Otočit klíče poskytované zákazníky

Pokud chcete otočit šifrovací klíč, který se použil k zašifrování objektu blob, Stáhněte si objekt BLOB a pak ho znovu nahrajte pomocí nového šifrovacího klíče.

> [!IMPORTANT]
> Azure Portal nelze použít ke čtení nebo zápisu do kontejneru nebo objektu blob, který je zašifrovaný pomocí klíče, který je k dispozici na žádosti.
>
> Nezapomeňte chránit šifrovací klíč, který zadáte na žádost pro úložiště objektů BLOB v zabezpečeném úložišti klíčů, jako je Azure Key Vault. Pokud se pokusíte o operaci zápisu na kontejneru nebo objektu BLOB bez šifrovacího klíče, operace se nezdaří a ztratíte přístup k objektu.

## <a name="next-steps"></a>Další kroky

- [Určení klíče poskytovaného zákazníkem pro požadavek na úložiště objektů BLOB s využitím .NET](storage-blob-customer-provided-key.md)
- [Šifrování služby Azure Storage pro neaktivní uložená data](../common/storage-service-encryption.md)
