---
title: Poznámky k verzi zařízení StorSimple 8000 řady aktualizace 1.2 | Microsoft Docs
description: Popisuje nové funkce, problémy a řešení pro zařízení StorSimple 8000 řady aktualizace 1.2.
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
ms.openlocfilehash: 67603c04cf7b5f45517d16620e5b79241aaae94d
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Aktualizovat 1.2 poznámky k verzi pro vaše zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikovat kritická otevřené problémy pro StorSimple 8000 řady aktualizace 1.2. Také obsahují seznam softwaru zařízení StorSimple, ovladačů a aktualizace firmwaru disku součástí této verze. 

Aktualizace 1.2 lze použít pro všechna zařízení StorSimple systémem verze (GA), Update 0.1, aktualizace 0,2 nebo Update 0.3 softwaru. Aktualizace 1.2 není k dispozici, pokud vaše zařízení používá Update 1 nebo Update 1.1. Pokud vaše zařízení používá verzi (GA), [kontaktovat Microsoft Support](storsimple-contact-microsoft-support.md) vám pomůže s instalací této aktualizace.

Následující tabulka uvádí verze softwaru zařízení odpovídající aktualizace 1, 1.1 a 1.2.

| Pokud systém aktualizace... | Toto je vaše zařízení verze softwaru. |
| --- | --- |
| Aktualizace 1.2 |6.3.9600.17584 |
| Aktualizace 1.1 |6.3.9600.17521 |
| Aktualizace 1.0 |6.3.9600.17491 |

Přečtěte si informace obsažené v poznámkách k verzi před nasazením aktualizace v řešení StorSimple. Další informace najdete v tématu Jak [nainstalovat 1.2 aktualizace zařízení StorSimple](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Jak dlouho trvá přibližně 5 až 10 hodin k instalaci této aktualizace (včetně aktualizace systému Windows). 
> * Aktualizace 1.2 má softwaru, LSI ovladače a aktualizace firmwaru disku. Pokud chcete nainstalovat, postupujte podle pokynů v [nainstalovat 1.2 aktualizace zařízení StorSimple](storsimple-install-update-1.md).
> * Pro nové verze, nemusí zobrazit aktualizace okamžitě, protože provedeme postupné zavádění aktualizací. Kontrola aktualizací za pár dní znovu jako tyto brzy bude dostupná.
> 
> 

## <a name="whats-new-in-update-12"></a>Co je nového v aktualizaci 1.2
Tyto funkce byly prvního vydání s Update 1, který byl zpřístupněn pro omezenou sadu uživatelů. S verzí 1.2 aktualizace je většina uživatelů StorSimple zobrazen následující nové funkce a vylepšení:

* **Migrace z řady 5000 7000 zařízení řady 8000** – tato verze přináší nové funkce migrace, která umožňuje StorSimple 5000 7000 řady zařízení uživatelů k migraci svá data o fyzické zařízení řady StorSimple 8000 nebo virtuální zařízení. Funkci migrace má dvě klíčové funkce:                                                                  
  
  * **Kontinuita podnikových procesů**, povolením migrace existujících dat na zařízení řady 5000 7000 tak, aby zařízení řady 8000.
  * **Vylepšené funkce nabídky zařízení řady 8000**, jako je například efektivní centralizovaná správa více zařízení prostřednictvím služby StorSimple Manager, lepší třída hardwaru a aktualizovat firmware, virtuální zařízení, data mobility Funkce a v budoucích plán.
    
    Odkazovat [příručka k migraci](http://www.microsoft.com/download/details.aspx?id=47322) pro podrobnosti o tom, jak migrovat StorSimple řady 5000 7000 zařízení řady 8000. 
* **Dostupnost v portálu Azure Government** – StorSimple je nyní k dispozici na portálu Azure Government. V tématu Jak [nasazení zařízení StorSimple na portálu Azure Government](storsimple-deployment-walkthrough-gov.md).
* **Podpora pro ostatní poskytovatele cloudových služeb** – jiných poskytovatelů cloudových služeb podporované jsou Amazon S3, Amazon S3 s RRS, HP a OpenStack (beta).
* **Aktualizace na nejnovější rozhraní API úložiště** – v této verzi se StorSimple aktualizoval na nejnovější rozhraní API služby Azure Storage. Řadu zařízení StorSimple 8000 s verzí softwaru před aktualizací 1 (verze 0.1, 0.2 a 0.3) používají verze API služby úložiště Azure starší než 17 července 2009. Jak je uvedeno v aktualizaci [oznámení o odebrání verze služby úložiště](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), podle 1 srpna 2016, bude tato rozhraní API zastaralá. Je nutné, abyste použili řady 8000 StorSimple Update 1 před 1 srpna 2016. Pokud tak učinit, zařízení StorSimple přestane fungovat správně.
* **Podpora pro zóny redundantní úložiště (ZRS)** – upgrade na nejnovější verzi rozhraní API úložiště, bude podporovat řady StorSimple 8000 zóny redundantní úložiště (ZRS) kromě místně redundantní úložiště (LRS) a geograficky redundantní úložiště (GRS ). Pokus [článek na možnosti redundance úložiště Azure](../storage/common/storage-redundancy.md) podrobnosti ZRS.
* **Rozšířené počáteční nasazení a aktualizace prostředí** – v této verzi, instalace a aktualizace procesů vylepšily. Instalace pomocí Průvodce instalací je vyšší, k poskytnutí zpětné vazby pro uživatele, pokud jsou nesprávná konfigurace sítě a nastavení brány firewall. Byly zadány další diagnostiky rutiny vám pomůže při řešení potíží s síťové zařízení. Najdete v článku [řešení potíží s nasazení článku](storsimple-troubleshoot-deployment.md) Další informace o nové diagnostické rutiny používají k řešení potíží.

## <a name="issues-fixed-in-update-12"></a>Chyby v aktualizaci 1.2
Následující tabulka obsahuje souhrn problémy, které jsme vyřešili v aktualizace 1.2, 1.1 a 1.    

| Ne. | Funkce | Problém | Pevné v aktualizaci | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Prostředí Windows PowerShell pro StorSimple |Pokud je uživatel vzdálený přístup zařízení StorSimple pomocí Windows Powershellu pro StorSimple a pak spustit Průvodce instalací, došlo k co nejdříve Data 0 byl vstup IP havárie. Tato chyba je nyní opravena v aktualizaci 1. |Update 1 |Ano |Ano |
| 2 |Obnovení továrního nastavení |V některých případech, když jste provedli obnovení továrního nastavení, zařízení StorSimple se aktivovala zablokuje a zobrazí tato zpráva: **resetovat do výrobního probíhá (fáze 8)**. To došlo k během provádění rutiny stisknutím CTRL + C. Tato chyba je nyní opravena. |Update 1 |Ano |Ne |
| 3 |Obnovení továrního nastavení |Po objekt factory kontroleru selhání duální resetovat, měla povoleno pokračovat s registrací zařízení. Výsledkem nepodporované konfiguraci systému. V Update 1 se zobrazí chybová zpráva a registrace blokováno na zařízení, že má neúspěšné obnovit tovární nastavení. |Update 1 |Ano |Ne |
| 4 |Obnovení továrního nastavení |V některých případech byly vyvolány false kladné neshoda výstrahy. Nesprávný neshoda výstrahy se budou generovat už na zařízení se systémem Update 1. |Update 1 |Ano |Ne |
| 5 |Obnovení továrního nastavení |Pokud obnovení továrního nastavení byl přerušen před dokončením, zařízení zadali režimu obnovení a neumožňuje přístup k Windows Powershellu pro StorSimple. Tato chyba je nyní opravena. |Update 1 |Ano |Ne |
| 6 |Zotavení po havárii |Chyby zotavení po havárii byla opravena, ve kterém by zotavení po Havárii selhala při zjišťování záloh v cílové zařízení. |Update 1 |Ano |Ano |
| 7 |Monitorování LED |V některých případech monitorování LED zadní straně zařízení neobsahoval pokyn správný stav. Modré DIODU byl vypnut. DATA 0 a 1 LED dat byly přerušované, i když nebyla nakonfigurovaná tato rozhraní. Tento problém byl opraven a monitorování LED teď správný stav. |Update 1 |Ano |Ne |
| 8 |Monitorování LED |V některých případech po použití Update 1 modrý indikátor v aktivním řadiči vypnuté a díky tomu je obtížné určit řadič služby active. Tento problém byl opraven v této verzi opravy. |Aktualizace 1.2 |Ano |Ne |
| 9 |Síťová rozhraní |V předchozích verzích se může zařízení StorSimple pomocí směrovat brány nakonfigurovat přejít do režimu offline. V této verzi metriky rozhraní data 0 byl provedené nejnižší; Proto i v případě jiných síťových rozhraní povolenou podporu cloudu, veškerý provoz cloudu ze zařízení, budou směrovány prostřednictvím Data 0. |Update 1 |Ano |Ano |
| 10 |Zálohy |Oprava verze 1.1 aktualizace byl opraven chyby v Update 1, který chybu způsobil zálohy selhávat po 24 dní. |Aktualizace 1.1 |Ano |Ano |
| 11 |Zálohy |Chyby v předchozích verzích za následek nízký výkon pro cloudové snímky s nízkou změnu sazby. V tomto vydání opravy byl opraven této chyby. |Aktualizace 1.2 |Ano |Ano |
| 12 |Aktualizace |Chyby v Update 1, který ohlásil selhání upgradu a způsobila řadiče uvést do režimu obnovení, byl opraven v této verzi opravy. |Aktualizace 1.2 |Ano |Ano |

## <a name="known-issues-in-update-12"></a>Známé problémy v aktualizaci 1.2
Následující tabulka obsahuje souhrn známých problémů v této verzi.

| Ne. | Funkce | Problém | Komentáře nebo alternativní řešení | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk kvora |Ve výjimečných případech Pokud jsou většinu disků ve skříni EBOD 8600 zařízení odpojeny, což vede k žádné disk kvora, pak fondu úložiště bude offline. Zůstane offline, i když jsou znovu připojeny disky. |Je potřeba restartovat zařízení. Pokud potíže potrvají, kontaktujte prosím Microsoft Support pro další kroky. |Ano |Ne |
| 2 |Nesprávný řadiče ID |Když se provádí nahrazení řadiče, může řadič 0 zobrazí jako řadič 1. Při nahrazení řadiče při načtení bitovou kopii z uzlu sdílené ID řadič může zobrazují původně jako ID partnera řadiče. Ve výjimečných případech může být toto chování vidět také po restartu systému. |Není třeba žádné akce uživatele. Tato situace bude automaticky vyřešen po dokončení nahrazení řadiče. |Ano |Ne |
| 3 |Účty úložiště |Pomocí služby úložiště odstranit účet úložiště se o nepodporovaný scénář. To povede k situaci, ve kterém nelze načíst data uživatele. |Ano |Ano | |
| 4 |Převzetí služeb při selhání zařízení |Více převzetí služeb při selhání kontejner svazků ze stejného zdrojového zařízení do jiné cílové zařízení není podporována. Zařízení převzetí služeb při selhání z jednoho neaktivní zařízení do více zařízení bude kontejnery svazků na prvním převzal zařízení dojít ke ztrátě dat vlastnictví. Po takové převzetí služeb při selhání, budou tyto kontejnery svazků zobrazí nebo chovat jinak při zobrazení na portálu Azure classic. | |Ano |Ne |
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

## <a name="physical-device-updates-in-update-12"></a>Aktualizace fyzického zařízení v aktualizaci 1.2
Pokud oprava aktualizovat 1.2 se použije pro fyzického zařízení (verze starší než Update 1 s), verze softwaru se změní na 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Řadiče a aktualizace firmwaru v aktualizaci 1.2
Tato verze aktualizuje ovladače a firmware disku na svém zařízení.

* Další informace o aktualizaci řadiče SAS najdete v tématu [aktualizace 1 pro řadičů LSI SAS v Microsoft Azure StorSimple zařízení](https://support.microsoft.com/kb/3043005). 
* Další informace o aktualizaci firmwaru disku najdete v tématu [disku firmware aktualizace 1 pro Microsoft Azure StorSimple zařízení](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Aktualizace virtuální zařízení v aktualizaci 1.2
Tuto aktualizaci nelze nainstalovat na virtuální zařízení. Nové virtuální zařízení bude potřeba vytvořit. 

## <a name="next-steps"></a>Další postup
* [Nainstalujte aktualizace 1.2 zařízení](storsimple-install-update-1.md).

