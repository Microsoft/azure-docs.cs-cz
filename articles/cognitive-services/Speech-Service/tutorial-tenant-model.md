---
title: Vytvoření modelu tenanta (Preview) – služba Speech
titleSuffix: Azure Cognitive Services
description: Automaticky vygenerujte model tenanta (Custom Speech s daty sady Office 365), který využívá data sady Office 365 k zajištění optimálního rozpoznávání řeči pro podnikové výrazy, které jsou bezpečné a kompatibilní.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 8ca31dcadebf2dc47d5a4b4db715f26fb38e204e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816376"
---
# <a name="create-a-tenant-model-preview"></a>Vytvoření modelu tenanta (Preview)

Model tenanta (Custom Speech s daty Office 365) je služba pro zákazníky Office 365 Enterprise, která automaticky generuje vlastní model rozpoznávání řeči z Office 365ch dat vaší organizace. Model, který je vytvořen, je optimalizován pro technické výrazy, názvy žargonu a lidí, a to vše bezpečným a kompatibilním způsobem.

> [!IMPORTANT]
> Pokud se vaše organizace zaregistruje s modelem tenanta, Služba rozpoznávání řeči může získat přístup k jazykovém modelu vaší organizace, který je vygenerovaný z e-mailů a dokumentů veřejných skupin Office 365, které může vidět kdokoli ve vaší organizaci. Správce Office 365 ve vaší organizaci může zapnout nebo vypnout používání jazykových modelů v rámci organizace pomocí portálu pro správu sady Office 365.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Registrace pro použití modelu tenanta v centru pro správu Microsoft 365
> * Získat klíč předplatného pro rozpoznávání řeči
> * Vytvoření modelu tenanta
> * Nasazení modelu tenanta
> * Použití modelu tenanta se sadou Speech SDK

## <a name="enroll-using-the-microsoft-365-admin-center"></a>Registrace pomocí centra pro správu Microsoft 365

Než budete moct nasadit model tenanta, nejdřív se musí zaregistrovat pomocí centra pro správu Microsoft 365. Tuto úlohu může dokončit jenom správce Microsoft 365.

1. Přihlaste se k [centru pro správu Microsoft 365](https://admin.microsoft.com ).
2. Na levém panelu vyberte **Nastavení** a pak **aplikace**.

   ![Registrace modelu tenanta](media/tenant-language-model/tenant-language-model-enrollment.png)

3. Vyhledejte a vyberte **Azure Speech Services**.

   ![Registrace modelu tenanta 2](media/tenant-language-model/tenant-language-model-enrollment-2.png)

4. Klikněte na zaškrtávací políčko a uložte.

Pokud potřebujete vypnout model tenanta, přejděte zpět na tuto obrazovku, zrušte zaškrtnutí políčka a uložte.

## <a name="get-a-speech-subscription-key"></a>Získat klíč předplatného pro rozpoznávání řeči

Aby bylo možné používat model tenanta se sadou Speech SDK, budete potřebovat prostředek pro rozpoznávání řeči a jeho přidružený klíč předplatného.

1. Přihlaste se k webu [Azure Portal](https://aka.ms/azureportal).
2. Vyberte **vytvořit prostředek**.
3. Do vyhledávacího panelu zadejte: **Speech**.
4. Vyberte **řeč**a pak klikněte na **vytvořit**.
5. Pokud chcete vytvořit prostředek, postupujte podle pokynů na obrazovce. Zjisti:
   * **Umístění** je nastaveno na hodnotu **eastus** nebo **westus**.
   * **Cenová úroveň** je nastavená na **S0**.
6. Klikněte na **Vytvořit**.
7. Po několika minutách se prostředek vytvoří. Klíč předplatného je k dispozici v části **Přehled** pro váš prostředek.

## <a name="create-a-model"></a>Vytvoření modelu

Až správce povolí model tenanta pro vaši organizaci, můžete vytvořit jazykový model založený na datech Office 365.

1. Přihlaste se ke službě [Speech Studio](https://speech.microsoft.com/).
2. V pravém horním rohu vyhledejte a klikněte na ikonu ozubeného kolečka (nastavení) a pak vyberte **Nastavení modelu tenanta**.

   ![Nabídka Nastavení](media/tenant-language-model/tenant-language-settings.png)

3. V tomto okamžiku se zobrazí zpráva s informacemi o tom, jestli jste oprávněni vytvořit model tenanta.
   > [!NOTE]
   > Zákazníci Office 365 Enterprise v Severní Amerika mají nárok na vytvoření modelu tenanta (anglicky). Pokud se jedná o Customer Lockbox (CLB), klíč zákazníka (CK) nebo zákazníka Office 365, tato funkce není k dispozici. Pokud chcete zjistit, jestli jste zákazník Key Customer Lockbox nebo zákazníkem, postupujte podle těchto pokynů:
   > * [Customer Lockbox](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack)
   > * [Klíč zákazníka](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests)
   > * [Vládní organizace pro Office 365](https://www.microsoft.com/microsoft-365/government)

4. V dalším kroku vyberte možnost přihlásit **se**. Po přípravě modelu tenanta vám obdržíte e-mail s pokyny.

## <a name="deploy-your-model"></a>Nasazení modelu

Když je model tenanta připravený, nasaďte svůj model pomocí těchto kroků:

1. Klikněte na tlačítko **Zobrazit model** v e-mailu s potvrzením, které jste dostali, nebo se přihlaste ke službě [Speech Studio](https://speech.microsoft.com/)
2. V pravém horním rohu vyhledejte a klikněte na ikonu ozubeného kolečka (nastavení) a pak vyberte **Nastavení modelu tenanta**.

   ![Nabídka Nastavení](media/tenant-language-model/tenant-language-settings.png)

3. Klikněte na tlačítko **nasazení**.
4. Při nasazení modelu se stav změní na **nasazeno**.

## <a name="use-your-model-with-the-speech-sdk"></a>Použití modelu se sadou Speech SDK

Teď, když jste model nasadili, ho můžete použít se sadou Speech SDK. V této části použijete vzorový kód poskytnutý pro volání služby Speech pomocí ověřování Azure AD.

Pojďme se podívat na kód, který budete používat pro volání sady Speech SDK v C#. V tomto příkladu provedete rozpoznávání řeči pomocí modelu tenanta. Tato příručka předpokládá, že už máte nastavenou platformu. Pokud potřebujete pomáhat s nastavením, přečtěte si [rychlý Start: rozpoznávání řeči C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

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

    // Note: ServiceApplicationId is a fixed value.  No need to change.

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

Dále budete potřebovat znovu sestavit a spustit projekt z příkazového řádku. Před spuštěním příkazu budete muset aktualizovat několik parametrů.

1. Nahraďte `<Username>` a `<Password>` hodnotami platného uživatele tenanta.
2. Nahraďte `<Subscription-Key>` klíčem předplatného pro váš prostředek řeči. Tato hodnota je k dispozici v části **Přehled** prostředku rozpoznávání řeči v [Azure Portal](https://aka.ms/azureportal).
3. Nahraďte `<Endpoint-Uri>` koncovým bodem níže. Ujistěte se, že `{your-region}` nahradíte oblastí, ve které jste vytvořili prostředek řeči. Podporovány jsou tyto oblasti: `westus`, `westus2`a `eastus`. Informace o vaší oblasti jsou k dispozici v části **Přehled** pro prostředek řeči v [Azure Portal](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
4. Spusťte příkaz:
   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

## <a name="next-steps"></a>Další kroky

* [Studio Speech](https://speech.microsoft.com/)
* [Speech SDK](speech-sdk.md)
