---
title: Přehled šablon licencí Widevine | Microsoft Docs
description: Toto téma poskytuje přehled šablony licencí Widevine, která se používá ke konfiguraci licencí Widevine.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 7bdffa607a1cbe47b940590d19f6140238d31bf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89266489"
---
# <a name="widevine-license-template-overview"></a>Přehled šablon licencování Widevine

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

K nakonfigurování a vyžádání licencí Google Widevine můžete použít Azure Media Services. Když se hráč pokusí přehrát obsah chráněný Widevine, pošle se do služby doručování licencí požadavek, aby získal licenci. Pokud licenční služba žádost schválí, služba vydá licenci. Odesílá se klientovi a používá se k dešifrování a přehrávání zadaného obsahu.

Žádost o licenci Widevine je naformátovaná jako zpráva JSON.  

>[!NOTE]
> Můžete vytvořit prázdnou zprávu bez hodnot, stačí " {} ." Pak se vytvoří šablona licence s výchozími hodnotami. Výchozí hodnota funguje ve většině případů. Scénáře pro doručování licencí založené na Microsoftu by měly vždycky používat výchozí hodnoty. Pokud potřebujete nastavit hodnoty "Provider" a "content_id", poskytovatel musí odpovídat přihlašovacím údajům Widevine.

```json
{  
   "payload": "<license challenge>",
   "content_id": "<content id>" 
   "provider": "<provider>"
   "allowed_track_types": "<types>",
   "content_key_specs": [  
      {  
         "track_type": "<track type 1>"
      },
      {  
         "track_type": "<track type 2>"
      },
      …
   ],
   "policy_overrides": {  
      "can_play": <can play>,
      "can persist": <can persist>,
      "can_renew": <can renew>,
      "rental_duration_seconds": <rental duration>,
      "playback_duration_seconds": <playback duration>,
      "license_duration_seconds": <license duration>,
      "renewal_recovery_duration_seconds": <renewal recovery duration>,
      "renewal_server_url": "<renewal server url>",
      "renewal_delay_seconds": <renewal delay>,
      "renewal_retry_interval_seconds": <renewal retry interval>,
      "renew_with_usage": <renew with usage>
   }
}
```

## <a name="json-message"></a>Zpráva JSON
| Name | Hodnota | Popis |
| --- | --- | --- |
| payload |Řetězec s kódováním base64 |Žádost o licenci odeslanou klientem |
| content_id |Řetězec s kódováním base64 |Identifikátor použitý k odvození ID klíče a klíče obsahu pro každý content_key_specs. track_type. |
| Zprostředkovatel |řetězec |Slouží k vyhledání klíčů obsahu a zásad. Pokud se pro doručování licencí Widevine používá doručování klíčů Microsoft, tento parametr se ignoruje. |
| policy_name |řetězec |Název dřív registrovaných zásad. Nepovinný parametr. |
| allowed_track_types |enum |SD_ONLY nebo SD_HD. Určuje, které klíče obsahu jsou zahrnuty v licenci. |
| content_key_specs |Pole struktur JSON najdete v části specifikace klíče obsahu.  |Jemnější ovládací prvek, který vrací klíče obsahu. Další informace najdete v části specifikace klíče obsahu. Lze zadat pouze jednu z hodnot allowed_track_types a content_key_specs. |
| use_policy_overrides_exclusively |Logická hodnota, true nebo false |Použijte atributy zásad určené policy_overrides a vynechejte všechny dřív uložené zásady. |
| policy_overrides |Struktury JSON najdete v části "přepsání zásad". |Nastavení zásad pro tuto licenci  V případě, že má tento Asset předdefinované zásady, použijí se tyto zadané hodnoty. |
| session_init |Struktury JSON najdete v části "inicializace relace". |K licenci se předávají volitelná data. |
| parse_only |Logická hodnota, true nebo false |Žádost o licenci se analyzuje, ale nevydá se žádná licence. Hodnoty z žádosti o licenci se ale vrátí v odpovědi. |

## <a name="content-key-specs"></a>Specifikace klíče obsahu
Pokud existují existující zásady, není nutné zadávat žádné hodnoty ve specifikaci klíče obsahu. K určení ochrany výstupu, jako je třeba vysoce šířka pásma digitální Content Protection (HDCP) a CGMS (copy General Management System), se použijí stávající zásady přidružené k tomuto obsahu. Pokud není u licenčního serveru Widevine zaregistrována předplatná zásada, může poskytovatel obsahu tyto hodnoty vložit do žádosti o licenci.   

Každá hodnota content_key_specs musí být zadána pro všechny stopy bez ohledu na možnost use_policy_overrides_exclusively. 

| Name | Hodnota | Popis |
| --- | --- | --- |
| content_key_specs. track_type |řetězec |Název typu stopy. Je-li v žádosti o licenci zadán content_key_specs, nezapomeňte explicitně zadat všechny typy sledování. V důsledku tohoto selhání dojde k selhání při přehrání posledních 10 sekund. |
| content_key_specs  <br/> security_level |UInt32 |Definuje požadavky na odolnost klienta pro přehrávání. <br/> – Vyžaduje se softwarově vycházející kryptografický modul s prázdným polem. <br/> – Vyžaduje se softwarová kryptografie a zakódováný dekodér. <br/> – Operace klíčového materiálu a kryptografie se musí provádět v rámci důvěryhodného spouštěcího prostředí s hardwarovým zálohováním. <br/> – Kryptografie a dekódování obsahu se musí provádět v rámci důvěryhodného spouštěcího prostředí s hardwarovým zálohováním.  <br/> – Kryptografie, dekódování a veškerá manipulace s médii (komprimovaná a nekomprimovaná) se musí zpracovat v rámci důvěryhodného spouštěcího prostředí pro hardware. |
| content_key_specs <br/> required_output_protection. HDC |String, jedna z HDCP_NONE, HDCP_V1, HDCP_V2 |Určuje, jestli je nutná HDCP. |
| content_key_specs <br/>key |Base<br/>kódovaný řetězec |Klíč obsahu, který se má použít pro tuto stopu Je-li tento parametr zadán, je požadován track_type nebo key_id. Poskytovatel obsahu může tuto možnost použít k vložení klíče obsahu pro tuto stopu místo toho, aby mohl licenční server Widevine vygenerovat nebo vyhledat klíč. |
| content_key_specs content_key_specs.Key_ID |Binární soubor řetězce kódovaný v kódování Base64, 16 bajtů |Jedinečný identifikátor pro klíč |

## <a name="policy-overrides"></a>Přepsání zásad
| Name | Hodnota | Popis |
| --- | --- | --- |
| policy_overrides. can_play |Logická hodnota, true nebo false |Indikuje, že přehrávání obsahu je povolené. Výchozí hodnota je false. |
| policy_overrides. can_persist |Logická hodnota, true nebo false |Označuje, že licence může být trvalá pro nestálé úložiště pro použití v offline režimu. Výchozí hodnota je false. |
| policy_overrides. can_renew |Logická hodnota, true nebo false |Označuje, že je povoleno obnovování této licence. V případě hodnoty true se dá prodloužit doba trvání licence pomocí prezenčního signálu. Výchozí hodnota je false. |
| policy_overrides. license_duration_seconds |Int64 |Označuje časový interval pro tuto konkrétní licenci. Hodnota 0 znamená, že doba trvání není omezena. Výchozí hodnota je 0. |
| policy_overrides. rental_duration_seconds |Int64 |Určuje časový interval, během kterého je povoleno přehrávání. Hodnota 0 znamená, že doba trvání není omezena. Výchozí hodnota je 0. |
| policy_overrides. playback_duration_seconds |Int64 |Časové období zobrazení, po kterém se přehrávání začne během trvání licence. Hodnota 0 znamená, že doba trvání není omezena. Výchozí hodnota je 0. |
| policy_overrides. renewal_server_url |řetězec |Všechny požadavky na prezenční signál (obnovení) pro tuto licenci jsou směrovány na zadanou adresu URL. Toto pole se používá pouze v případě, že je can_renew true. |
| policy_overrides. renewal_delay_seconds |Int64 |Počet sekund po license_start_time před prvním pokusem o obnovení. Toto pole se používá pouze v případě, že je can_renew true. Výchozí hodnota je 0. |
| policy_overrides. renewal_retry_interval_seconds |Int64 |Určuje zpoždění v sekundách mezi požadavky následného obnovení licence v případě selhání. Toto pole se používá pouze v případě, že je can_renew true. |
| policy_overrides. renewal_recovery_duration_seconds |Int64 |Časové období, ve kterém může přehrávání pokračovat během obnovování, ale neúspěšné z důvodu problémů back-endu s licenčním serverem. Hodnota 0 znamená, že doba trvání není omezena. Toto pole se používá pouze v případě, že je can_renew true. |
| policy_overrides. renew_with_usage |Logická hodnota, true nebo false |Indikuje, že licence se posílá k obnovení při zahájení používání. Toto pole se používá pouze v případě, že je can_renew true. |

## <a name="session-initialization"></a>Inicializace relace
| Name | Hodnota | Popis |
| --- | --- | --- |
| provider_session_token |Řetězec s kódováním base64 |Tento token relace se předává zpátky v licenci a existuje v následných obnoveních. Token relace není po relacích zachován. |
| provider_client_token |Řetězec s kódováním base64 |Token klienta k odeslání zpět v odpovědi na licenci Pokud žádost o licenci obsahuje token klienta, tato hodnota se ignoruje. Token klienta přetrvá nad rámec licenčních relací. |
| override_provider_client_token |Logická hodnota, true nebo false |Pokud je hodnota false a žádost o licenci obsahuje token klienta, použijte token z požadavku i v případě, že je v této struktuře zadaný token klienta. Je-li nastavena hodnota true, vždy použijte token zadaný v této struktuře. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Konfigurace licencí Widevine pomocí typů .NET
Media Services poskytuje rozhraní API .NET, která můžete použít ke konfiguraci licencí Widevine. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Třídy definované v sadě Media Services .NET SDK
Následující třídy jsou definice těchto typů:

```dotnetcli
public class WidevineMessage
{
    public WidevineMessage();

    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public AllowedTrackTypes? allowed_track_types { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public ContentKeySpecs[] content_key_specs { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public object policy_overrides { get; set; }
}

[JsonConverter(typeof(StringEnumConverter))]
public enum AllowedTrackTypes
{
    SD_ONLY = 0,
    SD_HD = 1
}
public class ContentKeySpecs
{
    public ContentKeySpecs();

    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public string key_id { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public RequiredOutputProtection required_output_protection { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public int? security_level { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public string track_type { get; set; }
}

public class RequiredOutputProtection
{
    public RequiredOutputProtection();

    public Hdcp hdcp { get; set; }
}

[JsonConverter(typeof(StringEnumConverter))]
public enum Hdcp
{
    HDCP_NONE = 0,
    HDCP_V1 = 1,
    HDCP_V2 = 2
}
```

### <a name="example"></a>Příklad
Následující příklad ukazuje, jak použít rozhraní API .NET ke konfiguraci jednoduché licence Widevine:

```dotnetcli
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
```

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Použití běžného dynamického šifrování PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md)

