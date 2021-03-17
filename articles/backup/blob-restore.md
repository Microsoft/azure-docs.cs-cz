---
title: Obnovení objektů blob Azure
description: Přečtěte si, jak obnovit objekty blob Azure (ve verzi Preview).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 4cbd47ea654115f00095e30f7d5114aec0f2c85a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744792"
---
# <a name="restore-azure-blobs-in-preview"></a>Obnovení objektů blob Azure (ve verzi Preview)

Objekty blob bloku v účtech úložiště s nakonfigurovaným provozním zálohováním je možné obnovit do libovolného bodu v čase v rámci rozsahu uchování. Můžete také nastavit rozsah obnovení na všechny objekty blob bloku v účtu úložiště nebo na podmnožinu objektů BLOB.

## <a name="before-you-start"></a>Než začnete

- Objekty blob budou obnoveny do stejného účtu úložiště. Objekty blob, které byly po dobu, kdy obnovujete změny, budou přepsány.
- V rámci operace obnovení lze obnovit pouze objekty blob bloku v účtu úložiště úrovně Standard pro obecné účely verze 2. Nejsou obnoveny objekty blob, objekty blob stránky a objekty blob bloku úrovně Premium.
- V průběhu probíhající úlohy obnovení nelze načíst objekty BLOB v úložišti ani je do nich zapisovat.
- Objekt BLOB s aktivním zapůjčením nejde obnovit. Pokud je objekt BLOB s aktivním zapůjčením zahrnutý do rozsahu objektů blob, které se mají obnovit, operace obnovení se automaticky nezdařila. Před zahájením operace obnovení podělte všechna aktivní zapůjčení.
- Snímky se v rámci operace obnovení nevytváří ani neodstraňují. Do předchozího stavu se obnoví jenom základní objekt BLOB.
- Pokud odstraníte kontejner z účtu úložiště voláním operace **odstranění kontejneru** , nelze tento kontejner obnovit pomocí operace obnovení. Místo odstranění celého kontejneru odstraňte jednotlivé objekty blob, pokud je budete chtít později obnovit. Kromě toho společnost Microsoft doporučuje povolit obnovitelné odstranění kontejnerů společně s provozním zálohováním a chránit před náhodným odstraněním kontejnerů.
- Všechna omezení a podporované scénáře najdete v [matici podpory](blob-backup-support-matrix.md) .

## <a name="restore-blobs"></a>Obnovit objekty blob

Obnovení můžete iniciovat prostřednictvím centra zálohování.

1. V centru pro zálohování klikněte na **obnovit** na horním panelu.

    ![Obnovení v centru zálohování](./media/blob-restore/backup-center-restore.png)

1. Na kartě **zahájit obnovení** zvolte jako typ zdroje dat **Azure Blob (Azure Storage)** a vyberte **instanci zálohování** , kterou chcete obnovit. Instance zálohy je tady účet úložiště, který obsahuje objekty blob, které chcete obnovit.

     ![Vybrat instanci zálohování](./media/blob-restore/select-backup-instance.png)

1. Na kartě **Vybrat bod obnovení** vyberte datum a čas, ze kterého chcete data obnovit. Pomocí posuvníku můžete také zvolit bod v čase, ze kterého chcete obnovit. Informační bublina vedle data zobrazuje platnou dobu trvání, ze které můžete obnovit data. Provozní zálohování pro objekty blob průběžné zálohování poskytuje podrobnější kontrolu nad body pro obnovení dat z.

    >[!NOTE]
    > Čas, který se tady popisuje, je váš místní čas.

    ![Datum a čas pro obnovení](./media/blob-restore/date-and-time.png)

1. Na kartě **obnovit parametry** vyberte, jestli chcete obnovit všechny objekty BLOB v účtu úložiště, konkrétní kontejnery nebo podmnožinu objektů BLOB pomocí shody předpon. Při použití shody předpon můžete zadat až 10 rozsahů předpon nebo cest k příponám. Další podrobnosti o používání shody předpon [najdete tady](#use-prefix-match-for-restoring-blobs).

    ![Obnovit parametry](./media/blob-restore/restore-parameters.png)

    Vyberte jednu z těchto možností:

    - **Obnovit všechny objekty BLOB v účtu úložiště**: pomocí této možnosti obnovíte všechny objekty blob bloku v účtu úložiště tak, že je vrátíte zpátky k vybranému bodu v čase. Obnovení účtů úložiště, které obsahují velké objemy dat nebo určující vysokou četnost změn, může trvat delší dobu.

    - **Procházet a obnovit vybrané kontejnery**: pomocí této možnosti můžete procházet a vybírat až 10 kontejnerů, které se mají obnovit. Musíte mít dostatečná oprávnění k zobrazení kontejnerů v účtu úložiště, jinak nemusí být možné zobrazit obsah účtu úložiště.

    - **Vyberte objekty blob pro obnovení pomocí shody předpon**: Tato možnost umožňuje obnovit podmnožinu objektů BLOB pomocí shody předpony. Můžete zadat až 10 lexicographical rozsahů objektů BLOB v jednom kontejneru nebo napříč více kontejnery, aby se tyto objekty blob vracely do jejich předchozího stavu v daném časovém okamžiku. Tady je několik věcí, které je potřeba vzít v úvahu:

        - K vymezení názvu kontejneru z předpony objektu blob můžete použít lomítko (/).
        - Začátek zadaného rozsahu je stejný, ale zadaný rozsah je exkluzivní.

    Další informace o použití předpon pro obnovení rozsahů objektů BLOB najdete v [této části](#use-prefix-match-for-restoring-blobs).

1. Jakmile dokončíte zadávání objektů BLOB k obnovení, pokračujte na kartu **Revize + obnovení** a výběrem možnosti **obnovit** spusťte obnovení.

1. **Sledovat obnovení**: k sledování podrobností a stavu obnovení použijte zobrazení **úlohy zálohování** . Provedete to tak, že přejdete na úlohy zálohovacího **centra zálohování**  >  . **V** průběhu provádění obnovení se zobrazí stav.

    ![Karta úlohy zálohování](./media/blob-restore/backup-jobs.png)

    Po úspěšném dokončení operace obnovení se stav změní na **dokončeno**. Po úspěšném dokončení obnovení budete moct znovu číst a zapisovat objekty BLOB v účtu úložiště.

## <a name="additional-topics"></a>Další témata

### <a name="use-prefix-match-for-restoring-blobs"></a>Použití shody předpon pro obnovení objektů BLOB

Uvažujte následující příklad:

![Obnovit s prefixovou shodou](./media/blob-restore/prefix-match.png)

Operace obnovení zobrazená na obrázku provádí následující akce:

- Obnoví celý obsah *container1*.
- Obnoví objekty BLOB v rozsahu lexicographical *blob1* prostřednictvím *blob5* v *container2*. Tento rozsah obnoví objekty BLOB s názvy, například *blob1*, *blob11*, *blob100*, *blob2* a tak dále. Vzhledem k tomu, že konec rozsahu je exkluzivní, obnoví objekty blob, jejichž názvy začínají na *blob4*, ale neobnoví objekty blob, jejichž názvy začínají na *blob5*.
- Obnoví všechny objekty BLOB v *container3* a *container4*. Vzhledem k tomu, že konec rozsahu je exkluzivní, tento rozsah neobnoví *container5*.

## <a name="next-steps"></a>Další kroky

- [Přehled operačního zálohování pro objekty blob Azure (ve verzi Preview)](blob-backup-overview.md)
