---
title: Snapshot Manager zařízení StorSimple a svazky | Microsoft Docs
description: Popisuje, jak pomocí modulu snap-in konzoly MMC StorSimple Snapshot Manager můžete zobrazit a spravovat svazků a konfigurace zálohování.
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
ms.openlocfilehash: 2c0b211bced99d272a73a7b018a22f99d8d58aa9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876989"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Pomocí StorSimple Snapshot Manager můžete zobrazit a spravovat svazky
## <a name="overview"></a>Přehled
Můžete použít StorSimple Snapshot Manager **svazky** uzlu (na **oboru** podokně) vybrat svazky a zobrazit informace o nich. Svazky jsou uvedené jako jednotky, které odpovídají pro svazky připojené hostitelem. **Svazky** uzlu se zobrazuje místní svazky a svazek typy, které jsou podporuje StorSimple, včetně svazků o zjištěných prostřednictvím iSCSI a zařízení. 

Další informace o podporovaných svazky, přejděte na [podporu pro více typů svazku](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Seznam svazků v podokně výsledků](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

**Svazky** uzlu také umožňuje prohledat znovu nebo odstraňte svazky po StorSimple Snapshot Manager zjistí. 

Tento kurz vysvětluje, jak můžete připojit, inicializovat a formátování svazků a pak použít StorSimple Snapshot Manager do:

* Zobrazení informací o svazcích 
* Odstraňte svazky
* Prohledat znovu svazky 
* Nakonfigurujte vytvoření základního svazku a vytvořte zálohu
* Konfigurace dynamické zrcadleného svazku a vytvořte zálohu

> [!NOTE]
> Všechny **svazku** jsou také k dispozici v uzlu akce **akce** podokně.
> 
> 

## <a name="mount-volumes"></a>Připojit svazky
Následující postup použijte k připojení, inicializace a formátování svazků zařízení StorSimple. Tento postup používá Správa disků, systémový nástroj pro správu pevné disky a odpovídající svazků nebo oddílů. Další informace o nástroji Správa disků, přejděte na [Správa disků](https://technet.microsoft.com/library/cc770943.aspx) na webu Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Chcete-li připojit svazky
1. V hostitelském počítači spusťte iniciátor iSCSI společnosti Microsoft.
2. Zadejte jednu z IP adresy rozhraní jako cílový portál nebo zjišťování IP adresu a připojte k zařízení. Až se zařízení připojí, svazky budou mít přístup do systému Windows. Další informace o použití iniciátoru iSCSI společnosti Microsoft, přejděte k části "Připojení k cílovému zařízení iSCSI" v [instalace a konfigurace Microsoft iSCSI Initiator][1].
3. Ke spuštění nástroje Správa disků, použijte některý z následujících možností:
   
   * Zadejte Diskmgmt.msc v **spustit** pole.
   * Spusťte správce serveru, rozbalte **úložiště** uzel a potom vyberte **Správa disků**.
   * Spustit **nástroje pro správu**, rozbalte **Správa počítače** uzel a potom vyberte **Správa disků**. 
     
     > [!NOTE]
     > Chcete-li spustit nástroj Správa disků, je nutné použít oprávnění správce.
     > 
     > 
4. Svazcích proveďte online:
   
   1. V nástroji Správa disků klikněte pravým tlačítkem na jakýkoli svazek označen **Offline**.
   2. Klikněte na tlačítko **aktivovat Disk**. Disk by měl být označen **Online** po aktivaci.
5. Inicializace svazcích:
   
   1. Klikněte pravým tlačítkem na zjištěné svazky.
   2. V nabídce vyberte **inicializovat Disk**.
   3. V **inicializovat Disk** dialogové okno Vyberte disky, které chcete inicializovat a pak klikněte na tlačítko **OK**.
6. Formát jednoduchými svazky:
   
   1. Klikněte pravým tlačítkem na svazku, který chcete formátovat.
   2. V nabídce vyberte **nový jednoduchý svazek**.
   3. Pomocí Průvodce Nový jednoduchý svazek naformátování svazku:
      
      * Zadejte velikost svazku.
      * Zadejte písmeno jednotky.
      * Vyberte systému souborů NTFS.
      * Zvolte velikost alokační jednotky 64 kB.
      * Proveďte rychlé formátování.
7. Formátování oddílu více svazků. Pokyny najdete v sekci "Oddíly a svazky" [implementace Správa disků](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Zobrazit informace o svých svazků
Následující postup použijte k zobrazení informací o místní a svazků Azure StorSimple.

#### <a name="to-view-volume-information"></a>Chcete-li zobrazit informace o svazku
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager. 
2. V **oboru** podokně klikněte **svazky** uzlu. Místní a připojené svazky, včetně všech svazků Azure StorSimple, objeví se v **výsledky** podokně. Sloupce v **výsledky** podokně se dají konfigurovat. (Klikněte pravým tlačítkem myši **svazky** uzlu, vyberte **zobrazení**a potom vyberte **přidat nebo odebrat sloupce**.)
   
    ![Konfigurace sloupců](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Sloupec výsledků | Popis |
   |:--- |:--- |
   |  Name (Název) |**Název** sloupec obsahuje písmeno jednotky přiřazené každý zjištěný svazek. |
   |  Zařízení |**Zařízení** sloupec obsahuje IP adresu zařízení připojené k hostitelskému počítači. |
   |  Název svazku zařízení |**Název svazku zařízení** sloupec obsahuje název zařízení svazek, ke kterému patří vybraného svazku. Toto je název svazku, který je definován na portálu Azure pro tento konkrétní svazek. |
   |  Cesty přístupu |**Cesty přístupu** sloupec zobrazuje cestu přístup ke svazku. Toto je písmeno jednotky nebo přípojný bod, kdy svazek je přístupný v hostitelském počítači. |

## <a name="delete-a-volume"></a>Odstranění svazku
Pomocí následujícího postupu můžete odstranit ze zařízení StorSimple Snapshot Manager svazku.

> [!NOTE]
> Svazek nelze odstranit, pokud je součástí žádné skupiny svazku. (Možnost odstranění není k dispozici pro svazky, které jsou členy skupiny svazku.) Je nutné odstranit celý svazek skupiny k odstranění svazku.

#### <a name="to-delete-a-volume"></a>Odstranění svazku
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně klikněte **svazky** uzlu. 
3. V **výsledky** podokně klikněte pravým tlačítkem na svazku, který chcete odstranit.
4. V nabídce klikněte na tlačítko **odstranit**. 
   
    ![Odstranění svazku](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. **Odstranit svazek** zobrazí se dialogové okno. Typ **potvrdit** textového pole a pak klikněte na **OK**.
6. Ve výchozím nastavení StorSimple Snapshot Manager zálohuje svazek před odstraněním. Toto opatření vám může chránit před ztrátou dat, pokud nebyla záměrná odstranění. Zobrazí StorSimple Snapshot Manager **automatické snímku** zprávu o průběhu během zálohování svazku. 
   
    ![Zpráva automatické snímku](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Prohledat znovu svazky
Pomocí následujícího postupu znovu prohledá svazky připojené k systému StorSimple Snapshot Manager.

#### <a name="to-rescan-the-volumes"></a>Jestliže chcete prohledat svazky
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně klikněte pravým tlačítkem na **svazky**a potom klikněte na **Prohledat znovu svazky**.
   
    ![Prohledat znovu svazky](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Tento postup synchronizuje seznam svazků s StorSimple Snapshot Manager. Všechny změny, jako je například nové svazky nebo odstraněné svazky, se zobrazí ve výsledcích.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurace a zálohovat vytvoření základního svazku
Použijte následující postup pro konfiguraci zálohování základního svazku a spustit okamžitě nebo vytvořte zásadu pro plánované zálohy.

### <a name="prerequisites"></a>Požadavky
Než začnete:

* Ujistěte se, zda jsou správně nakonfigurovány StorSimple zařízení a hostitele počítače. Další informace, přejděte na [nasazení místního zařízení StorSimple](storsimple-deployment-walkthrough-u2.md).
* Instalace a konfigurace zařízení StorSimple Snapshot Manager. Další informace, přejděte na [nasazení zařízení StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Pro konfiguraci zálohování základního svazku
1. Vytvořte základní svazek v zařízení StorSimple.
2. Připojení, inicializace a formátování svazku, jak je popsáno v [připojit svazky](#mount-volumes). 
3. Klikněte na ikonu Snapshot Manager zařízení StorSimple na ploše. Zobrazí se okno StorSimple Snapshot Manager. 
4. V **oboru** podokně klikněte pravým tlačítkem myši **svazky** uzel a potom vyberte **Prohledat znovu svazky**. Po dokončení kontroly by se zobrazit seznam svazků v **výsledky** podokně. 
5. V **výsledky** podokně klikněte pravým tlačítkem na svazek a potom vyberte **vytvořit skupinu svazku**. 
   
    ![Vytvoření svazku skupiny](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. V **vytvořit skupinu svazku** dialogové okno, zadejte název pro skupinu svazku, přiřadit svazky a pak klikněte na tlačítko **OK**.
7. V **oboru** podokně rozbalte **svazku skupiny** uzlu. V dialogovém okně do nové skupiny svazku **svazku skupiny** uzlu. 
8. Klikněte pravým tlačítkem na název skupiny svazku.
   
   * Chcete-li spustit úlohu zálohování interaktivní (na vyžádání), klikněte na tlačítko **provést zálohování**. 
   * Chcete-li naplánovat automatické zálohování, klikněte na tlačítko **vytvořit zásady zálohování**. Na **Obecné** stránky, vyberte skupinu svazek ze seznamu. Na **plán** zadejte podrobnosti plánu. Až budete hotovi, klikněte na tlačítko **OK**. 
9. Potvrďte, že byla spuštěna úloha zálohování, rozbalte **úlohy** uzlu v **oboru** podokně a pak klikněte na tlačítko **systémem** uzlu. Seznam aktuálně spuštěných úloh se zobrazí v **výsledky** podokně. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurace a zálohovat dynamické zrcadleného svazku
Proveďte následující kroky pro konfiguraci zálohování dynamické zrcadleného svazku:

* Krok 1: Použití nástroje Správa disků k vytvoření dynamických zrcadleného svazku. 
* Krok 2: Použití StorSimple Snapshot Manager ke konfiguraci zálohování.

### <a name="prerequisites"></a>Požadavky
Než začnete:

* Ujistěte se, zda jsou správně nakonfigurovány StorSimple zařízení a hostitele počítače. Další informace, přejděte na [nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Instalace a konfigurace zařízení StorSimple Snapshot Manager. Další informace, přejděte na [nasazení zařízení StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Nakonfigurujte dva svazky v zařízení StorSimple. (V příkladech jsou k dispozici svazky **Disk 1** a **disku 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Krok 1: Správa disků použijte vytvoření dynamické zrcadleného svazku
Správa disků je systémový nástroj pro správu pevné disky a svazky nebo oddíly, které obsahují. Další informace o nástroji Správa disků, přejděte na [Správa disků](https://technet.microsoft.com/library/cc770943.aspx) na webu Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Chcete-li vytvořit dynamické zrcadleného svazku
1. Ke spuštění nástroje Správa disků, použijte některý z následujících možností: 
   
   * Otevřete **spustit** zadejte **Diskmgmt.msc**, a stiskněte klávesu Enter.
   * Spusťte správce serveru, rozbalte **úložiště** uzel a potom vyberte **Správa disků**. 
   * Spustit **nástroje pro správu**, rozbalte **Správa počítače** uzel a potom vyberte **Správa disků**. 
2. Ujistěte se, že máte dva svazky, které jsou k dispozici v zařízení StorSimple. (V příkladu jsou k dispozici svazky **Disk 1** a **disku 2**.) 
3. V okně Správa disků, v pravém sloupci v dolním podokně klikněte pravým tlačítkem na **Disk 1** a vyberte **nový svazek zrcadleny**. 
   
    ![Nový zrcadlený svazek](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Na **nový svazek zrcadleny** stránka průvodce, klikněte na tlačítko **Další**.
5. Na **vyberte disky** vyberte **disku 2** v **vybrané** podokně klikněte na tlačítko **přidat**a pak klikněte na tlačítko **Další**. 
6. Na **přiřadit písmeno jednotky nebo cesta** stránky, přijměte výchozí hodnoty a pak klikněte na tlačítko **Další**. 
7. Na **formátování svazku** stránky v **velikost alokační jednotky** vyberte **64 tisíc**. Vyberte **rychlé formátování** zaškrtněte políčko a potom klikněte na **Další**. 
8. Na **dokončení nový zrcadlený svazek** stránka, zkontrolujte nastavení a pak klikněte na **Dokončit**. 
9. Zobrazí se zpráva označující, že bude základního disku převést na dynamický disk. Klikněte na **Ano**.
   
    ![Zpráva pro převod dynamického disku](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. Ve správě disků ověřte, jestli Disk 1 a 2 disku se zobrazují jako dynamické zrcadlové svazky. (**Dynamické** by se měla objevit ve sloupci Stav a barvu panelu kapacity by se měl změnit na červený, která určuje zrcadleného svazku.) 
    
    ![Dynamické disky správu zrcadlení disku](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Krok 2: Použití StorSimple Snapshot Manager ke konfiguraci zálohování
Chcete-li nakonfigurovat dynamické zrcadlený svazek a spustit okamžitě nebo vytvořte zásadu pro plánované zálohy použijte následující postup.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Pro konfiguraci zálohování dynamické zrcadleného svazku
1. Klikněte na ikonu Snapshot Manager zařízení StorSimple na ploše. Zobrazí se okno StorSimple Snapshot Manager. 
2. V **oboru** podokně klikněte pravým tlačítkem myši **svazky** uzel a vyberte možnost **Prohledat znovu svazky**. Po dokončení kontroly by se zobrazit seznam svazků v **výsledky** podokně. Dynamické zrcadlený svazek je uveden jako jeden svazek. 
3. V **výsledky** podokně klikněte pravým tlačítkem na dynamické zrcadleného svazku a pak klikněte na **vytvořit skupinu svazku**. 
4. V **vytvořit skupinu svazku** dialogové okno, zadejte název pro skupinu svazku, přiřadit dynamické zrcadleného svazku do této skupiny a pak klikněte na tlačítko **OK**. 
5. V **oboru** podokně rozbalte **svazku skupiny** uzlu. V dialogovém okně do nové skupiny svazku **svazku skupiny** uzlu. 
6. Klikněte pravým tlačítkem na název skupiny svazku. 
   
   * Chcete-li spustit úlohu zálohování interaktivní (na vyžádání), klikněte na tlačítko **provést zálohování**. 
   * Chcete-li naplánovat automatické zálohování, klikněte na tlačítko **vytvořit zásady zálohování**. Na **Obecné** skupině svazku vyberte ze seznamu. Na **plán** zadejte podrobnosti plánu. Až budete hotovi, klikněte na tlačítko **OK**. 
7. Úloha zálohování můžete monitorovat při jeho spuštění. V **oboru** podokně rozbalte **úlohy** uzel a pak klikněte na tlačítko **systémem**, podrobnosti úlohy se zobrazí v **výsledky** podokně. Po dokončení úlohy zálohování podrobnosti přenesou **posledních 24** čas úlohy seznamu. 

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [použít ke správě vašeho řešení StorSimple Snapshot Manager zařízení StorSimple](storsimple-snapshot-manager-admin.md).
* Zjistěte, jak [StorSimple Snapshot Manager použít k vytvoření a Správa skupin svazku](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
