---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: bb78a60b911823da96c52a104a3e06ecfc634da6
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210931"
---
V tomto rychlém startu se naučíte základní vzory návrhu pro rozpoznávání mluvčího pomocí sady Speech SDK, včetně těchto:

* Ověřování závislé na textu a nezávislé na textu
* Identifikace mluvčího pro identifikaci hlasového vzorku mezi skupinou hlasů
* Odstraňování hlasových profilů

Základní informace o konceptech rozpoznávání řeči najdete v článku [Přehled](../../../speaker-recognition-overview.md) .

## <a name="skip-to-samples-on-github"></a>Přeskočit na ukázky na GitHubu

Pokud chcete přeskočit přímý na vzorový kód, přečtěte si [ukázky rychlý Start JavaScriptu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/fa6428a0837779cbeae172688e0286625e340942/quickstart/javascript/node/speaker-recognition) na GitHubu.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Rozpoznávání mluvčího se aktuálně podporuje *jenom* v prostředcích Azure pro rozpoznávání řeči vytvořených v `westus` oblasti.

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">sadu Speech SDK pro JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. V závislosti na vaší platformě postupujte podle následujících pokynů:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webový prohlížeč <span class="docon docon-navigate-external x-hidden-focus"></span></a>

V závislosti na cílovém prostředí navíc použijte jednu z následujících možností:

# <a name="script"></a>[pravidel](#tab/script)

Stáhněte a extrahujte <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">sadu Speech SDK pro <span class="docon docon-navigate-external x-hidden-focus"></span> JavaScript</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* soubor a umístěte ji do složky přístupné pro váš soubor HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Pokud cílíte na webový prohlížeč a použijete `<script>` značku; `sdk` Předpona není nutná. `sdk`Předpona je alias použitý k pojmenování `require` modulu.

# <a name="import"></a>[importovat](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Další informace o najdete v `import` tématu <a href="https://javascript.info/import-export" target="_blank">Export a import <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

# <a name="require"></a>[žádá](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Další informace o najdete v `require` tématu <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">co je potřeba? <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

---

## <a name="import-dependencies"></a>Importovat závislosti

Chcete-li spustit příklady v tomto článku, přidejte následující příkazy v horní části souboru. js.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="dependencies":::

Tyto příkazy importují požadované knihovny a k získání klíče a oblasti předplatného služby Speech z proměnných prostředí. Také určují cesty ke zvukovým souborům, které budete používat v následujících úlohách.

## <a name="create-helper-function"></a>Vytvořit pomocnou funkci

Přidejte následující funkci pomocníka pro čtení zvukových souborů do datových proudů pro použití službou Speech.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="helpers":::

V této funkci použijete metody [AudioInputStream. createPushStream](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioinputstream?view=azure-node-latest&preserve-view=true#createpushstream-audiostreamformat-) a [AudioConfig. fromStreamInput](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true#fromstreaminput-audioinputstream---pullaudioinputstreamcallback-) k vytvoření objektu [AudioConfig](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true) . Tento `AudioConfig` objekt představuje zvukový datový proud. `AudioConfig`V následujících úlohách použijete několik těchto objektů.

## <a name="text-dependent-verification"></a>Ověřování závislé na textu

Ověření mluvčího je potvrzení, že mluvčí odpovídá známému nebo **zaregistrovanému** hlasu. Prvním krokem je **zápis** hlasového profilu, aby služba měla něco pro porovnání budoucích ukázek hlasu s. V tomto příkladu zaregistrujete profil s použitím strategie **závislé na textu** , která vyžaduje konkrétní přístupové heslo, které se má použít pro zápis i ověřování. Seznam podporovaných přístupových hesel najdete v [referenční dokumentaci](https://docs.microsoft.com/rest/api/speakerrecognition/) .

### <a name="textdependentverification-function"></a>TextDependentVerification – funkce

Začněte vytvořením `TextDependentVerification` funkce.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_dependent_verification":::

Tato funkce vytvoří objekt [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) s metodou [VoiceProfileClient. createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) . Všimněte si, že existují tři [typy](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofiletype?view=azure-node-latest&preserve-view=true) `VoiceProfile` :

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

V tomto případě předáte `VoiceProfileType.TextDependentVerification` `VoiceProfileClient.createProfileAsync` .

Pak zavoláte dvě pomocné funkce, které definujete jako další, `AddEnrollmentsToTextDependentProfile` a `SpeakerVerify` . Nakonec zavolejte [VoiceProfileClient. deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) a odstraňte profil.

### <a name="addenrollmentstotextdependentprofile-function"></a>AddEnrollmentsToTextDependentProfile – funkce

Zadejte následující funkci pro zápis hlasového profilu.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_dependent":::

V této funkci zavoláte `GetAudioConfigFromFile` funkci, kterou jste definovali dříve, a vytvoříte `AudioConfig` objekty ze vzorků zvuku. Tyto zvukové vzorky obsahují přístupové heslo, například "můj hlas je můj účet Passport, ověřit mě." Tyto ukázky zvuku pak zaregistrujete pomocí metody [VoiceProfileClient. enrollProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) .

### <a name="speakerverify-function"></a>SpeakerVerify – funkce

Definujte `SpeakerVerify` následujícím způsobem.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_verify":::

V této funkci vytvoříte objekt [SpeakerVerificationModel](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?view=azure-node-latest&preserve-view=true) s metodou [SpeakerVerificationModel. FromProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?view=azure-node-latest&preserve-view=true#fromprofile-voiceprofile-) předáním objektu [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) , který jste vytvořili dříve.

Dále zavoláte metodu [SpeechRecognizer. recognizeOnceAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) , která ověří ukázku zvuku, která obsahuje stejné heslo jako ukázky zvuku, které jste předtím zaregistrovali. `SpeechRecognizer.recognizeOnceAsync` Vrátí objekt [SpeakerRecognitionResult](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?view=azure-node-latest&preserve-view=true) , jehož `score` vlastnost obsahuje skóre podobnosti v rozsahu od 0,0 do 1,0. `SpeakerRecognitionResult`Objekt obsahuje také `reason` vlastnost typu [ResultReason](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/resultreason?view=azure-node-latest&preserve-view=true). Pokud bylo ověření úspěšné, `reason` vlastnost by měla mít hodnotu `RecognizedSpeaker` .

## <a name="text-independent-verification"></a>Ověřování nezávislé na textu

Na rozdíl od ověřování **závislého** na textu, ověřování **nezávislého na textu** :

* Nevyžaduje, aby bylo možné některé heslo vymluveným, cokoli se dá přehlasovat.
* Nevyžaduje tři zvukové vzorky *, ale vyžaduje* 20 sekund celkového zvuku.

### <a name="textindependentverification-function"></a>TextIndependentVerification – funkce

Začněte vytvořením `TextIndependentVerification` funkce.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_verification":::

Podobně jako `TextDependentVerification` funkce Tato funkce vytvoří objekt [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) s metodou [VoiceProfileClient. createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) .

V tomto případě předáte `VoiceProfileType.TextIndependentVerification` `createProfileAsync` .

Potom zavolejte dvě pomocné funkce: `AddEnrollmentsToTextIndependentProfile` , které definujete další, a `SpeakerVerify` , které jste již definovali. Nakonec zavolejte [VoiceProfileClient. deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) a odstraňte profil.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Zadejte následující funkci pro zápis hlasového profilu.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_independent":::

V této funkci zavoláte `GetAudioConfigFromFile` funkci, kterou jste definovali dříve, a vytvoříte `AudioConfig` objekty ze vzorků zvuku. Tyto ukázky zvuku pak zaregistrujete pomocí metody [VoiceProfileClient. enrollProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) .

## <a name="speaker-identification"></a>Identifikace mluvčího

Identifikace mluvčího slouží k určení toho **, kdo** z dané skupiny zaregistrovaných hlasů mluví. Tento proces se podobá **ověřování nezávisle na textu**, s hlavním rozdílem, který je schopný ověřit u několika hlasových profilů najednou, a ne u jednoho profilu.

### <a name="textindependentidentification-function"></a>TextIndependentIdentification – funkce

Začněte vytvořením `TextIndependentIdentification` funkce.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_indentification":::

Podobně jako `TextDependentVerification` `TextIndependentVerification` funkce a vytvoří tato funkce objekt [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) s metodou [VoiceProfileClient. createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) .

V tomto případě předáte `VoiceProfileType.TextIndependentIdentification` `VoiceProfileClient.createProfileAsync` .

Potom zavolejte dvě pomocné funkce: `AddEnrollmentsToTextIndependentProfile` , které jste již definovali a `SpeakerIdentify` které definujete další. Nakonec zavolejte [VoiceProfileClient. deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) a odstraňte profil.

### <a name="speakeridentify-function"></a>SpeakerIdentify – funkce

Definujte `SpeakerIdentify` funkci následujícím způsobem.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_identify":::

V této funkci vytvoříte objekt [SpeakerIdentificationModel](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?view=azure-node-latest&preserve-view=true) s metodou [SpeakerIdentificationModel. fromProfiles](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?view=azure-node-latest&preserve-view=true#fromprofiles-voiceprofile---) předáním objektu [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) , který jste vytvořili dříve.

Dále zavoláte metodu [SpeechRecognizer. recognizeOnceAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) a předáte ukázku zvuku.
`SpeechRecognizer.recognizeOnceAsync` pokusí se identifikovat hlas pro tuto ukázku zvuku na základě `VoiceProfile` objektů, které jste použili k vytvoření `SpeakerIdentificationModel` . Vrátí objekt [SpeakerRecognitionResult](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?view=azure-node-latest&preserve-view=true) , jehož `profileId` vlastnost identifikuje odpovídající `VoiceProfile` , pokud existuje, zatímco `score` vlastnost obsahuje skóre podobnosti v rozsahu od 0,0 do 1,0.

## <a name="main-function"></a>Main – funkce

Nakonec definujte funkci následujícím `main` způsobem.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="main":::

Tato funkce vytvoří objekt [VoiceProfileClient](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true) , který se používá k vytváření, registraci a odstraňování hlasových profilů. Potom volá dříve definované funkce.
