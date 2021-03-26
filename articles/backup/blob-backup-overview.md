---
title: Přehled operačního zálohování pro objekty blob Azure
description: Přečtěte si o provozním zálohování pro objekty blob Azure (ve verzi Preview).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 1f6bc98b92a2af4b05cae766a2186f2970b7133c
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558750"
---
# <a name="overview-of-operational-backup-for-azure-blobs-in-preview"></a>Přehled operačního zálohování pro objekty blob Azure (ve verzi Preview)

Provozní záloha pro objekty BLOB je spravované místní řešení pro ochranu dat, které umožňuje chránit objekty blob bloku z různých scénářů ztráty dat, jako jsou poškození, odstranění objektů BLOB a náhodné odstranění účtu úložiště. Data jsou uložená místně v rámci samotného zdrojového účtu úložiště a v každém případě je můžete kdykoli obnovit do vybraného bodu v čase. Poskytuje tak jednoduché, bezpečné a nákladově efektivní prostředky pro ochranu objektů BLOB.

Provozní záloha pro objekty BLOB se integruje do [centra zálohování](backup-center-overview.md), a to mimo jiné možnosti správy zálohování, aby poskytovala samostatné podokno se skleněným řízením, které vám umožní řídit, monitorovat, provozovat a analyzovat zálohy ve velkém měřítku.

## <a name="how-operational-backup-works"></a>Jak funguje zálohování do provozu

Provozní záloha objektů BLOB je **místní řešení zálohování** . Takže se zálohovaná data nepřesunou do trezoru záloh, ale ukládají se do samotného zdrojového účtu úložiště. Úložiště záloh ale pořád slouží jako jednotka pro správu záloh. Je to také řešení **průběžného zálohování** , což znamená, že nemusíte plánovat žádné zálohy a všechny změny se zachovají a obnovitelné ze stavu ve vybraném časovém okamžiku.

Provozní záloha používá funkce platformy objektů BLOB k ochraně vašich dat a v případě potřeby umožňuje obnovení:

- **Obnovení k časovému** okamžiku: [obnovení objektu BLOB v čase](../storage/blobs/point-in-time-restore-overview.md) umožňuje obnovení dat objektů blob do předchozího stavu. To zase používá **obnovitelné odstranění**, **změnu kanálu** a **verze objektů BLOB** k uchování dat po určenou dobu trvání. Provozní zálohování se stará o povolení obnovení k určitému časovému okamžiku i k základním funkcím, aby se zajistila doba uchovávání dat po určenou dobu.

- **Odstranit zámek**: odstranit zámek zabraňuje náhodnému odstranění účtu úložiště nebo neautorizovaným uživatelům. Provozní zálohování, když se nakonfiguruje, taky automaticky aplikuje zámek proti odstranění, aby se snížily možnosti ztráty dat kvůli odstranění účtu úložiště.

Pokud se chcete dozvědět o omezeních aktuálního řešení, podívejte se na [matici podpory](blob-backup-support-matrix.md) .

### <a name="protection"></a>Ochrana

Provozní zálohování se konfiguruje a spravuje na úrovni **účtu úložiště** a vztahuje se na všechny objekty blob bloku v rámci účtu úložiště. Provozní zálohování používá **zásady zálohování** ke správě doby, po kterou se budou uchovávat data záloh (včetně starších verzí a odstraněných objektů BLOB), a to tak, že definuje období, ve kterém můžete data obnovit. Zásada zálohování může mít maximální dobu uchování 360 dní nebo ekvivalentní počet úplných týdnů (51) nebo měsíců (11).

Když nakonfigurujete zálohování pro účet úložiště a přiřadíte zásady zálohování s uchováním n dní, nastaví se tyto základní vlastnosti, jak je popsáno níže. Tyto vlastnosti můžete zobrazit na kartě **Ochrana dat** ve službě BLOB Service ve vašem účtu úložiště.

- Obnovení k určitému **bodu v čase**: nastaveno na n dní, jak je definováno v zásadách zálohování. Pokud účet úložiště již měl povolený časový okamžik s uchováním, řekněme "x" dnů před konfigurací zálohování, nastaví se doba obnovení bodu v čase na větší z těchto dvou hodnot, což je Max (n, x). Pokud jste již nastavili obnovení k určitému určitému bodu v čase a v zásadě zálohování bylo stanoveno, že doba uchování je větší než ta, zůstane beze změny.

- **Obnovitelné odstranění**: nastavte na n + 5 dní, to znamená pět dní kromě doby, kterou jste zadali v zásadách zálohování. Pokud účet úložiště, který je nakonfigurovaný pro provozní zálohování, už má povolené obnovitelné odstranění s uchováním, řekněme, že "y" dnů, pak se doba uchování obnovitelného odstranění nastaví na maximum dvou hodnot, tj. Max (n + 5, y). Pokud jste již provedli možnost obnovitelného odstranění a zadané uchování je větší než v souladu se zásadami zálohování, zůstane beze změny.

- **Správa verzí pro objekty BLOB a kanál změny objektů BLOB**: Správa verzí a změna kanálu jsou povolené pro účty úložiště, které jsou nakonfigurované pro provozní zálohování.

- **Odstranit zámek**: konfigurace operačního zálohování v účtu úložiště také použije zámek proti odstranění v účtu úložiště. Zámek proti odstranění, který se používá při zálohování, můžete zobrazit na kartě **zámky** v účtu úložiště.

Aby mohla služba Backup povolit ochranu těchto vlastností účtů úložiště, musí být úložiště záloh udělené roli **Přispěvatel záloh účtu úložiště** v příslušných účtech úložiště.

>[!NOTE]
>Provozní zálohování podporuje jenom operace s objekty blob bloku a operace na kontejnerech se nedají obnovit. Pokud odstraníte kontejner z účtu úložiště voláním operace **odstranění kontejneru** , nelze tento kontejner obnovit pomocí operace obnovení. Doporučujeme vám povolit obnovitelné odstranění za účelem vylepšení ochrany a obnovení dat.

### <a name="management"></a>Správa

Po povolení zálohování v účtu úložiště se vytvoří instance zálohování odpovídající účtu úložiště v úložišti záloh. V rámci příslušné instance zálohování můžete provádět jakékoli operace související se zálohováním pro účet úložiště, jako je například zahájení obnovení, monitorování, zastavení ochrany atd.

Provozní zálohování se taky integruje přímo s centrem zálohování, které vám pomůžou spravovat ochranu všech účtů úložiště centrálně spolu se všemi ostatními úlohami, které podporují zálohování. Centrum záloh je vaše samostatné podokno pro všechny vaše požadavky na zálohování, jako je monitorování úloh a stav zálohování a obnovení, zajištění dodržování předpisů a zásad správného řízení, analýza využití zálohování a provádění operací, které se týkají zálohování a obnovení dat.

### <a name="restore"></a>Obnovení

Data můžete obnovit z libovolného bodu v čase, pro který existuje bod obnovení. Bod obnovení se vytvoří, když je účet úložiště v chráněném stavu, a dá se použít k obnovení dat, pokud spadá do doby uchování definované zásadou zálohování (a proto možnost obnovení služby BLOB Service v účtu úložiště v čase). Provozní zálohování používá obnovení k určitému bodu v čase pro obnovení dat z bodu obnovení.

Provozní zálohování nabízí možnost obnovit všechny objekty blob bloku v účtu úložiště, Procházet a obnovovat konkrétní kontejnery nebo použít shody předpon k obnovení podmnožiny objektů BLOB. Všechna obnovení se dají provádět jenom na zdrojovém účtu úložiště.

## <a name="pricing"></a>Ceny

Při použití provozní zálohy pro objekty BLOB se vám neúčtují žádné poplatky za správu ani poplatky za instance. Budou se vám ale účtovat tyto poplatky:

- Obnovení se provádí pomocí obnovení k bodu v čase blobu a přilákat poplatky na základě množství zpracovaných dat. Další informace najdete v tématu [ceny obnovení k bodu v čase](../storage/blobs/point-in-time-restore-overview.md#pricing-and-billing).

- Uchovávání dat kvůli [obnovitelnému odstranění objektů BLOB](../storage/blobs/soft-delete-blob-overview.md), [podpoře změn kanálu v Azure Blob Storage](../storage/blobs/storage-blob-change-feed.md)a [správy verzí objektů BLOB](../storage/blobs/versioning-overview.md).

## <a name="next-steps"></a>Další kroky

- [Konfigurace a Správa zálohování objektů blob Azure](blob-backup-configure-manage.md)