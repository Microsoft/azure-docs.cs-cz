---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 489514068d83f7b2953732415ba066a2d4555df8
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015487"
---
V tomto rychlém startu se naučíte základní vzory návrhu pro rozpoznávání mluvčího pomocí sady Speech SDK, včetně těchto:

* Ověřování závislé na textu a nezávislé na textu
* Identifikace mluvčího pro identifikaci hlasového vzorku mezi skupinou hlasů
* Odstraňování hlasových profilů

Základní informace o konceptech rozpoznávání řeči najdete v článku [Přehled](../../../speaker-recognition-overview.md) .

## <a name="skip-to-samples-on-github"></a>Přeskočit na ukázky na GitHubu

Pokud chcete přeskočit přímý na vzorový kód, přečtěte si [ukázky pro rychlý Start C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) na GitHubu.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Rozpoznávání mluvčího se aktuálně podporuje *jenom* v prostředcích Azure pro rozpoznávání řeči vytvořených v `westus` oblasti.

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat sadu Speech SDK. V závislosti na vaší platformě postupujte podle následujících pokynů:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Systému <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importovat závislosti

Chcete-li spustit příklady v tomto článku, přidejte následující příkazy v horní části souboru. cpp.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="dependencies":::

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Speech pomocí sady Speech SDK, je třeba vytvořit [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="get_speech_config":::

## <a name="text-dependent-verification"></a>Ověřování závislé na textu

Ověření mluvčího je potvrzení, že mluvčí odpovídá známému nebo **zaregistrovanému** hlasu. Prvním krokem je **zápis** hlasového profilu, aby služba měla něco pro porovnání budoucích ukázek hlasu s. V tomto příkladu zaregistrujete profil s použitím strategie **závislé na textu** , která vyžaduje konkrétní přístupové heslo, které se má použít pro zápis i ověřování. Seznam podporovaných přístupových hesel najdete v [referenční dokumentaci](/rest/api/speakerrecognition/) .

### <a name="textdependentverification-function"></a>TextDependentVerification – funkce

Začněte vytvořením `TextDependentVerification` funkce.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_dependent_verification":::

Tato funkce vytvoří objekt [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) s metodou [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) . Všimněte si, že existují tři [typy](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#enum-voiceprofiletype) `VoiceProfile` :

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

V tomto případě předáte `VoiceProfileType::TextDependentVerification` `CreateProfileAsync` .

Pak zavoláte dvě pomocné funkce, které definujete jako další, `AddEnrollmentsToTextDependentProfile` a `SpeakerVerify` . Nakonec zavolejte [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) a vyčistěte profil.

### <a name="addenrollmentstotextdependentprofile-function"></a>AddEnrollmentsToTextDependentProfile – funkce

Zadejte následující funkci pro zápis hlasového profilu.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_dependent":::

V této funkci zaregistrujete zvukové vzorky ve `while` smyčce, které sledují počet zbývajících vzorků a vyžadují se pro registraci. V každé iteraci vás [EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) vyzve k vymluvenému zadání hesla do mikrofonu a přidá ukázku do hlasového profilu.

### <a name="speakerverify-function"></a>SpeakerVerify – funkce

Definujte `SpeakerVerify` následujícím způsobem.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_verify":::

V této funkci vytvoříte objekt [SpeakerVerificationModel](/cpp/cognitive-services/speech/speakerverificationmodel) s metodou [SpeakerVerificationModel:: FromProfile](/cpp/cognitive-services/speech/speakerverificationmodel#fromprofile) předáním objektu [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) , který jste vytvořili dříve.

V dalším kroku [SpeechRecognizer:: RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) vás vyzve k opětovnému nahlasování hesla, ale tentokrát ho ověří proti vašemu hlasovém profilu a vrátí výsledek podobnosti v rozsahu od 0,0 do 1,0. Objekt [SpeakerRecognitionResult](/cpp/cognitive-services/speech/speakerrecognitionresult) také vrátí `Accept` nebo `Reject` , na základě toho, zda heslo odpovídá.

## <a name="text-independent-verification"></a>Ověřování nezávislé na textu

Na rozdíl od ověřování **závislého** na textu, ověřování **nezávislého na textu** :

* Nevyžaduje, aby bylo možné některé heslo vymluveným, cokoli se dá přehlasovat.
* Nevyžaduje tři zvukové vzorky *, ale vyžaduje* 20 sekund celkového zvuku.

### <a name="textindependentverification-function"></a>TextIndependentVerification – funkce

Začněte vytvořením `TextIndependentVerification` funkce.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_verification":::

Podobně jako `TextDependentVerification` funkce Tato funkce vytvoří objekt [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) s metodou [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) .

V tomto případě předáte `VoiceProfileType::TextIndependentVerification` `CreateProfileAsync` .

Potom zavolejte dvě pomocné funkce: `AddEnrollmentsToTextIndependentProfile` , které definujete další, a `SpeakerVerify` , které jste již definovali. Nakonec zavolejte [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) a vyčistěte profil.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Zadejte následující funkci pro zápis hlasového profilu.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_independent":::

V této funkci zaregistrujete zvukové vzorky ve `while` smyčce, které sledují počet sekund zvukového zůstatku a požadováno pro zápis. V každé iteraci vás [EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) vyzve, abyste se domluvili do mikrofonu a přidali ukázku do hlasového profilu.

## <a name="speaker-identification"></a>Identifikace mluvčího

Identifikace mluvčího slouží k určení toho **, kdo** z dané skupiny zaregistrovaných hlasů mluví. Tento proces je velmi podobný **ověřování nezávisle na textu**, s hlavním rozdílem, který je schopný ověřit u více hlasových profilů najednou, a ne u jednoho profilu.

### <a name="textindependentidentification-function"></a>TextIndependentIdentification – funkce

Začněte vytvořením `TextIndependentIdentification` funkce.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_indentification":::

Podobně jako `TextDependentVerification` `TextIndependentVerification` funkce a vytvoří tato funkce objekt [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) s metodou [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) .

V tomto případě předáte `VoiceProfileType::TextIndependentIdentification` `CreateProfileAsync` .

Potom zavolejte dvě pomocné funkce: `AddEnrollmentsToTextIndependentProfile` , které jste již definovali a `SpeakerIdentify` které definujete další. Nakonec zavolejte [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) a vyčistěte profil.

### <a name="speakeridentify-function"></a>SpeakerIdentify – funkce

Definujte `SpeakerIdentify` funkci následujícím způsobem.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_identify":::

V této funkci vytvoříte objekt [SpeakerIdentificationModel](/cpp/cognitive-services/speech/speakeridentificationmodel) s metodou [SpeakerIdentificationModel:: FromProfiles](/cpp/cognitive-services/speech/speakeridentificationmodel#fromprofiles) . `SpeakerIdentificationModel::FromProfiles` přijímá seznam objektů [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) . V takovém případě jednoduše předáte `VoiceProfile` objekt, který jste vytvořili dříve. Pokud však chcete, můžete předat více `VoiceProfile` objektů, z nichž každý je zapsán pomocí zvukového vzorku z jiného hlasu.

Potom [SpeechRecognizer:: RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) vás vyzve, abyste mohli znovu mluvit. Tentokrát porovná váš hlas k zaregistrovaným hlasovým profilům a vrátí nejvíce podobný hlasový profil.

## <a name="main-function"></a>Main – funkce

Nakonec definujte funkci následujícím `main` způsobem.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="main":::

Tato funkce jednoduše volá funkce, které jste definovali dříve. Za prvé, ale vytvoří objekt [VoiceProfileClient](/cpp/cognitive-services/speech/voiceprofileclient) a objekt [SpeakerRecognizer](/cpp/cognitive-services/speech/speakerrecognizer) .

```
auto speech_config = GetSpeechConfig();
auto client = VoiceProfileClient::FromConfig(speech_config);
auto recognizer = SpeakerRecognizer::FromConfig(speech_config, audio_config);
```

`VoiceProfileClient`Slouží k vytváření, registraci a odstraňování hlasových profilů. `SpeakerRecognizer`Slouží k ověření ukázek řeči proti jednomu nebo více zaregistrovaným hlasovým profilům.

## <a name="changing-audio-input-type"></a>Změna typu zvukového vstupu

Příklady v tomto článku používají jako vstup pro zvukové vzorky výchozí mikrofon zařízení. Nicméně ve scénářích, kdy potřebujete místo vstupu mikrofonu použít zvukové soubory, jednoduše změňte následující řádek:

```
auto audio_config = Audio::AudioConfig::FromDefaultMicrophoneInput();
```

na

```
auto audio_config = Audio::AudioConfig::FromWavFileInput(path/to/your/file.wav);
```

Nebo nahraďte jakékoliv použití `audio_config` příkazem [audio:: AudioConfig:: FromWavFileInput](/cpp/cognitive-services/speech/audio-audioconfig#fromwavfileinput). Můžete mít také smíšené vstupy, a to pomocí mikrofonu pro zápis a soubory pro účely ověření, například.