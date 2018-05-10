---
title: Chránit obsah pomocí Microsoft PlayReady nebo Apple FairPlay - Azure HLS | Microsoft Docs
description: Toto téma poskytuje přehled a ukazuje, jak pomocí Azure Media Services dynamicky šifrovat obsah HTTP Live Streaming (HLS) s Apple FairPlay. Také ukazuje, jak používat službu doručování licencí Media Services k doručování licence FairPlay klientům.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: fc3504f71af2f5e8f989a1d015ff78dda2c85d66
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Chránit váš obsah s Apple FairPlay nebo Microsoft PlayReady HLS
Azure Media Services umožňuje dynamicky šifrovat obsah HTTP Live Streaming (HLS) pomocí následujících formátů:  

* **Nezašifrovaný klíč AES-128 obálky**

    Celý blok se šifrují pomocí **AES-128 CBC** režimu. IOS a OS X player je nativně podporované dešifrování datového proudu. Další informace najdete v tématu [dynamického šifrování pomocí standardu AES-128 a doručení klíče služby](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    Jednotlivé vzorky videa a zvuku jsou šifrována pomocí **AES-128 CBC** režimu. **Streamování FairPlay** (FPS) je integrována do operačních systémů zařízení, s nativní podporou na iOS a Apple TV. Prohlížeč Safari na OS X umožňuje FPS s využitím podpory rozhraní šifrované rozšíření média (EME).
* **Microsoft PlayReady**

Na následujícím obrázku **HLS + FairPlay nebo PlayReady dynamického šifrování** pracovního postupu.

![Diagram pracovního postupu dynamického šifrování](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Tento článek ukazuje, jak dynamicky šifrovat obsah HLS s Apple FairPlay pomocí služby Media Services. Také ukazuje, jak používat službu doručování licencí Media Services k doručování licence FairPlay klientům.

> [!NOTE]
> Pokud chcete zašifrovat obsah HLS pomocí technologie PlayReady, budete muset vytvořit běžné klíč obsahu a přidružte ji k asset. Budete také muset nakonfigurovat zásady autorizace pro klíč k obsahu, jak je popsáno v [běžného dynamického šifrování PlayReady pomocí](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Požadavky a důležité informace

Toto jsou požadovány při použití služby Media Services k poskytování HLS se šifrováním pomocí FairPlay a k poskytování licence FairPlay:

  * Účet Azure. Podrobnosti najdete na stránce [bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Účet Media Services. Chcete-li vytvořit, přečtěte si téma [vytvoření účtu Azure Media Services pomocí webu Azure portal](media-services-portal-create-account.md).
  * Zaregistrovat [vývoj programem](https://developer.apple.com/).
  * Apple vyžaduje vlastníka obsahu k získání [balíček pro nasazení](https://developer.apple.com/contact/fps/). Stav už implementované klíč zabezpečení modulu (KSM) pomocí služby Media Services a že žádáte poslední balíček FPS. Pokyny v posledním FPS balíčku pro generování certifikační a získat klíč tajný klíč aplikace (požádejte) jsou. Požádejte použijete ke konfiguraci FairPlay.
  * Azure Media Services .NET SDK verze **3.6.0** nebo novější.

Na straně doručení klíče služby Media Services musí být nastavena následujících akcí:

  * **Aplikace Cert (AC)**: Toto je soubor .pfx, který obsahuje soukromý klíč. Tento soubor vytvořte a šifrování pomocí hesla.

       Když konfigurujete zásady doručení klíče, je nutné zadat toto heslo a soubor .pfx, ve formátu Base64.

      Následující kroky popisují, jak generovat soubor .pfx certifikátu pro FairPlay:

    1. Nainstalujte OpenSSL z https://slproweb.com/products/Win32OpenSSL.html.

        Přejděte do složky, kde jsou FairPlay certifikátu a další soubory dodané společností Apple.
    2. V příkazovém řádku spusťte následující příkaz. Tento soubor .cer převede na soubor .pem.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
    3. V příkazovém řádku spusťte následující příkaz. Tento soubor .pem převede do souboru .pfx s privátním klíčem. Heslo pro soubor .pfx pak požaduje od OpenSSL.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12-export - out FairPlay out.pfx-inkey privatekey.pem-v - passin file:privatekey-pem-pass.txt FairPlay out.pem
  * **Heslo aplikace Cert**: heslo pro vytvoření souboru .pfx.
  * **ID aplikace Cert heslo**: je potřeba načíst heslo, podobně jako jak odesílají jiných klíčů Media Services. Použití **ContentKeyType.FairPlayPfxPassword** hodnota výčtu získat ID služby média. Toto je požadované použít uvnitř možnost zásady doručení klíče.
  * **IV**: je to hodnota náhodných 16 bajtů. Musí se shodovat iv do zásady doručení assetu. Generovat iv a umístí jej na obou místech: zásady doručení assetu a možnost zásady doručení klíče.
  * **Požádejte**: Tento klíč je oznámených při generování certifikační pomocí portálu pro vývojáře Apple. Každý vývojový tým obdrží jedinečný požádejte. Uložte kopii požádejte a uložit na bezpečném místě. Budete muset nakonfigurovat požádejte jako FairPlayAsk ke službě Media Services později.
  * **Požádejte ID**: Toto ID je získali při nahrávání požádejte ve službě Media Services. Požádejte musíte nahrát pomocí **ContentKeyType.FairPlayAsk** hodnota výčtu. V důsledku toho se vrátí Media Services ID a je to, co by měl být použit při nastavení možnosti zásad doručení klíče.

Na straně klienta FPS musí nastavit následujících akcí:

  * **Aplikace Cert (AC)**: Toto je.cer/.der soubor, který obsahuje veřejný klíč, který operační systém používá k šifrování některé datové části. Služba Media Services je potřeba upozornit, protože je požadována přehrávače. Službu doručení klíče dešifruje pomocí příslušného privátního klíče.

Přehrát FairPlay šifrovaného datového proudu, převeďte skutečné požádejte první a pak vygenerovat certifikát skutečné. Tento proces vytvoří všech tří částí:

  * souboru .der
  * soubor .pfx
  * heslo .pfx

Následující klienti podporují HLS s **AES-128 CBC** šifrování: Safari v systému OS X, Apple TV, iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Konfigurace FairPlay dynamické šifrování a licencí služeb doručování
Následují obecné kroky pro své assety chráníte pomocí FairPlay pomocí službu doručování licencí Media Services a také používáte dynamické šifrování.

1. Vytvořte prostředek a nahrajte do něj soubory.
2. Zakódujte prostředek obsahující soubor do sady souborů MP4 s adaptivní přenosovou rychlostí.
3. Vytvořte klíč k obsahu a přiřaďte ho k zakódovanému prostředku.  
4. Nakonfigurujte zásady autorizace pro klíč k obsahu. Zadejte následující informace:

   * Metodu doručení (v tomto případě FairPlay).
   * Konfigurace možností FairPlay zásad. Podrobnosti o tom, jak nakonfigurovat FairPlay najdete v tématu **ConfigureFairPlayPolicyOptions()** metoda v následující ukázce.

     > [!NOTE]
     > Obvykle byste chcete nakonfigurovat možnosti zásad FairPlay jenom jednou, protože budou mít pouze jednu sadu certifikace a ASK.
     >
     >
   * Omezení (otevřené nebo s tokenem).
   * Informace specifické pro daný typ doručení klíče, který definuje, jak je klíč doručen do klienta.
5. Konfigurace zásad doručení assetu. Konfigurace zásad doručení zahrnuje:

   * Doručovací protokol (HLS).
   * Typ dynamického šifrování (běžné CBC šifrování).
   * Získávání adresu URL licence.

     > [!NOTE]
     > Pokud chcete poskytovat datový proud, který je šifrován FairPlay a jinému systému pro správu digitálních práv (DRM), budete muset nakonfigurovat zásady samostatné doručení:
     >
     > * Jeden IAssetDeliveryPolicy konfigurace dynamické adaptivní datové proudy přes protokol HTTP (pomlčka) s běžné šifrování (CENC) (PlayReady a Widevine) a protokol Smooth s technologií PlayReady
     > * Jiné IAssetDeliveryPolicy konfigurace FairPlay pro HLS
     >
     >
6. Pokud chcete získat adresu URL streamování, vytvořte lokátor OnDemand.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Použít doručení klíče FairPlay player aplikace
Přehrávač aplikace můžete vyvíjet pomocí iOS SDK. Abyste mohli k přehrávání obsahu FairPlay, budete muset implementovat protokol exchange licence. Tato položka protocol není zadán společností Apple. Je na každou aplikaci, jak odesílat žádosti o doručení klíče. Služba Media Services FairPlay doručení klíče očekává SPC pocházet jako zprávu kódovaného post www-form-url v následující podobě:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Azure Media Player podporuje FairPlay přehrávání. V tématu [dokumentace Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) Další informace.
>
>

## <a name="streaming-urls"></a>Adresy URL pro streamování
Pokud váš asset byla zašifrována pomocí více než jeden DRM, ve adresu URL streamování byste měli používat značky šifrování: (formát = 'm3u8-aapl' šifrování = 'xxx').

Platí následující aspekty:

* Lze zadat pouze nula nebo jeden typ šifrování.
* Typ šifrování nemusí být zadaného v adrese URL, pokud jenom jeden šifrování byla použita pro daný prostředek.
* Typ šifrování je malá a velká písmena.
* Určit lze následující typy šifrování:  
  * **šifrování cenc**: Common encryption (PlayReady nebo Widevine)
  * **cbcs-aapl**: FairPlay
  * **CBC**: šifrování AES obálky

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

1. Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 
2. Do části **appSettings** definované ve vašem souboru app.config přidejte následující elementy:

    ```xml
            <add key="Issuer" value="http://testacs.com"/>
            <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Příklad:

Následující příklad ukazuje možnost používat pro doručování obsahu šifrován FairPlay Media Services. Tato funkce byla zavedená v Azure Media Services SDK pro .NET verze 3.6.0. 

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

                // Generate a test token based on the the data in the given TokenRestrictionTemplate.
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

            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
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

## <a name="next-steps-media-services-learning-paths"></a>Další kroky: Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
