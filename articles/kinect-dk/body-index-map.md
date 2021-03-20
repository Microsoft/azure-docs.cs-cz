---
title: Mapa indexu těla služby Azure Kinect
description: Naučte se, jak zadat dotaz na mapu indexu sledování těla v Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, přenos, tělo, sledování, index, segmentace, mapa
ms.openlocfilehash: 8c128e59cef515aafb4f59794f0f644b90fd625d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277212"
---
# <a name="azure-kinect-body-tracking-index-map"></a>Mapa indexu sledování textu Azure Kinect

Mapa hlavního indexu obsahuje mapu segmentace instancí pro každý tělo v zachytávání hloubkové kamery. Každý pixel se mapuje na odpovídající pixel v hloubce nebo v obrázku IR. Hodnota pro každý pixel představuje tělo, ke kterému pixel patří. Může to být buď pozadí (hodnota `K4ABT_BODY_INDEX_MAP_BACKGROUND` ), nebo index zjištěného `k4abt_body_t` .

![Příklad mapy základního indexu](./media/concepts/body-index-map.png)

>[!NOTE]
> Index těla se liší od ID těla. Můžete zadat dotaz na ID těla z daného indexu těla voláním rozhraní API: [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92).


## <a name="using-body-index-map"></a>Použití mapy indexů textu

Mapa základního indexu je uložena jako `k4a_image_t` a má stejné rozlišení jako hloubka nebo infračervený obrázek. Každý pixel je 16bitová hodnota. Může být dotazována `k4abt_frame_t` voláním metody `k4abt_frame_get_body_index_map` . Vývojář zodpovídá za uvolnění paměti pro mapu indexů těla voláním `k4a_image_release()` .

## <a name="next-steps"></a>Další kroky

[Vytvoření první aplikace pro sledování pohybu těla](build-first-body-app.md)
