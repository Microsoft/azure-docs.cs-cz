---
title: Matice podpory pro zálohování objektů blob Azure
description: Poskytuje souhrn nastavení podpory a omezení při zálohování objektů blob Azure (ve verzi Preview).
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: 12d289fdc3f84e7cbb3489a3ece283179e51772c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561895"
---
# <a name="support-matrix-for-azure-blobs-backup-in-preview"></a>Matice podpory pro zálohování objektů blob Azure (ve verzi Preview)

Tento článek shrnuje regionální dostupnost, podporované scénáře a omezení provozní zálohy objektů BLOB.

## <a name="supported-regions"></a>Podporované oblasti

Provozní záloha pro objekty BLOB je aktuálně dostupná v následujících oblastech: Austrálie – střed, Austrálie – východ, Brazílie – jih, Kanada – střed, Střed Indie, Střed USA, Východní Asie, Východní USA, Východní USA 2, Německo – středozápad, Japonsko – východ, Japonsko – západ, Korea – střed, USA – jih, Severní Evropa, střed USA – jih, Východní Asie, Švýcarsko – sever , Západní USA Západní USA 2

## <a name="limitations"></a>Omezení

Provozní zálohování objektů BLOB používá obnovení k určitému bodu v čase blob, správu verzí objektů blob, obnovitelné odstranění pro objekty blob, změnu kanálu pro objekty BLOB a odstranění zámku k poskytnutí místního řešení zálohování. Omezení vztahující se na tyto možnosti platí i pro provozní zálohu.

**Podporované scénáře:** Provozní zálohování podporuje jenom objekty blob bloku ve standardních účtech úložiště úrovně Standard pro obecné účely v2. Takže účty ADLS Gen2 nejsou podporované. Také se obnoví všechny objekty blob stránky, doplňovací objekty BLOB a objekty blob úrovně Premium v účtu úložiště a obnoví se jenom objekty blob bloku.

**Další omezení:**

- Pokud jste během doby uchování odstranili kontejner, tento kontejner se neobnoví s operací obnovení k určitému bodu v čase. Pokud se pokusíte obnovit rozsah objektů blob, které obsahují objekty BLOB v odstraněném kontejneru, operace obnovení k určitému bodu v čase selže. Další informace o ochraně kontejnerů před odstraněním najdete v tématu [obnovitelné odstranění pro kontejnery (Preview)](../storage/blobs/soft-delete-container-overview.md).
- Pokud se objekt BLOB přesunul mezi horkou a studenou vrstvou v období od současného a bodu obnovení, obnoví se objekt blob do předchozí úrovně. Obnovování objektů blob bloku v archivní úrovni se nepodporuje. Pokud se například objekt BLOB v horké vrstvě přesunul do archivní úrovně před dvěma dny a operace obnovení se obnoví do před 3 dny, objekt BLOB se neobnoví do vrstvy Hot. Pokud chcete obnovit archivovaný objekt blob, nejdřív ho přesuňte mimo archivní vrstvu. Další informace najdete v tématu [dehydratované data objektů BLOB z archivní úrovně](../storage/blobs/storage-blob-rehydration.md).
- Blok, který byl nahrán prostřednictvím [bloku Put](/rest/api/storageservices/put-block) nebo [bloku Put z adresy URL](/rest/api/storageservices/put-block-from-url), ale není potvrzen prostřednictvím [seznamu blokovaných](/rest/api/storageservices/put-block-list)objektů, není součástí objektu blob, a proto není obnoven v rámci operace obnovení.
- Objekt BLOB s aktivním zapůjčením nejde obnovit. Pokud je objekt BLOB s aktivním zapůjčením zahrnutý do rozsahu objektů blob, které se mají obnovit, operace obnovení se automaticky nezdařila. Před zahájením operace obnovení podělte všechna aktivní zapůjčení.
- Snímky se v rámci operace obnovení nevytváří ani neodstraňují. Do předchozího stavu se obnoví jenom základní objekt BLOB.

## <a name="next-steps"></a>Další kroky

- [Přehled operačního zálohování pro objekty blob Azure (ve verzi Preview)](blob-backup-overview.md)