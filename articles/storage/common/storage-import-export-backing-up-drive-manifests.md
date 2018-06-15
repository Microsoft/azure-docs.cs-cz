---
title: Zálohování Azure Import/Export jednotky manifesty | Microsoft Docs
description: Zjistěte, jak má vaše manifesty jednotky pro službu Microsoft Azure Import/Export automaticky zálohovat.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 33eb8e1eea8f8aa7b79ef3e54f2b1ed88dc794ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873664"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Zálohování jednotky manifesty pro úlohy Azure Import/Export

Manifesty jednotky lze automaticky zálohovat na objekty BLOB nastavením `BackupDriveManifest` vlastnost `true` v [Put úlohy](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) nebo [vlastnosti úlohy aktualizace](/rest/api/storageimportexport/jobs#Jobs_Update) operace REST API. Ve výchozím nastavení nejsou manifesty jednotky zálohovat. Manifestu zálohy disku jsou uloženy jako objekty BLOB bloku v kontejneru v rámci účtu úložiště, které jsou přidružené k úloze. Ve výchozím nastavení, je název kontejneru `waimportexport`, ale můžete zadat jiný název v `DiagnosticsPath` vlastnost při volání metody `Put Job` nebo `Update Job Properties` operace. Zálohování manifestu blob jsou pojmenované v následujícím formátu: `waies/jobname_driveid_timestamp_manifest.xml`.

 Identifikátor URI manifesty zálohování jednotky pro úlohu můžete načíst pomocí volání [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) operaci. Identifikátor URI je vrácený v objektu blob `ManifestUri` vlastnosti pro každou jednotku.

## <a name="next-steps"></a>Další kroky

* [Pomocí REST API služby importu a exportu](storage-import-export-using-the-rest-api.md)
