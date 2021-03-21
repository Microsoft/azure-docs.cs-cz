---
title: Průvodce správou verzí objektů BLOB v Průzkumník služby Azure Storage | Microsoft Docs
description: Průvodce správou verzí objektů BLOB pro Průzkumník služby Azure Storage
services: storage
author: chuye
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: e20733cb4b93fcfac7606895746645727f12d6c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92783823"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Průvodce správou verzí objektů BLOB Průzkumník služby Azure Storage

Průzkumník služby Microsoft Azure Storage poskytuje snadný přístup a správu verzí objektů BLOB. Tato příručka vám pomůže pochopit, jak funguje Správa verzí objektů BLOB v Průzkumník služby Storage. Než budete pokračovat, doporučujeme si přečíst další informace o [verzích objektů BLOB](../blobs/versioning-overview.md).

## <a name="terminology"></a>Terminologie

V této části najdete některé definice, které vám pomůžou pochopit jejich použití v tomto článku.

- Obnovitelné odstranění: alternativní funkce automatické ochrany dat. Další informace o obnovitelném odstranění najdete [tady](../blobs/soft-delete-blob-overview.md).
- Aktivní objekt BLOB: v aktivním stavu je vytvořená verze objektu BLOB nebo BLOB. Můžete pracovat jenom s objekty blob nebo verze objektů BLOB v aktivním stavu.
- Měkký odstraněný objekt BLOB: verze objektu BLOB nebo BLOB s označením příznakem Soft-Deleted. Obnovitelné odstraněné objekty BLOB se uchovávají jenom po dobu uchování.
- Verze objektu BLOB: objekt BLOB vytvořený s povoleným správou verzí objektů BLOB. Každá verze objektu BLOB je přidružená k ID verze.
- Aktuální verze: verze objektu BLOB označená jako aktuální verze.
- Předchozí verze: verze objektu blob, která není aktuální verzí.
- Objekt BLOB bez verze: objekt BLOB vytvořený pomocí správy verzí BLOB je zakázaný. Objekt BLOB bez verze neobsahuje ID verze.

## <a name="view-blob-versions"></a>Zobrazit verze objektů BLOB

Průzkumník služby Storage podporuje čtyři různá zobrazení pro zobrazení objektů BLOB.

| Zobrazení | Aktivní objekty blob bez verze | Obnovitelné – odstraněné objekty blob bez verze | Verze objektů BLOB |
| ---- | :----------: | :-----------: | :------------------: |
| Aktivní objekty blob | Yes | No | Pouze aktuální verze |
| Aktivní objekty BLOB a obnovitelné odstraněné objekty blob | Yes | Yes | Pouze aktuální verze |
| Aktivní objekty BLOB a objekty blob bez aktuální verze | Yes | No | Aktuální verze nebo nejnovější aktivní verze |
| Všechny objekty BLOB a objekty blob bez aktuální verze | Yes | Yes | Aktuální verze nebo nejnovější verze |

### <a name="active-blobs"></a>Aktivní objekty blob

V tomto zobrazení Průzkumník služby Storage zobrazí:

- Aktivní objekty blob bez verze
- Aktuální verze

### <a name="active-blobs-and-soft-deleted-blobs"></a>Aktivní objekty BLOB a obnovitelné odstraněné objekty blob

V tomto zobrazení Průzkumník služby Storage zobrazí:

- Aktivní objekty blob bez verze
- Obnovitelné – odstraněné objekty blob bez verze
- Aktuální verze.

### <a name="active-blobs-and-blobs-without-current-version"></a>Aktivní objekty BLOB a objekty blob bez aktuální verze

V tomto zobrazení Průzkumník služby Storage zobrazí:

- Aktivní objekty blob bez verze
- Aktuální verze
- Nejnovější aktivní předchozí verze. 

U objektů blob, které nemají aktuální verzi, ale mají aktivní předchozí verzi, Průzkumník služby Storage zobrazuje nejnovější aktivní předchozí verzi jako reprezentaci tohoto objektu BLOB.

### <a name="all-blobs-and-blobs-without-current-version"></a>Všechny objekty BLOB a objekty blob bez aktuální verze

V tomto zobrazení Průzkumník služby Storage zobrazí:

- Aktivní objekty blob bez verze
- Obnovitelné – odstraněné objekty blob bez verze
- Aktuální verze
- Nejnovější předchozí verze. 

U objektů blob, které nemají aktuální verzi, Průzkumník služby Storage zobrazí nejnovější předchozí verzi jako reprezentaci tohoto objektu BLOB.

> [!Note]
> Kvůli omezení služby Průzkumník služby Storage potřebuje nějaké další zpracování, aby při výpisu verzí objektů BLOB získalo hierarchické zobrazení virtuálních adresářů. Výpis objektů BLOB v následujících zobrazeních bude trvat déle:
> 
> - Aktivní objekty BLOB a objekty blob bez aktuální verze
> - Všechny objekty BLOB a objekty blob bez aktuální verze

## <a name="manage-blob-versions"></a>Správa verzí objektů BLOB

### <a name="view-versions-of-a-blob"></a>Zobrazit verze objektu BLOB

Průzkumník služby Storage poskytuje příkaz pro **správu verzí** pro zobrazení všech verzí objektu BLOB. Chcete-li zobrazit verze objektu blob, vyberte objekt blob, pro který chcete zobrazit verze, a vyberte **Spravovat historii &rarr; Spravovat verze** z panelu nástrojů nebo místní nabídky.

### <a name="download-blob-versions"></a>Stáhnout verze objektů BLOB

Chcete-li stáhnout jednu nebo více verzí objektů blob, vyberte verze objektů blob, které chcete stáhnout, a vyberte **Stáhnout** z panelu nástrojů nebo místní nabídky.

Pokud stahujete více verzí objektu blob, stažené soubory budou mít ID verze na začátku svých názvů souborů.

### <a name="delete-blob-versions"></a>Odstranit verze objektů BLOB

Chcete-li odstranit jednu nebo více verzí objektů blob, vyberte verze objektů blob, které chcete odstranit, a vyberte možnost **Odstranit** z panelu nástrojů nebo místní nabídky.

Verze objektů BLOB jsou předmětem zásad pro obnovitelné odstranění. Pokud je povolené obnovitelné odstranění, verze objektů BLOB budou obnovitelné. Jedním z zvláštních případů je odstranění aktuální verze. Při odstranění aktuální verze se místo toho stane aktivní předchozí verze.

### <a name="promote-blob-version"></a>Zvýšit úroveň verze objektu BLOB

Obsah objektu blob můžete obnovit tak, že povýšíte předchozí verzi, aby se stala aktuální verzí. Vyberte verzi objektu blob, kterou chcete propagovat, a na panelu nástrojů nebo v místní nabídce vyberte **propagovat verzi** .

Objekty blob jiné než verze budou přepsány povýšenou verzí objektu BLOB. Ujistěte se, že už tato data nepotřebujete, nebo před potvrzením operace zálohujte data sami. Aktuální verze se automaticky stanou předchozími verzemi, takže Průzkumník služby Storage výzvu k potvrzení.

### <a name="undelete-blob-version"></a>Obnovit verzi objektu BLOB

Verze objektů BLOB se nedají obnovit jednotlivě. Musí být všechny najednou neodstraněné. Chcete-li obnovit všechny verze objektů BLOB v objektu blob, vyberte některou z verzí objektu BLOB a na panelu nástrojů nebo v místní nabídce vyberte možnost zrušit **odstranění** .

### <a name="change-access-tier-of-blob-versions"></a>Změna úrovně přístupu verzí objektů BLOB

Každá verze objektu BLOB má svou vlastní úroveň přístupu. Pokud chcete změnit úroveň přístupu verzí objektů blob, vyberte verze objektů blob, pro které chcete změnit úroveň přístupu, a v místní nabídce vyberte **změnit úroveň přístupu...**

## <a name="see-also"></a>Viz také

* [Správa verzí objektů BLOB](../blobs/versioning-overview.md)
* [Obnovitelné odstranění pro objekty blob](../blobs/soft-delete-blob-overview.md)