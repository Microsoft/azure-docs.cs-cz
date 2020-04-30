---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/05/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e90e750bf248bdcc8e50c6ddc6e9fa0273660195
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "79136014"
---
Azure Blob Storage je řešení úložiště objektů Microsoftu pro cloud. Úložiště objektů blob je optimalizované pro ukládání velkých objemů nestrukturovaných dat. Nestrukturovaná data jsou data, která nevyhovují konkrétnímu datovému modelu nebo definici, jako jsou textová nebo binární data.

## <a name="about-blob-storage"></a>Informace o službě Blob Storage

Úložiště objektů blob je navržena pro:

* Poskytování obrázků nebo dokumentů přímo do prohlížeče
* Ukládání souborů pro distribuovaný přístup
* Streamování videa a zvuku
* Zápis do souborů protokolů
* Ukládání dat pro zálohování a obnovování, zotavení po havárii a pro archivaci
* Ukládání dat, které bude analyzovat místní nebo v Azure hostovaná služba

Uživatelé nebo klientské aplikace mají přístup k objektům v úložišti objektů BLOB přes HTTP/HTTPS odkudkoli na světě. Objekty v úložišti objektů BLOB jsou přístupné prostřednictvím [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure POWERSHELL](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage)nebo Azure Storage klientské knihovny. Klientské knihovny jsou k dispozici pro různé jazyky, včetně:

* [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.js](https://azure.github.io/azure-storage-node) 
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Přejít](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>O Azure Data Lake Storage Gen2

Úložiště objektů blob podporuje Azure Data Lake Storage Gen2, řešení Microsoftu pro analýzy velkých objemů podnikových dat pro cloud. Azure Data Lake Storage Gen2 nabízí hierarchický systém souborů i výhody služby Blob Storage, včetně:

* Nízké náklady, vrstvené úložiště
* Vysoká dostupnost
* Silná konzistence
* Možnosti zotavení po havárii

Další informace o Data Lake Storage Gen2 najdete v tématu [Úvod do Azure Data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).
