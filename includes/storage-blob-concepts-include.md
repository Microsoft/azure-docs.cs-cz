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
ms.openlocfilehash: 32b9b12c2adf03a4cb0616a5da48dd33fc81fb4f
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52973084"
---
Azure Blob Storage je řešení úložiště objektů Microsoftu pro cloud. Úložiště objektů blob je optimalizovaná pro ukládání velkých objemů nestrukturovaných dat. Nestrukturovaných dat jsou data, která nedrží se konkrétního datového modelu nebo definice, jako jsou textová nebo binární data. 

## <a name="about-blob-storage"></a>O službě Blob storage

BLOB storage je navržená pro:

* Poskytování obrázků nebo dokumentů přímo do prohlížeče
* Ukládání souborů pro distribuovaný přístup
* Streamování videa a zvuku
* Zápis do souborů protokolů
* Ukládání dat pro zálohování a obnovování, zotavení po havárii a pro archivaci
* Ukládání dat, které bude analyzovat místní nebo v Azure hostovaná služba

Uživatelé nebo klientské aplikace můžete přístup k objektům ve službě Blob storage přes HTTP/HTTPS, z kdekoli na světě. Objekty ve službě Blob storage jsou přístupná přes [REST API služby Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/storage), nebo knihovny klienta služby Azure Storage. Klientské knihovny jsou dostupné pro různé jazyky, včetně [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://docs.microsoft.com/python/azure/), [přejít ](https://github.com/azure/azure-storage-blob-go/), [PHP](http://azure.github.io/azure-storage-php/), a [Ruby](http://azure.github.io/azure-storage-ruby).

## <a name="about-azure-data-lake-storage-gen2"></a>Informace o Azure Data Lake Storage Gen2 

Podporuje úložiště objektů BLOB v Azure Data Lake Storage Gen2, řešení analýzy velkých objemů dat společnosti Microsoft enterprise pro cloud. Azure Data Lake Storage Gen2 nabízí hierarchické souboru systému, jakož i výhody úložiště Blob Storage, včetně s nízkými náklady, vrstveného úložiště; Vysoká dostupnost; Silná konzistence; a možnosti zotavení po havárii. 

Další informace o Data Lake Storage Gen2 najdete v tématu [Úvod do služby Azure Data Lake Storage Gen2 Preview](../articles/storage/data-lake-storage/introduction.md).