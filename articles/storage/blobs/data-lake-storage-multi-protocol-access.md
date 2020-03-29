---
title: Přístup k více protokolům ve službě Azure Data Lake Storage | Dokumenty společnosti Microsoft
description: Pomocí objektů BLOB API a aplikací, které používají objektová api objektů blob s Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914854"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Přístup k více protokolům v úložišti Azure Data Lake Storage

Objekty API objektů blob nyní pracují s účty, které mají hierarchický obor názvů. Tím se odemkne ekosystém nástrojů, aplikací a služeb a také několik funkcí úložiště objektů Blob pro účty, které mají hierarchický obor názvů.

Až donedávna jste možná museli udržovat samostatná řešení úložiště pro úložiště objektů a analytické úložiště. Je to proto, že Azure Data Lake Storage Gen2 měl omezenou podporu ekosystému. Měl také omezený přístup k funkcím služby Blob, jako je například protokolování diagnostiky. Řešení fragmentovaného úložiště je obtížné udržovat, protože je třeba přesunout data mezi účty k provedení různých scénářů. Už to nemusíš dělat.

Díky přístupu k více protokolům v úložišti Data Lake Storage můžete pracovat se svými daty pomocí ekosystému nástrojů, aplikací a služeb. To zahrnuje také nástroje a aplikace třetích stran. Můžete je nasměrovat na účty, které mají hierarchický obor názvů, aniž byste je museli upravovat. Tyto aplikace fungují *stejně jako* je i v případě, že volání objektů BLOB API, protože objektová api objektů blob nyní můžete pracovat s daty v účtech, které mají hierarchický obor názvů.

Funkce úložiště objektů blob, jako je [diagnostické protokolování](../common/storage-analytics-logging.md), [úrovně přístupu](storage-blob-storage-tiers.md)a [zásady správy životního cyklu úložiště objektů Blob,](storage-lifecycle-management-concepts.md) teď fungují s účty, které mají hierarchický obor názvů. Proto můžete povolit hierarchické obory názvů na účtech úložiště objektů blob bez ztráty přístupu k těmto důležitým funkcím. 

> [!NOTE]
> Přístup k více protokolům v úložišti datových jezer je obecně dostupný a je k dispozici ve všech oblastech. Některé služby Azure nebo funkce úložiště objektů blob povolené přístupem s více protokoly zůstávají ve verzi Preview.  Tyto články shrnují aktuální podporu funkcí úložiště objektů Blob a integrace služeb Azure. 
>
> [Funkce úložiště objektů blob dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
>
>[Služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Jak funguje přístup k více protokolům v úložišti datových jezer

Api objektů blob a data lake storage gen2 api můžete pracovat na stejná data v účtech úložiště, které mají hierarchický obor názvů. Data Lake Storage Gen2 směruje rozhraní API objektů BLOB prostřednictvím hierarchického oboru názvů, takže můžete získat výhody operací adresáře první třídy a seznamů řízení přístupu kompatibilních s posix. 

![Víceprotokolový přístup v konceptuálním úložišti datových jezer](./media/data-lake-storage-interop/interop-concept.png) 

Existující nástroje a aplikace, které používají rozhraní BLOB API získat tyto výhody automaticky. Vývojáři je nebudou muset upravovat. Data Lake Storage Gen2 konzistentně používá seznamy ACL na úrovni adresářů a souborů bez ohledu na protokol, který nástroje a aplikace používají pro přístup k datům. 

## <a name="see-also"></a>Viz také

- [Funkce úložiště objektů blob dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Open source platformy, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Známé problémy s Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)




