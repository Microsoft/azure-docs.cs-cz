---
title: Deaktivace a odstranění zařízení řady StorSimple 8000 | Microsoft Docs
description: Popisuje postup odstranění zařízení StorSimple ze služby tak, že nejprve deaktivace služby a poté ji odstranit.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 3c00867a29cf8343a57e74e2aabe3971ae6837af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874966"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Deaktivace a odstranění zařízení StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje postup deaktivace a odstranění zařízení StorSimple, která je připojena k službě StorSimple Manager zařízení. Pokyny v tomto článku se vztahuje pouze na řadu zařízení StorSimple 8000 včetně zařízení cloudu StorSimple. Pokud používáte o pole virtuální zařízení StorSimple, potom přejděte na [deaktivace a odstranění o pole virtuální zařízení StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Deaktivace přeruší připojení mezi zařízením a odpovídající služby StorSimple Manager zařízení. Můžete chtít provést zařízení StorSimple mimo provoz (např. Pokud jsou výměna nebo upgrade zařízení nebo pokud už používáte StorSimple). Pokud ano, budete muset před odstraněním jej deaktivovat zařízení.

Po deaktivaci zařízení žádná data, která byla uložená místně na zařízení již není dostupný. Lze obnovit pouze data, které jsou přidružené k zařízení, která byla uložená v cloudu.

> [!WARNING]
> Deaktivace je trvalé a nedá se vrátit zpátky. Deaktivované zařízení nelze zaregistrovat u služby StorSimple Manager zařízení, pokud se resetují na výchozí tovární nastavení.
>
> Objekt factory resetovat proces odstraní všechna data, která byla uložená místně na vašem zařízení. Proto je třeba provést cloudový snímek všechna vaše data před deaktivací zařízení. Tento snímek cloudu umožňuje obnovit všechna data v pozdější fázi.

Po přečtení tohoto kurzu, budete moci:

* Deaktivace zařízení a odstranit data.
* Deaktivace zařízení a ukládat data.

> [!NOTE]
> Před deaktivací fyzického zařízení StorSimple nebo cloudu zařízení, zastavte nebo odstraňte klienty a hostitele, které jsou závislé na tomto zařízení.


## <a name="deactivate-and-delete-data"></a>Deaktivace a odstranění dat

Pokud mají zájem o odstraňování zařízení zcela a nechcete ukládat data na zařízení, potom proveďte následující kroky.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Chcete-li deaktivovat zařízení a odstranění dat

1. Před deaktivací zařízení, je nutné odstranit všechny svazku kontejnery (a svazcích) přidružené k zařízení. Kontejnery svazků můžete odstranit až poté, co jste odstranili přidružených záloh.

    > [!NOTE]
    > Před deaktivací fyzického zařízení StorSimple nebo cloudu zařízení, ujistěte se, že data z kontejneru odstraněné svazku je ve skutečnosti odstranit ze zařízení. Můžete monitorovat grafy využívání cloud a po zobrazení využití cloudu vyřadit z důvodu zálohování, které jste odstranili, potom můžete přejít k deaktivovat zařízení. Pokud deaktivujete zařízení, než dojde k této rozevírací, data se Bezvýchodná situace v účtu úložiště a nabíhají poplatky.

2. Deaktivace zařízení následujícím způsobem:
   
   1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V **zařízení** okně vyberte zařízení, které chcete deaktivovat, klikněte pravým tlačítkem a pak klikněte na tlačítko **deaktivovat**.

        ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. V **deaktivovat** okno, zadejte název zařízení pro potvrzení a pak klikněte na tlačítko **deaktivovat**. Proces deaktivovat spustí a trvá několik minut.

        ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Po deaktivaci můžete odstranit zařízení zcela. Odstranění zařízení odebere ze seznamu zařízení připojená ke službě. Služba poté již nebude možné spravovat zařízení. Pomocí následujících kroků můžete odstranit zařízení:
   
   1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V **zařízení** okně vyberte deaktivované zařízení, který chcete odstranit, klikněte pravým tlačítkem a pak klikněte na tlačítko **odstranit**.

        ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. V **odstranit** okno, zadejte název zařízení pro potvrzení a pak klikněte na tlačítko **odstranit**. Odstranění trvá několik minut.

        ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Po odstranění je úspěšně dokončete, budete upozorněni. V seznamu zařízení také aktualizuje tak, aby odrážela odstranění.

## <a name="deactivate-and-retain-data"></a>Deaktivovat a zachovat data

Pokud jsou zájem o odstraňování zařízení, ale chcete zachovat data, potom proveďte následující kroky:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Chcete-li deaktivovat zařízení a ukládat data
1. Deaktivujte zařízení. Všechny kontejnery svazků a snímky zařízení zůstanou.
   
   1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V **zařízení** okně vyberte zařízení, které chcete deaktivovat, klikněte pravým tlačítkem a pak klikněte na tlačítko **deaktivovat**.

         ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. V **deaktivovat** okno, zadejte název zařízení pro potvrzení a pak klikněte na tlačítko **deaktivovat**. Proces deaktivovat spustí a trvá několik minut.

         ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Nyní můžete převzít kontejnery svazků a přidružené snímky. Postupy, přejděte na [převzetí služeb při selhání a zotavení po havárii pro zařízení StorSimple](storsimple-8000-device-failover-disaster-recovery.md).
3. Po deaktivaci a převzetí služeb při selhání můžete odstranit zařízení zcela. Odstranění zařízení odebere ze seznamu zařízení připojená ke službě. Služba poté již nebude možné spravovat zařízení. Pokud chcete odstranit zařízení, proveďte následující kroky:
   
   1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V **zařízení** okně vyberte deaktivované zařízení, který chcete odstranit, klikněte pravým tlačítkem a pak klikněte na tlačítko **odstranit**.

       ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. V **odstranit** okno, zadejte název zařízení pro potvrzení a pak klikněte na tlačítko **odstranit**. Odstranění trvá několik minut.

       ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Po odstranění je úspěšně dokončete, budete upozorněni. V seznamu zařízení také aktualizuje tak, aby odrážela odstranění.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Deaktivace a odstranění zařízení cloudu

Pro zařízení StorSimple cloudu deaktivace z portálu zruší přidělení a odstraní virtuální počítač a prostředky vytvořené při jeho zřizování. Po deaktivaci cloudového zařízení není možné ho obnovit do předchozího stavu.

![Deaktivace zařízení StorSimple cloudu](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Deaktivaci má za následek následující akce:

* Zařízení StorSimple cloudu se odebere ze služby.
* Virtuální počítač pro zařízení StorSimple cloudu je odstranit.
* Disk operačního systému a datové disky, které jsou vytvořené pro zařízení StorSimple cloudu se odeberou.
* Hostované služby a virtuální sítě, které byly vytvořeny při zřizování zůstanou zachovány. Pokud nepoužíváte tyto entity, odstraňte je ručně.
* Cloudové snímky vytvořené cloudové zařízení StorSimple se zachovají.

Po deaktivaci cloudu zařízení ji můžete odstranit ze seznamu zařízení. Vyberte deaktivované zařízení, klikněte pravým tlačítkem a pak klikněte na tlačítko **odstranit**. StorSimple oznámení po odstranění zařízení a seznam zařízení aktualizací.

## <a name="next-steps"></a>Další kroky

* Chcete-li obnovit deaktivované zařízení výchozí tovární nastavení, přejděte na [zařízení resetovat výchozí tovární nastavení](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Pro technickou podporu [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Další informace o tom, jak používat službu StorSimple Manager zařízení, přejděte na [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

