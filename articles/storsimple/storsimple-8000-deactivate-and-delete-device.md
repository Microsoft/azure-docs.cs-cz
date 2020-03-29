---
title: Deaktivace a odstranění zařízení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Popisuje, jak odebrat zařízení StorSimple ze služby nejprve deaktivací a jeho odstraněním.
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
ms.openlocfilehash: 116ac5c4efda87b5d16336dd326d516299f6955d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61481936"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Deaktivace a odstranění zařízení StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje, jak deaktivovat a odstranit zařízení StorSimple, které je připojeno ke službě StorSimple Device Manager. Pokyny v tomto článku platí pouze pro zařízení řady StorSimple 8000, včetně storsimple cloudových zařízení. Pokud používáte storsimple virtuální pole, přejděte na [Deaktivovat a odstranit StorSimple Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md).

Deaktivace přeruší spojení mezi zařízením a odpovídající službou Správce zařízení StorSimple. Možná budete chtít vzít Zařízení StorSimple z provozu (například pokud je výměna nebo upgrade zařízení nebo pokud již nepoužíváte StorSimple). Pokud ano, musíte zařízení před odstraněním deaktivovat.

Když zařízení deaktivujete, všechna data, která byla v zařízení uložena místně, již nejsou přístupná. Lze obnovit pouze data přidružená k zařízení, které bylo uloženo v cloudu.

> [!WARNING]
> Deaktivace je trvalá operace a nelze ji vrátit zpět. Deaktivované zařízení nelze zaregistrovat ve službě StorSimple Device Manager, pokud není resetováno na výchozí hodnoty z výroby.
>
> Proces obnovení továrního nastavení odstraní všechna data, která byla uložena místně ve vašem zařízení. Proto je nutné pořizovat snímek cloudu všech dat před deaktivací zařízení. Tento snímek cloudu umožňuje obnovit všechna data v pozdější fázi.

Po přečtení tohoto výukového programu, budete moci:

* Deaktivujte zařízení a odstraňte data.
* Deaktivujte zařízení a uchovávejte data.

> [!NOTE]
> Před deaktivací fyzického zařízení Nebo cloudového zařízení StorSimple zastavte nebo odstraňte klienty a hostitele, kteří jsou na tomto zařízení závislí.


## <a name="deactivate-and-delete-data"></a>Deaktivace a odstranění dat

Máte-li zájem o úplné odstranění zařízení a nechcete uchovávat data v zařízení, proveďte následující kroky.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Deaktivace zařízení a odstranění dat

1. Před deaktivací zařízení je nutné odstranit všechny kontejnery svazků (a svazky) přidružené k zařízení. Kontejnery svazků můžete odstranit až po odstranění přidružených záloh.

    > [!NOTE]
    > Před deaktivací fyzického zařízení Nebo cloudového zařízení StorSimple se ujistěte, že data z odstraněného kontejneru svazku jsou ze zařízení skutečně odstraněna. Můžete sledovat grafy spotřeby cloudu a když uvidíte pokles využití cloudu kvůli zálohám, které jste odstranili, můžete pokračovat v deaktivaci zařízení. Pokud zařízení deaktivujete dříve, než dojde k tomuto přetažení, data se zvěčnou kapacitou v účtu úložiště a nasáhne poplatky.

2. Deaktivujte zařízení následujícím způsobem:
   
   1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V okně **Zařízení** vyberte zařízení, které chcete deaktivovat, klikněte pravým tlačítkem myši a potom klikněte na **Deaktivovat**.

        ![Deaktivovat zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. V okně **Deaktivovat** zadejte název zařízení, který potvrďte, a klepněte na tlačítko **Deaktivovat**. Proces deaktivace se spustí a trvá několik minut.

        ![Deaktivovat zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Po deaktivaci můžete zařízení zcela odstranit. Odstraněním zařízení odeberete ze seznamu zařízení připojených ke službě. Služba pak již nemůže spravovat odstraněné zařízení. Zařízení odstraníte následujícím postupem:
   
   1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V okně **Zařízení** vyberte deaktivované zařízení, které chcete odstranit, klepněte pravým tlačítkem myši a klepněte na příkaz **Odstranit**.

        ![Deaktivovat zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Do okna **Odstranit** zadejte název zařízení, který potvrďte, a klepněte na tlačítko **Odstranit**. Odstranění trvá několik minut.

        ![Deaktivovat zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Po úspěšném dokončení odstranění budete upozorněni. Seznam zařízení se také aktualizuje tak, aby odrážel odstranění.

## <a name="deactivate-and-retain-data"></a>Deaktivace a uchovávání dat

Pokud máte zájem o odstranění zařízení, ale chcete zachovat data, proveďte následující kroky:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Deaktivace zařízení a zachování dat
1. Deaktivujte zařízení. Všechny kontejnery svazků a snímky zařízení zůstávají.
   
   1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V okně **Zařízení** vyberte zařízení, které chcete deaktivovat, klikněte pravým tlačítkem myši a potom klikněte na **Deaktivovat**.

         ![Deaktivovat zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. V okně **Deaktivovat** zadejte název zařízení, který potvrďte, a klepněte na tlačítko **Deaktivovat**. Proces deaktivace se spustí a trvá několik minut.

         ![Deaktivovat zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Nyní můžete převzetí služeb při selhání kontejnery svazku a přidružené snímky. Postupy naleznete na [převzetí služeb při selhání a zotavení po havárii pro vaše zařízení StorSimple](storsimple-8000-device-failover-disaster-recovery.md).
3. Po deaktivaci a převzetí služeb při selhání můžete zařízení zcela odstranit. Odstraněním zařízení odeberete ze seznamu zařízení připojených ke službě. Služba pak již nemůže spravovat odstraněné zařízení. Chcete-li zařízení odstranit, proveďte následující kroky:
   
   1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V okně **Zařízení** vyberte deaktivované zařízení, které chcete odstranit, klepněte pravým tlačítkem myši a klepněte na příkaz **Odstranit**.

       ![Deaktivovat zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Do okna **Odstranit** zadejte název zařízení, který potvrďte, a klepněte na tlačítko **Odstranit**. Odstranění trvá několik minut.

       ![Deaktivovat zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Po úspěšném dokončení odstranění budete upozorněni. Seznam zařízení se také aktualizuje tak, aby odrážel odstranění.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Deaktivace a odstranění cloudového zařízení

Pro StorSimple Cloud Appliance deaktivace z portálu naruší a odstraní virtuální počítač a prostředky vytvořené při jeho zřízení. Po deaktivaci cloudového zařízení není možné ho obnovit do předchozího stavu.

![Deaktivace cloudového zařízení StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Deaktivace má za následek následující akce:

* StorSimple Cloud Appliance je odebrán ze služby.
* Virtuální počítač pro StorSimple Cloud Appliance se odstraní.
* Disk y operačního systému a datové disky vytvořené pro zařízení StorSimple Cloud Appliance jsou zachovány. Pokud tyto entity nepoužíváte, měli byste je odstranit ručně.
* Hostovaná služba a virtuální síť, které byly vytvořeny během zřizování jsou zachovány. Pokud tyto entity nepoužíváte, měli byste je odstranit ručně.
* Cloudové snímky vytvořené StorSimple Cloud Appliance jsou zachovány.

Po deaktivaci cloudového zařízení jej můžete odstranit ze seznamu zařízení. Vyberte deaktivované zařízení, klikněte pravým tlačítkem myši a potom klepněte na příkaz **Odstranit**. StorSimple vás upozorní, jakmile je zařízení odstraněno a seznam zařízení se aktualizuje.

## <a name="next-steps"></a>Další kroky

* Chcete-li obnovit výchozí nastavení deaktivovaného zařízení z výroby, přejděte na [obnovit výchozí nastavení zařízení z výroby](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Technickou pomoc [získáte od podpory společnosti Microsoft](storsimple-8000-contact-microsoft-support.md).
* Další informace o používání služby StorSimple Device Manager naleznete v [části Správa zařízení StorSimple pomocí služby StorSimple Device Manager](storsimple-8000-manager-service-administration.md).

