---
title: Azure Media Services s technologií Widevine licence Přehled šablon | Dokumentace Microsoftu
description: Toto téma obsahuje základní informace o šabloně licence Widevine, který se používá ke konfiguraci licencí Widevine.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 3615bd88cfadf2f59942fab7678d36d4d20d8c9f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992734"
---
# <a name="widevine-license-template-overview"></a>Přehled šablon licencování Widevine 

Azure Media Services umožňuje šifrování obsahu pomocí **Google Widevine**. Služba Media Services také poskytuje službu k doručování licencí Widevine. Rozhraní API služeb Azure Media Services můžete použít ke konfiguraci licencí Widevine. Přehrávač pokusí k přehrávání obsahu chráněného technologií Widevine, žádost přijde na službu doručování licencí k získání licence. Pokud licenční služby schválí, služba vydá licence. Je odeslat klientovi a slouží k dešifrování a přehrát zadaný obsah.

Žádost o licenci Widevine je formátován jako zprávy JSON.  

>[!NOTE]
> Prázdná zpráva s žádné hodnoty, můžete vytvořit pouze "{}." Potom šablonu licence se vytvoří s výchozí hodnoty. Výchozí hodnota vyhovuje většině případů. Scénáře založené na Microsoft-naším vlastním doručováním licencí používejte vždy výchozí hodnoty. Pokud je potřeba nastavit "provider" a "content_id" hodnoty, zprostředkovatele musí odpovídat přihlašovacím údajům Widevine.

    {  
       "payload":"<license challenge>",
       "content_id": "<content id>"
       "provider": "<provider>"
       "allowed_track_types":"<types>",
       "content_key_specs":[  
          {  
             "track_type":"<track type 1>"
          },
          {  
             "track_type":"<track type 2>"
          },
          …
       ],
       "policy_overrides":{  
          "can_play":<can play>,
          "can persist":<can persist>,
          "can_renew":<can renew>,
          "rental_duration_seconds":<rental duration>,
          "playback_duration_seconds":<playback duration>,
          "license_duration_seconds":<license duration>,
          "renewal_recovery_duration_seconds":<renewal recovery duration>,
          "renewal_server_url":"<renewal server url>",
          "renewal_delay_seconds":<renewal delay>,
          "renewal_retry_interval_seconds":<renewal retry interval>,
          "renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>Zprávy JSON

| Název | Hodnota | Popis |
| --- | --- | --- |
| datová část |Řetězec s kódováním base64 |Požadavek na licenční odeslány klientem. |
| content_id |Řetězec s kódováním base64 |Použít k odvození klíče ID a obsah identifikátor klíče pro každý content_key_specs.track_type. |
| Zprostředkovatel |řetězec |Použít k vyhledání obsahu klíčů a zásad. Pokud Microsoft doručení klíče slouží k doručování licencí Widevine, tento parametr je ignorován. |
| název_zásad |řetězec |Název zásady dříve zaregistrovaný. Volitelné. |
| allowed_track_types |Výčet |SD_ONLY nebo SD_HD. Ovládací prvky, které obsah klíče jsou součástí licencí. |
| content_key_specs |Pole JSON struktury, najdete v části "Obsahu specifikace klíče."  |Citlivější ovládací prvek, na které symetrické klíče k vrácení. Další informace najdete v části "Obsahu specifikace klíče." Lze zadat pouze jednu z hodnot allowed_track_types a content_key_specs. |
| use_policy_overrides_exclusively |Logická hodnota true nebo false |Použít zásady atributy určené policy_overrides a vynechat možnost všechny dříve uložené zásady. |
| policy_overrides |JSON strukturu, najdete v části "Zásady přepsání." |Nastavení zásad pro tuto licenci.  V případě, že tento prostředek obsahuje předdefinované zásady, se používají tyto zadané hodnoty. |
| session_init |JSON strukturu, najdete v části "Inicializace relace." |Licence, které je předáno volitelnými daty. |
| parse_only |Logická hodnota true nebo false |Požadavek licence je analyzován, ale vydán žádná licence. Však hodnoty ze žádosti o licenci jsou vráceny v odpovědi. |

## <a name="content-key-specs"></a>Obsahu specifikace klíče
Pokud již existující zásady existují, není nutné zadat jakoukoliv hodnotu v specifikace klíče obsahu. Existující zásady přidružené k tomuto obsahu se používá k určení ochrana výstupu, jako je například velkou šířkou pásma digitální obsah ochrany (HDCP) a kopie systému pro obecné správy (CGMS). Pokud již existujících zásad není zaregistrován u serveru licencí Widevine, můžete poskytovateli obsahu vložení hodnoty do žádosti o licenci.   

Každá hodnota content_key_specs je nutné zadat pro všechny stopy, bez ohledu na to use_policy_overrides_exclusively možnost. 

| Název | Hodnota | Popis |
| --- | --- | --- |
| content_key_specs. track_type |řetězec |Název typu sledování. Pokud content_key_specs je zadaná v žádosti o licenci, ujistěte se, že chcete určit, že všechny typy sledovat explicitně. Pokud tak neučiníte za následek selhání při přehrávání za 10 sekund. |
| content_key_specs  <br/> security_level |uint32 |Definuje požadavky na klienta odolnosti pro přehrávání. <br/> -Softwarových kryptografie prázdné pole je povinné. <br/> -Vyžaduje se šifrování software a dekodér obfuskovaný. <br/> -Klíčové materiálu a kryptografické operace je provést v rámci prostředí pro důvěryhodného spouštění podporovaný hardware. <br/> -Kryptografie a dekódování obsahu se musí provádět v rámci prostředí pro důvěryhodného spouštění podporovaný hardware.  <br/> -Kryptografie, dekódování a všechny zpracování médií (komprimovaným a nekomprimovaným formátem) musí zpracovat v rámci prostředí pro důvěryhodného spouštění podporovaný hardware. |
| content_key_specs <br/> required_output_protection.hdc |řetězec, jeden z HDCP_V2 HDCP_NONE HDCP_V1, |Označuje, zda HDCP je povinný. |
| content_key_specs <br/>key |Base64-<br/>řetězce s kódováním |Klíč k obsahu pro tento blok. Je-li zadána, je požadován track_type nebo key_id. Poskytovatel obsahu můžete použít tuto možnost vkládat klíče k obsahu pro toto sledování licenční server Widevine Generovat nebo vyhledat klíče místo. |
| content_key_specs.key_id |Řetězec s kódováním base64 binární, 16 bajtů |Jedinečný identifikátor pro klíč. |

## <a name="policy-overrides"></a>Přepsání zásad
| Název | Hodnota | Popis |
| --- | --- | --- |
| policy_overrides&#46;can_play |Logická hodnota true nebo false |Určuje povolené přehrávání obsahu. Výchozí hodnota je false. |
| policy_overrides&#46;can_persist |Logická hodnota true nebo false |Označuje, že může uložit trvale licence na stálé úložiště pro použití v offline režimu. Výchozí hodnota je false. |
| policy_overrides&#46;can_renew |Logická hodnota true nebo false |Označuje, že je povoleno obnovení této licence. Při hodnotě true je možné rozšířit trvání licence k podle prezenčního signálu. Výchozí hodnota je false. |
| policy_overrides&#46;license_duration_seconds |int64 |Určuje časový interval pro tuto konkrétní licenci. Hodnota 0 označuje, že neexistuje žádné omezení na délku. Výchozí hodnota je 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Určuje časový interval během přehrávání je povolené. Hodnota 0 označuje, že neexistuje žádné omezení na délku. Výchozí hodnota je 0. |
| policy_overrides&#46;playback_duration_seconds |int64 |Okno zobrazení času po zahájení v rámci doby trvání licence. Hodnota 0 označuje, že neexistuje žádné omezení na délku. Výchozí hodnota je 0. |
| policy_overrides&#46;renewal_server_url |řetězec |Všechny požadavky prezenčního signálu (prodloužení) pro tuto licenci se přesměruje na zadané adrese URL. Toto pole se používá pouze v případě, že can_renew má hodnotu true. |
| policy_overrides&#46;renewal_delay_seconds |int64 |Počet sekund po license_start_time před prvním pokusu o obnovení. Toto pole se používá pouze v případě, že can_renew má hodnotu true. Výchozí hodnota je 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |Určuje zpoždění v sekundách mezi požadavky na obnovení certifikátu další licence, v případě selhání. Toto pole se používá pouze v případě, že can_renew má hodnotu true. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |Okno čas, ve které přehrávání může pokračovat, když dojde k pokusu o obnovení, ale neúspěšné kvůli problémům s back-end s licenční server. Hodnota 0 označuje, že neexistuje žádné omezení na délku. Toto pole se používá pouze v případě, že can_renew má hodnotu true. |
| policy_overrides&#46;renew_with_usage |Logická hodnota true nebo false |Označuje, že licence, které je odesláno pro prodloužení platnosti při spuštění využití. Toto pole se používá pouze v případě, že can_renew má hodnotu true. |

## <a name="session-initialization"></a>Inicializace relace
| Název | Hodnota | Popis |
| --- | --- | --- |
| provider_session_token |Řetězec s kódováním base64 |Tento token relace je předána zpět v licenci a existuje v následné obnovení. Token relace není zachována po relace. |
| provider_client_token |Řetězec s kódováním base64 |Token klienta k odesílání zpět v odpovědi licence. Pokud požadavek na licenční obsahuje token klienta, tato hodnota je ignorována. Token klienta potrvá déle než relace licence. |
| override_provider_client_token |Logická hodnota true nebo false |Obsahuje-li hodnotu false a požadavek na licenční token klienta, použijte token z požadavku i v případě, že token klienta byla zadána v této struktuře. Při hodnotě true se vždycky používejte token zadaný v této struktuře. |

## <a name="configure-your-widevine-license-with-net"></a>Konfigurace licence Widevine s využitím .NET 

Služba Media Services poskytuje třídu, která vám umožní nakonfigurovat licencování Widevine. Chcete-li vytvořit licence, předejte JSON na [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Chcete-li nakonfigurovat šablony, můžete:

### <a name="directly-construct-a-json-string"></a>Přímo vytvořit řetězec formátu JSON

Tato metoda může být náchylné k chybě. Doporučuje se použít jiné metody, je popsáno v [definovat potřebné třídy a serializaci do formátu JSON](#classes).

    ```csharp
    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
    };
    ```

### <a id="classes"></a> Definovat potřebné třídy a serializaci do formátu JSON

#### <a name="define-classes"></a>Definování tříd

Následující příklad ukazuje příklad definice tříd, které jsou mapovány na schéma Widevine JSON. Můžete vytvořit instanci třídy před serializací řetězec JSON.  

    ```csharp
    public class PolicyOverrides
    {
        public bool CanPlay { get; set; }
        public bool CanPersist { get; set; }
        public bool CanRenew { get; set; }
        public int RentalDurationSeconds { get; set; }    //Indicates the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
        public int PlaybackDurationSeconds { get; set; }  //The viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
        public int LicenseDurationSeconds { get; set; }   //Indicates the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
    }

    public class ContentKeySpec
    {
        public string TrackType { get; set; }
        public int SecurityLevel { get; set; }
        public OutputProtection RequiredOutputProtection { get; set; }
    }

    public class OutputProtection
    {
        public string HDCP { get; set; }
    }

    public class WidevineTemplate
    {
        public string AllowedTrackTypes { get; set; }
        public ContentKeySpec[] ContentKeySpecs { get; set; }
        public PolicyOverrides PolicyOverrides { get; set; }
    }
    ```

#### <a name="configure-the-license"></a>Nakonfigurujte licenční

Pomocí třídy definované v předchozí části můžete vytvořit JSON, který se používá ke konfiguraci [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="next-steps"></a>Další postup

Podívejte se na tom, jak [chránit pomocí DRM](protect-with-drm.md)
