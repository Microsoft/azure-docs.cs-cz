---
title: Deaktivace a odstranění Microsoft Azure StorSimple Virtual Array | Dokumentace Microsoftu
description: Popisuje postup odstranění zařízení StorSimple ze služby tak, že nejprve deaktivace a poté odstraní.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580534"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Deaktivace a odstranění StorSimple Virtual Array

## <a name="overview"></a>Přehled

Když deaktivujete StorSimple Virtual Array přerušit připojení mezi zařízením a odpovídající služby Správce zařízení StorSimple. Tento kurz vysvětluje následující postupy:

* Deaktivace zařízení 
* Odstranit deaktivované zařízení

Informace v tomto článku se vztahují pouze na virtuálních polí StorSimple. Informace o 8000 series, přejděte na tom, jak [deaktivovat nebo odstranit zařízení](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Kdy se mají deaktivovat?

Deaktivace je trvalé a není možné vrátit zpět. Deaktivované zařízení nelze ve službě Správce zařízení StorSimple zaregistrovat znovu. Budete muset deaktivace a odstranění StorSimple Virtual Array v následujících scénářích:

* **Plánované převzetí služeb při selhání** : Vaše zařízení je online a vy plánujete provést převzetí služeb při selhání zařízení. Pokud máte v úmyslu upgradovat na větší zařízení, budete muset převzetí služeb při selhání zařízení. Po převodu vlastnictví dat a dokončení převzetí služeb při selhání, se automaticky odstraní zdrojové zařízení.
* **Neplánované převzetí služeb při selhání** : Vaše zařízení je offline a potřebujete převzít služby při selhání zařízení. Tento scénář může dojít při havárii, pokud dojde k výpadku v datovém centru a primární zařízení je mimo provoz. Máte v plánu převzetí služeb při selhání zařízení sekundární zařízení. Po převodu vlastnictví dat a dokončení převzetí služeb při selhání, se automaticky odstraní zdrojové zařízení.
* **Vyřazení z provozu** : Chcete vyřadit z provozu zařízení. To vyžaduje, abyste nejprve deaktivovat a odstranit jej. Po deaktivaci zařízení už mít přístup k všechna data, která se ukládají místně. Je možné pouze přístup a obnovit data uložená v cloudu. Pokud budete chtít po deaktivaci zachovat data zařízení, musí před deaktivací zařízení pořídit snímek v cloudu všechna vaše data. Tento snímek v cloudu umožňuje obnovit všechna data v pozdější fázi.

## <a name="deactivate-a-device"></a>Deaktivace zařízení

Deaktivace zařízení, proveďte následující kroky.

#### <a name="to-deactivate-the-device"></a>Deaktivace zařízení

1. Ve službě, přejděte na **správy > zařízení**. V **zařízení** okna, klikněte na tlačítko a vyberte zařízení, které chcete deaktivovat.
   
    ![Vyberte zařízení k deaktivaci](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. Ve vaší **řídicího panelu zařízení** okna, klikněte na tlačítko **... Další** ze seznamu vyberte **deaktivovat**.
   
    ![Klikněte na tlačítko deaktivace](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. V **deaktivovat** okně zadejte název zařízení a potom klikněte na tlačítko **deaktivovat**. 
   
    ![Potvrďte deaktivaci](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Proces deaktivovat začíná a trvá několik minut.
   
    ![Probíhá deaktivace](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Po deaktivaci aktualizuje seznam zařízení.
   
    ![Deaktivovat dokončení](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Nyní můžete odstranit toto zařízení.

## <a name="delete-the-device"></a>Odstranění zařízení

Zařízení má ho nejprve deaktivovat ho odstranit. Odstraňuje se zařízení odebere ze seznamu zařízení připojených ke službě. Služba potom již nemůže spravovat odstraněné zařízení. Data přidružená k zařízení ale zůstává v cloudu. Tato data pak nabíhají poplatky.

Pokud chcete odstranit zařízení, proveďte následující kroky.

#### <a name="to-delete-the-device"></a>Chcete-li odstranit zařízení

1. Přejděte do Správce zařízení StorSimple **správy > zařízení**. V **zařízení** okně Výběr deaktivovaného zařízení, kterou chcete odstranit.
2. V **řídicího panelu zařízení** okna, klikněte na tlačítko **... Další** a potom klikněte na tlačítko **odstranit**.
   
   ![Vyberte zařízení k odstranění](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. V **odstranit** okně zadejte název vašeho zařízení k potvrzení odstranění a poté klikněte na tlačítko **odstranit**. Odstraněním tohoto zařízení nedojde k odstranění dat cloud přidružené k zařízení. 
   
   ![Potvrzení odstranění](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Odstranění začíná a trvá několik minut.
   
   ![Probíhá odstraňování](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Po odstranění zařízení můžete zobrazit aktualizovaný seznam zařízení.

## <a name="next-steps"></a>Další postup

* Informace o tom, jak převzetí služeb při selhání najdete v části [převzetí služeb při selhání a zotavení po havárii StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md).

* Další informace o tom, jak použít službu Správce zařízení StorSimple, přejděte na [použít službu Správce zařízení StorSimple ke správě StorSimple Virtual Array](storsimple-virtual-array-manager-service-administration.md). 

