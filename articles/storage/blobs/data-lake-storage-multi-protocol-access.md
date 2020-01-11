---
title: Přístup k více protokolům na Azure Data Lake Storage | Microsoft Docs
description: Používejte rozhraní API objektů BLOB a aplikace, které používají rozhraní API objektů BLOB s Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f34c5d5069a158579864320d0fbf965de8936d9c
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896116"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Přístup k více protokolům na Azure Data Lake Storage

Rozhraní API objektů BLOB teď pracují s účty, které mají hierarchický obor názvů. Tím se odemkne ekosystém nástrojů, aplikací a služeb a také několik funkcí BLOB Storage pro účty, které mají hierarchický obor názvů.

Až do poslední doby, možná budete muset zachovat samostatná řešení úložiště pro úložiště objektů a analýzu. To je proto, že Azure Data Lake Storage Gen2 mít omezené podpory ekosystémů. Měl by taky mít omezený přístup k Blob service funkcím, jako je protokolování diagnostiky. Fragmentované řešení úložiště je obtížné udržovat, protože k provádění různých scénářů je potřeba přesunout data mezi účty. Už to nemusíte dělat.

Při přístupu k více protokolům na Data Lake Storage můžete pracovat s daty pomocí ekosystému nástrojů, aplikací a služeb. To zahrnuje i nástroje a aplikace třetích stran. Můžete je nasměrovat na účty, které mají hierarchický obor názvů, aniž byste je museli upravovat. Tyto aplikace fungují *tak, jak jsou* , i když volají rozhraní API objektů blob, protože rozhraní API objektů BLOB teď můžou pracovat s daty v účtech, které mají hierarchický obor názvů.

Funkce služby Blob Storage, jako je [protokolování diagnostiky](../common/storage-analytics-logging.md), [úrovně přístupu](storage-blob-storage-tiers.md)a [zásady správy životního cyklu úložiště objektů BLOB](storage-lifecycle-management-concepts.md) , teď fungují s účty, které mají hierarchický obor názvů. Proto můžete v účtech BLOB Storage povolit hierarchické obory názvů bez ztráty přístupu k těmto důležitým funkcím. 

> [!NOTE]
> Přístup k více protokolům na Data Lake Storage je všeobecně dostupný a je k dispozici ve všech oblastech. Některé služby Azure nebo funkce úložiště objektů blob, které jsou povolené přístupem přes protokol, zůstávají ve verzi Preview. Další informace najdete v tabulkách v jednotlivých částech tohoto článku. 

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Jak funguje přístup k více protokolům v Data Lake Storage

Rozhraní API objektů BLOB a rozhraní API pro Data Lake Storage Gen2 můžou pracovat se stejnými daty v účtech úložiště, které mají hierarchický obor názvů. Data Lake Storage Gen2 směruje rozhraní API objektů BLOB prostřednictvím hierarchického oboru názvů tak, aby bylo možné získat výhody první třídy operace s adresáři a seznamy řízení přístupu (ACL) kompatibilní se standardem POSIX. 

![Přístup k více protokolům na Data Lake Storage koncepční](./media/data-lake-storage-interop/interop-concept.png) 

Stávající nástroje a aplikace, které využívají rozhraní BLOB API, získají tyto výhody automaticky. Vývojáři je nebudou muset upravovat. Data Lake Storage Gen2 konzistentně používá adresáře a seznamy ACL na úrovni souborů bez ohledu na protokol, který nástroje a aplikace používají pro přístup k datům. 

## <a name="blob-storage-feature-support"></a>Podpora funkcí úložiště objektů BLOB

Přístup k více protokolům na Data Lake Storage umožňuje používat více funkcí úložiště BLOB ve vašich Data Lake Storage. Tato tabulka obsahuje seznam funkcí, které jsou povolené přístupem přes protokol s více protokoly na Data Lake Storage. 

Položky, které se zobrazují v této tabulce, se v průběhu času změní, protože podpora funkcí BLOB Storage se dál rozšiřuje. 

> [!NOTE]
> I když je přístup k více protokolům na Data Lake Storage všeobecně dostupný, podpora některých z těchto funkcí zůstává ve verzi Preview. 

|Funkce BLOB Storage | Úroveň podpory |
|---|---|
|[Studená úroveň přístupu](storage-blob-storage-tiers.md)|Obecně dostupná|
|Rozhraní REST API pro objekty blob|Obecně dostupná|
|Sady SDK objektů BLOB |Obecně dostupná|
|[PowerShell (BLOB)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell) |Obecně dostupná|
|[CLI (BLOB)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-cli) |Obecně dostupná|
|[Oznámení prostřednictvím Azure Event Grid](data-lake-storage-events.md)|Obecně dostupná|
|Sady SDK objektů BLOB se sémantikou systému souborů ([.net](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-dotnet) &vert; [Python](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-python) &vert; [Java](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-java))|Preview|
|[PowerShell se sémantikou systému souborů](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-powershell)|Preview|
|[Rozhraní příkazového řádku se sémantikou systému souborů](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-cli)|Preview|
|[Diagnostické protokoly](../common/storage-analytics-logging.md)| Preview|
|[Zásady správy životního cyklu](storage-lifecycle-management-concepts.md)| Preview|
|[Archivní úroveň přístupu](storage-blob-storage-tiers.md)| Preview|
|[blobfuse](storage-how-to-mount-container-linux.md)|Zatím nepodporováno|
|[Neměnné úložiště](storage-blob-immutable-storage.md)|Zatím nepodporováno|
|[Snímky](storage-blob-snapshots.md)|Zatím nepodporováno|
|[Obnovitelné odstranění](storage-blob-soft-delete.md)|Zatím nepodporováno|
|[Statické weby](storage-blob-static-website.md)|Zatím nepodporováno|

Další informace o obecných známých problémech a omezeních Azure Data Lake Storage Gen2 najdete v tématu [známé problémy](data-lake-storage-known-issues.md).

## <a name="azure-ecosystem-support"></a>Podpora ekosystému Azure

Přístup s více protokoly na Data Lake Storage taky umožňuje připojit k Data Lake Storage další služby Azure. Tato tabulka uvádí služby, které jsou povolené přístupem s více protokoly na Data Lake Storage. 

Stejně jako v seznamu podporovaných funkcí BLOB Storage se položky, které se zobrazují v této tabulce, mění v čase, protože se podpora služeb Azure dál rozšiřuje. 

> [!NOTE]
> I když je přístup k více protokolům na Data Lake Storage všeobecně dostupný, podpora některých z těchto služeb zůstane ve verzi Preview. 

|Služba Azure | Úroveň podpory |
|---|---|
|[Azure Data Box](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|Obecně dostupná|
|[Azure Event Hubs Capture](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|Obecně dostupná|
|[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal)|Obecně dostupná|
|[IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|Obecně dostupná|
|[Logic Apps](https://azure.microsoft.com/services/logic-apps/)|Obecně dostupná|
|[Kognitivní hledání Azure](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|Preview|

Úplný seznam podpory Azure ekosystému pro Data Lake Storage Gen2 najdete v tématu věnovaném [integraci Azure Data Lake Storage se službami Azure](data-lake-storage-integrate-with-azure-services.md).

Další informace o obecných známých problémech a omezeních Azure Data Lake Storage Gen2 najdete v tématu [známé problémy](data-lake-storage-known-issues.md).

## <a name="next-steps"></a>Další kroky

Zobrazit [známé problémy](data-lake-storage-known-issues.md)




