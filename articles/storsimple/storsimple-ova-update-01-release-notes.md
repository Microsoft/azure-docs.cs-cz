---
title: Poznámky k verzi aktualizace pole virtuální zařízení StorSimple | Microsoft Docs
description: Popisuje důležité otevřené problémy a řešení pro pole virtuální zařízení StorSimple spuštění Update 0,2 a 0,1.
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
ms.openlocfilehash: c4ccde9635b3874864baa9d4d262ff5ddcf2a425
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Poznámky k verzi virtuální pole aktualizace 0,2 a 0,1 StorSimple
## <a name="overview"></a>Přehled
Následující poznámky k verzi identifikovat kritická otevřené problémy a vyřešit problémy pro Microsoft Azure StorSimple virtuální pole aktualizace. (Microsoft Azure StorSimple virtuální pole je také označované jako místní virtuální zařízení StorSimple nebo virtuální zařízení StorSimple.) 

Poznámky k verzi se průběžně aktualizuje a při zjištění zásadních problémů, které vyžadují řešení, se řešení přidá. Před nasazením virtuálního zařízení StorSimple, pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 0,2 odpovídá verzi softwaru **10.0.10280.0**; Update 0.1 je verze **10.0.10279.0**. Následující části obsahují seznam změn pro jednotlivé aktualizace. 

> [!NOTE]
> Aktualizace jsou rušivý a restartuje vaše zařízení. Pokud vstupně-výstupní operace probíhá, zařízení je zpoplatněná výpadku.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Chyby v aktualizaci 0,2
Aktualizace 0,2 zahrnuje všechny změny z Update 0.1 kromě opravy popsané v následující tabulce:

| Funkce | Problém |
| --- | --- |
| Aktualizace |V poslední verzi nebyly zjištěny aktualizace automaticky na portálu Azure classic, jste museli používat místní webového uživatelského rozhraní pro instalaci aktualizací. Tento problém vyřešen v této verzi. Po instalaci aktualizace 0,2, můžete nainstalovat budoucí aktualizace pomocí portálu Azure classic. |

## <a name="whats-new-in-the-update-01"></a>Co je nového v aktualizaci 0,1
Update 0.1 obsahuje následující vylepšení a opravy chyb. 

* **Odolnost pro cloud výpadků**: Tato verze obsahuje několik oprav chyb kolem zotavení po havárii, zálohování, obnovení a vrstvení v případě přerušení připojení cloudu. 
* **Zvýšení výkonu obnovení**: Tato verze obsahuje opravy chyb, které výrazně zkrátit čas dokončení úloh obnovení.
* **Automatizované optimalizace recyklace místa**: Při odstranění dat na dynamicky zřizované svazky bloky nepoužívané úložiště, který je potřeba znovu použít. Tato verze je vylepšený proces recyklace místa z cloudu výsledná nevyužitého místa stává stále k dispozici rychlejší porovnání s předchozími verzemi.
* **Nové bitové kopie virtuálního disku**: nový virtuální pevný disk, VHDX a VMDK jsou nyní k dispozici prostřednictvím portálu Azure classic. Můžete si stáhnout těchto bitových kopií ke zřízení nových Update 0.1 zařízení.
* **Více stav úlohy na portálu**: V dřívější verzi softwaru, nebyl granulární stav úlohy vytváření sestav v portálu. Tento problém se vyřeší v této verzi.
* **Prostředí připojení k doméně**: opravy chyb týkající se připojení k doméně a přejmenování zařízení.

## <a name="issues-fixed-in-the-update-01"></a>Chyby v Update 0.1
Následující tabulka obsahuje souhrn chyby v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |VMDK |V některé verze VMware disk operačního systému zobrazila jako zhuštěné příčinou výstrah a přerušení normální provozní podmínky. To byla opravena v této verzi. |
| 2 |serveru se službou iSCSI |V poslední verzi uživatele vyžadoval určit bránu pro každé povolené síťové rozhraní virtuálního zařízení StorSimple. Toto chování je v této verzi změnilo, tak, že uživatel musí nakonfigurovat alespoň jednu bránu pro všechny povolené síťové rozhraní. |
| 3 |Balíček pro podporu |Ve starší verzi softwaru kolekce balíčků podporu selhalo při velikosti balíčku byly větší než 1 GB. Tento problém vyřešen v této verzi. |
| 4 |Přístup do cloudu |V poslední verzi Pokud pole virtuální zařízení StorSimple nemá připojení k síti a byl restartován, místní uživatelské rozhraní má problémy s připojením. Tento problém vyřešen v této verzi. |
| 5 |Monitorování grafy |Grafy využití kapacity cloudu v předchozí verzi, po selhání zařízení zobrazí nesprávné hodnoty na portálu Azure classic. Tento problém je vyřešený v aktuální verzi. |

## <a name="known-issues-in-the-update-01"></a>Známé problémy v aktualizaci 0,1
Následující tabulka obsahuje souhrn známé problémy pro pole virtuální zařízení StorSimple a zahrnuje problémy uvedené verze z předchozích verzí. **Verze problémy uvedené v této verzi jsou označeny hvězdičkou. Téměř všechny problémy v tomto seznamu mají přenesené z verze GA pole virtuální zařízení StorSimple.**

| Ne. | Funkce | Problém | Alternativní řešení a komentář |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Virtuální zařízení vytvořené ve verzi preview nelze aktualizovat na podporovanou verzi obecné dostupnosti. |Tato virtuální zařízení musí převzetí služeb při selhání pro verzi obecné dostupnosti pomocí pracovního postupu zotavení po havárii. |
| **2.** |Zřízené datový disk |Jakmile máte zřízen datový disk určité zadané velikosti a vytvořit odpovídající virtuální zařízení StorSimple, nesmí zvětšení nebo zmenšení datový disk. Pokusili, dojde ke ztrátě všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Když je zařízení připojené k doméně, použití zásad skupiny může nepříznivě ovlivnit činnost zařízení. |Zkontrolujte, zda je vaše virtuální pole ve vlastní organizační jednotku (OU) pro službu Active Directory a žádné objekty zásad skupiny (GPO) se použijí k němu. |
| **4.** |Místní webového uživatelského rozhraní |Pokud funkce Rozšířené zabezpečení jsou povolené v aplikaci Internet Explorer (IE ESC), některé z uživatelského rozhraní místní webové stránky, jako je například údržby nebo Poradce při potížích s nemusí fungovat správně. Tlačítka na těchto stránkách nemusí fungovat. |Vypněte funkce rozšířeného zabezpečení aplikace Internet Explorer. |
| **5.** |Místní webového uživatelského rozhraní |Ve virtuálním počítači technologie Hyper-V rozhraní sítě v webového uživatelského rozhraní se zobrazí jako 10 GB/s rozhraní. |Toto chování je odraz technologie Hyper-V. Technologie Hyper-V vždy zobrazuje 10 GB/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Rozsah bajtů uzamčení pro aplikace, které pracují s StorSimple vrstvené svazky se nepodporuje. Pokud je povolený zámek rozsah bajtů, StorSimple vrstvení nebude fungovat. |Doporučené míry zahrnují: <br></br>Vypněte rozsah bajtů zamykání logiky aplikace.<br></br>Zvolte dávat data pro tuto aplikaci v místně vázaných svazků a vrstvené svazky.<br></br>*Přímý přístup paměti*: Pokud místně pomocí připnutý svazky a je povolený zámek rozsah bajtů, uvědomte si, že místně vázaný svazek může být online i před dokončením obnovení. V takových případech Pokud obnovení probíhá, pak je nutné počkat na dokončení obnovení. |
| **7.** |Vrstvený sdílené složky |Práce s velkými soubory může mít za následek pomalé vrstvy. |Při práci s velkých souborů, doporučujeme vám, že na největší soubor je menší než velikost sdílené složky % 3. |
| **8.** |Použít kapacity pro sdílené složky |Může se zobrazit sdílet spotřeba neexistují žádná data ve sdílené složce. To je proto využité kapacitě pro sdílené složky obsahuje metadata. | |
| **9.** |Zotavení po havárii |Můžete provádět pouze obnovení po havárii souborového serveru ke stejné doméně jako u zdrojového zařízení. V této verzi nepodporuje zotavení po havárii na cílovém zařízení v jiné doméně. |To se provádí v novější verzi. |
| **10.** |Azure PowerShell |Virtuální zařízení StorSimple nelze spravovat pomocí prostředí Azure PowerShell v této verzi. |Všechny správu virtuálních zařízení by mělo být provedeno prostřednictvím portálu Azure classic a místní webového uživatelského rozhraní. |
| **11.** |Změna hesla |Konzole virtuální pole zařízení přijímá pouze vstup ve formátu klávesnice en US. | |
| **12.** |CHAP |Po vytvoření pověření CHAP nelze odebrat. Kromě toho pokud změníte přihlašovací údaje CHAP, musíte převést svazky do režimu offline a pak uvést je online změna projevila. |Tyto budou řešeny v novější verzi. |
| **13.** |serveru se službou iSCSI |'Použít úložiště, zobrazí pro svazek iSCSI může být jiný služby StorSimple Manager a iSCSI hostitele. |Zobrazení souborů má hostitel iSCSI.<br></br>Zařízení se zobrazí na bloky přidělené při při maximální velikosti svazku. |
| **14.** |Souborový server * |Pokud má soubor ve složce alternativní Data datového proudu (reklamy) s ním spojená, není reklamy zálohovat nebo obnovit prostřednictvím obnovení po havárii, klonování a obnovení na úrovni položek. | |

## <a name="next-step"></a>Další krok
[Nainstalujte aktualizace](storsimple-ova-install-update-01.md) na pole virtuální zařízení StorSimple.

