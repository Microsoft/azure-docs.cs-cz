---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/18/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 1950e55cf669ea7502e8523d7f8fe429c2caeb49
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69903904"
---
Azure Blob Storage je řešení úložiště objektů Microsoftu pro cloud. Úložiště objektů blob je optimalizované pro ukládání velkých objemů nestrukturovaných dat. Nestrukturovaná data jsou data, která nevyhovují konkrétnímu datovému modelu nebo definici, jako jsou textová nebo binární data.

## <a name="about-blob-storage"></a>O službě BLOB Storage

Úložiště objektů blob je navržena pro:

* Poskytování obrázků nebo dokumentů přímo do prohlížeče
* Ukládání souborů pro distribuovaný přístup
* Streamování videa a zvuku
* Zápis do souborů protokolů
* Ukládání dat pro zálohování a obnovování, zotavení po havárii a pro archivaci
* Ukládání dat, které bude analyzovat místní nebo v Azure hostovaná služba

Uživatelé nebo klientské aplikace mají přístup k objektům v úložišti objektů BLOB přes HTTP/HTTPS odkudkoli na světě. Objekty v úložišti objektů BLOB jsou přístupné prostřednictvím [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure POWERSHELL](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage)nebo Azure Storage klientské knihovny. Klientské knihovny jsou dostupné pro celou řadu jazyků, včetně [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [Node. js](https://azure.github.io/azure-storage-node), [Pythonu](https://docs.microsoft.com/python/azure/), [přechodu](https://github.com/azure/azure-storage-blob-go/), [php](https://azure.github.io/azure-storage-php/)a [Ruby](https://azure.github.io/azure-storage-ruby).

## <a name="about-azure-data-lake-storage-gen2"></a>O Azure Data Lake Storage Gen2

Úložiště objektů blob podporuje Azure Data Lake Storage Gen2, řešení Microsoftu pro analýzy velkých objemů podnikových dat pro cloud. Azure Data Lake Storage Gen2 nabízí hierarchický systém souborů a také výhody úložiště objektů blob, včetně cenově úsporného vrstveného úložiště; vysoké dostupnosti; silné konzistence a funkcí zotavení po havárii.

Další informace o Data Lake Storage Gen2 najdete v tématu [Úvod do Azure Data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).
