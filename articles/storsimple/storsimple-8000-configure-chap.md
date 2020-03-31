---
title: Konfigurace protokolu CHAP pro zařízení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Popisuje, jak nakonfigurovat protokol CHAP (Challenge Handshake Authentication Protocol) na zařízení StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267960"
---
# <a name="configure-chap-for-your-storsimple-device"></a>Konfigurace protokolu CHAP pro zařízení StorSimple

Tento kurz vysvětluje, jak nakonfigurovat protokol CHAP pro vaše zařízení StorSimple. Postup popsaný v tomto článku se vztahuje na zařízení řady StorSimple 8000.

Chap je zkratka pro Challenge Handshake Authentication Protocol. Jedná se o schéma ověřování používané servery k ověření identity vzdálených klientů. Ověření je založeno na sdíleném heslu nebo tajném klíči. CHAP může být jednosměrný (jednosměrný) nebo vzájemný (obousměrný). Jedním ze způsobů, chap je, když cíl ověří iniciátor. Ve vzájemné nebo reverzní CHAP cíl ověří iniciátora a pak iniciátor ověří cíl. Iniciátor ověřování lze implementovat bez ověřování cíle. Cílové ověřování však lze implementovat pouze v případě, že je implementováno také ověřování iniciátoru.

Jako osvědčený postup doporučujeme použít chap k vylepšení zabezpečení iSCSI.

> [!NOTE]
> Mějte na paměti, že protokol IPSEC není aktuálně podporován na zařízeních StorSimple.

Nastavení protokolu CHAP na zařízení StorSimple lze nakonfigurovat následujícími způsoby:

* Jednosměrné nebo jednosměrné ověřování
* Obousměrné nebo vzájemné nebo reverzní ověřování

V každém z těchto případů je třeba nakonfigurovat portál pro zařízení a iniciátor serveru iscsi. Podrobné kroky pro tuto konfiguraci jsou popsány v následujícím kurzu.

## <a name="unidirectional-or-one-way-authentication"></a>Jednosměrné nebo jednosměrné ověřování

Při jednosměrném ověřování cíl ověřuje iniciátor. Toto ověřování vyžaduje konfiguraci nastavení iniciátoru protokolu CHAP na zařízení StorSimple a v softwaru iniciátoru iSCSI na hostiteli. Podrobné postupy pro zařízení StorSimple a hostitele systému Windows jsou popsány dále.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Konfigurace zařízení pro jednosměrné ověřování

1. Na webu Azure Portal přejděte na službu Správce zařízení StorSimple. Klikněte na **Zařízení** a vyberte a klikněte na zařízení, pro které chcete chap konfigurovat. Přejděte na **nastavení zařízení > zabezpečení**. V okně **Nastavení zabezpečení** klepněte na příkaz **CHAP**.
   
    ![Iniciátor chap](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. V okně **CHAP** a v části **Iniciátor chAP:**
   
   1. Zadejte uživatelské jméno iniciátoru chap.
   2. Zadejte heslo pro iniciátor chap.
      
      > [!IMPORTANT]
      > Uživatelské jméno chap musí obsahovat méně než 233 znaků. Heslo chap musí být mezi 12 a 16 znaky. Delší uživatelské jméno nebo heslo má za následek selhání ověřování na hostiteli systému Windows.
   
   3. Potvrďte heslo.

       ![Iniciátor chap](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Klikněte na **Uložit**. Zobrazí se potvrzovací zpráva. Klikněte na tlačítko **OK** a uložte změny.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Konfigurace jednosměrného ověřování na hostitelském serveru systému Windows
1. Na hostitelském serveru systému Windows spusťte iniciátor iSCSI.
2. V okně **Vlastnosti iniciátoru iSCSI** proveďte následující kroky:
   
   1. Klikněte na kartu **Zjišťování.**
      
       ![Vlastnosti iniciátoru iSCSI](./media/storsimple-configure-chap/IC740944.png)
   2. Klepněte na tlačítko **Zjistit portál**.
3. V dialogovém okně **Zjišťovat cílový portál:**
   
   1. Zadejte IP adresu vašeho zařízení.
   2. Klikněte na tlačítko **Upřesnit**.
      
       ![Objevte cílový portál](./media/storsimple-configure-chap/IC740945.png)
4. V dialogovém okně **Upřesnit nastavení:**
   
   1. Zaškrtněte políčko **Povolit přihlášení chap.**
   2. Do pole **Název** zadejme uživatelské jméno, které jste zadali pro iniciátor chap na webu Azure Portal.
   3. V poli **Tajné zabezpečení cíl** zadej heslo, které jste zadali pro iniciátor chap na webu Azure Portal.
   4. Klikněte na tlačítko **OK**.
      
       ![Obecné rozšířené nastavení](./media/storsimple-configure-chap/IC740946.png)
5. Na kartě **Cíle** v okně **Vlastnosti iniciátoru iSCSI** by se měl stav zařízení zobrazit jako **Připojeno**. Pokud používáte zařízení StorSimple 1200, je každý svazek připojen jako cíl iSCSI. Proto kroky 3-4 bude třeba opakovat pro každý svazek.
   
    ![Svazky namontované jako samostatné cíle](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Pokud změníte název iSCSI, nový název se použije pro nové relace iSCSI. Nová nastavení se nepoužívají pro existující relace, dokud se neodhlásíte a znovu nepřihlásíte.

Další informace o konfiguraci chap na hostitelském serveru systému Windows naleznete v části [Další důležité informace](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Obousměrné nebo vzájemné ověřování

Při obousměrném ověřování cíl ověří iniciátora a pak iniciátor ověří cíl. Tento postup vyžaduje, aby uživatel nakonfiguroval nastavení iniciátoru protokolu CHAP, stornoval nastavení protokolu CHAP v zařízení a software iniciátoru iSCSI na hostiteli. Následující postupy popisují kroky konfigurace vzájemného ověřování na zařízení a na hostiteli systému Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Konfigurace zařízení pro vzájemné ověřování

1. Na webu Azure Portal přejděte na službu Správce zařízení StorSimple. Klikněte na **Zařízení** a vyberte a klikněte na zařízení, pro které chcete chap konfigurovat. Přejděte na **nastavení zařízení > zabezpečení**. V okně **Nastavení zabezpečení** klepněte na příkaz **CHAP**.
   
    ![Cíl CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Posuňte se dolů na této stránce a v části **Cíl chap:**
   
   1. Zadejte **uživatelské jméno reverse chap** pro vaše zařízení.
   2. Zadejte **heslo reverse chap** pro vaše zařízení.
   3. Potvrďte heslo.
3. V části **Iniciátor chap:**
   
   1. Zadejte **uživatelské jméno** zařízení.
   2. Zadejte **heslo** pro své zařízení.
   3. Potvrďte heslo.

       ![Iniciátor chap](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Klikněte na **Uložit**. Zobrazí se potvrzovací zpráva. Klikněte na tlačítko **OK** a uložte změny.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Konfigurace obousměrného ověřování na hostitelském serveru systému Windows

1. Na hostitelském serveru systému Windows spusťte iniciátor iSCSI.
2. V okně **Vlastnosti iniciátoru iSCSI** klepněte na kartu **Konfigurace.**
3. Klepněte na **položku CHAP**.
4. V dialogovém okně Tajný klíč **iniciátoru iscsi:**
   
   1. Zadejte **heslo reverse chap,** které jste nakonfigurovali na webu Azure Portal.
   2. Klikněte na tlačítko **OK**.
      
       ![Iniciátor iniciátoru vzájemného tajného klíče CHAP](./media/storsimple-configure-chap/IC740949.png)
5. Klikněte na kartu **Cíle.**
6. Klepněte na tlačítko **Připojit.** 
7. V dialogovém okně **Připojit k cíli** klepněte na tlačítko **Upřesnit**.
8. V dialogovém okně **Upřesnit vlastnosti:**
   
   1. Zaškrtněte políčko **Povolit přihlášení chap.**
   2. Do pole **Název** zadejme uživatelské jméno, které jste zadali pro iniciátor chap na webu Azure Portal.
   3. V poli **Tajné zabezpečení cíl** zadej heslo, které jste zadali pro iniciátor chap na webu Azure Portal.
   4. Zaškrtněte políčko **Provést vzájemné ověřování.**
      
       ![Vzájemné ověřování upřesňujících nastavení](./media/storsimple-configure-chap/IC740950.png)
   5. Chcete-li dokončit konfiguraci protokolu CHAP, klepněte na tlačítko **OK.**

Další informace o konfiguraci chap na hostitelském serveru systému Windows naleznete v části [Další důležité informace](#additional-considerations).

## <a name="additional-considerations"></a>Další aspekty

Funkce **Rychlé připojení** nepodporuje připojení, která mají povolenou protokol CHAP. Pokud je protokol CHAP povolen, ujistěte se, že pro připojení k cíli použijete tlačítko **Připojit,** které je k dispozici na kartě **Cíle.**

![Připojení k cíli](./media/storsimple-configure-chap/IC740947.png)

V zobrazeném dialogovém okně **Připojit k cíli** zaškrtněte políčko Přidat **toto připojení do seznamu oblíbených cílů.** Tento výběr zajišťuje, že při každém restartování počítače je proveden pokus o obnovení připojení k oblíbeným cílům iSCSI.

## <a name="errors-during-configuration"></a>Chyby během konfigurace

Pokud je konfigurace protokolu CHAP nesprávná, pravděpodobně se zobrazí chybová zpráva **Osazení.**

## <a name="verification-of-chap-configuration"></a>Ověření konfigurace protokolu CHAP

Pomocí následujících kroků můžete ověřit, zda je chap používán.

#### <a name="to-verify-your-chap-configuration"></a>Ověření konfigurace protokolu CHAP
1. Klepněte na **položku Oblíbené cíle**.
2. Vyberte cíl, pro který jste povolili ověřování.
3. Klepněte na **položku Podrobnosti**.
   
    ![Iniciátor iSCSI má oblíbené cíle](./media/storsimple-configure-chap/IC740951.png)
4. V dialogovém okně **Podrobnosti oblíbeného cíle** si poznamenejte položku v poli **Ověřování.** Pokud byla konfigurace úspěšná, mělo by se říci **CHAP**.
   
    ![Podrobnosti o oblíbeném cíli](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Další kroky

* Další informace o [zabezpečení StorSimple](storsimple-8000-security.md).
* Další informace o [správě zařízení StorSimple Device Manager pomocí služby StorSimple](storsimple-8000-manager-service-administration.md).

