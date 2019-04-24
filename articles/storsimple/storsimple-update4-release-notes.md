---
title: Poznámky k verzi StorSimple 8000 Series Update 4 | Dokumentace Microsoftu
description: Popisuje nové funkce, problémy a řešení StorSimple 8000 Series Update 4.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60530988"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Poznámky k verzi StorSimple 8000 Series Update 4

## <a name="overview"></a>Přehled

Následující poznámky k verzi popisují nové funkce a identifikovat kritické otevřené problémy pro StorSimple 8000 Series Update 4. Obsahují také seznam aktualizací softwaru StorSimple jsou v ní obsažené. 

Update 4 je použít na jakékoli zařízení StorSimple s vydání (GA) nebo aktualizace 0.1 prostřednictvím Update 3.1. Verze zařízení spojené s aktualizací Update 4 je 6.3.9600.17820.

Přečtěte si prosím informace obsažené v poznámkách k verzi, než nasadíte aktualizace v řešení StorSimple.

> [!IMPORTANT]
> * Update 4 je software pro zařízení, USM firmwaru, LSI ovladače a firmware, firmwaru disku, Storport a Spaceport, zabezpečení a jiné aktualizace operačního systému. Trvá přibližně 4 hodin k instalaci této aktualizace. Aktualizace firmwaru disku je rušivé aktualizace a má za následek výpadek pro vaše zařízení. Doporučujeme, abyste použili aktualizovat zařízení s aktualizací Update 4. 
> * Pro nové verze, nemusí zobrazit aktualizace okamžitě, protože děláme postupné zavádění aktualizací. Počkejte pár dní a pak vyhledávání aktualizací znovu jako tyto bude brzy k dispozici.

## <a name="whats-new-in-update-4"></a>Co je nového v aktualizaci Update 4

Byly provedeny následující klíčová vylepšení a opravy chyb v aktualizaci Update 4.

* **Chytřejší automatizované algoritmy recyklace místa** – aktualizace 4, automatizované pročištěním algoritmy jsou vylepšené tak, upravte cykly recyklaci místa podle očekávané uvolňovaného volné místo dostupné v cloudu. 

* **Vylepšení výkonu pro místně připojené svazky** – aktualizace Update 4 se zvýšil výkon místně připojené svazky ve scénářích, které mají vysokou ingestování (srovnatelná se velikost svazku data).

* **Obnovení na základě Heatmapu** – v dřívějších verzích po zotavení po havárii (DR) se data obnovila z cloudu na základě vzorců přístupu výsledkem je snížení výkonu. 

    Nová funkce je implementována v aktualizaci Update 4, že sleduje často používaná data k vytvoření heatmapu, když je zařízení v použití přijmou zotavení po Havárii (nejčastěji používané datové bloky mají vysokou heat vzhledem k tomu použít menší bloky dat mají nízké heat). Po zotavení po Havárii StorSimple využívá Heat mapě automaticky obnovit a dosazení dat z cloudu. 

    Všechny obnovení jsou nyní heatmapu na základě obnovení. Další informace o tom, jak dotazovat a zrušení úloh obnovení a dosazování heatmapu na základě, přejděte na [prostředí Windows PowerShell pro StorSimple Reference k rutinám](https://technet.microsoft.com/library/dn688168.aspx).

* **StorSimple Diagnostika** – umožňuje snadno diagnostikovat byla vydána v Update 4, StorSimple Diagnostika a řešení potíží týkající se stav součásti systému, sítě, výkon a hardware. Tento nástroj je spustit přes Windows PowerShell pro StorSimple. Další informace najdete v části [řešení potíží pomocí StorSimple Diagnostika](storsimple-8000-diagnostics.md).

* **Na základě uživatelského rozhraní nástroj pro migraci StorSimple** -před touto verzí vyžaduje migraci dat z řad 5000 – 7000 uživatelům provádět součástí postupu migrace pomocí rozhraní Azure Powershellu. V této verzi nástroj snadným ovládáním migrací s použitím uživatelského rozhraní StorSimple je k dispozici pro podporu pro usnadnění stejný pracovní postup migrace. Tento nástroj by také umožnilo pro konsolidaci intervalů obnovení. 

* **Změny související s FIPS** – toto vydání a vyšší, je povolený standard FIPS ve výchozím nastavení na všech zařízeních StorSimple 8000 series pro Microsoft Azure Government i Azure veřejný cloud účty.

* **Aktualizovat změny** – v tomto vydání jsme opravili chyby související s aktualizovat chyby.

* **Výstrahy pro selhání disku** – v této verzi se přidá nová výstraha, které uživatele upozorní na brzké selhání disku. Pokud narazíte na tuto výstrahu, obraťte se na Microsoft Support dodání náhradního disku. Další informace najdete v části [výstrahy hardwaru zařízení StorSimple](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Změny nahrazení řadiče** – přidání rutinu, která umožňuje uživateli zadat dotaz na stav proces nahrazení řadiče v této verzi. Další informace najdete v části [rutiny dotazu řadič nahrazení](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Chyby opravené v aktualizaci Update 4

Následující tabulka obsahuje souhrn problémy, které byly opraveny v aktualizaci Update 4.    

| Ne | Funkce | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Převzetí služeb při selhání |V předchozí verzi po převzetí služeb při selhání, došlo k nějakému problému související s vyčištění zjištěnou na webu zákazníka. Tento problém je vyřešen v této verzi. |Ano |Ano |
| 2 |Místně připojené svazky |V předchozí verzi došlo k nějakému problému na příslušný svazek vytváření místně vázaných svazků, které způsobí chyby při vytváření svazku. Tento problém byl způsobila kořenové a opravené v této verzi. |Ano |Ne |
| 3 |Balíček pro podporu |V předchozí verzi byly problémy související se balíček pro podporu, která způsobí System.OutOfMemory výjimky nebo jiné chyby, což vede k selhání vytváření balíčků podpory. Tyto chyby jsou opravené v této verzi. |Ano |Ano |
| 4 |Monitorování |V předchozí verzi existuje problém týkající se sledování grafů pro místně připojené svazky ve kterém se zobrazilo spotřebu v EB. Tato chyba se vyřeší v této verzi. |Ano |Ano |
| 5 |Migrace |V předchozí verzi byly několik problémů souvisejících s spolehlivost migrace z řad 5000 – 7000 zařízení řady 8000. V této verzi mají tyto problémy vyřeší. |Ano |Ano |
| 6 |Aktualizace |V předchozích verzích, pokud došlo k selhání aktualizace, řadiče přejde do režimu obnovení a proto nelze pokračovat v aktualizaci uživatele a bude nutné kontaktovat Microsoft Support. <br> Toto chování se změnil v této verzi. Pokud má uživatel k selhání aktualizace po obou řadičích spuštěna stejná verze (s aktualizací Update 4), řadiče nepřejdou do režimu obnovení. Pokud uživatel zaznamená toto selhání, doporučujeme, aby čekat bit a pak zkuste aktualizaci zopakovat. Opakování může uspět. Pokud se nový pokus nezdaří, měli byste kontaktovat Microsoft Support. |Ano |Ano |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Známé problémy v aktualizaci Update 4 z předchozích verzí

Nejsou žádné nové známé problémy v aktualizaci Update 4. Seznam problémů přenesou na aktualizaci Update 4 z předchozích verzí, přejděte na [zpráva k vydání verze Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Připojená k sériovým SCSI (SAS) kontroleru a aktualizace firmwaru v aktualizaci Update 4

Tato verze má řadič SAS a aktualizace LSI ovladače a firmware. Další informace o tom, jak tyto aktualizace nainstalovat, naleznete v tématu [instalace aktualizace 4](storsimple-install-update-4.md) zařízení StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Aktualizace virtuálního zařízení v aktualizaci Update 4

Tato aktualizace nejde použít u řešení StorSimple Cloud Appliance (označované také jako virtuální zařízení). Nové virtuální zařízení bude potřeba vytvořit. 

## <a name="next-step"></a>Další krok

Zjistěte, jak [instalace aktualizace 4](storsimple-install-update-4.md) zařízení StorSimple.

