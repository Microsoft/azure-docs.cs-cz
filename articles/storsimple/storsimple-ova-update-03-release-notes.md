---
title: Zpráva k vydání verze pro StorSimple Virtual Array Update 0,3
description: Popisuje kritické otevřené problémy a řešení pro virtuální pole StorSimple s aktualizací 0,3.
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
ms.openlocfilehash: 8301b45fe778bd3df7fc665db2662ba81d3e644f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966101"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>Zpráva k vydání verze pro StorSimple Virtual Array Update 0,3
## <a name="overview"></a>Přehled
Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple aktualizace virtuálních polí.

Poznámky k verzi se průběžně aktualizují a při zjištění kritických problémů vyžadujících alternativní řešení se přidají. Před nasazením StorSimple virtuálního pole pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 0,3 odpovídá **10.0.10288.0** verze softwaru.

> [!NOTE]
> Aktualizace jsou rušivé a restartují vaše zařízení. Pokud probíhá vstupně-výstupní operace, dojde k výpadku zařízení.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Co je nového v aktualizaci 0,3
Aktualizace 0,3 je primárně sestavení pro opravu chyb. V této verzi jsme vyřešili několik chyb, které vedly k selhání zálohování v předchozí verzi.

## <a name="issues-fixed-in-the-update-03"></a>Problémy opravené v aktualizaci 0,3
Následující tabulka poskytuje souhrn chyb opravených v této verzi.

| No. | Funkce | Problém |
| --- | --- | --- |
| 1 |Zálohování |Došlo k potížím v dřívější verzi, kde se zálohování nepovedlo dokončit pro sdílenou složku. Pokud k tomuto problému došlo, úloha zálohování se nezdařila a v StorSimple Manager službě se vyvolala kritická výstraha, která uživatele upozorní. Tento problém neovlivnil data sdílených složek ani přístup k datům. V této verzi se identifikovala a opravila hlavní příčina. <br></br> Oprava se nepoužije zpět na sdílené složky, u kterých se tento problém již zobrazuje. Zákazníci, kteří mají tento problém, by měli nejdřív použít aktualizaci 0,3 a pak kontaktovat podpora Microsoftu k provedení úplné zálohy systému za účelem vyřešení problému. Místo kontaktování podpora Microsoftu můžou zákazníci také obnovit novou sdílenou složku ze zálohy v pořádku pro příslušné sdílené složky. |
| 2 |iSCSI |Ve starší verzi se objevil problém, kdy svazky při kopírování dat do svazku ve virtuálním poli StorSimple zmizí. Tento problém byl opravený v této verzi. <br></br> Opravy se projeví pouze u nově vytvořených svazků. Opravy se nepoužijí zpětně na svazky, které tento problém již vidí. Zákazníkům se doporučuje přenést svazky do režimu online prostřednictvím portálu Azure Classic, zálohovat tyto svazky a pak tyto svazky obnovit na nové svazky. |

## <a name="known-issues-in-the-update-03"></a>Známé problémy v aktualizaci 0,3
Následující tabulka obsahuje souhrn známých problémů pro virtuální pole StorSimple a obsahuje vydané verze zaznamenané z předchozích verzí. 

| No. | Funkce | Problém | Alternativní řešení/komentáře |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Virtuální zařízení vytvořená ve verzi Preview nejde aktualizovat na podporovanou obecnou verzi dostupnosti. |U těchto virtuálních zařízení je potřeba převzít služby při selhání pro vydání obecné dostupnosti pomocí pracovního postupu zotavení po havárii (DR). |
| **2.** |Zřízený datový disk |Jakmile zřídíte datový disk určité zadané velikosti a vytvoříte odpovídající virtuální zařízení StorSimple, nesmíte tento datový disk rozbalit ani zmenšit. Při pokusu o provedení dojde ke ztrátě všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Když je zařízení připojené k doméně, může použití zásad skupiny negativně ovlivnit operaci zařízení. |Ujistěte se, že vaše virtuální pole je ve své vlastní organizační jednotce pro službu Active Directory a že pro něj nejsou použité žádné objekty zásad skupiny (GPO). |
| **4.** |Místní webové uživatelské rozhraní |Pokud jsou v aplikaci Internet Explorer povoleny rozšířené funkce zabezpečení (IE ESC), některé místní stránky webového uživatelského rozhraní, jako je například řešení potíží nebo údržba, nemusí správně fungovat. Tlačítka na těchto stránkách také nemusí fungovat. |Vypnout rozšířené funkce zabezpečení v Internet Exploreru. |
| **čl.** |Místní webové uživatelské rozhraní |Ve virtuálním počítači s technologií Hyper-V se síťová rozhraní ve webovém uživatelském rozhraní zobrazují jako rozhraní o 10 GB/s. |Toto chování je reflexe technologie Hyper-V. Technologie Hyper-V vždy zobrazuje 10 GB/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Uzamykání rozsahu bajtů pro aplikace, které pracují se StorSimple vrstvenými svazky, se nepodporuje. Pokud je povolené zamykání rozsahu bajtů, nefunguje vrstvení StorSimple. |Doporučené míry zahrnují: <br></br>Vypněte uzamykání rozsahu bajtů v logice aplikace.<br></br>Vyberte, chcete-li umístit data pro tuto aplikaci na místně připnuté svazky na rozdíl od vrstveného svazku.<br></br>*Upozornění*: Pokud je povolené použití místně připojených svazků a uzamykání rozsahu bajtů, může být místně připojený svazek online, i když se obnovení nedokončí. V takových případech, pokud probíhá obnovení, je nutné počkat na dokončení obnovení. |
| **čl.** |Vrstvené sdílené složky |Práce s velkými soubory může mít za následek zpomalení vrstvy. |Při práci s velkými soubory doporučujeme, aby byl největší soubor menší než 3% velikosti sdílené složky. |
| **8.** |Využitá kapacita pro sdílené složky |Pokud sdílená složka neobsahuje žádná data, může se zobrazit spotřeba sdílení. Důvodem je to, že využitá kapacita pro sdílené složky zahrnuje metadata. | |
| **9.** |Zotavení po havárii |Zotavení po havárii souborového serveru můžete provést jenom se stejnou doménou, jakou má zdrojové zařízení. V této verzi není podporováno zotavení po havárii do cílového zařízení v jiné doméně. |To je implementováno v novější verzi. |
| **10pruhový.** |Azure PowerShell |Virtuální zařízení StorSimple se nedají spravovat prostřednictvím Azure PowerShell v této verzi. |Veškerá správa virtuálních zařízení by se měla provádět prostřednictvím portálu Azure Classic a místního webového uživatelského rozhraní. |
| **odst.** |Změna hesla |Konzola zařízení Virtual Array podporuje pouze vstup ve formátu klávesnice en-US. | |
| **12,5.** |CHAP |Po vytvoření nelze odstranit přihlašovací údaje protokolu CHAP. Pokud navíc upravíte přihlašovací údaje protokolu CHAP, musíte svazky převést do režimu offline a pak je převést do režimu online, aby se změna projevila. |Tento problém je řešen v pozdější verzi. |
| **13,5.** |Server iSCSI |Použité úložiště pro svazek iSCSI se může lišit ve službě StorSimple Manager a hostiteli iSCSI. |Hostitel iSCSI má zobrazení systému souborů.<br></br>Zařízení vidí bloky přidělené v případě, že byl svazek v maximální velikosti. |
| **čtrnáct.** |Souborový server |Pokud k souboru ve složce je přidružen alternativní datový proud (ADS), nejsou tyto reklamy zálohovány ani obnoveny prostřednictvím zotavení po havárii, klonování a obnovení na úrovni položek. | |

## <a name="next-step"></a>Další krok
[Nainstalujte aktualizaci 0,3](./storsimple-virtual-array-install-update-06.md) na virtuální pole StorSimple.

## <a name="references"></a>Reference
Hledáte starší poznámku k verzi? Přejít na: 

* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,1 a 0,2](storsimple-ova-update-01-release-notes.md)
* [Poznámky k verzi pro StorSimple Virtual Array General Availability](./storsimple-virtual-array-update-06-release-notes.md)