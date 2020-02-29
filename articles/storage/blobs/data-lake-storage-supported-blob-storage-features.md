---
title: Funkce BLOB Storage dostupné v Azure Data Lake Storage Gen2 | Microsoft Docs
description: Zjistěte, které funkce služby Blob Storage můžete použít s Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 215244204aa58cc2fdedc639d48e01b514759694
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196004"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Funkce BLOB Storage dostupné v Azure Data Lake Storage Gen2

Blob Storage funkce, jako je [protokolování diagnostiky](../common/storage-analytics-logging.md), [úrovně přístupu](storage-blob-storage-tiers.md)a [zásady správy životního cyklu BLOB Storage](storage-lifecycle-management-concepts.md) , teď fungují s účty, které mají hierarchický obor názvů. Proto můžete v účtech BLOB Storage povolit hierarchické obory názvů bez ztráty přístupu k těmto funkcím.

V této tabulce jsou uvedené funkce služby Blob Storage, které můžete použít s Azure Data Lake Storage Gen2. Položky, které se zobrazí v těchto tabulkách, se v průběhu času změní, dokud je podpora stále rozbalená.

## <a name="supported-blob-storage-features"></a>Podporované funkce služby Blob Storage

> [!NOTE]
> Úroveň podpory odkazuje jenom na to, jak je funkce podporovaná s Data Lake Storage Gen2.

|Blob Storage funkce |Úroveň podpory |Související články |
|---------------|-------------------|---|
|Horká vrstva přístupu|Obecně dostupná|[Azure Blob Storage: Horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md)|
|Studená vrstva přístupu|Obecně dostupná|[Azure Blob Storage: Horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md)|
|Události|Obecně dostupná|[Reakce na události služby Blob Storage](storage-blob-event-overview.md)|
|Metriky (klasické)|Obecně dostupná|[Metriky Azure Storage Analytics (klasické)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metriky na platformě Azure Monitor|Obecně dostupná|[Metriky Azure Storage ve službě Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Příkazy prostředí PowerShell pro úložiště objektů BLOB|Obecně dostupná|[Rychlý Start: nahrání, stažení a výpis objektů BLOB pomocí PowerShellu](storage-quickstart-blobs-powershell.md)|
|Příkazy Azure CLI služby Blob Storage|Obecně dostupná|[Rychlý Start: vytvoření, stažení a výpis objektů BLOB pomocí Azure CLI](storage-quickstart-blobs-cli.md)|
|Úložiště objektů BLOB rozhraní API|Obecně dostupná|[Rychlý Start: Klientská knihovna pro úložiště objektů BLOB v Azure V12 pro .NET](storage-quickstart-blobs-dotnet.md)<br>[Rychlý Start: Správa objektů BLOB pomocí sady Java V12 SDK](storage-quickstart-blobs-java.md)<br>[Rychlý Start: Správa objektů BLOB pomocí sady Python V12 SDK](storage-quickstart-blobs-python.md)<br>[Rychlý Start: Správa objektů BLOB pomocí sady JavaScript V12 SDK v Node. js](storage-quickstart-blobs-nodejs.md)|
|Archivní úroveň přístupu|Preview|[Azure Blob Storage: Horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md)|
|Zásady správy životního cyklu|Preview|[Správa životního cyklu úložiště objektů blob v Azure](storage-lifecycle-management-concepts.md)|
|Diagnostické protokoly|Preview|[Protokolování analýzy Azure Storage](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Změna kanálu|Zatím nepodporováno|[Změna podpory kanálu ve službě Azure Blob Storage](storage-blob-change-feed.md)|
|Převzetí služeb při selhání účtu|Zatím nepodporováno|[Zotavení po havárii a převzetí služeb při selhání účtu](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Seznam ACL kontejneru objektů BLOB|Zatím nepodporováno|[Nastavení seznamu ACL kontejneru](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Vlastní domény|Zatím nepodporováno|[Mapování vlastní domény na koncový bod služby Azure Blob Storage](storage-custom-domain-name.md)|
|Neměnné úložiště|Zatím nepodporováno|[Ukládání důležitých podnikových dat objektů BLOB s neměnném úložištěm](storage-blob-immutable-storage.md)|
|Snímky|Zatím nepodporováno|[Vytvoření a Správa snímku objektu BLOB v .NET](storage-blob-snapshots.md)|
|Obnovitelné odstranění|Zatím nepodporováno|[Obnovitelné odstranění objektů blob služby Azure Storage](storage-blob-soft-delete.md)|
|Statické weby|Zatím nepodporováno|[Hostování statického webu ve službě Azure Storage](storage-blob-static-website.md)|
|Přihlašování Azure Monitor|Zatím nepodporováno|Ještě není k dispozici|
|Objekty blob bloku úrovně Premium|Zatím nepodporováno|[Vytvoření účtu BlockBlobStorage](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>Viz také

- [Známé problémy s Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Open Source platformy, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Přístup k více protokolům na Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)