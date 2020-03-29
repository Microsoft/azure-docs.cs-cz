---
title: Deaktivace a odstranění virtuálního pole Microsoft Azure StorSimple | Dokumenty společnosti Microsoft
description: Popisuje, jak odebrat zařízení StorSimple ze služby nejprve deaktivací a jeho odstraněním.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: bb1a56d204a46f89213f20e317494120f0ea565e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580534"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Deaktivace a odstranění služby StorSimple Virtual Array

## <a name="overview"></a>Přehled

Když deaktivujete virtuální pole StorSimple, přerušíte spojení mezi zařízením a odpovídající službou StorSimple Device Manager. Tento kurz vysvětluje následující postupy:

* Deaktivace zařízení 
* Odstranění deaktivovaného zařízení

Informace v tomto článku platí pouze pro virtuální pole StorSimple. Informace o řadě 8000 naleznete v části Jak [deaktivovat nebo odstranit zařízení](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Kdy deaktivovat?

Deaktivace je trvalá operace a nelze ji vrátit zpět. Deaktivované zařízení nelze znovu zaregistrovat pomocí služby StorSimple Device Manager. Virtuální pole StorSimple bude pravděpodobně nutné deaktivovat a odstranit v následujících scénářích:

* **Plánované převzetí služeb při selhání** : Vaše zařízení je online a vy plánujete převzetí služeb při selhání zařízení. Pokud plánujete upgrade na větší zařízení, možná budete muset převést na selhání zařízení. Po přenosu vlastnictví dat a dokončení převzetí služeb při selhání se zdrojové zařízení automaticky odstraní.
* **Neplánované převzetí služeb při selhání** : Zařízení je offline a je třeba ho přepojit na selhání. K tomuto scénáři může dojít během havárie při výpadku v datovém centru a primární zařízení je mimo provoz. Plánujete převzetí služeb při selhání zařízení do sekundárního zařízení. Po přenosu vlastnictví dat a dokončení převzetí služeb při selhání se zdrojové zařízení automaticky odstraní.
* **Vyřazení z provozu** : Chcete zařízení vyřadit z provozu. To vyžaduje, abyste zařízení nejprve deaktivovali a poté ho odstranili. Když zařízení deaktivujete, nebudete mít již přístup k datům, která jsou uložena místně. K datům uloženým v cloudu a jejich obnovení lze přistupovat pouze. Pokud plánujete zachovat data zařízení po deaktivaci, měli byste před deaktivací zařízení pořizovat snímek cloudu všech dat. Tento snímek cloudu umožňuje obnovit všechna data v pozdější fázi.

## <a name="deactivate-a-device"></a>Deaktivace zařízení

Chcete-li zařízení deaktivovat, proveďte následující kroky.

#### <a name="to-deactivate-the-device"></a>Deaktivace zařízení

1. Ve službě přejděte na **management > zařízení**. V okně **Zařízení** klikněte a vyberte zařízení, které chcete deaktivovat.
   
    ![Vyberte zařízení, které chcete deaktivovat.](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. V okně **řídicího panelu zařízení** klikněte na **... Další** a ze seznamu vyberte **Deaktivovat**.
   
    ![Klikněte na deaktivovat](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. V okně **Deaktivovat** zadejte název zařízení a klepněte na tlačítko **Deaktivovat**. 
   
    ![Potvrdit deaktivaci](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Proces deaktivace se spustí a trvá několik minut.
   
    ![Deaktivace probíhá](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Po deaktivaci se seznam zařízení aktualizuje.
   
    ![Deaktivovat dokončení](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Nyní můžete toto zařízení odstranit.

## <a name="delete-the-device"></a>Smazat zařízení

Zařízení musí být nejprve deaktivováno, aby bylo odstraněno. Odstraněním zařízení odeberete ze seznamu zařízení připojených ke službě. Služba pak již nemůže spravovat odstraněné zařízení. Data přidružená k zařízení však zůstávají v cloudu. Tato data pak časově rozlišují poplatky.

Chcete-li zařízení odstranit, proveďte následující kroky.

#### <a name="to-delete-the-device"></a>Odstranění zařízení

1. Ve Správci zařízení StorSimple přejděte na **způsob správy > zařízení**. V okně **Zařízení** vyberte deaktivované zařízení, které chcete odstranit.
2. V okně **řídicího panelu zařízení** klikněte na **... Další** a potom klepněte na tlačítko **Odstranit**.
   
   ![Výběr zařízení, které chcete odstranit](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. Do okna **Odstranit** zadejte název zařízení, abyste odstranění potvrdili, a klepněte na tlačítko **Odstranit**. Odstraněním zařízení neodstraníte cloudová data přidružená k zařízení. 
   
   ![Potvrzení odstranění](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Odstranění se spustí a trvá několik minut.
   
   ![Probíhající odstranění](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Po odstranění zařízení můžete zobrazit aktualizovaný seznam zařízení.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak převzetí služeb při selhání, přejděte na [převzetí služeb při selhání a zotavení po havárii vašeho virtuálního pole StorSimple](storsimple-virtual-array-failover-dr.md).

* Další informace o používání služby StorSimple Device Manager naleznete v [části Správa virtuálního pole StorSimple pomocí služby StorSimple Device Manager](storsimple-virtual-array-manager-service-administration.md). 

