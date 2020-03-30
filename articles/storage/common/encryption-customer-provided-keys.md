---
title: Poskytnutí šifrovacího klíče při požadavku na úložiště objektů Blob
titleSuffix: Azure Storage
description: Klienti, kteří pořazují požadavky na úložiště objektů Blob Azure, mají možnost poskytnout šifrovací klíč na základě požadavku (preview). Zahrnutí šifrovacího klíče v požadavku poskytuje podrobnou kontrolu nad nastavením šifrování pro operace úložiště objektů Blob.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c8a5555c5c33255fdc5902a115e7e9103a4e936f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79410061"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage-preview"></a>Poskytnutí šifrovacího klíče při požadavku na úložiště objektů Blob (preview)

Klienti, kteří pořazují požadavky na úložiště objektů Blob Azure, mají možnost poskytnout šifrovací klíč na základě požadavku (preview). Zahrnutí šifrovacího klíče v požadavku poskytuje podrobnou kontrolu nad nastavením šifrování pro operace úložiště objektů Blob. Klíče poskytované zákazníkem lze uložit v trezoru klíčů Azure nebo v jiném úložišti klíčů.

## <a name="encrypting-read-and-write-operations"></a>Šifrování operací čtení a zápisu

Když klientská aplikace poskytuje šifrovací klíč na žádost, Azure Storage provádí šifrování a dešifrování transparentně při čtení a zápisu dat objektu blob. Azure Storage zapíše sha-256 hash šifrovacího klíče vedle obsahu objektu blob. Soubor hash se používá k ověření, že všechny následné operace proti objektu blob používají stejný šifrovací klíč.

Azure Storage neukládá ani nespravuje šifrovací klíč, který klient odesílá s požadavkem. Klíč je bezpečně zahozen, jakmile je proces šifrování nebo dešifrování dokončen.

Když klient vytvoří nebo aktualizuje objekt blob pomocí klíče poskytnutého zákazníkem na požadavek, pak následné požadavky na čtení a zápis pro tento objekt blob musí také poskytnout klíč. Pokud klíč není k dispozici na žádost o objekt blob, který již byl zašifrován pomocí klíče zapředpokladu zákazníka, požadavek se nezdaří s kódem chyby 409 (Conflict).

Pokud klientská aplikace odešle šifrovací klíč na žádost a účet úložiště je také šifrována pomocí klíče spravovaného microsoftem nebo klíčem spravovaného zákazníkem, pak Azure Storage používá klíč k dispozici na žádost o šifrování a dešifrování.

Chcete-li odeslat šifrovací klíč jako součást požadavku, klient musí navázat zabezpečené připojení k úložišti Azure pomocí protokolu HTTPS.

Každý snímek objektu blob může mít vlastní šifrovací klíč.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Záhlaví požadavku pro určení klíčů poskytnutých zákazníkem

Pro volání REST mohou klienti pomocí následujících záhlaví bezpečně předat informace o šifrovacím klíči v požadavku do úložiště objektů Blob:

|Hlavička požadavku | Popis |
|---------------|-------------|
|`x-ms-encryption-key` |Povinné pro požadavky na zápis i čtení. Hodnota šifrovacího klíče AES-256 s kódováním Base64. |
|`x-ms-encryption-key-sha256`| Povinné pro požadavky na zápis i čtení. Base64 kódované SHA256 šifrovacího klíče. |
|`x-ms-encryption-algorithm` | Požadováno pro požadavky na zápis, volitelné pro požadavky na čtení. Určuje algoritmus, který se má použít při šifrování dat pomocí daného klíče. Musí být AES256. |

Zadání šifrovacích klíčů v požadavku je volitelné. Pokud však zadáte jednu z výše uvedených hlaviček pro operaci zápisu, musíte zadat všechny z nich.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operace úložiště objektů blob podporující klíče poskytované zákazníkem

Následující operace úložiště objektů Blob podporují odesílání šifrovacích klíčů poskytnutých zákazníkem na vyžádání:

- [Vložení objektu blob](/rest/api/storageservices/put-blob)
- [Seznam blokovaných položek](/rest/api/storageservices/put-block-list)
- [Umístit blok](/rest/api/storageservices/put-block)
- [Umístit blok z adresy URL](/rest/api/storageservices/put-block-from-url)
- [Umístit stránku](/rest/api/storageservices/put-page)
- [Umístit stránku z adresy URL](/rest/api/storageservices/put-page-from-url)
- [Připojit blok](/rest/api/storageservices/append-block)
- [Nastavení vlastností objektu blob](/rest/api/storageservices/set-blob-properties)
- [Nastavení metadat objektu blob](/rest/api/storageservices/set-blob-metadata)
- [Získat blob](/rest/api/storageservices/get-blob)
- [Získání vlastností objektu blob](/rest/api/storageservices/get-blob-properties)
- [Získání metadat objektů blob](/rest/api/storageservices/get-blob-metadata)
- [Pořízení snímku objektu blob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Otočit klíče poskytované zákazníkem

Chcete-li otočit šifrovací klíč, který byl použit k šifrování objektu blob, stáhněte objekt blob a znovu jej nahrajte pomocí nového šifrovacího klíče.

> [!IMPORTANT]
> Portál Azure nelze použít ke čtení z nebo zápisu do kontejneru nebo objektu blob, který je zašifrován s klíčem k dispozici na žádost.
>
> Nezapomeňte chránit šifrovací klíč, který zadáte na základě požadavku na úložiště objektů Blob v zabezpečeném úložišti klíčů, jako je Azure Key Vault. Pokud se pokusíte o operaci zápisu na kontejner nebo objekt blob bez šifrovacího klíče, operace se nezdaří a ztratíte přístup k objektu.

## <a name="next-steps"></a>Další kroky

- [Určení klíče poskytnutého zákazníkem pro požadavek na úložiště objektů Blob s rozhraním .NET](../blobs/storage-blob-customer-provided-key.md)
- [Šifrování Azure Storage pro data v klidovém stavu](storage-service-encryption.md)
