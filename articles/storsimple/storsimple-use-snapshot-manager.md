---
title: Uživatelské rozhraní StorSimple Snapshot Manageru | Dokumentace Microsoftu
description: Popisuje uživatelské rozhraní StorSimple Snapshot Manageru a vysvětluje, jak použít ho ke správě úloh zálohování a katalog záloh.
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
ms.openlocfilehash: 46225e5a332e035e4d1cc256e71c4b5d8686fd47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845133"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Použití StorSimple Snapshot Manageru uživatelské rozhraní pro správu úloh zálohování a katalog záloh

## <a name="overview"></a>Přehled
StorSimple Snapshot Manageru obsahuje intuitivní uživatelské rozhraní, které slouží k vytváření a správu záloh. Tento kurz obsahuje úvod do uživatelského rozhraní a pak vysvětluje způsob používání jednotlivých součástí. Podrobný popis StorSimple Snapshot Manageru najdete v tématu [co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Popis konzoly
Pokud chcete zobrazit uživatelské rozhraní, klikněte na ikonu StorSimple Snapshot Manageru na ploše. V okně konzoly se zobrazí, jak je znázorněno na následujícím obrázku.

![Podokna StorSimple Snapshot Manageru](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

V okně konzoly má pět hlavních prvků. Klikněte na příslušný odkaz úplný popis jednotlivých prvků.

* [Panel nabídek](#menu-bar) 
* [Panel nástrojů](#tool-bar) 
* [Podokno přehledu](#scope-pane) 
* [Podokno výsledků](#results-pane) 
* [Podokna akcí](#actions-pane) 

Kromě toho StorSimple Snapshot Manager podporuje [navigace a počet zkratky klávesnice](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Usnadnění pro konzolu
Uživatelské rozhraní StorSimple Snapshot Manager podporuje funkce pro usnadnění přístupu poskytované operačním systémem Windows a Microsoft Management Console (MMC), jakož i některé klávesové zkratky specifické pro StorSimple Snapshot Manageru. 

* Popis funkce pro usnadnění přístupu Windows, přejděte na [klávesové zkratky pro Windows](https://support.microsoft.com/kb/126449). 
* Popis funkcí usnadnění pro konzolu MMC, přejděte na [usnadnění pro konzolu MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* Popis funkce pro usnadnění přístupu StorSimple Snapshot Manageru, přejděte na [klávesové zkratky a zkratky navigační](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Panel nabídek
Panel nabídek v horní části okna konzoly obsahuje [souboru](#file-menu), [akce](#action-menu), [zobrazení](#view-menu), [Oblíbené](#favorites-menu), [okno](#window-menu), a [pomáhají](#help-menu) nabídky.

Kliknutím na libovolnou položku na řádku nabídek zobrazíte seznam dostupných příkazů v této nabídce. Následující příklad ukazuje **zobrazení** nabídky na řádku nabídek.

![Nabídka zobrazení vybrané](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>nabídka Soubor
**Souboru** nabídka obsahuje standardní příkazy konzoly Microsoft Management Console (MMC).

#### <a name="menu-access"></a>Přístup do nabídky
Chcete-li zobrazit **souboru** nabídky, klikněte na tlačítko **souboru** na řádku nabídek. V následující nabídce se zobrazí.

![Nabídka Soubor StorSimple Snapshot Manageru](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazují na **souboru** nabídky.

| Položka nabídky | Popis |
|:--- |:--- |
| Nová |Klikněte na tlačítko **nový** vytvořit novou konzolu založené na StorSimple Snapshot Manageru. |
| Otevřenost |Klikněte na tlačítko **otevřete** otevřít stávající konzoly. |
| Uložení |Klikněte na tlačítko **Uložit** uložit aktuální konzolu. |
| Uložit jako |Klikněte na tlačítko **uložit jako** vytvořit nové, přejmenováno instanci aktuální konzolu. Použití **uložit jako** možnost přizpůsobení zobrazení a uložte jej pro pozdější načtení. Můžete například vytvořit StorSimple Snapshot Manageru moduly snap in, které odkazují na konkrétních serverů. |
| Přidat nebo odebrat modul Snap-in |Klikněte na tlačítko **Přidat/odebrat modul Snap-in** přidat nebo odebrat moduly snap in a organizovat uzly v **oboru** podokně. Další informace najdete v části [přidat, odebrat a uspořádat moduly Snap in a rozšíření konzoly MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Možnosti |Klikněte na tlačítko **možnosti** přejdete na ikonu konzoly zadat režimy přístupu uživatelů a oprávnění a odstraňovat soubory konzoly o navýšení volného místa na disku. |
| Seznam cest k souborům |Klikněte na cestu v číslovaný seznam znovu otevřete soubor, který jste právě otevřeli. |
| Konec |Klikněte na tlačítko **ukončovací** zavřete **souboru** nabídky. |

### <a name="action-menu"></a>Nabídka Akce
Použití **akce** nabídky vybrat si z dostupných akcí. Položky k dispozici, závisí na výběru provedete v **oboru** podokně nebo **výsledky** podokně.

#### <a name="menu-access"></a>Přístup do nabídky
Chcete-li zobrazit **akce** nabídky, proveďte jednu z následujících akcí:

* Klikněte pravým tlačítkem na položku **oboru** podokně nebo **výsledky** podokně.
* Vyberte nějakou položku v **oboru** podokně nebo **výsledky** podokně a pak klikněte na tlačítko **akce** na řádku nabídek. 

Například, pokud vyberte nejvyšší uzel v **oboru** podokně a pak klikněte pravým tlačítkem nebo klepněte **akce** v řádku nabídek, zobrazí se následující nabídky.

![Nabídka Akce StorSimple Snapshot Manageru](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

**Akce** podokno (na pravé straně konzoly) obsahuje stejný seznam akcí, jako **akce** nabídky. Kromě toho **akce** podokno obsahuje **zobrazení** možnosti nabídky, které vám umožní vytvořit vlastní zobrazení **výsledky** podokně.

![Otevření podokna akcí pomocí nabídky zobrazení](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka obsahuje abecední seznam akcí StorSimple Snapshot Manageru. 

* **Akce** sloupci jsou uvedeny akce, které můžete provést na uzlech a výsledky. 
* **Navigace** sloupec vysvětluje, jak zobrazit odpovídající **akce** nabídky tak, aby bylo možné vybrat akci. Některé akce se zobrazí ve více **akce** nabídky. Pro tyto akce, vyberte jednu **navigace** možnost ze seznamu s odrážkami. 
* **Popis** sloupec popisuje, jak použít na každou akci **akce** nabídky nebo v podokně Akce a vysvětluje, co dělá.

> [!NOTE]
> **Akce** podokně a **akce** nabídky obsahují další možnosti, jako například **zobrazení**, **nové okno**,  **Aktualizovat**, **exportovat seznam**, a **pomáhají**. Tyto možnosti jsou k dispozici v rámci konzoly MMC a nejsou specifická pro StorSimple Snapshot Manageru. Tabulka obsahuje popis těchto možností.
> 
> 

| Akce | Navigace | Popis |
|:--- |:--- |:--- |
| Ověření |Klikněte na tlačítko **zařízení** uzel a klikněte pravým tlačítkem na zařízení **výsledky** podokně. |Klikněte na tlačítko **ověřit** zadat heslo, které jste nakonfigurovali pro zařízení. |
| Klon |Rozbalte **katalog záloh**, rozbalte **cloudové snímky**, klikněte na zálohu průkaz s datem a vyberte svazek v **výsledky** podokně. |Klikněte na tlačítko **klonování** vytvoření kopie snímek v cloudu a uloží je do umístění, které určíte. |
| Konfigurace zařízení |Klikněte pravým tlačítkem myši **zařízení** uzlu. |Klikněte na tlačítko **nakonfigurovat zařízení** ke konfiguraci jednoho nebo více zařízení pro připojení k hostiteli Windows. |
| Vytvoření zásady zálohování |Udělejte jednu z těchto věcí:<ul><li>Klikněte pravým tlačítkem na **zásady zálohování**.</li><li>Klikněte na tlačítko a rozšiřovat **skupin svazků**a potom klikněte pravým tlačítkem na skupinu svazku.</li><li>Klikněte na tlačítko a rozšiřovat **katalog zálohování**a potom klikněte pravým tlačítkem na skupinu svazku.</li></ul> |Klikněte na tlačítko **vytvořit zásady zálohování** konfigurace naplánované zálohování pro skupiny svazků. |
| Vytvoření skupiny svazků |Udělejte jednu z těchto věcí:<ul><li>Klikněte na tlačítko **svazky** uzel a potom klikněte pravým tlačítkem na svazku ve **výsledky** podokně.</li><li>Klikněte pravým tlačítkem myši **skupin svazků** uzlu.</li></ul> |Klikněte na tlačítko **vytvořit skupiny svazků** přiřadit svazky do skupiny svazku. |
| Odstranění |Klikněte na uzel nebo výsledek (Tato položka je zobrazena na mnoha **akce** nabídky a **akce** podoken.) |Klikněte na tlačítko **odstranit** odstranit uzel nebo výsledek, který jste vybrali. Jakmile se zobrazí dialogové okno potvrzení, potvrďte nebo zrušte odstraňování. |
| Podrobnosti |Klikněte na tlačítko **zařízení** uzel a potom klikněte pravým tlačítkem na zařízení do služby **výsledky** podokně. |Klikněte na tlačítko **podrobnosti** zobrazíte podrobnosti o konfiguraci pro zařízení. |
| Upravit |Klikněte na tlačítko **zásady zálohování**a potom klikněte pravým tlačítkem na zásadu v **výsledky** podokně. |Klikněte na tlačítko **upravit** Chcete-li změnit plán zálohování pro skupiny svazků. |
| Exportovat seznam |Klikněte na libovolný uzel nebo výsledek (Tato položka je zobrazena ve všech **akce** nabídky a **akce** podoken.) |Klikněte na tlačítko **exportovat seznam** uložit seznam v souboru hodnot oddělených čárkami (CSV). Tento soubor pak můžete importovat do aplikace tabulku pro analýzu. |
| Help |Klikněte na libovolný uzel nebo výsledek. (Tato položka je zobrazena ve všech **akce** nabídky a **akce** podoken.) |Klikněte na tlačítko **pomáhají** k otevření online nápovědy v samostatném okně prohlížeče. |
| Nové okno |Klikněte na libovolný uzel nebo výsledek (Tato položka je zobrazena ve všech **akce** nabídky a **akce** podoken.) |Klikněte na tlačítko **nové okno** otevřete nové okno StorSimple Snapshot Manageru. |
| Obnovení |Klikněte na libovolný uzel nebo výsledek (Tato položka je zobrazena ve všech **akce** nabídky a **akce** podoken.) |Klikněte na tlačítko **aktualizovat** aktualizovat aktuálně zobrazené okno StorSimple Snapshot Manageru. |
| Aktualizace zařízení |Klikněte na tlačítko **zařízení** uzel a klikněte pravým tlačítkem na zařízení **výsledky** podokně. |Klikněte na tlačítko **aktualizujte zařízení** synchronizovat konkrétní připojené zařízení pomocí StorSimple Snapshot Manageru. |
| Aktualizace zařízení |Klikněte pravým tlačítkem myši **zařízení** uzlu. |Klikněte na tlačítko **aktualizujte zařízení** synchronizovat seznam připojených zařízení pomocí StorSimple Snapshot Manageru. |
| Znovu prohledat svazky |Klikněte pravým tlačítkem myši **svazky** uzlu. |Klikněte na tlačítko **znovu prohledat svazky** aktualizujte seznam svazků, které se zobrazí v **výsledky** podokně. |
| Obnovení |Rozbalte **katalog záloh**, rozbalte skupinu svazku, rozbalte položku **místní snímky** nebo **cloudové snímky**a potom klikněte pravým tlačítkem na zálohu. |Klikněte na tlačítko **obnovení** k nahrazení aktuální data skupiny svazků s daty z vybrané zálohy. |
| Proveďte zálohu |Udělejte jednu z těchto věcí:<ul><li>Rozbalte **skupin svazků**a potom klikněte pravým tlačítkem na skupinu svazku.</li><li>Rozbalte **katalog zálohování**a potom klikněte pravým tlačítkem na skupinu svazku.</li></ul> |Klikněte na tlačítko **provést zálohování** ihned spustíte úlohu zálohování. |
| Přepnout zobrazení importy |Klikněte pravým tlačítkem na nejvyšší uzel v **oboru** podokně ( **StorSimple Snapshot Manageru** uzlu v příkladech). |Klikněte na tlačítko **přepnout importy zobrazení** zobrazení nebo skrytí skupin svazků a přidružené zálohy, které byly naimportovány z řídicího panelu služby Správce zařízení StorSimple. |

### <a name="view-menu"></a>Nabídka Zobrazit
Použití **zobrazení** chcete vytvořit vlastní zobrazení, v nabídce **výsledky** podokně obsah. **Zobrazení** nabídka obsahuje **Přidat/odebrat sloupce** a **vlastní** možnosti.

#### <a name="menu-access"></a>Přístup do nabídky
Můžete přistupovat **zobrazení** nabídku v panelu nebo v nabídce **akce** podokně.

![Nabídka zobrazení StorSimple Snapshot Manageru](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazují na **zobrazení** nabídky.

| Položka nabídky | Popis |
|:--- |:--- |
| Přidat/odebrat sloupce |Klikněte na tlačítko **Přidat/odebrat sloupce** k přidání nebo odebrání sloupců v **výsledky** podokně. |
| Přizpůsobit |Klikněte na tlačítko **vlastní** zobrazení nebo skrytí položky v okně konzoly StorSimple Snapshot Manageru. |

### <a name="favorites-menu"></a>Nabídka Oblíbené
Použít **Oblíbené** nabídky přidávat, odebírat a organizovat zobrazení stránek a úlohy, které často používáte. 

#### <a name="menu-access"></a>Přístup do nabídky
Můžete přistupovat **Oblíbené** nabídky na řádku nabídek.

![Nabídka Oblíbené StorSimple Snapshot Manageru](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazují na **Oblíbené** nabídky.

| Položka nabídky | Popis |
|:--- |:--- |
| Přidat k oblíbeným položkám |Klikněte na tlačítko **přidat k oblíbeným položkám** aktuální zobrazení přidat do seznamu oblíbených položek. |
| Uspořádání Oblíbené položky |Klikněte na tlačítko **uspořádat oblíbené položky** uspořádat obsah složky Oblíbené položky. |

### <a name="window-menu"></a>Nabídka okna
Použití **okno** nabídky k přidání a změna uspořádání oken konzoly StorSimple Snapshot Manageru.

#### <a name="menu-access"></a>Přístup do nabídky
Můžete přistupovat **okno** nabídky na řádku nabídek.

![Nabídka okna StorSimple Snapshot Manageru](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

Číslovaný seznam v dolní části nabídky ukazuje, že otevřete windows, které jsou v tuto chvíli. Klikněte na libovolné okno v tomto seznamu chcete přenést do popředí v okně. 

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazují v nabídce okno.

| Položka nabídky | Popis |
|:--- |:--- |
| Nové okno |Klikněte na tlačítko **nové okno** otevřete nové okno konzoly (navíc k existující okno). |
| Kaskádové |Klikněte na tlačítko **Cascade** k zobrazení okna otevřete konzoly ve stylu CSS. |
| Vodorovně nad sebe |Klikněte na tlačítko **vodorovně nad sebe** zobrazení otevřete konzoly windows ve formátu dlaždice (nebo tabulky). |
| Uspořádat ikony |Pokud máte více konzoly windows otevřete a rozmístěno jinde přes pracovní plochy je minimalizovat a potom klikněte na **uspořádat ikony** uspořádejte ve vodorovném řádku v dolní části obrazovky. |

### <a name="help-menu"></a>Nabídka Nápověda
Použití **pomáhají** nabídce pro zobrazení k dispozici online nápovědu pro StorSimple Snapshot Manageru a konzoly MMC. Můžete také zobrazit informace o MMC a StorSimple Snapshot Manageru verze softwaru, které jsou aktuálně nainstalovány ve vašem systému. 

Můžete přistupovat **pomáhají** nabídky na řádku nabídek. Témata nápovědy StorSimple Snapshot Manageru z se dá dostat taky **akce** podokně.

![Nabídky Nápověda StorSimple Snapshot Manageru](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Popis nabídky
Následující tabulka popisuje položky, které se zobrazují v nabídce Nápověda.

| Položka nabídky | Popis |
|:--- |:--- |
| Nápověda k StorSimple Snapshot Manageru |Klikněte na tlačítko **Nápověda na StorSimple Snapshot Manageru** k nápovědě StorSimple Snapshot Manageru v samostatném okně. |
| Témata nápovědy |Klikněte na tlačítko **témata nápovědy** otevřete konzoly MMC online nápovědu v samostatném okně. |
| TechCenter Web Site |Klikněte na tlačítko **webu TechCenter** otevřete na domovské stránce Microsoft TechNet Tech Center v samostatném okně. |
| O konzole Microsoft Management Console |Klikněte na tlačítko **o konzoly Microsoft Management Console** zobrazíte, která verze konzoly Microsoft Management Console je nainstalovaná ve vašem systému. |
| Informace o StorSimple Snapshot Manageru |Klikněte na tlačítko **o StorSimple Snapshot Manageru** zobrazíte, která verze modulu snap-in je nainstalovaná ve vašem systému. |

## <a name="tool-bar"></a>Panel nástrojů
Panel nástrojů, nacházel pod nabídek obsahuje ikony navigace a úloh. Jednotlivé ikony je zástupce ke konkrétnímu úkolu.

### <a name="icon-descriptions"></a>Ikona popisy
Následující tabulka popisuje ikon, které se zobrazí na panelu nástrojů. 

| Ikona | Popis |
|:--- |:--- |
| ![Šipka doleva](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Klikněte na šipku vlevo se vrátíte na předchozí stránku. |
| ![Šipka doprava](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Klikněte na tlačítko se šipkou doprava přejděte na další stránce (Pokud na šipku není šedá, akce k dispozici). |
| ![Šipka dolů](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Klikněte na ikonu si přejdete nahoru o jednu úroveň ve stromu konzoly ( **oboru** podokno). |
| ![Zobrazit/skrýt strom konzoly](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Klikněte na ikonu Zobrazení/skrytí konzoly stromové zobrazení nebo skrytí **oboru** podokně. |
| ![Exportovat seznam](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Klikněte na ikonu exportování seznamu seznam exportovat do souboru CSV, který zadáte. |
| ![Ikona nápovědy](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Kliknutím na ikonu nápovědy otevření tématu nápovědy online konzoly MMC. |
| ![Zobrazit/skrýt podokno akcí](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Klikněte na možnost Zobrazit/skrýt **akce** ikony podokna zobrazení nebo skrytí **akce** podokně. |

## <a name="scope-pane"></a>Podokno přehledu
**Oboru** podokno se v podokně úplně vlevo v Uživatelském rozhraní StorSimple Snapshot Manageru. Obsahuje stromové struktuře konzoly (nebo uzel) a je primární navigace mechanismus pro StorSimple Snapshot Manageru. 

### <a name="scope-pane-structure"></a>Struktura podokně oboru
**Oboru** podokno obsahuje řadu kliknout, čímž objekty (uzly) uspořádány do stromové struktury. 

![Podokno přehledu](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Chcete-li rozbalit nebo Sbalit uzel, klikněte na ikonu šipky vedle názvu uzlu.
* Chcete-li zobrazit stav nebo obsah uzlu, klikněte na název uzlu. Informace zobrazí **výsledky** podokně. 

**Oboru** podokno obsahuje následující uzly: 

* [Uzlu zařízení](#devices-node) 
* [Uzel svazky](#volumes-node) 
* [Uzel skupiny svazků](#volume-groups-node) 
* [Zálohování uzlu zásady](#backup-policies-node) 
* [Zálohovací uzel katalogu](#backup-catalog-node) 
* [Úlohy uzlu](#jobs-node) 

### <a name="scope-pane-tasks"></a>Obor podokna úloh
Můžete použít **oboru** podokně cílem udělat nějakou akci v konkrétním uzlu. Vyberte úlohu, proveďte jednu z následujících akcí:

* Klikněte pravým tlačítkem na uzel a potom vyberte úlohu ze zobrazené nabídky.
* Klikněte na uzel a potom klikněte na tlačítko **akce** na řádku nabídek. Vyberte úlohu ze zobrazené nabídky.
* Klikněte na uzel a pak vyberte akci v **akce** podokně.

Když vyberete uzel a pomocí některé z těchto metod najdete v seznamu úloh, se zobrazí pouze ty akce, které lze provést v tomto uzlu.

### <a name="devices-node"></a>Uzlu zařízení
**Zařízení** představuje uzel zařízení StorSimple a virtuální zařízení StorSimple, které jsou připojené k systému StorSimple Snapshot Manageru. Vyberte tento uzel k připojení a konfigurace zařízení a importovat jeho přidružené svazky, skupiny svazků a existující záložní kopie. Více zařízení lze připojit na jednoho hostitele.

* Rozbalte uzel, klikněte na ikonu šipky vedle **zařízení**.
* Zobrazit nabídku akcí k dispozici, klikněte pravým tlačítkem myši **zařízení** uzlu nebo klikněte pravým tlačítkem na některý z uzlů, které se zobrazují v rozšířené zobrazení.
* Pokud chcete zobrazit seznam nakonfigurovaných zařízení, klikněte na tlačítko **zařízení** v **oboru** podokně. Se zobrazí v seznamu zařízení, spolu s informacemi o každé zařízení, **výsledky** podokně.

### <a name="volumes-node"></a>Uzel svazky
**Svazky** představuje uzel jednotek, které odpovídají na svazky připojené hostitele, včetně těch, které zjištěný prostřednictvím iSCSI a ty zjištěný prostřednictvím zařízení. Tento uzel použijte k zobrazení seznamu dostupných svazků a přiřadit skupin svazků jednotlivé svazky.

* Rozbalte uzel, klikněte na ikonu šipky vedle **svazky**.
* Zobrazit nabídku akcí k dispozici, klikněte pravým tlačítkem myši **svazky** uzlu nebo klikněte pravým tlačítkem na některý z uzlů, které se zobrazují v rozšířené zobrazení.
* Chcete-li zobrazit seznam svazků, klikněte na tlačítko **svazky** v **oboru** podokně. Zobrazí se seznam svazků, spolu s informacemi o každém svazku v **výsledky** podokně.

### <a name="volume-groups-node"></a>Uzel skupiny svazků
Svazek skupin se také označují jako skupiny konzistence. Každá skupina svazek je fond svazky týkající se aplikace, která pomáhá zajistit konzistentnost aplikací během operace zálohování. Použití **skupin svazků** uzlu nakonfigurovat tyto skupiny a interaktivní zálohování nebo vytváření plánů zálohování. 

* Rozbalte uzel, klikněte na ikonu šipky vedle **skupin svazků**.
* Zobrazit nabídku akcí k dispozici, klikněte pravým tlačítkem myši **skupin svazků** uzlu nebo klikněte pravým tlačítkem na některý z uzlů, které se zobrazují v rozšířené zobrazení.
* Chcete-li zobrazit seznam skupin svazků, klikněte na tlačítko **skupin svazků** v **oboru** podokně. Zobrazí se seznam skupin svazků, spolu s informacemi o každé skupiny svazků v **výsledky** podokně.

### <a name="backup-policies-node"></a>Zálohování uzlu zásady
Zásady zálohování se plány úloh pro místní a cloudové snímky. Použití **zásady zálohování** uzel k určení, jak často se vytvoří zálohu a jak dlouho zálohy se uchovávají. 

* Rozbalte uzel, klikněte na ikonu šipky vedle **zásady zálohování**.
* Zobrazit nabídku akcí k dispozici, klikněte pravým tlačítkem myši **zásady zálohování** uzlu nebo klikněte pravým tlačítkem na některý z uzlů, které se zobrazují v rozšířené zobrazení.
* Chcete-li zobrazit seznam zásad zálohování, klikněte na tlačítko **zásady zálohování** v **oboru** podokně. Zobrazí se seznam zásad zálohování, spolu s informacemi o jednotlivých zásad v **výsledky** podokně.

> [!NOTE]
> Můžete uchovávat maximálně 64 záloh.


### <a name="backup-catalog-node"></a>Zálohovací uzel katalogu
**Katalog zálohování** uzel obsahuje seznamy na místě a odlehlého zálohování svazky zařízení StorSimple v Azure. Tento uzel je uspořádaný podle skupiny svazků a kontejneru skupiny pro každý svazek obsahuje samostatný struktury pro místní snímky ( **místní snímek**uzel) a cloudových snímků ( **cloudové snímky** uzlu). Po rozbalení kontejneru skupiny pro každý svazek obsahuje všechny úspěšné zálohy, které byly provedeny interaktivně nebo nakonfigurovaných zásad.

* Rozbalte uzel, klikněte na ikonu šipky vedle **katalog zálohování**.
* Zobrazit nabídku akcí k dispozici, klikněte pravým tlačítkem myši **katalog zálohování** uzlu nebo klikněte pravým tlačítkem na některý z uzlů, které se zobrazují v rozšířené zobrazení.
* Chcete-li zobrazit seznam snímků zálohy, klikněte na tlačítko **katalog záloh** v **oboru** podokně. Seznam snímků, spolu s informacemi o jednotlivých snímků, zobrazí se v **výsledky** podokně.

### <a name="local-snapshots-node"></a>Místní uzel wsfc snímky
**Místní snímky** uzel obsahuje seznam místních snímků pro skupinu pro určitý svazek. Uzel je umístěna ve složce **katalog zálohování** uzlu v **oboru** podokně. Místní snímky jsou kopie v okamžiku objem dat, které jsou uložené na zařízení Azure StorSimple. Tento typ zálohování obvykle, můžete vytvořit a rychle obnovit. Můžete použít místní snímek, stejně jako místní záložní kopie.

* Rozbalte uzel, klikněte na ikonu šipky vedle **místní snímky**.
* Zobrazit nabídku akcí k dispozici, klikněte pravým tlačítkem myši **místní snímky** uzlu nebo klikněte pravým tlačítkem na některý z uzlů, které se zobrazují v rozšířené zobrazení.
* Chcete-li zobrazit seznam místních snímků, klikněte na tlačítko **místní snímky** v **oboru** podokně. Seznam snímků, spolu s informacemi o jednotlivých snímků, zobrazí se v **výsledky** podokně.

### <a name="cloud-snapshots-node"></a>Cloudové snímky uzlu
**Cloudové snímky** uzel obsahuje cloudové snímky pro skupinu pro určitý svazek. Uzel je umístěna ve složce **katalog zálohování** uzlu v **oboru** podokně. Cloudové snímky jsou kopie v okamžiku objem dat, které jsou uložené v cloudu. Snímek v cloudu je ekvivalentní ke snímku replikovat i na jiné vzdálené úložiště systému. Cloudové snímky jsou zvláště užitečné ve scénářích zotavení po havárii.

* Rozbalte uzel, klikněte na ikonu šipky vedle **cloudové snímky**.
* Zobrazit nabídku akcí k dispozici, klikněte pravým tlačítkem myši **cloudové snímky** uzlu nebo klikněte pravým tlačítkem na některý z uzlů, které se zobrazují v rozšířené zobrazení.
* Chcete-li zobrazit seznam cloudové snímky, klikněte na tlačítko **cloudové snímky** v **oboru** podokně. Seznam snímků, spolu s informacemi o jednotlivých snímků, zobrazí se v **výsledky** podokně.

### <a name="jobs-node"></a>Úlohy uzlu
**Úlohy** uzel obsahuje informace o naplánované, spuštěné a nedávno dokončené úlohy zálohování. 

* Rozbalte uzel, klikněte na ikonu šipky vedle **úlohy**.
* Zobrazit nabídku akcí k dispozici, klikněte pravým tlačítkem myši **úlohy** uzlu nebo klikněte pravým tlačítkem na některý z uzlů, které se zobrazují v rozšířené zobrazení.
* Chcete-li zobrazit seznam naplánované úlohy, rozbalte **úlohy** uzlu a pak klikněte na tlačítko **naplánované**. Seznam dříve nakonfigurované úlohy a informace o každé úloze se zobrazí v **výsledky** podokně. 
* Chcete-li zobrazit seznam nedávno dokončené úlohy, rozbalte **úlohy** uzlu a pak klikněte na tlačítko **posledních 24 hodin**. Seznam úloh, které byly dokončeny za posledních 24 hodin se zobrazí v **výsledky** podokně. **Výsledky** podokno také obsahuje informace o každé dokončené úlohy.
* Chcete-li zobrazit seznam úloh, které jsou aktuálně spuštěné, rozbalte **úlohy** uzlu a pak klikněte na tlačítko **systémem**. Seznam aktuálně spuštěných úloh a informace o každé úloze se zobrazí v **výsledky** podokně.

## <a name="results-pane"></a>Podokno výsledků
**Výsledky** podokno se v prostředním podokně rozhraní StorSimple Snapshot Manageru. Obsahuje seznam a podrobné informace o stavu pro uzel, který jste vybrali v **oboru** podokně.

### <a name="example"></a>Příklad:
Podívejte se na následující příklad, klikněte na tlačítko **skupin svazků** uzlu v **oboru** podokně. **Výsledky** podokně se zobrazí seznam skupin svazků s podrobnostmi o každé skupiny.

![Podokno výsledků](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Můžete nakonfigurovat podrobnosti uvedené v **výsledky** podokně: klikněte pravým tlačítkem na uzel v **oboru** podokně klikněte na tlačítko **zobrazení**a potom klikněte na tlačítko **Přidat/odebrat sloupce** .

## <a name="actions-pane"></a>Podokna akcí
**Akce** podokno je v pravém podokně v Uživatelském rozhraní StorSimple Snapshot Manageru. Obsahuje nabídku operace, které můžete provést na uzlu, zobrazení nebo data, která jste vybrali v **oboru** podokně nebo **výsledky** podokně. **Akce** podokno obsahuje tytéž příkazy **akce** nabídky, které jsou k dispozici pro položky v **oboru** podokně a **výsledky** podokno. Popis každé akce, najdete v tabulce v **akce** části nabídky.

### <a name="examples"></a>Příklady
Chcete vidět v následujícím příkladu **oboru** podokně rozbalte **úlohy** uzel a klikněte na tlačítko **naplánované**. **Akce** podokno zobrazuje dostupné akce **naplánované** uzlu.

![Příklad naplánované úlohy podokna akce](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Zobrazíte další možnosti najdete v **oboru** podokně rozbalte **úlohy** uzlu, klikněte na tlačítko **naplánované**a potom klikněte na tlačítko naplánovaná úloha v **výsledky** podokno. **Akce** podokno zobrazuje dostupné akce pro naplánovanou úlohu, jak je znázorněno v následujícím příkladu.

![Příklad použití akce úlohy v podokně Akce](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Navigace pomocí kláves a klávesové zkratky
StorSimple Snapshot Manager umožňuje funkce usnadnění služby operačního systému Windows a Microsoft Management Console (MMC). Zahrnuje také některé vlastnosti navigace klávesnice a klávesových zkratek, které jsou specifické pro StorSimple Snapshot Manageru, jak je popsáno v následujících částech.

* [Navigační klávesy](#keyboard-navigation-keys) 
* [Řádek nabídek klávesové zkratky](#menu-bar-shortcut-keys) 
* [Obor podokně klávesové zkratky](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Navigační klávesy
Následující tabulka popisuje klíče, které vám umožní Navigovat v uživatelském rozhraní StorSimple Snapshot Manageru. 

| Navigační klávesy | Akce |
|:--- |:--- |
| Šipka dolů |Použijte klávesy se šipkou dolů svisle přesunout na další položku v nabídce nebo podokno. |
| Enter |Stisknutím klávesy Enter Dokončit akci a pak pokračujte k dalšímu kroku. Například stisknutím klávesy Enter provést výběr **Další**, **OK**, nebo **vytvořit**a potom přejděte k dalšímu kroku v průvodci. |
| Esc |Stisknutím klávesy Esc zavřete nabídku nebo zrušit a zavřít na stránce. |
| F1 |Stisknutím klávesy F1 zobrazíte tématu nápovědy pro aktuálně aktivní okno. |
| F5 |Stisknutím klávesy F5 aktualizujte uzel. |
| F6 |Stisknutím klávesy F6 přesouvat **oboru** podokně **výsledky** podokně. |
| F10 |Stiskněte klávesu F10 přejdete na řádku nabídek. |
| Šipka vlevo |Klávesa Šipka doleva z položku nabídky panelu vodorovně přejít na předchozí. Při přesunu do předchozí položky na řádku nabídek, zobrazí se nabídka Akce (nebo místní) pro předchozí položce. |
| Šipka vpravo |Vodorovně přesouvat z jednoho panelu nabídky na další pomocí klávesy se šipkou vpravo. Přesunout na další položku na řádku nabídek, zobrazí se nabídka Akce (nebo místní) pro novou položku. |
| Stisknutím klávesy TAB |Použijte klávesu Tab k přesunutí na další podokno v konzole nebo další výběr nebo textového pole na stránce. |
| Klávesa Šipka nahoru |Můžete vertikálně přesunout na předchozí položky v nabídce nebo podokno šipka nahoru. |

### <a name="menu-bar-shortcut-keys"></a>Řádek nabídek klávesové zkratky
Následující tabulka popisuje kombinace klávesových zkratek pro panel nabídek. Po otevření nabídky a stiskněte klávesovou zkratku můžete použít nabídku klávesové zkratky (podtržené klíče v nabídce). Další informace o řádku nabídek, přejděte na [nabídek](#menu-bar).

| Zástupce | Výsledek | Nabídka klávesové zkratky | Výsledek |
|:--- |:--- |:--- |:--- |
| ALT+F |Otevře **souboru** nabídky. |Ne |Otevře se nové instance konzoly. |
|  |O |Otevře **nástroje pro správu** stránky. | |
|  |S |Uloží konzole StorSimple Snapshot Manageru. | |
|  |A |Otevře **uložit jako** stránky. | |
|  |M |Otevře **Přidat/odebrat modul Snap-in** stránky. | |
|  |P |Otevře **možnosti** stránky. | |
|  |H |Otevření online nápovědy. | |
| ALT+A |Otevře **akce** nabídky. |I |Možnost importu zobrazení vypíná a zapíná. |
|  |W |Otevře se nové konzoly StorSimple Snapshot Manageru. | |
|  |F |Aktualizace konzoly StorSimple Snapshot Manageru. | |
|  |L |Otevře **exportovat seznam** stránky. | |
|  |H |Otevření online nápovědy. | |
| ALT+V |Otevře **zobrazení** nabídky. |A |Otevře **Přidat/odebrat sloupce** stránky. |
|  |U |Otevře **přizpůsobit zobrazení** stránky. | |
| ALT+O |Otevře **Oblíbené** nabídky. |A |Otevře **přidat k oblíbeným položkám** stránky. |
|  |O |Otevře **uspořádat oblíbené položky** stránky. | |
| ALT+W |Otevře **okno** nabídky. |Ne |Otevře se jiné okno StorSimple Snapshot Manageru. |
|  |C |Zobrazí všechny otevřené konzoly windows ve stylu CSS. | |
|  |T |Zobrazí všechny otevřené konzoly windows ve vzorku mřížky. | |
|  |I |Uspořádá ikony ve vodorovném řádku v dolní části obrazovky. | |
| ALT+H |Otevře **pomáhají** nabídky. |H |Otevření online nápovědy. |
|  |T |Otevře webovou stránku Microsoft TechNet Tech Center. | |
|  |A |Otevře **o konzoly Microsoft Management Console** stránky. | |

### <a name="scope-pane-shortcut-keys"></a>Obor podokně klávesové zkratky
Následující tabulky popisují zástupce kombinace kláves pro každý uzel v **oboru** podokně. 

* [Klávesové zkratky uzlu zařízení](#devices-node-shortcut-keys)
* [Svazky uzel klávesové zkratky](#volumes-node-shortcut-keys)
* [Svazek skupiny uzel klávesové zkratky](#volume-groups-node-shortcut-keys)
* [Klávesové zkratky uzlu zásady zálohování](#backup-policies-node-shortcut-keys)
* [Zálohování klávesové zkratky uzlu katalogu](#backup-catalog-node-shortcut-keys)
* [Úlohy uzel klávesové zkratky](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Klávesové zkratky uzlu zařízení
| Zástupce v nabídce | Výsledek |
|:--- |:--- |
| C |Otevře **nakonfigurovat zařízení** stránky. |
| D |Aktualizuje seznam zařízení a podrobnosti o zařízení. |
| V |Otevře **zobrazení** nabídky. |
| W |Otevře se nové konzoly StorSimple Snapshot Manageru, zaměřuje na **podrobnosti** uzlu. |
| F |Aktualizace konzoly StorSimple Snapshot Manageru. |
| L |Otevře **exportovat seznam** stránky. |
| H |Otevření online nápovědy. |

#### <a name="volumes-node-shortcut-keys"></a>Svazky uzel klávesové zkratky
| Zástupce v nabídce | Výsledek |
|:--- |:--- |
| V |Aktualizuje seznam svazků. |
| V (stiskněte dvakrát) |Otevře **zobrazení** nabídky. |
| W |Otevře se nové konzoly StorSimple Snapshot Manageru, zaměřuje na **svazky** uzlu. |
| F |Aktualizace konzoly StorSimple Snapshot Manageru. |
| L |Otevře **exportovat seznam** stránky. |
| H |Otevření online nápovědy. |

#### <a name="volume-groups-node-shortcut-keys"></a>Svazek skupiny uzel klávesové zkratky
| Zástupce v nabídce | Výsledek |
|:--- |:--- |
| G |Otevře **vytvořte skupinu svazku** stránky. |
| V |Otevře **zobrazení** nabídky. |
| W |Otevře se nové konzoly StorSimple Snapshot Manageru, zaměřuje na **skupin svazků** uzlu. |
| F |Aktualizace konzoly StorSimple Snapshot Manageru. |
| L |Otevře **exportovat seznam** stránky. |
| H |Otevření online nápovědy. |

#### <a name="backup-policies-node-shortcut-keys"></a>Klávesové zkratky uzlu zásady zálohování
| Zástupce v nabídce | Výsledek |
|:--- |:--- |
| B |Otevře **vytvořit zásadu** stránky. |
| V |Otevře **zobrazení** nabídky. |
| W |Otevře se nové konzoly StorSimple Snapshot Manageru, zaměřuje na **skupin svazků** uzlu. |
| F |Aktualizace konzoly StorSimple Snapshot Manageru. |
| L |Otevře se ** exportovat seznam ** stránky. |
| H |Otevření online nápovědy. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Zálohování klávesové zkratky uzlu katalogu
| Zástupce v nabídce | Výsledek |
|:--- |:--- |
| W |Otevře se nové konzoly StorSimple Snapshot Manageru, zaměřuje na **skupin svazků** uzlu. |
| F |Aktualizace konzoly StorSimple Snapshot Manageru. |
| H |Otevření online nápovědy. |

#### <a name="jobs-node-shortcut-keys"></a>Úlohy uzel klávesové zkratky
| Zástupce v nabídce | Výsledek |
|:--- |:--- |
| V |Otevře **zobrazení** nabídky. |
| W |Otevře se nové konzoly StorSimple Snapshot Manageru, zaměřuje na **úlohy** uzlu. |
| F |Aktualizace konzoly StorSimple Snapshot Manageru. |
| L |Otevře **exportovat seznam** stránky. |
| H |Otevření online nápovědy |

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [použít ke správě vašeho řešení StorSimple Snapshot Manageru zařízení StorSimple](storsimple-snapshot-manager-admin.md).
* Zjistěte, jak [připojit a spravovat zařízení pomocí StorSimple Snapshot Manageru](storsimple-snapshot-manager-manage-devices.md).

