---
title: Funkce úložiště objektů blob dostupné v Azure Data Lake Storage Gen2 | Dokumenty společnosti Microsoft
description: Informace o tom, které funkce úložiště objektů Blob můžete používat s Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 215244204aa58cc2fdedc639d48e01b514759694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196004"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Funkce úložiště objektů blob dostupné v Azure Data Lake Storage Gen2

Funkce úložiště objektů blob, jako je [diagnostické protokolování](../common/storage-analytics-logging.md), [úrovně přístupu](storage-blob-storage-tiers.md)a [zásady správy životního cyklu úložiště blob,](storage-lifecycle-management-concepts.md) teď fungují s účty, které mají hierarchický obor názvů. Proto můžete povolit hierarchické obory názvů na účtech úložiště objektů Blob bez ztráty přístupu k těmto funkcím.

V této tabulce jsou uvedeny funkce úložiště objektů blob, které můžete použít s Azure Data Lake Storage Gen2. Položky, které se zobrazí v těchto tabulkách se bude měnit v průběhu času jako podpora nadále rozšiřovat.

## <a name="supported-blob-storage-features"></a>Podporované funkce úložiště objektů Blob

> [!NOTE]
> Úroveň podpory odkazuje pouze na to, jak je funkce podporována pomocí data lake storage gen2.

|Funkce úložiště objektů blob |Úroveň podpory |Související články |
|---------------|-------------------|---|
|Horká vrstva přístupu|Obecná dostupnost|[Azure Blob Storage: Horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md)|
|Studená vrstva přístupu|Obecná dostupnost|[Azure Blob Storage: Horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md)|
|Události|Obecná dostupnost|[Reakce na události služby Blob Storage](storage-blob-event-overview.md)|
|Metriky (klasické)|Obecná dostupnost|[Metriky analýzy azure úložiště (classic)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metriky na platformě Azure Monitor|Obecná dostupnost|[Metriky Azure Storage ve službě Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Příkazy PowerShellu pro ukládání objektů blob|Obecná dostupnost|[Úvodní příručka: Nahrávání, stahování a seznam objektů BLOB pomocí PowerShellu](storage-quickstart-blobs-powershell.md)|
|Příkazy Azure CLI úložiště objektů blob|Obecná dostupnost|[Úvodní příručka: Vytváření, stahování a seznam objektů BLOB pomocí rozhraní příkazového příkazového příkazu Azure](storage-quickstart-blobs-cli.md)|
|Api úložiště objektů blob|Obecná dostupnost|[Úvodní příručka: Klientská knihovna úložiště objektů blob Azure v12 pro rozhraní .NET](storage-quickstart-blobs-dotnet.md)<br>[Úvodní příručka: Správa objektů BLOB pomocí sady Java v12 SDK](storage-quickstart-blobs-java.md)<br>[Úvodní příručka: Správa objektů BLOB pomocí pythonu v12 SDK](storage-quickstart-blobs-python.md)<br>[Úvodní příručka: Správa objektů BLOB pomocí sady JavaScript v12 SDK v souboru Node.js](storage-quickstart-blobs-nodejs.md)|
|Archivovat úroveň přístupu|Preview|[Azure Blob Storage: Horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md)|
|Zásady správy životního cyklu|Preview|[Správa životního cyklu úložiště objektů blob v Azure](storage-lifecycle-management-concepts.md)|
|Diagnostické protokoly|Preview|[Protokolování Analýzy úložiště Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Změna kanálu|Dosud není podporováno|[Změna podpory informačního kanálu v úložišti objektů Blob Azure](storage-blob-change-feed.md)|
|Převzetí služeb při selhání účtu|Dosud není podporováno|[Zotavení po havárii a převzetí služeb při selhání účtu](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|ACL kontejneru s objektem blob|Dosud není podporováno|[Nastavit kontejner ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Vlastní domény|Dosud není podporováno|[Mapování vlastní domény na koncový bod úložiště objektů Blob Azure](storage-custom-domain-name.md)|
|Neměnné úložiště|Dosud není podporováno|[Ukládání důležitých dat objektů blob s neměnným úložištěm](storage-blob-immutable-storage.md)|
|Snímky|Dosud není podporováno|[Vytvoření a správa snímku objektu blob v rozhraní .NET](storage-blob-snapshots.md)|
|Obnovitelné odstranění|Dosud není podporováno|[Obnovitelné odstranění objektů blob služby Azure Storage](storage-blob-soft-delete.md)|
|Statické weby|Dosud není podporováno|[Hostování statického webu ve službě Azure Storage](storage-blob-static-website.md)|
|Protokolování v Azure Monitoru|Dosud není podporováno|Zatím nedostupné|
|Prémiové bloky blobů|Dosud není podporováno|[Vytvoření účtu BlockBlobStorage](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>Viz také

- [Známé problémy s Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Open source platformy, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Přístup k více protokolům v úložišti Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)