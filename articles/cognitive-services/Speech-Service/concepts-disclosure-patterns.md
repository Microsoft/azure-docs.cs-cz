---
title: Vzory návrhu zpřístupnění
titleSuffix: Azure Cognitive Services
description: Vzory návrhu a osvědčené postupy pro zveřejnění.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: fe4f0cdcdb863582560b0eba3a22e95b93ff858b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150945"
---
# <a name="disclosure-design-patterns"></a>Zpřístupnění způsobů návrhu
Teď, když jste&#39;i zjistili správnou [úroveň zveřejnění](concepts-disclosure-guidelines.md#disclosure-assessment) pro syntetické hlasové prostředí,&#39;s vhodným časem prozkoumat potenciální vzory návrhu.
## <a name="overview"></a>Přehled
Existuje spektrum vzorů návrhu zpřístupnění, které můžete použít na syntetické hlasové prostředí. Pokud byl výsledek hodnocení zveřejnění "vysoké zpřístupnění", doporučujeme, abyste [**výslovně vyzrazeni**](#explicit-disclosure), což znamená, že navzájem komunikují původem syntetického hlasu. [**Implicitní zveřejnění**](#implicit-disclosure) zahrnuje hromádky a vzory interakce, které využívají hlasové funkce, ať už jsou požadované úrovně zveřejnění vysoké nebo nízké.
![Spektrum způsobů zpřístupnění](media/responsible-ai/disclosure-patterns/affordances.png)






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

![Transparentní Úvod během prvního spuštění](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*Při připojování nového uživatele se zavádí syntetický hlas.*

Doporučení
- Popište, že hlas je umělý (např. &quot; Digital &quot; ).
- Popište, co je agent schopný provádět
- Explicitní stav zdroje hlasových&#39;s
- Nabídnout vstupní bod pro další informace o syntetickém hlasu

#### <a name="returning-user-experience"></a>Vracení uživatelského prostředí

Pokud uživatel přeskočí prostředí pro registraci, pokračujte v poskytování vstupních bodů do transparentního úvodního prostředí, dokud uživatel poprvé nespustí hlasový vstup.
<br/>

![Transparentní Úvod během vracení koncového uživatele](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Poskytněte konzistentní vstupní bod syntetického hlasového prostředí. Umožní uživateli vracet se do prostředí pro registraci při prvním spuštění hlasu v jakémkoli okamžiku cesty uživatele.*


### <a name="verbal-transparent-introduction"></a>Slovní transparentní Úvod

Mluvené okno s oznámením, že zdroje digitálního asistenta&#39;s jsou dostatečně dostačující, aby bylo možné vyzrazení. Tento model je nejvhodnější pro scénáře s vysokou dostupností, kde je hlasový přístup jediným způsobem, který je v interakci k dispozici.
<br/>

![Vymluvené neprůhledný Úvod](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Pomocí transparentního úvodu v uživatelském prostředí, kde už možná zavedete nebo zadáte atribut person&#39;s.*


![V tuto operaci se v první osobě hovoří o transparentním úvodu.](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*Pro další transparentnost může hlas actor zveřejnit původ syntetického hlasu v první osobě.*

### <a name="explicit-byline"></a>Explicitní Podtitulek

Tento model použijte v případě, že uživatel bude při aktivaci hlasu spolupracovat se zvukovým přehrávačem nebo interaktivní komponentou.


![Explicitní Podtitulek ve scénáři média pro příspěvky](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Explicitní Podtitulek je přidělení místa, odkud hlas pochází.*

Doporučení

- Vstupní bod nabídky, kde najdete další informace o syntetizovém hlasu

### <a name="customization-and-calibration"></a>Přizpůsobení a kalibrace

Poskytněte uživatelům kontrolu nad tím, jak digitální asistent reaguje na ně (tj. jak hlasová zvuk).  Když uživatel pracuje s systémem na svých vlastních termínech a s konkrétními cíli, pak podle definice již nerozumí, že&#39;s nereálné osobě.

#### <a name="user-control"></a>Uživatelský ovládací prvek

Nabízí volby, které mají smysluplný a znatelný dopad na syntetické hlasové prostředí.

![Předvolby uživatele](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*Uživatelské předvolby umožňují uživatelům přizpůsobení a vylepšení jejich prostředí.*

Doporučení

- Umožněte uživatelům přizpůsobení hlasu (např. Vyberte jazyk a typ hlasu).
- Poskytněte uživatelům způsob, jak naučit systém, aby reagoval na jeho jedinečný hlas (například kalibrace hlasu, vlastní příkazy).
- Optimalizace pro uživatelem generovaná nebo kontextová interakce (například připomenutí)

#### <a name="persona-customization"></a>Přizpůsobení uživatelských serverů

Nabízí způsoby přizpůsobení hlasu&#39;s digitálním asistentem. Pokud je hlas založen na celebrit nebo široce rozpoznatelné osobě, zvažte použití vizuálního i mluveného úvodu, když uživatelé zobrazí náhled hlasu.

![Přizpůsobení hlasu](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Nabídka možnosti vybírat ze sady hlasů pomáhá předávat umělou povahu.*

Doporučení
- Umožní uživatelům zobrazit náhled zvuku každého hlasu.
- Používejte pro každý hlas závazné úvodní informace.
- Vstupní body nabídky pro další informace o syntetizovém hlasu

### <a name="parental-disclosure"></a>Odhalení rodičů

Kromě dodržování předpisů COPPA poskytují dětem informace o tom, jestli je vaše primární zamýšlená cílová skupina mladými dětmi a úroveň ozáření je vysoká. V případě citlivých použití zvažte možnost využívání tohoto prostředí, dokud dospělý nepotvrdí používání syntetického hlasu. Pozvěte rodiče, aby zprávu komunikovali se svými podřízenými položkami.

![Zveřejnění pro nadřazené položky](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*Transparentní Úvod, který je optimalizován pro nadřazené objekty, zajišťuje, že dospělý má na vědomí syntetickou povahu hlasu před tím, než se s ním podřízená.*

Doporučení

- Cílové nadřízené položky jako primární cílová skupina pro zpřístupnění
- Podpora rodičů pro komunikaci se svými dětmi
- Vstupní body nabídky pro další informace o syntetizovém hlasu
- Využijte možnost vyžádat si rodiče o jednoduchou &quot; bezpečnostní &quot; otázku, aby si ukázali, že si přečetli zveřejnění.

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Poskytnutí příležitostí k získání dalších informací o tom, jak byl hlas proveden

Nabízí kontextově závislé vstupní body na stránce, v místní nabídce nebo na externím webu, který poskytuje další informace o syntetické hlasové technologii. Můžete například vytvořit Surface odkazu a získat další informace během připojování nebo když se uživatel během konverzace vyzve k zadání dalších informací.

![Vstupní bod pro další informace](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Příklad vstupního bodu, který nabízí možnost získat další informace o syntetizovém hlasu.*

Jakmile si uživatel požádá o další informace o syntetickém hlasu, primárním cílem je informovat se o původu syntetického hlasu a být transparentní na technologii.

![Poskytněte uživatelům další informace o syntetickém hlasu.](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Další informace lze nabídnout na webu s externím webem pro podporu.*

Doporučení

- Zjednodušení složitých konceptů a zamezení používání legalese a technických žargonu
- Nebury tento obsah v prohlášeních o zásadách ochrany osobních údajů a podmínek použití
- Používejte stručně vypřesnější obsah a použijte k dispozici obrázek.

## <a name="implicit-disclosure"></a>Implicitní zveřejnění

Konzistence je klíč k dosažení implicitního zpřístupnění v průběhu cesty uživatele. Konzistentní používání vizuálních a auditorních pomůcek napříč zařízeními a režimy interakce může pomáhat při vytváření přidružení mezi implicitními vzory a explicitním zveřejněním.

![Konzistence implicitních pomůcek](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Implicitní hromádky & zpětné vazby

Anthropomorphism může manifestovat různými způsoby, od skutečného vizuální reprezentace agenta až po hlas, zvuky, vzory světla, skákající tvary nebo dokonce vibrace zařízení. Při definování svého uživatelů Využijte implicitní hromádky a vzory zpětné vazby, místo cílení na miniaturu, která je člověkem podobná. To je jeden ze způsobů, jak minimalizovat nutnost výslovného odhalení.

![Vizuální pomůcky a zpětná vazba](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Tyto pomůcky vám pomůžou anthropomorphize agenta bez toho, aby byl příliš člověk podobný. Můžou se také stát účinnými mechanismy zveřejnění, pokud se v průběhu času používají konzistentně.*

Při začleňování následujících typů pomůcek zvažte různé režimy interakce s vaším prostředím:

| Vizuální pomůcky                                                                                                                                                               | Upozornění pro auditory                                                      | Hmatové |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>Reakce na pomůcky v reálném čase (například animace)<br> Pomůcky neobsahující obrazovku (například světla a vzory na zařízení)<br>  | Sonicon (např. Stručný rozlišující zvuk, série hudebních poznámek) | Vibrační   |

### <a name="capability-disclosure"></a>Vyzrazení schopností

Zveřejnění se dá provést implicitně nastavením přesného očekávání za to, co je digitální asistent schopný. Poskytněte ukázkové příkazy, aby se uživatelé mohli naučit pracovat s digitálním asistentem a nabídnout kontextovou nápovědu pro další informace o syntetickém hlasu v počátečních fázích prostředí.

![Vizuální pomůcky a zpětná vazba](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Průhlednost konverzací

V případě, že konverzace spadají do neočekávaných cest, zvažte vytvoření výchozích odpovědí, které mohou přispět k resetování očekávání, posílení transparentnosti a řízení uživatelů na úspěšné cesty. K dispozici jsou také možnosti použití explicitního zveřejnění v konverzaci.

![Zpracování neočekávaných cest](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Mimo úkol nebo &quot; osobní &quot; dotazy směrované k agentovi jsou vhodný čas k přihlašování uživatelů syntetické povahy agenta a jejich řízení, aby se s ním mohli řádně zapojit nebo přesměrovat na skutečnou osobu.

![Zpracování dotazů na úlohy](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Kdy se má zveřejnit

Existuje mnoho příležitostí k odhalení během cesty uživatele. Návrh prvního použití, druhé použití, n-tý použití..., ale také &quot; k výpadku selhání &quot; při zdůraznění transparentnosti, například v případě, že systém provede chybu nebo když uživatel zjistí omezení schopností&#39;s agentem.

![Příležitosti k zveřejnění v průběhu cesty uživatelů](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Příklad standardní cesty uživatele digitálního asistenta zvýrazňování různých příležitostí k vyzrazení.

### <a name="up-front"></a>Dopředu

Optimální termín pro zveřejnění je první, kdo komunikuje s syntetickým hlasem.V osobním scénáři hlasového asistenta by to bylo během připojování, nebo když uživatel prakticky nepředá toto prostředí. V jiných scénářích to může být první, když syntetický hlas čte obsah na webu nebo když uživatel poprvé komunikuje s virtuálním znakem.

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



## <a name="additional-resources"></a>Další zdroje
- [Pokyny pro Microsoft bot](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Pokyny pro návrh Cortany](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Pokyny pro návrh mluveného slova pro Microsoft Windows UWP](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Microsoft Windows Mixed reality – pokyny pro hlasové příkazy](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>Referenční dokumenty

* [Zveřejnění pro hlasový talentů](https://aka.ms/disclosure-voice-talent)
* [Pokyny pro zodpovědnost nasazení syntetické hlasové technologie](concepts-guidelines-responsible-deployment-synthetic.md)
* [Přehled o uzavírání](concepts-gating-overview.md)
* [Jak zveřejnit](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>Další kroky

* [Zveřejnění pro hlasový talentů](https://aka.ms/disclosure-voice-talent)
