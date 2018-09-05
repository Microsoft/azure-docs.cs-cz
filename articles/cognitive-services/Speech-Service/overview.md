---
title: Co je služba Speech (Preview)?
description: 'Služba Speech, která je součástí služeb Microsoft Cognitive Services, sjednocuje několik hlasových služeb Azure, které byly dříve k dispozici samostatně: Zpracování řeči Bingu (skládající se z rozpoznávání řeči a převodu textu na řeč), Custom Speech a Překlad řeči.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: overview
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 922320bb0b880e933b27025257e6a533fe257680
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091468"
---
# <a name="what-is-the-speech-service"></a>Co je služba Speech?

Služba Speech sjednocuje hlasové funkce Azure, které byly dříve dostupné prostřednictvím služeb [Zpracování řeči Bingu](https://docs.microsoft.com/azure/cognitive-services/speech/home), [Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/), [Custom Speech](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home) a [Custom Voice](http://customvoice.ai/). Teď přístup ke všem těmto funkcím poskytuje jedno předplatné.

Podobně jako ostatní hlasové služby Azure i služba Speech využívá prověřené hlasové technologie používané v produktech, jako je Cortana a Microsoft Office. Můžete se spolehnout na kvalitu výsledků a spolehlivost cloudu Azure.

> [!NOTE]
> Služba Speech je v současné době ve verzi Public Preview. Pravidelně se sem vracejte, aby vám neunikly aktualizace dokumentace, nové vzorové kódy a další.

## <a name="main-speech-service-functions"></a>Hlavní funkce služby Speech

Hlavními funkcemi služby Speech jsou převod řeči na text (označuje se také jako rozpoznávání nebo přepis řeči), převod textu na řeč (syntéza řeči) a překlad řeči.

|Funkce|Funkce|
|-|-|
|[Převod řeči na text](speech-to-text.md)| <ul><li>Přepisuje plynulou řeč v reálném čase do textové podoby.<li>Dokáže dávkově přepisovat řeč ze zvukových nahrávek. <li>Nabízí režimy rozpoznávání pro tyto případy použití: interaktivní, konverzační a diktáty.<li>Podporuje okamžité výsledky, detekci konce řeči, automatické formátování textu a maskování vulgarismů. <li>Může volat službu [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), která z přepsané řeči odvodí záměr uživatele.\*|
|[Převod textu na řeč](text-to-speech.md)| <ul><li>Převádí text do přirozeně znějící řeči. <li>Nabízí několik pohlaví a dialektů pro celou řadu podporovaných jazyků. <li>Podporuje vstup v podobě prostého textu nebo jazyk SSML (Speech Synthesis Markup Language). |
|[Překlad řeči](speech-translation.md)| <ul><li>Téměř v reálném čase překládá streamovaný zvuk.<li> Dokáže zpracovat také záznam řeči.<li>Poskytuje výsledky v podobě textu nebo syntetizované řeči. |

\* *K rozpoznávání záměru se vyžaduje předplatné LUIS.*


## <a name="customizing-speech-features"></a>Přizpůsobení hlasových funkcí

Služba Speech umožňuje použít k trénování modelů, které využívají její funkce převodu řeči na text a převodu textu na řeč, vlastní text. 

|Funkce|Model|Účel|
|-|-|-|
|Převod řeči na text|[Akustický model](how-to-customize-acoustic-models.md)|Pomáhá s přepisem u konkrétních mluvčích a prostředí, jako jsou auta nebo továrny.|
||[Jazykový model](how-to-customize-language-model.md)|Pomáhá s přepisem slovní zásoby a gramatiky specifických pro určitý obor, jako je lékařský nebo IT žargon.|
||[Model výslovnosti](how-to-customize-pronunciation.md)|Pomáhá s přepisem zkratek a akronymů, jako je IOU znamenající „I owe you“ (Dlužím ti). |
|Převod textu na řeč|[Hlasové písmo](how-to-customize-voice-font.md)|Poskytuje vaší aplikaci vlastní hlas díky trénování modelu na ukázkách lidské řeči.|

Po vytvoření můžete vlastní modely použít v libovolné funkci převodu řeči na text nebo převodu textu na řeč ve vaší aplikaci, kde byste použili standardní modely.


## <a name="using-the-speech-service"></a>Použití služby Speech

Pro zjednodušení vývoje aplikací s podporou hlasových služeb poskytuje Microsoft sadu [Speech SDK](speech-sdk.md), kterou můžete použít s novou službou Speech. Sada Speech SDK poskytuje konzistentní a nativní rozhraní API pro převod řeči na text a překlad řeči pro jazyky C#, C++ a Java. Pokud k vývoji používáte některý z těchto jazyků, sada Speech SDK vám zjednoduší vývoj tím, že se postará o podrobnosti sítě za vás.

Služba Speech nabízí také rozhraní [REST API](rest-apis.md), které funguje s jakýmkoli programovacím jazykem, který dokáže provádět požadavky HTTP. Rozhraní REST však nenabízí funkci streamování v reálném čase jako sada SDK.

|<br>Metoda|Řeč<br>na text|Text na<br>Řeč|Řeč<br>Překlad|<br>Popis|
|-|-|-|-|-|
|[Speech SDK](speech-sdk.md)|Ano|Ne|Ano|Nativní rozhraní API pro jazyky C#, C++ a Java pro zjednodušení vývoje.|
|[REST](rest-apis.md)|Ano|Ano|Ne|Jednoduché rozhraní API založené na HTTP, které usnadňuje přidání hlasových služeb do aplikací.|

### <a name="websockets"></a>WebSockets

Služba Speech nabízí také protokoly WebSocket pro streamování převodu řeči na text a překladu řeči. Sady Speech SDK pomocí těchto protokolů komunikují se službou Speech. Měli byste použít sadu Speech SDK, a nesnažit se implementovat vlastní komunikaci přes protokoly WebSocket se službou Speech.

Pokud však již máte kód využívající Zpracování řeči Bingu nebo Translator Speech přes protokoly WebSocket, jednoduše ho můžete aktualizovat tak, aby využíval službu Speech. Protokoly WebSocket jsou kompatibilní, liší se pouze koncové body.

### <a name="speech-devices-sdk"></a>Speech Devices SDK

Sada [Speech Devices SDK](speech-devices-sdk.md) je integrovaná hardwarová a softwarová platforma pro vývojáře zařízení s podporou hlasových služeb. Náš partner pro hardware poskytuje referenční návrhy a vývojové jednotky. Microsoft poskytuje sadu SDK optimalizovanou pro zařízení, která naplno využívá možnosti hardwaru.


## <a name="speech-scenarios"></a>Scénáře pro službu Speech

Mezi případy použití služby Speech patří:

> [!div class="checklist"]
> * Vytváření aplikací aktivovaných hlasem
> * Přepis nahrávek z call centra
> * Implementace hlasových robotů

### <a name="voice-user-interface"></a>Hlasové uživatelské prostředí

Hlasový vstup představuje skvělý způsob, jak zajistit flexibilitu a možnost rychlého používání aplikace bez pomoci rukou. V aplikaci s podporou hlasových služeb nemusí uživatelé ručně vyhledávat požadované informace, ale můžou se na ně přímo zeptat.

Pokud je vaše aplikace určená pro veřejnost, můžete využít výchozí modely rozpoznávání řeči. Dobře slouží k rozpoznávání celé řady mluvčích v běžných prostředích.

Pokud se vaše aplikace bude používat v určitém oboru (například lékařství nebo IT), můžete vytvořit [jazykový model](how-to-customize-language-model.md), kterým službu Speech naučíte odbornou terminologii používanou ve vaší aplikaci.

Pokud se vaše aplikace bude používat v hlučném prostředí, například v továrně, můžete vytvořit vlastní [akustický model](how-to-customize-acoustic-models.md), který službě Speech umožní lépe rozlišit řeč od hluku.

Začít je snadné, stačí stáhnout sadu [Speech SDK](speech-sdk.md) a postupovat podle příslušného článku [Rychlý start](quickstart-csharp-dotnet-windows.md).

### <a name="call-center-transcription"></a>Přepis nahrávek z call centra

Nahrávky z call centra se často analyzují pouze v případě, že při volání nastane problém. S využitím služby Speech je snadné přepsat všechny nahrávky do textové podoby. Jakmile budou v textové podobě, můžete je snadno indexovat pro [fulltextové vyhledávání](https://docs.microsoft.com/azure/search/search-what-is-azure-search) nebo použít [Analýzu textu](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) k rozpoznání mínění, jazyka a klíčových frází.

Pokud se ve vašich nahrávkách z call centra ve velké míře používá odborná terminologie (například názvy produktů nebo žargon IT), můžete vytvořit [jazykový model](how-to-customize-language-model.md), kterým službu Speech tuto slovní zásobu naučíte. Vlastní [akustický model](how-to-customize-acoustic-models.md) může službě Speech pomoct porozumět ne zcela optimálním telefonním spojením.

Další informace o tomto scénáři najdete v článku o [dávkovém přepisu](batch-transcription.md) pomocí služby Speech.

### <a name="voice-bots"></a>Hlasoví roboti

[Roboti](https://dev.botframework.com/) představují stále oblíbenější způsob spojování uživatelů s požadovanými informacemi a zákazníků s oblíbenými firmami. Přidáním konverzačního uživatelského rozhraní na web nebo do aplikace zjednodušíte hledání funkcí a zrychlíte k nim přístup. Se službou Speech získává plynulost této konverzace nový rozměr díky tomu, že služba věcně reaguje na mluvené dotazy.

Pokud chcete do svého robota s podporou hlasových služeb přidat jedinečný charakter (a tím posílit vaši značku), můžete mu dát vlastní hlas. Vytvoření vlastního hlasu je dvoustupňový proces. Nejprve [pořídíte nahrávky](record-custom-voice-samples.md) hlasu, který chcete použít. Pak můžete [tyto nahrávky odeslat](how-to-customize-voice-font.md) (společně s textovým přepisem) na [portál pro přizpůsobení hlasu](https://cris.ai/Home/CustomVoice) služby Speech, která se postará o zbytek. Po vytvoření vlastního hlasu ho můžete jednoduše použít ve své aplikaci.

## <a name="next-steps"></a>Další kroky

Získejte klíč předplatného pro službu Speech.

> [!div class="nextstepaction"]
> [Vyzkoušejte si službu Speech zdarma](get-started.md)
