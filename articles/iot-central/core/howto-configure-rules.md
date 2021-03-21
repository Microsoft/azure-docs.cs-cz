---
title: Konfigurace pravidel a akcí v Azure IoT Central | Microsoft Docs
description: V tomto článku se dozvíte, jak můžete jako tvůrce nakonfigurovat pravidla a akce založené na telemetrie v aplikaci Azure IoT Central.
author: vavilla
ms.author: vavilla
ms.date: 12/23/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 378a1dbcfbc89cdf9b24dc2490db583f1135b9a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97796784"
---
# <a name="configure-rules"></a>Konfigurace pravidel

*Tento článek je pro operátory, tvůrce a správce.*

Pravidla v IoT Central slouží jako přizpůsobitelný Nástroj pro odpověď, který se aktivuje na aktivně monitorovaných událostech z připojených zařízení. Následující části popisují, jak se pravidla vyhodnocují.

## <a name="select-target-devices"></a>Vybrat cílová zařízení

V části cílová zařízení vyberte, jaký typ zařízení bude toto pravidlo používat. Filtry vám umožní lépe upřesnit, jaká zařízení by měla být zahrnutá. Filtry používají vlastnosti v šabloně zařízení k vyfiltrování sady zařízení. Filtry samy neaktivují akci. Na následujícím snímku obrazovky jsou zařízení, na která se cílí, zařízení s typem **chladničky**. Filtr uvádí, že pravidlo by mělo obsahovat pouze **chladničky** , kde je vlastnost **vyrobeného stavu** rovna **Washington**.

![Podmínky](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Použít více podmínek

Podmínky určují, na čem se pravidla spouštějí. Když v současné době přidáte více podmínek do pravidla, jsou logicky a společně. Jinými slovy, pro pravidlo, které se má vyhodnotit jako true, musí být splněné všechny podmínky.  

Na následujícím snímku obrazovky se podmínky kontrolují, když je teplota větší než 70 &deg; F a vlhkost je menší než 10. Pokud jsou oba tyto příkazy pravdivé, pravidlo se vyhodnotí jako true a aktivuje akci.

![Snímek obrazovky ukazuje monitor chladničky s podmínkami určenými pro teplotu a vlhkost.](media/howto-configure-rules/conditions.png)

### <a name="use-a-cloud-property-in-a-value-field"></a>Použití vlastnosti Cloud v poli hodnota

Můžete odkazovat na vlastnost cloudu ze šablony zařízení v poli **hodnota** pro podmínku. Vlastnost cloudu a hodnota telemetrie musí mít podobné typy. Například pokud je **teplota** dvojitá, pak se jako možnosti v rozevíracím seznamu **hodnota** zobrazí jenom vlastnosti cloudu typu Double.

Pokud zvolíte hodnotu telemetrie typ události, rozevírací seznam **hodnota** obsahuje možnost **Any**. Možnost **Any** znamená, že se pravidlo aktivuje, když vaše aplikace obdrží událost daného typu bez ohledu na datovou část.

## <a name="use-aggregate-windowing"></a>Použít agregované okno

Pravidla vyhodnocují agregovaná časová okna jako Bubnová okna. Na snímku obrazovky níže je časový interval pět minut. Každých pět minut pravidlo vyhodnotí za posledních pět minut dat. Data jsou vyhodnocena pouze jednou v okně, ke kterému odpovídá.

![Bubnová okna](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Použití pravidel s IoT Edge moduly

Omezení platí pro pravidla, která se použijí pro IoT Edge moduly. Pravidla telemetrie z různých modulů se nevyhodnotí jako platná pravidla. Jako příklad postupujte následovně. První podmínka pravidla se vychází z telemetrie na teplotu z modulu A. Druhá podmínka pravidla je pro telemetrii vlhkosti v modulu B. Vzhledem k tomu, že tyto dvě podmínky jsou z různých modulů, jedná se o neplatnou sadu podmínek. Pravidlo není platné a při pokusu o uložení pravidla vyvolá chybu.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nakonfigurovat pravidlo v aplikaci Azure IoT Central, se dozvíte, jak [nakonfigurovat Rozšířená pravidla](howto-configure-rules-advanced.md) pomocí Power Automate nebo Azure Logic Apps.
