---
title: Změna hesla služby StorSimple | Microsoft Docs
description: Popisuje, jak změnit správce hesla služby StorSimple Snapshot Manager a zařízení pomocí služby StorSimple Manager zařízení.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 7762f8499c67672f0a2ffed99e98baea4c940fa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874805"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Chcete-li změnit hesla služby StorSimple používat službu Správce zařízení StorSimple

## <a name="overview"></a>Přehled
Portál Azure **nastavení zařízení** možnost obsahuje všechny parametry zařízení, které můžete změnit konfiguraci na zařízení StorSimple, který je spravovaný nástrojem service Manager zařízení StorSimple. Tento kurz popisuje, jak můžete použít **zabezpečení** možnost pod **nastavení zařízení** změnit správce zařízení nebo heslo Snapshot Manager zařízení StorSimple.

## <a name="change-the-device-administrator-password"></a>Změna hesla správce zařízení
Pokud použijete rozhraní Windows PowerShell k přístupu k zařízení StorSimple, musíte k zadání hesla správce zařízení. Pokud služba není zaregistrována prvního zařízení StorSimple, výchozí heslo pro toto rozhraní je *Heslo1*. Pro zabezpečení vašich dat musíte změnit toto heslo na konci procesu registrace. Nelze ukončit proces registrace beze změny toto heslo. Další informace najdete v tématu [krok 3: Konfigurace a registrace zařízení pomocí Windows Powershellu pro StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Heslo, které se nejdřív nastavit pomocí rozhraní Windows PowerShell během registrace lze později změnit prostřednictvím portálu Azure. Proveďte následující kroky, chcete-li změnit heslo správce zařízení.

#### <a name="to-change-the-device-administrator-password"></a>Chcete-li změnit heslo správce zařízení
1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**.

2. Tabulkový seznam zařízení, vyberte a klikněte na zařízení, jehož heslo chcete změnit.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. V **nastavení** okno, přejděte na **nastavení zařízení > zabezpečení**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. V **nastavení zabezpečení** okně klikněte na tlačítko **heslo** Změna hesla správce zařízení.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. V **heslo** okno, zadejte heslo správce, který obsahuje 8 až 15 znaků. Heslo musí být kombinací 3 nebo více velká písmena, malá písmena, číselné a speciální znaky.

6. Potvrzení hesla.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Klikněte na tlačítko **Uložit** a po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Nyní je třeba aktualizovat heslo správce zařízení. Můžete toto upravené heslo pro přístup k rozhraní Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Nastavení hesla Snapshot Manageru zařízení StorSimple
Software Snapshot Manager zařízení StorSimple se nachází na hostiteli systému Windows a umožňuje správcům spravovat zálohy zařízení StorSimple ve formě místních a cloudových snímků.

Při konfiguraci zařízení ve Snapshot Manageru zařízení StorSimple, budete vyzváni k zadání zařízení IP adresu a heslo k ověření zařízení úložiště.

Můžete nastavit nebo změnit heslo pro StorSimple Snapshot Manager prostřednictvím portálu Azure. Proveďte následující kroky pro nastavení nebo změna hesla Snapshot Manageru zařízení StorSimple.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Chcete-li nastavit heslo Snapshot Manager zařízení StorSimple
1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**.

2. Tabulkový seznam zařízení, vyberte a klikněte na zařízení, jehož heslo Snapshot Manager zařízení StorSimple chcete nastavit nebo změnit.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. V **nastavení** okno, přejděte na **nastavení zařízení > zabezpečení**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. V **nastavení zabezpečení** okně klikněte na tlačítko **heslo** nastavit nebo změnit heslo Snapshot Manager zařízení StorSimple.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. V **heslo** okno, zadejte heslo, které je tvořeno 14 až 15 znaků. Ujistěte se, zda heslo obsahuje kombinaci 3 nebo více velká písmena, malá písmena, číselné a speciální znaky.

6. Potvrzení hesla.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Klikněte na tlačítko **Uložit** a po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Nyní je třeba aktualizovat heslo Snapshot Manager zařízení StorSimple.

## <a name="next-steps"></a>Další kroky
* Další informace o [zabezpečení zařízení StorSimple](storsimple-8000-security.md).
* Další informace o [úprava konfiguraci zařízení](storsimple-8000-modify-device-config.md).
* Další informace o [pomocí služby StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

