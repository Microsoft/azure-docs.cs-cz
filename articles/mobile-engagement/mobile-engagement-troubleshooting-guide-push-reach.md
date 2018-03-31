---
title: Průvodce - Push nebo Reach odstraňováním potíží s Azure Mobile Engagement.
description: Řešení potíží s uživatelské interakce a oznámení v Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3f1886b7-1fdd-47f4-b6b0-d79f158d5ef3
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: bc94353e60288ba1acfb1855b3a7b416a03e73bc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-push-and-reach-issues"></a>Průvodce řešením potíží se službami Push a Reach
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Následují možných problémů se můžete setkat s Azure Mobile Engagement jak odesílá informace pro vaše uživatele.

## <a name="push-failures"></a>Push selhání
### <a name="issue"></a>Problém
* Oznámení nepodporují (v aplikaci z aplikace nebo obojí).

### <a name="causes"></a>Způsobí, že
* Kolikrát selhání nabízené je jako ukazatel toho, že není správně integraci Azure Mobile Engagement Reach a další funkce Azure Mobile engagementu nebo že upgradu je potřeba v sadě SDK oprava známému problému se nová platforma operačního systému nebo zařízení.
* Otestujte právě nabízení v aplikaci a právě aplikaci z nabízené k určení, zda se jedná o problém v aplikaci nebo aplikaci z.
* Z uživatelského rozhraní a rozhraní API v rámci řešení potíží, které další informace o chybě jsou dostupné otestujte obou místech.
* Mimo aplikaci nabízených oznámení nebude fungovat, pokud Azure Mobile Engagement a Reach jsou integrované v sadě SDK.
* Oznámení nebude fungovat, pokud certifikáty jsou neplatné nebo používají PRODUKČNÍMU vs. DEV správně (jenom iOS). (**Poznámka:** "mimo aplikaci" nabízená oznámení nemusí doručit iOS, pokud máte i vývoj (vývoj) a produkční (PRODUKČNÍMU) verzí aplikace nainstalován na stejném zařízení od token zabezpečení přidružený certifikát může být zrušena společností Apple. Chcete-li vyřešit tento problém, vývoj a PRODUKČNÍMU verze aplikace odinstalovat a znovu nainstalovat jenom jednu verzi na zařízení.)
* Mimo aplikaci nabízená počty jsou zpracovány jinak na různých platformách (iOS zobrazí menší informace než Android, pokud nativní oznámení jsou zakázány na zařízení, rozhraní API poskytne informace než uživatelského rozhraní pro nabízené statistiky).
* Mimo aplikaci můžete blokovat nabízených oznámení zákazníci na úrovni operačního systému (iOS a Android).
* Mimo aplikaci nabízených oznámení se zobrazí jako zakázané v uživatelském rozhraní Azure Mobile Engagement, pokud nejsou správně integrované, ale může selhat bezobslužně z rozhraní API.
* V aplikaci nabízených oznámení nebude fungovat, pokud Azure Mobile Engagement a Reach jsou integrované v sadě SDK.
* Nabízených oznámení GCM a ADM nebude fungovat, pokud Azure Mobile Engagement a konkrétní server jsou integrované v sadě SDK (jen Android).
* V aplikaci a mimo aplikaci by měla být nabízených oznámení testována samostatně k určení, zda se jedná o problém s nabízené nebo Reach.
* V aplikaci, kterou nabízených oznámení vyžadují, aby aplikace otevřete k přijetí.
* V aplikaci nabízených oznámení jsou často instalace filtrovat podle značky informace o aplikaci přihlášení nebo odhlášení.
* Je-li použít vlastní kategorii v Reach k zobrazení oznámení v aplikaci, je nutné postupovat správné životní cyklus oznámení, jinak oznámení nemusí být vymazán když uživatel zprávu zavřete.
* Pokud spustíte kampaň s žádné datum ukončení a zařízení obdrží v aplikaci oznámení, ale nemá není zobrazení se ještě, budou uživatel nadále přijímat oznámení při příštím přihlášení do aplikace i v případě, že ručně ukončení kampaně.
* Pro problémy s rozhraním API Push potvrďte Opravdu chcete použít rozhraní API Push místo rozhraní Reach API (protože rozhraní Reach API je častěji) a že nejsou složitá parametry "datová část" a "oznamovatelem".
* Testování kampaň nabízených pomocí obou zařízení, připojení přes Wi-Fi a 3G odstranit připojení k síti jako zdroj možných problémů.

## <a name="push-testing"></a>Push testování
### <a name="issue"></a>Problém
* Oznámení lze odesílat na určité zařízení podle ID zařízení.

### <a name="causes"></a>Způsobí, že
* Testovací zařízení jsou instalace pro každou platformu, ale způsobuje událost v aplikaci na testovací zařízení a hledáte ID zařízení na portálu by měly fungovat najít ID zařízení pro všechny platformy.
* Testovací zařízení zavedením IDFA vs. IDFV (jenom iOS).

## <a name="push-customization"></a>Push přizpůsobení
### <a name="issue"></a>Problém
* Advanced obsah, nebude fungovat položky (oznámení, prstence, zavibrovat, obrázek, atd.).
* Odkazy z nabízených oznámení nepodporují (mimo aplikaci, v aplikaci na webu, do umístění v aplikaci).
* Statistiky nabízené ukazují, že push nebyla odeslána tolik lidem podle očekávání (příliš mnoho nebo není dostatek).
* Push duplicitní a přijaté dvakrát.
* Nelze zaregistrovat testovací zařízení Azure Mobile Engagement sami (s vlastními produkčnímu nebo vývoj aplikací).

### <a name="causes"></a>Způsobí, že
* Propojení na konkrétní umístění v aplikaci vyžaduje "kategorie" (jen Android).
* Přímé propojení schémata přesměrovat uživatele do alternativního umístění po kliknutí na nabízených oznámení je třeba vytvořit v a přímo spravuje vaše aplikace a operačního systému zařízení není pomocí Mobile Engagement. (**Poznámka:** mimo aplikaci oznámení nelze odkaz přímo na v umístění aplikaci s iOS jako je tomu u Android.)
* Servery externí image musejí mít na používání protokolu HTTP "GET" a "HEAD" pro velký obrázek nabízených oznámení (jen Android) fungovat.
* V kódu můžete zakázat agenta Azure Mobile Engagement, když je otevřen klávesnici a mít kódu znovu aktivovat agenta Azure Mobile Engagement po zavření klávesnice tak, aby klávesnice nebude mít vliv na vzhled oznámení (jenom iOS).
* Některé položky nejsou funkční v testovací simulace, ale pouze skutečné kampaně (oznámení, prstence, zavibrovat, obrázek, atd.).
* Když použijete tlačítko pro "test" nabízených oznámení, zaprotokoluje se žádná data na straně serveru. Data se protokolují pouze pro skutečné nabízené kampaně.
* Snažte se určit problém, Poradce při potížích s: test, simulaci a skutečné kampaň vzhledem k tomu, že každý fungují trochu jinak.
* Doba, kterou vaše "v aplikace" a "kdykoliv" kampaně spuštění je naplánováno na můžete ovlivňuje doručení čísla, protože kampaň budou doručeny pouze uživatelé, kteří jsou "v aplikaci" průběhu kampaň (a uživatelé, kteří mají své zařízení nastavení pro příjem oznámení "mimo aplikaci").
* Rozdíly mezi jak pracovat mimo aplikaci oznámení Android a iOS je obtížné přímo Porovnat statistiku nabízené mezi Android a iOS verze vaší aplikace. Android poskytuje další úroveň oznámení informace operačního systému než iOS. Android sestavy, když je nativní oznámení přijatých, kliknutí na nebo odstranit v centru oznámení, ale iOS nehlásí tyto informace, pokud se po kliknutí na oznámení. 
* Hlavním důvodem, které jsou jiné než než kampaně reach "doručit" čísla pro různé je, že "v aplikace" a "mimo aplikaci" oznámení, se počítají jinak "stisknutí" čísla. "V aplikaci" oznámení zpracovává Mobile Engagement, ale "mimo aplikaci" oznámení zpracovává centru oznámení v operačním systému vašeho zařízení.

## <a name="push-targeting"></a>Push cílení
### <a name="issue"></a>Problém
* Součástí cílení nebude fungovat podle očekávání.
* Cílení na značky informace o aplikaci nebude fungovat podle očekávání.
* Cílení na geografického umístění, nebude fungovat podle očekávání.
* Jazykového nefungují podle očekávání.

### <a name="causes"></a>Způsobí, že
* Ujistěte se, že jste nahráli značky informace o aplikaci pomocí Azure Mobile Engagement uživatelského rozhraní nebo rozhraní API.
* Omezení rychlost nabízení nebo nabízené kvóta na úrovni aplikace, nebo omezení cílové skupiny na úrovni kampaň můžete zabránit osoby přijetí konkrétní push i v případě, že splňují vaše kritéria cílení. 
* Nastavení "Jazyk" je jiné než cílení založený na zemi nebo národní prostředí, které se také liší od cílení podle geografického umístění na telefonu umístění nebo umístění GPS.
* Je odeslána zpráva v jazyce"Výchozí" žádné zákazníkovi, který nemá jejich zařízení, nastavte na některou z alternativní jazyky, které zadáte.

## <a name="push-scheduling"></a>Push plánování
### <a name="issue"></a>Problém
* Plánování nabízené nebude fungovat dle očekávání, (odeslat příliš časnému nebo zpožděné).

### <a name="causes"></a>Způsobí, že
* Časová pásma může problémy s plánování, zvláště při používání koncoví uživatelé časové pásmo.
* Funkce Rozšířené nabízené zpozdit nabízených oznámení.
* Cílení na základě na telefon, který se může zpozdit nastavení (namísto značky informace o aplikaci) nabízených oznámení, protože Azure Mobile Engagement může být nutné požádat dat reálném čase phone před odesláním oznámení.
* Kampaně vytvořen bez koncové datum místně uložených nabízeného oznámení na zařízení a zobrazit ji při příštím otevření aplikace i v případě, že se tato kampaň ručně ukončí.
* Spouštění více než jeden kampaň ve stejnou dobu může trvat delší dobu ke kontrole vaší uživatelské základny (pokuste se spustit pouze jeden kampaň najednou s maximálně čtyři, také cílový jenom na aktivní uživatele tak, aby staré uživatele nemusí být kontrolována).
* Pokud použijete možnost "Ignorovat cílovou skupinu, nabízení se se uživatelům odešle přes rozhraní API" v části "Kampaň" kampaně Reach, kampaň se neodesílal automaticky, musíte ručně odesílání prostřednictvím rozhraní Reach API.
* Je-li použít vlastní kategorii v Reach k zobrazení oznámení v aplikaci, je nutné postupovat správné životní cyklus oznámení, jinak oznámení nemusí být vymazán když uživatel zprávu zavřete.

