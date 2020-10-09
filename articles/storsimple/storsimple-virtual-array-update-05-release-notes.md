---
title: Zpráva k vydání verze s aktualizací StorSimple Virtual Array 0,5 | Microsoft Docs
description: Popisuje kritické otevřené problémy a řešení pro virtuální pole StorSimple s aktualizací 0,5.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "60870668"
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>Zpráva k vydání verze pro StorSimple Virtual Array Update 0,5

## <a name="overview"></a>Přehled

Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple aktualizace virtuálních polí.

Poznámky k verzi se průběžně aktualizují a při zjištění kritických problémů vyžadujících alternativní řešení se přidají. Před nasazením StorSimple virtuálního pole pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 0,5 odpovídá **10.0.10290.0**verze softwaru.

> [!NOTE]
> Aktualizace jsou rušivé a restartují vaše zařízení. Pokud probíhá vstupně-výstupní operace, dojde k výpadku zařízení. Podrobné pokyny, jak použít aktualizaci, najdete v článku [instalace aktualizace 0,5](storsimple-virtual-array-install-update-05.md).


## <a name="whats-new-in-the-update-05"></a>Co je nového v aktualizaci 0,5
Aktualizace 0,5 je primárně sestavení pro opravu chyb. Hlavní vylepšení a opravy chyb jsou následující:

- **Vylepšení odolnosti** proti chybám – Tato verze obsahuje opravy, které zlepšují odolnost proti zálohování. V dřívějších verzích byly zálohy opakovány pouze pro určité výjimky. Tato verze opakuje všechny výjimky zálohování a provede větší odolnost zálohování.

- **Aktualizace monitorování využití úložiště** – od 30. června 2017 se monitorování využití úložiště pro řady virtuálních zařízení StorSimple vyřadí. To platí pro grafy monitorování ve všech virtuálních polích, které používají aktualizaci 0,4 nebo nižší. Tato aktualizace obsahuje změny potřebné k tomu, abyste mohli pokračovat v používání monitorování využití úložiště v Azure Portal. Tuto důležitou aktualizaci nainstalujte do 30. června 2017, abyste mohli dál používat funkci monitorování.


## <a name="issues-fixed-in-the-update-05"></a>Problémy opravené v aktualizaci 0,5

Následující tabulka poskytuje souhrn chyb opravených v této verzi.

| Ne. | Příznak | Problém |
| --- | --- | --- |
| 1 |Odolnost proti zálohování| V dřívějších verzích byly zálohy opakovány pouze pro určité výjimky. Tato verze obsahuje opravu pro zajištění větší odolnosti před opakováním všech výjimek zálohování.|
| 2 |Sledování| Monitorování využití úložiště pro řady virtuálních zařízení StorSimple se od 30. června 2017 už nepoužívá. Tato akce má vliv na grafy monitorování ve službě StorSimple Device Manager běžící na virtuálních polích StorSimple (1200 modelu). Tato verze obsahuje aktualizace, které umožňují uživateli pokračovat ve využívání monitorování využívání úložiště u virtuálních polí po 30. června 2017.|
| 3 |Souborový server| V dřívějších verzích mohl uživatel omylem zkopírovat šifrované soubory do virtuálního pole. Tato verze obsahuje opravu, která neumožňuje kopírování šifrovaných souborů do virtuálního pole. Pokud zařízení obsahuje před aktualizací existující šifrované soubory, zálohy budou pokračovat, dokud se všechny šifrované soubory ze systému neodstraní. |


## <a name="known-issues-in-the-update-05"></a>Známé problémy v aktualizaci 0,5

Následující tabulka obsahuje souhrn známých problémů pro virtuální pole StorSimple a obsahuje vydané verze zaznamenané z předchozích verzí.

| Ne. | Příznak | Problém | Alternativní řešení/komentáře |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Virtuální zařízení vytvořená ve verzi Preview nejde aktualizovat na podporovanou obecnou verzi dostupnosti. |U těchto virtuálních zařízení je potřeba převzít služby při selhání pro vydání obecné dostupnosti pomocí pracovního postupu zotavení po havárii (DR). |
| **2.** |Zřízený datový disk |Jakmile zřídíte datový disk určité zadané velikosti a vytvoříte odpovídající virtuální zařízení StorSimple, nesmíte tento datový disk rozbalit ani zmenšit. Při pokusu o provedení dojde ke ztrátě všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Když je zařízení připojené k doméně, může použití zásad skupiny negativně ovlivnit operaci zařízení. |Ujistěte se, že vaše virtuální pole je ve své vlastní organizační jednotce pro službu Active Directory a že pro něj nejsou použité žádné objekty zásad skupiny (GPO). |
| **4.** |Místní webové uživatelské rozhraní |Pokud jsou v aplikaci Internet Explorer povoleny rozšířené funkce zabezpečení (IE ESC), některé místní stránky webového uživatelského rozhraní, jako je například řešení potíží nebo údržba, nemusí správně fungovat. Tlačítka na těchto stránkách také nemusí fungovat. |Vypnout rozšířené funkce zabezpečení v Internet Exploreru. |
| **čl.** |Místní webové uživatelské rozhraní |Ve virtuálním počítači s technologií Hyper-V se síťová rozhraní ve webovém uživatelském rozhraní zobrazují jako rozhraní o 10 GB/s. |Toto chování je reflexe technologie Hyper-V. Technologie Hyper-V vždy zobrazuje 10 GB/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Uzamykání rozsahu bajtů pro aplikace, které pracují se StorSimple vrstvenými svazky, se nepodporuje. Pokud je povolené zamykání rozsahu bajtů, nefunguje vrstvení StorSimple. |Doporučené míry zahrnují: <br></br>Vypněte uzamykání rozsahu bajtů v logice aplikace.<br></br>Vyberte, chcete-li umístit data pro tuto aplikaci na místně připnuté svazky na rozdíl od vrstveného svazku.<br></br>*Upozornění*: Pokud je povolené použití místně připojených svazků a uzamykání rozsahu bajtů, může být místně připojený svazek online, i když se obnovení nedokončí. V takových případech, pokud probíhá obnovení, je nutné počkat na dokončení obnovení. |
| **čl.** |Vrstvené sdílené složky |Práce s velkými soubory může mít za následek zpomalení vrstvy. |Při práci s velkými soubory doporučujeme, aby byl největší soubor menší než 3% velikosti sdílené složky. |
| **8.** |Využitá kapacita pro sdílené složky |Pokud sdílená složka neobsahuje žádná data, může se zobrazit spotřeba sdílení. Tato spotřeba je způsobená tím, že využitá kapacita pro sdílené složky zahrnuje metadata. | |
| **9.** |Zotavení po havárii |Zotavení po havárii souborového serveru můžete provést jenom se stejnou doménou, jakou má zdrojové zařízení. V této verzi není podporováno zotavení po havárii do cílového zařízení v jiné doméně. |To je implementováno v novější verzi. Další informace najdete v [poznámkách k převzetí služeb při selhání a zotavení po havárii pro virtuální pole StorSimple](storsimple-virtual-array-failover-dr.md) . |
| **10pruhový.** |Azure PowerShell |Virtuální zařízení StorSimple se nedají spravovat prostřednictvím Azure PowerShell v této verzi. |Veškerá správa virtuálních zařízení by se měla provádět prostřednictvím Azure Portal a místního webového uživatelského rozhraní. |
| **odst.** |Změna hesla |Konzola zařízení Virtual Array podporuje pouze vstup ve formátu klávesnice en-US. | |
| **12,5.** |CHAP |Po vytvoření nelze odstranit přihlašovací údaje protokolu CHAP. Pokud navíc upravíte přihlašovací údaje protokolu CHAP, musíte svazky převést do režimu offline a pak je převést do režimu online, aby se změna projevila. |Tento problém je řešen v pozdější verzi. |
| **13,5.** |Server iSCSI |Zobrazené úložiště pro svazek iSCSI se může lišit v StorSimple Device Manager službě a hostiteli iSCSI. |Hostitel iSCSI má zobrazení systému souborů.<br></br>Zařízení vidí bloky přidělené v případě, že byl svazek v maximální velikosti. |
| **čtrnáct.** |Souborový server |Pokud k souboru ve složce je přidružen alternativní datový proud (ADS), nejsou tyto reklamy zálohovány ani obnoveny prostřednictvím zotavení po havárii, klonování a obnovení na úrovni položek. | |
| **15.** |Souborový server |Symbolické odkazy nejsou podporovány. | |
| **16bitovém.** |Souborový server |Soubory chráněné systémem Windows systém souborů EFS (Encrypting File System) (EFS) při zkopírování nebo uložení na souborový server virtuálních polí StorSimple mají za následek nepodporovanou konfiguraci.  | |

## <a name="next-step"></a>Další krok
[Nainstalujte aktualizaci 0,5](storsimple-virtual-array-install-update-05.md) na virtuální pole StorSimple.

## <a name="references"></a>Reference
Hledáte starší poznámku k verzi? Přejít na:

* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,4](storsimple-virtual-array-update-04-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,3](storsimple-ova-update-03-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,1 a 0,2](storsimple-ova-update-01-release-notes.md)
* [Poznámky k verzi pro StorSimple Virtual Array General Availability](storsimple-ova-pp-release-notes.md)

