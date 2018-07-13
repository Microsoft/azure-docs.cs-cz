---
title: Správa zásad zálohování StorSimple 8000 series | Dokumentace Microsoftu
description: Vysvětluje, jak můžete pomocí služby Správce zařízení StorSimple k vytváření a správě ručního zálohování, plány zálohování a uchování zálohy na zařízení řady StorSimple 8000.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38681578"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Správa zásad zálohování pomocí služby Správce zařízení StorSimple na webu Azure portal


## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak pomocí služby Správce zařízení StorSimple **zásady zálohování** okno pro řízení procesů zálohování a uchovávání záloh pro svazky zařízení StorSimple. Také popisuje, jak dokončit ručního zálohování.

Při zálohování svazku, můžete vytvořit místní snímek nebo cloudový snímek. Pokud zálohujete místně vázaný svazek, doporučujeme zadat snímek v cloudu. V situaci, ve kterém můžete rychle spustit mimo místní prostor způsobí trvá velký počet místních snímků místně vázaný svazek s datovou sadou, která obsahuje mnoho změn s velkou provázaností. Pokud vyberete možnost vytvářet místní snímky, doporučujeme trvat méně denní snímky zálohovat aktuální stav je zachovat za den a potom je odstraňte.

Při pořídíte cloudový snímek místně vázaný svazek, kopírovat změněná data do cloudu, kde je s odstraněním duplicitních dat a komprimované.

## <a name="the-backup-policy-blade"></a>Okno zásady zálohování

**Zásady zálohování** okno pro zařízení StorSimple umožňuje spravovat zásady zálohování a plánování místních a cloudových snímků. Zásady zálohování se používají ke konfiguraci plány zálohování a uchovávání záloh pro kolekci svazků. Zásady zálohování umožňují vytvoření snímku více svazků najednou. To znamená, že zálohy vytvořené zásady zálohování bude konzistentní kopie.

Zásady zálohování tabulkovém výpisu také vám umožní filtrovat existující zásady zálohování jednu nebo více z následujících polí:

* **Název zásad** – název přidružených k zásadě. Zahrnout různé typy zásad:

  * Naplánované zásady, které jsou explicitně vytvořený uživatelem.
  * Importované zásady, které byly původně vytvořeny StorSimple Snapshot Manageru. Ty se musí značky, které popisují, které zásady byly naimportovány z hostitele StorSimple Snapshot Manageru.

  > [!NOTE]
  > Automatické nebo výchozí zásady zálohování jsou již není povolena v době vytvoření svazku.

* **Poslední úspěšná záloha** – datum a čas posledního úspěšného zálohování, která byla provedena s touto zásadou.

* **Příští zálohování** – datum a čas další plánované zálohy, aktivuje se v těchto zásadách.

* **Svazky** – svazky přidružené k těmto zásadám. Všechny svazky, které jsou přidružené k těmto zásadám zálohování jsou seskupené dohromady při vytváření zálohy.

* **Plány** – počet plánů, které jsou přidružené k těmto zásadám zálohování.

Jsou často používaných operací, které můžete provést pro zásady zálohování:

* Přidání zásady zálohování
* Přidat nebo změnit plán
* Přidat nebo odebrat svazek
* Odstraňování zásady zálohování
* Proveďte ruční zálohování

## <a name="add-a-backup-policy"></a>Přidání zásady zálohování

Přidání zásady zálohování a automaticky naplánovat zálohování. Při prvním vytvoření svazku, neexistuje žádný výchozí zásady zálohování přidružené k svazku. Budete muset přidat a přiřadit zásady zálohování pro ochranu dat svazku.

Proveďte následující kroky na webu Azure Portal k přidání zásady zálohování pro zařízení StorSimple. Jakmile přidáte zásady, můžete definovat plán (viz [přidat nebo změnit plán](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Přidat nebo změnit plán

Můžete přidat nebo upravit plán, který je připojen k existující zásadu zálohování zařízení StorSimple. Proveďte následující kroky na webu Azure Portal přidat nebo upravit plán.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Přidat nebo odebrat svazek

Můžete přidat nebo odebrat svazek přiřazené zásady zálohování v zařízení StorSimple. Proveďte následující kroky na webu Azure Portal přidat nebo odebrat svazek.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Odstraňování zásady zálohování

Proveďte následující kroky na webu Azure Portal k odstraňování zásady zálohování v zařízení StorSimple.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Proveďte ruční zálohování

Proveďte následující kroky na webu Azure Portal k vytvoření zálohy na vyžádání (ručně) pro samostatný svazek.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Další postup

Další informace o [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

