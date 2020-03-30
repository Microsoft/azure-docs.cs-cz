---
title: Správa zásad zálohování řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Vysvětluje, jak můžete pomocí služby StorSimple Device Manager vytvářet a spravovat ruční zálohování, plány zálohování a uchovávání záloh na zařízení řady StorSimple 8000.
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
ms.openlocfilehash: 607379f8645226a031646376df9ca18f4d3164bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267791"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Správa zásad zálohování pomocí služby StorSimple Device Manager na webu Azure Portal


## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak používat okno **zásad zálohování** služby StorSimple Device Manager k řízení procesů zálohování a uchovávání záloh pro vaše svazky StorSimple. Popisuje také, jak dokončit ruční zálohování.

Při zálohování svazku můžete vytvořit místní snímek nebo snímek cloudu. Pokud zálohujete místně vázaný svazek, doporučujeme zadat snímek cloudu. Vezmeme-li velký počet místních snímků místně vázaný svazek spolu s datovou sadou, která má velké množství změn bude mít za následek situaci, ve které by mohl rychle dojdou místní místo. Pokud se rozhodnete pořizovat místní snímky, doporučujeme pořídit méně denních snímků, abyste zálohovali nejnovější stav, uchovávali je na jeden den a pak je odstraňte.

Když pohrajete snímek cloudu místně vázaný svazek, zkopírujete pouze změněná data do cloudu, kde je deduplikována a komprimována.

## <a name="the-backup-policy-blade"></a>Okno zásad zálohování

Okno **zásad zálohování** pro vaše zařízení StorSimple umožňuje spravovat zásady zálohování a plánovat místní a cloudové snímky. Zásady zálohování se používají ke konfiguraci plánů zálohování a uchovávání záloh pro kolekci svazků. Zásady zálohování umožňují pořizovat snímek více svazků současně. To znamená, že zálohy vytvořené zásadou zálohování budou konzistentní s havárií kopie.

Tabulkový výpis zásad zálohování také umožňuje filtrovat existující zásady zálohování podle jednoho nebo více z následujících polí:

* **Název zásady** – název přidružený k této zásadě. Mezi různé typy zásad patří:

  * Naplánované zásady, které jsou explicitně vytvořeny uživatelem.
  * Importované zásady, které byly původně vytvořeny ve Správci snímků StorSimple. Ty mají značku, která popisuje storsimple snapshot manager hostitele, ze kterého byly importovány zásady.

  > [!NOTE]
  > Automatické nebo výchozí zásady zálohování již nejsou v době vytváření svazku povoleny.

* **Poslední úspěšná záloha** – datum a čas poslední úspěšné zálohy, která byla přijata s touto zásadou.

* **Další záloha** – datum a čas další naplánované zálohy, která bude iniciována touto zásadou.

* **Svazky** – svazky přidružené k zásadě. Všechny svazky přidružené k zásadě zálohování jsou seskupeny při vytváření záloh.

* **Plány** – počet plánů přidružených k zásadám zálohování.

Často používané operace, které lze provádět pro zásady zálohování jsou:

* Přidání zásady zálohování
* Přidání nebo úprava plánu
* Přidání nebo odebrání svazku
* Odstranění zásad zálohování
* Ruční zálohování

## <a name="add-a-backup-policy"></a>Přidání zásady zálohování

Přidejte zásady zálohování pro automatické plánování záloh. Při prvním vytvoření svazku neexistuje k svazku přidružena žádná výchozí zásada zálohování. Chcete-li chránit data svazku, je třeba přidat a přiřadit zásady zálohování.

Pomocí následujících kroků na webu Azure Portal přidejte zásady zálohování pro vaše zařízení StorSimple. Po přidání zásady můžete definovat plán (viz [Přidání nebo úprava plánu).](#add-or-modify-a-schedule)

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Přidání nebo úprava plánu

Můžete přidat nebo upravit plán, který je připojen k existující zásady zálohování na zařízení StorSimple. Pomocí následujících kroků na webu Azure Portal můžete přidat nebo upravit plán.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Přidání nebo odebrání svazku

Můžete přidat nebo odebrat svazek přiřazený k zásadám zálohování na zařízení StorSimple. Chcete-li přidat nebo odebrat svazek, proveďte na webu Azure Portal následující kroky.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Odstranění zásad zálohování

Na webu Azure Portal proveďte následující kroky a odstraňte zásady zálohování na zařízení StorSimple.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Ruční zálohování

Na webu Azure Portal proveďte následující kroky a vytvořte zálohu na vyžádání (ruční) pro jeden svazek.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Další kroky

Další informace o [správě zařízení StorSimple Device Manager pomocí služby StorSimple](storsimple-8000-manager-service-administration.md).

