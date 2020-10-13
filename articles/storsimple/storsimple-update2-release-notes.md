---
title: StorSimple 8000 Series Update 2 – zpráva k vydání verze | Microsoft Docs
description: Popisuje nové funkce, problémy a alternativní řešení pro StorSimple 8000 Series Update 2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 53be638c46685dd46a5afb5ed56f9a76ed7b5fd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90055856"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>Zpráva k vydání verze pro StorSimple 8000 Series Update 2

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikují důležité otevřené problémy pro StorSimple 8000 Series Update 2. Obsahují také seznam aktualizací StorSimple softwaru, ovladače a firmwaru disku, které jsou součástí této verze. 

Aktualizaci 2 můžete použít pro jakékoli zařízení s StorSimple s verzí (GA) nebo Update 0,1 až do aktualizace 1,2. Verze zařízení, která je přidružená k Update 2, je 6.3.9600.17673.

Před nasazením aktualizace do řešení StorSimple si prosím přečtěte informace uvedené v poznámkách k verzi.

> [!IMPORTANT]
> * Instalace této aktualizace (včetně aktualizací systému Windows) trvá přibližně 4-7 hodin. 
> * Aktualizace 2 má software, ovladač LSI a aktualizace firmwaru SSD.
> * Pro nové verze se aktualizace nemusí okamžitě zobrazovat, protože provádíme dvoufázové zavedení aktualizací. Počkejte několik dní a pak znovu vyhledejte aktualizace, protože budou brzy k dispozici.
> 
> 

## <a name="whats-new-in-update-2"></a>Co je nového v aktualizaci Update 2
Aktualizace 2 zavádí následující nové funkce.

* **Místně připnuté svazky** – v předchozích verzích řady StorSimple 8000 byly bloky dat vrstveny do cloudu na základě využití. Neexistuje žádný způsob, jak zaručit, že by bloky zůstaly v místním prostředí. Když ve službě Update 2 vytvoříte svazek, můžete ho označit jako místně připojený a primární data z tohoto svazku nebudou vrstvená do cloudu. Snímky místně připnutého svazku se zkopírují do cloudu pro zálohování, takže Cloud se dá použít pro účely mobility dat a zotavení po havárii. Navíc můžete změnit typ svazku (to znamená převést vrstvené svazky na místně připojené svazky a převést místně připojené svazky na vrstvený). 
* **Vylepšení virtuálních zařízení StorSimple** – řada StorSimple 8000 umístila virtuální zařízení jako řešení zotavení po havárii nebo pro vývoj/testování. Byl k dispozici pouze jeden model virtuálního zařízení (model 1100). Aktualizace 2 přináší dva modely virtuálních zařízení: 
  
  * 8010 (dříve označované jako 1100) – bez změny; má kapacitu 30 TB a používá službu Azure Storage úrovně Standard.
  * 8020 – má kapacitu 64 TB a využívá Azure Premium Storage pro zlepšení výkonu.
    
    Pro modely virtuálních zařízení (8010/8020) existuje jeden virtuální pevný disk (VHD). Při prvním spuštění virtuálního zařízení se zjistí parametry platformy a použije se správná verze modelu.
* **Vylepšení sítě** – aktualizace 2 obsahuje následující vylepšení sítě:
  
  * Pro Cloud může být povoleno více síťových rozhraní, aby převzetí služeb při selhání mohlo probíhat v případě selhání síťové karty.
  * Vylepšení směrování s pevnými metrikami pro bloky s povoleným cloudem.
  * Online opakování neúspěšných prostředků před převzetím služeb při selhání.
  * Nové výstrahy týkající se selhání služby.
* **Aktualizace vylepšení** – v aktualizaci 1,2 a starších verzích se řada StorSimple 8000 aktualizovala prostřednictvím dvou kanálů: web Windows Update pro clusteringu, iSCSI atd. a Microsoft Update pro binární soubory a firmware.
    Aktualizace 2 používá Microsoft Update pro všechny balíčky aktualizací. To by mělo mít za následek méně času na opravy nebo převzetí služeb při selhání. 
* **Aktualizace firmwaru** – jsou k dispozici následující aktualizace firmwaru:
  
  * LSI: lsi_sas2.sys verze produktu 2.00.72.10
  * Pouze SSD (žádné aktualizace HDD): XMGG, XGEG, KZ50, F6C2 a VR08
* **Proaktivní podpora** – aktualizace 2 umožňuje Microsoftu vyžádat si další diagnostické informace ze zařízení. V případě, že náš provozní tým identifikuje zařízení, u kterých dochází k problémům, je vhodnější shromažďovat informace ze zařízení a diagnostikovat problémy. **Přijetím aktualizace Update 2 nám umožníme poskytnout tuto proaktivní podporu**.    

## <a name="issues-fixed-in-update-2"></a>Problémy opravené ve Update 2
Následující tabulky obsahují souhrn problémů vyřešených v aktualizacích 2.    

| Ne. | Příznak | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Síťová rozhraní |Po upgradu na aktualizaci 1 Služba StorSimple Manager nahlásila, že porty Data2 a DATA3 se na jednom řadiči nezdařily. Tento problém byl opraven. |Yes |No |
| 2 |Aktualizace |Po upgradu na aktualizaci 1 se na portálu Azure Classic na více zařízeních objevily zvukové výstrahy s varováním. Tento problém byl opraven. |Yes |No |
| 3 |OpenStack ověřování |Při použití OpenStack jako poskytovatele cloudových služeb se může zobrazit chyba, že váš řetězec cloudového ověřování byl příliš dlouhý. To jsme opravili. |Yes |No |

## <a name="known-issues-in-update-2"></a>Známé problémy v aktualizaci Update 2
Následující tabulka poskytuje souhrn známých problémů v této verzi.

| Ne. | Příznak | Problém | Komentáře a alternativní řešení | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Kvorum disku |Ve výjimečných případech dojde k odpojení většiny disků v EBOD skříni zařízení 8600, což způsobí nedostatečné diskové kvorum, ale fond úložiště přejde do režimu offline. Zůstane v režimu offline i v případě, že jsou disky znovu připojeny. |Budete muset zařízení restartovat. Pokud se problém opakuje, kontaktujte prosím podpora Microsoftu pro další kroky. |Yes |No |
| 2 |Nesprávné ID kontroleru |Když se provede nahrazení řadiče, může se řadič 0 zobrazit jako kontroler 1. Když se při nahrazení řadiče načte z uzlu rovnocenného uzlu, ID kontroleru se může zpočátku zobrazit jako ID partnerského řadiče. Ve výjimečných případech se toto chování může zobrazit i po restartování systému. |Není vyžadována žádná akce uživatele. Tato situace se vyřeší sám po dokončení nahrazení kontroleru. |Yes |No |
| 3 |Účty úložiště |Použití služby úložiště k odstranění účtu úložiště je nepodporovaný scénář. To vede k situaci, kdy nelze načíst uživatelská data. | |Yes |Yes |
| 4 |Převzetí služeb při selhání zařízení |Víc převzetí služeb při selhání kontejneru svazků ze stejného zdrojového zařízení do různých cílových zařízení se nepodporuje. Při převzetí služeb při selhání z jednoho nedoručeného zařízení na několik zařízení dojde ke ztrátě vlastnictví dat v kontejnerech svazků u prvního zařízení při selhání. Po takovém převzetí služeb při selhání se tyto kontejnery svazků zobrazí nebo se budou chovat jinak, než je zobrazíte na portálu Azure Classic. | |Yes |No |
| 5 |Instalace |Během instalace služby StorSimple Adapter pro SharePoint je potřeba zadat IP adresu zařízení, aby se instalace úspěšně dokončila. | |Yes |No |
| 6 |Webový proxy server |Pokud vaše konfigurace webového proxy serveru jako zadaného protokolu obsahuje HTTPS, bude ovlivněná komunikace mezi zařízením a zařízení bude offline. Balíčky podpory budou také vygenerovány v procesu a budou spotřebovávat významné prostředky na vašem zařízení. |Ujistěte se, že adresa URL webového proxy serveru má jako zadaný protokol HTTP. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](storsimple-configure-web-proxy.md). |Yes |No |
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
| 18 |Místně připojené svazky |Pokud zrušíte úlohu obnovení a okamžitě dojde k převzetí služeb při selhání, úloha obnovení **se místo** **zrušení**zobrazí. Pokud úloha obnovení selže a okamžitě dojde k převzetí služeb při selhání, úloha obnovení se místo **selhání**zobrazí jako **zrušená** . |K tomuto chování může dojít v případě, že úloha obnovení má jenom místně připnuté svazky nebo kombinaci místně připnutého a vrstveného svazku. Pokud úloha obnovení zahrnuje jenom vrstvené svazky, pak k tomuto chování nedojde. Není vyžadován žádný zásah uživatele. |Yes |No |
| 19 |Místně připojené svazky |Pokud zrušíte úlohu obnovení, nebo pokud obnovení selhává a pak dojde k převzetí služeb při selhání, zobrazí se na stránce **úlohy** další úloha obnovení. |K tomuto chování může dojít v případě, že úloha obnovení má jenom místně připnuté svazky nebo kombinaci místně připnutého a vrstveného svazku. Pokud úloha obnovení zahrnuje jenom vrstvené svazky, pak k tomuto chování nedojde. Není vyžadován žádný zásah uživatele. |Yes |No |
| 20 |Místně připojené svazky |Pokud se pokusíte převést vrstvený svazek (vytvořený a Klonovaný pomocí aktualizace 1,2 nebo starší) na místně připojený svazek a na vašem zařízení dochází volné místo nebo dojde k výpadku cloudu, klony můžou být poškozené. |K tomuto problému dochází pouze se svazky vytvořenými a klonovánými pomocí softwaru před aktualizací 2. To by mělo být nečastý scénář. | | |
| 21 |Převod svazků |Neaktualizujte záznamů ACR připojené ke svazku, když probíhá převod svazku (vrstvené na místně připnuté nebo naopak). Aktualizace záznamů ACR by mohla způsobit poškození dat. |V případě potřeby aktualizujte záznamů ACR před převodem svazku a neprovádějte žádné další aktualizace ACR, zatímco probíhá převod. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Aktualizace řadiče a firmwaru v aktualizaci Update 2
Tato verze aktualizuje ovladač a firmware disku na vašem zařízení.

* Další informace o aktualizaci firmwaru LSI najdete v článku 3121900 znalostní báze Microsoft Knowledge Base. 
* Další informace o aktualizaci firmwaru disku najdete v článku 3121899 znalostní báze Microsoft Knowledge Base.

## <a name="virtual-device-updates-in-update-2"></a>Aktualizace virtuálních zařízení v aktualizaci Update 2
Tuto aktualizaci nelze použít pro virtuální zařízení. Nová virtuální zařízení bude potřeba vytvořit. 

## <a name="next-step"></a>Další krok
Přečtěte si, jak [nainstalovat Update 2](storsimple-install-update-2.md) na zařízení StorSimple.

