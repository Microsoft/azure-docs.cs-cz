---
title: Diagnostiky a zotavení po chybě pro úlohy Azure Import/Export | Microsoft Docs
description: Zjistěte, jak povolit podrobné protokolování pro úlohy služby Microsoft Azure Import/Export.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 096cc795-9af6-4335-9fe8-fffa9f239a17
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 0068aae9d6780aa41a070db0eb191d0d5a165d21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873636"
---
# <a name="diagnostics-and-error-recovery-for-azure-importexport-jobs"></a>Diagnostiky a zotavení po chybě pro úlohy Azure Import/Export
Pro každou jednotku zpracovat vytvoří služba Azure Import/Export protokol chyb v přidruženého účtu úložiště. Můžete také povolit podrobné protokolování nastavením `LogLevel` vlastnost `Verbose` při volání metody [Put úlohy](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) nebo [vlastnosti úlohy aktualizace](/rest/api/storageimportexport/jobs#Jobs_Update) operace.

 Ve výchozím nastavení se protokoly zapisují do kontejner s názvem `waimportexport`. Můžete zadat jiný název nastavení `DiagnosticsPath` vlastnost při volání metody `Put Job` nebo `Update Job Properties` operace. Protokoly se ukládají jako objekty BLOB bloku se podle následující konvence: `waies/jobname_driveid_timestamp_logtype.xml`.

 Identifikátor URI protokoly pro úlohu můžete načíst pomocí volání [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) operaci. Identifikátor URI pro podrobného protokolování se vrátí v `VerboseLogUri` vlastnosti pro každou jednotku, když je identifikátor URI v protokolu chyb vrácený v `ErrorLogUri` vlastnost.

Data protokolování můžete použít k identifikaci následující problémy.

## <a name="drive-errors"></a>Jednotka chyby

Následující položky jsou klasifikovány jako chyby jednotky:

-   Chyby v přístupu ke službám nebo načítání souboru manifestu

-   Nesprávný klíče nástroje BitLocker

-   Jednotky pro čtení a zápis chyby

## <a name="blob-errors"></a>Objekt BLOB chyby

Následující položky jsou klasifikovány jako chyby objektů blob:

-   Objekt blob nesprávnou či konfliktní nebo názvy

-   Chybějící soubory

-   Nebyl nalezen objekt BLOB

-   Zkrácený soubory (soubory na disku je menší, než je zadáno v manifestu)

-   Poškozený soubor obsahu (pro úlohy importu, zjistila se neshoda MD5 kontrolního součtu)

-   Soubory metadat a vlastnost poškozená blob (zjistila se neshoda MD5 kontrolního součtu)

-   Nesprávné schéma pro vlastnosti objektů blob a soubory metadat

Můžou nastat případy, kde některé části úlohu import nebo export není úspěšně dokončil, zatímco stále dokončení celkové úlohy. V takovém případě můžete buď odeslání nebo stažení části chybějí dat přes síť, nebo můžete vytvořit novou úlohu k přenosu dat. Najdete v článku [Azure Import/Export nástroj odkaz](storage-import-export-tool-how-to-v1.md) se dozvíte, jak opravit data přes síť.

## <a name="next-steps"></a>Další kroky

* [Pomocí REST API služby importu a exportu](storage-import-export-using-the-rest-api.md)
