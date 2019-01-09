---
title: Poznámky k verzi StorSimple Virtual Array aktualizace | Dokumentace Microsoftu
description: Pro StorSimple Virtual Array s verzí Update 0.3 popisuje důležité otevřených problémů a jejich řešení.
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
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117669"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>StorSimple Virtual Array aktualizace 0.3 poznámky
## <a name="overview"></a>Přehled
Následující zpráva k vydání verze Identifikujte kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple Virtual Array aktualizace.

Zpráva k vydání verze se průběžně aktualizuje, a při zjištění zásadních problémů vyžadujících alternativní řešení, se přidají. Před nasazením StorSimple Virtual Array, pečlivě si prostudujte informace obsažené v poznámkách k verzi.

Aktualizace 0.3 odpovídá verzi softwaru **10.0.10288.0**.

> [!NOTE]
> Aktualizace se jedná o narušující a restartujte zařízení. Pokud probíhají vstupně-výstupních operací, zařízení způsobí výpadek.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Co je nového v aktualizace 0.3
Aktualizace 0.3 je primárně oprava chyby sestavení. V této verzi byla vyřešili několik chyb, což vede k selhání zálohování v předchozí verzi.

## <a name="issues-fixed-in-the-update-03"></a>Chyby opravené v této aktualizace 0.3
Následující tabulka obsahuje souhrn chyby opravené v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Zálohování |Problém byl viděli v předchozí verzi, ve kterém by nepovede dokončit. sdílené složky záloh. Pokud došlo k tomuto problému, úloha zálohování selže a Kritická výstraha byla vyvolána ve službě StorSimple Manager upozorní uživatele. Tento problém neovlivnila dat na sdílených složek nebo přístup k datům. Byla zjištěna a opravené v této verzi odstraníte hlavní příčinu. <br></br> Oprava nevztahuje na zařazené do sdílené složky, které se už zobrazuje tento problém. Zákazníci, kteří se zobrazuje tento problém by měly nejprve aktualizace 0.3 měli kontaktovat Microsoft Support provést úplné zálohy systému k vyřešení problému. Místo kontaktovat Microsoft Support zákazníků můžete také obnovit do nové sdílené složky ze zálohy v pořádku pro ovlivněné sdílené složky. |
| 2 |iSCSI |Problém byl viděli v dřívější verzi, kdy svazky zmizí při kopírování dat do svazku na StorSimple Virtual Array. Tento problém byl vyřešen v této verzi. <br></br> Tyto opravy projeví jenom na nově vytvořené svazky. Tyto opravy nevztahuje na zařazené do svazků, které se už zobrazuje tento problém. Zákazníci by měli ovlivněných svazky online prostřednictvím portálu Azure classic převést, proveďte zálohu pro tyto svazky a pak tyto svazky obnovit pro nové svazky. |

## <a name="known-issues-in-the-update-03"></a>Známé problémy v aktualizace 0.3
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
| **12.** |PROTOKOL CHAP |Přihlašovací údaje protokolu CHAP po vytvoření nelze odebrat. Kromě toho pokud změníte přihlašovací údaje protokolu CHAP, musíte převést svazky do režimu offline a pak přiřaďte je online se změna projevila. |Tento problém je vyřešen v pozdější verzi. |
| **13.** |iSCSI server |"Použili úložiště" zobrazí u svazku iSCSI, může být jiný ve službě StorSimple Manager a iSCSI hostitele. |Zobrazení systému souborů má hostitel iSCSI.<br></br>Zařízení se zobrazí bloky přidělí, když objem byl při maximální velikosti. |
| **14.** |Souborový server |Pokud má soubor ve složce alternativní datový Stream (reklamy) s ním spojená, reklamy nejsou zálohovány ani obnoveny prostřednictvím zotavení po havárii, klonování a obnovení na úrovni položek. | |

## <a name="next-step"></a>Další krok
[Instalace aktualizace 0.3](storsimple-ova-install-update-01.md) na StorSimple Virtual Array.

## <a name="references"></a>Odkazy
Hledáte starší poznámku k verzi? Přejít na: 

* [Poznámky k verzi StorSimple Virtual Array Update 0.1 a 0.2](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array obecné dostupnosti zpráva k vydání verze](storsimple-ova-pp-release-notes.md)

