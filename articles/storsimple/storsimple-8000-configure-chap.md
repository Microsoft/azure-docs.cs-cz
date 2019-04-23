---
title: Konfigurace protokolu CHAP pro zařízení StorSimple řady 8000 | Dokumentace Microsoftu
description: Popisuje, jak nakonfigurovat Handshake ověřování protokol CHAP (Challenge) na zařízení StorSimple.
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
ms.openlocfilehash: efc116c278bfe72419800603a3b365f461fe0a28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362701"
---
# <a name="configure-chap-for-your-storsimple-device"></a>Konfigurace protokolu CHAP pro zařízení StorSimple

Tento kurz vysvětluje postup konfigurace protokolu CHAP pro zařízení StorSimple. Tento postup podrobně popsané v tomto článku se vztahuje na zařízeních StorSimple řady 8000.

Protokol CHAP jsou zahrnovaného Challenge Handshake Authentication Protocol. Je schéma ověřování používat servery pro ověření identity vzdálených klientů. Ověření je založen na sdílené heslo nebo tajný klíč. Protokol CHAP může být jedním ze způsobů (jednosměrných) nebo vzájemné (obousměrné). Jedním ze způsobů CHAP je při cíl ověření iniciátor. Ve vzájemné nebo reverzního CHAP cíl ověřuje iniciátor a pak je iniciátor ověřuje cíl. Iniciátor ověřování je možné implementovat bez cíl ověřování. Cíl ověřování, ale je možné implementovat pouze v případě, že je iniciátor ověřování také implementováno.

Jako osvědčený postup doporučujeme použít protokol CHAP k vylepšení zabezpečení iSCSI.

> [!NOTE]
> Uvědomte si, že protokol IPSEC není aktuálně podporován zařízení StorSimple.

Nastavení protokolu CHAP na zařízení StorSimple můžete nastavit následujícími způsoby:

* Jednosměrné nebo jednosměrného ověřování
* Obousměrné nebo vzájemné nebo reverzní ověření

Ve všech těchto případech je potřeba nakonfigurovat na portálu pro zařízení a serverovým softwarem iniciátoru iSCSI. Podrobné kroky pro tuto konfiguraci jsou popsány v následujícím kurzu.

## <a name="unidirectional-or-one-way-authentication"></a>Jednosměrné nebo jednosměrného ověřování

Při ověřování jednosměrnou ověřuje cíl iniciátor. Toto ověřování, musíte nakonfigurovat nastavení iniciátoru protokolu CHAP na zařízení StorSimple a iSCSI software Initiator na hostiteli. Podrobné pokyny k zařízení StorSimple a hostitele Windows jsou popsány níže.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Pro konfiguraci zařízení jednosměrného ověřování

1. Na webu Azure Portal přejděte do služby Správce zařízení StorSimple. Klikněte na tlačítko **zařízení** a vyberte a klikněte na které chcete nakonfigurovat protokol CHAP pro zařízení. Přejděte na **zařízení Nastavení > zabezpečení**. V **nastavení zabezpečení** okna, klikněte na tlačítko **CHAP**.
   
    ![Iniciátor CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. V **CHAP** okně a **iniciátor protokolu CHAP** části:
   
   1. Zadejte uživatelské jméno pro vaše iniciátoru protokolu CHAP.
   2. Zadejte heslo pro váš iniciátoru protokolu CHAP.
      
      > [!IMPORTANT]
      > Uživatelské jméno CHAP musí obsahovat méně než 233 znaků. Heslo CHAP musí být 12 až 16 znaků. Delší uživatelské jméno nebo heslo má za následek selhání ověřování na hostitele Windows.
   
   3. Potvrďte heslo.

       ![Iniciátor CHAP](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Klikněte na **Uložit**. Zobrazí se potvrzovací zpráva. Klikněte na tlačítko **OK** a uložte změny.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Postup konfigurace jednosměrného ověřování na hostitelském Windows serveru
1. Na hostitelském serveru Windows spusťte iniciátor iSCSI.
2. V **vlastnosti iniciátoru iSCSI** okno, proveďte následující kroky:
   
   1. Klikněte na tlačítko **zjišťování** kartu.
      
       ![Vlastnosti iniciátoru iSCSI](./media/storsimple-configure-chap/IC740944.png)
   2. Klikněte na tlačítko **zjistit portál**.
3. V **zjistit cílový portál** dialogové okno:
   
   1. Zadejte IP adresu vašeho zařízení.
   2. Klikněte na tlačítko **Advanced**.
      
       ![Zjistit cílový portál](./media/storsimple-configure-chap/IC740945.png)
4. V **Upřesnit nastavení** dialogové okno:
   
   1. Vyberte **povolit protokol CHAP přihlášení** zaškrtávací políčko.
   2. V **název** pole, zadejte uživatelské jméno, které jste zadali pro iniciátor protokolu CHAP na webu Azure Portal.
   3. V **cílového tajného kódu** pole, zadejte heslo, které jste zadali pro iniciátor protokolu CHAP na webu Azure Portal.
   4. Klikněte na **OK**.
      
       ![Upřesňující nastavení, obecné](./media/storsimple-configure-chap/IC740946.png)
5. Na **cíle** karty **vlastnosti iniciátoru iSCSI** okna, stav zařízení mají zobrazit jako **připojeno**. Pokud používáte zařízení StorSimple 1200, všechny svazky připojené jako cíl iSCSI. Proto kroky 3 až 4 muset opakovat pro každý svazek.
   
    ![Svazky připojené jako samostatné cíle](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Pokud změníte název iSCSI, nový název se používá pro nové relace iSCSI. Nové nastavení nejsou použity pro existující relace, dokud se neodhlásíte a přihlásit znovu.

Další informace o konfiguraci protokolu CHAP na hostitelském Windows serveru, přejděte na [další aspekty](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Obousměrné nebo vzájemné ověřování

V případě obousměrné ověřování cíl ověřuje iniciátor a potom iniciátor ověřuje cíl. Tento postup vyžaduje, aby uživatel můžete nakonfigurovat nastavení iniciátoru protokolu CHAP, nastavení protokolu reverse CHAP na zařízení a iSCSI software Initiator na hostiteli. Následující postupy popisují postup konfigurace vzájemného ověřování v zařízení a na hostitele Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Konfigurace zařízení pro vzájemné ověřování

1. Na webu Azure Portal přejděte do služby Správce zařízení StorSimple. Klikněte na tlačítko **zařízení** a vyberte a klikněte na které chcete nakonfigurovat protokol CHAP pro zařízení. Přejděte na **zařízení Nastavení > zabezpečení**. V **nastavení zabezpečení** okna, klikněte na tlačítko **CHAP**.
   
    ![Cíl CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Posuňte se dolů na této stránce a v **cíl protokolu CHAP** části:
   
   1. Zadejte **uživatelského jména** pro vaše zařízení.
   2. Zadat **hesla reverzního CHAP** pro vaše zařízení.
   3. Potvrďte heslo.
3. V **iniciátor protokolu CHAP** části:
   
   1. Zadejte **uživatelské jméno** pro vaše zařízení.
   2. Zadejte **heslo** pro vaše zařízení.
   3. Potvrďte heslo.

       ![Iniciátor CHAP](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Klikněte na **Uložit**. Zobrazí se potvrzovací zpráva. Klikněte na tlačítko **OK** a uložte změny.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Konfigurace obousměrné ověřování na hostitelském Windows serveru

1. Na hostitelském serveru Windows spusťte iniciátor iSCSI.
2. V **vlastnosti iniciátoru iSCSI** okna, klikněte na tlačítko **konfigurace** kartu.
3. Klikněte na tlačítko **CHAP**.
4. V **tajný klíč pro vzájemné CHAP iniciátoru iSCSI** dialogové okno:
   
   1. Typ **hesla Reverse CHAP** , který jste nakonfigurovali na webu Azure Portal.
   2. Klikněte na **OK**.
      
       ![iSCSI initiator vzájemné tajný klíč protokolu CHAP](./media/storsimple-configure-chap/IC740949.png)
5. Klikněte na tlačítko **cíle** kartu.
6. Klikněte na tlačítko **připojit** tlačítko. 
7. V **připojení k cílové** dialogové okno, klikněte na tlačítko **Upřesnit**.
8. V **Upřesnit vlastnosti** dialogové okno:
   
   1. Vyberte **povolit protokol CHAP přihlášení** zaškrtávací políčko.
   2. V **název** pole, zadejte uživatelské jméno, které jste zadali pro iniciátor protokolu CHAP na webu Azure Portal.
   3. V **cílového tajného kódu** pole, zadejte heslo, které jste zadali pro iniciátor protokolu CHAP na webu Azure Portal.
   4. Vyberte **provést vzájemné ověřování** zaškrtávací políčko.
      
       ![Upřesňující nastavení vzájemného ověřování](./media/storsimple-configure-chap/IC740950.png)
   5. Klikněte na tlačítko **OK** k dokončení konfigurace protokolu CHAP

Další informace o konfiguraci protokolu CHAP na hostitelském Windows serveru, přejděte na [další aspekty](#additional-considerations).

## <a name="additional-considerations"></a>Další aspekty

**Rychlé připojení** funkce nepodporuje připojení, které mají povolen protokol CHAP. Když je povolené CHAP, ujistěte se, že použít **připojit** tlačítka, které je k dispozici na **cíle** kartu pro připojení k cíli.

![Připojit k cíli](./media/storsimple-configure-chap/IC740947.png)

V **připojit k cíli** dialogové okno, které se zobrazí, vyberte **přidat toto připojení do seznamu oblíbených cíle** zaškrtávací políčko. Tato volba zajistí, že pokaždé, když se počítač restartuje, je proveden pokus o obnovení připojení na Oblíbené cíle iSCSI.

## <a name="errors-during-configuration"></a>Chyby během konfigurace

Pokud je nesprávná konfigurace protokolu CHAP, pak budete chtít nejspíš najdete v článku **ověřování se nezdařilo** chybovou zprávu.

## <a name="verification-of-chap-configuration"></a>Ověření konfigurace protokolu CHAP

Můžete ověřit, že používá protokol CHAP podle následujících kroků.

#### <a name="to-verify-your-chap-configuration"></a>Chcete-li ověřit konfiguraci protokolu CHAP
1. Klikněte na tlačítko **Oblíbené cíle**.
2. Vyberte cíl, pro které jste povolili ověřování.
3. Klikněte na tlačítko **podrobnosti**.
   
    ![Oblíbené cíle vlastnosti iniciátoru iSCSI](./media/storsimple-configure-chap/IC740951.png)
4. V **Oblíbené podrobnosti o cíli** dialogové okno pole, mějte na paměti, položku **ověřování** pole. Pokud konfigurace proběhla úspěšně, by mělo být uvedeno **CHAP**.
   
    ![Podrobnosti o cíli Oblíbené](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Další postup

* Další informace o [zabezpečení zařízení StorSimple](storsimple-8000-security.md).
* Další informace o [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

