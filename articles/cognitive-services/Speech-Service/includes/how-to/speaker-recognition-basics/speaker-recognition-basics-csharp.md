---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 2b5a34e8f3e7132a16ad3683b846d57e9ece2cb6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015464"
---
V tomto rychlém startu se naučíte základní vzory návrhu pro rozpoznávání mluvčího pomocí sady Speech SDK, včetně těchto:

* Ověřování závislé na textu a nezávislé na textu
* Identifikace mluvčího pro identifikaci hlasového vzorku mezi skupinou hlasů
* Odstraňování hlasových profilů

Základní informace o konceptech rozpoznávání řeči najdete v článku [Přehled](../../../speaker-recognition-overview.md) .

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Rozpoznávání mluvčího se aktuálně podporuje *jenom* v prostředcích Azure pro rozpoznávání řeči vytvořených v `westus` oblasti.

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat sadu Speech SDK. V závislosti na vaší platformě postupujte podle následujících pokynů:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Jednot <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">PODPORUJÍ <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importovat závislosti

Chcete-li spustit příklady v tomto článku, zahrňte do `using` horní části skriptu následující příkazy.

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Speech pomocí sady Speech SDK, je třeba vytvořit [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet) . V tomto příkladu vytvoříte [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet) pomocí klíče a oblasti předplatného. Také můžete vytvořit nějaký základní často používaný kód, který se použije pro zbytek tohoto článku, který můžete upravit pro různá přizpůsobení.

Všimněte si, že oblast je nastavená na `westus` , protože je jedinou podporovanou oblastí pro službu.

```csharp
public class Program 
{
    static async Task Main(string[] args)
    {
        // replace with your own subscription key 
        string subscriptionKey = "YourSubscriptionKey";
        string region = "westus";
        var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    }
}
```

## <a name="text-dependent-verification"></a>Ověřování závislé na textu

Ověření mluvčího je potvrzení, že mluvčí odpovídá známému nebo **zaregistrovanému** hlasu. Prvním krokem je **zápis** hlasového profilu, aby služba měla něco pro porovnání budoucích ukázek hlasu s. V tomto příkladu zaregistrujete profil s použitím strategie **závislé na textu** , která vyžaduje konkrétní předávací frázi, která se má použít pro zápis i ověřování. Seznam podporovaných předávacích frází najdete v [referenční dokumentaci](/rest/api/speakerrecognition/) .

Začněte vytvořením následující funkce ve `Program` třídě pro zápis hlasového profilu.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextDependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsCount > 0)
            {
                Console.WriteLine("Speak the passphrase, \"My voice is my passport, verify me.\"");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollments needed: {result.RemainingEnrollmentsCount}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

V této funkci `await client.CreateProfileAsync()` se ve skutečnosti vytvoří nový hlasový profil. Po vytvoření určíte, jak budete zadávat zvukové vzorky, a to pomocí `AudioConfig.FromDefaultMicrophoneInput()` v tomto příkladu zaznamenáte zvuk z výchozího vstupního zařízení. V dalším kroku zaregistrujete zvukové vzorky ve `while` smyčce, které sledují počet zbývajících vzorků a vyžadují se pro registraci. V každé iteraci `client.EnrollProfileAsync(profile, audioInput)` se zobrazí výzva k vymluvenému zadání fráze do mikrofonu a přidání ukázky do hlasového profilu.

Po dokončení registrace zavoláte, `await SpeakerVerify(config, profile, profileMapping)` abyste ověřili profil, který jste právě vytvořili. Přidejte další funkci k definování `SpeakerVerify` .

```csharp
public static async Task SpeakerVerify(SpeechConfig config, VoiceProfile profile, Dictionary<string, string> profileMapping)
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerVerificationModel.FromProfile(profile);

    Console.WriteLine("Speak the passphrase to verify: \"My voice is my passport, please verify me.\"");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"Verified voice profile for speaker {profileMapping[result.ProfileId]}, score is {result.Score}");
}
```

V této funkci předáte objekt, `VoiceProfile` který jste právě vytvořili, k inicializaci modelu pro ověřování. V dalším kroku se `await speakerRecognizer.RecognizeOnceAsync(model)` vyzve k opětovnému vymluvenému vynechání fráze, ale tentokrát ji ověří proti vašemu hlasovém profilu a vrátí výsledek podobnosti v rozsahu od 0,0 do 1,0. `result`Objekt také vrátí `Accept` nebo, na `Reject` základě toho, zda se shodují fráze Pass-fráze.

Dále upravte `Main()` funkci tak, aby volala nové funkce, které jste vytvořili. Kromě toho si všimněte, že vytvoříte `Dictionary<string, string>` odkaz pro předání prostřednictvím volání funkcí. Důvodem je, že služba nepovoluje ukládání uživatelsky čitelného názvu s vytvořeným `VoiceProfile` a ukládá pouze ID pro účely ochrany osobních údajů. Ve `VerificationEnroll` funkci přidáte do tohoto slovníku záznam s nově vytvořeným ID společně s textovým názvem. Ve scénářích vývoje aplikací, kde potřebujete zobrazit uživatelsky čitelný název, je **nutné toto mapování uložit někam, služba ho nebude moci uložit**.

```csharp
static async Task Main(string[] args)
{
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    await VerificationEnroll(config, profileMapping);

    Console.ReadLine();
}
```

Spusťte skript a zobrazí se výzva k vymluvenému *zaznamenání mého hlasu je můj účet Passport, prověřit* si třikrát registraci a pro ověření proveďte další čas. Vrácený výsledek je skóre podobnosti, které můžete použít k vytvoření vlastních prahových hodnot pro ověření.

```shell
Enrolling profile id 87-2cef-4dff-995b-dcefb64e203f.
Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 2

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 1

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 0

Speak the passphrase to verify: "My voice is my passport, verify me."
Verified voice profile for speaker Your Name, score is 0.915581
```

## <a name="text-independent-verification"></a>Ověřování nezávislé na textu

Na rozdíl od ověřování **závislého** na textu, ověřování **nezávislého na textu** :

* Nevyžaduje, aby se provedla určitá předávací fráze, cokoli se dá vymluveným.
* Nevyžaduje tři zvukové vzorky *, ale vyžaduje* 20 sekund celkového zvuku.

Udělejte několik jednoduchých změn `VerificationEnroll` funkce pro přepnutí na ověřování nezávislé na **textu** . Nejprve změníte typ ověřování na `VoiceProfileType.TextIndependentVerification` . V dalším kroku změňte `while` smyčku, která se má sledovat `result.RemainingEnrollmentsSpeechLength` , a zobrazí se výzva, abyste mluvit do doby, než bude zachyceno 20 sekund zvuku.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
            {
                Console.WriteLine("Continue speaking to add to the profile enrollment sample.");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

Spusťte program znovu a během fáze ověření vymluvte cokoli, protože není vyžadována předávací fráze. Znovu se vrátí skóre podobnosti.

```shell
Enrolling profile id 4tt87d4-f2d3-44ae-b5b4-f1a8d4036ee9.
Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:15.3200000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:09.8100008

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:05.1900000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00.8700000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00

Speak the passphrase to verify: "My voice is my passport, please verify me."
Verified voice profile for speaker Your Name, score is 0.849409
```

## <a name="speaker-identification"></a>Identifikace mluvčího

Identifikace mluvčího slouží k určení toho **, kdo** z dané skupiny zaregistrovaných hlasů mluví. Tento proces je velmi podobný **ověřování nezávisle na textu**, s hlavním rozdílem, který je schopný ověřit u více hlasových profilů najednou, a ne u jednoho profilu.

Vytvořte funkci `IdentificationEnroll` pro registraci více hlasových profilů. Proces registrace pro každý profil je stejný jako proces registrace pro **ověřování nezávislé na textu** a pro každý profil vyžaduje 20 sekund zvuk. Tato funkce přijímá seznam řetězců a vytvoří `profileNames` nový hlasový profil pro každý název v seznamu. Funkce vrátí seznam `VoiceProfile` objektů, které použijete v další funkci pro identifikaci mluvčího.

```csharp
public static async Task<List<VoiceProfile>> IdentificationEnroll(SpeechConfig config, List<string> profileNames, Dictionary<string, string> profileMapping)
{
    List<VoiceProfile> voiceProfiles = new List<VoiceProfile>();
    using (var client = new VoiceProfileClient(config))
    {
        foreach (string name in profileNames)
        {
            using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
            {
                var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentIdentification, "en-us");
                Console.WriteLine($"Creating voice profile for {name}.");
                profileMapping.Add(profile.Id, name);

                VoiceProfileEnrollmentResult result = null;
                while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
                {
                    Console.WriteLine($"Continue speaking to add to the profile enrollment sample for {name}.");
                    result = await client.EnrollProfileAsync(profile, audioInput);
                    Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                    Console.WriteLine("");
                }
                voiceProfiles.Add(profile);
            }
        }
    }
    return voiceProfiles;
}
```

Vytvořte následující funkci `SpeakerIdentification` , která odešle žádost o identifikaci. Hlavní rozdíl v této funkci v porovnání s požadavkem na **ověření mluvčího** je použití `SpeakerIdentificationModel.FromProfiles()` , které přijímá seznam `VoiceProfile` objektů. 

```csharp
public static async Task SpeakerIdentification(SpeechConfig config, List<VoiceProfile> voiceProfiles, Dictionary<string, string> profileMapping) 
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerIdentificationModel.FromProfiles(voiceProfiles);

    Console.WriteLine("Speak some text to identify who it is from your list of enrolled speakers.");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"The most similar voice profile is {profileMapping[result.ProfileId]} with similarity score {result.Score}");
}
```

Změňte `Main()` funkci na následující. Vytvoříte seznam řetězců `profileNames` , které předáte do `IdentificationEnroll()` funkce. Zobrazí se výzva k vytvoření nového hlasového profilu pro každý název v tomto seznamu, abyste mohli přidat další názvy pro vytvoření dalších profilů pro přátele nebo kolegy.

```csharp
static async Task Main(string[] args)
{
    // replace with your own subscription key 
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    var profileNames = new List<string>() { "Your name", "A friend's name" };
    
    var enrolledProfiles = await IdentificationEnroll(config, profileNames, profileMapping);
    await SpeakerIdentification(config, enrolledProfiles, profileMapping);

    foreach (var profile in enrolledProfiles)
    {
        profile.Dispose();
    }
    Console.ReadLine();
}
```

Spusťte skript a zobrazí se výzva k zápisu ukázek hlasu pro první profil. Po dokončení registrace se zobrazí výzva, abyste tento postup opakovali pro každý název v seznamu `profileNames` . Po dokončení každého zápisu se zobrazí výzva, abyste popsali každého **uživatele** a služba se pokusí tuto osobu identifikovat z vašich registrovaných hlasových profilů.

Tento příklad vrátí jenom nejbližší shodu a je to skóre podobnosti, ale můžete získat úplnou odpověď, která obsahuje prvních pět výsledků podobnosti přidáním `string json = result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)` do `SpeakerIdentification` funkce.

## <a name="changing-audio-input-type"></a>Změna typu zvukového vstupu

Příklady v tomto článku používají jako vstup pro zvukové vzorky výchozí mikrofon zařízení. Nicméně ve scénářích, kdy potřebujete místo vstupu mikrofonu použít zvukové soubory, stačí změnit jakoukoli instanci nástroje `AudioConfig.FromDefaultMicrophoneInput()` na na `AudioConfig.FromWavFileInput(path/to/your/file.wav)` vstup souboru. Můžete mít také smíšené vstupy, a to pomocí mikrofonu pro zápis a soubory pro účely ověření, například.

## <a name="deleting-voice-profile-enrollments"></a>Odstraňování zápisu hlasových profilů

Pokud chcete odstranit zaregistrovaný profil, použijte `DeleteProfileAsync()` funkci na `VoiceProfileClient` objektu. Následující příklad funkce ukazuje, jak odstranit hlasový profil ze známého ID hlasového profilu.

```csharp
public static async Task DeleteProfile(SpeechConfig config, string profileId) 
{
    using (var client = new VoiceProfileClient(config))
    {
        var profile = new VoiceProfile(profileId);
        await client.DeleteProfileAsync(profile);
    }
}
```