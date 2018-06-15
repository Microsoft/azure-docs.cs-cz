---
title: Nasazení Snapshot Manager zařízení StorSimple | Microsoft Docs
description: Zjistěte, jak stáhnout a nainstalovat Snapshot Manager zařízení StorSimple, modul snap-in konzoly MMC pro správu funkce pro ochranu a zálohování dat StorSimple.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: cde355381b0d726a1ab340bc4230b2dc8f6e2c56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876317"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Nasazení modulu snap-in konzoly MMC Snapshot Manager zařízení StorSimple

## <a name="overview"></a>Přehled
Snapshot Manager zařízení StorSimple je modul snap-in konzoly Microsoft Management Console (MMC), který zjednodušuje ochrany dat a správu záloh v prostředí Microsoft Azure StorSimple. S StorSimple Snapshot Manager můžete spravovat Microsoft Azure StorSimple místního a cloudového úložiště, jako by šlo plně integrované úložiště systému, proto kvůli zjednodušení procesů zálohování a obnovení a snižuje tak náklady. 

Tento kurz popisuje požadavky na konfiguraci, také s postupy pro instalaci, odebrání a upgrade StorSimple Snapshot Manager.

> [!NOTE]
> * StorSimple Snapshot Manager nelze použít pro správu Microsoft Azure StorSimple virtuální pole (také označované jako StorSimple místní virtuální zařízení).
> * Pokud plánujete nainstalovat StorSimple Update 2 na zařízení StorSimple, je nutné stáhnout nejnovější verzi služby StorSimple Snapshot Manager a nainstalujte ji **před instalací StorSimple Update 2**. Nejnovější verzi služby StorSimple Snapshot Manager je zpětně kompatibilní a spolupracuje s všechny vydané verze Microsoft Azure StorSimple. Pokud používáte předchozí verze služby StorSimple Snapshot Manager, musíte aktualizovat (není nutné odinstalovat předchozí verzi před instalací nové verze).


## <a name="storsimple-snapshot-manager-installation"></a>Instalace Snapshot Manager zařízení StorSimple
StorSimple Snapshot Manager lze nainstalovat do počítačů se systémem Windows Server 2008 R2 SP1, Windows Server 2012 nebo operačního systému Windows Server 2012 R2. Na serverech se systémem Windows 2008 R2 musíte také nainstalovat systém Windows Server 2008 SP1 a Windows Management Framework 3.0.

Před instalací nebo upgradem modulu snap-in Snapshot Manager zařízení StorSimple pro Microsoft Management Console (MMC), ujistěte se, zda jsou správně nakonfigurovány pro zařízení a hostitele server Microsoft Azure StorSimple.

## <a name="configure-prerequisites"></a>Konfigurace požadavků
V následujících krocích najdete přehled úlohy konfigurace, které je třeba provést před instalací StorSimple Snapshot Manager. Kompletní konfigurace Microsoft Azure StorSimple a informace o instalaci, včetně požadavky na systém a podrobné pokyny najdete v tématu [nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Než začnete, zkontrolujte [kontrolní seznam konfigurace nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) a a [požadavky nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) v [nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Před instalací Snapshot Manager zařízení StorSimple
1. – Vybalení, připojení a připojení zařízení Microsoft Azure StorSimple, jak je popsáno v [instalace zařízení StorSimple 8100](storsimple-8100-hardware-installation.md) nebo [instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Ověřte, že hostitelský počítač je spuštěný jeden z následujících operačních systémů:
   
   * Windows Server 2008 R2 (na servery se systémem Windows 2008 R2, musíte také nainstalovat systém Windows Server 2008 SP1 a Windows Management Framework 3.0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     Hostitel pro virtuální zařízení StorSimple, musí být virtuální počítač Microsoft Azure.
3. Ujistěte se, že jsou splněny všechny požadavky konfigurace Microsoft Azure StorSimple. Podrobnosti, přejděte na [požadavky nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Připojit zařízení k hostiteli a provedení počáteční konfigurace. Podrobnosti, přejděte na [kroky nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Vytvářet svazky na zařízení, je přiřadit ke hostitele a ověřte, zda hostitel může připojit a používat. StorSimple Snapshot Manager podporuje následující typy svazků:
   
   * Základní svazky
   * Jednoduché svazky
   * Dynamické svazky
   * Zrcadlené dynamické svazky (RAID 1)
   * Sdílených svazků clusteru
     
     Informace o vytváření svazků v zařízení StorSimple nebo virtuální zařízení StorSimple, přejděte na [krok 6: vytvoření svazku](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)v [nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Instalace nové Snapshot Manager zařízení StorSimple
Před instalací Snapshot Manager zařízení StorSimple, ujistěte se, že jsou svazky, které jste vytvořili v zařízení StorSimple nebo virtuální zařízení StorSimple připojené, inicializovat a naformátován, jak je popsáno v [konfigurace požadavků](#configure-prerequisites).

> [!IMPORTANT]
> * Hostitel pro virtuální zařízení StorSimple, musí být virtuální počítač Microsoft Azure. 
> * Hostitel musí používat Windows 2008 R2, Windows Server 2012 nebo Windows Server 2012 R2. Pokud váš server se systémem Windows Server 2008 R2, musíte také nainstalovat Windows Server 2008 SP1 a Windows Management Framework 3.0.
> * Připojení k iSCSI na hostiteli a zařízení StorSimple je nutné nakonfigurovat, než se zařízení může připojit k systému StorSimple Snapshot Manager.

Postupujte podle následujících kroků dokončete novou instalací služby StorSimple Snapshot Manager. Pokud instalujete upgrade, přejděte na [upgradovat nebo přeinstalovat StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Krok 1: Instalace zařízení StorSimple Snapshot Manager 
* Krok 2: Připojení k zařízení StorSimple Snapshot Manager 
* Krok 3: Ověření připojení k zařízení 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Krok 1: Instalace zařízení StorSimple Snapshot Manager
Pomocí následujících kroků k instalaci StorSimple Snapshot Manager.

#### <a name="to-install-storsimple-snapshot-manager"></a>Chcete-li nainstalovat Snapshot Manager zařízení StorSimple
1. Stáhněte si software Snapshot Manager zařízení StorSimple (přejděte na [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) na webu Microsoft Download Center) a uložte místně na hostiteli.
2. V Průzkumníku souborů klikněte pravým tlačítkem na komprimovanou složku a pak klikněte na tlačítko **extrahujte všechny**.
3. V **extrahujte komprimované (Zipped) složky** okno v **vyberte cíl a extrahujte soubory** zadejte nebo vyhledejte cestu, kam chcete soubor k extrakci.
   
    > [!IMPORTANT]
    > Snapshot Manager zařízení StorSimple je nutné nainstalovat na jednotce C:.
    
4. Vyberte **zobrazit rozbalené soubory po dokončení** zaškrtněte políčko a potom klikněte na **extrahovat**.
   
    ![Extrahujte soubory](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Po dokončení extrahování otevře cílovou složku. Dvakrát klikněte na ikonu instalační program aplikace, která se zobrazí v cílové složce.
6. Když **instalační program úspěšně** se zobrazí zpráva, klikněte na tlačítko **Zavřít**. Měli byste vidět StorSimple Snapshot Manager ikony na ploše.
   
    ![ikony na ploše](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Krok 2: Připojení k zařízení StorSimple Snapshot Manager
Pomocí následujících kroků pro připojení k zařízení StorSimple Snapshot Manager zařízení StorSimple.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Pro připojení k zařízení StorSimple Snapshot Manager
1. Klikněte na ikonu Snapshot Manager zařízení StorSimple na ploše. Zobrazí se okno StorSimple Snapshot Manager. Okno obsahuje **oboru** podokně **výsledky** podokně a **akce** podokně. 
   
    ![Uživatelské rozhraní Snapshot Manager zařízení StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * **Oboru** podokně (levé podokno) obsahuje seznam uzlů, které jsou uspořádány do stromové struktury. Můžete rozbalit uzly a vyberte zobrazení nebo konkrétní data související s Tento uzel. Kliknutím na ikonu šipky rozbalit nebo Sbalit uzel. Klikněte pravým tlačítkem na položku v **oboru** podokno zobrazíte seznam dostupných akcí pro tuto položku.
   * **Výsledky** podokně (prostřední podokno) obsahuje podrobné informace o uzlu, zobrazení nebo data, která jste vybrali v stavu **oboru** podokně.
   * **Akce** uvádí operace, které můžete provádět na uzlu, zobrazení nebo data, která jste vybrali v podokně **oboru** podokně.
     
     Úplný popis uživatelské rozhraní Snapshot Manager zařízení StorSimple, najdete v části [StorSimple Snapshot Manager uživatelské rozhraní](storsimple-use-snapshot-manager.md).
2. V **oboru** podokně klikněte pravým tlačítkem myši **zařízení** uzel a pak klikněte na tlačítko **nakonfigurovat zařízení**. **Nakonfigurovat zařízení** zobrazí se dialogové okno.
   
    ![Konfigurace zařízení](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. V **zařízení** vyberte IP adresu zařízení Microsoft Azure StorSimple nebo virtuální zařízení. V **heslo** textové pole, zadejte heslo Snapshot Manager zařízení StorSimple, kterou jste vytvořili pro zařízení na portálu Azure. Klikněte na **OK**.
4. StorSimple Snapshot Manager hledá zařízení, které jste určili. Pokud zařízení není k dispozici, StorSimple Snapshot Manager přidá připojení. Můžete [ověřit připojení k zařízení](#to-verify-the-connection) potvrďte, že připojení byl úspěšně přidán.
   
    Pokud zařízení není k dispozici z jakéhokoli důvodu, StorSimple Snapshot Manager vrátí chybovou zprávu. Klikněte na tlačítko **OK** zavřete chybovou zprávu, a pak klikněte na **zrušit** zavřete **nakonfigurovat zařízení** dialogové okno.
5. Při připojení zařízení StorSimple Snapshot Manager importuje každý svazek skupiny nakonfigurované pro toto zařízení za předpokladu, že svazek skupiny přidruženy zálohy. Svazek skupin, které nemají přidružených záloh nebyly naimportovány. Kromě toho zásady zálohování, které byly vytvořeny pro skupinu svazku nebyly naimportovány. Zobrazení importované skupin, klikněte pravým tlačítkem na nejvyšší úrovni **svazku skupiny** uzlu v **oboru** panelu a klikněte na tlačítko **přepnutí importovaných skupin**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Krok 3: Ověření připojení k zařízení
Pomocí následujících kroků ověřte, zda je připojen StorSimple Snapshot Manager zařízení StorSimple.

#### <a name="to-verify-the-connection"></a>Chcete-li ověřit připojení
1. V **oboru** podokně klikněte **zařízení** uzlu.
   
    ![Stav zařízení StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Zkontrolujte **výsledky** podokně: 
   
   * Pokud se zobrazí na zelenou ukazatel na ikonu zařízení a **dostupné** se zobrazí v **stav** sloupec a potom zařízení je připojen. 
   * Pokud se zobrazí červený ukazatele na ikonu zařízení a není k dispozici v **stav** sloupec a potom zařízení není připojen. 
   * Pokud **aktualizace** se zobrazí v **stav** sloupec a potom StorSimple Snapshot Manager načítá skupiny svazku a přidružených záloh pro připojené zařízení.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Upgradovat nebo přeinstalovat Snapshot Manager zařízení StorSimple
StorSimple Snapshot Manager byste měli odinstalovat úplně upgradovat nebo přeinstalovat software. 

Před opětovnou instalací Snapshot Manager zařízení StorSimple, zálohujte existující databázi Snapshot Manager zařízení StorSimple na hostitelském počítači. To umožňuje ušetřit informace o zálohování zásad a konfigurace tak, aby tato data můžete snadno obnovit ze zálohy.

Pokud se upgrade nebo opětovné instalaci Snapshot Manager zařízení StorSimple, postupujte takto:

* Krok 1: Odinstalujte Snapshot Manager zařízení StorSimple 
* Krok 2: Vytvořte zálohu databáze Snapshot Manager zařízení StorSimple 
* Krok 3: Přeinstalujte Snapshot Manager zařízení StorSimple a obnovení databáze 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Krok 1: Odinstalujte Snapshot Manager zařízení StorSimple
K odinstalaci StorSimple Snapshot Manager použijte následující postup.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Chcete-li odinstalovat Snapshot Manager zařízení StorSimple
1. Otevřete na hostitelském počítači, **ovládací panely**, klikněte na tlačítko **programy**a potom klikněte na **programy a funkce**.
2. V levém podokně klikněte na **odinstalovat nebo změnit program**.
3. Klikněte pravým tlačítkem na **StorSimple Snapshot Manager**a potom klikněte na **odinstalovat**.
4. Spustí se StorSimple Snapshot Manager instalační program. Klikněte na tlačítko **upravit nastavení**a potom klikněte na **odinstalovat**.
   
   > [!NOTE]
   > Pokud neexistují žádné procesy konzoly MMC, spuštěná na pozadí, třeba Snapshot Manager zařízení StorSimple nebo Správa disků, odinstalace selže a zobrazí se zpráva, zavřete všechny instance konzoly MMC před dalším pokusem o odinstalovat program. Vyberte **automaticky ukončit aplikace a pokusit se je restartovat po dokončení instalace**a potom klikněte na **OK**.
   > 
   > 
5. Po dokončení procesu odinstalace **instalační program úspěšně** se zobrazí zpráva. Klikněte na **Zavřít**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Krok 2: Vytvořte zálohu databáze Snapshot Manager zařízení StorSimple
Pomocí následujících kroků vytvořte a uložte kopii databáze StorSimple Snapshot Manager.

#### <a name="to-back-up-the-database"></a>K zálohování databáze
1. Zastavte službu Microsoft StorSimple správy:
   
   1. Spusťte správce serveru.
   2. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   3. Na **služby** vyberte **služba správy zařízení StorSimple Microsoft**.
   4. V pravém podokně v části **služba správy zařízení StorSimple Microsoft**, klikněte na tlačítko **zastavit službu**.
      
        ![Zastavte službu StorSimple Manager zařízení](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Přejděte do C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData je skrytá složka.
  
3. Najít soubor XML katalogu, zkopírujte soubor a uložení kopie do bezpečného umístění nebo v cloudu.
   
    ![Soubor zálohy katalogu StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Restartujte službu Microsoft StorSimple Management: 
   
   1. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   2. Na **služby** vyberte **Microsoft StorSimple správu Statistika**e.
   3. V pravém podokně v části **služba správy zařízení StorSimple Microsoft**, klikněte na tlačítko **restartujte službu**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Krok 3: Přeinstalujte Snapshot Manager zařízení StorSimple a obnovení databáze
Přeinstalujte Snapshot Manager zařízení StorSimple, postupujte podle kroků v [nainstalovat nový StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Pak použijte následující postup k obnovení databáze StorSimple Snapshot Manager.

#### <a name="to-restore-the-database"></a>Obnovení databáze
1. Zastavte službu Microsoft StorSimple správy:
   
   1. Spusťte správce serveru.
   2. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   3. Na **služby** vyberte **služba správy zařízení StorSimple Microsoft**.
   4. V pravém podokně v části **služba správy zařízení StorSimple Microsoft**, klikněte na tlačítko **zastavit službu**.
2. Přejděte do C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > ProgramData je skrytá složka.
   > 
   > 
3. Odstraňte soubor XML katalogu a nahraďte ji na verzi, který jste předtím uložili.
4. Restartujte službu Microsoft StorSimple Management: 
   
   1. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   2. Na **služby** vyberte **služba správy zařízení StorSimple Microsoft**.
   3. V pravém podokně v části **služba správy zařízení StorSimple Microsoft**, klikněte na tlačítko **restartujte službu**.

## <a name="next-steps"></a>Další kroky
* Další informace o Snapshot Manager zařízení StorSimple, přejděte na [co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Další informace o uživatelském rozhraní Snapshot Manager zařízení StorSimple, přejděte na [StorSimple Snapshot Manager uživatelské rozhraní](storsimple-use-snapshot-manager.md).
* Další informace o používání Snapshot Manager zařízení StorSimple, přejděte na [použití StorSimple Snapshot Manager ke správě vašeho řešení StorSimple](storsimple-snapshot-manager-admin.md).

