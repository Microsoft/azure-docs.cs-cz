---
title: Zpráva k vydání verze StorSimple 8000 Series Update 1,2 | Microsoft Docs
description: Popisuje nové funkce, problémy a alternativní řešení pro StorSimple 8000 Series Update 1,2.
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
ms.openlocfilehash: 17e20048c4cb4de2be6fe36be100b472f0b8ee73
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459959"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Zpráva k vydání verze 1,2 pro zařízení s StorSimple 8000 series

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikují důležité otevřené problémy pro StorSimple 8000 Series Update 1,2. Obsahují také seznam aktualizací StorSimple softwaru, ovladačů a firmwaru disku, které jsou součástí této verze. 

Aktualizaci 1,2 lze použít pro jakékoli zařízení StorSimple s verzí (GA), aktualizaci 0,1, aktualizaci 0,2 nebo aktualizace softwaru 0,3. Aktualizace 1,2 není k dispozici, pokud je na zařízení spuštěná aktualizace 1 nebo aktualizace 1,1. Pokud na zařízení běží verze (GA), obraťte se prosím na [Podpora Microsoftu](storsimple-contact-microsoft-support.md) , který vám pomůže s instalací této aktualizace.

V následující tabulce jsou uvedené verze softwaru pro zařízení, které odpovídají aktualizacím 1, 1,1 a 1,2.

| Při spuštění aktualizace... | Toto je verze softwaru vašeho zařízení. |
| --- | --- |
| Aktualizace 1.2 |6.3.9600.17584 |
| Aktualizace 1.1 |6.3.9600.17521 |
| Aktualizace 1,0 |6.3.9600.17491 |

Před nasazením aktualizace do řešení StorSimple si prosím přečtěte informace uvedené v poznámkách k verzi. Další informace najdete v tématu [instalace aktualizace 1,2 na zařízení StorSimple](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Instalace této aktualizace (včetně aktualizací systému Windows) trvá přibližně 5-10 hodin. 
> * Aktualizace 1,2 obsahuje software, ovladač LSI a aktualizace firmwaru disku. Pokud ho chcete nainstalovat, postupujte podle pokynů v článku [instalace aktualizace 1,2 na zařízení StorSimple](storsimple-install-update-1.md).
> * Pro nové verze se aktualizace nemusí okamžitě zobrazovat, protože provádíme dvoufázové zavedení aktualizací. Vyhledá aktualizace za několik dní znovu, protože budou brzy k dispozici.
> 
> 

## <a name="whats-new-in-update-12"></a>Co je nového v aktualizaci 1,2
Tyto funkce byly nejprve vydány s aktualizací Update 1, která byla k dispozici pro omezené skupiny uživatelů. V případě verze Update 1,2 se většina uživatelů StorSimple zobrazí v následujících nových funkcích a vylepšeních:

* **Migrace z 5000-7000 řad na zařízení 8000 series** – Tato verze zavádí novou funkci migrace, která umožňuje uživatelům zařízení StorSimple 5000-7000 Series migrovat svá data do fyzického zařízení StorSimple 8000 Series nebo virtuálního zařízení. Funkce migrace má dvě pozice pro klíčové hodnoty:                                                                  
  
  * **Provozní kontinuita**tím, že umožňuje migrovat stávající data na zařízeních řady 5000-7000 až 8000 series.
  * **Vylepšené nabídky funkcí řady 8000**, jako je například efektivní Centralizovaná správa více zařízení prostřednictvím služby StorSimple Manager, lepšího druhu hardwaru a aktualizovaného firmwaru, virtuálních zařízení, mobility dat a funkcí v budoucím plánu.
    
    Podrobnosti o migraci řady StorSimple 5000-7000 na zařízení 8000 series najdete v [Průvodci migrací](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) . 
* **Dostupnost na portálu Azure Government** – StorSimple je teď k dispozici na portálu Azure Government. Další informace najdete [v tématu nasazení zařízení StorSimple na portálu Azure Government](storsimple-deployment-walkthrough-gov.md).
* **Podpora jiných poskytovatelů cloudových služeb** – podporovaná ostatní poskytovatelé cloudových služeb jsou Amazon S3, Amazon S3 s použitím prostředků RR, HP a OpenStack (beta).
* **Aktualizace na nejnovější rozhraní API úložiště** – v této verzi jsme StorSimple aktualizovali na nejnovější rozhraní api služby Azure Storage. Na zařízeních řady StorSimple 8000, na kterých běží předběžná aktualizace 1 verze softwaru (verze, 0,1, 0,2 a 0,3), používají verze rozhraní API služby Azure Storage starší než 17. července 2009. Jak je uvedeno v aktualizovaném [oznámení o odebrání verzí služby úložiště](https://docs.microsoft.com/archive/blogs/windowsazurestorage/microsoft-azure-storage-service-version-removal-update-extension-to-2016), od 1. srpna 2016 budou tato rozhraní API zastaralá. Je potřeba, abyste před 1. srpna 2016 používali aktualizaci řady StorSimple 8000 Series Update 1. Pokud to neuděláte, zařízení StorSimple přestanou fungovat správně.
* **Podpora pro zónu redundantního úložiště (ZRS)** – při upgradu na nejnovější verzi rozhraní API úložiště bude řada StorSimple 8000 podporovat úložiště ZRS () Kromě místně redundantního úložiště (LRS) a geograficky redundantního úložiště (GRS). Podrobnosti o ZRS najdete [v tomto článku týkajícím se Azure Storage možností redundance](../storage/common/storage-redundancy.md) .
* **Rozšířené počáteční nasazení a možnosti aktualizace** – v této verzi byly vylepšeny procesy instalace a aktualizace. Instalace prostřednictvím Průvodce instalací je vylepšena, aby poskytla uživateli zpětnou vazbu, pokud není správná konfigurace sítě a nastavení brány firewall. K dispozici jsou další diagnostické rutiny, které vám pomůžou při řešení potíží se sítí zařízení. Další informace o nových rutinách pro diagnostiku používaných při řešení potíží najdete v [článku věnovaném řešení potíží s nasazením](storsimple-troubleshoot-deployment.md) .

## <a name="issues-fixed-in-update-12"></a>Problémy opravené v aktualizaci 1,2
Následující tabulka poskytuje souhrn problémů vyřešených v aktualizacích 1,2, 1,1 a 1.    

| No. | Příznak | Problém | Opraveno v aktualizaci | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell pro StorSimple |Když se uživatel vzdáleně přistupoval k zařízení StorSimple pomocí Windows PowerShell pro StorSimple a potom spustí Průvodce instalací, došlo k chybě hned po vstupu data 0 IP. Tato chyba je nyní opravena ve verzi Update 1. |Update 1 |Yes |Yes |
| 2 |Obnovení továrního nastavení |Když jste v některých případech provedli obnovení továrního nastavení, zařízení StorSimple se zablokoval a zobrazila se tato zpráva: **resetování na tovární nastavení probíhá (fáze 8)**. K tomu došlo, pokud jste při běhu rutiny stiskli kombinaci kláves CTRL + C. Tato chyba je nyní opravena. |Update 1 |Yes |No |
| 3 |Obnovení továrního nastavení |Po neúspěšném obnovení továrního nastavení s dvojím kontrolérem jste mohli pokračovat v registraci zařízení. Výsledkem je Nepodporovaná konfigurace systému. V Update 1 se zobrazí chybová zpráva a na zařízení s neúspěšnou resetováním továrního nastavení se zablokuje registrace. |Update 1 |Yes |No |
| 4 |Obnovení továrního nastavení |V některých případech byly vyvolány falešně pozitivní výstrahy neshody. Na zařízeních s aktualizací Update 1 se již negenerují nesprávné výstrahy neshody. |Update 1 |Yes |No |
| 5 |Obnovení továrního nastavení |Pokud bylo obnovení továrního nastavení přerušeno před dokončením, zařízení vstoupilo do režimu obnovení a nepovolilo vám přístup k Windows PowerShell pro StorSimple. Tato chyba je nyní opravena. |Update 1 |Yes |No |
| 6 |Zotavení po havárii |Došlo k opravené chybě zotavení po havárii (DR) při zjišťování zálohování na cílovém zařízení. |Update 1 |Yes |Yes |
| 7 |Monitorování diody LED |V některých případech monitorované indikátory LED na zadní straně zařízení neoznačovaly správný stav. Modrý indikátor LED byl vypnut. DATA 0 a diody dat 1 byly blikat, i když tato rozhraní nebyla nakonfigurována. Problém byl opraven a monitorování diod LED nyní indikuje správný stav. |Update 1 |Yes |No |
| 8 |Monitorování diody LED |V některých případech po použití aktualizace 1 se modré světlo na aktivním kontroleru vypne a díky tomu je obtížné identifikovat aktivní kontroler. Tento problém byl opravený v této verzi opravy. |Aktualizace 1.2 |Yes |No |
| 9 |Síťová rozhraní |V předchozích verzích mohl být zařízení StorSimple nakonfigurované s Nesměrovatelné bránou, které by bylo online. V této verzi se metrika směrování pro data 0 provedla nejnižší; Proto i v případě, že jsou v cloudu zapnutá jiná síťová rozhraní, veškerý cloudový provoz ze zařízení bude směrován přes data 0. |Update 1 |Yes |Yes |
| 10 |Zálohování |Chyba v Update 1, která způsobila selhání zálohování po 24 dnech, byla opravena ve verzi patch Update 1,1. |Aktualizace 1.1 |Yes |Yes |
| 11 |Zálohování |Výsledkem chyby v předchozích verzích byl špatný výkon pro cloudové snímky s nízkou mírou změn. Tato chyba byla opravena v této verzi opravy. |Aktualizace 1.2 |Yes |Yes |
| 12 |Aktualizace |Došlo k chybě v aktualizaci Update 1, která ohlásila selhání upgradu a způsobila, že řadiče přešly do režimu obnovení, který byl opraven v této verzi opravy. |Aktualizace 1.2 |Yes |Yes |

## <a name="known-issues-in-update-12"></a>Známé problémy v aktualizaci 1,2
Následující tabulka poskytuje souhrn známých problémů v této verzi.

| No. | Příznak | Problém | Komentáře a alternativní řešení | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Kvorum disku |Ve výjimečných případech dojde k odpojení většiny disků v EBOD skříni zařízení 8600, což způsobí nedostatečné diskové kvorum, ale fond úložiště bude offline. Zůstane v režimu offline i v případě, že jsou disky znovu připojeny. |Budete muset zařízení restartovat. Pokud se problém opakuje, kontaktujte prosím podpora Microsoftu pro další kroky. |Yes |No |
| 2 |Nesprávné ID kontroleru |Když se provede nahrazení řadiče, může se řadič 0 zobrazit jako kontroler 1. Když se při nahrazení řadiče načte z uzlu rovnocenného uzlu, ID kontroleru se může zpočátku zobrazit jako ID partnerského řadiče. Ve výjimečných případech se toto chování může zobrazit i po restartování systému. |Není vyžadována žádná akce uživatele. Tato situace se vyřeší sám po dokončení nahrazení kontroleru. |Yes |No |
| 3 |Účty úložiště |Použití služby úložiště k odstranění účtu úložiště je nepodporovaný scénář. To vede k situaci, kdy nelze načíst uživatelská data. |Yes |Yes | |
| 4 |Převzetí služeb při selhání zařízení |Víc převzetí služeb při selhání kontejneru svazků ze stejného zdrojového zařízení do různých cílových zařízení se nepodporuje. Převzetí služeb zařízení při selhání z jednoho nedoručeného zařízení na několik zařízení způsobí, že kontejnery svazků u prvního zařízení, které převezme služby při selhání, ztratí vlastnictví dat. Po takovém převzetí služeb při selhání se tyto kontejnery svazků zobrazí nebo se budou chovat jinak, než je zobrazíte na portálu Azure Classic. | |Yes |No |
| 5 |Instalace |Během instalace služby StorSimple Adapter pro SharePoint je potřeba zadat IP adresu zařízení, aby se instalace úspěšně dokončila. | |Yes |No |
| 6 |Webový proxy server |Pokud vaše konfigurace webového proxy serveru jako zadaného protokolu obsahuje HTTPS, bude ovlivněná komunikace mezi zařízením a zařízení bude offline. Balíčky podpory budou také vygenerovány v procesu a budou spotřebovávat významné prostředky na vašem zařízení. |Ujistěte se, že adresa URL webového proxy serveru má jako zadaný protokol HTTP. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](storsimple-configure-web-proxy.md). |Yes |No |
| 7 |Webový proxy server |Pokud nakonfigurujete a povolíte webový proxy server na registrovaném zařízení, budete muset na svém zařízení restartovat aktivní kontroler. | |Yes |No |
| 8 |Vysoká latence v cloudu a vysoká zátěž/výstup |Když zařízení StorSimple zaznamená kombinaci velmi vysoké latence v cloudu (v řádu sekund) a vysoké vstupně-výstupní úlohy, svazky zařízení přejdou do stavu sníženo a vstupně-výstupní operace se nemusí zdařit s chybou zařízení není připraveno. |V takovém případě budete muset ručně restartovat řadiče zařízení nebo provést převzetí služeb při selhání zařízení pro obnovení. |Yes |No |
| 9 |Azure PowerShell |Když použijete rutinu StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object-First 1-počkat** na výběr prvního objektu, abyste mohli vytvořit nový objekt **ke kontejneru svazků** , rutina vrátí všechny objekty. |Zabalte rutinu do závorek následujícím způsobem: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object-First 1-Wait** |Yes |Yes |
| 10 |Migrace |Pokud je pro migraci předána více kontejnerů svazků, je ETA pro nejnovější zálohování přesné pouze pro první kontejner svazků. Kromě toho se spustí paralelní migrace po migraci prvních 4 záloh v prvním kontejneru svazků. |Doporučujeme migrovat jeden kontejner svazků v jednom okamžiku. |Yes |No |
| 11 |Migrace |Po obnovení nebudou svazky přidány do zásad zálohování nebo do skupiny virtuálních disků. |Tyto svazky budete muset přidat do zásad zálohování, aby bylo možné vytvářet zálohy. |Yes |Yes |
| 12 |Migrace |Po dokončení migrace nesmí zařízení řady 5000/7000 přistupovat ke migrovaných datovým kontejnerům. |Po dokončení a potvrzení migrace doporučujeme odstranit migrované datové kontejnery. |Yes |No |
| 13 |Klonování a zotavení po havárii |Zařízení StorSimple se systémem Update 1 nemůže klonovat nebo provést zotavení po havárii na zařízení, na kterém běží software před aktualizací 1. |Aby bylo možné tyto operace, je třeba aktualizovat cílové zařízení na aktualizaci 1. |Yes |Yes |
| 14 |Migrace |Pokud existují skupiny svazků bez přidružených svazků, zálohování konfigurace pro migraci na zařízení řady 5000-7000 se nemusí zdařit. |Odstraňte všechny prázdné skupiny svazků bez přidružených svazků a opakujte zálohování konfigurace. |Yes |No |

## <a name="physical-device-updates-in-update-12"></a>Aktualizace fyzických zařízení v aktualizaci 1,2
Pokud je aktualizace 1,2 na fyzickém zařízení použita (verze se spouští před aktualizací Update 1), verze softwaru se změní na 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Aktualizace řadiče a firmwaru v aktualizaci 1,2
Tato verze aktualizuje ovladač a firmware disku na vašem zařízení.

* Další informace o aktualizaci řadiče SAS najdete [v části Update 1 pro řadiče LSI SAS v zařízení Microsoft Azure StorSimple](https://support.microsoft.com/kb/3043005). 
* Další informace o aktualizaci firmwaru disku naleznete v části [firmware disku Update 1 pro Microsoft Azure StorSimple zařízení](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Aktualizace virtuálních zařízení v aktualizaci Update 1,2
Tuto aktualizaci nelze použít pro virtuální zařízení. Nová virtuální zařízení bude potřeba vytvořit. 

## <a name="next-steps"></a>Další kroky
* [Na vaše zařízení nainstalujte aktualizaci 1,2](storsimple-install-update-1.md).

