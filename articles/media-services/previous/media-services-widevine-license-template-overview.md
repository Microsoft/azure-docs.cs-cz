---
title: Přehled šablon licencování Widevine | Dokumentace Microsoftu
description: Toto téma obsahuje základní informace o šabloně licence Widevine, který se používá ke konfiguraci licencí Widevine.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 4b5196a995576e5b00a988e14183eb720d5b2eae
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55989852"
---
# <a name="widevine-license-template-overview"></a>Přehled šablon licencování Widevine 
Azure Media Services můžete použít ke konfiguraci a o licence Google Widevine. Když hráč se snaží přehrávání obsahu chráněného technologií Widevine, žádost přijde na službu doručování licencí licenci. Pokud licenční služby schválí, služba vydá licence. Je odeslat klientovi a slouží k dešifrování a přehrát zadaný obsah.

Žádost o licenci Widevine je formátován jako zprávy JSON.  

>[!NOTE]
> Prázdná zpráva s žádné hodnoty, můžete vytvořit pouze "{}." Potom šablonu licence se vytvoří s výchozí hodnoty. Výchozí hodnota vyhovuje většině případů. Scénáře založené na Microsoft-naším vlastním doručováním licencí používejte vždy výchozí hodnoty. Pokud je potřeba nastavit "provider" a "content_id" hodnoty, zprostředkovatele musí odpovídat přihlašovacím údajům Widevine.

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
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
Pokud existuje stávající zásady, není nutné zadat jakoukoliv hodnotu v specifikace klíče obsahu. Dříve existující zásady přidružené k tomuto obsahu se používá k určení ochrana výstupu, jako je například velkou šířkou pásma digitální obsah ochrany (HDCP) a kopie systému pro obecné správy (CGMS). Pokud dříve existující zásady není zaregistrován u serveru licencí Widevine, můžete poskytovateli obsahu vložení hodnoty do žádosti o licenci.   

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
| policy_overrides. can_play |Logická hodnota true nebo false |Určuje povolené přehrávání obsahu. Výchozí hodnota je false. |
| policy_overrides. can_persist |Logická hodnota true nebo false |Označuje, že může uložit trvale licence na stálé úložiště pro použití v offline režimu. Výchozí hodnota je false. |
| policy_overrides. can_renew |Logická hodnota true nebo false |Označuje, že je povoleno obnovení této licence. Při hodnotě true je možné rozšířit trvání licence k podle prezenčního signálu. Výchozí hodnota je false. |
| policy_overrides. license_duration_seconds |int64 |Určuje časový interval pro tuto konkrétní licenci. Hodnota 0 označuje, že neexistuje žádné omezení na délku. Výchozí hodnota je 0. |
| policy_overrides. rental_duration_seconds |int64 |Určuje časový interval během přehrávání je povolené. Hodnota 0 označuje, že neexistuje žádné omezení na délku. Výchozí hodnota je 0. |
| policy_overrides. playback_duration_seconds |int64 |Okno zobrazení času po zahájení v rámci doby trvání licence. Hodnota 0 označuje, že neexistuje žádné omezení na délku. Výchozí hodnota je 0. |
| policy_overrides. renewal_server_url |řetězec |Všechny požadavky prezenčního signálu (prodloužení) pro tuto licenci jsou směrované na zadané adrese URL. Toto pole se používá pouze v případě, že can_renew má hodnotu true. |
| policy_overrides. renewal_delay_seconds |int64 |Počet sekund po license_start_time před prvním pokusu o obnovení. Toto pole se používá pouze v případě, že can_renew má hodnotu true. Výchozí hodnota je 0. |
| policy_overrides. renewal_retry_interval_seconds |int64 |Určuje zpoždění v sekundách mezi požadavky na obnovení certifikátu další licence, v případě selhání. Toto pole se používá pouze v případě, že can_renew má hodnotu true. |
| policy_overrides. renewal_recovery_duration_seconds |int64 |Okno čas, ve které přehrávání může pokračovat, když dojde k pokusu o obnovení, ale neúspěšné kvůli problémům s back-end s licenční server. Hodnota 0 označuje, že neexistuje žádné omezení na délku. Toto pole se používá pouze v případě, že can_renew má hodnotu true. |
| policy_overrides. renew_with_usage |Logická hodnota true nebo false |Označuje, že licence, které je odesláno pro prodloužení platnosti při spuštění využití. Toto pole se používá pouze v případě, že can_renew má hodnotu true. |

## <a name="session-initialization"></a>Inicializace relace
| Název | Hodnota | Popis |
| --- | --- | --- |
| provider_session_token |Řetězec s kódováním base64 |Tento token relace je předána zpět v licenci a existuje v následné obnovení. Token relace není zachována po relace. |
| provider_client_token |Řetězec s kódováním base64 |Token klienta k odesílání zpět v odpovědi licence. Pokud požadavek na licenční obsahuje token klienta, tato hodnota je ignorována. Token klienta potrvá déle než relace licence. |
| override_provider_client_token |Logická hodnota true nebo false |Obsahuje-li hodnotu false a požadavek na licenční token klienta, použijte token z požadavku i v případě, že token klienta byla zadána v této struktuře. Při hodnotě true se vždycky používejte token zadaný v této struktuře. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Nakonfigurujte své licence Widevine s použitím typů .NET
Služba Media Services poskytuje rozhraní API pro .NET, který vám pomůže a nakonfigurujte své licence Widevine. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Třídy definované v sadě Media Services .NET SDK
Následující třídy jsou tyto typy definice:

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

### <a name="example"></a>Příklad:
Následující příklad ukazuje, jak pomocí rozhraní API .NET můžete nakonfigurovat jednoduchou licencí Widevine:

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


## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Další informace najdete v tématech
[Použití běžného dynamického šifrování PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md)

