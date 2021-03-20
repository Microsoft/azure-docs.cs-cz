---
title: Přístup k více protokolům na Azure Data Lake Storage | Microsoft Docs
description: Používejte rozhraní API objektů BLOB a aplikace, které používají rozhraní API objektů BLOB s Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "77914854"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Přístup k více protokolům na Azure Data Lake Storage

Rozhraní API objektů BLOB teď pracují s účty, které mají hierarchický obor názvů. Tím se odemkne ekosystém nástrojů, aplikací a služeb a také několik funkcí BLOB Storage pro účty, které mají hierarchický obor názvů.

Až do poslední doby, možná budete muset zachovat samostatná řešení úložiště pro úložiště objektů a analýzu. To je proto, že Azure Data Lake Storage Gen2 mít omezené podpory ekosystémů. Měl by taky mít omezený přístup k Blob service funkcím, jako je protokolování diagnostiky. Fragmentované řešení úložiště je obtížné udržovat, protože k provádění různých scénářů je potřeba přesunout data mezi účty. Už to nemusíte dělat.

Při přístupu k více protokolům na Data Lake Storage můžete pracovat s daty pomocí ekosystému nástrojů, aplikací a služeb. To zahrnuje i nástroje a aplikace třetích stran. Můžete je nasměrovat na účty, které mají hierarchický obor názvů, aniž byste je museli upravovat. Tyto aplikace fungují *tak, jak jsou* , i když volají rozhraní API objektů blob, protože rozhraní API objektů BLOB teď můžou pracovat s daty v účtech, které mají hierarchický obor názvů.

Funkce služby Blob Storage, jako je [protokolování diagnostiky](../common/storage-analytics-logging.md), [úrovně přístupu](storage-blob-storage-tiers.md)a [zásady správy životního cyklu úložiště objektů BLOB](storage-lifecycle-management-concepts.md) , teď fungují s účty, které mají hierarchický obor názvů. Proto můžete v účtech BLOB Storage povolit hierarchické obory názvů bez ztráty přístupu k těmto důležitým funkcím. 

> [!NOTE]
> Přístup k více protokolům na Data Lake Storage je všeobecně dostupný a je k dispozici ve všech oblastech. Některé služby Azure nebo funkce úložiště objektů blob, které jsou povolené přístupem přes protokol, zůstávají ve verzi Preview.  Tyto články shrnují aktuální podporu funkcí služby Blob Storage a integrace služeb Azure. 
>
> [Funkce Blob Storage dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
>
>[Služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Jak funguje přístup k více protokolům v Data Lake Storage

Rozhraní API objektů BLOB a rozhraní API pro Data Lake Storage Gen2 můžou pracovat se stejnými daty v účtech úložiště, které mají hierarchický obor názvů. Data Lake Storage Gen2 směruje rozhraní API objektů BLOB prostřednictvím hierarchického oboru názvů tak, aby bylo možné získat výhody první třídy operace s adresáři a seznamy řízení přístupu (ACL) kompatibilní se standardem POSIX. 

![Přístup k více protokolům na Data Lake Storage koncepční](./media/data-lake-storage-interop/interop-concept.png) 

Stávající nástroje a aplikace, které využívají rozhraní BLOB API, získají tyto výhody automaticky. Vývojáři je nebudou muset upravovat. Data Lake Storage Gen2 konzistentně používá adresáře a seznamy ACL na úrovni souborů bez ohledu na protokol, který nástroje a aplikace používají pro přístup k datům. 

## <a name="see-also"></a>Viz také

- [Funkce Blob Storage dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Open Source platformy, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Známé problémy s Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)




