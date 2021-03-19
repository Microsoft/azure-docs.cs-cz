---
title: Azure Media Services kódy chyb živé události
description: V tomto článku jsou uvedeny kódy chyb živé události.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 02/12/2020
ms.author: inhenkel
ms.openlocfilehash: 5463f1d8376cbe1a6e81d17c1f95a84e67f3b418
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581078"
---
# <a name="media-services-live-event-error-codes"></a>Media Services kódy chyb živé události

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V následujících tabulkách jsou uvedeny kódy chyb [živé události](live-events-outputs-concept.md) .

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Když se přihlásíte k odběru události [Event Grid](../../event-grid/index.yml) pro živou událost, může se zobrazit jedna z následujících chyb z události [LiveEventConnectionRejected](monitoring/media-services-event-schemas.md\#liveeventconnectionrejected) .
> [!div class="mx-tdCol2BreakAll"]
>| Chyba | Informace |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|Description | Nesprávná adresa URL pro příjem |
>|Navrhované řešení| APPID je token GUID v adrese URL ingestování RTMP. Ujistěte se, že se shoduje s adresou URL pro příjem z rozhraní API. |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| Description |V seznamu konfigurovaných povolených IP adres není kodér k dispozici. |
>| Navrhované řešení| Ujistěte se, že je v seznamu povolených IP adres kodéru. K nastavení správné hodnoty použijte online nástroj, jako je *whoismyip* nebo *Kalkulačka CIDR* .  Zajistěte, aby kodér mohl kontaktovat server před skutečnou živou událostí. |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| Description|Kodér RTMP neodeslal `setDataFrame` příkaz. |
>| Navrhované řešení|Většina komerčních kodérů odesílá metadata datového proudu. V případě kodéru, který nabízí příjem jedné přenosové rychlosti, to nemusí být problém. Livestream je schopný vypočítat příchozí přenos dat, když metadata streamu chybí.  Pro ingestování s více přenosovými rychlostmi pro PassThru kanál nebo scénář dvojitého navýšení se můžete pokusit připojit řetězec dotazu pomocí ' videodatarate ' a ' audiodatarate ' v adrese URL ingestování. Je možné, že bude fungovat přibližná hodnota. Jednotka je v kbit. Například  `rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| Description|Zadaný kodek není podporován.|
>| Navrhované řešení| Livestream obdržel nepodporovaný kodek. Třeba ingestování RTMP Livestream přijalo video kodek bez AVC.  Ověřte předvolby kodéru. |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| Description |Předtím, než se doručí skutečná data médií, se informace o popisu média nepřijaly. |
>| Navrhované řešení|Livestream neobdrží z kodéru popis datového proudu (značka header nebo FLV). Toto je narušení protokolu. Obraťte se na dodavatele kodéru. |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| Description|Počet kvality pro typ zvuku nebo videa překročil maximální povolený limit. |
>| Navrhované řešení|Když je režim živé události Live Encoding, kodér by měl vložit jednu přenosovou rychlost videa a zvuku.  Mějte na paměti, že je povolen nadbytečný nabízený zápis ze stejné přenosové rychlosti. Zkontrolujte přednastavený nebo výstupní nastavení kodéru a ujistěte se, že výstup vytvoří datový proud s jednou přenosovou rychlostí. |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| Description|Celkový počet příchozích přenosů v živé události nebo službě kanálu překročil maximální povolený limit. |
>| Navrhované řešení|Kodér překročil maximální příchozí přenosovou rychlost. Toto omezení agreguje všechna příchozí data z přispívajícího kodéru. Ověřte nastavení předvolby kodéru nebo výstupní nastavení, abyste snížili přenosovou rychlost. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| Description|Časové razítko pro video nebo audio FLVTag je z kodéru RTMP neplatné. |
>| Navrhované řešení|Zastaralé |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| Description|Příchozí kodér ingestující datové proudy s kmitočty snímků překročil maximální povolený počet 30 snímků pro kódování živých událostí nebo kanálů. |
>| Navrhované řešení|Zaškrtněte předvolby kodéru a snižte kmitočet snímků na 36 FPS. |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| Description|Příchozí kanály ingestující kodér překročily následující povolená řešení: 1920x1088 pro kódování živých událostí a kanálů a 4096 x 2160 pro předávací živé události a kanály. |
>| Navrhované řešení|Ověřte předvolby kodéru, aby se snížilo rozlišení videa, aby nepřekročilo limit. |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| Description|Živá událost dostala velké množství zvukových dat najednou nebo velké množství dat videa bez jakýchkoli klíčových snímků. Kodér jsme odpojili, abychom mu pomohli opakovat se správnými daty. |
>| Navrhované řešení|Zajistěte, aby kodér odesílal klíčový snímek pro každý interval klíčových snímků (skupinu GOP).  Povolte nastavení jako "konstantní přenosová rychlost (CBR)" nebo "Zarovnat klíčové snímky". V některých případech může pomocník pro nastavování, který přispívá, obnovit. Pokud to nepomůže, obraťte se na dodavatele kodéru. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

V události [LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md\#liveeventencoderdisconnected) se může zobrazit jedna z následujících chyb.

> [!div class="mx-tdCol2BreakAll"]
>| Chyba | Informace |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| Description|Platnost relace RTMP vypršela po nečinnosti pro povolený časový limit. |
>|Navrhované řešení|K tomu obvykle dochází v případě, že kodér přestane přijímat vstupní kanál, takže relace se stane nečinným, protože nejsou k dispozici žádná data pro vložení. Ověřte, zda je stav kodéru nebo vstupního kanálu v dobrém stavu. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|Description| Časové razítko pro video nebo audio FLVTag je od kodéru RTMP neplatné. |
>| Navrhované řešení| Zastaralé |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| Description|Kodér odesílá data příliš rychle. |
>| Navrhované řešení|K tomu dojde v případě, že kodér v krátké době rozpíná velkou sadu fragmentů.  K tomu může dojít v případě, že kodér nemůže doručovat data z důvodu potíží se sítí a shluky vyčerpá data v případě, že je síť k dispozici. Najděte důvod z protokolu kodéru nebo systémového protokolu. |
>|**Neznámé kódy chyb** |
>| Description| Tyto kódy chyb můžou být v rozsahu od chyby paměti až po duplicitní položky v mapě algoritmu hash. |

## <a name="other-error-codes"></a>Jiné kódy chyb

> [!div class="mx-tdCol2BreakAll"]
>| Chyba | Informace |Odmítnutá/odpojená událost|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||Yes|
>| Description|Toto je obecná chyba. ||
>|Navrhované řešení| Žádné||
>|**MPI_SYSTEM_MAINTENANCE** ||Yes|
>| Description|Kodér byl odpojen z důvodu aktualizace služby nebo údržby systému. ||
>|Navrhované řešení|Ujistěte se, že kodér povoluje automatické připojení. Toto je funkce kodéru pro obnovení neočekávaného odpojení relace. ||
>|**MPE_BAD_URL_SYNTAX** ||Yes|
>| Description|Adresa URL ingestování je nesprávně naformátovaná. ||
>|Navrhované řešení|Ujistěte se, že adresa URL ingestování je správně naformátovaná. Pro RTMP by měl být `rtmp[s]://hostname:port/live/GUID_APPID/streamname` ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||Yes|
>| Description|Kodér odpojil relaci.  ||
>|Navrhované řešení|Nejedná se o chybu. Jedná se o případ, kdy kodér zahájil odpojení, včetně bezproblémového odpojení. Pokud se jedná o neočekávané odpojení, podívejte se do protokolu kodéru nebo systémového protokolu. |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||Ne|
>| Popis|Míra příchozích dat se neshoduje s očekávanou přenosovou rychlostí. ||
>|Navrhované řešení|Toto je upozornění, které se stane, když je rychlost příchozího přenosu dat příliš nízká nebo rychlá. Ověřte protokol kodéru nebo systémový protokol.||
>|**MPE_INGEST_DISCONTINUITY** ||Ne|
>| Popis| V příchozích datech se discontinuty.||
>|Navrhované řešení| Toto upozornění znamená, že kodér odmítá data z důvodu potíží se sítí nebo systémových prostředků. Ověřte protokol kodéru nebo systémový protokol. Monitorujte také systémový prostředek (procesor, paměť nebo síť). Pokud je systémový procesor příliš vysoký, zkuste snížit přenosovou rychlost nebo použít možnost kodéru H/W ze systémové grafické karty.||

## <a name="see-also"></a>Viz také

[Kódy chyb koncového bodu streamování (Origin)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Další kroky

[Kurz: živé streamování pomocí Media Services](stream-live-tutorial-with-api.md)
