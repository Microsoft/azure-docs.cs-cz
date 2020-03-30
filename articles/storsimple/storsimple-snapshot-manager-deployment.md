---
title: Nasazení správce snímků StorSimple | Dokumenty společnosti Microsoft
description: Přečtěte si, jak stáhnout a nainstalovat Správce snímků StorSimple, modul snap-in konzoly MMC pro správu funkcí ochrany dat a zálohování StorSimple.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 6d3e3d6cdf7a831bf09d9c4709c1a60d27683438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933383"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Nasazení modulu snap-in Správce snímků StorSimple MMC

## <a name="overview"></a>Přehled
Správce snímků StorSimple je modul snap-in konzoly MMC (Microsoft Management Console), který zjednodušuje ochranu dat a správu zálohování v prostředí Microsoft Azure StorSimple. Pomocí Správce snímků StorSimple můžete spravovat místní a cloudové úložiště Microsoft Azure StorSimple, jako by se jednalo o plně integrovaný úložný systém, čímž zjednodušíte procesy zálohování a obnovení a snížíte náklady. 

Tento kurz popisuje požadavky na konfiguraci, stejně jako postupy pro instalaci, odebrání a upgrade Správce snímků StorSimple.

> [!NOTE]
> * Správce snímků StorSimple nelze použít ke správě virtuálních polí Microsoft Azure StorSimple (označovaných také jako místní virtuální zařízení StorSimple).
> * Pokud plánujete nainstalovat StorSimple Update 2 na zařízení StorSimple, nezapomeňte stáhnout nejnovější verzi Správce snímků StorSimple a nainstalovat jej **před instalací StorSimple Update 2**. Nejnovější verze Správce snímků StorSimple je zpětně kompatibilní a funguje se všemi vydanými verzemi Microsoft Azure StorSimple. Pokud používáte předchozí verzi Správce snímků StorSimple, budete ji muset aktualizovat (před instalací nové verze není nutné odinstalovat předchozí verzi).


## <a name="storsimple-snapshot-manager-installation"></a>Instalace správce snímků StorSimple
Správce snímků StorSimple lze nainstalovat do počítačů s operačním systémem Windows Server 2008 R2 SP1, Windows Server 2012 nebo Windows Server 2012 R2. Na serverech se systémem Windows 2008 R2 je nutné nainstalovat také systémy Windows Server 2008 SP1 a Windows Management Framework 3.0.

Před instalací nebo upgradem modulu snap-in Správce snímků StorSimple pro konzolu MMC (MMC) zkontrolujte, zda jsou správně nakonfigurovány zařízení Microsoft Azure StorSimple a hostitelský server.

## <a name="configure-prerequisites"></a>Konfigurace požadavků
Následující kroky poskytují přehled na vysoké úrovni úloh konfigurace, které je nutné provést před instalací Správce snímků StorSimple. Kompletní informace o konfiguraci a nastavení Microsoft Azure StorSimple, včetně systémových požadavků a podrobných pokynů, najdete v tématu [Nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Než začnete, zkontrolujte [kontrolní seznam konfigurace nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) a požadavky [nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) v části [Nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Před instalací Správce snímků StorSimple
1. Rozbalte, připevněte a připojte zařízení Microsoft Azure StorSimple, jak je popsáno v [části Instalace zařízení StorSimple 8100](storsimple-8100-hardware-installation.md) nebo [Instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Zkontrolujte, zda je v hostitelském počítači spuštěn jeden z následujících operačních systémů:
   
   * Systém Windows Server 2008 R2 (na serverech se systémem Windows 2008 R2 je nutné nainstalovat také systémy Windows Server 2008 SP1 a Windows Management Framework 3.0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     Pro virtuální zařízení StorSimple musí být hostitelem virtuální počítač Microsoft Azure.
3. Ujistěte se, že splňujete všechny požadavky na konfiguraci Microsoft Azure StorSimple. Podrobnosti naleznete v [části Požadavky na nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Připojte zařízení k hostiteli a proveďte počáteční konfiguraci. Podrobnosti naleznete v [krocích nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Vytvořte svazky na zařízení, přiřaďte je hostiteli a ověřte, zda je hostitel může připojit a používat. Správce snímků StorSimple podporuje následující typy svazků:
   
   * Základní svazky
   * Jednoduché svazky
   * Dynamické svazky
   * Zrcadlené dynamické svazky (RAID 1)
   * Svazky sdílené clusterem
     
     Informace o vytváření svazků na zařízení StorSimple nebo StorSimple najdete [v kroku 6: Vytvoření svazku](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)v části [Nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Instalace nového Správce snímků StorSimple
Před instalací Správce snímků StorSimple zkontrolujte, zda jsou svazky vytvořené na zařízení StorSimple nebo virtuálním zařízení StorSimple připojeny, inicializovány a formátovány, jak je popsáno v [části Konfigurovat požadavky](#configure-prerequisites).

> [!IMPORTANT]
> * Pro virtuální zařízení StorSimple musí být hostitelem virtuální počítač Microsoft Azure. 
> * Na hostiteli musí být systém Windows 2008 R2, Windows Server 2012 nebo Windows Server 2012 R2. Pokud je na serveru spuštěn systém Windows Server 2008 R2, je třeba nainstalovat také systémy Windows Server 2008 SP1 a Windows Management Framework 3.0.
> * Před připojením zařízení ke Správci snímků StorSimple je nutné nakonfigurovat připojení iSCSI z hostitele k zařízení StorSimple.

Následujícím postupem dokončete novou instalaci Správce snímků StorSimple. Pokud instalujete upgrade, přejděte na [upgrade nebo přeinstalujte Správce snímků StorSimple](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Krok 1: Instalace Správce snímků StorSimple 
* Krok 2: Připojení Správce snímků StorSimple k zařízení 
* Krok 3: Ověření připojení k zařízení 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Krok 1: Instalace Správce snímků StorSimple
Pomocí následujících kroků nainstalujte Správce snímků StorSimple.

#### <a name="to-install-storsimple-snapshot-manager"></a>Instalace Správce snímků StorSimple
1. Stáhněte si software Správce snímků StorSimple (přejděte na [Web StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) v centru pro stahování Microsoft Download Center) a uložte jej místně na hostiteli.
2. V Průzkumníkovi souborů klikněte pravým tlačítkem myši na komprimovoci a potom klikněte na **Příkaz Extrahovat vše**.
3. V okně **Extrahovat komprimované složky** v poli **Vybrat cíl a extrahovat soubory** zadejte nebo přejděte na cestu, kde chcete soubor extrahovat.
   
    > [!IMPORTANT]
    > Je nutné nainstalovat StorSimple Snapshot Manager na jednotce C: .
    
4. Zaškrtněte **políčko Zobrazit extrahované soubory po dokončení** a klepněte na tlačítko **Extrahovat**.
   
    ![Dialogové okno Extrahovat soubory](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Po dokončení extrakce se otevře cílová složka. Poklepejte na ikonu nastavení aplikace, která se zobrazí v cílové složce.
6. Po zobrazení zprávy **O úspěšné instalaci** klepněte na tlačítko **Zavřít**. Na ploše by se měla zobrazit ikona Správce snímků StorSimple.
   
    ![ikona plochy](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Krok 2: Připojení Správce snímků StorSimple k zařízení
Pomocí následujících kroků připojte Správce snímků StorSimple k zařízení StorSimple.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Připojení Správce snímků StorSimple k zařízení
1. Klikněte na ikonu Správce snímků StorSimple na ploše. Zobrazí se okno Správce snímků StorSimple. Okno obsahuje **podokno Obor,** podokno **Výsledky** a podokno **Akce.** 
   
    ![Uživatelské rozhraní Správce snímků StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * Podokno **Obor** (levé podokno) obsahuje seznam uzlů uspořádaných ve stromové struktuře. Některé uzly můžete rozbalit a vybrat zobrazení nebo konkrétní data související s tímto uzlem. Kliknutím na ikonu šipky rozbalte nebo sbalíte uzel. Kliknutím pravým tlačítkem myši na položku v podokně **Obor** zobrazíte seznam dostupných akcí pro tuto položku.
   * Podokno **Výsledky** (prostřední podokno) obsahuje podrobné informace o stavu uzlu, zobrazení nebo dat, které jste vybrali v podokně **Obor.**
   * Podokno **Akce** obsahuje seznam operací, které lze provádět s uzem, zobrazením nebo daty vybranými v podokně **Obor.**
     
     Úplný popis uživatelského rozhraní Správce snímků StorSimple naleznete v tématu [StorSimple Snapshot Manager user interface](storsimple-use-snapshot-manager.md).
2. V podokně **Obor** klikněte pravým tlačítkem myši na uzel **Zařízení** a potom **klikněte**na Konfigurovat zařízení . Zobrazí se dialogové okno **Konfigurovat zařízení.**
   
    ![Konfigurace zařízení](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. V seznamu **Zařízení** vyberte IP adresu zařízení Microsoft Azure StorSimple nebo virtuálního zařízení. Do textového pole **Heslo** zadejte heslo Správce snímků StorSimple, které jste pro zařízení vytvořili na webu Azure Portal. Klikněte na tlačítko **OK**.
4. Správce snímků StorSimple vyhledá zařízení, které jste identifikovali. Pokud je zařízení k dispozici, Správce snímků StorSimple přidá připojení. Připojení [k zařízení](#to-verify-the-connection) můžete ověřit a ověřit, zda bylo připojení úspěšně přidáno.
   
    Pokud zařízení není z nějakého důvodu k dispozici, Správce snímků StorSimple vrátí chybovou zprávu. Klepnutím na **tlačítko OK** zavřete chybovou zprávu a klepnutím na tlačítko **Storno** zavřete dialogové okno **Konfigurovat zařízení.**
5. Když se připojí k zařízení, Správce snímků StorSimple importuje každou skupinu svazků nakonfigurovanou pro toto zařízení za předpokladu, že skupina svazků má přidružené zálohy. Skupiny svazků, které nemají přidružené zálohy, se neimportují. Kromě toho nejsou importovány zásady zálohování, které byly vytvořeny pro skupinu svazků. Importované skupiny zobrazíte pravým tlačítkem myši na uzel **Skupiny svazků** v podokně **Obor** a klepněte na příkaz **Přepnout importované skupiny**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Krok 3: Ověření připojení k zařízení
Pomocí následujících kroků ověřte, zda je Správce snímků StorSimple připojen k zařízení StorSimple.

#### <a name="to-verify-the-connection"></a>Ověření připojení
1. V podokně **Obor** klikněte na uzel **Zařízení.**
   
    ![StorSimple Snapshot Manager stav zařízení](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Zkontrolujte podokno **Výsledky:** 
   
   * Pokud se na ikoně zařízení zobrazí zelený indikátor a ve sloupci **Stav** se zobrazí **zelený** indikátor, je zařízení připojeno. 
   * Pokud se na ikoně zařízení zobrazí červený indikátor a ve sloupci Stav se zobrazí **nedostupný,** zařízení není připojeno. 
   * Pokud **se aktualizace** zobrazí ve sloupci **Stav,** správce snímků StorSimple načítá skupiny svazků a přidružené zálohy pro připojené zařízení.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Upgrade nebo přeinstalace Správce snímků StorSimple
Před upgradem nebo přeinstalací softwaru byste měli správce snímků StorSimple úplně odinstalovat. 

Před přeinstalací Správce snímků StorSimple zálohovat existující databázi Správce snímků StorSimple v hostitelském počítači. Tím se uloží zásady zálohování a informace o konfiguraci, takže můžete tato data snadno obnovit ze zálohy.

Pokud inovujete nebo přeinstalujete Správce snímků StorSimple, postupujte takto:

* Krok 1: Odinstalace Správce snímků StorSimple 
* Krok 2: Zálohování databáze Správce snímků StorSimple 
* Krok 3: Přeinstalace Správce snímků StorSimple a obnovení databáze 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Krok 1: Odinstalace Správce snímků StorSimple
Pomocí následujících kroků odinstalujte Správce snímků StorSimple.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Odinstalace Správce snímků StorSimple
1. V hostitelském počítači otevřete **Ovládací panely**, klepněte na **položku Programy**a potom klepněte na **položku Programy a funkce**.
2. V levém podokně klepněte na tlačítko **Odinstalovat nebo změnit program**.
3. Klepněte pravým tlačítkem myši na **položku StorSimple Snapshot Manager**a potom klepněte na příkaz **Odinstalovat**.
4. Tím se spustí instalační program Správce snímků StorSimple. Klepněte na tlačítko **Změnit instalační program**a potom klepněte na tlačítko **Odinstalovat**.
   
   > [!NOTE]
   > Pokud jsou na pozadí spuštěny nějaké procesy konzoly MMC, například Správce snímků StorSimple nebo Správa snímků, odinstalace se nezdaří a před pokusem o odinstalaci programu se zobrazí zpráva o ukončení všech instancí konzoly MMC. Vyberte **Možnost Automaticky ukončit aplikace a pokusit se je po dokončení instalace restartovat**a klepněte na tlačítko **OK**.
   > 
   > 
5. Po dokončení procesu odinstalace se zobrazí zpráva **O úspěšné instalaci.** Klikněte na **Zavřít**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Krok 2: Zálohování databáze Správce snímků StorSimple
Pomocí následujících kroků vytvořte a uložte kopii databáze Správce snímků StorSimple.

#### <a name="to-back-up-the-database"></a>Zálohování databáze
1. Zastavte službu Microsoft StorSimple Management Service:
   
   1. Spusťte Správce serveru.
   2. Na řídicím panelu Správce serveru vyberte v nabídce **Nástroje** **položku Služby**.
   3. Na stránce **Služby** vyberte položku **Microsoft StorSimple Management Service**.
   4. V pravém podokně klikněte v části **Microsoft StorSimple Management Service**na zastavit **službu**.
      
        ![Zastavení služby Správce zařízení StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Přejděte na c:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > Složka ProgramData je skrytá.
  
3. Najděte soubor XML katalogu, zkopírujte soubor a uložte kopii na bezpečném místě nebo v cloudu.
   
    ![Soubor katalogu zálohování StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Restartujte službu Microsoft StorSimple Management Service: 
   
   1. Na řídicím panelu Správce serveru vyberte v nabídce **Nástroje** **položku Služby**.
   2. Na stránce **Služby** vyberte **službu Microsoft StorSimple Management Service**.
   3. V pravém podokně klepněte v části **Microsoft StorSimple Management Service**na **restartovat službu**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Krok 3: Přeinstalace Správce snímků StorSimple a obnovení databáze
Chcete-li znovu nainstalovat Správce snímků StorSimple, postupujte podle pokynů v [části Instalace nového Správce snímků StorSimple](#install-a-new-storsimple-snapshot-manager). Potom použijte následující postup k obnovení databáze Správce snímků StorSimple.

#### <a name="to-restore-the-database"></a>Obnovení databáze
1. Zastavte službu Microsoft StorSimple Management Service:
   
   1. Spusťte Správce serveru.
   2. Na řídicím panelu Správce serveru vyberte v nabídce **Nástroje** **položku Služby**.
   3. Na stránce **Služby** vyberte položku **Microsoft StorSimple Management Service**.
   4. V pravém podokně klikněte v části **Microsoft StorSimple Management Service**na zastavit **službu**.
2. Přejděte na c:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > Složka ProgramData je skrytá.
   > 
   > 
3. Odstraňte soubor XML katalogu a nahraďte jej verzí, kterou jste uložili dříve.
4. Restartujte službu Microsoft StorSimple Management Service: 
   
   1. Na řídicím panelu Správce serveru vyberte v nabídce **Nástroje** **položku Služby**.
   2. Na stránce **Služby** vyberte položku **Microsoft StorSimple Management Service**.
   3. V pravém podokně klepněte v části **Microsoft StorSimple Management Service**na **restartovat službu**.

## <a name="next-steps"></a>Další kroky
* Chcete-li se dozvědět více o Správci snímků StorSimple, přejděte na [co je Správce snímků StorSimple?](storsimple-what-is-snapshot-manager.md).
* Další informace o uživatelském rozhraní Správce snímků StorSimple naleznete v [uživatelském rozhraní Správce snímků StorSimple](storsimple-use-snapshot-manager.md).
* Další informace o použití Správce snímků StorSimple naleznete v [aplikaci StorSimple Snapshot Manager ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).

