---
title: StorSimple 8000 Series Update 2 poznámky k verzi | Dokumenty společnosti Microsoft
description: Popisuje nové funkce, problémy a řešení pro aktualizaci Řady StorSimple 8000 2.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: twooley
ms.openlocfilehash: 4e57fffd2f74ae1b14f51537c92299607f193ad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934055"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>Poznámky k verzi verze StorSimple 8000 Series Update 2

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikují kritické otevřené problémy pro aktualizaci Řady StorSimple 8000 2. Obsahují také seznam aktualizací softwaru StorSimple, ovladače a firmwaru disku, které jsou součástí této verze. 

Aktualizaci 2 lze použít na libovolné zařízení StorSimple se spuštěním verze (GA) nebo aktualizace 0.1 až aktualizace 1.2. Verze zařízení přidružená k aktualizaci 2 je 6.3.9600.17673.

Před nasazením aktualizace v řešení StorSimple si přečtěte informace obsažené v poznámkách k verzi.

> [!IMPORTANT]
> * Instalace této aktualizace (včetně aktualizací systému Windows) trvá přibližně 4 až 7 hodin. 
> * Aktualizace 2 obsahuje aktualizace softwaru, ovladače LSI a firmwaru SSD.
> * U nových verzí se aktualizace nemusí zobrazit okamžitě, protože provádíme postupné zavádění aktualizací. Počkejte několik dní a pak znovu vyskenujte aktualizace, protože budou brzy k dispozici.
> 
> 

## <a name="whats-new-in-update-2"></a>Co je nového v aktualizaci 2
Aktualizace 2 zavádí následující nové funkce.

* **Místně vázaných svazků** – V předchozích verzích řady StorSimple 8000 byly bloky dat vrstvené do cloudu na základě využití. Neexistoval žádný způsob, jak zaručit, že bloky zůstanou na místní. V aktualizaci 2 můžete při vytváření svazku určit svazek jako místně vázaný a primární data z tohoto svazku nebudou vrstvena do cloudu. Snímky místně vázaných svazků se budou stále zkopírovat do cloudu pro zálohování, aby bylo možné cloud použít pro účely mobility dat a zotavení po havárii. Kromě toho můžete změnit typ svazku (to znamená převést vrstvené svazky na místně vázaných svazků a převést místně vázaných svazků na vrstvené). 
* **StorSimple vylepšení virtuálních zařízení** – Dříve řada StorSimple 8000 umístila virtuální zařízení jako řešení zotavení po havárii nebo řešení vývoje/testování. Existoval pouze jeden model virtuálního zařízení (model 1100). Aktualizace 2 zavádí dva modely virtuálních zařízení: 
  
  * 8010 (dříve nazývané 1100) – Žádná změna; má kapacitu 30 TB a používá standardní úložiště Azure.
  * 8020 – má kapacitu 64 TB a využívá úložiště Azure Premium pro lepší výkon.
    
    Pro oba modely virtuálních zařízení (8010/8020) je k dispozici jeden virtuální pevný disk. Při prvním spuštění virtuálního zařízení detekuje parametry platformy a použije správnou verzi modelu.
* **Vylepšení sítě** – aktualizace 2 obsahuje následující vylepšení sítě:
  
  * Pro cloud lze povolit více nic, takže k převzetí služeb při selhání může dojít, pokud dojde k selhání síťové karty.
  * Vylepšení směrování s pevnými metrikami pro bloky s podporou cloudu.
  * Online opakování neúspěšných prostředků před převzetím služeb při selhání.
  * Nové výstrahy pro selhání služby.
* **Aktualizace vylepšení** – V aktualizaci 1.2 a starší byla řada StorSimple 8000 aktualizována prostřednictvím dvou kanálů: Windows Update pro clustering, iSCSI a tak dále a Microsoft Update pro binární soubory a firmware.
    Aktualizace 2 používá službu Microsoft Update pro všechny balíčky aktualizací. To by mělo vést k kratšímu času opravy nebo provádění převzetí služeb při selhání. 
* **Aktualizace firmwaru** – jsou zahrnuty následující aktualizace firmwaru:
  
  * LSI: lsi_sas2.sys verze produktu 2.00.72.10
  * Pouze SSD (bez aktualizací HDD): XMGG, XGEG, KZ50, F6C2 a VR08
* **Proaktivní podpora** – aktualizace 2 umožňuje společnosti Microsoft získat další diagnostické informace ze zařízení. Když náš provozní tým identifikuje zařízení, která mají problémy, jsme lépe vybaveni ke shromažďování informací ze zařízení a diagnostice problémů. **Přijetím aktualizace 2 nám umožňujete poskytovat tuto proaktivní podporu**.    

## <a name="issues-fixed-in-update-2"></a>Problémy opravené v aktualizaci 2
Následující tabulky obsahují souhrn problémů, které byly opraveny v aktualizacích 2.    

| Ne. | Funkce | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Síťová rozhraní |Po upgradu na aktualizaci 1 služba StorSimple Manager oznámila, že porty Data2 a Data3 se nezdařily na jednom řadiči. Tento problém byl opraven. |Ano |Ne |
| 2 |Aktualizace |Po upgradu na aktualizaci 1 došlo k zvukovým poplachovým upozorněním na klasickém portálu Azure na více zařízeních. Tento problém byl opraven. |Ano |Ne |
| 3 |Ověřování openstacku |Při použití Openstack jako poskytovatele cloudových služeb se může zobrazit chyba, že váš řetězec cloudového ověřování byl příliš dlouhý. To jsme opravili. |Ano |Ne |

## <a name="known-issues-in-update-2"></a>Známé problémy v aktualizaci 2
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
| 20 |Místně připojené svazky |Pokud se pokusíte převést vrstvený svazek (vytvořený a klonovaný pomocí aktualizace 1.2 nebo starší) na místně vázaný svazek a zařízení nemá místo nebo dojde k výpadku cloudu, pak může být klon (y) poškozen. |K tomuto problému dochází pouze u svazků, které byly vytvořeny a klonovány pomocí softwaru pre-Update 2. To by mělo být zřídka scénář. | | |
| 21 |Převod objemu |Neaktualizujte akreditované kartotéky připojené ke svazku v průběhu převodu svazku (vrstvené na místně připnuté nebo naopak). Aktualizace akrsů může vést k poškození dat. |V případě potřeby aktualizujte acr před převodem objemu a neprovázte žádné další aktualizace ACR, zatímco probíhá převod. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Aktualizace řadiče a firmwaru v aktualizaci 2
Tato verze aktualizuje ovladač a firmware disku v zařízení.

* Další informace o aktualizaci firmwaru LSI naleznete v článku 3121900 znalostní báze Microsoft Knowledge Base. 
* Další informace o aktualizaci firmwaru disku naleznete v článku 3121899 znalostní báze Microsoft Knowledge Base.

## <a name="virtual-device-updates-in-update-2"></a>Aktualizace virtuálních zařízení v aktualizaci 2
Tuto aktualizaci nelze použít na virtuální zařízení. Bude třeba vytvořit nová virtuální zařízení. 

## <a name="next-step"></a>Další krok
Přečtěte si, jak [nainstalovat aktualizaci 2](storsimple-install-update-2.md) do zařízení StorSimple.

