---
title: Azure Media Services v3 s přehledem šablony licence Widevine
description: Toto téma poskytuje přehled šablony licence Widevine, která se používá ke konfiguraci licencí Widevine.
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
ms.openlocfilehash: 94ce5e45a9a43e81020096ddc0a67429b286d9b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705628"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Mediální služby v3 s přehledem šablony licence Widevine

Azure Media Services umožňuje šifrovat obsah pomocí **Google Widevine**. Media Services také poskytuje službu pro doručování licencí Widevine. Rozhraní API Azure Media Services můžete použít ke konfiguraci licencí Widevine. Když se hráč pokusí přehrát váš obsah chráněný widevine, je odeslána žádost do služby doručování licencí získat licenci. Pokud licenční služba žádost schválí, vydá licenci. Odešle se klientovi a slouží k dešifrování a přehrání zadaného obsahu.

Požadavek na licenci Widevine je formátován jako zpráva JSON.  

>[!NOTE]
> Můžete vytvořit prázdnou zprávu bez hodnot, pouze ""{}. Poté je vytvořena licenční šablona s výchozími hodnotami. Výchozí funguje ve většině případů. Scénáře doručování licencí společnosti Microsoft by měly vždy používat výchozí hodnoty. Pokud potřebujete nastavit hodnoty "zprostředkovatel" a "content_id", zprostředkovatel musí odpovídat Widevine pověření.

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
          "renew_with_usage":<renew with usage>
       }
    }

## <a name="json-message"></a>Zpráva JSON

| Name (Název) | Hodnota | Popis |
| --- | --- | --- |
| payload |Řetězec kódovaný base64 |Žádost o licenci odeslaná klientem. |
| content_id |Řetězec kódovaný base64 |Identifikátor používaný k odvození ID klíče klíče a klíče obsahu pro každý content_key_specs.track_type. |
| Zprostředkovatel |řetězec |Slouží k vyhledávání klíčů a zásad obsahu. Pokud se pro doručování licencí Widevine používá doručení klíče společnosti Microsoft, bude tento parametr ignorován. |
| policy_name |řetězec |Název dříve registrované zásady. Nepovinný parametr. |
| allowed_track_types |enum |SD_ONLY nebo SD_HD. Určuje, které klíče obsahu jsou součástí licence. |
| content_key_specs |Pole struktur JSON naleznete v části Specifikace klíčových prvků obsahu.  |Jemnější ovládací prvek, na kterém obsahu klíče vrátit. Další informace naleznete v části Specifikace klíčových obsahu. Lze zadat pouze jednu z hodnot allowed_track_types a content_key_specs. |
| use_policy_overrides_exclusively |Logická hodnota, pravdivá nebo nepravdivá |Použijte atributy zásad určené policy_overrides a vynechte všechny dříve uložené zásady. |
| policy_overrides |Struktura JSON, viz část "Přepsání zásad". |Nastavení zásad pro tuto licenci.  V případě, že tento majetek má předdefinovanou zásadu, použijí se tyto zadané hodnoty. |
| session_init |Struktura JSON, viz část "Inicializace relace". |Volitelná data jsou předána licenci. |
| parse_only |Logická hodnota, pravdivá nebo nepravdivá |Žádost o licenci je analyzována, ale není vydána žádná licence. V odpovědi jsou však vráceny hodnoty z žádosti o licenci. |

## <a name="content-key-specs"></a>Klíčové specifikace obsahu
Pokud již existující zásada existuje, není nutné zadávat žádnou z hodnot v specifikaci klíče obsahu. Již existující zásady přidružené k tomuto obsahu se používají k určení ochrany výstupu, jako je například ochrana digitálního obsahu s vysokou šířkou pásma (HDCP) a systém pro správu obecné kopie (CGMS). Pokud již existující zásady není registrována na licenčním serveru Widevine, poskytovatel obsahu může vložit hodnoty do žádosti o licenci.   

Každá hodnota content_key_specs musí být zadána pro všechny stopy bez ohledu na možnost use_policy_overrides_exclusively. 

| Name (Název) | Hodnota | Popis |
| --- | --- | --- |
| content_key_specs. track_type |řetězec |Název typu stopy. Pokud je v žádosti o licenci zadáno content_key_specs, nezapomeňte explicitně zadat všechny typy tratí. Pokud tak neučiníte, nepodaří se přehrát po dobu 10 sekund. |
| content_key_specs  <br/> security_level |Uint32 |Definuje požadavky na odolnost klienta pro přehrávání. <br/> - Je vyžadována softwarová kryptografie s bílým rámečkem. <br/> - Je vyžadována softwarová kryptografie a zamlžený dekodér. <br/> - Klíčové materiálové a kryptografické operace musí být prováděny v rámci hardwarově podporovaného prostředí provádění. <br/> - Kryptografie a dekódování obsahu musí být provedena v rámci hardwarově podporovaného prostředí pro spuštění.  <br/> - Kryptografie, dekódování a veškerá manipulace s médii (komprimovaná a nekomprimovaná) musí být zpracována v rámci hardwarově podporovaného prostředí pro spuštění. |
| content_key_specs <br/> required_output_protection.hdc |řetězec, jeden z HDCP_NONE, HDCP_V1, HDCP_V2 |Označuje, zda je vyžadován hdcp. |
| content_key_specs <br/>key |Základní 64-<br/>kódovaný řetězec |Klíč obsahu, který chcete použít pro tuto stopu. Je-li zadána, je vyžadován track_type nebo key_id. Poskytovatel obsahu může tuto možnost použít k vložení klíče obsahu pro tuto stopu namísto toho, aby licenční server Widevine generoval nebo vyhledává klíč. |
| content_key_specs.key_id |Binární řetězec kódovaný base64, 16 bajtů |Jedinečný identifikátor klíče |

## <a name="policy-overrides"></a>Přepsání zásad
| Name (Název) | Hodnota | Popis |
| --- | --- | --- |
| policy_overrides&#46;can_play |Logická hodnota, pravdivá nebo nepravdivá |Označuje, že přehrávání obsahu je povoleno. Výchozí hodnota je false. |
| policy_overrides&#46;can_persist |Logická hodnota, pravdivá nebo nepravdivá |Označuje, že licence může být trvalé do stálého úložiště pro použití v offline. Výchozí hodnota je false. |
| &#46;policy_overrides can_renew |Logická hodnota, pravdivá nebo nepravdivá |Označuje, že obnovení této licence je povoleno. Pokud true, doba trvání licence může být prodloužena o prezenční signál. Výchozí hodnota je false. |
| policy_overrides license_duration_seconds&#46; |int64 |Označuje časové okno pro tuto konkrétní licenci. Hodnota 0 označuje, že neexistuje žádné omezení trvání. Výchozí hodnota je 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Označuje časové okno, kdy je přehrávání povoleno. Hodnota 0 označuje, že neexistuje žádné omezení trvání. Výchozí hodnota je 0. |
| policy_overrides&#46;playback_duration_seconds |int64 |Doba zobrazení po spuštění přehrávání v rámci doby trvání licence. Hodnota 0 označuje, že neexistuje žádné omezení trvání. Výchozí hodnota je 0. |
| policy_overrides&#46;renewal_server_url |řetězec |Všechny požadavky na prezenční signál (obnovení) pro tuto licenci jsou směrovány na zadanou adresu URL. Toto pole se používá pouze v případě, can_renew je pravdivá. |
| policy_overrides&#46;&#46;&#46;renewal_delay_seconds&#46; |int64 |Kolik sekund po license_start_time před prvním pokusem o obnovení. Toto pole se používá pouze v případě, can_renew je pravdivá. Výchozí hodnota je 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |Určuje prodlevu v sekundách mezi následnými žádostmi o obnovení licence v případě selhání. Toto pole se používá pouze v případě, can_renew je pravdivá. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |Časové okno, ve kterém může přehrávání pokračovat při pokusu o obnovení, ale neúspěšné z důvodu problémů back-end s licenčním serverem. Hodnota 0 označuje, že neexistuje žádné omezení trvání. Toto pole se používá pouze v případě, can_renew je pravdivá. |
| &#46;renew_with_usage&#46;policy_overrides |Logická hodnota, pravdivá nebo nepravdivá |Označuje, že licence je odeslána k obnovení při spuštění použití. Toto pole se používá pouze v případě, can_renew je pravdivá. |

## <a name="session-initialization"></a>Inicializace relace
| Name (Název) | Hodnota | Popis |
| --- | --- | --- |
| provider_session_token |Řetězec kódovaný base64 |Tento token relace je předán zpět v licenci a existuje v následné obnovení. Token relace nepřetrvává mimo relace. |
| provider_client_token |Řetězec kódovaný base64 |Klientský token pro odeslání zpět v odpovědi na licenci. Pokud žádost o licenci obsahuje token klienta, tato hodnota je ignorována. Token klienta přetrvává i po udělení licence. |
| override_provider_client_token |Logická hodnota, pravdivá nebo nepravdivá |Pokud false a požadavek na licenci obsahuje token klienta, použijte token z požadavku i v případě, že token klienta byl zadán v této struktuře. Pokud true, vždy použijte token zadaný v této struktuře. |

## <a name="configure-your-widevine-license-with-net"></a>Konfigurace licence Widevine pomocí rozhraní .NET 

Služba Media Services poskytuje třídu, která umožňuje konfigurovat licenci Widevine. Chcete-li vytvořit licenci, přejděte JSON [na WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Chcete-li šablonu nakonfigurovat, můžete:

### <a name="directly-construct-a-json-string"></a>Přímo postavit řetězec JSON

Tato metoda může být náchylná k chybám. Doporučuje se použít jinou metodu, popsanou v [poli Definovat potřebné třídy a serializovat na JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a>Definování potřebných tříd a serializace do jsonu

#### <a name="define-classes"></a>Definování tříd

Následující příklad ukazuje příklad definice tříd, které mapují na schéma Widevine JSON. Můžete vytvořit konkretizovat třídy před serializací je řetězec JSON.  

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

#### <a name="configure-the-license"></a>Konfigurace licence

Pomocí tříd definovaných v předchozí části vytvořte json, který se používá ke konfiguraci [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

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

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="next-steps"></a>Další kroky

Podívejte se, jak [chránit pomocí DRM](protect-with-drm.md)
