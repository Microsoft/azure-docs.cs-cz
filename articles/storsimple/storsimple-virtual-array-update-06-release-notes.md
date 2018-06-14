---
title: Poznámky k verzi 0,6 aktualizace pole virtuální zařízení StorSimple | Microsoft Docs
description: Popisuje důležité otevřené problémy a řešení pro pole virtuální zařízení StorSimple spuštění Update 0,6.
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
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: af202cf652300ee7897eb2dede33f38058fc2837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876436"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>Poznámky k verzi 0,6 aktualizace pole virtuální zařízení StorSimple

## <a name="overview"></a>Přehled

Následující poznámky k verzi identifikovat kritická otevřené problémy a vyřešit problémy pro Microsoft Azure StorSimple virtuální pole aktualizace.

Poznámky k verzi se průběžně aktualizuje a při zjištění zásadních problémů, které vyžadují řešení, se řešení přidá. Před nasazením pole virtuální zařízení StorSimple, pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 0,6 odpovídá verzi softwaru **10.0.10293.0**.

> [!IMPORTANT]
> - Aktualizace jsou rušivý a zařízení restartovat. Pokud vstupně-výstupní operace probíhá, zařízení způsobuje výpadek. Podrobné pokyny o tom, jak použít aktualizaci, přejděte na [nainstalujte aktualizaci 0,6](storsimple-virtual-array-install-update-06.md).
>
> - Důrazně doporučujeme nainstalovat aktualizaci 0,6 okamžitě, protože obsahuje opravy zabezpečení.


## <a name="whats-new-in-the-update-06"></a>Co je nového v aktualizaci 0,6
Aktualizace 0,6 je důležité aktualizace a musí být nasazené, okamžitě. Tato aktualizace obsahuje následující opravy: 

- **Opravy zabezpečení systému Windows** – tato verze obsahuje **opravy zabezpečení systému Windows kritické**. Projděte si další informace o problémy zabezpečení a přidružené opravy následující aktualizace zabezpečení:
    - [Prosinec 2016 pouze kvality zabezpečení aktualizace pro Windows 8.1 a Windows Server 2012 R2](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [2017 března pouze kvality zabezpečení aktualizace pro Windows 8.1 a Windows Server 2012 R2](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [9 může 2017 – KB4019213 (jen zabezpečení aktualizace)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Obnovení opravu** -ve starších verzích byla chybu, která by zabránila v dokončení obnovení. Tato chyba byla opravena v této verzi.


## <a name="issues-fixed-in-the-update-06"></a>Chyby v aktualizaci 0,6

Následující tabulka obsahuje souhrn chyby v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Zabezpečení| Tato verze obsahuje důležité aktualizace zabezpečení systému Windows. Doporučujeme nainstalovat tuto aktualizaci okamžitě.|
| 2 |Obnovení| Během obnovení byl spor, který by brání v dokončení úlohy obnovení. Oprava chyby řeší tento spor.|


## <a name="known-issues-in-the-update-06"></a>Známé problémy v aktualizaci 0,6

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
| **8.** |Použít kapacity pro sdílené složky |Může se zobrazit sdílení spotřeby, pokud nejsou žádná data ve sdílené složce. Tato spotřeba totiž využité kapacitě pro sdílené složky obsahuje metadata. | |
| **9.** |Zotavení po havárii |Můžete provádět pouze obnovení po havárii souborového serveru ke stejné doméně jako u zdrojového zařízení. V této verzi nepodporuje zotavení po havárii na cílovém zařízení v jiné doméně. |Tato možnost je implementovaná v novější verzi. Další informace, přejděte na [převzetí služeb při selhání a zotavení po havárii pro pole virtuální zařízení StorSimple](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Virtuální zařízení StorSimple nelze spravovat pomocí prostředí Azure PowerShell v této verzi. |Všechny správu virtuálních zařízení by mělo být provedeno prostřednictvím portálu Azure a místní webového uživatelského rozhraní. |
| **11.** |Změna hesla |Konzole virtuální pole zařízení přijímá pouze vstup v en-us klávesové formátu. | |
| **12.** |CHAP |Po vytvoření pověření CHAP nelze odebrat. Kromě toho pokud změníte přihlašovací údaje CHAP, budete muset svazky převést do režimu offline a pak přiřaďte je online změna se projeví. |Tomuto problému dochází v novější verzi. |
| **13.** |serveru se službou iSCSI |'Použít úložiště, zobrazí pro svazek iSCSI může být jiný služby StorSimple Manager zařízení a iSCSI hostitele. |Zobrazení souborů má hostitel iSCSI.<br></br>Zařízení se zobrazí na bloky přidělené při při maximální velikosti svazku. |
| **14.** |Souborový server |Pokud má soubor ve složce alternativní Data datového proudu (reklamy) s ním spojená, není reklamy zálohovat nebo obnovit prostřednictvím obnovení po havárii, klonování a obnovení na úrovni položek. | |
| **15.** |Souborový server |Symbolické odkazy nejsou podporovány. | |
| **16.** |Souborový server |Soubory chráněné pomocí Windows systém souboru (Encrypting File System) po zkopírování přes nebo uložený na výsledek pole virtuální zařízení StorSimple souborového serveru v nepodporované konfigurace.  | |
| **17.** |Aktualizace |Pokud se zobrazí chyba kód: 2359302 (šestnáctkově 0x240006) při pokusu o instalaci opravy hotfix prostřednictvím místního uživatelského rozhraní, pak z toho vyplývá, že oprava hotfix je již nainstalována na zařízení.   | |

## <a name="next-step"></a>Další krok
[Nainstalujte aktualizaci 0,6](storsimple-virtual-array-install-update-06.md) na pole virtuální zařízení StorSimple.

## <a name="references"></a>Odkazy
Hledáte starší poznámku k verzi? Přejít na:

* [K vydání 0,5 verze Update pole virtuální zařízení StorSimple](storsimple-virtual-array-update-05-release-notes.md)
* [K vydání 0,4 verze Update pole virtuální zařízení StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [K vydání 0,3 verze Update pole virtuální zařízení StorSimple](storsimple-ova-update-03-release-notes.md)
* [Poznámky k verzi zařízení StorSimple virtuální pole aktualizace 0,1 a 0,2](storsimple-ova-update-01-release-notes.md)
* [Poznámky k verzi zařízení StorSimple virtuální pole Obecné dostupnosti](storsimple-ova-pp-release-notes.md)

