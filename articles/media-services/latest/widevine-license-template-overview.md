---
title: Azure Media Services s technologií Widevine licence přehled šablony | Microsoft Docs
description: Toto téma poskytuje přehled o šablonu licence Widevine, který slouží ke konfiguraci licence na Widevine.
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: juliako
ms.openlocfilehash: e3af5efd253458401c13f6174d9567f932482eb0
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132853"
---
# <a name="widevine-license-template-overview"></a>Přehled šablonu licence Widevine

Azure Media Services můžete použít ke konfiguraci a požadují licence Google Widevine. Když přehrávač pokusí o přehrání obsahu chráněného Widevine, žádost o posílá službu doručování licencí jak licenci získat. Pokud licenční služby schvalovat žádosti, služba vydává licence. To se může odeslat klientovi a slouží k dešifrování- and -play zadaný obsah.

Žádost o licenci Widevine je naformátován jako zprávu JSON.  

>[!NOTE]
> Prázdná zpráva můžete vytvořit pomocí žádné hodnoty právě "{}." Šablona licence je vytvořen s výchozím nastavením. Výchozí hodnota se dá použít na většině případů. Na základě Microsoft licence doručení scénáře měli vždycky používat výchozí hodnoty. Pokud potřebujete nastavit "zprostředkovatele" a "content_id" hodnoty, zprostředkovatele odpovídat přihlašovacím údajům Widevine.

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
| policy_overrides&#46;can_play |Logická hodnota PRAVDA nebo NEPRAVDA |Určuje, že přehrávání obsahu je povolen. Výchozí hodnota je false. |
| policy_overrides&#46;can_persist |Logická hodnota PRAVDA nebo NEPRAVDA |Označuje, že může uložit trvale licence na stálé úložiště pro použití v offline režimu. Výchozí hodnota je false. |
| policy_overrides&#46;can_renew |Logická hodnota PRAVDA nebo NEPRAVDA |Označuje, zda je povoleno obnovení tuto licenci. V případě hodnoty true platnost licence, které lze rozšířit pomocí prezenčního signálu. Výchozí hodnota je false. |
| policy_overrides&#46;license_duration_seconds |int64 |Určuje časový interval pro tuto konkrétní licenci. Hodnota 0 značí, že neexistuje žádné omezení doba trvání. Výchozí hodnota je 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Určuje časový interval, při přehrávání je povoleno. Hodnota 0 značí, že neexistuje žádné omezení doba trvání. Výchozí hodnota je 0. |
| policy_overrides&#46;playback_duration_seconds |int64 |Okno zobrazení času po zahájení v rámci platnost licence. Hodnota 0 značí, že neexistuje žádné omezení doba trvání. Výchozí hodnota je 0. |
| policy_overrides&#46;renewal_server_url |řetězec |Všechny požadavky prezenčního signálu (obnovení) pro tuto licenci jsou směrované na zadanou adresu URL. V tomto poli se používá pouze v případě, že can_renew má hodnotu true. |
| policy_overrides&#46;renewal_delay_seconds |int64 |Kolik sekund po license_start_time před první pokus o obnovení. V tomto poli se používá pouze v případě, že can_renew má hodnotu true. Výchozí hodnota je 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |Určuje zpoždění v sekundách mezi požadavky na obnovení certifikátu další licence, v případě selhání. V tomto poli se používá pouze v případě, že can_renew má hodnotu true. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |Okno čas, ve které přehrávání můžete pokračovat, když dojde k pokusu o obnovení, ale neúspěšný kvůli problémům s back-end s licenční server. Hodnota 0 značí, že neexistuje žádné omezení doba trvání. V tomto poli se používá pouze v případě, že can_renew má hodnotu true. |
| policy_overrides&#46;renew_with_usage |Logická hodnota PRAVDA nebo NEPRAVDA |Označuje, že licence, které je odesláno pro obnovení, při využití spuštění. V tomto poli se používá pouze v případě, že can_renew má hodnotu true. |

## <a name="session-initialization"></a>Inicializace relace
| Název | Hodnota | Popis |
| --- | --- | --- |
| provider_session_token |Řetězec s kódováním base64 |Tento token relace je předán zpět v licenci a v dalších prodloužení existuje. Token relace není zachována nad rámec relací. |
| provider_client_token |Řetězec s kódováním base64 |Token klienta odeslat zpět v reakci licence. Pokud požadavek na licenční obsahuje token klienta, je tato hodnota ignorována. Token klienta trvá nad rámec relace licence. |
| override_provider_client_token |Logická hodnota PRAVDA nebo NEPRAVDA |Obsahuje-li hodnotu false a požadavek na licenční token klienta, použití tokenu z požadavku i v případě, že token klienta byla zadána v této struktuře. V případě hodnoty true vždy používejte uveden v této struktuře token. |

## <a name="configure-your-widevine-license-with-net"></a>Konfigurace licence Widevine pomocí rozhraní .NET 

Služba Media Services poskytuje třídu, která vám umožní nakonfigurovat licenci Widevine. Chcete-li vytvořit licence, předat JSON do [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Konfigurace šablony můžete:

1.  Přímo konstrukce/závislé řetězce formátu JSON (které mohou být náchylné);

    ```csharp
        ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
    };
    ```

2.  Konstrukce potřebné třídy s vlastnostmi mapování těchto atributů JSON a doložit je před jejich serializaci do řetězce formátu JSON. Níže je příklad těchto tříd a jak jsou vytvořeny a serializovat.

    ```csharp
    public class policy_overrides
    {
        public bool can_play { get; set; }
        public bool can_persist { get; set; }
        public bool can_renew { get; set; }
        public int rental_duration_seconds { get; set; }    //Indicates the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
        public int playback_duration_seconds { get; set; }  //The viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
        public int license_duration_seconds { get; set; }   //Indicates the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
    }

    public class content_key_spec
    {
        public string track_type { get; set; }
        public int security_level { get; set; }
        public output_protection required_output_protection { get; set; }
    }

    public class output_protection
    {
        public string hdcp { get; set; }
    }

    public class widevine_template
    {
        public string allowed_track_types { get; set; }
        public content_key_spec[] content_key_specs { get; set; }
        public policy_overrides policy_overrides { get; set; }
    }
    ```

### <a name="configure-the-license"></a>Konfigurace licence

Použít k vytvoření formátu JSON, který slouží ke konfiguraci třídy definované v předchozí části [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

```csharp
void ConfigureLicense()
{
    widevine_template objwidevine_template = new widevine_template()
    {
        allowed_track_types = "SD_HD",
        content_key_specs = new content_key_spec[]
        {
            new content_key_spec()
            {
                track_type = "SD",
                security_level = 1,
                required_output_protection = new output_protection()
                {
                hdcp = "HDCP_V2"
                }
            }
        },
        policy_overrides = new policy_overrides()
        {
            can_play = true,
            can_persist = true,
            can_renew = false,
            license_duration_seconds = 2592000,
            playback_duration_seconds = 10800,
            rental_duration_seconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(objwidevine_template)
    };
}
```

## <a name="next-steps"></a>Další postup

[Přehled](content-protection-overview.md)