---
title: Poznámky k verzi zařízení StorSimple virtuální pole Update 1.0 | Microsoft Docs
description: Popisuje důležité otevřené problémy a řešení pro pole virtuální zařízení StorSimple systémem Update 1.0.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 777718c4893f1edab3613be5dc941e2716d4c972
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2017
ms.locfileid: "24012103"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>Poznámky k verzi zařízení StorSimple virtuální pole Update 1.0

## <a name="overview"></a>Přehled

Následující poznámky k verzi identifikovat kritická otevřené problémy a vyřešit problémy pro Microsoft Azure StorSimple virtuální pole aktualizace.

Poznámky k verzi se průběžně aktualizuje a při zjištění zásadních problémů, které vyžadují řešení, se řešení přidá. Před nasazením pole virtuální zařízení StorSimple, pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 1.0 odpovídá verzi softwaru **10.0.10296.0**.

> [!IMPORTANT]
> - Aktualizace jsou rušivý a zařízení restartovat. Pokud vstupně-výstupní operace probíhá, zařízení způsobuje výpadek. Podrobné pokyny o tom, jak použít aktualizaci, přejděte na [nainstalovat aktualizace 1.0](storsimple-virtual-array-install-update-1.md).
>
> - Aktualizace 1 je prostřednictvím portálu Azure k dispozici pouze v případě zařízení se systémem aktualizace 0,6.

## <a name="whats-new-in-update-10"></a>Co je nového v Update 1.0

**Aktualizace 1.0 obsahuje změny související s ověřováním služby StorSimple Manager zařízení a musí být nasazené na vaše nejdřívější.** Tato aktualizace obsahuje následující vylepšení a opravy chyb:

 - **Použití nástroje Azure Active Directory (AAD) k ověření pomocí služby StorSimple Manager zařízení** – od aktualizace 1.0 a vyšší, Azure Active Directory se používá k ověření u služby StorSimple Manager zařízení. Původní mechanismus ověřování přestanou prosinec 2017. Všichni uživatelé musí obsahovat nové adresy URL ověřování v jejich pravidla brány firewall. Další informace najdete v tématu ověřování adresy URL uvedené v [požadavcích sítě pro virtuální zařízení StorSimple pole](storsimple-ova-system-requirements.md).
 
    Pokud adresa URL pro ověření není zahrnut do pravidel brány firewall, uživatelé uvidí kritickou výstrahu, že jejich zařízení StorSimple nelze ověřit ve službě. Pokud uživatelé zobrazit tuto výstrahu, musí obsahovat nové adresy URL ověřování. Další informace, přejděte na [StorSimple sítě výstrahy](storsimple-virtual-array-manage-alerts.md).

 - **Zlepšení výkonu** -byly k vylepšení rychlostmi čtení cloudu, vrstvy in a vrstvy li provést několik oprav chyb. V důsledku toho je vylepšený výkon zálohování a obnovení pro zařízení iSCSI a souboru serveru.

 - **Uvolňování paměti kolekce zlepšování** – tato verze obsahuje opravy chyb, které zlepšují výkon při účet zařízení a úložiště jsou ve dvou oblastech vzdáleným cyklu uvolňování paměti.

 - **Protokolování zlepšování** – tato verze obsahuje vylepšení protokolování související s uvolňování paměti a vstupně-výstupní cesta.


## <a name="issues-fixed-in-update-10"></a>Chyby v Update 1.0

Následující tabulka obsahuje souhrn chyby v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Ověřování pomocí AAD| Tato verze obsahuje změny, které umožňuje AAD k ověření pomocí Správce zařízení StorSimple.|
| 2 |Uvolňování paměti| Tento problém byla nahlášena u zákazníka, kde jsou účty zařízení a úložiště v různých oblastech a zákazník hlášené chyby přerušované sítě tím, které mají vliv fakturace. V této verzi tento problém byl opraven. |
| 3 |Výkon| Tato verze obsahuje změny, které mít za následek čtení obnovení nebo cloudem nebo vrstvy / vrstvy se výkonu v přírůstcích.|
| 4 |Aktualizace| Došlo problému s aktualizací starší verze, které způsobilo selhání zálohování na webu zákazníka. Tento problém vyřešen v této verzi.|

## <a name="known-issues-in-update-10"></a>Známé problémy v Update 1.0

Následující tabulka obsahuje souhrn známé problémy pro pole virtuální zařízení StorSimple a zahrnuje problémy uvedené verze z předchozích verzí.

| Ne. | Funkce | Problém | Alternativní řešení a komentář |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Pole virtuální vytvořen ve verzi preview nelze aktualizovat na podporovanou verzi obecné dostupnosti. |Tyto virtuální pole musí převzetí služeb při selhání pro verzi obecné dostupnosti pomocí pracovního postupu zotavení po havárii. |
| **2.** |Zřízené datový disk |Jednou, když máte zřízenou datový disk určité zadané velikosti a vytvořit odpovídající pole virtuální zařízení StorSimple, nesmí zvětšení nebo zmenšení datový disk. Probíhá pokus o provést výsledky ke ztrátě všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Když je zařízení připojené k doméně, použití zásad skupiny může nepříznivě ovlivnit činnost zařízení. |Zkontrolujte, zda je vaše virtuální pole ve vlastní organizační jednotku (OU) pro službu Active Directory a žádné objekty zásad skupiny (GPO) se použijí k němu. |
| **4.** |Místní webového uživatelského rozhraní |Pokud funkce Rozšířené zabezpečení jsou povolené v aplikaci Internet Explorer (IE ESC), některé z uživatelského rozhraní místní webové stránky, jako je například údržby nebo Poradce při potížích s nemusí fungovat správně. Tlačítka na těchto stránkách nemusí fungovat. |Vypněte funkce rozšířeného zabezpečení aplikace Internet Explorer. |
| **5.** |Místní webového uživatelského rozhraní |Ve virtuálním počítači technologie Hyper-V rozhraní sítě v webového uživatelského rozhraní se zobrazí jako 10 GB/s rozhraní. |Toto chování je odraz technologie Hyper-V. Technologie Hyper-V vždy zobrazuje 10 GB/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Rozsah bajtů uzamčení pro aplikace, které pracují s StorSimple vrstvené svazky se nepodporuje. Pokud je povolený zámek rozsah bajtů, StorSimple vrstvení není funkční. |Doporučené míry zahrnují: <br></br>Vypněte rozsah bajtů zamykání logiky aplikace.<br></br>Zvolte dávat data pro tuto aplikaci v místně vázaných svazků a vrstvené svazky.<br></br>*Přímý přístup paměti*: při použití místně připnutý svazky a je povolený zámek rozsah bajtů, místně vázaný svazek může být online i před dokončením obnovení. V takových případech Pokud obnovení probíhá, pak je nutné počkat na dokončení obnovení. |
| **7.** |Vrstvený sdílené složky |Práce s velkými soubory může mít za následek pomalé vrstvy. |Při práci s velkých souborů, doporučujeme vám, že na největší soubor je menší než velikost sdílené složky % 3. |
| **8.** |Použít kapacity pro sdílené složky |Může se zobrazit sdílení spotřeby, pokud nejsou žádná data ve sdílené složce. Tato spotřeba totiž využité kapacitě pro sdílené složky obsahuje metadata. | |
| **9.** |Zotavení po havárii |Můžete provádět pouze obnovení po havárii souborového serveru ke stejné doméně jako u zdrojového zařízení. V této verzi nepodporuje zotavení po havárii na cílovém zařízení v jiné doméně. |Tato možnost je implementovaná v novější verzi. Další informace, přejděte na [převzetí služeb při selhání a zotavení po havárii pro pole virtuální zařízení StorSimple](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Pole virtuální zařízení StorSimple nelze spravovat pomocí prostředí Azure PowerShell v této verzi. |Všechny správu virtuálních zařízení by mělo být provedeno prostřednictvím portálu Azure a místní webového uživatelského rozhraní. |
| **11.** |Změna hesla |Konzole virtuální pole zařízení přijímá pouze vstup v en-us klávesové formátu. | |
| **12.** |CHAP |Po vytvoření pověření CHAP nelze odebrat. Kromě toho pokud změníte přihlašovací údaje CHAP, budete muset svazky převést do režimu offline a pak přiřaďte je online změna se projeví. |Tomuto problému dochází v novější verzi. |
| **13.** |serveru se službou iSCSI |'Použít úložiště, zobrazí pro svazek iSCSI může být jiný služby StorSimple Manager zařízení a iSCSI hostitele. |Zobrazení souborů má hostitel iSCSI.<br></br>Zařízení se zobrazí na bloky přidělené při při maximální velikosti svazku. |
| **14.** |Souborový server |Pokud má soubor ve složce alternativní Data datového proudu (reklamy) s ním spojená, není reklamy zálohovat nebo obnovit prostřednictvím obnovení po havárii, klonování a obnovení na úrovni položek. | |
| **15.** |Souborový server |Symbolické odkazy nejsou podporovány. | |
| **16.** |Souborový server |Soubory chráněné pomocí Windows systém souboru (Encrypting File System) po zkopírování přes nebo uložený na výsledek pole virtuální zařízení StorSimple souborového serveru v nepodporované konfigurace.  | |
| **17.** |Aktualizace |Pokud se zobrazí chyba kód: 2359302 (šestnáctkově 0x240006) při pokusu o instalaci opravy hotfix prostřednictvím místního uživatelského rozhraní, pak z toho vyplývá, že oprava hotfix je již nainstalována na zařízení.   | |
| **18.** |Aktualizace |Pokud používáte místní webového uživatelského rozhraní pro instalaci aktualizace 1 na vaše virtuální pole, ujistěte se, že používáte verzi Update 0,6. Pokud používáte verzi nižší než aktualizace 0,6, musíte nejprve nainstalovat aktualizace 0,6 a pak aplikovat Update 1. Pokud instalujete Update 1.0 přímo z verze před aktualizací 0,6, pak bude neproběhly některé aktualizace a monitorování grafy nebude fungovat.   | |


## <a name="next-steps"></a>Další kroky
[Nainstalujte aktualizaci 1.0](storsimple-virtual-array-install-update-1.md) na pole virtuální zařízení StorSimple.

## <a name="references"></a>Odkazy
Hledáte starší poznámku k verzi? Přejít na:
*  [K vydání 0,6 verze Update pole virtuální zařízení StorSimple](storsimple-virtual-array-update-06-release-notes.md)
* [K vydání 0,5 verze Update pole virtuální zařízení StorSimple](storsimple-virtual-array-update-05-release-notes.md)
* [K vydání 0,4 verze Update pole virtuální zařízení StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [K vydání 0,3 verze Update pole virtuální zařízení StorSimple](storsimple-ova-update-03-release-notes.md)
* [Poznámky k verzi zařízení StorSimple virtuální pole aktualizace 0,1 a 0,2](storsimple-ova-update-01-release-notes.md)
* [Poznámky k verzi zařízení StorSimple virtuální pole Obecné dostupnosti](storsimple-ova-pp-release-notes.md)
