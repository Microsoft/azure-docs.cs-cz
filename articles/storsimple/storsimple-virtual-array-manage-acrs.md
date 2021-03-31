---
title: Správa záznamů řízení přístupu pro virtuální pole StorSimple | Microsoft Docs
description: Popisuje, jak spravovat záznamy řízení přístupu (záznamů ACR) a určit, kteří hostitelé se mohou připojit ke svazku ve virtuálním poli StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bad0d7adfd77dff53b1582e63a91f2cd87a9233d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85507615"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Použití Správce zařízení StorSimple ke správě záznamů řízení přístupu pro virtuální pole StorSimple

## <a name="overview"></a>Přehled

Záznamy řízení přístupu (záznamů ACR) umožňují určit hostitele, kteří se mohou připojit ke svazku ve virtuálním poli StorSimple (označované také jako místní virtuální zařízení StorSimple). Záznamů ACR jsou nastavené na konkrétní svazek a obsahují kvalifikované názvy iSCSI (IQNs) hostitelů. Když se hostitel pokusí připojit ke svazku, zařízení zkontroluje ACR přidružené k tomuto svazku pro název IQN a pokud existuje shoda, naváže se připojení. Okno **záznamy řízení přístupu** v části **Konfigurace** služby Správce zařízení zobrazuje všechny záznamy řízení přístupu s odpovídajícími IQNs hostitelů.

![Správa záznamů řízení přístupu](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

V tomto kurzu se dozvíte o následujících běžných úkolech souvisejících s ACR:

* Získání názvu IQN
* Přidat záznam řízení přístupu
* Upravit záznam řízení přístupu
* Odstranění záznamu řízení přístupu

> [!IMPORTANT]
> 
> * Při přiřazování ACR ke svazku se ujistěte, že se ke svazku nepoužívá souběžně více než jeden neclusterovaný hostitel, protože by to mohlo poškodit svazek.
> * Při odstraňování ACR ze svazku se ujistěte, že příslušný hostitel nepřistupuje ke svazku, protože odstranění může způsobit přerušení čtení a zápisu.


## <a name="get-the-iqn"></a>Získání názvu IQN

Pomocí následujících kroků získejte identifikátor IQN hostitele systému Windows, na kterém běží systém Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Přidat ACR

V rámci **konfiguračního** oddílu služby StorSimple Správce zařízení můžete použít okno **záznamů řízení přístupu** a přidat záznamů ACR. Obvykle přiřadíte jeden ACR s jedním svazkem.

Informace o přidružení ACR ke svazku získáte tak, že přejdete na [Přidat svazek](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Při přiřazování ACR ke svazku se ujistěte, že se ke svazku nepoužívá souběžně více než jeden neclusterovaný hostitel, protože by to mohlo poškodit svazek.


Chcete-li přidat ACR, proveďte následující kroky.

#### <a name="to-add-an-acr"></a>Přidání ACR

1. Na stránce cílová služba vyberte svou službu, dvakrát klikněte na název služby a potom v části **Konfigurace** klikněte na možnost **záznamy řízení přístupu**.
2. V okně **záznamy řízení přístupu** klikněte na **Přidat**.
3. V okně **Přidat ACR** postupujte takto:
   
    1. Zadejte **Název** záznamu ACR.
    
    2. V části **název iniciátoru iSCSI** zadejte název IQN hostitele Windows. K získání názvu IQN hostitele Windows serveru udělejte toto:
   
    3. Na hostiteli s Windows spusťte iniciátor iSCSI od Microsoftu. V okně iSCSI Initiator Properties (Vlastnosti iniciátoru iSCSI) na kartě **Konfigurace** vyberte a zkopírujte řetězec z pole **Název iniciátoru**.
    Vložte tento řetězec do pole **IQN** v okně **Přidat ACR** .
   
    6. Kliknutím na **Přidat** přidejte ACR.  
   
        ![Přidat záznamy řízení přístupu](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. Tabulkový výpis je aktualizován, aby odrážel toto sčítání.

## <a name="edit-an-acr"></a>Upravit ACR

V okně Správce zařízení služby v Azure Portal k úpravám záznamů ACR použijete okno pro **záznamy řízení přístupu** v rámci **konfiguračního** oddílu.

> [!NOTE]
> Neměňte ACR, který se právě používá. Pokud chcete upravit ACR přidružené ke svazku, který se právě používá, měli byste nejdřív svazek převést do režimu offline.


Chcete-li upravit ACR, proveďte následující kroky.

#### <a name="to-edit-an-acr"></a>Úprava ACR

1. Na stránce cílová služba vyberte svou službu, dvakrát klikněte na název služby a potom v části **Konfigurace** zkontrolujte **záznamy řízení přístupu**.
2. V okně **záznamy řízení přístupu** v tabulkovém výpisu záznamů řízení přístupu POKLIKEJTE na ACR, který chcete upravit.
3. V okně **Upravit záznamy řízení přístupu** udělejte toto:
   
    1. Zadejte identifikátor IQN pro ACR.
   
    2. Kliknutím na **Uložit** v horní části okna uložte upravenou ACR. Zobrazí se následující potvrzovací zpráva:
   
        ![Upravit záznamy řízení přístupu](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Odstranění záznamu řízení přístupu

Pomocí stránky **Konfigurace** v Azure Portal odstranit záznamů ACR.

> [!NOTE]
> 
> * Neodstraňujte ACR, který se právě používá. Pokud chcete odstranit ACR přidružené ke svazku, který se právě používá, měli byste nejdřív svazek převést do režimu offline.
> * Při odstraňování ACR ze svazku se ujistěte, že příslušný hostitel nepřistupuje ke svazku, protože odstranění může způsobit přerušení čtení a zápisu.


Chcete-li odstranit záznam řízení přístupu, proveďte následující kroky.

#### <a name="to-delete-an-access-control-record"></a>Postup odstranění záznamu řízení přístupu

1. Na stránce cílová služba vyberte svou službu, dvakrát klikněte na název služby a potom v části **Konfigurace** zkontrolujte **záznamy řízení přístupu**.

2. V okně **záznamy řízení přístupu** v tabulkovém výpisu záznamů řízení přístupu POKLIKEJTE na ACR, který chcete odstranit.

3. V okně Upravit záznamy řízení přístupu klikněte na **Odstranit**.
   
    ![Odstranit záznamů ACR](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Po zobrazení výzvy k potvrzení klikněte na **Odstranit** , aby bylo možné pokračovat v odstraňování. Tabulkový výpis je aktualizován, aby odrážel odstranění.
   
   ![Zpráva upozornění](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [přidávání svazků a konfiguraci záznamů ACR](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

