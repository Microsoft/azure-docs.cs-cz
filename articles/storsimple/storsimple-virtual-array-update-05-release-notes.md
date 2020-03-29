---
title: StorSimple Virtual Array Update 0,5 poznámky k verzi | Dokumenty společnosti Microsoft
description: Popisuje kritické otevřené problémy a řešení pro virtuální pole StorSimple se spuštěnou aktualizací 0.5.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2017
ms.author: alkohli
ms.openlocfilehash: 385d9126d578250064659153f6f0f54eec696790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60870668"
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>StorSimple Virtual Array Update 0,5 poznámky k verzi

## <a name="overview"></a>Přehled

Následující poznámky k verzi identifikují důležité problémy s otevřením a vyřešené problémy pro aktualizace virtuálního pole Microsoft Azure StorSimple.

Poznámky k verzi jsou průběžně aktualizovány a jako kritické problémy, které vyžadují řešení jsou zjištěny, jsou přidány. Před nasazením virtuálního pole StorSimple pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 0.5 odpovídá verzi softwaru **10.0.10290.0**.

> [!NOTE]
> Aktualizace jsou rušivé a restartujte zařízení. Pokud vstupně-in/o probíhá, zařízení dojde k prostojům. Podrobné pokyny k instalaci aktualizace naleznete v [části Instalace aktualizace 0.5](storsimple-virtual-array-install-update-05.md).


## <a name="whats-new-in-the-update-05"></a>Co je nového v aktualizaci 0.5
Aktualizace 0.5 je především sestavení opravy chyb. Hlavní vylepšení a opravy chyb jsou následující:

- **Vylepšení odolnosti proti chybám zálohování** – tato verze obsahuje opravy, které zlepšují odolnost proti chybám zálohování. V předchozích verzích byly zálohy opakovány pouze pro určité výjimky. Tato verze opakuje všechny výjimky zálohování a umožňuje zálohování odolnější.

- **Aktualizace pro monitorování využití úložiště** – od 30 června 2017 bude monitorování využití úložiště pro řadu virtuálních zařízení StorSimple vyřazeno. To platí pro monitorovací grafy na všech virtuálních polích se spuštěnou aktualizací 0.4 nebo nižší. Tato aktualizace obsahuje změny potřebné pro pokračování v používání monitorování využití úložiště na webu Azure Portal. Nainstalujte tuto důležitou aktualizaci před 30.


## <a name="issues-fixed-in-the-update-05"></a>Problémy opravené v aktualizaci 0.5

Následující tabulka obsahuje souhrn problémů opravených v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Odolnost proti chybám zálohování| V předchozích verzích byly zálohy opakovány pouze pro určité výjimky. Tato verze obsahuje opravu, která má způsobit větší odolnost záloh opakováním všech výjimek zálohování.|
| 2 |Monitorování| Monitorování využití úložiště pro řadu virtuálních zařízení StorSimple se bude od 30. Tato akce má vliv na monitorovací grafy ve službě StorSimple Device Manager spuštěné na virtuálních polích StorSimple (model 1200). Tato verze obsahuje aktualizace, které umožňují uživateli pokračovat v používání monitorování využití úložiště na virtuálních polích po 30.|
| 3 |Souborový server| V dřívějších verzích by uživatel mohl omylem zkopírovat šifrované soubory do virtuálního pole. Tato verze obsahuje opravu, která by neumožňovala kopírování šifrovaných souborů do virtuálního pole. Pokud vaše zařízení má existující šifrované soubory před aktualizací, zálohy budou nadále selhat, dokud nebudou všechny šifrované soubory odstraněny ze systému. |


## <a name="known-issues-in-the-update-05"></a>Známé problémy v aktualizaci 0.5

Následující tabulka obsahuje souhrn známých problémů pro virtuální pole StorSimple a obsahuje problémy s poznámkou k vydání z předchozích verzí.

| Ne. | Funkce | Problém | Řešení/komentáře |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Virtuální zařízení vytvořená ve verzi preview nelze aktualizovat na podporovanou verzi obecné dostupnosti. |Tato virtuální zařízení musí být převzetí služeb při selhání pro vydání obecné dostupnosti pomocí pracovního postupu zotavení po havárii (DR). |
| **2.** |Zřízený datový disk |Jakmile zřídíte datový disk určité zadané velikosti a vytvoříte odpovídající virtuální zařízení StorSimple, nesmíte datový disk rozbalit ani zmenšit. Pokus o to má za následek ztrátu všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Pokud je zařízení připojeno k doméně, použití zásad skupiny může nepříznivě ovlivnit provoz zařízení. |Ujistěte se, že vaše virtuální pole je ve své vlastní organizační jednotce (OU) pro službu Active Directory a nejsou na něj použity žádné objekty zásad skupiny (GPO). |
| **4.** |Místní webové uživatelské prostředí |Pokud jsou v aplikaci Internet Explorer (IE ESC) povoleny rozšířené funkce zabezpečení, nemusí některé místní webové stránky uživatelského rozhraní, například Poradce při potížích nebo údržba, pracovat správně. Tlačítka na těchto stránkách také nemusí fungovat. |Vypněte rozšířené funkce zabezpečení v aplikaci Internet Explorer. |
| **5.** |Místní webové uživatelské prostředí |Ve virtuálním počítači Hyper-V jsou síťová rozhraní ve webovém uživatelském rozhraní zobrazena jako rozhraní 10 Gb/s. |Toto chování je odrazem Hyper-V. Technologie Hyper-V vždy zobrazuje 10 Gb/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Uzamčení rozsahu bajtů pro aplikace, které pracují s vrstvenými svazky StorSimple, není podporováno. Pokud je povoleno uzamčení rozsahu bajtů, StorSimple vrstvení nefunguje. |Mezi doporučená opatření patří: <br></br>Vypněte uzamčení rozsahu bajtů v logice aplikace.<br></br>Zvolte, zda chcete umístit data pro tuto aplikaci do místně vázaných svazků na rozdíl od vrstvených svazků.<br></br>*Upozornění:* Pokud je povoleno použití místně vázaných svazků a zamykání rozsahu bajtů, místně vázaný svazek může být online ještě před dokončením obnovení. V takových případech pokud probíhá obnovení, musíte počkat na dokončení obnovení. |
| **7.** |Vrstvené sdílené složky |Práce s velkými soubory může mít za následek pomalé vrstvy ven. |Při práci s velkými soubory doporučujeme, aby největší soubor byl menší než 3 % velikosti sdílené složky. |
| **8.** |Použitá kapacita pro sdílené složky |Spotřeba sdílení se může zobrazit, pokud ve sdílené složce nejsou žádná data. Tato spotřeba je proto, že použitá kapacita pro sdílené složky obsahuje metadata. | |
| **9.** |Zotavení po havárii |Zotavení po havárii souborového serveru lze provést pouze ve stejné doméně jako zdrojové zařízení. Zotavení po havárii na cílové zařízení v jiné doméně není v této verzi podporováno. |To je implementováno v novější verzi. Další informace naleznete v oblasti [obnovení služeb při selhání a zotavení po havárii pro virtuální pole StorSimple](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Virtuální zařízení StorSimple nelze spravovat prostřednictvím Azure PowerShell v této verzi. |Veškerá správa virtuálních zařízení by měla být provedena prostřednictvím portálu Azure a místního webového uživatelského prostředí. |
| **11.** |Změna hesla |Konzola virtuálního pole zařízení přijímá vstup pouze ve formátu en-us klávesnice. | |
| **12.** |CHAP |Pověření chap po vytvoření nelze odebrat. Navíc pokud změníte pověření CHAP, je třeba převést svazky do režimu offline a převést je do režimu online, aby se změna projevila. |Tento problém je vyřešen v novější verzi. |
| **13.** |Server iSCSI |Použité úložiště zobrazené pro svazek iSCSI se může lišit ve službě StorSimple Device Manager a v hostiteli iSCSI. |Hostitel iSCSI má zobrazení souborového systému.<br></br>Zařízení vidí bloky přidělené, když byl svazek v maximální velikosti. |
| **14.** |Souborový server |Pokud je k souboru ve složce přidružen alternativní datový proud (ADS), není služba ADS zálohována ani obnovena prostřednictvím zotavení po havárii, klonování a obnovení úrovně položky. | |
| **15.** |Souborový server |Symbolické odkazy nejsou podporovány. | |
| **16.** |Souborový server |Soubory chráněné systémem SOUBORŮ EFS systému Windows při kopírování nebo ukládání na souborovém serveru StorSimple Virtual Array mají za následek nepodporovanou konfiguraci.  | |

## <a name="next-step"></a>Další krok
[Nainstalujte aktualizaci 0.5](storsimple-virtual-array-install-update-05.md) do virtuálního pole StorSimple.

## <a name="references"></a>Odkazy
Hledáte starší poznámku k vydání? Přejít na:

* [Aktualizace virtuálního pole StorSimple 0.4 Poznámky k verzi](storsimple-virtual-array-update-04-release-notes.md)
* [Aktualizace virtuálního pole StorSimple 0.3 Poznámky k verzi](storsimple-ova-update-03-release-notes.md)
* [Aktualizace virtuálního pole StorSimple 0.1 a 0.2 Poznámky k verzi](storsimple-ova-update-01-release-notes.md)
* [Poznámky k verzi pro všeobecné dostupnosti storSimple Virtual Array](storsimple-ova-pp-release-notes.md)

