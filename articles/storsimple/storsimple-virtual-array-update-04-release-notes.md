---
title: StorSimple Virtual Array aktualizace 0.4 poznámky k verzi | Dokumentace Microsoftu
description: Pro StorSimple Virtual Array spuštění aktualizace 0.4 popisuje důležité otevřených problémů a jejich řešení.
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
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54319397"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>StorSimple Virtual Array aktualizace 0.4 poznámky

## <a name="overview"></a>Přehled

Následující zpráva k vydání verze Identifikujte kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple Virtual Array aktualizace.

Zpráva k vydání verze se průběžně aktualizuje, a při zjištění zásadních problémů vyžadujících alternativní řešení, se přidají. Před nasazením StorSimple Virtual Array, pečlivě si prostudujte informace obsažené v poznámkách k verzi.

Aktualizace 0.4 odpovídá verzi softwaru **10.0.10289.0**.

> [!NOTE]
> Aktualizace se jedná o narušující a restartujte zařízení. Pokud probíhají vstupně-výstupních operací, zařízení způsobí výpadek.


## <a name="whats-new-in-the-update-04"></a>Co je nového v aktualizace 0.4
Aktualizace 0.4 je primárně oprava chyby sestavení s velkou provázaností několik vylepšení. V této verzi byla vyřešili několik chyb, což vede k selhání zálohování v předchozí verzi. Hlavní vylepšení a opravy chyb jsou takto:

- **Vylepšení výkonu zálohování** – tato verze má provedeno několik klíčových vylepšení ke zlepšení výkonu zálohování. V důsledku toho zálohy, které se týkají velký počet souborů naleznete v tématu k výraznému snížení času k dokončení úplné a přírůstkové zálohování.

- **Vylepšený výkon obnovení** – tato verze obsahuje vylepšení, která výrazně zlepšit výkon obnovení při použití velkého počtu souborů. Pokud používáte 2 – 4 miliony souborů, doporučujeme, že zřídíte virtuální pole s 16 GB paměti RAM zobrazíte vylepšení. Při použití souborů menší než 2 miliony, minimální požadavek pro virtuální počítač nadále 8 GB paměti RAM.

- **Vylepšení balíček pro podporu** – vylepšení zahrnují protokolování statistiky týkající se disku, využití procesoru, paměti, sítě a cloudu do balíčku pro podporu; tím se zvýší proces Diagnostika a ladění problémů se zařízením.

- **Limit místně připojené svazky iSCSI až 200 GB** – místně vázaných svazků, doporučujeme omezit na svazek iSCSI 200 GB na StorSimple Virtual Array. Místní rezervace pro vrstvené svazky i nadále 10 % velikosti zřízeného svazku, ale je omezené na 200 GB. 

- **Opravy chyb spojených s** – v předchozích verzích softwaru, byly nějaké problémy související s zálohy, které by mohly způsobit selhání zálohování. Tyto chyby vyřešeny byly v této verzi.


## <a name="issues-fixed-in-the-update-04"></a>Chyby opravené v této aktualizace 0.4

Následující tabulka obsahuje souhrn chyby opravené v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Výkon zálohování|V dřívějších verzích zálohy zahrnující velkého počtu souborů by trvalo dlouhou dobu pro dokončení (v pořadí podle dní). V této verzi najdete v úplných a přírůstkových záloh k výraznému snížení čas na dokončení. |
| 2 |Balíček pro podporu|Disk, procesoru, paměti, sítě a cloudové statistiky jsou nyní přihlášeni do protokolů podpory vytváření velmi efektivní s řešením problémů zařízení podpůrných balíčků.|
| 3 |Backup |V dřívějších verzích dlouho trvající záloh může vést zpracujte místo na zařízení, což vede k selhání zálohování. Tato chyba opravená tím, že více než 5 záloh do fronty v jednom okamžiku v této verzi.|
| 4 |iSCSI | V dřívějších verzích byla místní rezervace pro vrstvené nebo místně připojené svazky 10 % velikosti zřízeného svazku. V této verzi je omezené na 10 % s délkou maximálně 200 GB (pro vrstvené svazky větší než 2 TB) místní rezervace pro všechny svazky iSCSI (místně připnuté nebo vrstvené) a tím uvolnění další místo na místním disku. Doporučujeme vám, že místně připojené svazky v této verzi omezena na 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Známé problémy v aktualizace 0.4

Následující tabulka obsahuje souhrn známé problémy pro StorSimple Virtual Array a problémy uvedené verze z předchozích verzí. 

| Ne. | Funkce | Problém | Alternativní řešení a poznámky |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Virtuální zařízení, vytvořené ve verzi preview, nejde aktualizovat na podporovanou verzi obecné dostupnosti. |Tato virtuální zařízení musí být převzetí služeb při selhání pro všeobecně dostupné verze pomocí pracovního postupu zotavení po havárii. |
| **2.** |Zřízené datového disku |Po zřízení některých zadané velikosti datového disku a vytvoří odpovídající virtuální zařízení StorSimple, nesmí zvětšení nebo zmenšení datový disk. Pokus výsledky za následek ztrátu všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Když je zařízení připojené k doméně, použití zásad skupiny nepříznivě ovlivnit operace zařízení. |Ujistěte se, že vaše virtuální pole je ve vlastní organizační jednotka (OU) služby Active Directory a žádné objekty zásad skupiny (GPO) se použijí k němu. |
| **4.** |Místního webového uživatelského rozhraní |Pokud jsou povolené rozšířené funkce zabezpečení v aplikaci Internet Explorer (IE ESC), některé místního webového uživatelského rozhraní stránky jako je například Poradce při potížích nebo údržby nemusí fungovat správně. Tlačítka na těchto stránkách nemusí fungovat. |Vypněte rozšířené funkce zabezpečení v Internet Exploreru. |
| **5.** |Místního webového uživatelského rozhraní |Ve virtuálním počítači Hyper-V síťová rozhraní ve webovém uživatelském rozhraní se zobrazují jako 10 GB/s rozhraní. |Toto chování je odraz technologie Hyper-V. Technologie Hyper-V vždy zobrazovat 10 GB/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Rozsah bajtů uzamčení pro aplikace, které využívají StorSimple vrstvených svazků se nepodporuje. Pokud je povolený zámek rozsah bajtů, StorSimple ovládání datových vrstev nebude fungovat. |Doporučené opatření patří: <br></br>Vypněte rozsah bajtů zamykání v logice aplikace.<br></br>Tlačítko Vložit data pro tuto aplikaci místně připojené svazky na rozdíl od vrstvených svazků.<br></br>*Výstražné*: Když je povolený zámek rozsah bajtů pomocí místně připojené svazky, místně vázaný svazek může být online, ještě předtím, než se obnovení dokončí. V takových případech Pokud obnovení probíhá, pak je musí počkat na dokončení obnovení. |
| **7.** |Vrstvené sdílené složky |Práce s velkými soubory by mohlo způsobit zmenšení pomalé vrstvy. |Při práci s velkými soubory, doporučujeme vám, že je největší soubor menší než velikost sdílené složky % 3. |
| **8.** |Použít kapacity pro sdílené složky |Může se zobrazit sdílet spotřeby, když nejsou žádná data ve sdílené složce. Je to proto, že zahrnuje jejich metadata použitá kapacita pro sdílené složky. | |
| **9.** |Zotavení po havárii |Lze provést pouze zotavení po havárii souborového serveru ke stejné doméně jako zdrojové zařízení. V této verzi nepodporuje zotavení po havárii do cílové zařízení v jiné doméně. |To je implementováno v novější verzi. |
| **10.** |Azure PowerShell |Virtuální zařízení StorSimple nejde spravovat prostřednictvím Azure Powershellu v této verzi. |Veškerá Správa virtuálního zařízení se provádí prostřednictvím portálu Azure classic a místního webového uživatelského rozhraní. |
| **11.** |Změna hesla |Zařízení konzoly virtuálního pole přijímá pouze vstup ve formátu klávesnice en US. | |
| **12.** |CHAP |Přihlašovací údaje protokolu CHAP po vytvoření nelze odebrat. Kromě toho pokud změníte přihlašovací údaje protokolu CHAP, musíte převést svazky do režimu offline a pak přiřaďte je online se změna projevila. |Tento problém je vyřešen v pozdější verzi. |
| **13.** |iSCSI server |"Použili úložiště" zobrazí u svazku iSCSI, může být jiný ve službě StorSimple Manager a iSCSI hostitele. |Zobrazení systému souborů má hostitel iSCSI.<br></br>Zařízení se zobrazí bloky přidělí, když objem byl při maximální velikosti. |
| **14.** |Souborový server |Pokud má soubor ve složce alternativní datový Stream (reklamy) s ním spojená, reklamy nejsou zálohovány ani obnoveny prostřednictvím zotavení po havárii, klonování a obnovení na úrovni položek. | |
| **15.** |Souborový server |Symbolické odkazy nejsou podporovány. | |
| **16.** |Souborový server |Soubory chráněné službou Windows systém (ENCRYPTING File) po zkopírování přes nebo uložený na výsledek StorSimple Virtual Array souboru server má nepodporovanou konfiguraci.  | |

## <a name="next-step"></a>Další krok
[Instalace aktualizace 0.4](storsimple-virtual-array-install-update-04.md) na StorSimple Virtual Array.

## <a name="references"></a>Odkazy
Hledáte starší poznámku k verzi? Přejít na: 

* [StorSimple Virtual Array Update 0.3 poznámky](storsimple-ova-update-03-release-notes.md)
* [Poznámky k verzi StorSimple Virtual Array Update 0.1 a 0.2](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array obecné dostupnosti zpráva k vydání verze](storsimple-ova-pp-release-notes.md)

