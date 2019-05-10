---
title: Známé problémy s Azure Data Lake Storage Gen2 | Dokumentace Microsoftu
description: Další informace o omezeních a známých problémech s Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: normesta
ms.openlocfilehash: 27adc0eeeabed2b1f2e86f301a60604a3d358b82
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464720"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Známé problémy s Azure Data Lake Storage Gen2

Tento článek obsahuje seznam funkcí a nástrojů, které není zatím podporována jen částečně podporované s účty úložiště, které mají hierarchického oboru názvů (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>Úložiště objektů BLOB rozhraní API

Úložiště objektů BLOB, které rozhraní API jsou zakázané, aby se zabránilo problémům s přístup zvyšuje ochranu před nechtěnými daty, které mohou nastat, protože zatím nejsou spolupracují se službou Azure Data Lake Gen2 API rozhraní API služby Blob Storage.

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Co dělat s existujícími nástroji, aplikacemi a službami

Pokud některý z těchto použití rozhraní API služby Blob který chcete použít pro práci se veškerý obsah, který nahrajete do vašeho účtu, potom nepovolí hierarchického oboru názvů na svůj účet Blob storage než rozhraní API služby Blob stane spolupracují se službou Azure Data Lake Gen2 rozhraní API.

Použití účtu úložiště bez hierarchického oboru názvů znamená, že pak nebudete mít přístup k Data Lake Storage Gen2 specifické funkce, jako je například adresář a soubor seznamy řízení přístupu k systému.

### <a name="what-to-do-with-unmanaged-virtual-machine-vm-disks"></a>Co dělat s nespravovanými disky virtuálního počítače (VM)

Tyto závisí na zakázaném rozhraní API úložiště objektů Blob, takže pokud chcete povolit hierarchického oboru názvů na účet úložiště, vezměte v úvahu je umístit do účtu úložiště, který nemá povolenou funkci hierarchického oboru názvů.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Co dělat, když jste použili objektů Blob rozhraní API k načtení dat, než bylo zakázáno rozhraní API služby Blob

Pokud jste použili tato rozhraní API k načtení dat, než byly zakázány, aby bylo produkční požadavek na přístup k datům, obraťte se Microsoft Support s následujícími informacemi:

> [!div class="checklist"]
> * ID předplatného (identifikátor GUID, nikoli název).
> * Názvy účtů úložišť.
> * Zda se aktivně to týká v produkčním prostředí a pokud ano, pro které účty úložiště.
> * I když nejsou aktivně vliv v produkčním prostředí, dejte nám vědět, jestli tato data, které se mají zkopírovat do jiného účtu úložiště z nějakého důvodu potřebujete a pokud ano, proč?

Za těchto okolností abychom mohli obnovit přístup k rozhraní API objektů Blob po omezenou dobu tak, aby tato data můžete zkopírovat do účtu úložiště, který nemá povolenou funkci hierarchického oboru názvů.

## <a name="all-other-features-and-tools"></a>Všechny ostatní funkce a nástroje

V následující tabulce jsou uvedeny všechny ostatní funkce a nástroje, které ještě není podporován nebo jen částečně podporované s účty úložiště, které mají hierarchického oboru názvů (Azure Data Lake Storage Gen2).

| Funkce / nástroj    | Další informace    |
|--------|-----------|
| **Rozhraní API pro účty úložiště Data Lake Storage Gen2** | Částečně podporované <br><br>Můžete použít Data Lake Storage Gen2 **REST** rozhraní API, ale rozhraní API v jiných sad SDK objektů Blob, jako jsou sady SDK pro .NET, Java, Python ještě nejsou k dispozici.|
| **AzCopy** | Podpora specifické pro verzi <br><br>Použít pouze nejnovější verzi AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Starší verze nástroje AzCopy například AzCopy v8.1 se nepodporují.|
| **Zásady životního cyklu správy Azure Blob úložiště** | Není dosud podporován. |
| **Azure Content Delivery Network (CDN)** | Není dosud podporován.|
| **Azure Event Grid** | Není dosud podporován. |
| **Služba Azure search** |Není dosud podporován.|
| **Azure Storage Explorer** | Podpora specifické pro verzi <br><br>Použijte pouze verzi `1.6.0` nebo vyšší. <br>Verze `1.6.0` je k dispozici jako [zdarma ke stažení](https://azure.microsoft.com/features/storage-explorer/).|
| **Kontejner objektů BLOB seznamy ACL** |Není dosud podporován.|
| **Blobfuse** |Není dosud podporován.|
| **Vlastní domény** |Není dosud podporován.|
| **Diagnostické protokoly** |Není dosud podporován.|
| **Průzkumníka souborového systému** | Omezená podpora |
| **Neměnné úložiště** |Není dosud podporován. <br><br>Neměnné storage poskytuje možnost ukládat data [ČERV (zápis, mnoho čtení)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) stavu.|
| **Vrstvy na úrovni objektů** |Není dosud podporován. <br><br>Příklad: Premium, horké a studené a archivní úrovně.|
| **Podpora prostředí PowerShell a rozhraní příkazového řádku** | Omezená funkčnost <br><br>Účet můžete vytvořit pomocí Powershellu nebo rozhraní příkazového řádku. Nelze provádět operace, nebo nastavte seznamy řízení přístupu na systémy souborů, adresářů a souborů.|
| **Statických webů** |Není dosud podporován. <br><br>Konkrétně schopnost poskytovat soubory [statických webů](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Aplikací třetích stran.** | Omezená podpora <br><br>Aplikací jiných výrobců, které používají rozhraní REST API pro práci se budou nadále fungovat, pokud je používáte s Data Lake Storage Gen2. <br>Pokud máte aplikaci, která používá rozhraní API služby Blob, tato aplikace pravděpodobně mít problémy, pokud použijete tuto aplikaci a Data Lake Storage Gen2. Další informace najdete v tématu [Blob storage jsou zakázané rozhraní API pro účty úložiště Data Lake Storage Gen2](#blob-apis-disabled) části tohoto článku.|
| **Funkce správy verzí** |Není dosud podporován. <br><br>Jedná se o [snímky](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) a [obnovitelné odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|
|

