---
title: Volání určené k transkripci System Center – hlasové služby
titleSuffix: Azure Cognitive Services
description: Běžným scénářem, převodu řeči na text je zdlouhavého přepisování pořídili velké objemy dat telefonního subsystému, která může pocházet z různých systémů, jako je například interaktivní hlasové odezvy (IRV). Zvuk se dá stereo nebo mono, a nezpracované s malou ne následné zpracování na signál. S využitím hlasových služeb a modelu řeči Unified podniku získat přepisů vysoce kvalitní, s mnoha systémy zvuku.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 37d68a4d2b7658542ebcfdb5d22a10676a8e4d52
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603310"
---
# <a name="speech-services-for-telephony-data"></a>Hlasových služeb pro data telefonního subsystému

Data telefonního subsystému, které je generován prostřednictvím landlines, mobilní telefony a radiostanicím jsou obvykle nízké kvality a úzkopásmové v rozsahu od 8 KHz, který vytváří výzvy při převodu řeči na text. Nejnovější modely rozpoznávání řeči z Azure hlasové služby vynikají při přepisování tato data telefonního subsystému, dokonce i v případech, kdy data jsou těžko lidských pochopit. Tyto modely jsou trénované s velkými objemy dat telefonního subsystému a mít nejlépe přesnost rozpoznávání na trhu, i v prostředích hlučného.

Běžným scénářem, převodu řeči na text je zdlouhavého přepisování pořídili velké objemy dat telefonního subsystému, která může pocházet z různých systémů, jako je například interaktivní hlasové odezvy (IRV). Zvuk, který poskytuje tyto systémy lze stereo nebo mono a raw trochu žádné následné zpracování na signál. Použitím hlasových služeb a modelu řeči Unified, firmy můžete získat vysoce kvalitní přepisů, ať systémů slouží k zaznamenání zvuk.

Data telefonního subsystému umožňuje lepší porozumění potřeby vašich zákazníků, identifikovat nové marketingové příležitosti nebo vyhodnocení výkonu volání agenty System center. Poté, co je přepisována dat, obchodní mohl použít výstup pro lepší telemetrii, identifikace klíčových frází a analýza mínění zákazníků.

Technologie popsané na této stránce se společností Microsoft interně pro podporu různých služeb zpracování volání, i v reálném čase a režim služby batch.

Pojďme se podívat na některé technologie a nabídky Azure hlasové služby související funkce.

> [!IMPORTANT]
> Řeči sjednocené služby model se trénuje s různými daty a nabízí řešení jednoho modelu na počet scénář z diktování telefonního subsystému analytics.

## <a name="azure-technology-for-call-centers"></a>Technologie pro volání centra Azure

Nad rámec funkční aspekty hlasové služby je jejich hlavním účelem – při použití do call-Centrum – ke zlepšení zkušeností zákazníků. V tomto ohledu existují tři vymazat domény:

* Po volání analytics to znamená, dávkové zpracování záznamů volání
* Zpracování analýzy v reálném čase zvukový signál k extrahování přehledů různé, protože volání probíhat (s právě případu použití viditelného mínění) a
* Virtuálních asistentů (robotům), vedení dialog mezi zákazníkem a robota za účelem řešení problému zákazníka s žádné účast agenta systému nebo aplikace AI se protokoly pomáhat agenta.

Diagram typickou architekturu provádění scénář služby batch je znázorněno na následujícím obrázku ![architektura přepis volání centra](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Komponenty technologie Analytics řeči

Zda je doména po volání nebo v reálném čase, Azure nabízí sadu až po zralé a nově vznikajícími sadu technologií pro zlepšení zkušeností zákazníků.

### <a name="speech-to-text-stt"></a>Převod řeči na text (STT)

[Převod řeči na text](speech-to-text.md) je nejvíce hledané kombinaci po funkci v žádném volání řešení System center. Protože mnoho analytics podřízené procesy, které využívají přepisována text, míra chyb aplikace word (zasílání) je naprosto. Jeden z klíčových problémů v centru přepis volání je šum, který je běžně se vyskytujícím call-centrum (například jiní agenti pro čtení na pozadí), bohatou řadu národní prostředí a dialekty, jakož i nízká kvalita signál, který se telefonu. Zasílání je vysoce korelují s jak dobře jsou akustických a jazykových modelů trénovaných pro dané národní prostředí, proto ji bude možné přizpůsobení modelu, který má národní prostředí je důležité. Naše nejnovější Unified verze 4.x modely jsou řešení tak, aby určené k transkripci přesnost a latenci. Školení s desítkami tisíc hodin akustických dat a miliardy lexikální informace Unified modely jsou velmi přesné modely na trhu pro přepisy data center volání.

### <a name="sentiment"></a>Mínění
Měření, zda měl zákazník kvalitní prostředí je jedna z vašich nejdůležitějších oblastí Řečovou analýzu, při použití na System center místa volání. Naše [rozhraní API služby Batch určené k transkripci](batch-transcription.md) nabízí analýzu subjektivního hodnocení na utterance. Můžete shromažďovat sadu hodnot zjištěných jako součást volání přepis můžete zjistit mínění volání pro agenty a zákazníka.

### <a name="silence-non-talk"></a>Nečinnosti (bez Přednáška)
není, 35 procent společností z žebříčku volání podpory být říkáme doba bez hovoru. Některé scénáře, které bez Přednáška vyvolá jsou: agentů vyhledávání předchozí historie případu se zákazníkem, agentů pomocí nástroje, které zajistí, aby přístup k ploše zákazníka a provádět funkce, zákazníci na uchování čekání na přenos a tak dále. Je velmi důležité můžete měřidla při nečinnosti se vyskytuje v volání, protože počet citlivost důležitý zákazník, který dojde k kolem tyto druhy scénářů a kde k nim dojde při volání metody.

### <a name="translation"></a>Překlad
Některé společnosti jsou experimentování s poskytující přeložený záznamy o studiu hovorů podpory cizích jazyků tak, aby správci doručování by rozuměla na celém světě zkušenosti zákazníků. Naše [překlad](translation.md) možnosti jsou unsurpassed. Zvuk na zvuk nebo zvukový jsme na text přeložit z velkého počtu národní prostředí.

### <a name="text-to-speech"></a>Převod textu na řeč
[Převod textu na řeč](text-to-speech.md) je jiné důležité oblasti při implementaci roboty, kteří komunikovat se zákazníky. Typickou cestu je, že zákazník mluví, je přepisována hlasu na text, text je analyzován z hlediska záměry, odpověď je syntetizovat založené na uznávané záměr a pak buď prezentované prostředek zákazníkovi nebo syntetizovaný hlasové odezvy je vygenerovat. Samozřejmě to všechno dojít k žádnému rychle – tedy latence je jejich důležitou součástí úspěchu tyto systémy.

Naše začátku do konce latence je poměrně málo zvažování různých technologií, které jsou zahrnuty jako [Speech to text](speech-to-text.md), [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [Bot Framework](https://dev.botframework.com/), [ Převod textu na řeč](text-to-speech.md).

Naše nové hlasy jsou také nerozeznatelná od lidí hlasy. Můžete si hlasy Zajistěte svému robotovi jeho jedinečný vzhled.

### <a name="search"></a>Search
Jiné sešívání analytics je identifikace interakce tam, kde určité události nebo prostředí došlo k chybě. To se obvykle provádí s jedním ze dvou následujících metod, buď ad hoc vyhledávání, ve kterém uživatel jednoduše zadá nějakou frázi a systém reaguje při nebo více strukturovaných dotazů, kde analytikovi můžete vytvořit sadu logických příkazy, které identifikovat scénář ve volání , a pak můžete tyto sady dotazů indexovat každé volání. Příklad dobrou prohledávání je příkaz všudypřítomná dodržování předpisů "Toto volání se zaznamená pro účely kvality... "– jak mnoho společností chtít mít jistotu, že jejich agentů přináší toto ustanovení o právním omezení zákazníkům předtím, než je ve skutečnosti zaznamenány volání. Většina systémů analytics se budou moct trend chování objevila algoritmy /search dotaz – při vytváření těchto sestav trendů v konečném důsledku je jednou z vašich nejdůležitějších funkcí systému analytics. Prostřednictvím [adresář služeb Cognitive services](https://azure.microsoft.com/services/cognitive-services/directory/search/) ucelené řešení může být k výraznému rozšíření s funkcemi, indexování a vyhledávání.

### <a name="key-phrase-extraction"></a>Extrakce klíčových frází
Tato oblast je jedním z náročnější analytické aplikace a ten, který těží z aplikace AI a ML. Primární scénáře je odvodit záměru zákazníka. Proč je volání zákazníka? V čem je problém zákazníků? Proč zákazník mají negativní zkušenosti? Naše [služba Text analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) poskytuje sadu analytics připravených pro rychlé inovace vaší ucelené řešení k extrakci těchto důležitých klíčových slov nebo frází.

Pojďme teď Podíváme se na zpracování služby batch a v reálném čase kanály pro rozpoznávání řeči trochu podrobněji.

## <a name="batch-transcription-of-call-center-data"></a>Batch přepis volání zarovnání dat

Pro přepisování hromadné zvuk jsme vyvinuli [rozhraní API služby Batch určené k transkripci](batch-transcription.md). Rozhraní API služby Batch určené k transkripci byla vyvinuta přepisy velké množství zvuková data asynchronně. S ohledem na přepisování data center volání, je naše řešení založené na tyto pilíře:

* **Přesnost**: Čtvrtý generování Unified modely nabízíme unsurpassed určené k transkripci kvality.
* **Latence**: Chápeme, že při provádění hromadných přepisů přepisy jsou potřeba rychle. Přepis úloh spuštěných prostřednictvím [rozhraní API služby Batch určené k transkripci](batch-transcription.md) se zařadí do fronty okamžitě, a po spuštění úlohy se provádí rychleji, než v reálném čase určené k transkripci.
* **Zabezpečení:** Chápeme, že volání mohou obsahovat citlivá data. Buďte bez obav, že zabezpečení je jedním z našich nejvyšší prioritu. Naše služba získal ISO, SOC, HIPAA, PCI certifikace.

Volání centra generují velké objemy zvukových dat denně. Pokud váš podnik ukládá data telefonních v centrálním umístění, jako je Azure Storage, můžete použít [rozhraní API služby Batch určené k transkripci](batch-transcription.md) asynchronně požadovat a přijímat přepisů.

Typické řešení používá tyto služby:

* Hlasové služby Azure se používají k přepisy speech to text. Standardní předplatné (Další) pro hlasové služby je potřeba použít rozhraní API služby Batch určené k transkripci. Bezplatné předplatné (F0) nebudou fungovat.
* [Azure Storage](https://azure.microsoft.com/services/storage/) se používá k uložení dat telefonního subsystému a záznamy o studiu vrácená rozhraním API Batch určené k transkripci. Tento účet úložiště by měl použít oznámení, speciálně pro když se přidají nové soubory. Tato oznámení se používají ke spouštění procesu určené k transkripci.
* [Služba Azure Functions](https://docs.microsoft.com/azure/azure-functions/) slouží k vytvoření sdílené přístupové podpisy (SAS) identifikátor URI pro každý záznam a aktivuje požadavek HTTP POST na spuštění přepis. Kromě toho Azure Functions slouží k vytvoření žádosti o získání a odstranění přepisů pomocí rozhraní API určené k transkripci.
* [Webhooky](webhooks.md) slouží k přijímání oznámení po dokončení přepisů.

Interně používáme výše uvedené technologie pro podporu Microsoft volá zákazníka v dávkovém režimu.
![Architektura služby batch](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>V reálném čase přepis pro System center data volání

Některé podniky se vyžadují pro přepisy konverzace v reálném čase. V reálném čase přepis je možné identifikovat klíčových slov a aktivovat vyhledávání pro obsah a prostředky, které jsou relevantní pro konverzace pro sledování mínění, zlepšení dostupnosti, nebo zajistit překlady pro zákazníky a agenty, kteří nejsou nativní přednášející.

Pro scénáře, které vyžadují v reálném čase přepis, doporučujeme použít [sadou SDK pro řeč](speech-sdk.md). V současné době je k dispozici v speech to text [více než 20 jazycích](language-support.md), a je k dispozici v sadě SDK C++, C#, Java, Python, Node.js, Objective-C a jazyka JavaScript. Ukázky jsou dostupné v každém jazyce na [Githubu](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Nejnovější novinky a aktualizace naleznete v tématu [poznámky k verzi](releasenotes.md).

Výše uvedené technologie se používá interně k analýze ve voláních zákazníků v reálném čase Microsoft při jejich provádění.

![Architektura služby batch](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Slovo na IVRs

Hlasové služby je možné snadno integrovat do řešení pomocí [sadou SDK pro řeč](speech-sdk.md) nebo [rozhraní REST API](rest-apis.md). Přepis volání center však může vyžadovat další technologie. Obvykle je vyžadováno připojení mezi IRV systému a Azure. I když tyto komponenty nenabízíme, rádi bychom popsat, co vyžaduje připojení k IRV.

Několik IRV nebo telefonní služby produkty (například Genesys nebo AudioCodes) nabízí možnosti integrace, které můžete využít pro povolení příchozích a odchozích zvuku průchodu do služby Azure. V podstatě vlastní služby Azure může poskytnout určité rozhraní k definování relací telefonního hovoru (například volání Start nebo End volání) a vystavit WebSocket API pro příjem příchozího datového proudu zvuku, který se používá s hlasové služby. Odchozí odpovědi, jako jsou určené k transkripci konverzace nebo připojení s použitím rozhraní Bot Framework, můžete syntetizovat převod textu na řeč službou od Microsoftu a vrátí IRV pro přehrávání.

Další možností je integrace s přímým přístupem SIP. Služba Azure připojuje k serveru SIP, tedy získávání příchozího datového proudu a výstupního datového proudu, který se používá pro fáze převodu řeči na text a převod textu na řeč. Pro připojení k SIP Server i tam jsou obchodní softwarové produkty, jako je například Ozeki SDK nebo [týmy volání a rozhraní API schůzky](https://docs.microsoft.com/graph/api/resources/calls-api-overview?view=graph-rest-beta) (aktuálně ve verzi beta), který jsou navrženy pro podporu tento druh scénář pro zvuk volání.

## <a name="customize-existing-experiences"></a>Přizpůsobení současné prostředí

Hlasové služby Azure dobře funguje pro předdefinované modely, ale můžete chtít dále přizpůsobit a optimalizovat prostředí pro váš produkt nebo prostředí. Přizpůsobení možností v rozsahu od akustický model ladění jedinečný hlasová písma pro vlastní značky. Po začlenění vlastního modelu, můžete s libovolnou hlasových služeb Azure v reálném čase nebo v dávkovém režimu.

| Speech service | Model | Popis |
|----------------|-------|-------------|
| Převod řeči na text | [Akustický model](how-to-customize-acoustic-models.md) | Vytvoření vlastního akustického modelu pro aplikace, nástroje, nebo zařízení, která se používá především prostředích, jako je v automobilu nebo ve výrobním závodě, každý s konkrétní záznam podmínky. Mezi příklady patří s diakritikou řeči, zvuků na pozadí konkrétní nebo použitím mikrofonu konkrétní záznam. |
| | [Jazykový model](how-to-customize-language-model.md) | Vytvoření vlastního jazykového modelu pro zlepšení přepis slovník specifických pro dané odvětví a gramatiku, jako jsou lékařské terminologie nebo žargonu IT. |
| | [Model výslovnosti](how-to-customize-pronunciation.md) | Pomocí vlastních výslovnost modelu můžete definovat zapsané ve fonetické formuláře a zobrazení slovo nebo termín. Je vhodný pro zpracování vlastní podmínky, jako jsou názvy produktů nebo zkratky. Vše, co potřebujete, abyste mohli začít se výslovnost soubor – soubor .txt jednoduché. |
| Převod textu na řeč | [Hlasové písmo](how-to-customize-voice-font.md) | Vlastní hlasové písmo umožňují vytvářet rozpoznat, jeden z druhu hlas pro hodnotu značky. Trvá jen malé množství dat, abyste mohli začít. Čím více dat, že poskytují více přirozený a podobnou té lidské bude hlasové písmo zvuku. |

## <a name="sample-code"></a>Ukázka kódu

Ukázkový kód je k dispozici na Githubu pro každou hlasových služeb Azure. Tyto ukázky zahrnují běžné scénáře, jako jsou čtení zvuk ze souboru nebo datový proud, rozpoznávání průběžné a jednorázová, prostředků a práci s vlastní modely. Pomocí těchto odkazů můžete zobrazit ukázky sady SDK a REST:

* [Ukázky překladu řeči na text a řeč (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Přepis ukázek služby Batch (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Převod textu na řeč ukázky (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](speech-sdk-reference.md)
* [Rozpoznávání řeči zařízení SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)
* [REST API: Přepis služby batch a přizpůsobení](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získejte klíč předplatného hlasových služeb zdarma](get-started.md)
