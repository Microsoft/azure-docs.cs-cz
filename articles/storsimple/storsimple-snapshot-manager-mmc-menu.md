---
title: Akce nabídky StorSimple Snapshot Manager MMC | Microsoft Docs
description: Popisuje, jak používat standardní akce nabídky konzoly Microsoft Management Console (MMC) v StorSimple Snapshot Manager.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 01064c3668b673baea7aedd9a65c92b03c48dc10
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055999"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Použití akcí v nabídce MMC v StorSimple Snapshot Manager

## <a name="overview"></a>Přehled
V StorSimple Snapshot Manager uvidíte následující akce uvedené na všech nabídkách akce a všech variacích podokna **Akce** .

* Zobrazit
* New Window from Here 
* Aktualizovat 
* Exportovat seznam 
* Nápověda 

Tyto akce jsou součástí konzoly Microsoft Management Console (MMC) a nejsou specifické pro StorSimple Snapshot Manager. Tento kurz popisuje tyto akce a vysvětluje, jak je používat StorSimple Snapshot Manager.

## <a name="view"></a>Zobrazit
Pomocí možnosti **zobrazení** můžete změnit zobrazení podokna **výsledků** a změnit zobrazení okna konzoly. 

#### <a name="to-change-the-results-pane-view"></a>Změna zobrazení podokna výsledků
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** klikněte pravým tlačítkem myši na libovolný uzel nebo rozbalte uzel a klikněte pravým tlačítkem myši na položku v podokně **výsledků** a poté klikněte na možnost **zobrazení** . 
3. Chcete-li přidat nebo odebrat sloupce, které se zobrazí v podokně **výsledků** , klikněte na tlačítko **Přidat nebo odebrat sloupce**. Zobrazí se dialogové okno **Přidat nebo odebrat sloupce** .
   
    ![Přidat nebo odebrat sloupce z podokna výsledků](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Vyplňte formulář následujícím způsobem:
   
   * Vyberte položku ze seznamu **dostupné** sloupce a kliknutím na tlačítko **Přidat** je přidejte do seznamu **zobrazené sloupce** . 
   * Klikněte na položku položky v seznamu **zobrazené sloupce** a kliknutím na tlačítko **Odebrat** je odeberte ze seznamu. 
   * Vyberte položku v seznamu **zobrazené** sloupce **a kliknutím na tlačítko nahoru nebo** dolů **přesuňte** položku v seznamu nahoru nebo dolů. 
   * Klikněte na **Obnovit výchozí hodnoty** a vraťte se k výchozí konfiguraci podokna **výsledků** . 
5. Až budete s vybranými možnostmi hotovi, klikněte na **OK**. 

#### <a name="to-change-the-console-window-view"></a>Změna zobrazení okna konzoly
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** klikněte pravým tlačítkem na libovolný uzel, klikněte na **Zobrazit**a pak klikněte na **přizpůsobit**. Zobrazí se dialogové okno **přizpůsobit** .
   
    ![Přizpůsobení okna konzoly](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Zaškrtnutím nebo zrušením zaškrtnutí políček zobrazíte nebo skryjete položky v okně konzoly. Až budete s vybranými možnostmi hotovi, klikněte na **OK**.

## <a name="new-window-from-here"></a>New Window from Here
Nové okno konzoly můžete otevřít pomocí **nového okna z možnosti zde** .

#### <a name="to-open-a-new-console-window"></a>Otevření nového okna konzoly
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** klikněte pravým tlačítkem na libovolný uzel a pak klikněte na **nové okno z tohoto místa**. 
   
    Zobrazí se nové okno zobrazující pouze vybraný obor. Pokud například kliknete pravým tlačítkem na uzel **zásady zálohování** , v novém okně se zobrazí pouze uzel **zásady zálohování** v podokně **Rozsah** a seznam definovaných zásad zálohování v podokně **výsledků** . Prohlédněte si následující příklad.
   
    ![New Window from Here](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Aktualizovat
Pomocí akce **aktualizovat** můžete aktualizovat okno konzoly.

#### <a name="to-update-the-console-window"></a>Aktualizace okna konzoly
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **Rozsah** klikněte pravým tlačítkem na libovolný uzel nebo rozbalte uzel, klikněte pravým tlačítkem myši na položku v podokně **výsledků** a pak klikněte na tlačítko **aktualizovat**. 

## <a name="export-list"></a>Exportovat seznam
Pomocí akce **seznam exportu** můžete uložit seznam do souboru s hodnotami oddělenými čárkou (CSV). Můžete například exportovat seznam zásad zálohování nebo katalog záloh. Pak můžete soubor CSV importovat do aplikace v tabulce pro účely analýzy.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Uložení seznamu v souboru s hodnotami oddělenými čárkou (CSV)
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager. 
2. V podokně **Rozsah** klikněte pravým tlačítkem myši na libovolný uzel nebo rozbalte uzel, klikněte pravým tlačítkem myši na položku v podokně **výsledků** a potom klikněte na položku **Exportovat seznam**. 
3. Zobrazí se dialogové okno **Exportovat seznam** . Vyplňte formulář následujícím způsobem: 
   
   1. Do pole **název souboru** zadejte název souboru CSV nebo klikněte na šipku, kterou chcete vybrat v rozevíracím seznamu.
   2. V poli **Uložit jako typ** klikněte na šipku a v rozevíracím seznamu vyberte typ souboru.
   3. Chcete-li uložit pouze vybrané položky, vyberte řádky a zaškrtněte políčko **Uložit pouze vybrané řádky** . Chcete-li uložit všechny exportované seznamy, zrušte zaškrtnutí políčka **Uložit pouze vybrané řádky** .
   4. Klikněte na **Uložit**.
      
      ![Exportovat seznam jako soubor hodnot oddělených čárkami](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Nápověda
Pomocí nabídky **help** můžete zobrazit dostupnou online nápovědu pro StorSimple Snapshot Manager a konzolu MMC.

#### <a name="to-view-available-online-help"></a>Zobrazení dostupné online nápovědě
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **Rozsah** klikněte pravým tlačítkem na libovolný uzel nebo rozbalte uzel, klikněte pravým tlačítkem myši na položku v podokně **výsledků** a pak klikněte na tlačítko **help**. 

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [uživatelském rozhraní StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
* Přečtěte si další informace o [použití Snapshot Manager StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).

