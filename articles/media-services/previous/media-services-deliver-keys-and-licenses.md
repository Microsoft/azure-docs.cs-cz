---
title: Doručování licencí DRM nebo AES klíčů pomocí Azure Media Services | Dokumentace Microsoftu
description: Tento článek popisuje, jak Azure Media Services můžete použít k doručování licencí PlayReady nebo Widevine a plán klíče AES ale udělá zbytek (kódování, šifrování a streamování) pomocí místních serverů.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 8546c2c1-430b-4254-a88d-4436a83f9192
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: juliako
ms.openlocfilehash: 300673ffd62324de1207a29551694f12dd84ea93
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366365"
---
# <a name="use-azure-media-services-to-deliver-drm-licenses-or-aes-keys"></a>Používání Azure Media Services k doručování licencí DRM nebo AES klíče

Azure Media Services umožňuje ingestovat, zakódovat, přidat ochranu obsahu a streamování vašeho obsahu. Další informace najdete v tématu [běžného dynamického šifrování pomocí PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md). Zákazníci, kteří mají používat Media Services jenom k doručování licencí a klíčů a kódování, šifrování a streamování pomocí místních serverů. Tento článek popisuje, jak můžete pomocí Media Services k doručování licencí PlayReady nebo Widevine, ale udělá zbytek s místními servery. 

## <a name="overview"></a>Přehled
Služba Media Services poskytuje službu k doručování licencí (DRM Správa) DRM PlayReady a Widevine a klíče AES-128. Služba Media Services také poskytuje rozhraní API umožňující nakonfigurovat práva a omezení, které chcete pro modul runtime DRM vynucovat, když uživatel přehrává obsah chráněný pomocí DRM. Když si uživatel vyžádá chráněný obsah, aplikace přehrávače požádá o licenci z licenční služby Media Services. Pokud licence je ověřen, licenční služby Media Services problémy se službou přehrávači licenci. Licence PlayReady a Widevine obsahuje dešifrovací klíč, který lze použít klientský přehrávač dešifrovat a Streamovat obsah.

Služba Media Services podporuje více způsobů autorizace uživatelů, kteří žádají licence nebo o klíč. Můžete nakonfigurovat zásady autorizace klíče obsahu. Zásada může mít jeden nebo více omezení. Možnosti jsou otevřené nebo s tokenem omezení. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve formátu jednoduchý webový token (SWT) a ve formátu JSON Web Token (JWT).

Následující diagram znázorňuje hlavní kroky, že které potřebujete provádět použití Media Services k doručování licencí PlayReady nebo Widevine, ale udělá zbytek s místními servery:

![Ochrana technologií PlayReady](./media/media-services-deliver-keys-and-licenses/media-services-diagram1.png)

## <a name="download-sample"></a>Stažení ukázky
Stáhněte si ukázku popsanou v tomto článku, najdete v článku [použití Azure Media Services k doručování licencí PlayReady nebo Widevine s využitím .NET](https://github.com/Azure/media-services-dotnet-deliver-drm-licenses).

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

1. Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md).

2. Do části **appSettings** definované ve vašem souboru app.config přidejte následující elementy:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```
 
## <a name="net-code-example"></a>Příklad kódu .NET
Následující příklad kódu ukazuje, jak vytvořit společné klíče k obsahu a získání URL získání licence PlayReady nebo Widevine. Pokud chcete nakonfigurovat na místním serveru, potřebujete klíč k obsahu, klíč ID a adresu URL pro získání licence. Když nakonfigurujete místní server, můžete Streamovat z datových proudů serveru. Protože body šifrovaného datového proudu ke službě Media Services licenční server, přehrávače požádá o licenci ze služby Media Services. Pokud zvolíte ověřování pomocí tokenu, Media Services licenční server ověří token, který jste odeslali pomocí protokolu HTTPS. Pokud je token platný, licenční server poskytuje licenci zpět do svého hráče. Následující příklad kódu ukazuje jenom postup vytvoření společné klíče k obsahu a získání URL získání licence PlayReady nebo Widevine. Pokud chcete dodávat klíče AES-128, musíte vytvořit klíč obálky obsahu a získání klíče získání adresy URL. Další informace najdete v tématu [dynamického šifrování pomocí AES-128 a služba doručování klíčů](media-services-protect-with-aes128.md).

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
using Newtonsoft.Json;

namespace DeliverDRMLicenses
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

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = true;
            string tokenTemplateString = null;


            IContentKey key = CreateCommonTypeContentKey();

            // Print out the key ID and Key in base64 string format
            Console.WriteLine("Created key {0} with key value {1} ",
                key.Id, System.Convert.ToBase64String(key.GetClearKeyValue()));

            Console.WriteLine("PlayReady License Key delivery URL: {0}",
                key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));

            Console.WriteLine("Widevine License Key delivery URL: {0}",
                key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine));

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}",
                key.AuthorizationPolicyId);
            Console.WriteLine();
            Console.ReadLine();
        }

        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {

            // Create ContentKeyAuthorizationPolicy with Open restrictions 
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>
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

            List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>
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

            // Associate the content key authorization policy with the content key
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
            // The following code configures PlayReady License Template using .NET classes
            // and returns the XML string.

            //The PlayReadyLicenseResponseTemplate class represents the template 
            //for the response sent back to the end user. 
            //It contains a field for a custom data string between the license server 
            //and the application (may be useful for custom app logic) 
            //as well as a list of one or more license templates.

            PlayReadyLicenseResponseTemplate responseTemplate =
                new PlayReadyLicenseResponseTemplate();

            // The PlayReadyLicenseTemplate class represents a license template 
            // for creating PlayReady licenses
            // to be returned to the end users. 
            // It contains the data on the content key in the license 
            // and any rights or restrictions to be 
            // enforced by the PlayReady DRM runtime when using the content key.
            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();

            // Configure whether the license is persistent 
            // (saved in persistent storage on the client) 
            // or non-persistent (only held in memory while the player is using the license).  
            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

            // AllowTestDevices controls whether test devices can use the license or not.  
            // If true, the MinimumSecurityLevel property of the license
            // is set to 150.  If false (the default), 
            // the MinimumSecurityLevel property of the license is set to 2000.
            licenseTemplate.AllowTestDevices = true;

            // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
            // It grants the user the ability to play back the content subject to the zero or more restrictions 
            // configured in the license and on the PlayRight itself (for playback specific policy). 
            // Much of the policy on the PlayRight has to do with output restrictions 
            // which control the types of outputs that the content can be played over and 
            // any restrictions that must be put in place when using a given output.
            // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
            //then the DRM runtime will only allow the video to be displayed over digital outputs 
            //(analog video outputs won’t be allowed to pass the content).

            // IMPORTANT: These types of restrictions can be very powerful 
            // but can also affect the consumer experience. 
            // If the output protections are configured too restrictive, 
            // the content might be unplayable on some clients. 
            // For more information, see the PlayReady Compliance Rules document.

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
                                required_output_protection =
                                    new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
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


        static public IContentKey CreateCommonTypeContentKey()
        {
            // Create envelope encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                                    keyId,
                                    contentKey,
                                    "ContentKey",
                                    ContentKeyType.CommonEncryption);

            return key;
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

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Další informace najdete v tématech
* [Použití běžného dynamického šifrování PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md)
* [Použití dynamického šifrování AES-128 a doručení klíče služby](media-services-protect-with-aes128.md)
