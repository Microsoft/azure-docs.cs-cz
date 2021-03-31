---
title: Zpráva k vydání verze pro StorSimple 8000 Series Update 3
description: Popisuje nové funkce, problémy a alternativní řešení pro StorSimple 8000 Series Update 3.
author: alkohli
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e081acc357c1b8872dd3a25c552d4cbe9785e212
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96007065"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Aktualizace 3 poznámky k verzi pro zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikují kritické otevřené problémy pro StorSimple 8000 Series Update 3. Obsahují také seznam StorSimple aktualizací softwaru, které jsou součástí této verze. 

Aktualizaci 3 lze použít pro jakékoli zařízení StorSimple s verzí (GA) nebo Update 0,1 až do aktualizace 2,2. Verze zařízení, která je přidružená k Update 3, je 6.3.9600.17759.

Před nasazením aktualizace do řešení StorSimple si prosím přečtěte informace uvedené v poznámkách k verzi.

> [!IMPORTANT]
> * Aktualizace 3 má software zařízení, ovladač LSI a firmware a aktualizace ovladačů Storport a Spaceport. Instalace této aktualizace trvá přibližně 1,5 – 2 hodiny. 
> * Pro nové verze se aktualizace nemusí okamžitě zobrazovat, protože provádíme dvoufázové zavedení aktualizací. Počkejte několik dní a pak znovu vyhledejte aktualizace, protože budou brzy k dispozici.
> 
> 

## <a name="whats-new-in-update-3"></a>Co je nového v aktualizaci Update 3
Následující klíčová vylepšení a opravy chyb byly provedeny ve službě Update 3.

* **Automatizované změny recyklace prostoru** – počínaje aktualizací 3 se algoritmy recyklace prostoru spouštějí v pohotovostním kontroleru systému, což má za následek rychlejší spuštění. Další informace o portech, které jsou potřeba pro práci s recyklací místa, najdete v článku [StorSimple požadavky na síť](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Vylepšení výkonu** – aktualizace 3 má Vylepšený výkon pro čtení i zápis do cloudu.
* **Vylepšení týkající se migrace** – v této verzi jsme dokončili několik oprav chyb a vylepšení pro funkci migrace ze zařízení řady 5000/7000 na 8000 series. Další informace o tom, jak používat funkci migrace, najdete v tématu [migrace ze zařízení 5000/7000 Series na 8000 series Device](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Monitorování souvisejících oprav** – v této verzi jsme opravili chyby související s monitorováním grafů, řídicím panelem služeb a řídicím panelem zařízení.

## <a name="issues-fixed-in-update-3"></a>Problémy opravené ve Update 3
Následující tabulky obsahují souhrn problémů, které byly opraveny ve verzi Update 3.    

| Ne | Doporučené | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Migrace dat na straně hostitele |V dřívější verzi StorSimple Cloud Appliance přechodem do režimu offline při migraci dat na straně hostitele. Tento problém je opravený v této verzi. |No |Yes |
| 2 |Místně připojené svazky |V předchozí verzi byly problémy související se selháním vstupu a výstupu, selháním převodu svazků a selháním DataPath pro místně připnuté svazky. Tyto problémy byly způsobeny rootem a opraveny v této verzi. |Yes |No |
| 3 |Monitorování |Existuje několik problémů souvisejících s jednotkami a monitorováním sestav a s grafy řídicích panelů zařízení, ve kterých byly pro místně připojené svazky zobrazeny nesprávné informace. Tyto problémy jsou opravené v této verzi. |Yes |No |
| 4 |I/O silné zápisy |Při použití StorSimple pro úlohy obsahující těžké zápisy by se uživatel spouštěl v nečasté chybě, při které byla pracovní sada vrstvená do cloudu. Tato chyba je opravena v této verzi. |Yes |Yes |
| 5 |Backup |V některých vzácných instancích v předchozích verzích softwaru, když uživatel trval zálohu vzdáleného klonu, mohl spustit chyby v cloudu a tato operace by se stala chybou. V této verzi se problém vyřešil a operace se úspěšně dokončila. |Yes |Yes |
| 6 |Zásady zálohování |V některých vzácných instancích v dřívějších verzích softwaru došlo k chybě související s odstraňováním zásad zálohování. Tento problém je opravený v této verzi. |Yes |Yes |

## <a name="known-issues-in-update-3"></a>Známé problémy v aktualizaci Update 3
Následující tabulka poskytuje souhrn známých problémů v této verzi.

| No. | Funkce | Problém | Komentáře a alternativní řešení | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Kvorum disku |Ve výjimečných případech dojde k odpojení většiny disků v EBOD skříni zařízení 8600, což způsobí nedostatečné diskové kvorum, ale fond úložiště přejde do režimu offline. Zůstane v režimu offline i v případě, že jsou disky znovu připojeny. |Budete muset zařízení restartovat. Pokud se problém opakuje, kontaktujte prosím podpora Microsoftu pro další kroky. |Yes |No |
| 2 |Nesprávné ID kontroleru |Když se provede nahrazení řadiče, může se řadič 0 zobrazit jako kontroler 1. Když se při nahrazení řadiče načte z uzlu rovnocenného uzlu, ID kontroleru se může zpočátku zobrazit jako ID partnerského řadiče. Ve výjimečných případech se toto chování může zobrazit i po restartování systému. |Není vyžadována žádná akce uživatele. Tato situace se vyřeší sám po dokončení nahrazení kontroleru. |Yes |No |
| 3 |Účty úložiště |Použití služby úložiště k odstranění účtu úložiště je nepodporovaný scénář. To vede k situaci, kdy nelze načíst uživatelská data. | |Yes |Yes |
| 4 |Převzetí služeb při selhání zařízení |Víc převzetí služeb při selhání kontejneru svazků ze stejného zdrojového zařízení do různých cílových zařízení se nepodporuje. Při převzetí služeb při selhání z jednoho nedoručeného zařízení na několik zařízení dojde ke ztrátě vlastnictví dat v kontejnerech svazků u prvního zařízení při selhání. Po takovém převzetí služeb při selhání se tyto kontejnery svazků zobrazí nebo se budou chovat jinak, než je zobrazíte na portálu Azure Classic. | |Yes |No |
| 5 |Instalace |Během instalace služby StorSimple Adapter pro SharePoint je potřeba zadat IP adresu zařízení, aby se instalace úspěšně dokončila. | |Yes |No |
| 6 |Webový proxy server |Pokud vaše konfigurace webového proxy serveru jako zadaného protokolu obsahuje HTTPS, bude ovlivněná komunikace mezi zařízením a zařízení bude offline. Balíčky podpory budou také vygenerovány v procesu a budou spotřebovávat významné prostředky na vašem zařízení. |Ujistěte se, že adresa URL webového proxy serveru má jako zadaný protokol HTTP. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](storsimple-8000-configure-web-proxy.md). |Yes |No |
| 7 |Webový proxy server |Pokud nakonfigurujete a povolíte webový proxy server na registrovaném zařízení, budete muset na svém zařízení restartovat aktivní kontroler. | |Yes |No |
| 8 |Vysoká latence v cloudu a vysoká zátěž/výstup |Když zařízení StorSimple zaznamená kombinaci velmi vysoké latence v cloudu (v řádu sekund) a vysoké vstupně-výstupní úlohy, svazky zařízení přejdou do stavu sníženo a vstupně-výstupní operace se nemusí zdařit s chybou zařízení není připraveno. |V takovém případě budete muset ručně restartovat řadiče zařízení nebo provést převzetí služeb při selhání zařízení pro obnovení. |Yes |No |
| 9 |Azure PowerShell |Použijete-li rutinu StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object-First 1-počkat** na výběr prvního objektu, abyste mohli vytvořit nový objekt **ke kontejneru svazků** , rutina vrátí všechny objekty. |Zabalte rutinu do závorek následujícím způsobem: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object – First 1-Wait** |Yes |Yes |
| 10 |Migrace |Pokud je pro migraci předána více kontejnerů svazků, je ETA pro nejnovější zálohování přesné pouze pro první kontejner svazků. Kromě toho se spustí paralelní migrace po migraci prvních 4 záloh v prvním kontejneru svazků. |Doporučujeme migrovat jeden kontejner svazků v jednom okamžiku. |Yes |No |
| 11 |Migrace |Po obnovení nebudou svazky přidány do zásad zálohování nebo do skupiny virtuálních disků. |Tyto svazky budete muset přidat do zásad zálohování, aby bylo možné vytvářet zálohy. |Yes |Yes |
| 12 |Migrace |Po dokončení migrace nesmí zařízení řady 5000/7000 přistupovat ke migrovaných datovým kontejnerům. |Po dokončení a potvrzení migrace doporučujeme odstranit migrované datové kontejnery. |Yes |No |
| 13 |Klonování a zotavení po havárii |Zařízení StorSimple se systémem Update 1 nemůže klonovat nebo provést zotavení po havárii na zařízení, na kterém běží software před aktualizací 1. |Aby bylo možné tyto operace, je třeba aktualizovat cílové zařízení na aktualizaci 1. |Yes |Yes |
| 14 |Migrace |Pokud existují skupiny svazků bez přidružených svazků, zálohování konfigurace pro migraci na zařízení řady 5000-7000 se nemusí zdařit. |Odstraňte všechny prázdné skupiny svazků bez přidružených svazků a opakujte zálohování konfigurace. |Yes |No |
| 15 |Rutiny Azure PowerShell a místně připnuté svazky |Pomocí rutin Azure PowerShell nemůžete vytvořit místně připojený svazek. (Všechny svazky, které vytvoříte prostřednictvím Azure PowerShell, budou vrstveny.) |Vždy používejte službu StorSimple Manager ke konfiguraci místně připojených svazků. |Yes |No |
| 16 |Místo k dispozici pro místně připojené svazky |Odstraníte-li místně připojený svazek, nebude možné okamžitě aktualizovat místo dostupné pro nové svazky. Služba StorSimple Manager aktualizuje místní prostor, který je k dispozici přibližně každou hodinu. |Než se pokusíte vytvořit nový svazek, počkejte hodinu. |Yes |No |
| 17 |Místně připojené svazky |Vaše úloha obnovení zpřístupňuje dočasné zálohování snímku v katalogu záloh, ale pouze po dobu trvání úlohy obnovení. Kromě toho zveřejňuje skupinu virtuálních disků s předponou **tmpCollection** na stránce **zásady zálohování** , ale pouze po dobu trvání úlohy obnovení. |K tomuto chování může dojít v případě, že úloha obnovení má jenom místně připnuté svazky nebo kombinaci místně připnutého a vrstveného svazku. Pokud úloha obnovení zahrnuje jenom vrstvené svazky, pak k tomuto chování nedojde. Není vyžadován žádný zásah uživatele. |Yes |No |
| 18 |Místně připojené svazky |Pokud zrušíte úlohu obnovení a okamžitě dojde k převzetí služeb při selhání, úloha obnovení **se místo** **zrušení** zobrazí. Pokud úloha obnovení selže a okamžitě dojde k převzetí služeb při selhání, úloha obnovení se místo **selhání** zobrazí jako **zrušená** . |K tomuto chování může dojít v případě, že úloha obnovení má jenom místně připnuté svazky nebo kombinaci místně připnutého a vrstveného svazku. Pokud úloha obnovení zahrnuje jenom vrstvené svazky, pak k tomuto chování nedojde. Není vyžadován žádný zásah uživatele. |Yes |No |
| 19 |Místně připojené svazky |Pokud zrušíte úlohu obnovení, nebo pokud obnovení selhává a pak dojde k převzetí služeb při selhání, zobrazí se na stránce **úlohy** další úloha obnovení. |K tomuto chování může dojít v případě, že úloha obnovení má jenom místně připnuté svazky nebo kombinaci místně připnutého a vrstveného svazku. Pokud úloha obnovení zahrnuje jenom vrstvené svazky, pak k tomuto chování nedojde. Není vyžadován žádný zásah uživatele. |Yes |No |
| 20 |Místně připojené svazky |Pokud se pokusíte převést vrstvený svazek (vytvořený a Klonovaný pomocí aktualizace 1,2 nebo starší) na místně připojený svazek a na vašem zařízení dochází volné místo nebo dojde k výpadku cloudu, klony můžou být poškozené. |K tomuto problému dochází jenom se svazky vytvořenými a naklonované pomocí softwaru před aktualizací 2,1. To by mělo být nečastý scénář. | | |
| 21 |Převod svazků |Neaktualizujte záznamů ACR připojené ke svazku, když probíhá převod svazku (vrstvené na místně připnuté nebo naopak). Aktualizace záznamů ACR by mohla způsobit poškození dat. |V případě potřeby aktualizujte záznamů ACR před převodem svazku a neprovádějte žádné další aktualizace ACR, zatímco probíhá převod. | | |
| 22 |Aktualizace |Při použití aktualizace Update 3 se na stránce **Údržba** na portálu Azure Classic zobrazí následující zpráva týkající se aktualizace Update 2 – StorSimple 8000 Series Update 2 zahrnuje možnost, aby Microsoft proaktivně shromáždili informace protokolu ze zařízení, když zjistíme potenciální problémy. To je zavádějící, protože indikuje, že se zařízení aktualizuje na Update 2. Po úspěšné aktualizaci zařízení na aktualizaci 3 bude tato zpráva zmizet. |Toto chování bude opraveno v budoucí verzi. |Yes |No |

## <a name="controller-and-firmware-updates-in-update-3"></a>Aktualizace řadiče a firmwaru v aktualizaci Update 3
Tato verze obsahuje aktualizace ovladačů LSI a firmwaru. Další informace o tom, jak nainstalovat ovladač LSI a aktualizace firmwaru, najdete v článku o [instalaci aktualizace 3](./index.yml) na zařízení StorSimple.

## <a name="virtual-device-updates-in-update-3"></a>Aktualizace virtuálních zařízení v aktualizaci Update 3
Tuto aktualizaci nelze použít pro StorSimple Cloud Appliance (označuje se také jako virtuální zařízení). Nová virtuální zařízení bude potřeba vytvořit. 

## <a name="next-step"></a>Další krok
Přečtěte si, jak [nainstalovat Update 3](./index.yml) na zařízení StorSimple.