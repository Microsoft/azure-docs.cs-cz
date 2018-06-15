---
title: Poznámky k verzi zařízení StorSimple 8000 řady aktualizace 2.2 | Microsoft Docs
description: Popisuje nové funkce, problémy a řešení pro zařízení StorSimple 8000 řady aktualizace 2.2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 78be340b4a47fed88f5e8c3f5741ae7024124bd5
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057838"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>Poznámky k verzi zařízení StorSimple 8000 řady aktualizace 2.2

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikovat kritická otevřené problémy pro StorSimple 8000 řady aktualizace 2.2. Také obsahují seznam aktualizací softwaru zařízení StorSimple, zahrnuté v této verzi.

Aktualizace 2.2 lze použít pro všechna zařízení StorSimple, s verzí (GA) nebo Update 0.1 prostřednictvím Update 2.1. Verze zařízení přidružené k aktualizaci 2.2 je 6.3.9600.17708.

Přečtěte si informace obsažené v poznámkách k verzi před nasazením aktualizace v řešení StorSimple.

> [!IMPORTANT]
> * Aktualizace 2.2 má pouze aktualizace softwaru. Jak dlouho trvá přibližně 1.5 – 2 hodiny nainstalovat tuto aktualizaci. 
> * Pokud používáte Update 2.1, doporučujeme použít aktualizaci 2.2 co nejdříve.
> * Pro nové verze, nemusí zobrazit aktualizace okamžitě, protože provedeme postupné zavádění aktualizací. Počkejte několik dní, a pak vyhledejte aktualizace znovu jako tyto brzy bude dostupná.
> 
> 

## <a name="whats-new-in-update-22"></a>Co je nového v aktualizaci 2.2
Byly provedeny následující klíčových vylepšení v aktualizaci 2.2.

* **Automatizované optimalizace recyklace místa** – při odstranění dat na dynamicky zřizované svazky, uvolní nutnost nepoužívané úložiště bloků. Tato verze je vylepšený proces recyklace místa z cloudu výsledná nevyužitého místa stává stále k dispozici rychlejší porovnání s předchozími verzemi.
* **Pořízení snímku vylepšení výkonu** – 2.2 aktualizace je vylepšený času na zpracování snímku v některých scénářích, kde je velké svazky se používá a je minimální k žádné mísení dat cloudu. Scénář, který bude využívat toto vylepšení by svazky archivu.
* **Posílení zabezpečení balíčku podporu shromažďování** – byly vylepšení, jako je balíček pro podporu shromáždit a nahrát v této verzi. 
* **Aktualizovat vylepšení spolehlivosti** – tato verze obsahuje opravy chyb, jejichž výsledkem vylepšení spolehlivosti aktualizace.

## <a name="issues-fixed-in-update-22"></a>Chyby v 2.2 aktualizace
Následující tabulka obsahuje souhrn problémy, které jsme vyřešili 2.2 aktualizace a 2.1.    

| Ne | Funkce | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Výkon hostitele |V dřívějších verzí byly problémy s výkonem na straně hostitele zaznamenali během vytváření místně vázaný svazek a převod vrstvený svazek na místně vázaný svazek. V této verzi, což by vedlo k zlepšení výkonu hostitele během vytváření a převod postupů svazku opravě těchto problémů. |Ano |Ne |
| 2 |Místně vázaných svazků |Ve výjimečných případech by systém k chybě při vytváření místně vázaný svazek. Tato chyba byla opravena v této verzi. |Ano |Ne |
| 3 |Vrstvení |Když metadata pro Cloud zařízení StorSimple (8010 a 8020) vrstvené do cloudu se vyskytly Ojediněle dojde k chybě. Tento problém vyřešen v této verzi. |Ne |Ano |
| 4 |Vytvoření snímku |Byly nějaké problémy, týkající se vytváření přírůstkové snímků ve scénářích s velké svazky a minimální k změn žádná data. V této verzi opravě těchto problémů. |Ano |Ano |
| 5 |Openstack ověřování |Při použití Openstack jako poskytovatele cloudové služby, může uživatel by spustit do nepravidelným chyby související s ověřováním, kde analyzátor JSON výsledkem havárie. Tato chyba je opravena v této verzi. |Ano |Ne |
| 6 |Kopírování na straně hostitele |V dřívějších verzích softwaru nepravidelným chyby související s ODX načasování zobrazila při kopírování dat z jednoho svazku na jiný svazek. To by způsobilo selhání řadiče a systém může potenciálně přejděte do režimu obnovení. Tato chyba je opravena v této verzi. |Ano |Ne |
| 7 |Windows Management Instrumentation (WMI) |V předchozích verzích softwaru, byly několik instancí webového proxy serveru selhání s výjimkou "<ManagementException> selhání načtení zprostředkovatele". Tato chyba byly připsány navrácená paměť WMI a nyní vyřešen. |Ano |Ne |
| 8 |Aktualizovat |V určitých výjimečných případech v předchozích verzích softwaru uživatel přijata "CisPowershellHcsscripterror" při skenování nebo instalace aktualizací. Tento problém vyřešen v této verzi. |Ano |Ano |
| 9 |Balíček pro podporu |V této verzi se vylepšení způsob je shromáždit a odeslat balíček pro podporu. |Ano |Ano |

## <a name="known-issues-in-update-22"></a>Známé problémy v 2.2 aktualizace
Následující tabulka obsahuje souhrn známých problémů v této verzi.

| Ne. | Funkce | Problém | Komentáře nebo alternativní řešení | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk kvora |Ve výjimečných případech Pokud většinu disků ve skříni EBOD 8600 zařízení odpojeny, což vede k žádné disk kvora, pak fondu úložiště přejde do režimu offline. Zůstane offline, i když jsou znovu připojeny disky. |Je potřeba restartovat zařízení. Pokud potíže potrvají, kontaktujte prosím Microsoft Support pro další kroky. |Ano |Ne |
| 2 |Nesprávný řadiče ID |Když se provádí nahrazení řadiče, může řadič 0 zobrazí jako řadič 1. Při nahrazení řadiče při načtení bitovou kopii z uzlu sdílené ID řadič může zobrazují původně jako ID partnera řadiče. Ve výjimečných případech může být toto chování vidět také po restartu systému. |Není třeba žádné akce uživatele. Tato situace bude automaticky vyřešen po dokončení nahrazení řadiče. |Ano |Ne |
| 3 |Účty úložiště |Pomocí služby úložiště odstranit účet úložiště se o nepodporovaný scénář. To povede k situaci, ve kterém nelze načíst data uživatele. | |Ano |Ano |
| 4 |Převzetí služeb při selhání zařízení |Více převzetí služeb při selhání kontejner svazků ze stejného zdrojového zařízení do jiné cílové zařízení není podporována. Převzetí služeb při selhání z jednoho neaktivní zařízení do více zařízení bude kontejnery svazků na prvním převzal zařízení dojít ke ztrátě dat vlastnictví. Po takové převzetí služeb při selhání, budou tyto kontejnery svazků zobrazí nebo chovat jinak při zobrazení na portálu Azure classic. | |Ano |Ne |
| 5 |Instalace |Během StorSimple adaptéru pro instalaci služby SharePoint je třeba zadat IP zařízení v pořadí pro instalaci úspěšně dokončit. | |Ano |Ne |
| 6 |Webový proxy server |Pokud vaše konfigurace webového proxy serveru má zadaný protokol HTTPS, bude mít vliv na komunikaci služby zařízení a zařízení přejde do režimu offline. Podpora balíčky se budou generovat také v procesu spotřebovávat značné množství prostředků vašeho zařízení. |Ujistěte se, že má adresu URL webového proxy serveru HTTP jako zadaný protokol. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](storsimple-configure-web-proxy.md). |Ano |Ne |
| 7 |Webový proxy server |Pokud nakonfigurujete a povolíte webový proxy server na registrované zařízení, budete muset restartujte řadič active na vašem zařízení. | |Ano |Ne |
| 8 |Cloud vysokou latencí a vysokou vstupně-výstupní úlohy |Když v zařízení StorSimple dojde kombinaci cloudu velmi vysoké latenci (pořadí sekund) a vysoké vstupně-výstupní úlohy, svazky zařízení přejděte do sníženou stavu a vstupně-výstupních operací může selhat s chybou "zařízení není připraveno". |Je nutné ručně restartovat řadiče zařízení nebo provádět převzetí služeb při selhání zařízení obnovit z této situaci. |Ano |Ne |
| 9 |Azure PowerShell |Při použití rutiny StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - nejprve 1 - čekání** vyberte první objekt tak, že můžete vytvořit nový **VolumeContainer** objekt, rutina vrátí všechny objekty. |Zabalit rutinu v závorkách následujícím způsobem: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - First 1 - čekání** |Ano |Ano |
| 10 |Migrace |Při předávání více kontejnery svazků pro migraci, je přesné jenom pro první kontejneru svazků ETA pro poslední zálohu. Kromě toho paralelní migrace se spustí po první 4 záloh v první kontejneru svazků jsou migrovány. |Doporučujeme, abyste současně migrovat jeden kontejner svazků. |Ano |Ne |
| 11 |Migrace |Po obnovení nejsou svazky přidání do zásad zálohování nebo skupinu virtuálního disku. |Musíte přidat tyto svazky do zásad zálohování k vytvoření zálohy. |Ano |Ano |
| 12 |Migrace |Po dokončení migrace řad 5000/7000 zařízení nesmí mít přístup k kontejnery migrovaná data. |Doporučujeme, abyste po dokončení a potvrdit migrace odstraňte kontejnery migrovaná data. |Ano |Ne |
| 13 |Klon a zotavení po Havárii |Zařízení StorSimple softwarem Update 1 nelze klonovat nebo provést zotavení po havárii pro zařízení se systémem před aktualizací softwaru 1. |Budete muset aktualizovat cílové zařízení Update 1 umožňuje tyto operace |Ano |Ano |
| 14 |Migrace |Zálohu konfigurace pro migraci na zařízení řady 5000 7000 může selhat, pokud existují skupiny svazek s žádné přidružené svazky. |Odstranit všechny skupiny prázdný svazek s žádné přidružené svazky a poté opakujte zálohu konfigurace. |Ano |Ne |
| 15 |Rutiny Azure PowerShell a místně vázaných svazků |Nelze vytvořit místně vázaný svazek prostřednictvím rutin prostředí Azure PowerShell. (Jakýkoli svazek, který vytvoříte pomocí prostředí Azure PowerShell bude víceúrovňová.) |Vždy použijte službu StorSimple Manager ke konfiguraci místně vázaných svazků. |Ano |Ne |
| 16 |K dispozici místa pro místně vázaných svazků |Pokud odstraníte místně vázaný svazek, místa na disku pro nové svazky nemusí být okamžitě aktualizován. Služby StorSimple Manager aktualizuje volné místo dostupné přibližně za hodinu. |Počkejte jednu hodinu, než se pokusíte vytvořit nový svazek. |Ano |Ne |
| 17 |Místně vázaných svazků |Úlohu obnovení zpřístupní dočasné snímku zálohy v katalogu zálohování, ale pouze po dobu trvání úlohy obnovení. Kromě toho zpřístupňuje skupinu virtuální disk s předponou **tmpCollection** na **zásady zálohování** stránky, ale jenom po dobu trvání úlohy obnovení. |Toto chování může dojít, pokud vaše úlohy obnovení má pouze místně vázaný svazky nebo kombinaci místně vázaný a vrstvené svazky. Pokud úloha obnovení obsahuje pouze vrstvené svazky, toto chování nedojde. Není vyžadován žádný zásah uživatele. |Ano |Ne |
| 18 |Místně vázaných svazků |Pokud zrušení úlohy obnovení a okamžitě později, se zobrazí úloha obnovení, dojde k selhání řadiče **se nezdařilo** místo **zrušeno**. Pokud úloha obnovení selže a dojde k selhání řadiče okamžitě později, se zobrazí úloha obnovení **zrušeno** místo **se nezdařilo**. |Toto chování může dojít, pokud vaše úlohy obnovení má pouze místně vázaný svazky nebo kombinaci místně vázaný a vrstvené svazky. Pokud úloha obnovení obsahuje pouze vrstvené svazky, toto chování nedojde. Není vyžadován žádný zásah uživatele. |Ano |Ne |
| 19 |Místně vázaných svazků |Pokud zrušení úlohy obnovení, nebo pokud se nezdaří obnovení a pak dojde k selhání řadiče, úlohu další obnovení se zobrazí na **úlohy** stránky. |Toto chování může dojít, pokud vaše úlohy obnovení má pouze místně vázaný svazky nebo kombinaci místně vázaný a vrstvené svazky. Pokud úloha obnovení obsahuje pouze vrstvené svazky, toto chování nedojde. Není vyžadován žádný zásah uživatele. |Ano |Ne |
| 20 |Místně vázaných svazků |Pokud pro místně vázaný svazek převodem vrstvený svazek (vytvořený a klonovaný s aktualizace 1.2 nebo starší) a vaše zařízení není dostatek místa nebo je dispozici výpadku cloudu, může být poškozena clone(s). |K tomuto problému dochází pouze pro svazky, které byly vytvořené a klonovaný s 2.1 před aktualizací softwaru. To by měl být nepravidelným scénář. | | |
| 21 |Převod svazku |Nelze aktualizovat ACRs připojené ke svazku v průběhu převodu svazku (víceúrovňová pro místně vázaný nebo naopak). Aktualizace ACRs může mít za následek poškození dat. |V případě potřeby aktualizujte ACRs před převodu svazku a neprovádějte žádné další aktualizace ACR během převodu. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Řadiče a aktualizace firmwaru v 2.2 aktualizace
Tato verze obsahuje softwarové aktualizace. Ale při aktualizaci z verze před Update 2, budete muset nainstalovat ovladač Storport, Spaceport a (v některých případech) na disku při aktualizacích firmwaru v zařízení.

Další informace o tom, jak nainstalovat ovladače, Storport, Spaceport a aktualizace firmwaru disku najdete v tématu [nainstalovat aktualizace 2.2](storsimple-install-update-21.md) zařízení StorSimple.

## <a name="virtual-device-updates-in-update-22"></a>Aktualizace virtuální zařízení v 2.2 aktualizace
Tuto aktualizaci nelze nainstalovat na virtuální zařízení. Nové virtuální zařízení bude potřeba vytvořit. 

## <a name="next-step"></a>Další krok
Zjistěte, jak [nainstalovat aktualizace 2.2](storsimple-install-update-21.md) zařízení StorSimple.

