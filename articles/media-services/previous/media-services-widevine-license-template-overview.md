---
title: Widevine licence šablony přehled | Dokumenty společnosti Microsoft
description: Toto téma poskytuje přehled šablony licence Widevine, která se používá ke konfiguraci licencí Widevine.
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
ms.openlocfilehash: c7511279e66ab598e4ae3c26f053915b7393b39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978386"
---
# <a name="widevine-license-template-overview"></a>Přehled šablon licencování Widevine 
Pomocí mediálních služeb Azure můžete konfigurovat a požadovat licence Google Widevine. Když se přehrávač pokusí přehrát váš obsah chráněný widevine, je odeslána žádost do služby doručování licencí získat licenci. Pokud licenční služba žádost schválí, vydá licenci. Odešle se klientovi a slouží k dešifrování a přehrání zadaného obsahu.

Požadavek na licenci Widevine je formátován jako zpráva JSON.  

>[!NOTE]
> Můžete vytvořit prázdnou zprávu bez hodnot, pouze ""{}. Poté je vytvořena licenční šablona s výchozími hodnotami. Výchozí funguje ve většině případů. Scénáře doručování licencí společnosti Microsoft by měly vždy používat výchozí hodnoty. Pokud potřebujete nastavit hodnoty "zprostředkovatel" a "content_id", zprostředkovatel musí odpovídat Widevine pověření.

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
Pokud již existující zásady existuje, není nutné zadávat žádné hodnoty v obsahu klíčové specifikace. Již existující zásady přidružené k tomuto obsahu se používají k určení ochrany výstupu, jako je například ochrana digitálního obsahu s vysokou šířkou pásma (HDCP) a systém pro správu obecné kopie (CGMS). Pokud již existující zásady není registrována na licenčním serveru Widevine, poskytovatel obsahu může vložit hodnoty do žádosti o licenci.   

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
| policy_overrides. can_play |Logická hodnota, pravdivá nebo nepravdivá |Označuje, že přehrávání obsahu je povoleno. Výchozí hodnota je false. |
| policy_overrides. can_persist |Logická hodnota, pravdivá nebo nepravdivá |Označuje, že licence může být trvalé do stálého úložiště pro použití v offline. Výchozí hodnota je false. |
| policy_overrides. can_renew |Logická hodnota, pravdivá nebo nepravdivá |Označuje, že obnovení této licence je povoleno. Pokud true, doba trvání licence může být prodloužena o prezenční signál. Výchozí hodnota je false. |
| policy_overrides. license_duration_seconds |int64 |Označuje časové okno pro tuto konkrétní licenci. Hodnota 0 označuje, že neexistuje žádné omezení trvání. Výchozí hodnota je 0. |
| policy_overrides. rental_duration_seconds |int64 |Označuje časové okno, kdy je přehrávání povoleno. Hodnota 0 označuje, že neexistuje žádné omezení trvání. Výchozí hodnota je 0. |
| policy_overrides. playback_duration_seconds |int64 |Doba zobrazení po spuštění přehrávání v rámci doby trvání licence. Hodnota 0 označuje, že neexistuje žádné omezení trvání. Výchozí hodnota je 0. |
| policy_overrides. renewal_server_url |řetězec |Všechny požadavky na prezenční signál (obnovení) pro tuto licenci jsou směrovány na zadanou adresu URL. Toto pole se používá pouze v případě, can_renew je pravdivá. |
| policy_overrides. renewal_delay_seconds |int64 |Kolik sekund po license_start_time před prvním pokusem o obnovení. Toto pole se používá pouze v případě, can_renew je pravdivá. Výchozí hodnota je 0. |
| policy_overrides. renewal_retry_interval_seconds |int64 |Určuje prodlevu v sekundách mezi následnými žádostmi o obnovení licence v případě selhání. Toto pole se používá pouze v případě, can_renew je pravdivá. |
| policy_overrides. renewal_recovery_duration_seconds |int64 |Časové okno, ve kterém může přehrávání pokračovat při pokusu o obnovení, ale neúspěšné z důvodu problémů back-end s licenčním serverem. Hodnota 0 označuje, že neexistuje žádné omezení trvání. Toto pole se používá pouze v případě, can_renew je pravdivá. |
| policy_overrides. renew_with_usage |Logická hodnota, pravdivá nebo nepravdivá |Označuje, že licence je odeslána k obnovení při spuštění použití. Toto pole se používá pouze v případě, can_renew je pravdivá. |

## <a name="session-initialization"></a>Inicializace relace
| Name (Název) | Hodnota | Popis |
| --- | --- | --- |
| provider_session_token |Řetězec kódovaný base64 |Tento token relace je předán zpět v licenci a existuje v následné obnovení. Token relace nepřetrvává mimo relace. |
| provider_client_token |Řetězec kódovaný base64 |Klientský token pro odeslání zpět v odpovědi na licenci. Pokud žádost o licenci obsahuje token klienta, tato hodnota je ignorována. Token klienta přetrvává i po udělení licence. |
| override_provider_client_token |Logická hodnota, pravdivá nebo nepravdivá |Pokud false a požadavek na licenci obsahuje token klienta, použijte token z požadavku i v případě, že token klienta byl zadán v této struktuře. Pokud true, vždy použijte token zadaný v této struktuře. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Konfigurace licencí Widevine pomocí typů .NET
Služba Media Services poskytuje rozhraní API rozhraní .NET, která můžete použít ke konfiguraci licencí Widevine. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Třídy definované v sadě Media Services .NET SDK
Následující třídy jsou definice těchto typů:

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

### <a name="example"></a>Příklad
Následující příklad ukazuje, jak používat rozhraní API ROZHRANÍ .NET ke konfiguraci jednoduché licence Widevine:

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

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Použití běžného dynamického šifrování PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md)

