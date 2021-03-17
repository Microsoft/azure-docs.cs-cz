---
title: Použití služeb Holographic Remoting a Remote Rendering v Unity
description: Jak se dá v kombinaci se službou Azure Remote rendering využít ukázkovou vzdálenou komunikaci
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: bd69710b6e4404a76d3cca385b6c07ea7c1f3f5c
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92201813"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Použití služeb Holographic Remoting a Remote Rendering v Unity

[Holografická Vzdálená komunikace](/windows/mixed-reality/holographic-remoting-player) a vzdálené vykreslování Azure se vzájemně vylučují v rámci jedné aplikace. V takovém případě není k dispozici také [režim Unity Play](/windows/mixed-reality/unity-play-mode) .

Pro každé spuštění v editoru Unity lze použít pouze jednu z těchto dvou. Pokud chcete použít druhou, nejdřív restartujte Unity.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Použití režimu Unity Play k zobrazení náhledu na HoloLens 2

 Je možné, že se k otestování uživatelského rozhraní aplikace stále používá režim Unity Play. Je ale důležité, aby se šipka nikdy neinicializoval. V opačném případě dojde k chybě.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Použití sluchátka s WMRou VR pro náhled na desktopu

Pokud je k dispozici náhlavní souprava Windows Mixed reality, můžete ji použít k náhledu v rámci Unity. V tomto případě je to pro inicializaci funkce ARR velmi přesné, ale nebude možné se připojit k relaci, zatímco je použita WMRá sluchátka.