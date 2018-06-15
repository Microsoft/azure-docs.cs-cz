---
title: Spravovat zařízení s Snapshot Manager zařízení StorSimple | Microsoft Docs
description: Popisuje způsob použití modulu snap-in konzoly MMC StorSimple Snapshot Manager k připojení a správě zařízení StorSimple.
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
ms.openlocfilehash: f5e3186a4271e0be781f367fa75ada195c58c960
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876807"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Použít k připojení a správě zařízení StorSimple Snapshot Manager zařízení StorSimple
## <a name="overview"></a>Přehled
Uzly můžete použít v Snapshot Manager zařízení StorSimple **oboru** podokně ověřte importovaných dat zařízení StorSimple a aktualizujte připojené paměťové zařízení. Kromě toho, když kliknete **zařízení** uzlu, zobrazí se seznam připojených zařízení a odpovídající informace o stavu v **výsledky** podokně.

![Připojená zařízení](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Obrázek 1: StorSimple Snapshot Manager připojeného zařízení** 

V závislosti na vaší **zobrazení** výběr **výsledky** podokně se zobrazují následující informace o každé zařízení. (Další informace o konfiguraci zobrazení, přejděte na [nabídka Zobrazit](storsimple-use-snapshot-manager.md#view-menu).

| Sloupec výsledků | Popis |
|:--- |:--- |
| Name (Název) |Název zařízení podle konfigurace v portálu Azure classic |
| Model |Číslo modelu zařízení |
| Verze |Verze softwaru na zařízení nainstalovaný. |
| Status |Zda je k dispozici zařízení |
| Poslední synchronizace proběhla |Datum a čas, kdy zařízení poslední synchronizace |
| Sériové číslo. |Sériové číslo zařízení |

Pokud kliknete pravým tlačítkem na **zařízení** uzlu **oboru** podokně, můžete vybrat z následujících akcí:

* Přidat nebo nahradit zařízení
* Připojte zařízení a ověřte importy
* Aktualizujte připojená zařízení

Pokud kliknete **zařízení** uzel a potom klikněte pravým tlačítkem na zařízení názvu **výsledky** podokně, můžete vybrat z následujících akcí:

* Ověřování zařízení
* Zobrazení podrobností o zařízeních
* Aktualizace zařízení
* Odstranění konfigurace zařízení
* Změnit heslo k zařízení

> [!NOTE]
> Všechny tyto akce jsou také k dispozici v **akce** podokně.


Tento kurz vysvětluje, jak použít StorSimple Snapshot Manager k připojení a správě zařízení a provádět následující úlohy:

* Přidat nebo nahradit zařízení
* Připojte zařízení a ověřte importy
* Aktualizujte připojená zařízení
* Ověřování zařízení
* Zobrazení podrobností o zařízeních
* Aktualizace k jednotlivým zařízením
* Odstranění konfigurace zařízení
* Změnit heslo k zařízení s vypršenou platností
* Nahraďte zařízení se nezdařilo

> [!NOTE]
> Obecné informace o používání rozhraní Snapshot Manager zařízení StorSimple, přejděte na [StorSimple Snapshot Manager uživatelské rozhraní](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Přidat nebo nahradit zařízení
Pomocí následujícího postupu můžete přidat nebo nahradit zařízení StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Chcete přidat nebo nahradit zařízení
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně klikněte pravým tlačítkem myši **zařízení** uzel a pak klikněte na tlačítko **nakonfigurovat zařízení**. **Nakonfigurovat zařízení** zobrazí se dialogové okno.
   
    ![Konfigurace zařízení StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. V **zařízení** rozevíracího seznamu vyberte adresu IP virtuálního zařízení nebo zařízení. 
4. V **heslo** textové pole, zadejte heslo Snapshot Manager zařízení StorSimple, kterou jste vytvořili pro zařízení na portálu Azure classic. Klikněte na **OK**. StorSimple Snapshot Manager hledá zařízení, které jste určili. 
   
   * Pokud zařízení není k dispozici, StorSimple Snapshot Manager přidá připojení.
   * Pokud zařízení není k dispozici z jakéhokoli důvodu, StorSimple Snapshot Manager vrátí chybovou zprávu. Klikněte na tlačítko **OK** zavřete chybovou zprávu, a pak klikněte na **zrušit** zavřete **nakonfigurovat zařízení** dialogové okno.

## <a name="connect-a-device-and-verify-imports"></a>Připojte zařízení a ověřte importy
Následující postup použijte k připojení zařízení StorSimple a ověřit, zda jsou naimportovány jakékoli existující svazek skupiny, které mají přidružené zálohy.

#### <a name="to-connect-a-device-and-verify-imports"></a>K připojení zařízení a ověřte importy
1. K připojení k systému StorSimple Snapshot Manager zařízení, postupujte podle pokynů v přidat nebo nahradit zařízení. Při připojení zařízení StorSimple Snapshot Manager zareaguje takto:
   
   * Pokud zařízení není k dispozici z jakéhokoli důvodu, StorSimple Snapshot Manager vrátí chybovou zprávu. 
   
   * Pokud zařízení není k dispozici, StorSimple Snapshot Manager přidá připojení. Pokud vyberete zařízení, zobrazí se v **výsledky** podokno a stav pole označuje, že zařízení je **dostupné**. StorSimple Snapshot Manager importuje všechny svazku skupiny nakonfigurované pro zařízení, za předpokladu, že svazek skupiny mají přidružené zálohy. Zásady zálohování nebyly naimportovány. Svazek skupin, které nemají přidružených záloh nebyly naimportovány.
2. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
3. Klikněte pravým tlačítkem myši na nejvyšší uzel v **oboru** podokně a pak klikněte na tlačítko **přepnout importů zobrazení**.
   
    ![Vyberte možnost přepnout importů zobrazení](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. **Přepnout importů zobrazení** se zobrazí dialogové okno, zobrazením stavu skupiny importované svazku a zálohování. Klikněte na **OK**.

Jakmile skupiny svazku a zálohování jsou úspěšně importovány, můžete použít StorSimple Snapshot Manager spravovat, stejně, jako by spravovat skupiny svazku a zálohování, které vytvoříte a nakonfigurujete s StorSimple Snapshot Manager. 

## <a name="refresh-connected-devices"></a>Aktualizujte připojená zařízení
Použijte následující postup k synchronizaci s Snapshot Manager zařízení StorSimple připojené zařízení StorSimple.

#### <a name="to-refresh-connected-devices"></a>K aktualizaci připojeného zařízení
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně klikněte pravým tlačítkem na **zařízení**a potom klikněte na **aktualizace zařízení**. Provádí synchronizaci připojená zařízení s StorSimple Snapshot Manager tak, aby můžete zobrazit skupiny svazku a zálohování, včetně všech nedávno přidány. 
   
    ![Aktualizace zařízení StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

**Aktualizace zařízení** akce načte všechny nové skupiny svazek a všechny přidružené zálohy z připojených zařízení. Na rozdíl od **Prohledat znovu svazky** akce, které jsou k dispozici pro **svazky** uzlu, **aktualizujte zařízení** neobnoví zálohování registru.

## <a name="authenticate-a-device"></a>Ověřování zařízení
Použijte následující postup k ověření s StorSimple Snapshot Manager zařízení StorSimple.

#### <a name="to-authenticate-a-device"></a>K ověření zařízení
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně klikněte na tlačítko **zařízení**.
3. V **výsledky** podokně klikněte pravým tlačítkem na název zařízení a pak klikněte na tlačítko **ověřit**.
4. **Ověřit** zobrazí se dialogové okno. Zadejte heslo k zařízení a pak klikněte na tlačítko **OK**.
   
    ![Ověření dialogové okno](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Zobrazení podrobností o zařízeních
Následující postup použijte k zobrazení podrobností o zařízení StorSimple a v případě potřeby znovu spusťte synchronizaci s StorSimple Snapshot Manager zařízení.

#### <a name="to-view-and-resynchronize-device-details"></a>K zobrazení a Opětovná synchronizace podrobnosti o zařízení
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně klikněte na tlačítko **zařízení**.
3. V **výsledky** podokně klikněte pravým tlačítkem na název zařízení a pak klikněte na tlačítko **podrobnosti**.

4 na **podrobnosti o zařízení** zobrazí se dialogové okno. Toto pole ukazuje název, modelu, verzi, sériové číslo, stav, cíl iSCSI kvalifikovaný název IQN () a poslední synchronizace datum a čas.

* Klikněte na tlačítko **nové synchronizace** synchronizovat zařízení.
* Klikněte na tlačítko **OK** nebo **zrušit** zavřete dialogové okno.
  
  ![Podrobnosti o zařízení](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Aktualizace k jednotlivým zařízením
Opětovná synchronizace jednotlivých s StorSimple Snapshot Manager zařízení StorSimple, použijte následující postup.

#### <a name="to-refresh-a-device"></a>Aktualizace zařízení
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager. 
2. V **oboru** podokně klikněte na tlačítko **zařízení**. 
3. V **výsledky** podokně klikněte pravým tlačítkem na název zařízení a pak klikněte na tlačítko **aktualizace zařízení**. Provádí synchronizaci zařízení s StorSimple Snapshot Manager.

## <a name="delete-a-device-configuration"></a>Odstranění konfigurace zařízení
Pomocí následujícího postupu můžete odstranit z Snapshot Manager zařízení StorSimple jednotlivé konfigurace zařízení StorSimple.

#### <a name="to-delete-a-device-configuration"></a>Chcete-li odstranit konfigurace zařízení
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně klikněte na tlačítko **zařízení**. 
3. V **výsledky** podokně klikněte pravým tlačítkem na název zařízení a pak klikněte na tlačítko **odstranit**. 
4. Zobrazí se následující zpráva. Klikněte na tlačítko **Ano** odstranit konfiguraci nebo kliknutím na tlačítko **ne** následované.
   
    ![Odstranit konfigurace zařízení](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Změnit heslo k zařízení s vypršenou platností
Musíte zadat heslo k ověření s StorSimple Snapshot Manager zařízení StorSimple. Konfigurujete toto heslo použít rozhraní Windows PowerShell k nastavení zařízení. Však můžete vypršení platnosti hesla. Pokud k tomu dojde, můžete změnit heslo portálu Azure classic. Pak, protože zařízení byl nakonfigurován v Snapshot Manager zařízení StorSimple, než vyprší platnost hesla, je třeba znovu ověřit zařízení ve Snapshot Manageru zařízení StorSimple.

#### <a name="to-change-the-expired-password"></a>Chcete-li změnit vypršela platnost hesla
1. Na portálu Azure classic spusťte službu StorSimple Manager.
2. Klikněte na tlačítko **zařízení** > **konfigurace** pro zařízení.
3. Přejděte do části StorSimple Snapshot Manager. Zadejte heslo, které je 14 až 15 znaků. Ujistěte se, zda heslo obsahuje směs velká písmena, malá písmena, číselné a speciální znaky.
4. Znovu zadejte heslo a potvrďte ho.
5. V dolní části stránky klikněte na **Uložit**.

#### <a name="to-re-authenticate-the-device"></a>Opakované ověření zařízení
1. Spusťte Snapshot Manager zařízení StorSimple.
2. V **oboru** podokně klikněte na tlačítko **zařízení**. Seznam zařízení, nakonfigurovaného se zobrazí v **výsledky** podokně.
3. Vyberte zařízení, klikněte pravým tlačítkem a pak klikněte na tlačítko **ověřit**.
4. V **ověřit** okno, zadejte nové heslo.
5. Vyberte zařízení, klikněte pravým tlačítkem a vyberte **aktualizace zařízení**. Provádí synchronizaci zařízení s StorSimple Snapshot Manager.

## <a name="replace-a-failed-device"></a>Nahraďte zařízení se nezdařilo
Pokud zařízení StorSimple selže a je nahrazena zařízení úsporném režimu (převzetí služeb při selhání), použijte následující postup k připojení k nové zařízení a zobrazení přidružených záloh.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Pro připojení k nové zařízení po převzetí služeb při selhání
1. Překonfigurujte iSCSI připojení k nové zařízení. Pokyny najdete v tématu "krok 7: připojení, inicializace a formát svazek" v [nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Pokud nové zařízení StorSimple má stejnou IP adresu jako ten starý, může být schopni připojit původní konfiguraci.


1. Zastavte službu Microsoft StorSimple správy:
   
   1. Spusťte správce serveru.
   2. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   3. Na **služby** vyberte **služba správy zařízení StorSimple Microsoft**.
   4. V pravém podokně v části **služba správy zařízení StorSimple Microsoft**, klikněte na tlačítko **zastavit službu**.
2. Odeberte informace o konfiguraci související s původním zařízení:
   
   1. V Průzkumníku souborů přejděte do C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Odstraňte soubory ve složce BACatalog.
3. Restartujte službu Microsoft StorSimple Management:
   
   1. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   2. Na **služby** vyberte **služba správy zařízení StorSimple Microsoft**.
   3. V pravém podokně v části **služba správy zařízení StorSimple Microsoft**, klikněte na tlačítko **restartujte službu**.
4. Spusťte Snapshot Manager zařízení StorSimple.
5. Pokud chcete konfigurovat nové zařízení StorSimple, proveďte kroky v kroku 2: připojení zařízení StorSimple v [nasazení zařízení StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
6. Klikněte pravým tlačítkem na uzel na nejvyšší úrovni v **oboru** panelu (Snapshot Manager zařízení StorSimple v příkladu) a pak klikněte na tlačítko **přepnout importů zobrazení**. 
7. Zpráva se zobrazí, když se zobrazí v zařízení StorSimple Snapshot Manager skupiny importované svazku a zálohování. Klikněte na **OK**.

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [použít ke správě vašeho řešení StorSimple Snapshot Manager zařízení StorSimple](storsimple-snapshot-manager-admin.md).
* Zjistěte, jak [pomocí StorSimple Snapshot Manager můžete zobrazit a spravovat svazky](storsimple-snapshot-manager-manage-volumes.md).

