---
title: Poznámky k verzi StorSimple 8000 Series Update 3 | Dokumentace Microsoftu
description: Popisuje nové funkce, problémy a řešení pro StorSimple 8000 Series Update 3.
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
ms.openlocfilehash: d18feba4ded3dfccb8f774112a7dc8d42b12f1d5
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488318"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Aktualizace 3 zpráva k vydání verze pro vaše zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikovat kritické otevřené problémy pro StorSimple 8000 Series Update 3. Obsahují také seznam aktualizací softwaru StorSimple jsou v ní obsažené. 

Update 3 můžete použít na jakékoli zařízení StorSimple s vydání (GA) nebo aktualizace 0.1 prostřednictvím aktualizace 2.2. Verze zařízení spojené s aktualizací Update 3 je 6.3.9600.17759.

Přečtěte si prosím informace obsažené v poznámkách k verzi, než nasadíte aktualizace v řešení StorSimple.

> [!IMPORTANT]
> * Aktualizací Update 3 obsahuje softwaru zařízení, LSI ovladače a firmware a ovladače Storport a Spaceport aktualizuje. Trvá přibližně 1.5 2 hodin k instalaci této aktualizace. 
> * Pro nové verze, nemusí zobrazit aktualizace okamžitě, protože děláme postupné zavádění aktualizací. Počkejte pár dní a pak vyhledávání aktualizací znovu jako tyto bude brzy k dispozici.
> 
> 

## <a name="whats-new-in-update-3"></a>Co je nového ve verzi Update 3
Byly provedeny následující klíčová vylepšení a opravy chyb ve verzi Update 3.

* **Automatizované změny recyklace místa** – od aktualizace 3 algoritmy recyklace místa spustit na kontroler v pohotovostním režimu systému, výsledkem je rychlejší spouštění. Další informace o portech, které jsou nezbytné pro práci s recyklace místa, najdete [StorSimple požadavky na síť](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Vylepšení výkonu** – Update 3 se zvýšil výkon pro čtení a zápis do cloudu.
* **Vylepšení související s migrací** – v této verzi několik oprav chyb a vylepšení byly dokončeny pro funkci migrace ze zařízení řady 5000/7000 zařízení řady 8000. Další informace o tom, jak využít funkci migrace naleznete v části [migrace ze zařízení řady 5000/7000 zařízení řady 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Monitorování oprav souvisejících** – v této verzi chyby související s monitorování grafy, řídicí panel služby a které jsme opravili řídicího panelu zařízení.

## <a name="issues-fixed-in-update-3"></a>Chyby opravené v Update 3
Následující tabulka obsahuje stručný problémy opravené ve verzi Update 3.    

| Ne | Funkce | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Migrace dat na straně hostitele |V předchozí verzi řešení StorSimple Cloud Appliance můru offline při migraci dat na straně hostitele. Tento problém je vyřešen v této verzi. |Ne |Ano |
| 2 |Místně připojené svazky |V předchozí verzi byly problémy související s datapath selhání místně vázaných svazků, selhání převodu svazku a chyby vstupně-výstupních operací. Byly tyto problémy způsobila kořenové a opravené v této verzi. |Ano |Ne |
| 3 |Monitorování |Došlo k více problémů souvisejících s hlášení jednotky a sledování a také grafům na řídicím panelu zařízení kde nesprávné informace se zobrazí místně vázaných svazků. Tyto problémy jsou opravené v této verzi. |Ano |Ne |
| 4 |Zápisy náročné vstupně-výstupních operací |Při použití StorSimple pro úlohy zahrnující náročné zápisy, uživatel by narazíte občasné chyby, kde se právě pracovní sady vrstveného do cloudu. Tato chyba je opravené v této verzi. |Ano |Ano |
| 5 |Backup |V některých výjimečných případech v předchozích verzích softwaru když uživatel provedl zálohu vzdálené klonování, by spuštění do cloudu chyby a operace by chybou. V této verzi dojde k vyřešení problému a operace úspěšně dokončí. |Ano |Ano |
| 6 |Zásady zálohování |V některých výjimečných případech ve starších verzích softwaru, se chyby týkající se odstraňování zásady zálohování. Tento problém je vyřešen v této verzi. |Ano |Ano |

## <a name="known-issues-in-update-3"></a>Známé problémy ve verzi Update 3
V následující tabulce je uveden seznam známých problémů v této verzi.

| Ne. | Funkce | Problém | Komentáře / řešení | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk kvora |Ve výjimečných případech Pokud jsou většinou disků ve skříni EBOD zařízení 8600 odpojeny, výsledkem žádný disk kvora, pak fondu úložiště se přejdou do režimu offline. Zůstane offline, i v případě, že jsou znovu připojeny disky. |Je potřeba restartovat zařízení. Pokud se problém nevyřeší, obraťte se o dalších krocích Microsoft Support. |Ano |Ne |
| 2 |ID nesprávné kontroleru |Při provedení nahrazení řadiče řadiče 0 může zobrazit jako řadič 1. Při nahrazení řadiče na obrázku je načtena z rovnocenný uzel ID adaptéru mohl zobrazit zpočátku jako ID partnera kontroleru. Ve výjimečných případech toto chování může také projeví po restartu systému. |Není nutná žádná akce uživatele. Tato situace vyřeší se sám po dokončení nahrazení řadiče. |Ano |Ne |
| 3 |Účty úložiště |Použití služby úložiště pro odstranění účtu úložiště se o nepodporovaný scénář. To povede k situaci, ve kterém nelze načíst data uživatele. | |Ano |Ano |
| 4 |Převzetí služeb při selhání zařízení |Více převzetí služeb při selhání služby kontejneru svazků ze stejného zdrojového zařízení do jiné cílové zařízení se nepodporuje. Převzetí služeb při selhání z jednoho neaktivní zařízení na různých zařízeních způsobí, že kontejnery svazků na první převzetí služeb zařízení při selhání dojít ke ztrátě dat vlastnictví. Tyto kontejnery svazků se po selhání, zobrazí nebo chovat jinak při prohlížení na portálu Azure classic. | |Ano |Ne |
| 5 |Instalace |Během StorSimple Adapter pro instalaci služby SharePoint je třeba zadat zařízení IP, aby se instalace úspěšně dokončit. | |Ano |Ne |
| 6 |Webový proxy server |Pokud vaše konfigurace webového proxy serveru má jako zadaný protokol HTTPS, bude mít vliv na vaši komunikaci služby zařízení a zařízení bude přejdou do režimu offline. Podpora balíčky také vygeneruje v procesu spotřebovávat značné množství prostředků vašeho zařízení. |Ujistěte se, že má adresu URL webového proxy serveru HTTP jako zadaný protokol. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](storsimple-8000-configure-web-proxy.md). |Ano |Ne |
| 7 |Webový proxy server |Pokud nakonfigurujete a povolíte webový proxy server na registrovaná zařízení, je potřeba restartovat aktivní kontroler na vašem zařízení. | |Ano |Ne |
| 8 |Cloud vysokou latencí a vysokou vstupně-výstupní úlohy |Když v zařízení StorSimple dojde ke kombinaci komponent cloudu velmi vysoké latenci (pořadí sady sekund) a vysoké vstupně-výstupní úlohy, svazky zařízení přejde do ke špatnému stavu a vstupně-výstupních operací může selhat s chybou "zařízení není připraveno". |Je potřeba ručně restartovat řadiče zařízení nebo převzetí služeb zařízení k této situace. |Ano |Ne |
| 9 |Azure PowerShell |Když použijete rutinu StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - nejprve 1 - Wait** vyberte první objekt tak, že můžete vytvořit nový **ke kontejneru svazků** objekt, rutina vrátí všechny objekty. |Zabalte rutinu v závorkách následujícím způsobem: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - nejprve 1 - Wait** |Ano |Ano |
| 10 |Migrace |Při předávání několika kontejnerů svazků pro migraci, je přesné jenom pro první kontejner svazků odhadovaným časem doručení pro poslední zálohy. Kromě toho paralelní migraci se spustí po první 4 zálohy v prvním kontejner svazků se migrují. |Doporučujeme migrovat jeden kontejner svazků najednou. |Ano |Ne |
| 11 |Migrace |Po obnovení svazky nejsou přidány do zásad zálohování nebo skupinu virtuálního disku. |Je potřeba přidat tyto svazky do zásady zálohování k vytvoření zálohy. |Ano |Ano |
| 12 |Migrace |Po dokončení migrace se nesmí řady 5000/7000 zařízení přístup k kontejnery migrovaná data. |Doporučujeme odstranit kontejnery migrovaná data po migraci je kompletní a potvrzené. |Ano |Ne |
| 13 |Klonování a zotavení po Havárii |Zařízení StorSimple s aktualizací 1 nelze klonovat nebo provést zotavení po havárii do zařízení se systémem 1 před aktualizací softwaru. |Budete muset aktualizovat cílové zařízení k aktualizaci 1 umožňuje tyto operace |Ano |Ano |
| 14 |Migrace |Konfigurace zálohování pro migrace mohou být neúspěšné zařízení řady 5000-7000 po skupin svazků s žádné přidružené svazky. |Odstranit všechny skupiny prázdný svazek s žádné přidružené svazky a pak zkuste zálohování zopakovat konfiguraci. |Ano |Ne |
| 15 |Rutiny Powershellu pro Azure a místně připojené svazky |Nelze vytvořit místně připojený svazek prostřednictvím rutin Powershellu pro Azure. (Bude vrstvené svazky, které vytvoříte pomocí prostředí Azure PowerShell.) |Služba StorSimple Manager vždy používejte ke konfiguraci místně připojené svazky. |Ano |Ne |
| 16 |Volné místo dostupné ke místně připojené svazky |Pokud odstraníte místně vázaný svazek, volné místo dostupné pro nové svazky nemusí aktualizovat okamžitě. Služba StorSimple Manager aktualizuje volné místo dostupné přibližně každou hodinu. |Počkejte hodinu, než se pokusíte vytvořit nového svazku. |Ano |Ne |
| 17 |Místně připojené svazky |Vaše úloha obnovení zpřístupňuje zálohy dočasné snímku v katalogu záloh, ale pouze po dobu trvání úlohy obnovení. Kromě toho poskytuje skupinu virtuálního disku s předponou **tmpCollection** na **zásady zálohování** stránky, ale pouze po dobu trvání úlohy obnovení. |Toto chování může dojít, pokud vaše úloha obnovení má jenom místně připojené svazky nebo kombinaci místně připnuté a vrstvené svazky. Pokud úloha obnovení obsahuje pouze vrstvené svazky, toto chování nedojde. Není vyžadován žádný zásah uživatele. |Ano |Ne |
| 18 |Místně připojené svazky |Pokud zrušíte úlohu obnovení a ihned poté, se zobrazí úloha obnovení, dojde k převzetí služeb kontroleru při **neúspěšné** místo **zrušeno**. Pokud úloha obnovení selže a dojde k selhání kontroleru ihned poté, se zobrazí úloha obnovení **zrušeno** místo **neúspěšné**. |Toto chování může dojít, pokud vaše úloha obnovení má jenom místně připojené svazky nebo kombinaci místně připnuté a vrstvené svazky. Pokud úloha obnovení obsahuje pouze vrstvené svazky, toto chování nedojde. Není vyžadován žádný zásah uživatele. |Ano |Ne |
| 19 |Místně připojené svazky |Pokud zrušíte úlohu obnovení nebo pokud selže obnovení a poté dojde k selhání řadič, další obnovení úlohy se zobrazí na **úlohy** stránky. |Toto chování může dojít, pokud vaše úloha obnovení má jenom místně připojené svazky nebo kombinaci místně připnuté a vrstvené svazky. Pokud úloha obnovení obsahuje pouze vrstvené svazky, toto chování nedojde. Není vyžadován žádný zásah uživatele. |Ano |Ne |
| 20 |Místně připojené svazky |Pokud pokusu o převod vrstveného svazku (vytvořený a klonování se aktualizace 1.2 nebo starší) místně vázaný svazek a volné místo na zařízení běží nebo cloudu výpadku, můžete clone(s) poškozený. |K tomuto problému dochází pouze u svazků, které byly vytvořené a naklonované s 2.1 před aktualizací softwaru. To by měl být občasné scénář. | | |
| 21 |Převod svazku |Nelze aktualizovat záznamů Acr připojené ke svazku probíhá převod svazku (vrstveného na místně připnutý nebo naopak). Aktualizace záznamů Acr může způsobit poškození dat. |V případě potřeby aktualizujte záznamů Acr před převodu svazku a Nedovolte, aby byly dalšího ACR aktualizace probíhá převod. | | |
| 22 |Aktualizace |Při použití aktualizací Update 3 **údržby** stránky na portálu Azure classic zobrazí následující zprávy související s aktualizací Update 2 – "řady StorSimple 8000 series Update 2 zahrnuje možnost pro společnost Microsoft k proaktivnímu shromažďování protokolů informace z vašeho zařízení, když nám umožňují odhalit možné problémy". To je zavádějící, protože naznačuje, že zařízení se aktualizuje na Update 2. Po úspěšné aktualizaci zařízení pro s aktualizací Update 3 Tato zpráva zmizí. |Toto chování bude opraven v budoucí verzi. |Ano |Ne |

## <a name="controller-and-firmware-updates-in-update-3"></a>Kontroler a aktualizace firmwaru ve verzi Update 3
Tato verze má LSI aktualizací ovladače a firmware. Další informace o tom, jak nainstalovat ovladač LSI a aktualizace firmwaru, naleznete v tématu [instalace aktualizace 3](storsimple-install-update-3.md) zařízení StorSimple.

## <a name="virtual-device-updates-in-update-3"></a>Aktualizace virtuální zařízení ve verzi Update 3
Tato aktualizace nejde použít u řešení StorSimple Cloud Appliance (označované také jako virtuální zařízení). Nové virtuální zařízení bude potřeba vytvořit. 

## <a name="next-step"></a>Další krok
Zjistěte, jak [instalace aktualizace 3](storsimple-install-update-3.md) zařízení StorSimple.

