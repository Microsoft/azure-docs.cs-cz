---
title: Rychlý Start – týmy komunikující na komunikačních službách Azure
titleSuffix: An Azure Communication Services quickstart
description: V tomto rychlém startu se dozvíte, jak se připojit k týmům, které se účastní komunikace s voláním sady SDK pro komunikaci Azure.
author: chpalm
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 39c35bc90986126d099e68023e15b97c58d0ac26
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108167"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Rychlý Start: připojení volající aplikace ke schůzce týmu

> [!IMPORTANT]
> Pokud chcete povolit nebo zakázat [interoperabilitu tenanta týmů](../../concepts/teams-interop.md), vyplňte [Tento formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Začněte s komunikačními službami Azure tím, že své volání připojíte k Microsoft Teams pomocí sady JavaScript SDK.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- Podívejte se na naši [ukázku volání Hero](../../samples/calling-hero-sample.md)
- Další informace o [volání funkcí sady SDK](./calling-client-samples.md)
- Další informace o [volání funkce](../../concepts/voice-video-calling/about-call-types.md)
