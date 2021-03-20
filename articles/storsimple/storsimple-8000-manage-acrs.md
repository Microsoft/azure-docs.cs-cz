---
title: Správa záznamů řízení přístupu v StorSimple | Microsoft Docs
description: Popisuje, jak pomocí záznamů řízení přístupu (záznamů ACR) určit, kteří hostitelé se mohou připojit ke svazku na zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 15c35fb314af27b1ced129a12f752d0a2794e0f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91949900"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Správa záznamů řízení přístupu pomocí služby StorSimple Manager

## <a name="overview"></a>Přehled
Záznamy řízení přístupu (záznamů ACR) umožňují určit hostitele, kteří se mohou připojit ke svazku na zařízení StorSimple. Záznamů ACR jsou nastavené na konkrétní svazek a obsahují kvalifikované názvy iSCSI (IQNs) hostitelů. Když se hostitel pokusí připojit ke svazku, zařízení zkontroluje ACR přidružené k tomuto svazku pro název IQN a pokud existuje shoda, naváže se připojení. V okně StorSimple Správce zařízení služby se v části **Konfigurace** řízení přístupu zobrazí všechny záznamy řízení přístupu s odpovídajícími IQNs hostitelů.

V tomto kurzu se dozvíte o následujících běžných úkolech souvisejících s ACR:

* Přidat záznam řízení přístupu
* Upravit záznam řízení přístupu
* Odstranění záznamu řízení přístupu

> [!IMPORTANT]
> * Při přiřazování ACR ke svazku se ujistěte, že se ke svazku nepoužívá souběžně více než jeden neclusterovaný hostitel, protože by to mohlo poškodit svazek.
> * Při odstraňování ACR ze svazku se ujistěte, že příslušný hostitel nepřistupuje ke svazku, protože odstranění může způsobit přerušení čtení a zápisu.

## <a name="get-the-iqn"></a>Získání názvu IQN

Pomocí následujících kroků získejte identifikátor IQN hostitele systému Windows, na kterém běží systém Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Přidat záznam řízení přístupu
Pomocí oddílu **Konfigurace** v okně StorSimple Správce zařízení služby můžete přidat záznamů ACR. Obvykle přiřadíte jeden ACR s jedním svazkem.

Chcete-li přidat ACR, proveďte následující kroky.

#### <a name="to-add-an-acr"></a>Přidání ACR

1. Přejděte na službu StorSimple Správce zařízení, dvakrát klikněte na název služby a potom v části **Konfigurace** klikněte na možnost **záznamy řízení přístupu**.
2. V okně **záznamy řízení přístupu** klikněte na **+ Přidat ACR**.

    ![Klikněte na Přidat ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. V okně **Přidat ACR** proveďte následující kroky:

    1. Zadejte název svého ACR.
    
    2. Zadejte název IQN hostitele Windows serveru v části **název iniciátoru iSCSI (IQN)**.

    3. Kliknutím na **Přidat** vytvořte ACR.

        ![Klikněte na Přidat ACR 2.](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Nově přidané ACR se zobrazí v tabulkovém výpisu záznamů ACR.

    ![Klikněte na Přidat ACR 3.](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Upravit záznam řízení přístupu
V okně StorSimple Správce zařízení Service můžete použít **konfigurační** oddíl k úpravám záznamů ACR.

> [!NOTE]
> Doporučujeme, abyste změnili pouze ty záznamů ACR, které se momentálně nepoužívají. Pokud chcete upravit ACR přidružené ke svazku, který se právě používá, musíte nejdřív svazek převést do režimu offline.

Chcete-li upravit ACR, proveďte následující kroky.

#### <a name="to-edit-an-access-control-record"></a>Postup úpravy záznamu řízení přístupu
1.  Přejděte na službu StorSimple Správce zařízení, dvakrát klikněte na název služby a potom v části **Konfigurace** klikněte na možnost **záznamy řízení přístupu**.

    ![Přejít na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/createacr1.png)

2. V tabulkovém výpisu záznamů řízení přístupu klikněte a vyberte ACR, který chcete upravit.

    ![Upravit záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/editacr1.png)

3. V okně **Upravit záznam řízení přístupu** zadejte jiný identifikátor IQN odpovídající jinému hostiteli.

    ![Upravit záznamy řízení přístupu 2](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Klikněte na **Uložit**. Po zobrazení výzvy k potvrzení klikněte na **Ano**. 

    ![Upravit záznamy řízení přístupu 3](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Po aktualizaci ACR se zobrazí oznámení. V tabulkovém výpisu se také aktualizuje, aby odrážela změnu.

   
## <a name="delete-an-access-control-record"></a>Odstranění záznamu řízení přístupu
Pomocí oddílu **Konfigurace** v okně StorSimple Správce zařízení služby odstraníte záznamů ACR.

> [!NOTE]
> Odstranit můžete jenom ty záznamů ACR, které se v tuto chvíli nepoužívají. Pokud chcete odstranit ACR přidružené ke svazku, který se právě používá, musíte nejdřív svazek převést do režimu offline.

Chcete-li odstranit záznam řízení přístupu, proveďte následující kroky.

#### <a name="to-delete-an-access-control-record"></a>Postup odstranění záznamu řízení přístupu
1.  Přejděte na službu StorSimple Správce zařízení, dvakrát klikněte na název služby a potom v části **Konfigurace** klikněte na možnost **záznamy řízení přístupu**.

    ![Přejít na záznamy řízení přístupu 1b](./media/storsimple-8000-manage-acrs/createacr1.png)

2. V tabulkovém výpisu záznamů řízení přístupu klikněte a vyberte ACR, který chcete odstranit.

    ![Přejít na záznamy řízení přístupu 2](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Kliknutím pravým tlačítkem myši zavolejte kontextovou nabídku a vyberte možnost **Odstranit**.

    ![Přejít na záznamy řízení přístupu 3](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Po zobrazení výzvy k potvrzení zkontrolujte informace a klikněte na **Odstranit**.

    ![Přejít na záznamy řízení přístupu 4](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Po dokončení odstranění budete upozorněni. Tabulkový výpis je aktualizován, aby odrážel odstranění.

    ![Přejít na záznamy řízení přístupu 5](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [správě svazků StorSimple](storsimple-8000-manage-volumes-u2.md).
* Přečtěte si další informace o [používání služby StorSimple Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

