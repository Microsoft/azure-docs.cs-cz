---
title: Poznámky k verzi aktualizace aktualizace StorSimple Virtual Array Updates| Dokumenty společnosti Microsoft
description: Popisuje kritické otevřené problémy a řešení pro virtuální pole StorSimple se spuštěnou aktualizací 0.3.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: b197651a-3c40-4185-b23d-4c8f22cfa8f4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli
ms.openlocfilehash: 635b5f4edf5d403c569b4957540fc105997b3e8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60629267"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>StorSimple Virtual Array Update 0.3 poznámky k verzi
## <a name="overview"></a>Přehled
Následující poznámky k verzi identifikují důležité problémy s otevřením a vyřešené problémy pro aktualizace virtuálního pole Microsoft Azure StorSimple.

Poznámky k verzi jsou průběžně aktualizovány a jako kritické problémy, které vyžadují řešení jsou zjištěny, jsou přidány. Před nasazením virtuálního pole StorSimple pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 0.3 odpovídá verzi softwaru **10.0.10288.0**.

> [!NOTE]
> Aktualizace jsou rušivé a restartujte zařízení. Pokud vstupně-in/o probíhá, zařízení dojde k prostojům.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Co je nového v aktualizaci 0.3
Aktualizace 0.3 je především sestavení opravy chyb. V této verzi bylo vyřešeno několik chyb, které vedly k selhání zálohování v předchozí verzi.

## <a name="issues-fixed-in-the-update-03"></a>Problémy opravené v aktualizaci 0.3
Následující tabulka obsahuje souhrn problémů opravených v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Zálohování |Problém byl viděn v dřívější verzi, kde zálohy by se nepodařilo dokončit pro sdílenou složku. Pokud k tomuto problému došlo, úloha zálohování by se nezdaří a kritické výstrahy byla vyvolána ve službě StorSimple Manager upozornit uživatele. Tento problém neměl vliv na data o sdílených sporecích nebo přístup k datům. Hlavní příčina byla identifikována a opravena v této verzi. <br></br> Oprava se nevztahuje zpětně na sdílené složky, které již vidí tento problém. Zákazníci, kteří se unějí tento problém by nejprve použít aktualizaci 0.3, pak se obraťte na podporu společnosti Microsoft provést úplnou zálohu systému k vyřešení problému. Namísto kontaktování podpory společnosti Microsoft mohou zákazníci také obnovit novou sdílenou složku ze zálohy v pořádku pro ohrožené sdílené složky. |
| 2 |iSCSI |Problém byl viděn v dřívější verzi, kde svazky zmizí při kopírování dat do svazku na StorSimple Virtual Array. Tento problém byl opraven v této verzi. <br></br> Opravy se projeví pouze na nově vytvořených svazcích. Opravy se nevztahují zpětně na svazky, které jsou již vidět tento problém. Zákazníkům se doporučuje přenést ovlivněné svazky do režimu online prostřednictvím klasického portálu Azure, provést zálohování pro tyto svazky a potom tyto svazky obnovit do nových svazků. |

## <a name="known-issues-in-the-update-03"></a>Známé problémy v aktualizaci 0.3
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

## <a name="next-step"></a>Další krok
[Nainstalujte aktualizaci 0.3](storsimple-ova-install-update-01.md) do virtuálního pole StorSimple.

## <a name="references"></a>Odkazy
Hledáte starší poznámku k vydání? Přejít na: 

* [Aktualizace virtuálního pole StorSimple 0.1 a 0.2 Poznámky k verzi](storsimple-ova-update-01-release-notes.md)
* [Poznámky k verzi pro všeobecné dostupnosti storSimple Virtual Array](storsimple-ova-pp-release-notes.md)

