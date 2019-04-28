---
title: StorSimple Snapshot Manageru a svazků | Dokumentace Microsoftu
description: Popisuje, jak pomocí modulu snap-in konzoly MMC StorSimple Snapshot Manageru k zobrazení a správa svazků a konfigurace zálohování.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: v-sharos
ms.openlocfilehash: 260dfdd4b8fe7c277358fa5773029ea9a532740a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61077526"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Pomocí StorSimple Snapshot Manageru k zobrazení a správa svazků
## <a name="overview"></a>Přehled
Můžete pomocí StorSimple Snapshot Manageru **svazky** uzel (na **oboru** podokno) vyberte svazky a zobrazit informace o nich. Svazky se zobrazují jako jednotky, které odpovídají svazků připojených hostitelem. **Svazky** uzlu se zobrazuje místních svazcích a typy svazků, které jsou podporovány řešení storsimple, včetně svazků o zjištěných pomocí iSCSI a zařízení. 

Další informace o podporovaných svazky, přejděte na [podporu více typů svazku](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Seznam svazků v podokně výsledků](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

**Svazky** uzel také umožňuje prohledat znovu, nebo odstraňte svazky po StorSimple Snapshot Manager rozpozná. 

Tento kurz vysvětluje, jak můžete připojit, inicializovat a formátování svazků a pak pomocí StorSimple Snapshot Manageru do:

* Zobrazení informací o svazcích 
* Odstraňte svazky
* Znovu prohledat svazky 
* Konfigurace základního svazku a proveďte zálohu
* Konfigurace dynamického zrcadlového svazku a proveďte zálohu

> [!NOTE]
> Všechny **svazku** jsou také k dispozici v uzlu akce **akce** podokně.
> 
> 

## <a name="mount-volumes"></a>Připojit svazky
Pomocí následujícího postupu na připojení, inicializace a formátování svazků zařízení StorSimple. Tento postup používá Správa disků systému pro správu pevné disky a odpovídající svazky nebo oddíly. Další informace o správě disků, přejděte na [Správa disků](https://technet.microsoft.com/library/cc770943.aspx) na webu Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Připojit svazky
1. Na hostitelském počítači spusťte iniciátor iSCSI od Microsoftu.
2. Zadejte jednu z IP adresy rozhraní jako cílový portál nebo zjišťování IP adres a připojení k zařízení. Po připojení zařízení, svazky budou mít přístup k systému Windows. Další informace o použití iniciátoru iSCSI od Microsoftu, najdete v části "Připojení k zařízení iSCSI target" v [instalace a konfigurace iniciátoru Microsoft iSCSI][1].
3. Spustit nástroj Správa disků, použijte některý z následujících možností:
   
   * Zadejte Diskmgmt.msc v **spustit** pole.
   * Spusťte správce serveru, rozbalte **úložiště** uzlu a pak vyberte **Správa disků**.
   * Spustit **nástroje pro správu**, rozbalte **Správa počítače** uzlu a pak vyberte **Správa disků**. 
     
     > [!NOTE]
     > Chcete-li spustit nástroj Správa disků je nutné použít oprávnění správce.
     > 
     > 
4. Využijte svazky online:
   
   1. V nástroji Správa disků klikněte pravým tlačítkem na jakýkoli svazek označen **Offline**.
   2. Klikněte na tlačítko **aktivovat Disk**. Na disku by měla být označena **Online** po aktivaci.
5. Inicializace svazky:
   
   1. Klikněte pravým tlačítkem na zjištěné svazky.
   2. V nabídce vyberte **inicializovat Disk**.
   3. V **inicializovat Disk** dialogové okno, vyberte disky, které chcete inicializovat a pak klikněte na tlačítko **OK**.
6. Formát jednoduchými svazky:
   
   1. Klikněte pravým tlačítkem na svazku, který má být zformátován.
   2. V nabídce vyberte **nový jednoduchý svazek**.
   3. Pomocí Průvodce Nový jednoduchý svazek naformátování svazku:
      
      * Zadejte velikost svazku.
      * Zadejte písmeno jednotky.
      * Vyberte systém souborů NTFS.
      * Zvolte velikost alokační jednotky 64 kB.
      * Proveďte rychlé formátování.
7. Formátovat svazky více oddílů. Pokyny najdete v části "Oddíly nebo svazky" [implementace Správa disků](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Zobrazení informací o vašich svazků
Následující postup použijte k zobrazení informací o místní verzi a svazky zařízení StorSimple v Azure.

#### <a name="to-view-volume-information"></a>Chcete-li zobrazit informace o svazku
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru. 
2. V **oboru** podokně klikněte na tlačítko **svazky** uzlu. Zobrazí se seznam místní a připojené svazky, včetně všech svazků o Azure StorSimple v **výsledky** podokně. Sloupce v **výsledky** podokno se dají konfigurovat. (Klikněte pravým tlačítkem myši **svazky** uzlu, vyberte **zobrazení**a pak vyberte **Přidat/odebrat sloupce**.)
   
    ![Konfigurace sloupců](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Sloupec výsledků | Popis |
   |:--- |:--- |
   |  Název |**Název** sloupec obsahuje písmeno jednotky přiřazené každý zjištěný svazek. |
   |  Zařízení |**Zařízení** sloupec obsahuje IP adresu zařízení připojená k hostitelskému počítači. |
   |  Název svazku zařízení |**Název svazku zařízení** sloupec obsahuje název zařízení svazek, ke kterému patří vybraného svazku. Toto je název svazku, které jsou definované na portálu Azure Portal pro tento konkrétní svazek. |
   |  Cesty přístupu |**Cesty přístupu** sloupec zobrazuje cestu, přístup ke svazku. Toto je písmeno jednotky nebo přípojný bod jakou svazek je přístupný na hostitelském počítači. |

## <a name="delete-a-volume"></a>Odstranění svazku
Pomocí následujícího postupu Odstranit svazek ze StorSimple Snapshot Manageru.

> [!NOTE]
> Svazek nejde odstranit, pokud je součástí žádné skupiny svazků. (Možnost odstranění není k dispozici pro svazky, které jsou členy skupiny svazků.) Je nutné odstranit skupinu celý svazek, kterou chcete odstranit svazek.

#### <a name="to-delete-a-volume"></a>Chcete-li odstranit svazek
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně klikněte na tlačítko **svazky** uzlu. 
3. V **výsledky** podokně klikněte pravým tlačítkem na svazku, který chcete odstranit.
4. V nabídce klikněte na tlačítko **odstranit**. 
   
    ![Odstranění svazku](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. **Odstranit svazek** zobrazí se dialogové okno. Typ **potvrdit** textového pole a pak klikněte na **OK**.
6. Ve výchozím nastavení StorSimple Snapshot Manageru zálohuje svazku před jeho odstraněním. Toto opatření vám dokáže chránit před ztrátou dat, pokud nebyla záměrná odstranění. StorSimple Snapshot Manager zobrazuje **automatické snímku** zpráva o průběhu a jejich zálohování svazku. 
   
    ![Zpráva automatické snímku](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Znovu prohledat svazky
Pomocí následujícího postupu znovu zkontrolovat svazky připojené k systému StorSimple Snapshot Manageru.

#### <a name="to-rescan-the-volumes"></a>Znovu prohledejte svazky
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně klikněte pravým tlačítkem na **svazky**a potom klikněte na tlačítko **znovu prohledat svazky**.
   
    ![Znovu prohledat svazky](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Tento postup synchronizuje seznam svazků pomocí StorSimple Snapshot Manageru. Ve výsledcích se projeví všechny změny, jako je například nové svazky nebo odstraněné svazky.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurace a zálohujte základního svazku
Pomocí následujícího postupu do konfigurace služby backup základního svazku a okamžitě spustit zálohování nebo vytvořit zásadu pro naplánované zálohování.

### <a name="prerequisites"></a>Požadavky
Než začnete:

* Ujistěte se, zda jsou správně nakonfigurovány StorSimple zařízení a hostitelského počítače. Další informace najdete v části [nasazení zařízení StorSimple v místním](storsimple-deployment-walkthrough-u2.md).
* Nainstalovat a nakonfigurovat StorSimple Snapshot Manageru. Další informace najdete v části [nasazení StorSimple Snapshot Manageru](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Ke konfiguraci zálohování základního svazku
1. Vytvoření základního svazku na zařízení StorSimple.
2. Připojení, inicializace a formátování svazku, jak je popsáno v [připojit svazky](#mount-volumes). 
3. Klikněte na ikonu StorSimple Snapshot Manageru na ploše. Zobrazí se okno StorSimple Snapshot Manageru. 
4. V **oboru** podokně klikněte pravým tlačítkem na **svazky** uzlu a pak vyberte **znovu prohledat svazky**. Po dokončení kontroly by se měla zobrazit seznam svazků v **výsledky** podokně. 
5. V **výsledky** podokně klikněte pravým tlačítkem na svazku a pak vyberte **vytvořit skupiny svazků**. 
   
    ![Vytvoření skupiny svazků](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. V **vytvořit skupiny svazků** dialogové okno, zadejte název skupiny svazků, přiřadit svazky a pak klikněte na tlačítko **OK**.
7. V **oboru** podokně rozbalte **skupin svazků** uzlu. V části by se měla zobrazit nová skupina svazek **skupin svazků** uzlu. 
8. Klikněte pravým tlačítkem na název skupiny svazků.
   
   * Chcete-li spustit interaktivní úlohy zálohování (na vyžádání), klikněte na tlačítko **provést zálohování**. 
   * Naplánujte automatické zálohování, klikněte na tlačítko **vytvořit zásady zálohování**. Na **Obecné** stránky, vyberte svazek skupinu ze seznamu. Na **plán** zadejte podrobnosti plánu. Až budete hotovi, klikněte na tlačítko **OK**. 
9. Potvrzuje, že byla spuštěna úloha zálohování, rozbalte **úlohy** uzlu v **oboru** podokně a pak klikněte na tlačítko **systémem** uzlu. Seznam aktuálně spuštěných úloh se zobrazí v **výsledky** podokně. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurace a zálohovat dynamické zrcadlený svazek
Proveďte následující kroky pro konfiguraci zálohování dynamické zrcadleného svazku:

* Krok 1: Použijte program Správa disků k vytvoření dynamického zrcadlového svazku. 
* Krok 2: Použití StorSimple Snapshot Manageru pro konfiguraci zálohování.

### <a name="prerequisites"></a>Požadavky
Než začnete:

* Ujistěte se, zda jsou správně nakonfigurovány StorSimple zařízení a hostitelského počítače. Další informace najdete v části [nasazení zařízení StorSimple v místním](storsimple-8000-deployment-walkthrough-u2.md).
* Nainstalovat a nakonfigurovat StorSimple Snapshot Manageru. Další informace najdete v části [nasazení StorSimple Snapshot Manageru](storsimple-snapshot-manager-deployment.md).
* Nakonfigurujte dva svazky na zařízení StorSimple. (V příkladech jsou k dispozici svazky **Disk 1** a **disku 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Krok 1: Pomocí nástroje Správa disků k vytvoření dynamické zrcadlený svazek
Správa disků je nástroj, který systém pro správu pevné disky a svazky nebo oddíly, které obsahují. Další informace o správě disků, přejděte na [Správa disků](https://technet.microsoft.com/library/cc770943.aspx) na webu Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Chcete-li vytvořit dynamické zrcadlený svazek
1. Spustit nástroj Správa disků, použijte některý z následujících možností: 
   
   * Otevřít **spustit** zadejte **Diskmgmt.msc**, a stiskněte klávesu Enter.
   * Spusťte správce serveru, rozbalte **úložiště** uzlu a pak vyberte **Správa disků**. 
   * Spustit **nástroje pro správu**, rozbalte **Správa počítače** uzlu a pak vyberte **Správa disků**. 
2. Ujistěte se, že máte k dispozici dva svazky na zařízení StorSimple. (V tomto příkladu jsou k dispozici svazky **Disk 1** a **disku 2**.) 
3. V okně Správa disků v pravém sloupci v dolním podokně klikněte pravým tlačítkem na **Disk 1** a vyberte **nový zrcadlený svazek**. 
   
    ![Nový zrcadlený svazek](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Na **nový zrcadlený svazek** stránku průvodce, klikněte na tlačítko **Další**.
5. Na **vyberte disky** stránce **disku 2** v **vybrané** podokně klikněte na tlačítko **přidat**a potom klikněte na tlačítko **Další**. 
6. Na **přiřadit písmeno jednotky nebo cesta** stránce, přijměte výchozí hodnoty a pak klikněte na tlačítko **Další**. 
7. Na **formátování svazku** stránku, **velikost alokační jednotky** vyberte **64 KB**. Vyberte **provést rychlé formátování** zaškrtněte políčko a potom klikněte na tlačítko **Další**. 
8. Na **dokončení nový zrcadlený svazek** stránky, zkontrolujte nastavení a potom klikněte na tlačítko **Dokončit**. 
9. Zobrazí se zpráva označující, že základní disk se převedou na dynamický disk. Klikněte na **Ano**.
   
    ![Zpráva převodu dynamického disku](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. V nástroji Správa disků ověřte, že Disk 1 a 2 disku se zobrazují jako dynamické zrcadlených svazků. (**Dynamické** by se měla objevit ve sloupci Stav a změnit barvu panelu kapacitu na červenou, která zrcadlený svazek.) 
    
    ![Dynamické disky disk zrcadleny správy](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Krok 2: Použití StorSimple Snapshot Manageru pro konfiguraci zálohování
Pomocí následujícího postupu pro konfiguraci dynamické zrcadlený svazek a okamžitě spustit zálohování nebo vytvořit zásadu pro naplánované zálohování.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Ke konfiguraci zálohování dynamické zrcadlený svazek
1. Klikněte na ikonu StorSimple Snapshot Manageru na ploše. Zobrazí se okno StorSimple Snapshot Manageru. 
2. V **oboru** podokně klikněte pravým tlačítkem na **svazky** uzel a vyberte možnost **znovu prohledat svazky**. Po dokončení kontroly by se měla zobrazit seznam svazků v **výsledky** podokně. Dynamické zrcadlený svazek je uveden jako jeden svazek. 
3. V **výsledky** podokně klikněte pravým tlačítkem na dynamické zrcadleného svazku a pak klikněte na tlačítko **vytvořit skupiny svazků**. 
4. V **vytvořit skupiny svazků** dialogovém okně zadejte název skupiny svazků, přiřaďte dynamický zrcadlený svazek k této skupině a potom klikněte na tlačítko **OK**. 
5. V **oboru** podokně rozbalte **skupin svazků** uzlu. V části by se měla zobrazit nová skupina svazek **skupin svazků** uzlu. 
6. Klikněte pravým tlačítkem na název skupiny svazků. 
   
   * Chcete-li spustit interaktivní úlohy zálohování (na vyžádání), klikněte na tlačítko **provést zálohování**. 
   * Naplánujte automatické zálohování, klikněte na tlačítko **vytvořit zásady zálohování**. Na **Obecné** skupiny svazků vyberte ze seznamu. Na **plán** zadejte podrobnosti plánu. Až budete hotovi, klikněte na tlačítko **OK**. 
7. Můžete monitorovat úlohy zálohování při jejím spuštění. V **oboru** podokně rozbalte **úlohy** uzlu a pak klikněte na tlačítko **systémem**, podrobnosti o úloze se zobrazí v **výsledky** podokně. Po dokončení úlohy zálohování se podrobnosti se přesunou **posledních 24** hodin práce seznamu. 

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [použít ke správě vašeho řešení StorSimple Snapshot Manageru zařízení StorSimple](storsimple-snapshot-manager-admin.md).
* Zjistěte, jak [pomocí StorSimple Snapshot Manageru k vytvoření a Správa skupin svazků](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
