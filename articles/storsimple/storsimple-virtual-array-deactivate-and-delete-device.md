---
title: Deaktivovat a odstranit virtuální pole Microsoft Azure StorSimple | Microsoft Docs
description: Popisuje postup odstranění zařízení StorSimple ze služby tak, že nejprve deaktivace služby a poté ji odstranit.
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
ms.openlocfilehash: 8dea36f92b034f8c6cdb6875634848d37f4c6606
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875827"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Deaktivovat a odstranit pole virtuální zařízení StorSimple

## <a name="overview"></a>Přehled

Pokud deaktivujete o pole virtuální zařízení StorSimple, zrušení propojení mezi zařízením a odpovídající služby StorSimple Manager zařízení. Tento kurz vysvětluje postup:

* Deaktivace zařízení 
* Odstranit deaktivované zařízení

Informace v tomto článku se vztahují pouze na pole virtuální zařízení StorSimple. Informace o řady 8000, přejděte na postup [deaktivujte nebo odstraňte zařízení](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Kdy deaktivovat?

Deaktivace je trvalé a nedá se vrátit zpátky. Deaktivované zařízení pomocí služby StorSimple Manager zařízení nelze zaregistrovat znovu. Může třeba deaktivovat a odstranit o virtuální zařízení StorSimple pole v následujících scénářích:

* **Plánované převzetí služeb při selhání** : zařízení je online a chcete převzít zařízení. Pokud máte v úmyslu upgradovat na větší zařízení, musíte při selhání zařízení. Po převodu vlastnictví dat a dokončení převzetí služeb při selhání, zdrojového zařízení se automaticky odstraní.
* **Neplánované převzetí služeb při selhání** : zařízení je offline a potřebujete převzít zařízení. Tento scénář může dojít při havárii, když je výpadek v datovém centru a primární zařízení je mimo provoz. Chcete převzít zařízení sekundární zařízení. Po převodu vlastnictví dat a dokončení převzetí služeb při selhání, zdrojového zařízení se automaticky odstraní.
* **Vyřadit z provozu** : chcete vyřadit z provozu zařízení. To vyžaduje, abyste nejdřív deaktivovat zařízení a potom jej odstraňte. Po deaktivaci zařízení jste už mít přístup k všechna data, která je uložený místně. Je možné provádět pouze přístup a obnovit data uložená v cloudu. Pokud budete chtít zachovat data zařízení po deaktivaci, pak byste měli vzít cloudový snímek všechna vaše data před deaktivací zařízení. Tento snímek cloudu umožňuje obnovit všechna data v pozdější fázi.

## <a name="deactivate-a-device"></a>Deaktivace zařízení

Chcete-li deaktivovat zařízení, proveďte následující kroky.

#### <a name="to-deactivate-the-device"></a>Chcete-li deaktivovat zařízení

1. Ve službě, přejděte na **správy > zařízení**. V **zařízení** okně klikněte a vyberte zařízení, které chcete deaktivovat.
   
    ![Vyberte zařízení deaktivace](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. Ve vaší **řídicí panel zařízení** okně klikněte na tlačítko **... Další** a ze seznamu vyberte **deaktivovat**.
   
    ![Kliknutím na možnost deaktivovat](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. V **deaktivovat** okno, zadejte název zařízení a pak klikněte na tlačítko **deaktivovat**. 
   
    ![Potvrďte deaktivovat](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Proces deaktivovat spustí a trvá několik minut.
   
    ![Deaktivace v průběhu](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Po deaktivaci aktualizuje seznam zařízení.
   
    ![Deaktivovat dokončení](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Nyní můžete odstranit toto zařízení.

## <a name="delete-the-device"></a>Odstranění zařízení

Zařízení musí nejprve deaktivovat ho odstranit. Odstranění zařízení odebere ze seznamu zařízení připojená ke službě. Služba poté již nebude možné spravovat zařízení. Data přidružená k zařízení, ale zůstává v cloudu. Tato data pak nabíhají poplatky.

Pokud chcete odstranit zařízení, proveďte následující kroky.

#### <a name="to-delete-the-device"></a>Odstranění zařízení

1. Přejděte ve vašem Správci zařízení StorSimple na **správy > zařízení**. V **zařízení** okně vyberte deaktivované zařízení, které chcete odstranit.
2. V **řídicí panel zařízení** okně klikněte na tlačítko **... Další** a pak klikněte na **odstranit**.
   
   ![Vyberte zařízení k odstranění](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. V **odstranit** okno, zadejte název zařízení pro potvrzení odstranění a pak klikněte na tlačítko **odstranit**. Odstraněním zařízení nedojde k odstranění dat cloud přidružené k zařízení. 
   
   ![Potvrzení odstranění](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Odstranění se spustí a trvá několik minut.
   
   ![Probíhá odstraňování](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Po odstranění zařízení můžete zobrazit aktualizovaný seznam zařízení.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak převzetí služeb při selhání, přejděte na [převzetí služeb při selhání a zotavení po havárii vaše pole virtuální zařízení StorSimple](storsimple-virtual-array-failover-dr.md).

* Další informace o tom, jak používat službu StorSimple Manager zařízení, přejděte na [použít službu StorSimple Manager zařízení ke správě pole virtuální zařízení StorSimple](storsimple-virtual-array-manager-service-administration.md). 

