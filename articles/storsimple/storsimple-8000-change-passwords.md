---
title: Změna hesel StorSimple | Microsoft Docs
description: Popisuje, jak používat službu StorSimple Device Manager ke změně hesla StorSimple Snapshot Manager a Správce zařízení.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 038084ba9ae43e14bc2eb42bf258912be27d062c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86527859"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Použijte službu StorSimple Device Manager ke změně hesla StorSimple.

## <a name="overview"></a>Přehled
Možnost **nastavení zařízení** Azure Portal obsahuje všechny parametry zařízení, které můžete znovu nakonfigurovat na zařízení StorSimple, které spravuje služba StorSimple Device Manager. V tomto kurzu se dozvíte, jak můžete pomocí možnosti **zabezpečení** v části **nastavení zařízení** změnit správce zařízení nebo StorSimple heslo Snapshot Manager.

## <a name="change-the-device-administrator-password"></a>Změna hesla správce zařízení
Pokud pro přístup k zařízení StorSimple použijete rozhraní Windows PowerShell, je nutné zadat heslo správce zařízení. Když je první zařízení StorSimple zaregistrované ve službě, výchozí heslo pro toto rozhraní je *Heslo1*. Z důvodu zabezpečení vašich dat je nutné změnit toto heslo na konci procesu registrace. Nemůžete ukončit proces registrace beze změny tohoto hesla. Další informace najdete v části [Krok 3: konfigurace a registrace zařízení prostřednictvím Windows PowerShell pro StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Heslo, které se poprvé nastavilo prostřednictvím rozhraní Windows PowerShell během registrace, se dá později změnit přes Azure Portal. Chcete-li změnit heslo správce zařízení, proveďte následující kroky.

#### <a name="to-change-the-device-administrator-password"></a>Změna hesla správce zařízení
1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**.

2. V tabulkovém výpisu zařízení vyberte a klikněte na zařízení, jehož heslo máte v úmyslu změnit.

    ![Snímek obrazovky zobrazující službu StorSimple Device Manager. V části Správa vyberte zařízení. V seznamu zařízení je vybráno jedno zařízení.](./media/storsimple-8000-change-passwords/changepwd1.png)

3. V okně **Nastavení** přejdete na **nastavení zařízení > zabezpečení**.

    ![Snímek obrazovky zobrazující okno nastavení služby Device Manager V části nastavení zařízení je vybrána možnost zabezpečení.](./media/storsimple-8000-change-passwords/changepwd2.png)

4. V okně **nastavení zabezpečení** klikněte na **heslo** a změňte heslo správce zařízení.

    ![Snímek obrazovky zobrazující okno nastavení zabezpečení Tlačítko heslo je zvýrazněné.](./media/storsimple-8000-change-passwords/changepwd3.png)

5. V okně **heslo** zadejte heslo správce, které obsahuje 8 až 15 znaků. Heslo musí obsahovat 3 nebo více velkých a malých písmen, číslic a speciálních znaků.

6. Potvrďte heslo.

    ![Snímek obrazovky zobrazující okno s heslem V části heslo správce zařízení se vyplní pole nové heslo a potvrzení hesla.](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Klikněte na **Uložit** a po zobrazení výzvy k potvrzení klikněte na **Ano**.

    ![Snímek obrazovky zobrazující okno s heslem Tlačítko Uložit je zvýrazněno.](./media/storsimple-8000-change-passwords/changepwd6.png)

Heslo správce zařízení by se teď mělo aktualizovat. Toto upravené heslo můžete použít pro přístup k rozhraní Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Nastavení StorSimple Snapshot Manager hesla
Software Snapshot Manager zařízení StorSimple se nachází na hostiteli systému Windows a umožňuje správcům spravovat zálohy zařízení StorSimple ve formě místních a cloudových snímků.

Při konfiguraci zařízení v StorSimple Snapshot Manager budete vyzváni k zadání IP adresy a hesla zařízení k ověření úložného zařízení.

Můžete nastavit nebo změnit heslo pro Snapshot Manager StorSimple prostřednictvím Azure Portal. Chcete-li nastavit nebo změnit heslo StorSimple Snapshot Manager, proveďte následující kroky.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Nastavení Snapshot Manager hesla StorSimple
1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**.

2. V tabulkovém výpisu zařízení vyberte a klikněte na zařízení, jehož StorSimple Snapshot Manager heslo máte v úmyslu nastavit nebo změnit.

     ![Snímek obrazovky zobrazující službu StorSimple Device Manager. V části Správa vyberte zařízení. V seznamu zařízení je vybráno jedno zařízení.](./media/storsimple-8000-change-passwords/changepwd1.png)

3. V okně **Nastavení** přejdete na **nastavení zařízení > zabezpečení**.

     ![Snímek obrazovky zobrazující okno nastavení služby Device Manager V části nastavení zařízení je vybrána možnost zabezpečení.](./media/storsimple-8000-change-passwords/changepwd2.png)

4. V okně **nastavení zabezpečení** klikněte na **heslo** , abyste nastavili nebo změnili StorSimple Snapshot Manager heslo.

     ![Snímek obrazovky zobrazující okno nastavení zabezpečení Tlačítko heslo je zvýrazněné.](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. V okně **heslo** zadejte heslo, které je 14 nebo 15 znaků. Ujistěte se, že heslo obsahuje kombinaci 3 nebo více velkých písmen, malých písmen, číslic a speciálních znaků.

6. Potvrďte heslo.

     ![Snímek obrazovky zobrazující okno s heslem V části Snapshot Manager heslo jsou vyplněna pole nové heslo a potvrzení hesla.](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Klikněte na **Uložit** a po zobrazení výzvy k potvrzení klikněte na **Ano**.

     ![Snímek obrazovky zobrazující okno s heslem Tlačítko Uložit je zvýrazněno.](./media/storsimple-8000-change-passwords/changepwd6.png)

Heslo Snapshot Manager StorSimple by teď mělo být aktualizované.

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [zabezpečení StorSimple](storsimple-8000-security.md).
* Přečtěte si další informace o [změně konfigurace zařízení](storsimple-8000-modify-device-config.md).
* Přečtěte si další informace o [používání služby StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

