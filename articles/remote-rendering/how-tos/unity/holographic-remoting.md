---
title: Použití holografického vzdáleného vykreslování a vzdáleného vykreslování v jednotě
description: Jak lze použít náhled holografické vzdálené komunikace v kombinaci s vzdáleným vykreslováním Azure
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681204"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Použití holografického vzdáleného vykreslování a vzdáleného vykreslování v jednotě

[Holografické vzdálené komunikace](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) a vzdálené vykreslování Azure se vzájemně vylučují v rámci jedné aplikace. Jako takový [režim Unity přehrávání](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) také není k dispozici.

Pro každé spuštění editoru Unity lze použít pouze jeden z těchto dvou. Chcete-li použít druhý, restartujte unity první.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Použijte režim Unity play k náhledu na Hololens 2

 Režim unity přehrávání lze stále použít, například k testování ui aplikace. Je však důležité, aby arr nikdy inicializována. V opačném případě se zhroutí.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Zobrazení náhledu na ploše pomocí náhlavní soupravy WMR VR

Pokud je k dispozici náhlavní souprava Windows Mixed Reality VR, lze ji použít k náhledu uvnitř Unity. V tomto případě je v pořádku inicializovat ARR, ale nebude možné se připojit k relaci, zatímco se používá náhlavní souprava WMR.
