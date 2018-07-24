---
title: Deaktivace a odstranění zařízení StorSimple 8000 series | Dokumentace Microsoftu
description: Popisuje postup odstranění zařízení StorSimple ze služby tak, že nejprve deaktivace a poté odstraní.
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
ms.date: 07/23/2018
ms.author: alkohli
ms.openlocfilehash: a2b764e76cd1987c83e7be38d365c1dfa8513db9
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214784"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Deaktivace a odstranění zařízení StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje, jak deaktivace a odstranění zařízení StorSimple, který je připojen ke službě Správce zařízení StorSimple. Pokyny v tomto článku se vztahuje pouze na zařízeních StorSimple řady 8000 včetně řešení StorSimple Cloud Appliance. Pokud používáte StorSimple Virtual Array, pak přejděte na [deaktivace a odstranění StorSimple Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md).

Deaktivace přeruší připojení mezi zařízením a odpovídající služby Správce zařízení StorSimple. Můžete chtít provést zařízení StorSimple mimo provoz (např. Pokud jsou nahrazení nebo upgrade zařízení nebo pokud už používáte StorSimple). Pokud ano, budete muset deaktivovat zařízení, než budete moct odstranit.

Po deaktivaci zařízení všechna data, která byla uložená místně na zařízení už nejsou dostupné. Je možné obnovit jenom data, které jsou přidružené k zařízení, která byla uložená v cloudu.

> [!WARNING]
> Deaktivace je trvalé a není možné vrátit zpět. Deaktivované zařízení nejde zaregistrovat ve službě Správce zařízení StorSimple, není-li obnovit tovární nastavení.
>
> Obnovení továrního nastavení procesu odstraní všechna data, která byla uložená místně na vašem zařízení. Proto je nutné provést cloudový snímek všech vašich dat před deaktivací zařízení. Tento snímek v cloudu umožňuje obnovit všechna data v pozdější fázi.

Po přečtení tohoto kurzu, budete moct:

* Deaktivace zařízení a odstraňte data.
* Deaktivace zařízení a zachovat data.

> [!NOTE]
> Před deaktivací fyzického zařízení StorSimple nebo cloudové zařízení zastavit nebo odstranit klienty a hostitele, které jsou závislé na tomto zařízení.


## <a name="deactivate-and-delete-data"></a>Deaktivace a odstranění dat

Pokud mají zájem o úplné odstraněním tohoto zařízení a nechcete uchovat data na zařízení, potom proveďte následující kroky.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Chcete-li deaktivovat a odstranit data

1. Před deaktivací zařízení, je nutné odstranit všechny svazku kontejnerů (a svazky) přidružené k zařízení. Kontejnery svazků můžete odstranit pouze po odstranění přidružené zálohy.

    > [!NOTE]
    > Před deaktivací cloudového zařízení a fyzickým zařízením StorSimple, ujistěte se, že data z kontejneru odstraněných svazku se ve skutečnosti odstraní ze zařízení. Můžete monitorovat grafy využití cloudu a když se zobrazí využití cloudu vyřadit z důvodu zálohy, který jste odstranili, potom můžete přejít k deaktivaci zařízení. Při deaktivaci zařízení, než dojde k této rozevírací, data se Bezvýchodná situace v účtu úložiště a nabíhají poplatky.

2. Deaktivace zařízení následujícím způsobem:
   
   1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V **zařízení** okno, vyberte zařízení, které chcete deaktivovat, klikněte pravým tlačítkem a pak klikněte na tlačítko **deaktivovat**.

        ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. V **deaktivovat** okno, zadejte název zařízení potvrďte a pak klikněte na tlačítko **deaktivovat**. Proces deaktivovat začíná a trvá několik minut.

        ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Po deaktivaci můžete zcela odstranit zařízení. Odstraňuje se zařízení odebere ze seznamu zařízení připojených ke službě. Služba potom již nemůže spravovat odstraněné zařízení. Pomocí následujících kroků odstraňte zařízení:
   
   1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V **zařízení** okně Výběr deaktivovaného zařízení, kterou chcete odstranit, klikněte pravým tlačítkem a pak klikněte na tlačítko **odstranit**.

        ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. V **odstranit** okno, zadejte název zařízení potvrďte a pak klikněte na tlačítko **odstranit**. Odstranění trvá několik minut.

        ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Po odstranění se úspěšně dokončí, budete upozorněni. Seznam zařízení se rovněž aktualizuje tak, aby odrážely odstranění.

## <a name="deactivate-and-retain-data"></a>Deaktivace a zachovat data

Pokud mají zájem odstraněním tohoto zařízení, ale chcete zachovat data, potom proveďte následující kroky:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Deaktivace zařízení a zachovat data
1. Deaktivujte zařízení. Všechny kontejnery svazků a snímků zařízení zůstanou.
   
   1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V **zařízení** okno, vyberte zařízení, které chcete deaktivovat, klikněte pravým tlačítkem a pak klikněte na tlačítko **deaktivovat**.

         ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. V **deaktivovat** okno, zadejte název zařízení potvrďte a pak klikněte na tlačítko **deaktivovat**. Proces deaktivovat začíná a trvá několik minut.

         ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Můžete teď převzetí služeb při selhání kontejnerů svazků a přidružené snímky. Postupy najdete v části [převzetí služeb při selhání a zotavení po havárii pro zařízení StorSimple](storsimple-8000-device-failover-disaster-recovery.md).
3. Po deaktivaci a převzetí služeb při selhání můžete zcela odstranit zařízení. Odstraňuje se zařízení odebere ze seznamu zařízení připojených ke službě. Služba potom již nemůže spravovat odstraněné zařízení. Pokud chcete odstranit zařízení, proveďte následující kroky:
   
   1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V **zařízení** okně Výběr deaktivovaného zařízení, kterou chcete odstranit, klikněte pravým tlačítkem a pak klikněte na tlačítko **odstranit**.

       ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. V **odstranit** okno, zadejte název zařízení potvrďte a pak klikněte na tlačítko **odstranit**. Odstranění trvá několik minut.

       ![Deaktivace zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Po odstranění se úspěšně dokončí, budete upozorněni. Seznam zařízení se rovněž aktualizuje tak, aby odrážely odstranění.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Deaktivace a odstranění cloudového zařízení

Deaktivace z portálu pro řešení StorSimple Cloud Appliance, zruší přidělení a odstraní virtuální počítač a prostředky vytvořené při jeho zřizování. Po deaktivaci cloudového zařízení není možné ho obnovit do předchozího stavu.

![Deaktivace řešení StorSimple Cloud Appliance](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Deaktivace za následek následující akce:

* Řešení StorSimple Cloud Appliance se odebere ze služby.
* Odstranění virtuálního počítače pro řešení StorSimple Cloud Appliance.
* Disk s operačním systémem a datové disky vytvořené pro řešení StorSimple Cloud Appliance zůstanou zachovány. Pokud nepoužíváte tyto entity, je nutné je odstranit ručně.
* Hostované služby a virtuální sítě, které se vytvořily při zřizování zůstanou zachovány. Pokud nepoužíváte tyto entity, je nutné je odstranit ručně.
* Cloudové snímky vytvořené službou řešení StorSimple Cloud Appliance se zachovají.

Po deaktivaci cloudového zařízení můžete ho odstranit ze seznamu zařízení. Vyberte deaktivované zařízení, klikněte pravým tlačítkem a pak klikněte na tlačítko **odstranit**. StorSimple oznámení po odstranění zařízení a seznam zařízení se aktualizuje.

## <a name="next-steps"></a>Další postup

* Deaktivované zařízení obnovíte výchozí tovární nastavení, přejděte na [obnovíte výchozí tovární nastavení zařízení](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Pro technickou podporu [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Další informace o tom, jak použít službu Správce zařízení StorSimple, přejděte na [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

