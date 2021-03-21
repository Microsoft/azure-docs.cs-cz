---
title: Deaktivace a odstranění Microsoft Azure StorSimpleho virtuálního pole | Microsoft Docs
description: Popisuje, jak odebrat zařízení StorSimple ze služby tím, že ji nejdřív deaktivujete a pak ji odstraníte.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 84fd1a2d3d0dc4ce6960469ea3212b8ca1d4d07c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023019"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Deaktivace a odstranění služby StorSimple Virtual Array

## <a name="overview"></a>Přehled

Když deaktivujete virtuální pole StorSimple, přerušíte připojení mezi zařízením a odpovídající službou StorSimple Správce zařízení. Tento kurz vysvětluje následující postupy:

* Deaktivace zařízení 
* Odstranění deaktivovaného zařízení

Informace v tomto článku se týkají pouze virtuálních polí StorSimple. Informace o řadě 8000 najdete v tématu Jak [deaktivovat nebo odstranit zařízení](./storsimple-8000-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Kdy se má deaktivovat?

Deaktivace je TRVALá operace a nelze ji vrátit zpět. Nemůžete znovu zaregistrovat deaktivované zařízení ve službě StorSimple Správce zařízení. Může být nutné deaktivovat a odstranit virtuální pole StorSimple v následujících scénářích:

* **Plánované převzetí služeb při selhání** : vaše zařízení je online a vy plánujete převzít služby při selhání zařízení. Pokud plánujete upgradovat na větší zařízení, možná bude nutné provést převzetí služeb při selhání zařízení. Po přenosu vlastnictví dat a dokončení převzetí služeb při selhání se zdrojové zařízení automaticky odstraní.
* **Neplánované převzetí služeb při selhání** : vaše zařízení je offline a v zařízení je potřeba převzít služby při selhání. K tomuto scénáři může dojít během havárie, když dojde k výpadku v datovém centru a primární zařízení je mimo provoz. Plánujete převzít služby zařízení při selhání do sekundárního zařízení. Po přenosu vlastnictví dat a dokončení převzetí služeb při selhání se zdrojové zařízení automaticky odstraní.
* **Vyřazeno z provozu** : chcete zařízení vyřadit z provozu. To vyžaduje, abyste nejdřív zařízení deaktivovali a pak ho odstranili. Když deaktivujete zařízení, nebudete už mít přístup k žádným místně uloženým datům. Můžete získat přístup k datům uloženým v cloudu a jenom je obnovit. Pokud máte v úmyslu zachovat data zařízení po deaktivaci, měli byste před deaktivací zařízení pořídit snímek všech vašich dat v cloudu. Tento snímek v cloudu umožňuje obnovit všechna data v pozdější fázi.

## <a name="deactivate-a-device"></a>Deaktivace zařízení

K deaktivaci zařízení proveďte následující kroky.

#### <a name="to-deactivate-the-device"></a>Deaktivace zařízení

1. V rámci služby přejdete na **správa > zařízení**. V okně **zařízení** klikněte na zařízení, které chcete deaktivovat, a vyberte ho.
   
    ![Vyberte zařízení, které chcete deaktivovat.](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. V okně **řídicí panel zařízení** klikněte na **...** V seznamu vyberte možnost **deaktivovat**.
   
    ![Klikněte na deaktivovat.](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. V okně **deaktivovat** zadejte název zařízení a klikněte na **deaktivovat**. 
   
    ![Potvrďte deaktivaci](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Proces deaktivace se spustí a dokončení bude trvat několik minut.
   
    ![Probíhá deaktivace](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Po deaktivaci se seznam zařízení aktualizuje.
   
    ![Deaktivovat dokončení](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Teď můžete toto zařízení odstranit.

## <a name="delete-the-device"></a>Smazat zařízení

Aby se zařízení odstranilo, musí se nejdřív deaktivovat. Odstraněním zařízení se odebere ze seznamu zařízení připojených ke službě. Služba pak už nebude moct odstraněné zařízení spravovat. Data, která jsou přidružená k zařízení, ale zůstanou v cloudu. Tato data pak účtují poplatky.

Pokud chcete zařízení odstranit, proveďte následující kroky.

#### <a name="to-delete-the-device"></a>Postup odstranění zařízení

1. V Správce zařízení StorSimple, navštivte **> Správa zařízení**. V okně **zařízení** vyberte deaktivované zařízení, které chcete odstranit.
2. V okně **řídicí panel zařízení** klikněte na **... A pak** klikněte na **Odstranit**.
   
   ![Vyberte zařízení, které chcete odstranit.](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. V okně **Odstranit** zadejte název vašeho zařízení a potvrďte odstranění a pak klikněte na **Odstranit**. Když zařízení odstraníte, neodstraní se data cloudu přidružená k tomuto zařízení. 
   
   ![Potvrzení odstranění](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Odstranění se spustí a zabere několik minut, než se dokončí.
   
   ![Probíhá odstraňování.](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Po odstranění zařízení můžete zobrazit aktualizovaný seznam zařízení.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak převzít služby při selhání, najdete v tématu [převzetí služeb při selhání a zotavení po havárii ve virtuálním poli StorSimple](storsimple-virtual-array-failover-dr.md).

* Další informace o tom, jak používat službu StorSimple Správce zařízení, najdete v článku [použití služby StorSimple Správce zařízení ke správě vašeho virtuálního pole StorSimple](storsimple-virtual-array-manager-service-administration.md).