---
title: Ochrana HLS obsahu s Microsoft PlayReady nebo Apple FairPlay – Azure | Dokumentace Microsoftu
description: Toto téma poskytuje přehled a ukazuje, jak pomocí Azure Media Services dynamicky šifrovat technologií Apple FairPlay obsah HTTP Live Streaming (HLS). Také ukazuje, jak používat službu doručování licencí Media Services k doručování licencí FairPlay pro klienty.
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
ms.date: 09/18/2018
ms.author: juliako
ms.openlocfilehash: 32f3f4fd3f4f299c9b084ab8604b56ea70e639a4
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368219"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Chránit obsah pomocí Apple FairPlay nebo Microsoft PlayReady HLS
Azure Media Services umožňuje dynamicky šifrovat obsah HTTP Live Streaming (HLS) pomocí následujících formátů:  

* **Nezašifrovaný klíč AES-128 obálky**

    Celý blok dat je šifrován pomocí **AES-128 CBC** režimu. Zařízení s iOS a OS X player je nativně podporovány dešifrování datového proudu. Další informace najdete v tématu [dynamického šifrování pomocí AES-128 a služba doručování klíčů](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    Jednotlivé vzorky video a audiostreamů jsou šifrované pomocí **AES-128 CBC** režimu. **FairPlay Streaming** (FPS) je integrované do operačních systémů zařízení s nativní podporou v Iosu a Apple TV. Safari v systému OS X umožňuje snímků za Sekundu s využitím podpory rozhraní rozšíření eme (Encrypted Media Extensions).
* **Microsoft PlayReady**

Na následujícím obrázku **HLS + FairPlay nebo technologií PlayReady dynamického šifrování** pracovního postupu.

![Diagram pracovního postupu dynamického šifrování](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Tento článek ukazuje, jak dynamicky šifrovat obsahu HLS technologií Apple FairPlay pomocí Media Services. Také ukazuje, jak používat službu doručování licencí Media Services k doručování licencí FairPlay pro klienty.

> [!NOTE]
> Pokud chcete šifrovat pomocí PlayReady obsahu HLS, musíte vytvořit společné klíče k obsahu a jeho přiřazení k vaší assetu. Musíte také nakonfigurovat zásady autorizace klíče k obsahu, jak je popsáno v [běžného dynamického šifrování pomocí PlayReady](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Požadavky a předpoklady

Následující jsou potřeba při použití služby Media Services k zajištění HLS se šifrováním pomocí FairPlay a k poskytování licencí FairPlay:

  * Účet Azure. Podrobnosti najdete na stránce [bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Účet Media Services. Pokud chcete jeden vytvořit, přečtěte si téma [vytvoření účtu služby Azure Media Services pomocí webu Azure portal](media-services-portal-create-account.md).
  * Zaregistrovat s [programu pro vývoj Apple](https://developer.apple.com/).
  * Apple vyžaduje k získání vlastníka obsahu [balíček pro nasazení](https://developer.apple.com/contact/fps/). Stav již implementováno klíč zabezpečení modulu (KSM) pomocí služby Media Services, a že žádáte finálním balíčku snímků za Sekundu. Existují pokyny ve finálním balíčku snímků za Sekundu pro generování certifikace a získat klíč tajný klíč aplikace (AKS). Konfigurace FairPlay použijete dotaz.
  * Azure Media Services .NET SDK verze **3.6.0** nebo novější.

Na straně doručení klíče služby Media Services musí být nastaveny následující věci:

  * **Certifikát aplikace (AC)**: Toto je soubor .pfx, který obsahuje privátní klíč. Tento soubor vytvořte a šifrovat pomocí hesla.

       Když konfigurujete zásady doručení klíče, je nutné zadat toto heslo a soubor .pfx ve formátu Base64.

      Následující kroky popisují, jak generovat soubor certifikátu .pfx pro FairPlay:

    1. Nainstalujte OpenSSL z https://slproweb.com/products/Win32OpenSSL.html.

        Přejděte do složky, kde jsou FairPlay certifikát a dalších souborů od společnosti Apple.
    2. V příkazovém řádku spusťte následující příkaz. Tento soubor .cer převede na soubor .pem.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
    3. V příkazovém řádku spusťte následující příkaz. Soubor .pem tím převedete do souboru .pfx s privátním klíčem. Heslo pro soubor .pfx se zobrazí výzva, pomocí OpenSSL.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12-export - out FairPlay out.pfx-inkey privatekey.pem – v file:privatekey-pem-pass.txt - passin FairPlay out.pem
  * **Heslo certifikátu aplikace**: heslo pro vytvoření souboru .pfx.
  * **ID heslo certifikátu aplikace**: musíte nahrát heslo, podobně jako jak odesílají další klíče služby Media Services. Použití **ContentKeyType.FairPlayPfxPassword** hodnotu výčtu, chcete-li získat ID Media Services Toto je to, co chtějí používat uvnitř možnost doručení klíče zásad.
  * **vektor IV**: Jedná se o náhodné hodnotu 16 bajtů. Musí se shodovat iv v zásady doručení assetu. Generovat vektor iv a vložit ho do obou místech: zásady doručení assetu a možnost doručení klíče zásad.
  * **Požádejte**: Tento klíč je dostanete při generování certifikace prostřednictvím portálu pro vývojáře Apple. Jednotlivé vývojové týmy obdrží jedinečný položit dotaz. Uložit kopii zadání a uložte ho na bezpečném místě. Musíte nakonfigurovat položit dotaz jako FairPlayAsk ke službě Media Services později.
  * **Požádejte ID**: Toto ID získáte při odeslat dotaz do Media Services. ZEPTEJTE se musí nahrát pomocí **ContentKeyType.FairPlayAsk** hodnotu výčtu. V důsledku toho se vrátí Media Services ID a je to, co má být použit při nastavení možnosti doručení klíče zásad.

Snímků za Sekundu na straně klienta je nutné nastavit následující věci:

  * **Certifikát aplikace (AC)**: Toto je.cer/.der soubor, který obsahuje veřejný klíč, který operační systém používá k šifrování některé datové části. Služba Media Services je potřeba vědět, protože je vyžadováno přehrávač. Služba doručování klíčů dešifruje ji pomocí odpovídajícího soukromého klíče.

Pro přehrávání šifrovaného datového proudu FairPlay, získat první skutečné položit dotaz a pak vygenerovat skutečný certifikát. Tento proces vytvoří všechny tři části:

  * souboru .der
  * soubor .pfx
  * heslo pro soubor .pfx

Následující klienti podporují HLS s **AES-128 CBC** šifrování: Safari v iOS OS X, Apple TV.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Konfigurace FairPlay dynamického šifrování a licence služeb doručování
Následují obecné kroky pro své assety chráníte pomocí FairPlay s použitím službu doručování licencí Media Services a také použitím dynamického šifrování.

1. Vytvořte prostředek a nahrajte do něj soubory.
2. Zakódujte prostředek obsahující soubor do sady souborů MP4 s adaptivní přenosovou rychlostí.
3. Vytvořte klíč k obsahu a přiřaďte ho k zakódovanému prostředku.  
4. Nakonfigurujte zásady autorizace pro klíč k obsahu. Zadejte následující informace:

   * Metodu doručení (v tomto případě FairPlay).
   * Konfigurace možností FairPlay zásad. Podrobnosti o tom, jak konfigurace FairPlay, najdete v článku **ConfigureFairPlayPolicyOptions()** metoda v následující ukázce.

     > [!NOTE]
     > Obvykle byste to nakonfigurovat možnosti zásad FairPlay pouze jednou, protože budete používat jen jednu sadu certifikace a nazvanou ZEPTEJTE se.
     >
     >
   * Omezení (otevřené nebo s tokenem).
   * Informace specifické pro daný typ doručení klíče, který definuje, jak je klíč doručen klientovi.
5. Konfigurace zásad doručení assetu. Konfigurace zásad doručení zahrnuje:

   * Doručovací protokol (HLS).
   * Typ dynamického šifrování (běžné CBC šifrování).
   * URL pro získání licence.

     > [!NOTE]
     > Pokud chcete dodávat datový proud, který je zašifrovaný pomocí FairPlay a jiný systém správy digitálních práv (DRM), budete muset nakonfigurovat zásady samostatné doručování:
     >
     > * Jeden IAssetDeliveryPolicy pro konfiguraci dynamické adaptivní streamování přes HTTP (DASH) s Common Encryption (CENC) (PlayReady a Widevine) a protokol Smooth pomocí technologie PlayReady
     > * Jiné IAssetDeliveryPolicy konfigurace FairPlay pro HLS
     >
     >
6. Pokud chcete získat adresu URL streamování, vytvořte lokátor OnDemand.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Pomocí aplikace přehrávače doručení klíče FairPlay
Pomocí sady SDK pro iOS můžete vyvíjet aplikace přehrávače. Aby bylo možné k přehrávání obsahu s FairPlay, budete muset implementovat protokol exchange licence. Tento protokol není zadán společností Apple. Je až po každé aplikaci, jak odesílat žádosti o doručení klíče. Doručení klíče služby Media Services FairPlay očekává, že certifikát SPC pocházet jako zpráva www-form-url kódovaného příspěvku. v následujícím tvaru:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Azure Media Player podporuje FairPlay přehrávání. Zobrazit [dokumentace ke službě Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) pro další informace.
>
>

## <a name="streaming-urls"></a>Adresy URL pro streamování
Pokud váš asset byla zašifrována s více DRM, měli byste použít značku šifrování v adrese URL streamování: (format = "m3u8-aapl" šifrování = "xxx").

Platí následující aspekty:

* Je možné zadat pouze žádný nebo jeden typ šifrování.
* Typ šifrování nemusí být zadané v adrese URL, pokud pouze jeden šifrování byla použita k assetu.
* Typ šifrování se nerozlišují malá a velká písmena.
* Můžete zadat následující typy šifrování:  
  * **šifrování cenc**: Standard Common encryption (PlayReady nebo Widevine)
  * **cbcs-aapl**: FairPlay
  * **CBC**: šifrování standardu AES envelope

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

1. Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md). 
2. Do části **appSettings** definované ve vašem souboru app.config přidejte následující elementy:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Příklad:

Následující příklad ukazuje schopnost pomocí Media Services můžete doručovat obsah zašifrovaný pomocí FairPlay. Tato funkce byla zavedena v Azure Media Services SDK pro .NET verze 3.6.0. 

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
