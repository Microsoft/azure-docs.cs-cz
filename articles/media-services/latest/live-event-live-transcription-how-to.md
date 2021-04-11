---
title: Live přepis: Azure Media Services popis: informace o Azure Media Services živý přepis.  
služby: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' ' MS. Service: Media-Services MS. rebavování: Media ms.tgt_pltfrm: na MS. devlang: ne MS. kapitola: How-to MS. Date: 08/31/2020 MS. Author: inhenkel

---

# <a name="live-transcription-preview"></a>Živý přepis (Preview)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Služba Azure Media Service nabízí video, zvuk a text v různých protokolech. Když publikujete živý datový proud pomocí MPEG-POMLČKy nebo HLS/CMAF, pak společně s videem a zvukem doručí naše služba přepisu text v IMSC 1.1 kompatibilní TTML. Doručení se zabalí do fragmentů MPEG-4 Part 30 (ISO/IEC 14496-30). Pokud používáte doručování přes HLS/TS, text se doručí jako VTTý v bloku.

Pokud je živý přepis zapnutý, platí další poplatky. Podívejte se prosím na informace o cenách v části Live video [stránky s cenami Media Services](https://azure.microsoft.com/pricing/details/media-services/).

Tento článek popisuje, jak povolit živý přepis při streamování živé události pomocí Azure Media Services. Než budete pokračovat, ujistěte se, že jste obeznámeni s používáním rozhraní REST API pro Media Services V3 (podrobnosti najdete v [tomto kurzu](stream-files-tutorial-with-rest.md) ). Měli byste se také seznámit s konceptem [živého streamování](stream-live-streaming-concept.md) . Při Media Services kurzu se doporučuje dokončit [Stream živě](stream-live-tutorial-with-api.md) .

## <a name="live-transcription-preview-regions-and-languages"></a>Live Preview – oblasti a jazyky

Živý přepis je k dispozici v následujících oblastech:

- Southeast Asia
- West Europe
- Severní Evropa
- East US
- USA – střed
- Středojižní USA
- Západní USA 2
- Brazílie – jih

Toto je seznam dostupných jazyků, které je možné přepisu, pomocí kódu jazyka v rozhraní API.

| Jazyk | Kód jazyka |
| -------- | ------------- |
| Katalánština  | ca-ES |
| dánština (Dánsko) | da-DK |
| němčina (Německo) | de-DE |
| Angličtina (Austrálie) | EN-AU |
| Angličtina (Kanada) | en-CA |
| Angličtina (Spojené království) | en-GB |
| Angličtina (Indie) | en-IN |
| Angličtina (Nový Zéland) | EN-NZ |
| Angličtina (Spojené státy) | en-US |
| Španělština (Španělsko) | es-ES |
| Španělština (Mexiko) | ES – MX |
| finština (Finsko) | fi-FI |
| Francouzština (Kanada) | fr – CA |
| francouzština (Francie) | fr-FR |
| italština (Itálie) | it-IT |
| nizozemština (Nizozemsko) | nl-NL |
| Portugalština (Brazílie) | pt-BR |
| portugalština (Portugalsko) | pt-PT |
| švédština (Švédsko) | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>Vytvoření živé události pomocí živého přepisu

Chcete-li vytvořit živou událost se zapnutým přepisem, odešlete operaci PUT pomocí rozhraní API verze 2019-05-01-Preview, například:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

Operace má následující text (kde se vytvoří předávací živá událost s RTMP jako protokol ingestování). Všimněte si přidání vlastnosti přepisy.

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions",
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "useStaticHostname": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>Spustit nebo zastavit přepis po zahájení aktivní události

Můžete spustit a zastavit živý přepis, zatímco živá událost je ve spuštěném stavu. Další informace o spouštění a zastavování živých událostí najdete v části dlouhotrvající operace v tématu [vývoj s rozhraními api Media Services V3](media-services-apis-overview.md#long-running-operations).

Pokud chcete zapnout funkci Live přepisu nebo aktualizovat jazyk přepisu, opravte živou událost tak, aby zahrnovala vlastnost "přepisy". Chcete-li vypnout živé přepisy, odeberte vlastnost "přepisy" z objektu živé události.  

> [!NOTE]
> Zapnutí nebo vypnutí přepisu **více než jednou** během živé události není podporovaným scénářem.

Toto je ukázkové volání pro zapnutí živých přepisů.

POUŽITA ```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions", 
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "useStaticHostname": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="transcription-delivery-and-playback"></a>Přepisovat doručování a přehrávání

Přečtěte si článek [Přehled dynamického balení](encode-dynamic-packaging-concept.md#to-prepare-your-source-files-for-delivery) , jak naše služba používá dynamické balení k doručování videa, zvuku a textu v různých protokolech. Když publikujete živý datový proud pomocí MPEG-POMLČKy nebo HLS/CMAF, pak společně s videem a zvukem doručí naše služba přepisu text v IMSC 1.1 kompatibilní TTML. Toto doručení se zabalí do fragmentů MPEG-4 Part 30 (ISO/IEC 14496-30). Pokud používáte doručování přes HLS/TS, text se doručuje jako VTT v bloku. K přehrání datového proudu můžete použít webový přehrávač, například [Azure Media Player](player-use-azure-media-player-how-to.md) .  

> [!NOTE]
> Pokud používáte Azure Media Player, použijte verzi 2.3.3 nebo novější.

## <a name="known-issues"></a>Známé problémy

Pro verzi Preview jsou zde známé problémy s živým přepisem:

- Aplikace musí používat rozhraní API verze Preview, které jsou popsané ve [specifikaci Media Services V3 openapi Specification](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
- Ochrana DRM (Správa digitálních práv) se nevztahuje na textovou stopu, je možné pouze šifrování obálek AES.

## <a name="next-steps"></a>Další kroky

* [Přehled Media Services](media-services-overview.md)
