---
title: Nasazení Snapshot Manageru zařízení StorSimple | Dokumentace Microsoftu
description: Zjistěte, jak stáhnout a nainstalovat StorSimple Snapshot Manageru, modul snap-in konzoly MMC pro správu funkcí ochrany a zálohování dat StorSimple.
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
ms.openlocfilehash: ee17e4b69d1e6c9de465e4241ee2237361e320b7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168001"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Nasazení modulu snap-in konzoly MMC StorSimple Snapshot Manageru

## <a name="overview"></a>Přehled
StorSimple Snapshot Manageru je modul snap-in konzoly Microsoft Management Console (MMC), který zjednodušuje ochranu dat a správy zálohování v prostředí Microsoft Azure StorSimple. Pomocí StorSimple Snapshot Manager můžete spravovat Microsoft Azure StorSimple v místním a cloudovém úložišti, jako kdyby byly plně integrované úložný systém, tedy zjednodušení procesů zálohování a obnovení a snížit náklady na. 

Tento kurz popisuje požadavky na konfiguraci, také s postupy pro instalaci, odebrání a inovace StorSimple Snapshot Manageru.

> [!NOTE]
> * StorSimple Snapshot Manageru nejde použít pro správu Microsoft Azure StorSimple virtuální pole (označované také jako StorSimple místní virtuální zařízení).
> * Pokud budete chtít nainstalovat StorSimple Update 2 na zařízení StorSimple, je potřeba stáhnout nejnovější verzi StorSimple Snapshot Manageru a nainstalujte ho **před instalací StorSimple Update 2**. Nejnovější verzi StorSimple Snapshot Manageru je zpětně kompatibilní a funguje ve všech vydaných verzích Microsoft Azure StorSimple. Pokud používáte předchozí verzi StorSimple Snapshot Manageru, je potřeba aktualizovat (není potřeba odinstalovat předchozí verzi před instalací nové verze).


## <a name="storsimple-snapshot-manager-installation"></a>Instalace StorSimple Snapshot Manageru
StorSimple Snapshot Manageru můžete nainstalovat na počítačích, na kterých běží Windows Server 2008 R2 SP1, Windows Server 2012 nebo operační systém Windows Server 2012 R2. Na serverech se systémem Windows 2008 R2 musíte také nainstalovat systém Windows Server 2008 SP1 a Windows Management Framework 3.0.

Před instalací nebo upgradovat modulu snap-in StorSimple Snapshot Manageru Microsoft Management Console (MMC), ujistěte se, zda jsou správně nakonfigurovány zařízení a hostitelů serveru Microsoft Azure StorSimple.

## <a name="configure-prerequisites"></a>Konfigurovat požadavky související s
Následující kroky obsahují podrobný přehled úlohy konfigurace, které musíte provést před instalací StorSimple Snapshot Manageru. Kompletní konfigurace Microsoft Azure StorSimple a informace o nastavení, včetně požadavky na systém a podrobné pokyny najdete v tématu [nasazení zařízení StorSimple v místním](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Než začnete, projděte si [kontrolní seznam konfigurace nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) a a [požadavky na nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) v [nasazení zařízení StorSimple v místním](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Před instalací StorSimple Snapshot Manageru
1. Rozbalit, připojit a připojení zařízení Microsoft Azure StorSimple, jak je popsáno v [instalaci zařízení StorSimple 8100](storsimple-8100-hardware-installation.md) nebo [instalaci zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Ujistěte se, že hostitelský počítač s některým z následujících operačních systémů:
   
   * Windows Server 2008 R2 (na servery se systémem Windows 2008 R2, musíte také nainstalovat systém Windows Server 2008 SP1 a Windows Management Framework 3.0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     Hostitel pro virtuální zařízení StorSimple, musí být na virtuálním počítači Microsoft Azure.
3. Ujistěte se, že se splnily všechny požadavky konfigurace Microsoft Azure StorSimple. Podrobnosti najdete v části [požadavky na nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Připojení zařízení k hostiteli a proveďte počáteční konfiguraci. Podrobnosti najdete v části [kroky nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Vytvořte svazky na zařízení, je přiřadit k hostiteli a ověřte, že hostitel může připojit a jejich použití. StorSimple Snapshot Manager podporuje následující typy svazků:
   
   * Základní svazky
   * Jednoduché svazky
   * Dynamické svazky
   * Zrcadlené dynamické svazky (RAID 1)
   * Sdílené svazky clusteru
     
     Informace o vytváření svazků v zařízení StorSimple nebo virtuální zařízení StorSimple, přejděte na [krok 6: Vytvoření svazku](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)v [nasazení zařízení StorSimple v místním](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Instalace nové StorSimple Snapshot Manageru
Před instalací StorSimple Snapshot Manageru, ujistěte se, že do svazků vytvořených v zařízení StorSimple nebo virtuální zařízení StorSimple připojit, inicializovat a ve formátu, jak je popsáno v [konfigurovat požadavky související s](#configure-prerequisites).

> [!IMPORTANT]
> * Hostitel pro virtuální zařízení StorSimple, musí být na virtuálním počítači Microsoft Azure. 
> * Hostitel musí běžet Windows 2008 R2, Windows Server 2012 nebo Windows Server 2012 R2. Pokud na serveru běží Windows Server 2008 R2, musíte také nainstalovat systém Windows Server 2008 SP1 a Windows Management Framework 3.0.
> * Předtím, než zařízení lze připojit k Snapshot Manageru zařízení StorSimple je nutné nakonfigurovat připojení k iSCSI z hostitele do zařízení StorSimple.

Postupujte podle následujících kroků dokončete instalaci StorSimple Snapshot Manageru. Pokud instalujete upgrade, přejděte na [upgradovat nebo přeinstalovat StorSimple Snapshot Manageru](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Krok 1: Nainstalovat Snapshot Manageru zařízení StorSimple 
* Krok 2: Připojení k zařízení StorSimple Snapshot Manageru 
* Krok 3: Ověření připojení k zařízení 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Krok 1: Nainstalovat Snapshot Manageru zařízení StorSimple
Následujícím postupem nainstalujte StorSimple Snapshot Manageru.

#### <a name="to-install-storsimple-snapshot-manager"></a>Chcete-li nainstalovat StorSimple Snapshot Manageru
1. Stáhnout software Snapshot Manageru zařízení StorSimple (přejděte na [StorSimple Snapshot Manageru](https://www.microsoft.com/download/details.aspx?id=44220) webu Microsoft Download Center) a uložte ho místně na hostiteli.
2. V Průzkumníku souborů klikněte pravým tlačítkem na komprimovanou složku a potom klikněte na tlačítko **extrahovat všechny**.
3. V **extrahujte komprimované (Zipped) složky** okno v **vyberte cíl a extrahování souborů** zadejte nebo přejděte na cestu, kam chcete soubor, který má být extrahován.
   
    > [!IMPORTANT]
    > StorSimple Snapshot Manageru je nutné nainstalovat na jednotce C:.
    
4. Vyberte **zobrazit rozbalené soubory po dokončení** zaškrtněte políčko a potom klikněte na tlačítko **extrahovat**.
   
    ![Extrahujte soubory dialogové okno](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Po dokončení extrakce otevře do cílové složky. Dvakrát klikněte na ikonu nastavení aplikace, která se zobrazí v cílové složce.
6. Když **instalace úspěšná** se zobrazí zpráva, klikněte na tlačítko **Zavřít**. Na ploše by měl zobrazit ikonu StorSimple Snapshot Manageru.
   
    ![ikony na ploše](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Krok 2: Připojení k zařízení StorSimple Snapshot Manageru
Pomocí následujících kroků pro připojení k zařízení StorSimple Snapshot Manageru zařízení StorSimple.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Pro připojení k zařízení StorSimple Snapshot Manageru
1. Klikněte na ikonu StorSimple Snapshot Manageru na ploše. Zobrazí se okno StorSimple Snapshot Manageru. Okno obsahuje **oboru** podokně **výsledky** podokně a **akce** podokně. 
   
    ![Uživatelské rozhraní StorSimple Snapshot Manageru](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * **Oboru** podokně (levé podokno) obsahuje seznam uzlů, které jsou uspořádány ve stromové struktuře. Některé uzly výběr zobrazení nebo konkrétní data týkající se takový uzel můžete rozbalit. Klikněte na ikonu šipky pro rozbalení a sbalení uzlu. Klikněte pravým tlačítkem na položku **oboru** podokno zobrazíte seznam dostupných akcí pro danou položku.
   * **Výsledky** podokno (prostřední podokno) obsahuje podrobné informace o uzlu, zobrazení nebo data, která jste vybrali v stavu **oboru** podokně.
   * **Akce** podokně je seznam operací, které můžete provést na uzlu, zobrazení nebo data, která jste vybrali v **oboru** podokně.
     
     Úplný popis uživatelské rozhraní StorSimple Snapshot Manageru najdete v tématu [uživatelské rozhraní StorSimple Snapshot Manageru](storsimple-use-snapshot-manager.md).
2. V **oboru** podokně klikněte pravým tlačítkem na **zařízení** uzlu a pak klikněte na tlačítko **nakonfigurovat zařízení**. **Nakonfigurovat zařízení** zobrazí se dialogové okno.
   
    ![Konfigurace zařízení](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. V **zařízení** seznamu, vyberte IP adresu virtuálního zařízení na zařízení Microsoft Azure StorSimple. V **heslo** textové pole, zadejte heslo Snapshot Manageru zařízení StorSimple, kterou jste vytvořili pro zařízení na portálu Azure portal. Klikněte na **OK**.
4. StorSimple Snapshot Manageru vyhledá na zařízení, které jste určili. Pokud je zařízení k dispozici, přidá StorSimple Snapshot Manageru připojení. Je možné [ověřit připojení k zařízení](#to-verify-the-connection) potvrďte, že připojení byl úspěšně přidán.
   
    Pokud zařízení není k dispozici z jakéhokoli důvodu, StorSimple Snapshot Manageru vrátí chybovou zprávu. Klikněte na tlačítko **OK** zavřete chybovou zprávu a pak klikněte na tlačítko **zrušit** zavřete **nakonfigurovat zařízení** dialogové okno.
5. Při připojení k zařízení StorSimple Snapshot Manageru importuje každý svazek skupiny nakonfigurované pro dané zařízení za předpokladu, že skupiny svazků má přidružené zálohy. Nejsou naimportovány skupin svazků, které nemají přidružené zálohy. Kromě toho nejsou importovány zásady zálohování, které byly vytvořeny pro skupiny svazků. Pokud chcete zobrazit importované skupiny, klikněte pravým tlačítkem na nejvyšší **skupin svazků** uzlu v **oboru** podokně a klepněte na **přepnout importované skupiny**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Krok 3: Ověření připojení k zařízení
Ověřit připojení k zařízení StorSimple Snapshot Manageru zařízení StorSimple pomocí následujících kroků.

#### <a name="to-verify-the-connection"></a>Chcete-li ověřit připojení
1. V **oboru** podokně klikněte na tlačítko **zařízení** uzlu.
   
    ![Stav zařízení StorSimple Snapshot Manageru](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Zkontrolujte, **výsledky** podokna: 
   
   * Pokud na ikonu zařízení, zobrazí se zelená indikátor a **dostupné** se zobrazí v **stav** sloupec a pak zařízení je připojené. 
   * Pokud na zařízení ikonu se zobrazí červený indikátor a není k dispozici v **stav** sloupec a pak zařízení není připojený. 
   * Pokud **aktualizací** se zobrazí v **stav** sloupec a pak StorSimple Snapshot Manageru je načítání skupin svazků a přidružené zálohy pro připojené zařízení.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Upgradovat nebo přeinstalovat StorSimple Snapshot Manageru
StorSimple Snapshot Manageru byste měli odinstalovat úplně upgradovat nebo přeinstalovat software. 

Před opětovnou instalací StorSimple Snapshot Manageru, proveďte zálohu existující databáze StorSimple Snapshot Manageru v hostitelském počítači. Toto uloží informace o zálohování zásady a konfigurace tak, aby tato data můžete snadno obnovit ze zálohy.

Pokud se upgrade nebo opětovné instalace StorSimple Snapshot Manageru, postupujte takto:

* Krok 1: Odinstalujte StorSimple Snapshot Manageru 
* Krok 2: Proveďte zálohu databáze StorSimple Snapshot Manageru 
* Krok 3: Přeinstalujte StorSimple Snapshot Manageru a obnovit databázi 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Krok 1: Odinstalujte StorSimple Snapshot Manageru
Použijte následující postup odinstalace StorSimple Snapshot Manageru.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Chcete-li odinstalovat StorSimple Snapshot Manageru
1. Na hostitelském počítači, otevřete **ovládací panely**, klikněte na tlačítko **programy**a potom klikněte na tlačítko **programy a funkce**.
2. V levém podokně klikněte na tlačítko **odinstalovat nebo změnit program**.
3. Klikněte pravým tlačítkem na **StorSimple Snapshot Manageru**a potom klikněte na tlačítko **odinstalovat**.
4. Spustí se StorSimple Snapshot Manageru instalační program. Klikněte na tlačítko **upravit instalaci**a potom klikněte na tlačítko **odinstalovat**.
   
   > [!NOTE]
   > Pokud neexistují žádné procesy konzoly MMC běžet na pozadí, jako je Snapshot Manageru zařízení StorSimple nebo Správa disků, odinstalace selže a zobrazí se zpráva, zavřete všechny instance konzoly MMC, než se pokusíte odinstalovat program. Vyberte **automaticky ukončit aplikace a pokusit se je restartovat po dokončení instalace**a potom klikněte na tlačítko **OK**.
   > 
   > 
5. Po dokončení odinstalace **instalace úspěšná** se zobrazí zpráva. Klikněte na **Zavřít**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Krok 2: Proveďte zálohu databáze StorSimple Snapshot Manageru
Následujícím postupem vytvoříte a uložíte kopii databáze StorSimple Snapshot Manageru.

#### <a name="to-back-up-the-database"></a>K zálohování databáze
1. Zastavte službu Microsoft StorSimple pro správu:
   
   1. Spusťte správce serveru.
   2. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   3. Na **služby** stránce **službě pro správu Microsoft StorSimple**.
   4. V pravém podokně v části **službě pro správu Microsoft StorSimple**, klikněte na tlačítko **zastavit službu**.
      
        ![Zastavte služby Správce zařízení StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Přejděte na C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData je skrytá složka.
  
3. Najít soubor XML katalogu, zkopírujte soubor a kopii uložit na bezpečném místě nebo v cloudu.
   
    ![Soubor katalogu záloh StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Restartujte službu Microsoft StorSimple pro správu: 
   
   1. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   2. Na **služby** stránky, vyberte **službě pro správu Microsoft StorSimple**.
   3. V pravém podokně v části **službě pro správu Microsoft StorSimple**, klikněte na tlačítko **restartujte službu**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Krok 3: Přeinstalujte StorSimple Snapshot Manageru a obnovit databázi
StorSimple Snapshot Manager přeinstalovat, postupujte podle kroků v [nainstalovat nový StorSimple Snapshot Manageru](#install-a-new-storsimple-snapshot-manager). Pak následujícím postupem obnovit databázi StorSimple Snapshot Manageru.

#### <a name="to-restore-the-database"></a>Chcete-li obnovit databázi
1. Zastavte službu Microsoft StorSimple pro správu:
   
   1. Spusťte správce serveru.
   2. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   3. Na **služby** stránce **službě pro správu Microsoft StorSimple**.
   4. V pravém podokně v části **službě pro správu Microsoft StorSimple**, klikněte na tlačítko **zastavit službu**.
2. Přejděte na C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > ProgramData je skrytá složka.
   > 
   > 
3. Odstranit soubor XML katalogu a nahradit verzí, kterou jste předtím uložili.
4. Restartujte službu Microsoft StorSimple pro správu: 
   
   1. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   2. Na **služby** stránce **službě pro správu Microsoft StorSimple**.
   3. V pravém podokně v části **službě pro správu Microsoft StorSimple**, klikněte na tlačítko **restartujte službu**.

## <a name="next-steps"></a>Další postup
* Další informace o StorSimple Snapshot Manageru, přejděte na [co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Další informace o uživatelském rozhraní StorSimple Snapshot Manageru, přejděte na [uživatelské rozhraní StorSimple Snapshot Manageru](storsimple-use-snapshot-manager.md).
* Další informace o použití StorSimple Snapshot Manageru, přejděte na [pomocí StorSimple Snapshot Manager ke správě vašeho řešení StorSimple](storsimple-snapshot-manager-admin.md).

