---
title: Ochrana obsahu HLS pomocí microsoftplayready nebo Apple FairPlay – Azure | Dokumenty společnosti Microsoft
description: Toto téma poskytuje přehled a ukazuje, jak pomocí Mediálních služeb Azure dynamicky šifrovat obsah http živého streamování (HLS) pomocí Apple FairPlay. Ukazuje také, jak používat službu doručování licencí Media Services k doručování licencí FairPlay klientům.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 873bc4ab5e435b91ff4400a39c92db0d0bb9baa8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74968761"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Chraňte svůj obsah HLS pomocí Apple FairPlay nebo Microsoft PlayReady

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).   > Do služby Media Services v2 nejsou přidávány žádné nové funkce ani funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)
>

Azure Media Services umožňuje dynamicky šifrovat obsah http živého streamování (HLS) pomocí následujících formátů:  

* **AES-128 obálka zrušte klíč**

    Celý blok je šifrován pomocí režimu **AES-128 CBC.** Dešifrování datového proudu je podporováno iOS a OS X přehrávačnativně. Další informace naleznete [v tématu Použití dynamického šifrování AES-128 a služby doručování klíčů](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    Jednotlivé vzorky videa a zvuku jsou šifrovány pomocí režimu **AES-128 CBC.** **FairPlay Streaming** (FPS) je integrován do operačních systémů zařízení s nativní podporou v systémech iOS a Apple TV. Safari na OS X umožňuje FPS pomocí podpory rozhraní Encrypted Media Extensions (EME).
* **Microsoft PlayReady**

Následující obrázek znázorňuje pracovní postup **dynamického šifrování HLS + FairPlay nebo PlayReady.**

![Diagram pracovního postupu dynamického šifrování](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Tento článek ukazuje, jak pomocí mediálních služeb dynamicky šifrovat obsah HLS pomocí Apple FairPlay. Ukazuje také, jak používat službu doručování licencí Media Services k doručování licencí FairPlay klientům.

> [!NOTE]
> Pokud chcete také šifrovat obsah HLS pomocí služby PlayReady, musíte vytvořit společný klíč obsahu a přidružit jej ke svému datovému zdroji. Je také třeba nakonfigurovat zásady autorizace klíče obsahu, jak je popsáno v [části Použití běžného dynamického šifrování PlayReady](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Požadavky a úvahy

Při používání Mediálních služeb je nutné dodat HLS šifrované pomocí FairPlay a dodat licence FairPlay:

  * Účet Azure. Podrobnosti najdete v [tématu bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Účet Media Services. Pokud si ho nechcete vytvořit, přečtěte [si tématu Vytvoření účtu Mediální chodníčků Azure pomocí portálu Azure](media-services-portal-create-account.md).
  * Zaregistrujte se [pomocí vývojového programu Apple](https://developer.apple.com/).
  * Společnost Apple vyžaduje, aby vlastník obsahu získal [balíček nasazení](https://developer.apple.com/contact/fps/). Uveďte, že jste již implementovali modul zabezpečení klíčů (KSM) s mediálními službami a že požadujete konečný balíček FPS. V konečném balíčku FPS jsou pokyny pro generování certifikace a získání tajného klíče aplikace (ASK). Ke konfiguraci FairPlay používáte ASK.
  * Azure Media Services .NET SDK verze **3.6.0** nebo novější.

Následující věci musí být nastaveny na straně doručování klíčů mediálních služeb:

  * **Certifikát aplikace (AC):** Jedná se o soubor .pfx, který obsahuje soukromý klíč. Tento soubor vytvoříte a zašifrujete heslem.

       Při konfiguraci zásady doručování klíčů je nutné zadat toto heslo a soubor PFX ve formátu Base64.

      Následující kroky popisují, jak generovat soubor certifikátu .pfx pro FairPlay:

    1. Nainstalujte OpenSSL z https://slproweb.com/products/Win32OpenSSL.html.

        Přejděte do složky, kde je certifikát FairPlay a další soubory dodané společností Apple.
    2. V příkazovém řádku spusťte následující příkaz. Tím se převede soubor CER na soubor PEM.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
    3. V příkazovém řádku spusťte následující příkaz. Tím se soubor PEM převede na soubor Pfx se soukromým klíčem. Heslo pro soubor .pfx je pak požádán OpenSSL.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
  * **Heslo certifikátu aplikace**: Heslo pro vytvoření souboru .pfx.
  * **Id hesla certifikátu aplikace**: Musíte nahrát heslo, podobně jako nahrají další klíče Mediálních služeb. K získání ID mediálních služeb použijte výčet výčet **ContentKeyType.FairPlayPfxPassword.** To je to, co je třeba použít uvnitř klíčové možnosti zásad doručování.
  * **iv**: Jedná se o náhodnou hodnotu 16 bajtů. Musí odpovídat iv v zásadách doručování majetku. Vygenerujete iv a umístíte jej na obě místa: zásady doručování majetku a možnost zásady klíčového doručování.
  * **ASK**: Tento klíč je přijat při generování certifikace pomocí portálu Apple Developer. Každý vývojový tým obdrží jedinečný ASK. Uložte kopii ask a uložte ji na bezpečném místě. Ask je třeba nakonfigurovat ASK jako FairPlayAsk na Media Services později.
  * **ASK ID**: Toto ID se získá při nahrávání ASK do media services. Musíte nahrát ASK pomocí **contentkeytype.FairPlayAsk** výčet hodnotu. V důsledku toho je vráceno ID mediálních služeb, a to je to, co by mělo být použito při nastavování možnosti zásad y klíčového doručení.

Na straně klienta FPS musí být nastaveny následující věci:

  * **Certifikát aplikace (AC)**: Jedná se o soubor CER/.der, který obsahuje veřejný klíč, který operační systém používá k šifrování některých datových částek. Media Services potřebuje vědět o tom, protože je vyžadovánpřehrávačem. Služba doručování klíčů ji dešifruje pomocí odpovídajícího soukromého klíče.

Chcete-li přehrát šifrovaný stream FairPlay, získejte nejprve skutečný ASK a poté vygenerujte skutečný certifikát. Tento proces vytváří všechny tři části:

  * Soubor .der
  * Soubor .pfx
  * heslo pro .pfx

Následující klienti podporují HLS s **šifrováním AES-128 CBC:** Safari na OS X, Apple TV, iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Konfigurace dynamických šifrovacích a doručovacích služeb FairPlay
Níže jsou uvedeny obecné kroky pro ochranu vašich datových zdrojů pomocí fairplay pomocí služby doručování licencí Media Services a také pomocí dynamického šifrování.

1. Vytvořte prostředek a nahrajte do něj soubory.
2. Zakódujte prostředek obsahující soubor do sady souborů MP4 s adaptivní přenosovou rychlostí.
3. Vytvořte klíč k obsahu a přiřaďte ho k zakódovanému prostředku.  
4. Nakonfigurujte zásady autorizace pro klíč k obsahu. Určete následující nastavení:

   * Způsob doručení (v tomto případě FairPlay).
   * Konfigurace možností zásad FairPlay. Podrobnosti o konfiguraci FairPlay naleznete v metodě **ConfigureFairPlayPolicyOptions()** v následující ukázce.

     > [!NOTE]
     > Obvykle byste chtěli nakonfigurovat možnosti zásad FairPlay pouze jednou, protože budete mít pouze jednu sadu certifikace a ASK.
     >
     >
   * Omezení (otevřít nebo token).
   * Informace specifické pro typ doručení klíče, který definuje, jak je klíč doručován klientovi.
5. Nakonfigurujte zásady poskytování majetku. Konfigurace zásad doručení zahrnuje:

   * Protokol doručení (HLS).
   * Typ dynamického šifrování (běžné šifrování CBC).
   * Adresa URL získání licence.

     > [!NOTE]
     > Pokud chcete dodat datový proud, který je šifrován pomocí FairPlay a jiného systému správy digitálních práv (DRM), musíte nakonfigurovat samostatné zásady doručování:
     >
     > * Jeden iAssetDeliveryPolicy pro konfiguraci dynamického adaptivního streamování přes HTTP (DASH) s běžným šifrováním (CENC) (PlayReady + Widevine) a Plynulý s PlayReady
     > * Další IAssetDeliveryPolicy pro konfiguraci FairPlay pro HLS
     >
     >
6. Pokud chcete získat adresu URL streamování, vytvořte lokátor OnDemand.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Použití doručení klíče FairPlay podle aplikací pro hráče
Aplikace přehrávače můžete vyvíjet pomocí sady iOS SDK. Abyste mohli přehrávat obsah FairPlay, musíte implementovat protokol výměny licencí. Tento protokol není společností Apple určen. Je na každé aplikaci, jak odeslat žádosti o doručení klíčů. Služba doručování klíčů Media Services FairPlay očekává, že SPC přijde jako postová zpráva s kódem www-form-url v následujícím formuláři:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Azure Media Player podporuje přehrávání FairPlay. Další informace najdete v [dokumentaci k programu Azure Media Player.](https://amp.azure.net/libs/amp/latest/docs/index.html)
>
>

## <a name="streaming-urls"></a>Datové adresy URL pro streamování
Pokud byl váš prostředek zašifrován pomocí více než jednoho DRM, měli byste použít šifrovací značku v adrese URL streamování: (format='m3u8-aapl', encryption='xxx').

Platí následující důležité informace:

* Lze zadat pouze nulový nebo jeden typ šifrování.
* Typ šifrování nemusí být zadán v adrese URL, pokud bylo na prostředek použito pouze jedno šifrování.
* Typ šifrování je malá a velká písmena.
* Lze zadat následující typy šifrování:  
  * **cenc**: Běžné šifrování (PlayReady nebo Widevine)
  * **cbcs-aapl**: FairPlay
  * **cbc**: Šifrování obálky AES

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

1. Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 
2. Do části **appSettings** definované ve vašem souboru app.config přidejte následující elementy:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Příklad

Následující ukázka ukazuje schopnost používat mediální služby k doručování obsahu zašifrovaného pomocí FairPlay. Tato funkce byla zavedena v Azure Media Services SDK pro .NET verze 3.6.0. 

Přepište kód v souboru Program.cs kódem zobrazeným v této části.

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. Další informace naleznete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

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

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="next-steps-media-services-learning-paths"></a>Další kroky: Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
