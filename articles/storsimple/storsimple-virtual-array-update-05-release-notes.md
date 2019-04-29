---
title: StorSimple Virtual Array aktualizace 0,5 poznámky k verzi | Dokumentace Microsoftu
description: Pro StorSimple Virtual Array spuštění aktualizace 0.5 popisuje důležité otevřených problémů a jejich řešení.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870668"
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>StorSimple Virtual Array aktualizace 0,5 poznámky

## <a name="overview"></a>Přehled

Následující zpráva k vydání verze Identifikujte kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple Virtual Array aktualizace.

Zpráva k vydání verze se průběžně aktualizuje, a při zjištění zásadních problémů vyžadujících alternativní řešení, se přidají. Před nasazením StorSimple Virtual Array, pečlivě si prostudujte informace obsažené v poznámkách k verzi.

Aktualizace 0.5 odpovídá verzi softwaru **10.0.10290.0**.

> [!NOTE]
> Aktualizace se jedná o narušující a restartujte zařízení. Pokud probíhají vstupně-výstupních operací, zařízení způsobí výpadek. Podrobné pokyny o tom, jak použít aktualizaci, přejděte na [instalace aktualizace 0.5](storsimple-virtual-array-install-update-05.md).


## <a name="whats-new-in-the-update-05"></a>Co je nového v aktualizace 0.5
Aktualizace 0.5 je primárně oprava chyby sestavení. Hlavní vylepšení a opravy chyb jsou takto:

- **Vylepšení odolnosti proti chybám zálohování** – tato verze obsahuje opravy, které vylepšují odolnost proti chybám zálohování. V dřívějších verzích byly zálohování opakovat jenom pro určité výjimky. Tato verze opakování zálohování výjimky a díky odolnější zálohy.

- **Aktualizace pro sledování využití úložiště** – od 30. června 2017, úložiště využití sledování pro řada virtuálních zařízení StorSimple se přestane nabízet. To platí pro grafy monitorování na všechny virtuální pole s verzí Update 0.4 nebo nižší. Tato aktualizace obsahuje změny, které potřebujete, chcete-li pokračovat v používání monitorování využití úložiště na webu Azure Portal. Nainstalujte tuto aktualizaci před 30. června 2017 můžete pokračovat v používání funkce sledování.


## <a name="issues-fixed-in-the-update-05"></a>Chyby opravené v aktualizace 0.5

Následující tabulka obsahuje souhrn chyby opravené v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Zálohování odolnost proti chybám| V dřívějších verzích byly zálohování opakovat jenom pro určité výjimky. Tato verze obsahuje opravu provést zálohování odolnější opakováním zálohování výjimky.|
| 2 |Monitorování| Monitorování využití úložiště pro řada virtuálních zařízení StorSimple bude zastaralé od 30. června 2017. Tato akce ovlivní monitorovacích grafů ve službě Správce zařízení StorSimple běží na virtuálních polí StorSimple (1200 model). Tato verze obsahuje aktualizace, které uživateli umožní pokračovat v používání monitorování využití úložiště pro virtuální pole nad rámec 30. června 2017.|
| 3 |Souborový server| V dřívějších verzích může uživatel omylem zkopírujte šifrované soubory pro virtuální pole. Tato verze obsahuje opravu, která nepovolí kopírování zašifrovaných souborů do virtuálního pole. Pokud vaše zařízení má existující šifrované soubory před aktualizací, zálohování bude pokračovat fungovat, dokud všechny šifrované soubory se odstraní ze systému. |


## <a name="known-issues-in-the-update-05"></a>Známé problémy v aktualizace 0.5

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
| **8.** |Použít kapacity pro sdílené složky |Může se zobrazit sdílet spotřeby, když nejsou žádná data ve sdílené složce. Toto využití je, protože zahrnuje jejich metadata použitá kapacita pro sdílené složky. | |
| **9.** |Zotavení po havárii |Lze provést pouze zotavení po havárii souborového serveru ke stejné doméně jako zdrojové zařízení. V této verzi nepodporuje zotavení po havárii do cílové zařízení v jiné doméně. |To je implementováno v novější verzi. Další informace najdete v části [převzetí služeb při selhání a zotavení po havárii pro StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Virtuální zařízení StorSimple nejde spravovat prostřednictvím Azure Powershellu v této verzi. |Veškerá Správa virtuálního zařízení se provádí prostřednictvím webu Azure portal a místního webového uživatelského rozhraní. |
| **11.** |Změna hesla |Zařízení konzoly virtuálního pole přijímá pouze vstupu v en-us klávesnice formátu. | |
| **12.** |CHAP |Přihlašovací údaje protokolu CHAP po vytvoření nelze odebrat. Kromě toho pokud změníte přihlašovací údaje protokolu CHAP, musíte převést svazky do režimu offline a pak přiřaďte je online se změna projevila. |Tento problém je vyřešen v pozdější verzi. |
| **13.** |iSCSI server |"Použili úložiště" zobrazí u svazku iSCSI, může být jiný ve službě Správce zařízení StorSimple a iSCSI hostitele. |Zobrazení systému souborů má hostitel iSCSI.<br></br>Zařízení se zobrazí bloky přidělí, když objem byl při maximální velikosti. |
| **14.** |Souborový server |Pokud má soubor ve složce alternativní datový Stream (reklamy) s ním spojená, reklamy nejsou zálohovány ani obnoveny prostřednictvím zotavení po havárii, klonování a obnovení na úrovni položek. | |
| **15.** |Souborový server |Symbolické odkazy nejsou podporovány. | |
| **16.** |Souborový server |Soubory chráněné službou Windows systém (ENCRYPTING File) po zkopírování přes nebo uložený na výsledek StorSimple Virtual Array souboru server má nepodporovanou konfiguraci.  | |

## <a name="next-step"></a>Další krok
[Instalace aktualizace 0.5](storsimple-virtual-array-install-update-05.md) na StorSimple Virtual Array.

## <a name="references"></a>Odkazy
Hledáte starší poznámku k verzi? Přejít na:

* [StorSimple Virtual Array aktualizace 0.4 poznámky](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 poznámky](storsimple-ova-update-03-release-notes.md)
* [Poznámky k verzi StorSimple Virtual Array Update 0.1 a 0.2](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array obecné dostupnosti zpráva k vydání verze](storsimple-ova-pp-release-notes.md)

