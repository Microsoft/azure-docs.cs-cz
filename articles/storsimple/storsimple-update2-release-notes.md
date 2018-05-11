---
title: Poznámky k verzi zařízení StorSimple 8000 řady Update 2 | Microsoft Docs
description: Popisuje nové funkce, problémy a řešení pro zařízení StorSimple 8000 řady Update 2.
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
ms.openlocfilehash: 0667c62e2ee2039c2b96b8c4ba81619969c3611b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>Poznámky k verzi zařízení StorSimple 8000 řady Update 2

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikovat kritická otevřené problémy pro StorSimple 8000 řady Update 2. Také obsahují seznam softwaru, ovladačů a aktualizace firmwaru disku součástí této verze zařízení StorSimple. 

Aktualizace 2 můžete použít pro všechna zařízení StorSimple, s verzí (GA) nebo Update 0.1 prostřednictvím aktualizace 1.2. Zařízení verzi spojenou s aktualizací 2 je 6.3.9600.17673.

Přečtěte si informace obsažené v poznámkách k verzi před nasazením aktualizace v řešení StorSimple.

> [!IMPORTANT]
> * Jak dlouho trvá přibližně 4-7 hodin k instalaci této aktualizace (aktualizace systému Windows včetně). 
> * Aktualizace 2 má softwaru, LSI ovladače a aktualizace firmwaru SSD.
> * Pro nové verze, nemusí zobrazit aktualizace okamžitě, protože provedeme postupné zavádění aktualizací. Počkejte několik dní, a pak vyhledejte aktualizace znovu jako tyto brzy bude dostupná.
> 
> 

## <a name="whats-new-in-update-2"></a>Co je nového v aktualizaci Update 2
Aktualizace 2 zavádí následující nové funkce.

* **Místně vázaný svazky** – v předchozích verzích řady StorSimple 8000 byly vrstvené bloků dat v cloudu na základě využití. Došlo k dispozici žádný způsob, jak zajistit, že by bloky Zůstaňte na místní. V aktualizaci Update 2 Když vytváříte svazek, můžete určit svazek jako místně vázaný a primární data ze svazku nesmí být rozvrstvena do cloudu. Snímky místně vázaných svazků stále budou zkopírovány do cloudu pro zálohování, aby cloudu můžete použít pro účely obnovení dat mobility a po havárii. Kromě toho můžete změnit typ svazku (to znamená, převést vrstvené svazky, které mají místně vázaných svazků a vrstvené svazky převést místně vázaný). 
* **Vylepšení virtuálního zařízení StorSimple** – dřív řady StorSimple 8000 umístěný virtuálního zařízení jako řešení pro obnovení nebo vývoj/testování po havárii. Došlo jenom jeden model virtuálního zařízení (model 1100). Aktualizace 2 uvádí dva modely virtuální zařízení: 
  
  * 8010 (dříve označovaný jako 1100) – žádná změna; má kapacitou 30 TB a používá standardní úložiště Azure.
  * 8020 – má kapacitou 64 TB a pro zlepšení výkonu používá Azure Premium storage.
    
    Neexistuje jeden virtuální pevný disk pro oba modely virtuální zařízení (8010/8020). Při prvním spuštění virtuálního zařízení, zjistí parametry platformy a použije verze správné modelu.
* **Vylepšení sítě** – Update 2 obsahuje následující vylepšení sítě:
  
  * Několik síťových adaptérů se dá nastavit pro cloud, aby převzetí služeb při selhání může dojít, pokud síťový adaptér se nezdaří.
  * Směrování vylepšení s pevnou metriky pro cloud povolené bloky.
  * Online opakování se nezdařilo prostředky převzetí služeb při selhání.
  * Nové výstrahy pro selhání služby.
* **Aktualizace vylepšení** – v aktualizaci 1.2 a starší, byla aktualizována řady StorSimple 8000 prostřednictvím dvou kanálů: Windows Update pro clustering, iSCSI a tak dále a Microsoft Update pro binární soubory a firmwaru.
    Aktualizace 2 používá službu Microsoft Update všech balíčků aktualizace. To by měla vést k méně času opravy nebo provádění převzetí služeb při selhání. 
* **Aktualizace firmwaru** – následující firmware aktualizace jsou součástí:
  
  * LSI: lsi_sas2.sys verze produktu 2.00.72.10
  * Pouze SSD (žádné aktualizace HDD): XMGG, XGEG, KZ50, F6C2 a VR08
* **Proaktivní podporu** – Update 2 umožňuje Microsoftu do jiné diagnostické informace z tohoto zařízení pro vyžádání obsahu. Náš tým operations identifikovat zařízení, které došlo k potížím, nejsme lépe vybavené shromažďovat informace ze zařízení a diagnostikovat problémy. **Přijetím Update 2 povolíte, abychom mohli poskytovat tato proaktivní podpora**.    

## <a name="issues-fixed-in-update-2"></a>Chyby v Update 2
Následující tabulka obsahuje souhrn problémy, které jsme vyřešili v aktualizace 2.    

| Ne. | Funkce | Problém | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- |
| 1 |Síťová rozhraní |Po upgradu na Update 1 služby StorSimple Manager oznámila, že porty Data2 a Data3 se nezdařilo u jednoho řadiče. Tento problém byl opraven. |Ano |Ne |
| 2 |Aktualizace |Po upgradu na Update 1 zvukové poplašné výstrahy došlo k chybě na portálu Azure classic na několika zařízeních. Tento problém byl opraven. |Ano |Ne |
| 3 |Openstack ověřování |Při použití Openstack jako poskytovatele cloudových služeb, obdržíte chybu, že řetězec vašeho cloudu ověřování byla příliš dlouhá. To byl opraven. |Ano |Ne |

## <a name="known-issues-in-update-2"></a>Známé problémy v aktualizaci Update 2
Následující tabulka obsahuje souhrn známých problémů v této verzi.

| Ne. | Funkce | Problém | Komentáře nebo alternativní řešení | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk kvora |Ve výjimečných případech Pokud většinu disků ve skříni EBOD 8600 zařízení odpojeny, což vede k žádné disk kvora, pak fondu úložiště přejde do režimu offline. Zůstane offline, i když jsou znovu připojeny disky. |Je potřeba restartovat zařízení. Pokud potíže potrvají, kontaktujte prosím Microsoft Support pro další kroky. |Ano |Ne |
| 2 |Nesprávný řadiče ID |Když se provádí nahrazení řadiče, může řadič 0 zobrazí jako řadič 1. Při nahrazení řadiče při načtení bitovou kopii z uzlu sdílené ID řadič může zobrazují původně jako ID partnera řadiče. Ve výjimečných případech může být toto chování vidět také po restartu systému. |Není třeba žádné akce uživatele. Tato situace bude automaticky vyřešen po dokončení nahrazení řadiče. |Ano |Ne |
| 3 |Účty úložiště |Pomocí služby úložiště odstranit účet úložiště se o nepodporovaný scénář. To povede k situaci, ve kterém nelze načíst data uživatele. | |Ano |Ano |
| 4 |Převzetí služeb při selhání zařízení |Více převzetí služeb při selhání kontejner svazků ze stejného zdrojového zařízení do jiné cílové zařízení není podporována. Převzetí služeb při selhání z jednoho neaktivní zařízení do více zařízení bude kontejnery svazků na prvním převzal zařízení dojít ke ztrátě dat vlastnictví. Po takové převzetí služeb při selhání, budou tyto kontejnery svazků zobrazí nebo chovat jinak při zobrazení na portálu Azure classic. | |Ano |Ne |
| 5 |Instalace |Během StorSimple adaptéru pro instalaci služby SharePoint je třeba zadat IP zařízení v pořadí pro instalaci úspěšně dokončit. | |Ano |Ne |
| 6 |Webový proxy server |Pokud vaše konfigurace webového proxy serveru má zadaný protokol HTTPS, bude mít vliv na komunikaci služby zařízení a zařízení přejde do režimu offline. Podpora balíčky se budou generovat také v procesu spotřebovávat značné množství prostředků vašeho zařízení. |Ujistěte se, že má adresu URL webového proxy serveru HTTP jako zadaný protokol. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](storsimple-configure-web-proxy.md). |Ano |Ne |
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
| 20 |Místně vázaných svazků |Pokud pro místně vázaný svazek převodem vrstvený svazek (vytvořený a klonovaný s aktualizace 1.2 nebo starší) a vaše zařízení není dostatek místa nebo je dispozici výpadku cloudu, může být poškozena clone(s). |K tomuto problému dochází pouze pro svazky, které byly vytvořené a klonovaný s před aktualizací 2 softwaru. To by měl být nepravidelným scénář. | | |
| 21 |Převod svazku |Nelze aktualizovat ACRs připojené ke svazku v průběhu převodu svazku (víceúrovňová pro místně vázaný nebo naopak). Aktualizace ACRs může mít za následek poškození dat. |V případě potřeby aktualizujte ACRs před převodu svazku a neprovádějte žádné další aktualizace ACR během převodu. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Řadiče a aktualizace firmwaru v aktualizaci Update 2
Tato verze aktualizuje ovladače a firmware disku na svém zařízení.

* Další informace o LSI firmware aktualizace, najdete v článku znalostní báze Microsoft Knowledge base 3121900. 
* Další informace o disku firmware aktualizace, najdete v článku znalostní báze Microsoft Knowledge base 3121899.

## <a name="virtual-device-updates-in-update-2"></a>Aktualizace virtuální zařízení v aktualizaci Update 2
Tuto aktualizaci nelze nainstalovat na virtuální zařízení. Nové virtuální zařízení bude potřeba vytvořit. 

## <a name="next-step"></a>Další krok
Zjistěte, jak [instalaci aktualizace 2](storsimple-install-update-2.md) zařízení StorSimple.

