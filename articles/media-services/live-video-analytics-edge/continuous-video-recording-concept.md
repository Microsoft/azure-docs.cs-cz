---
title: Nepřetržité nahrávání videa – Azure
description: Záznam průběžného nahrávání videa (CVR) označuje proces průběžného zaznamenávání videa ze zdroje videa. Toto téma popisuje, co je CVR.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: f8b25d74db279f8cfff68f08efeab2975484a0c5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453746"
---
# <a name="continuous-video-recording"></a>Nepřetržité nahrávání videa  

## <a name="suggested-pre-reading"></a>Navrhované před čtením  

* [Koncept Media graphu](media-graph-concept.md)
* [Koncept záznamu videa](video-recording-concept.md)

## <a name="overview"></a>Přehled

Záznam průběžného nahrávání videa (CVR) označuje proces průběžného zaznamenávání videa ze zdroje videa. Live video Analytics na IoT Edge podporuje nepřetržité nahrávání videa z kamery CCTV prostřednictvím [mediálního grafu](media-graph-concept.md) , který se skládá ze zdrojového uzlu RTSP a uzlu jímky assetu. Následující diagram znázorňuje grafické znázornění takového grafu médií. Obrázek JSON [topologie grafu](media-graph-concept.md#media-graph-topologies-and-instances) takového mediálního grafu najdete [tady](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="Nepřetržité nahrávání videa":::

Graf Media Graph zobrazený výše se dá spustit na hraničním zařízení a video s nahráváním jímky assetu na Azure Media Services [assetu](terminology.md#asset). Video se bude zaznamenávat tak dlouho, dokud mediální graf zůstane v aktivovaném stavu. Vzhledem k tomu, že se video zaznamenává jako prostředek, můžete ho přehrát pomocí stávajících možností streamování Media Services. Další podrobnosti najdete v tématu [přehrávání zaznamenaného obsahu](video-playback-concept.md) .

## <a name="resilient-recording"></a>Odolný záznam

Live video Analytics na IoT Edge podporuje provoz v rámci méně než dokonalé síťové podmínky, kdy hraniční zařízení občas ztratí připojení ke cloudu nebo se může vyskytnout jako vyřazení dostupné šířky pásma. Z toho vyplývá, že video ze zdroje se místně ukládá do mezipaměti a v pravidelných intervalech se automaticky synchronizuje s Assetem. Pokud prohlížíte [JSON topologie grafu](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json), uvidíte, že má definované následující vlastnosti:

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```

Tyto dvě vlastnosti jsou relevantní pro odolný záznam (pro uzel jímky assetu jsou také vyžadovány vlastnosti). Vlastnost localMediaCachePath instruuje jímku assetů, aby používala tuto cestu ke složce k ukládání dat médií do mezipaměti před odesláním do assetu. V tomto článku se seznámíte s [tím](../../iot-edge/how-to-access-host-storage-from-module.md) , jak může modul Edge využít místní úložiště vašeho zařízení. Vlastnost localMediaCacheMaximumSizeMiB definuje, kolik místa na disku může jímka assetů použít jako mezipaměť (1 MiB = 1024 × 1024 bajtů). 

Pokud váš modul Edge ztratí připojení po dlouhou dobu a obsah uložený ve složce mezipaměti dosáhne hodnoty localMediaCacheMaximumSizeMiB, bude jímka assetu začít zahození dat z mezipaměti, počínaje nejstarší daty. Například pokud zařízení ztratilo připojení na 10AM a mezipaměť dosáhne maximálního limitu na 18:00, pak se jímka assetu začne odstraňovat data zaznamenaná na 10AM. 

Po obnovení připojení k síti bude jímka assetu začít nahrávat z mezipaměti a znovu počínaje nejstarší daty. Ve výše uvedeném příkladu Předpokládejme, že by bylo nutné zahodit video z mezipaměti v době, kdy bylo obnoveno připojení (například 6:02PM), pak se jímka assetu začne nahrávat z značky 10:05AM.

Pokud později prohlížíte prostředek pomocí [těchto](playback-recordings-how-to.md) rozhraní API, uvidíte, že v assetu existuje mezera od přibližně 10AM do 10:05AM.

## <a name="segmented-recording"></a>Segmentované nahrávání  

Jak je popsáno výše, uzel jímka assetu bude nahrávat video do místní mezipaměti a bude ho pravidelně nahrávat do cloudu. Vlastnost segmentLength (uvedená v předchozí části) vám pomůže řídit náklady na transakce zápisu spojené s zápisem dat do účtu úložiště, kde se Asset zaznamenává. Pokud třeba prodloužete dobu nahrávání z 30 sekund na 5 minut, pak se počet transakcí úložiště vynechá faktorem 10 (5 * 60/30).

Vlastnost segmentLength zajišťuje, aby modul Edge nahrál video najednou za segmentLength sekund. Tato vlastnost má minimální hodnotu 30 sekund (výchozí nastavení) a dá se zvýšit o 30 sekundových zvýšení na maximálně 5 minut.

> [!NOTE]
> Účinek, který segmentLength má na přehrávání, najdete v článku věnovaném [přehrávání záznamů](playback-recordings-how-to.md) .

## <a name="see-also"></a>Viz také

* [Nahrávání videa na základě událostí](event-based-video-recording-concept.md)
* [Přehrávání zaznamenaného obsahu](video-playback-concept.md)

## <a name="next-steps"></a>Další kroky

[Kurz: nepřetržité nahrávání videa](continuous-video-recording-tutorial.md)
