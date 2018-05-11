---
title: Konfigurace protokolů CHAP pro zařízení řady StorSimple 8000 | Microsoft Docs
description: Popisuje, jak nakonfigurovat výzvu ověřování protokol CHAP (Handshake) na zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: c1a558769ffaa52ed2e996a2b537a5ea409101bd
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="configure-chap-for-your-storsimple-device"></a>Konfigurace protokolů CHAP pro vaše zařízení StorSimple

Tento kurz vysvětluje postup konfigurace CHAP pro vaše zařízení StorSimple. Tento postup podrobně popsané v tomto článku se vztahuje na řadu zařízení StorSimple 8000.

CHAP znamená Challenge Handshake Authentication Protocol. Je příslušné schéma ověřování používají servery pro ověření identity vzdálených klientů. Ověření je založena na sdílené heslo nebo tajný klíč. CHAP může být jeden ze způsobů (Jednosměrný) nebo vzájemné (obousměrné). Jedním ze způsobů CHAP je při cíl ověření iniciátor. Ve vzájemné nebo zpětného CHAP cíl ověřuje iniciátoru a pak iniciátoru ověřuje cíl. Iniciátor ověřování se dá implementovat bez cíl ověřování. Cíl ověřování však můžete implementovat, pouze v případě, že se také implementuje iniciátor ověřování.

Jako osvědčený postup doporučujeme použít CHAP pro zvýšení zabezpečení iSCSI.

> [!NOTE]
> Uvědomte si, že protokol IPSEC není aktuálně podporován zařízení StorSimple.

CHAP nastavení v zařízení StorSimple můžete nakonfigurovat následujícím způsobem:

* Jednosměrný nebo jednosměrné ověřování
* Obousměrné nebo vzájemné nebo zpětného ověřování

V každé z těchto případech je potřeba nakonfigurovat na portálu pro zařízení a serverového softwaru iniciátoru iSCSI. Podrobné pokyny pro tuto konfiguraci jsou popsané v následujícím kurzu.

## <a name="unidirectional-or-one-way-authentication"></a>Jednosměrný nebo jednosměrné ověřování

Cíl v jednosměrný ověřování ověřuje iniciátor. Toto ověřování vyžaduje, že nakonfigurujete nastavení iniciátoru CHAP na zařízení StorSimple a iSCSI software Initiator na hostiteli. Podrobné pokyny k zařízení StorSimple a hostitele Windows jsou popsané dále.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Pro konfiguraci zařízení pro jednosměrné ověřování

1. V portálu Azure přejděte do služby StorSimple Manager zařízení. Klikněte na tlačítko **zařízení** a vyberte a klikněte na zařízení, které chcete nakonfigurovat CHAP pro. Přejděte na **nastavení zařízení > zabezpečení**. V **nastavení zabezpečení** okně klikněte na tlačítko **CHAP**.
   
    ![Iniciátoru CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. V **CHAP** okno a v **iniciátoru CHAP** části:
   
   1. Zadejte uživatelské jméno pro vaše iniciátoru CHAP.
   2. Zadejte heslo pro vaše iniciátoru CHAP.
      
    > [!IMPORTANT]
    > Uživatelské jméno CHAP musí obsahovat méně než 233 znaků. CHAP heslo musí být 12 až 16 znaků. Delší uživatelské jméno nebo heslo vede k chybě ověřování na hostiteli systému Windows.
   
   3. Potvrzení hesla.

       ![Iniciátoru CHAP](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Klikněte na **Uložit**. Se zobrazí potvrzovací zpráva. Klikněte na tlačítko **OK** a uložte změny.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Jednosměrné ověřování nakonfigurujete na hostitelském serveru Windows
1. Na hostitelském serveru systému Windows spusťte iniciátor iSCSI.
2. V **vlastnosti iniciátoru iSCSI** okno, proveďte následující kroky:
   
   1. Klikněte **zjišťování** kartě.
      
       ![Vlastnosti iniciátoru iSCSI](./media/storsimple-configure-chap/IC740944.png)
   2. Klikněte na tlačítko **zjistit portál**.
3. V **zjistit cílový portál** dialogové okno:
   
   1. Zadejte IP adresu vašeho zařízení.
   2. Klikněte na tlačítko **rozšířené**.
      
       ![Zjistit cílový portál](./media/storsimple-configure-chap/IC740945.png)
4. V **Upřesnit nastavení** dialogové okno:
   
   1. Vyberte **povolit CHAP přihlásit** zaškrtávací políčko.
   2. V **název** pole, zadejte uživatelské jméno, které jste zadali pro iniciátoru CHAP na portálu Azure.
   3. V **tajný klíč cíle** pole, zadejte heslo, které jste zadali pro iniciátoru CHAP na portálu Azure.
   4. Klikněte na **OK**.
      
       ![Upřesňující nastavení, obecné](./media/storsimple-configure-chap/IC740946.png)
5. Na **cíle** kartě **vlastnosti iniciátoru iSCSI** okně Stav zařízení mají zobrazit jako **připojeno**. Pokud používáte zařízení StorSimple 1200, každý svazek připojit jako cíle iSCSI. Proto kroky 3 a 4 muset opakovat pro každý svazek.
   
    ![Svazky připojené jako samostatné cíle](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Pokud změníte název iSCSI, nový název se používá pro nové relace iSCSI. Nové nastavení nejsou znovu použít pro existující až do odhlášení a přihlášení.

Další informace o konfiguraci CHAP na hostitelském serveru Windows, přejděte na [další aspekty](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Obousměrné nebo vzájemné ověřování

V obousměrné ověřování cíl ověřuje iniciátoru a pak iniciátoru ověřuje cíl. Tento postup vyžaduje, aby uživatel k konfigurace nastavení iniciátoru CHAP a reverse CHAP nastavení na zařízení a služby iSCSI software Initiator na hostiteli. Následující postupy popisují postup konfigurace vzájemného ověřování v zařízení a na hostiteli systému Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Pro konfiguraci zařízení pro vzájemné ověřování

1. V portálu Azure přejděte do služby StorSimple Manager zařízení. Klikněte na tlačítko **zařízení** a vyberte a klikněte na zařízení, které chcete nakonfigurovat CHAP pro. Přejděte na **nastavení zařízení > zabezpečení**. V **nastavení zabezpečení** okně klikněte na tlačítko **CHAP**.
   
    ![Cíle CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Posuňte se dolů na této stránce a v **CHAP cíl** části:
   
   1. Zadejte **Reverse CHAP uživatelské jméno** pro vaše zařízení.
   2. Zadejte **hesla Reverse CHAP** pro vaše zařízení.
   3. Potvrzení hesla.
3. V **iniciátoru CHAP** části:
   
   1. Zadejte **uživatelské jméno** pro vaše zařízení.
   2. Zadejte **heslo** pro vaše zařízení.
   3. Potvrzení hesla.

       ![Iniciátoru CHAP](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Klikněte na **Uložit**. Se zobrazí potvrzovací zpráva. Klikněte na tlačítko **OK** a uložte změny.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Postup konfigurace obousměrné ověřování na hostitelském serveru Windows

1. Na hostitelském serveru systému Windows spusťte iniciátor iSCSI.
2. V **vlastnosti iniciátoru iSCSI** okně klikněte na tlačítko **konfigurace** kartě.
3. Klikněte na tlačítko **CHAP**.
4. V **tajný klíč pro vzájemné CHAP iniciátor iSCSI** dialogové okno:
   
   1. Typ **hesla Reverse CHAP** nakonfigurovaný na portálu Azure.
   2. Klikněte na **OK**.
      
       ![iSCSI initiator vzájemné CHAP tajný klíč](./media/storsimple-configure-chap/IC740949.png)
5. Klikněte **cíle** kartě.
6. Klikněte **Connect** tlačítko. 
7. V **připojení k cílové** dialogové okno, klikněte na tlačítko **Upřesnit**.
8. V **Upřesnit vlastnosti** dialogové okno:
   
   1. Vyberte **povolit CHAP přihlásit** zaškrtávací políčko.
   2. V **název** pole, zadejte uživatelské jméno, které jste zadali pro iniciátoru CHAP na portálu Azure.
   3. V **tajný klíč cíle** pole, zadejte heslo, které jste zadali pro iniciátoru CHAP na portálu Azure.
   4. Vyberte **provést vzájemné ověřování** zaškrtávací políčko.
      
       ![Upřesnit nastavení vzájemného ověření](./media/storsimple-configure-chap/IC740950.png)
   5. Klikněte na tlačítko **OK** k dokončení konfigurace CHAP

Další informace o konfiguraci CHAP na hostitelském serveru Windows, přejděte na [další aspekty](#additional-considerations).

## <a name="additional-considerations"></a>Další aspekty

**Rychlé připojení** funkce nepodporuje připojení, které mají povolené CHAP. Pokud je povoleno CHAP, ujistěte se, že používáte **připojit** tlačítko, která je dostupná na **cíle** kartě připojit k cíli.

![Připojit k cíli](./media/storsimple-configure-chap/IC740947.png)

V **připojit k cíli** dialogu, který se zobrazí, vyberte **přidejte toto připojení do seznamu oblíbených cílů** zaškrtávací políčko. Tato volba zajistí, že pokaždé, když se počítač restartuje, je proveden pokus o obnovení připojení k cílům iSCSI oblíbených položek.

## <a name="errors-during-configuration"></a>Chyby během konfigurace

Pokud vaše konfigurace CHAP je nesprávná, pak budete chtít nejspíš najdete v článku **selhání ověření** chybová zpráva.

## <a name="verification-of-chap-configuration"></a>Ověření konfigurace CHAP

Můžete ověřit, jestli se používá protokol CHAP podle následujících kroků.

#### <a name="to-verify-your-chap-configuration"></a>Chcete-li ověřit konfiguraci CHAP
1. Klikněte na tlačítko **Oblíbené cíle**.
2. Vyberte cíl, pro které jste povolili ověřování.
3. Klikněte na tlačítko **podrobnosti**.
   
    ![Oblíbené cíle vlastnosti iniciátoru iSCSI](./media/storsimple-configure-chap/IC740951.png)
4. V **Oblíbené podrobnosti cíl** dialogové okno pole si všimněte, položky v **ověřování** pole. Pokud konfigurace proběhla úspěšně, by mělo být uvedeno **CHAP**.
   
    ![Podrobnosti o oblíbeného cíle](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Další postup

* Další informace o [zabezpečení zařízení StorSimple](storsimple-8000-security.md).
* Další informace o [pomocí služby StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

