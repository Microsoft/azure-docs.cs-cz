---
title: Graf metriky pro metriky metriky
titleSuffix: Azure Cognitive Services
description: Postup konfigurace grafu metrik a vizualizace souvisejících anomálií ve vašich datech.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: bcedef4a1339dacaff3fe841e97f985c42320819
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "92043149"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>Postupy: sestavení grafu metrik pro analýzu souvisejících metrik

Každá metrika v Advisoru metriky se monitoruje samostatně podle modelu, který se učí z historických dat a předpovídá budoucí trendy. Každá metrika má samostatný model, který se na něj aplikuje. V některých případech se ale může vzájemně navzájem týkat několik metrik a anomálie se musí analyzovat napříč několika metrikami. **Graf metriky** pomáhá s tímto. 

Pokud máte například různé streamy telemetrie v samostatných metrikách, Poradce pro metriky je bude monitorovat samostatně. Pokud anomálie v jedné metrice způsobují anomálie v jiných metrikách, hledání těchto vztahů a hlavní příčiny vašich dat může být užitečné při řešení incidentů. Graf metriky umožňuje vytvořit graf vizuálních topologií nalezených anomálií. 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>Vyberte metriku pro vložení prvního uzlu do grafu.

Na navigačním panelu klikněte na kartu **metriky grafu** . Prvním krokem při sestavování grafu metrik je umístit uzel do grafu. V horní části stránky vyberte datový kanál a metriku. Uzel se zobrazí v dolním panelu. 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="Vybrat metriku":::

## <a name="add-a-noderelation-on-existing-node"></a>Přidat uzel/relaci na existujícím uzlu

Dále potřebujete přidat další uzel a zadat relaci ke stávajícím uzlům. Vyberte existující uzel a klikněte na něj pravým tlačítkem. Místní nabídka se zobrazí s několika možnostmi. 

Klikněte na **Přidat relaci** a budete moct vybrat jinou metriku a mezi těmito dvěma uzly zadat typ vztahu. Můžete použít také konkrétní filtry dimenzí. 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="Přidání uzlu a vztahu":::

Po zopakování výše uvedených kroků budete mít graf metrik popisující vztahy mezi všemi souvisejícími metrikami.
**Pomocný parametr pro barvy uzlů**
> [!TIP]
> - Když vyberete filtr metrik a dimenzí, budou se všechny uzly se stejnou metrikou a filtrem dimenzí v grafu vybarvit **<font color=blue>modře</font>**.
> - Nevybrané uzly, které reprezentují metriku v grafu, budou barevné **<font color=green>Zeleny</font>**.
> - Pokud je v aktuální metrikě pozorována anomálie, uzel bude **<font color=red>červeně</font>** barevný.

## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>Zobrazit související stav anomálií metrik v centru incidentů

Když je graf metriky sestavený, kdykoli je u metrik v grafu zjištěna anomálie, budete moci zobrazit související stavy anomálií a získat podrobný pohled na incident. 

Klikněte na incident v grafu a posuňte se dolů k **analýze mezi metrikami** pod diagnostické informace.

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="Zobrazit související metriky a anomálie":::

## <a name="next-steps"></a>Další kroky

- [Úprava detekce anomálií pomocí zpětné vazby](anomaly-feedback.md)
- [Diagnostikujte incident](diagnose-incident.md).
- [Konfigurace metrik a doladění konfigurace zjišťování](configure-metrics.md)
