---
title: Správa zařízení pomocí StorSimple Snapshot Manager | Microsoft Docs
description: Popisuje, jak pomocí modulu snap-in StorSimple Snapshot Manager konzoly MMC připojit a spravovat zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: dbb7e835d110bcb6cb81fd5425a2aafbb578e692
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022968"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Připojení a Správa zařízení StorSimple pomocí Snapshot Manager StorSimple
## <a name="overview"></a>Přehled
Pomocí uzlů v podokně StorSimple Snapshot Manager **Scope** můžete ověřit importovaná data zařízení StorSimple a aktualizovat připojená úložná zařízení. Pokud navíc kliknete na uzel **zařízení** , zobrazí se v podokně **výsledků** seznam připojených zařízení a odpovídající informace o stavu.

![Připojená zařízení](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Obrázek 1: StorSimple Snapshot Manager připojené zařízení** 

V závislosti na výběru **zobrazení** zobrazuje podokno **výsledků** následující informace o jednotlivých zařízeních. (Další informace o konfiguraci zobrazení naleznete v [nabídce Zobrazit](storsimple-use-snapshot-manager.md#view-menu).

| Sloupec výsledků | Popis |
|:--- |:--- |
| Název |Název zařízení, jak je nakonfigurované na portálu Azure Classic |
| Modelování |Číslo modelu zařízení |
| Verze |Verze softwaru nainstalovaného v zařízení |
| Status |Zda je zařízení k dispozici |
| Poslední synchronizace |Datum a čas poslední synchronizace zařízení |
| Sériové číslo. |Sériové číslo zařízení |

Pokud kliknete pravým tlačítkem myši na uzel **zařízení** v podokně **oboru** , můžete vybrat z následujících akcí:

* Přidání nebo nahrazení zařízení
* Připojit zařízení a ověřit import
* Aktualizovat připojená zařízení

Pokud kliknete na uzel **zařízení** a potom v podokně **výsledků** kliknete pravým tlačítkem myši na název zařízení, můžete vybrat z těchto akcí:

* Ověření zařízení
* Zobrazení podrobností o zařízení
* Aktualizace zařízení
* Odstranění konfigurace zařízení
* Změna hesla zařízení

> [!NOTE]
> Všechny tyto akce jsou také k dispozici v podokně **Akce** .


V tomto kurzu se dozvíte, jak pomocí Snapshot Manager StorSimple připojit a spravovat zařízení a provádět následující úlohy:

* Přidání nebo nahrazení zařízení
* Připojit zařízení a ověřit import
* Aktualizovat připojená zařízení
* Ověření zařízení
* Zobrazení podrobností o zařízení
* Aktualizace jednotlivých zařízení
* Odstranění konfigurace zařízení
* Změna hesla zařízení s vypršenou platností
* Výměna neúspěšného zařízení

> [!NOTE]
> Obecné informace o používání rozhraní StorSimple Snapshot Manager najdete v [Snapshot Manager uživatelském rozhraní StorSimple](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Přidání nebo nahrazení zařízení
K přidání nebo nahrazení zařízení StorSimple použijte následující postup.

#### <a name="to-add-or-replace-a-device"></a>Přidání nebo nahrazení zařízení
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** klikněte pravým tlačítkem na uzel **zařízení** a pak klikněte na **Konfigurovat zařízení**. Zobrazí se dialogové okno **Konfigurace zařízení** .
   
    ![Konfigurace zařízení StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. V rozevíracím seznamu **zařízení** vyberte IP adresu zařízení nebo virtuálního zařízení. 
4. Do textového pole **heslo** zadejte StorSimple Snapshot Manager heslo, které jste vytvořili pro zařízení na portálu Azure Classic. Klikněte na **OK**. StorSimple Snapshot Manager vyhledá zařízení, které jste identifikovali. 
   
   * Pokud je zařízení k dispozici, StorSimple Snapshot Manager přidá připojení.
   * Pokud zařízení není z nějakého důvodu k dispozici, StorSimple Snapshot Manager vrátí chybovou zprávu. Kliknutím na tlačítko **OK** zavřete chybovou zprávu a kliknutím na tlačítko **Storno** zavřete dialogové okno **Konfigurace zařízení** .

## <a name="connect-a-device-and-verify-imports"></a>Připojit zařízení a ověřit import
Pomocí následujícího postupu připojte zařízení StorSimple a ověřte, zda jsou importovány všechny existující skupiny svazků s přidruženými zálohami.

#### <a name="to-connect-a-device-and-verify-imports"></a>Připojení zařízení a ověření importu
1. Pokud chcete zařízení připojit k StorSimple Snapshot Manager, postupujte podle pokynů v části Přidání nebo nahrazení zařízení. Když se připojí k zařízení, StorSimple Snapshot Manager odpoví následujícím způsobem:
   
   * Pokud zařízení není z nějakého důvodu k dispozici, StorSimple Snapshot Manager vrátí chybovou zprávu. 
   
   * Pokud je zařízení k dispozici, StorSimple Snapshot Manager přidá připojení. Po výběru zařízení se zobrazí v podokně **výsledků** a pole stav indikuje, že je zařízení **k dispozici**. StorSimple Snapshot Manager importuje všechny skupiny svazků nakonfigurované pro zařízení za předpokladu, že mají skupiny svazků přidružené zálohy. Zásady zálohování se neimportují. Skupiny svazků, které nemají přidružené zálohy, se neimportují.
2. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
3. Pravým tlačítkem myši klikněte na nejvyšší uzel v podokně **Rozsah** a pak klikněte na tlačítko **Přepnout import zobrazení**.
   
    ![Vyberte možnost přepnout zobrazení importů.](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. Zobrazí se dialogové okno **Přepnout zobrazení importů** , ve kterém se zobrazuje stav importovaných skupin svazků a záloh. Klikněte na **OK**.

Po úspěšném naimportování skupin svazků a zálohování můžete k jejich správě použít Snapshot Manager StorSimple stejně, jako byste spravovali skupiny svazků a zálohy, které jste vytvořili a nakonfigurovali pomocí Snapshot Manager StorSimple. 

## <a name="refresh-connected-devices"></a>Aktualizovat připojená zařízení
Pomocí následujícího postupu synchronizujte připojená zařízení StorSimple s StorSimple Snapshot Manager.

#### <a name="to-refresh-connected-devices"></a>Aktualizace připojených zařízení
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** klikněte pravým tlačítkem na **zařízení** a pak klikněte na **aktualizovat zařízení**. Tím se synchronizují připojená zařízení s StorSimple Snapshot Manager, abyste mohli zobrazit skupiny svazků a zálohy včetně všech nedávných přídavků. 
   
    ![Aktualizace zařízení StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

Akce **aktualizovat zařízení** načte všechny nové skupiny svazků a všechny přidružené zálohy z připojených zařízení. Na rozdíl od dostupné akce **znovu prohledat svazky** pro uzel **svazky** obnovení **zařízení** neobnoví registr zálohování.

## <a name="authenticate-a-device"></a>Ověření zařízení
Pomocí následujícího postupu ověříte zařízení StorSimple pomocí nástroje StorSimple Snapshot Manager.

#### <a name="to-authenticate-a-device"></a>Ověření zařízení
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** klikněte na **zařízení**.
3. V podokně **výsledků** klikněte pravým tlačítkem na název zařízení a potom klikněte na **ověřit**.
4. Zobrazí se dialogové okno **ověřování** . Zadejte heslo zařízení a pak klikněte na **OK**.
   
    ![Dialogové okno ověřování](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Zobrazení podrobností o zařízení
Následující postup slouží k zobrazení podrobností o zařízení StorSimple a v případě potřeby k opětovné synchronizaci zařízení s StorSimple Snapshot Manager.

#### <a name="to-view-and-resynchronize-device-details"></a>Zobrazení a opětovná synchronizace podrobností o zařízení
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** klikněte na **zařízení**.
3. V podokně **výsledků** klikněte pravým tlačítkem na název zařízení a pak klikněte na **Podrobnosti**.

4. zobrazí se dialogové okno **Podrobnosti o zařízení** . V tomto poli se zobrazuje název, model, verze, sériové číslo, stav, cílový iSCSI kvalifikovaný název (IQN) a datum a čas poslední synchronizace.

* Kliknutím na znovu **synchronizovat** synchronizujte zařízení.
* Kliknutím na tlačítko **OK** nebo **Zrušit** zavřete dialogové okno.
  
  ![Podrobnosti o zařízení](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Aktualizace jednotlivých zařízení
Pomocí následujícího postupu můžete znovu synchronizovat jednotlivá zařízení StorSimple s StorSimple Snapshot Manager.

#### <a name="to-refresh-a-device"></a>Aktualizace zařízení
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager. 
2. V podokně **obor** klikněte na **zařízení**. 
3. V podokně **výsledků** klikněte pravým tlačítkem na název zařízení a pak klikněte na **aktualizovat zařízení**. Tím se zařízení synchronizuje s Snapshot Manager StorSimple.

## <a name="delete-a-device-configuration"></a>Odstranění konfigurace zařízení
Pomocí následujícího postupu můžete odstranit individuální konfiguraci zařízení StorSimple z StorSimple Snapshot Manager.

#### <a name="to-delete-a-device-configuration"></a>Odstranění konfigurace zařízení
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** klikněte na **zařízení**. 
3. V podokně **výsledků** klikněte pravým tlačítkem na název zařízení a pak klikněte na **Odstranit**. 
4. Zobrazí se následující zpráva. Kliknutím na **Ano** odstraníte konfiguraci, nebo kliknutím na **ne** zrušíte odstranění.
   
    ![Odstranění konfigurace zařízení](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Změna hesla zařízení s vypršenou platností
Abyste mohli ověřit zařízení StorSimple pomocí StorSimple Snapshot Manager, musíte zadat heslo. Toto heslo nakonfigurujete při nastavení zařízení pomocí rozhraní Windows PowerShell. Heslo ale může vypršet. Pokud k tomu dojde, můžete změnit heslo pomocí portálu Azure Classic. Poté, protože zařízení bylo nakonfigurováno v StorSimple Snapshot Manager před vypršením platnosti hesla, je nutné znovu ověřit zařízení v Snapshot Manager StorSimple.

#### <a name="to-change-the-expired-password"></a>Změna hesla s vypršenou platností
1. Na portálu Azure Classic spusťte službu StorSimple Manager.
2. Klikněte na **zařízení**  >  **Konfigurovat** pro zařízení.
3. Přejděte dolů do části Snapshot Manager StorSimple. Zadejte heslo, které je 14-15 znaků. Ujistěte se, že heslo obsahuje kombinaci velkých a malých písmen, číslic a speciálních znaků.
4. Znovu zadejte heslo a potvrďte ho.
5. V dolní části stránky klikněte na **Uložit**.

#### <a name="to-re-authenticate-the-device"></a>Opětovné ověření zařízení
1. Spusťte StorSimple Snapshot Manager.
2. V podokně **obor** klikněte na **zařízení**. V podokně **výsledků** se zobrazí seznam nakonfigurovaných zařízení.
3. Vyberte zařízení, klikněte pravým tlačítkem myši a potom klikněte na **ověřit**.
4. V okně **ověření** zadejte nové heslo.
5. Vyberte zařízení, klikněte pravým tlačítkem myši a vyberte **aktualizovat zařízení**. Tím se zařízení synchronizuje s Snapshot Manager StorSimple.

## <a name="replace-a-failed-device"></a>Výměna neúspěšného zařízení
Pokud zařízení StorSimple selhalo a nahrazuje ho pohotovostním zařízením (převzetí služeb při selhání), připojte se k novému zařízení a Prohlédněte si přidružené zálohy pomocí následujícího postupu.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Připojení k novému zařízení po převzetí služeb při selhání
1. Překonfigurujte připojení iSCSI k novému zařízení. Pokyny najdete v tématu "krok 7: připojení, inicializace a formátování svazku" v tématu nasazení místního [zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Pokud má nové zařízení StorSimple stejnou IP adresu jako starou, může být možné připojit starou konfiguraci.


1. Zastavte službu Microsoft StorSimple Management:
   
   1. Spusťte Správce serveru.
   2. Na řídicím panelu Správce serveru v nabídce **nástroje** vyberte **služby**.
   3. V okně **služby** vyberte **službu Microsoft StorSimple Management Service**.
   4. V pravém podokně v části **Služba správy Microsoft StorSimple** klikněte na **Zastavit službu**.
2. Odeberte konfigurační informace související se starým zařízením:
   
   1. V Průzkumníku souborů přejděte na C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Odstraňte soubory ve složce BACatalog.
3. Restartujte službu Microsoft StorSimple Management:
   
   1. Na řídicím panelu Správce serveru v nabídce **nástroje** vyberte **služby**.
   2. V okně **služby** vyberte **službu Microsoft StorSimple Management Service**.
   3. V pravém podokně v části **Služba správy Microsoft StorSimple** klikněte na **restartovat službu**.
4. Spusťte StorSimple Snapshot Manager.
5. Pokud chcete nakonfigurovat nové zařízení StorSimple, postupujte podle kroků v části Krok 2: připojení zařízení StorSimple v části [nasazení StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
6. Klikněte pravým tlačítkem myši na uzel nejvyšší úrovně v podokně **oboru** (StorSimple Snapshot Manager v příkladu) a pak klikněte na **Přepnout zobrazení importy**. 
7. Zpráva se zobrazí, když se importované skupiny svazků a zálohy zobrazí v StorSimple Snapshot Manager. Klikněte na **OK**.

## <a name="next-steps"></a>Další kroky
* Naučte se [používat Snapshot Manager StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Naučte se [používat Snapshot Manager StorSimple k zobrazení a správě svazků](storsimple-snapshot-manager-manage-volumes.md).

