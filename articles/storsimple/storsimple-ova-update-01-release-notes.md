---
title: Poznámky k verzi StorSimple Virtual Array aktualizace | Dokumentace Microsoftu
description: Pro StorSimple Virtual Array spuštění aktualizace 0.2 a 0.1 popisuje důležité otevřených problémů a jejich řešení.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: aad60024187ca180c002f119f4b975e8f69796e5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60629284"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Poznámky k verzi StorSimple Virtual Array aktualizace 0.2 a 0.1
## <a name="overview"></a>Přehled
Následující zpráva k vydání verze Identifikujte kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple Virtual Array aktualizace. (Microsoft Azure StorSimple Virtual Array je označované také jako virtuální zařízení StorSimple v místním nebo virtuálním zařízením StorSimple.) 

Zpráva k vydání verze se průběžně aktualizuje, a při zjištění zásadních problémů vyžadujících alternativní řešení, se přidají. Než nasadíte virtuální zařízení StorSimple, pečlivě si prostudujte informace obsažené v poznámkách k verzi.

Aktualizace 0.2 odpovídá verzi softwaru **10.0.10280.0**; Je verzi aktualizace 0.1 **10.0.10279.0**. Níže uvedených částech najdete seznam změn pro každou aktualizaci. 

> [!NOTE]
> Aktualizace se jedná o narušující a restartuje vaše zařízení. Pokud probíhají vstupně-výstupních operací, zařízení budou účtovat za výpadky.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Chyby opravené v aktualizace 0.2
Aktualizace 0.2 obsahuje všechny změny z aktualizace 0.1 kromě opravy popsané v následující tabulce:

| Funkce | Problém |
| --- | --- |
| Aktualizace |V poslední verzi nebyly zjištěny aktualizace automaticky na portálu Azure classic, takže jste museli instalace aktualizací pomocí místního webového uživatelského rozhraní. Tento problém je vyřešen v této verzi. Budoucí aktualizace pomocí portálu Azure classic můžete nainstalovat po instalaci aktualizace 0.2. |

## <a name="whats-new-in-the-update-01"></a>Co je nového v aktualizaci 0.1
Aktualizace 0.1 obsahuje následující opravy chyb a vylepšení. 

* **Lepší odolnost proti chybám nedošlo k výpadku cloudu**: Tato verze má několik oprav chyb po zotavení po havárii, zálohování, obnovení a ovládání datových vrstev v případě přerušení připojení cloudu. 
* **Vylepšení výkonu obnovení**: Tato verze obsahuje opravy chyb, které mají výrazně zkrátit čas dokončení úlohy obnovení.
* **Automatické optimalizace recyklace místa**: Při odstranění dat na dynamicky zřizované svazky bloky nevyužité úložiště je potřeba uvolnit. Tato verze přináší Vylepšená procesu recyklace místa z cloudu, což vede k nevyužité místo stále k dispozici rychleji v porovnání s předchozími verzemi.
* **Nové Image virtuálního disku**: Nový virtuální pevný disk VHDX a VMDK jsou teď dostupné prostřednictvím portálu Azure classic. Tyto Image zřizovat nové aktualizace 0.1 zařízení si můžete stáhnout.
* **Zlepšení přesnosti stav úlohy na portálu**: V dřívější verzi softwaru nebyl detailní stav úlohy vytváření sestav na portálu. Tento problém je vyřešen v této verzi.
* **Prostředí pro připojení k doméně**: Opravy chyb souvisejících s připojení k doméně a přejmenování zařízení.

## <a name="issues-fixed-in-the-update-01"></a>Chyby opravené v této aktualizace 0.1
Následující tabulka obsahuje souhrn chyby opravené v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |VMDK |V některých verzích VMware disk s operačním systémem viděla jako zhuštěné způsobí upozornění a přerušení běžného provozu. Tato chyba byla opravena v této verzi. |
| 2 |iSCSI server |V poslední verzi uživatele bylo nutné určit bránu pro každé rozhraní povolené síťové virtuální zařízení StorSimple. Toto chování se změnilo v této verzi tak, aby uživatel měl nakonfigurovat aspoň jednu bránu pro všechny povolené síťové rozhraní. |
| 3 |Balíček pro podporu. |Ve starší verzi softwaru podporují kolekci balíčku selhalo při velikosti balíčku byly větší než 1 GB. Tento problém je vyřešen v této verzi. |
| 4 |Přístup pro cloud |V poslední verzi StorSimple Virtual Array nemá připojení k síti a byl restartován, by místního uživatelského rozhraní máte potíže s připojením. Tento problém je vyřešen v této verzi. |
| 5 |Grafy monitorování |V předchozí verzi, po převzetí služeb zařízení grafy využití kapacity cloudu zobrazují nesprávné hodnoty na portálu Azure classic. Tento problém je vyřešený v aktuální verzi. |

## <a name="known-issues-in-the-update-01"></a>Známé problémy v aktualizace 0.1
Následující tabulka obsahuje souhrn známé problémy pro StorSimple Virtual Array a problémy uvedené verze z předchozích verzí. **Verze problémy uvedené v této verzi jsou označeny hvězdičkou. Téměř všechny problémy v tomto seznamu mají přeneseny z verze GA StorSimple Virtual Array.**

| Ne. | Funkce | Problém | Alternativní řešení a poznámky |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Virtuální zařízení, vytvořené ve verzi preview, nejde aktualizovat na podporovanou verzi obecné dostupnosti. |Tato virtuální zařízení musí být převzetí služeb při selhání pro všeobecně dostupné verze pomocí pracovního postupu zotavení po havárii. |
| **2.** |Zřízené datového disku |Po zřízení některých zadané velikosti datového disku a vytvoří odpovídající virtuální zařízení StorSimple, nesmí zvětšení nebo zmenšení datový disk. To pokusíte, bude mít za následek ztrátu všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Když je zařízení připojené k doméně, použití zásad skupiny nepříznivě ovlivnit operace zařízení. |Ujistěte se, že vaše virtuální pole je ve vlastní organizační jednotka (OU) služby Active Directory a žádné objekty zásad skupiny (GPO) se použijí k němu. |
| **4.** |Místního webového uživatelského rozhraní |Pokud jsou povolené rozšířené funkce zabezpečení v aplikaci Internet Explorer (IE ESC), některé místního webového uživatelského rozhraní stránky jako je například Poradce při potížích nebo údržby nemusí fungovat správně. Tlačítka na těchto stránkách nemusí fungovat. |Vypněte rozšířené funkce zabezpečení v Internet Exploreru. |
| **5.** |Místního webového uživatelského rozhraní |Ve virtuálním počítači Hyper-V síťová rozhraní ve webovém uživatelském rozhraní se zobrazují jako 10 GB/s rozhraní. |Toto chování je odraz technologie Hyper-V. Technologie Hyper-V vždy zobrazovat 10 GB/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Rozsah bajtů uzamčení pro aplikace, které využívají StorSimple vrstvených svazků se nepodporuje. Pokud je povolený zámek rozsah bajtů, StorSimple ovládání datových vrstev nebude fungovat. |Doporučené opatření patří: <br></br>Vypněte rozsah bajtů zamykání v logice aplikace.<br></br>Tlačítko Vložit data pro tuto aplikaci místně připojené svazky na rozdíl od vrstvených svazků.<br></br>*Výstražné*: Pokud je povolený zámek rozsah bajtů pomocí místně připojené svazky, mějte na paměti, že místně vázaný svazek může být online, ještě než se obnovení dokončí. V takových případech Pokud obnovení probíhá, pak je musí počkat na dokončení obnovení. |
| **7.** |Vrstvené sdílené složky |Práce s velkými soubory by mohlo způsobit zmenšení pomalé vrstvy. |Při práci s velkými soubory, doporučujeme vám, že je největší soubor menší než velikost sdílené složky % 3. |
| **8.** |Použít kapacity pro sdílené složky |Může se zobrazit sdílet spotřeby neexistují žádná data ve sdílené složce. Je to proto, že zahrnuje jejich metadata použitá kapacita pro sdílené složky. | |
| **9.** |Zotavení po havárii |Lze provést pouze zotavení po havárii souborového serveru ke stejné doméně jako zdrojové zařízení. V této verzi nepodporuje zotavení po havárii do cílové zařízení v jiné doméně. |To se provádí v novější verzi. |
| **10.** |Azure PowerShell |Virtuální zařízení StorSimple nejde spravovat prostřednictvím Azure Powershellu v této verzi. |Veškerá Správa virtuálního zařízení se provádí prostřednictvím portálu Azure classic a místního webového uživatelského rozhraní. |
| **11.** |Změna hesla |Zařízení konzoly virtuálního pole přijímá pouze vstup ve formátu klávesnice en US. | |
| **12.** |CHAP |Přihlašovací údaje protokolu CHAP po vytvoření nelze odebrat. Kromě toho pokud změníte přihlašovací údaje protokolu CHAP, musíte převést svazky do režimu offline a pak přiřaďte je online se změna projevila. |Toto bude vyřešen v pozdější verzi. |
| **13.** |iSCSI server |"Použili úložiště" zobrazí u svazku iSCSI, může být jiný ve službě StorSimple Manager a iSCSI hostitele. |Zobrazení systému souborů má hostitel iSCSI.<br></br>Zařízení se zobrazí bloky přidělí, když objem byl při maximální velikosti. |
| **14.** |Soubor serveru * |Pokud má soubor ve složce alternativní datový Stream (reklamy) s ním spojená, reklamy nejsou zálohovány ani obnoveny prostřednictvím zotavení po havárii, klonování a obnovení na úrovni položek. | |

## <a name="next-step"></a>Další krok
[Instalace aktualizací](storsimple-ova-install-update-01.md) na StorSimple Virtual Array.

