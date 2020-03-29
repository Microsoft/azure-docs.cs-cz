---
title: StorSimple Virtual Array Update 0.4 poznámky k verzi | Dokumenty společnosti Microsoft
description: Popisuje kritické otevřené problémy a řešení pro virtuální pole StorSimple se spuštěnou aktualizací 0.4.
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
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: 06a3469507631d032535bce62b01d964e99dc603
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60334790"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>StorSimple Virtual Array Update 0,4 poznámky k verzi

## <a name="overview"></a>Přehled

Následující poznámky k verzi identifikují důležité problémy s otevřením a vyřešené problémy pro aktualizace virtuálního pole Microsoft Azure StorSimple.

Poznámky k verzi jsou průběžně aktualizovány a jako kritické problémy, které vyžadují řešení jsou zjištěny, jsou přidány. Před nasazením virtuálního pole StorSimple pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 0.4 odpovídá verzi softwaru **10.0.10289.0**.

> [!NOTE]
> Aktualizace jsou rušivé a restartujte zařízení. Pokud vstupně-in/o probíhá, zařízení dojde k prostojům.


## <a name="whats-new-in-the-update-04"></a>Co je nového v aktualizaci 0.4
Aktualizace 0.4 je především bug-fix sestavení spolu s několika vylepšeními. V této verzi bylo vyřešeno několik chyb, které vedly k selhání zálohování v předchozí verzi. Hlavní vylepšení a opravy chyb jsou následující:

- **Vylepšení výkonu zálohování** – tato verze provedla několik klíčových vylepšení pro zlepšení výkonu zálohování. V důsledku toho zálohy, které zahrnují velký počet souborů vidět významné snížení času na dokončení, pro úplné a přírůstkové zálohy.

- **Vylepšený výkon obnovení** – tato verze obsahuje vylepšení, která výrazně zlepšují výkon obnovení při použití velkého počtu souborů. Pokud používáte 2 až 4 miliony souborů, doporučujeme zřídit virtuální pole s 16 GB RAM, abyste viděli vylepšení. Při použití méně než 2 miliony souborů, minimální požadavek pro virtuální počítač i nadále 8 GB RAM.

- **Vylepšení balíčku podpory** – vylepšení zahrnují protokolování statistik y pro disk, procesor, paměť, síť a cloud do balíčku podpory a tím zlepšit proces diagnostiky nebo ladění problémů zařízení.

- **Omezte místně připnuté svazky iSCSI na 200 GB** – u místně vázaných svazků doporučujeme omezit na 200 GB svazku iSCSI ve virtuálním poli StorSimple. Místní rezervace pro vrstvené svazky je i nadále 10 % zřízenévelikosti svazku, ale je omezena na 200 GB. 

- **Opravy chyb související se zálohováním** – v předchozích verzích softwaru došlo k problémům souvisejícím se zálohováním, které by způsobovaly selhání zálohování. Tyto chyby byly řešeny v této verzi.


## <a name="issues-fixed-in-the-update-04"></a>Problémy opravené v aktualizaci 0.4

Následující tabulka obsahuje souhrn problémů opravených v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Výkon zálohování|V dřívějších verzích zálohy zahrnující velký počet souborů bude trvat dlouhou dobu k dokončení (v pořadí dnů). V této verzi úplné i přírůstkové zálohy vidět významné snížení doby do dokončení. |
| 2 |Balíček podpory|Disk, procesor, paměť, síť a cloud statistiky jsou nyní přihlášeni k protokolům podpory, takže balíčky podpory jsou velmi efektivní při řešení problémů se zařízeními.|
| 3 |Zálohování |V dřívějších verzích může mít dlouhotrvající zálohování za následek mezeru v zařízení, což by mělo za následek selhání zálohování. Tato chyba je řešena v této verzi tím, že umožňuje více než 5 záloh do fronty najednou.|
| 4 |iSCSI | V dřívějších verzích byla místní rezervace pro vrstvené nebo místně vázaných svazků 10 % velikosti zřízeného svazku. V této verzi je místní rezervace pro všechny svazky iSCSI (místně připnutá nebo vrstvená) omezena na 10 % s maximálně 200 GB (pro vrstvené svazky větší než 2 TB), čímž se uvolní více místa na místním disku. Doporučujeme, aby místně vázaných svazků v této verzi být omezena na 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Známé problémy v aktualizaci 0.4

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
| **8.** |Použitá kapacita pro sdílené složky |Spotřeba sdílení se může zobrazit, pokud ve sdílené složce nejsou žádná data. Důvodem je, že použitá kapacita pro sdílené složky obsahuje metadata. | |
| **9.** |Zotavení po havárii |Zotavení po havárii souborového serveru lze provést pouze ve stejné doméně jako zdrojové zařízení. Zotavení po havárii na cílové zařízení v jiné doméně není v této verzi podporováno. |To je implementováno v novější verzi. |
| **10.** |Azure PowerShell |Virtuální zařízení StorSimple nelze spravovat prostřednictvím Azure PowerShell v této verzi. |Veškerá správa virtuálních zařízení by měla být provedena prostřednictvím klasického portálu Azure a místního webového uživatelského prostředí. |
| **11.** |Změna hesla |Konzola zařízení virtuálního pole přijímá vstup pouze ve formátu klávesnice en-US. | |
| **12.** |CHAP |Pověření chap po vytvoření nelze odebrat. Navíc pokud změníte pověření CHAP, je třeba převést svazky do režimu offline a převést je do režimu online, aby se změna projevila. |Tento problém je vyřešen v novější verzi. |
| **13.** |Server iSCSI |Použité úložiště zobrazené pro svazek iSCSI se může lišit ve službě StorSimple Manager a v hostiteli iSCSI. |Hostitel iSCSI má zobrazení souborového systému.<br></br>Zařízení vidí bloky přidělené, když byl svazek v maximální velikosti. |
| **14.** |Souborový server |Pokud je k souboru ve složce přidružen alternativní datový proud (ADS), není služba ADS zálohována ani obnovena prostřednictvím zotavení po havárii, klonování a obnovení úrovně položky. | |
| **15.** |Souborový server |Symbolické odkazy nejsou podporovány. | |
| **16.** |Souborový server |Soubory chráněné systémem SOUBORŮ EFS systému Windows při kopírování nebo ukládání na souborovém serveru StorSimple Virtual Array mají za následek nepodporovanou konfiguraci.  | |

## <a name="next-step"></a>Další krok
[Nainstalujte aktualizaci 0.4](storsimple-virtual-array-install-update-04.md) do virtuálního pole StorSimple.

## <a name="references"></a>Odkazy
Hledáte starší poznámku k vydání? Přejít na: 

* [Aktualizace virtuálního pole StorSimple 0.3 Poznámky k verzi](storsimple-ova-update-03-release-notes.md)
* [Aktualizace virtuálního pole StorSimple 0.1 a 0.2 Poznámky k verzi](storsimple-ova-update-01-release-notes.md)
* [Poznámky k verzi pro všeobecné dostupnosti storSimple Virtual Array](storsimple-ova-pp-release-notes.md)

