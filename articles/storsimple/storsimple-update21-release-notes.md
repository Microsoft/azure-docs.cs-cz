---
title: Zpráva k vydání verze StorSimple 8000 Series Update 2,2 | Microsoft Docs
description: Popisuje nové funkce, problémy a alternativní řešení pro StorSimple 8000 Series Update 2,2.
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
ms.openlocfilehash: 73b9ecd03875b60ed2d9b9d4c8e8a3a0c8de3cfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956598"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>Zpráva k vydání verze pro StorSimple 8000 Series Update 2,2

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikují důležité otevřené problémy pro StorSimple 8000 Series Update 2,2. Obsahují také seznam StorSimple aktualizací softwaru, které jsou součástí této verze.

Aktualizaci 2,2 lze použít pro jakékoli zařízení StorSimple s verzí (GA) nebo Update 0,1 prostřednictvím aktualizace Update 2,1. Verze zařízení, která je přidružená k aktualizaci 2,2, je 6.3.9600.17708.

Před nasazením aktualizace do řešení StorSimple si prosím přečtěte informace uvedené v poznámkách k verzi.

> [!IMPORTANT]
> * Aktualizace 2,2 obsahuje pouze aktualizace softwaru. Instalace této aktualizace trvá přibližně 1,5 – 2 hodiny. 
> * Pokud používáte aktualizaci 2,1, doporučujeme, abyste aktualizaci 2,2 napoužívali co nejrychleji.
> * Pro nové verze se aktualizace nemusí okamžitě zobrazovat, protože provádíme dvoufázové zavedení aktualizací. Počkejte několik dní a pak znovu vyhledejte aktualizace, protože budou brzy k dispozici.
> 
> 

## <a name="whats-new-in-update-22"></a>Co je nového v aktualizaci 2,2
V aktualizaci 2,2 byla provedena následující vylepšení klíčů.

* **Automatizovaná optimalizace opětovného získávání prostorů** – při odstraňování dat na dynamicky zajištěných svazcích je nutné uvolnit nepoužívané bloky úložiště. Tato verze zlepšila proces opětovného získávání místa z cloudu, což znamená, že nevyužité místo bude v porovnání s předchozími verzemi rychlejší než k dispozici.
* **Vylepšení výkonu snímků** – aktualizace 2,2 zvýšila čas na zpracování snímku v cloudu v určitých situacích, kdy se používají velké svazky a že je minimálním počtem změn dat. Scénář, který by tento vylepšení využil, by představoval archivní svazky.
* **Posílení zabezpečení shromažďování balíčků podpory** – bylo vylepšeno, jak se balíček pro podporu shromažďuje a nahrává v této verzi. 
* **Aktualizace vylepšení spolehlivosti** – Tato verze obsahuje opravy chyb, jejichž výsledkem je Vylepšená spolehlivost aktualizace.

## <a name="issues-fixed-in-update-22"></a>Problémy opravené v aktualizaci 2,2
Následující tabulky obsahují souhrn problémů vyřešených v aktualizacích 2,2 a 2,1.    

| Ne | Doporučené | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Výkon hostitele |V dřívější verzi byly problémy s výkonem na straně hostitele pozorovány při vytváření místně připnutého svazku a při převodu vrstveného svazku na místně připojený svazek. Tyto problémy jsou opravené v této verzi, což vede ke zvýšení výkonu hostitele během vytváření a postupů převodu svazku. |Yes |No |
| 2 |Místně připojené svazky |Ve výjimečných případech dojde při vytváření místně připojeného svazku k chybě systému. Tato chyba byla opravena v této verzi. |Yes |No |
| 3 |Vrstvení |V případě, že se metadata pro cloudová zařízení StorSimple (8010 a 8020) vrstvená do cloudu dostala do více než občasných havárií. Tento problém je opravený v této verzi. |No |Yes |
| 4 |Vytvoření snímku |Došlo k potížím souvisejícím s vytvářením přírůstkových snímků ve scénářích s velkými objemy a minimálními nároky na změny dat. Tyto problémy jsou opravené v této verzi. |Yes |Yes |
| 5 |OpenStack ověřování |Při použití OpenStack jako poskytovatele cloudových služeb by se uživatel spouštěl v nečasté chybě související s ověřováním, kde analyzátor JSON způsobil chybu. Tato chyba je opravena v této verzi. |Yes |No |
| 6 |Kopírování na straně hostitele |V dřívějších verzích softwaru byla při kopírování dat z jednoho svazku do jiného svazku zjištěna nečastá chyba související s časováním ODX. Výsledkem by bylo převzetí služeb při selhání řadiče a systém může potenciálně přejít do režimu obnovení. Tato chyba je opravena v této verzi. |Yes |No |
| 7 |Rozhraní WMI (Windows Management Instrumentation) (WMI) |V předchozích verzích softwaru existovala několik instancí webového proxy serveru s výjimkou " \<ManagementException> selhání načtení zprostředkovatele". Tato chyba byla připsána k nevracení paměti WMI a je nyní opravena. |Yes |No |
| 8 |Aktualizace |V některých vzácných instancích v předchozích verzích softwaru uživatel při pokusu o kontrolu nebo instalaci aktualizací obdržel "CisPowershellHcsscripterror". Tento problém je opravený v této verzi. |Yes |Yes |
| 9 |Balíček pro podporu |V této verzi jsme nastavili vylepšení způsobu, jakým se balíček pro podporu shromáždí a nahraje. |Yes |Yes |

## <a name="known-issues-in-update-22"></a>Známé problémy v aktualizaci 2,2
Následující tabulka poskytuje souhrn známých problémů v této verzi.

| No. | Funkce | Problém | Komentáře a alternativní řešení | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Kvorum disku |Ve výjimečných případech dojde k odpojení většiny disků v EBOD skříni zařízení 8600, což způsobí nedostatečné diskové kvorum, ale fond úložiště přejde do režimu offline. Zůstane v režimu offline i v případě, že jsou disky znovu připojeny. |Budete muset zařízení restartovat. Pokud se problém opakuje, kontaktujte prosím podpora Microsoftu pro další kroky. |Yes |No |
| 2 |Nesprávné ID kontroleru |Když se provede nahrazení řadiče, může se řadič 0 zobrazit jako kontroler 1. Když se při nahrazení řadiče načte z uzlu rovnocenného uzlu, ID kontroleru se může zpočátku zobrazit jako ID partnerského řadiče. Ve výjimečných případech se toto chování může zobrazit i po restartování systému. |Není vyžadována žádná akce uživatele. Tato situace se vyřeší sám po dokončení nahrazení kontroleru. |Yes |No |
| 3 |Účty úložiště |Použití služby úložiště k odstranění účtu úložiště je nepodporovaný scénář. To vede k situaci, kdy nelze načíst uživatelská data. | |Yes |Yes |
| 4 |Převzetí služeb při selhání zařízení |Víc převzetí služeb při selhání kontejneru svazků ze stejného zdrojového zařízení do různých cílových zařízení se nepodporuje. Při převzetí služeb při selhání z jednoho nedoručeného zařízení na několik zařízení dojde ke ztrátě vlastnictví dat v kontejnerech svazků u prvního zařízení při selhání. Po takovém převzetí služeb při selhání se tyto kontejnery svazků zobrazí nebo se budou chovat jinak, než je zobrazíte na portálu Azure Classic. | |Yes |No |
| 5 |Instalace |Během instalace služby StorSimple Adapter pro SharePoint je potřeba zadat IP adresu zařízení, aby se instalace úspěšně dokončila. | |Yes |No |
| 6 |Webový proxy server |Pokud vaše konfigurace webového proxy serveru jako zadaného protokolu obsahuje HTTPS, bude ovlivněná komunikace mezi zařízením a zařízení bude offline. Balíčky podpory budou také vygenerovány v procesu a budou spotřebovávat významné prostředky na vašem zařízení. |Ujistěte se, že adresa URL webového proxy serveru má jako zadaný protokol HTTP. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](./storsimple-8000-configure-web-proxy.md). |Yes |No |
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

## <a name="controller-and-firmware-updates-in-update-22"></a>Aktualizace řadiče a firmwaru v aktualizaci 2,2
Tato verze obsahuje jenom softwarové aktualizace. Pokud ale provádíte aktualizaci z verze před aktualizací Update 2, budete muset na svém zařízení nainstalovat aktualizace firmwaru disku, ovladače Storport, Spaceport a (v některých případech).

Další informace o tom, jak nainstalovat aktualizace ovladače, ovladačů Storport, Spaceport a firmwaru disku, najdete v článku [instalace aktualizace 2,2](./storsimple-8000-install-update-5.md) na zařízení StorSimple.

## <a name="virtual-device-updates-in-update-22"></a>Aktualizace virtuálních zařízení v aktualizaci Update 2,2
Tuto aktualizaci nelze použít pro virtuální zařízení. Nová virtuální zařízení bude potřeba vytvořit. 

## <a name="next-step"></a>Další krok
Přečtěte si, jak [nainstalovat Update 2,2](./storsimple-8000-install-update-5.md) na zařízení StorSimple.