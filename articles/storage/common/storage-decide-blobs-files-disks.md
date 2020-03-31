---
title: Rozhodování o tom, kdy použít objekty BLOB Azure, soubory Azure nebo disky Azure
description: Přečtěte si o různých způsobech ukládání a přístupu k datům v Azure, které vám pomůžou rozhodnout, kterou technologii použít.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71671049"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Rozhodování o tom, kdy použít objekty BLOB Azure, soubory Azure nebo disky Azure

Microsoft Azure poskytuje několik funkcí v Azure Storage pro ukládání a přístup k vašim datům v cloudu. Tento článek popisuje soubory Azure, objekty BLOB a disky a je navržen tak, aby vám pomohl vybrat mezi těmito funkcemi.

## <a name="scenarios"></a>Scénáře

Následující tabulka porovnává soubory, objekty BLOB a disky a zobrazuje ukázkové scénáře vhodné pro každý z nich.

| Funkce | Popis | Kdy je použít |
|--------------|-------------|-------------|
| **Soubory Azure** | Poskytuje rozhraní SMB, klientské knihovny a [rozhraní REST,](/rest/api/storageservices/file-service-rest-api) které umožňuje přístup odkudkoli k uloženým souborům. | Chcete "zvednout a přesunout" aplikaci do cloudu, který už používá nativní souborový systém API pro sdílení dat mezi ním a dalšími aplikacemi spuštěnými v Azure.<br/><br/>Chcete uložit nástroje pro vývoj a ladění, které je potřeba přistupovat z mnoha virtuálních počítačů. |
| **Objekty blob Azure** | Poskytuje klientské knihovny a [rozhraní REST,](/rest/api/storageservices/blob-service-rest-api) které umožňuje nestrukturovaná data, které mají být uloženy a přistupovat v masivním měřítku v blob bloku.<br/><br/>Podporuje také [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) pro podniková řešení pro analýzu velkých objemů dat. | Chcete, aby vaše aplikace podporovala streamování a scénáře náhodného přístupu.<br/><br/>Chcete mít přístup k datům aplikace odkudkoli.<br/><br/>Chcete v Azure vytvořit jezero podnikových dat a provádět analýzy velkých objemů dat. |
| **Disky Azure** | Poskytuje klientské knihovny a [rozhraní REST,](/rest/api/compute/manageddisks/disks/disks-rest-api) které umožňuje trvale ukládat data a přistupovat k nim z připojeného virtuálního pevného disku. | Chcete zrušit a přesunout aplikace, které používají nativní souborová systémová úložiště API ke čtení a zápisu dat na trvalé disky.<br/><br/>Chcete ukládat data, která není nutné přistupovat z mimo virtuální počítač, ke kterému je připojen disk. |


## <a name="next-steps"></a>Další kroky

Při rozhodování o tom, jak jsou vaše data uložena a jak k nim máte přístup, byste měli také zvážit související náklady. Další informace najdete v [tématu Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/).
  
Některé funkce SMB se nevztahují na cloud. Další informace najdete [v tématu Funkce, které nejsou podporovány službou Azure File](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
 
Další informace o objektech Blob Azure najdete v článku [Co je úložiště objektů blob Azure?](../blobs/storage-blobs-overview.md).

Další informace o úložišti disků naleznete v našem [úvodu ke spravovaným diskům](../../virtual-machines/windows/managed-disks-overview.md).

Další informace o souborech Azure najdete v článku [Plánování nasazení souborů Azure](../files/storage-files-planning.md).