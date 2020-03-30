---
title: Správa zařízení pomocí správce snímků StorSimple | Dokumenty společnosti Microsoft
description: Popisuje použití modulu snap-in Správce snímků StorSimple mmc pro připojení a správu zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: twooley
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: a037ddc5a43b288349b17dc7d954991a2c634aa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254687"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Použití Správce snímků StorSimple k připojení a správě zařízení StorSimple
## <a name="overview"></a>Přehled
Uzly v podokně **Obor** správce snímků StorSimple můžete použít k ověření importovaných dat zařízení StorSimple a k aktualizaci připojených úložných zařízení. Navíc po kliknutí na uzel **Zařízení,** můžete zobrazit seznam připojených zařízení a odpovídající informace o stavu v podokně **Výsledky.**

![Připojená zařízení](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Obrázek 1: Připojené zařízení Správce snímků StorSimple** 

V závislosti na výběrech **zobrazení** se v podokně **Výsledky** zobrazí následující informace o jednotlivých zařízeních. (Další informace o konfiguraci zobrazení naleznete v [nabídce Zobrazení](storsimple-use-snapshot-manager.md#view-menu).

| Sloupec Výsledky | Popis |
|:--- |:--- |
| Name (Název) |Název zařízení nakonfigurovaný na klasickém portálu Azure |
| Model |Číslo modelu zařízení |
| Version |Verze softwaru nainstalovaného v zařízení |
| Status |Zda je zařízení k dispozici |
| Naposledy synchronizované |Datum a čas, kdy bylo zařízení naposledy synchronizováno |
| Sériové číslo |Sériové číslo zařízení |

Pokud klepnete pravým tlačítkem myši na uzel **Zařízení** v podokně **Obor,** můžete vybrat z následujících akcí:

* Přidání nebo nahrazení zařízení
* Připojení zařízení a ověření importu
* Aktualizace připojených zařízení

Pokud kliknete na uzel **Zařízení** a potom v podokně **Výsledky** kliknete pravým tlačítkem myši na název zařízení, můžete vybrat z následujících akcí:

* Ověření zařízení
* Zobrazení podrobností o zařízení
* Aktualizace zařízení
* Odstranění konfigurace zařízení
* Změna hesla zařízení

> [!NOTE]
> Všechny tyto akce jsou také k dispozici v podokně **Akce.**


Tento kurz vysvětluje, jak používat Správce snímků StorSimple k připojení a správě zařízení a provádění následujících úloh:

* Přidání nebo nahrazení zařízení
* Připojení zařízení a ověření importu
* Aktualizace připojených zařízení
* Ověření zařízení
* Zobrazení podrobností o zařízení
* Aktualizace jednotlivých zařízení
* Odstranění konfigurace zařízení
* Změna hesla zařízení, jehož platnost vypršela
* Nahrazení neúspěšného zařízení

> [!NOTE]
> Obecné informace o použití rozhraní Správce snímků StorSimple naleznete v [uživatelském rozhraní Správce snímků StorSimple](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Přidání nebo nahrazení zařízení
Pomocí následujícího postupu přidejte nebo nahraďte zařízení StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Přidání nebo nahrazení zařízení
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klikněte pravým tlačítkem myši na uzel **Zařízení** a potom **klikněte**na Konfigurovat zařízení . Zobrazí se dialogové okno **Konfigurovat zařízení.**
   
    ![Konfigurace zařízení StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. V rozevíracím poli **Zařízení** vyberte IP adresu zařízení nebo virtuálního zařízení. 
4. Do textového pole **Heslo** zadejte heslo Správce snímků StorSimple, které jste pro zařízení vytvořili na klasickém portálu Azure. Klikněte na tlačítko **OK**. Správce snímků StorSimple vyhledá zařízení, které jste identifikovali. 
   
   * Pokud je zařízení k dispozici, Správce snímků StorSimple přidá připojení.
   * Pokud zařízení není z nějakého důvodu k dispozici, Správce snímků StorSimple vrátí chybovou zprávu. Klepnutím na **tlačítko OK** zavřete chybovou zprávu a klepnutím na tlačítko **Storno** zavřete dialogové okno **Konfigurovat zařízení.**

## <a name="connect-a-device-and-verify-imports"></a>Připojení zařízení a ověření importu
Pomocí následujícího postupu připojte zařízení StorSimple a ověřte, zda jsou importovány všechny existující skupiny svazků, které mají přidružené zálohy.

#### <a name="to-connect-a-device-and-verify-imports"></a>Připojení zařízení a ověření importu
1. Chcete-li připojit zařízení ke Správci snímků StorSimple, postupujte podle pokynů v části Přidání nebo nahrazení zařízení. Když se připojí k zařízení, StorSimple Snapshot Manager reaguje takto:
   
   * Pokud zařízení není z nějakého důvodu k dispozici, Správce snímků StorSimple vrátí chybovou zprávu. 
   
   * Pokud je zařízení k dispozici, Správce snímků StorSimple přidá připojení. Když vyberete zařízení, zobrazí se v podokně **Výsledky** a pole stavu označuje, že zařízení je **k dispozici**. Správce snímků StorSimple importuje všechny skupiny svazků nakonfigurované pro zařízení za předpokladu, že skupiny svazků mají přidružené zálohy. Zásady zálohování nejsou importovány. Skupiny svazků, které nemají přidružené zálohy, se neimportují.
2. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
3. Klepněte pravým tlačítkem myši na horní uzel v podokně **Obor** a potom klepněte na příkaz **Přepnout zobrazení importu**.
   
    ![Vybrat přepnout zobrazení importů](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. Zobrazí se dialogové okno **Přepnout zobrazení importu,** které zobrazuje stav importovaných skupin svazků a záloh. Klikněte na tlačítko **OK**.

Po úspěšném importu skupin svazků a záloh můžete ke správě storsimple snapshot manageru použít správce svazků, stejně jako byste spravovali skupiny svazků a zálohy, které jste vytvořili a nakonfigurovali pomocí Správce snímků StorSimple. 

## <a name="refresh-connected-devices"></a>Aktualizace připojených zařízení
Pomocí následujícího postupu synchronizujte připojená zařízení StorSimple se Správcem snímků StorSimple.

#### <a name="to-refresh-connected-devices"></a>Aktualizace připojených zařízení
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klepněte pravým tlačítkem myši na **položku Zařízení**a potom klepněte na příkaz **Aktualizovat zařízení**. Tím se synchronizují připojená zařízení se Správcem snímků StorSimple, takže můžete zobrazit skupiny svazků a zálohy, včetně všech nedávných dodatků. 
   
    ![Aktualizace zařízení StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

Akce **Aktualizovat zařízení** načte všechny nové skupiny svazků a všechny přidružené zálohy z připojených zařízení. Na rozdíl od akce **Znovu prohledat svazky,** která je k dispozici pro uzel **Svazky,** **obnovovací zařízení** neobnoví záložní registr.

## <a name="authenticate-a-device"></a>Ověření zařízení
Pomocí následujícího postupu ověřte zařízení StorSimple pomocí Správce snímků StorSimple.

#### <a name="to-authenticate-a-device"></a>Ověření zařízení
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klepněte na **položku Zařízení**.
3. V podokně **Výsledky** klikněte pravým tlačítkem myši na název zařízení a potom klikněte na **příkaz Ověřit**.
4. Zobrazí se dialogové okno **Ověřit.** Zadejte heslo zařízení a klepněte na tlačítko **OK**.
   
    ![Dialogové okno Ověřit](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Zobrazení podrobností o zařízení
Pomocí následujícího postupu můžete zobrazit podrobnosti o zařízení StorSimple a v případě potřeby znovu synchronizovat zařízení se Správcem snímků StorSimple.

#### <a name="to-view-and-resynchronize-device-details"></a>Zobrazení a synchronizace podrobností zařízení
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klepněte na **položku Zařízení**.
3. V podokně **Výsledky** klikněte pravým tlačítkem myši na název zařízení a potom klikněte na **položku Podrobnosti**.

4. Zobrazí se dialogové okno **Podrobnosti o zařízení.** Toto pole zobrazuje název, model, verzi, sériové číslo, stav, cílový kvalifikovaný název iSCSI (IQN) a datum a čas poslední synchronizace.

* Chcete-li zařízení **synchronizovat,** klepněte na tlačítko Znovu synchronizovat.
* Klepnutím na **tlačítko OK** nebo **Storno** zavřete dialogové okno.
  
  ![Podrobnosti o zařízení](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Aktualizace jednotlivých zařízení
Pomocí následujícího postupu znovu synchronizovat jednotlivé zařízení StorSimple s Správce snímků StorSimple.

#### <a name="to-refresh-a-device"></a>Aktualizace zařízení
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple. 
2. V podokně **Obor** klepněte na **položku Zařízení**. 
3. V podokně **Výsledky** klikněte pravým tlačítkem myši na název zařízení a potom klikněte na **příkaz Aktualizovat zařízení**. Tím se zařízení synchronizuje se Správcem snímků StorSimple.

## <a name="delete-a-device-configuration"></a>Odstranění konfigurace zařízení
Pomocí následujícího postupu odstraňte jednotlivé konfigurace zařízení StorSimple ze Správce snímků StorSimple.

#### <a name="to-delete-a-device-configuration"></a>Odstranění konfigurace zařízení
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klepněte na **položku Zařízení**. 
3. V podokně **Výsledky** klikněte pravým tlačítkem myši na název zařízení a potom klikněte na **příkaz Odstranit**. 
4. Zobrazí se následující zpráva. Klepnutím na tlačítko **Ano** odstraňte konfiguraci nebo klepnutím na tlačítko **Ne** odstranění zrušte.
   
    ![Odstranění konfigurace zařízení](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Změna hesla zařízení, jehož platnost vypršela
Je nutné zadat heslo k ověření zařízení StorSimple pomocí Správce snímků StorSimple. Toto heslo nakonfigurujete při nastavení zařízení pomocí rozhraní prostředí Windows PowerShell. Platnost hesla však může vypršet. Pokud k tomu dojde, můžete použít klasický portál Azure ke změně hesla. Poté, protože zařízení bylo nakonfigurováno ve Správci snímků StorSimple před vypršením platnosti hesla, je nutné znovu ověřit zařízení ve Správci snímků StorSimple.

#### <a name="to-change-the-expired-password"></a>Změna hesla, jehož platnost vypršela
1. Na klasickém portálu Azure spusťte službu StorSimple Manager.
2. Klikněte na**Konfigurace** **zařízení** > pro zařízení.
3. Posuňte se dolů do části Správce snímků StorSimple. Zadejte heslo, které má 14 až 15 znaků. Ujistěte se, že heslo obsahuje kombinaci velkých, malých, číselných a speciálních znaků.
4. Znovu zadejte heslo, abyste ho potvrdili.
5. V dolní části stránky klikněte na **Uložit**.

#### <a name="to-re-authenticate-the-device"></a>Opětovné ověření zařízení
1. Spusťte Správce snímků StorSimple.
2. V podokně **Obor** klepněte na **položku Zařízení**. V podokně **Výsledky** se zobrazí seznam nakonfigurovaných zařízení.
3. Vyberte zařízení, klikněte pravým tlačítkem myši a potom klikněte na **Ověřit**.
4. V okně **Ověření** zadejte nové heslo.
5. Vyberte zařízení, klikněte pravým tlačítkem myši a vyberte **Aktualizovat zařízení**. Tím se zařízení synchronizuje se Správcem snímků StorSimple.

## <a name="replace-a-failed-device"></a>Nahrazení neúspěšného zařízení
Pokud zařízení StorSimple selže a je nahrazeno pohotovostním zařízením (převzetíslužeb při selhání), připojte se k novému zařízení pomocí následujících kroků a zobrazte přidružené zálohy.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Připojení k novému zařízení po převzetí služeb při selhání
1. Překonfigurujte připojení iSCSI k novému zařízení. Pokyny najdete v části "Krok 7: Připojení, inicializaci a formátování svazku" v [části Nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Pokud nové zařízení StorSimple má stejnou ADRESU IP jako stará, můžete připojit starou konfiguraci.


1. Zastavte službu Microsoft StorSimple Management Service:
   
   1. Spusťte Správce serveru.
   2. Na řídicím panelu Správce serveru vyberte v nabídce **Nástroje** **položku Služby**.
   3. V okně **Služby** vyberte **službu Microsoft StorSimple Management Service**.
   4. V pravém podokně klikněte v části **Microsoft StorSimple Management Service**na zastavit **službu**.
2. Odeberte informace o konfiguraci související se starým zařízením:
   
   1. V Průzkumníkovi souborů přejděte na C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Odstraňte soubory ve složce BACatalog.
3. Restartujte službu Microsoft StorSimple Management Service:
   
   1. Na řídicím panelu Správce serveru vyberte v nabídce **Nástroje** **položku Služby**.
   2. V okně **Služby** vyberte **službu Microsoft StorSimple Management Service**.
   3. V pravém podokně klepněte v části **Microsoft StorSimple Management Service**na **restartovat službu**.
4. Spusťte Správce snímků StorSimple.
5. Chcete-li nakonfigurovat nové zařízení StorSimple, proveďte kroky v kroku 2: Připojení zařízení StorSimple ve [Správci snímků Nasazení StorSimple](storsimple-snapshot-manager-deployment.md).
6. Klepněte pravým tlačítkem myši na uzel nejvyšší úrovně v podokně **Obor** (Správce snímků StorSimple v příkladu) a potom klepněte na příkaz **Přepnout zobrazení importu**. 
7. Zpráva se zobrazí, když jsou importované skupiny svazků a zálohy viditelné ve Správci snímků StorSimple. Klikněte na tlačítko **OK**.

## <a name="next-steps"></a>Další kroky
* Naučte se [používat Správce snímků StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Naučte se [používat Správce snímků StorSimple k zobrazení a správě svazků](storsimple-snapshot-manager-manage-volumes.md).

