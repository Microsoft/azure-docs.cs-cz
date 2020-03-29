---
title: Správa záznamů řízení přístupu pro virtuální pole StorSimple | Dokumenty společnosti Microsoft
description: Popisuje, jak spravovat záznamy řízení přístupu (AVR) k určení, kteří hostitelé se mohou připojit ke svazku na virtuálním poli StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1dfc1b0e0576402624bfe62de0e206d9bd7cd1b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "64724426"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Správa záznamů řízení přístupu pro virtuální pole StorSimple pomocí Správce zařízení StorSimple

## <a name="overview"></a>Přehled

Záznamy řízení přístupu (AVR) umožňují určit, kteří hostitelé se mohou připojit ke svazku na virtuálním poli StorSimple (označované také jako místní virtuální zařízení StorSimple). ACC jsou nastaveny na určitý svazek a obsahují kvalifikované názvy iSCSI (IQN) hostitelů. Když se hostitel pokusí připojit ke svazku, zařízení zkontroluje ACR přidružené k tomuto svazku pro název IQN a pokud je shoda, je připojení navázáno. Okno **Řízení přístupu zaznamenává** v části **Konfigurace** služby Správce zařízení zobrazuje všechny záznamy řízení přístupu s odpovídajícími sítěmi IQN hostitelů.

![Správa záznamů řízení přístupu](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Tento kurz vysvětluje následující běžné úkoly související s ACR:

* Získejte IQN
* Přidání záznamu řízení přístupu
* Úprava záznamu řízení přístupu
* Odstranění záznamu řízení přístupu

> [!IMPORTANT]
> 
> * Při přiřazování ACR ke svazku dbát na to, aby ke svazku současně nepřistupovalo více než jeden neclusterovaný hostitel, protože by to mohlo svazek poškodit.
> * Při odstraňování ACR ze svazku se ujistěte, že odpovídající hostitel nemá přístup ke svazku, protože odstranění může vést k přerušení čtení a zápisu.


## <a name="get-the-iqn"></a>Získejte IQN

Chcete-li získat iqn hostitele systému Windows se systémem Windows Server 2012, postupujte podle následujících kroků.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Přidání ACR

K přidání záznamů o řízení přístupu v části **Konfigurace** služby Správce zařízení se používá okno **záznamů řízení přístupu.** Obvykle přidružíte jeden ACR k jednomu svazku.

Informace o přisuzování acr se svazkem získáte k [přidání svazku](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Při přiřazování ACR ke svazku dbát na to, aby ke svazku současně nepřistupovalo více než jeden neclusterovaný hostitel, protože by to mohlo svazek poškodit.


Chcete-li přidat acr, proveďte následující kroky.

#### <a name="to-add-an-acr"></a>Přidání ACR

1. Na vstupní stránce služby vyberte službu, poklepejte na název služby a v části **Konfigurace** klikněte na **záznamy řízení přístupu**.
2. V okně **Záznamy ovládacího prvku access** klepněte na tlačítko **Přidat**.
3. V okně **Přidat acr** postupujte takto:
   
    1. Zadejte **Název** záznamu ACR.
    
    2. V **části Název iniciátoru iSCSI**zadejte název IQN hostitele systému Windows. Chcete-li získat iqn hostitele systému Windows Server, postupujte takto:
   
    3. Na hostiteli s Windows spusťte iniciátor iSCSI od Microsoftu. V okně iSCSI Initiator Properties (Vlastnosti iniciátoru iSCSI) na kartě **Konfigurace** vyberte a zkopírujte řetězec z pole **Název iniciátoru**.
    Vložte tento řetězec do pole **IQN** v okně **Přidat ACR.**
   
    6. Kliknutím na **Přidat** přidejte acr.  
   
        ![Přidání záznamů řízení přístupu](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. Tabulkový výpis je aktualizován tak, aby odrážel tento přírůstek.

## <a name="edit-an-acr"></a>Úprava acr

Okno **Záznamy řízení přístupu** v části **Konfigurace** služby Správce zařízení na webu Azure Portal se používá k úpravám akreditivů.

> [!NOTE]
> ACR, který je právě používán, byste neměli měnit. Chcete-li upravit acr přidružený ke svazku, který je právě používán, měli byste nejprve převést svazek do offline.


Chcete-li upravit acr, proveďte následující kroky.

#### <a name="to-edit-an-acr"></a>Úprava acr

1. Na vstupní stránce služby vyberte službu, poklepejte na název služby a potom v části **Konfigurace,** **záznamy řízení přístupu**.
2. V okně **Záznamy řízení přístupu** poklepejte na tabulkový výpis záznamů řízení přístupu na acr, který chcete upravit.
3. V okně **Upravit záznamy řízení přístupu** postupujte takto:
   
    1. Dodávat IQN pro ACR.
   
    2. Klepnutím na tlačítko **Uložit** v horní části okna uložte upravený acr. Zobrazí se následující potvrzovací zpráva:
   
        ![Úprava záznamů řízení přístupu](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Odstranění záznamu řízení přístupu

Pomocí stránky **Konfigurace** na webu Azure Portal můžete odstranit akreditace.

> [!NOTE]
> 
> * ACR, který je právě používán, byste neměli odstraňovat. Chcete-li odstranit acr přidružený ke svazku, který je právě používán, měli byste nejprve převést svazek do offline.
> * Při odstraňování ACR ze svazku se ujistěte, že odpovídající hostitel nemá přístup ke svazku, protože odstranění může vést k přerušení čtení a zápisu.


Chcete-li odstranit záznam řízení přístupu, proveďte následující kroky.

#### <a name="to-delete-an-access-control-record"></a>Odstranění záznamu řízení přístupu

1. Na vstupní stránce služby vyberte službu, poklepejte na název služby a potom v části **Konfigurace,** **záznamy řízení přístupu**.

2. V okně **Záznamy řízení přístupu** poklepejte na tabulkový výpis záznamů řízení přístupu na acr, který chcete odstranit.

3. V okně Upravit záznamy řízení přístupu klepněte na tlačítko **Odstranit**.
   
    ![Odstranit acrs](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Po zobrazení výzvy k potvrzení pokračujte klepnutím na **tlačítko Odstranit.** Tabulkový výpis je aktualizován tak, aby odrážel odstranění.
   
   ![Varovná zpráva](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [přidávání svazků a konfiguraci akreditivů](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

