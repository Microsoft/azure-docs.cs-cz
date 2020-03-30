---
title: Konfigurace pravidel a akcí v Azure IoT Central | Microsoft Docs
description: Tento článek s návody ukazuje, jak jako tvůrce, jak nakonfigurovat pravidla a akce založené na telemetrii ve vaší aplikaci Azure IoT Central.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 509f9557a8128df12353ad02a7c7db02b7b42631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158443"
---
# <a name="configure-rules"></a>Konfigurace pravidel



*Tento článek je pro operátory, tvůrce a správce.*

Pravidla v IoT Central slouží jako přizpůsobitelný nástroj pro odezvu, který se aktivuje na aktivně monitorovaných událostech z připojených zařízení. Následující části popisují, jak jsou pravidla vyhodnocována.

## <a name="select-target-devices"></a>Výběr cílových zařízení

Pomocí části cílová zařízení vyberte, na jakém druhu zařízení bude toto pravidlo použito. Filtry umožňují dále upřesnit, jaká zařízení by měla být zahrnuta. Filtry používají vlastnosti v šabloně zařízení k filtrování sady zařízení. Filtry samy o sobě nespustí akci. Na následujícím snímku obrazovky jsou zařízení, která jsou cílená, typu šablony zařízení **Chladnička**. Filtr uvádí, že pravidlo by mělo zahrnovat pouze **chladničky,** kde **vyrobeno state** vlastnost rovná **Washington**.

![Podmínky](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Použití více podmínek

Podmínky jsou to, co pravidla aktivovat. V současné době, když přidáte více podmínek do pravidla, jsou logicky AND'd společně. Jinými slovy, musí být splněny všechny podmínky, aby bylo pravidlo vyhodnoceno jako pravdivé.  

Na následujícím snímku obrazovky podmínky kontrolují, kdy je&deg; teplota vyšší než 70 F a vlhkost vzduchu je menší než 10. Pokud jsou oba tyto příkazy true, pravidlo vyhodnotí na true a spustí akci.

![Podmínky](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>Použití agregovaných oken

Pravidla vyhodnocují agregovaná časová okna jako omílaná okna. Na následujícím snímku obrazovky je časové okno pět minut. Každých pět minut pravidlo vyhodnotí posledních pět minut dat. Data jsou vyhodnocena pouze jednou v okně, kterému odpovídají.

![Omílání Windows](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Použití pravidel s moduly IoT Edge

Omezení se vztahuje na pravidla, která se vztahují na moduly IoT Edge. Pravidla telemetrie z různých modulů nejsou vyhodnocena jako platná pravidla. Vezměme si následující příklad. První podmínkou pravidla je telemetrie teploty z modulu A. Druhá podmínka pravidla je na telemetrii vlhkosti v modulu B. Vzhledem k tomu, že tyto dvě podmínky pocházejí z různých modulů, jedná se o neplatnou sadu podmínek. Pravidlo není platné a při pokusu o uložení pravidla vyvolá chybu.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nakonfigurovat pravidlo v aplikaci Azure IoT Central, můžete:

> [!div class="nextstepaction"]
> [Analyzujte data za chodu](howto-create-analytics.md)
