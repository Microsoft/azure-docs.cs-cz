---
title: Změna hesla služby StorSimple | Dokumentace Microsoftu
description: Popisuje způsob použití služby Správce zařízení StorSimple ke změně hesla správce Snapshot Manageru zařízení StorSimple a zařízení.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60637961"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Použití služby Správce zařízení StorSimple, chcete-li změnit hesla služby StorSimple

## <a name="overview"></a>Přehled
Na webu Azure portal **nastavení zařízení** možnost obsahuje všechny parametry zařízení, které můžete změnit konfiguraci na zařízení StorSimple, která jsou spravována služby Správce zařízení StorSimple. Tento kurz vysvětluje, jak můžete použít **zabezpečení** v části **nastavení zařízení** změnit správce zařízení nebo hesla Snapshot Manageru zařízení StorSimple.

## <a name="change-the-device-administrator-password"></a>Změna hesla správce zařízení
Pokud použijete rozhraní Windows PowerShell pro přístup k zařízení StorSimple, jsou nutné k zadání hesla správce zařízení. Při registraci prvního zařízení StorSimple se službou, výchozí heslo pro toto rozhraní je *Heslo1*. Pro zabezpečení vašich dat se musí změnit heslo na konci procesu registrace. Při registraci, nelze ukončit bez změny toto heslo. Další informace najdete v tématu [krok 3: Konfigurace a registrace zařízení pomocí Windows Powershellu pro StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Heslo, které se nejdřív nastavit prostřednictvím rozhraní Windows PowerShell při registraci můžete později změnit prostřednictvím webu Azure portal. Proveďte následující kroky ke změně hesla správce zařízení.

#### <a name="to-change-the-device-administrator-password"></a>Chcete-li změnit heslo správce zařízení
1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**.

2. V tabulkovém výpisu zařízení vyberte a klikněte na zařízení, jehož heslo chcete změnit.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. V **nastavení** okno, přejděte na **zařízení Nastavení > zabezpečení**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. V **nastavení zabezpečení** okna, klikněte na tlačítko **heslo** ke změně hesla správce zařízení.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. V **heslo** okno, zadejte heslo správce, který obsahuje z 8 až 15 znaků. Heslo musí být kombinací 3 nebo více velká písmena, malá písmena, číselné a speciální znaky.

6. Potvrďte heslo.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Klikněte na tlačítko **Uložit** a po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Nyní je třeba aktualizovat heslo správce zařízení. Můžete použít toto změněné heslo pro přístup k rozhraní Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Nastavení hesla Snapshot Manageru zařízení StorSimple
Software Snapshot Manager zařízení StorSimple se nachází na hostiteli systému Windows a umožňuje správcům spravovat zálohy zařízení StorSimple ve formě místních a cloudových snímků.

Při konfiguraci zařízení StorSimple Snapshot Manageru, vyzve k zadání zařízení IP adresy a hesla pro ověření zařízení úložiště.

Můžete nastavit nebo změnit heslo pro StorSimple Snapshot Manageru prostřednictvím webu Azure portal. Proveďte následující kroky k nastavení nebo změna hesla Snapshot Manageru zařízení StorSimple.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Chcete-li nastavit heslo StorSimple Snapshot Manageru
1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**.

2. V tabulkovém výpisu zařízení vyberte a klikněte na zařízení, jehož heslo Snapshot Manageru zařízení StorSimple, máte v úmyslu nastavit nebo změnit.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. V **nastavení** okno, přejděte na **zařízení Nastavení > zabezpečení**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. V **nastavení zabezpečení** okna, klikněte na tlačítko **heslo** k nastavení nebo změna hesla Snapshot Manageru zařízení StorSimple.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. V **heslo** okno, zadejte heslo, které je tvořeno 14 až 15 znaků. Ujistěte se, že heslo obsahuje kombinaci 3 nebo více velká písmena, malá písmena, číselné a speciální znaky.

6. Potvrďte heslo.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Klikněte na tlačítko **Uložit** a po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Nyní je třeba aktualizovat hesla Snapshot Manageru zařízení StorSimple.

## <a name="next-steps"></a>Další postup
* Další informace o [zabezpečení zařízení StorSimple](storsimple-8000-security.md).
* Další informace o [upravíte konfiguraci zařízení](storsimple-8000-modify-device-config.md).
* Další informace o [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

