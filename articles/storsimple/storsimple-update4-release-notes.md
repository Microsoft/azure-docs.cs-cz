---
title: Poznámky k verzi zařízení StorSimple 8000 řady aktualizace 4 | Microsoft Docs
description: Popisuje nové funkce, problémy a řešení pro zařízení StorSimple 8000 řady aktualizací 4.
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
ms.openlocfilehash: f4db5a49b71b0c384da7af2b90bd5426a4f35c72
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Poznámky k verzi zařízení StorSimple 8000 řady aktualizací 4

## <a name="overview"></a>Přehled

Následující poznámky k verzi popisují nové funkce a identifikovat kritická otevřené problémy pro StorSimple 8000 řady aktualizací 4. Také obsahují seznam aktualizací softwaru zařízení StorSimple, zahrnuté v této verzi. 

Aktualizaci 4 můžete použít pro všechna zařízení StorSimple, s verzí (GA) nebo Update 0.1 prostřednictvím aktualizace 3.1. Zařízení verzi spojenou s aktualizací 4 je 6.3.9600.17820.

Přečtěte si informace obsažené v poznámkách k verzi před nasazením aktualizace v řešení StorSimple.

> [!IMPORTANT]
> * Aktualizace 4 obsahuje software zařízení, Seznam USM firmwaru, LSI ovladače a firmware, firmwaru disku, Storport a Spaceport, zabezpečení a jiné aktualizace operačního systému. Jak dlouho trvá přibližně 4 hodiny nainstalovat tuto aktualizaci. Aktualizace firmwaru disku je rušivý aktualizace a výsledkem výpadku pro vaše zařízení. Doporučujeme, abyste použili aktualizaci 4 k zachování aktualizovaného stavu zařízení. 
> * Pro nové verze, nemusí zobrazit aktualizace okamžitě, protože provedeme postupné zavádění aktualizací. Počkejte několik dní, a pak vyhledejte aktualizace znovu jako tyto brzy bude dostupná.

## <a name="whats-new-in-update-4"></a>Co je nového v aktualizaci 4

V aktualizaci 4 byly provedeny následující klíčových vylepšení a opravy chyb.

* **Chytřejší automatizované algoritmy recyklace místa** – v aktualizaci 4, recyklaci automatizované místa algoritmy líp upravit cyklů recyklaci místa podle očekávané regenerovaný místo k dispozici v cloudu. 

* **Vylepšení výkonu pro místně vázaných svazků** – aktualizace 4 je vylepšený výkon místně vázaných svazků ve scénářích, které mají přijímat vysoké data (data srovnatelná velikosti svazku).

* **Obnovení na základě Heatmap** -ve starších verzích, následující zotavení po havárii (DR), data byla obnovena z cloudu podle vzorů přístupu, výsledkem je nízký výkon. 

    Nová funkce je implementována v aktualizaci 4 sleduje často používaná data a vytvořit heatmap, když je zařízení používá před zotavení po Havárii (nejčastěji používaných bloky dat mají vysokou heat, zatímco menší bloky dat používá mít nízkou heat). Po zotavení po Havárii používá StorSimple heatmap pro automatické obnovení a rehydrataci při spotřebě data z cloudu. 

    Všechny obnovení jsou nyní heatmap na základě obnovení. Další informace o tom, jak dotaz a zrušení úloh obnovení a rehydrataci heatmap na základě, přejděte na [prostředí Windows PowerShell pro referenční informace o rutinách StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

* **Nástroj diagnostiky StorSimple** – v aktualizaci 4, nástroj pro diagnostiku StorSimple je vydán umožňující snadno diagnostice a řešení potíží s problémy související se stav součásti systému, sítě, výkonu a hardwaru. Tento nástroj je spuštěn prostřednictvím Windows Powershellu pro StorSimple. Další informace, přejděte na [řešení problémů pomocí diagnostiky StorSimple nástroj](storsimple-8000-diagnostics.md).

* **Na základě uživatelského rozhraní nástroj pro migraci StorSimple** -před touto verzí migraci dat z řady 5000 7000 požadované uživatelům provést část pracovní postup migrace pomocí rozhraní Azure PowerShell. V této verzi je k dispozici pro podporu, aby se usnadnilo stejný pracovní postup migrace nástroj Migrace StorSimple na základě uživatelského rozhraní na snadno použitelné. Tento nástroj by také umožňují konsolidace intervalů obnovení. 

* **Změny související s FIPS** – toto vydání a vyšší, FIPS ve výchozím na všechna zařízení řady StorSimple 8000 je povolený pro Microsoft Azure Government i Azure veřejných cloudů, účty.

* **Aktualizovat změny** – v této verzi bylo opraveno chyby související s aktualizovat selhání.

* **Výstrahy pro selhání disku** – v této verzi se přidá nová výstraha s upozorněním, uživatel brzké selhání disku. Pokud narazíte na tuto výstrahu, obraťte se na Microsoft Support pro odeslání náhradní disk. Další informace, přejděte na [hardwaru výstrahy na zařízení StorSimple](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Změny nahrazení řadiče** – přidání rutinu, která umožňuje uživatelům zjistit stav procesu nahrazení řadiče v této verzi. Další informace naleznete [rutiny zjistit stav nahrazení řadiče](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Chyby v aktualizaci 4

Následující tabulka obsahuje souhrn problémy, které jsme vyřešili v aktualizaci 4.    

| Ne | Funkce | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Převzetí služeb při selhání |Ve starší verzi po převzetí služeb při selhání, se problém související s čištění zjištěnými na straně zákazníka. Tento problém vyřešen v této verzi. |Ano |Ano |
| 2 |Místně vázaných svazků |V předchozí verzi došlo problém související svazku vytváření místně vázaných svazků, které by způsobilo chyby při vytváření svazku. Tento problém byl způsobeno kořenové a pevné v této verzi. |Ano |Ne |
| 3 |Balíček pro podporu |V předchozí verzi byly problémy související s podporu balíček, který by způsobilo System.OutOfMemory výjimky nebo jiné chyby, které vedly k nedodržení vytvoření balíčku podpory. Tyto chyby budou opraveny v této verzi. |Ano |Ano |
| 4 |Monitorování |V předchozí verzi existuje problém související s monitorování grafy pro místně připnuli svazky kde byl spotřeby ukazuje EB. Tato chyba se vyřeší v této verzi. |Ano |Ano |
| 5 |Migrace |V předchozí verzi nebyly k dispozici několik problémy související s spolehlivost migrace z řady 5000 7000 zařízení řady 8000. Mít byly tyto problémy řešeny v této verzi. |Ano |Ano |
| 6 |Aktualizovat |V předchozích verzích, pokud došlo k chybě aktualizace, pak se řadiče přejde do režimu obnovení a proto uživatele nelze pokračovat v aktualizaci a by bylo potřeba kontaktovat Microsoft Support. <br> Toto chování se změnilo v této verzi. Pokud má uživatel k selhání aktualizace po oběma řadičům běží stejná verze (aktualizace 4), pak se řadiče nepřecházejí do režimu obnovení. Pokud uživatel zaznamená této chybě, doporučujeme, abyste chvilku počkejte a pak zkuste znovu aktualizovat. Opakovaném může být úspěšné. V případě selhání opakovaném se měli kontaktovat Microsoft Support. |Ano |Ano |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Známé problémy v aktualizaci 4 z předchozích verzí

Nejsou žádné nové známé problémy v aktualizaci 4. Pro seznam problémů, které přenášejí do aktualizace 4 z předchozích verzí, přejděte na [poznámky k verzi Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Serial-attached SCSI (SAS) řadiče a aktualizace firmwaru v aktualizaci 4

Tato verze obsahuje řadič SAS a LSI ovladače a firmware aktualizace. Další informace o tom, jak tyto aktualizace nainstalujete najdete v tématu [nainstalovat aktualizace 4](storsimple-install-update-4.md) zařízení StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Aktualizace virtuální zařízení v aktualizaci 4

Tuto aktualizaci nelze nainstalovat do cloudu zařízení StorSimple (také označované jako virtuální zařízení). Nové virtuální zařízení bude potřeba vytvořit. 

## <a name="next-step"></a>Další krok

Zjistěte, jak [nainstalovat aktualizace 4](storsimple-install-update-4.md) zařízení StorSimple.

