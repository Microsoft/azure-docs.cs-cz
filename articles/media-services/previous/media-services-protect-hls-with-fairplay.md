---
title: Ochrana obsahu HLS pomocí Microsoft PlayReady nebo Apple FairPlay – Azure | Microsoft Docs
description: Toto téma poskytuje přehled a ukazuje, jak použít Azure Media Services k dynamickému šifrování obsahu HTTP Live Streaming (HLS) pomocí nástroje Apple FairPlay. Také ukazuje, jak používat službu Media Services License Delivery Service k doručování licencí FairPlay klientům.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 15aab28b7dfbaf305412f1080346b54cc6827437
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009636"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Chraňte svůj HLS obsah pomocí Apple FairPlay nebo Microsoft PlayReady

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).   > do Media Services V2 se přidávají žádné nové funkce ani funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .
>

Azure Media Services slouží k dynamickému šifrování obsahu HTTP Live Streaming (HLS) pomocí následujících formátů:  

* **Nejasný klíč pro obálku AES-128**

    Celý blok dat je zašifrovaný pomocí režimu **CBC AES-128** . Dešifrování datového proudu je podporováno v nativním přehrávači pro iOS a OS X. Další informace najdete v tématu [použití dynamického šifrování AES-128 a služby pro doručování klíčů](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    Jednotlivé ukázky videa a zvuku jsou zašifrované pomocí režimu **CBC AES-128** . **Fairplay streamování** (FPS) je integrované do operačních systémů zařízení s nativní podporou pro iOS a Apple TV. Safari v OS X umožňuje použití snímků na základě podpory rozhraní EME (Encrypted Media Extensions).
* **Microsoft PlayReady**

Na následujícím obrázku vidíte pracovní postup **dynamického šifrování HLS + Fairplay nebo PlayReady** .

![Diagram pracovního postupu dynamického šifrování](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Tento článek ukazuje, jak použít Media Services k dynamickému šifrování obsahu HLS pomocí Apple FairPlay. Také ukazuje, jak používat službu Media Services License Delivery Service k doručování licencí FairPlay klientům.

> [!NOTE]
> Pokud chcete obsah HLS šifrovat pomocí PlayReady, je potřeba vytvořit společný klíč obsahu a přidružit ho k assetu. Také je potřeba nakonfigurovat zásady autorizace klíče obsahu, jak je popsáno v tématu [použití dynamického dynamického šifrování PlayReady](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Požadavky a předpoklady

Při použití Media Services k zajištění HLS šifrovaných s FairPlay a k doručování licencí FairPlay se vyžadují následující:

  * Účet Azure: Podrobnosti najdete v článku [bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Účet Media Services. Pokud ho chcete vytvořit, přečtěte si téma [Vytvoření účtu Azure Media Services pomocí Azure Portal](media-services-portal-create-account.md).
  * Zaregistrujte se pomocí [vývojového programu Apple](https://developer.apple.com/).
  * Apple vyžaduje, aby vlastník obsahu získal [balíček pro nasazení](https://developer.apple.com/contact/fps/). Stav, kdy už jste implementovali modul zabezpečení klíčů (KSM) s Media Services a že požadujete finální balíček pro FPS. K dispozici jsou pokyny v posledním balíčku FPS pro vygenerování certifikace a získání tajného klíče aplikace (ASK). Pro konfiguraci FairPlay použijte dotaz.
  * Azure Media Services .NET SDK verze **3.6.0** nebo novější.

Na straně Media Services Key Delivery se musí nastavit následující věci:

  * **Certifikát aplikace (AC)**: Jedná se o soubor. pfx, který obsahuje privátní klíč. Vytvořte tento soubor a Zašifrujte ho heslem.

       Když konfigurujete zásady doručování klíčů, musíte toto heslo a soubor. pfx zadat ve formátu base64.

      Následující postup popisuje, jak vygenerovat soubor certifikátu. pfx pro FairPlay:

    1. Nainstalujte OpenSSL z https://slproweb.com/products/Win32OpenSSL.html .

        Přejít do složky, kde je certifikát FairPlay a další soubory dodávané společností Apple.
    2. V příkazovém řádku spusťte následující příkaz. Tím se soubor. cer převede na soubor. pem.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509-informující der-in FairPlay. cer-out FairPlay-out. pem
    3. V příkazovém řádku spusťte následující příkaz. Tím se soubor. pem převede na soubor. pfx s privátním klíčem. Heslo pro soubor. pfx pak vyzve OpenSSL.

        "C:\OpenSSL-Win32\bin\openssl.exe" PKCS12-export FairPlay-out. pfx-INKEY PrivateKey. pem-in FairPlay-out. pem-Passin file:privatekey-pem-pass.txt
  * **Heslo certifikátu aplikace**: heslo pro vytvoření souboru. pfx.
  * **Heslo certifikátu aplikace**: heslo musíte nahrát podobně jako při odesílání jiných klíčů Media Services. K získání ID Media Services použijte hodnotu výčtu **ContentKeyType. FairPlayPfxPassword** . To je to, co potřebují k použití v rámci možnosti zásady doručování klíčů.
  * **IV**: Tato náhodná hodnota je 16 bajtů. Musí odpovídat IV v zásadě doručení assetu. Vygenerujete IV a umístíte ho na obě místa: zásady doručení assetu a zásady doručení klíčů.
  * **Dotaz**: Tento klíč se obdrží, když vygenerujete certifikaci pomocí portálu pro vývojáře Apple. Každý vývojový tým obdrží jedinečný dotaz. Uložte kopii žádosti a uložte ji na bezpečném místě. Musíte nakonfigurovat ASK jako FairPlayAsk pro pozdější Media Services.
  * **ID žádosti**: Toto ID se získá, když odešlete dotaz do Media Services. Je nutné nahrát dotaz pomocí hodnoty výčtu **ContentKeyType. FairPlayAsk** . V důsledku toho se vrátí ID Media Services a to, co byste měli použít při nastavování možnosti zásady doručování klíčů.

Následující akce musí být nastaveny na straně klienta FPS:

  * **Certifikát aplikace (AC)**: Jedná se o soubor. cer/. der obsahující veřejný klíč, který operační systém používá k šifrování některých datových částí. Media Services musí o tom znát, protože ho vyžaduje hráč. Služba doručování klíčů dešifruje pomocí odpovídajícího privátního klíče.

Pokud chcete přehrát FairPlay zašifrovaný Stream, Získejte nejdřív skutečný dotaz a pak vygenerujte skutečný certifikát. Tento proces vytvoří všechny tři části:

  * soubor. der
  * soubor. pfx
  * heslo pro soubor. pfx

Následující klienti podporují HLS šifrování **AES-128 CBC** : Safari v OS X, Apple TV, iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Konfigurace dynamického šifrování FairPlay a služeb doručování licencí
Níže najdete obecné kroky pro ochranu prostředků pomocí FairPlay pomocí služby doručování licencí Media Services a také pomocí dynamického šifrování.

1. Vytvořte prostředek a nahrajte do něj soubory.
2. Zakódujte prostředek obsahující soubor do sady souborů MP4 s adaptivní přenosovou rychlostí.
3. Vytvořte klíč k obsahu a přiřaďte ho k zakódovanému prostředku.  
4. Nakonfigurujte zásady autorizace pro klíč k obsahu. Určete následující nastavení:

   * Způsob doručení (v tomto případě FairPlay).
   * Konfigurace možností zásad FairPlay Podrobnosti o tom, jak nakonfigurovat FairPlay, najdete v níže uvedené ukázce metody **ConfigureFairPlayPolicyOptions ()** .

     > [!NOTE]
     > Obvykle byste chtěli nakonfigurovat možnosti zásad FairPlay jenom jednou, protože budete mít jenom jednu sadu certifikace a dotaz.
     >
     >
   * Omezení (otevřít nebo token).
   * Informace specifické pro typ doručení klíče, který definuje, jak se klíč doručí klientovi.
5. Nakonfigurujte zásady doručení assetu. Konfigurace zásad doručování zahrnuje:

   * Protokol doručení (HLS).
   * Typ dynamického šifrování (společné šifrování CBC).
   * Adresa URL pro získání licence

     > [!NOTE]
     > Pokud chcete doručovat datový proud, který je zašifrovaný pomocí FairPlay a jiný systém DRM (Digital Rights Management), musíte nakonfigurovat samostatné zásady doručování:
     >
     > * Jedna IAssetDeliveryPolicya pro konfiguraci dynamického adaptivního streamování přes HTTP (POMLČKa) pomocí Common Encryption (CENC) (PlayReady + Widevine) a hladkého pomocí PlayReady
     > * Další IAssetDeliveryPolicy ke konfiguraci FairPlay pro HLS
     >
     >
6. Pokud chcete získat adresu URL streamování, vytvořte lokátor OnDemand.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Použití FairPlay Key Delivery v aplikacích přehrávače
Aplikace přehrávače můžete vyvíjet pomocí sady iOS SDK. Aby bylo možné přehrávat FairPlay obsah, je nutné implementovat protokol License Exchange. Tento protokol není specifikován společností Apple. Jak odesílat požadavky na doručení klíčů, je k diskaždé aplikaci. Služba doručování klíčů Media Services FairPlay očekává, že se SPC přidělí jako webová zpráva zakódovaná v URL formátu www v následujícím tvaru:

`spc=<Base64 encoded SPC>`

> [!NOTE]
> Azure Media Player podporuje přehrávání FairPlay. Další informace najdete v [dokumentaci k Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) .
>
>

## <a name="streaming-urls"></a>Adresy URL streamování
Pokud byl váš Asset zašifrovaný s více než jedním DRM, měli byste použít šifrovací značku v adrese URL streamování: (Format = ' 3u8-AAPL ', Encryption = ' xxx ').

Platí následující důležité informace:

* Lze zadat pouze nula nebo jeden typ šifrování.
* V případě, že se k assetu použilo jenom jedno šifrování, nemusí se typ šifrování zadat v adrese URL.
* Typ šifrování rozlišuje velká a malá písmena.
* Lze zadat následující typy šifrování:  
  * **CENC**: běžné šifrování (PlayReady nebo Widevine)
  * **cbcs-AAPL**: Fairplay
  * **CBC**: šifrování obálek AES

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

1. Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 
2. Do části **appSettings** definované ve vašem souboru app.config přidejte následující elementy:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Příklad

Následující ukázka demonstruje možnost použití Media Services k doručování obsahu zašifrovaného pomocí FairPlay. Tato funkce byla představena v sadě Azure Media Services SDK pro .NET verze 3.6.0. 

Přepište kód v souboru Program.cs kódem zobrazeným v této části.

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

Nezapomeňte aktualizovat proměnné tak, aby odkazovaly do složek, ve kterých jsou umístěné vaše vstupní soubory.

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
using Newtonsoft.Json;
using System.Security.Cryptography.X509Certificates;

namespace DynamicEncryptionWithFairPlay
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
            Console.WriteLine();

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note, you need to pass the key id Guid because we specified
                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }


        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {
            // Create ContentKeyAuthorizationPolicy with Open restrictions
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Open",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                        Requirements = null
                    }
                    };


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Token Authorization Policy",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                        Requirements = tokenTemplateString,
                    }
                    };

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
        }

        static private string GenerateTokenRequirements()
        {
            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

            template.PrimaryVerificationKey = new SymmetricVerificationKey();
            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

            return TokenRestrictionTemplateSerializer.Serialize(template);
        }

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            assetDeliveryPolicyConfiguration);

            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        }


        /// <summary>
        /// Gets the streaming origin locator.
        /// </summary>
        /// <param name="assets"></param>
        /// <returns></returns>
        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the  
            // collection of files in the asset.

            var assetFile = asset.AssetFiles.LoList().Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day readonly access policy.
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin.
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file.
            return originLocator.Path + assetFile.Name;
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
        }

        static private byte[] GetRandomBuffer(int length)
        {
            var returnValue = new byte[length];

            using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
            {
                rng.GetBytes(returnValue);
            }

            return returnValue;
        }
    }
}
```

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="next-steps-media-services-learning-paths"></a>Další kroky: Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
