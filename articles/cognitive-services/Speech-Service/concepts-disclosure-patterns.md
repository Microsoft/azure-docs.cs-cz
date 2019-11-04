---
title: Vzory návrhu zpřístupnění
titleSuffix: Azure Cognitive Services
description: Vzory návrhu a osvědčené postupy pro zveřejnění.
services: cognitive-services
author: angle
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: angle
ms.openlocfilehash: fde4a73dd4a95028f23563fb6a8ae36c0142fd39
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507316"
---
# <a name="disclosure-design-patterns"></a>Vzory návrhu zpřístupnění
Teď, když&#39;jste zjistili správnou [úroveň zveřejnění](concepts-disclosure-guidelines.md#disclosure-assessment) pro syntetické hlasové zážitky, je&#39;vhodný čas na prozkoumání potenciálních vzorů návrhu.
## <a name="overview"></a>Přehled
Existuje spektrum vzorů návrhu zpřístupnění, které můžete použít na syntetické hlasové prostředí. Pokud byl výsledek hodnocení zveřejnění "vysoké zpřístupnění", doporučujeme, abyste [**výslovně vyzrazeni**](#explicit-disclosure), což znamená, že navzájem komunikují původem syntetického hlasu. [**Implicitní zveřejnění**](#implicit-disclosure) zahrnuje hromádky a vzory interakce, které využívají hlasové funkce, ať už jsou požadované úrovně zveřejnění vysoké nebo nízké.
![spektrum způsobů zpřístupnění](media/responsible-ai/disclosure-patterns/affordances.png)






| Explicitní vzory zveřejnění                                                                                                                                                                                    | Implicitní vzory zveřejnění                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Transparentní Úvod](#transparent-introduction)<br> [Slovní transparentní Úvod](#verbal-transparent-introduction)<br>  [Explicitní Podtitulek](#explicit-byline)<br>  [Přizpůsobení a kalibrace](#customization-and-calibration)<br> [Odhalení rodičů](#parental-disclosure)<br> [Poskytnutí příležitostí k získání dalších informací o tom, jak byl hlas proveden](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Vyzrazení schopností](#capability-disclosure)<br>[Implicitní upozornění a zpětná vazba](#implicit-cues--feedback)<br> [Průhlednost konverzací](#conversational-transparency) |



Následující graf vám umožní odkazovat přímo na vzory, které se vztahují k vašemu syntetickému hlasu. Některé další podmínky v tomto grafu se můžou vztahovat i na váš scénář:<br/>



| Pokud máte syntetické hlasové prostředí... | Doporučení | Způsoby návrhu |
| --- | --- | --- |
| Vyžaduje vysoké zveřejnění  | K tomu, aby uživatelé mohli vytvářet přidružení, použijte alespoň jeden explicitní vzor a implicitní hromádky. |[Explicitní zveřejnění](#explicit-disclosure)<br>[Implicitní zveřejnění](#implicit-disclosure)  |
| Vyžaduje nízké zpřístupnění | Zveřejnění může být minimální nebo zbytečné, ale může mít užitek z některých implicitních vzorů. | [Vyzrazení schopností](#capability-disclosure)<br>[Průhlednost konverzací](#conversational-transparency)  |
| Má vysokou úroveň zapojení | Vytvářejte po dlouhou dobu a poskytněte více vstupních bodů k zveřejnění na cestě uživatele. Důrazně doporučujeme mít prostředí pro připojování. | [Transparentní Úvod](#transparent-introduction)<br>[Přizpůsobení a kalibrace](#customization-and-calibration)<br>[Vyzrazení schopností](#capability-disclosure) |
| Zahrnuje podřízené objekty jako primární zamýšlenou cílovou skupinu. | Cílí na cíle jako na primární cílovou skupinu pro zveřejňování a zajistěte, aby mohli efektivně komunikovat s podřízenou položkou.  | [Odhalení rodičů](#parental-disclosure)<br>[Slovní transparentní Úvod](#verbal-transparent-introduction)<br> [Implicitní zveřejnění](#implicit-disclosure)<br> [Průhlednost konverzací](#conversational-transparency)  |
| Zahrnuje nevidomé uživatele nebo lidi s nízkou schopností jako primární zamýšlenou cílovou skupinu.  | Být zahrnuti do všech uživatelů a zajistěte, aby jakékoli formy vizuálního zpřístupnění byly přidružené k alternativnímu textovému nebo zvukovému dopadu. Dodržování standardů přístupnosti pro kontrastní poměr a velikost zobrazení K sdělování informací používejte auditorské pomůcky.  | [Slovní transparentní Úvod](#verbal-transparent-introduction) <br>[Upozornění pro auditory](#implicit-cues--feedback)<br>[Hmatové](#implicit-cues--feedback)<br>[Průhlednost konverzací](#conversational-transparency)<br>[Standardy přístupnosti](https://www.microsoft.com/accessibility) |
| Je menší než na obrazovce, zařízení nebo používá hlas jako primární nebo jediný režim interakce | K sdělování informací používejte auditorské pomůcky. | [Slovní transparentní Úvod](#verbal-transparent-introduction) <br> [Upozornění pro auditory](#implicit-cues--feedback)  |
| Potenciálně zahrnuje více uživatelů nebo naslouchacího procesu (například osobní asistent ve více domácnostech).  | Je třeba mít na vědomí různé kontexty uživatelů a úrovně porozumění a nabízet více příležitostí k odhalení na cestě uživatele.  | [Transparentní Úvod (návrat uživatele)](#transparent-introduction)<br> [Poskytnutí příležitostí k získání dalších informací o tom, jak byl hlas proveden](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Průhlednost konverzací](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Explicitní zveřejnění
Pokud vaše syntetické hlasové prostředí vyžaduje vysoké zveřejnění, je vhodné použít alespoň jeden z následujících explicitních vzorů k jasnému zjištění syntetické povahy.
### <a name="transparent-introduction"></a>Transparentní Úvod

Než začnete s hlasovým prostředím, zaveďte digitální asistenta tak, aby byl plně transparentní o původu jeho hlasu a jeho schopností. Optimálním okamžikem použití tohoto modelu je při připojování nového uživatele nebo při zavádění nových funkcí vracejícího uživatele. Implementace implicitních pomůcek během úvodu pomůže uživatelům vytvořit duševní model o syntetické povaze digitálního agenta.

#### <a name="first-time-user-experience"></a>Uživatelské prostředí při prvním spuštění

![transparentní Úvod během prvního spuštění](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*Při připojování nového uživatele se zavádí syntetický hlas.*

Doporučení
- Popište, že je tento hlas umělý (např. &quot;Digital&quot;).
- Popište, co je agent schopný provádět
- Explicitní stav zdroje hlasu&#39;s
- Nabídnout vstupní bod pro další informace o syntetickém hlasu

#### <a name="returning-user-experience"></a>Vracení uživatelského prostředí

Pokud uživatel přeskočí prostředí pro registraci, pokračujte v poskytování vstupních bodů do transparentního úvodního prostředí, dokud uživatel poprvé nespustí hlasový vstup.
<br/>

![transparentní Úvod během vracení koncového uživatele](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Poskytněte konzistentní vstupní bod syntetického hlasového prostředí. Umožní uživateli vracet se do prostředí pro registraci při prvním spuštění hlasu v jakémkoli okamžiku cesty uživatele.*


### <a name="verbal-transparent-introduction"></a>Slovní transparentní Úvod

Mluvené okno s upozorněním, že zdroje digitálního asistenta&#39;s jsou dostatečně dostačující, aby bylo možné vyzrazení. Tento model je nejvhodnější pro scénáře s vysokou dostupností, kde je hlasový přístup jediným způsobem, který je v interakci k dispozici.
<br/>

![operace s transparentním představením](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Využijte transparentní Úvod, pokud se setkáte s uživatelským prostředím, kde jste už mohli začlenit nebo zadat nějaký hlas osoby&#39;s.*


![operace s průhledným představením při první osobě](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*Pro další transparentnost může hlas actor zveřejnit původ syntetického hlasu v první osobě.*

### <a name="explicit-byline"></a>Explicitní Podtitulek

Tento model použijte v případě, že uživatel bude při aktivaci hlasu spolupracovat se zvukovým přehrávačem nebo interaktivní komponentou.


![explicitní Podtitulek ve scénáři pro příspěvky na média](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Explicitní Podtitulek je přidělení místa, odkud hlas pochází.*

Doporučení

- Vstupní bod nabídky, kde najdete další informace o syntetizovém hlasu

### <a name="customization-and-calibration"></a>Přizpůsobení a kalibrace

Poskytněte uživatelům kontrolu nad tím, jak digitální asistent reaguje na ně (tj. jak hlasová zvuk).  Když uživatel komunikuje s systémem na svých vlastních termínech a s konkrétními cíli je, pak podle definice již rozumí, že se&#39;nejedná o skutečnou osobu.

#### <a name="user-control"></a>Uživatelský ovládací prvek

Nabízí volby, které mají smysluplný a znatelný dopad na syntetické hlasové prostředí.

![předvolby uživatele](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*Uživatelské předvolby umožňují uživatelům přizpůsobení a vylepšení jejich prostředí.*

Doporučení

- Umožněte uživatelům přizpůsobení hlasu (např. Vyberte jazyk a typ hlasu).
- Poskytněte uživatelům způsob, jak naučit systém, aby reagoval na jeho jedinečný hlas (například kalibrace hlasu, vlastní příkazy).
- Optimalizace pro uživatelem generovaná nebo kontextová interakce (například připomenutí)

#### <a name="persona-customization"></a>Přizpůsobení uživatelských serverů

Nabízí způsoby přizpůsobení hlasu digitálního asistenta&#39;s. Pokud je hlas založen na celebrit nebo široce rozpoznatelné osobě, zvažte použití vizuálního i mluveného úvodu, když uživatelé zobrazí náhled hlasu.

![přizpůsobení hlasu](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Nabídka možnosti vybírat ze sady hlasů pomáhá předávat umělou povahu.*

Doporučení
- Umožní uživatelům zobrazit náhled zvuku každého hlasu.
- Používejte pro každý hlas závazné úvodní informace.
- Vstupní body nabídky pro další informace o syntetizovém hlasu

### <a name="parental-disclosure"></a>Odhalení rodičů

Kromě dodržování předpisů COPPA poskytují dětem informace o tom, jestli je vaše primární zamýšlená cílová skupina mladými dětmi a úroveň ozáření je vysoká. V případě citlivých použití zvažte možnost využívání tohoto prostředí, dokud dospělý nepotvrdí používání syntetického hlasu. Pozvěte rodiče, aby zprávu komunikovali se svými podřízenými položkami.

![zpřístupnění pro nadřazené](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*Transparentní Úvod, který je optimalizován pro nadřazené objekty, zajišťuje, že dospělý má na vědomí syntetickou povahu hlasu před tím, než se s ním podřízená.*

Doporučení

- Cílové nadřízené položky jako primární cílová skupina pro zpřístupnění
- Podpora rodičů pro komunikaci se svými dětmi
- Vstupní body nabídky pro další informace o syntetizovém hlasu
- Využijte možnost vyžádat si rodiče a jednoduše &quot;chránit&quot; otázky, aby viděli, že si přečetli zveřejnění.

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Poskytnutí příležitostí k získání dalších informací o tom, jak byl hlas proveden

Nabízí kontextově závislé vstupní body na stránce, v místní nabídce nebo na externím webu, který poskytuje další informace o syntetické hlasové technologii. Můžete například vytvořit Surface odkazu a získat další informace během připojování nebo když se uživatel během konverzace vyzve k zadání dalších informací.

![vstupní bod pro další informace](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Příklad vstupního bodu, který nabízí možnost získat další informace o syntetizovém hlasu.*

Jakmile si uživatel požádá o další informace o syntetickém hlasu, primárním cílem je informovat se o původu syntetického hlasu a být transparentní na technologii.

![poskytnout uživatelům další informace o syntetickém hlasovém](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Další informace lze nabídnout na webu s externím webem pro podporu.*

Doporučení

- Zjednodušení složitých konceptů a zamezení používání legalese a technických žargonu
- Nebury tento obsah v prohlášeních o zásadách ochrany osobních údajů a podmínek použití
- Používejte stručně vypřesnější obsah a použijte k dispozici obrázek.

## <a name="implicit-disclosure"></a>Implicitní zveřejnění

Konzistence je klíč k dosažení implicitního zpřístupnění v průběhu cesty uživatele. Konzistentní používání vizuálních a auditorních pomůcek napříč zařízeními a režimy interakce může pomáhat při vytváření přidružení mezi implicitními vzory a explicitním zveřejněním.

![Konzistence implicitních pomůcek](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues-amp-feedback"></a>Implicitní hromádky &amp; zpětné vazby

Anthropomorphism může manifestovat různými způsoby, od skutečného vizuální reprezentace agenta až po hlas, zvuky, vzory světla, skákající tvary nebo dokonce vibrace zařízení. Při definování svého uživatelů Využijte implicitní hromádky a vzory zpětné vazby, místo cílení na miniaturu, která je člověkem podobná. To je jeden ze způsobů, jak minimalizovat nutnost výslovného odhalení.

![vizuálních pomůcek a Feedback](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Tyto pomůcky vám pomůžou anthropomorphize agenta bez toho, aby byl příliš člověk podobný. Můžou se také stát účinnými mechanismy zveřejnění, pokud se v průběhu času používají konzistentně.*

Při začleňování následujících typů pomůcek zvažte různé režimy interakce s vaším prostředím:

| Vizuální pomůcky                                                                                                                                                               | Upozornění pro auditory                                                      | Hmatové |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Miniatur <br>Reakce na pomůcky v reálném čase (například animace)<br> Pomůcky neobsahující obrazovku (například světla a vzory na zařízení)<br>  | Sonicon (např. Stručný rozlišující zvuk, série hudebních poznámek) | Vibrační   |

### <a name="capability-disclosure"></a>Vyzrazení schopností

Zveřejnění se dá provést implicitně nastavením přesného očekávání za to, co je digitální asistent schopný. Poskytněte ukázkové příkazy, aby se uživatelé mohli naučit pracovat s digitálním asistentem a nabídnout kontextovou nápovědu pro další informace o syntetickém hlasu v počátečních fázích prostředí.

![Vizuální pomůcky a zpětná vazba](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Průhlednost konverzací

V případě, že konverzace spadají do neočekávaných cest, zvažte vytvoření výchozích odpovědí, které mohou přispět k resetování očekávání, posílení transparentnosti a řízení uživatelů na úspěšné cesty. K dispozici jsou také možnosti použití explicitního zveřejnění v konverzaci.

![Zpracování neočekávaných cest](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Mimo úkol nebo &quot;osobní&quot; dotazy směrované na agenta jsou vhodný čas k připomenutí uživatelům syntetické povahy agenta a jejich řízení, aby se s ním mohli řádně zapojit nebo přesměrovat na skutečnou osobu.

![Zpracování dotazů na úlohy](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Kdy se má zveřejnit

Existuje mnoho příležitostí k odhalení během cesty uživatele. Návrh pro první použití, druhé použití, n-tý použití..., ale také k výpadku &quot;selhání&quot; ke zvýraznění transparentnosti, například když systém vytvoří chybu nebo když uživatel zjistí omezení schopností s agentem&#39;.

![Příležitosti k zveřejnění v průběhu cesty uživatelů](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Příklad standardní cesty uživatele digitálního asistenta zvýrazňování různých příležitostí k vyzrazení.

### <a name="up-front"></a>Dopředu

Optimální termín pro zveřejnění je první, kdo komunikuje s syntetickým hlasem.  V osobním scénáři hlasového asistenta by to bylo během připojování, nebo když uživatel prakticky nepředá toto prostředí. V jiných scénářích to může být první, když syntetický hlas čte obsah na webu nebo když uživatel poprvé komunikuje s virtuálním znakem.

- [Transparentní Úvod](#transparent-introduction)
- [Vyzrazení schopností](#capability-disclosure)
- [Přizpůsobení a kalibrace](#customization-and-calibration)
- [Implicitní hromádky](#implicit-cues--feedback)

### <a name="upon-request"></a>Na vyžádání

Uživatelé by měli mít možnost snadno získat přístup k dalším informacím, předvolby řízení a přijímat transparentní komunikaci kdykoli během každé žádosti o cestu uživatele.

- [Poskytnutí příležitostí k získání dalších informací o tom, jak byl hlas proveden](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Přizpůsobení a kalibrace](#customization-and-calibration)
- [Průhlednost konverzací](#conversational-transparency)

### <a name="continuously"></a>Soustavné

Používejte implicitní vzory návrhu, které uživateli zvyšují nepřetržité uživatelské prostředí.

- [Vyzrazení schopností](#capability-disclosure)
- [Implicitní hromádky](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>Když dojde k chybě systému

Vyzrazení můžete využít jako příležitost k řádnému selhání.

- [Průhlednost konverzací](#conversational-transparency)
- [Poskytnutí příležitostí k získání dalších informací o tom, jak byl hlas proveden](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Předání do lidského](#conversational-transparency)



## <a name="additional-resources"></a>Další zdroje informací:
- [Pokyny pro Microsoft bot](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Pokyny pro návrh Cortany](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Pokyny pro návrh mluveného slova pro Microsoft Windows UWP](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Microsoft Windows Mixed reality – pokyny pro hlasové příkazy](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>Referenční dokumenty

* [Poznámka transparentnosti](https://aka.ms/neural-tts-transparency-note)
* [Pokyny pro zodpovědnost nasazení syntetické hlasové technologie](concepts-guidelines-responsible-deployment-synthetic.md)
* [Přehled o uzavírání](concepts-gating-overview.md)
* [Jak zveřejnit](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>Další kroky

* [Poznámka transparentnosti](https://aka.ms/neural-tts-transparency-note)
