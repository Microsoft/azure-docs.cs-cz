---
title: Poznámky k verzi – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Běžící protokol vydaných verzí funkcí hlasové služby, vylepšení, oprav chyb a známé problémy.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 7bab0a28ba2b75903b6bdf4708e6aa0a98bdc9e5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80607401"
---
# <a name="release-notes"></a>Poznámky k verzi
## <a name="speech-sdk-1110-2020-march-release"></a>Sada Speech SDK 1.11.0:2020-březen verze

**Nové funkce**

- Linux: Přidání podpory pro Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 s [pokyny](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-rhel-centos-7) , jak nakonfigurovat systém pro sadu Speech SDK.
- Linux: Přidání podpory pro .NET Core C# v systémech Linux ARM32 a ARM64. Další informace najdete [tady](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux). 
- C#, C++: přidáno `UtteranceId` v `ConversationTranscriptionResult`, konzistentní ID napříč všemi zprostředkujícími a konečný výsledek rozpoznávání řeči. Podrobnosti pro [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult?view=azure-dotnet), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult).
- Python: přidala se `Language ID`podpora pro. Podívejte se prosím na speech_sample. py v [úložišti GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console).
- Windows: Přidání komprimovaného formátu zvukového vstupu na platformu Windows pro všechny konzolové aplikace Win32. Podrobnosti [.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams) 
- JavaScript: podporuje syntézu řeči (převod textu na řeč) v NodeJS. Další informace najdete [tady](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech). 
- JavaScript: přidejte nové rozhraní API, které umožní kontrolu všech zpráv Send a Received. Další informace najdete [tady](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript). 
        
**Opravy chyb**

- C#, C++: vyřešil se problém, `SendMessageAsync` takže teď odesílá binární zprávu jako binární typ. Podrobnosti pro [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection).
- C#, C++: Opravili jsme problém, `Connection MessageReceived` kdy použití události může způsobit `Recognizer` chybu, pokud je `Connection` uvolněn před objektem. Podrobnosti pro [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived?view=azure-dotnet), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection#messagereceived).
- Android: velikost zvukové vyrovnávací paměti z mikrofonu se snížila z 800ms na 100 ms, aby se zlepšila latence.
- Android: Opravili jsme [problém](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) s emulátorem x86 pro Android v Android Studio.
- JavaScript: Přidání podpory pro oblasti v Číně s `fromSubscription` rozhraním API. Podrobnosti [.](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#fromsubscription-string--string-) 
- JavaScript: přidejte další informace o chybě pro chyby připojení z NodeJS.
        
**ukázky**

- Unity: veřejná ukázka rozpoznávání záměrů je pevná, kde se LUIS import JSON nezdařil. Podrobnosti [.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369)
- Python: byla přidána ukázka `Language ID`pro. Podrobnosti [.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)
    
**Covid19 zkrácené testování**

Vzhledem k tomu, že během posledních několika týdnů pracujete vzdáleně, nemůžeme provést tolik ručních testů ověřování zařízení, jako bychom normálně. Příkladem je testování mikrofonu vstupu a mluvčího výstupu v systémech Linux, iOS a macOS. Neudělali jsme žádné změny, které jsme na těchto platformách mohli jakkoli rozdělit, a naše automatizované testy jsme prošli všemi úspěchy. V nepravděpodobném případě, že jsme něco zmeškali, dejte nám prosím na [GitHubu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen)informace.<br> Děkujeme za vaši trvalou podporu. Jako vždycky prosím vydejte dotazy nebo připomínky na [GitHubu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen) nebo [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Buďte v pořádku!

## <a name="speech-sdk-1100-2020-february-release"></a>Sada Speech SDK 1.10.0:2020 – Únorová verze

**Nové funkce**

 - Přidání balíčků Pythonu pro podporu nové verze 3,8 Pythonu
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 Support (C++, C#, Java, Python).
   > [!NOTE] 
   > Zákazníci musí podle [těchto pokynů](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)nakonfigurovat OpenSSL.
 - Podpora ARM32 pro Linux pro Debian a Ubuntu.
 - DialogServiceConnector nyní podporuje volitelné parametr "robot ID" v BotFrameworkConfig. Tento parametr umožňuje použití několika přímých roboty řeči s jedním prostředkem řeči Azure. Bez zadaného parametru se použije výchozí robot (tak, jak je určený na stránce Konfigurace kanálu pro přímé čáry).
 - DialogServiceConnector má teď vlastnost SpeechActivityTemplate. Obsah tohoto řetězce JSON bude použit přímým převodem na řádku k předběžnému naplnění široké škály podporovaných polí ve všech činnostech, které se dodávají k přímému line Speech bot, včetně aktivit automaticky generovaných v reakci na události, jako je rozpoznávání řeči.
 - TTS nyní používá pro ověřování klíč předplatného, což snižuje latenci prvního výsledku Shrnutí po vytvoření syntetizátoru.
 - Aktualizované modely rozpoznávání řeči pro 19 národních prostředí pro průměrné snížení četnosti chyb slov 18,6% (ES-ES, ES-MX, fr-CA, fr-FR, IT-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, NB-NO, Fi-FL, ru-RU, pl-PL, CA-ES, zh-TW, th-TH, pt-PT, tr-TR). Nové modely přináší významná vylepšení napříč více doménami včetně diktování, přepisu a scénářů indexování videa.

**Opravy chyb**

 - Opravená chyba, kde konverzace Transcriber v rozhraní JAVA API nečekala správně 
 - Oprava emulátoru Androidu x86 pro [problém GitHubu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363) pro Xamarin
 - Přidat chybějící (Get | Set) vlastnosti metod na AudioConfig
 - Oprava chyby TTS, kde audioDataStream nebylo možné zastavit, pokud selhalo připojení
 - Použití koncového bodu bez oblasti způsobí selhání USP pro překladatele konverzace.
 - Generování ID v univerzálních aplikacích pro Windows teď používá vhodně jedinečný algoritmus GUID; dřív a záměrně neúmyslně nastavily implementaci podložit, která často produkuje kolizí v rámci velkých sad interakcí.
 
 **ukázky**
 
 - Ukázka Unity pro použití sady Speech SDK s [mikrofonem Unity a streamování přes nabízený režim](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Další změny**

 - [Dokumentace ke konfiguraci OpenSSL se aktualizovala pro Linux.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Sada Speech SDK 1.9.0:2020 – leden Release

**Nové funkce**

- Konverzace s více zařízeními: umožňuje propojit více zařízení se stejnou hlasovou nebo textovou konverzací a volitelně překládat zprávy odesílané mezi nimi. Další informace najdete v [tomto článku](multi-device-conversation.md). 
- Podpora rozpoznávání klíčových slov se přidala pro balíček Android. AAR a přidala se podpora pro typy x86 a x64. 
- Cíl-C: `SendMessage` a `SetMessageProperty` metody přidané do `Connection` objektu. Další informace [najdete v dokumentaci.](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection)
- Rozhraní TTS C++ API teď `std::wstring` podporuje jako Shrnutí textu, takže před předáním do sady SDK je potřeba převést wstring na řetězec. [Tady](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync)najdete podrobnosti. 
- C#: [ID jazyka](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) a [konfigurace zdrojového jazyka](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) jsou nyní k dispozici.
- JavaScript: Přidání funkce do `Connection` objektu pro předání vlastní zprávy ze služby Speech jako zpětného volání. `receivedServiceMessage`
- JavaScript: Přidání podpory pro `FromHost API` pro usnadnění používání s Prem kontejnery a svrchovanými cloudy. Další informace [najdete v dokumentaci.](speech-container-howto.md)
- JavaScript: nyní jsme se `NODE_TLS_REJECT_UNAUTHORIZED` spojili s příspěvkem z [orgads](https://github.com/orgads). [Tady](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)najdete podrobnosti.

**Změny způsobující chyby**

- `OpenSSL`byla aktualizována na verzi 1.1.1 b a staticky propojená se základní knihovnou sady Speech SDK pro Linux. To může způsobit přerušení, pokud vaše Doručená pošta `OpenSSL` nebyla nainstalována do `/usr/lib/ssl` adresáře v systému. Pokud chcete tento problém obejít, přečtěte si [naši dokumentaci](how-to-configure-openssl-linux.md) v části dokumentace k sadě Speech SDK.
- Změnili jsme datový typ vrácený pro `WordLevelTimingResult.Offset` C# z `int` na `long` , aby bylo umožněno přístupu `WordLevelTimingResults` k datům, když jsou data řeči delší než 2 minuty.
- `PushAudioInputStream`a `PullAudioInputStream` teď pošle informace o hlavičce WAV do služby pro rozpoznávání `AudioStreamFormat`řeči na základě, volitelně zadané při jejich vytvoření. Zákazníci teď musí používat [podporovaný formát zvukového vstupu](how-to-use-audio-input-streams.md). Všechny ostatní formáty získají dílčí optimální výsledky rozpoznávání nebo můžou způsobit jiné problémy. 

**Opravy chyb**

- Podívejte se `OpenSSL` na aktualizaci v části nejnovější změny výše. V systému Linux a Java jsme opravili občasné chyby a problémy s výkonem (při vysokém zatížení zamknete spory). 
- Java: vylepšení uzavření objektu ve scénářích s vysokou mírou souběžnosti.
- Znovu se strukturuje náš balíček NuGet. Odebrali jsme tři kopie `Microsoft.CognitiveServices.Speech.core.dll` a `Microsoft.CognitiveServices.Speech.extension.kws.dll` složky lib, čímž se balíček NuGet zmenší a rychleji stáhne a přidali jsme hlavičky potřebné ke kompilaci některých nativních aplikací C++.
- Pevné ukázky pro rychlý Start [najdete tady](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp). Ty se ukončily bez zobrazení výjimky "mikrofon nebyl nalezen" na Linux, MacOS a Windows.
- Opravili jsme chybu sady SDK s dlouhým výsledkem rozpoznávání řeči na určitých cestách kódu, jako je [Tato ukázka](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Opravená chyba nasazení sady SDK ve službě Azure Web App Environment pro vyřešení [tohoto problému se zákazníky](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396).
- Opravili jsme chybu TTS při použití `<voice>` vícenásobné `<audio>` značky nebo značky k vyřešení [tohoto problému zákazníka](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433). 
- Opravená chyba TTS 401 při obnovení sady SDK z pozastavené.
- JavaScript: Děkujeme, že se vám jako příspěvek z [Euirim](https://github.com/euirim)opravil cyklický import zvukových dat. 
- JavaScript: Přidání podpory pro nastavení vlastností služby, jak je přidáno v 1,7.
- JavaScript: Opravili jsme problém, kdy by chyba připojení mohla způsobit průběžné pokusy o opětovné připojení k soketu WebSocket.

**ukázky**

- [Sem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)přidejte ukázku rozpoznávání klíčových slov pro Android.
- [Sem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)přidejte ukázku TTS pro scénář serveru.
- [Sem](quickstarts/multi-device-conversation.md)jste přidali rychlý Start konverzace s více zařízeními pro jazyky C# a C++.

**Další změny**

- Optimalizovaná velikost knihovny SDK jádra v Androidu
- Sada SDK v 1.9.0 a vyšší podporuje `int` `string` typy i v poli verze hlasového podpisu pro Transcriber pro konverzaci.

## <a name="speech-sdk-180-2019-november-release"></a>Sada Speech SDK 1.8.0:2019 – listopadová verze

**Nové funkce**

- Přidali jsme `FromHost()` rozhraní API, které můžete používat s kontejnery Prem a svrchovanými cloudy.
- Přidání automatického zdrojového Rozpoznávání jazyka pro rozpoznávání řeči (v jazycích Java a C++)
- Přidání `SourceLanguageConfig` objektu pro rozpoznávání řeči, pomocí kterého se určí očekávané zdrojové jazyky (v jazycích Java a C++)
- Přidání `KeywordRecognizer` podpory pro Windows (UWP), Android a iOS prostřednictvím balíčků NuGet a Unity
- Bylo přidáno rozhraní API vzdálené konverzace Java, které umožní přepis konverzace v asynchronních dávkách.

**Změny způsobující chyby**

- Funkce konverzace Transcriber přesunuté do oboru `Microsoft.CognitiveServices.Speech.Transcription`názvů.
- Součást metod konverzace Transcriber je přesunuta do nové `Conversation` třídy.
- Vyřazena podpora pro 32-bit (ARMv7 a x86) iOS

**Opravy chyb**

- Opravit pro chybu, pokud `KeywordRecognizer` je místní použití bez platného klíče předplatného služby Speech

**ukázky**

- Ukázka Xamarin pro`KeywordRecognizer`
- Ukázka Unity pro`KeywordRecognizer`
- Ukázky pro automatické zdrojové Rozpoznávání jazyka v jazyce C++ a Java

## <a name="speech-sdk-170-2019-september-release"></a>Sada Speech SDK 1.7.0:2019 – září verze

**Nové funkce**

- Přidání podpory beta verze pro Xamarin on Univerzální platforma Windows (UWP), Android a iOS
- Přidala se podpora iOS pro Unity.
- Přidání `Compressed` podpory vstupu pro ALaw, MULAW, FLAC v Androidu, iOS a Linux
- Přidáno `SendMessageAsync` do `Connection` třídy pro odeslání zprávy službě
- Přidáno `SetMessageProperty` do `Connection` třídy pro vlastnost nastavení zprávy
- TTS – přidané vazby pro Java (JRE a Android), Python, SWIFT a cíl-C
- TTS přidal podporu přehrávání pro macOS, iOS a Android.
- Přidání informací o hranici slova pro TTS

**Opravy chyb**

- Problém s opravenou IL2CPP sestavení v Unity 2019 pro Android
- Opravený problém se nesprávně zpracovávanými nepoškozenými záhlavími ve vstupním souboru WAV
- Opravený problém s identifikátory UUID není jedinečný v některých vlastnostech připojení.
- Opravili jsme několik upozornění na specifikátory hodnoty null v vazbách SWIFT (může vyžadovat malé změny kódu).
- Opravili jsme chybu, která způsobila, že připojení protokolu WebSocket se v zatížení sítě nekorektně zavřou.
- Opravili jsme problém v Androidu, který někdy má za následek duplicitní ID dojmů, které používá.`DialogServiceConnector`
- Vylepšení stability připojení v rámci vícenásobných interakcí a hlášení selhání (prostřednictvím `Canceled` událostí), když k nim dojde`DialogServiceConnector`
- `DialogServiceConnector`spuštění relace nyní bude správně poskytovat události, včetně při volání `ListenOnceAsync()` během aktivní`StartKeywordRecognitionAsync()`
- Vyřešená havárie přidružená `DialogServiceConnector` k přijatým aktivitám

**ukázky**

- Rychlý Start pro Xamarin
- Aktualizovaný rychlý Start pro CPP s ARM64 informacemi pro Linux
- Aktualizovaný rychlý Start Unity s informacemi pro iOS

## <a name="speech-sdk-160-2019-june-release"></a>Sada Speech SDK 1.6.0:2019 – červenou verzi

**ukázky**

- Ukázky rychlý Start pro převod textu na řeč pro UWP a Unity
- Ukázka rychlého startu pro SWIFT v iOS
- Ukázky Unity pro rozpoznávání řeči & Rozpoznávání záměru a překlad
- Aktualizované ukázky pro rychlý Start pro`DialogServiceConnector`

**Vylepšení/změny**

- Obor názvů dialogového okna:
  - Přejmenování `SpeechBotConnector` na `DialogServiceConnector`
  - Přejmenování `BotConfig` na `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()`bylo přemapováno na`DialogServiceConfig::FromBotSecret()`
  - Po přejmenování budou dál podporovány všechny existující klienty s přímým ovládáním řeči.
- Aktualizace adaptéru pro převod textu na řeč pro podporu proxy a trvalého připojení
- Vylepšit chybovou zprávu, když je předána neplatná oblast
- SWIFT/objektiv-C:
  - Vylepšené zasílání zpráv o chybách: metody, které mohou mít za následek chybu, jsou nyní k dispozici `NSError` ve dvou verzích: jeden, který zpřístupňuje objekt pro zpracování chyb, a druhý, který vyvolává výjimku. Bývalé jsou zpřístupněny SWIFT. Tato změna vyžaduje úpravu stávajícího kódu SWIFT.
  - Vylepšené zpracování událostí

**Opravy chyb**

- Oprava pro TTS: místo `SpeakTextAsync` , kde se v budoucnu vrátilo, dokud se nedokončí vykreslování zvuku
- Oprava pro zařazování řetězců v jazyce C# pro povolení plné jazykové podpory
- Oprava problému aplikace .NET Core pro načtení základní knihovny s cílovou architekturou net461 v ukázkách
- Oprava pro příležitostné problémy s nasazováním nativních knihoven do výstupní složky v ukázkách
- Oprava pro spolehlivou zavírání webového soketu
- Oprava pro možnou chybu při otevírání připojení v rámci velmi velkého zatížení systému Linux
- Oprava chybějících metadat v sadě rozhraní pro macOS
- Oprava pro problémy s `pip install --user` Windows

## <a name="speech-sdk-151"></a>Sada Speech SDK 1.5.1

Toto je vydání opravy chyby a ovlivňuje pouze nativní/spravované sady SDK. Nemá vliv na verzi JavaScriptu sady SDK.

**Opravy chyb**

- Opravte FromSubscription při použití s přepisem konverzace.
- Opravte chybu v klíčovém slovu hledání pro hlasové asistenty.

## <a name="speech-sdk-150-2019-may-release"></a>Sada Speech SDK 1.5.0:2019 – může vydávat

**Nové funkce**

- Klíčové slovo hledání (KWS) je teď k dispozici pro Windows a Linux. Funkce KWS můžou pracovat s jakýmkoli typem mikrofonu. oficiální podpora KWS se ale v současné době omezila na pole mikrofonu, která najdete v sadě Azure Kinect DK hardware nebo v sadě Speech SDK (Speech Devices SDK).
- Funkce pomocných parametrů fráze je dostupná prostřednictvím sady SDK. Další informace najdete [tady](how-to-phrase-lists.md).
- Funkce přepisu konverzace je k dispozici prostřednictvím sady SDK. Podívejte se [sem](conversation-transcription-service.md).
- Přidejte podporu pro hlasové asistenty pomocí kanálu Direct line Speech.

**ukázky**

- Byly přidány ukázky pro nové funkce nebo nové služby, které sada SDK podporuje.

**Vylepšení/změny**

- Přidání různých vlastností nástroje pro rozpoznávání pro úpravu chování služby nebo výsledků služby (například vulgárních výrazů a dalších).
- Nástroj pro rozpoznávání teď můžete nakonfigurovat pomocí standardních vlastností konfigurace, a to i v případě, že jste vytvořili `FromEndpoint`Nástroj pro rozpoznávání.
- Cíl-C: `OutputFormat` vlastnost byla přidána do `SPXSpeechConfiguration`.
- Sada SDK teď podporuje Debian 9 jako distribuci Linux.

**Opravy chyb**

- Opravili jsme problém, kdy byl prostředek mluvčíe ve fázi převodu textu na řeč příliš nestrukturované.

## <a name="speech-sdk-142"></a>Sada Speech SDK 1.4.2

Toto je vydání opravy chyby a ovlivňuje pouze nativní/spravované sady SDK. Nemá vliv na verzi JavaScriptu sady SDK.

## <a name="speech-sdk-141"></a>Sada Speech SDK 1.4.1

Toto je verze jenom pro JavaScript. Nepřidaly se žádné funkce. Byly provedeny následující opravy:

- Zabrání webovému balíčku načíst agenta HTTPS-proxy-.

## <a name="speech-sdk-140-2019-april-release"></a>Sada Speech SDK 1.4.0:2019 – duben

**Nové funkce**

- Sada SDK teď podporuje službu převodu textu na řeč jako beta verzi. Podporuje se v desktopech Windows a Linux z C++ a C#. Další informace najdete v [přehledu převod textu na řeč](text-to-speech.md#get-started).
- Sada SDK teď podporuje zvukové soubory MP3 a Opus/OGG jako vstupní soubory streamu. Tato funkce je k dispozici pouze pro Linux z C++ a C# a je momentálně ve verzi beta (další podrobnosti najdete [tady](how-to-use-codec-compressed-audio-input-streams.md)).
- Sada Speech SDK pro jazyky Java, .NET Core, C++ a objektivní-C získala podporu macOS. Podpora pro macOS je v současnosti ve verzi beta.
- iOS: sada Speech SDK pro iOS (cíl-C) je teď publikovaná taky jako CocoaPod.
- JavaScript: podpora pro jiný než výchozí mikrofon jako vstupní zařízení.
- JavaScript: proxy podpora pro Node. js.

**ukázky**

- Byly přidány ukázky pro použití sady Speech SDK s C++ a s cílem-C v macOS.
- Byly přidány ukázky demonstrující použití služby převodu textu na řeč.

**Vylepšení/změny**

- Python: další vlastnosti výsledků rozpoznávání jsou nyní zpřístupněny prostřednictvím `properties` vlastnosti.
- Pro další podporu vývoje a ladění můžete přesměrovat informace o protokolování a diagnostice sady SDK do souboru protokolu (další podrobnosti najdete [tady](how-to-use-logging.md)).
- JavaScript: zlepšení výkonu zpracování zvuku.

**Opravy chyb**

- Mac/iOS: Chyba, která vedla k dlouhému čekání v případě, že nebylo možné navázat spojení se službou Speech.
- Python: zlepšení zpracování chyb pro argumenty v zpětných voláních Pythonu.
- JavaScript: Opraveno chybné vytváření sestav stavu pro rozpoznávání řeči skončilo v RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Sada Speech SDK 1.3.1:2019 – aktualizace z února

Toto je vydání opravy chyby a ovlivňuje pouze nativní/spravované sady SDK. Nemá vliv na verzi JavaScriptu sady SDK.

**Oprava chyby**

- Při použití vstupu mikrofonu byla vyřešena nevracení paměti. Na základě datového proudu nebo vstupu souboru není ovlivněno.

## <a name="speech-sdk-130-2019-february-release"></a>Sada Speech SDK 1.3.0:2019 – Únorová verze

**Nové funkce**

- Sada Speech SDK podporuje výběr vstupního mikrofonu skrze `AudioConfig` třídu. To vám umožní streamovat zvuková data do služby pro rozpoznávání řeči z jiného než výchozího mikrofonu. Další informace najdete v dokumentaci popisující [Výběr zvukového vstupního zařízení](how-to-select-audio-input-devices.md). Tato funkce není ještě dostupná z JavaScriptu.
- Sada Speech SDK teď ve verzi beta verze podporuje Unity. Poskytněte zpětnou vazbu v části problém v [ukázkovém úložišti GitHubu](https://aka.ms/csspeech/samples). Tato verze podporuje Unity v systémech Windows x86 a x64 (desktopové nebo Univerzální platforma Windows aplikace) a Androidu (ARM32/64, x86). Další informace najdete v našem [rychlém](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)startu pro Unity.
- Soubor `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (dodán v předchozích verzích) už není potřeba. Tato funkce je teď integrovaná do základní sady SDK.

**ukázky**

V našem [ukázkovém úložišti](https://aka.ms/csspeech/samples)je k dispozici následující nový obsah:

- Další ukázky pro `AudioConfig.FromMicrophoneInput`.
- Další ukázky Pythonu pro rozpoznávání a překlad záměrů.
- Další ukázky pro používání `Connection` objektu v iOS
- Další ukázky Java pro překlad pomocí zvukového výstupu.
- Nová ukázka použití [dávkového přepisu REST API](batch-transcription.md).

**Vylepšení/změny**

- Python
  - Vylepšené ověřování parametrů a chybové zprávy `SpeechConfig`v.
  - Přidejte podporu `Connection` objektu.
  - Podpora 32-bitové verze Pythonu (x86) ve Windows.
  - Sada Speech SDK for Python je mimo verzi beta.
- iOS
  - Sada SDK je teď založená na sadě iOS SDK verze 12,1.
  - Sada SDK teď podporuje iOS verze 9,2 a novější.
  - Vylepšete referenční dokumentaci a opravte několik názvů vlastností.
- JavaScript
  - Přidejte podporu `Connection` objektu.
  - Přidat soubory definice typu pro seskupené JavaScripty
  - Počáteční podpora a implementace pro pomocný parametr fráze
  - Návratová kolekce vlastností s JSON služby pro rozpoznávání
- Knihovny DLL systému Windows teď obsahují prostředek verze.
- Pokud vytvoříte Nástroj pro rozpoznávání `FromEndpoint` , můžete přidat parametry přímo k adrese URL koncového bodu. Pomocí `FromEndpoint` standardních vlastností konfigurace nemůžete konfigurovat nástroj pro rozpoznávání.

**Opravy chyb**

- Prázdné uživatelské jméno proxy serveru a heslo proxy serveru nebyly zpracovány správně. Pokud v této verzi nastavíte uživatelské jméno a heslo proxy serveru na prázdný řetězec, nebudou odeslány při připojování k proxy serveru.
- Identifikátor SessionId, který sada SDK vytvořil, nebyl vždycky pro některé jazyky&nbsp;nebo prostředí vždy náhodný. K vyřešení tohoto problému se přidala inicializace náhodného generátoru.
- Vylepšete zpracování autorizačního tokenu. Pokud chcete použít autorizační token, zadejte v `SpeechConfig` a ponechte klíč předplatného prázdný. Pak vytvořte Nástroj pro rozpoznávání obvyklým způsobem.
- V některých případech se `Connection` objekt nevydal správně. Tento problém byl opraven.
- Ukázka JavaScriptu byla opravena tak, aby podporovala zvukový výstup pro syntézu překladu i v Safari.

## <a name="speech-sdk-121"></a>Sada Speech SDK 1.2.1

Toto je verze jenom pro JavaScript. Nepřidaly se žádné funkce. Byly provedeny následující opravy:

- Požárový konec streamu. konec, ne na řeč. end.
- Oprava chyby v zvukové pumpě, která neplánovala další odeslání, pokud se aktuální odeslání nepovedlo.
- Opravte průběžné rozpoznávání pomocí ověřovacího tokenu.
- Oprava chyb pro různé funkce pro rozpoznávání nebo koncové body.
- Vylepšení dokumentace.

## <a name="speech-sdk-120-2018-december-release"></a>Sada Speech SDK 1.2.0:2018 – prosinec verze

**Nové funkce**

- Python
  - V této verzi je k dispozici beta verze podpory Pythonu (3,5 a novější). Další informace naleznete zde] (rychlý Start – python.md).
- JavaScript
  - Sada Speech SDK pro JavaScript je open source. Zdrojový kód je k dispozici na [GitHubu](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Nyní podporujeme Node. js. Další informace najdete [tady](quickstart-js-node.md).
  - Omezení délky pro zvukové relace bylo odebráno, k automatickému připojení dojde v rámci pokrytí.
- `Connection`předmětů
  - Z `Recognizer`, můžete získat přístup k `Connection` objektu. Tento objekt vám umožňuje explicitně iniciovat připojení služby a přihlásit se k odběru událostí a odpojovat se.
    (Tato funkce ještě není dostupná z JavaScriptu a Pythonu.)
- Podpora pro Ubuntu 18,04.
- Android
  - Povolená podpora ProGuard během generování APK

**Vylepšen**

- Vylepšení využití interního vlákna, což snižuje počet vláken, zámků a mutexů.
- Vylepšené zasílání zpráv o chybách/informace. V několika případech se chybové zprávy nerozšířily tak, jak jsou.
- Aktualizované závislosti vývoje v JavaScriptu pro použití aktuálnosti modulů.

**Opravy chyb**

- Pevná nevracení paměti kvůli neshodě typu v `RecognizeAsync`.
- V některých případech došlo k úniku výjimek.
- Oprava nevrácené paměti v argumentech události překladu.
- Opravili jsme problém se zámkem při opětovném připojení v dlouho spuštěných relacích.
- Opravili jsme problém, který by mohl vést k chybějícímu konečnému výsledku pro neúspěšné překlady.
- C#: Pokud v `async` hlavním vlákně nečekala operace, je možné, že by se modul pro rozpoznávání odstranil před dokončením asynchronní úlohy.
- Java: Opravili jsme problém, což vedlo k chybě virtuálního počítače Java.
- Cíl-C: pevné mapování výčtu; Místo bylo vráceno RecognizedIntent `RecognizingIntent`.
- JavaScript: Nastavte výchozí výstupní formát na možnost "jednoduchý" `SpeechConfig`v.
- JavaScript: odebrání nekonzistence mezi vlastnostmi objektu konfigurace v jazyce JavaScript a dalších jazycích.

**ukázky**

- Aktualizovali a opravili jsme několik ukázek (například výstupní hlasy pro překlad atd.).
- Do [ukázkového úložiště](https://aka.ms/csspeech/samples)se přidaly ukázky pro Node. js.

## <a name="speech-sdk-110"></a>Sada Speech SDK 1.1.0

**Nové funkce**

- Podpora pro Android x86/x64.
- Podpora proxy serveru: v `SpeechConfig` objektu nyní můžete zavolat funkci pro nastavení informací o proxy serveru (název hostitele, port, uživatelské jméno a heslo). Tato funkce není ještě k dispozici v iOS.
- Vylepšený kód chyby a zprávy. Pokud rozpoznávání vrátilo chybu, již bylo nastaveno `Reason` (ve zrušené události) nebo `CancellationDetails` (ve výsledku rozpoznávání) na. `Error` Zrušená událost teď obsahuje dva další členy `ErrorCode` a. `ErrorDetails` Pokud server vrátil Další informace o chybě s nahlášenou chybou, bude nyní k dispozici v nových členech.

**Vylepšen**

- Do konfigurace nástroje pro rozpoznávání se přidalo další ověření a přidala se další chybová zpráva.
- Vylepšené zpracování dlouhodobě tichého podoby uprostřed zvukového souboru.
- Balíček NuGet: u .NET Frameworkch projektů brání sestavování s konfigurací AnyCPU.

**Opravy chyb**

- V nástroji pro rozpoznávání se našla několik výjimek. Kromě toho jsou výjimky zachyceny a převedeny na `Canceled` událost.
- Opravte nevrácení paměti ve správě vlastností.
- Opravili jsme chybu, ve které by zvukový vstupní soubor mohl způsobit selhání nástroje pro rozpoznávání.
- Opravili jsme chybu, kdy se události dostaly po události ukončení relace.
- Opravili jsme některé konflikty časování ve vláknování.
- Opravili jsme problém s kompatibilitou iOS, který by mohl způsobit chybu.
- Vylepšení stability pro podporu mikrofonu Androidu.
- Opravili jsme chybu, kdy by nástroj pro rozpoznávání v JavaScriptu ignoroval jazyk rozpoznávání.
- Opravili jsme chybu, která `EndpointId` brání v nastavení (v některých případech) v JavaScriptu.
- Změnili jsme pořadí parametrů v AddIntent v JavaScriptu a přidali `AddIntent` jste chybějící podpis JavaScriptu.

**ukázky**

- Přidány ukázky jazyka C++ a C# pro použití pro vyžádání obsahu a nabízené vysílání do [vzorového úložiště](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Sada Speech SDK 1.0.1

Vylepšení spolehlivosti a opravy chyb:

- Opravená potenciální závažná chyba kvůli konfliktu časování v nástroji pro rozpoznávání disposing
- Opravená potenciální závažná chyba pro případ zrušení vlastností
- Přidala se další chyba a kontrola parametrů.
- Cíl-C: Opravili jsme možnou závažnou chybu způsobenou přepsáním názvu v NSString.
- Cíl-C: upravená viditelnost rozhraní API
- JavaScript: opravené související události a jejich datové části.
- Vylepšení dokumentace.

V našem [ukázkovém úložišti](https://aka.ms/csspeech/samples)se přidala Nová ukázka pro JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0:2018 – září verze

**Nové funkce**

- Podpora pro cíl-C v iOS. Podívejte se na náš rychlý úvodní úkol [v jazyce C pro iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Podpora JavaScriptu v prohlížeči Podívejte se na náš [rychlý úvodní JavaScript](quickstart-js-browser.md).

**Změny způsobující chyby**

- V této verzi se zavádí určitý počet nezměněných změn.
  Podrobnosti najdete na [této stránce](https://aka.ms/csspeech/breakingchanges_1_0_0) .

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0:2018 – srpen Release

**Nové funkce**

- Aplikace pro UWP vytvořené pomocí sady Speech SDK teď můžou předat sadu Windows App Certification Kit (WACK).
  Podívejte se na [rychlé zprovoznění UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Podpora pro .NET Standard 2,0 na platformě Linux (Ubuntu 16,04 x64).
- Experimentální: podporují Java 8 v systému Windows (64 bitů) a Linux (Ubuntu 16,04 x64).
  Podívejte se na [rychlý úvodní Java Runtime Environment](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Změna funkčnosti**

- Vystavení dalších podrobností o chybách při chybě připojení

**Změny způsobující chyby**

- V jazyce Java (Android) již `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` funkce nevyžaduje parametr cesty. Tato cesta se teď automaticky detekuje na všech podporovaných platformách.
- Přístupový objekt get vlastnosti `EndpointUrl` v jazycích Java a C# byl odebrán.

**Opravy chyb**

- V jazyce Java je nyní implementována výsledná syntéza zvuku na nástroji pro rozpoznávání překladu.
- Opravili jsme chybu, která by mohla způsobit neaktivní vlákna a zvýšený počet otevřených a nevyužitých soketů.
- Opravili jsme problém, kdy dlouhodobě běžící rozpoznávání může končit uprostřed přenosu.
- Opravili jste konflikt časování při vypnutí nástroje pro rozpoznávání.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0:2018 – červenec verze

**Nové funkce**

- Podpora platformy Android (rozhraní API 23: Android 6,0 Marshmallow nebo novější). Podívejte se na [rychlý Start pro Android](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Podpora .NET Standard 2,0 ve Windows. Podívejte se na [rychlé zprovoznění .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experimentální: podporují UWP ve Windows (verze 1709 nebo novější).
  - Podívejte se na [rychlé zprovoznění UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - Poznámka: aplikace pro UWP vytvořené pomocí sady Speech SDK ještě nepředá certifikační sadu aplikací pro Windows (WACK).
- Podpora dlouhotrvajícího rozpoznávání při automatickém opětovném připojení.

**Funkční změny**

- `StartContinuousRecognitionAsync()`podporuje dlouhodobě běžící rozpoznávání.
- Výsledek rozpoznávání obsahuje více polí. Jsou posunuty od zahájení zvuku a doby trvání (v tiků) rozpoznaného textu a dalších hodnot, které představují stav rozpoznávání, například `InitialSilenceTimeout` a. `InitialBabbleTimeout`
- Podporuje AuthorizationToken pro vytváření instancí továrny.

**Změny způsobující chyby**

- Události rozpoznávání: `NoMatch` typ události byl sloučen do `Error` události.
- SpeechOutputFormat v jazyce C# byla přejmenována na `OutputFormat` , aby zůstala v souladu s C++.
- Návratový typ některých metod rozhraní se `AudioInputStream` mírně změnil:
  - V jazyce Java se `read` metoda nyní vrací `long` místo `int`.
  - V jazyce C# se `Read` metoda nyní vrací `uint` místo `int`.
  - V jazyce C++ se `Read` nyní `GetFormat` vrátí `size_t` metody a místo `int`.
- C++: instance zvukových vstupních streamů se teď dají předat jenom jako `shared_ptr`.

**Opravy chyb**

- Opravily se nesprávné návratové hodnoty v `RecognizeAsync()` důsledku vypršení časového limitu.
- Došlo k odebrání závislosti v knihovnách Media Foundation v systému Windows. SDK teď používá základní zvuková rozhraní API.
- Oprava dokumentace: Přidání stránky [oblastí](regions.md) pro popis podporovaných oblastí.

**Známý problém**

- Sada Speech SDK pro Android nehlásí výsledky Shrnutí řeči pro překlad. Tento problém bude vyřešen v příští verzi.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0:2018 – červená verze

**Funkční změny**

- AudioInputStream

  Nástroj pro rozpoznávání teď může jako zdroj zvuku spotřebovat datový proud. Další informace najdete v části související [Průvodce](how-to-use-audio-input-streams.md).

- Podrobný výstupní formát

  Když vytvoříte `SpeechRecognizer`, můžete požadovat `Detailed` nebo `Simple` výstupní formát. `DetailedSpeechRecognitionResult` Obsahuje hodnocení spolehlivosti, rozpoznaný text, nezpracovaný lexikální tvar, normalizovanou formu a normalizovanou formu s maskovánými vulgárními výrazy.

**Zásadní změna**

- Změněno na `SpeechRecognitionResult.Text` z `SpeechRecognitionResult.RecognizedText` jazyka C#.

**Opravy chyb**

- Opravili jsme možný problém zpětného volání ve vrstvě USP během vypínání.
- Pokud nástroj pro rozpoznávání použil zvukový vstupní soubor, byl uložený na popisovači souboru déle, než je nutné.
- Bylo odebráno několik zablokování mezi čerpadlem zpráv a modulem pro rozpoznávání.
- Spustí `NoMatch` výsledek, když vypršel časový limit odpovědi na službu.
- Knihovny Media Foundation v systému Windows jsou načteny opožděně. Tato knihovna se vyžaduje jenom pro vstup přes mikrofon.
- Rychlost nahrávání zvukových dat je omezená na přibližně dvojnásobek původní rychlosti zvuku.
- V systému Windows jsou nyní sestavení C# .NET silně pojmenována.
- Oprava dokumentace: `Region` jsou požadovány informace k vytvoření nástroje pro rozpoznávání.

Přidali jsme další ukázky a neustále se aktualizují. Nejnovější sadu ukázek najdete v části [sada Speech SDK Samples v úložišti GitHub](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733:2018-může vydávat

Tato verze je první verze Public Preview sady Cognitive Services Speech SDK.
