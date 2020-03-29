---
title: Přepis call centra - služba řeči
titleSuffix: Azure Cognitive Services
description: Běžným scénářem pro převod řeči na text je přepis velkých objemů telefonních dat, které pocházejí z různých systémů, jako je například interaktivní hlasová odezva (IVR). Pomocí služby Speech service a modelu sjednocené řeči může firma získat vysoce kvalitní přepisy se systémy pro digitalizaci zvuku.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d959f4948d6b848f3b399c1310add06991d72012
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806316"
---
# <a name="speech-service-for-telephony-data"></a>Služba řeči pro telefonní data

Telefonní data generovaná prostřednictvím pevných linek, mobilních telefonů a rádií jsou obvykle nekvalitní a úzkopásmová v pásmu 8 KHz, což vytváří problémy při převodu převodu řeči na text. Nejnovější modely rozpoznávání řeči ze služby Rozpoznávání řeči vynikají v přepisu těchto telefonních dat, a to i v případech, kdy je pro člověka obtížné porozumět. Tyto modely jsou trénované s velkými objemy telefonních dat a mají nejlepší přesnost rozpoznávání na trhu, a to i v hlučném prostředí.

Běžným scénářem pro převod řeči na text je přepis velkých objemů telefonních dat, které mohou pocházet z různých systémů, jako je například interaktivní hlasová odezva (IVR). Zvuk, který tyto systémy poskytují, může být stereo nebo mono a surový s malým až žádným post ovým zpracováním provedeným na signálu. Pomocí služby Řeč a modelu sjednocené řeči může firma získat vysoce kvalitní přepisy bez ohledu na to, které systémy se používají k zachycení zvuku.

Telefonní data lze použít k lepšímu pochopení potřeb vašich zákazníků, identifikaci nových marketingových příležitostí nebo k vyhodnocení výkonu agentů call centra. Po přepisu dat může firma použít výstup pro účely, jako je vylepšená telemetrická data, identifikace klíčových frází nebo analýza mínění zákazníků.

Technologie popsané na této stránce jsou společností Microsoft interně pro různé služby zpracování volání podpory, a to jak v reálném čase, tak v dávkovém režimu.

Podívejme se na některé technologie a související funkce, které služba Speech nabízí.

> [!IMPORTANT]
> Jednotný model služby Speech je trénovaný s různými daty a nabízí řešení jednoho modelu pro řadu scénářů od diktování až po analýzu telefonního subsystému.

## <a name="azure-technology-for-call-centers"></a>Technologie Azure pro call centra

Kromě funkčního aspektu funkcí služby Speech je jejich primárním účelem – při aplikaci na call centrum – zlepšit zákaznickou zkušenost. V tomto ohledu existují tři jasné domény:

- Analýza po volání, což je v podstatě dávkové zpracování záznamů hovorů po volání.
- Analýza v reálném čase, která zpracovává zvukový signál, aby získala různé poznatky, protože volání probíhá (s sentimentem je prominentní případ použití).
- Hlasoví asistenti (roboty), buď řízení dialogu mezi zákazníkem a botem ve snaze vyřešit problém zákazníka bez účasti agenta, nebo je použití protokolů umělé inteligence (AI) na pomoc agentovi.

Typický diagram architektury implementace dávkového scénáře je znázorněn na ![obrázku pod architekturou přepisu call centra.](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Součásti technologie analýzy řeči

Ať už je doména po volání nebo v reálném čase, Azure nabízí sadu vyspělých a vznikajících technologií pro zlepšení zákaznického prostředí.

### <a name="speech-to-text-stt"></a>Převod řeči na text (STT)

[Funkce převodu řeči na text](speech-to-text.md) je nejvyhledávanější funkcí v jakémkoli řešení call centra. Vzhledem k tomu, že mnoho následných analytických procesů závisí na přepisovaném textu, je nanejvýš důležité slovo error rate (_WER_). Jednou z klíčových výzev v přepisu call centra je hluk, který je převládající v call centru (například jiní agenti mluví v pozadí), bohatá rozmanitost jazykových prostředí a dialektů, stejně jako nízká kvalita skutečného telefonního signálu. WER je vysoce koreluje s tím, jak dobře akustické a jazykové modely jsou vyškoleni pro dané národní prostředí, tedy schopnost přizpůsobit model pro vaše národní prostředí je důležité. Naše nejnovější modely Unified verze 4.x jsou řešením přesnosti přepisu i latence. Unifikované modely, které jsou vyškoleny s desítkami tisíc hodin akustických dat a miliardami lexikálních informací, jsou nejpřesnějšími modely na trhu pro přepis dat call center.

### <a name="sentiment"></a>Mínění

Měření, zda má zákazník dobré zkušenosti, je jednou z nejdůležitějších oblastí analýzy řeči při aplikaci na místo v call centru. Naše [rozhraní API pro batch transkripce](batch-transcription.md) nabízí analýzu mínění na utterance. Můžete agregovat sadu hodnot získaných jako součást přepisu volání k určení mínění volání pro vaše agenty i zákazníka.

### <a name="silence-non-talk"></a>Ticho (non-talk)

To není neobvyklé pro 35 procent podpory volání, které mají být to, co nazýváme non-talk čas. Některé scénáře, pro které dochází k non-talk jsou: agenti vyhledání předchozí historie případů se zákazníkem, agenti pomocí nástrojů, které jim umožňují přístup k ploše zákazníka a provádět funkce, zákazníci, kteří čekají na převod a tak dále. Je nesmírně důležité odhadnout, kdy ticho dochází ve volání, protože existuje počet důležitých citlivostí zákazníků, ke kterým dochází kolem těchto typů scénářů a kde k nim dochází ve volání.

### <a name="translation"></a>Překlad

Některé společnosti experimentují s poskytováním přeložených přepisů z volání podpory cizích jazyků, aby správci dodávek mohli pochopit celosvětové zkušenosti svých zákazníků. Naše [překladatelské](translation.md) schopnosti jsou nepřekonatelné. Můžeme přeložit audio-to-audio nebo audio-to-text pro velký počet národních prostředí.

### <a name="text-to-speech"></a>Převod textu na řeč

[Převod textu na řeč](text-to-speech.md) je další důležitou oblastí při implementaci robotů, kteří komunikují se zákazníky. Typickou cestou je, že zákazník mluví, jejich hlas je přepsán na text, text je analyzován na záměry, odpověď je syntetizována na základě rozpoznaného záměru a pak se aktivum buď objeví zákazníkovi, nebo syntetizovaná hlasová odpověď je Generované. Samozřejmě to vše musí nastat rychle - tedy nízká latence je důležitou součástí úspěchu těchto systémů.

Naše end-to-end latence je značně nízká pro různé technologie, jako je [například řeč na text](speech-to-text.md), [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [Bot Framework](https://dev.botframework.com/), Převod textu [na řeč](text-to-speech.md).

Naše nové hlasy jsou také k nerozeznání od lidských hlasů. Můžete použít naše hlasy, aby váš bot jeho jedinečnou osobnost.

### <a name="search"></a>Search

Dalším základem analýzy je identifikace interakcí, kde došlo k určité události nebo prostředí. To se obvykle provádí s jedním ze dvou přístupů; buď ad hoc vyhledávání, kde uživatel jednoduše zadá frázi a systém odpoví, nebo strukturovanější dotaz, kde analytik může vytvořit sadu logických příkazů, které identifikují scénář ve volání a pak každé volání lze indexovat proti této sadě dotazů. Dobrým příkladem vyhledávání je všudypřítomné prohlášení o shodě "tato výzva se zaznamenává pro účely kvality... ". Mnoho společností se chce ujistit, že jejich agenti poskytují toto prohlášení o vyloučení odpovědnosti zákazníkům před tím, než je hovor skutečně zaznamenán. Většina analytických systémů má schopnost trendovat chování nalezené algoritmy dotazů /vyhledávání a toto vykazování trendů je v konečném důsledku jednou z nejdůležitějších funkcí analytického systému. Prostřednictvím [adresáře služeb Cognitive](https://azure.microsoft.com/services/cognitive-services/directory/search/) lze vaše komplexní řešení výrazně vylepšit pomocí možností indexování a vyhledávání.

### <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Tato oblast je jednou z náročnějších analytických aplikací a jednou z nich těží z aplikace ai a strojového učení. Primární scénář v tomto případě je odvodit záměr zákazníka. Proč zákazník volá? Jaký je problém se zákazníkem? Proč měl zákazník negativní zkušenosti? Naše [služba pro analýzu textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) poskytuje sadu analýz po vybalení z krabice pro rychlou upgrade vašeho komplexního řešení pro extrahování těchto důležitých klíčových slov nebo frází.

Podívejme se nyní na dávkové zpracování a kanály v reálném čase pro rozpoznávání řeči v trochu podrobněji.

## <a name="batch-transcription-of-call-center-data"></a>Dávkový přepis dat call centra

Pro přepis hromadného zvuku jsme [vyvinuli rozhraní API pro přepis dávky](batch-transcription.md). Rozhraní API pro batch transcription bylo vyvinuto pro asynchronně přepisování velkých objemů zvukových dat. Pokud jde o přepis dat call centra, naše řešení je založeno na těchto pilířích:

- **Přesnost** - U modelů Unified čtvrté generace nabízíme nepřekonatelnou kvalitu přepisu.
- **Latence** – chápeme, že při hromadné transkripce, přepisy jsou potřeba rychle. Úlohy přepisu iniciované prostřednictvím [rozhraní API pro přepis dávky](batch-transcription.md) budou okamžitě zařazeny do fronty a jakmile se úloha spustí, bude provedena rychleji než přepis v reálném čase.
- **Zabezpečení** – chápeme, že volání mohou obsahovat citlivá data. Buďte ujištěni, že bezpečnost je jednou z našich nejvyšších priorit. Naše služba získala certifikace ISO, SOC, HIPAA, PCI.

Call centra generují velké objemy zvukových dat na denní bázi. Pokud vaše firma ukládá telefonní data v centrálním umístění, jako je azure storage, můžete použít [rozhraní API dávkového přepisu](batch-transcription.md) asynchronně požadovat a přijímat přepisy.

Typické řešení používá tyto služby:

- Služba Řeč se používá k přepisu řeči na text. Standardní předplatné (S0) pro službu řeči je nutné použít rozhraní API dávkového přepisu. Bezplatné předplatné (F0) nebude fungovat.
- [Azure Storage](https://azure.microsoft.com/services/storage/) se používá k ukládání dat telefonního subsystému a přepisy vrácené rozhraní api batch transcription. Tento účet úložiště by měl používat oznámení, konkrétně pro při přidání nových souborů. Tato oznámení se používají k aktivaci procesu přepisu.
- [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) se používá k vytvoření identifikátoru URI sdílených přístupových podpisů (SAS) pro každý záznam a aktivaci požadavku HTTP POST pro spuštění přepisu. Kromě toho Azure Functions se používá k vytvoření požadavků na načtení a odstranění přepisů pomocí rozhraní API batch transcription.

Interně používáme výše uvedené technologie pro podporu zákaznických hovorů společnosti Microsoft v dávkovém režimu.
![Dávková architektura](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Přepis dat call centra v reálném čase

Některé podniky jsou povinny přepisovat konverzace v reálném čase. Přepis v reálném čase lze použít k identifikaci klíčových slov a spuštění vyhledávání obsahu a zdrojů relevantních pro konverzaci, pro sledování mínění, ke zlepšení přístupnosti nebo k poskytování překladů zákazníkům a agentům, kteří nejsou nativní. Reproduktory.

Pro scénáře, které vyžadují přepis v reálném čase, doporučujeme použít [sadu Speech SDK](speech-sdk.md). V současné době je sada převodřeči na text k dispozici ve [více než 20 jazycích](language-support.md)a sada SDK je k dispozici v jazycích C++, C#, Java, Python, Node.js, Objective-C a JavaScript. Ukázky jsou k dispozici v každém jazyce na [GitHubu](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Nejnovější zprávy a aktualizace naleznete v [tématu Poznámky k verzi](releasenotes.md).

Interně používáme výše uvedené technologie k analýze v reálném čase volání zákazníků společnosti Microsoft, jak k nim dochází, jak je znázorněno na následujícím diagramu.

![Dávková architektura](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Slovo na IVR

Službu Řeč lze snadno integrovat do libovolného řešení pomocí sady [Speech SDK](speech-sdk.md) nebo [rozhraní REST API](rest-apis.md). Přepis call centra však může vyžadovat další technologie. Připojení mezi systémem IVR a Azure je obvykle vyžadováno. I když takové komponenty nenabízíme, zde je popis toho, co znamená spojení s IVR.

Několik produktů služby IVR nebo telefonní služby (například Genesys nebo AudioCodes) nabízí možnosti integrace, které lze využít k povolení příchozího a odchozího přenosu zvuku do služby Azure. V podstatě vlastní služba Azure může poskytnout konkrétní rozhraní pro definování relací telefonního hovoru (například zahájení volání nebo ukončení volání) a vystavit rozhraní API WebSocket pro příjem příchozího datového proudu zvuku, který se používá se službou Speech. Odchozí odpovědi, jako je přepis konverzace nebo připojení k rozhraní Bot Framework, lze syntetizovat se službou převodu textu na řeč společnosti Microsoft a vrátit se do ivr pro přehrávání.

Dalším scénářem je přímá integrace s protokolem SIP (Session Initiation Protocol). Služba Azure se připojuje k SERVERU SIP, čímž získává příchozí datový proud a odchozí datový proud, který se používá pro fáze převodu řeči na text a převod textu na řeč. Chcete-li se připojit k serveru SIP, existují komerční softwarové nabídky, jako je například Ozeki SDK nebo [rozhraní API pro volání a schůzky týmů](/graph/api/resources/communications-api-overview) (aktuálně v beta verzi), které jsou navrženy tak, aby podporovaly tento typ scénáře pro zvukové hovory.

## <a name="customize-existing-experiences"></a>Přizpůsobení stávajících prostředí

 Služba Řeč funguje dobře s vestavěnými modely. Můžete však dále přizpůsobit a vyladit prostředí pro váš produkt nebo prostředí. Možnosti přizpůsobení sahají od akustického ladění modelů až po jedinečná hlasová písma pro vaši značku. Po vytvoření vlastního modelu jej můžete použít s některou z funkcí služby Rozpoznávání řeči v režimu v reálném čase nebo v dávkovém režimu.

| Služba Speech | Model | Popis |
| -------------- | ----- | ----------- |
| Převod řeči na text | [Akustický model](how-to-customize-acoustic-models.md) | Vytvořte vlastní akustický model pro aplikace, nástroje nebo zařízení, které se používají v konkrétních prostředích, jako je v autě nebo ve výrobním závodě, z nichž každý má specifické podmínky nahrávání. Mezi příklady patří řeč s diakritikou, specifické zvuky na pozadí nebo použití konkrétního mikrofonu pro nahrávání. |
|                | [Jazykový model](how-to-customize-language-model.md) | Vytvořte vlastní jazykový model pro zlepšení přepisu slovníku a gramatiky specifického pro dané odvětví, jako je lékařská terminologie nebo žargon IT. |
|                | [Model výslovnosti](how-to-customize-pronunciation.md) | Pomocí vlastního modelu výslovnosti můžete definovat fonetický formulář a zobrazit slovo nebo termín. Je to užitečné pro zpracování přizpůsobených termínů, jako jsou názvy produktů nebo zkratky. Vše, co potřebujete, abyste mohli začít, je soubor `.txt` výslovnosti, což je jednoduchý soubor. |
| Převod textu na řeč | [Hlasové písmo](how-to-customize-voice-font.md) | Vlastní hlasová písma umožňují vytvořit pro vaši značku rozpoznatelný, jedinečný hlas. Chcete-li začít, stačí jen malé množství dat. Čím více dat poskytnete, tím přirozenější a lidské písmo bude znít. |

## <a name="sample-code"></a>Ukázka kódu

Ukázkový kód je k dispozici na GitHubu pro každou z funkcí služby Rozpoznávání řeči. Tyto ukázky zahrnují běžné scénáře, jako je čtení zvuku ze souboru nebo datového proudu, nepřetržité rozpoznávání a rozpoznávání jedním výstřelem a práce s vlastními modely. Tyto odkazy slouží k zobrazení ukázek sady SDK a REST:

- [Ukázky překladu řeči na text a řeč (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Vzorky přepisu dávky (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Ukázky převodu textu na řeč (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Referenční dokumenty

- [Speech SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [ROZHRANÍ REST API: Převod řeči na text](rest-speech-to-text.md)
- [ROZHRANÍ REST API: Převod textu na řeč](rest-text-to-speech.md)
- [ROZHRANÍ REST API: Dávkový přepis a přizpůsobení](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získejte zdarma klíč předplatného služby Speech](get-started.md)
