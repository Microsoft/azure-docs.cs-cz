---
title: Synchronizace souborů Azure zásady cloudové vrstvy | Microsoft Docs
description: Podrobnosti o tom, jak zásady pro volné místo pro data a svazky spolupracují v různých scénářích.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3020737e91e1fe5c4af3e23a147fa0ea16037d3b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204321"
---
# <a name="cloud-tiering-policies"></a>Zásady vrstvení cloudu

Vrstvení cloudu má dvě zásady, které určují, které soubory jsou vrstveny do cloudu: **zásady pro volné místo svazku** a **zásady data**.

**Zásada pro volné místo svazku** zajišťuje, že zadané procento místního svazku, na kterém je umístěný koncový bod serveru, je vždycky zadarmo. 

Počet souborů úrovně **zásad** , které se naposled přistupovaly k poslednímu použití před × dní nebo později. Zásada pro volné místo svazku bude vždycky mít přednost. Pokud na svazku není dost volného místa pro uložení tolika dní, kolik je potřeba u souborů popsaných v zásadách data, Synchronizace souborů Azure přepíše zásadu data a bude pokračovat ve vrstvení souborů coldest, dokud nedosáhne procento volného místa na svazku.

## <a name="how-both-policies-work-together"></a>Jak obě zásady společně spolupracují

K ilustraci, jak tyto zásady fungují, použijeme příklad: řekněme, že jste nakonfigurovali Synchronizace souborů Azure na místním svazku 500 GB a nepovolili jste cloudovou vrstvu. Jedná se o soubory ve sdílené složce:

|Název souboru |Čas posledního přístupu  |Velikost souboru  |Uloženo v |
|----------|------------------|-----------|----------|
|Soubor 1    | před 2 dny  | 10 GB | Server a sdílená složka Azure
|Soubor 2    | před 10 dny | 30 GB | Server a sdílená složka Azure
|Soubor 3    | před 1 rokem | 200 GB | Server a sdílená složka Azure
|Soubor 4    | 1 rok, před 2 dny | 130 GB | Server a sdílená složka Azure
|Soubor 5    | 2 roky, před 1 dnem | 140 GB | Server a sdílená složka Azure

**Změna 1:** Povolili jste vrstvení cloudu, nastavili jste zásady volného místa svazku o 20% a zanechali jste zakázané zásady data. Při této konfiguraci zajišťuje cloudová vrstva 20% (v tomto případě 100 GB) místa, které je zdarma a k dispozici na místním počítači. V důsledku toho je celková kapacita místní mezipaměti 400 GB. To 400 GB uloží poslední a často dostupné soubory na místním svazku.

V této konfiguraci budou v místní mezipaměti uloženy pouze soubory 1 až 4 a soubor 5 by byl vrstven. Toto je jenom 370 GB ze 400 GB, které by bylo možné použít. Soubor 5 je 140 GB a překročil limit 400-GB, pokud byl uložen do mezipaměti v místním počítači. 

**Změna 2:** Řekněme, že uživatel přistupuje k souboru 5. Tím se ve sdílené složce vytvoří soubor 5 s naposledy použitým souborem. V důsledku toho by byl soubor 5 uložen v místní mezipaměti a musí odpovídat limitu 400 GB, soubor 4 by byl vrstven. Následující tabulka uvádí, kde jsou soubory uloženy, s těmito aktualizacemi:

|Název souboru |Čas posledního přístupu  |Velikost souboru  |Uloženo v |
|----------|------------------|-----------|----------|
|Soubor 5    | Před 2 hodinami | 140 GB | Server a sdílená složka Azure
|Soubor 1    | před 2 dny  | 10 GB | Server a sdílená složka Azure
|Soubor 2    | před 10 dny | 30 GB | Server a sdílená složka Azure
|Soubor 3    | před 1 rokem | 200 GB | Server a sdílená složka Azure
|Soubor 4    | 1 rok, před 2 dny | 130 GB | Sdílená složka Azure, vrstvená místně

**Změna 3:** Řekněme, že jste aktualizovali zásady tak, aby zásady vrstev na základě data byly 60 dní a aby zásady volného místa svazku byly 70%. V místní mezipaměti se teď dají ukládat jenom až 150 GB. I když byl k souboru 2 přidaný před méně než 60 dní, zásada pro volné místo svazku přepíše zásady data a zachová se v souboru s 70% místním volným místem.

**Změna 4:** Pokud jste změnili zásadu volného místa svazku na 20% a potom jste použili `Invoke-StorageSyncFileRecall` k odvolání všech souborů, které se vejdou na místní disk, a přitom dodržují zásady vrstev cloudu, tabulka by vypadala takto:

|Název souboru |Čas posledního přístupu  |Velikost souboru  |Uloženo v |
|----------|------------------|-----------|----------|
|Soubor 5    | před 1 hodinou  | 140 GB | Server a sdílená složka Azure
|Soubor 1    | před 2 dny  | 10 GB | Server a sdílená složka Azure
|Soubor 2    | před 10 dny | 30 GB | Server a sdílená složka Azure
|Soubor 3    | před 1 rokem | 200 GB | Sdílená složka Azure, vrstvená místně
|Soubor 4    | 1 rok, před 2 dny | 130 GB | Sdílená složka Azure, vrstvená místně

V tomto případě by soubory 1, 2 a 5 byly uloženy v mezipaměti místně a soubory 3 a 4 by byly vrstveny. Vzhledem k tomu, že zásady data jsou 60 dní, jsou soubory 3 a 4 vrstveny, i když zásada volného místa svazku umožňuje až 400 GB v místním prostředí.

> [!NOTE] 
> Když zákazníci změní zásadu volného místa svazku na menší hodnotu (například z 20% na 10%), soubory se nevrátí automaticky. nebo změňte zásady data na větší hodnotu (například 20 dní až 50 dní).

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Několik koncových bodů serveru na místním svazku

Ve vrstvách cloudu se dá povolit několik koncových bodů serveru na jednom místním svazku. Pro tuto konfiguraci byste měli nastavit velikost volného místa svazku na stejnou hodnotu pro všechny koncové body serveru na stejném svazku. Pokud nastavíte jiné zásady volného místa svazku pro několik koncových bodů serveru na stejném svazku, bude mít přednost největší procento volného místa na svazku. Označuje se jako **efektivní zásada volného místa svazku**. Pokud máte například tři koncové body serveru na stejném místním svazku, jedna sada na 15%, další sada na 20% a třetí sada na 30%, budou všechny začínat na vrstvu souborů coldest, pokud mají k dispozici méně než 30% volného místa.

## <a name="next-steps"></a>Další kroky
* [Monitorování vrstvení cloudu](storage-sync-monitor-cloud-tiering.md)
