---
title: Funkce BLOB Storage dostupné v Azure Data Lake Storage Gen2 | Microsoft Docs
description: Zjistěte, které funkce služby Blob Storage můžete použít s Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 5be7c9800076cfaf361a3e752162e14d10f22655
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650754"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Funkce BLOB Storage dostupné v Azure Data Lake Storage Gen2

Blob Storage funkce, jako je [protokolování diagnostiky](../common/storage-analytics-logging.md), [úrovně přístupu](storage-blob-storage-tiers.md)a [zásady správy životního cyklu BLOB Storage](storage-lifecycle-management-concepts.md) , teď fungují s účty, které mají hierarchický obor názvů. Proto můžete v účtech BLOB Storage povolit hierarchické obory názvů bez ztráty přístupu k těmto funkcím.

V této tabulce jsou uvedené funkce služby Blob Storage, které můžete použít s Azure Data Lake Storage Gen2. Položky, které se zobrazí v těchto tabulkách, se v průběhu času změní, dokud je podpora stále rozbalená. Další informace o konkrétních problémech přidružených ke stavu verze Preview funkce najdete v článku [známé problémy](data-lake-storage-known-issues.md) .

## <a name="supported-blob-storage-features"></a>Podporované funkce Blob Storage

> [!NOTE]
> Úroveň podpory odkazuje jenom na to, jak je funkce podporovaná s Data Lake Storage Gen2. 
>
> [Účty BlockBlobStorage úrovně Premium-Performance](storage-blob-create-account-block-blob.md) pro data Lake Storage Gen2 jsou momentálně ve verzi Public Preview. Úrovně podpory pro tyto typy účtů se zobrazí ve sloupci **BlockBlobStorage (Premium)** .

|Blob Storage funkce |Obecné účely v2 |BlockBlobStorage (Premium) |Související články |
|---------------|-------------------|---|
|Horká vrstva přístupu|Obecná dostupnost|Nepodporuje se|[Azure Blob Storage: Horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md)|
|Studená vrstva přístupu|Obecná dostupnost|Nepodporuje se|[Azure Blob Storage: Horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md)|
|Události|Obecná dostupnost|Preview|[Reakce na události služby Blob Storage](storage-blob-event-overview.md)|
|Metriky (klasické)|Obecná dostupnost|Nepodporuje se|[Metriky Azure Storage Analytics (klasické)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metriky na platformě Azure Monitor|Obecná dostupnost|Preview|[Azure Storage metriky v Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Příkazy prostředí PowerShell pro úložiště objektů BLOB|Obecná dostupnost|Preview|[Rychlý Start: nahrání, stažení a výpis objektů BLOB pomocí PowerShellu](storage-quickstart-blobs-powershell.md)|
|Příkazy Azure CLI služby Blob Storage|Obecná dostupnost|Preview|[Rychlý Start: vytvoření, stažení a výpis objektů BLOB pomocí Azure CLI](storage-quickstart-blobs-cli.md)|
|Rozhraní API pro úložiště objektů BLOB|Obecná dostupnost|Preview|[Rychlý Start: Klientská knihovna pro úložiště objektů BLOB v Azure V12 pro .NET](storage-quickstart-blobs-dotnet.md)<br>[Rychlý Start: Správa objektů BLOB pomocí sady Java V12 SDK](storage-quickstart-blobs-java.md)<br>[Rychlý Start: Správa objektů BLOB pomocí sady Python V12 SDK](storage-quickstart-blobs-python.md)<br>[Rychlý Start: Správa objektů BLOB pomocí sady JavaScript V12 SDK v Node. js](storage-quickstart-blobs-nodejs.md)|
|Diagnostické protokoly|Obecná dostupnost|Preview <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Protokolování analýz služby Azure Storage](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Přihlašování Azure Monitor|Preview |Zatím nepodporováno|[Azure Storage monitorování](../common/monitor-storage.md)|
|Archivní úroveň přístupu|Preview|Nepodporuje se|[Azure Blob Storage: Horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md)|
|Zásady správy životního cyklu|Preview|Zatím nepodporováno|[Správa životního cyklu úložiště objektů blob v Azure](storage-lifecycle-management-concepts.md)|
|Změna kanálu|Zatím nepodporováno|Zatím nepodporováno|[Změna podpory kanálu ve službě Azure Blob Storage](storage-blob-change-feed.md)|
|Převzetí služeb při selhání účtu|Zatím nepodporováno|Zatím nepodporováno|[Zotavení po havárii a převzetí služeb při selhání účtu](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Seznam ACL kontejneru objektů BLOB|Zatím nepodporováno|Zatím nepodporováno|[Nastavení seznamu ACL kontejneru](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Vlastní domény|Zatím nepodporováno|Zatím nepodporováno|[Mapování vlastní domény na koncový bod služby Azure Blob Storage](storage-custom-domain-name.md)|
|Neměnné úložiště|Zatím nepodporováno|Zatím nepodporováno|[Ukládání důležitých podnikových dat objektů BLOB s neměnném úložištěm](storage-blob-immutable-storage.md)|
|Snímky|Zatím nepodporováno|Zatím nepodporováno|[Vytvoření a Správa snímku objektu BLOB v .NET](storage-blob-snapshots.md)|
|Obnovitelné odstranění|Zatím nepodporováno|Zatím nepodporováno|[Obnovitelné odstranění objektů blob služby Azure Storage](storage-blob-soft-delete.md)|
|Statické weby|Zatím nepodporováno|Zatím nepodporováno|[Hostování statického webu ve službě Azure Storage](storage-blob-static-website.md)|

<div id="diagnostic-logging"><sup>1</sup> Pro účty úložiště blob bloku úrovně Premium není možné povolit diagnostické protokoly (Classic) pomocí Azure Portal. Povolte je pomocí PowerShellu.</div>

## <a name="see-also"></a>Viz také

- [Známé problémy s Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Open Source platformy, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Přístup k více protokolům na Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)