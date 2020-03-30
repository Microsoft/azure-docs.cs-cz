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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136014"
---
Azure Blob Storage je řešení úložiště objektů Microsoftu pro cloud. Úložiště objektů blob je optimalizované pro ukládání velkých objemů nestrukturovaných dat. Nestrukturovaná data jsou data, která nedodržují určitý datový model nebo definici, například text nebo binární data.

## <a name="about-blob-storage"></a>Informace o službě Blob Storage

Úložiště objektů blob je navržena pro:

* Poskytování obrázků nebo dokumentů přímo do prohlížeče
* Ukládání souborů pro distribuovaný přístup
* Streamování videa a zvuku
* Zápis do souborů protokolů
* Ukládání dat pro zálohování a obnovování, zotavení po havárii a pro archivaci
* Ukládání dat, které bude analyzovat místní nebo v Azure hostovaná služba

Uživatelé nebo klientské aplikace mohou přistupovat k objektům v úložišti objektů Blob prostřednictvím protokolu HTTP/HTTPS z libovolného místa na světě. Objekty v úložišti objektů Blob jsou přístupné prostřednictvím [rozhraní AZURE Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), Azure [PowerShellu](https://docs.microsoft.com/powershell/module/azure.storage), [rozhraní API Azure](https://docs.microsoft.com/cli/azure/storage)nebo klientské knihovny Azure Storage. Klientské knihovny jsou k dispozici pro různé jazyky, včetně:

* [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.js](https://azure.github.io/azure-storage-node) 
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Přejít](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>O službě Azure Data Lake Storage Gen2

Úložiště objektů blob podporuje Azure Data Lake Storage Gen2, řešení Microsoftu pro analýzy velkých objemů podnikových dat pro cloud. Azure Data Lake Storage Gen2 nabízí hierarchický systém souborů a výhody úložiště objektů Blob, včetně:

* Nízkonákladové vrstvené úložiště
* Vysoká dostupnost
* Silná konzistence
* Možnosti zotavení po havárii

Další informace o úložišti datových jezer Gen2 najdete [v tématu Úvod do Azure Data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).
