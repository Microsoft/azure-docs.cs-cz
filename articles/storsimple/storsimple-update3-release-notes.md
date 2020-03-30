---
title: Poznámky k verzi verze StorSimple 8000 Series Update 3
description: Popisuje nové funkce, problémy a řešení pro aktualizaci StorSimple 8000 Series Update 3.
author: alkohli
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b61caecd67881eb08c82ea0c26522c63c3e8396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254609"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Aktualizace 3 poznámky k verzi pro zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikují kritické otevřené problémy pro aktualizaci StorSimple 8000 Series Update 3. Obsahují také seznam aktualizací softwaru StorSimple zahrnutých v této verzi. 

Aktualizaci 3 lze použít na libovolné zařízení StorSimple se spuštěním verze (GA) nebo aktualizace 0.1 až aktualizace 2.2. Verze zařízení přidružená k aktualizaci 3 je 6.3.9600.17759.

Před nasazením aktualizace v řešení StorSimple si přečtěte informace obsažené v poznámkách k verzi.

> [!IMPORTANT]
> * Aktualizace 3 obsahuje software zařízení, ovladač LSI a firmware a aktualizace Storport a Spaceport. Instalace této aktualizace trvá přibližně 1,5–2 hodiny. 
> * U nových verzí se aktualizace nemusí zobrazit okamžitě, protože provádíme postupné zavádění aktualizací. Počkejte několik dní a pak znovu vyskenujte aktualizace, protože budou brzy k dispozici.
> 
> 

## <a name="whats-new-in-update-3"></a>Co je nového v aktualizaci 3
V aktualizaci 3 byla provedena následující klíčová vylepšení a opravy chyb.

* **Automatické změny rekultivace místa** – Spuštění aktualizace 3, algoritmy rekultivace prostoru běží na pohotovostním řadiči systému, což vede k rychlejšímu spuštění. Další informace o portech, které jsou nutné pro práci s rekultivací místa, naleznete [v požadavcích na síť StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Vylepšení výkonu** – aktualizace 3 má lepší výkon pro čtení a zápis do cloudu.
* **Vylepšení související s migrací** – V této verzi bylo provedeno několik oprav a vylepšení chyb pro funkci Migrace z zařízení řady 5000/7000 na zařízení řady 8000. Další informace o použití funkce migrace naleznete v části [Migrace ze zařízení řady 5000/7000 na zařízení řady 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Monitorování souvisejících oprav** – v této verzi byly opraveny chyby související s grafy monitorování, řídicím panelem služby a řídicím panelem zařízení.

## <a name="issues-fixed-in-update-3"></a>Problémy opravené v aktualizaci 3
Následující tabulky obsahují souhrn problémů, které byly opraveny v aktualizaci 3.    

| Ne | Funkce | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Migrace dat na straně hostitele |V dřívější verzi StorSimple Cloud Appliance byl přechod do offline během migrace dat na straně hostitele. Tento problém je vyřešen v této verzi. |Ne |Ano |
| 2 |Místně připojené svazky |V předchozí verzi došlo k problémům souvisejícím s chybami vstupně-videa, selháním převodu svazku a selháním datové cesty pro místně vázané svazky. Tyto problémy byly root-způsobil a opraveny v této verzi. |Ano |Ne |
| 3 |Monitorování |Došlo k několika problémům souvisejícím s jednotkami pro vykazování a monitorováním, stejně jako grafy řídicího panelu zařízení, kde byly zobrazeny nesprávné informace pro místně vázané svazky. Tyto problémy jsou opraveny v této verzi. |Ano |Ne |
| 4 |Těžké zápisy I / O |Při použití StorSimple pro úlohy zahrnující těžké zápisy, uživatel by spustit do zřídka chyby, kde pracovní sada byla vrstvené do cloudu. Tato chyba je opravena v této verzi. |Ano |Ano |
| 5 |Zálohování |V některých výjimečných případech, v předchozích verzích softwaru, když uživatel vzal zálohu vzdáleného klonu, by se dostali do chyb cloudu a operace by došlo k chybě. V této verzi je problém vyřešen a operace úspěšně dokončena. |Ano |Ano |
| 6 |Zásady zálohování |V některých výjimečných případech v dřívějších verzích softwaru došlo k chybě související s odstraněním zásad zálohování. Tento problém je vyřešen v této verzi. |Ano |Ano |

## <a name="known-issues-in-update-3"></a>Známé problémy v aktualizaci 3
Následující tabulka obsahuje souhrn známých problémů v této verzi.

| Ne. | Funkce | Problém | Komentáře / řešení | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Diskové kvorum |Ve výjimečných případech pokud většina disků ve skříni EBOD zařízení 8600 jsou odpojeny, což vede k žádné kvorum disku, pak fond úložiště přejde do režimu offline. Zůstane v offline i v případě, že jsou disky znovu připojeny. |Budete muset restartovat zařízení. Pokud problém přetrvává, obraťte se na podporu společnosti Microsoft pro další kroky. |Ano |Ne |
| 2 |Nesprávné ID řadiče |Při provedení výměny řadiče se může řadič 0 zobrazit jako řadič 1. Během nahrazení řadiče, když je bitová kopie načtena z uzlu peer, ID řadiče se může zpočátku zobrazit jako ID peer controller. Ve výjimečných případech toto chování může být také vidět po restartování systému. |Není vyžadována žádná akce uživatele. Tato situace se vyřeší sama po dokončení nahrazení řadiče. |Ano |Ne |
| 3 |Účty úložiště |Použití služby Úložiště k odstranění účtu úložiště je nepodporovaný scénář. To povede k situaci, ve které nelze načíst uživatelská data. | |Ano |Ano |
| 4 |Převzetí služeb při selhání zařízení |Více převzetí služeb při selhání kontejneru svazku ze stejného zdrojového zařízení do různých cílových zařízení není podporováno. Převzetí služeb při selhání z jednoho mrtvého zařízení na více zařízení způsobí, že kontejnery svazků na prvním převzetí služeb při selhání zařízení ztratí vlastnictví dat. Po takovém převzetí služeb při selhání se tyto kontejnery svazků zobrazí nebo se budou chovat jinak, když je zobrazíte na klasickém portálu Azure. | |Ano |Ne |
| 5 |Instalace |Během instalace aplikace StorSimple Adapter pro službu SharePoint je třeba zadat ip adresu zařízení, aby byla instalace úspěšně dokončena. | |Ano |Ne |
| 6 |Webový proxy server |Pokud je konfigurace webového proxy serveru jako zadaný protokol protokolem https, bude ovlivněna komunikace mezi zařízeními a službami a zařízení přejde do offline. Balíčky podpory budou také generovány v procesu, spotřebovávající významné prostředky na vašem zařízení. |Ujistěte se, že adresa URL webového proxy serveru má jako zadaný protokol protokol HTTP. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](storsimple-8000-configure-web-proxy.md). |Ano |Ne |
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
| 22 |Aktualizace |Při použití aktualizace 3 se na stránce **Údržba** na klasickém portálu Azure zobrazí následující zpráva týkající se aktualizace 2 – "Aktualizace řady 2 StorSimple 8000 zahrnuje možnost společnosti Microsoft proaktivně shromažďovat informace protokolu z vašeho zařízení, když zjistíme potenciální problémy". To je zavádějící, protože znamená, že zařízení je aktualizováno na aktualizaci 2. Po úspěšné aktualizaci zařízení na aktualizaci 3 tato zpráva zmizí. |Toto chování bude opraveno v budoucí verzi. |Ano |Ne |

## <a name="controller-and-firmware-updates-in-update-3"></a>Aktualizace řadiče a firmwaru v aktualizaci 3
Tato verze obsahuje aktualizace ovladačů a firmwaru LSI. Další informace o instalaci aktualizací ovladače LSI a firmwaru naleznete v [tématu instalace aktualizace 3](storsimple-install-update-3.md) do zařízení StorSimple.

## <a name="virtual-device-updates-in-update-3"></a>Aktualizace virtuálních zařízení v aktualizaci 3
Tuto aktualizaci nelze použít na StorSimple Cloud Appliance (označované také jako virtuální zařízení). Bude třeba vytvořit nová virtuální zařízení. 

## <a name="next-step"></a>Další krok
Přečtěte si, jak [nainstalovat aktualizaci 3](storsimple-install-update-3.md) do zařízení StorSimple.

