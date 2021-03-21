---
ms.openlocfilehash: 9c1b521a0f10da77295fd2457793566d787cb2cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89570241"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs4.svg" alt-text="Tok signálů":::

Předchozí diagram ukazuje, jak tok signalizuje v tomto rychlém startu. [Hraniční modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuluje kameru protokolu IP, která je hostitelem serveru RTSP (Real-Time streaming Protocol). [Zdrojový uzel RTSP](../../../media-graph-concept.md#rtsp-source) načte kanál videa z tohoto serveru a odešle snímky videa do uzlu [procesoru detekce pohybu](../../../media-graph-concept.md#motion-detection-processor) . Zdroj RTSP pošle stejné snímky videa do uzlu [procesoru signálu signálu](../../../media-graph-concept.md#signal-gate-processor) , který zůstane uzavřený, dokud ho událost nespustí.

Když procesor detekce pohybů detekuje pohyb ve videu, pošle událost na uzel procesoru brány signálu, který ho aktivuje. Brána se otevře po nakonfigurované době trvání a odeslání snímků videa do uzlu [jímky souborů](../../../media-graph-concept.md#file-sink) . Tento uzel jímky zaznamená video jako soubor MP4 v místním systému souborů vašeho hraničního zařízení. Soubor je uložený v nakonfigurovaném umístění.

V tomto rychlém startu budete:

1. Vytvořte a nasaďte Media Graph.
1. Interpretujte výsledky.
1. Vyčistěte prostředky.
