---
title: Poznámky k verzi StorSimple 8000 Series Update 2.2 | Dokumentace Microsoftu
description: Popisuje nové funkce, problémy a řešení pro StorSimple 8000 Series Update 2.2.
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
ms.openlocfilehash: 12d11cddf077d4d07732490255d44e89ddaf3217
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531056"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>Poznámky k verzi StorSimple 8000 Series Update 2.2

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikovat kritické otevřené problémy pro StorSimple 8000 Series Update 2.2. Obsahují také seznam aktualizací softwaru StorSimple jsou v ní obsažené.

Aktualizace 2.2 lze použít na jakékoli zařízení StorSimple s vydání (GA) nebo aktualizace 0.1 prostřednictvím aktualizace 2.1. Verze zařízení spojené s aktualizace 2.2 je 6.3.9600.17708.

Přečtěte si prosím informace obsažené v poznámkách k verzi, než nasadíte aktualizace v řešení StorSimple.

> [!IMPORTANT]
> * Aktualizace 2.2 má pouze aktualizace softwaru. Trvá přibližně 1.5 2 hodin k instalaci této aktualizace. 
> * Pokud používáte aktualizace 2.1, doporučujeme nejdříve použít aktualizace 2.2.
> * Pro nové verze, nemusí zobrazit aktualizace okamžitě, protože děláme postupné zavádění aktualizací. Počkejte pár dní a pak vyhledávání aktualizací znovu jako tyto bude brzy k dispozici.
> 
> 

## <a name="whats-new-in-update-22"></a>Co je nového v aktualizaci 2.2
Byly provedeny následující klíčová vylepšení v aktualizace 2.2.

* **Automatické optimalizace recyklace místa** – když se odstraní data na dynamicky zřizované svazky, třeba bloky nevyužité úložiště mají být získány zpět. Tato verze přináší Vylepšená procesu recyklace místa z cloudu, což vede k nevyužité místo stále k dispozici rychleji v porovnání s předchozími verzemi.
* **Pořízení snímku vylepšení výkonu** – aktualizace 2.2 zlepšil čas ke zpracování cloudových snímků v určitých scénářích, kde je velké svazky nich se právě používá a je minimální denní četnost změn dat. Tento scénář je výhodná toto vylepšení by archivní svazky.
* **Posílení zabezpečení balíčku pro podporu shromažďování** – došlo k vylepšení tak, jak shromážděné a nahráli v této verzi balíčku pro podporu. 
* **Aktualizovat vylepšení spolehlivosti** – tato verze obsahuje opravy chyb, jejichž výsledkem je vylepšení spolehlivosti aktualizace.

## <a name="issues-fixed-in-update-22"></a>Chyby opravené v aktualizace 2.2
Následující tabulka obsahuje souhrn problémů, které byly opraveny v aktualizace 2.2 a 2.1.    

| Ne | Funkce | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Výkon hostitele |V předchozí verzi nezjistily problémy s výkonem na straně hostitele během vytváření místně vázaný svazek a během převodu vrstvený svazek na místně vázaný svazek. Tyto problémy jsou opravené v této verzi, což by vedlo k zlepšení výkonu hostitele během postupy vytváření a převod svazku. |Ano |Ne |
| 2 |Místně připojené svazky |Ve výjimečných případech systém by selhání při vytváření místně vázaných svazků. Tato chyba byla opravena v této verzi. |Ano |Ne |
| 3 |ovládání datových vrstev |Došlo k občasnému selhání při metadata pro Cloudová zařízení StorSimple (8010 a 8020) Vrstvená do cloudu. Tento problém je vyřešen v této verzi. |Ne |Ano |
| 4 |Vytvoření snímku |Týkající se vytváření přírůstkových snímků ve scénářích s velkými objemy a minimální denní četnost změn dat došlo k problémům. Tyto problémy jsou opravené v této verzi. |Ano |Ano |
| 5 |Openstack ověřování |Při použití Openstack jako poskytovatele cloudových služeb, uživatel by narazíte občasné chyby související s ověřováním, kde analyzátor JSON způsobila selhání. Tato chyba je opravené v této verzi. |Ano |Ne |
| 6 |Kopie na straně hostitele |V dřívějších verzích softwaru občasné chyby související s ODX časování viděla při kopírování dat z jednoho svazku na jiný svazek. To způsobí selhání kontroleru a systém může potenciálně přejít do režimu obnovení. Tato chyba je opravené v této verzi. |Ano |Ne |
| 7 |Windows Management Instrumentation (WMI) |V předchozích verzích softwaru, došlo k chybě proxy webu s výjimkou několik instancí "\<managementexception – > Chyba zprostředkovatele zatížení". Tato chyba byla přiřadit nevracení paměti WMI a nyní je opraveno. |Ano |Ne |
| 8 |Aktualizace |V některých výjimečných případech v předchozích verzích softwaru uživatel přijal "CisPowershellHcsscripterror" při pokusu o skenování nebo instalace aktualizací. Tento problém je vyřešen v této verzi. |Ano |Ano |
| 9 |Balíček pro podporu |V této verzi byly vylepšení způsobu, jakým shromážděné a nahrát balíček pro podporu. |Ano |Ano |

## <a name="known-issues-in-update-22"></a>Známé problémy v aktualizace 2.2
V následující tabulce je uveden seznam známých problémů v této verzi.

| Ne. | Funkce | Problém | Komentáře / řešení | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk kvora |Ve výjimečných případech Pokud jsou většinou disků ve skříni EBOD zařízení 8600 odpojeny, výsledkem žádný disk kvora, pak fondu úložiště se přejdou do režimu offline. Zůstane offline, i v případě, že jsou znovu připojeny disky. |Je potřeba restartovat zařízení. Pokud se problém nevyřeší, obraťte se o dalších krocích Microsoft Support. |Ano |Ne |
| 2 |ID nesprávné kontroleru |Při provedení nahrazení řadiče řadiče 0 může zobrazit jako řadič 1. Při nahrazení řadiče na obrázku je načtena z rovnocenný uzel ID adaptéru mohl zobrazit zpočátku jako ID partnera kontroleru. Ve výjimečných případech toto chování může také projeví po restartu systému. |Není nutná žádná akce uživatele. Tato situace vyřeší se sám po dokončení nahrazení řadiče. |Ano |Ne |
| 3 |Účty úložiště |Použití služby úložiště pro odstranění účtu úložiště se o nepodporovaný scénář. To povede k situaci, ve kterém nelze načíst data uživatele. | |Ano |Ano |
| 4 |Převzetí služeb při selhání zařízení |Více převzetí služeb při selhání služby kontejneru svazků ze stejného zdrojového zařízení do jiné cílové zařízení se nepodporuje. Převzetí služeb při selhání z jednoho neaktivní zařízení na různých zařízeních způsobí, že kontejnery svazků na první převzetí služeb zařízení při selhání dojít ke ztrátě dat vlastnictví. Tyto kontejnery svazků se po selhání, zobrazí nebo chovat jinak při prohlížení na portálu Azure classic. | |Ano |Ne |
| 5 |Instalace |Během StorSimple Adapter pro instalaci služby SharePoint je třeba zadat zařízení IP, aby se instalace úspěšně dokončit. | |Ano |Ne |
| 6 |Webový proxy server |Pokud vaše konfigurace webového proxy serveru má jako zadaný protokol HTTPS, bude mít vliv na vaši komunikaci služby zařízení a zařízení bude přejdou do režimu offline. Podpora balíčky také vygeneruje v procesu spotřebovávat značné množství prostředků vašeho zařízení. |Ujistěte se, že má adresu URL webového proxy serveru HTTP jako zadaný protokol. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](storsimple-configure-web-proxy.md). |Ano |Ne |
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

## <a name="controller-and-firmware-updates-in-update-22"></a>Kontroler a aktualizace firmwaru v aktualizace 2.2
Tato verze je výhradně softwarová aktualizace. Ale při aktualizaci z verze před Update 2, budete muset nainstalovat ovladač Storport, Spaceport a (v některých případech) disku aktualizace firmwaru na vašem zařízení.

Další informace o tom, jak nainstalovat ovladač, Storport, Spaceport a aktualizace firmwaru disku najdete v tématu [instalace aktualizace 2.2](storsimple-install-update-21.md) zařízení StorSimple.

## <a name="virtual-device-updates-in-update-22"></a>Aktualizace virtuálního zařízení v aktualizace 2.2
Tato aktualizace nejde použít u virtuálního zařízení. Nové virtuální zařízení bude potřeba vytvořit. 

## <a name="next-step"></a>Další krok
Zjistěte, jak [instalace aktualizace 2.2](storsimple-install-update-21.md) zařízení StorSimple.

