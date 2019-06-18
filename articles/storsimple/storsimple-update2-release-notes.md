---
title: Poznámky k verzi StorSimple 8000 Series Update 2 | Dokumentace Microsoftu
description: Popisuje nové funkce, problémy a řešení StorSimple 8000 Series Update 2.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: f23a507ab631be553613e22cafa037291548a8aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717134"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>Poznámky k verzi StorSimple 8000 Series Update 2

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikovat kritické otevřené problémy pro StorSimple 8000 Series Update 2. Obsahují také seznam softwaru StorSimple, ovladačů a aktualizace firmwaru disku jsou v ní obsažené. 

Update 2 je použít na jakékoli zařízení StorSimple s vydání (GA) nebo aktualizace 0.1 prostřednictvím aktualizace 1.2. Verze zařízení spojené s aktualizací Update 2 je 6.3.9600.17673.

Přečtěte si prosím informace obsažené v poznámkách k verzi, než nasadíte aktualizace v řešení StorSimple.

> [!IMPORTANT]
> * Trvá přibližně 4 – 7 hodin k instalaci této aktualizace (včetně aktualizací Windows). 
> * Update 2 obsahuje software, LSI ovladač a aktualizace firmwaru SSD.
> * Pro nové verze, nemusí zobrazit aktualizace okamžitě, protože děláme postupné zavádění aktualizací. Počkejte pár dní a pak vyhledávání aktualizací znovu jako tyto bude brzy k dispozici.
> 
> 

## <a name="whats-new-in-update-2"></a>Co je nového v aktualizaci Update 2
Update 2 přináší následující nové funkce.

* **Místně připojené svazky** – předchozí verze StorSimple 8000 series, byly bloků dat Vrstvená do cloudu na základě využití. Neexistoval způsob, jak zajistit, že bloky by zůstat na místní. V aktualizaci Update 2 při vytváření svazku, můžete určit svazku jako místně připnuté a primární data z tohoto svazku nebude Vrstvená do cloudu. Snímky místně připojených svazků bude stále zkopírují do cloudu pro zálohování tak, aby cloud můžete použít pro účely obnovení dat. mobility a po havárii. Kromě toho můžete změnit typ svazku (to znamená, převést vrstvené svazky na místně připojených svazků a převést místně připojené svazky do vrstev). 
* **Vylepšení virtuálního zařízení StorSimple** – dříve, řady StorSimple 8000 series umístěné virtuální zařízení jako řešení zotavení nebo vývoje/testování po havárii. Došlo k dispozici pouze jeden model virtuálního zařízení (model 1100). Update 2 představuje dva modely virtuální zařízení: 
  
  * 8010 (dříve označovaný jako 1100) – beze změny, má kapacitu s 30 TB a používá Azure storage úrovně standard.
  * 8020 – má kapacitu o velikosti 64 TB a používá Azure Premium storage za účelem vylepšení výkonu.
    
    Existuje jeden virtuální pevný disk pro oba modely virtuální zařízení (8010/8020). Při prvním spuštění virtuálního zařízení, zjistí parametrů platform a použije verze správné modelu.
* **Vylepšení sítě** – aktualizace 2 obsahuje následující vylepšení sítě:
  
  * Několik síťových karet můžete pro cloud povolené, tak, aby převzetí služeb při selhání může dojít v případě selhání síťové rozhraní.
  * Směrování vylepšení s pevnou metriky pro cloud povolené bloky.
  * Online opakování nevydařené zdroje před převzetí služeb při selhání.
  * Nové výstrahy při selhání služby.
* **Aktualizuje se vylepšení** – v aktualizaci 1.2 a starší, byla aktualizována řady StorSimple 8000 series přes dva kanály: Windows Update pro clustering, iSCSI a tak dále a Microsoft Update pro binární soubory a firmware.
    Update 2 používá službu Microsoft Update všech balíčků aktualizace. To by měla vést k méně času, opravami nebo provádíte převzetí služeb při selhání. 
* **Aktualizace firmwaru** – po firmwaru jsou zahrnuty aktualizace:
  
  * Adaptér LSI: lsi_sas2.sys verze produktu 2.00.72.10
  * Jen SSD (žádné aktualizace HDD): XMGG XGEG, KZ50, F6C2 a VR08
* **Aktivní podpora** – aktualizace 2 umožňuje společnosti Microsoft o přijetí změn dalších diagnostických informací ze zařízení. Pokud naše provozní tým identifikuje zařízení, která máte problémy, je lepší jsou shromažďovat informace ze zařízení a diagnostikovat problémy. **Přijetím aktualizace 2 povolíte, abychom mohli poskytovat tato podpora proaktivní**.    

## <a name="issues-fixed-in-update-2"></a>Chyby opravené v aktualizaci Update 2
Následující tabulka obsahuje souhrn problémů, které byly opraveny v aktualizace 2.    

| Ne. | Funkce | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Síťová rozhraní |Po upgradu na aktualizaci 1 služby StorSimple Manager oznámil, že porty Data2 a Data3 se nezdařilo u jednoho řadiče. Tento problém byl vyřešen. |Ano |Ne |
| 2 |Aktualizace |Po upgradu na aktualizaci 1 došlo k akustický signál upozornění na portálu Azure classic na více zařízeních. Tento problém byl vyřešen. |Ano |Ne |
| 3 |Openstack ověřování |Používáte-li Openstack jako poskytovatele cloudových služeb, zobrazí chybová zpráva, že vaše cloudové ověřování řetězec je příliš dlouhý. Tato chyba byla opravena. |Ano |Ne |

## <a name="known-issues-in-update-2"></a>Známé problémy v aktualizaci Update 2
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
| 20 |Místně připojené svazky |Pokud pokusu o převod vrstveného svazku (vytvořený a klonování se aktualizace 1.2 nebo starší) místně vázaný svazek a volné místo na zařízení běží nebo cloudu výpadku, můžete clone(s) poškozený. |K tomuto problému dochází pouze u svazků, které byly vytvořené a naklonované s před aktualizací 2 softwaru. To by měl být občasné scénář. | | |
| 21 |Převod svazku |Nelze aktualizovat záznamů Acr připojené ke svazku probíhá převod svazku (vrstveného na místně připnutý nebo naopak). Aktualizace záznamů Acr může způsobit poškození dat. |V případě potřeby aktualizujte záznamů Acr před převodu svazku a Nedovolte, aby byly dalšího ACR aktualizace probíhá převod. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Kontroler a aktualizace firmwaru ve verzi Update 2
Tato verze aktualizuje ovladače a firmware disku na vašem zařízení.

* Další informace o LSI firmware aktualizovat, najdete v článku znalostní báze Microsoft Knowledge base 3121900. 
* Další informace o firmwaru disku aktualizace najdete v článku znalostní báze Microsoft Knowledge base 3121899.

## <a name="virtual-device-updates-in-update-2"></a>Aktualizace virtuálního zařízení v aktualizaci Update 2
Tato aktualizace nejde použít u virtuálního zařízení. Nové virtuální zařízení bude potřeba vytvořit. 

## <a name="next-step"></a>Další krok
Zjistěte, jak [instalace aktualizace 2](storsimple-install-update-2.md) zařízení StorSimple.

