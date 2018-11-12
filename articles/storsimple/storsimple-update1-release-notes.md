---
title: Poznámky k verzi StorSimple 8000 Series Update 1.2 | Dokumentace Microsoftu
description: Popisuje nové funkce, problémy a řešení pro StorSimple 8000 Series Update 1.2.
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
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258982"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Aktualizace 1.2 zpráva k vydání verze pro vaše zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Následující poznámky k verzi popisují nové funkce a identifikovat kritické otevřené problémy pro StorSimple 8000 Series Update 1.2. Obsahují také seznam softwaru StorSimple, ovladačů a aktualizace firmwaru disku jsou v ní obsažené. 

Aktualizace 1.2 lze použít na jakékoli zařízení StorSimple s vydání (GA), aktualizace 0.1, aktualizace 0.2 nebo software Update 0.3. Aktualizace 1.2 není k dispozici, pokud vaše zařízení používá aktualizaci 1 nebo Update 1.1. Pokud vaše zařízení používá verzi (GA), [obraťte se na Microsoft Support](storsimple-contact-microsoft-support.md) pomůcku, vám pomůže s instalací této aktualizace.

Následující tabulka uvádí verze softwaru zařízení odpovídající aktualizace 1, 1.1 a 1.2.

| Pokud aktualizace... | Toto je verzi vašeho zařízení. |
| --- | --- |
| Aktualizace 1.2 |6.3.9600.17584 |
| Aktualizace 1.1 |6.3.9600.17521 |
| Update 1.0 |6.3.9600.17491 |

Přečtěte si prosím informace obsažené v poznámkách k verzi, než nasadíte aktualizace v řešení StorSimple. Další informace najdete v tématu Jak [instalace aktualizace 1.2 na zařízení StorSimple](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Trvá přibližně 5 až 10 hodin k instalaci této aktualizace (včetně aktualizací Windows). 
> * Aktualizace 1.2 je software, LSI ovladač a aktualizace firmwaru disku. Pokud chcete nainstalovat, postupujte podle pokynů v [instalace aktualizace 1.2 na zařízení StorSimple](storsimple-install-update-1.md).
> * Pro nové verze, nemusí zobrazit aktualizace okamžitě, protože děláme postupné zavádění aktualizací. Kontrola aktualizací znovu za několik dnů se brzy bude dostupná.
> 
> 

## <a name="whats-new-in-update-12"></a>Co je nového v aktualizaci 1.2
Tyto funkce poprvé objevily s aktualizací Update 1, který byl zpřístupněn pro omezenou sadu uživatelů. S vydáním aktualizace 1.2 většina uživatelů StorSimple zobrazoval se vám následující nové funkce a vylepšení:

* **Migrace z řad 5000 – 7000 pro zařízení 8000 series** – tato verze přináší nové funkci migrace, který umožňuje uživatelům zařízení řad 5000 – 7000 migrovat svoje data pro fyzická zařízení řady StorSimple 8000 nebo virtuálního StorSimple zařízení. Funkce migrace má dvě klíčové prvky:                                                                  
  
  * **Kontinuita podnikových procesů**, tím, že migrace existujících dat v zařízení řad 5000 – 7000 tak, aby zařízení řady 8000.
  * **Vylepšené funkce nabídek zařízení řady 8000**, jako je například efektivní centralizovaná správa více zařízení prostřednictvím služby StorSimple Manager, lepší třídu hardwaru a aktualizace firmwaru, virtuální zařízení, data mobility Funkce a v budoucích plán.
    
    Odkazovat [Průvodce migrací](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) pro podrobnosti o tom, jak migrovat StorSimple řad 5000 – 7000 zařízení řady 8000. 
* **Dostupnost na portálu Azure Government** – StorSimple je nyní k dispozici na portálu Azure Government. V tématu Jak [nasazení zařízení StorSimple na portálu Azure Government](storsimple-deployment-walkthrough-gov.md).
* **Podpora dalších poskytovatelů cloudových služeb** – jiných poskytovatelů cloudových služeb podporované jsou Amazon S3, Amazon S3 s RRS, HP a OpenStack (beta verze).
* **Aktualizace na nejnovější rozhraní API úložiště** – v této verzi StorSimple je aktualizovaná na nejnovější rozhraní API služby Azure Storage. Zařízení StorSimple 8000 series, na kterých běží software verze než Update 1 (verze, 0.1, 0.2 a 0.3) musí používat verze rozhraní API služeb úložiště Azure starší než 17. července 2009. Jak je uvedeno v aktualizovaném [oznámení o odebrání verze služby úložiště](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), od 1. srpna 2016 přestanou používat tato rozhraní API. Je nutné použít StorSimple 8000 Series Update 1 před 1. srpna 2016. Pokud k tomu, zařízení StorSimple přestane fungovat správně.
* **Podpora pro Zónově redundantní úložiště (ZRS)** – v upgradu na nejnovější verzi rozhraní API úložiště řady StorSimple 8000 series bude podporovat Zónově redundantní úložiště (ZRS) kromě místně redundantní úložiště (LRS) a geograficky redundantní úložiště (GRS ). Projít tento [článku o možnostech redundance služby Azure Storage](../storage/common/storage-redundancy.md) ZRS podrobnosti.
* **Vylepšené prostředí pro počáteční nasazení a aktualizace** – v této verzi byly vylepšeny procesu instalace a aktualizace. Instalace pomocí Průvodce instalací je vylepšená k poskytnutí zpětné vazby pro uživatele, pokud jsou nesprávná konfigurace sítě a nastavení brány firewall. Byly zadány dalších diagnostických rutin vám pomůže při řešení potíží s síťové zařízení. Zobrazit [nasazení článek pro řešení potíží](storsimple-troubleshoot-deployment.md) Další informace o nových diagnostických rutin používají k řešení potíží.

## <a name="issues-fixed-in-update-12"></a>Chyby opravené v aktualizace 1.2
Následující tabulka obsahuje souhrn problémy, které byly opraveny v aktualizacích 1.2, 1.1 a 1.    

| Ne. | Funkce | Problém | Opraveno v aktualizaci | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Prostředí Windows PowerShell pro StorSimple |Když uživatel vzdálený přístup zařízení StorSimple pomocí prostředí Windows PowerShell pro StorSimple a potom spustit Průvodce instalací, selhání došlo k co nejdříve IP se vstupní Data 0. Tato chyba je opravená ve verzi Update 1. |Update 1 |Ano |Ano |
| 2 |Obnovení do výrobního nastavení |V některých případech se při provedení obnovení do výrobního nastavení zařízení StorSimple se zablokuje a zobrazí tuto zprávu: **resetování k objektu pro vytváření probíhá (fáze 8)**. K tomu dojít, pokud stisknutí kombinace kláves CTRL + C, zatímco rutina probíhala. Tato chyba je opravená. |Update 1 |Ano |Ne |
| 3 |Obnovení do výrobního nastavení |Po obnovení objekt factory kontroleru, který selhal duální bylo povoleno pokračovat s registrací zařízení. Kvůli tomu nepodporované konfiguraci systému. Ve verzi Update 1 se zobrazí chybová zpráva a registrace je blokována v zařízení, že má neúspěšné obnovení továrního nastavení. |Update 1 |Ano |Ne |
| 4 |Obnovení do výrobního nastavení |V některých případech byly vyvolány neshoda falešně pozitivní výstrahy. Nesprávný neshoda výstrahy se vygeneruje už na zařízení se softwarem Update 1. |Update 1 |Ano |Ne |
| 5 |Obnovení do výrobního nastavení |Pokud při obnovení továrního nastavení byl přerušen před dokončením, zařízení zadat režim obnovení a nepovolil vám přístup k prostředí Windows PowerShell pro StorSimple. Tato chyba je opravená. |Update 1 |Ano |Ne |
| 6 |Zotavení po havárii |Byl opraven chybu zotavení po havárii, ve které zotavení po Havárii selže během zjišťování záloh na cílovém zařízení. |Update 1 |Ano |Ano |
| 7 |Monitorovacích indikátorů LED |V některých případech monitorovacích indikátorů LED zádi zařízení neobsahovala správný stav. Modré Indikátor je vypnuté. DATA 0 a 1 LED dat byly bliká, i když nebyly nakonfigurovány těchto rozhraní. Opravili jsme problém a monitorování indikátorů LED teď ukazují správný stav. |Update 1 |Ano |Ne |
| 8 |Monitorovacích indikátorů LED |V některých případech po použití aktualizace 1, modrý indikátor na aktivním řadiči vypnuté a díky tomu je obtížné identifikace aktivního kontroleru. Tento problém byl vyřešen v této vydané verzi opravy. |Aktualizace 1.2 |Ano |Ne |
| 9 |Síťová rozhraní |V předchozích verzích se může zařízení StorSimple nakonfigurovanou bránu nesměrovatelných přejdou do režimu offline. Metriky rozhraní data 0 byl v této verzi provedeny nejnižší; Proto i v případě, že další síťová rozhraní jsou povolenou podporu cloudu, veškerý provoz cloud ze zařízení, budou směrovány prostřednictvím Data 0. |Update 1 |Ano |Ano |
| 10 |Zálohování |Opravili jsme chybu ve verzi Update 1, která způsobila zálohování selhávat po 24 dní ve vydané verzi opravy aktualizace 1.1. |Aktualizace 1.1 |Ano |Ano |
| 11 |Zálohování |Chyby v předchozích verzích za následek nízký výkon pro cloudové snímky s nízkou změnu sazeb. Tato chyba byla opravena v této vydané verzi opravy. |Aktualizace 1.2 |Ano |Ano |
| 12 |Aktualizace |Opravili jsme chybu ve verzi Update 1, která hlásí selhání upgradu a způsobilo řadiče přejde do režimu obnovení, v této vydané verzi opravy. |Aktualizace 1.2 |Ano |Ano |

## <a name="known-issues-in-update-12"></a>Známé problémy v aktualizace 1.2
V následující tabulce je uveden seznam známých problémů v této verzi.

| Ne. | Funkce | Problém | Komentáře/řešení | Platí pro fyzické zařízení | Platí pro virtuální zařízení |
| --- | --- | --- | --- | --- | --- |
| 1 |Disk kvora |Ve výjimečných případech Pokud Většina disků ve skříni EBOD 8600 zařízení jsou odpojené výsledkem žádný disk kvora fondu úložiště budou offline. Zůstane offline, i v případě, že jsou znovu připojeny disky. |Je potřeba restartovat zařízení. Pokud se problém nevyřeší, obraťte se o dalších krocích Microsoft Support. |Ano |Ne |
| 2 |ID nesprávné kontroleru |Při provedení nahrazení řadiče řadiče 0 může zobrazit jako řadič 1. Při nahrazení řadiče na obrázku je načtena z rovnocenný uzel ID adaptéru mohl zobrazit zpočátku jako ID partnera kontroleru. Ve výjimečných případech toto chování může také projeví po restartu systému. |Není nutná žádná akce uživatele. Tato situace vyřeší se sám po dokončení nahrazení řadiče. |Ano |Ne |
| 3 |Účty úložiště |Použití služby úložiště pro odstranění účtu úložiště se o nepodporovaný scénář. To povede k situaci, ve kterém nelze načíst data uživatele. |Ano |Ano | |
| 4 |Převzetí služeb při selhání zařízení |Více převzetí služeb při selhání služby kontejneru svazků ze stejného zdrojového zařízení do jiné cílové zařízení se nepodporuje. Kontejnery svazků na první převzetí služeb zařízení při selhání dojít ke ztrátě vlastnictví dat způsobí, že zařízení převzetí služeb při selhání z jednoho neaktivní zařízení na různých zařízeních. Tyto kontejnery svazků se po selhání, zobrazí nebo chovat jinak při prohlížení na portálu Azure classic. | |Ano |Ne |
| 5 |Instalace |Během StorSimple Adapter pro instalaci služby SharePoint je třeba zadat zařízení IP, aby se instalace úspěšně dokončit. | |Ano |Ne |
| 6 |Webový proxy server |Pokud vaše konfigurace webového proxy serveru má jako zadaný protokol HTTPS, bude mít vliv na vaši komunikaci služby zařízení a zařízení bude přejdou do režimu offline. Podpora balíčky také vygeneruje v procesu spotřebovávat značné množství prostředků vašeho zařízení. |Ujistěte se, že má adresu URL webového proxy serveru HTTP jako zadaný protokol. Další informace najdete v článku [Konfigurace webového proxy serveru pro zařízení](storsimple-configure-web-proxy.md). |Ano |Ne |
| 7 |Webový proxy server |Pokud nakonfigurujete a povolíte webový proxy server na registrovaná zařízení, je potřeba restartovat aktivní kontroler na vašem zařízení. | |Ano |Ne |
| 8 |Cloud vysokou latencí a vysokou vstupně-výstupní úlohy |Když v zařízení StorSimple dojde ke kombinaci komponent cloudu velmi vysoké latenci (pořadí sady sekund) a vysoké vstupně-výstupní úlohy, svazky zařízení přejde do ke špatnému stavu a vstupně-výstupních operací může selhat s chybou "zařízení není připraveno". |Je potřeba ručně restartovat řadiče zařízení nebo převzetí služeb zařízení k této situace. |Ano |Ne |
| 9 |Azure PowerShell |Když použijete rutinu StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - nejprve 1 - Wait** vyberte první objekt tak, že můžete vytvořit nový **ke kontejneru svazků** objekt, rutina vrátí všechny objekty. |Zabalit rutinu v závorkách následujícím způsobem: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - First 1 - Wait** |Ano |Ano |
| 10 |Migrace |Při předávání několika kontejnerů svazků pro migraci, je přesné jenom pro první kontejner svazků odhadovaným časem doručení pro poslední zálohy. Kromě toho paralelní migraci se spustí po první 4 zálohy v prvním kontejner svazků se migrují. |Doporučujeme migrovat jeden kontejner svazků najednou. |Ano |Ne |
| 11 |Migrace |Po obnovení svazky nejsou přidány do zásad zálohování nebo skupinu virtuálního disku. |Je potřeba přidat tyto svazky do zásady zálohování k vytvoření zálohy. |Ano |Ano |
| 12 |Migrace |Po dokončení migrace se nesmí řady 5000/7000 zařízení přístup k kontejnery migrovaná data. |Doporučujeme odstranit kontejnery migrovaná data po migraci je kompletní a potvrzené. |Ano |Ne |
| 13 |Klonování a zotavení po Havárii |Zařízení StorSimple s aktualizací 1 nelze klonovat nebo provést zotavení po havárii do zařízení se systémem 1 před aktualizací softwaru. |Budete muset aktualizovat cílové zařízení k aktualizaci 1 umožňuje tyto operace |Ano |Ano |
| 14 |Migrace |Konfigurace zálohování pro migrace mohou být neúspěšné zařízení řady 5000-7000 po skupin svazků s žádné přidružené svazky. |Odstranit všechny skupiny prázdný svazek s žádné přidružené svazky a pak zkuste zálohování zopakovat konfiguraci. |Ano |Ne |

## <a name="physical-device-updates-in-update-12"></a>Aktualizace fyzické zařízení v aktualizace 1.2
Pokud oprava aktualizace 1.2 se použijí na fyzického zařízení (verze starší než Update 1 s), verze softwaru se změní na 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Kontroler a aktualizace firmwaru v aktualizace 1.2
Tato verze aktualizuje ovladače a firmware disku na vašem zařízení.

* Další informace o aktualizace kontroleru SAS najdete v tématu [aktualizace 1 pro kontrolerů LSI SAS v Microsoft Azure StorSimple Appliance](https://support.microsoft.com/kb/3043005). 
* Další informace o aktualizaci firmwaru disku najdete v tématu [firmwaru disku aktualizace 1 pro Microsoft Azure StorSimple Appliance](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Aktualizace virtuálního zařízení v aktualizace 1.2
Tato aktualizace nejde použít u virtuálního zařízení. Nové virtuální zařízení bude potřeba vytvořit. 

## <a name="next-steps"></a>Další postup
* [Instalace aktualizace 1.2 na zařízení s](storsimple-install-update-1.md).

