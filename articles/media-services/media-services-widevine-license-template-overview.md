---
title: Přehled šablonu licence Widevine | Microsoft Docs
description: Toto téma poskytuje přehled o šablonu licence Widevine, který slouží ke konfiguraci licence na Widevine.
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 85de5765975b0c55fafe9bb4c14a1c1f435a6d5c
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2018
ms.locfileid: "27742894"
---
# <a name="widevine-license-template-overview"></a>Přehled šablonu licence Widevine
Azure Media Services můžete použít ke konfiguraci a požadují licence Google Widevine. Když přehrávač pokusí o přehrání obsahu chráněného Widevine, žádost o posílá službu doručování licencí jak licenci získat. Pokud licenční služby schvalovat žádosti, služba vydává licence. To se může odeslat klientovi a slouží k dešifrování- and -play zadaný obsah.

Žádost o licenci Widevine je naformátován jako zprávu JSON.  

>[!NOTE]
> Můžete vytvořit prázdnou zprávu s žádné hodnoty, jednoduše "{}." Šablona licence je vytvořen s výchozím nastavením. Výchozí hodnota se dá použít na většině případů. Na základě Microsoft licence doručení scénáře měli vždycky používat výchozí hodnoty. Pokud potřebujete nastavit "zprostředkovatele" a "content_id" hodnoty, zprostředkovatele odpovídat přihlašovacím údajům Widevine.

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

## <a name="json-message"></a>Zpráva JSON
| Název | Hodnota | Popis |
| --- | --- | --- |
| datová část |Řetězec s kódováním base64 |Licence požadavek odeslaný klientem. |
| content_id |Řetězec s kódováním base64 |Identifikátor použít k odvození klíče ID a obsah klíče pro každý content_key_specs.track_type. |
| Zprostředkovatel |řetězec |Použít k vyhledání obsahu klíče a zásady. Pokud se doručení klíče Microsoft používá pro doručování licence Widevine, tento parametr je ignorován. |
| název_zásad |řetězec |Název zásady dříve zaregistrovaný. Volitelné. |
| allowed_track_types |výčet |SD_ONLY nebo SD_HD. Ovládací prvky, které obsahu klíče jsou součástí licenci. |
| content_key_specs |Pole JSON struktury, najdete v části "Obsahu klíče specifikací."  |Citlivější ovládacího prvku na které obsahu klíče vrátit. Další informace najdete v části "Obsahu klíče specifikací." Lze zadat pouze jednu z hodnot allowed_track_types a content_key_specs. |
| use_policy_overrides_exclusively |Logická hodnota PRAVDA nebo NEPRAVDA |Pomocí určeného policy_overrides atributy zásad a vynechejte všechny dříve uložené zásad. |
| policy_overrides |JSON struktury, najdete v části "Zásady přepsání." |Nastavení zásad pro tuto licenci.  V případě, že tento prostředek má předdefinované zásady, se používají tyto zadané hodnoty. |
| session_init |JSON struktury, najdete v části "Inicializace relace." |Volitelná data jsou předána licence. |
| parse_only |Logická hodnota PRAVDA nebo NEPRAVDA |Požadavek na licenční je analyzována, ale je vydán žádná licence. Hodnoty z požadavku licence jsou však vráceny v odpovědi. |

## <a name="content-key-specs"></a>Obsahu specifikace klíče
Pokud existuje dříve existující zásady, není třeba chcete zadat některé z hodnot v specifikace klíče obsahu. Dříve existující zásady, které jsou přidružené k tomuto obsahu slouží k určení ochrany výstup, jako je například digitálního obsahu velkou šířku pásma ochrany (HDCP) a kopie systému pro obecné správy (CGMS). Pokud se serverem licence Widevine není zaregistrován dříve existující zásady, poskytovateli obsahu můžete vložit hodnoty do žádosti o licenci.   

Pro všechny sleduje, bez ohledu na to možnost use_policy_overrides_exclusively musí být zadána hodnota každý content_key_specs. 

| Název | Hodnota | Popis |
| --- | --- | --- |
| content_key_specs. track_type |řetězec |Název typu sledování. Pokud content_key_specs je zadané v žádosti o licenci, nezapomeňte určit, že všechny typy sledovat explicitně. Tak neučiníte způsobuje selhání k přehrání po 10 sekund. |
| content_key_specs  <br/> security_level |UInt32 |Definuje požadavky na klienta odolnosti pro přehrávání. <br/> -Softwarový kryptografie prázdné pole je povinný. <br/> -Vyžaduje se šifrování software a zakódovaná decoder. <br/> -Materiálu a kryptografické operace s klíčem musí provést v rámci podporovaný hardware důvěryhodné prováděcí prostředí. <br/> -Kryptografie a dekódování obsahu, je nutné provést v rámci podporovaný hardware důvěryhodné spouštěcí prostředí.  <br/> -Kryptografie, dekódování a všechny zpracování média (komprimovaným a nekomprimovaným) musí být zpracovávaných v rámci podporovaný hardware důvěryhodné spouštěcí prostředí. |
| content_key_specs <br/> required_output_protection.hDC |řetězec, jeden z HDCP_V2 HDCP_NONE, HDCP_V1, |Určuje, zda HDCP je povinný. |
| content_key_specs <br/>key |Base64-<br/>řetězec s kódováním |Klíč obsahu používat tento stopy. -Li zadána, je zapotřebí track_type nebo key_id. Poskytovateli obsahu můžete použít tuto možnost vložení klíč k obsahu pro toto sledování místo licenční server Widevine Generovat nebo vyhledat klíč. |
| content_key_specs.key_id |Řetězec s kódováním base64 binární, 16 bajtů |Jedinečný identifikátor pro klíč. |

## <a name="policy-overrides"></a>Přepsání zásad
| Název | Hodnota | Popis |
| --- | --- | --- |
| policy_overrides. can_play |Logická hodnota PRAVDA nebo NEPRAVDA |Určuje, že přehrávání obsahu je povolen. Výchozí hodnota je false. |
| policy_overrides. can_persist |Logická hodnota PRAVDA nebo NEPRAVDA |Označuje, že může uložit trvale licence na stálé úložiště pro použití v offline režimu. Výchozí hodnota je false. |
| policy_overrides. can_renew |Logická hodnota PRAVDA nebo NEPRAVDA |Označuje, zda je povoleno obnovení tuto licenci. V případě hodnoty true platnost licence, které lze rozšířit pomocí prezenčního signálu. Výchozí hodnota je false. |
| policy_overrides. license_duration_seconds |Int64 |Určuje časový interval pro tuto konkrétní licenci. Hodnota 0 značí, že neexistuje žádné omezení doba trvání. Výchozí hodnota je 0. |
| policy_overrides. rental_duration_seconds |Int64 |Určuje časový interval, při přehrávání je povoleno. Hodnota 0 značí, že neexistuje žádné omezení doba trvání. Výchozí hodnota je 0. |
| policy_overrides. playback_duration_seconds |Int64 |Okno zobrazení času po zahájení v rámci platnost licence. Hodnota 0 značí, že neexistuje žádné omezení doba trvání. Výchozí hodnota je 0. |
| policy_overrides. renewal_server_url |řetězec |Všechny požadavky prezenčního signálu (obnovení) pro tuto licenci jsou směrované na zadanou adresu URL. V tomto poli se používá pouze v případě, že can_renew má hodnotu true. |
| policy_overrides. renewal_delay_seconds |Int64 |Kolik sekund po license_start_time před první pokus o obnovení. V tomto poli se používá pouze v případě, že can_renew má hodnotu true. Výchozí hodnota je 0. |
| policy_overrides. renewal_retry_interval_seconds |Int64 |Určuje zpoždění v sekundách mezi požadavky na obnovení certifikátu další licence, v případě selhání. V tomto poli se používá pouze v případě, že can_renew má hodnotu true. |
| policy_overrides. renewal_recovery_duration_seconds |Int64 |Okno čas, ve které přehrávání můžete pokračovat, když dojde k pokusu o obnovení, ale neúspěšný kvůli problémům s back-end s licenční server. Hodnota 0 značí, že neexistuje žádné omezení doba trvání. V tomto poli se používá pouze v případě, že can_renew má hodnotu true. |
| policy_overrides. renew_with_usage |Logická hodnota PRAVDA nebo NEPRAVDA |Označuje, že licence, které je odesláno pro obnovení, při využití spuštění. V tomto poli se používá pouze v případě, že can_renew má hodnotu true. |

## <a name="session-initialization"></a>Inicializace relace
| Název | Hodnota | Popis |
| --- | --- | --- |
| provider_session_token |Řetězec s kódováním base64 |Tento token relace je předán zpět v licenci a v dalších prodloužení existuje. Token relace není zachována nad rámec relací. |
| provider_client_token |Řetězec s kódováním base64 |Token klienta odeslat zpět v reakci licence. Pokud požadavek na licenční obsahuje token klienta, je tato hodnota ignorována. Token klienta trvá nad rámec relace licence. |
| override_provider_client_token |Logická hodnota PRAVDA nebo NEPRAVDA |Obsahuje-li hodnotu false a požadavek na licenční token klienta, použití tokenu z požadavku i v případě, že token klienta byla zadána v této struktuře. V případě hodnoty true vždy používejte uveden v této struktuře token. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Nakonfigurujte své licence Widevine pomocí typy .NET
Služba Media Services poskytuje rozhraní API technologie .NET, můžete použít a nakonfigurujte své licence Widevine. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Třídy definované v .NET SDK služby Media Services
Následující třídy jsou definice z těchto typů:

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
Následující příklad ukazuje, jak konfigurovat jednoduché licenci Widevine pomocí rozhraní API technologie .NET:

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
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Další informace najdete v tématech
[Použití dynamické běžné šifrování PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md)

