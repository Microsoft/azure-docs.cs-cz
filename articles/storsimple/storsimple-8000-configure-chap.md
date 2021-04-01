---
title: Konfigurace protokolu CHAP pro zařízení řady StorSimple 8000 | Microsoft Docs
description: V této části najdete popis postupu konfigurace protokolu CHAP (Challenge Handshake Authentication Protocol) na zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 694d63dacedd8ffcd075e73bb91b5ab0a089b58c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91766493"
---
# <a name="configure-chap-for-your-storsimple-device"></a>Konfigurace protokolu CHAP pro zařízení StorSimple

V tomto kurzu se dozvíte, jak nakonfigurovat protokol CHAP pro zařízení StorSimple. Postup podrobný v tomto článku se týká zařízení řady StorSimple 8000.

Protokol CHAP představuje ověřovací protokol Challenge Handshake. Jedná se o schéma ověřování používané servery k ověření identity vzdálených klientů. Ověření vychází ze sdíleného hesla nebo tajného klíče. Protokol CHAP může být jednosměrný (jednosměrný) nebo společný (obousměrný). Jedním ze způsobů protokolu CHAP je, když cíl ověřuje iniciátor. V rámci vzájemného nebo reverzního protokolu CHAP cíl ověřuje iniciátor a pak iniciátor ověřuje cíl. Ověřování iniciátoru se dá implementovat bez cílového ověřování. Cílové ověřování však lze implementovat pouze v případě, že je implementováno také ověřování iniciátoru.

Jako osvědčený postup doporučujeme, abyste pomocí protokolu CHAP zvýšili zabezpečení iSCSI.

> [!NOTE]
> Mějte na paměti, že na zařízeních StorSimple se aktuálně nepodporuje protokol IPSEC.

Nastavení protokolu CHAP na zařízení StorSimple se dá nakonfigurovat následujícími způsoby:

* Jednosměrné nebo jednosměrné ověřování
* Obousměrné nebo vzájemné zpětné ověřování

V každém z těchto případů je nutné nakonfigurovat portál pro zařízení a software iniciátoru iSCSI serveru. Podrobné pokyny pro tuto konfiguraci jsou popsány v následujícím kurzu.

## <a name="unidirectional-or-one-way-authentication"></a>Jednosměrné nebo jednosměrné ověřování

V případě jednosměrného ověřování cíl ověřuje iniciátor. Toto ověření vyžaduje, abyste nakonfigurovali nastavení iniciátoru protokolu CHAP na zařízení StorSimple a softwaru iniciátoru iSCSI na hostiteli. Podrobné pokyny pro zařízení StorSimple a hostitele Windows jsou popsané dál.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Konfigurace zařízení na jednosměrné ověřování

1. V Azure Portal přejdete do služby StorSimple Správce zařízení. Klikněte na **zařízení** a vyberte a klikněte na zařízení, pro které chcete nakonfigurovat protokol CHAP. Přejít na **nastavení zařízení > zabezpečení**. V okně **nastavení zabezpečení** klikněte na **CHAP**.
   
    ![Iniciátor protokolu CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. V okně **protokolu CHAP** a v části **iniciátoru protokolu CHAP** :
   
   1. Zadejte uživatelské jméno pro iniciátora protokolu CHAP.
   2. Zadejte heslo pro iniciátor protokolu CHAP.
      
      > [!IMPORTANT]
      > Uživatelské jméno protokolu CHAP musí obsahovat méně než 233 znaků. Heslo protokolu CHAP musí mít délku 12 až 16 znaků. Delší uživatelské jméno nebo heslo způsobí selhání ověřování hostitele Windows.
   
   3. Potvrďte heslo.

       ![Iniciátor protokolu CHAP 2](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Klikněte na **Uložit**. Zobrazí se potvrzovací zpráva. Kliknutím na tlačítko **OK** uložte změny.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Konfigurace jednosměrového ověřování na hostitelském serveru Windows
1. Na hostitelském serveru Windows spusťte iniciátor iSCSI.
2. V okně **Vlastnosti iniciátoru iSCSI** proveďte následující kroky:
   
   1. Klikněte na kartu **zjišťování** .
      
       ![Vlastnosti iniciátoru iSCSI](./media/storsimple-configure-chap/IC740944.png)
   2. Klikněte na **Vyhledat portál**.
3. V dialogovém okně **zjistit cílový portál** :
   
   1. Zadejte IP adresu vašeho zařízení.
   2. Klikněte na tlačítko **Upřesnit**.
      
       ![Vyhledat cílový portál](./media/storsimple-configure-chap/IC740945.png)
4. V dialogovém okně **Upřesnit nastavení** :
   
   1. Zaškrtněte políčko **Povolit přihlášení protokolem CHAP** .
   2. Do pole **název** zadejte uživatelské jméno, které jste zadali pro iniciátor protokolu CHAP v Azure Portal.
   3. V poli **cílový tajný klíč** zadejte heslo, které jste zadali pro iniciátor protokolu CHAP v Azure Portal.
   4. Klikněte na **OK**.
      
       ![Rozšířená nastavení – Obecné](./media/storsimple-configure-chap/IC740946.png)
5. Na kartě **cíle** v okně **Vlastnosti iniciátoru iSCSI** se má stav zařízení zobrazit jako **připojeno**. Pokud používáte zařízení StorSimple 1200, pak je každý svazek připojený jako cíl iSCSI. Proto se pro každý svazek musí opakovat kroky 3-4.
   
    ![Svazky připojené jako samostatné cíle](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Pokud změníte název iSCSI, nový název se použije pro nové relace iSCSI. Nová nastavení se nepoužijí pro existující relace, dokud se odhlásíte a znovu přihlásíte.

Další informace o konfiguraci protokolu CHAP na hostitelském serveru Windows najdete v části [Další informace](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Obousměrné nebo vzájemné ověřování

V obousměrném ověřování cíl ověřuje iniciátor a potom iniciátor ověřuje cíl. Tento postup vyžaduje, aby uživatel nakonfiguroval nastavení iniciátoru protokolu CHAP, reverzní nastavení protokolu CHAP na zařízení a software iniciátoru iSCSI na hostiteli. Následující postupy popisují postup konfigurace vzájemného ověřování na zařízení a na hostiteli s Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Konfigurace zařízení pro vzájemné ověřování

1. V Azure Portal přejdete do služby StorSimple Správce zařízení. Klikněte na **zařízení** a vyberte a klikněte na zařízení, pro které chcete nakonfigurovat protokol CHAP. Přejít na **nastavení zařízení > zabezpečení**. V okně **nastavení zabezpečení** klikněte na **CHAP**.
   
    ![Cíl protokolu CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Přejděte dolů na této stránce a v části **cíl protokolu CHAP** :
   
   1. Zadejte **uživatelské jméno reverzního protokolu CHAP** pro vaše zařízení.
   2. Zadejte **reverzní heslo CHAP** pro vaše zařízení.
   3. Potvrďte heslo.
3. V části **iniciátoru protokolu CHAP** :
   
   1. Zadejte **uživatelské jméno** pro vaše zařízení.
   2. Zadejte **heslo** pro vaše zařízení.
   3. Potvrďte heslo.

       ![Iniciátor protokolu CHAP 3](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Klikněte na **Uložit**. Zobrazí se potvrzovací zpráva. Kliknutím na tlačítko **OK** uložte změny.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Konfigurace obousměrného ověřování na hostitelském serveru Windows

1. Na hostitelském serveru Windows spusťte iniciátor iSCSI.
2. V okně **Vlastnosti iniciátoru iSCSI** klikněte na kartu **Konfigurace** .
3. Klikněte na **protokol CHAP**.
4. V dialogovém okně **vzájemného tajného klíče CHAP iniciátoru iSCSI** :
   
   1. Zadejte **heslo reverzního protokolu CHAP** , které jste nakonfigurovali v Azure Portal.
   2. Klikněte na **OK**.
      
       ![společný tajný klíč protokolu CHAP iniciátoru iSCSI](./media/storsimple-configure-chap/IC740949.png)
5. Klikněte na kartu **cíle** .
6. Klikněte na tlačítko **připojit** . 
7. V dialogovém okně **připojit k cíli** klikněte na **Upřesnit**.
8. V dialogovém okně **Upřesnit vlastnosti** :
   
   1. Zaškrtněte políčko **Povolit přihlášení protokolem CHAP** .
   2. Do pole **název** zadejte uživatelské jméno, které jste zadali pro iniciátor protokolu CHAP v Azure Portal.
   3. V poli **cílový tajný klíč** zadejte heslo, které jste zadali pro iniciátor protokolu CHAP v Azure Portal.
   4. Zaškrtněte políčko **provést vzájemné ověřování** .
      
       ![Rozšířené vzájemné ověřování nastavení](./media/storsimple-configure-chap/IC740950.png)
   5. Kliknutím na **OK** dokončete konfiguraci protokolu CHAP.

Další informace o konfiguraci protokolu CHAP na hostitelském serveru Windows najdete v části [Další informace](#additional-considerations).

## <a name="additional-considerations"></a>Další aspekty

Funkce **rychlé připojení** nepodporuje připojení s POVOLENým protokolem CHAP. Když je protokol CHAP povolený, ujistěte se, že pro připojení k cíli použijete tlačítko **připojit** , které je k dispozici na kartě **cíle** .

![Připojit k cíli](./media/storsimple-configure-chap/IC740947.png)

V dialogovém okně **připojit k cílovému cíli** vyberte zaškrtávací políčko **Přidat toto připojení do seznamu oblíbených cílů** . Tento výběr zajistí, že se pokaždé, když se počítač restartuje, provede pokus o obnovení připojení k oblíbeným cílům iSCSI.

## <a name="errors-during-configuration"></a>Chyby během konfigurace

Pokud je konfigurace protokolu CHAP nesprávná, pravděpodobně se zobrazí chybová zpráva **selhání ověřování** .

## <a name="verification-of-chap-configuration"></a>Ověření konfigurace protokolu CHAP

To, jestli se protokol CHAP používá, můžete ověřit provedením následujících kroků.

#### <a name="to-verify-your-chap-configuration"></a>Ověření konfigurace protokolu CHAP
1. Klikněte na **Oblíbené cíle**.
2. Vyberte cíl, pro který jste povolili ověřování.
3. Klikněte na tlačítko **Podrobnosti**.
   
    ![Vlastnosti iniciátoru iSCSI – oblíbené cíle](./media/storsimple-configure-chap/IC740951.png)
4. V dialogovém okně **Podrobnosti o oblíbeném cíli** si poznamenejte položku v poli **ověřování** . Pokud byla konfigurace úspěšná, měla by se jednat o **protokol CHAP**.
   
    ![Podrobnosti o oblíbeném cíli](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [zabezpečení StorSimple](storsimple-8000-security.md).
* Přečtěte si další informace o [používání služby StorSimple Správce zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

