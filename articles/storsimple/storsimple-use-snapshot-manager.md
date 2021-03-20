---
title: StorSimple Snapshot Manager uživatelské rozhraní | Microsoft Docs
description: Popisuje uživatelské rozhraní StorSimple Snapshot Manager a vysvětluje, jak ho použít ke správě úloh zálohování a katalogu záloh.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 35fa0fc134045585eebd21324352f9b4ff272e87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954235"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Použití uživatelského rozhraní StorSimple Snapshot Manager ke správě úloh zálohování a katalogu záloh

## <a name="overview"></a>Přehled
StorSimple Snapshot Manager má intuitivní uživatelské rozhraní, které můžete použít k převzetí a správě záloh. Tento kurz obsahuje úvod do uživatelského rozhraní a pak vysvětluje, jak používat jednotlivé komponenty. Podrobný popis Snapshot Manager StorSimple najdete v tématu [co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Popis konzoly
Pokud chcete zobrazit uživatelské rozhraní, klikněte na ikonu Snapshot Manager StorSimple na ploše. Zobrazí se okno konzoly, jak je znázorněno na následujícím obrázku.

![StorSimple Snapshot Manager podokna](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

Okno konzoly má pět hlavních prvků. Klikněte na příslušný odkaz pro úplný popis každého prvku.

* [Řádek nabídek](#menu-bar) 
* [Panel nástrojů](#tool-bar) 
* [Podokno oboru](#scope-pane) 
* [Podokno výsledků](#results-pane) 
* [Podokno akcí](#actions-pane) 

StorSimple Snapshot Manager navíc podporuje [navigaci pomocí klávesnice a řadu klávesových zkratek](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Přístupnost konzoly
Uživatelské rozhraní StorSimple Snapshot Manager podporuje funkce usnadnění poskytované operačním systémem Windows a konzolu MMC (Microsoft Management Console) a také některé Snapshot Manager StorSimple pro konkrétní klávesové zkratky. 

* Popis funkcí usnadnění systému Windows naleznete v [klávesových zkratce pro systém Windows](https://support.microsoft.com/kb/126449). 
* Popis funkcí usnadnění přístupu MMC najdete v části [přístupnost pro MMC 3,0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc766075(v=ws.11)) .
* Popis funkcí StorSimple Snapshot Manager dostupnosti najdete na stránce [navigace a zástupci klávesnice](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Řádek nabídek
Panel nabídek v horní části okna konzoly obsahuje nabídky [soubor](#file-menu), [Akce](#action-menu), [zobrazení](#view-menu), [Oblíbené položky](#favorites-menu), [okno](#window-menu)a [help](#help-menu) .

Kliknutím na libovolnou položku na panelu nabídek zobrazíte seznam dostupných příkazů v této nabídce. Následující příklad ukazuje nabídku **zobrazení** vybranou na panelu nabídek.

![Vybraná nabídka zobrazení](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Nabídka Soubor
Nabídka **soubor** obsahuje standardní příkazy konzoly Microsoft Management Console (MMC).

#### <a name="menu-access"></a>Přístup k nabídce
Chcete-li zobrazit nabídku **soubor** , klikněte na položku **soubor** na panelu nabídek. Zobrazí se následující nabídka.

![StorSimple Snapshot Manager – nabídka souboru](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazí v nabídce **soubor** .

| Položka nabídky | Description |
|:--- |:--- |
| Nová |Kliknutím na **Nový** vytvořte novou konzolu založenou na StorSimple Snapshot Manager. |
| Otevřít |Kliknutím na **otevřít** otevřete existující konzolu. |
| Uložit |Kliknutím na **Uložit** uložte aktuální konzolu. |
| Uložit jako |Klikněte na **Uložit jako** a vytvořte novou, přejmenovanou instanci aktuální konzoly. Pomocí možnosti **Uložit jako** můžete přizpůsobit zobrazení a uložit ho pro pozdější načtení. Můžete například vytvořit StorSimple Snapshot Manager moduly snap-in, které odkazují na konkrétní servery. |
| Přidat nebo odebrat modul snap-in |Kliknutím na **Přidat nebo odebrat modul snap-** in můžete přidat nebo odebrat moduly snap-in a uspořádat uzly v podokně **Rozsah** . Další informace najdete [v konzole MMC 3,0 na Přidat, odebrat a uspořádat moduly snap-in a rozšíření](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc722035(v=ws.11)). |
| Možnosti |Kliknutím na **Možnosti** změňte ikonu konzoly, určete režimy přístupu uživatele a oprávnění nebo odstraňte soubory konzoly, abyste zvýšili dostupné místo na disku. |
| Seznam cest k souborům |Kliknutím na cestu v číslovaném seznamu znovu otevřete soubor, který jste nedávno otevřeli. |
| Ukončit |Kliknutím na tlačítko **ukončit** zavřete nabídku **soubor** . |

### <a name="action-menu"></a>Nabídka akce
V nabídce **Akce** můžete vybrat z dostupných akcí. Položky, které jsou k dispozici, závisí na výběru provedeném v podokně **oboru** nebo v podokně **výsledků** .

#### <a name="menu-access"></a>Přístup k nabídce
Chcete-li zobrazit nabídku **Akce** , proveďte jednu z následujících akcí:

* V podokně **oboru** nebo v podokně **výsledků** klikněte pravým tlačítkem myši na položku.
* V podokně **oboru** nebo v podokně **výsledků** vyberte položku a pak klikněte na tlačítko **Akce** na řádku nabídek. 

Pokud například v podokně **Rozsah** vyberete uzel nejvyšší úrovně a kliknete pravým tlačítkem nebo kliknete na tlačítko **Akce** v řádku nabídek, zobrazí se následující nabídka.

![StorSimple Snapshot Manager – nabídka akce](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

Podokno **Akce** (na pravé straně konzoly) obsahuje stejný seznam akcí jako nabídka **Akce** . Kromě toho podokno **Akce** obsahuje možnosti nabídky **zobrazení** , které umožňují vytvořit vlastní zobrazení podokna **výsledků** .

![Podokno akce s otevřenou nabídkou zobrazení](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka obsahuje abecední seznam akcí StorSimple Snapshot Manager. 

* Sloupec **Akce** obsahuje seznam akcí, které lze provádět na uzlech a výsledcích. 
* **Navigační** sloupec vysvětluje, jak zobrazit příslušnou nabídku **Akce** , abyste mohli vybrat akci. Některé akce se zobrazí v několika nabídkách **akcí** . V případě těchto akcí vyberte v seznamu s odrážkami jednu možnost **Navigace** . 
* Sloupec **Popis** popisuje, jak použít jednotlivé akce v nabídce **Akce** nebo podokně akce a vysvětluje, co dělá.

> [!NOTE]
> Podokno **Akce** a nabídky **akcí** obsahují další možnosti, jako je například **zobrazení**, **nové okno z tohoto místa**, **aktualizace**, **seznam exportu** a **help**. Tyto možnosti jsou k dispozici v rámci konzoly MMC a nejsou specifické pro StorSimple Snapshot Manager. Tabulka obsahuje popisy těchto možností.
> 
> 

| Akce | Navigace | Description |
|:--- |:--- |:--- |
| Ověření |Klikněte na uzel **zařízení** a v podokně **výsledků** klikněte pravým tlačítkem na zařízení. |Kliknutím na **ověřit** zadejte heslo, které jste nakonfigurovali pro zařízení. |
| Klonování |Rozbalte **Katalog zálohování**, rozbalte **cloudové snímky**, klikněte na zálohu s datem a potom v podokně **výsledků** vyberte svazek. |Kliknutím na **klonovat** vytvořte kopii snímku cloudu a uložte ji do umístění, které určíte. |
| Konfigurace zařízení |Klikněte pravým tlačítkem myši na uzel **zařízení** . |Kliknutím na **Konfigurovat zařízení** nakonfigurujte jedno zařízení nebo více zařízení pro připojení k hostiteli Windows. |
| Vytvořit zásady zálohování |Proveďte některou z následujících akcí:<ul><li>Klikněte pravým tlačítkem na **zásady zálohování**.</li><li>Klikněte nebo rozbalte **skupiny svazků** a potom klikněte pravým tlačítkem na skupinu svazků.</li><li>Klikněte nebo rozbalte **Katalog zálohování** a potom klikněte pravým tlačítkem na skupinu svazků.</li></ul> |Klikněte na **vytvořit zásadu zálohování** a nakonfigurujte naplánované zálohování pro skupinu svazků. |
| Vytvořit skupinu svazků |Proveďte některou z následujících akcí:<ul><li>Klikněte na uzel **svazky** a potom v podokně **výsledků** klikněte pravým tlačítkem na svazek.</li><li>Klikněte pravým tlačítkem myši na uzel **skupiny svazků** .</li></ul> |Kliknutím na **vytvořit skupinu svazků** přiřaďte svazky ke skupině svazků. |
| Odstranit |Klikněte na uzel nebo výsledek (Tato položka se zobrazí v řadě **akcí** a podoknech  **akcí** .) |Kliknutím na tlačítko **Odstranit** odstraníte uzel nebo výsledek, který jste vybrali. Až se zobrazí potvrzovací dialogové okno, potvrďte nebo zrušte odstranění. |
| Podrobnosti |Klikněte na uzel **zařízení** a potom v podokně **výsledků** klikněte pravým tlačítkem na zařízení. |Kliknutím na **Podrobnosti** zobrazíte podrobnosti o konfiguraci zařízení. |
| Upravit |Klikněte na **zásady zálohování** a potom v podokně **výsledků** klikněte pravým tlačítkem na zásadu. |Klikněte na **Upravit** a změňte plán zálohování pro skupinu svazků. |
| Exportovat seznam |Klikněte na libovolný uzel nebo výsledek (Tato položka se zobrazí v podoknech všechny **Akce** a **Akce** ). |Kliknutím na **Exportovat seznam** uložte seznam do souboru s hodnotami oddělenými čárkou (CSV). Tento soubor pak můžete importovat do aplikace v tabulce pro účely analýzy. |
| Help |Klikněte na libovolný uzel nebo výsledek. (Tato položka se zobrazí v podoknech všechny **Akce** **a akce.** ) |Kliknutím na tlačítko **Zobrazit** otevřete online nápovědě v samostatném okně prohlížeče. |
| New Window from Here |Klikněte na libovolný uzel nebo výsledek (Tato položka se zobrazí v podoknech všechny **Akce** a **Akce** ). |Kliknutím na **nové okno z tohoto místa** otevřete nové okno StorSimple Snapshot Manager. |
| Aktualizovat |Klikněte na libovolný uzel nebo výsledek (Tato položka se zobrazí v podoknech všechny **Akce** a **Akce** ). |Kliknutím na **aktualizovat** aktualizujte aktuálně zobrazené Snapshot Manager okno StorSimple. |
| Aktualizovat zařízení |Klikněte na uzel **zařízení** a v podokně **výsledků** klikněte pravým tlačítkem na zařízení. |Kliknutím na **aktualizovat zařízení** synchronizujete konkrétní připojené zařízení s Snapshot Manager StorSimple. |
| Aktualizovat zařízení |Klikněte pravým tlačítkem myši na uzel **zařízení** . |Kliknutím na **aktualizovat zařízení** synchronizujete seznam připojených zařízení s Snapshot Manager StorSimple. |
| Znovu prohledat svazky |Klikněte pravým tlačítkem na uzel **svazky** . |Kliknutím na možnost **Prohledat svazky** aktualizujte seznam svazků, které se zobrazí v podokně **výsledků** . |
| Obnovení |Rozbalte **Katalog zálohování**, rozbalte skupinu svazků, rozbalte **místní snímky** nebo **cloudové snímky** a potom klikněte pravým tlačítkem na zálohu. |Kliknutím na **obnovit** nahraďte aktuální data skupiny svazků daty z vybrané zálohy. |
| Provést zálohování |Proveďte některou z následujících akcí:<ul><li>Rozbalte položku **skupiny svazků** a potom klikněte pravým tlačítkem myši na skupinu svazků.</li><li>Rozbalte položku **katalog záloh** a potom klikněte pravým tlačítkem na skupinu svazků.</li></ul> |Kliknutím na **vytvořit zálohu** spusťte úlohu zálohování okamžitě. |
| Přepnout zobrazení importů |Pravým tlačítkem myši klikněte na nejvyšší uzel v podokně **oboru** ( **StorSimple Snapshot Manager** v příkladech). |Kliknutím na tlačítko **Přepnout importy zobrazíte** nebo skryjete skupiny svazků a přidružené zálohy, které byly naimportovány z řídicího panelu služby StorSimple Správce zařízení. |

### <a name="view-menu"></a>Nabídka Zobrazit
Pomocí nabídky **Zobrazit** můžete vytvořit vlastní zobrazení obsahu podokna **výsledků** . Nabídka **zobrazení** obsahuje možnosti **Přidat/odebrat sloupce** a **přizpůsobit** možnosti.

#### <a name="menu-access"></a>Přístup k nabídce
K nabídce **zobrazení** můžete získat přístup na panelu nabídek nebo v podokně **Akce** .

![Nabídka zobrazení Snapshot Manager StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazí v nabídce **zobrazení** .

| Položka nabídky | Description |
|:--- |:--- |
| Přidat či odebrat sloupce |Kliknutím na **Přidat nebo odebrat sloupce** přidejte nebo odeberte sloupce v podokně **výsledků** . |
| Přizpůsobení |Kliknutím na **přizpůsobit** zobrazíte nebo skryjete položky v okně konzoly StorSimple Snapshot Manager. |

### <a name="favorites-menu"></a>Nabídka Oblíbené
Pomocí nabídky **Oblíbené** můžete přidávat, odebírat a uspořádávat zobrazení a úlohy, které často používáte. 

#### <a name="menu-access"></a>Přístup k nabídce
K nabídce **Oblíbené položky** můžete získat přístup v řádku nabídek.

![Nabídka oblíbených položek StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazí v nabídce **Oblíbené** .

| Položka nabídky | Description |
|:--- |:--- |
| Add to Favorites |Kliknutím na tlačítko **Přidat k oblíbeným položkám** přidáte aktuální zobrazení do seznamu oblíbených položek. |
| Uspořádat oblíbené položky |Kliknutím na **Uspořádat oblíbené položky** uspořádáte obsah složky Oblíbené položky. |

### <a name="window-menu"></a>Nabídka okno
Pomocí nabídky **okno** můžete přidat a změnit uspořádání Snapshot Manager oken konzoly StorSimple.

#### <a name="menu-access"></a>Přístup k nabídce
Přístup k nabídce **okna** můžete získat z panelu nabídek.

![Nabídka okna Snapshot Manager StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

Číslovaný seznam v dolní části nabídky zobrazuje okna, která jsou momentálně otevřená. Kliknutím na libovolné okno v tomto seznamu přepněte okno do popředí. 

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazí v nabídce okna.

| Položka nabídky | Description |
|:--- |:--- |
| Nové okno |Kliknutím na **nové okno** otevřete nové okno konzoly (kromě stávajícího okna). |
| Nášejí |Kliknutím na tlačítko **kaskády** zobrazíte otevřená okna konzoly v Kaskádovém stylu. |
| Vedle sebe vodorovně |Kliknutím na **dlaždici vodorovně** zobrazíte otevřená okna konzoly ve formátu dlaždice (neboli mřížka). |
| Uspořádat ikony |Pokud máte více oken konzoly otevřené a rozptýlené na ploše, minimalizujte je a potom kliknutím na **Uspořádat ikony** je uspořádejte do vodorovného řádku v dolní části obrazovky. |

### <a name="help-menu"></a>Nabídka Nápověda
V nabídce **help** můžete zobrazit dostupnou online nápovědu pro StorSimple Snapshot Manager a konzolu MMC. Můžete si také prohlédnout informace o Snapshot Manager verzích konzoly MMC a StorSimple, které jsou aktuálně nainstalované ve vašem systému. 

K nabídce **help** můžete získat přístup na řádku nabídek. Témata nápovědy k StorSimple Snapshot Manager můžete získat také v podokně **Akce** .

![Nabídka StorSimple Snapshot Manager Help](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazí v nabídce Help.

| Položka nabídky | Description |
|:--- |:--- |
| Help Snapshot Manager StorSimple |Kliknutím na tlačítko **help on StorSimple Snapshot Manager** otevřete Snapshot Manager nápovědě k StorSimple v samostatném okně. |
| Témata nápovědy |Kliknutím na **témata nápovědy** otevřete MMC online nápovědu v samostatném okně. |
| Web TechCenter |Kliknutím na **web TechCenter** otevřete domovskou stránku Microsoft TechNet Tech Center v samostatném okně. |
| O konzole Microsoft Management Console |Kliknutím na položku **informace o konzole Microsoft Management Console** zjistíte, která verze konzoly Microsoft Management Console je v systému nainstalována. |
| O Snapshot Manager StorSimple |Kliknutím na **StorSimple Snapshot Manager** zjistíte, která verze modulu snap-in je v systému nainstalovaná. |

## <a name="tool-bar"></a>Panel nástrojů
Panel nástrojů umístěný pod řádkem nabídek obsahuje ikony navigace a úkolů. Každá ikona je zástupcem konkrétního úkolu.

### <a name="icon-descriptions"></a>Popisy ikon
Následující tabulka popisuje ikony, které se zobrazí na panelu nástrojů. 

| Ikona | Description |
|:--- |:--- |
| ![Šipka vlevo](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Kliknutím na ikonu se šipkou doleva se vrátíte na předchozí stránku. |
| ![Šipka vpravo](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Kliknutím na šipku doprava přejdete na další stránku (Pokud je šipka šedá, akce není k dispozici). |
| ![Ikona nahoru](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Kliknutím na ikonu nahoru přejděte do stromu konzoly o jednu úroveň (podokno **Rozsah** ). |
| ![Zobrazit nebo skrýt strom konzoly](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Kliknutím na ikonu Zobrazit/skrýt strom konzoly zobrazíte nebo skryjete podokno **Rozsah** . |
| ![Exportovat seznam](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Klikněte na ikonu Exportovat seznam a exportujte seznam do souboru CSV, který zadáte. |
| ![Ikona pomocníka](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Kliknutím na ikonu nápovědy otevřete online téma nápovědy pro konzolu MMC. |
| ![Zobrazit/skrýt podokno akcí](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Kliknutím na ikonu Zobrazit/skrýt **Akce** v podokně akce zobrazíte nebo skryjete podokno **akcí** . |

## <a name="scope-pane"></a>Podokno oboru
Podokno **oboru** je levé podokno v uživatelském rozhraní StorSimple Snapshot Manager. Obsahuje strom konzoly (nebo uzlu) a je primárním mechanismem navigace pro StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Struktura podokna oboru
Podokno **oboru** obsahuje řadu objektů, které se ve stromové struktuře uspořádají (uzly). 

![Podokno oboru](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Chcete-li rozbalit nebo Sbalit uzel, klikněte na ikonu šipky vedle názvu uzlu.
* Chcete-li zobrazit stav nebo obsah uzlu, klikněte na název uzlu. Informace se zobrazí v podokně **výsledků** . 

Podokno **oboru** obsahuje následující uzly: 

* [Uzel zařízení](#devices-node) 
* [Uzel svazky](#volumes-node) 
* [Uzel skupiny svazků](#volume-groups-node) 
* [Uzel zásady zálohování](#backup-policies-node) 
* [Uzel katalogu zálohování](#backup-catalog-node) 
* [Uzel úlohy](#jobs-node) 

### <a name="scope-pane-tasks"></a>Úkoly v podokně rozsah
Můžete použít podokno **oboru** k dokončení akce v konkrétním uzlu. Chcete-li vybrat úlohu, proveďte jednu z následujících akcí:

* Pravým tlačítkem myši klikněte na uzel a potom vyberte úlohu z nabídky, která se zobrazí.
* Klikněte na uzel a pak klikněte na **akci** na řádku nabídek. Vyberte úlohu z nabídky, která se zobrazí.
* Klikněte na uzel a pak v podokně **Akce** vyberte akci.

Když vyberete uzel a použijete některou z těchto metod k zobrazení seznamu úkolů, zobrazí se pouze ty akce, které lze provést v tomto uzlu.

### <a name="devices-node"></a>Uzel zařízení
Uzel **zařízení** představuje zařízení StorSimple a virtuální zařízení StorSimple, která jsou připojená k StorSimple Snapshot Manager. Vyberte tento uzel pro připojení a konfiguraci zařízení a importujte jeho přidružené svazky, skupiny svazků a existující záložní kopie. K jednomu hostiteli lze připojit více zařízení.

* Uzel rozbalíte kliknutím na ikonu šipky vedle položky **zařízení**.
* Pokud chcete zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem myši na uzel **zařízení** nebo klikněte pravým tlačítkem na kterýkoli z uzlů, které se zobrazí v rozbaleném zobrazení.
* Seznam nakonfigurovaných zařízení zobrazíte tak, že v podokně **Rozsah** kliknete na **zařízení** . Seznam zařízení spolu s informacemi o jednotlivých zařízeních se zobrazí v podokně **výsledků** .

### <a name="volumes-node"></a>Uzel svazky
Uzel **svazky** představuje jednotky, které odpovídají svazkům připojeným k hostiteli, včetně těch zjištěných prostřednictvím iSCSI a zjištěných prostřednictvím zařízení. Pomocí tohoto uzlu můžete zobrazit seznam dostupných svazků a přiřadit jednotlivé svazky ke skupinám svazků.

* Chcete-li uzel rozbalit, klikněte na ikonu šipky vedle položky **svazky**.
* Pokud chcete zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem myši na uzel **svazky** nebo klikněte pravým tlačítkem na kterýkoli z uzlů, které se zobrazí v rozbaleném zobrazení.
* Pokud chcete zobrazit seznam svazků, klikněte na **svazky** v podokně **Rozsah** . Seznam svazků spolu s informacemi o jednotlivých svazcích se zobrazí v podokně **výsledků** .

### <a name="volume-groups-node"></a>Uzel skupiny svazků
Skupiny svazků se také označují jako skupiny konzistence. Každá skupina svazků je fondem svazků souvisejících s aplikacemi, které pomáhají zajistit konzistenci aplikací během operací zálohování. Uzel **skupiny svazků** použijte ke konfiguraci těchto skupin a k provádění interaktivních záloh nebo vytváření plánů zálohování. 

* Uzel rozbalíte kliknutím na ikonu šipky vedle položky **skupiny svazků**.
* Chcete-li zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem myši na uzel **skupiny svazků** nebo klikněte pravým tlačítkem myši na kterýkoli uzel, který se zobrazí v rozbaleném zobrazení.
* Pokud chcete zobrazit seznam skupin svazků, klikněte na **skupiny svazků** v podokně **Rozsah** . Seznam skupin svazků spolu s informacemi o jednotlivých skupinách svazků se zobrazí v podokně **výsledků** .

### <a name="backup-policies-node"></a>Uzel zásady zálohování
Zásady zálohování jsou plány úloh pro místní a cloudové snímky. Uzel **zásady zálohování** použijte k určení, jak často se vytvoří záloha a jak dlouho se má zachovat záloha. 

* Uzel rozbalíte kliknutím na ikonu se šipkou vedle **zásady zálohování**.
* Chcete-li zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem myši na uzel **zásady zálohování** nebo klikněte pravým tlačítkem myši na kterýkoli uzel, který se zobrazí v rozbaleném zobrazení.
* Pokud chcete zobrazit seznam zásad zálohování, klikněte na **zásady zálohování** v podokně **Rozsah** . Seznam zásad zálohování spolu s informacemi o jednotlivých zásadách se zobrazí v podokně **výsledků** .

> [!NOTE]
> Můžete uchovávat maximálně 64 záloh.


### <a name="backup-catalog-node"></a>Uzel katalogu zálohování
Uzel **Katalog zálohování** obsahuje seznam zálohování svazků Azure StorSimple na pracovišti a mimo pracoviště. Tento uzel je uspořádán podle skupiny svazků a každý kontejner skupiny svazků obsahuje samostatné struktury pro místní snímky (uzel **místní snímek** s) a snímky cloudu (uzel **snímky cloudu** ). Když je rozbalený, každý kontejner skupiny svazků obsahuje seznam všech úspěšných záloh, které se provedly interaktivně nebo nakonfigurovanými zásadami.

* Uzel rozbalíte kliknutím na ikonu šipky vedle položky **Katalog zálohování**.
* Pokud chcete zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem myši na uzel **katalogu zálohování** nebo klikněte pravým tlačítkem na kterýkoli z uzlů, které se zobrazí v rozbaleném zobrazení.
* Pokud chcete zobrazit seznam záložních snímků, klikněte v podokně **oboru** na **Katalog zálohování** . Seznam snímků společně s informacemi o jednotlivých snímcích se zobrazí v podokně **výsledků** .

### <a name="local-snapshots-node"></a>Uzel místních snímků
Uzel **místní snímky** obsahuje seznam místních snímků pro určitou skupinu svazků. Uzel je umístěný pod uzlem **záložní katalog** v podokně **oboru** . Místní snímky jsou kopie dat svazků, které jsou uložené na zařízení Azure StorSimple, na základě bodu v čase. Tento typ zálohování je typicky možné vytvořit a rychle obnovit. Místní snímek můžete použít stejně jako místní záložní kopii.

* Chcete-li uzel rozšířit, klikněte na ikonu šipky vedle položky **místní snímky**.
* Chcete-li zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem myši na uzel **místní snímky** nebo klikněte pravým tlačítkem myši na kterýkoli uzel, který se zobrazí v rozbaleném zobrazení.
* Pokud chcete zobrazit seznam místních snímků, klikněte na **místní snímky** v podokně **Rozsah** . Seznam snímků společně s informacemi o jednotlivých snímcích se zobrazí v podokně **výsledků** .

### <a name="cloud-snapshots-node"></a>Uzel snímků cloudu
Uzel **snímky cloudu** obsahuje seznam cloudových snímků pro určitou skupinu svazků. Uzel je umístěný pod uzlem **záložní katalog** v podokně **oboru** . Cloudové snímky jsou kopie dat svazků, které jsou uložené v cloudu, v časovém bodě. Snímek v cloudu je ekvivalentní snímku replikovanému na jiném systému úložiště mimo lokalitu. Cloudové snímky jsou zvláště užitečné ve scénářích zotavení po havárii.

* Chcete-li uzel rozšířit, klikněte na ikonu šipky vedle položky **cloudové snímky**.
* Chcete-li zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem myši na uzel **snímky cloudu** nebo klikněte pravým tlačítkem na kterýkoli uzel, který se zobrazí v rozbaleném zobrazení.
* Pokud chcete zobrazit seznam cloudových snímků, klikněte na **snímky cloudu** v podokně **Rozsah** . Seznam snímků společně s informacemi o jednotlivých snímcích se zobrazí v podokně **výsledků** .

### <a name="jobs-node"></a>Uzel úlohy
Uzel **Jobs** obsahuje informace o plánovaných, spuštěných a nedávno dokončených úlohách zálohování. 

* Chcete-li uzel rozbalit, klikněte na ikonu šipky vedle položky **úlohy**.
* Chcete-li zobrazit nabídku dostupných akcí, klikněte pravým tlačítkem myši na uzel **úlohy** nebo klikněte pravým tlačítkem myši na kterýkoli z uzlů, které se zobrazí v rozbaleném zobrazení.
* Pokud chcete zobrazit seznam naplánovaných úloh, rozbalte uzel **úlohy** a potom klikněte na **naplánované**. V podokně **výsledků** se zobrazí seznam dříve nakonfigurovaných úloh a informací o jednotlivých úlohách. 
* Chcete-li zobrazit seznam nedávno dokončených úloh, rozbalte uzel **úlohy** a klikněte na tlačítko **Poslední 24 hodin**. V podokně **výsledků** se zobrazí seznam úloh, které byly dokončeny za posledních 24 hodin. Podokno **výsledků** obsahuje také informace o každé dokončené úloze.
* Chcete-li zobrazit seznam aktuálně spuštěných úloh, rozbalte uzel **úlohy** a potom klikněte na možnost **spuštěno**. V podokně **výsledků** se zobrazí seznam aktuálně spuštěných úloh a informací o jednotlivých úlohách.

## <a name="results-pane"></a>Podokno výsledků
Podokno **výsledků** je prostřední podokno v uživatelském rozhraní StorSimple Snapshot Manager. Obsahuje seznamy a podrobné informace o stavu pro uzel, který jste vybrali v podokně **Rozsah** .

### <a name="example"></a>Příklad
Chcete-li zobrazit následující příklad, klikněte na uzel **skupiny svazků** v podokně **Rozsah** . V podokně **výsledků** se zobrazí seznam skupin svazků s podrobnostmi o každé skupině.

![Podokno výsledků](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Můžete nakonfigurovat podrobnosti zobrazené v podokně **výsledků** : klikněte pravým tlačítkem myši na uzel v podokně **Rozsah** , klikněte na možnost **Zobrazit** a poté klikněte na možnost **Přidat nebo odebrat sloupce**.

## <a name="actions-pane"></a>Podokno akcí
Podokno **Akce** je pravé podokno v uživatelském rozhraní StorSimple Snapshot Manager. Obsahuje nabídku operací, které můžete provádět na uzlu, zobrazení nebo datech, která jste vybrali v podokně **oboru** nebo v podokně **výsledků** . Podokno **Akce** obsahuje stejné příkazy jako nabídky **akcí** , které jsou k dispozici pro položky v podokně **oboru** a v podokně **výsledků** . Popis jednotlivých akcí naleznete v tabulce v části menu **Akce** .

### <a name="examples"></a>Příklady
Následující příklad zobrazíte tak, že v podokně **oboru** rozbalíte uzel **úlohy** a kliknete na **naplánované**. V podokně **Akce** se zobrazí dostupné akce pro **plánovaný** uzel.

![Příklad plánovaných úloh podokna akcí](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Pokud chcete zobrazit další možnosti, v podokně **Rozsah** rozbalte uzel **úlohy** , klikněte na **naplánované** a potom v podokně **výsledků** klikněte na naplánovanou úlohu. V podokně **Akce** se zobrazí dostupné akce pro naplánovanou úlohu, jak je znázorněno v následujícím příkladu.

![Příklad akcí úlohy podokna akce](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Navigace na klávesnici a zástupci
StorSimple Snapshot Manager povoluje funkce usnadnění pro operační systém Windows a konzolu MMC (Microsoft Management Console). Obsahuje také některé funkce a zástupce klávesnice, které jsou specifické pro StorSimple Snapshot Manager, jak je popsáno v následujících částech.

* [Navigační klávesy klávesnice](#keyboard-navigation-keys) 
* [Klávesové zkratky panelu nabídek](#menu-bar-shortcut-keys) 
* [Klávesové zkratky v podokně rozsah](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Navigační klávesy klávesnice
Následující tabulka obsahuje popis klíčů, které můžete použít k procházení uživatelského rozhraní StorSimple Snapshot Manager. 

| Navigační klíč | Akce |
|:--- |:--- |
| Klávesa šipka dolů |Pomocí klávesy se šipkou dolů se můžete pohybovat svisle k další položce v nabídce nebo v podokně. |
| Enter |Stisknutím klávesy ENTER dokončete akci a potom přejděte k dalšímu kroku. Například stisknutím klávesy ENTER můžete vybrat **Další**, **OK** nebo **vytvořit** a pak přejít k dalšímu kroku v průvodci. |
| Esc |Stisknutím klávesy ESC zavřete nabídku nebo zrušit a zavřete stránku. |
| F1 |Stisknutím klávesy F1 zobrazíte téma nápovědy pro aktuálně aktivní okno. |
| F5 |Stisknutím klávesy F5 aktualizujte uzel. |
| F6 |Stisknutím klávesy F6 se přesunete z podokna **Rozsah** do podokna **výsledků** . |
| F10 |Stisknutím klávesy F10 přejděte na panel nabídek. |
| Šipka vlevo |Použijte klávesu šipka vlevo k přesunutí vodorovně z možnosti panelu nabídek na předchozí možnost. Při přechodu na předchozí položku na řádku nabídek se zobrazí nabídka Akce (nebo kontext) pro předchozí položku. |
| Šipka vpravo |Použijte klávesu šipka doprava k přesunutí vodorovně z jedné možnosti panelu nabídek na další. Když přejdete na další položku na řádku nabídek, zobrazí se nabídka Akce (nebo kontext) pro novou položku. |
| Klávesa TAB |Pomocí klávesy TAB se můžete pohybovat k dalšímu podoknu v konzole nástroje nebo k dalšímu výběru nebo textovému poli na stránce. |
| Klávesa šipka nahoru |K přesunutí svisle na předchozí položku v nabídce nebo v podokně použijte klávesu šipka nahoru. |

### <a name="menu-bar-shortcut-keys"></a>Klávesové zkratky panelu nabídek
Následující tabulka popisuje kombinace klávesových zkratek pro panel nabídek. Po stisknutí klávesových zkratek, které se otevře, můžete použít klávesové zkratky v nabídce (podtržené klíče v nabídce). Další informace o řádku nabídek najdete na [řádku nabídek](#menu-bar).

| Zástupce | Výsledek | Klávesová zkratka nabídky | Výsledek |
|:--- |:--- |:--- |:--- |
| ALT + F |Otevře nabídku **soubor** . |N |Otevře novou instanci konzoly. |
|  |O |Otevře stránku **Nástroje pro správu** . | |
|  |S |Uloží konzolu Snapshot Manager StorSimple. | |
|  |A |Otevře stránku **Uložit jako** . | |
|  |M |Otevře stránku **Přidat nebo odebrat modul snap-in** . | |
|  |P |Otevře stránku **Možnosti** . | |
|  |H |Otevře online nápovědě. | |
| ALT + A |Otevře nabídku **Akce** . |I |Zapne nebo vypne možnost zobrazení importu. |
|  |W |Otevře novou StorSimple Snapshot Manager konzolu. | |
|  |F |Aktualizuje konzolu Snapshot Manager StorSimple. | |
|  |L |Otevře stránku **seznam exportu** . | |
|  |H |Otevře online nápovědě. | |
| ALT + V |Otevře nabídku **zobrazení** . |A |Otevře stránku **Přidat nebo odebrat sloupce** . |
|  |U |Otevře stránku **přizpůsobit zobrazení** . | |
| ALT+O |Otevře nabídku **Oblíbené** . |A |Otevře stránku **Přidat k oblíbeným položkám** . |
|  |O |Otevře stránku **Uspořádat oblíbené položky** . | |
| ALT + W |Otevře nabídku **okna** . |N |Otevře jiné okno Snapshot Manager StorSimple. |
|  |C |Zobrazí všechna otevřená okna konzoly v Kaskádovém stylu. | |
|  |T |Zobrazí všechna otevřená okna konzoly ve vzoru mřížky. | |
|  |I |Uspořádá ikony do vodorovného řádku v dolní části obrazovky. | |
| ALT + H |Otevře nabídku **help** . |H |Otevře online nápovědě. |
|  |T |Otevře webovou stránku Microsoft TechNet Tech Center. | |
|  |A |Otevře stránku **o konzole Microsoft Management Console** . | |

### <a name="scope-pane-shortcut-keys"></a>Klávesové zkratky v podokně rozsah
V následujících tabulkách jsou uvedeny kombinace klávesových zkratek pro každý uzel v podokně **Rozsah** . 

* [Klávesové zkratky uzlu zařízení](#devices-node-shortcut-keys)
* [Klávesové zkratky uzlu svazků](#volumes-node-shortcut-keys)
* [Klávesové zkratky uzlu skupiny svazků](#volume-groups-node-shortcut-keys)
* [Klávesové zkratky uzlu zásady zálohování](#backup-policies-node-shortcut-keys)
* [Klávesové zkratky uzlu katalogu zálohování](#backup-catalog-node-shortcut-keys)
* [Klávesové zkratky uzlu úlohy](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Klávesové zkratky uzlu zařízení
| Zástupce nabídky | Výsledek |
|:--- |:--- |
| C |Otevře stránku **Konfigurace zařízení** . |
| D |Aktualizuje seznam zařízení a podrobnosti o zařízení. |
| V |Otevře nabídku **zobrazení** . |
| W |Otevře novou StorSimple Snapshot Manager konzolu, která se zaměřuje na uzel **Details** . |
| F |Aktualizuje konzolu Snapshot Manager StorSimple. |
| L |Otevře stránku **seznam exportu** . |
| H |Otevře online nápovědě. |

#### <a name="volumes-node-shortcut-keys"></a>Klávesové zkratky uzlu svazků
| Zástupce nabídky | Výsledek |
|:--- |:--- |
| V |Aktualizuje seznam svazků. |
| V (stiskněte dvakrát) |Otevře nabídku **zobrazení** . |
| W |Otevře novou StorSimple Snapshot Manager konzolu zaměřenou na uzel **svazky** . |
| F |Aktualizuje konzolu Snapshot Manager StorSimple. |
| L |Otevře stránku **seznam exportu** . |
| H |Otevře online nápovědě. |

#### <a name="volume-groups-node-shortcut-keys"></a>Klávesové zkratky uzlu skupiny svazků
| Zástupce nabídky | Výsledek |
|:--- |:--- |
| G |Otevře stránku **vytvořit skupinu svazků** . |
| V |Otevře nabídku **zobrazení** . |
| W |Otevře novou StorSimple Snapshot Manager konzolu zaměřenou na uzel **skupiny svazků** . |
| F |Aktualizuje konzolu Snapshot Manager StorSimple. |
| L |Otevře stránku **seznam exportu** . |
| H |Otevře online nápovědě. |

#### <a name="backup-policies-node-shortcut-keys"></a>Klávesové zkratky uzlu zásady zálohování
| Zástupce nabídky | Výsledek |
|:--- |:--- |
| B |Otevře stránku **vytvořit zásadu** . |
| V |Otevře nabídku **zobrazení** . |
| W |Otevře novou StorSimple Snapshot Manager konzolu zaměřenou na uzel **skupiny svazků** . |
| F |Aktualizuje konzolu Snapshot Manager StorSimple. |
| L |Otevře stránku **seznam exportu** . |
| H |Otevře online nápovědě. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Klávesové zkratky uzlu katalogu zálohování
| Zástupce nabídky | Výsledek |
|:--- |:--- |
| W |Otevře novou StorSimple Snapshot Manager konzolu zaměřenou na uzel **skupiny svazků** . |
| F |Aktualizuje konzolu Snapshot Manager StorSimple. |
| H |Otevře online nápovědě. |

#### <a name="jobs-node-shortcut-keys"></a>Klávesové zkratky uzlu úlohy
| Zástupce nabídky | Výsledek |
|:--- |:--- |
| V |Otevře nabídku **zobrazení** . |
| W |Otevře novou StorSimple Snapshot Manager konzolu zaměřenou na uzel **Jobs** . |
| F |Aktualizuje konzolu Snapshot Manager StorSimple. |
| L |Otevře stránku **seznam exportu** . |
| H |Otevře online nápovědě. |

## <a name="next-steps"></a>Další kroky
* Naučte se [používat Snapshot Manager StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Naučte se [používat StorSimple Snapshot Manager pro připojení a správu zařízení](storsimple-snapshot-manager-manage-devices.md).