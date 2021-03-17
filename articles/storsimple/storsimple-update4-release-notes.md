---
title: StorSimple 8000 Series Update 4 – zpráva k vydání verze | Microsoft Docs
description: Popisuje nové funkce, problémy a alternativní řešení pro StorSimple 8000 Series Update 4.
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
ms.openlocfilehash: 060a52b61dcd43fe0d05b2a8ef594c0ab9f2cc9e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954048"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Zpráva k vydání verze pro StorSimple 8000 Series Update 4

## <a name="overview"></a>Přehled

Následující poznámky k verzi popisují nové funkce a identifikují důležité otevřené problémy pro StorSimple 8000 Series Update 4. Obsahují také seznam StorSimple aktualizací softwaru, které jsou součástí této verze. 

Aktualizaci 4 lze použít pro jakékoli zařízení StorSimple s verzí (GA) nebo Update 0,1 až do aktualizace 3,1. Verze zařízení, která je přidružená k Update 4, je 6.3.9600.17820.

Před nasazením aktualizace do řešení StorSimple si prosím přečtěte informace uvedené v poznámkách k verzi.

> [!IMPORTANT]
> * Aktualizace 4 má software zařízení, firmware USM, adaptér LSI a firmware, firmware disku, ovladače Storport a Spaceport, zabezpečení a další aktualizace operačního systému. Instalace této aktualizace trvá přibližně 4 hodiny. Aktualizace firmwaru disku je nevratná aktualizace a vede k výpadkům vašeho zařízení. Doporučujeme, abyste použili aktualizaci 4, aby vaše zařízení zůstalo v aktuálním stavu. 
> * Pro nové verze se aktualizace nemusí okamžitě zobrazovat, protože provádíme dvoufázové zavedení aktualizací. Počkejte několik dní a pak znovu vyhledejte aktualizace, protože budou brzy k dispozici.

## <a name="whats-new-in-update-4"></a>Co je nového ve Update 4

V Update 4 byly provedeny následující hlavní vylepšení a opravy chyb.

* **Inteligentní automatizované algoritmy pro opětovné získávání prostoru** – v Update 4 jsou vylepšené algoritmy automatického opětovného získávání prostorů rozšířené, aby se nastavily cykly opětovného získávání prostorů na základě očekávaného uvolněného místa v cloudu. 

* **Vylepšení výkonu pro místně připojené svazky** – aktualizace 4 vylepšuje výkon místně připnutého svazku ve scénářích, které mají vysokou příjem dat (data srovnatelná s velikostí svazku).

* **Obnovení založené na heatmapu** – v předchozích verzích po zotavení po havárii (Dr) se data obnovila z cloudu na základě vzorů přístupu, což má za následek pomalý výkon. 

    V Update 4 je implementována nová funkce, která sleduje často používaná data k vytvoření heatmapu, když se zařízení používá před nástrojem DR (nejčastěji používané datové bloky mají vysoký teplo, zatímco méně využité bloky dat mají nízký výkon). Po zotavení po havárii nástroj StorSimple použije heatmapu k automatickému obnovení a dehydratovanému využití dat z cloudu. 

    Všechna obnovení jsou nyní heatmapu založenou na obnovení. Další informace o tom, jak provést dotazování a zrušení úloh obnovení a reheatmapu na základě služby, naleznete v tématu [Windows PowerShell pro StorSimple Reference k rutinám](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).

* **Nástroj StorSimple Diagnostics** – v Update 4 se uvolňuje nástroj StorSimple Diagnostics, který umožňuje snadnou diagnostikovat a řešit problémy související se stavem systému, sítě, výkonu a hardwarových součástí. Tento nástroj se spouští prostřednictvím Windows PowerShell pro StorSimple. Další informace najdete v tématu [řešení potíží pomocí nástroje StorSimple Diagnostics](storsimple-8000-diagnostics.md).

* **Nástroj pro migraci StorSimple založený na uživatelském rozhraní** – před touto verzí musí migrace dat z řady 5000-7000 vyžadovat, aby uživatelé mohli spustit součást pracovního postupu migrace pomocí rozhraní Azure PowerShell. V této verzi je k dispozici snadno použitelný nástroj pro migraci StorSimple založený na uživatelském rozhraní, který vám umožní podporovat stejný pracovní postup migrace. Tento nástroj také umožňuje konsolidaci sad obnovení. 

* **Změny týkající se standardu FIPS** – Tato verze je navýšená, FIPS je ve výchozím nastavení povolená na všech zařízeních řady StorSimple 8000 pro účty Azure s veřejným cloudem Microsoft Azure Government i Azure.

* **Aktualizace změn** – v této verzi byla opravena chyba související s chybami aktualizace.

* **Výstraha při selhání disku** – v této verzi se přidá nová výstraha upozorňující uživatele na hrozící selhání disku. Pokud se zobrazí tato výstraha, kontaktujte podpora Microsoftu k odeslání náhradního disku. Další informace najdete [v oznámeních o hardwaru na zařízení StorSimple](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Změny nahrazení řadiče** – v této verzi se přidá rutina, která uživateli umožní dotazovat se na stav procesu nahrazení řadiče. Pokud potřebujete další informace, přečtěte si do [rutiny stav nahrazení řadiče dotazů](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).


## <a name="issues-fixed-in-update-4"></a>Problémy opravené ve Update 4

Následující tabulka poskytuje souhrn problémů, které byly opraveny v Update 4.    

| Ne | Doporučené | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Převzetí služeb při selhání |V dřívější verzi se po převzetí služeb při selhání objevil problém týkající se vyčištění na webu zákazníka. Tento problém je opravený v této verzi. |Yes |Yes |
| 2 |Místně připojené svazky |V předchozí verzi došlo k potížím se souvisejícím vytvářením svazků pro místně připnuté svazky, které by způsobily selhání vytvoření svazku. Tento problém byl způsobený kořenem a opravil v této verzi. |Yes |Ne |
| 3 |Balíček pro podporu |V předchozí verzi byly problémy související s balíčkem podpory, které by způsobily výjimku System. OutOfMemory nebo jiné chyby, což vedlo k chybě při vytváření balíčku pro podporu. Tyto chyby jsou v této verzi opravené. |Yes |Yes |
| 4 |Monitorování |V předchozí verzi došlo k potížím souvisejícím s monitorováním grafů pro místně připnuté svazky, ve kterých se spotřeba zobrazila v EB. Tato chyba je vyřešena v této verzi. |Yes |Yes |
| 5 |Migrace |V předchozí verzi bylo k dispozici několik problémů souvisejících s spolehlivostí migrace z 5000-7000 řad na zařízení 8000 series. Tyto problémy byly řešeny v této verzi. |Yes |Yes |
| 6 |Aktualizace |Pokud v předchozích verzích došlo k chybě aktualizace, řadiče přešly do režimu obnovení, takže uživatel nemůže v aktualizaci pokračovat a bude potřebovat kontaktovat podpora Microsoftu. <br> Toto chování se v této verzi změnilo. Pokud má uživatel chybu aktualizace, když oba řadiče používají stejnou verzi (Update 4), řadiče nepřejde do režimu obnovení. Pokud se tato chyba vyskytne uživateli, doporučujeme, abyste počkali na bitovou kopii, a pak zkuste aktualizaci zopakovat. Opakování může být úspěšné. Pokud se opakování nepovede, měli byste se obrátit na podpora Microsoftu. |Yes |Yes |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Známé problémy v Update 4 z předchozích verzí

V Update 4 nejsou žádné nové známé problémy. Seznam problémů přepravovaných s aktualizací Update 4 z předchozích verzí najdete v [poznámkách k verzi Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Řadič SCSI a aktualizace firmwaru připojené k sériovému portu (SAS) ve Update 4

Tato verze obsahuje řadič SAS a aktualizace ovladačů LSI a firmware. Další informace o tom, jak tyto aktualizace nainstalovat, najdete v tématu [instalace aktualizace Update 4](./storsimple-8000-install-update-4.md) na zařízení StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Aktualizace virtuálních zařízení v aktualizaci Update 4

Tuto aktualizaci nelze použít pro StorSimple Cloud Appliance (označuje se také jako virtuální zařízení). Nová virtuální zařízení bude potřeba vytvořit. 

## <a name="next-step"></a>Další krok

Přečtěte si, jak [nainstalovat Update 4](./storsimple-8000-install-update-4.md) na zařízení StorSimple.