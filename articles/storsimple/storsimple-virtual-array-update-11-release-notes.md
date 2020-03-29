---
title: Poznámky k verzi StorSimple Virtual Array Update 1.1| Dokumenty společnosti Microsoft
description: Popisuje kritické otevřené problémy a řešení pro virtuální pole StorSimple se spuštěnou aktualizací 1.1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: alkohli
ms.openlocfilehash: 55103d6307614f8796c41c35d6345e1fc3aca261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60789633"
---
# <a name="storsimple-virtual-array-update-11-release-notes"></a>Poznámky k verzi aktualizace 1.1 aktualizace virtuálního pole StorSimple

## <a name="overview"></a>Přehled

Následující poznámky k verzi identifikují důležité problémy s otevřením a vyřešené problémy pro aktualizace virtuálního pole Microsoft Azure StorSimple.

Poznámky k verzi jsou průběžně aktualizovány a jako kritické problémy, které vyžadují řešení jsou zjištěny, jsou přidány. Před nasazením virtuálního pole StorSimple pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 1.1 odpovídá verzi softwaru **10.0.10307.0**.

> [!IMPORTANT]
> - Aktualizace jsou rušivé a restartujte zařízení. Pokud vstupně-in/o probíhá, zařízení dojde k prostojům. Podrobné pokyny k instalaci aktualizace naleznete v [části Instalace aktualizace 1.1](storsimple-virtual-array-install-update-11.md).
>
> - Aktualizace 1.1 je k dispozici prostřednictvím portálu Azure pouze v případě, že vaše zařízení používá aktualizaci 1.0.

## <a name="whats-new-in-update-11"></a>Co je nového v aktualizaci 1.1

Tato aktualizace obsahuje následující vylepšení a opravy chyb:

 - Selhání zálohování – byly **vylepšeny** zvýšením odolnosti proti chybám cloudových selhání a vysokou spotřebou procesoru.
 - **Protokolování** – změny byly provedeny podrobné ho režimu protokolování, když je zařízení v relaci podpory.


## <a name="issues-fixed-in-update-11"></a>Problémy opravené v aktualizaci 1.1

Následující tabulka obsahuje souhrn problémů opravených v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Zálohování| Tato verze obsahuje změny, které zlepšily selhání zálohování zvýšením odolnosti proti selhání cloudu a vysoké využití procesoru.|
| 2 |protokolování| Tato verze obsahuje změny protokolování, zatímco zařízení je v relaci podpory v podrobném režimu.|


## <a name="known-issues-in-update-11"></a>Známé problémy v aktualizaci 1.1

Následující tabulka obsahuje souhrn známých problémů pro virtuální pole StorSimple a obsahuje problémy s poznámkou k vydání z předchozích verzí.

| Ne. | Funkce | Problém | Řešení/komentáře |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Virtuální pole vytvořená ve verzi preview nelze aktualizovat na podporovanou verzi obecné dostupnosti. |Tato virtuální pole musí být převzetí služeb při selhání pro vydání obecné dostupnosti pomocí pracovního postupu zotavení po havárii (DR). |
| **2.** |Zřízený datový disk |Jakmile zřídíte datový disk určité zadané velikosti a vytvoříte odpovídající virtuální pole StorSimple, nesmíte datový disk rozbalit ani zmenšit. Pokus o to má za následek ztrátu všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Pokud je zařízení připojeno k doméně, použití zásad skupiny může nepříznivě ovlivnit provoz zařízení. |Ujistěte se, že vaše virtuální pole je ve své vlastní organizační jednotce (OU) pro službu Active Directory a nejsou na něj použity žádné objekty zásad skupiny (GPO). |
| **4.** |Místní webové uživatelské prostředí |Pokud jsou v aplikaci Internet Explorer (IE ESC) povoleny rozšířené funkce zabezpečení, nemusí některé místní webové stránky uživatelského rozhraní, například Poradce při potížích nebo údržba, pracovat správně. Tlačítka na těchto stránkách také nemusí fungovat. |Vypněte rozšířené funkce zabezpečení v aplikaci Internet Explorer. |
| **5.** |Místní webové uživatelské prostředí |Ve virtuálním počítači Hyper-V jsou síťová rozhraní ve webovém uživatelském rozhraní zobrazena jako rozhraní 10 Gb/s. |Toto chování je odrazem Hyper-V. Technologie Hyper-V vždy zobrazuje 10 Gb/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Uzamčení rozsahu bajtů pro aplikace, které pracují s vrstvenými svazky StorSimple, není podporováno. Pokud je povoleno uzamčení rozsahu bajtů, StorSimple vrstvení nefunguje. |Mezi doporučená opatření patří: <br></br>Vypněte uzamčení rozsahu bajtů v logice aplikace.<br></br>Zvolte, zda chcete umístit data pro tuto aplikaci do místně vázaných svazků na rozdíl od vrstvených svazků.<br></br>*Upozornění:* Pokud je povoleno použití místně vázaných svazků a zamykání rozsahu bajtů, místně vázaný svazek může být online ještě před dokončením obnovení. V takových případech pokud probíhá obnovení, musíte počkat na dokončení obnovení. |
| **7.** |Vrstvené sdílené složky |Práce s velkými soubory může mít za následek pomalé vrstvy ven. |Při práci s velkými soubory doporučujeme, aby největší soubor byl menší než 3 % velikosti sdílené složky. |
| **8.** |Použitá kapacita pro sdílené složky |Spotřeba sdílení se může zobrazit, pokud ve sdílené složce nejsou žádná data. Tato spotřeba je proto, že použitá kapacita pro sdílené složky obsahuje metadata. | |
| **9.** |Zotavení po havárii |Zotavení po havárii souborového serveru lze provést pouze ve stejné doméně jako zdrojové zařízení. Zotavení po havárii na cílové zařízení v jiné doméně není v této verzi podporováno. |To je implementováno v novější verzi. Další informace naleznete v oblasti [obnovení služeb při selhání a zotavení po havárii pro virtuální pole StorSimple](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Virtuální pole StorSimple nelze spravovat prostřednictvím prostředí Azure PowerShell v této verzi. |Veškerá správa virtuálních zařízení by měla být provedena prostřednictvím portálu Azure a místního webového uživatelského prostředí. |
| **11.** |Změna hesla |Konzola virtuálního pole zařízení přijímá vstup pouze ve formátu en-us klávesnice. | |
| **12.** |CHAP |Pověření chap po vytvoření nelze odebrat. Navíc pokud změníte pověření CHAP, je třeba převést svazky do režimu offline a převést je do režimu online, aby se změna projevila. |Tento problém je vyřešen v novější verzi. |
| **13.** |Server iSCSI |Použité úložiště zobrazené pro svazek iSCSI se může lišit ve službě StorSimple Device Manager a v hostiteli iSCSI. |Hostitel iSCSI má zobrazení souborového systému.<br></br>Zařízení vidí bloky přidělené, když byl svazek v maximální velikosti. |
| **14.** |Souborový server |Pokud je k souboru ve složce přidružen alternativní datový proud (ADS), není služba ADS zálohována ani obnovena prostřednictvím zotavení po havárii, klonování a obnovení úrovně položky. | |
| **15.** |Souborový server |Symbolické odkazy nejsou podporovány. | |
| **16.** |Souborový server |Soubory chráněné systémem SOUBORŮ EFS systému Windows při kopírování nebo ukládání na souborovém serveru StorSimple Virtual Array mají za následek nepodporovanou konfiguraci.  | |
| **17.** |Aktualizace |Pokud se při pokusu o instalaci opravy hotfix prostřednictvím místního uživatelského rozhraní zobrazí kód chyby: 2359302 (hex 0x240006), znamená to, že oprava hotfix je již v zařízení nainstalována.   | |
| **18.** |Aktualizace |Pokud k instalaci aktualizace 1 do virtuálního pole použijete místní webové uživatelské uživatelské tlačítko, musíte zajistit, že používáte aktualizaci 0.6. Pokud používáte verzi nižší než aktualizace 0.6, musíte nejprve nainstalovat aktualizaci 0.6 a potom nainstalovat aktualizaci 1. Pokud přímo nainstalujete aktualizaci 1.0 z verze před aktualizací 0.6, pak vám budou chybět některé aktualizace a monitorovací grafy nebudou fungovat.   | |


## <a name="next-steps"></a>Další kroky
[Nainstalujte aktualizaci 1.1](storsimple-virtual-array-install-update-11.md) do virtuálního pole StorSimple.

## <a name="references"></a>Odkazy
Hledáte starší poznámku k vydání? Přejít na:
* [Poznámky k verzi 1.0 aktualizace virtuálního pole StorSimple](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 Poznámky k verzi](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 Poznámky k verzi](storsimple-virtual-array-update-05-release-notes.md)
* [Aktualizace virtuálního pole StorSimple 0.4 Poznámky k verzi](storsimple-virtual-array-update-04-release-notes.md)
* [Aktualizace virtuálního pole StorSimple 0.3 Poznámky k verzi](storsimple-ova-update-03-release-notes.md)
* [Aktualizace virtuálního pole StorSimple 0.1 a 0.2 Poznámky k verzi](storsimple-ova-update-01-release-notes.md)
* [Poznámky k verzi pro všeobecné dostupnosti storSimple Virtual Array](storsimple-ova-pp-release-notes.md)
