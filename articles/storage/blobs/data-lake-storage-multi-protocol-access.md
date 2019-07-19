---
title: Přístup k více protokolům na Azure Data Lake Storage | Microsoft Docs
description: Používejte rozhraní API objektů BLOB a aplikace, které používají rozhraní API objektů BLOB s Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: normesta
ms.openlocfilehash: f384fb738fe719b8e622e8d61502e6acba2bbf31
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314370"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Přístup k více protokolům na Azure Data Lake Storage

Rozhraní API objektů BLOB teď pracují s účty, které mají hierarchický obor názvů. Tím se odemkne celý ekosystém nástrojů, aplikací a služeb a také všechny funkce služby Blob Storage pro účty, které mají hierarchický obor názvů.

Až do poslední doby, možná budete muset zachovat samostatná řešení úložiště pro úložiště objektů a analýzu. To je proto, že Azure Data Lake Storage Gen2 mít omezené podpory ekosystémů. Měl by taky mít omezený přístup k Blob service funkcím, jako je protokolování diagnostiky. Fragmentované řešení úložiště je obtížné udržovat, protože k provádění různých scénářů je potřeba přesunout data mezi účty. Už to nemusíte dělat.

> [!NOTE]
> Přístup k více protokolům na Data Lake Storage je ve verzi Public Preview a je dostupný jenom v oblastech **západní USA 2** a **středozápadní USA** . Chcete-li zkontrolovat omezení, přečtěte si článek [známé problémy](data-lake-storage-known-issues.md) . Pokud se chcete zaregistrovat ve verzi Preview, podívejte se na [tuto stránku](https://aka.ms/blobinteropsignup).

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>Používání celého ekosystému aplikací, nástrojů a služeb

Pokud se zaregistrujete do verze Preview přístupu k více protokolům na Data Lake Storage, můžete pracovat se všemi Vašimi daty pomocí celého ekosystému nástrojů, aplikací a služeb. Patří sem služby Azure, jako jsou [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction), [IOT hub](https://docs.microsoft.com/azure/iot-hub/), [Power BI](https://docs.microsoft.com/power-bi/desktop-data-sources)a spousta dalších. 

To zahrnuje i nástroje a aplikace třetích stran. Můžete je nasměrovat na účty, které mají hierarchický obor názvů, aniž byste je museli upravovat. Tyto aplikace fungují *tak, jak jsou* , i když volají rozhraní API objektů blob, protože rozhraní API objektů BLOB teď můžou pracovat s daty v účtech, které mají hierarchický obor názvů.

> [!NOTE]
> Chcete-li zkontrolovat omezení, přečtěte si článek [známé problémy](data-lake-storage-known-issues.md) .

## <a name="use-all-blob-storage-features"></a>Používání všech funkcí služby Blob Storage

Funkce služby Blob Storage, jako je [protokolování diagnostiky](../common/storage-analytics-logging.md), [úrovně přístupu](storage-blob-storage-tiers.md)a [zásady správy životního cyklu úložiště objektů BLOB](storage-lifecycle-management-concepts.md) , teď fungují s účty, které mají hierarchický obor názvů. Proto můžete v účtech BLOB Storage povolit hierarchické obory názvů bez přístupu k těmto důležitým funkcím ztrátě. 

> [!NOTE]
> Chcete-li zkontrolovat omezení, přečtěte si článek [známé problémy](data-lake-storage-known-issues.md) .

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Jak funguje přístup k více protokolům v Data Lake Storage

Rozhraní API objektů BLOB a rozhraní API pro Data Lake Storage Gen2 můžou pracovat se stejnými daty v účtech úložiště, které mají hierarchický obor názvů. Data Lake Storage Gen2 směruje rozhraní API objektů BLOB prostřednictvím hierarchického oboru názvů tak, aby bylo možné získat výhody první třídy operace s adresáři a seznamy řízení přístupu (ACL) kompatibilní se standardem POSIX. 

![Přístup k více protokolům na Data Lake Storage koncepční](./media/data-lake-storage-interop/interop-concept.png) 

Stávající nástroje a aplikace, které využívají rozhraní BLOB API, získají tyto výhody automaticky. Vývojáři je nebudou muset upravovat. Data Lake Storage Gen2 konzistentně používá adresáře a seznamy ACL na úrovni souborů bez ohledu na protokol, který nástroje a aplikace používají pro přístup k datům.   

## <a name="next-steps"></a>Další postup

Zobrazit [známé problémy](data-lake-storage-known-issues.md)




