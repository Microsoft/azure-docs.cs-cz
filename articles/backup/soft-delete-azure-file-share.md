---
title: Náhodné odstranění ochrany sdílených složek Azure
description: Naučte se, jak obnovitelné odstranění může chránit sdílené složky Azure před náhodným odstraněním.
ms.topic: conceptual
ms.date: 02/02/2020
ms.custom: references_regions
ms.openlocfilehash: 52a116320e07c25d4ee7f00b8063ca15faeb8560
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89179908"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>Náhodné odstranění ochrany sdílených složek Azure pomocí Azure Backup

Aby se zajistila ochrana před kyberútokům nebo náhodným odstraněním, je pro všechny sdílené složky v účtu úložiště povolené [obnovitelné odstranění](../storage/files/storage-files-prevent-file-share-deletion.md) , když nakonfigurujete zálohování pro jakoukoli sdílenou složku v příslušném účtu úložiště. Pomocí obnovitelného odstranění, a to i v případě, že škodlivý objekt actor odstraní sdílenou složku, obsah sdílené složky a body obnovení (snímky) se uchovávají minimálně 14 dalších dnů, což umožňuje obnovení sdílených složek bez ztráty dat.  Pro účty úložiště úrovně Standard a Premium se podporuje obnovitelné odstranění a nastavení je povolené Azure Backup pro všechny účty úložiště hostující záložní sdílené složky.

Následující graf Flow znázorňuje různé kroky a stavy zálohované položky, když je u sdílených složek v účtu úložiště povolené obnovitelné odstranění:

 ![Měkký odstranění toku grafu](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>Kdy se má u sdílených složek v mém účtu úložiště povolit obnovitelné odstranění?

Při konfiguraci zálohování pro každou sdílenou složku v účtu úložiště Azure Backup služba umožňuje obnovitelné odstranění pro všechny sdílené složky v příslušném účtu úložiště.

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>Můžu nakonfigurovat počet dní, po které se po odstranění sdílené složky budou uchovávat vaše snímky a body obnovení ve stavu tichého odstranění?

Ano, doba uchovávání můžete nastavit podle vašich požadavků. [Tento dokument](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal) popisuje kroky pro konfiguraci doby uchovávání. U účtů úložiště s zálohovanými sdílenými složkami musí být minimální nastavení uchovávání 14 dnů.

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>Obnoví Azure Backup nastavení zachování, protože jsem nakonfigurovali méně než 14 dní?

Z hlediska zabezpečení doporučujeme pro účty úložiště s zálohovanými sdílenými složkami minimální dobu uchovávání 14 dní. Takže když Azure Backup identifikuje nastavení tak, aby bylo méně než 14 dní, pak se při každém spuštění úlohy zálohování nastaví na 14 dní.

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>Jaké jsou náklady vzniklé během doby uchování?

Během období podmíněného odstranění zůstanou náklady na chráněnou instanci a náklady na úložiště.  Také se vám bude účtovat využitá kapacita při běžné sazbě pro standardní sdílené složky a tempo úložiště snímků pro sdílené složky prémiových souborů.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>Můžu provést operaci obnovení, když jsou moje data ve stavu tichého odstranění?

Abyste mohli provádět operace obnovení, musíte nejdřív odstraněnou sdílenou složku zrušit. Operace zrušení odstranění zařadí sdílenou složku do zálohovaného stavu, ve kterém se můžete kdykoli vrátit k libovolnému bodu v čase. Informace o tom, jak zrušit odstranění sdílené složky, najdete na [tomto odkazu](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) nebo v tématu [zrušení odstranění sdílení souborů](./scripts/backup-powershell-script-undelete-file-share.md).

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>Jak můžu vymazat data sdílené složky v účtu úložiště, který má alespoň jednu chráněnou sdílenou složku?

Pokud máte v účtu úložiště aspoň jednu chráněnou sdílenou složku, znamená to, že pro všechny sdílené složky v tomto účtu je povolené obnovitelné odstranění a data se uchovávají po dobu 14 dnů od operace odstranění. Pokud ale chcete data Vymazat hned a nechcete, aby byla zachována, postupujte podle těchto kroků:

1. Pokud jste sdílenou složku už odstranili, když je povolené obnovitelné odstranění, odstraňte nejprve sdílenou složku z [portálu souborů](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) nebo pomocí [skriptu pro odstranění sdílené složky](./scripts/backup-powershell-script-undelete-file-share.md).
2. Pomocí kroků uvedených v [tomto dokumentu](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#disable-soft-delete)zakažte obnovitelné odstranění pro sdílené složky v účtu úložiště.
3. Nyní odstraňte sdílenou složku, jejíž obsah chcete okamžitě vyprázdnit.

>[!NOTE]
>Před zahájením další naplánované úlohy zálohování s chráněnou sdílenou složkou v účtu úložiště byste měli provést krok 2. Vzhledem k tomu, že při každém spuštění úlohy zálohování znovu povolíte obnovitelné odstranění pro všechny sdílené složky v účtu úložiště.

>[!WARNING]
>Po deaktivaci obnovitelného odstranění v kroku 2 je operace odstranění prováděná proti sdíleným složkám trvalá operace odstranění. Takže pokud omylem odstraníte zálohovanou sdílenou složku po zakázání obnovitelného odstranění, ztratíte všechny vaše snímky a nebudete moct data obnovit.

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>V kontextu nastavení obnovitelného odstranění sdílené složky se u kterých změn Azure Backup udělat, když zruším registraci účtu úložiště?

V době zrušení registrace Azure Backup kontroluje nastavení doby uchování pro sdílené složky a pokud je větší než 14 dní nebo méně než 14 dní, zůstane uchovávání tak, jak je. Pokud je však doba uchovávání 14 dnů, považujeme za povolenou Azure Backup, takže během procesu rušení registrace deaktivuje obnovitelné odstranění. Pokud chcete zrušit registraci účtu úložiště a přitom ponechat nastavení uchovávání, můžete ho znovu povolit z podokna účet úložiště po dokončení zrušení registrace. Postup konfigurace najdete na [tomto odkazu](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) .

## <a name="next-steps"></a>Další kroky

Informace o [Zálohování sdílených složek Azure z Azure Portal](backup-afs.md)
