---
title: StorSimple 8000 Series Update 2.2 poznámky k verzi | Dokumenty společnosti Microsoft
description: Popisuje nové funkce, problémy a řešení aktualizace 2.2 řady StorSimple 8000.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60531056"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>StorSimple 8000 Series Update 2.2 poznámky k verzi

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikují kritické otevřené problémy pro aktualizaci 2.2 řady StorSimple 8000. Obsahují také seznam aktualizací softwaru StorSimple zahrnutých v této verzi.

Aktualizaci 2.2 lze použít na libovolné zařízení StorSimple se spuštěnou verzí (GA) nebo aktualizací 0.1 až aktualizací 2.1. Verze zařízení přidružená k aktualizaci 2.2 je 6.3.9600.17708.

Před nasazením aktualizace v řešení StorSimple si přečtěte informace obsažené v poznámkách k verzi.

> [!IMPORTANT]
> * Aktualizace 2.2 obsahuje pouze aktualizace softwaru. Instalace této aktualizace trvá přibližně 1,5–2 hodiny. 
> * Pokud používáte aktualizaci 2.1, doporučujeme co nejdříve nainstalovat aktualizaci 2.2.
> * U nových verzí se aktualizace nemusí zobrazit okamžitě, protože provádíme postupné zavádění aktualizací. Počkejte několik dní a pak znovu vyskenujte aktualizace, protože budou brzy k dispozici.
> 
> 

## <a name="whats-new-in-update-22"></a>Co je nového v aktualizaci 2.2
V aktualizaci 2.2 byla provedena následující klíčová vylepšení.

* **Automatická optimalizace rekultivace místa** – při odstranění dat na tence zřízených svazcích je třeba uvolnit nepoužívané bloky úložiště. Tato verze vylepšila proces rekultivace prostoru z cloudu, což má za následek rychlejší dostupnost nevyužitého místa ve srovnání s předchozími verzemi.
* **Vylepšení výkonu snímku** – aktualizace 2.2 zlepšila čas pro zpracování snímku cloudu v určitých scénářích, kde se používají velké svazky a je minimální až žádné změny dat. Scénář, který by měl prospěch z tohoto vylepšení by archivní svazky.
* **Posílení zabezpečení shromažďování balíčků podpory** – došlo ke zlepšení ve způsobu, jakým je balíček podpory shromážděn a nahrán v této verzi. 
* **Vylepšení spolehlivosti aktualizací** – tato verze obsahuje opravy chyb, které vedou ke zlepšení spolehlivosti aktualizace.

## <a name="issues-fixed-in-update-22"></a>Problémy opravené v aktualizaci 2.2
Následující tabulky obsahují souhrn problémů, které byly opraveny v aktualizacích 2.2 a 2.1.    

| Ne | Funkce | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Výkon hostitele |V dřívější verzi byly zjištěny problémy s výkonem na straně hostitele při vytváření místně vázaný svazek a při převodu vrstveného svazku na místně vázaný svazek. Tyto problémy jsou opraveny v této verzi, což vede ke zlepšení výkonu hostitele během postupů vytváření a převodu svazku. |Ano |Ne |
| 2 |Místně připojené svazky |Ve výjimečných případech by systém selhání při vytváření místně vázaný svazek. Tato chyba byla opravena v této verzi. |Ano |Ne |
| 3 |Tiering |Při přechodu metadat pro storsimple cloudové spotřebiče (8010 a 8020) na přechod na cloud došlo k sporadickým chybám. Tento problém je vyřešen v této verzi. |Ne |Ano |
| 4 |Vytvoření snímku |Byly problémy související s vytvářením přírůstkových snímků ve scénářích s velkými svazky a minimální minou dat. Tyto problémy jsou opraveny v této verzi. |Ano |Ano |
| 5 |Ověřování openstacku |Při použití Openstack jako poskytovatele cloudových služeb by uživatel narazit na zřídka chyby související s ověřováním, kde Analyzátor JSON za následek selhání. Tato chyba je opravena v této verzi. |Ano |Ne |
| 6 |Kopie na straně hostitele |V dřívějších verzích softwaru byla při kopírování dat z jednoho svazku do jiného pozorována vzácná chyba související s časováním ODX. To by mělo za následek převzetí služeb při selhání řadiče a systém by mohl potenciálně přejít do režimu obnovení. Tato chyba je opravena v této verzi. |Ano |Ne |
| 7 |Služba WMI (WMI) |V předchozích verzích softwaru bylo několik instancí selhání webového proxy serveru s výjimkou "ManagementException\<> Zprostředkovatel selhání zatížení". Tato chyba byla připisována nevracení paměti úložiště WMI a je nyní opravena. |Ano |Ne |
| 8 |Aktualizace |V některých výjimečných případech v předchozích verzích softwaru uživatel obdržel "CisPowershellHcsscripterror" při pokusu o skenování nebo instalaci aktualizací. Tento problém je vyřešen v této verzi. |Ano |Ano |
| 9 |Balíček podpory |V této verzi došlo ke zlepšení způsobu, jakým je balíček podpory shromážděn a nahrán. |Ano |Ano |

## <a name="known-issues-in-update-22"></a>Známé problémy v aktualizaci 2.2
Následující tabulka obsahuje souhrn známých problémů v této verzi.

| Ne. | Funkce | Problém | Komentáře / řešení | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Diskové kvorum |Ve výjimečných případech pokud většina disků ve skříni EBOD zařízení 8600 jsou odpojeny, což vede k žádné kvorum disku, pak fond úložiště přejde do režimu offline. Zůstane v offline i v případě, že jsou disky znovu připojeny. |Budete muset restartovat zařízení. Pokud problém přetrvává, obraťte se na podporu společnosti Microsoft pro další kroky. |Ano |Ne |
| 2 |Nesprávné ID řadiče |Při provedení výměny řadiče se může řadič 0 zobrazit jako řadič 1. Během nahrazení řadiče, když je bitová kopie načtena z uzlu peer, ID řadiče se může zpočátku zobrazit jako ID peer controller. Ve výjimečných případech toto chování může být také vidět po restartování systému. |Není vyžadována žádná akce uživatele. Tato situace se vyřeší sama po dokončení nahrazení řadiče. |Ano |Ne |
| 3 |Účty úložiště |Použití služby Úložiště k odstranění účtu úložiště je nepodporovaný scénář. To povede k situaci, ve které nelze načíst uživatelská data. | |Ano |Ano |
| 4 |Převzetí služeb při selhání zařízení |Více převzetí služeb při selhání kontejneru svazku ze stejného zdrojového zařízení do různých cílových zařízení není podporováno. Převzetí služeb při selhání z jednoho mrtvého zařízení na více zařízení způsobí, že kontejnery svazků na prvním převzetí služeb při selhání zařízení ztratí vlastnictví dat. Po takovém převzetí služeb při selhání se tyto kontejnery svazků zobrazí nebo se budou chovat jinak, když je zobrazíte na klasickém portálu Azure. | |Ano |Ne |
| 5 |Instalace |Během instalace aplikace StorSimple Adapter pro službu SharePoint je třeba zadat ip adresu zařízení, aby byla instalace úspěšně dokončena. | |Ano |Ne |
| 6 |Webový proxy server |Pokud je konfigurace webového proxy serveru jako zadaný protokol protokolem https, bude ovlivněna komunikace mezi zařízeními a službami a zařízení přejde do offline. Balíčky podpory budou také generovány v procesu, spotřebovávající významné prostředky na vašem zařízení. |Ujistěte se, že adresa URL webového proxy serveru má jako zadaný protokol protokol HTTP. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](storsimple-configure-web-proxy.md). |Ano |Ne |
| 7 |Webový proxy server |Pokud nakonfigurujete a povolíte webový proxy server na registrovaném zařízení, budete muset restartovat aktivní řadič v zařízení. | |Ano |Ne |
| 8 |Vysoká latence cloudu a vysoké vstupně-výstupní úlohy |Když vaše zařízení StorSimple narazí na kombinaci velmi vysoké latence cloudu (pořadí sekund) a vysoké vstupně-výstupní úlohy, svazky zařízení přejdou do zhoršeného stavu a vstupně-výstupních služeb může selhat s chybou "zařízení není připraveno". |Budete muset ručně restartovat řadiče zařízení nebo provést převzetí služeb při selhání zařízení k obnovení z této situace. |Ano |Ne |
| 9 |Azure PowerShell |Při použití Rutina StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** vybrat první objekt, takže můžete vytvořit nový **Objekt VolumeContainer,** rutina vrátí všechny objekty. |Zalomit rutinu do závorek takto: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Ano |Ano |
| 10 |Migrace |Při předání více kontejnerů svazku pro migraci, ETA pro nejnovější zálohování je přesné pouze pro první kontejner svazku. Paralelní migrace navíc začne po migraci prvních 4 záloh v prvním kontejneru svazku. |Doporučujeme migrovat jeden kontejner svazku najednou. |Ano |Ne |
| 11 |Migrace |Po obnovení nejsou svazky přidány do zásady zálohování nebo skupiny virtuálních disků. |Chcete-li vytvořit zálohy, budete muset tyto svazky přidat do zásad zálohování. |Ano |Ano |
| 12 |Migrace |Po dokončení migrace zařízení řady 5000/7000 nesmí přistupovat k migrované datové kontejnery. |Doporučujeme odstranit migrované datové kontejnery po dokončení migrace a potvrzené. |Ano |Ne |
| 13 |Klonování a dr. |Zařízení StorSimple se spuštěnou aktualizací 1 nemůže klonovat nebo provádět zotavení po havárii zařízení se systémem předaktualizací 1. |Chcete-li povolit tyto operace, budete muset aktualizovat cílové zařízení na aktualizaci 1. |Ano |Ano |
| 14 |Migrace |Zálohování konfigurace pro migraci může selhat na zařízení řady 5000-7000, pokud existují skupiny svazků bez přidružených svazků. |Odstraňte všechny prázdné skupiny svazků bez přidružených svazků a opakujte zálohu konfigurace. |Ano |Ne |
| 15 |Rutiny prostředí Azure PowerShell a místně vázaných svazků |Nelze vytvořit místně vázaný svazek prostřednictvím rutin Azure PowerShell. (Všechny svazky, které vytvoříte prostřednictvím Azure PowerShellu, se budou vrstvené.) |Ke konfiguraci místně vázaných svazků vždy používejte službu StorSimple Manager. |Ano |Ne |
| 16 |Volné místo pro místně připnuté svazky |Pokud odstraníte místně vázaný svazek, místo, které je k dispozici pro nové svazky, nemusí být okamžitě aktualizováno. Služba StorSimple Manager aktualizuje místní prostor, který je k dispozici přibližně každou hodinu. |Počkejte hodinu, než se pokusíte vytvořit nový svazek. |Ano |Ne |
| 17 |Místně připojené svazky |Úloha obnovení zpřístupňuje dočasné zálohy snímek v katalogu zálohování, ale pouze po dobu trvání úlohy obnovení. Navíc zpřístupňuje skupinu virtuálních disků s předponou **tmpCollection** na stránce **zásady zálohování,** ale pouze po dobu trvání úlohy obnovení. |K tomuto chování může dojít, pokud úloha obnovení má pouze místně vázaných svazků nebo kombinaci místně vázaných a vrstvených svazků. Pokud úloha obnovení obsahuje pouze vrstvené svazky, toto chování nedojde. Není vyžadován žádný zásah uživatele. |Ano |Ne |
| 18 |Místně připojené svazky |Pokud zrušíte úlohu obnovení a převzetí služeb při selhání řadiče dojde bezprostředně poté, úloha obnovení se zobrazí **nezdařilo** místo **Zrušeno**. Pokud úloha obnovení selže a převzetí služeb při selhání řadiče dojde bezprostředně poté, úloha obnovení se zobrazí **Zrušeno** místo **Nezdařilo**. |K tomuto chování může dojít, pokud úloha obnovení má pouze místně vázaných svazků nebo kombinaci místně vázaných a vrstvených svazků. Pokud úloha obnovení obsahuje pouze vrstvené svazky, toto chování nedojde. Není vyžadován žádný zásah uživatele. |Ano |Ne |
| 19 |Místně připojené svazky |Pokud zrušíte úlohu obnovení nebo pokud se obnovení nezdaří a dojde k převzetí služeb při selhání řadiče, zobrazí se na stránce Úlohy další **úloha** obnovení. |K tomuto chování může dojít, pokud úloha obnovení má pouze místně vázaných svazků nebo kombinaci místně vázaných a vrstvených svazků. Pokud úloha obnovení obsahuje pouze vrstvené svazky, toto chování nedojde. Není vyžadován žádný zásah uživatele. |Ano |Ne |
| 20 |Místně připojené svazky |Pokud se pokusíte převést vrstvený svazek (vytvořený a klonovaný pomocí aktualizace 1.2 nebo starší) na místně vázaný svazek a zařízení nemá místo nebo dojde k výpadku cloudu, pak může být klon (y) poškozen. |K tomuto problému dochází pouze u svazků, které byly vytvořeny a klonovány pomocí softwaru pre-Update 2.1. To by mělo být zřídka scénář. | | |
| 21 |Převod objemu |Neaktualizujte akreditované kartotéky připojené ke svazku v průběhu převodu svazku (vrstvené na místně připnuté nebo naopak). Aktualizace akrsů může vést k poškození dat. |V případě potřeby aktualizujte acr před převodem objemu a neprovázte žádné další aktualizace ACR, zatímco probíhá převod. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Aktualizace řadiče a firmwaru v aktualizaci 2.2
Tato verze obsahuje aktualizace pouze pro software. Pokud však aktualizujete verzi před aktualizací 2, budete muset do zařízení nainstalovat aktualizace firmwaru ovladače, storportu, kosmodromu a (v některých případech).

Další informace o instalaci aktualizace ovladače, storportu, kosmodromu a firmwaru disku naleznete v [tématu instalace aktualizace 2.2](storsimple-install-update-21.md) na zařízení StorSimple.

## <a name="virtual-device-updates-in-update-22"></a>Aktualizace virtuálních zařízení v aktualizaci 2.2
Tuto aktualizaci nelze použít na virtuální zařízení. Bude třeba vytvořit nová virtuální zařízení. 

## <a name="next-step"></a>Další krok
Přečtěte si, jak [nainstalovat aktualizaci 2.2](storsimple-install-update-21.md) do zařízení StorSimple.

