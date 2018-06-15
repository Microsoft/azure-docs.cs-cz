---
title: Uživatelské rozhraní Snapshot Manager zařízení StorSimple | Microsoft Docs
description: Popisuje uživatelské rozhraní Snapshot Manager zařízení StorSimple a vysvětluje, jak použít ho ke správě úloh zálohování a zálohování katalog.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.custom: ''
ms.openlocfilehash: b48c507e38eb7cadff56259f617e336e4efe5708
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927947"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Použití StorSimple Snapshot Manager uživatelské rozhraní pro správu úloh zálohování a zálohování katalogu

## <a name="overview"></a>Přehled
StorSimple Snapshot Manager má intuitivní uživatelské rozhraní, které můžete provést a spravovat zálohy. Tento kurz obsahuje úvod do uživatelského rozhraní a pak vysvětluje, jak používat jednotlivých komponent. Podrobný popis Snapshot Manager zařízení StorSimple, najdete v části [co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Popis konzoly
Pokud chcete zobrazit uživatelské rozhraní, klikněte na ikonu Snapshot Manager zařízení StorSimple na ploše. V okně konzoly se zobrazí, jak je znázorněno na následujícím obrázku.

![Podokna Snapshot Manager zařízení StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

V okně konzoly má pět hlavních prvků. Kliknutím na příslušný odkaz úplný popis jednotlivých prvků.

* [Panel nabídek](#menu-bar) 
* [Panel nástrojů](#tool-bar) 
* [Podokno oboru](#scope-pane) 
* [Podokno výsledků](#results-pane) 
* [Podokna akcí](#actions-pane) 

Kromě toho StorSimple Snapshot Manager podporuje [klávesové navigaci a počet zkratky](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Usnadnění přístupu konzoly
Uživatelské rozhraní StorSimple Snapshot Manager podporuje funkce pro usnadnění přístupu poskytované operačním systémem Windows a Microsoft Management Console (MMC) a také některé klávesové zkratky specifické StorSimple Snapshot Manager. 

* Popis funkce usnadnění systému Windows, přejděte na [klávesové zkratky pro systém Windows](https://support.microsoft.com/kb/126449). 
* Popis funkce pro usnadnění přístupu konzoly MMC, přejděte na [usnadnění pro konzolu MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* Popis funkce pro usnadnění přístupu Snapshot Manager zařízení StorSimple, přejděte na [klávesové navigaci a zkratky](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Panel nabídek
Panel nabídek v horní části okna konzoly obsahuje [soubor](#file-menu), [akce](#action-menu), [zobrazení](#view-menu), [Oblíbené](#favorites-menu), [okno](#window-menu), a [pomoci](#help-menu) nabídky.

Kliknutím na libovolnou položku v řádku nabídek zobrazíte seznam dostupných příkazů v této nabídce. Následující příklad ukazuje **zobrazení** nabídce vybrali v řádku nabídek.

![Vybraná nabídka Zobrazit](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Nabídka Soubor
**Souboru** nabídky obsahuje standardní příkazy Microsoft Management Console (MMC).

#### <a name="menu-access"></a>Přístup do nabídky
Chcete-li zobrazit **soubor** nabídky, klikněte na tlačítko **souboru** v řádku nabídek. Zobrazí se následující nabídky.

![Nabídka Soubor Snapshot Manager zařízení StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazují na **souboru** nabídky.

| Položky nabídky | Popis |
|:--- |:--- |
| Nový |Klikněte na tlačítko **nový** k vytvoření nové konzoly na základě na Snapshot Manager zařízení StorSimple. |
| Otevřenost |Klikněte na tlačítko **otevřete** otevřete stávající konzolu. |
| Uložit |Klikněte na tlačítko **Uložit** uložit aktuální konzolu. |
| Uložit jako |Klikněte na tlačítko **uložit jako** se vytvořit nové, přejmenovat instanci aktuální konzolu. Použití **uložit jako** možnost pro přizpůsobení zobrazení a uložit pro pozdější načtení. Můžete například vytvořit StorSimple Snapshot Manager moduly snap in, které odkazují na konkrétních serverů. |
| Přidat nebo odebrat modul Snap-in |Klikněte na tlačítko **přidat nebo odebrat modul Snap-in** přidat nebo odebrat moduly snap in a k uspořádání uzlů v **oboru** podokně. Další informace, přejděte na [přidat, odebrat a uspořádat moduly Snap in a rozšíření v MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Možnosti |Klikněte na tlačítko **možnosti** změnit ikonu konzoly, zadejte režimy přístupu uživatele a oprávnění nebo odstranění soubory konzoly a zvětšit dostupné místo na disku. |
| Seznam cest k souborům |Klikněte na tlačítko k cestě v seznamu číslem a pak znovu otevřete soubor, který jste právě otevřeli. |
| Ukončení |Klikněte na tlačítko **ukončení** zavřete **souboru** nabídky. |

### <a name="action-menu"></a>Nabídka Akce
Použití **akce** nabídku a vyberte dostupné akce. K dispozici položky jsou závislé na výběr provedete v **oboru** podokně nebo **výsledky** podokně.

#### <a name="menu-access"></a>Přístup do nabídky
Chcete-li zobrazit **akce** nabídky, proveďte jednu z následujících akcí:

* Klikněte pravým tlačítkem na položku v **oboru** podokně nebo **výsledky** podokně.
* Vyberte položku v **oboru** podokně nebo **výsledky** podokně a pak klikněte na tlačítko **akce** v řádku nabídek. 

Například, pokud vyberte nejvyšší uzel v **oboru** podokně a pak klikněte pravým tlačítkem nebo klikněte na **akce** v panelu nabídek, zobrazí se následující nabídky.

![Nabídka Akce Snapshot Manager zařízení StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

**Akce** podokně (na pravé straně konzoly) obsahuje stejný seznam akcí, jako **akce** nabídky. Kromě toho **akce** obsahuje podokně **zobrazení** možnosti nabídky, které vám umožní vytvořit vlastní zobrazení **výsledky** podokně.

![Otevření podokna akce s nabídka Zobrazit](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka obsahuje abecední seznam akcí StorSimple Snapshot Manager. 

* **Akce** sloupci jsou uvedeny akce, které můžete provádět na uzlech a výsledky. 
* **Navigační** sloupec vysvětluje, jak zobrazit odpovídající **akce** nabídky, ve kterém můžete vybrat akci. Některé akce se zobrazí v několika **akce** nabídky. Pro tyto akce, vyberte jednu **navigační** možnost ze seznamu s odrážkami. 
* **Popis** sloupec popisuje, jak použít na každou akci **akce** nabídky nebo podokna akce a vysvětluje, jak funguje.

> [!NOTE]
> **Akce** podokně a **akce** nabídky obsahovat další možnosti, jako například **zobrazení**, **nové okno**,  **Aktualizujte**, **exportovat seznam**, a **pomoci**. Tyto možnosti jsou k dispozici v rámci konzoly MMC a nejsou specifické pro StorSimple Snapshot Manager. Tabulka obsahuje popisy těchto možností.
> 
> 

| Akce | Navigace | Popis |
|:--- |:--- |:--- |
| Ověření |Klikněte na tlačítko **zařízení** uzel a klikněte pravým tlačítkem na zařízení v **výsledky** podokně. |Klikněte na tlačítko **ověřit** k zadání hesla, který jste nakonfigurovali pro zařízení. |
| Klon |Rozbalte položku **zálohování katalogu**, rozbalte položku **cloudových snímků**, klikněte na tlačítko ze zálohy a pak vyberte svazek v **výsledky** podokně. |Klikněte na tlačítko **klon** vytvořit kopii cloudový snímek a uložte ho do umístění, který určíte. |
| Konfigurace zařízení |Klikněte pravým tlačítkem myši **zařízení** uzlu. |Klikněte na tlačítko **nakonfigurovat zařízení** ke konfiguraci jednoho zařízení nebo více zařízení pro připojení k hostiteli systému Windows. |
| Vytvořit zásady zálohování |Proveďte jednu z následujících akcí:<ul><li>Klikněte pravým tlačítkem na **zásady zálohování**.</li><li>Klikněte na tlačítko nebo rozbalte **svazku skupiny**a potom klikněte pravým tlačítkem na skupinu svazku.</li><li>Klikněte na tlačítko nebo rozbalte **katalog zálohování**a potom klikněte pravým tlačítkem na skupinu svazku.</li></ul> |Klikněte na tlačítko **vytvořit zásady zálohování** konfigurace plánované zálohování pro skupinu svazku. |
| Vytvoření svazku skupiny |Proveďte jednu z následujících akcí:<ul><li>Klikněte na tlačítko **svazky** uzel a potom klikněte pravým tlačítkem na svazek v **výsledky** podokně.</li><li>Klikněte pravým tlačítkem myši **svazku skupiny** uzlu.</li></ul> |Klikněte na tlačítko **vytvořit skupinu svazku** přiřadit svazky do skupiny svazku. |
| Odstranění |Klikněte na uzel nebo výsledek (Tato položka je zobrazena na mnoho **akce** nabídky a **akce** podokna.) |Klikněte na tlačítko **odstranit** odstranit uzel nebo výsledek, který jste vybrali. Když se zobrazí dialogové okno potvrzení, potvrďte, nebo zrušte odstraňování. |
| Podrobnosti |Klikněte na tlačítko **zařízení** uzel a potom klikněte pravým tlačítkem na zařízení v **výsledky** podokně. |Klikněte na tlačítko **podrobnosti** zobrazíte podrobnosti konfigurace pro zařízení. |
| Upravit |Klikněte na tlačítko **zásady zálohování**a potom klikněte pravým tlačítkem na zásadu v **výsledky** podokně. |Klikněte na tlačítko **upravit** Chcete-li změnit plán zálohování pro skupinu svazku. |
| Export seznamu |Klikněte na libovolný uzel nebo výsledek (Tato položka je zobrazena na všech **akce** nabídky a **akce** podokna.) |Klikněte na tlačítko **exportovat seznam** k uložení seznamu v souboru hodnot oddělených čárkami (CSV). Tento soubor pak můžete importovat do aplikace tabulkového procesoru k analýze. |
| Nápověda |Klikněte na libovolný uzel nebo výsledek. (Tato položka je zobrazena na všech **akce** nabídky a **akce** podokna.) |Klikněte na tlačítko **pomoci** k online nápovědě v samostatném okně prohlížeče. |
| Nové okno |Klikněte na libovolný uzel nebo výsledek (Tato položka je zobrazena na všech **akce** nabídky a **akce** podokna.) |Klikněte na tlačítko **nové okno** otevřete nové okno StorSimple Snapshot Manager. |
| Aktualizace |Klikněte na libovolný uzel nebo výsledek (Tato položka je zobrazena na všech **akce** nabídky a **akce** podokna.) |Klikněte na tlačítko **aktualizovat** aktualizovat okno aktuálně zobrazený StorSimple Snapshot Manager. |
| Aktualizace zařízení |Klikněte na tlačítko **zařízení** uzel a klikněte pravým tlačítkem na zařízení v **výsledky** podokně. |Klikněte na tlačítko **aktualizace zařízení** synchronizovat konkrétní připojené zařízení s StorSimple Snapshot Manager. |
| Aktualizace zařízení |Klikněte pravým tlačítkem myši **zařízení** uzlu. |Klikněte na tlačítko **aktualizace zařízení** synchronizovat seznam připojených zařízení s StorSimple Snapshot Manager. |
| Prohledat znovu svazky |Klikněte pravým tlačítkem myši **svazky** uzlu. |Klikněte na tlačítko **Prohledat znovu svazky** k aktualizaci seznamu svazků, které se zobrazí v **výsledky** podokně. |
| Obnovení |Rozbalte položku **zálohování katalogu**, rozbalte skupinu svazku, rozbalte položku **místní snímky** nebo **cloudových snímků**a potom klikněte pravým tlačítkem na zálohu. |Klikněte na tlačítko **obnovení** nahradit aktuální data skupiny svazek pomocí dat z vybrané zálohy. |
| Proveďte zálohování |Proveďte jednu z následujících akcí:<ul><li>Rozbalte položku **svazku skupiny**a potom klikněte pravým tlačítkem na skupinu svazku.</li><li>Rozbalte položku **katalog zálohování**a potom klikněte pravým tlačítkem na skupinu svazku.</li></ul> |Klikněte na tlačítko **provést zálohování** pro okamžité spuštění úlohy zálohování. |
| Přepnout importů zobrazení |Klikněte pravým tlačítkem myši na nejvyšší uzel v **oboru** podokně ( **StorSimple Snapshot Manager** uzlu v příkladech). |Klikněte na tlačítko **přepnout importů zobrazení** zobrazit nebo skrýt skupiny svazku a přidružených záloh, které byly naimportovány z řídicího panelu služby StorSimple Manager zařízení. |

### <a name="view-menu"></a>Nabídka Zobrazit
Použití **zobrazení** chcete vytvořit vlastní zobrazení, v nabídce **výsledky** podokně obsah. **Zobrazení** nabídky obsahuje **přidat nebo odebrat sloupce** a **přizpůsobit** možnosti.

#### <a name="menu-access"></a>Přístup do nabídky
Dostanete **zobrazení** v nabídce panelu nebo v nabídce **akce** podokně.

![Nabídka Zobrazit Snapshot Manager zařízení StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazují na **zobrazení** nabídky.

| Položky nabídky | Popis |
|:--- |:--- |
| Přidat nebo odebrat sloupce |Klikněte na tlačítko **přidat nebo odebrat sloupce** přidat nebo odebrat sloupce v **výsledky** podokně. |
| Přizpůsobení |Klikněte na tlačítko **přizpůsobit** zobrazení nebo skrytí položek v okně konzoly StorSimple Snapshot Manager. |

### <a name="favorites-menu"></a>Oblíbené položky nabídky
Použít **Oblíbené** nabídky k přidání, odebrání a organizace zobrazení stránky a úlohy, které používáte. 

#### <a name="menu-access"></a>Přístup do nabídky
Dostanete **Oblíbené** nabídky v řádku nabídek.

![StorSimple Snapshot Manager oblíbených položek nabídky](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazují na **Oblíbené** nabídky.

| Položky nabídky | Popis |
|:--- |:--- |
| Přidat k oblíbeným položkám. |Klikněte na tlačítko **přidat k oblíbeným položkám** aktuální zobrazení přidat do seznamu oblíbených položek. |
| Uspořádání oblíbených položek |Klikněte na tlačítko **uspořádat oblíbené položky** k uspořádání obsahu složky Oblíbené položky. |

### <a name="window-menu"></a>Nabídky okna
Použití **okno** nabídky k přidání a změna uspořádání okna konzoly StorSimple Snapshot Manager.

#### <a name="menu-access"></a>Přístup do nabídky
Dostanete **okno** nabídky v řádku nabídek.

![Nabídka okno Snapshot Manager zařízení StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

Číslovaný seznam v dolní části nabídky ukazuje, že windows, které jsou aktuálně otevřít. Klikněte na libovolné okno v tomto seznamu tím okna do popředí. 

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazují v nabídce okno.

| Položky nabídky | Popis |
|:--- |:--- |
| Nové okno |Klikněte na tlačítko **nové okno** otevřete nové okno konzoly (kromě existující okno). |
| CASCADE |Klikněte na tlačítko **Cascade** zobrazíte otevřít konzolu systému windows ve stylu CSS. |
| Vedle sebe |Klikněte na tlačítko **dlaždici vodorovně** zobrazíte otevřít konzolu systému windows ve formátu dlaždice (nebo mřížky). |
| Seřadí ikony |Pokud máte více konzoly windows otevřete a rozmístěny v ploše je minimalizovat a pak klikněte na **. Seřadit ikony** uspořádejte na vodorovném řádku v dolní části obrazovky. |

### <a name="help-menu"></a>Nabídka Nápověda
Použití **pomoci** nabídky, které chcete zobrazit dostupné online nápovědu pro StorSimple Snapshot Manager a konzoly MMC. Můžete také zobrazit informace o softwaru verze konzoly MMC a Snapshot Manager zařízení StorSimple, které jsou aktuálně nainstalovány ve vašem systému. 

Dostanete **pomoci** nabídky v řádku nabídek. Můžete taky přejít témata nápovědy StorSimple Snapshot Manager z **akce** podokně.

![Nabídka Nápověda Snapshot Manager zařízení StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazují v nabídce Nápověda.

| Položky nabídky | Popis |
|:--- |:--- |
| Nápovědy na StorSimple Snapshot Manager |Klikněte na tlačítko **pomoci na StorSimple Snapshot Manager** k nápovědě Snapshot Manager zařízení StorSimple v samostatném okně. |
| Témata nápovědy |Klikněte na tlačítko **témata nápovědy** otevření online nápovědy konzoly MMC v samostatném okně. |
| Webu TechCenter |Klikněte na tlačítko **webu TechCenter** otevřete domovskou stránku Microsoft TechNet technická Center v samostatném okně. |
| Konzola Microsoft Management Console |Klikněte na tlačítko **o konzoly Microsoft Management Console** zobrazíte, která verze konzoly Microsoft Management Console je nainstalovaná ve vašem systému. |
| O Snapshot Manager zařízení StorSimple |Klikněte na tlačítko **o StorSimple Snapshot Manager** zobrazíte, která verze modulu snap-in je nainstalovaná ve vašem systému. |

## <a name="tool-bar"></a>Panel nástrojů
Panel nástrojů, nacházel pod panelu nabídek obsahuje ikony navigaci a úloh. Každá ikona odkazuje na konkrétní úkol.

### <a name="icon-descriptions"></a>Ikona popisy
Následující tabulka popisuje ikony, které se zobrazují na panelu nástrojů. 

| Ikona | Popis |
|:--- |:--- |
| ![Šipka doleva](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Klikněte na šipku vlevo ikonu vrátit na předchozí stránku. |
| ![Šipka doprava](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Klikněte na tlačítko se šipkou doprava přejděte na další stránku (Pokud na šipku není šedá, akce k dispozici). |
| ![Šipka dolů](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Klikněte na ikonu aktuálním přejdete jednu úroveň ve stromu konzoly ( **oboru** podokně). |
| ![Zobrazit/skrýt strom konzoly](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Klikněte na ikonu Zobrazit či skrýt stromu konzoly můžete zobrazit nebo skrýt **oboru** podokně. |
| ![Export seznamu](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Klikněte na ikonu exportování seznamu exportovat seznam do souboru CSV, který určíte. |
| ![Ikonu nápovědy](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Klikněte na ikonu nápovědy otevření online téma nápovědy konzoly MMC. |
| ![Zobrazit či skrýt podokno akcí](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Klikněte na tlačítko Zobrazit či skrýt **akce** ikonu podokně můžete zobrazit nebo skrýt **akce** podokně. |

## <a name="scope-pane"></a>Podokno oboru
**Oboru** podokno je krajní levé podokno v uživatelském rozhraní StorSimple Snapshot Manager. Obsahuje stromu konzoly (nebo uzel) a je primární navigační mechanismus pro StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Struktura podokně oboru
**Oboru** podokně obsahuje řadu prokliknutelný objekty (uzlů) uspořádány do stromové struktury. 

![Podokno oboru](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Chcete-li rozbalit nebo Sbalit uzel, klikněte na ikonu šipky vedle názvu uzlu.
* K zobrazení stavu nebo obsah uzlu, klikněte na název uzlu. Informace, **výsledky** podokně. 

**Oboru** podokně obsahuje následující uzly: 

* [Uzlu zařízení](#devices-node) 
* [Svazky uzlu](#volumes-node) 
* [Uzel skupiny svazku](#volume-groups-node) 
* [Zálohování uzlu zásady](#backup-policies-node) 
* [Zálohování uzel katalogu](#backup-catalog-node) 
* [Uzel úlohy](#jobs-node) 

### <a name="scope-pane-tasks"></a>Obor podokna úloh
Můžete použít **oboru** podokně na dokončení akce v konkrétním uzlu. Vybrat úlohy, proveďte jednu z následujících:

* Klikněte pravým tlačítkem na uzel a pak vyberte úlohu v nabídce, která se zobrazí.
* Klikněte na uzel a potom klikněte na **akce** v řádku nabídek. Vyberte úkol v nabídce, která se zobrazí.
* Klikněte na uzel a potom vyberte akci, která v **akce** podokně.

Když vyberete uzel a použít některou z těchto metod pro zobrazení seznamu úloh, se zobrazí pouze akce, které lze provést v tomto uzlu.

### <a name="devices-node"></a>Uzlu zařízení
**Zařízení** představuje uzel zařízení StorSimple a virtuální zařízení StorSimple, které jsou připojené k systému StorSimple Snapshot Manager. Vyberte tento uzel k připojení a konfigurace zařízení a importovat jeho přidružené svazky, svazky skupiny a existující záložní kopie. Více zařízení lze připojit na jednoho hostitele.

* Rozbalte uzel, klikněte na ikonu šipky vedle **zařízení**.
* Chcete-li zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem **zařízení** uzlu nebo klikněte pravým tlačítkem na uzly, které se zobrazují v rozšířené zobrazení.
* Chcete-li zobrazit seznam nakonfigurované zařízení, klikněte na tlačítko **zařízení** v **oboru** podokně. Seznam zařízení, spolu s informacemi o každé zařízení se zobrazí v **výsledky** podokně.

### <a name="volumes-node"></a>Svazky uzlu
**Svazky** představuje uzel jednotky, které odpovídají na svazky připojené pro hostitele, včetně těch, které zjištěný prostřednictvím iSCSI a ty zjištěný prostřednictvím zařízení. Tento uzel použijte k zobrazení seznamu dostupných svazků a přiřadit skupiny svazku jednotlivé svazky.

* Rozbalte uzel, klikněte na ikonu šipky vedle **svazky**.
* Chcete-li zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem **svazky** uzlu nebo klikněte pravým tlačítkem na uzly, které se zobrazují v rozšířené zobrazení.
* Chcete-li zobrazit seznam svazků, klikněte na tlačítko **svazky** v **oboru** podokně. Se zobrazí v seznamu svazků, spolu s informacemi o každém svazku **výsledky** podokně.

### <a name="volume-groups-node"></a>Uzel skupiny svazku
Svazek skupin se také označují jako konzistence skupin. Každá skupina svazek je fond svazků týkající se aplikací, který pomáhá zajistit konzistenci aplikace během operace zálohování. Použití **svazku skupiny** uzlu ke konfiguraci těchto skupin a interaktivní zálohování nebo vytváření plánů zálohování. 

* Rozbalte uzel, klikněte na ikonu šipky vedle **svazku skupiny**.
* Zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem **svazku skupiny** uzlu nebo klikněte pravým tlačítkem na uzly, které se zobrazují v rozšířené zobrazení.
* Chcete-li zobrazit seznam skupin svazek, klikněte na tlačítko **svazku skupiny** v **oboru** podokně. Se zobrazí v seznamu skupin svazku, spolu s informacemi o skupině každý svazek **výsledky** podokně.

### <a name="backup-policies-node"></a>Zálohování uzlu zásady
Zásady zálohování jsou plány úloh pro místní a cloudových snímků. Použití **zásady zálohování** uzel k určení, jak často se vytvoří zálohu a jak dlouho zálohování by měl být zachován. 

* Rozbalte uzel, klikněte na ikonu šipky vedle **zásady zálohování**.
* Chcete-li zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem **zásady zálohování** uzlu nebo klikněte pravým tlačítkem na uzly, které se zobrazují v rozšířené zobrazení.
* Chcete-li zobrazit seznam zásady zálohování, klikněte na tlačítko **zásady zálohování** v **oboru** podokně. Seznam zásady zálohování, spolu s informacemi o Každá zásada se zobrazí v **výsledky** podokně.

> [!NOTE]
> Můžete uchovávat maximálně 64 zálohy.


### <a name="backup-catalog-node"></a>Zálohování uzel katalogu
**Katalog zálohování** uzel obsahuje seznamy na místě a odlehlého zálohování svazků Azure StorSimple. Tento uzel je seřazená podle skupiny svazku a kontejneru skupiny pro každý svazek obsahuje samostatné struktury pro místní snímky ( **místní snímek**uzlu s) a cloudových snímků ( **cloudových snímků** uzlu). Po rozbalení kontejneru skupiny pro každý svazek uvádí úspěšné zálohy, které byly provedeny interaktivně nebo nakonfigurované zásady.

* Rozbalte uzel, klikněte na ikonu šipky vedle **katalog zálohování**.
* Chcete-li zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem **katalog zálohování** uzlu nebo klikněte pravým tlačítkem na uzly, které se zobrazují v rozšířené zobrazení.
* Chcete-li zobrazit seznam snímků zálohy, klikněte na tlačítko **zálohování katalogu** v **oboru** podokně. Se zobrazí v seznamu snímků, spolu s informacemi o jednotlivých snímků **výsledky** podokně.

### <a name="local-snapshots-node"></a>Místní uzel snímky
**Místní snímky** uzel obsahuje seznam místních snímků pro skupinu konkrétní svazku. Uzlu je umístěná **katalog zálohování** uzel v **oboru** podokně. Místní snímky jsou v daném okamžiku kopie svazku data, která jsou uložená v zařízení StorSimple v Azure. Tento typ zálohy obvykle, můžete vytvořit a rychle obnovit. Stejně jako místní záložní kopii můžete použít místní snímek.

* Rozbalte uzel, klikněte na ikonu šipky vedle **místní snímky**.
* Chcete-li zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem **místní snímky** uzlu nebo klikněte pravým tlačítkem na uzly, které se zobrazují v rozšířené zobrazení.
* Chcete-li zobrazit seznam místních snímků, klikněte na tlačítko **místní snímky** v **oboru** podokně. Se zobrazí v seznamu snímků, spolu s informacemi o jednotlivých snímků **výsledky** podokně.

### <a name="cloud-snapshots-node"></a>Cloudové snímky uzlu
**Cloudových snímků** uzlu uvádí cloudových snímků pro skupinu konkrétní svazku. Uzlu je umístěná **katalog zálohování** uzel v **oboru** podokně. Cloudové snímky jsou v daném okamžiku kopie svazku data, která jsou uložená v cloudu. Cloudový snímek je stejná jako snímek replikují na jiný, odlehlého úložiště systému. Cloudové snímky jsou obzvláště užitečná ve scénářích zotavení po havárii.

* Rozbalte uzel, klikněte na ikonu šipky vedle **cloudových snímků**.
* Chcete-li zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem **cloudových snímků** uzlu nebo klikněte pravým tlačítkem na uzly, které se zobrazují v rozšířené zobrazení.
* Chcete-li zobrazit seznam cloudových snímků, klikněte na tlačítko **cloudových snímků** v **oboru** podokně. Se zobrazí v seznamu snímků, spolu s informacemi o jednotlivých snímků **výsledky** podokně.

### <a name="jobs-node"></a>Uzel úlohy
**Úlohy** uzel obsahuje informace o naplánované, spuštěné a nedávno dokončené úlohy zálohování. 

* Rozbalte uzel, klikněte na ikonu šipky vedle **úlohy**.
* Chcete-li zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem **úlohy** uzlu nebo klikněte pravým tlačítkem na uzly, které se zobrazují v rozšířené zobrazení.
* Chcete-li zobrazit seznam naplánované úlohy, rozbalte **úlohy** uzel a pak klikněte na tlačítko **naplánovaná**. Seznam dříve nakonfigurované úlohy a informace o každé úloze se zobrazí v **výsledky** podokně. 
* Chcete-li zobrazit seznam nedávno dokončené úlohy, rozbalte **úlohy** uzel a pak klikněte na tlačítko **posledních 24 hodin**. Seznam úloh, které byly dokončeny za posledních 24 hodin se zobrazí v **výsledky** podokně. **Výsledky** podokně také obsahuje informace o každé dokončené úlohy.
* Chcete-li zobrazit seznam úloh, které jsou aktuálně spuštěné, rozbalte **úlohy** uzel a pak klikněte na tlačítko **systémem**. Seznam aktuálně spuštěných úloh a informace o každé úloze se zobrazí v **výsledky** podokně.

## <a name="results-pane"></a>Podokno výsledků
**Výsledky** podokně se prostředním podokně v uživatelském rozhraní StorSimple Snapshot Manager. Obsahuje seznamy a podrobné informace o stavu pro uzel, který jste vybrali v **oboru** podokně.

### <a name="example"></a>Příklad
V následujícím příkladu zobrazíte kliknutím **svazku skupiny** uzel v **oboru** podokně. **Výsledky** podokně se zobrazí seznam skupin svazek s podrobnostmi o každé skupiny.

![Podokno výsledků](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Můžete nakonfigurovat podrobnosti uvedené v **výsledky** podokně: klikněte pravým tlačítkem na uzel v **oboru** podokně klikněte na tlačítko **zobrazení**a pak klikněte na tlačítko **přidat nebo odebrat sloupce** .

## <a name="actions-pane"></a>Podokna akcí
**Akce** podokno je v pravém podokně v uživatelském rozhraní StorSimple Snapshot Manager. Obsahuje operací, které můžete provádět na uzlu, zobrazení nebo data, která jste vybrali v nabídce **oboru** podokně nebo **výsledky** podokně. **Akce** podokně obsahuje tytéž příkazy, jako **akce** nabídky, které jsou dostupné pro položky v **oboru** podokně a **výsledky** podokno. Popis každá akce, najdete v tabulce v **akce** části nabídky.

### <a name="examples"></a>Příklady
K najdete v následujícím příkladu v **oboru** podokně rozbalte **úlohy** uzel a klikněte na tlačítko **naplánovaná**. **Akce** panel zobrazuje dostupné akce **naplánovaná** uzlu.

![Příklad naplánované úlohy podokna akce](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Zobrazíte další možnosti, v **oboru** podokně rozbalte **úlohy** uzel, klikněte na tlačítko **naplánovaná**a pak klikněte na naplánovanou úlohu v **výsledky** podokno. **Akce** panel zobrazuje dostupné akce pro naplánovanou úlohu, jak je znázorněno v následujícím příkladu.

![Příklad použití akce úlohy v podokně Akce](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Navigace klávesnice a zkratky
StorSimple Snapshot Manager umožňuje funkce pro usnadnění přístupu operačního systému Windows a Microsoft Management Console (MMC). Zahrnuje také některé vlastnosti navigace klávesnice a zkratky, které jsou specifické pro Snapshot Manager zařízení StorSimple, jak je popsáno v následujících částech.

* [Navigační klávesy klávesnice](#keyboard-navigation-keys) 
* [Řádek nabídek klávesové zkratky](#menu-bar-shortcut-keys) 
* [Obor podokně klávesové zkratky](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Navigační klávesy klávesnice
Následující tabulka popisuje klíčů, které můžete Navigovat v uživatelském rozhraní StorSimple Snapshot Manager. 

| Navigační klávesy | Akce |
|:--- |:--- |
| Šipka dolů |Přesunout svisle na další položku v nabídce nebo podokně použijte klávesu šipka dolů. |
| Zadejte |Stisknutím klávesy Enter k dokončení akce a poté pokračovat k dalšímu kroku. Například můžete stisknutím klávesy Enter vyberte **Další**, **OK**, nebo **vytvořit**a potom přejděte na další krok průvodce. |
| ESC |Stisknutím klávesy Esc zavřete nabídku nebo se zrušit a zavřete stránku. |
| F1 |Stisknutím klávesy F1 zobrazíte téma nápovědy pro aktuálně aktivní okno. |
| F5 |Stisknutím klávesy F5 aktualizujte uzlu. |
| F6 |Stiskněte klávesu F6 přesunout z **oboru** podokně **výsledky** podokně. |
| F10 |Stisknutím klávesy F10 přejít na řádku nabídek. |
| Klávesy šipka vlevo |Pomocí klávesy šipka doleva vodorovně přesunout z z nabídky panelu na možnost předchozí. Když přesunete na předchozí položku na panelu nabídek, zobrazí se v nabídce Akce (nebo kontextu) pro předchozí položce. |
| Klávesy šipka vpravo |Pomocí klávesy se šipkou doprava vodorovně přesunout z jednoho řádku nabídky na další. Když přesunete na další položku na panelu nabídek, zobrazí se v nabídce Akce (nebo kontextu) pro novou položku. |
| Klávesy TAB |Pomocí klávesy Tab pro přesun na další podokno v konzole nebo na další výběr nebo textové pole na stránce. |
| Klávesa Šipka nahoru |Šipka nahoru použijte při podokna nebo nabídky přejít svisle na předchozí položky. |

### <a name="menu-bar-shortcut-keys"></a>Řádek nabídek klávesové zkratky
Následující tabulka popisuje kombinace klávesových zkratek řádku nabídek. Po stisknutí klávesy klávesové zkratky a otevře se v nabídce, můžete použít nabídku klávesové zkratky (podtržené klíče v nabídce). Další informace o řádku nabídek, přejděte na [řádku nabídek](#menu-bar).

| Zástupce | výsledek | Klávesová zkratka nabídky | výsledek |
|:--- |:--- |:--- |:--- |
| ALT + F |Otevře se **souboru** nabídky. |N |Otevře se nové instance konzoly. |
|  |O |Otevře se **nástroje pro správu** stránky. | |
|  |S |Uloží konzole StorSimple Snapshot Manager. | |
|  |A |Otevře se **uložit jako** stránky. | |
|  |M |Otevře se **přidat nebo odebrat modul Snap-in** stránky. | |
|  |P |Otevře se **možnosti** stránky. | |
|  |H |Otevře se online nápovědy. | |
| ALT + A |Otevře se **akce** nabídky. |I |Možnost zobrazení import vypíná a zapíná. |
|  |W |Otevře se nové konzoly StorSimple Snapshot Manager. | |
|  |F |Aktualizace konzoly StorSimple Snapshot Manager. | |
|  |L |Otevře se **exportovat seznam** stránky. | |
|  |H |Otevře se online nápovědy. | |
| ALT + V |Otevře se **zobrazení** nabídky. |A |Otevře se **přidat nebo odebrat sloupce** stránky. |
|  |U |Otevře se **přizpůsobit zobrazení** stránky. | |
| ALT + O |Otevře se **Oblíbené** nabídky. |A |Otevře se **přidat k oblíbeným položkám** stránky. |
|  |O |Otevře se **uspořádat oblíbené položky** stránky. | |
| ALT + W |Otevře se **okno** nabídky. |N |Otevře další okno StorSimple Snapshot Manager. |
|  |C |Zobrazí všechny otevřené konzoly windows ve stylu CSS. | |
|  |T |Zobrazí všechny otevřené konzoly windows ve tvaru mřížky. | |
|  |I |Seřadí ikony na vodorovném řádku v dolní části obrazovky. | |
| ALT + H |Otevře se **pomoci** nabídky. |H |Otevře se online nápovědy. |
|  |T |Otevře webovou stránku Microsoft TechNet technická Center. | |
|  |A |Otevře se **o konzoly Microsoft Management Console** stránky. | |

### <a name="scope-pane-shortcut-keys"></a>Obor podokně klávesové zkratky
Následující tabulky popisují zástupce kombinace kláves pro každý uzel v **oboru** podokně. 

* [Klávesové zkratky uzlu zařízení](#devices-node-shortcut-keys)
* [Svazky uzlu klávesové zkratky](#volumes-node-shortcut-keys)
* [Svazek skupiny uzlu klávesové zkratky](#volume-groups-node-shortcut-keys)
* [Zálohování zásady uzlu klávesové zkratky](#backup-policies-node-shortcut-keys)
* [Zálohování katalogu uzlu klávesové zkratky](#backup-catalog-node-shortcut-keys)
* [Úlohy uzlu klávesové zkratky](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Klávesové zkratky uzlu zařízení
| Místní nabídky | výsledek |
|:--- |:--- |
| C |Otevře se **nakonfigurovat zařízení** stránky. |
| D |Aktualizuje seznam zařízení a podrobnosti o zařízení. |
| V |Otevře se **zobrazení** nabídky. |
| W |Otevře se nové konzoly StorSimple Snapshot Manager zaměřuje na **podrobnosti** uzlu. |
| F |Aktualizace konzoly StorSimple Snapshot Manager. |
| L |Otevře se **exportovat seznam** stránky. |
| H |Otevře se online nápovědy. |

#### <a name="volumes-node-shortcut-keys"></a>Svazky uzlu klávesové zkratky
| Místní nabídky | výsledek |
|:--- |:--- |
| V |Aktualizuje seznam svazků. |
| V (dvakrát stiskněte) |Otevře se **zobrazení** nabídky. |
| W |Otevře se nové konzoly StorSimple Snapshot Manager zaměřuje na **svazky** uzlu. |
| F |Aktualizace konzoly StorSimple Snapshot Manager. |
| L |Otevře se **exportovat seznam** stránky. |
| H |Otevře se online nápovědy. |

#### <a name="volume-groups-node-shortcut-keys"></a>Svazek skupiny uzlu klávesové zkratky
| Místní nabídky | výsledek |
|:--- |:--- |
| G |Otevře se **vytvořte skupinu svazku** stránky. |
| V |Otevře se **zobrazení** nabídky. |
| W |Otevře se nové konzoly StorSimple Snapshot Manager zaměřuje na **svazku skupiny** uzlu. |
| F |Aktualizace konzoly StorSimple Snapshot Manager. |
| L |Otevře se **exportovat seznam** stránky. |
| H |Otevře se online nápovědy. |

#### <a name="backup-policies-node-shortcut-keys"></a>Zálohování zásady uzlu klávesové zkratky
| Místní nabídky | výsledek |
|:--- |:--- |
| B |Otevře se **vytvořit zásadu** stránky. |
| V |Otevře se **zobrazení** nabídky. |
| W |Otevře se nové konzoly StorSimple Snapshot Manager zaměřuje na **svazku skupiny** uzlu. |
| F |Aktualizace konzoly StorSimple Snapshot Manager. |
| L |Otevře se ** exportovat seznam ** stránky. |
| H |Otevře se online nápovědy. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Zálohování katalogu uzlu klávesové zkratky
| Místní nabídky | výsledek |
|:--- |:--- |
| W |Otevře se nové konzoly StorSimple Snapshot Manager zaměřuje na **svazku skupiny** uzlu. |
| F |Aktualizace konzoly StorSimple Snapshot Manager. |
| H |Otevře se online nápovědy. |

#### <a name="jobs-node-shortcut-keys"></a>Úlohy uzlu klávesové zkratky
| Místní nabídky | výsledek |
|:--- |:--- |
| V |Otevře se **zobrazení** nabídky. |
| W |Otevře se nové konzoly StorSimple Snapshot Manager zaměřuje na **úlohy** uzlu. |
| F |Aktualizace konzoly StorSimple Snapshot Manager. |
| L |Otevře se **exportovat seznam** stránky. |
| H |Otevře online nápovědy |

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [použít ke správě vašeho řešení StorSimple Snapshot Manager zařízení StorSimple](storsimple-snapshot-manager-admin.md).
* Zjistěte, jak [použít k připojení a správě zařízení StorSimple Snapshot Manager](storsimple-snapshot-manager-manage-devices.md).

