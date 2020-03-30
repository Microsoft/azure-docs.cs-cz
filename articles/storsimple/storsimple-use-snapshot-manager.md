---
title: Uživatelské rozhraní Správce snímků StorSimple | Dokumenty společnosti Microsoft
description: Popisuje uživatelské rozhraní Správce snímků StorSimple a vysvětluje, jak jej použít ke správě úloh zálohování a katalogu záloh.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.custom: ''
ms.openlocfilehash: 56771d2e62289485017f34c6a9ab26e1d53610ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933977"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Použití uživatelského rozhraní Správce snímků StorSimple ke správě úloh zálohování a katalogu záloh

## <a name="overview"></a>Přehled
Správce snímků StorSimple má intuitivní uživatelské rozhraní, které můžete použít k pořizování a správě záloh. Tento kurz poskytuje úvod do uživatelského rozhraní a pak vysvětluje, jak používat každou součást. Podrobný popis Správce snímků StorSimple naleznete v tématu [Co je Správce snímků StorSimple?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Popis konzoly
Chcete-li zobrazit uživatelské rozhraní, klepněte na ikonu Správce snímků StorSimple na ploše. Zobrazí se okno konzoly, jak je znázorněno na následujícím obrázku.

![Podokna Správce snímků StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

Okno konzoly má pět hlavních prvků. Kliknutím na příslušný odkaz zobrazíte úplný popis každého prvku.

* [Nabídek](#menu-bar) 
* [Panel nástrojů](#tool-bar) 
* [Podokno Obor](#scope-pane) 
* [Podokno Výsledky](#results-pane) 
* [Podokno Akce](#actions-pane) 

Správce snímků StorSimple navíc podporuje [navigaci pomocí klávesnice a několik klávesových zkratek](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Usnadnění přístupu konzoly
Uživatelské rozhraní Správce snímků StorSimple podporuje funkce usnadnění poskytované operačním systémem Windows a konzolou MMC (Microsoft Management Console) a také některé klávesové zkratky specifické pro Správce snímků StorSimple. 

* Popis funkcí usnadnění systému Windows naleznete v [tématu Klávesové zkratky pro Windows](https://support.microsoft.com/kb/126449). 
* Popis funkcí usnadnění mmc naleznete v tématu [Usnadnění pro konzolu MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* Popis funkcí usnadnění správce snímků StorSimple naleznete v [části Navigace pomocí klávesnice a klávesové zkratky](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Nabídek
Panel nabídek v horní části okna konzoly obsahuje nabídky [Soubor](#file-menu), [Akce](#action-menu), [Zobrazení](#view-menu), [Oblíbené položky](#favorites-menu), [Okno](#window-menu)a [Nápověda.](#help-menu)

Klepnutím na libovolnou položku na řádku nabídek zobrazíte seznam dostupných příkazů v této nabídce. Následující příklad ukazuje nabídku **Zobrazení** vybranou na řádku nabídek.

![Vybraná nabídka Zobrazit](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Nabídka Soubor
Nabídka **Soubor** obsahuje standardní příkazy konzoly MMC (MMC).

#### <a name="menu-access"></a>Přístup k nabídce
Chcete-li zobrazit nabídku **Soubor,** klepněte na panelu nabídek na **položku Soubor.** Zobrazí se následující nabídka.

![Nabídka Soubor správce snímků StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazí v nabídce **Soubor.**

| Položka nabídky | Popis |
|:--- |:--- |
| Nová |Klepnutím na tlačítko **Nový** vytvořte novou konzolu založenou na Správci snímků StorSimple. |
| Otevřít |Kliknutím na **Otevřít** otevřete existující konzolu. |
| Uložit |Kliknutím na **Uložit** uložte aktuální konzolu. |
| Uložit jako |Kliknutím na **Uložit jako** vytvoříte novou přejmenovanou instanci aktuální konzoly. Pomocí možnosti **Uložit jako** můžete přizpůsobit zobrazení a uložit ho pro pozdější načtení. Můžete například vytvořit moduly snap-in Správce snímků StorSimple, které odkazují na konkrétní servery. |
| Přidání nebo odebrání modulu snap-in |Kliknutím na **Přidat nebo odebrat modul snap-in** přidáte nebo odeberete moduly snap-in a uspořádáte uzly v podokně **Obor.** Další informace naleznete v [doplňku Přidat, odebrat a uspořádat moduly Snap-in a rozšíření v konzoli MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Možnosti |Klepnutím na **možnostI** změňte ikonu konzoly, určete režimy a oprávnění přístupu uživatelů nebo odstraňte soubory konzoly, abyste zvýšili dostupné místo na disku. |
| Seznam cest k souborům |Kliknutím na cestu v číslovaném seznamu znovu otevřete soubor, který jste nedávno otevřeli. |
| Ukončit |Klepnutím na **tlačítko Ukončit** zavřete nabídku **Soubor.** |

### <a name="action-menu"></a>Nabídka akce
Pomocí nabídky **Akce** vyberte z dostupných akcí. Dostupné položky závisí na výběru, který provedete v podokně **Obor** nebo **Ve výsledcích.**

#### <a name="menu-access"></a>Přístup k nabídce
Chcete-li zobrazit nabídku **Akce,** proveďte jeden z následujících akcí:

* Klepněte pravým tlačítkem myši na položku v podokně **Obor** nebo **Výsledky.**
* Vyberte položku v podokně **Obor** nebo **Výsledky** a na řádku nabídek klikněte na **Akce.** 

Pokud například vyberete horní uzel v podokně **Obor** a potom kliknete pravým **tlačítkem** nebo kliknete na akci v řádku nabídek, zobrazí se následující nabídka.

![Nabídka akce Správce snímků StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

Podokno **Akce** (vpravo od konzoly) obsahuje stejný seznam akcí jako nabídka **Akce.** Podokno **Akce** navíc obsahuje možnosti nabídky **Zobrazení,** které umožňují vytvořit vlastní zobrazení podokna **Výsledky.**

![Podokno Akce s otevřenou nabídkou Zobrazení](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka obsahuje abecední seznam akcí Správce snímků StorSimple. 

* Sloupec **Akce** uvádí akce, které lze provádět na uzlech a výsledcích. 
* Sloupec **Navigace** vysvětluje, jak zobrazit příslušnou nabídku **Akce,** abyste mohli vybrat akci. Některé akce se zobrazí ve více nabídkách **akce.** Pro tyto akce vyberte jednu možnost **Navigace** ze seznamu s odrážkami. 
* Sloupec **Popis** popisuje použití jednotlivých akcí v nabídce **Akce** nebo v podokně Akce a vysvětluje, co dělá.

> [!NOTE]
> Podokno **Akce** a nabídky **akcí** obsahují další možnosti, například **Zobrazení**, **Nové okno odtud**, **Aktualizace**, **Seznam exportu**a **Nápověda**. Tyto možnosti jsou k dispozici jako součást konzoly MMC a nejsou specifické pro Správce snímků StorSimple. Tabulka obsahuje popisy těchto možností.
> 
> 

| Akce | Navigace | Popis |
|:--- |:--- |:--- |
| Ověření |Klikněte na uzel **Zařízení** a v podokně **Výsledky** klikněte pravým tlačítkem myši na zařízení. |Kliknutím na **Ověřit** zadejte heslo, které jste pro zařízení nakonfigurovali. |
| Klonování |Rozbalte **katalog zálohování**, **rozbalte položku Cloud Snapshots**, klikněte na zálohu datované a vyberte svazek v podokně **Výsledky.** |Kliknutím na **Klonovat** vytvořte kopii snímku cloudu a uložte ho do určeného umístění. |
| Konfigurace zařízení |Klikněte pravým tlačítkem myši na uzel **Zařízení.** |Chcete-li nakonfigurovat jedno zařízení nebo více zařízení pro připojení k hostiteli systému Windows, klepněte na **možnost Konfigurovat zařízení.** |
| Vytvořit zásady zálohování |Proveďte jednu z těchto akcí:<ul><li>Klepněte pravým tlačítkem myši na **položku Zásady zálohování**.</li><li>Klikněte nebo rozbalte **Skupiny svazků**a potom klikněte pravým tlačítkem myši na skupinu svazků.</li><li>Klikněte nebo **rozbalte katalog zálohování**a potom klikněte pravým tlačítkem myši na skupinu svazků.</li></ul> |Chcete-li nakonfigurovat naplánované zálohování pro skupinu svazků, klepněte na tlačítko **Vytvořit zásady zálohování.** |
| Vytvořit skupinu svazků |Proveďte jednu z těchto akcí:<ul><li>Klikněte na uzel **Objemy** a potom klikněte pravým tlačítkem myši na svazek v podokně **Výsledky.**</li><li>Klikněte pravým tlačítkem myši na uzel **Skupiny svazků.**</li></ul> |Kliknutím na **Vytvořit skupinu svazků** přiřadíte svazky skupině svazků. |
| Odstranění |Klepněte na uzel nebo výsledek (Tato položka se zobrazí v mnoha nabídkách **akcí** a podoknech **akce.)** |Kliknutím na **Odstranit** odstraňte vybraný uzel nebo výsledek. Po zobrazení potvrzovacího dialogového okna odstranění potvrďte nebo zrušte. |
| Podrobnosti |Klikněte na uzel **Zařízení** a potom klikněte pravým tlačítkem myši na zařízení v podokně **Výsledky.** |Kliknutím na **Podrobnosti** zobrazíte podrobnosti konfigurace zařízení. |
| Upravit |Klikněte na **Zásady zálohování**a potom klikněte pravým tlačítkem myši na zásadu v podokně **Výsledky.** |Kliknutím na **Upravit** změňte plán zálohování pro skupinu svazků. |
| Exportovat seznam |Klepněte na libovolný uzel nebo výsledek (Tato položka se zobrazí ve všech nabídkách **akcí** a podoknech **akce.)** |Kliknutím na **Exportovat seznam** uložte seznam do souboru csv odděleného čárkami. Tento soubor pak můžete importovat do aplikace tabulky pro analýzu. |
| Nápověda |Klikněte na libovolný uzel nebo výsledek. (Tato položka se zobrazí ve všech nabídkách **akcí** a podoknech **akce.)** |Klepnutím na **tlačítko Nápověda** otevřete nápovědu online v samostatném okně prohlížeče. |
| New Window from Here |Klepněte na libovolný uzel nebo výsledek (Tato položka se zobrazí ve všech nabídkách **akcí** a podoknech **akce.)** |Chcete-li otevřít nové okno Správce snímků StorSimple, klepněte na tlačítko **Nové okno zde.** |
| Obnovení |Klepněte na libovolný uzel nebo výsledek (Tato položka se zobrazí ve všech nabídkách **akcí** a podoknech **akce.)** |Chcete-li aktualizovat aktuálně zobrazené okno Správce snímků StorSimple, klepněte na tlačítko **Aktualizovat.** |
| Aktualizovat zařízení |Klikněte na uzel **Zařízení** a v podokně **Výsledky** klikněte pravým tlačítkem myši na zařízení. |Kliknutím na **Aktualizovat zařízení** synchronizujte konkrétní připojené zařízení se Správcem snímků StorSimple. |
| Aktualizovat zařízení |Klikněte pravým tlačítkem myši na uzel **Zařízení.** |Kliknutím na **Aktualizovat zařízení** synchronizujte seznam připojených zařízení se Správcem snímků StorSimple. |
| Opětovné prohledání svazků |Klikněte pravým tlačítkem myši na uzel **Svazky.** |Kliknutím na **Znovu prohledávat svazky** aktualizujte seznam svazků, které se zobrazí v podokně **Výsledky.** |
| Obnovení |Rozbalte **katalog zálohování**, rozbalte skupinu svazků, **rozbalte položku Místní snímky** nebo Snímky **cloudu**a potom klepněte pravým tlačítkem myši na zálohu. |Klepnutím na **tlačítko Obnovit** nahraďte aktuální data skupiny svazků daty z vybrané zálohy. |
| Přijmout zálohu |Proveďte jednu z těchto akcí:<ul><li>Rozbalte **skupiny svazků**a potom klikněte pravým tlačítkem myši na skupinu svazků.</li><li>Rozbalte **položku Backup Catalog**a potom klepněte pravým tlačítkem myši na skupinu svazků.</li></ul> |Chcete-li okamžitě spustit úlohu zálohování, klepněte na tlačítko **Převzít zálohování.** |
| Přepnout zobrazení importů |Klepněte pravým tlačítkem myši na horní uzel v podokně **Obor** (uzel **Správce snímků StorSimple** v příkladech). |Klepnutím **na tlačítko Přepnout zobrazení importu** zobrazíte nebo skryjete skupiny svazků a přidružené zálohy, které byly importovány z řídicího panelu služby Správce zařízení StorSimple. |

### <a name="view-menu"></a>Nabídka Zobrazit
Pomocí nabídky **Zobrazení** vytvořte vlastní zobrazení obsahu podokna **Výsledky.** Nabídka **Zobrazení** obsahuje **možnosti Přidat nebo odebrat sloupce** a **Přizpůsobit.**

#### <a name="menu-access"></a>Přístup k nabídce
K nabídce **Zobrazení** najdete na panelu nabídek nebo v podokně **Akce.**

![Nabídka Zobrazení správce snímků StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazí v nabídce **Zobrazení.**

| Položka nabídky | Popis |
|:--- |:--- |
| Přidat nebo odebrat sloupce |Kliknutím na **Přidat nebo odebrat sloupce** přidáte nebo odeberete sloupce v podokně **Výsledky.** |
| Přizpůsobení |Kliknutím na **Přizpůsobit** zobrazíte nebo skryjte položky v okně konzoly Správce snímků StorSimple. |

### <a name="favorites-menu"></a>Nabídka Oblíbené položky
Pomocí nabídky **Oblíbené položky** můžete přidávat, odebírat a uspořádat často používanými zobrazení stránek a úkoly. 

#### <a name="menu-access"></a>Přístup k nabídce
Na řádku nabídek můžete získat přístup k nabídce **Oblíbené položky.**

![Nabídka Oblíbené položky Správce snímků StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazí v nabídce **Oblíbené položky.**

| Položka nabídky | Popis |
|:--- |:--- |
| Add to Favorites |Kliknutím na **Přidat k oblíbeným položkám** přidáte aktuální zobrazení do seznamu oblíbených položek. |
| Uspořádání oblíbených položek |Kliknutím na **Uspořádat oblíbené položky** uspořádejte obsah složky Oblíbené. |

### <a name="window-menu"></a>Nabídka Okno
Pomocí nabídky **Okno** můžete přidat a změnit uspořádání oken konzoly Správce snímků StorSimple.

#### <a name="menu-access"></a>Přístup k nabídce
Na řádku nabídek se dostanete do nabídky **Okno.**

![Nabídka Okna Správce snímků StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

Číslovaný seznam v dolní části nabídky zobrazuje aktuálně otevřená okna. Kliknutím na libovolné okno v tomto seznamu přenesete okno do popředí. 

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazí v nabídce Okno.

| Položka nabídky | Popis |
|:--- |:--- |
| Nové okno |Klepnutím na **tlačítko Nové okno** otevřete nové okno konzoly (kromě existujícího okna). |
| Kaskády |Klepnutím na **kaskádu** zobrazíte otevřená okna konzoly v kaskádovém stylu. |
| Dlaždice vodorovně |Kliknutím na **Dlaždice vodorovně** zobrazte otevřená okna konzoly ve formátu dlaždic (nebo mřížky). |
| Uspořádat ikony |Pokud máte více oken konzoly otevřených a rozptýlených po ploše, minimalizujte je a pak klikněte na **Uspořádat ikony** a uspořádejte je do vodorovného řádku v dolní části obrazovky. |

### <a name="help-menu"></a>Nabídka Nápověda
Pomocí nabídky **Nápověda** můžete zobrazit dostupnou online nápovědu pro Správce snímků StorSimple a konzolu MMC. Můžete také zobrazit informace o verzích softwaru MMC a StorSimple Snapshot Manager, které jsou aktuálně nainstalovány v systému. 

Na řádku nabídek se dostanete do nabídky **Nápověda.** Můžete také přistupovat k tématům nápovědy Správce snímků StorSimple z podokna **Akce.**

![Nabídka Nápověda správce snímků StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazí v nabídce Nápověda.

| Položka nabídky | Popis |
|:--- |:--- |
| Nápověda k Správci snímků StorSimple |Klepnutím **na tlačítko Nápověda ve Správci snímků StorSimple** otevřete nápovědu Správce snímků StorSimple v samostatném okně. |
| Témata nápovědy |Klepnutím na **tlačítko Témata nápovědy** otevřete online nápovědu konzoly MMC v samostatném okně. |
| Webový server techcenter |Klepnutím na **web TechCenter** otevřete domovskou stránku Technologického centra Webu Microsoft TechNet v samostatném okně. |
| O konzoli MMC |Klepnutím na **tlačítko O konzoli MMC** zobrazíte, která verze konzoly MMC je v systému nainstalována. |
| O Správci snímků StorSimple |Klepnutím na tlačítko **O Správci snímků StorSimple** zobrazíte, která verze modulu snap-in je v systému nainstalována. |

## <a name="tool-bar"></a>Panel nástrojů
Panel nástrojů umístěný pod panelem nabídek obsahuje navigační ikony a ikony úkolů. Každá ikona je zástupcem konkrétního úkolu.

### <a name="icon-descriptions"></a>Popisy ikon
Následující tabulka popisuje ikony, které se zobrazí na panelu nástrojů. 

| Ikona | Popis |
|:--- |:--- |
| ![Šipka vlevo](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Kliknutím na ikonu šipky vlevo se vrátíte na předchozí stránku. |
| ![Šipka vpravo](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Kliknutím na šipku vpravo přejdete na další stránku (pokud je šedá, akce není k dispozici). |
| ![Ikona Nahoru](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Kliknutím na ikonu nahoru přejdete o jednu úroveň výš ve stromu konzoly (podokno **Obor).** |
| ![Zobrazit/skrýt strom konzoly](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Kliknutím na ikonu zobrazit nebo skrýt strom konzoly zobrazíte nebo skryjete **podokno Obor.** |
| ![Exportovat seznam](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Klepnutím na ikonu exportu seznamu exportujte seznam do zadaného souboru CSV. |
| ![Ikona nápovědy](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Kliknutím na ikonu nápovědy otevřete téma nápovědy online konzoly MMC. |
| ![Podokno Zobrazit nebo skrýt položky Akce](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Kliknutím na ikonu podokna Zobrazit nebo skrýt **akce** zobrazte nebo skryjte podokno **Akce.** |

## <a name="scope-pane"></a>Podokno Obor
Podokno **Obor** je podokno zcela vlevo v uzdu Správce snímků StorSimple. Obsahuje strom konzoly (nebo uzlu) a je primární navigační mechanismus pro StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Struktura podokna oboru
Podokno **Obor** obsahuje řadu objektů ,, na které lze klepnout (uzly) uspořádané ve stromové struktuře. 

![Podokno Obor](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Chcete-li rozbalit nebo sbalit uzel, klepněte na ikonu šipky vedle názvu uzlu.
* Chcete-li zobrazit stav nebo obsah uzlu, klepněte na název uzlu. Informace se zobrazí v podokně **Výsledky.** 

Podokno **Obor** obsahuje následující uzly: 

* [Uzel zařízení](#devices-node) 
* [Uzel svazků](#volumes-node) 
* [Uzel Skupiny svazků](#volume-groups-node) 
* [Uzel Zásady zálohování](#backup-policies-node) 
* [Uzel katalogu záloh](#backup-catalog-node) 
* [Uzel úlohy](#jobs-node) 

### <a name="scope-pane-tasks"></a>Úlohy podokna oboru
Podokno **Obor** můžete použít k dokončení akce na konkrétním uzlu. Chcete-li vybrat úkol, proveďte jeden z následujících akcí:

* Klikněte pravým tlačítkem myši na uzel a vyberte úkol z nabídky, která se zobrazí.
* Klikněte na uzel a potom na řádku nabídek klikněte na **Akce.** Vyberte úkol z nabídky, která se zobrazí.
* Klikněte na uzel a vyberte akci v podokně **Akce.**

Vyberete-li uzel a pomocí některé z těchto metod zobrazíte seznam úkolů, zobrazí se pouze akce, které lze v tomto uzlu provést.

### <a name="devices-node"></a>Uzel zařízení
Uzel **Devices** představuje zařízení StorSimple a virtuální zařízení StorSimple, která jsou připojena ke Správci snímků StorSimple. Vyberte tento uzel, chcete-li připojit a nakonfigurovat zařízení a importovat jeho přidružené svazky, skupiny svazků a existující záložní kopie. K jednomu hostiteli lze připojit více zařízení.

* Chcete-li uzel rozbalit, klepněte na ikonu šipky vedle **položky Zařízení**.
* Pokud chcete zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem myši na uzel **Zařízení** nebo klikněte pravým tlačítkem myši na některý z uzlů, které se zobrazí v rozbaleném zobrazení.
* Pokud chcete zobrazit seznam nakonfigurovaných zařízení, klikněte v podokně **Obor** na **Zařízení.** Seznam zařízení spolu s informacemi o jednotlivých zařízeních se zobrazí v podokně **Výsledky.**

### <a name="volumes-node"></a>Uzel svazků
Uzel **Svazky** představuje jednotky, které odpovídají svazky připojené hostitelem, včetně těch zjištěných prostřednictvím iSCSI a ty zjištěné prostřednictvím zařízení. Tento uzel slouží k zobrazení seznamu dostupných svazků a přiřazení jednotlivých svazků skupinám svazků.

* Chcete-li uzel rozbalit, klepněte na ikonu šipky vedle **položky Svazky**.
* Chcete-li zobrazit nabídku dostupných akcí, klepněte pravým tlačítkem myši na uzel **Objemy** nebo klepněte pravým tlačítkem myši na některý z uzlů, které se zobrazí v rozbaleném zobrazení.
* Chcete-li zobrazit seznam svazků, klepněte v podokně **Obor** na **Položky Svazky.** Seznam svazků spolu s informacemi o jednotlivých svazcích se zobrazí v podokně **Výsledky.**

### <a name="volume-groups-node"></a>Uzel Skupiny svazků
Skupiny svazků jsou také označovány jako skupiny konzistence. Každá skupina svazků je fond svazků souvisejících s aplikací, který pomáhá zajistit konzistenci aplikace během operací zálohování. Pomocí uzlu **Skupiny svazků** můžete tyto skupiny nakonfigurovat a provést interaktivní zálohy nebo vytvořit plány zálohování. 

* Chcete-li uzel rozbalit, klepněte na ikonu šipky vedle **položky Skupiny svazků**.
* Chcete-li zobrazit nabídku dostupných akcí, klepněte pravým tlačítkem myši na uzel **Skupiny svazků** nebo klepněte pravým tlačítkem myši na některý z uzlů, které se zobrazí v rozbaleném zobrazení.
* Chcete-li zobrazit seznam skupin svazků, klepněte v podokně Obor na **Položky Skupiny** **svazků.** Seznam skupin svazků spolu s informacemi o jednotlivých skupinách svazků se zobrazí v podokně **Výsledky.**

### <a name="backup-policies-node"></a>Uzel Zásady zálohování
Zásady zálohování jsou plány úloh pro místní a cloudové snímky. Pomocí uzlu **Zásady zálohování** určete, jak často je záloha vytvořena a jak dlouho má být záloha zachována. 

* Chcete-li uzel rozbalit, klepněte na ikonu šipky vedle **položky Zásady zálohování**.
* Chcete-li zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem myši na uzel **Zásady zálohování** nebo klikněte pravým tlačítkem myši na některý z uzlů, které se zobrazí v rozbaleném zobrazení.
* Pokud chcete zobrazit seznam zásad zálohování, klikněte v podokně **Obor** na **Zásady zálohování.** Seznam zásad zálohování spolu s informacemi o jednotlivých zásadách se zobrazí v podokně **Výsledky.**

> [!NOTE]
> Můžete zachovat maximálně 64 záloh.


### <a name="backup-catalog-node"></a>Uzel katalogu záloh
Uzel **katalogu zálohování** obsahuje seznamy záloh na webu a mimo pracoviště svazků Azure StorSimple. Tento uzel je uspořádán podle skupiny svazků a každý kontejner skupiny svazků obsahuje samostatné struktury pro místní snímky **(uzel s místní snímek)** a snímky cloudu (uzel **Cloud Snapshots).** Při rozbalení každý kontejner skupiny svazků uvádí všechny úspěšné zálohy, které byly pořízeny interaktivně nebo nakonfigurované zásady.

* Chcete-li uzel rozbalit, klepněte na ikonu šipky vedle **položky Backup Catalog**.
* Chcete-li zobrazit nabídku dostupných akcí, klepněte pravým tlačítkem myši na uzel **Katalog zálohování** nebo klepněte pravým tlačítkem myši na některý z uzlů, které se zobrazí v rozbaleném zobrazení.
* Chcete-li zobrazit seznam snímků záloh, klepněte v podokně **Obor** na **položku Katalog zálohování.** Seznam snímků spolu s informacemi o každém snímku se zobrazí v podokně **Výsledky.**

### <a name="local-snapshots-node"></a>Uzel místní snímky
Uzel **Místní snímky** uvádí místní snímky pro určitou skupinu svazků. Uzel je umístěn pod uzětem **Katalog zálohování** v podokně **Obor.** Místní snímky jsou kopie dat svazku v okamžiku, které jsou uloženy na zařízení Azure StorSimple. Tento typ zálohy lze obvykle rychle vytvořit a obnovit. Můžete použít místní snímek jako místní záložní kopii.

* Chcete-li uzel rozbalit, klepněte na ikonu šipky vedle **položky Místní snímky**.
* Chcete-li zobrazit nabídku dostupných akcí, klepněte pravým tlačítkem myši na uzel **Místní snímky** nebo klepněte pravým tlačítkem myši na některý z uzlů, které se zobrazí v rozbaleném zobrazení.
* Chcete-li zobrazit seznam místních snímků, klepněte v podokně **Obor** na **položku Místní snímky.** Seznam snímků spolu s informacemi o každém snímku se zobrazí v podokně **Výsledky.**

### <a name="cloud-snapshots-node"></a>Uzel Cloud Snapshots
Uzel **Cloud Snapshots** uvádí snímky cloudu pro konkrétní skupinu svazků. Uzel je umístěn pod uzětem **Katalog zálohování** v podokně **Obor.** Cloudové snímky jsou kopie dat svazku v okamžiku, které jsou uloženy v cloudu. Snímek cloudu je ekvivalentní snímku replikovaného v jiném systému úložiště mimo pracoviště. Cloudové snímky jsou užitečné zejména ve scénářích zotavení po havárii.

* Chcete-li uzel rozbalit, klepněte na ikonu šipky vedle **položky Snímky cloudu**.
* Chcete-li zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem myši na uzel **Cloud Snapshots** nebo klikněte pravým tlačítkem myši na některý z uzlů, které se zobrazí v rozbaleném zobrazení.
* Pokud chcete zobrazit seznam cloudových snímků, klikněte v podokně **Obor** na **Snímky cloudu.** Seznam snímků spolu s informacemi o každém snímku se zobrazí v podokně **Výsledky.**

### <a name="jobs-node"></a>Uzel úlohy
Uzel **Úlohy** obsahuje informace o naplánovaných, spuštěných a nedávno dokončených úlohách zálohování. 

* Chcete-li uzel rozbalit, klepněte na ikonu šipky vedle **položky Úlohy**.
* Chcete-li zobrazit nabídku dostupných akcí, klepněte pravým tlačítkem myši na uzel **Úlohy** nebo klepněte pravým tlačítkem myši na některý z uzlů, které se zobrazí v rozbaleném zobrazení.
* Pokud chcete zobrazit seznam naplánovaných úloh, rozbalte uzel **Úlohy** a klikněte na **Naplánované**. Seznam dříve nakonfigurovaných úloh a informace o každé úloze se zobrazí v podokně **Výsledky.** 
* Chcete-li zobrazit seznam nedávno dokončených úloh, rozbalte uzel **Úlohy** a klepněte na tlačítko **Posledních 24 hodin**. Seznam úloh, které byly dokončeny za posledních 24 hodin, se zobrazí v podokně **Výsledky.** Podokno **Výsledky** také obsahuje informace o každé dokončené úloze.
* Chcete-li zobrazit seznam úloh, které jsou aktuálně spuštěny, rozbalte uzel **Úlohy** a klepněte na tlačítko **Spuštěno**. Seznam aktuálně spuštěných úloh a informace o každé úloze se zobrazí v podokně **Výsledky.**

## <a name="results-pane"></a>Podokno Výsledky
Podokno **Výsledky** je středové podokno v ui Správce snímků StorSimple. Obsahuje seznamy a podrobné informace o stavu uzlu, který jste vybrali v podokně **Obor.**

### <a name="example"></a>Příklad
Chcete-li zobrazit následující příklad, klepněte na uzel **Skupiny svazků** v podokně **Obor.** Podokno **Výsledky** zobrazuje seznam skupin svazků s podrobnostmi o jednotlivých skupinách.

![Podokno Výsledky](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Můžete nakonfigurovat podrobnosti zobrazené v podokně **Výsledky:** klepněte pravým tlačítkem myši na uzel v podokně **Obor,** klepněte na příkaz **Zobrazit**a potom klepněte na příkaz **Přidat nebo odebrat sloupce**.

## <a name="actions-pane"></a>Podokno Akce
Podokno **Akce** je pravé podokno v ui Správce snímků StorSimple. Obsahuje nabídku operací, které můžete provádět na uzlu, zobrazení nebo datech, které vyberete v podokně **Obor** nebo **Výsledky.** Podokno **Akce** obsahuje stejné příkazy jako nabídky **akcí,** které jsou k dispozici pro položky v podokně **oborů** a **podokně Výsledky.** Popis jednotlivých akcí naleznete v tabulce v části nabídky **Akce.**

### <a name="examples"></a>Příklady
Chcete-li zobrazit následující příklad, rozbalte v podokně **Obor** uzel **Úlohy** a klepněte na **položku Naplánováno**. Podokno **Akce** zobrazuje dostupné akce pro **naplánovaný** uzel.

![Příklad naplánovaných úloh v podokně akcí](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Chcete-li zobrazit další možnosti, rozbalte v podokně **Obor** uzel **Úlohy,** klikněte na **Naplánované**a potom klikněte na naplánovanou úlohu v podokně **Výsledky.** Podokno **Akce** zobrazuje dostupné akce pro naplánovanou úlohu, jak je znázorněno v následujícím příkladu.

![Příklad akcí v podokně akcí v podokně akcí](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Navigace na klávesnici a klávesové zkratky
Správce snímků StorSimple umožňuje funkce usnadnění operačního systému Windows a konzoly MMC (Microsoft Management Console). Obsahuje také některé funkce navigace klávesnice a klávesové zkratky, které jsou specifické pro Správce snímků StorSimple, jak je popsáno v následujících částech.

* [Navigační klávesy klávesnice](#keyboard-navigation-keys) 
* [Klávesové zkratky panelu nabídek](#menu-bar-shortcut-keys) 
* [Klávesové zkratky podokna oboru](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Navigační klávesy klávesnice
Následující tabulka popisuje klíče, které můžete použít k navigaci v uživatelském rozhraní Správce snímků StorSimple. 

| Navigační klávesa | Akce |
|:--- |:--- |
| Šipka dolů |Pomocí klávesy se šipkou dolů se můžete svisle přesunout k další položce v nabídce nebo podokně. |
| Enter |Stisknutím klávesy Enter dokončete akci a pak přejděte k dalšímu kroku. Můžete například stisknout klávesu Enter a vybrat **možnost Další**, **OK**nebo **Vytvořit**a pak přejít k dalšímu kroku průvodce. |
| Esc |Stisknutím klávesy Esc zavřete nabídku nebo stránku zrušte a zavřete. |
| F1 |Stisknutím klávesy F1 zobrazíte téma nápovědy pro aktuálně aktivní okno. |
| F5 |Stisknutím klávesy F5 aktualizujte uzel. |
| F6 |Stisknutím klávesy F6 přejděte z podokna **Obor** do podokna **Výsledky.** |
| F10 |Stisknutím klávesy F10 přejděte na řádek nabídek. |
| Šipka vlevo |Pomocí klávesy se šipkou vlevo se můžete vodorovně přesunout z možnosti řádku nabídek na předchozí možnost. Když přesunete na předchozí položku na řádku nabídek, zobrazí se nabídka akce (nebo kontext) pro předchozí položku. |
| Šipka vpravo |Pomocí klávesy se šipkou vpravo se můžete vodorovně přesunout z jedné možnosti řádku nabídek na další. Když přesunete na další položku na řádku nabídek, zobrazí se nabídka akce (nebo kontext) pro novou položku. |
| Klávesa Tab |Pomocí klávesy Tabulátor přejděte do dalšího podokna v konzole nebo do dalšího výběru nebo textového pole na stránce. |
| Šipka nahoru |Pomocí klávesy šipky nahoru se můžete přesunout svisle k předchozí položce v nabídce nebo podokně. |

### <a name="menu-bar-shortcut-keys"></a>Klávesové zkratky panelu nabídek
Následující tabulka popisuje kombinace klávesových zkratek pro řádek nabídek. Po stisknutí klávesových zkratek a otevření nabídky můžete použít klávesové zkratky nabídky (podtržené klávesy v nabídce). Další informace o panelu nabídek naleznete na [panelu nabídek](#menu-bar).

| Zástupce | Výsledek | Klávesová zkratka nabídky | Výsledek |
|:--- |:--- |:--- |:--- |
| ALT+F |Otevře nabídku **Soubor.** |Ne |Otevře novou instanci konzoly. |
|  |O |Otevře stránku **Nástroje pro správu.** | |
|  |S |Uloží konzolu Správce snímků StorSimple. | |
|  |A |Otevře stránku **Uložit jako.** | |
|  |M |Otevře stránku **Přidat nebo odebrat modul snap-in.** | |
|  |P |Otevře stránku **Možnosti.** | |
|  |H |Otevře online nápovědu. | |
| ALT+A |Otevře nabídku **Akce.** |I |Zapíná a vypíná možnost zobrazení importu. |
|  |W |Otevře novou konzolu Správce snímků StorSimple. | |
|  |F |Aktualizuje konzolu Správce snímků StorSimple. | |
|  |L |Otevře stránku **Exportovat seznam.** | |
|  |H |Otevře online nápovědu. | |
| ALT+V |Otevře nabídku **Zobrazení.** |A |Otevře stránku **Přidat nebo odebrat sloupce.** |
|  |U |Otevře stránku **Přizpůsobit zobrazení.** | |
| ALT+O |Otevře nabídku **Oblíbené položky.** |A |Otevře stránku **Přidat k oblíbeným položkám.** |
|  |O |Otevře stránku **Uspořádat oblíbené položky.** | |
| ALT+W |Otevře nabídku **Okno.** |Ne |Otevře další okno Správce snímků StorSimple. |
|  |C |Zobrazí všechna otevřená okna konzoly v kaskádovém stylu. | |
|  |T |Zobrazí všechna otevřená okna konzoly ve vzorku mřížky. | |
|  |I |Uspořádá ikony do vodorovného řádku v dolní části obrazovky. | |
| ALT+H |Otevře nabídku **Nápověda.** |H |Otevře online nápovědu. |
|  |T |Otevře webovou stránku Technického centra Microsoft TechNet. | |
|  |A |Otevře stránku **O konzoli MMC.** | |

### <a name="scope-pane-shortcut-keys"></a>Klávesové zkratky podokna oboru
V následujících tabulkách jsou uvedeny kombinace klávesových zkratek pro každý uzel v podokně **Obor.** 

* [Klávesové zkratky uzlů zařízení](#devices-node-shortcut-keys)
* [Klávesové zkratky uzlů svazků](#volumes-node-shortcut-keys)
* [Klávesové zkratky uzlu Skupiny svazků](#volume-groups-node-shortcut-keys)
* [Klávesové zkratky uzlu Zásady zálohování](#backup-policies-node-shortcut-keys)
* [Klávesové zkratky uzlů zálohovacího katalogu](#backup-catalog-node-shortcut-keys)
* [Klávesové zkratky uzlu úloh](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Klávesové zkratky uzlů zařízení
| Zástupce nabídky | Výsledek |
|:--- |:--- |
| C |Otevře **stránku Konfigurovat zařízení.** |
| D |Aktualizuje seznam zařízení a podrobnosti o zařízení. |
| V |Otevře nabídku **Zobrazení.** |
| W |Otevře novou konzolu Správce snímků StorSimple zaměřenou na uzel **Podrobnosti.** |
| F |Aktualizuje konzolu Správce snímků StorSimple. |
| L |Otevře stránku **Exportovat seznam.** |
| H |Otevře online nápovědu. |

#### <a name="volumes-node-shortcut-keys"></a>Klávesové zkratky uzlů svazků
| Zástupce nabídky | Výsledek |
|:--- |:--- |
| V |Aktualizuje seznam svazků. |
| V (dvakrát stiskněte) |Otevře nabídku **Zobrazení.** |
| W |Otevře novou konzolu Správce snímků StorSimple zaměřenou na uzel **Svazky.** |
| F |Aktualizuje konzolu Správce snímků StorSimple. |
| L |Otevře stránku **Exportovat seznam.** |
| H |Otevře online nápovědu. |

#### <a name="volume-groups-node-shortcut-keys"></a>Klávesové zkratky uzlu Skupiny svazků
| Zástupce nabídky | Výsledek |
|:--- |:--- |
| G |Otevře stránku **Vytvořit skupinu svazků.** |
| V |Otevře nabídku **Zobrazení.** |
| W |Otevře novou konzolu Správce snímků StorSimple zaměřenou na uzel **Skupiny svazků.** |
| F |Aktualizuje konzolu Správce snímků StorSimple. |
| L |Otevře stránku **Exportovat seznam.** |
| H |Otevře online nápovědu. |

#### <a name="backup-policies-node-shortcut-keys"></a>Klávesové zkratky uzlu Zásady zálohování
| Zástupce nabídky | Výsledek |
|:--- |:--- |
| B |Otevře **stránku Vytvořit zásadu.** |
| V |Otevře nabídku **Zobrazení.** |
| W |Otevře novou konzolu Správce snímků StorSimple zaměřenou na uzel **Skupiny svazků.** |
| F |Aktualizuje konzolu Správce snímků StorSimple. |
| L |Otevře stránku **Export list **. |
| H |Otevře online nápovědu. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Klávesové zkratky uzlů zálohovacího katalogu
| Zástupce nabídky | Výsledek |
|:--- |:--- |
| W |Otevře novou konzolu Správce snímků StorSimple zaměřenou na uzel **Skupiny svazků.** |
| F |Aktualizuje konzolu Správce snímků StorSimple. |
| H |Otevře online nápovědu. |

#### <a name="jobs-node-shortcut-keys"></a>Klávesové zkratky uzlu úloh
| Zástupce nabídky | Výsledek |
|:--- |:--- |
| V |Otevře nabídku **Zobrazení.** |
| W |Otevře novou konzolu Správce snímků StorSimple zaměřenou na uzel **Úlohy.** |
| F |Aktualizuje konzolu Správce snímků StorSimple. |
| L |Otevře stránku **Exportovat seznam.** |
| H |Otevře online nápovědu. |

## <a name="next-steps"></a>Další kroky
* Naučte se [používat Správce snímků StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Přečtěte si, jak [pomocí Správce snímků StorSimple připojit a spravovat zařízení](storsimple-snapshot-manager-manage-devices.md).

