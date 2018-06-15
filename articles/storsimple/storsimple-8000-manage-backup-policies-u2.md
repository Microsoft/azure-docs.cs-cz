---
title: Spravovat zásady zálohování řady StorSimple 8000 | Microsoft Docs
description: Vysvětluje, jak je možné použít službu StorSimple Manager zařízení k vytváření a správě ručního zálohování, plánů zálohování a uchovávání záloh na zařízení řady StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 569dbfdeb7dcd526cb5a54b487ea1bfb59b13cc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874868"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Použít službu StorSimple Manager zařízení na portálu Azure ke správě zásady zálohování


## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak používat službu StorSimple Manager zařízení **zálohování zásad** okno k řízení procesů zálohování a uchovávání záloh pro formátujte svazky zařízení StorSimple. Také popisuje, jak provést ruční zálohy.

Při zálohování svazku, můžete vytvořit místní snímek nebo cloudový snímek. Pokud zálohujete místně vázaný svazek, doporučujeme vám, že zadáváte cloudový snímek. Pořízení velký počet místní snímky místně vázaný svazek kombinaci s datovou sadou, která obsahuje mnoho změn způsobí v situaci, ve kterém můžete rychle spustit nedostatek místa na místní. Pokud se rozhodnete převést místní snímky, doporučujeme provést méně snímky denní zálohování nejnovější stav, je zachování za den a pak odstraňte je.

Pokud pořídíte snímek cloudu místně vázaný svazek, zkopírovat pouze změněné data do cloudu, kde je s odstraněním duplicitních dat a komprimované.

## <a name="the-backup-policy-blade"></a>Okno zásady zálohování

**Zálohování zásad** okno pro zařízení StorSimple umožňuje spravovat zásady zálohování a plánování místních a cloudových snímků. Zásady zálohování se používají ke konfiguraci plánů zálohování a uchovávání záloh pro kolekci svazků. Zásady zálohování umožňují pořízení snímku více svazků najednou. To znamená, že zálohy vytvořené zásady zálohování bude kopie konzistentní při selhání.

Výpis tabulkové zásady zálohování také vám umožní filtrovat existující zásady zálohování jednu nebo více z následujících polí:

* **Název zásady** – název přidružených k zásadě. Různé typy zásady patří:

  * Naplánované zásady, které jsou explicitně vytvořený uživatelem.
  * Importované zásady, které byly původně vytvořil v Snapshot Manager zařízení StorSimple. Tyto mají značku, která popisuje StorSimple Snapshot Manager hostitele, který zásady, které byly naimportovány z.

  > [!NOTE]
  > Automatická nebo výchozí zásady zálohování již není povoleno v době vytváření svazků.

* **Poslední úspěšná zálohy** – datum a čas poslední úspěšné zálohy pořízené touto zásadou.

* **Další zálohování** – datum a čas další naplánované zálohování, které bude inicializován pomocí těchto zásad.

* **Svazky** – svazky přidružených k zásadě. Všechny svazky, které jsou přidružené k zásadě zálohování jsou seskupeny dohromady při vytvoření zálohy.

* **Plány** – počet plány přidružené zásady zálohování.

Jsou často používaných operace, které můžete provést pro zásady zálohování:

* Přidání zásady zálohování
* Přidat nebo změnit plán
* Přidat nebo odebrat svazku
* Odstranit zásady zálohování
* Proveďte ruční zálohy

## <a name="add-a-backup-policy"></a>Přidání zásady zálohování

Přidáte zásadu zálohování automaticky naplánovat zálohování. Při prvním vytvoření svazku, neexistuje žádný výchozí zásady zálohování přidružené k svazku. Budete muset přidat a přiřadit zásady zálohování pro ochranu dat svazku.

Proveďte následující kroky na portálu Azure přidat zásady zálohování pro zařízení StorSimple. Po přidání zásady můžete definovat plán (viz [přidat nebo změnit plán](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Přidat nebo změnit plán

Můžete přidat nebo změnit plán, který je připojen k existující zásady zálohování v zařízení StorSimple. Proveďte následující kroky na portálu Azure přidat nebo upravit plán.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Přidat nebo odebrat svazku

Můžete přidat nebo odebrat svazek přiřazené zásady zálohování v zařízení StorSimple. Proveďte následující kroky na portálu Azure můžete přidat nebo odebrat svazek.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Odstranit zásady zálohování

Proveďte následující kroky na portálu Azure odstranit zásady zálohování v zařízení StorSimple.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Proveďte ruční zálohy

Proveďte následující kroky na portálu Azure vytvořit zálohu na vyžádání (ručně) pro jeden svazek.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Další kroky

Další informace o [pomocí služby StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

