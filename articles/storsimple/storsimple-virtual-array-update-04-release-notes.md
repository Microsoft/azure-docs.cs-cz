---
title: Poznámky k verzi 0,4 aktualizace pole virtuální zařízení StorSimple | Microsoft Docs
description: Popisuje důležité otevřené problémy a řešení pro pole virtuální zařízení StorSimple spuštění Update 0.4.
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
ms.openlocfilehash: cc2b025b7f3e28954c7f95409ffab03e5cbcf13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927758"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>Poznámky k verzi 0,4 aktualizace pole virtuální zařízení StorSimple

## <a name="overview"></a>Přehled

Následující poznámky k verzi identifikovat kritická otevřené problémy a vyřešit problémy pro Microsoft Azure StorSimple virtuální pole aktualizace.

Poznámky k verzi se průběžně aktualizuje a při zjištění zásadních problémů, které vyžadují řešení, se řešení přidá. Před nasazením pole virtuální zařízení StorSimple, pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 0.4 odpovídá verzi softwaru **10.0.10289.0**.

> [!NOTE]
> Aktualizace jsou rušivý a zařízení restartovat. Pokud vstupně-výstupní operace probíhá, zařízení způsobuje výpadek.


## <a name="whats-new-in-the-update-04"></a>Co je nového v aktualizaci 0.4
Aktualizace 0.4 je primárně oprava chyby sestavení kombinaci s několik vylepšení. V této verzi byla vyřešili několik chyb, což vede k selhání zálohování v předchozí verzi. Hlavní vylepšení a opravy chyb jsou následující:

- **Vylepšení výkonu zálohování** – tato verze udělal několik klíčových vylepšení zlepšit výkon zálohování. V důsledku toho zálohování, které zahrnují velké množství souborů najdete v části k výraznému snížení času k dokončení úplné a přírůstkové zálohování.

- **Vylepšený výkon obnovení** – tato verze obsahuje vylepšení, která výrazně zlepšit výkon obnovení při použití velkého počtu souborů. Pokud používáte 2 – 4 miliony souborů, doporučujeme zřízení virtuální pole s 16 GB paměti RAM zobrazíte vylepšení. Při použití méně než 2 miliony souborů, minimální požadavek pro virtuální počítač je nadále 8 GB paměti RAM.

- **Vylepšení podpory balíček** – vylepšení zahrnují protokolování v statistiku disku, procesoru, paměti, sítě a cloudu do balíčku podpora a vylepšení proces diagnostikování nebo ladění problémů zařízení.

- **Limit místně připnutý iSCSI svazky na 200 GB** -místně vázaných svazků, doporučujeme omezit na svazek iSCSI 200 GB na virtuální zařízení StorSimple pole. Místní rezervace pro vrstvené svazky nadále 10 % velikost zřízeného svazku, ale je limitován 200 GB. 

- **Opravy chyb spojených s** – v předchozích verzích softwaru, byly nějaké problémy související s zálohování, které by způsobily selhání zálohování. V této verzi se odstranily tyto chyby.


## <a name="issues-fixed-in-the-update-04"></a>Chyby v aktualizaci 0.4

Následující tabulka obsahuje souhrn chyby v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Výkon zálohování|V dřívějších verzích by zálohy zahrnující velké množství souborů trvat dlouhou dobu pro dokončení (v pořadí podle dní). V této verzi najdete úplné a přírůstkové zálohování k výraznému snížení čas na dokončení. |
| 2 |Balíček pro podporu|Disku, procesoru, paměti, sítě a cloudu statistiky jsou nyní přihlášeni do protokolů podporu vytváření balíčků podpora velmi efektivní při odstraňování problémů jakékoli zařízení.|
| 3 |Zálohování |V dřívějších verzích dlouho běžící zálohování může mít za následek zpracujte místo na zařízení, což vede k selhání zálohování. Tato chyba opravená v této verzi tím, že se víc než 5 zálohy do fronty najednou.|
| 4 |iSCSI | V dřívějších verzích se místní rezervace pro vrstvené nebo místně vázaných svazků 10 % velikost zřízeného svazku. V této verzi místní rezervace pro všechny svazky iSCSI (místně připnuté nebo víceúrovňová) je omezený na 10 % s maximálně až 200 GB (pro vrstvené svazky větší než 2 TB) a tím uvolňování další místo na místním disku. Doporučujeme vám, že místně vázaných svazků v této verzi omezena na 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Známé problémy v aktualizaci 0.4

Následující tabulka obsahuje souhrn známé problémy pro pole virtuální zařízení StorSimple a zahrnuje problémy uvedené verze z předchozích verzí. 

| Ne. | Funkce | Problém | Alternativní řešení a komentář |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Virtuální zařízení vytvořené ve verzi preview nelze aktualizovat na podporovanou verzi obecné dostupnosti. |Tato virtuální zařízení musí převzetí služeb při selhání pro verzi obecné dostupnosti pomocí pracovního postupu zotavení po havárii. |
| **2.** |Zřízené datový disk |Jakmile máte zřízen datový disk určité zadané velikosti a vytvořit odpovídající virtuální zařízení StorSimple, nesmí zvětšení nebo zmenšení datový disk. Probíhá pokus o provést výsledky ke ztrátě všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Když je zařízení připojené k doméně, použití zásad skupiny může nepříznivě ovlivnit činnost zařízení. |Zkontrolujte, zda je vaše virtuální pole ve vlastní organizační jednotku (OU) pro službu Active Directory a žádné objekty zásad skupiny (GPO) se použijí k němu. |
| **4.** |Místní webového uživatelského rozhraní |Pokud funkce Rozšířené zabezpečení jsou povolené v aplikaci Internet Explorer (IE ESC), některé z uživatelského rozhraní místní webové stránky, jako je například údržby nebo Poradce při potížích s nemusí fungovat správně. Tlačítka na těchto stránkách nemusí fungovat. |Vypněte funkce rozšířeného zabezpečení aplikace Internet Explorer. |
| **5.** |Místní webového uživatelského rozhraní |Ve virtuálním počítači technologie Hyper-V rozhraní sítě v webového uživatelského rozhraní se zobrazí jako 10 GB/s rozhraní. |Toto chování je odraz technologie Hyper-V. Technologie Hyper-V vždy zobrazuje 10 GB/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Rozsah bajtů uzamčení pro aplikace, které pracují s StorSimple vrstvené svazky se nepodporuje. Pokud je povolený zámek rozsah bajtů, StorSimple vrstvení není funkční. |Doporučené míry zahrnují: <br></br>Vypněte rozsah bajtů zamykání logiky aplikace.<br></br>Zvolte dávat data pro tuto aplikaci v místně vázaných svazků a vrstvené svazky.<br></br>*Přímý přístup paměti*: při použití místně připnutý svazky a je povolený zámek rozsah bajtů, místně vázaný svazek může být online i před dokončením obnovení. V takových případech Pokud obnovení probíhá, pak je nutné počkat na dokončení obnovení. |
| **7.** |Vrstvený sdílené složky |Práce s velkými soubory může mít za následek pomalé vrstvy. |Při práci s velkých souborů, doporučujeme vám, že na největší soubor je menší než velikost sdílené složky % 3. |
| **8.** |Použít kapacity pro sdílené složky |Může se zobrazit sdílení spotřeby, pokud nejsou žádná data ve sdílené složce. To je proto využité kapacitě pro sdílené složky obsahuje metadata. | |
| **9.** |Zotavení po havárii |Můžete provádět pouze obnovení po havárii souborového serveru ke stejné doméně jako u zdrojového zařízení. V této verzi nepodporuje zotavení po havárii na cílovém zařízení v jiné doméně. |Tato možnost je implementovaná v novější verzi. |
| **10.** |Azure PowerShell |Virtuální zařízení StorSimple nelze spravovat pomocí prostředí Azure PowerShell v této verzi. |Všechny správu virtuálních zařízení by mělo být provedeno prostřednictvím portálu Azure classic a místní webového uživatelského rozhraní. |
| **11.** |Změna hesla |Konzole virtuální pole zařízení přijímá pouze vstup ve formátu klávesnice en US. | |
| **12.** |CHAP |Po vytvoření pověření CHAP nelze odebrat. Kromě toho pokud změníte přihlašovací údaje CHAP, budete muset svazky převést do režimu offline a pak přiřaďte je online změna se projeví. |Tomuto problému dochází v novější verzi. |
| **13.** |serveru se službou iSCSI |'Použít úložiště, zobrazí pro svazek iSCSI může být jiný služby StorSimple Manager a iSCSI hostitele. |Zobrazení souborů má hostitel iSCSI.<br></br>Zařízení se zobrazí na bloky přidělené při při maximální velikosti svazku. |
| **14.** |Souborový server |Pokud má soubor ve složce alternativní Data datového proudu (reklamy) s ním spojená, není reklamy zálohovat nebo obnovit prostřednictvím obnovení po havárii, klonování a obnovení na úrovni položek. | |
| **15.** |Souborový server |Symbolické odkazy nejsou podporovány. | |
| **16.** |Souborový server |Soubory chráněné pomocí Windows systém souboru (Encrypting File System) po zkopírování přes nebo uložený na výsledek pole virtuální zařízení StorSimple souborového serveru v nepodporované konfigurace.  | |

## <a name="next-step"></a>Další krok
[Nainstalujte aktualizaci 0.4](storsimple-virtual-array-install-update-04.md) na pole virtuální zařízení StorSimple.

## <a name="references"></a>Odkazy
Hledáte starší poznámku k verzi? Přejít na: 

* [K vydání 0,3 verze Update pole virtuální zařízení StorSimple](storsimple-ova-update-03-release-notes.md)
* [Poznámky k verzi zařízení StorSimple virtuální pole aktualizace 0,1 a 0,2](storsimple-ova-update-01-release-notes.md)
* [Poznámky k verzi zařízení StorSimple virtuální pole Obecné dostupnosti](storsimple-ova-pp-release-notes.md)

