---
title: Poznámky k verzi zařízení StorSimple 8000 řady Update 3 | Microsoft Docs
description: Popisuje nové funkce, problémy a řešení pro zařízení StorSimple 8000 řady Update 3.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9308602b77903e553e26d6f0586d963a4c58230d
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057821"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Aktualizace 3 poznámky k verzi pro vaše zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikovat kritická otevřené problémy pro StorSimple 8000 řady Update 3. Také obsahují seznam aktualizací softwaru zařízení StorSimple, zahrnuté v této verzi. 

Update 3 můžete použít pro všechna zařízení StorSimple, s verzí (GA) nebo Update 0.1 prostřednictvím aktualizace 2.2. Zařízení verzi spojenou s aktualizací 3 je 6.3.9600.17759.

Přečtěte si informace obsažené v poznámkách k verzi před nasazením aktualizace v řešení StorSimple.

> [!IMPORTANT]
> * Aktualizace 3 má software zařízení, LSI ovladače a firmware a ovladače Storport a Spaceport aktualizace. Jak dlouho trvá přibližně 1.5 – 2 hodiny nainstalovat tuto aktualizaci. 
> * Pro nové verze, nemusí zobrazit aktualizace okamžitě, protože provedeme postupné zavádění aktualizací. Počkejte několik dní, a pak vyhledejte aktualizace znovu jako tyto brzy bude dostupná.
> 
> 

## <a name="whats-new-in-update-3"></a>Co je nového ve verzi Update 3
Byly provedeny následující klíčových vylepšení a opravy chyb ve verzi Update 3.

* **Automatizované změny recyklace místa** – od aktualizace 3, algoritmy recyklace místa se spouští na pohotovostní řadiči výsledkem je rychlejší spouštění systému. Další informace o portech, které jsou potřebné pro práci s recyklace místa, najdete v části [StorSimple sítě požadavky](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Vylepšení výkonu** – Update 3 obsahuje zvýšení výkonu pro čtení a zápis do cloudu.
* **Vylepšení související s migrací** – v této verzi několik oprav chyb a vylepšení měla provést pro funkci migrace z řad 5000/7000 zařízení do 8000 řady zařízení. Další informace o tom, jak využít funkci migrace, přejděte na [migrace z řad 5000/7000 zařízení na zařízení řady 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Monitorování související opravy** – v této verzi chyby související s monitorování grafy, řídicí panel služby, a zařízení řídicí panel jsme vyřešili.

## <a name="issues-fixed-in-update-3"></a>Chyby v Update 3
Následující tabulka obsahuje souhrn problémy, které jsme vyřešili ve verzi Update 3.    

| Ne | Funkce | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Migrace dat na straně hostitele |Ve starší verzi zařízení StorSimple cloudu byl přechod do stavu offline při migraci dat na straně hostitele. Tento problém vyřešen v této verzi. |Ne |Ano |
| 2 |Místně vázaných svazků |V předchozí verzi byly problémy související s chyb, selhání převodu svazku a selhání datapath místně vázaných svazků vstupně-výstupních operací. Tyto problémy se kvůli kořenové a pevné v této verzi. |Ano |Ne |
| 3 |Monitorování |Došlo k několika problémy související s monitorování a také zařízení řídicího panelu grafů, kde se zobrazí nesprávné informace pro místně vázaných svazků a vytváření sestav jednotky. V této verzi opravě těchto problémů. |Ano |Ne |
| 4 |Zápisy náročnými vstupně-výstupních operací |Při použití StorSimple pro úlohy zahrnující velkou zápisy, může uživatel by spustit do nepravidelným chyb, kde se právě pracovní sady vrstvené do cloudu. Tato chyba je opravena v této verzi. |Ano |Ano |
| 5 |Backup |V určitých výjimečných případech v předchozích verzích softwaru když uživatel provedl zálohování vzdálené klon, by spustit do cloudu chyby a operaci by chybu. V této verzi je problém vyřešen a po úspěšném dokončení operace. |Ano |Ano |
| 6 |Zásady zálohování |V určitých výjimečných případech ve starších verzích softwaru, se chyby související s odstranění zásady zálohování. Tento problém vyřešen v této verzi. |Ano |Ano |

## <a name="known-issues-in-update-3"></a>Známé problémy ve verzi Update 3
Následující tabulka obsahuje souhrn známých problémů v této verzi.

| Ne. | Funkce | Problém | Komentáře nebo alternativní řešení | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk kvora |Ve výjimečných případech Pokud většinu disků ve skříni EBOD 8600 zařízení odpojeny, což vede k žádné disk kvora, pak fondu úložiště přejde do režimu offline. Zůstane offline, i když jsou znovu připojeny disky. |Je potřeba restartovat zařízení. Pokud potíže potrvají, kontaktujte prosím Microsoft Support pro další kroky. |Ano |Ne |
| 2 |Nesprávný řadiče ID |Když se provádí nahrazení řadiče, může řadič 0 zobrazí jako řadič 1. Při nahrazení řadiče při načtení bitovou kopii z uzlu sdílené ID řadič může zobrazují původně jako ID partnera řadiče. Ve výjimečných případech může být toto chování vidět také po restartu systému. |Není třeba žádné akce uživatele. Tato situace bude automaticky vyřešen po dokončení nahrazení řadiče. |Ano |Ne |
| 3 |Účty úložiště |Pomocí služby úložiště odstranit účet úložiště se o nepodporovaný scénář. To povede k situaci, ve kterém nelze načíst data uživatele. | |Ano |Ano |
| 4 |Převzetí služeb při selhání zařízení |Více převzetí služeb při selhání kontejner svazků ze stejného zdrojového zařízení do jiné cílové zařízení není podporována. Převzetí služeb při selhání z jednoho neaktivní zařízení do více zařízení bude kontejnery svazků na prvním převzal zařízení dojít ke ztrátě dat vlastnictví. Po takové převzetí služeb při selhání, budou tyto kontejnery svazků zobrazí nebo chovat jinak při zobrazení na portálu Azure classic. | |Ano |Ne |
| 5 |Instalace |Během StorSimple adaptéru pro instalaci služby SharePoint je třeba zadat IP zařízení v pořadí pro instalaci úspěšně dokončit. | |Ano |Ne |
| 6 |Webový proxy server |Pokud vaše konfigurace webového proxy serveru má zadaný protokol HTTPS, bude mít vliv na komunikaci služby zařízení a zařízení přejde do režimu offline. Podpora balíčky se budou generovat také v procesu spotřebovávat značné množství prostředků vašeho zařízení. |Ujistěte se, že má adresu URL webového proxy serveru HTTP jako zadaný protokol. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](storsimple-8000-configure-web-proxy.md). |Ano |Ne |
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
| 22 |Aktualizace |Při použití aktualizace 3 **údržby** na portálu Azure classic zobrazí následující zprávu související s Update 2 – "řady StorSimple 8000 Update 2 obsahuje možnosti pro Microsoft proaktivně shromažďovat informace o protokolu z vašeho zařízení, když je zjištěno potenciální problémy". To je zavádějící, protože naznačuje, že zařízení je aktualizováno na Update 2. Jakmile zařízení succeesfully aktualizovat, aby Update 3, tato zpráva zmizí. |Toto chování bude opraven v budoucí verzi. |Ano |Ne |

## <a name="controller-and-firmware-updates-in-update-3"></a>Řadiče a aktualizace firmwaru. ve verzi Update 3
Tato verze obsahuje LSI ovladače a firmware aktualizace. Další informace o tom, jak nainstalovat LSI ovladače a firmware aktualizace najdete v tématu [instalaci aktualizace Update 3](storsimple-install-update-3.md) zařízení StorSimple.

## <a name="virtual-device-updates-in-update-3"></a>Aktualizace virtuální zařízení ve verzi Update 3
Tuto aktualizaci nelze nainstalovat do cloudu zařízení StorSimple (také označované jako virtuální zařízení). Nové virtuální zařízení bude potřeba vytvořit. 

## <a name="next-step"></a>Další krok
Zjistěte, jak [instalaci aktualizace Update 3](storsimple-install-update-3.md) zařízení StorSimple.

