---
title: Zpráva k vydání verze s aktualizací StorSimple Virtual Array 0,4 | Microsoft Docs
description: Popisuje kritické otevřené problémy a řešení pro virtuální pole StorSimple s aktualizací 0,4.
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
ms.openlocfilehash: 392e930dcb69392b6f9425abba0efa516d5c82b8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967053"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>Zpráva k vydání verze pro StorSimple Virtual Array Update 0,4

## <a name="overview"></a>Přehled

Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple aktualizace virtuálních polí.

Poznámky k verzi se průběžně aktualizují a při zjištění kritických problémů vyžadujících alternativní řešení se přidají. Před nasazením StorSimple virtuálního pole pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 0,4 odpovídá **10.0.10289.0** verze softwaru.

> [!NOTE]
> Aktualizace jsou rušivé a restartují vaše zařízení. Pokud probíhá vstupně-výstupní operace, dojde k výpadku zařízení.


## <a name="whats-new-in-the-update-04"></a>Co je nového v aktualizaci 0,4
Aktualizace 0,4 je primárně sestavení s opravou chyby, které je několik vylepšení. V této verzi jsme vyřešili několik chyb, které vedly k selhání zálohování v předchozí verzi. Hlavní vylepšení a opravy chyb jsou následující:

- **Vylepšení výkonu zálohování** – Tato verze provedla několik klíčových vylepšení, která zlepšují výkon zálohování. V důsledku toho se zálohy, které zahrnují velký počet souborů, zobrazují výrazné snížení času na dokončení, a to u úplných a přírůstkových záloh.

- **Vylepšený výkon obnovení** – Tato verze obsahuje vylepšení, která významně zlepšují výkon při obnovení při použití velkého počtu souborů. Pokud používáte 2-4 milionů souborů, doporučujeme zřídit virtuální pole se 16 GB paměti RAM, abyste viděli vylepšení. Při použití méně než 2 000 000 souborů bude mít minimální požadavek na virtuální počítač velikost paměti RAM 8 GB.

- **Vylepšení podpory balíčku** : mezi vylepšení patří protokolování statistik pro disk, procesor, paměť, síť a Cloud do balíčku pro podporu. tím se zlepšuje proces diagnostiky nebo ladění problémů zařízení.

- **Omezte místně připojené svazky iSCSI na 200 GB** – pro místně připnuté svazky doporučujeme, abyste na virtuálním poli StorSimple omezili na 200 GB svazku iSCSI. Místní rezervace pro vrstvené svazky má nadále 10% velikosti zřízeného svazku, ale je omezené na 200 GB. 

- **Opravy chyb související se zálohováním** – v předchozích verzích softwaru byly problémy související se zálohami, které by způsobily selhání zálohování. Tyto chyby byly řešeny v této verzi.


## <a name="issues-fixed-in-the-update-04"></a>Problémy opravené v aktualizaci 0,4

Následující tabulka poskytuje souhrn chyb opravených v této verzi.

| Ne. | Příznak | Problém |
| --- | --- | --- |
| 1 |Výkon zálohování|V dřívějších verzích se zálohování zahrnující velký počet souborů může trvat dlouhou dobu (v řádu dnů). V této verzi se úplné i přírůstkové zálohování zobrazuje výrazné snížení času na dokončení. |
| 2 |Balíček pro podporu|Statistiky disku, procesoru, paměti, sítě a cloudu jsou teď přihlášené k protokolům podpory, které usnadňují řešení problémů s problémy v zařízení.|
| 3 |Backup |V dřívějších verzích by dlouho spuštěné zálohy mohly způsobit, že se v zařízení zpracovávejte prostor, který má za následek selhání zálohování. Tato chyba je řešena v této verzi tím, že do fronty nepovoluje více než 5 záloh najednou.|
| 4 |iSCSI | V dřívějších verzích bylo místní rezervace pro vrstvené nebo místně připnuté svazky 10% velikosti zřízeného svazku. V této verzi se místní rezervace pro všechny svazky iSCSI (místně připnuté nebo vrstvené) omezuje na 10% s maximálně 200 GB (pro vrstvené svazky větší než 2 TB) a uvolní se tak více místa na místním disku. Doporučujeme, aby místně připnuté svazky v této verzi byly omezeny na 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Známé problémy v aktualizaci 0,4

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
| **8.** |Využitá kapacita pro sdílené složky |Pokud sdílená složka neobsahuje žádná data, může se zobrazit spotřeba sdílení. Důvodem je to, že využitá kapacita pro sdílené složky zahrnuje metadata. | |
| **9.** |Zotavení po havárii |Zotavení po havárii souborového serveru můžete provést jenom se stejnou doménou, jakou má zdrojové zařízení. V této verzi není podporováno zotavení po havárii do cílového zařízení v jiné doméně. |To je implementováno v novější verzi. |
| **10pruhový.** |Azure PowerShell |Virtuální zařízení StorSimple se nedají spravovat prostřednictvím Azure PowerShell v této verzi. |Veškerá správa virtuálních zařízení by se měla provádět prostřednictvím portálu Azure Classic a místního webového uživatelského rozhraní. |
| **odst.** |Změna hesla |Konzola zařízení Virtual Array podporuje pouze vstup ve formátu klávesnice en-US. | |
| **12,5.** |CHAP |Po vytvoření nelze odstranit přihlašovací údaje protokolu CHAP. Pokud navíc upravíte přihlašovací údaje protokolu CHAP, musíte svazky převést do režimu offline a pak je převést do režimu online, aby se změna projevila. |Tento problém je řešen v pozdější verzi. |
| **13,5.** |Server iSCSI |Použité úložiště pro svazek iSCSI se může lišit ve službě StorSimple Manager a hostiteli iSCSI. |Hostitel iSCSI má zobrazení systému souborů.<br></br>Zařízení vidí bloky přidělené v případě, že byl svazek v maximální velikosti. |
| **čtrnáct.** |Souborový server |Pokud k souboru ve složce je přidružen alternativní datový proud (ADS), nejsou tyto reklamy zálohovány ani obnoveny prostřednictvím zotavení po havárii, klonování a obnovení na úrovni položek. | |
| **15.** |Souborový server |Symbolické odkazy nejsou podporovány. | |
| **16bitovém.** |Souborový server |Soubory chráněné systémem Windows systém souborů EFS (Encrypting File System) (EFS) při zkopírování nebo uložení na souborový server virtuálních polí StorSimple mají za následek nepodporovanou konfiguraci.  | |

## <a name="next-step"></a>Další krok
[Nainstalujte aktualizaci 0,4](storsimple-virtual-array-install-update-04.md) na virtuální pole StorSimple.

## <a name="references"></a>Odkazy
Hledáte starší poznámku k verzi? Přejít na: 

* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,3](storsimple-ova-update-03-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,1 a 0,2](storsimple-ova-update-01-release-notes.md)
* [Poznámky k verzi pro StorSimple Virtual Array General Availability](./storsimple-virtual-array-update-06-release-notes.md)