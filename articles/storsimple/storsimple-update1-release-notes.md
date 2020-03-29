---
title: StorSimple 8000 Series Update 1.2 poznámky k verzi | Dokumenty společnosti Microsoft
description: Popisuje nové funkce, problémy a řešení pro aktualizaci 1.2 řady StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6c9aae87-6f77-44b8-b7fa-ebbdc9d8517c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 11138857e33eec0f854ddb61956ea24c858c49a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60530996"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Aktualizace 1.2 poznámky k verzi pro zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikují kritické otevřené problémy pro aktualizaci 1.2 řady StorSimple 8000. Obsahují také seznam aktualizací softwaru StorSimple, ovladače a diskového firmwaru, které jsou součástí této verze. 

Aktualizaci 1.2 lze použít na libovolné zařízení StorSimple se spuštěnou verzí (GA), aktualizací 0.1, aktualizací 0.2 nebo aktualizací 0.3. Aktualizace 1.2 není k dispozici, pokud je v zařízení spuštěna aktualizace 1 nebo aktualizace 1.1. Pokud je v zařízení spuštěna verze (GA), obraťte se na [podporu společnosti Microsoft,](storsimple-contact-microsoft-support.md) která vám pomůže s instalací této aktualizace.

V následující tabulce jsou uvedeny verze softwaru zařízení odpovídající aktualizacím 1, 1.1 a 1.2.

| Pokud běží aktualizace ... | jedná se o verzi softwaru vašeho zařízení. |
| --- | --- |
| Aktualizace 1.2 |6.3.9600.17584 |
| Aktualizace 1.1 |6.3.9600.17521 |
| Aktualizace 1.0 |6.3.9600.17491 |

Před nasazením aktualizace v řešení StorSimple si přečtěte informace obsažené v poznámkách k verzi. Další informace naleznete v tématu [jak nainstalovat aktualizaci 1.2 do zařízení StorSimple](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Instalace této aktualizace (včetně aktualizací systému Windows) trvá přibližně 5 až 10 hodin. 
> * Aktualizace 1.2 obsahuje aktualizace softwaru, ovladače LSI a firmwaru disku. Chcete-li nainstalovat, postupujte podle pokynů v [instalaci aktualizace 1.2 na zařízení StorSimple](storsimple-install-update-1.md).
> * U nových verzí se aktualizace nemusí zobrazit okamžitě, protože provádíme postupné zavádění aktualizací. Skenování aktualizace za pár dní znovu, protože tyto budou brzy k dispozici.
> 
> 

## <a name="whats-new-in-update-12"></a>Co je nového v aktualizaci 1.2
Tyto funkce byly poprvé vydány s aktualizací 1, která byla zpřístupněna omezené sadě uživatelů. S aktualizací 1.2 by většina uživatelů StorSimple viděla následující nové funkce a vylepšení:

* **Migrace z řady 5000-7000 na zařízení řady 8000** – Tato verze představuje novou funkci migrace, která umožňuje uživatelům zařízení řady StorSimple 5000-7000 migrovat svá data do fyzického zařízení řady StorSimple 8000 nebo virtuálního zařízení. Funkce migrace má dvě klíčové návrhy hodnoty:                                                                  
  
  * **Kontinuita provozu**tím, že umožňuje migraci stávajících dat na zařízeních řady 5000-7000 na zařízení řady 8000.
  * **Vylepšené nabídky funkcí zařízení řady 8000**, jako je efektivní centralizovaná správa více spotřebičů prostřednictvím služby StorSimple Manager, lepší třída hardwaru a aktualizovaného firmwaru, virtuálních zařízení, mobility dat a funkcí v budoucím plánu.
    
    Podrobnosti o migraci řady StorSimple 5000-7000 do zařízení řady 8000 naleznete v [průvodci migrací.](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) 
* **Dostupnost na portálu Azure Government Portal** – StorSimple je teď dostupná na portálu Azure Government. Podívejte se, jak [nasadit zařízení StorSimple na portálu Azure Government Portal](storsimple-deployment-walkthrough-gov.md).
* **Podpora pro ostatní poskytovatele cloudových služeb** - Dalšími podporovanými poskytovateli cloudových služeb jsou Amazon S3, Amazon S3 s RRS, HP a OpenStack (beta).
* **Aktualizace na nejnovější úložiště API** – S touto verzí StorSimple byla aktualizována na nejnovější azure storage service API. Zařízení řady StorSimple 8000 se spuštěnou verzí softwaru před aktualizací 1 (release, 0.1, 0.2 a 0.3) používají verze api služby Azure Storage Service starší než 17. Jak je uvedeno v aktualizovaném [oznámení o odebrání verzí služby Storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), do srpna 1, 2016, tato api budou zastaralé. Je nezbytně nutné použít aktualizaci řady StorSimple 8000 1 před srpnem 1, 2016. Pokud tak neučiníte, zařízení StorSimple přestanou fungovat správně.
* **Podpora zónově redundantního úložiště (ZRS)** – S upgradem na nejnovější verzi api úložiště bude řada StorSimple 8000 podporovat zónově redundantní úložiště (ZRS) kromě místně redundantního úložiště (LRS) a geograficky redundantního úložiště (GRS). Podrobnosti o ZRS najdete v tomto [článku o možnostech redundance úložiště Azure.](../storage/common/storage-redundancy.md)
* **Rozšířené počáteční nasazení a aktualizace –** V této verzi byly vylepšeny procesy instalace a aktualizace. Instalace prostřednictvím průvodce instalací je vylepšena tak, aby poskytovala uživateli zpětnou vazbu, pokud je konfigurace sítě a nastavení brány firewall nesprávná. Další diagnostické rutiny byly poskytnuty, které vám pomohou s odstraňováním potíží se sítí zařízení. Další informace o nových diagnostických rutinách používaných k řešení potíží naleznete v [článku o nasazení řešení potíží.](storsimple-troubleshoot-deployment.md)

## <a name="issues-fixed-in-update-12"></a>Problémy opravené v aktualizaci 1.2
Následující tabulka obsahuje souhrn problémů, které byly opraveny v aktualizacích 1.2, 1.1 a 1.    

| Ne. | Funkce | Problém | Opraveno v aktualizaci | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Prostředí Windows PowerShell pro StorSimple |Když uživatel vzdáleně přistupoval k zařízení StorSimple pomocí prostředí Windows PowerShell pro StorSimple a poté spustil průvodce instalací, došlo k chybě, jakmile byla zadána adresa IP data 0. Tato chyba je nyní opravena v aktualizaci 1. |Update 1 |Ano |Ano |
| 2 |Obnovení továrního nastavení |V některých případech při obnovení továrního nastavení se zařízení StorSimple zaseklo a zobrazilo tuto zprávu: **Probíhá obnovení továrního nastavení (fáze 8).** K tomu došlo, pokud jste během probíhajícírutiny stiskli kombinaci kláves CTRL+C. Tato chyba je nyní opravena. |Update 1 |Ano |Ne |
| 3 |Obnovení továrního nastavení |Po neúspěšném obnovení továrního nastavení duálního řadiče bylo povoleno pokračovat v registraci zařízení. Výsledkem byla nepodporovaná konfigurace systému. V aktualizaci 1 se zobrazí chybová zpráva a registrace je blokována na zařízení, které má selhání obnovení továrního nastavení. |Update 1 |Ano |Ne |
| 4 |Obnovení továrního nastavení |V některých případech byly vyvolány falešně pozitivní neshody výstrahy. Na zařízeních se spuštěnou aktualizací 1 již nebudou generovány nesprávné výstrahy neshody. |Update 1 |Ano |Ne |
| 5 |Obnovení továrního nastavení |Pokud obnovení továrního nastavení bylo přerušeno před dokončením, zařízení vstoupilo do režimu obnovení a neumožnilo přístup k prostředí Windows PowerShell pro StorSimple. Tato chyba je nyní opravena. |Update 1 |Ano |Ne |
| 6 |Zotavení po havárii |Chyba zotavení po havárii (DR) byla opravena v případě, že by se při zjišťování záloh na cílovém zařízení nezdaří zotavení po havárii. |Update 1 |Ano |Ano |
| 7 |Sledování LED diod |V některých případech monitorování LED diod na zadní straně přístroje nenaznačovalo správný stav. Modrá LED dioda byla vypnuta. DATA 0 a DATA 1 LED diody blikaly, i když tato rozhraní nebyla nakonfigurována. Problém byl opraven a monitorování LED diod nyní označují správný stav. |Update 1 |Ano |Ne |
| 8 |Sledování LED diod |V některých případech se po použití aktualizace 1 modré světlo na aktivním ovladači vypne, což ztěžuje identifikaci aktivního řadiče. Tento problém byl opraven v této verzi opravy. |Aktualizace 1.2 |Ano |Ne |
| 9 |Síťová rozhraní |V předchozích verzích může zařízení StorSimple nakonfigurované s nesměrovatelnou bránou přejít do offline. V této verzi byla metrika směrování pro data 0 nejnižší; proto i v případě, že jiná síťová rozhraní jsou povolena v cloudu, veškerý cloudový provoz ze zařízení bude směrován přes data 0. |Update 1 |Ano |Ano |
| 10 |Zálohování |Chyba v aktualizaci 1, která způsobila selhání záloh po 24 dnech, byla opravena v aktualizaci aktualizace 1.1. |Aktualizace 1.1 |Ano |Ano |
| 11 |Zálohování |Chyba v předchozích verzích vedla ke snížení výkonu pro snímky cloudu s nízkou mírou změn. Tato chyba byla opravena v této verzi opravy. |Aktualizace 1.2 |Ano |Ano |
| 12 |Aktualizace |V této verzi opravy byla opravena chyba v aktualizaci 1, která ohlásila neúspěšný upgrade a způsobila, že řadiče přejdou do režimu obnovení. |Aktualizace 1.2 |Ano |Ano |

## <a name="known-issues-in-update-12"></a>Známé problémy v aktualizaci 1.2
Následující tabulka obsahuje souhrn známých problémů v této verzi.

| Ne. | Funkce | Problém | Komentáře/řešení | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Diskové kvorum |Ve výjimečných případech pokud většina disků ve skříni EBOD zařízení 8600 jsou odpojeny, což nemá žádné kvorum disku, pak fond úložiště bude offline. Zůstane v offline i v případě, že jsou disky znovu připojeny. |Budete muset restartovat zařízení. Pokud problém přetrvává, obraťte se na podporu společnosti Microsoft pro další kroky. |Ano |Ne |
| 2 |Nesprávné ID řadiče |Při provedení výměny řadiče se může řadič 0 zobrazit jako řadič 1. Během nahrazení řadiče, když je bitová kopie načtena z uzlu peer, ID řadiče se může zpočátku zobrazit jako ID peer controller. Ve výjimečných případech toto chování může být také vidět po restartování systému. |Není vyžadována žádná akce uživatele. Tato situace se vyřeší sama po dokončení nahrazení řadiče. |Ano |Ne |
| 3 |Účty úložiště |Použití služby Úložiště k odstranění účtu úložiště je nepodporovaný scénář. To povede k situaci, ve které nelze načíst uživatelská data. |Ano |Ano | |
| 4 |Převzetí služeb při selhání zařízení |Více převzetí služeb při selhání kontejneru svazku ze stejného zdrojového zařízení do různých cílových zařízení není podporováno. Převzetí služeb při selhání zařízení z jednoho mrtvého zařízení na více zařízení způsobí, že kontejnery svazků na prvním selhání zařízení ztratí vlastnictví dat. Po takovém převzetí služeb při selhání se tyto kontejnery svazků zobrazí nebo se budou chovat jinak, když je zobrazíte na klasickém portálu Azure. | |Ano |Ne |
| 5 |Instalace |Během instalace aplikace StorSimple Adapter pro službu SharePoint je třeba zadat ip adresu zařízení, aby byla instalace úspěšně dokončena. | |Ano |Ne |
| 6 |Webový proxy server |Pokud je konfigurace webového proxy serveru jako zadaný protokol protokolem https, bude ovlivněna komunikace mezi zařízeními a službami a zařízení přejde do offline. Balíčky podpory budou také generovány v procesu, spotřebovávající významné prostředky na vašem zařízení. |Ujistěte se, že adresa URL webového proxy serveru má jako zadaný protokol protokol HTTP. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](storsimple-configure-web-proxy.md). |Ano |Ne |
| 7 |Webový proxy server |Pokud nakonfigurujete a povolíte webový proxy server na registrovaném zařízení, budete muset restartovat aktivní řadič v zařízení. | |Ano |Ne |
| 8 |Vysoká latence cloudu a vysoké vstupně-výstupní úlohy |Když vaše zařízení StorSimple narazí na kombinaci velmi vysoké latence cloudu (pořadí sekund) a vysoké vstupně-výstupní úlohy, svazky zařízení přejdou do zhoršeného stavu a vstupně-výstupních služeb může selhat s chybou "zařízení není připraveno". |Budete muset ručně restartovat řadiče zařízení nebo provést převzetí služeb při selhání zařízení k obnovení z této situace. |Ano |Ne |
| 9 |Azure PowerShell |Při použití Rutina StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** vybrat první objekt, takže můžete vytvořit nový **Objekt VolumeContainer,** rutina vrátí všechny objekty. |Zalomit rutinu do závorek takto: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Ano |Ano |
| 10 |Migrace |Při předání více kontejnerů svazku pro migraci, ETA pro nejnovější zálohování je přesné pouze pro první kontejner svazku. Paralelní migrace navíc začne po migraci prvních 4 záloh v prvním kontejneru svazku. |Doporučujeme migrovat jeden kontejner svazku najednou. |Ano |Ne |
| 11 |Migrace |Po obnovení nejsou svazky přidány do zásady zálohování nebo skupiny virtuálních disků. |Chcete-li vytvořit zálohy, budete muset tyto svazky přidat do zásad zálohování. |Ano |Ano |
| 12 |Migrace |Po dokončení migrace zařízení řady 5000/7000 nesmí přistupovat k migrované datové kontejnery. |Doporučujeme odstranit migrované datové kontejnery po dokončení migrace a potvrzené. |Ano |Ne |
| 13 |Klonování a dr. |Zařízení StorSimple se spuštěnou aktualizací 1 nemůže klonovat nebo provádět zotavení po havárii na zařízení se systémem předaktualizací 1. |Chcete-li povolit tyto operace, budete muset aktualizovat cílové zařízení na aktualizaci 1. |Ano |Ano |
| 14 |Migrace |Zálohování konfigurace pro migraci může selhat na zařízení řady 5000-7000, pokud existují skupiny svazků bez přidružených svazků. |Odstraňte všechny prázdné skupiny svazků bez přidružených svazků a opakujte zálohu konfigurace. |Ano |Ne |

## <a name="physical-device-updates-in-update-12"></a>Aktualizace fyzických zařízení v aktualizaci 1.2
Pokud je aktualizace opravy 1.2 použita na fyzické zařízení (spuštěné verze před aktualizací 1), verze softwaru se změní na 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Aktualizace řadiče a firmwaru v aktualizaci 1.2
Tato verze aktualizuje ovladač a firmware disku v zařízení.

* Další informace o aktualizaci řadiče SAS naleznete [v tématu Aktualizace 1 pro řadiče LSI SAS v zařízení Microsoft Azure StorSimple Appliance](https://support.microsoft.com/kb/3043005). 
* Další informace o aktualizaci firmwaru disku naleznete v [tématu Aktualizace firmwaru disku 1 pro zařízení Microsoft Azure StorSimple Appliance](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Aktualizace virtuálních zařízení v aktualizaci 1.2
Tuto aktualizaci nelze použít na virtuální zařízení. Bude třeba vytvořit nová virtuální zařízení. 

## <a name="next-steps"></a>Další kroky
* [Nainstalujte do zařízení aktualizaci 1.2](storsimple-install-update-1.md).

