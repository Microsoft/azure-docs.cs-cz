---
title: Nasadit StorSimple Snapshot Manager | Microsoft Docs
description: Naučte se stahovat a instalovat StorSimple Snapshot Manager, modul snap-in konzoly MMC pro správu funkcí StorSimple a ochrany dat pro zálohování.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 5f94564c5e769d3cf0e0abbe92a309a1ee2117a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96003867"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Nasazení modulu snap-in StorSimple Snapshot Manager MMC

## <a name="overview"></a>Přehled
StorSimple Snapshot Manager je modul snap-in konzoly MMC (Microsoft Management Console), který zjednodušuje správu ochrany a zálohování dat ve Microsoft Azure StorSimplem prostředí. StorSimple Snapshot Manager můžete spravovat Microsoft Azure StorSimple místního a cloudového úložiště, jako by se jednalo o plně integrovaný systém úložiště, což zjednodušuje procesy zálohování a obnovení a snižuje náklady. 

Tento kurz popisuje požadavky na konfiguraci a také postupy pro instalaci, odebrání a upgrade StorSimple Snapshot Manager.

> [!NOTE]
> * Snapshot Manager StorSimple nemůžete použít ke správě Microsoft Azure StorSimple virtuálních polí (označují se také jako StorSimple místní virtuální zařízení).
> * Pokud plánujete nainstalovat StorSimple Update 2 na zařízení StorSimple, nezapomeňte si stáhnout nejnovější verzi StorSimple Snapshot Manager a nainstalovat ji **ještě před instalací StorSimple Update 2**. Nejnovější verze StorSimple Snapshot Manager je zpětně kompatibilní a funguje se všemi vydanými verzemi Microsoft Azure StorSimple. Pokud používáte předchozí verzi StorSimple Snapshot Manager, budete ji muset aktualizovat (před instalací nové verze ale nemusíte tuto předchozí verzi odinstalovat).


## <a name="storsimple-snapshot-manager-installation"></a>Instalace Snapshot Manager StorSimple
StorSimple Snapshot Manager lze nainstalovat do počítačů s operačním systémem Windows Server 2008 R2 SP1, Windows Server 2012 nebo Windows Server 2012 R2. Na serverech se systémem Windows 2008 R2 je také nutné nainstalovat systém Windows Server 2008 SP1 a rozhraní Windows Management Framework 3,0.

Před instalací nebo upgradem modulu snap-in StorSimple Snapshot Manager pro konzolu Microsoft Management Console (MMC) se ujistěte, že Microsoft Azure StorSimple zařízení a hostitelský server jsou správně nakonfigurované.

## <a name="configure-prerequisites"></a>Konfigurovat požadavky
Následující kroky obsahují podrobný přehled úloh konfigurace, které je nutné provést před instalací StorSimple Snapshot Manager. Kompletní informace o konfiguraci Microsoft Azure StorSimple a nastaveních, včetně požadavků na systém a podrobných pokynů, najdete v tématu [nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Než začnete, přečtěte si [Kontrolní seznam konfigurace nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) a [požadavky na nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) v tématu nasazení místního [zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Než nainstalujete StorSimple Snapshot Manager
1. Rozbalení, připojení a připojení zařízení Microsoft Azure StorSimple, jak je popsáno v tématu [instalace zařízení s StorSimple 8100](storsimple-8100-hardware-installation.md) nebo [instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Ujistěte se, že na hostitelském počítači běží jeden z následujících operačních systémů:
   
   * Windows Server 2008 R2 (na serverech se systémem Windows 2008 R2 je nutné nainstalovat také Windows Server 2008 SP1 a Windows Management Framework 3,0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     Pro virtuální zařízení StorSimple musí být hostitelem Microsoft Azure virtuální počítač.
3. Ujistěte se, že splňujete všechny požadavky na konfiguraci Microsoft Azure StorSimple. Podrobnosti najdete v článku [požadavky na nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Připojte zařízení k hostiteli a proveďte počáteční konfiguraci. Podrobnosti najdete v [postupu nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Vytvořte na zařízení svazky, přiřaďte je k hostiteli a ověřte, jestli je hostitel může připojit a použít. StorSimple Snapshot Manager podporuje následující typy svazků:
   
   * Základní svazky
   * Jednoduché svazky
   * Dynamické svazky
   * Zrcadlené dynamické svazky (RAID 1)
   * Sdílené svazky clusteru
     
     Informace o vytváření svazků na virtuálním zařízení s StorSimple nebo StorSimple, najdete v části [Krok 6: vytvoření svazku](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)v části [nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Nainstalovat novou Snapshot Manager StorSimple
Před instalací StorSimple Snapshot Manager zajistěte, aby byly svazky, které jste vytvořili na virtuálním zařízení StorSimple nebo StorSimple, připojené, inicializované a naformátované, jak je popsáno v tématu [Konfigurace požadavků](#configure-prerequisites).

> [!IMPORTANT]
> * Pro virtuální zařízení StorSimple musí být hostitelem Microsoft Azure virtuální počítač. 
> * V hostiteli musí být spuštěný systém Windows 2008 R2, Windows Server 2012 nebo Windows Server 2012 R2. Pokud na serveru běží systém Windows Server 2008 R2, je nutné také nainstalovat systém Windows Server 2008 SP1 a rozhraní Windows Management Framework 3,0.
> * Než budete moct zařízení připojit k StorSimple Snapshot Manager, musíte nakonfigurovat připojení iSCSI z hostitele na zařízení StorSimple.

Pomocí těchto kroků dokončete novou instalaci StorSimple Snapshot Manager. Pokud instalujete upgrade, Projděte si [upgrade nebo přeinstalujte StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Krok 1: instalace StorSimple Snapshot Manager 
* Krok 2: připojení Snapshot Manager StorSimple k zařízení 
* Krok 3: ověření připojení k zařízení 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Krok 1: instalace StorSimple Snapshot Manager
K instalaci StorSimple Snapshot Manager použijte následující postup.

#### <a name="to-install-storsimple-snapshot-manager"></a>Instalace StorSimple Snapshot Manager
1. Stáhněte si software StorSimple Snapshot Manager (na webu Microsoft Download Center klikněte na [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) ) a uložte ho místně na hostiteli.
2. V Průzkumníku souborů klikněte pravým tlačítkem na komprimovanou složku a pak klikněte na **Extrahovat vše**.
3. V okně **extrahování komprimovaných složek (metoda ZIP)** zadejte do pole **Vybrat cílové a extrahované soubory** nebo vyhledejte cestu, kam chcete soubor extrahovat.
   
    > [!IMPORTANT]
    > Na jednotce C: je třeba nainstalovat StorSimple Snapshot Manager.
    
4. Zaškrtněte políčko **zobrazit extrahované soubory po dokončení** a pak klikněte na **extrahovat**.
   
    ![Dialogové okno extrahování souborů](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Po dokončení extrakce se cílová složka otevře. Dvakrát klikněte na ikonu instalace aplikace, která se zobrazí v cílové složce.
6. Jakmile se zobrazí zpráva o **úspěšné instalaci** , klikněte na tlačítko **Zavřít**. Na ploše by se měla zobrazit ikona Snapshot Manager StorSimple.
   
    ![ikona desktopu](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Krok 2: připojení Snapshot Manager StorSimple k zařízení
K připojení StorSimple Snapshot Manager k zařízení StorSimple použijte následující postup.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Připojení Snapshot Manager StorSimple k zařízení
1. Klikněte na ikonu Snapshot Manager StorSimple na ploše. Zobrazí se okno StorSimple Snapshot Manager. Okno obsahuje podokno **oboru** , podokno **výsledků** a podokno **akcí** . 
   
    ![Uživatelské rozhraní StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * Podokno **Rozsah** (levé podokno) obsahuje seznam uzlů organizovaných ve stromové struktuře. Můžete rozbalit některé uzly a vybrat zobrazení nebo specifická data související s tímto uzlem. Kliknutím na ikonu se šipkou rozbalíte nebo sbalíte uzel. Kliknutím pravým tlačítkem myši na položku v podokně **Rozsah** zobrazíte seznam dostupných akcí pro tuto položku.
   * Podokno **výsledků** (prostřední podokno) obsahuje podrobné informace o stavu uzlu, zobrazení nebo dat, která jste vybrali v podokně **Rozsah** .
   * Podokno **Akce** obsahuje seznam operací, které můžete provádět na uzlu, zobrazení nebo datech, která jste vybrali v podokně **Rozsah** .
     
     Úplný popis Snapshot Manager uživatelského rozhraní StorSimple naleznete v tématu [StorSimple Snapshot Manager User Interface](storsimple-use-snapshot-manager.md).
2. V podokně **obor** klikněte pravým tlačítkem na uzel **zařízení** a pak klikněte na **Konfigurovat zařízení**. Zobrazí se dialogové okno **Konfigurace zařízení** .
   
    ![Konfigurace zařízení](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. V poli seznam **zařízení** vyberte IP adresu zařízení Microsoft Azure StorSimple nebo virtuálního zařízení. Do textového pole **heslo** zadejte StorSimple Snapshot Manager heslo, které jste vytvořili pro zařízení v Azure Portal. Klikněte na **OK**.
4. StorSimple Snapshot Manager vyhledá zařízení, které jste identifikovali. Pokud je zařízení k dispozici, StorSimple Snapshot Manager přidá připojení. Můžete [ověřit připojení k zařízení](#to-verify-the-connection) a potvrdit, že připojení bylo úspěšně přidáno.
   
    Pokud zařízení není z nějakého důvodu k dispozici, StorSimple Snapshot Manager vrátí chybovou zprávu. Kliknutím na tlačítko **OK** zavřete chybovou zprávu a kliknutím na tlačítko **Storno** zavřete dialogové okno **Konfigurace zařízení** .
5. Když se připojí k zařízení, StorSimple Snapshot Manager importuje každou skupinu svazků nakonfigurovanou pro toto zařízení, za předpokladu, že má skupina svazků přidružené zálohy. Skupiny svazků, které nemají přidružené zálohy, se neimportují. Kromě toho se neimportují zásady zálohování vytvořené pro skupinu svazků. Importované skupiny zobrazíte tak, že kliknete pravým tlačítkem na uzel nejvyšší **skupiny svazků** v podokně **Rozsah** a kliknete na **Přepnout importované skupiny**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Krok 3: ověření připojení k zařízení
Pomocí následujícího postupu ověřte, zda je StorSimple Snapshot Manager připojen k zařízení StorSimple.

#### <a name="to-verify-the-connection"></a>Ověření připojení
1. V podokně **obor** klikněte na uzel **zařízení** .
   
    ![StorSimple Snapshot Manager stav zařízení](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Ověřte podokno **výsledků** : 
   
   * Pokud se na ikoně zařízení objeví zelený indikátor a **k dispozici** je ve sloupci **stav** , je zařízení připojené. 
   * Pokud se ve sloupci **stav** zobrazí červený indikátor, který není dostupný, zařízení není připojené. 
   * Pokud se **aktualizace** zobrazí ve sloupci **stav** , pak StorSimple Snapshot Manager načítá skupiny svazků a přidružené zálohy pro připojené zařízení.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Upgradovat nebo přeinstalovat StorSimple Snapshot Manager
Snapshot Manager StorSimple je třeba před upgradem nebo přeinstalací softwaru úplně odinstalovat. 

Před opětovnou instalací StorSimple Snapshot Manager Zálohujte existující databázi StorSimple Snapshot Manager na hostitelském počítači. Tím se uloží zásady zálohování a konfigurační informace, abyste mohli tato data snadno obnovit ze zálohy.

Pokud upgradujete nebo přeinstalujete StorSimple Snapshot Manager, postupujte podle těchto kroků:

* Krok 1: Odinstalace StorSimple Snapshot Manager 
* Krok 2: zálohování databáze Snapshot Manager StorSimple 
* Krok 3: Přeinstalujte StorSimple Snapshot Manager a obnovte databázi. 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Krok 1: Odinstalace StorSimple Snapshot Manager
K odinstalaci StorSimple Snapshot Manager použijte následující postup.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Odinstalace StorSimple Snapshot Manager
1. V hostitelském počítači otevřete **Ovládací panely**, klikněte na **programy** a potom klikněte na **programy a funkce**.
2. V levém podokně klikněte na možnost **Odinstalovat nebo změnit program**.
3. Pravým tlačítkem myši klikněte na **StorSimple Snapshot Manager** a pak klikněte na **odinstalovat**.
4. Spustí se instalační program StorSimple Snapshot Manager. Klikněte na **Upravit nastavení** a pak klikněte na **odinstalovat**.
   
   > [!NOTE]
   > Pokud na pozadí běží nějaké procesy MMC, například StorSimple Snapshot Manager nebo Správa disků, odinstalace se nezdaří a zobrazí se zpráva, abyste zavřeli všechny instance konzoly MMC předtím, než se pokusíte odinstalovat program. Vyberte možnost **automaticky ukončit aplikace a pokusit se o jejich restartování po dokončení instalace** a pak klikněte na tlačítko **OK**.
   > 
   > 
5. Po dokončení procesu odinstalace se zobrazí zpráva o **úspěšné instalaci** . Klikněte na **Zavřít**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Krok 2: zálohování databáze Snapshot Manager StorSimple
Pomocí následujících kroků můžete vytvořit a uložit kopii databáze StorSimple Snapshot Manager.

#### <a name="to-back-up-the-database"></a>Zálohování databáze
1. Zastavte službu Microsoft StorSimple Management:
   
   1. Spusťte Správce serveru.
   2. Na řídicím panelu Správce serveru v nabídce **nástroje** vyberte **služby**.
   3. Na stránce **služby** vyberte **Služba Microsoft StorSimple Management Service**.
   4. V pravém podokně v části **Služba správy Microsoft StorSimple** klikněte na **Zastavit službu**.
      
        ![Zastavení služby StorSimple Správce zařízení](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Přejít na C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > Složka ProgramData je skrytá.
  
3. Vyhledejte soubor XML katalogu, zkopírujte soubor a uložte kopii v bezpečném umístění nebo v cloudu.
   
    ![Soubor katalogu StorSimple Backup](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Restartujte službu Microsoft StorSimple Management: 
   
   1. Na řídicím panelu Správce serveru v nabídce **nástroje** vyberte **služby**.
   2. Na stránce **služby** vyberte **službu Microsoft StorSimple Management Service**.
   3. V pravém podokně v části **Služba správy Microsoft StorSimple** klikněte na **restartovat službu**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Krok 3: Přeinstalujte StorSimple Snapshot Manager a obnovte databázi.
Pokud chcete přeinstalovat StorSimple Snapshot Manager, postupujte podle pokynů v části [instalace nového StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Potom pomocí následujícího postupu obnovte databázi StorSimple Snapshot Manager.

#### <a name="to-restore-the-database"></a>Obnovení databáze
1. Zastavte službu Microsoft StorSimple Management:
   
   1. Spusťte Správce serveru.
   2. Na řídicím panelu Správce serveru v nabídce **nástroje** vyberte **služby**.
   3. Na stránce **služby** vyberte **Služba Microsoft StorSimple Management Service**.
   4. V pravém podokně v části **Služba správy Microsoft StorSimple** klikněte na **Zastavit službu**.
2. Přejít na C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > Složka ProgramData je skrytá.
   > 
   > 
3. Odstraňte soubor XML katalogu a nahraďte ho verzí, kterou jste předtím uložili.
4. Restartujte službu Microsoft StorSimple Management: 
   
   1. Na řídicím panelu Správce serveru v nabídce **nástroje** vyberte **služby**.
   2. Na stránce **služby** vyberte **Služba Microsoft StorSimple Management Service**.
   3. V pravém podokně v části **Služba správy Microsoft StorSimple** klikněte na **restartovat službu**.

## <a name="next-steps"></a>Další kroky
* Pokud chcete získat další informace o Snapshot Manager StorSimple, podívejte se na to, [co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Další informace o uživatelském rozhraní StorSimple Snapshot Manager najdete v [Snapshot Manager uživatelském rozhraní StorSimple](storsimple-use-snapshot-manager.md).
* Další informace o používání StorSimple Snapshot Manager najdete v [Snapshot Manager ke správě řešení StorSimple pomocí StorSimple](storsimple-snapshot-manager-admin.md).

