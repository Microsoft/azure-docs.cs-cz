---
title: Přehled šablon licencí Azure Media Services V3 with Widevine
description: Toto téma poskytuje přehled šablony licencí Widevine, která se používá ke konfiguraci licencí Widevine.
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705628"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Přehled šablon licencí Media Services V3 with Widevine

Azure Media Services vám umožní šifrovat obsah pomocí **Google Widevine**. Media Services taky poskytuje službu pro doručování licencí Widevine. K nakonfigurování licencí Widevine můžete použít rozhraní API pro Azure Media Services. Když se hráč pokusí přehrát obsah chráněný Widevine, pošle se mu žádost o získání licence. Pokud licenční služby schválí, služba vydá licence. Je odeslat klientovi a slouží k dešifrování a přehrát zadaný obsah.

Žádost o licenci Widevine je naformátovaná jako zpráva JSON.  

>[!NOTE]
> Můžete vytvořit prázdnou zprávu bez hodnot, stačí "{}." Pak se vytvoří šablona licence s výchozími hodnotami. Výchozí hodnota funguje ve většině případů. Scénáře pro doručování licencí založené na Microsoftu by měly vždycky používat výchozí hodnoty. Pokud potřebujete nastavit hodnoty "Provider" a "content_id", poskytovatel musí odpovídat přihlašovacím údajům Widevine.

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
| Délka |Řetězec s kódováním base64 |Žádost o licenci odeslanou klientem |
| content_id |Řetězec s kódováním base64 |Identifikátor použitý k odvození ID klíče a klíče obsahu pro každý content_key_specs. track_type |
| Zprostředkovatel |string |Slouží k vyhledání klíčů obsahu a zásad. Pokud se pro doručování licencí Widevine používá doručování klíčů Microsoft, tento parametr se ignoruje. |
| policy_name |string |Název dřív registrovaných zásad. Nepovinný parametr. |
| allowed_track_types |Výčet |SD_ONLY nebo SD_HD. Určuje, které klíče obsahu jsou zahrnuty v licenci. |
| content_key_specs |Pole struktur JSON najdete v části specifikace klíče obsahu.  |Jemnější ovládací prvek, který vrací klíče obsahu. Další informace najdete v části specifikace klíče obsahu. Lze zadat pouze jednu z hodnot allowed_track_types a content_key_specs. |
| use_policy_overrides_exclusively |Logická hodnota, true nebo false |Použijte atributy zásad určené policy_overrides a vynechejte všechny dřív uložené zásady. |
| policy_overrides |Struktury JSON najdete v části "přepsání zásad". |Nastavení zásad pro tuto licenci  V případě, že má tento Asset předdefinované zásady, použijí se tyto zadané hodnoty. |
| session_init |Struktury JSON najdete v části "inicializace relace". |K licenci se předávají volitelná data. |
| parse_only |Logická hodnota, true nebo false |Žádost o licenci se analyzuje, ale nevydá se žádná licence. Hodnoty z žádosti o licenci se ale vrátí v odpovědi. |

## <a name="content-key-specs"></a>Specifikace klíče obsahu
Pokud existují existující zásady, není nutné zadávat žádné hodnoty ve specifikaci klíče obsahu. K určení ochrany výstupu, jako je třeba vysoce šířka pásma digitální Content Protection (HDCP) a CGMS (copy General Management System), se použijí stávající zásady přidružené k tomuto obsahu. Pokud se u licenčního serveru Widevine nezaregistruje již existující zásada, může poskytovatel obsahu tyto hodnoty vložit do žádosti o licenci.   

Každá hodnota content_key_specs musí být zadána pro všechny stopy bez ohledu na možnost use_policy_overrides_exclusively. 

| Name (Název) | Hodnota | Popis |
| --- | --- | --- |
| content_key_specs. track_type |string |Název typu stopy. Je-li v žádosti o licenci zadán content_key_specs, nezapomeňte explicitně zadat všechny typy sledování. V důsledku tohoto selhání dojde k selhání při přehrání posledních 10 sekund. |
| content_key_specs  <br/> security_level |uint32 |Definuje požadavky na odolnost klienta pro přehrávání. <br/> – Vyžaduje se softwarově vycházející kryptografický modul s prázdným polem. <br/> – Vyžaduje se softwarová kryptografie a zakódováný dekodér. <br/> – Operace klíčového materiálu a kryptografie se musí provádět v rámci důvěryhodného spouštěcího prostředí s hardwarovým zálohováním. <br/> – Kryptografie a dekódování obsahu se musí provádět v rámci důvěryhodného spouštěcího prostředí s hardwarovým zálohováním.  <br/> – Kryptografie, dekódování a veškerá manipulace s médii (komprimovaná a nekomprimovaná) se musí zpracovat v rámci důvěryhodného spouštěcího prostředí pro hardware. |
| content_key_specs <br/> required_output_protection.hdc |String, jedna z HDCP_NONE, HDCP_V1, HDCP_V2 |Určuje, jestli je nutná HDCP. |
| content_key_specs <br/>key |Base<br/>kódovaný řetězec |Klíč obsahu, který se má použít pro tuto stopu Je-li tento parametr zadán, je požadován track_type nebo key_id. Poskytovatel obsahu může tuto možnost použít k vložení klíče obsahu pro tuto stopu místo toho, aby mohl licenční server Widevine vygenerovat nebo vyhledat klíč. |
| content_key_specs. key_id |Binární soubor řetězce kódovaný v kódování Base64, 16 bajtů |Jedinečný identifikátor pro klíč |

## <a name="policy-overrides"></a>Přepsání zásad
| Name (Název) | Hodnota | Popis |
| --- | --- | --- |
| policy_overrides&#46;can_play |Logická hodnota, true nebo false |Indikuje, že přehrávání obsahu je povolené. Výchozí hodnota je false. |
| policy_overrides&#46;can_persist |Logická hodnota, true nebo false |Označuje, že licence může být trvalá pro nestálé úložiště pro použití v offline režimu. Výchozí hodnota je false. |
| policy_overrides&#46;can_renew |Logická hodnota, true nebo false |Označuje, že je povoleno obnovování této licence. V případě hodnoty true se dá prodloužit doba trvání licence pomocí prezenčního signálu. Výchozí hodnota je false. |
| policy_overrides&#46;license_duration_seconds |int64 |Označuje časový interval pro tuto konkrétní licenci. Hodnota 0 znamená, že doba trvání není omezena. Výchozí hodnota je 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Určuje časový interval, během kterého je povoleno přehrávání. Hodnota 0 znamená, že doba trvání není omezena. Výchozí hodnota je 0. |
| policy_overrides&#46;playback_duration_seconds |int64 |Časové období zobrazení, po kterém se přehrávání začne během trvání licence. Hodnota 0 znamená, že doba trvání není omezena. Výchozí hodnota je 0. |
| policy_overrides&#46;renewal_server_url |string |Všechny požadavky na prezenční signál (obnovení) pro tuto licenci jsou směrovány na zadanou adresu URL. Toto pole se používá pouze v případě, že je can_renew true. |
| policy_overrides&#46;renewal_delay_seconds |int64 |Počet sekund po license_start_time před prvním pokusem o obnovení. Toto pole se používá pouze v případě, že je can_renew true. Výchozí hodnota je 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |Určuje zpoždění v sekundách mezi požadavky následného obnovení licence v případě selhání. Toto pole se používá pouze v případě, že je can_renew true. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |Časové období, ve kterém může přehrávání pokračovat během obnovování, ale neúspěšné z důvodu problémů back-endu s licenčním serverem. Hodnota 0 znamená, že doba trvání není omezena. Toto pole se používá pouze v případě, že je can_renew true. |
| policy_overrides&#46;renew_with_usage |Logická hodnota, true nebo false |Indikuje, že licence se posílá k obnovení při zahájení používání. Toto pole se používá pouze v případě, že je can_renew true. |

## <a name="session-initialization"></a>Inicializace relace
| Name (Název) | Hodnota | Popis |
| --- | --- | --- |
| provider_session_token |Řetězec s kódováním base64 |Tento token relace se předává zpátky v licenci a existuje v následných obnoveních. Token relace není po relacích zachován. |
| provider_client_token |Řetězec s kódováním base64 |Token klienta k odeslání zpět v odpovědi na licenci Pokud žádost o licenci obsahuje token klienta, tato hodnota se ignoruje. Token klienta přetrvá nad rámec licenčních relací. |
| override_provider_client_token |Logická hodnota, true nebo false |Pokud je hodnota false a žádost o licenci obsahuje token klienta, použijte token z požadavku i v případě, že je v této struktuře zadaný token klienta. Je-li nastavena hodnota true, vždy použijte token zadaný v této struktuře. |

## <a name="configure-your-widevine-license-with-net"></a>Konfigurace licence Widevine pomocí .NET 

Media Services poskytuje třídu, která vám umožní nakonfigurovat licenci Widevine. Pokud chcete sestavit licenci, předejte JSON do [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Chcete-li nakonfigurovat šablonu, můžete:

### <a name="directly-construct-a-json-string"></a>Přímá konstrukce řetězce JSON

Tato metoda může být náchylná k chybám. Doporučuje se použít jinou metodu popsanou v tématu [Definování potřebných tříd a serializace do formátu JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a id="classes"></a>Definování potřebných tříd a serializace na JSON

#### <a name="define-classes"></a>Definovat třídy

Následující příklad ukazuje příklad definice tříd, které jsou mapovány na Widevine schématu JSON. Třídy lze vytvořit před jejich serializací do řetězce JSON.  

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

Použijte třídy definované v předchozí části k vytvoření formátu JSON, který se používá ke konfiguraci [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

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

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="next-steps"></a>Další kroky

Podívejte se na tom, jak [chránit pomocí DRM](protect-with-drm.md)
