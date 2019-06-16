---
title: Zálohování manifestů jednotek Azure Import/Export | Dokumentace Microsoftu
description: Zjistěte, jak má vaše manifestů jednotek pro službu Microsoft Azure Import/Export zálohován automaticky.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ea223ea3ccd113014ceabff34cc4d0174abb1ddf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61483124"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Zálohování disku manifestů pro úlohy Azure Import/Export

Manifestů jednotek můžete být automaticky zálohována do objektů BLOB tak, že nastavíte `BackupDriveManifest` vlastnost `true` v [úlohy umístit](/rest/api/storageimportexport/jobs) nebo [aktualizovat vlastnosti úlohy](/rest/api/storageimportexport/jobs) operace REST API. Ve výchozím nastavení nejsou zálohovány manifestů jednotek. Jednotky manifestu zálohy jsou uložené jako objekty BLOB bloku v kontejneru v účtu úložiště, které jsou přidružené k úloze. Ve výchozím nastavení, je název kontejneru `waimportexport`, ale můžete zadat jiný název v `DiagnosticsPath` vlastnost při volání `Put Job` nebo `Update Job Properties` operace. Zálohování manifestu objektů blob jsou pojmenovány v následujícím formátu: `waies/jobname_driveid_timestamp_manifest.xml`.

 Identifikátor URI manifesty zálohování disku pro úlohu můžete načíst pomocí volání [Get Job](/rest/api/storageimportexport/jobs) operace. Identifikátor URI se vrátí v objektu blob `ManifestUri` vlastností pro každou jednotku.

## <a name="next-steps"></a>Další postup

* [Pomocí rozhraní REST API služby Import/Export](storage-import-export-using-the-rest-api.md)
