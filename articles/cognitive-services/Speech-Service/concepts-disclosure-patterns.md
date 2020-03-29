---
title: Zveřejnění návrhových vzorů
titleSuffix: Azure Cognitive Services
description: Návrhové vzory a osvědčené postupy pro zveřejňování informací.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: 3e7d8ee2b156a30b11cda79798a8af8a8ecf4f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776618"
---
# <a name="disclosure-design-patterns"></a>Zpřístupnění způsobů návrhu
Nyní, když jste&#39;určili správnou [úroveň zveřejnění](concepts-disclosure-guidelines.md#disclosure-assessment) pro váš syntetistický hlasový zážitek,&#39;je vhodná doba prozkoumat potenciální návrhové vzory.
## <a name="overview"></a>Přehled
Existuje spektrum vzorů odhalení, které můžete použít pro váš syntetický hlasový zážitek. Pokud byl výsledek vašeho zveřejnění "High Disclosure", doporučujeme [**výslovné zveřejnění**](#explicit-disclosure), což znamená, že sdělujete původ syntetického hlasu. [**Implicitní zveřejnění**](#implicit-disclosure) obsahuje podněty a interakce vzory, které využívají hlasové zkušenosti, zda jsou požadované úrovně zveřejnění vysoké nebo nízké.
![Spektrum vzorců zveřejňování](media/responsible-ai/disclosure-patterns/affordances.png)






| Explicitní vzory zveřejňování                                                                                                                                                                                    | Implicitní vzory zveřejňování                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Transparentní úvod](#transparent-introduction)<br> [Verbální transparentní úvod](#verbal-transparent-introduction)<br>  [Explicitní podřád](#explicit-byline)<br>  [Přizpůsobení a kalibrace](#customization-and-calibration)<br> [Zveřejnění rodičovské zodpovědnosti](#parental-disclosure)<br> [Poskytování příležitostí dozvědět se více o tom, jak byl hlas](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Zveřejňování schopností](#capability-disclosure)<br>[Implicitní podněty a zpětná vazba](#implicit-cues--feedback)<br> [Konverzační průhlednost](#conversational-transparency) |



Pomocí následujícího grafu odkazovat přímo na vzory, které se vztahují k syntetické hlas. Některé další podmínky v tomto grafu mohou také platit pro váš scénář:<br/>



| Pokud váš syntetický hlas zkušenosti ... | Doporučení | Způsoby návrhu |
| --- | --- | --- |
| Vyžaduje vysoké zveřejnění  | Použijte alespoň jeden explicitní vzor a implicitní podněty předem pomáhá uživatelům vytvářet přidružení. |[Výslovné zveřejnění](#explicit-disclosure)<br>[Implicitní zveřejnění](#implicit-disclosure)  |
| Vyžaduje nízké zveřejnění | Zveřejnění může být minimální nebo zbytečné, ale mohlo by těžit z některých implicitních vzorců. | [Zveřejňování schopností](#capability-disclosure)<br>[Konverzační průhlednost](#conversational-transparency)  |
| Má vysokou úroveň angažovanosti | Stavět na dlouhou dobu a nabízejí více vstupních bodů pro zveřejnění podél cesty uživatele. Důrazně doporučujeme mít zážitek z nástupu. | [Transparentní úvod](#transparent-introduction)<br>[Přizpůsobení a kalibrace](#customization-and-calibration)<br>[Zveřejňování schopností](#capability-disclosure) |
| Zahrnuje děti jako primární zamýšlené publikum | Zacilte na rodiče jako na primární publika a zajistěte, aby mohli účinně sdělovat informace dětem.  | [Zveřejnění rodičovské zodpovědnosti](#parental-disclosure)<br>[Verbální transparentní úvod](#verbal-transparent-introduction)<br> [Implicitní zveřejnění](#implicit-disclosure)<br> [Konverzační průhlednost](#conversational-transparency)  |
| Zahrnuje nevidomé uživatele nebo osoby se zlabujícím zrakem jako primární zamýšlené publikum  | Buďte zahrnutí všech uživatelů a ujistěte se, že jakákoli forma vizuálního zveřejnění má přidružené alternativní textové nebo zvukové efekty. Dodržujte standardy přístupnosti pro kontrastní poměr a velikost displeje. Ke komunikaci s informací používejte sluchové podněty.  | [Verbální transparentní úvod](#verbal-transparent-introduction) <br>[Sluchové podněty](#implicit-cues--feedback)<br>[Haptické podněty](#implicit-cues--feedback)<br>[Konverzační průhlednost](#conversational-transparency)<br>[Standardy přístupnosti](https://www.microsoft.com/accessibility) |
| Je obrazovka bez obrazovky, bez zařízení nebo používá hlas jako primární nebo jediný způsob interakce | Ke komunikaci s informací používejte sluchové podněty. | [Verbální transparentní úvod](#verbal-transparent-introduction) <br> [Sluchové podněty](#implicit-cues--feedback)  |
| Potenciálně zahrnuje více uživatelů/posluchačů (např. osobní asistent ve více domácnostech)  | Mějte na paměti různé uživatelské kontexty a úrovně porozumění a nabízejí více příležitostí pro zveřejnění v cestě uživatele.  | [Transparentní úvod (návratový uživatel)](#transparent-introduction)<br> [Poskytování příležitostí dozvědět se více o tom, jak byl hlas](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Konverzační průhlednost](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Výslovné zveřejnění
Pokud váš syntetický hlas ový zážitek vyžaduje vysoké zveřejnění, je nejlepší použít alespoň jeden z následujících explicitních vzorů, aby bylo jasné, že je syntetická povaha.
### <a name="transparent-introduction"></a>Transparentní úvod

Než začne hlasové prostředí, představte digitálního asistenta tím, že bude plně transparentní, pokud jde o původ jeho hlasu a jeho schopnosti. Optimální okamžik pro použití tohoto vzoru je při zapnutí nového uživatele nebo při zavádění nových funkcí vracejícímu se uživateli. Implementace implicitních podnětů během úvodu pomáhá uživatelům vytvořit mentální model o syntetické povaze digitálního agenta.

#### <a name="first-time-user-experience"></a>První uživatelské prostředí

![Transparentní úvod při prvním spuštění](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*Syntetický hlas je zaveden při nástupu nového uživatele.*

Doporučení
- Popište, že hlas je umělý &quot;&quot;(např. digitální)
- Popište, čeho je agent schopen dělat
- Explicitně uveďte původ hlasu&#39;
- Nabídněte vstupní bod, abyste se dozvěděli více o syntetickém hlasu

#### <a name="returning-user-experience"></a>Vrácení uživatelského prostředí

Pokud uživatel přeskočí prostředí pro připojení, pokračujte v nabídce vstupních bodů do prostředí Transparentní úvod, dokud uživatel poprvé nespustí hlas.
<br/>

![Transparentní úvod během zpětného uživatelského prostředí](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Zajistěte konzistentní vstupní bod syntetického hlasového prostředí. Umožněte uživateli vrátit se k nástupu, když poprvé spustí hlas v libovolném okamžiku cesty uživatele.*


### <a name="verbal-transparent-introduction"></a>Verbální transparentní úvod

Mluvená výzva uvádějící původ hlasu digitálního asistenta&#39;je dostatečně explicitní, aby bylo dosaženo zveřejnění. Tento vzor je nejvhodnější pro scénáře s vysokým zveřejněním, kde je hlas jediným dostupným způsobem interakce.
<br/>

![Verbálně mluvený transparentní úvod](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Transparentní úvod použijte v momentech, kdy v uživatelském prostředí jsou okamžiky, kdy již můžete osobu&#39;hlas uvádět nebo přiřazovat.*


![Verbálně mluvený transparentní úvod v první osobě](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*Pro větší transparentnost může hlasový objekt odhalit původ syntetického hlasu v první osobě.*

### <a name="explicit-byline"></a>Explicitní podřád

Tento vzor použijte, pokud uživatel bude pracovat se zvukovým přehrávačem nebo interaktivní komponentou pro spuštění hlasu.


![Explicitní podtitulek ve scénáři zpravodajských médií](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Explicitní podřádka je přiřazení místa, odkud hlas pochází.*

Doporučení

- Nabídněte vstupní bod, abyste se dozvěděli více o syntetizovaném hlasu

### <a name="customization-and-calibration"></a>Přizpůsobení a kalibrace

Poskytněte uživatelům kontrolu nad tím, jak na ně digitální asistent reaguje (tj. na to, jak zní hlas).  Když uživatel interaguje se systémem na své vlastní podmínky a s konkrétními cíli na mysli, pak podle definice, oni už pochopili, že to&#39;není skutečná osoba.

#### <a name="user-control"></a>Uživatelské řízení

Nabídněte možnosti, které mají smysluplný a znatelný dopad na syntetickou hlasovou zkušenost.

![Uživatelské předvolby](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*Uživatelské předvolby umožňují uživatelům přizpůsobit a zlepšit jejich zkušenosti.*

Doporučení

- Umožnit uživatelům přizpůsobit hlas (např. vybrat jazyk a typ hlasu)
- Poskytnout uživatelům způsob, jak naučit systém reagovat na jeho jedinečný hlas (např. kalibrace hlasu, vlastní příkazy)
- Optimalizace pro uživatelem generované nebo kontextové interakce (např. připomenutí)

#### <a name="persona-customization"></a>Přizpůsobení persona

Nabídněte způsoby přizpůsobení digitálního asistenta&#39;hlasu. Pokud je hlas založen na celebritách nebo široce rozpoznatelné osobě, zvažte použití vizuálních i mluvených úvodů, když uživatelé zobrazí náhled hlasu.

![Přizpůsobení hlasu](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Nabídka možnosti výběru ze sady hlasů pomáhá zprostředkovat umělou povahu.*

Doporučení
- Povolit uživatelům zobrazit náhled zvuku každého hlasu
- Použití autentického úvodu pro každý hlas
- Nabídněte vstupní body, abyste se dozvěděli více o syntetizovaném hlasu

### <a name="parental-disclosure"></a>Zveřejnění rodičovské zodpovědnosti

Kromě dodržování předpisů COPPA poskytněte rodičům informace, pokud je vaším primárním zamýšleným publikem malé děti a vaše úroveň expozice je vysoká. Pro citlivá použití, zvažte gating zkušenosti, dokud dospělý uznal použití syntetického hlasu. Povzbuďte rodiče, aby poselství sdělili svým dětem.

![Zveřejnění pro rodiče](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*Transparentní úvod optimalizovaný pro rodiče zajišťuje, že dospělý byl informován o syntetické povaze hlasu předtím, než s ním dítě interaguje.*

Doporučení

- Cílit rodiče jako primární publikum pro zveřejnění
- Povzbuzovat rodiče, aby svým dětem sdělovali informace
- Nabídněte vstupní body, abyste se dozvěděli více o syntetizovaném hlasu
- Gate zkušenosti tím, že &quot;&quot; žádá rodiče jednoduchou ochrannou otázku ukázat, že si přečetli zveřejnění

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Poskytování příležitostí dozvědět se více o tom, jak byl hlas

Nabídněte kontextové vstupní body na stránku, automaticky otevírané okno nebo externí web, který poskytuje další informace o technologii syntetického hlasu. Můžete například zobrazit odkaz, abyste se dozvěděli více během registrace nebo když uživatel během konverzace vyzve k zadání dalších informací.

![Vstupní bod se dozvíte více](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Příklad vstupního bodu, který nabízí možnost dozvědět se více o syntetizovaném hlasu.*

Jakmile uživatel požaduje více informací o syntetickém hlasu, hlavním cílem je vzdělávat je o původu syntetického hlasu a být transparentní o technologii.

![Poskytnout uživatelům více informací o syntetickém hlasu](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Další informace lze nabídnout na externím webu nápovědy.*

Doporučení

- Zjednodušte složité pojmy a vyhněte se používání legalese a technického žargonu
- Nezakopávejte tento obsah v prohlášeních o ochraně osobních údajů a podmínkách použití
- Udržujte obsah výstižný a používejte snímky, pokud jsou k dispozici

## <a name="implicit-disclosure"></a>Implicitní zveřejnění

Konzistence je klíčem k dosažení zpřístupnění implicitně v průběhu cesty uživatele. Konzistentní používání vizuálních a zvukových podnětů napříč zařízeními a režimy interakce může pomoci vytvářet přidružení mezi implicitními vzory a explicitním zveřejněním.

![Konzistence implicitních podnětů](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Implicitní upozornění & zpětnou vazbu

Antropomorfismus se může projevit různými způsoby, od skutečné vizuální reprezentace agenta, až po hlas, zvuky, vzory světla, skákací tvary nebo dokonce vibrace zařízení. Při definování vaší osobnosti, pákový efekt implicitní podněty a zpětnou vazbu vzory spíše než usilovat o velmi lidské-jako avatar. To je jeden ze způsobů, jak minimalizovat potřebu jasnějšího zveřejnění.

![Vizuální podněty a zpětná vazba](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Tyto podněty pomáhají antropomorfizovat agenta, aniž by byl příliš lidský- jako. Mohou se také stát účinnými mechanismy zveřejňování samy o sobě, pokud jsou používány konzistentně v průběhu času.*

Při začleňování následujících typů podnětů zvažte různé způsoby interakce, které máte zacházet:

| Vizuální podněty                                                                                                                                                               | Sluchové podněty                                                      | Haptické podněty |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>Responzivní podněty v reálném čase (např. animace)<br> Upozornění mimo obrazovku (např. světla a vzory na zařízení)<br>  | Sonicon (např. krátký výrazný zvuk, série notových zkratek) | Vibrace   |

### <a name="capability-disclosure"></a>Zveřejňování schopností

Zveřejnění lze dosáhnout implicitně nastavením přesných očekávání pro to, čeho je digitální asistent schopen. Poskytněte ukázkové příkazy, aby se uživatelé mohli naučit pracovat s digitálním asistentem a nabídnout kontextovou pomoc, abyste se dozvěděli více o syntetickém hlasu v raných fázích prostředí.

![Vizuální podněty a zpětná vazba](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Konverzační průhlednost

Když konverzace spadají do neočekávaných cest, zvažte vytvoření výchozích odpovědí, které mohou pomoci obnovit očekávání, posílit transparentnost a nasměrovat uživatele k úspěšným cestám. Existují příležitosti k použití explicitní zveřejnění v konverzaci stejně.

![Zpracování neočekávaných cest](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Off-úkol &quot;nebo&quot; osobní otázky zaměřené na agenta jsou vhodná doba připomenout uživatelům syntetickou povahu agenta a řídit je, aby se s ním vhodně zapojili nebo je přesměrovali na skutečnou osobu.

![Zpracování otázek mimo úkol](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Kdy zveřejnit

Existuje mnoho příležitostí pro zveřejnění v průběhu cesty uživatele. Návrh pro první použití, druhé použití, n-té použití..., ale také přijmout okamžiky selhání &quot;&quot; upozornit na transparentnost-jako když systém udělá chybu, nebo když uživatel zjistí omezení agenta&#39;s schopnosti.

![Příležitosti k zveřejnění během cesty uživatele](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Příklad standardní cesty uživatele digitálního asistenta, která zvýrazňuje různé možnosti zveřejnění.

### <a name="up-front"></a>Up-front

Optimální okamžik pro zveřejnění je poprvé, kdy člověk interaguje se syntetickým hlasem.V případě osobního hlasového asistenta by to bylo během registrace nebo poprvé, kdy uživatel prakticky rozbalí prostředí. V jiných scénářích to může být poprvé, kdy syntetický hlas čte obsah na webu nebo poprvé, kdy uživatel interaguje s virtuálním znakem.

- [Transparentní úvod](#transparent-introduction)
- [Zveřejňování schopností](#capability-disclosure)
- [Přizpůsobení a kalibrace](#customization-and-calibration)
- [Implicitní podněty](#implicit-cues--feedback)

### <a name="upon-request"></a>Vyžádání

Uživatelé by měli mít možnost snadno přistupovat k dalším informacím, předvolbám kontroly a přijímat transparentní komunikaci kdykoli během cesty uživatele, pokud je o to požádáni.

- [Poskytování příležitostí dozvědět se více o tom, jak byl hlas](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Přizpůsobení a kalibrace](#customization-and-calibration)
- [Konverzační průhlednost](#conversational-transparency)

### <a name="continuously"></a>Neustále

Používejte implicitní návrhové vzory, které neustále vylepšují uživatelské prostředí.

- [Zveřejňování schopností](#capability-disclosure)
- [Implicitní podněty](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>Když systém selže

Použijte zveřejnění jako příležitost k selhání elegantně.

- [Konverzační průhlednost](#conversational-transparency)
- [Poskytování příležitostí dozvědět se více o tom, jak byl hlas](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Předání člověku](#conversational-transparency)



## <a name="additional-resources"></a>Další zdroje
- [Pokyny pro roboty Microsoft Bot](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Pokyny pro návrh Cortany](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Pokyny pro návrh řeči služby Microsoft Windows UPW](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Pokyny pro hlasové příkazy pro smíšenou realitu systému Microsoft Windows](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>Referenční dokumenty

* [Zveřejnění pro hlasové talenty](https://aka.ms/disclosure-voice-talent)
* [Pokyny pro odpovědné zavádění syntetické hlasové technologie](concepts-guidelines-responsible-deployment-synthetic.md)
* [Gating – přehled](concepts-gating-overview.md)
* [Jak zveřejnit](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>Další kroky

* [Zveřejnění pro hlasové talenty](https://aka.ms/disclosure-voice-talent)
