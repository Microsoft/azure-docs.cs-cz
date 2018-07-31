---
title: Poznámky k verzi StorSimple Virtual Array Update 1.1 | Dokumentace Microsoftu
description: Popisuje důležité otevřených problémů a jejich řešení pro StorSimple Virtual Array spuštění aktualizace 1.1.
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
ms.openlocfilehash: d73f45ec5ff1ffbe207fc45a1f87dcbe4f8ff021
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39348259"
---
# <a name="storsimple-virtual-array-update-11-release-notes"></a>Poznámky k verzi StorSimple Virtual Array Update 1.1

## <a name="overview"></a>Přehled

Následující zpráva k vydání verze Identifikujte kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple Virtual Array aktualizace.

Zpráva k vydání verze se průběžně aktualizuje, a při zjištění zásadních problémů vyžadujících alternativní řešení, se přidají. Před nasazením StorSimple Virtual Array, pečlivě si prostudujte informace obsažené v poznámkách k verzi.

Aktualizace 1.1 odpovídá verzi softwaru **10.0.10307.0**.

> [!IMPORTANT]
> - Aktualizace se jedná o narušující a restartujte zařízení. Pokud probíhají vstupně-výstupních operací, zařízení způsobí výpadek. Podrobné pokyny o tom, jak použít aktualizaci, přejděte na [nainstalovat aktualizace 1.1](storsimple-virtual-array-install-update-11.md).
>
> - Aktualizace 1.1 je prostřednictvím portálu Azure portal k dispozici pouze v případě, že vaše zařízení používá Update 1.0.

## <a name="whats-new-in-update-11"></a>Co je nového v aktualizaci 1.1

Tato aktualizace obsahuje následující vylepšení a opravy chyb:

 - **Selhání zálohování** -byly vylepšeny zvýšením odolnost vůči selhání cloudových a vysoké využití procesoru.
 - **Protokolování** -byly provedeny změny režim podrobného vypisování protokolování, když je zařízení v relaci.


## <a name="issues-fixed-in-update-11"></a>Chyby opravené v aktualizaci 1.1

Následující tabulka obsahuje souhrn chyby opravené v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Zálohování| Tato verze obsahuje změny, které mají vylepšit zvyšuje odolnost vůči selhání cloudových a vysoké využití procesoru případné selhání zálohování.|
| 2 |Protokolování| Tato verze obsahuje změny protokolování, když zařízení pracuje v relaci v režimu s komentářem.|


## <a name="known-issues-in-update-11"></a>Známé problémy v aktualizaci 1.1

Následující tabulka obsahuje souhrn známé problémy pro StorSimple Virtual Array a problémy uvedené verze z předchozích verzí.

| Ne. | Funkce | Problém | Alternativní řešení a poznámky |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Virtuální pole vytvořené ve verzi preview, nejde aktualizovat na podporovanou verzi obecné dostupnosti. |Tyto virtuální pole musí být převzetí služeb při selhání pro všeobecně dostupné verze pomocí pracovního postupu zotavení po havárii. |
| **2.** |Zřízené datového disku |Po zřízení některých zadané velikosti datového disku a vytvoří odpovídající StorSimple Virtual Array, nesmí zvětšení nebo zmenšení datový disk. Pokus výsledky za následek ztrátu všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Když je zařízení připojené k doméně, použití zásad skupiny nepříznivě ovlivnit operace zařízení. |Ujistěte se, že vaše virtuální pole je ve vlastní organizační jednotka (OU) služby Active Directory a žádné objekty zásad skupiny (GPO) se použijí k němu. |
| **4.** |Místního webového uživatelského rozhraní |Pokud jsou povolené rozšířené funkce zabezpečení v aplikaci Internet Explorer (IE ESC), některé místního webového uživatelského rozhraní stránky jako je například Poradce při potížích nebo údržby nemusí fungovat správně. Tlačítka na těchto stránkách nemusí fungovat. |Vypněte rozšířené funkce zabezpečení v Internet Exploreru. |
| **5.** |Místního webového uživatelského rozhraní |Ve virtuálním počítači Hyper-V síťová rozhraní ve webovém uživatelském rozhraní se zobrazují jako 10 GB/s rozhraní. |Toto chování je odraz technologie Hyper-V. Technologie Hyper-V vždy zobrazovat 10 GB/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Rozsah bajtů uzamčení pro aplikace, které využívají StorSimple vrstvených svazků se nepodporuje. Pokud je povolený zámek rozsah bajtů, StorSimple ovládání datových vrstev nebude fungovat. |Doporučené opatření patří: <br></br>Vypněte rozsah bajtů zamykání v logice aplikace.<br></br>Tlačítko Vložit data pro tuto aplikaci místně připojené svazky na rozdíl od vrstvených svazků.<br></br>*Výstražné*: když je povolený zámek rozsah bajtů pomocí místně připojené svazky, místně vázaný svazek může být online, ještě předtím, než se obnovení dokončí. V takových případech Pokud obnovení probíhá, pak je musí počkat na dokončení obnovení. |
| **7.** |Vrstvené sdílené složky |Práce s velkými soubory by mohlo způsobit zmenšení pomalé vrstvy. |Při práci s velkými soubory, doporučujeme vám, že je největší soubor menší než velikost sdílené složky % 3. |
| **8.** |Použít kapacity pro sdílené složky |Může se zobrazit sdílet spotřeby, když nejsou žádná data ve sdílené složce. Toto využití je, protože zahrnuje jejich metadata použitá kapacita pro sdílené složky. | |
| **9.** |Zotavení po havárii |Lze provést pouze zotavení po havárii souborového serveru ke stejné doméně jako zdrojové zařízení. V této verzi nepodporuje zotavení po havárii do cílové zařízení v jiné doméně. |To je implementováno v novější verzi. Další informace najdete v části [převzetí služeb při selhání a zotavení po havárii pro StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Virtuální pole StorSimple, nejde spravovat pomocí Azure Powershellu v této verzi. |Veškerá Správa virtuálního zařízení se provádí prostřednictvím webu Azure portal a místního webového uživatelského rozhraní. |
| **11.** |Změna hesla |Zařízení konzoly virtuálního pole přijímá pouze vstupu v en-us klávesnice formátu. | |
| **12.** |PROTOKOL CHAP |Přihlašovací údaje protokolu CHAP po vytvoření nelze odebrat. Kromě toho pokud změníte přihlašovací údaje protokolu CHAP, musíte převést svazky do režimu offline a pak přiřaďte je online se změna projevila. |Tento problém je vyřešen v pozdější verzi. |
| **13.** |iSCSI server |"Použili úložiště" zobrazí u svazku iSCSI, může být jiný ve službě Správce zařízení StorSimple a iSCSI hostitele. |Zobrazení systému souborů má hostitel iSCSI.<br></br>Zařízení se zobrazí bloky přidělí, když objem byl při maximální velikosti. |
| **14.** |Souborový server |Pokud má soubor ve složce alternativní datový Stream (reklamy) s ním spojená, reklamy nejsou zálohovány ani obnoveny prostřednictvím zotavení po havárii, klonování a obnovení na úrovni položek. | |
| **15.** |Souborový server |Symbolické odkazy nejsou podporovány. | |
| **16.** |Souborový server |Soubory chráněné službou Windows systém (ENCRYPTING File) po zkopírování přes nebo uložený na výsledek StorSimple Virtual Array souboru server má nepodporovanou konfiguraci.  | |
| **17.** |Aktualizace |Pokud se zobrazí chyby kódu: 2359302 (šestnáctkově 0x240006) při pokusu o instalaci oprav hotfix pomocí místního uživatelského rozhraní, pak z toho vyplývá, že oprava hotfix nainstalovaná ve vašem zařízení.   | |
| **18.** |Aktualizace |Pokud instalace Update 1 na vaše virtuální pole pomocí místního webového uživatelského rozhraní, musíte zajistit, že používáte aktualizace 0.6. Pokud používáte verzi nižší než aktualizace 0.6, musíte nejprve nainstalovat aktualizace 0.6 a pak použít Update 1. Pokud přímo z verze před aktualizací 0,6 nainstalovat Update 1.0, pak bude chybí některé aktualizace a grafy monitorování nebude fungovat.   | |


## <a name="next-steps"></a>Další postup
[Instalace aktualizace 1.1](storsimple-virtual-array-install-update-11.md) na StorSimple Virtual Array.

## <a name="references"></a>Odkazy
Hledáte starší poznámku k verzi? Přejít na:
* [StorSimple Virtual Array Update 1.0 poznámky](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0,6 poznámky](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0,5 poznámky](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array aktualizace 0.4 poznámky](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 poznámky](storsimple-ova-update-03-release-notes.md)
* [Poznámky k verzi StorSimple Virtual Array Update 0.1 a 0.2](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array obecné dostupnosti zpráva k vydání verze](storsimple-ova-pp-release-notes.md)
