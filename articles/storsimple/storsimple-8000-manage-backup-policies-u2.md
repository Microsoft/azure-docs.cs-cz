---
title: Správa zásad zálohování řady StorSimple 8000 | Microsoft Docs
description: Vysvětluje, jak můžete službu StorSimple Device Manager použít k vytváření a správě ručních záloh, plánů zálohování a uchovávání záloh na zařízení řady StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 9ec2bbef88ed185c3927676ad2596269783d1850
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85515622"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Ke správě zásad zálohování použijte službu StorSimple Device Manager v Azure Portal.


## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak pomocí okna **zásady zálohování** služby StorSimple Device Manager řídit procesy zálohování a uchovávání záloh pro vaše svazky StorSimple. Také popisuje, jak provést ruční zálohování.

Při zálohování svazku můžete zvolit vytvoření místního snímku nebo snímku v cloudu. Pokud zálohujete místně připojený svazek, doporučujeme, abyste určili snímek v cloudu. Výsledkem většího počtu místních snímků místně připnutého svazku, na kterém je propojená sada dat s velkým množstvím změn, je situace, kdy se můžete rychle dostat mimo místní prostor. Pokud se rozhodnete použít místní snímky, doporučujeme vám, abyste měli méně denní snímky pro zálohování nejaktuálnějšího stavu, zachováte je za den a pak je odstraníte.

Když pořídíte snímek v cloudu místně připojeného svazku, kopírujete jenom změněná data do cloudu, kde se odstraňují a komprimují.

## <a name="the-backup-policy-blade"></a>Okno zásady zálohování

Okno **zásady zálohování** pro zařízení StorSimple umožňuje spravovat zásady zálohování a naplánovat místní a cloudové snímky. Zásady zálohování se používají ke konfiguraci plánů zálohování a uchovávání záloh pro kolekci svazků. Zásady zálohování umožňují pořizovat snímek více svazků současně. To znamená, že zálohy vytvořené zásadami zálohování budou kopie konzistentní vzhledem k chybě.

Tabulkový výpis zásad zálohování také umožňuje filtrovat existující zásady zálohování podle jednoho nebo více následujících polí:

* **Název zásady** – název přidružený k zásadě. Mezi různé typy zásad patří:

  * Naplánované zásady, které uživatel výslovně vytvořil.
  * Importované zásady, které se původně vytvořily v StorSimple Snapshot Manager. Mají značku, která popisuje StorSimple Snapshot Manager hostitele, ze kterého byly zásady naimportovány.

  > [!NOTE]
  > Automatické nebo výchozí zásady zálohování už nejsou povolené v době vytváření svazku.

* **Poslední úspěšná záloha** – datum a čas posledního úspěšného zálohování, které se provedlo pomocí této zásady.

* **Další zálohování** – datum a čas příštího plánovaného zálohování, které budou touto zásadou iniciovány.

* **Svazky** – svazky přidružené k zásadám. Všechny svazky přidružené k zásadám zálohování se při vytváření záloh seskupují dohromady.

* **Plány** – počet plánů přidružených k zásadě zálohování.

Často používané operace, které můžete provést pro zásady zálohování, jsou tyto:

* Přidání zásady zálohování
* Přidat nebo upravit plán
* Přidat nebo odebrat svazek
* Odstranění zásady zálohování
* Provedení ručního zálohování

## <a name="add-a-backup-policy"></a>Přidání zásady zálohování

Přidejte zásady zálohování pro automatické naplánování zálohování. Při prvním vytvoření svazku nejsou k tomuto svazku přidruženy žádné výchozí zásady zálohování. Chcete-li chránit data svazků, je nutné přidat a přiřadit zásady zálohování.

Chcete-li přidat zásady zálohování pro zařízení StorSimple, proveďte následující kroky v Azure Portal. Po přidání zásady můžete definovat plán (viz [Přidat nebo upravit plán](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Přidat nebo upravit plán

Můžete přidat nebo upravit plán, který je připojený k existující zásadě zálohování na zařízení StorSimple. Chcete-li přidat nebo upravit plán, proveďte následující kroky v Azure Portal.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Přidat nebo odebrat svazek

Na zařízení StorSimple můžete přidat nebo odebrat svazek přiřazený k zásadám zálohování. Chcete-li přidat nebo odebrat svazek, proveďte následující kroky v Azure Portal.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Odstranění zásady zálohování

Proveďte následující kroky v Azure Portal k odstranění zásad zálohování na zařízení StorSimple.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Provedení ručního zálohování

Pokud chcete vytvořit zálohu na vyžádání (ruční) pro jeden svazek, proveďte následující kroky v Azure Portal.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [používání služby StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

