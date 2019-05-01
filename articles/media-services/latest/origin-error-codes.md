---
title: Chyby balení a původu Azure Media Services | Dokumentace Microsoftu
description: Toto téma popisuje chyby, které můžete dostávat z balíčku služby Azure Media Services.
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
ms.date: 04/28/2019
ms.author: juliako
ms.openlocfilehash: c350a989f23eb667923a53f6eb06dd55905b0fab
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927309"
---
# <a name="media-services-packaging-errors"></a>Chyby balení Media Services 

Toto téma popisuje chyby, které můžete dostávat ze služby Azure Media Services [balení služby](streaming-endpoint-concept.md).

## <a name="400-bad-request"></a>400 – Chybný požadavek

Požadavek obsahuje neplatné informace a je odmítnuto pomocí těchto kódů chyb a kvůli jednomu z následujících důvodů:

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Adresu URL syntaxe nebo má chybný formát. Mezi příklady patří žádosti o neplatný typ, neplatný fragment nebo neplatný sledování. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|Požadavek nemá žádnou značku šifrování v adrese URL. Požadavky na CMAF vyžadují značku šifrování v adrese URL. Jiné protokoly, které jsou nakonfigurovány s více než jeden typ šifrování také vyžadovat šifrování značky pro odstraňování mnohoznačnosti. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|Požadavek na úložiště ke splnění tohoto požadavku se nezdařilo s chybou chybný požadavek. |

## <a name="403-forbidden"></a>403 Zakázáno

Žádost není povolena kvůli jednomu z následujících důvodů:

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|Požadavek na úložiště ke splnění tohoto požadavku se nezdařilo s chybu ověřování. To může nastat, pokud byly otočen klíče úložiště a služba nemohla synchronizovat klíče úložiště. <br/><br/>Obraťte se na Azure tak, že přejdete na podporu [Nápověda a podpora](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) na webu Azure Portal.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Chyba operace úložiště, přístup se nezdařilo z důvodu nedostatečných oprávnění účtu. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |Požadavek na úložiště ke splnění žádosti se nezdařilo, protože účet úložiště je zakázaný. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Chyba operace úložiště, přístup se nezdařilo z důvodu obecné chyby. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |Výstupní formát blokovaný z důvodu konfigurace v nástroji StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |Šifrování se vyžaduje pro obsah, zásady pro doručení, je třeba formát výstupu. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Šifrování není nastavena v nastavení zásad doručování. |

## <a name="404-not-found"></a>404 – Nenalezeno

Operace se pokouší tak, aby fungoval na prostředek, který již existuje. Například prostředek už byla odstraněna.

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |Nenašel se požadovaný sledování. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |Požadovaný prostředek se nenašel. |
|MPE_UNAUTHORIZED |0x80890244 |Nemá přístup. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |Nenašel se požadovaný časové razítko. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Nenašel se požadovaný filtr dynamických manifestů. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |Požadovaný fragment index je mimo platný rozsah. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Živá média, které položky se nenašel získat moov vyrovnávací paměť. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Nepovedlo se najít fragment v požadované době pro konkrétní stopu.<br/><br/>Může být, že fragment není v úložišti. Zkuste jiné vrstvy, který může mít fragment prezentace. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Nepovedlo se najít položku média pro požadovanou přenosovou rychlostí v manifestu. <br/><br/>Může být, že hráč výzva pro video stopu bitrate, který nebyl v manifestu.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Nepovedlo se najít určité metadata v manifestu nebo nelze nalézt přenesení změn z úložiště. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Chyba operace úložiště, prostředek se nenašel. |

## <a name="409-conflict"></a>409 – konflikt

Zadané ID prostředku na `PUT` nebo `POST` operaci je obsazené existující prostředek. Tento problém můžete vyřešit pomocí jiné ID prostředku.

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Chyba operace úložiště, konfliktu chyby.  |

## <a name="410"></a>410

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Pro živé streamování, když filtr, který má je forceEndTimestamp nastavena na hodnotu true, začátek nebo konec časového razítka mimo aktuální okno DVR.|

## <a name="412-precondition-failure"></a>412 předběžné podmínky selhání

Operace uvedla značku eTag, která se liší od verze k dispozici na na server, to znamená chybu optimistického řízení souběžnosti. Zkuste požadavek zopakovat za čtení nejnovější verzi prostředku a aktualizovat značku eTag na požadavek.

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Požadovaný fragment není připravený.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Chyba operace úložiště, selhání předběžné podmínky.|

## <a name="415-unsupported-media-type"></a>415 Nepodporovaný typ média

Formát datové části zaslanou klientem je v nepodporovaném formátu.

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Neměli použít šifrování na již šifrovaný obsah.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |Šifrování je neplatný pro formát vstupu.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Typ zásady doručování je neplatný.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |Původní nastavení může být sdílen více formátů výstupu.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Formát médií nebo typu se nepodporuje. Například Media Services nepodporuje počet úrovní kvality, který je víc než 64. FLV značku pro video Media Services nepodporuje snímku videa s více aktualizace Service PACKU a více PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Vstupní formát pro požadovaný prostředek se nepodporuje. Služba Media Services podporuje protokol Smooth (za provozu), MP4 (Video-on-demand) a progresivního stahování formátů.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Požadovaný výstupní formát není podporován. Media Services podporuje protokol Smooth, DASH (CSF, CMAF), HLS (v3, v4, CMAF) a progresivního stahování formátů.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Byl zjištěn typ šifrování není podporován.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Výstupní formát nepodporuje požadovaný typ média. Podporované typy jsou videa, zvuk nebo podnadpis "SUBT".|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Zdrojové médium asset kódované pomocí, který není kompatibilní s formátem výstupu formát média.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|Zdroj prostředku kódované video formátu, který není kompatibilní s formátem výstupu. Jsou podporovány H.264 AVC, H.265 (HEVC, hev1 nebo hvc1).|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|Zdroj prostředku kódované pomocí zvukový formát, který není kompatibilní s formátem výstupu. Podporované formáty zvuku se AAC, E-AC3 (DD +), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|Asset chráněný zdroj nelze převést na formát výstupu.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Formát ochrany nepodporuje formát výstupu.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Formát ochrany nepodporuje formát vstupu.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Neplatný videa NAL jednotek, například pouze první vrstvě NAL v ukázce může být AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Neplatná velikost jednotky NAL.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Neplatná hodnota délka jednotky NAL.|
|MPE_FILTER_INVALID|0x80890236|Neplatný filtry dynamických manifestů.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Neplatný nebo nepodporovaný filtr verzí.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Neplatný typ filtru.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Filtr je zadán neplatný rozsah.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Filtr je zadán neplatný sledování atribut.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Neplatná délka okna je zadaný filtr.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Neplatný živé regresi je zadaný filtr.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Pouze jeden element absTimeInHNS je podporované ve starší verzi filtry.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Po použití filtrů vůbec neexistuje žádné další datové proudy.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Živé regrese je nad rámec okna DVR.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Živé regrese je větší než okna prezentace.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Překročení maximální povolené výchozí filtry deseti (10).|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Více první kvalita videa operátor není povolený v kombinované filtrů.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Počet atributů s přenosovou rychlostí první kvality musí být jedna (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|Doba trvání segmentu HLS musí být menší než jednu třetinu okna DVR a HLS vraťte se zpět.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Fragment dob trvání musí být menší než nebo rovna hodnotě přibližně 20 sekund nebo nejsou úrovně kvality vstupní čas zarovnána.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Chyba specifické pro typy nasazení, nemůže najít ReservedBox, když by se měl prokázat v DTSSpecficBox při analýze pole DTS.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|Chyba specifické pro typy nasazení, žádné kanály součástí DTSSpecficBox během analýzy služby DTS pole.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|Chyba specifické pro typy nasazení, ukázka Neshoda typu v DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Chybě specifické pro typy nasazení, ale Neshoda typu DTSH ukázkové nastavena více prostředků.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Chyby specifické pro typy nasazení, základní velikost streamu je neplatný.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Chyby specifické pro typy nasazení, rozlišení vzorku je neplatný.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Chyby specifické pro typy nasazení, dílčí stream rozšíření index je neplatný.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Chyby specifické pro typy nasazení, dílčí stream blok číslo je neplatné.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Chyba specifické pro typy nasazení, vzorkovací frekvence je neplatná.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|Chyby specifické pro typy nasazení referenční hodiny kód v dílčí stream rozšíření je neplatný.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|Chyba specifické pro typy nasazení, počet přednášející přemapování sady je neplatná.|

Šifrování články a příklady najdete v tématu:

- [Koncept: ochrana obsahu](content-protection-overview.md)
- [Koncept: Zásady klíče obsahu](content-key-policy-concept.md)
- [Koncept: Streamování zásady](streaming-policy-concept.md)
- [Ukázka: ochrana s využitím šifrování AES](protect-with-aes128.md)
- [Ukázka: ochrana s využitím DRM](protect-with-drm.md)

Filtr pokyny najdete v tématu:

- [Koncept: dynamických manifestů](filters-dynamic-manifest-overview.md)
- [Koncept: filtry](filters-concept.md)
- [Ukázka: vytváření filtrů pomocí rozhraní REST API](filters-dynamic-manifest-rest-howto.md)
- [Ukázka: vytváření filtrů pomocí .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Ukázka: vytváření filtrů pomocí rozhraní příkazového řádku](filters-dynamic-manifest-cli-howto.md)

Živé články a ukázky naleznete v tématu:

- [Koncepce: živé streamování přehled](live-streaming-overview.md)
- [Koncept: Události v reálném čase a živé výstupy](live-events-outputs-concept.md)
- [Ukázka: živé streamování kurzu](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 rozsahu není uspokojit

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Chyba operace úložiště vrátila chybu http 416, neplatný rozsah.|

## <a name="500-internal-server-error"></a>500 – Interní chyba serveru

Během zpracování požadavku, Media Services nalezne nějaké chybě, která zabrání v pokračování zpracování.  

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Přijatá a přeložený ze služby Winhttp chybovým kódem ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Přijatá a přeložený ze služby Winhttp chybovým kódem ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Přijatá a přeložený ze služby Winhttp chybovým kódem ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Chyba operace úložiště, obecné vnitřní chyba jednoho z chyby HTTP 500.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Chyba operace úložiště, obecné OperationTimedOut některého z chyby HTTP 500.|
|MPE_STORAGE_FAILURE|0x808900F2|Chyba operace úložiště, jiné chyby HTTP 500 než vnitřní chyba nebo OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 – Nedostupná služba

Server je nyní nelze přijmout požadavky. Tuto chybu může způsobovat nadměrné požadavky na službu. Mechanismus omezení služby Media Services omezuje využití prostředků pro aplikace, které usnadňují nadměrnou ke službě.

> [!NOTE]
> Zkontrolujte chybovou zprávu a řetězec kódu chyby, chcete-li získat podrobnější informace o důvodu, že jste získali došlo k chybě 503. Tato chyba nemusí to znamenat omezení šířky pásma.
> 

|Kód chyby|Šestnáctková hodnota |Popis chyby|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Chyba operace úložiště, došlo k chybě zaneprázdněný ve server HTTP 503.|

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Podívejte se [komunita Azure Media Services](media-services-community.md) článek a zobrazit různé způsoby můžete klást otázky, poskytnout zpětnou vazbu a aktualizace o Media Services.

## <a name="see-also"></a>Další informace najdete v tématech

- [Kódy chyb kódování](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Koncepty služby Azure Media Services](concepts-overview.md)
- [Kvóty a omezení](limits-quotas-constraints.md)

## <a name="next-steps"></a>Další postup

[Příklad: přístup k kód chyby a zprávy z ApiException s využitím .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
