---
title: StorSimple Snapshot Manager a svazky | Microsoft Docs
description: Popisuje, jak pomocí modulu snap-in StorSimple Snapshot Manager MMC zobrazit a spravovat svazky a nakonfigurovat zálohy.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: alkohli
ms.openlocfilehash: 79a239def70f0455f63c18d86397ec8d927f244c
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90054903"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Zobrazení a správa svazků pomocí Snapshot Manager StorSimple
## <a name="overview"></a>Přehled
Uzel StorSimple Snapshot Manager **svazky** (v podokně **oboru** ) můžete použít k výběru svazků a zobrazení informací o nich. Svazky se zobrazují jako jednotky, které odpovídají svazkům připojeným k hostiteli. Uzel **svazky** zobrazuje místní svazky a typy svazků, které StorSimple podporuje, včetně svazků zjištěných pomocí iSCSI a zařízení. 

Další informace o podporovaných svazcích najdete v [podpoře více typů svazků](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Seznam svazků v podokně výsledků](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Uzel **svazky** také umožňuje znovu prohledat nebo odstranit svazky po StorSimple Snapshot Manager zjistí je. 

V tomto kurzu se dozvíte, jak můžete připojit, inicializovat a naformátovat svazky a pak použít StorSimple Snapshot Manager k těmto akcím:

* Zobrazit informace o svazcích 
* Odstranit svazky
* Znovu prohledat svazky 
* Konfigurace základního svazku a jeho zálohování
* Konfigurace dynamického zrcadleného svazku a jeho zálohování

> [!NOTE]
> Všechny akce uzlu **svazku** jsou také k dispozici v podokně **Akce** .
> 
> 

## <a name="mount-volumes"></a>Připojit svazky
K připojení, inicializaci a formátování svazků StorSimple použijte následující postup. Tento postup využívá správu disků, systémový nástroj pro správu pevných disků a příslušné svazky nebo oddíly. Další informace o správě disků najdete v části [Správa disků](https://technet.microsoft.com/library/cc770943.aspx) na webu Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Připojení svazků
1. Na hostitelském počítači spusťte iniciátor iSCSI společnosti Microsoft.
2. Zadejte jednu z IP adres rozhraní jako cílový portál nebo IP adresu zjišťování a připojte se k zařízení. Po připojení zařízení budou svazky dostupné pro váš systém Windows. Další informace o použití iniciátoru iSCSI od Microsoftu najdete v části připojení k cílovému zařízení iSCSI v tématu [instalace a Konfigurace iniciátoru iSCSI společnosti Microsoft][1].
3. Ke spuštění správy disků použijte kteroukoli z následujících možností:
   
   * Do pole **Spustit** zadejte diskmgmt. msc.
   * Spusťte Správce serveru, rozbalte uzel **úložiště** a pak vyberte **Správa disků**.
   * Spusťte **Nástroje pro správu**, rozbalte uzel **Správa počítače** a pak vyberte **Správa disků**. 
     
     > [!NOTE]
     > Ke spuštění správy disků je nutné použít oprávnění správce.
     > 
     > 
4. Převzít svazky online:
   
   1. V nástroji Správa disků klikněte pravým tlačítkem na libovolný svazek označený jako **offline**.
   2. Klikněte na možnost **znovu aktivovat disk**. Po opětovné aktivaci disku by měl být disk označen **online** .
5. Inicializovat svazky:
   
   1. Klikněte pravým tlačítkem na zjištěné svazky.
   2. V nabídce vyberte **inicializovat disk**.
   3. V dialogovém okně **inicializovat disk** vyberte disky, které chcete inicializovat, a poté klikněte na tlačítko **OK**.
6. Naformátujte jednoduché svazky:
   
   1. Klikněte pravým tlačítkem na svazek, který chcete naformátovat.
   2. V nabídce vyberte **Nový jednoduchý svazek**.
   3. Pomocí Průvodce vytvořením jednoduchého svazku naformátujte svazek:
      
      * Zadejte velikost svazku.
      * Zadejte písmeno jednotky.
      * Vyberte systém souborů NTFS.
      * Zvolte velikost alokační jednotky 64 kB.
      * Proveďte rychlé formátování.
7. Naformátujte svazky s více oddíly. Pokyny najdete v části "oddíly a svazky" v tématu [Implementace správy disků](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Zobrazit informace o svazcích
K zobrazení informací o místních a StorSimple svazcích Azure použijte následující postup.

#### <a name="to-view-volume-information"></a>Zobrazení informací o svazku
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager. 
2. V podokně **obor** klikněte na uzel **svazky** . V podokně **výsledků** se zobrazí seznam místních a připojených svazků, včetně všech svazků StorSimple Azure. Sloupce v podokně **výsledků** lze konfigurovat. (Klikněte pravým tlačítkem myši na uzel **svazky** , vyberte možnost **zobrazení**a pak vyberte možnost **Přidat nebo odebrat sloupce**.)
   
    ![Konfigurace sloupců](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Sloupec výsledků | Popis |
   |:--- |:--- |
   |  Název |Sloupec **název** obsahuje písmeno jednotky přiřazené ke každému zjištěnému svazku. |
   |  Zařízení |Sloupec **zařízení** obsahuje IP adresu zařízení, které je připojené k hostitelskému počítači. |
   |  Název svazku zařízení |Sloupec **název svazku zařízení** obsahuje název svazku zařízení, ke kterému patří vybraný svazek. Toto je název svazku definovaný v Azure Portal pro tento konkrétní svazek. |
   |  Přístup k cestám |Sloupec **přístupové cesty** zobrazuje cestu přístupu ke svazku. Toto je písmeno jednotky nebo přípojný bod, ve kterém je svazek přístupný na hostitelském počítači. |

## <a name="delete-a-volume"></a>Odstranění svazku
K odstranění svazku z StorSimple Snapshot Manager použijte následující postup.

> [!NOTE]
> Svazek nelze odstranit, pokud je součástí jakékoli skupiny svazků. (Možnost Odstranit není k dispozici pro svazky, které jsou členy skupiny svazků.) Aby bylo možné svazek odstranit, je nutné odstranit celou skupinu svazků.

#### <a name="to-delete-a-volume"></a>Odstranění svazku
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** klikněte na uzel **svazky** . 
3. V podokně **výsledků** klikněte pravým tlačítkem myši na svazek, který chcete odstranit.
4. V nabídce klikněte na **Odstranit**. 
   
    ![Odstranění svazku](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Zobrazí se dialogové okno **Odstranit svazek** . Do textového pole zadejte **Confirm** a pak klikněte na **OK**.
6. Ve výchozím nastavení StorSimple Snapshot Manager zálohuje svazek před jeho odstraněním. Tato preventivní opatrnost vám může chránit před ztrátou dat v případě, že odstranění nebylo úmyslné. StorSimple Snapshot Manager zobrazí při zálohování svazku automatickou zprávu o průběhu **snímku** . 
   
    ![Automatická zpráva snímku](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Znovu prohledat svazky
K opětovnému prohledání svazků připojených k StorSimple Snapshot Manager použijte následující postup.

#### <a name="to-rescan-the-volumes"></a>Opětovné prohledání svazků
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** klikněte pravým tlačítkem na **svazky**a pak klikněte na **Prohledat svazky**.
   
    ![Znovu prohledat svazky](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Tento postup synchronizuje seznam svazků s Snapshot Manager StorSimple. Ve výsledcích se projeví všechny změny, třeba nové svazky nebo odstraněné svazky.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurace a zálohování základního svazku
Pomocí následujícího postupu můžete nakonfigurovat zálohu základního svazku a potom buď spustit zálohování okamžitě, nebo vytvořit zásadu pro naplánované zálohování.

### <a name="prerequisites"></a>Požadavky
Než začnete:

* Ujistěte se, že zařízení StorSimple a hostitelský počítač jsou správně nakonfigurované. Další informace najdete [v poznámkách k nasazení místního zařízení StorSimple](storsimple-deployment-walkthrough-u2.md).
* Nainstalujte a nakonfigurujte StorSimple Snapshot Manager. Další informace najdete v [nasazení StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Konfigurace zálohování základního svazku
1. Na zařízení StorSimple vytvořte základní svazek.
2. Připojte, inicializujte a naformátujte svazek, jak je popsáno v části [připojení svazků](#mount-volumes). 
3. Klikněte na ikonu Snapshot Manager StorSimple na ploše. Zobrazí se okno StorSimple Snapshot Manager. 
4. V podokně **obor** klikněte pravým tlačítkem myši na uzel **svazky** a potom vyberte možnost **Prohledat svazky**. Po dokončení kontroly by se měl v podokně **výsledků** zobrazit seznam svazků. 
5. V podokně **výsledků** klikněte pravým tlačítkem na svazek a vyberte **vytvořit skupinu svazků**. 
   
    ![Vytvořit skupinu svazků](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. V dialogovém okně **vytvořit skupinu svazků** zadejte název skupiny svazků, přiřaďte k ní svazky a pak klikněte na **OK**.
7. V podokně **Rozsah** rozbalte uzel **skupiny svazků** . Nová skupina svazků by se měla zobrazit v uzlu **skupiny svazků** . 
8. Pravým tlačítkem myši klikněte na název skupiny svazků.
   
   * Pokud chcete spustit interaktivní úlohu zálohování (na vyžádání), klikněte na **vytvořit zálohu**. 
   * Pokud chcete naplánovat automatické zálohování, klikněte na **vytvořit zásadu zálohování**. Na stránce **Obecné** vyberte ze seznamu skupinu svazků. Na stránce **plán** zadejte podrobnosti plánu. Jakmile budete hotovi, klikněte na **Uložit**. 
9. Chcete-li ověřit, zda byla úloha zálohování zahájena, rozbalte uzel **úlohy** v podokně **obor** a poté klikněte na **běžící** uzel. V podokně **výsledků** se zobrazí seznam aktuálně spuštěných úloh. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurace a zálohování dynamického zrcadleného svazku
Provedením následujících kroků nakonfigurujte zálohování dynamického zrcadleného svazku:

* Krok 1: pomocí nástroje Správa disků vytvořte dynamický zrcadlený svazek. 
* Krok 2: ke konfiguraci zálohování použijte Snapshot Manager StorSimple.

### <a name="prerequisites"></a>Požadavky
Než začnete:

* Ujistěte se, že zařízení StorSimple a hostitelský počítač jsou správně nakonfigurované. Další informace najdete [v poznámkách k nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Nainstalujte a nakonfigurujte StorSimple Snapshot Manager. Další informace najdete v [nasazení StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Nakonfigurujte na zařízení StorSimple dva svazky. (V těchto příkladech jsou dostupné svazky **disk 1** a **disk 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Krok 1: použití správy disků k vytvoření dynamického zrcadleného svazku
Správa disků je systémový nástroj pro správu pevných disků a svazků nebo oddílů, které obsahují. Další informace o správě disků najdete v části [Správa disků](https://technet.microsoft.com/library/cc770943.aspx) na webu Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Postup vytvoření dynamického zrcadleného svazku
1. Ke spuštění správy disků použijte kteroukoli z následujících možností: 
   
   * Otevřete pole **Spustit** , zadejte **diskmgmt. msc**a stiskněte klávesu ENTER.
   * Spusťte Správce serveru, rozbalte uzel **úložiště** a pak vyberte **Správa disků**. 
   * Spusťte **Nástroje pro správu**, rozbalte uzel **Správa počítače** a pak vyberte **Správa disků**. 
2. Ujistěte se, že na zařízení StorSimple máte k dispozici dva svazky. (V tomto příkladu jsou dostupné svazky **disk 1** a **disk 2**.) 
3. V okně Správa disků v pravém sloupci dolního podokna klikněte pravým tlačítkem myši na položku **disk 1** a vyberte možnost **nový zrcadlený svazek**. 
   
    ![Nový zrcadlený svazek](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Na stránce průvodce **vytvořením zrcadleného svazku** klikněte na **Další**.
5. Na stránce **Vybrat disky** vyberte v podokně **vybrané** možnost **disk 2** , klikněte na tlačítko **Přidat**a poté klikněte na tlačítko **Další**. 
6. Na stránce **přiřadit písmeno jednotky nebo cestu** přijměte výchozí hodnoty a potom klikněte na tlačítko **Další**. 
7. Na stránce **Formát svazku** v poli **velikost alokační jednotky** vyberte **64 KB**. Zaškrtněte políčko **provést rychlé formátování** a potom klikněte na tlačítko **Další**. 
8. Na stránce **dokončení nového zrcadleného svazku** zkontrolujte nastavení a pak klikněte na **Dokončit**. 
9. Zobrazí se zpráva oznamující, že základní disk bude převeden na dynamický disk. Klikněte na **Ano**.
   
    ![Zpráva s dynamickým převodem disku](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. V nástroji Správa disků ověřte, že disk 1 a disk 2 jsou zobrazené jako dynamické zrcadlené svazky. (Funkce**Dynamic** by se měla zobrazit ve sloupci stav a barva pruhu kapacity by se měla změnit na červenou, což značí zrcadlený svazek.) 
    
    ![Zrcadlené dynamické disky správy disků](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Krok 2: použití Snapshot Manager StorSimple ke konfiguraci zálohování
Pomocí následujícího postupu nakonfigurujte dynamický zrcadlený svazek a potom buď spusťte zálohování okamžitě, nebo vytvořte zásadu pro naplánované zálohování.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Konfigurace zálohování dynamického zrcadleného svazku
1. Klikněte na ikonu Snapshot Manager StorSimple na ploše. Zobrazí se okno StorSimple Snapshot Manager. 
2. V podokně **obor** klikněte pravým tlačítkem myši na uzel **svazky** a vyberte možnost **Prohledat svazky**. Po dokončení kontroly by se měl v podokně **výsledků** zobrazit seznam svazků. Dynamický zrcadlený svazek je uveden jako jeden svazek. 
3. V podokně **výsledků** klikněte pravým tlačítkem na dynamický zrcadlený svazek a pak klikněte na **vytvořit skupinu svazků**. 
4. V dialogovém okně **vytvořit skupinu svazků** zadejte název skupiny svazků, přiřaďte k této skupině dynamický zrcadlený svazek a pak klikněte na **OK**. 
5. V podokně **Rozsah** rozbalte uzel **skupiny svazků** . Nová skupina svazků by se měla zobrazit v uzlu  **skupiny svazků** . 
6. Pravým tlačítkem myši klikněte na název skupiny svazků. 
   
   * Pokud chcete spustit interaktivní úlohu zálohování (na vyžádání), klikněte na **vytvořit zálohu**. 
   * Pokud chcete naplánovat automatické zálohování, klikněte na **vytvořit zásadu zálohování**. Na stránce **Obecné** vyberte ze seznamu skupinu svazků. Na stránce **plán** zadejte podrobnosti plánu. Jakmile budete hotovi, klikněte na **Uložit**. 
7. Úlohu zálohování můžete monitorovat při spuštění. V podokně **Rozsah** rozbalte uzel **úlohy** a potom klikněte na možnost **spuštěno**. v podokně **výsledků** se zobrazí podrobnosti o úloze. Po dokončení úlohy zálohování se podrobnosti přenesou na seznam úloh **posledních 24** hodin. 

## <a name="next-steps"></a>Další kroky
* Naučte se [používat Snapshot Manager StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Naučte se [používat Snapshot Manager StorSimple k vytváření a správě skupin svazků](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
