---
title: Služba Call Center – přepis – Speech Service
titleSuffix: Azure Cognitive Services
description: Běžným scénářem pro převod řeči na text jsou zdlouhavého přepisování velké objemy telefonních dat, která může pocházet z různých systémů, jako je například interaktivní hlasová odezva (IRV). Zvuk může být stereo nebo mono a RAW s malým až žádným zpracováním prováděným na signálu. Pomocí služeb Speech a sjednoceného modelu řeči může společnost získat kvalitní přepisy s mnoha systémy pro záznam zvuku.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 4f5163ba448e4cc7e18b0e794a44003ce5ab1dce
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516763"
---
# <a name="speech-services-for-telephony-data"></a>Hlasové služby pro data telefonního subsystému

Data telefonního subsystému, která se generují prostřednictvím landlines, mobilních telefonů a přepínačů, jsou typicky nízká kvalita a v rozmezí 8 KHz, které vytváří problémy při převodu řeči na text. Nejnovější modely rozpoznávání řeči z Excelu pro služby Azure Speech Services na zdlouhavého přepisování tato data telefonního subsystému, a to i v případech, kdy jsou data obtížná člověkem srozumitelná. Tyto modely jsou vyškolené s velkými objemy dat telefonního subsystému a mají nejlepší přesnost rozpoznávání trhu, a to i v prostředích s vysokou úrovní šumu.

Běžným scénářem pro převod řeči na text jsou zdlouhavého přepisování velké objemy telefonních dat, která může pocházet z různých systémů, jako je například interaktivní hlasová odezva (IRV). Zvuk, který tyto systémy poskytují, může být stereo nebo mono a holý a nezpracovaný po nedokončeném zpracování na signálu. Díky službám Speech a sjednocenému modelu řeči může společnost získat kvalitní přepisy bez ohledu na systémy použité k zachytávání zvuku.

Data telefonního subsystému se dají použít k lepšímu pochopení potřeb vašich zákazníků, identifikaci nových marketingových příležitostí nebo vyhodnocení výkonu agentů služby Call Center. Po přepisuí dat může společnost využít výstup pro lepší telemetrii, identifikovat klíčové fráze nebo analyzovat zákaznickou mínění.

Technologie, které jsou uvedené na této stránce, jsou interně Microsoftem pro různé služby zpracování volání podpory v reálném čase i v režimu dávky.

Pojďme si projít některé technologie a související funkce, které nabízí Azure Speech Services.

> [!IMPORTANT]
> Unified model služby Speech je vyškolený s různými daty a nabízí řešení s jedním modelem v několika scénářích od diktování až po účely analýzy telefonního subsystému.

## <a name="azure-technology-for-call-centers"></a>Technologie Azure pro centra volání

Kromě funkčního aspektu služeb rozpoznávání řeči jejich primární účel – při použití na centrum volání – slouží ke zlepšení prostředí pro zákazníky. V tomto ohledu existují tři jasné domény:

* Analýza po volání, která znamená dávkové zpracování záznamů volání
* Analýzy zvukového signálu v reálném čase pro extrakci různých přehledů, jak probíhá volání (s mínění se jedná o výrazný případ použití) a
* Virtuální asistenti (roboty) – Řídící dialog mezi zákazníkem a robotem v pokusu o vyřešení problému zákazníka bez účasti agenta nebo použití protokolů AI pro pomoc s agentem.

Typický diagram architektury implementace scénáře Batch je znázorněný na obrázku níže ](media/scenarios/call-center-transcription-architecture.png) ![Call Center přepis architektury.

## <a name="speech-analytics-technology-components"></a>Komponenty technologie Speech Analytics

Bez ohledu na to, jestli je doména na vyžádání nebo v reálném čase, nabízí Azure sadu vyspělých a nově vznikajících technologií pro zlepšení prostředí pro zákazníky.

### <a name="speech-to-text-stt"></a>Převod řeči na text (STT)

[Převod řeči na text](speech-to-text.md) je co nejužitečnější po funkci v jakémkoli řešení služby Call Center. Vzhledem k tomu, že spousta procesů pro přenos dat spoléhá na přepisu text, je četnost chyb slov (WER) největší důležitost. Jedním z klíčových problémů v přepisu služby Call Center je hluk, který je předaný v centru volání (například ostatní agenti mluví na pozadí), bohatou škálu jazykových národních prostředí a dialektů a také nízkou kvalitou vlastního telefonního signálu. Funkce WER je vysoce korelujá s tím, jak dobře jsou zvukové a jazykové modely učené pro dané národní prostředí, takže je důležité přizpůsobit model pro vaše národní prostředí. Naše nejnovější sjednocené modely 4. x jsou řešením pro přesnost přepisu i latenci. Vyškolený s desítkami tisíc hodin akustických dat a miliardy lexikálních informací o sjednocených modelech je nejpřesnější modely na trhu přepisovat data centra volání.

### <a name="sentiment"></a>Zabarvení
Měření, jestli má zákazník dobré prostředí, je jedním z nejdůležitějších oblastí analýzy řeči při použití v prostoru volání centra. Naše [rozhraní API pro dávkové přepisy](batch-transcription.md) nabízí analýzu mínění na utterance. Můžete agregovat sadu hodnot získaných jako součást přepisu volání, abyste zjistili mínění volání pro vaše agenty i zákazníka.

### <a name="silence-non-talk"></a>Netiché (bez rozhovorů)
Nejedná se o neobvyklou hodnotu 35 procent volání podpory, které by volalo nerozhovorový čas. Některé scénáře, které nemluví, jsou: agenti, kteří hledají předchozí historii případu u zákazníka, agenti pomocí nástrojů, které jim umožňují přístup k desktopu zákazníka a k provádění funkcí, zákazníkům, kteří pracují s blokovaným přenosem a tak dále. Je velmi důležité, aby bylo možné posoudit, zda se ve volání vyskytuje tichost, protože se jedná o počet důležitých Sensitivities zákazníků, které se vyskytují v těchto typech scénářů, a jejich výskyt ve volání.

### <a name="translation"></a>Překlad
Některé společnosti experimentují s poskytováním překladových přepisů z cizích jazyků, takže správci doručování můžou porozumět celosvětovým zkušenostem zákazníků. Naše možnosti [překladu](translation.md) se překročí. Zvuk můžete převést na zvuk nebo zvuk na text z velkého počtu národních prostředí.

### <a name="text-to-speech"></a>Převod textu na řeč
[Převod textu na řeč](text-to-speech.md) je další důležitou oblastí při implementaci roboty, která komunikuje se zákazníky. Typickou cestou je, že zákazník mluví, jejich hlas je přepisu na text, text je analyzován pro záměry, odpověď je syntetizovaná na základě rozpoznaného záměru a pak je Asset buď povrchový na zákazníka, nebo syntetizovaná hlasová odezva. dojde. Samozřejmě to vše musí probíhat rychle, takže latence je důležitou součástí v úspěšnosti těchto systémů.

Naší kompletní latence je poměrně nízká úvaha o různých technologiích, jako je například [Převod řeči na text](speech-to-text.md), [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [robot Framework](https://dev.botframework.com/), [Převod textu na řeč](text-to-speech.md).

Naše nové hlasy jsou také odlišitelné od lidského hlasy. Můžete použít hlasy a dát svým robotovi svůj vlastní osobnost.

### <a name="search"></a>Hledat
Další sešívání analýz je identifikace interakcí, u kterých došlo k určité události nebo zkušenostem. To se obvykle provádí jedním ze dvou přístupů, a to buď pomocí služby ad hoc Search, kde uživatel jednoduše zadá frázi a systém reaguje, nebo složitější dotaz, kde analytik může vytvořit sadu logických příkazů, které identifikují scénář ve volání. a pak každé volání může být indexováno na základě těchto sad dotazů. Dobrým příkladem hledání je příkaz všudypřítomný dodržování předpisů "Toto volání se bude zaznamenávat z hlediska kvality... "– tolik společností chce zajistit, aby jejich agenti poskytovali tomuto právnímu omezení zákazníkům před tím, než je volání skutečně zaznamenáno. Většina analytických systémů má schopnost trendů najít chování nalezené pomocí algoritmů/Search dotazů – protože vytváření sestav trendů je nakonec jednou z nejdůležitějších funkcí analytického systému. V [adresáři služby pro rozpoznávání](https://azure.microsoft.com/services/cognitive-services/directory/search/) koncových služeb je možné výrazně zvýšit možnosti indexování a vyhledávání.

### <a name="key-phrase-extraction"></a>Extrakce klíčových frází
Tato oblast je jednou z náročnějších analytických aplikací a jedna z nich je výhodná z aplikace AI a ML. Hlavním scénářem je zde odvodit záměr zákazníka. Proč zákazník volá? Co je problém zákazníka? Proč má zákazník negativní zkušenosti? Naše [Služba pro analýzu textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) poskytuje sadu analýz, které vám umožní rychle upgradovat vaše koncové řešení a extrahovat tak důležitá klíčová slova nebo fráze.

Pojďme se teď podívat na dávkové zpracování a kanály v reálném čase pro rozpoznávání řeči v několika dalších podrobnostech.

## <a name="batch-transcription-of-call-center-data"></a>Dávkové přepisy dat služby Call Center

V případě zdlouhavého přepisování hromadného zvuku jsme vyvinuli [rozhraní API služby Batch pro přepis](batch-transcription.md). Rozhraní API dávkového přepisu bylo vyvinuto, aby asynchronně přepisovat velké objemy zvukových dat. S ohledem na data centra volání zdlouhavého přepisování je naše řešení založeno na těchto pilířích:

* **Přesnost**: u sjednocených modelů založených na čtvrté generaci nabízíme nepřekročenou kvalitu přepisu.
* **Latence**: rozumíme, že při hromadném přepisu jsou přepisy potřebné k rychlému provedení. Úlohy přepisu iniciované prostřednictvím [rozhraní API služby Batch přepisu](batch-transcription.md) se okamžitě zařadí do fronty a jakmile se úloha spustí, bude se provádět rychleji, než je přepis v reálném čase.
* **Zabezpečení**: rozumíme, že volání mohou obsahovat citlivá data. Je zaručeno, že zabezpečení je jednou z našich nejvyšší priority. Naše služba získala certifikace ISO, SOC, HIPAA a PCI.

Volání Center generuje velké objemy zvukových dat denně. Pokud vaše firma ukládá data telefonního subsystému do centrálního umístění, jako je například Azure Storage, můžete k asynchronnímu vypisování požadavků a přijímání přepisů použít [rozhraní API služby Batch přepisu](batch-transcription.md) .

Typické řešení používá tyto služby:

* Služba Azure Speech Services slouží k přepisovat převodu řeči na text. K použití rozhraní API služby Batch pro přepis se vyžaduje standardní předplatné (takže) pro služby řeči. Bezplatné odběry (F0) nebudou fungovat.
* [Azure Storage](https://azure.microsoft.com/services/storage/) slouží k ukládání dat telefonního subsystému a přepisů vrácených rozhraním API služby Batch přepisu. Tento účet úložiště by měl používat oznámení, konkrétně pro přidání nových souborů. Tato oznámení slouží k aktivaci procesu přepisu.
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) slouží k vytvoření identifikátoru URI sdíleného přístupového podpisu (SAS) pro každý záznam a aktivaci požadavku HTTP POST pro spuštění přepisu. Kromě toho Azure Functions slouží k vytváření žádostí o načtení a odstranění přepisů pomocí rozhraní API dávkového přepisu.
* [Webhooky](webhooks.md) slouží k získání oznámení, když jsou přepisy dokončeny.

Interně používáme výše uvedené technologie k podpoře zákaznických hovorů Microsoftu v dávkovém režimu.
Architektura ![Batch ](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Přepis v reálném čase pro data centra volání

Některé firmy se vyžadují pro přepisovat konverzací v reálném čase. Přepis v reálném čase se dá použít k identifikaci klíčových slov a triggerů, které jsou relevantní pro konverzaci, pro monitorování mínění, ke zlepšení dostupnosti nebo k poskytování překladů pro zákazníky a agenty, kteří nejsou nativní. mluvčích.

Pro scénáře, které vyžadují přepis v reálném čase, doporučujeme použít [sadu Speech SDK](speech-sdk.md). V současné době je převod řeči na text k dispozici ve [více než 20 jazycích](language-support.md)a sada SDK je k C++dispozici v jazycích, C#, Java, Python, Node. js, objektivní-C a JavaScriptu. Ukázky jsou k dispozici v každém jazyku na [GitHubu](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Nejnovější novinky a aktualizace najdete v [poznámkách k verzi](releasenotes.md).

Interně používáme výše uvedené technologie k analýze v reálném čase zákaznických hovorů od Microsoftu.

![Architektura Batch](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Slovo na IVRs

Hlasové služby je možné snadno integrovat do libovolného řešení pomocí [sady Speech SDK](speech-sdk.md) nebo [REST API](rest-apis.md). Přepis centra volání ale může vyžadovat další technologie. Obvykle se vyžaduje připojení mezi systémem IRV a Azure. I když tyto komponenty nenabízíme, rádi bychom popsali, co připojení k IRV zahrnuje.

Několik IRV nebo produktů služby Telephony Service (například Genesys nebo AudioCodes) nabízí možnosti integrace, které se dají využít k povolení průchodu příchozího a odchozího zvuku do služby Azure. V podstatě může vlastní služba Azure poskytovat konkrétní rozhraní pro definování relací telefonních hovorů (například spuštění volání nebo ukončení volání) a vystavit rozhraní WebSocket API pro příjem zvuku příchozího datového proudu, který se používá ve službě Speech. Odchozí odpovědi, jako je například přepis konverzace nebo připojení s rozhraním robota, je možné syntetizovat pomocí služby převodu textu na řeč od Microsoftu a vrátit se do IRV k přehrávání.

Dalším scénářem je přímá integrace protokolu SIP. Služba Azure se připojuje k serveru SIP, takže získává příchozí datový proud a odchozí datový proud, který se používá pro fáze převodu řeči na text a převod textu na řeč. Pokud se chcete připojit k serveru SIP, máte k dispozici komerční nabídky, jako je Ozeki SDK nebo [týmy pro volání a schůzky rozhraní API](/graph/api/resources/communications-api-overview) (v současnosti ve verzi beta), které jsou navržené tak, aby podporovaly tento druh scénářů pro volání zvuku.

## <a name="customize-existing-experiences"></a>Přizpůsobení stávajících prostředí

Služba Azure Speech Services dobře funguje s integrovanými modely, ale možná budete chtít další přizpůsobení a vyladit prostředí pro svůj produkt nebo prostředí. Možnosti přizpůsobení jsou v rozsahu od ladění akustického modelu až po jedinečná hlasová písma pro vaši značku. Po vytvoření vlastního modelu ho můžete používat s jakoukoli službou Azure Speech Services v reálném čase nebo v dávkovém režimu.

| Služba řeči | Model | Popis |
|----------------|-------|-------------|
| Převod řeči na text | [Akustický model](how-to-customize-acoustic-models.md) | Vytvořte si vlastní akustický model pro aplikace, nástroje nebo zařízení, které se používají v různých prostředích, jako je třeba v kleci nebo v továrně, z nichž každá má konkrétní podmínky záznamu. Mezi příklady patří zvýrazněné rozpoznávání řeči, konkrétní šum na pozadí nebo použití konkrétního mikrofonu pro záznam. |
| | [Jazykový model](how-to-customize-language-model.md) | Vytvořte vlastní jazykový model pro zlepšení přepisu slovníku a gramatiky specifické pro konkrétní odvětví, jako je lékařské terminologie nebo žargonu. |
| | [Model výslovnosti](how-to-customize-pronunciation.md) | Pomocí vlastního modelu výslovnosti můžete definovat fonetický tvar a zobrazení slova nebo termínu. Je užitečné pro zpracování přizpůsobených podmínek, jako jsou názvy produktů nebo akronymy. Vše, co je třeba začít, je soubor výslovnosti – jednoduchý soubor. txt. |
| Převod textu na řeč | [Hlasové písmo](how-to-customize-voice-font.md) | Vlastní hlasová písma umožňují vytvořit rozpoznatelný hlasový hlas pro vaši značku. Pro začátek zabere pouze malé množství dat. Větší údaje, které poskytnete, budou zvukové písmo větší, než je vaše hlasová. |

## <a name="sample-code"></a>Ukázka kódu

Vzorový kód je k dispozici na GitHubu pro každou službu Azure Speech Services. Tyto ukázky se týkají běžných scénářů, jako je čtení zvuku ze souboru nebo datového proudu, průběžné a jednorázové rozpoznávání a práce s vlastními modely. Pomocí těchto odkazů můžete zobrazit ukázky sady SDK a REST:

* [Ukázky překladu řeči na text a rozpoznávání řeči (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Ukázky pro dávková přepisy (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Ukázky převodu textu na řeč (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](speech-sdk-reference.md)
* [Sada Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Převod řeči na text](rest-speech-to-text.md)
* [REST API: převod textu na řeč](rest-text-to-speech.md)
* [REST API: přepis a přizpůsobení Batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získat klíč předplatného služby Speech Services zdarma](get-started.md)
