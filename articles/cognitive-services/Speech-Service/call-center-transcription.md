---
title: Služba Call Center – přepis – Speech Service
titleSuffix: Azure Cognitive Services
description: Běžným scénářem pro převod řeči na text jsou zdlouhavého přepisování velké objemy dat telefonního subsystému, které pocházejí z různých systémů, jako je například interaktivní hlasová odezva (IRV). Pomocí služby Speech a sjednoceného modelu řeči může společnost získat kvalitní přepisy pomocí systémů pro záznam zvuku.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: c592055be1987786b94623bde5352e2a3cc0e092
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91630147"
---
# <a name="speech-service-for-telephony-data"></a>Služba řeči pro data telefonního subsystému

Data telefonního subsystému, která se generují prostřednictvím landlines, mobilních telefonů a přepínačů, jsou typicky nízká kvalita a v rozmezí 8 KHz, které vytváří problémy při převodu řeči na text. Nejnovější modely rozpoznávání řeči z Excelu služby Speech na zdlouhavého přepisování tato data telefonního subsystému, a to i v případě, že jsou data obtížná člověkem srozumitelná. Tyto modely jsou vyškolené s velkými objemy telefonních dat a mají nejlepší přesnost rozpoznávání na trhu, a to i v prostředích s vysokou úrovní šumu.

Běžným scénářem pro převod řeči na text jsou zdlouhavého přepisování velké objemy telefonních dat, která může pocházet z různých systémů, jako je například interaktivní hlasová odezva (IRV). Zvuk, který tyto systémy poskytují, může být stereo nebo mono a holý a nezpracovaný po nedokončeném zpracování na signálu. Pomocí služby Speech a sjednoceného modelu řeči může společnost získat kvalitní přepisy, ať už se systémy používají k zachycení zvuku.

Data telefonního subsystému se dají použít k lepšímu pochopení potřeb vašich zákazníků, identifikaci nových marketingových příležitostí nebo vyhodnocení výkonu agentů služby Call Center. Po přepisuí dat může společnost použít výstup pro účely, jako je například Vylepšená telemetrie, identifikovat klíčové fráze nebo analyzovat zákaznickou mínění.

Technologie, které jsou uvedené na této stránce, jsou interně Microsoftem pro různé služby zpracování volání podpory v reálném čase i v režimu dávky.

Pojďme se podívat na některé technologie a související funkce, které nabízí služba pro rozpoznávání řeči.

> [!IMPORTANT]
> Sjednocený model služby Speech je vyškolený s různými daty a nabízí jedno modelové řešení pro řadu scénářů od diktování až po účely analýzy telefonního subsystému.

## <a name="azure-technology-for-call-centers"></a>Technologie Azure pro centra volání

Kromě funkčního aspektu funkcí služby Speech, jejich primární účel – při použití na centrum volání – slouží ke zlepšení prostředí pro zákazníky. V tomto ohledu existují tři jasné domény:

- Analýza po volání, která je v podstatě dávkové zpracování záznamů volání po volání.
- Analýzy v reálném čase, což je zpracování zvukového signálu k extrakci různých přehledů, jak probíhá volání (s mínění se jedná o výrazný případ použití).
- Hlasová asistenti (roboty), ať už si nastavili dialog mezi zákazníkem a robotem v pokusu o vyřešení problému zákazníka bez účasti na agentovi, nebo použití protokolů v programu umělal Intelligence (AI), aby se mohl pomáhat na agentovi.

Typický diagram architektury implementace scénáře Batch je znázorněný na obrázku níže v ![ architektuře pro přepisy služby Call Center.](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Komponenty technologie Speech Analytics

Bez ohledu na to, jestli je doména po volání nebo v reálném čase, nabízí Azure sadu vyspělých a nově vznikajících technologií, které zlepšují prostředí pro zákazníky.

### <a name="speech-to-text-stt"></a>Převod řeči na text (STT)

[Převod řeči na text](speech-to-text.md) je co nejužitečnější funkce v jakémkoli řešení pro centrum volání. Vzhledem k tomu, že mnoho procesů pro průběžné analýzy spoléhá na přepisu text, je četnost výskytu chyb slov (_wer_) největší důležitost. Jedním z klíčových problémů v přepisu služby Call Center je hluk, který je předaný v centru volání (například ostatní agenti mluví na pozadí), bohatou škálu jazykových národních prostředí a dialektů a také nízkou kvalitou vlastního telefonního signálu. Funkce WER je vysoce korelujá s tím, jak dobře jsou zvukové a jazykové modely učené pro dané národní prostředí, takže je důležité možnost přizpůsobit model pro vaše národní prostředí. Naše nejnovější sjednocené modely 4. x jsou řešením pro přesnost přepisu i latenci. S využitím desítek tisíc hodin akustických dat a miliard lexikálních informací představují sjednocené modely nejpřesnější modely na trhu, aby přepisovat data centra volání.

### <a name="sentiment"></a>Mínění

Měření, jestli má zákazník dobré prostředí, je jedním z nejdůležitějších oblastí analýzy řeči při použití v prostoru volání centra. Naše [rozhraní API pro dávkové přepisy](batch-transcription.md) nabízí analýzu mínění na utterance. Můžete agregovat sadu hodnot získaných jako součást přepisu volání, abyste zjistili mínění volání pro vaše agenty i zákazníka.

### <a name="silence-non-talk"></a>Netiché (bez rozhovorů)

Nejedná se o neobvyklou hodnotu 35 procent volání podpory, které by volalo nerozhovorový čas. Některé scénáře, pro které se nemluví, jsou agenti, kteří hledají předchozí historii případu se zákazníkem, agenti pomocí nástrojů, které jim umožňují přístup k desktopu zákazníka a k provádění funkcí, zákazníkům, kteří se zablokují při čekání na přenos, a tak dále. Je velmi důležité vyhodnotit, když dojde ke tichému volání, protože se jedná o řadu důležitých Sensitivities zákazníků, ke kterým dochází v těchto typech scénářů, a jejich výskyt ve volání.

### <a name="translation"></a>Překlad

Některé společnosti experimentují s poskytováním překladných přepisů z volání podpory cizích jazyků, takže správci doručování mohou pochopit, jak se zákazníci setkávají na celém světě. Naše možnosti [překladu](/azure/cognitive-services/speech-service/speech-translation) se překročí. Pro velké množství národních prostředí můžeme přeložit zvuk na zvuk nebo zvuk na text.

### <a name="text-to-speech"></a>Převod textu na řeč

[Převod textu na řeč](text-to-speech.md) je další důležitou oblastí při implementaci roboty, která komunikuje se zákazníky. Typickou cestou je, že zákazník mluví, jejich hlas je přepisu na text, text se analyzuje pro záměry, odpověď je syntetizovaná na základě rozpoznaného záměru a pak se aktivuje buď vytvářená hlasová odezva. Samozřejmě to vše musí probíhat rychle, takže nízká latence je důležitou součástí úspěchu těchto systémů.

Naše kompletní latence je výrazně nízká pro různé technologie, jako je například [Převod řeči na text](speech-to-text.md), [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [robot Framework](https://dev.botframework.com/), [Převod textu na řeč](text-to-speech.md).

Naše nové hlasy jsou také odlišitelné od lidského hlasy. Naše hlasy vám umožní dát své robot své jedinečné preference.

### <a name="search"></a>Hledat

Další sešívání analýz je identifikace interakcí, u kterých došlo k určité události nebo zkušenostem. To se obvykle provádí s jedním ze dvou přístupů; buď vyhledávání ad hoc, kde uživatel jednoduše zadá frázi a systém odpoví, nebo více strukturovaný dotaz, kde analytik může vytvořit sadu logických příkazů, které identifikují scénář ve volání, a potom každé volání může být indexováno proti této sadě dotazů. Dobrým příkladem hledání je příkaz všudypřítomný dodržování předpisů "Toto volání se bude zaznamenávat z hlediska kvality... ". Mnohé společnosti chtějí zajistit, aby jejich agenti poskytovali tomuto právnímu omezení zákazníkům před tím, než se volání skutečně zaznamená. Většina analytických systémů má schopnost trendu chování zjištěného pomocí algoritmů dotazu nebo vyhledávání a vytváření sestav trendů je nakonec jednou z nejdůležitějších funkcí analytického systému. V [adresáři služby pro rozpoznávání](https://azure.microsoft.com/services/cognitive-services/directory/search/) koncových služeb je možné výrazně zvýšit možnosti indexování a vyhledávání.

### <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Tato oblast představuje jednu z náročnějších analytických aplikací a jednu z nich, která je výhodná z aplikace AI a strojového učení. Primární scénář v tomto případě je odvodit záměr zákazníka. Proč zákazník volá? Co je problém zákazníka? Proč má zákazník negativní zkušenosti? Naše [Služba pro analýzu textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) poskytuje sadu analýz, které vám umožní rychle upgradovat vaše ucelené řešení pro extrakci těchto důležitých klíčových slov nebo frází.

Pojďme se teď podívat na dávkové zpracování a kanály v reálném čase pro rozpoznávání řeči v několika dalších podrobnostech.

## <a name="batch-transcription-of-call-center-data"></a>Dávkové přepisy dat služby Call Center

Pro zdlouhavého přepisování hromadný zvuk jsme vyvinuli [rozhraní API služby Batch pro přepis](batch-transcription.md). Rozhraní API dávkového přepisu bylo vyvinuto, aby asynchronně přepisovat velké objemy zvukových dat. S ohledem na data centra volání zdlouhavého přepisování je naše řešení založeno na těchto pilířích:

- **Na základě** čtvrté generace sjednocených modelů nabízíme nepřekročenou kvalitu přepisu.
- **Latence** – chápeme, že při hromadném přepisu jsou přepisy potřebné k rychlému provedení. Úlohy přepisu iniciované prostřednictvím [rozhraní API služby Batch přepisu](batch-transcription.md) se okamžitě zařadí do fronty a jakmile se úloha spustí, bude se provádět rychleji, než je přepis v reálném čase.
- **Zabezpečení** – chápeme, že volání mohou obsahovat citlivá data. Je zaručeno, že zabezpečení je jednou z našich nejvyšší priority. Naše služba získala certifikace ISO, SOC, HIPAA a PCI.

Volání Center generuje velké objemy zvukových dat denně. Pokud vaše firma ukládá data telefonního subsystému do centrálního umístění, jako je například Azure Storage, můžete k asynchronnímu vypisování požadavků a přijímání přepisů použít [rozhraní API služby Batch přepisu](batch-transcription.md) .

Typické řešení používá tyto služby:

- Služba Speech slouží k přepisovat převodu řeči na text. K použití rozhraní API služby Batch pro přepis se vyžaduje standardní předplatné (S0) pro službu Speech. Bezplatné odběry (F0) nebudou fungovat.
- [Azure Storage](https://azure.microsoft.com/services/storage/) slouží k ukládání dat telefonního subsystému a přepisů vrácených rozhraním API služby Batch přepisu. Tento účet úložiště by měl používat oznámení, konkrétně pro přidání nových souborů. Tato oznámení slouží k aktivaci procesu přepisu.
- [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) slouží k vytvoření identifikátoru URI sdíleného přístupového podpisu (SAS) pro každý záznam a aktivaci požadavku HTTP POST pro spuštění přepisu. Kromě toho Azure Functions slouží k vytváření žádostí o načtení a odstranění přepisů pomocí rozhraní API dávkového přepisu.

Interně používáme výše uvedené technologie k podpoře zákaznických hovorů Microsoftu v dávkovém režimu.
:::image type="content" source="media/scenarios/call-center-batch-pipeline.png" alt-text="Technologie sloužící k podpoře zákaznických hovorů Microsoftu v režimu dávky.":::

## <a name="real-time-transcription-for-call-center-data"></a>Přepis v reálném čase pro data centra volání

Některé firmy se vyžadují pro přepisovat konverzací v reálném čase. Přepis v reálném čase se dá použít k identifikaci klíčových slov a triggerů, které jsou relevantní pro konverzaci, pro monitorování mínění, ke zlepšení dostupnosti nebo k poskytování překladů pro zákazníky a agenty, kteří nejsou nativními reproduktory.

Pro scénáře, které vyžadují přepis v reálném čase, doporučujeme použít [sadu Speech SDK](speech-sdk.md). V současné době je převod řeči na text k dispozici ve [více než 20 jazycích](language-support.md)a sada SDK je k dispozici v jazyce C++, C#, Java, Python, Node.js, objektivní-C a JavaScriptu. Ukázky jsou k dispozici v každém jazyku na [GitHubu](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Nejnovější novinky a aktualizace najdete v [poznámkách k verzi](releasenotes.md).

Interně používáme výše uvedené technologie k analýze v reálném čase zákaznických hovorů od Microsoftu, jak je znázorněno v následujícím diagramu.

![Architektura Batch](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Slovo na IVRs

Službu Speech lze snadno integrovat do libovolného řešení pomocí [sady Speech SDK](speech-sdk.md) nebo [REST API](rest-apis.md). Přepis centra volání ale může vyžadovat další technologie. Obvykle se vyžaduje připojení mezi systémem IRV a Azure. I když tyto komponenty nenabízíme, tady je popis toho, co připojení k IRV vyvolá.

Několik IRV nebo produktů služby Telephony Service (například Genesys nebo AudioCodes) nabízí možnosti integrace, které se dají využít k povolení příchozího a odchozího zvukového průchodu do služby Azure. V podstatě může vlastní služba Azure poskytovat konkrétní rozhraní pro definování relací telefonních hovorů (například spuštění volání nebo ukončení volání) a vystavit rozhraní WebSocket API pro příjem zvuku příchozího datového proudu, který se používá ve službě Speech. Odchozí odpovědi, jako je například přepis konverzace nebo připojení s rozhraním robota, je možné syntetizovat pomocí služby převodu textu na řeč od Microsoftu a vrátit se do IRV k přehrávání.

Dalším scénářem je přímá integrace s protokolem protokolu SIP (Session Iniciing Protocol). Služba Azure se připojuje k serveru SIP, takže získává příchozí datový proud a odchozí datový proud, který se používá pro fáze převodu řeči na text a převod textu na řeč. Pokud se chcete připojit k serveru SIP, máte k dispozici komerční nabídky, jako je Ozeki SDK nebo [týmy pro volání a schůzky rozhraní API](/graph/api/resources/communications-api-overview) (v současnosti ve verzi beta), které jsou navržené tak, aby podporovaly tento druh scénářů pro volání zvuku.

## <a name="customize-existing-experiences"></a>Přizpůsobení stávajících prostředí

 Služba Speech funguje dobře s integrovanými modely. Můžete ale chtít ještě více přizpůsobit a vyladit prostředí pro svůj produkt nebo prostředí. Možnosti přizpůsobení jsou v rozsahu od ladění akustického modelu až po jedinečná hlasová písma pro vaši značku. Po vytvoření vlastního modelu ho můžete použít s kteroukoli funkcí služby Speech Service v reálném čase nebo v režimu dávky.

| Služba Speech | Model | Description |
| -------------- | ----- | ----------- |
| Převod řeči na text | [Akustický model](how-to-customize-acoustic-models.md) | Vytvořte si vlastní akustický model pro aplikace, nástroje nebo zařízení, které se používají v různých prostředích, jako je třeba v kleci nebo v továrně, z nichž každá má konkrétní podmínky záznamu. Mezi příklady patří zvýrazněné rozpoznávání řeči, konkrétní šum na pozadí nebo použití konkrétního mikrofonu pro záznam. |
|                | [Jazykový model](how-to-customize-language-model.md) | Vytvořte vlastní jazykový model pro zlepšení přepisu slovníku a gramatiky specifické pro konkrétní odvětví, jako je lékařské terminologie nebo žargonu. |
|                | [Model výslovnosti](how-to-customize-pronunciation.md) | Pomocí vlastního modelu výslovnosti můžete definovat fonetický formulář a zobrazit ho pro slovo nebo termín. Je užitečné pro zpracování přizpůsobených podmínek, jako jsou názvy produktů nebo akronymy. Vše, co potřebujete začít, je soubor výslovnosti, což je jednoduchý `.txt` soubor. |
| Převod textu na řeč | [Hlasové písmo](how-to-customize-voice-font.md) | Vlastní hlasová písma umožňují vytvořit rozpoznatelný hlasový hlas pro vaši značku. Pro začátek zabere pouze malé množství dat. Větší údaje, které poskytnete, budou zvukové písmo větší, než je vaše hlasová. |

## <a name="sample-code"></a>Ukázka kódu

Vzorový kód je k dispozici na GitHubu pro každou funkci služby Speech. Tyto ukázky se týkají běžných scénářů, jako je čtení zvuku ze souboru nebo datového proudu, průběžné a jednorázové rozpoznávání a práce s vlastními modely. Pomocí těchto odkazů můžete zobrazit ukázky sady SDK a REST:

- [Ukázky překladu řeči na text a rozpoznávání řeči (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Ukázky pro dávková přepisy (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Ukázky převodu textu na řeč (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Referenční dokumenty

- [Speech SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: Převod řeči na text](rest-speech-to-text.md)
- [REST API: převod textu na řeč](rest-text-to-speech.md)
- [REST API: přepis a přizpůsobení Batch](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získejte zdarma klíč předplatného služby Speech](overview.md#try-the-speech-service-for-free)
