---
title: Vytvoření modelu klienta (náhled) – služba rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Automaticky vygenerujte zabezpečený a kompatibilní model klienta (vlastní řeč s daty Office 365), který používá vaše data Office 365 k zajištění optimálního rozpoznávání řeči pro podmínky specifické pro organizaci.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: a83ed5c9cec994c1bc4cadd5cf6208c159823658
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469013"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Kurz: Vytvoření modelu klienta (náhled)

Tenant Model (Vlastní řeč s daty Office 365) je služba pro přihlášení pro zákazníky Office 365 enterprise, která automaticky generuje vlastní model rozpoznávání řeči z dat Office 365 vaší organizace. Model je optimalizován pro technické termíny, žargon a jména lidí, to vše bezpečným a kompatibilním způsobem.

> [!IMPORTANT]
> Pokud se vaše organizace zaregistruje pomocí služby Model klienta, může služba Speech Service přistupovat k jazykovému modelu vaší organizace. Model se generuje z e-mailů a dokumentů veřejné skupiny Office 365, které může zobrazit kdokoli ve vaší organizaci. Správce Office 365 vaší organizace může zapnout nebo vypnout používání jazykového modelu celé organizace z portálu pro správu Office 365.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Registrace modelu klienta pomocí Centra pro správu Microsoftu 365
> * Získání klíče předplatného řeči
> * Vytvoření modelu klienta
> * Nasazení modelu klienta
> * Použití modelu klienta se sadou Speech SDK

## <a name="enroll-in-the-tenant-model-service"></a>Zapsat do služby Model klienta

Než budete moci nasadit model klienta, musíte být zapsáni ve službě Model klienta. Registrace je dokončena v Centru pro správu Microsoftu 365 a může ji provádět jenom správce Microsoftu 365.

1. Přihlaste se k [Centru pro správu Microsoftu 365](https://admin.microsoft.com).

1. V levém podokně vyberte **Nastavení**, pak vyberte **Nastavení** z vnořené nabídky a pak v hlavním okně vyberte **Azure Speech Services.**

   ![Podokno Služby & doplňky](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Zaškrtněte políčko **Povolit model jazyka celé organizace** a pak vyberte Uložit **změny**.

   ![Podokno Azure Speech Services](media/tenant-language-model/tenant-language-model-enrollment-2.png)

Vypnutí instance modelu klienta:
1. Opakujte předchozí kroky 1 a 2.
1. Zrušte zaškrtnutí **políčka Povolit model jazyka celé organizace** a pak vyberte Uložit **změny**.

## <a name="get-a-speech-subscription-key"></a>Získání klíče předplatného řeči

Chcete-li použít model klienta s sadou Speech SDK, potřebujete prostředek řeči a jeho přidružený klíč předplatného.

1. Přihlaste se k [portálu Azure](https://aka.ms/azureportal).
1. Vyberte **Vytvořit prostředek**.
1. Do pole **Hledat** zadejte **řeč**.
1. V seznamu výsledků vyberte **Možnost Řeč**a pak **vyberte Vytvořit**.
1. Podle pokynů na obrazovce vytvořte svůj zdroj. Ujistěte se, že:
   * **Umístění** je nastaveno na **eastus** nebo **westus**.
   * **Cenová úroveň** je nastavena na **S0**.
1. Vyberte **Vytvořit**.

   Po několika minutách je vytvořen prostředek. Klíč předplatného je k dispozici v části **Přehled** pro váš prostředek.

## <a name="create-a-language-model"></a>Vytvoření jazykového modelu

Poté, co váš správce povolil model klienta pro vaši organizaci, můžete vytvořit jazykový model založený na datech Office 365.

1. Přihlaste se do [aplikace Speech Studio](https://speech.microsoft.com/).
1. Vpravo nahoře vyberte **Nastavení** (ikona ozubeného kola) a pak vyberte **Nastavení modelu klienta**.

   ![Odkaz "Nastavení modelu klienta"](media/tenant-language-model/tenant-language-settings.png)

   Aplikace Speech Studio zobrazí zprávu, která vás pozná, zda máte kvalifikaci k vytvoření modelu klienta.

   > [!NOTE]
   > Zákazníci Office 365 enterprise v Severní Americe mohou vytvořit model klienta (anglicky). Pokud jste zákazník, klíč zákazníka nebo zákazník Office 365 Government, tato funkce není k dispozici. Informace o tom, zda se jedná o bezpečnostní schránku zákazníka nebo zákazníka, najdete v tématu:
   > * [Customer Lockbox](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Klíč zákazníka](/microsoft-365/compliance/customer-key-overview)
   > * [Office 365 pro státní správu](https://www.microsoft.com/microsoft-365/government)

1. Zvolte **Přihlásit**.

   Až bude model vašeho tenanta připravený, obdržíte potvrzovací e-mailovou zprávu s dalšími pokyny.

## <a name="deploy-your-tenant-model"></a>Nasazení modelu klienta

Když je instance modelu klienta připravená, nasaďte ji následujícím způsobem:

1. V potvrzovací e-mailové zprávě vyberte tlačítko **Zobrazit model.** Nebo se přihlaste do [aplikace Speech Studio](https://speech.microsoft.com/).
1. Vpravo nahoře vyberte **Nastavení** (ikona ozubeného kola) a pak vyberte **Nastavení modelu klienta**.

   ![Odkaz "Nastavení modelu klienta"](media/tenant-language-model/tenant-language-settings.png)

1. Vyberte **Nasadit**.

   Po nasazení modelu se stav změní na *Nasazeno*.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Použití modelu klienta se sadou Speech SDK

Teď, když jste nasadili model, můžete jej použít s sadou Speech SDK. V této části použijete ukázkový kód k volání služby Řeči pomocí ověřování Azure Active Directory (Azure AD).

Podívejme se na kód, který použijete k volání sady Speech SDK v c#. V tomto příkladu provedete rozpoznávání řeči pomocí modelu klienta. Tato příručka předpokládá, že vaše platforma je již nastavena. Pokud potřebujete nápovědu k nastavení, [přečtěte si úvodní příručku: Rozpoznat řeč, C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

Zkopírujte do svého projektu tento kód:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // ServiceApplicationId is a fixed value. No need to change it.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Dále je třeba znovu sestavit a spustit projekt z příkazového řádku. Před spuštěním příkazu aktualizujte několik parametrů následujícím způsobem:

1. Nahraďte `<Username>` a `<Password>` s hodnotami pro platného uživatele klienta.
1. Nahraďte `<Subscription-Key>` klíčem předplatného pro prostředek řeči. Tato hodnota je dostupná v části **Přehled** pro váš prostředek řeči na [webu Azure Portal](https://aka.ms/azureportal).
1. Nahraďte `<Endpoint-Uri>` následujícím koncovým bodem. Ujistěte se, `{your region}` že nahradíte oblastí, kde byl vytvořen prostředek řeči. Tyto oblasti jsou `westus` `westus2`podporovány: , a `eastus`. Informace o vaší oblasti jsou dostupné v části **Přehled** prostředku řeči na [webu Azure Portal](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Spusťte následující příkaz:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

V tomto kurzu jste se naučili používat data Office 365 k vytvoření vlastního modelu rozpoznávání řeči, jeho nasazení a použití s sadou Speech SDK.

## <a name="next-steps"></a>Další kroky

* [Řečové studio](https://speech.microsoft.com/)
* [Speech SDK](speech-sdk.md)
