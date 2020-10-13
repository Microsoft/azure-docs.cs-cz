---
title: Obnovení bodu v čase pro objekty blob bloku
titleSuffix: Azure Storage
description: Obnovení k určitému bodu v čase pro objekty blob bloku zajišťuje ochranu proti náhodnému odstranění nebo poškození tím, že vám umožní obnovit účet úložiště do předchozího stavu v daném časovém okamžiku.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aed3116976d57df81da399495fd9da3722ba770a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91960673"
---
# <a name="point-in-time-restore-for-block-blobs"></a>Obnovení bodu v čase pro objekty blob bloku

Obnovení k bodu v čase poskytuje ochranu proti náhodnému odstranění nebo poškození tím, že umožňuje obnovit data objektů blob bloku do dřívějšího stavu. Obnovení k určitému bodu v čase je užitečné ve scénářích, kdy uživatel nebo aplikace nechtěně odstraní data nebo kde chyba aplikace poškozuje data. Obnovení k určitému bodu v čase umožňuje také scénáře testování, které před spuštěním dalších testů vyžadují vrácení sady dat do známého stavu.

Obnovení k bodu v čase se podporuje jenom pro účty úložiště pro obecné účely verze 2. Pomocí obnovení k bodu v čase lze obnovit pouze data z horké a studené úrovně přístupu.

Informace o tom, jak povolit obnovení k určitému bodu v čase pro účet úložiště, najdete v tématu [provedení obnovení k určitému bodu v čase u dat objektů blob bloku](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Jak funguje obnovení k časovému okamžiku

Pokud chcete povolit obnovení k určitému bodu v čase, vytvořte zásadu správy pro účet úložiště a zadejte dobu uchování. Během doby uchování můžete obnovit objekty blob bloku z současného stavu do stavu v předchozím bodě v čase.

Chcete-li zahájit obnovení k určitému bodu v čase, zavolejte operaci [obnovit rozsahy objektů BLOB](/rest/api/storagerp/storageaccounts/restoreblobranges) a určete bod obnovení v čase UTC. Můžete zadat lexicographical rozsahy názvů kontejnerů a objektů blob, které se mají obnovit, nebo vynecháte rozsah pro obnovení všech kontejnerů v účtu úložiště. Pro každou operaci obnovení se podporuje až 10 lexicographical rozsahů.

Azure Storage analyzuje všechny změny provedené v zadaných objektech blob mezi požadovaným bodem obnovení, který je zadaný v čase UTC, a v současné době. Operace obnovení je atomická, takže se buď úspěšně dokončí obnovení všech změn, nebo selže. Pokud existují objekty blob, které se nedají obnovit, operace se nezdařila a operace čtení a zápisu do ovlivněných kontejnerů se obnoví.

V účtu úložiště se dá spustit jenom jedna operace obnovení. Operaci obnovení nelze zrušit, jakmile probíhá, ale můžete provést druhou operaci obnovení, která vrátí zpět první operaci.

Operace **obnovit rozsahy objektů BLOB** vrátí ID obnovení, které jedinečně identifikuje operaci. Chcete-li zjistit stav obnovení k určitému bodu v čase, zavolejte operaci **získat stav obnovení** s ID obnovení vráceným z operace **obnovit rozsahy objektů BLOB** .

> [!IMPORTANT]
> Když provádíte operaci obnovení, Azure Storage blokuje operace s daty u objektů BLOB v rozsahu obnovování po dobu trvání operace. Operace čtení, zápisu a odstranění jsou v primárním umístění blokované. Z tohoto důvodu nemusí operace, jako je například výpis kontejnerů v Azure Portal, fungovat podle očekávání, zatímco probíhá operace obnovení.
>
> Operace čtení ze sekundárního umístění můžou během operace obnovení pokračovat, pokud je účet úložiště geograficky replikovaný.

> [!CAUTION]
> Obnovení k bodu v čase podporuje pouze obnovení operací pouze pro objekty blob bloku. Operace na kontejnerech nelze obnovit. Pokud odstraníte kontejner z účtu úložiště voláním operace [odstranění kontejneru](/rest/api/storageservices/delete-container) , nelze tento kontejner obnovit pomocí operace obnovení. Místo odstranění kontejneru odstraňte jednotlivé objekty blob, pokud je budete chtít obnovit.

### <a name="prerequisites-for-point-in-time-restore"></a>Předpoklady pro obnovení k bodu v čase

Obnovení k bodu v čase vyžaduje, aby byly povolené následující funkce Azure Storage, než můžete povolit obnovení k bodu v čase:

- [Obnovitelné odstranění](soft-delete-overview.md)
- [Změna kanálu](storage-blob-change-feed.md)
- [Správa verzí objektů BLOB](versioning-overview.md)

### <a name="retention-period-for-point-in-time-restore"></a>Doba uchování pro obnovení k určitému bodu v čase

Když pro účet úložiště povolíte obnovení k určitému bodu v čase, zadáte dobu uchování. Objekty blob bloku v účtu úložiště se dají obnovit během doby uchování.

Doba uchování začíná, když povolíte obnovení k určitému bodu v čase. Mějte na paměti, že nemůžete obnovit objekty blob do stavu před začátkem doby uchování. Pokud jste například povolili obnovení k určitému bodu v čase, může 1. až do 30 dnů trvat, a až do 15 dnů budete moct obnovení obnovit. 1. června můžete data obnovit z 1 až 30 dnů.

Doba uchování pro obnovení k určitému bodu v čase musí být alespoň jeden den kratší než doba uchování zadaná pro obnovitelné odstranění. Například pokud je doba uchování obnovitelného odstranění nastavená na 7 dní, pak doba uchování obnovení k určitému bodu v čase může být v rozmezí od 1 do 6 dnů.

### <a name="permissions-for-point-in-time-restore"></a>Oprávnění k obnovení k časovému okamžiku

Aby bylo možné zahájit operaci obnovení, musí mít klient oprávnění pro zápis do všech kontejnerů v účtu úložiště. Pokud chcete udělit oprávnění k autorizaci operace obnovení s Azure Active Directory (Azure AD), přiřaďte roli **Přispěvatel účtu úložiště** k objektu zabezpečení na úrovni účtu úložiště, skupiny prostředků nebo předplatného.

## <a name="limitations-and-known-issues"></a>Omezení a známé problémy

Obnovení bodu v čase pro objekty blob bloku má následující omezení a známé problémy:

- V rámci operace obnovení k určitému bodu v čase lze obnovit pouze objekty blob bloku v účtu úložiště úrovně Standard pro obecné účely verze 2. Objekty blob bloku, objekty blob stránky a objekty blob bloku úrovně Premium se neobnoví. 
- Pokud jste během doby uchování odstranili kontejner, nebude tento kontejner obnoven s operací obnovení k určitému bodu v čase. Pokud se pokusíte obnovit rozsah objektů blob, které obsahují objekty BLOB v odstraněném kontejneru, operace obnovení k určitému bodu v čase selže. Další informace o ochraně kontejnerů před odstraněním najdete v tématu [obnovitelné odstranění pro kontejnery (Preview)](soft-delete-container-overview.md).
- Pokud se objekt BLOB přesunul mezi horkou a studenou vrstvou v období od současného a bodu obnovení, obnoví se objekt blob do předchozí úrovně. Obnovování objektů blob bloku v archivní úrovni se nepodporuje. Pokud se například objekt blob na horké úrovni před dvěma dny přesunul na archivní úroveň a operace obnovení provádí obnovení k bodu před třemi dny, objekt blob se neobnoví na horké úrovni. Pokud chcete obnovit archivovaný objekt blob, nejdřív ho přesuňte mimo archivní vrstvu. Další informace najdete v tématu [dehydratované data objektů BLOB z archivní úrovně](storage-blob-rehydration.md).
- Blok, který byl nahrán prostřednictvím [bloku Put](/rest/api/storageservices/put-block) nebo [bloku Put z adresy URL](/rest/api/storageservices/put-block-from-url), ale není potvrzen prostřednictvím [seznamu blokovaných](/rest/api/storageservices/put-block-list)objektů, není součástí objektu blob, takže není obnoven v rámci operace obnovení.
- Objekt BLOB s aktivním zapůjčením nejde obnovit. Pokud je objekt BLOB s aktivním zapůjčením zahrnutý do rozsahu objektů blob, které se mají obnovit, operace obnovení se nezdařila. Před zahájením operace obnovení přerušte všechna aktivní zapůjčení.
- Snímky se v rámci operace obnovení nevytváří ani neodstraňují. Do předchozího stavu se obnoví jenom základní objekt BLOB.
- Obnovení Azure Data Lake Storage Gen2 plochých a hierarchických oborů názvů není podporováno.

> [!IMPORTANT]
> Pokud obnovíte objekty blob bloku do bodu, který je starší než 22. září 2020, vstoupí v platnost omezení pro obnovení k určitému bodu v čase. Společnost Microsoft doporučuje zvolit bod obnovení, který se rovná nebo je vyšší než 22. září 2020, aby využil všeobecně dostupné funkce obnovení k určitému bodu v čase.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Fakturace pro obnovení k bodu v čase závisí na množství zpracovaných dat k provedení operace obnovení. Množství zpracovaných dat vychází z počtu změn, ke kterým došlo mezi bodem obnovení a současným okamžikem. Například za předpokladu poměrně konstantní frekvence změny pro blokování dat objektů BLOB v účtu úložiště, operace obnovení, která se vrátí za čas 1 den, by cena obnovení, která se vrátí za čas po dobu 10 dní, byla 1 desetinná.

Chcete-li odhadnout náklady na operaci obnovení, Projděte si protokol změn kanálu, abyste mohli odhadnout množství dat, která byla během období obnovení změněna. Například pokud doba uchovávání kanálu pro změnu je 30 dní a velikost kanálu změn je 10 MB, pak obnovení do výše uvedeného bodu 10 dnů by se za účet LRS v této oblasti dozvědělo přibližně o třetinu ceny. Obnovení do bodu, který je starší než 27 dní, by mělo mít náklady přibližně devět desetin cen uvedených v seznamu.

Další informace o cenách pro obnovení k bodu v čase najdete v tématu ceny za objekty [blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="next-steps"></a>Další kroky

- [Provedení obnovení k určitému bodu v čase u dat objektů blob bloku](point-in-time-restore-manage.md)
- [Změna podpory kanálu v Azure Blob Storage](storage-blob-change-feed.md)
- [Povolení obnovitelného odstranění pro objekty blob](soft-delete-enable.md)
- [Povolení a správa verzí objektů BLOB](versioning-enable.md)
