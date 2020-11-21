---
title: Vytvoření modelu tenanta (Preview) – služba Speech
titleSuffix: Azure Cognitive Services
description: Automaticky vygenerujte zabezpečený a kompatibilní model klienta (Custom Speech s daty Microsoft 365), který používá vaše Microsoft 365 data k zajištění optimálního rozpoznávání řeči pro konkrétní organizace.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 48cde51ee9941f705aa848d121c419a8f0c9ad1a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013706"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Kurz: vytvoření modelu tenanta (Preview)

Model tenanta (Custom Speech s daty Microsoft 365) je služba pro Microsoft 365 podnikovým zákazníkům, která automaticky generuje vlastní model rozpoznávání řeči z Microsoft 365ch dat vaší organizace. Model je optimalizovaný pro technické výrazy, žargonu a jména lidí, a to vše bezpečným a kompatibilním způsobem.

> [!IMPORTANT]
> Pokud se vaše organizace zaregistruje pomocí služby modelu klienta, služba Speech může získat přístup k jazykovým modelům vaší organizace. Model se generuje z Microsoft 365 e-mailů a dokumentů veřejné skupiny, které může vidět kdokoli ve vaší organizaci. Správce vaší organizace může na portálu pro správu zapnout nebo vypnout použití jazykového modelu v rámci organizace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zaregistrujte se do modelu tenanta pomocí centra pro správu Microsoft 365.
> * Získat klíč předplatného pro rozpoznávání řeči
> * Vytvoření modelu tenanta
> * Nasazení modelu tenanta
> * Použití modelu tenanta se sadou Speech SDK

## <a name="enroll-in-the-tenant-model-service"></a>Zaregistrujte se do služby modelu tenanta.

Než budete moct nasadit model tenanta, musíte být zaregistrované ve službě modelu tenanta. Registrace se dokončila v centru pro správu Microsoft 365 a může ji udělat jenom správce.

1. Přihlaste se k [Centru pro správu Microsoftu 365](https://admin.microsoft.com).

1. V levém podokně vyberte **Nastavení**, v nabídce vnořené vyberte **Nastavení** a pak v hlavním okně vyberte **Azure Speech Services** .

   ![Podokno "& doplňky" Services](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Zaškrtněte políčko pro **Povolení jazykových modelů v rámci organizace** a pak vyberte **Uložit změny**.

   ![Podokno služby Azure Speech Services](media/tenant-language-model/tenant-language-model-enrollment-2.png)

Postup vypnutí instance modelu tenanta:
1. Opakujte předchozí kroky 1 a 2.
1. Zrušte zaškrtnutí políčka pro **Povolení jazykových modelů v rámci organizace** a pak vyberte **Uložit změny**.

## <a name="get-a-speech-subscription-key"></a>Získat klíč předplatného pro rozpoznávání řeči

Pokud chcete model tenanta použít se sadou Speech SDK, potřebujete prostředek pro rozpoznávání řeči a jeho přidružený klíč předplatného.

1. Přihlaste se na [Azure Portal](https://aka.ms/azureportal).
1. Vyberte **Vytvořit prostředek**.
1. Do **vyhledávacího** pole zadejte **Speech**.
1. V seznamu výsledků vyberte **řeč** a pak vyberte **vytvořit**.
1. Pokud chcete vytvořit prostředek, postupujte podle pokynů na obrazovce. Ujistěte se, že:
   * **Umístění** je nastaveno na hodnotu **eastus** nebo **westus**.
   * **Cenová úroveň** je nastavená na **S0**.
1. Vyberte **Vytvořit**.

   Po několika minutách se prostředek vytvoří. Klíč předplatného je k dispozici v části **Přehled** pro váš prostředek.

## <a name="create-a-language-model"></a>Vytvoření jazykového modelu

Až správce povolí model tenanta pro vaši organizaci, můžete vytvořit jazykový model založený na datech Microsoft 365.

1. Přihlaste se ke službě [Speech Studio](https://speech.microsoft.com/).
1. V pravém horním rohu vyberte **Nastavení** (ikona ozubeného kolečka) a pak vyberte **Nastavení modelu tenanta**.

   ![Odkaz "nastavení modelu tenanta"](media/tenant-language-model/tenant-language-settings.png)

   Speech Studio zobrazí zprávu, která vám umožní zjistit, jestli máte v úmyslu vytvořit model tenanta.

   > [!NOTE]
   > Podnikoví zákazníci v Severní Amerika mají nárok na vytvoření modelu tenanta (anglicky). Pokud jste Customer Lockbox, klíč zákazníka nebo zákazníkem pro státní správu Office 365, tato funkce není k dispozici. Pokud chcete zjistit, jestli Customer Lockbox jste zákazníkem zákaznického zákazníka, přečtěte si téma:
   > * [Customer Lockbox](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Klíč zákazníka](/microsoft-365/compliance/customer-key-overview)
   > * [Vládní organizace pro Office 365](https://www.microsoft.com/microsoft-365/government)

1. Zvolte **Přihlásit**.

   Až bude model tenanta připravený, obdržíte e-mailovou zprávu s potvrzením s dalšími pokyny.

## <a name="deploy-your-tenant-model"></a>Nasazení modelu tenanta

Když je vaše instance modelu tenanta připravená, nasaďte ji následujícím způsobem:

1. V potvrzovací e-mailové zprávě vyberte tlačítko **Zobrazit model** . Nebo se přihlaste ke službě [Speech Studio](https://speech.microsoft.com/).
1. V pravém horním rohu vyberte **Nastavení** (ikona ozubeného kolečka) a pak vyberte **Nastavení modelu tenanta**.

   ![Odkaz "nastavení modelu tenanta"](media/tenant-language-model/tenant-language-settings.png)

1. Vyberte **Nasadit**.

   Po nasazení modelu se stav změní na *nasazeno*.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Použití modelu tenanta se sadou Speech SDK

Teď, když jste model nasadili, ho můžete použít se sadou Speech SDK. V této části použijete vzorový kód pro volání služby Speech pomocí ověřování Azure Active Directory (Azure AD).

Pojďme se podívat na kód, který použijete pro volání sady Speech SDK v jazyce C#. V tomto příkladu provedete rozpoznávání řeči pomocí modelu tenanta. Tato příručka předpokládá, že už máte nastavenou platformu. Pokud potřebujete pomocníka s nastavením, přečtěte si [rychlý Start: rozpoznávání řeči, C# (.NET Core)](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore).

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

Dále je nutné znovu sestavit a spustit projekt z příkazového řádku. Před spuštěním příkazu aktualizujte několik parametrů provedením následujícího postupu:

1. `<Username>`Hodnoty a nahraďte `<Password>` platným uživatelem tenanta.
1. Nahraďte `<Subscription-Key>` klíčem předplatného pro váš prostředek řeči. Tato hodnota je k dispozici v části **Přehled** prostředku rozpoznávání řeči v [Azure Portal](https://aka.ms/azureportal).
1. Nahraďte `<Endpoint-Uri>` následujícím koncovým bodem. Ujistěte se, že jste nahradili `{your region}` oblast, ve které byl prostředek řeči vytvořen. Tyto oblasti jsou podporované: `westus` , `westus2` a `eastus` . Informace o vaší oblasti jsou k dispozici v části **Přehled** prostředku rozpoznávání řeči v [Azure Portal](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Spusťte následující příkaz:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

V tomto kurzu jste se naučili, jak pomocí Microsoft 365ch dat vytvořit vlastní model rozpoznávání řeči, nasadit ho a použít ho v sadě Speech SDK.

## <a name="next-steps"></a>Další kroky

* [Speech Studio](https://speech.microsoft.com/)
* [Speech SDK](speech-sdk.md)