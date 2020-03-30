---
title: Změna hesla StorSimple | Dokumenty společnosti Microsoft
description: Popisuje, jak pomocí služby StorSimple Device Manager změnit správce snímků StorSimple a hesla správce zařízení.
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
ms.openlocfilehash: ab874bbdcd47a4bfa9abfba721afa46d0f23a338
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268025"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Změna hesla StorSimple Pomocí správce zařízení StorSimple

## <a name="overview"></a>Přehled
Možnost **Nastavení zařízení** portálu Azure obsahuje všechny parametry zařízení, které můžete překonfigurovat na zařízení StorSimple, které je spravované službou StorSimple Device Manager. Tento kurz vysvětluje, jak můžete použít možnost **Zabezpečení** v části **Nastavení zařízení** ke změně správce zařízení nebo hesla Správce snímků StorSimple.

## <a name="change-the-device-administrator-password"></a>Změna hesla správce zařízení
Při použití rozhraní prostředí Windows PowerShell pro přístup k zařízení StorSimple, je nutné zadat heslo správce zařízení. Při registraci prvního zařízení StorSimple se službou je výchozím heslem pro toto rozhraní *Password1*. Pro zabezpečení vašich dat je nutné toto heslo na konci registračního procesu změnit. Proces registrace nelze ukončit bez změny tohoto hesla. Další informace naleznete [v tématu Krok 3: Konfigurace a registrace zařízení prostřednictvím prostředí Windows PowerShell pro StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Heslo, které bylo poprvé nastaveno prostřednictvím rozhraní prostředí Windows PowerShell během registrace, lze později změnit prostřednictvím portálu Azure. Chcete-li změnit heslo správce zařízení, proveďte následující kroky.

#### <a name="to-change-the-device-administrator-password"></a>Změna hesla správce zařízení
1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**.

2. Z tabulkového výpisu zařízení vyberte a klikněte na zařízení, jehož heslo chcete změnit.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. V okně **Nastavení** přejděte na **nastavení zařízení > zabezpečení**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. V okně **Nastavení zabezpečení** klikněte na **Heslo** a změňte heslo správce zařízení.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. V okně **Heslo** zadejte heslo správce, které obsahuje 8 až 15 znaků. Heslo musí být kombinací 3 nebo více velkých, malých, číselných a speciálních znaků.

6. Potvrďte heslo.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Klikněte na **Uložit** a po zobrazení výzvy k potvrzení klepněte na tlačítko **Ano**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Heslo správce zařízení by nyní mělo být aktualizováno. Toto upravené heslo můžete použít pro přístup k rozhraní prostředí Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Nastavení hesla Správce snímků StorSimple
Software Snapshot Manager zařízení StorSimple se nachází na hostiteli systému Windows a umožňuje správcům spravovat zálohy zařízení StorSimple ve formě místních a cloudových snímků.

Při konfiguraci zařízení ve Správci snímků StorSimple budete vyzváni k zadání IP adresy a hesla zařízení k ověření vašeho úložného zařízení.

Heslo pro Správce snímků StorSimple můžete nastavit nebo změnit prostřednictvím portálu Azure. Proveďte následující kroky k nastavení nebo změně hesla Správce snímků StorSimple.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Nastavení hesla Správce snímků StorSimple
1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**.

2. Z tabulkového výpisu zařízení vyberte a klikněte na zařízení, jehož heslo Správce snímků StorSimple chcete nastavit nebo změnit.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. V okně **Nastavení** přejděte na **nastavení zařízení > zabezpečení**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. V okně **Nastavení zabezpečení** klepněte na **tlačítko Heslo** a nastavte nebo změňte heslo správce snímků StorSimple.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. V okně **Heslo** zadejte heslo, které má 14 nebo 15 znaků. Ujistěte se, že heslo obsahuje kombinaci 3 nebo více velkých, malých, číselných a speciálních znaků.

6. Potvrďte heslo.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Klikněte na **Uložit** a po zobrazení výzvy k potvrzení klepněte na tlačítko **Ano**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Heslo správce snímků StorSimple by nyní mělo být aktualizováno.

## <a name="next-steps"></a>Další kroky
* Další informace o [zabezpečení StorSimple](storsimple-8000-security.md).
* Přečtěte si další informace [o úpravách konfigurace zařízení](storsimple-8000-modify-device-config.md).
* Další informace o [správě zařízení StorSimple Device Manager pomocí služby StorSimple](storsimple-8000-manager-service-administration.md).

