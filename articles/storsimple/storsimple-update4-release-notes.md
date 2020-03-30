---
title: StorSimple 8000 Series Update 4 poznámky k verzi | Dokumenty společnosti Microsoft
description: Popisuje nové funkce, problémy a řešení pro aktualizaci StorSimple 8000 Series Update 4.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: ef95ca7b9f94690b607e37fbf5d9378c2f2bcfda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254583"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>StorSimple 8000 Series Update 4 poznámky k verzi

## <a name="overview"></a>Přehled

Následující poznámky k verzi popisují nové funkce a identifikují kritické otevřené problémy pro aktualizaci Řady StorSimple 8000 4. Obsahují také seznam aktualizací softwaru StorSimple zahrnutých v této verzi. 

Aktualizaci 4 lze použít na libovolné zařízení StorSimple se spuštěním verze (GA) nebo aktualizace 0.1 až aktualizace 3.1. Verze zařízení přidružená k aktualizaci 4 je 6.3.9600.17820.

Před nasazením aktualizace v řešení StorSimple si přečtěte informace obsažené v poznámkách k verzi.

> [!IMPORTANT]
> * Aktualizace 4 obsahuje software zařízení, firmware USM, ovladač a firmware LSI, firmware disku, Storport a Spaceport, zabezpečení a další aktualizace operačního systému. Instalace této aktualizace trvá přibližně 4 hodiny. Aktualizace firmwaru disku je rušivá aktualizace a má za následek prostoje vašeho zařízení. Doporučujeme použít aktualizaci 4, aby bylo vaše zařízení aktuální. 
> * U nových verzí se aktualizace nemusí zobrazit okamžitě, protože provádíme postupné zavádění aktualizací. Počkejte několik dní a pak znovu vyskenujte aktualizace, protože budou brzy k dispozici.

## <a name="whats-new-in-update-4"></a>Co je nového v aktualizaci 4

V aktualizaci 4 byla provedena následující klíčová vylepšení a opravy chyb.

* **Chytřejší automatizované algoritmy rekultivace prostoru** – V aktualizaci 4 jsou automatizované algoritmy rekultivace prostoru vylepšeny tak, aby upravovaly cykly rekultivace prostoru na základě očekávaného regenerovaného prostoru dostupného v cloudu. 

* **Vylepšení výkonu pro místně vázaných svazků** – aktualizace 4 zlepšila výkon místně vázaných svazků ve scénářích s vysokým požitím dat (data srovnatelná s velikostí svazku).

* **Obnovení na základě heatmap** – v dřívějších verzích po zotavení po havárii (DR) byla data obnovena z cloudu na základě vzorů přístupu, což vede ke pomalému výkonu. 

    Nová funkce je implementována v aktualizaci 4, která sleduje často používaná data, aby vytvořila heatmapu, když je zařízení používáno před dr. (Většina použitých datových bloků má vysoké teplo, zatímco méně používané bloky mají nízké teplo). Po zotavení po zotavení po havárii StorSimple používá heatmap automaticky obnovit a rehydratovat data z cloudu. 

    Všechna obnovení jsou nyní heatmap na obnovení. Další informace o tom, jak dotazovat a zrušit úlohy obnovení a rehydratace na základě heatmap, naleznete v [aplikaci Windows PowerShell for StorSimple cmdlet reference](https://technet.microsoft.com/library/dn688168.aspx).

* **Nástroj StorSimple Diagnostics** – V aktualizaci 4 je vydán nástroj StorSimple Diagnostics, který umožňuje snadnou diagnostiku a řešení problémů souvisejících se stavem systémových, síťových, výkonových a hardwarových součástí. Tento nástroj je spuštěn prostřednictvím prostředí Windows PowerShell pro StorSimple. Další informace naleznete v [řešení potíží pomocí nástroje StorSimple Diagnostics](storsimple-8000-diagnostics.md).

* **Nástroj StorSimple Migration založený na uživatelském rozhraní** – před vydáním této verze migrace dat z řady 5000 7000 vyžadovala, aby uživatelé provedli část pracovního postupu migrace pomocí rozhraní Azure PowerShell. V této verzi je pro podporu k dispozici snadno použitelný nástroj StorSimple Migration založený na rozhraní, který usnadňuje stejný pracovní postup migrace. Tento nástroj by také umožnil konsolidaci nástrojů pro obnovu. 

* **Změny související s FIPS** – tato verze je ve výchozím nastavení povolená na všech zařízeních řady StorSimple 8000 pro účty veřejného cloudu Microsoft Azure a Azure.

* **Změny aktualizací** – V této verzi byly opraveny chyby související s chybami aktualizace.

* **Výstraha pro selhání disku** – v této verzi je přidána nová výstraha, která uživatele upozorní na hrozící selhání disku. Pokud narazíte na tuto výstrahu, obraťte se na podporu společnosti Microsoft a dodáte náhradní disk. Další informace naleznete v [hardwarových výstrahách na zařízení StorSimple](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Změny nahrazení řadiče** - V této verzi je přidána rutina, která uživateli umožňuje dotaz ovat stav procesu nahrazení řadiče. Další informace naleznete v [rutině na dotaz řadiče náhradní stav](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Problémy opravené v aktualizaci 4

Následující tabulka obsahuje souhrn problémů, které byly opraveny v aktualizaci 4.    

| Ne | Funkce | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Převzetí služeb při selhání |V dřívější verzi po převzetí služeb při selhání došlo k problému souvisejícímu s vyčištěním pozorovaným v místě zákazníka. Tento problém je vyřešen v této verzi. |Ano |Ano |
| 2 |Místně připojené svazky |V předchozí verzi došlo k problému souvisejícího vytváření svazků pro místně vázané svazky, který by vedl k selhání vytváření svazku. Tento problém byl root-caused a opravena v této verzi. |Ano |Ne |
| 3 |Balíček podpory |V předchozí verzi došlo k problémům souvisejícím s balíčkem podpory, které by vedly k výjimce System.OutOfMemory nebo jiným chybám, které vedly k selhání vytvoření balíčku podpory. Tyto chyby jsou opraveny v této verzi. |Ano |Ano |
| 4 |Monitorování |V předchozí verzi došlo k problému souvisejícímu s monitorováním grafů pro místně vázané svazky, kde byla spotřeba zobrazena v EB. Tato chyba je vyřešena v této verzi. |Ano |Ano |
| 5 |Migrace |V předchozí verzi bylo několik problémů souvisejících se spolehlivostí migrace z řady 5000-7000 na zařízení řady 8000. Tyto problémy byly vyřešeny v této verzi. |Ano |Ano |
| 6 |Aktualizace |V předchozích verzích, pokud došlo k selhání aktualizace, řadiče by přejít do režimu obnovení, a proto uživatel nemohl pokračovat v aktualizaci a bude muset kontaktovat podporu společnosti Microsoft. <br> Toto chování bylo změněno v této verzi. Pokud má uživatel selhání aktualizace po obou řadičů jsou spuštěny stejnou verzi (aktualizace 4), řadiče nepřejdou do režimu obnovení. Pokud uživatel narazí na tuto chybu, doporučujeme, aby počkat na bit a potom opakujte aktualizaci. Opakování může být úspěšné. Pokud se opakování nezdaří, měli by kontaktovat podporu společnosti Microsoft. |Ano |Ano |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Známé problémy v aktualizaci 4 z předchozích verzí

V aktualizaci 4 nejsou žádné nové známé problémy. Seznam problémů přenesených do aktualizace 4 z předchozích verzí naleznete v [poznámkách k verzi aktualizace 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Sériově připojený řadič SCSI (SAS) a aktualizace firmwaru v aktualizaci 4

Tato verze obsahuje řadič SAS a aktualizace ovladačů a firmwaru LSI. Další informace o instalaci těchto aktualizací naleznete v [tématu instalace aktualizace 4](storsimple-install-update-4.md) na zařízení StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Aktualizace virtuálních zařízení v aktualizaci 4

Tuto aktualizaci nelze použít na StorSimple Cloud Appliance (označované také jako virtuální zařízení). Bude třeba vytvořit nová virtuální zařízení. 

## <a name="next-step"></a>Další krok

Přečtěte si, jak [nainstalovat aktualizaci 4](storsimple-install-update-4.md) do zařízení StorSimple.

