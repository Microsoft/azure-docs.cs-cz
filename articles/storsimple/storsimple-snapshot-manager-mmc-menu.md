---
title: Akce nabídky konzoly MMC správce snímků StorSimple | Dokumenty společnosti Microsoft
description: Popisuje způsob použití standardních akcí nabídky konzoly MMC (MMC) ve Správci snímků StorSimple.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: ced075395c4f2a51308d5d3b0cbe4c0153a17816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931468"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Použití akcí nabídky konzoly MMC ve Správci snímků StorSimple

## <a name="overview"></a>Přehled
Ve Správci snímků StorSimple se zobrazí následující akce uvedené ve všech nabídkách akcí a ve všech variantách podokna **Akce.**

* Zobrazení
* New Window from Here 
* Obnovení 
* Exportovat seznam 
* Nápověda 

Tyto akce jsou součástí konzoly MMC (MMC) a nejsou specifické pro Správce snímků StorSimple. Tento kurz popisuje tyto akce a vysvětluje, jak použít každý z nich ve Správci snímků StorSimple.

## <a name="view"></a>Zobrazení
Pomocí možnosti **Zobrazení** můžete změnit zobrazení podokna **Výsledky** a změnit zobrazení okna konzoly. 

#### <a name="to-change-the-results-pane-view"></a>Změna zobrazení podokna Výsledky
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klikněte pravým tlačítkem myši na libovolný uzel nebo rozbalte uzel a klikněte pravým tlačítkem myši na položku v podokně **Výsledky** a potom klikněte na možnost **Zobrazit.** 
3. Pokud chcete přidat nebo odebrat sloupce, které se zobrazí v podokně **Výsledky,** klikněte na **Přidat nebo odebrat sloupce**. Zobrazí se dialogové okno **Přidat nebo odebrat sloupce.**
   
    ![Přidání nebo odebrání sloupců z podokna Výsledky](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Vyplňte formulář takto:
   
   * Vyberte položky ze seznamu **Dostupné** sloupce a kliknutím na **Přidat** je přidejte do seznamu **Zobrazené sloupce.** 
   * Klikněte na položky v seznamu **Zobrazené sloupce** a kliknutím na **Odebrat** je odeberte ze seznamu. 
   * Vyberte položku v seznamu **Zobrazené** sloupce a kliknutím na **Přesunout nahoru** nebo **Přesunout dolů** přesuňte položku v seznamu nahoru nebo dolů. 
   * Kliknutím na **Obnovit výchozí se** vrátíte do výchozí konfigurace podokna **Výsledky.** 
5. Po dokončení výběrů klepněte na tlačítko **OK**. 

#### <a name="to-change-the-console-window-view"></a>Změna zobrazení okna konzoly
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klepněte pravým tlačítkem myši na libovolný uzel, klikněte na **příkaz Zobrazit**a potom klikněte na **příkaz Přizpůsobit**. Zobrazí se dialogové okno **Přizpůsobit.**
   
    ![Přizpůsobení okna konzoly](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Zaškrtnutím nebo zrušením zaškrtnutí políček zobrazíte nebo skryjete položky v okně konzoly. Po dokončení výběrů klepněte na tlačítko **OK**.

## <a name="new-window-from-here"></a>New Window from Here
K otevření nového okna konzoly můžete použít možnost **Nové okno ze zde.**

#### <a name="to-open-a-new-console-window"></a>Otevření nového okna konzoly
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klepněte pravým tlačítkem myši na libovolný uzel a potom klikněte na **příkaz Nové okno odtud**. 
   
    Zobrazí se nové okno zobrazující pouze vybraný obor. Pokud například klepnete pravým tlačítkem myši na uzel **Zásady zálohování,** zobrazí se v novém okně pouze uzel **Zásady zálohování** v podokně **Obor** a seznam definovaných zásad zálohování v podokně **Výsledky.** Prohlédněte si následující příklad.
   
    ![New Window from Here](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Obnovení
K aktualizaci okna konzoly můžete použít akci **Aktualizovat.**

#### <a name="to-update-the-console-window"></a>Aktualizace okna konzoly
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klepněte pravým tlačítkem myši na libovolný uzel nebo rozbalte uzel a klepněte pravým tlačítkem myši na položku v podokně **Výsledky** a potom klepněte na příkaz **Aktualizovat**. 

## <a name="export-list"></a>Exportovat seznam
Akci **Exportovat seznam** můžete použít k uložení seznamu do souboru csv (s čárkou). Můžete například exportovat seznam zásad zálohování nebo katalog záloh. Soubor CSV pak můžete importovat do tabulkové aplikace pro analýzu.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Uložení seznamu do souboru csv odděleného čárkami
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple. 
2. V podokně **Obor** klepněte pravým tlačítkem myši na libovolný uzel nebo rozbalte uzel a klepněte pravým tlačítkem myši na položku v podokně **Výsledky** a potom klepněte na příkaz **Exportovat seznam**. 
3. Zobrazí se dialogové okno **Exportovat seznam.** Vyplňte formulář takto: 
   
   1. Do pole **Název souboru** zadejte název souboru CSV nebo klepněte na šipku a vyberte z rozevíracího seznamu.
   2. V poli **Uložit jako typ** klikněte na šipku a v rozevíracím seznamu vyberte typ souboru.
   3. Chcete-li uložit pouze vybrané položky, zaškrtněte řádky a klepněte na políčko **Uložit pouze vybrané řádky.** Chcete-li uložit všechny exportované seznamy, zrušte zaškrtnutí políčka **Uložit pouze vybrané řádky.**
   4. Klikněte na **Uložit**.
      
      ![Exportovat seznam jako soubor hodnot oddělených čárkami](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Nápověda
Pomocí nabídky **Nápověda** můžete zobrazit dostupnou online nápovědu pro Správce snímků StorSimple a konzolu MMC.

#### <a name="to-view-available-online-help"></a>Zobrazení dostupné online nápovědy
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klepněte pravým tlačítkem myši na libovolný uzel nebo rozbalte uzel a klepněte pravým tlačítkem myši na položku v podokně **Výsledky** a potom klepněte na příkaz **Nápověda**. 

## <a name="next-steps"></a>Další kroky
* Další informace o [uživatelském rozhraní Správce snímků StorSimple](storsimple-use-snapshot-manager.md).
* Další informace o [použití Správce snímků StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).

