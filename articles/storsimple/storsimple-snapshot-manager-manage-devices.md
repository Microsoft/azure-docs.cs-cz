---
title: Správa zařízení pomocí StorSimple Snapshot Manageru | Dokumentace Microsoftu
description: Popisuje způsob použití modulu snap-in konzoly MMC StorSimple Snapshot Manageru k připojení a správě zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: SharS
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 51632b8b68640814fc113a94925b6d6deaca4c5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60482498"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>K připojení a správě zařízení StorSimple pomocí StorSimple Snapshot Manageru
## <a name="overview"></a>Přehled
Uzly můžete pomocí StorSimple Snapshot Manageru **oboru** podoknem ověřte importovaná data zařízení StorSimple a aktualizujte zařízení připojené úložiště. Kromě toho, když kliknete **zařízení** uzlu, zobrazí se seznam připojených zařízení a v odpovídající informace o stavu **výsledky** podokně.

![Připojená zařízení](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Obrázek 1: Připojené zařízení StorSimple Snapshot Manageru** 

V závislosti na vaší **zobrazení** výběr **výsledky** podokně se zobrazí následující informace o každém zařízení. (Další informace o konfiguraci zobrazení, přejděte na [nabídka zobrazení](storsimple-use-snapshot-manager.md#view-menu).

| Sloupec výsledků | Popis |
|:--- |:--- |
| Name |Název zařízení podle konfigurace v portálu Azure classic |
| Model |Číslo modelu zařízení |
| Version |Verze softwaru nainstalovaného na zařízení |
| Status |Určuje, zda je k dispozici zařízení |
| Poslední synchronizace |Datum a čas, kdy byl poslední synchronizace zařízení |
| Sériové číslo |Sériové číslo zařízení |

Pokud kliknete pravým tlačítkem **zařízení** uzlu **oboru** podokně můžete vybrat z následujících akcí:

* Přidat nebo nahradit zařízení
* Připojení zařízení a ověřte importy
* Aktualizovat připojená zařízení

Pokud kliknete **zařízení** uzel a potom klikněte pravým tlačítkem na zařízení, název v **výsledky** podokně můžete vybrat z následujících akcí:

* Ověřování zařízení
* Zobrazení podrobností o zařízení
* Aktualizace zařízení
* Odstranění konfigurace zařízení
* Změnit heslo zařízení

> [!NOTE]
> Všechny tyto akce jsou také k dispozici v **akce** podokně.


Tento kurz vysvětluje, jak používat StorSimple Snapshot Manageru k připojení a správa zařízení a provádět následující úlohy:

* Přidat nebo nahradit zařízení
* Připojení zařízení a ověřte importy
* Aktualizovat připojená zařízení
* Ověřování zařízení
* Zobrazení podrobností o zařízení
* Aktualizace k jednotlivým zařízením
* Odstranění konfigurace zařízení
* Změnit heslo k zařízení s vypršenou platností
* Nahraďte zařízení se nezdařilo

> [!NOTE]
> Obecné informace o rozhraní StorSimple Snapshot Manageru najdete v části [uživatelské rozhraní StorSimple Snapshot Manageru](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Přidat nebo nahradit zařízení
Pomocí následujícího postupu a přidat nebo odebrat zařízení StorSimple.

#### <a name="to-add-or-replace-a-device"></a>A přidat nebo odebrat zařízení
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně klikněte pravým tlačítkem na **zařízení** uzlu a pak klikněte na tlačítko **nakonfigurovat zařízení**. **Nakonfigurovat zařízení** zobrazí se dialogové okno.
   
    ![Konfigurace zařízení StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. V **zařízení** rozevíracího seznamu vyberte IP adresu virtuálního zařízení na zařízení. 
4. V **heslo** textové pole, zadejte heslo Snapshot Manageru zařízení StorSimple, kterou jste vytvořili pro zařízení na portálu Azure classic. Klikněte na **OK**. StorSimple Snapshot Manageru vyhledá na zařízení, které jste určili. 
   
   * Pokud je zařízení k dispozici, přidá StorSimple Snapshot Manageru připojení.
   * Pokud zařízení není k dispozici z jakéhokoli důvodu, StorSimple Snapshot Manageru vrátí chybovou zprávu. Klikněte na tlačítko **OK** zavřete chybovou zprávu a pak klikněte na tlačítko **zrušit** zavřete **nakonfigurovat zařízení** dialogové okno.

## <a name="connect-a-device-and-verify-imports"></a>Připojení zařízení a ověřte importy
Pomocí následujícího postupu připojení zařízení StorSimple a ověřte, že se importují žádné existující skupiny svazků, které mají přidružené zálohy.

#### <a name="to-connect-a-device-and-verify-imports"></a>Připojení zařízení a ověřte importy
1. K připojení k systému StorSimple Snapshot Manageru zařízení, postupujte podle pokynů v přidat nebo nahradit zařízení. Po připojení k zařízení StorSimple Snapshot Manageru odpovídá následujícím způsobem:
   
   * Pokud zařízení není k dispozici z jakéhokoli důvodu, StorSimple Snapshot Manageru vrátí chybovou zprávu. 
   
   * Pokud je zařízení k dispozici, přidá StorSimple Snapshot Manageru připojení. Pokud vyberete zařízení, zobrazí se v **výsledky** podokno a stav pole označuje, že zařízení je **dostupné**. StorSimple Snapshot Manageru importuje všechny skupiny svazek nakonfigurovat pro zařízení, za předpokladu, že svazek skupiny mají přidružené zálohy. Zásady zálohování nebudou importovány. Nejsou naimportovány skupin svazků, které nemají přidružené zálohy.
2. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
3. Klikněte pravým tlačítkem na nejvyšší uzel v **oboru** podokně a pak klikněte na tlačítko **přepnout importy zobrazení**.
   
    ![Vyberte možnost přepnout importy zobrazení](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. **Přepnout importy zobrazení** se zobrazí dialogové okno, zobrazení stavu skupiny importované svazku a zálohování. Klikněte na **OK**.

Po skupin svazků a záloh jsou úspěšně importovány, můžete použít StorSimple Snapshot Manager ke správě, stejně jako by Správa skupin svazků a záloh, které vytvoříte a nakonfigurujete pomocí StorSimple Snapshot Manageru. 

## <a name="refresh-connected-devices"></a>Aktualizovat připojená zařízení
Použijte následující postup k synchronizaci připojených zařízení StorSimple ve StorSimple Snapshot Manageru.

#### <a name="to-refresh-connected-devices"></a>Chcete-li aktualizovat připojená zařízení
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně klikněte pravým tlačítkem na **zařízení**a potom klikněte na tlačítko **aktualizujte zařízení**. Tato funkce se synchronizuje připojených zařízení pomocí StorSimple Snapshot Manageru tak, aby se zobrazí skupiny svazku a zálohování, včetně všech nedávno přidanými funkcemi. 
   
    ![Aktualizace zařízení StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

**Aktualizujte zařízení** akce zkopíruje všechny nové skupiny svazek a všechny přidružené zálohy z připojených zařízení. Na rozdíl od **znovu prohledat svazky** akce, které jsou k dispozici pro **svazky** uzlu **aktualizujte zařízení** neobnoví zálohování registru.

## <a name="authenticate-a-device"></a>Ověřování zařízení
Pomocí následujícího postupu ověřit pomocí StorSimple Snapshot Manageru zařízení StorSimple.

#### <a name="to-authenticate-a-device"></a>K ověření zařízení
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně klikněte na tlačítko **zařízení**.
3. V **výsledky** podokně klikněte pravým tlačítkem na název zařízení a potom klikněte na tlačítko **ověřit**.
4. **Ověřit** zobrazí se dialogové okno. Zadejte heslo zařízení a potom klikněte na tlačítko **OK**.
   
    ![Ověření dialogové okno](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Zobrazení podrobností o zařízení
Následující postup použijte k zobrazení podrobností o zařízení StorSimple a v případě potřeby znovu synchronizovat zařízení pomocí StorSimple Snapshot Manageru.

#### <a name="to-view-and-resynchronize-device-details"></a>K zobrazení a Opětovná synchronizace podrobností o zařízení
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně klikněte na tlačítko **zařízení**.
3. V **výsledky** podokně klikněte pravým tlačítkem na název zařízení a potom klikněte na tlačítko **podrobnosti**.

4. tím **podrobnosti o zařízení** zobrazí se dialogové okno. Toto pole zobrazí název, modelu, verzi, sériové číslo, stav, cíl iSCSI kvalifikovaný název (IQN) a poslední synchronizace datum a čas.

* Klikněte na tlačítko **resynchronizace** synchronizovat zařízení.
* Klikněte na tlačítko **OK** nebo **zrušit** zavřete dialogové okno.
  
  ![Detaily zařízení](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Aktualizace k jednotlivým zařízením
Pomocí následujícího postupu opakovanou synchronizaci jednotlivých pomocí StorSimple Snapshot Manageru zařízení StorSimple.

#### <a name="to-refresh-a-device"></a>Aktualizace zařízení
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru. 
2. V **oboru** podokně klikněte na tlačítko **zařízení**. 
3. V **výsledky** podokně klikněte pravým tlačítkem na název zařízení a potom klikněte na tlačítko **aktualizujte zařízení**. Tato funkce se synchronizuje zařízení pomocí StorSimple Snapshot Manageru.

## <a name="delete-a-device-configuration"></a>Odstranění konfigurace zařízení
Pomocí následujícího postupu odstranit jednotlivé konfigurace zařízení StorSimple z portálu StorSimple Snapshot Manager.

#### <a name="to-delete-a-device-configuration"></a>Odstranění konfigurace zařízení
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně klikněte na tlačítko **zařízení**. 
3. V **výsledky** podokně klikněte pravým tlačítkem na název zařízení a potom klikněte na tlačítko **odstranit**. 
4. Zobrazí se následující zpráva. Klikněte na tlačítko **Ano** odstranit konfiguraci nebo klikněte na tlačítko **ne** odstranění lze zrušit.
   
    ![Odstranění konfigurace zařízení](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Změnit heslo k zařízení s vypršenou platností
Je nutné zadat heslo k ověření pomocí StorSimple Snapshot Manageru zařízení StorSimple. Nakonfigurujete toto heslo použijete rozhraní Windows PowerShell k nastavení zařízení. Však můžete ukončit platnost hesla. Pokud k tomu dojde, můžete heslo změnit na portálu Azure classic. Potom protože zařízení StorSimple Snapshot Manageru nenakonfigurovali, než vyprší platnost hesla, je třeba znovu ověřit zařízení StorSimple Snapshot Manageru.

#### <a name="to-change-the-expired-password"></a>Chcete-li změnit vypršet platnost jeho hesla
1. Na portálu Azure classic spusťte službu StorSimple Manager.
2. Klikněte na tlačítko **zařízení** > **konfigurovat** pro zařízení.
3. Posuňte se dolů k části StorSimple Snapshot Manageru. Zadejte heslo, které je 14 až 15 znaků. Ujistěte se, že heslo obsahuje kombinaci velká písmena, malá písmena, číselné a speciální znaky.
4. Znovu zadejte heslo, potvrďte ho.
5. V dolní části stránky klikněte na **Uložit**.

#### <a name="to-re-authenticate-the-device"></a>Opakované ověření zařízení
1. Spuštění Snapshot Manageru zařízení StorSimple.
2. V **oboru** podokně klikněte na tlačítko **zařízení**. Nakonfigurovaná zařízení objeví v **výsledky** podokně.
3. Vyberte zařízení, klikněte pravým tlačítkem a pak klikněte na tlačítko **ověřit**.
4. V **ověřit** okno, zadejte nové heslo.
5. Vyberte zařízení, klikněte pravým tlačítkem a vyberte **aktualizace zařízení**. Tato funkce se synchronizuje zařízení pomocí StorSimple Snapshot Manageru.

## <a name="replace-a-failed-device"></a>Nahraďte zařízení se nezdařilo
Pokud se zařízení StorSimple nepovede a nahrazuje zařízení pohotovostním režimu (převzetí služeb při selhání), použijte následující kroky pro připojení k nové zařízení a zobrazit přidružené zálohy.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Připojení k nové zařízení po převzetí služeb při selhání
1. Znovu nakonfigurujte připojení k iSCSI do nového zařízení. Pokyny najdete v části "krok 7: Připojení, inicializace a formátování svazků"v [nasazení zařízení StorSimple v místním](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Pokud nové zařízení StorSimple nemá stejnou IP adresu, jako měl ten starý, může být schopná připojit starou konfigurací.


1. Zastavte službu Microsoft StorSimple pro správu:
   
   1. Spusťte správce serveru.
   2. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   3. Na **služby** okna, vyberte **službě pro správu Microsoft StorSimple**.
   4. V pravém podokně v části **službě pro správu Microsoft StorSimple**, klikněte na tlačítko **zastavit službu**.
2. Odeberte informace o konfiguraci související se starým zařízením:
   
   1. V Průzkumníku souborů přejděte do C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Odstraňte soubory ve složce BACatalog.
3. Restartujte službu Microsoft StorSimple pro správu:
   
   1. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   2. Na **služby** okna, vyberte **službě pro správu Microsoft StorSimple**.
   3. V pravém podokně v části **službě pro správu Microsoft StorSimple**, klikněte na tlačítko **restartujte službu**.
4. Spuštění Snapshot Manageru zařízení StorSimple.
5. Pokud chcete nakonfigurovat nové zařízení StorSimple, proveďte kroky v kroku 2: Připojení zařízení StorSimple v [nasazení StorSimple Snapshot Manageru](storsimple-snapshot-manager-deployment.md).
6. Klikněte pravým tlačítkem na uzel nejvyšší úrovně v **oboru** podokno (StorSimple Snapshot Manageru v příkladu) a pak klikněte na tlačítko **přepnout importy zobrazení**. 
7. Zpráva se zobrazí, když jsou viditelné ve StorSimple Snapshot Manageru skupin importované svazků a záloh. Klikněte na **OK**.

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [použít ke správě vašeho řešení StorSimple Snapshot Manageru zařízení StorSimple](storsimple-snapshot-manager-admin.md).
* Zjistěte, jak [pomocí StorSimple Snapshot Manageru k zobrazení a správa svazků](storsimple-snapshot-manager-manage-volumes.md).

