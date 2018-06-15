---
title: Poznámky k verzi aktualizace pole virtuální zařízení StorSimple | Microsoft Docs
description: Popisuje důležité otevřené problémy a řešení pro pole virtuální zařízení StorSimple verzi Update 0.3.
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
ms.openlocfilehash: fe9d4f6b232e9abcf1fe9fc5657044b6c72fedb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875512"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>Poznámky k verzi 0,3 aktualizace pole virtuální zařízení StorSimple
## <a name="overview"></a>Přehled
Následující poznámky k verzi identifikovat kritická otevřené problémy a vyřešit problémy pro Microsoft Azure StorSimple virtuální pole aktualizace.

Poznámky k verzi se průběžně aktualizuje a při zjištění zásadních problémů, které vyžadují řešení, se řešení přidá. Před nasazením pole virtuální zařízení StorSimple, pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Update 0.3 odpovídá verzi softwaru **10.0.10288.0**.

> [!NOTE]
> Aktualizace jsou rušivý a zařízení restartovat. Pokud vstupně-výstupní operace probíhá, zařízení způsobuje výpadek.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Co je nového v aktualizaci Update 0.3
Update 0.3 je primárně oprava chyby sestavení. V této verzi byla vyřešili několik chyb, což vede k selhání zálohování v předchozí verzi.

## <a name="issues-fixed-in-the-update-03"></a>Chyby v Update 0.3
Následující tabulka obsahuje souhrn chyby v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Zálohování |Problém zobrazila v starší verzi, kde by zálohování nezdaří dokončete pro sdílené složky. Pokud tento problém došlo k chybě, dojde k selhání úlohy zálohování a Kritická výstraha objevila na službu StorSimple Manager upozorní uživatele. Tento problém neovlivnila dat na sdílených složek nebo přístup k datům. Hlavní příčinou byl zjištění a napravení v této verzi. <br></br> Oprava zpětně neprojeví u sdílené složky, které jsou již zobrazuje tento problém. Zákazníci, kteří se zobrazuje tento problém by měl nejprve použít Update 0.3, a poté kontaktovat Microsoft Support k provedení úplné zálohy systému o vyřešení problému. Místo kontaktovat Microsoft Support, zákazníků můžete také obnovit na novou sdílenou složku ze zálohy v pořádku pro ovlivněné sdílené složky. |
| 2 |iSCSI |Problém zobrazila v starší verzi, kde by při kopírování dat na svazek v poli virtuální zařízení StorSimple zmizí svazky. Tento problém byl opraven v této verzi. <br></br> Opravy se projeví pouze na nově vytvořený svazky. Opravy zpětně neprojeví u svazků, které jsou již zobrazuje tento problém. Zákazníci by měli uvést ovlivněných svazky online prostřednictvím portálu Azure classic, proveďte zálohu pro tyto svazky a potom obnovte tyto svazky nové svazky. |

## <a name="known-issues-in-the-update-03"></a>Známé problémy v aktualizaci Update 0.3
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

## <a name="next-step"></a>Další krok
[Nainstalujte aktualizaci 0,3](storsimple-ova-install-update-01.md) na pole virtuální zařízení StorSimple.

## <a name="references"></a>Odkazy
Hledáte starší poznámku k verzi? Přejít na: 

* [Poznámky k verzi zařízení StorSimple virtuální pole aktualizace 0,1 a 0,2](storsimple-ova-update-01-release-notes.md)
* [Poznámky k verzi zařízení StorSimple virtuální pole Obecné dostupnosti](storsimple-ova-pp-release-notes.md)

