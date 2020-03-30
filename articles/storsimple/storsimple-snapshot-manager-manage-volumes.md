---
title: Správce snímků A svazky StorSimple | Dokumenty společnosti Microsoft
description: Popisuje použití modulu snap-in Správce snímků StorSimple MMC k zobrazení a správě svazků a konfiguraci záloh.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: twooley
ms.openlocfilehash: f09d4dd46a50f1794e51342a939b8919c5c523ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254648"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Zobrazení a správa svazků pomocí Správce snímků StorSimple
## <a name="overview"></a>Přehled
Uzel **Svazky** Správce snímků StorSimple (v podokně **Obor)** můžete použít k výběru svazků a zobrazení informací o nich. Svazky jsou prezentovány jako jednotky, které odpovídají svazkům připojeným hostitelem. Uzel **Svazky** zobrazuje místní svazky a typy svazků, které jsou podporovány StorSimple, včetně svazků zjištěných pomocí iSCSI a zařízení. 

Další informace o podporovaných svazcích naleznete v podpoře [více typů svazků](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Seznam svazků v podokně Výsledky](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Uzel **Svazky** také umožňuje znovu prohledávat nebo odstraňovat svazky poté, co je správce snímků StorSimple zjistí. 

Tento kurz vysvětluje, jak můžete připojit, inicializovat a formátovat svazky a potom použít Správce snímků StorSimple k:

* Zobrazení informací o svazcích 
* Odstranění svazků
* Opětovné prohledání svazků 
* Konfigurace základního svazku a jeho zálohování
* Konfigurace dynamického zrcadleného svazku a jeho zálohování

> [!NOTE]
> Všechny akce uzlu **svazku** jsou také k dispozici v podokně **Akce.**
> 
> 

## <a name="mount-volumes"></a>Připojit svazky
Pomocí následujícího postupu můžete připojit, inicializovat a formátovat svazky StorSimple. Tento postup používá nástroj Disk Management, systémový nástroj pro správu pevných disků a odpovídající chsvazky nebo oddíly. Další informace o správě disků naleznete na webu Microsoft TechNet na webu Nástroje [pro správu](https://technet.microsoft.com/library/cc770943.aspx) disků.

#### <a name="to-mount-volumes"></a>Připojení svazků
1. V hostitelském počítači spusťte iniciátor iSCSI společnosti Microsoft.
2. Zadej jednu z IP adres rozhraní jako cílovou adresu IP portálu nebo zjišťování a připojte se k zařízení. Po připojení zařízení budou svazky přístupné vašemu systému Windows. Další informace o použití iniciátoru iSCSI společnosti Microsoft naleznete v části Připojení k cílovému zařízení iSCSI v [části Instalace a konfigurace iniciátoru iSCSI][1]společnosti Microsoft .
3. Ke spuštění nástroje Správa disků použijte některou z následujících možností:
   
   * Do pole **Spustit** zadejte příkaz Diskmgmt.msc.
   * Spusťte Správce serveru, rozbalte uzel **úložiště** a vyberte **položku Správa disků**.
   * **Spusťte nástroje pro správu**, rozbalte uzel **Správa počítače** a vyberte **položku Správa disků**. 
     
     > [!NOTE]
     > Ke spuštění nástroje Správa disků je nutné použít oprávnění správce.
     > 
     > 
4. Vezměte objem (y) on-line:
   
   1. V části Správa disků klepněte pravým tlačítkem myši na libovolný svazek označený **offline**.
   2. Klepněte na tlačítko **Znovu aktivovat disk**. Disk by měl být po opětovné aktivaci disku označen jako **online.**
5. Inicializovat objem(y):
   
   1. Klikněte pravým tlačítkem myši na zjištěné svazky.
   2. V nabídce vyberte **možnost Inicializovat disk**.
   3. V dialogovém okně **Inicializovat disk** vyberte disky, které chcete inicializovat, a klepněte na tlačítko **OK**.
6. Formátování jednoduchých svazků:
   
   1. Klikněte pravým tlačítkem myši na svazek, který chcete formátovat.
   2. V nabídce vyberte **Nový jednoduchý svazek**.
   3. K formátování svazku použijte Průvodce novým jednoduchým svazkem:
      
      * Zadejte velikost svazku.
      * Zadej písmeno jednotky.
      * Vyberte systém souborů NTFS.
      * Zvolte velikost alokační jednotky 64 kB.
      * Proveďte rychlé formátování.
7. Formátování svazků s více oddíly. Pokyny naleznete v části Oddíly a svazky v [části Implementace správy disků](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Zobrazení informací o svazcích
Pomocí následujícího postupu můžete zobrazit informace o místních svazcích azure a svazcích Azure StorSimple.

#### <a name="to-view-volume-information"></a>Zobrazení informací o svazku
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple. 
2. V podokně **Obor** klikněte na uzel **Svazky.** Seznam místních a připojených svazků, včetně všech svazků Azure StorSimple, se zobrazí v podokně **výsledky.** Sloupce v podokně **Výsledky** jsou konfigurovatelné. (Klepněte pravým tlačítkem myši na uzel **Svazky,** vyberte **Zobrazit**a pak vyberte **Přidat nebo odebrat sloupce**.)
   
    ![Konfigurace sloupců](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Sloupec Výsledky | Popis |
   |:--- |:--- |
   |  Name (Název) |Sloupec **Název** obsahuje písmeno jednotky přiřazené ke každému zjištěnému svazku. |
   |  Zařízení |Sloupec **Zařízení** obsahuje IP adresu zařízení připojeného k hostitelskému počítači. |
   |  Název svazku zařízení |Sloupec **Název svazku zařízení** obsahuje název svazku zařízení, do kterého vybraný svazek patří. Toto je název svazku definovaný na portálu Azure pro tento konkrétní svazek. |
   |  Přístupové cesty |Ve sloupci **Přístupové cesty** se zobrazí přístupová cesta ke svazku. Toto je písmeno jednotky nebo přípojný bod, ve kterém je svazek přístupný v hostitelském počítači. |

## <a name="delete-a-volume"></a>Odstranění svazku
Pomocí následujícího postupu odstraňte svazek ze Správce snímků StorSimple.

> [!NOTE]
> Svazek nelze odstranit, pokud je součástí libovolné skupiny svazků. (Možnost odstranění není k dispozici pro svazky, které jsou členy skupiny svazků.) Chcete-li odstranit svazek, je nutné odstranit celou skupinu svazků.

#### <a name="to-delete-a-volume"></a>Odstranění svazku
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klikněte na uzel **Svazky.** 
3. V podokně **Výsledky** klikněte pravým tlačítkem myši na svazek, který chcete odstranit.
4. V nabídce klepněte na tlačítko **Odstranit**. 
   
    ![Odstranění svazku](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Zobrazí se dialogové okno **Odstranit svazek.** Do textového pole **zadejte Potvrdit** a klepněte na tlačítko **OK**.
6. Ve výchozím nastavení Správce snímků StorSimple zálohuje svazek před jeho odstraněním. Toto opatření vás může chránit před ztrátou dat, pokud bylo odstranění neúmyslné. Správce snímků StorSimple zobrazí zprávu o průběhu **automatického snímku,** zatímco zálohuje svazek. 
   
    ![Zpráva o automatickém snímku](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Opětovné prohledání svazků
Pomocí následujícího postupu znovu prohledáte svazky připojené ke Správci snímků StorSimple.

#### <a name="to-rescan-the-volumes"></a>Opětovné prohledání svazků
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klepněte pravým tlačítkem myši na **položku Svazky**a potom klepněte na příkaz **Znovu prohledávat svazky**.
   
    ![Opětovné prohledání svazků](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Tento postup synchronizuje seznam svazků se Správcem snímků StorSimple. Všechny změny, například nové svazky nebo odstraněné svazky, se projeví ve výsledcích.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurace a zálohování základního svazku
Pomocí následujícího postupu nakonfigurujte zálohu základního svazku a potom zálohu spusťte okamžitě nebo vytvořte zásadu pro naplánované zálohy.

### <a name="prerequisites"></a>Požadavky
Než začnete:

* Ujistěte se, že zařízení StorSimple a hostitelský počítač jsou správně nakonfigurovány. Další informace najdete v [části Nasazení místního zařízení StorSimple](storsimple-deployment-walkthrough-u2.md).
* Nainstalujte a nakonfigurujte Správce snímků StorSimple. Další informace naleznete v [najdete v například Nasazení Správce snímků StorSimple](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Konfigurace zálohování základního svazku
1. Vytvořte základní svazek na zařízení StorSimple.
2. Připojte, inicializovat a naformátujte svazek, jak je popsáno v [části Připojit svazky](#mount-volumes). 
3. Klikněte na ikonu Správce snímků StorSimple na ploše. Zobrazí se okno Správce snímků StorSimple. 
4. V podokně **Obor** klepněte pravým tlačítkem myši na uzel **Svazky** a vyberte příkaz **Znovu proskenovat svazky**. Po dokončení prohledávání by se měl v podokně **Výsledky** zobrazit seznam svazků. 
5. V podokně **Výsledky** klepněte pravým tlačítkem myši na svazek a pak vyberte **příkaz Vytvořit skupinu svazků**. 
   
    ![Vytvořit skupinu svazků](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. V dialogovém okně **Vytvořit skupinu svazků** zadejte název skupiny svazků, přiřaďte k ní svazky a klepněte na tlačítko **OK**.
7. V podokně **Obor** rozbalte uzel **Skupiny svazků.** Nová skupina svazků by se měla zobrazit v uzlu **Skupiny svazků.** 
8. Klikněte pravým tlačítkem myši na název skupiny svazků.
   
   * Chcete-li spustit interaktivní úlohu zálohování (na vyžádání), klepněte na tlačítko **Převzít zálohu**. 
   * Chcete-li naplánovat automatické zálohování, klepněte na tlačítko **Vytvořit zásady zálohování**. Na stránce **Obecné** vyberte skupinu svazků ze seznamu. Na stránce **Plán** zadejte podrobnosti plánu. Jakmile budete hotovi, klikněte na **Uložit**. 
9. Chcete-li ověřit, zda byla úloha zálohování spuštěna, rozbalte uzel **Úlohy** v podokně **Obor** a klikněte na **příkaz Spuštěný** uzel. Seznam aktuálně spuštěných úloh se zobrazí v podokně **Výsledky.** 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurace a zálohování dynamického zrcadleného svazku
Chcete-li nakonfigurovat zálohování dynamického zrcadleného svazku, proveďte následující kroky:

* Krok 1: Pomocí nástroje Správa disků vytvořte dynamický zrcadlený svazek. 
* Krok 2: Ke konfiguraci zálohování použijte Správce snímků StorSimple.

### <a name="prerequisites"></a>Požadavky
Než začnete:

* Ujistěte se, že zařízení StorSimple a hostitelský počítač jsou správně nakonfigurovány. Další informace najdete v [části Nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Nainstalujte a nakonfigurujte Správce snímků StorSimple. Další informace naleznete v [najdete v například Nasazení Správce snímků StorSimple](storsimple-snapshot-manager-deployment.md).
* Nakonfigurujte dva svazky na zařízení StorSimple. (V příkladech jsou k dispozici svazky **Disk 1** a **Disk 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Krok 1: Vytvoření dynamického zrcadleného svazku pomocí nástroje Správa disků
Správa disků je systémový nástroj pro správu pevných disků a svazků nebo oddílů, které obsahují. Další informace o správě disků naleznete na webu Microsoft TechNet na webu Nástroje [pro správu](https://technet.microsoft.com/library/cc770943.aspx) disků.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Vytvoření dynamického zrcadleného svazku
1. Ke spuštění nástroje Správa disků použijte některou z následujících možností: 
   
   * Otevřete pole **Spustit,** zadejte **Diskmgmt.msc**a stiskněte Enter.
   * Spusťte Správce serveru, rozbalte uzel **úložiště** a vyberte **položku Správa disků**. 
   * **Spusťte nástroje pro správu**, rozbalte uzel **Správa počítače** a vyberte **položku Správa disků**. 
2. Ujistěte se, že máte dva svazky k dispozici na zařízení StorSimple. (V příkladu jsou k dispozici svazky **Disk 1** a **Disk 2**.) 
3. V okně Správa disků v pravém sloupci dolního podokna klepněte pravým tlačítkem myši na **disk 1** a vyberte **nový zrcadlený svazek**. 
   
    ![Nový zrcadlový svazek](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Na stránce **Průvodce novým zrcadlenou majekce** klepněte na tlačítko **Další**.
5. Na stránce **Vybrat disky** vyberte v **podokně Vybrané** položku **Disk 2,** klepněte na tlačítko **Přidat**a potom klepněte na tlačítko **Další**. 
6. Na stránce **Přiřadit písmeno jednotky nebo cestu** přijměte výchozí hodnoty a klepněte na tlačítko **Další**. 
7. Na stránce **Formát svazku** vyberte v poli **Velikost alokační jednotky** **64 kF**. Zaškrtněte **políčko Provést rychlý formát** a klepněte na tlačítko **Další**. 
8. Na stránce **Dokončení nového zrcadleného svazku** zkontrolujte nastavení a klepněte na tlačítko **Dokončit**. 
9. Zobrazí se zpráva označující, že základní disk bude převeden na dynamický disk. Klepněte na tlačítko **Ano**.
   
    ![Zpráva o převodu dynamického disku](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. V části Správa disků ověřte, zda jsou disky 1 a 2 zobrazeny jako dynamické zrcadlené svazky. (**Dynamický** by se měl objevit ve sloupci stavu a barva panelu kapacity by se měla změnit na červenou, což znamená zrcadlený svazek.) 
    
    ![Zrcadlené dynamické disky se správou disků](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Krok 2: Konfigurace zálohování pomocí Správce snímků StorSimple
Pomocí následujícího postupu nakonfigurujte dynamický zrcadlený svazek a potom spusťte zálohu okamžitě nebo vytvořte zásadu pro naplánované zálohy.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Konfigurace zálohování dynamického zrcadleného svazku
1. Klikněte na ikonu Správce snímků StorSimple na ploše. Zobrazí se okno Správce snímků StorSimple. 
2. V podokně **Obor** klepněte pravým tlačítkem myši na uzel **Svazky** a vyberte **možnost Znovu proskenovat svazky**. Po dokončení prohledávání by se měl v podokně **Výsledky** zobrazit seznam svazků. Dynamický zrcadlený svazek je uveden jako jeden svazek. 
3. V podokně **Výsledky** klepněte pravým tlačítkem myši na dynamický zrcadlený svazek a potom klepněte na příkaz **Vytvořit skupinu svazků**. 
4. V dialogovém okně **Vytvořit skupinu svazků** zadejte název skupiny svazků, přiřaďte dynamicky zrcadlený svazek této skupině a klepněte na tlačítko **OK**. 
5. V podokně **Obor** rozbalte uzel **Skupiny svazků.** Nová skupina svazků by se měla zobrazit v uzlu **Skupiny svazků.** 
6. Klikněte pravým tlačítkem myši na název skupiny svazků. 
   
   * Chcete-li spustit interaktivní úlohu zálohování (na vyžádání), klepněte na tlačítko **Převzít zálohu**. 
   * Chcete-li naplánovat automatické zálohování, klepněte na tlačítko **Vytvořit zásady zálohování**. Na stránce **Obecné** vyberte skupinu svazků ze seznamu. Na stránce **Plán** zadejte podrobnosti plánu. Jakmile budete hotovi, klikněte na **Uložit**. 
7. Můžete sledovat úlohu zálohování při spuštění. V podokně **Obor** rozbalte uzel **Úlohy** a potom klepněte na tlačítko **Spuštěno**, podrobnosti o úloze se zobrazí v podokně **Výsledky.** Po dokončení úlohy zálohování jsou podrobnosti převedeny do seznamu úloh **Posledních 24** hodin. 

## <a name="next-steps"></a>Další kroky
* Naučte se [používat Správce snímků StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Naučte se [používat Správce snímků StorSimple k vytváření a správě skupin svazků](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
