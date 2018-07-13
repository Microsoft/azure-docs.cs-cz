---
title: Spravovat záznamy řízení přístupu pro StorSimple Virtual Array | Dokumentace Microsoftu
description: Popisuje, jak spravovat záznamy řízení přístupu (záznamů Acr) k určení, které hostitele může připojit k svazku na StorSimple Virtual Array.
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
ms.openlocfilehash: 2ce65aa4efba735305208f7a6d761bc2814d1b8f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38718882"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Pomocí Správce zařízení StorSimple ke správě záznamy řízení přístupu pro StorSimple Virtual Array

## <a name="overview"></a>Přehled

Záznamy řízení přístupu (záznamů Acr) umožňují určit, které hostitele může připojit k svazku na StorSimple Virtual Array (označované také jako místní virtuální zařízení StorSimple). Záznamů Acr jsou nastaveny na určitý svazek a obsahovat kvalifikované názvy iSCSI (IQN) hostitele. Když hostitel se pokusí připojit ke svazku, zařízení zkontroluje ACR přidružené k tento svazek pro název IQN a pokud se zjistí shoda, bude připojení navázáno. **Záznamy řízení přístupu** okno v rámci **konfigurace** část služby Správce zařízení zobrazí všechny záznamy řízení přístupu s odpovídající IQN hostitele.

![Spravovat záznamy řízení přístupu](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Tento kurz vysvětluje následující běžné úkoly související s ACR:

* Získání názvu IQN
* Přidat záznam řízení přístupu
* Upravit záznam řízení přístupu
* Odstranit záznam řízení přístupu

> [!IMPORTANT]
> 
> * Při přiřazování ACR svazku, zajistíme, že svazek není přistupovat souběžně více než jeden neclusterovaného hostitele protože to může způsobit poškození svazku.
> * Při odstraňování ACR ze svazku, ujistěte se, že daný hostitel není přístup ke svazku protože odstranění by mohlo způsobit přerušení čtení i zápis.


## <a name="get-the-iqn"></a>Získání názvu IQN

Proveďte následující kroky k získání názvu IQN hostitele Windows, na kterém běží Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Přidat ACR

Použijete **záznamy řízení přístupu** okno v rámci **konfigurace** část služby Správce zařízení StorSimple pro přidání záznamů Acr. Jeden svazek obvykle lze přidružit jeden ACR.

Informace o přidružování ACR svazku, přejděte na [přidat svazek](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Při přiřazování ACR svazku, zajistíme, že svazek není přistupovat souběžně více než jeden neclusterovaného hostitele protože to může způsobit poškození svazku.


Proveďte následující kroky pro přidání ACR.

#### <a name="to-add-an-acr"></a>Chcete-li přidat službu ACR

1. Na úvodní stránce služby vyberte svoji službu, klikněte dvakrát na název služby a pak v rámci **konfigurace** klikněte na tlačítko **záznamy řízení přístupu**.
2. V **záznamy řízení přístupu** okna, klikněte na tlačítko **přidat**.
3. V **přidat ACR** okno, postupujte takto:
   
    1. Zadejte **Název** záznamu ACR.
    
    2. V části **název iniciátoru iSCSI**, zadejte název IQN hostitele Windows. K získání názvu IQN hostitele Windows serveru, postupujte takto:
   
    3. Na hostiteli s Windows spusťte iniciátor iSCSI od Microsoftu. V okně Vlastnosti iniciátoru iSCSI na **konfigurace** kartu, vyberte a zkopírujte řetězec z **název iniciátoru** pole.
    Vložte tento řetězec **IQN** pole **přidat ACR** okno.
   
    6. Klikněte na tlačítko **přidat** přidáte služby ACR.  
   
        ![Přidat záznamy řízení přístupu](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. Tabulkovém výpisu se aktualizuje tak, aby odrážely tento sčítání.

## <a name="edit-an-acr"></a>Upravovat ACR

Můžete použít **záznamy řízení přístupu** okno v rámci **konfigurace** část služby Správce zařízení na webu Azure Portal k úpravě záznamů Acr.

> [!NOTE]
> Byste neměli měnit ACR, který se právě používá. Upravovat ACR přidružené k svazku, který je aktuálně používán, byste měli nejprve provést svazku do režimu offline.


Proveďte následující kroky a upravovat ACR.

#### <a name="to-edit-an-acr"></a>Chcete-li upravit službu ACR

1. Na úvodní stránce služby vyberte svoji službu, klikněte dvakrát na název služby a pak v rámci **konfigurace** části **záznamy řízení přístupu**.
2. V **záznamy řízení přístupu** okno v tabulkovém výpisu záznamy řízení přístupu, klikněte dvakrát na ACR, kterou chcete upravit.
3. V **upravit záznamy řízení přístupu** okno, postupujte takto:
   
    1. Zadejte identifikátor IQN ACR.
   
    2. Klikněte na tlačítko **Uložit** v horní části okna uložte změny služby ACR. Zobrazí následující potvrzující zpráva:
   
        ![Upravit záznamy řízení přístupu](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Odstranit záznam řízení přístupu

Můžete použít **konfigurace** stránky na webu Azure Portal k odstranění záznamů Acr.

> [!NOTE]
> 
> * Nedoporučuje se mazat ACR, který se právě používá. Odstraníte ACR přidružené k svazku, který se právě používá, je měli nejprve provést svazku do režimu offline.
> * Při odstraňování ACR ze svazku, ujistěte se, že daný hostitel není přístup ke svazku protože odstranění by mohlo způsobit přerušení čtení i zápis.


Proveďte následující kroky, chcete-li odstranit záznam řízení přístupu.

#### <a name="to-delete-an-access-control-record"></a>Chcete-li odstranit záznam řízení přístupu

1. Na úvodní stránce služby vyberte svoji službu, klikněte dvakrát na název služby a pak v rámci **konfigurace** části **záznamy řízení přístupu**.

2. V **záznamy řízení přístupu** okno v tabulkovém výpisu záznamy řízení přístupu, klikněte dvakrát na ACR, kterou chcete odstranit.

3. V okně záznamy řízení přístupu úprav klikněte na tlačítko **odstranit**.
   
    ![Odstranění záznamů ACR](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **odstranit** pro pokračování v odstranění. Tabulkovém výpisu se aktualizuje tak, aby odrážely odstranění.
   
   ![Zpráva upozornění](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Další postup

* Další informace o [přidání svazků a konfigurace záznamů Acr](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

