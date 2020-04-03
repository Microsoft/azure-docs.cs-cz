---
title: Chyby v balení a původu mediálních služeb Azure | Dokumenty společnosti Microsoft
description: Toto téma popisuje chyby, které můžete obdržet ze služby Azure Media Services Streaming Endpoint (Orgin).
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: 7d3a85e6fcc5b9d1c5ca1511cd7edd05ff5d9ae4
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582688"
---
# <a name="streaming-endpoint-origin-errors"></a>Chyby koncového bodu streamování (Origin) 

Toto téma popisuje chyby, které můžete obdržet ze služby Azure Media Services [Streaming Endpoint](streaming-endpoint-concept.md).

## <a name="400-bad-request"></a>400 Chybný požadavek

Požadavek obsahuje neplatné informace a je odmítnut s těmito kódy chyb a z jednoho z následujících důvodů:

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Syntaxe adresy URL nebo chyba formátu. Příklady zahrnují požadavky na neplatný typ, neplatný fragment nebo neplatnou stopu. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|Požadavek nemá v adrese URL žádnou šifrovací značku. Požadavky CMAF vyžadují šifrovací značku v adrese URL. Jiné protokoly, které jsou nakonfigurovány s více než jeden typ šifrování také vyžadují šifrovací značku pro rozdvojení. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x8089009|Požadavek na úložiště ke splnění požadavku se nezdařil s chybou chybného požadavku. |

## <a name="403-forbidden"></a>403 Zakázáno

Žádost není povolena z jednoho z následujících důvodů:

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|Požadavek na úložiště ke splnění požadavku se nezdařil o chybu ověřování. K tomu může dojít, pokud byly klíče úložiště otočeny a služba nemohla synchronizovat klíče úložiště. <br/><br/>Kontaktujte podporu Azure tak, že přejdete na [nápovědu + podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) na webu Azure Portal.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Chyba Operace úložiště, přístup se nezdařil z důvodu nedostatečných oprávnění účtu. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |Požadavek na úložiště ke splnění požadavku se nezdařil, protože účet úložiště je zakázán. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Chyba operace úložiště, přístup se nezdařil z důvodu obecných chyb. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |Výstupní formát je blokován z důvodu konfigurace v StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |Pro obsah je vyžadováno šifrování, pro výstupní formát je vyžadována zásada doručení. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Šifrování není nastaveno v nastavení zásad doručení. |

## <a name="404-not-found"></a>404 Nenalezeno

Operace se pokouší o činnost s prostředkem, který již neexistuje. Například prostředek již byl odstraněn.

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |Požadovaná stopa nebyla nalezena. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |Požadovaný prostředek nebyl nalezen. |
|MPE_UNAUTHORIZED |0x80890244 |Přístup je neoprávněný. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |Požadované časové razítko nebylo nalezeno. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Požadovaný filtr dynamického manifestu nebyl nalezen. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |Požadovaný index fragmentu je mimo platný rozsah. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Živé mediální položky nelze nalézt získat moov vyrovnávací paměti. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Nelze najít fragment v požadovaném čase pro konkrétní stopu.<br/><br/>Mohlo by to být, že fragment není ve skladu. Zkuste jinou vrstvu prezentace, která může mít fragment. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Nelze najít položku média pro požadovaný přenosový tok v manifestu. <br/><br/>Mohlo by to být, že hráč požádal o video stopu určitého toku, který nebyl v manifestu.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Nelze najít určitá metadata v manifestu nebo nelze najít rebase z úložiště. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Chyba Operace úložiště, prostředek nebyl nalezen. |

## <a name="409-conflict"></a>409 Konflikt

ID poskytnuté pro prostředek `PUT` nebo `POST` operace byla přijata existující prostředek. Tento problém vyřešit použijte jiné ID prostředku.

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Chyba operace úložiště, chyba konfliktu.  |

## <a name="410"></a>410

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Pro živé vysílání, když filtr, který má forceEndTimestamp nastavena na hodnotu true, počáteční nebo koncové časové razítko je mimo aktuální okno DVR.|

## <a name="412-precondition-failure"></a>412 Selhání předběžné podmínky

Operace zadaná eTag, která se liší od verze k dispozici na serveru, to znamená, že optimistická chyba souběžnosti. Opakujte požadavek po přečtení nejnovější verze prostředku a aktualizaci eTag na požádání.

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Požadovaný fragment není připraven.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900 EF|Chyba operace úložiště, selhání předběžné podmínky.|

## <a name="415-unsupported-media-type"></a>415 Typ nepodporovaného média

Formát datové části odeslaný klientem je v nepodporovaném formátu.

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Neměl by používat šifrování na již šifrovaný obsah.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |Šifrování je pro vstupní formát neplatné.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Typ zásad doručení je neplatný.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |Původní nastavení může být sdíleno více výstupními formáty.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Formát média nebo typ není podporován. Například Media Services nepodporuje počet úrovní kvality, který je starší 64 let. Ve značce videa FLV nepodporuje mediální služby snímek videa s více sps a více PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Vstupní formát požadovaného datového zdroje není podporován. Media Services podporuje formáty Smooth (live), MP4 (VoD) a Progressive download.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Požadovaný výstupní formát není podporován. Media Services podporuje formáty smooth, DASH(CSF, CMAF), HLS (v3, v4, CMAF) a progresivní formáty stahování.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Došlo k nepodporovanému typu šifrování.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Požadovaný typ média není ve výstupním formátu podporován. Podporované typy jsou video, audio nebo "SUBT" titulky.|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Zdrojové médium datových zdrojů bylo zakódováno formátem média, který není kompatibilní s výstupním formátem.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|Zdrojový datový zdroj byl kódován formátem videa, který není kompatibilní s výstupním formátem. H.264, AVC, H.265 (HEVC, hev1 nebo hvc1) jsou podporovány.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|Zdrojový datový zdroj byl zakódován zvukovým formátem, který není kompatibilní s výstupním formátem. Podporované zvukové formáty jsou AAC, E-AC3 (DD+), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|Zdrojchráněný datový zdroj nelze převést na výstupní formát.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Formát ochrany není podporován výstupním formátem.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Formát ochrany není podporován vstupním formátem.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Neplatná jednotka NAL videa, například pouze první NAL v ukázce může být AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Neplatná velikost jednotky NAL.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Neplatná hodnota délky jednotky NAL.|
|MPE_FILTER_INVALID|0x80890236|Neplatné filtry dynamického manifestu.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Neplatné nebo nepodporované verze filtrů.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Neplatný typ filtru.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Filtrem je určen neplatný rozsah.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Filtrem je určen neplatný atribut sledování.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Filtrem je určena neplatná délka okna prezentace.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Filtrem je určen neplatný live back off.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Pouze jeden prvek absTimeInHNS je podporován ve starších filtrech.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Po použití filtrů nejsou žádné další datové proudy.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Live back off je za oknem DVR.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Live back off je větší než okno prezentace.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Bylo překročeno deset (10) maximální povolené výchozí filtry.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Více násobné první video kvalitní operátor není povoleno v kombinovaných požadavků filtry.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Počet atributů první kvality datového toku musí být jeden (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|Trvání segmentu HLS musí být menší než jedna třetina okna DVR a HLS zpět.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Doba trvání fragmentu musí být menší nebo rovna přibližně 20 sekundám, jinak úrovně vstupní kvality nejsou zarovnány časem.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|DTS specifické chyby, nelze najít ReservedBox, když by měl být přítomen v DTSSpecficBox během analýzy pole DTS.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|Chyba specifická pro DTS, žádné kanály nalezené v DTSSpecficBox během analýzy pole DTS.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|Chyba specifická pro DTS, neshoda typu vzorku v dtsspecficboxu.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Chyba specifická pro DTS, je nastavena více datových zdrojů, ale neshoda typu vzorku DTSH.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Chyba specifická pro DTS, velikost datového proudu jádra je neplatná.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Chyba specifická pro DTS, rozlišení vzorku je neplatné.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Chyba specifická pro DTS, index rozšíření dílčího datového proudu je neplatný.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Chyba specifická pro DTS, číslo bloku dílčího datového proudu je neplatné.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Chyba specifická pro DTS, vzorkovací frekvence je neplatná.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|Chyba specifická pro DTS, kód referenčních hodin v rozšíření dílčího datového proudu je neplatný.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|Chyba specifická pro DTS, počet přemapovaných reproduktorů je neplatný.|

Šifrovací články a příklady najdete v tématu:

- [Koncept: ochrana obsahu](content-protection-overview.md)
- [Koncept: Klíčové zásady obsahu](content-key-policy-concept.md)
- [Koncept: Zásady streamování](streaming-policy-concept.md)
- [Ukázka: ochrana šifrováním AES](protect-with-aes128.md)
- [Ukázka: ochrana pomocí DRM](protect-with-drm.md)

Pokyny pro filtr naleznete v těchto tématech:

- [Koncept: dynamické manifesty](filters-dynamic-manifest-overview.md)
- [Koncept: filtry](filters-concept.md)
- [Ukázka: vytvoření filtrů s rest API](filters-dynamic-manifest-rest-howto.md)
- [Ukázka: vytvoření filtrů pomocí rozhraní .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Ukázka: vytvoření filtrů pomocí cli](filters-dynamic-manifest-cli-howto.md)

Živé články a vzorky naleznete v:

- [Koncept: přehled živého streamování](live-streaming-overview.md)
- [Koncept: Živé události a živé výstupy](live-events-outputs-concept.md)
- [Ukázka: živé streamování tutorial](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 Rozsah není satisfiable

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Chyba operace úložiště, vrácená chyba http 416, neplatný rozsah.|

## <a name="500-internal-server-error"></a>500 – Interní chyba serveru

Během zpracování požadavku media services narazí na některé chyby, které brání zpracování pokračovat.  

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Přijata a přeložena z Winhttp kód chyby ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Přijata a přeložena z Winhttp kód chyby ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Přijata a přeložena z Winhttp kód chyby ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Chyba operace úložiště, obecné InternalError jedné z chyb HTTP 500.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Chyba operace úložiště, obecná funkce OperationTimedOut jedné z chyb protokolu HTTP 500.|
|MPE_STORAGE_FAILURE|0x808900F2|Chyba operace úložiště, jiné chyby HTTP 500 než InternalError nebo OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 – Nedostupná služba

Server nyní nemůže přijímat požadavky. Tato chyba může být způsobena nadměrným požadavky na službu. Mechanismus omezení mediálních služeb omezuje využití prostředků pro aplikace, které poskytují nadměrné požadavky na službu.

> [!NOTE]
> Zkontrolujte chybovou zprávu a řetězec kódu chyby, abyste získali podrobnější informace o důvodu, proč se chyba 503. Tato chyba nemusí vždy znamenat omezení.
> 

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x8089008|Chyba Operace úložiště, přijatá chyba zaneprázdnění serveru HTTP 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="see-also"></a>Viz také

- [Kódy chyb kódování](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Koncepty Mediálních služeb Azure](concepts-overview.md)
- [Kvóty a omezení](limits-quotas-constraints.md)

## <a name="next-steps"></a>Další kroky

[Příklad: přístup k errorcode a zprávy z ApiException s rozhraním .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
