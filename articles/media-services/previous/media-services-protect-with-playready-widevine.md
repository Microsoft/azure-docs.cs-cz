---
title: Použití běžného dynamického šifrování PlayReady nebo Widevine | Microsoft Docs
description: Službu Azure Media Services můžete použít k doručování datových proudů MPEG-DASH, Smooth Streaming a HTTP Live Streaming (HLS) chráněných technologií Microsoft PlayReady DRM. Umožňuje také doručovat datové proudy DASH šifrované pomocí Widevine DRM. Toto téma ukazuje, jak dynamicky šifrovat pomocí PlayReady DRM nebo Widevine DRM.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 548d1a12-e2cb-45fe-9307-4ec0320567a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: e783dc8848a5aa7db17e3cb4d9019fd7f284e79e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003750"
---
# <a name="use-playready-andor-widevine-dynamic-common-encryption"></a>Použití běžného dynamického šifrování PlayReady nebo Widevine

> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-playready-widevine.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>

> [!NOTE]
> Pokud chcete získat nejnovější verzi sady Java SDK a začít s vývojem v jazyce Java, přečtěte si článek [Začínáme s klientskou sadou Java SDK pro Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Pokud si chcete stáhnout nejnovější sadu PHP SDK pro službu Media Services, najděte si v [úložišti Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7) balíček Microsoft/WindowsAzure verze 0.5.7. 

## <a name="overview"></a>Přehled

 Službu Media Services můžete použít k doručování datových proudů MPEG-DASH, Smooth Streaming a HTTP Live Streaming (HLS) chráněných [technologií správy digitálních práv (DRM) PlayReady](https://www.microsoft.com/playready/overview/). Můžete také doručovat šifrované datové proudy DASH s licencemi Widevine DRM. Technologie PlayReady i Widevine jsou šifrované podle specifikace Common Encryption (ISO/IEC CENC 23001-7). Konfiguraci zásady AssetDeliveryConfiguration na používání technologie Widevine můžete provést pomocí sady [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (počínaje verzí 3.5.1) nebo rozhraní REST API.

Media Services poskytuje službu k doručování licencí PlayReady DRM a Widevine DRM. Služba Media Services také poskytuje rozhraní API, které můžete použít ke konfiguraci práv a omezení, která má modul runtime PlayReady nebo Widevine DRM vynucovat, když uživatel přehrává chráněný obsah. Když si uživatel vyžádá obsah chráněný pomocí DRM, aplikace přehrávače požádá o licenci z licenční služby Media Services. Pokud je aplikace přehrávače oprávněná, licenční služba Media Services vydá přehrávači licenci. Licence PlayReady nebo Widevine obsahuje dešifrovací klíč, kterým může klientský přehrávač dešifrovat a streamovat obsah.

K distribuci licencí Widevine můžete použít i následující partnery Media Services: 

* [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](http://ezdrm.com/) 
* [castLabs](http://castlabs.com/company/partners/azure/) 

Další informace najdete v popisu integrace s [Axinom](media-services-axinom-integration.md) a [castLabs](media-services-castlabs-integration.md).

Služba Media Services podporuje více způsobů autorizace uživatelů, kteří žádají o klíč. Zásady autorizace pro klíč k obsahu mohou obsahovat jedno nebo více omezení autorizace: buď otevřená omezení, nebo omezení s tokenem. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve formátu [jednoduchých webových tokenů](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) a [webových tokenů JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). 

Další informace najdete v tématu [Konfigurace zásad autorizace klíče k obsahu](media-services-protect-with-aes128.md#configure_key_auth_policy).

Abyste mohli využívat dynamické šifrování, je třeba mít prostředek (označovaný také jako asset), který obsahuje sadu souborů MP4 s více přenosovými rychlostmi nebo zdrojové soubory technologie Smooth Streaming s více přenosovými rychlostmi. U assetu je také nutné nakonfigurovat zásady pro doručování (popsáno dále v tomto tématu). Potom, na základě formátu určeného v adrese URL streamování, server streamování na vyžádání zajistí, aby byl datový proud doručen v protokolu podle vašeho výběru. Výsledkem je, že ukládáte a platíte za soubory jen v jednom úložném formátu. Služba Media Services sestaví a odešle odpovídající odpověď HTTP na základě jednotlivých žádosti od klientů.

Tento článek je užitečný pro vývojáře pracující na aplikacích, které doručují média s několikanásobnou ochranou DRM, například PlayReady a Widevine. Tento článek ukazuje, jak nakonfigurovat službu doručování licencí PlayReady pomocí zásad autorizace tak, aby licence PlayReady nebo Widevine mohli dostávat pouze autorizovaní klienti. Také ukazuje, jak používat dynamické šifrování s licencemi DRM PlayReady nebo Widevine přes streamování DASH.

>[!NOTE]
>Po vytvoření účtu Media Services se do vašeho účtu přidá výchozí koncový bod streamování ve stavu Zastaveno. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu Spuštěno. 

## <a name="download-the-sample"></a>Stažení ukázky
Ukázku popsanou v tomto článku si můžete stáhnout v [ukázkách Azure na GitHubu](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

## <a name="configure-dynamic-common-encryption-and-drm-license-delivery-services"></a>Konfigurace běžného dynamického šifrování a služeb doručování licencí DRM

Následující základní kroky proveďte, když chcete své prostředky chránit pomocí technologie PlayReady se službou doručování licencí Media Services a také použitím dynamického šifrování:

1. Vytvořte prostředek a nahrajte do něj soubory.

2. Zakódujte prostředek obsahující soubor do sady souborů MP4 s adaptivní přenosovou rychlostí.

3. Vytvořte klíč k obsahu a přiřaďte ho k zakódovanému prostředku. Ve službě Media Services obsahuje klíč obsahu šifrovací klíč prostředku.

4. Nakonfigurujte zásady autorizace klíče obsahu. Je třeba nakonfigurovat zásady autorizace klíče obsahu. Klient musí zásady splňovat, jinak mu nebude klíč obsahu poskytnut.

    Když vytvoříte zásadu autorizace klíče obsahu, je nutné zadat metodu doručení (PlayReady nebo Widevine) a omezení (otevřené nebo s tokenem). Musíte zadat také informace specifické pro daný typ doručení klíče, které definují, jak je klíč doručen klientovi (šablona licence [PlayReady](media-services-playready-license-template-overview.md) nebo [Widevine](media-services-widevine-license-template-overview.md)).

5. Konfigurace zásad doručení pro asset. Konfigurace zásad doručení zahrnuje doručovací protokol (například MPEG-DASH, HLS, Smooth Streaming nebo všechny). Konfigurace zahrnuje také typ dynamického šifrování (například běžné šifrování) a adresu URL pro získání licence PlayReady nebo Widevine.

    Na každý protokol stejného prostředku můžete použít jiné zásady. Můžete například použít šifrování PlayReady na protokol Smooth/DASH a šifrování pomocí standardu AES Envelope na protokol HLS. Všechny protokoly, které nejsou v zásadách doručení definovány (například když přidáte jedinou zásadu, která jako protokol určuje pouze HLS), budou při streamování blokovány. Výjimkou je, pokud nemáte definovány vůbec žádné zásady doručení prostředku. Pak budou všechny protokoly povolené v nešifrované podobě.

6. Pokud chcete získat adresu URL streamování, vytvořte lokátor OnDemand.

Kompletní příklad v .NET najdete na konci článku.

Následující obrázek znázorňuje výše popsaný pracovní postup. Zde se k ověření používá token.

![Ochrana technologií PlayReady](media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

Zbývající část tohoto článku poskytuje podrobná vysvětlení, příklady kódu a odkazy na témata, která ukazují, jak provést výše popsané úlohy.

## <a name="current-limitations"></a>Aktuální omezení
Pokud přidáte nebo aktualizujete zásady pro doručení prostředku, musíte odstranit přidružený lokátor a vytvořit nový.

V současné době se při šifrování pomocí Widevine ve službě Media Services nepodporuje více klíčů obsahu. 

## <a name="create-an-asset-and-upload-files-into-the-asset"></a>Vytvoření assetu a nahrání souborů do assetu
Aby bylo možné spravovat, kódovat a streamovat videa, musíte nejprve nahrát obsah do služby Media Services. Po nahrání bude váš obsah bezpečně uložen v cloudu pro další zpracování a streamování.

Podrobné informace najdete v článku o [nahrání souborů do účtu služby Media Services](media-services-dotnet-upload-files.md).

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a>Kódování prostředku obsahujícího soubor do sady souborů MP4 s adaptivní přenosovou rychlostí
V případě dynamického šifrování je třeba pouze vytvořit prostředek, který obsahuje sadu souborů MP4 s více přenosovými rychlostmi nebo zdrojové soubory technologie Smooth Streaming s více přenosovými rychlostmi. Potom server streamování na vyžádání zajistí na základě formátu určeného v manifestu a požadavku na fragment, abyste datový proud obdrželi v protokolu podle vašeho výběru. Díky tomu ukládáte a platíte za soubory jen v jednom úložném formátu. Služba Media Services sestaví a odešle odpovídající odpověď na základě žádosti od klientů. Další informace najdete v tématu [Přehled dynamického balení](media-services-dynamic-packaging-overview.md).

Pokyny ke kódování najdete v článku o [kódování prostředku pomocí kodéru Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Vytvoření klíče k obsahu a jeho přiřazení k zakódovanému assetu
Klíč k obsahu ve službě Media Services obsahuje klíč, kterým chcete asset šifrovat.

Podrobné informace najdete v tématu [Vytvoření klíče k obsahu](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Konfigurace zásad autorizace klíče obsahu
Služba Media Services podporuje více způsobů ověřování uživatelů, kteří žádají o klíč. Je třeba nakonfigurovat zásady autorizace klíče obsahu. Klient (přehrávač) musí zásady splňovat, jinak mu nebude klíč obsahu poskytnut. Zásady autorizace pro klíč k obsahu mohou obsahovat jedno nebo více omezení autorizace: buď otevřená omezení, nebo omezení s tokenem.

Další informace najdete v tématu [Konfigurace zásad autorizace klíče k obsahu](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

## <a id="configure_asset_delivery_policy"></a>Konfigurace zásad doručení prostředku
Nakonfigurujte zásady doručení pro asset. Konfigurace zásad doručení prostředku zahrnuje následující položky:

* Adresa URL pro získání licence DRM.
* Doručovací protokol prostředku (například MPEG DASH, HLS, technologie Smooth Streaming nebo všechny).
* Typ dynamického šifrování (v tomto případě běžné šifrování).

Podrobné informace najdete v tématu [Konfigurace zásad doručení prostředku](media-services-dotnet-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Vytvoření lokátoru streamování OnDemand pro získání adresy URL streamování
Uživateli je třeba poskytnout adresu URL streamování pro protokol Smooth Streaming, DASH nebo HLS.

> [!NOTE]
> Pokud přidáte nebo aktualizujete zásady pro doručení prostředku, musíte odstranit stávající lokátor a vytvořit nový.
>
>

Pokyny k publikování assetu a vytvoření adresy URL streamování najdete v článku o [vytvoření adresy URL streamování](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Získání testovacího tokenu
Získejte testovací token na základě omezení s tokenem, které se používá v zásadách autorizace klíče.

```csharp
// Deserializes a string containing an XML representation of a TokenRestrictionTemplate
// back into a TokenRestrictionTemplate class instance.
TokenRestrictionTemplate tokenTemplate =
TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

// Generate a test token based on the data in the given TokenRestrictionTemplate.
//The GenerateTestToken method returns the token without the word "Bearer" in front,
//so you have to add it in front of the token string.
string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

K testování datového proudu můžete použít [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

1. Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md).

2. Do části **appSettings** definované ve vašem souboru app.config přidejte následující elementy:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Příklad

Následující příklad ukazuje funkci, která byla zavedena v sadě Media Services SDK pro .NET ve verzi 3.5.2. (Konkrétně obsahuje možnost definovat šablonu licence Widevine a žádat o licenci Widevine ze služby Media Services.)

Přepište kód v souboru Program.cs kódem zobrazeným v této části.

>[!NOTE]
>Je stanovený limit 1 miliónu zásad pro různé zásady Media Services (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždycky používáte přístupová oprávnění se stejným časovým omezením, měli byste používat stejné ID zásady. Příkladem je zásada pro lokátory, které mají zůstat v platnosti delší dobu (zásady bez odesílání). 

Další informace najdete v tématu [Správa prostředků a přidružených entit v sadě Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies).

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
using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
using Newtonsoft.Json;

namespace DynamicEncryptionWithDRM
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

            IContentKey key = CreateCommonTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
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
                // Deserializes a string containing an XML representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note that you need to pass the key ID GUID because 
                // TokenClaim.ContentKeyIdentifierClaim was specified during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
            // Note that DASH works on Internet Explorer 11 (via PlayReady), Microsoft Edge (via PlayReady), and Chrome (via Widevine).

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);

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

            IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                        inputAsset.Name,
                        encodingPreset));

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


        static public IContentKey CreateCommonTypeContentKey(IAsset asset)
        {

            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryption);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }

        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {

            // Create ContentKeyAuthorizationPolicy with open restrictions
            // and create an authorization policy.         

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                {
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Open",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                    Requirements = null
                }
                };

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.Widevine,
                restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common Content Key with no restrictions").
                Result;


            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
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

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.Widevine,
                restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);

            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
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

        static private string ConfigurePlayReadyLicenseTemplate()
        {
            // The following code configures the PlayReady license template by using .NET classes
            // and returns the XML string.

            //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user.
            //It contains a field for a custom data string between the license server and the application
            //(which might be useful for custom app logic) as well as a list of one or more license templates.
            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

            // The PlayReadyLicenseTemplate class represents a license template you can use to create PlayReady licenses
            // to be returned to end users.
            //It contains the data on the content key in the license and any rights or restrictions to be
            //enforced by the PlayReady DRM runtime when you use the content key.
            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
            //Configure whether the license is persistent (saved in persistent storage on the client)
            //or nonpersistent (held in memory only while the player uses the license).  
            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

            // AllowTestDevices controls whether test devices can use the license or not.  
            // If true, the MinimumSecurityLevel property of the license
            // is set to 150. If false (the default), the MinimumSecurityLevel property of the license is set to 2,000.
            licenseTemplate.AllowTestDevices = true;

            // You also can configure the PlayRight in the PlayReady license by using the PlayReadyPlayRight class.
            // It grants the user the ability to play back the content subject to the zero or more restrictions
            // configured in the license and on the PlayRight itself (for playback-specific policy).
            // Much of the policy on the PlayRight has to do with output restrictions,
            // which control the types of outputs that the content can be played over and
            // any restrictions that must be put in place when you use a given output.
            // For example, if DigitalVideoOnlyContentRestriction is enabled,
            // the DRM runtime allows the video to be displayed only over digital outputs
            //(analog video outputs aren't allowed to pass the content).

            //IMPORTANT: These types of restrictions can be very powerful but also can affect the consumer experience.
            // If output protections are too restrictive, 
            // content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

            // For example:
            //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

            responseTemplate.LicenseTemplates.Add(licenseTemplate);

            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
        }

        private static string ConfigureWidevineLicenseTemplate()
        {
            var template = new WidevineMessage
            {
                allowed_track_types = AllowedTrackTypes.SD_HD,
                content_key_specs = new[]
            {
                    new ContentKeySpecs
                    {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                    }
                },
                policy_overrides = new
                {
                    can_play = true,
                    can_persist = true,
                    can_renew = false
                }
            };

            string configuration = JsonConvert.SerializeObject(template);
            return configuration;
        }

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            // Get the PlayReady license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

            // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
            // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
            // WidevineBaseLicenseAcquisitionUrl (used in the following) also tells dynamic encryption
            // to append /? KID =< keyId > to the end of the URL when you create the manifest.
            // As a result, the Widevine license acquisition URL has the KID appended twice,
            // so you need to remove the KID in the URL when you call GetKeyDeliveryUrl.

            Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
            UriBuilder uriBuilder = new UriBuilder(widevineUrl);
            uriBuilder.Query = String.Empty;
            widevineUrl = uriBuilder.Uri;

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}

            };

            // In this case, we specify only the DASH streaming protocol in the delivery policy.
            // All other protocols are blocked from streaming.
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


            // Add AssetDelivery Policy to the asset.
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

            // Create a 30-day read-only access policy.
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

## <a name="next-steps"></a>Další kroky

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
* [Použití šifrování CENC s více technologiemi DRM a řízením přístupu](media-services-cenc-with-multidrm-access-control.md)
* [Konfigurace balení Widevine ve službě Media Services](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)
* [Uvedení služeb doručování licence Google Widevine ve službě Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
