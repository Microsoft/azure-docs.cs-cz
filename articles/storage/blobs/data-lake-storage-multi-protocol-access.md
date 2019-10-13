---
title: Přístup k více protokolům na Azure Data Lake Storage (Preview) | Microsoft Docs
description: Používejte rozhraní API objektů BLOB a aplikace, které používají rozhraní API objektů BLOB s Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 9767282b3dd764a45f25a14d62af70a13c80b0ac
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300271"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage-preview"></a>Přístup k více protokolům na Azure Data Lake Storage (verze Preview)

Rozhraní API objektů BLOB teď pracují s účty, které mají hierarchický obor názvů. Tím se odemkne celý ekosystém nástrojů, aplikací a služeb a také všechny funkce služby Blob Storage pro účty, které mají hierarchický obor názvů.

Až do poslední doby, možná budete muset zachovat samostatná řešení úložiště pro úložiště objektů a analýzu. To je proto, že Azure Data Lake Storage Gen2 mít omezené podpory ekosystémů. Měl by taky mít omezený přístup k Blob service funkcím, jako je protokolování diagnostiky. Fragmentované řešení úložiště je obtížné udržovat, protože k provádění různých scénářů je potřeba přesunout data mezi účty. Už to nemusíte dělat.

> [!NOTE]
> Přístup k více protokolům na Data Lake Storage je ve verzi Public Preview a je dostupný ve všech oblastech. Nemusíte se registrovat ve verzi Public Preview, protože je automaticky dostupná pro všechny účty, které mají hierarchický obor názvů. Chcete-li zkontrolovat omezení, přečtěte si článek [známé problémy](data-lake-storage-known-issues.md) .

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>Používání celého ekosystému aplikací, nástrojů a služeb

Při přístupu k více protokolům na Data Lake Storage můžete pracovat se všemi Vašimi daty pomocí celého ekosystému nástrojů, aplikací a služeb. Patří sem služby Azure, jako jsou [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction), [IOT hub](https://docs.microsoft.com/azure/iot-hub/), [Power BI](https://docs.microsoft.com/power-bi/desktop-data-sources)a spousta dalších. Úplný seznam najdete v tématu věnovaném [integraci Azure Data Lake Storage se službami Azure](data-lake-store-integrate-with-azure-services.md).

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

## <a name="next-steps"></a>Další kroky

Zobrazit [známé problémy](data-lake-storage-known-issues.md)




