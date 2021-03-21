---
title: Matice podpory pro zálohování objektů blob Azure
description: Poskytuje souhrn nastavení podpory a omezení při zálohování objektů blob Azure (ve verzi Preview).
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: ade43350bbe3fa1bcf58f47e93b948db3a5b21bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744796"
---
# <a name="support-matrix-for-azure-blobs-backup-in-preview"></a>Matice podpory pro zálohování objektů blob Azure (ve verzi Preview)

Tento článek shrnuje regionální dostupnost, podporované scénáře a omezení provozní zálohy objektů BLOB.

## <a name="supported-regions"></a>Podporované oblasti

Provozní záloha pro objekty BLOB je aktuálně dostupná v následujících oblastech: Austrálie – střed, Austrálie – východ, Brazílie – jih, Kanada – střed, Střed Indie, Střed USA, Východní Asie, Východní USA, Východní USA 2, Německo – středozápad, Japonsko – východ, Japonsko – západ, Korea – střed, USA – jih, Severní Evropa, střed USA – jih, Východní Asie, Švýcarsko – sever , Západní USA Západní USA 2

## <a name="limitations"></a>Omezení

Provozní zálohování objektů BLOB používá obnovení k určitému bodu v čase blob, správu verzí objektů blob, obnovitelné odstranění pro objekty blob, změnu kanálu pro objekty BLOB a odstranění zámku k poskytnutí místního řešení zálohování. Omezení vztahující se na tyto možnosti platí i pro provozní zálohu.

**Podporované scénáře:** Provozní zálohování podporuje jenom objekty blob bloku ve standardních účtech úložiště úrovně Standard pro obecné účely v2. Takže účty ADLS Gen2 nejsou podporované. Také se obnoví všechny objekty blob stránky, doplňovací objekty BLOB a objekty blob úrovně Premium v účtu úložiště a obnoví se jenom objekty blob bloku.

**Další omezení:**

- Pokud jste během doby uchování odstranili kontejner, tento kontejner se neobnoví s operací obnovení k určitému bodu v čase. Pokud se pokusíte obnovit rozsah objektů blob, které obsahují objekty BLOB v odstraněném kontejneru, operace obnovení k určitému bodu v čase selže. Další informace o ochraně kontejnerů před odstraněním najdete v tématu [obnovitelné odstranění pro kontejnery (Preview)](https://docs.microsoft.com/azure/storage/blobs/soft-delete-container-overview).
- Pokud se objekt BLOB přesunul mezi horkou a studenou vrstvou v období od současného a bodu obnovení, obnoví se objekt blob do předchozí úrovně. Obnovování objektů blob bloku v archivní úrovni se nepodporuje. Pokud se například objekt BLOB v horké vrstvě přesunul do archivní úrovně před dvěma dny a operace obnovení se obnoví do před 3 dny, objekt BLOB se neobnoví do vrstvy Hot. Pokud chcete obnovit archivovaný objekt blob, nejdřív ho přesuňte mimo archivní vrstvu. Další informace najdete v tématu [dehydratované data objektů BLOB z archivní úrovně](https://docs.microsoft.com/azure/storage/blobs/storage-blob-rehydration).
- Blok, který byl nahrán prostřednictvím [bloku Put](https://docs.microsoft.com/rest/api/storageservices/put-block) nebo [bloku Put z adresy URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url), ale není potvrzen prostřednictvím [seznamu blokovaných](https://docs.microsoft.com/rest/api/storageservices/put-block-list)objektů, není součástí objektu blob, a proto není obnoven v rámci operace obnovení.
- Objekt BLOB s aktivním zapůjčením nejde obnovit. Pokud je objekt BLOB s aktivním zapůjčením zahrnutý do rozsahu objektů blob, které se mají obnovit, operace obnovení se automaticky nezdařila. Před zahájením operace obnovení podělte všechna aktivní zapůjčení.
- Snímky se v rámci operace obnovení nevytváří ani neodstraňují. Do předchozího stavu se obnoví jenom základní objekt BLOB.

## <a name="next-steps"></a>Další kroky

- [Přehled operačního zálohování pro objekty blob Azure (ve verzi Preview)](blob-backup-overview.md)
