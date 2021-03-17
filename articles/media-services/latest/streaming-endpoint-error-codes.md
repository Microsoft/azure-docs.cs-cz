---
title: Azure Media Services balení a chyby původu
description: V tomto tématu jsou popsány chyby, které se mohou vysílat ze služby Azure Media Services (orgin) pro streaming Endpoint.
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
ms.date: 05/07/2019
ms.author: inhenkel
ms.openlocfilehash: 994e5ae0647f350e0a64f35318bd5803f4ed79b2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98894979"
---
# <a name="streaming-endpoint-origin-errors"></a>Chyby koncového bodu streamování (zdroj)

V tomto tématu jsou popsány chyby, které se mohou vysílat ze [služby Azure Media Services streaming Endpoint](streaming-endpoint-concept.md).

## <a name="400-bad-request"></a>400 – Chybný požadavek

Požadavek obsahuje neplatné informace a je odmítnut s těmito kódy chyb a z jednoho z následujících důvodů:

|Kód chyby|Hexadecimální hodnota |Popis chyby|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Syntaxe adresy URL nebo Chyba formátu. Příklady zahrnují požadavky na neplatný typ, neplatný fragment nebo neplatnou stopu. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|Požadavek nemá v adrese URL žádnou šifrovací značku. Požadavky CMAF vyžadují v adrese URL značku šifrování. Jiné protokoly, které jsou nakonfigurované s více než jedním typem šifrování, vyžadují také šifrovací značku pro nejednoznačnost. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|Požadavek na úložiště pro splnění žádosti se nezdařil s chybou požadavku. |

## <a name="403-forbidden"></a>403 – Zakázáno

Požadavek není povolen z některého z následujících důvodů:

|Kód chyby|Hexadecimální hodnota |Popis chyby|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|Požadavek na úložiště pro splnění žádosti se nezdařil s chybou ověřování. K tomu může dojít, když se klíče úložiště otočí a služba nemohla synchronizovat klíče úložiště. <br/><br/>Kontaktujte podporu Azure tak, že přejdete na [pomoc a podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) v Azure Portal.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Chyba operace úložiště, přístup se nezdařil z důvodu nedostatečného oprávnění účtu. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |Požadavek na úložiště pro splnění žádosti se nezdařil, protože účet úložiště je zakázán. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Chyba operace úložiště, přístup se nezdařil z důvodu obecných chyb. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |Výstupní formát je blokovaný kvůli konfiguraci v StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |Pro obsah se vyžaduje šifrování. pro výstupní formát se vyžadují zásady doručování. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |V nastavení zásad doručování není nastavené šifrování. |

## <a name="404-not-found"></a>404 Nenalezeno

Operace se pokouší jednat o prostředek, který již neexistuje. Prostředek mohl být například již odstraněn.

|Kód chyby|Hexadecimální hodnota |Popis chyby|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |Požadovaná stopa nebyla nalezena. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |Požadovaný prostředek nebyl nalezen. |
|MPE_UNAUTHORIZED |0x80890244 |Přístup je neautorizovaný. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |Požadované časové razítko nebylo nalezeno. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Požadovaný dynamický filtr manifestu nebyl nalezen. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |Požadovaný index fragmentu překračuje platný rozsah. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Nepodařilo se najít položky živého média, které by získaly vyrovnávací paměť Moov. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Nelze najít fragment v požadovaném čase konkrétního záznamu.<br/><br/>Je možné, že fragment není v úložišti. Vyzkoušejte jinou vrstvu prezentace, která může obsahovat fragment. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |V manifestu se nepovedlo najít záznam média pro požadovanou přenosovou rychlost. <br/><br/>Je možné, že hráč požádal o video o konkrétní přenosové rychlosti, která nebyla v manifestu.|
|MPE_METADATA_NOT_FOUND |0x80890257 |V manifestu se nepovedlo najít určitá metadata nebo nejde najít přenesení změn ze služby Storage. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Chyba operace úložiště, prostředek nebyl nalezen. |

## <a name="409-conflict"></a>409 – Konflikt

IDENTIFIKÁTOR poskytnutý pro prostředek v `PUT` operaci nebo byl `POST` proveden existujícím prostředkem. K vyřešení tohoto problému použijte jiné ID prostředku.

|Kód chyby|Hexadecimální hodnota |Popis chyby|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Chyba operace úložiště, chyba konfliktu.  |

## <a name="410"></a>410

|Kód chyby|Hexadecimální hodnota |Popis chyby|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|V případě živého streamování, když je filtr s forceEndTimestamp nastaven na hodnotu true, je časové razítko začátku nebo konce mimo aktuální okno DVR.|

## <a name="412-precondition-failure"></a>412 selhání předběžné podmínky

Operace určila eTag, která se liší od verze dostupné na serveru, což je optimistická chyba souběžnosti. Po načtení nejnovější verze prostředku a aktualizaci značky ETag v požadavku zkuste požadavek zopakovat.

|Kód chyby|Hexadecimální hodnota |Popis chyby|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Požadovaný fragment není připravený.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Chyba operace úložiště, předběžná podmínka se nezdařila.|

## <a name="415-unsupported-media-type"></a>415 nepodporovaný typ média

Formát datové části odesílaný klientem má nepodporovaný formát.

|Kód chyby|Hexadecimální hodnota |Popis chyby|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Pro již zašifrovaný obsah byste neměli používat šifrování.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |Pro vstupní formát je šifrování neplatné.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Typ zásad doručení je neplatný.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |Původní nastavení může být sdíleno více formáty výstupu.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Formát nebo typ média není podporován. Například Media Services nepodporuje počet úrovní kvality, který je větší než 64. Ve značce videa FLV Media Services nepodporuje snímek videa s více službami SPS a více PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Vstupní formát požadovaného prostředku se nepodporuje. Media Services podporuje hladké formáty (živé), MP4 (VoD) a progresivní stahování.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Požadovaný výstupní formát není podporován. Media Services podporuje hladce, POMLČKu (CSF, CMAF), HLS (v3, v4, CMAF) a progresivní stahování.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Byl zjištěn nepodporovaný typ šifrování.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Požadovaný typ média není výstupním formátem podporován. Podporované typy jsou video, zvuk nebo "SUBT" Podnadpis.|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Médium zdrojového prostředku bylo kódováno pomocí formátu média, který není kompatibilní s formátem výstupu.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|Zdrojový prostředek byl kódovaný ve formátu videa, který není kompatibilní s formátem výstupu. Jsou podporovány h. 264, AVC, H. 265 (HEVC, hev1 nebo hvc1).|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|Zdrojový prostředek byl kódovaný pomocí zvukového formátu, který není kompatibilní s formátem výstupu. Podporované formáty zvuku jsou AAC, E-AC3 (DD +), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|Zdrojový chráněný prostředek nelze převést do výstupního formátu.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Formát ochrany není podporován výstupním formátem.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Formát ochrany není podporován vstupním formátem.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Neplatná jednotka NAL pro video, například pouze první NAL v ukázce může být AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Neplatná velikost jednotky seznamu NAL.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Hodnota délky jednotky NAL je neplatná.|
|MPE_FILTER_INVALID|0x80890236|Neplatné dynamické filtry manifestu.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Neplatné nebo nepodporované verze filtru.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Neplatný typ filtru|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Filtr určil neplatný rozsah.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Filtr určil neplatný atribut Track.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Filtr určil neplatnou délku okna prezentace.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Neplatnou hodnotu Live Back-off Určuje filtr.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Ve starších verzích filtrů je podporován pouze jeden element absTimeInHNS.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Po použití filtrů už nejsou žádné další streamy.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Živý přechod je mimo okno DVR.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Živý návrat je větší než okno prezentace.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Překročilo se deset (10) maximálních povolených výchozích filtrů.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|V kombinovaných filtrech požadavků není povolený vícenásobný první operátor kvality videa.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Počet prvních atributů přenosů kvality musí být jeden (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|Doba trvání segmentu HLS musí být menší než jedna třetina okna DVR a HLS zpátky.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Trvání fragmentů musí být kratší nebo rovno přibližně 20 sekund nebo vstupní úrovně kvality nejsou zarovnané na čas.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Chyba specifická pro DTS, nelze najít ReservedBox, pokud by měla být přítomna v DTSSpecficBox během analýzy pole DTS.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|Chyba specifická pro DTS: během analýzy pole DTS se nenašly žádné kanály v DTSSpecficBox.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|Chyba specifická pro DTS, Neshoda typu vzorku v DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Chyba specifická pro službu DTS, je nastaveno více prostředků, ale Neshoda typu DTSH Sample.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Chyba specifická pro DTS, velikost základního datového proudu je neplatná.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Chyba specifická pro DTS, ukázkové řešení je neplatné.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Chyba specifická pro DTS, index rozšíření dílčího datového proudu je neplatný.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Chyba specifická pro DTS, číslo bloku podproudu je neplatné.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Chyba specifická pro DTS, frekvence vzorkování je neplatná.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|Chyba specifická pro DTS, kód referenčních hodin v rozšíření dílčího datového proudu je neplatný.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|Chyba specifická pro DTS, počet přemapování mluvčího je neplatný.|

Články a příklady šifrování najdete v těchto tématech:

- [Koncept: Ochrana obsahu](content-protection-overview.md)
- [Koncept: zásady klíčů obsahu](content-key-policy-concept.md)
- [Koncept: streamování zásad](streaming-policy-concept.md)
- [Ukázka: Ochrana pomocí šifrování AES](protect-with-aes128.md)
- [Ukázka: Ochrana pomocí DRM](protect-with-drm.md)

Pokyny k filtru najdete v těchto tématech:

- [Koncept: dynamické manifesty](filters-dynamic-manifest-overview.md)
- [Koncept: filtry](filters-concept.md)
- [Ukázka: vytváření filtrů pomocí rozhraní REST API](filters-dynamic-manifest-rest-howto.md)
- [Ukázka: vytváření filtrů pomocí .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Ukázka: vytvoření filtrů pomocí rozhraní příkazového řádku](filters-dynamic-manifest-cli-howto.md)

Informace o živých článcích a ukázkách najdete tady:

- [Koncept: Přehled živého streamování](live-streaming-overview.md)
- [Koncept: živé události a živé výstupy](live-events-outputs-concept.md)
- [Ukázka: kurz živého streamování](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 Neuspokojivý rozsah

|Kód chyby|Hexadecimální hodnota |Popis chyby|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Chyba operace úložiště, Vrácená chyba HTTP 416, neplatný rozsah.|

## <a name="500-internal-server-error"></a>500 – Vnitřní chyba serveru

Při zpracování požadavku Media Services dojde k nějaké chybě, která brání v pokračování zpracování.  

|Kód chyby|Hexadecimální hodnota |Popis chyby|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Byl přijat a přeložen z kódu chyby WinHTTP ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Byl přijat a přeložen z kódu chyby WinHTTP ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Byl přijat a přeložen z kódu chyby WinHTTP ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Chyba operace úložiště, obecná InternalError jedné z chyb HTTP 500.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Chyba operace úložiště, obecná OperationTimedOut jedné z chyb HTTP 500.|
|MPE_STORAGE_FAILURE|0x808900F2|Chyba operace úložiště, jiné chyby HTTP 500 než InternalError nebo OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 – Nedostupná služba

Server momentálně nemůže přijímat požadavky. Příčinou této chyby může být nadměrné množství požadavků na službu. Mechanismus omezování služby Media Services omezuje využití prostředků pro aplikace, které provádějí nadměrné množství požadavků na službu.

> [!NOTE]
> Podrobnější informace o důvodu, proč se zobrazuje chyba 503, najdete v chybové zprávě a řetězci kódu chyby. Tato chyba ne vždy znamená, že dochází k omezování.
> 

|Kód chyby|Hexadecimální hodnota |Popis chyby|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Chyba operace úložiště, přijala se chyba zaneprázdnění serveru HTTP 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="see-also"></a>Viz také

- [Kódy chyb kódování](/rest/api/media/jobs/get#joberrorcode)
- [Azure Media Services koncepty](concepts-overview.md)
- [Kvóty a omezení](limits-quotas-constraints.md)

## <a name="next-steps"></a>Další kroky

[Příklad: přístup k ErrorCode a zprávě z ApiException pomocí .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
