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
ms.openlocfilehash: 0e75d2b480a9cbfd2977d9d449c1ea12bdfe4920
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095607"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Rychlý Start: připojení volající aplikace ke schůzce týmu

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

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

Funkce popsané v tomto dokumentu používají obecnou verzi dostupnost sady SDK služby Communications Services. Interoperabilita týmů vyžaduje beta verzi sady SDK služby Communications Services. Sady SDK beta verze lze prozkoumat na [stránce poznámky k verzi](https://github.com/Azure/Communication/tree/master/releasenotes).

Při provádění kroku instalovat balíček se sadami beta SDK změňte verzi balíčku na nejnovější beta verzi, a to zadáním verze `@1.0.0-beta.10` (verze v okamžiku psaní tohoto článku) v `communication-calling` názvu balíčku. Nemusíte měnit `communication-common` příkaz balíčku. Například:

```console
npm install @azure/communication-calling@1.0.0-beta.10 --save
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- Podívejte se na naši [ukázku volání Hero](../../samples/calling-hero-sample.md)
- Další informace o [volání funkcí sady SDK](./calling-client-samples.md)
- Další informace o [volání funkce](../../concepts/voice-video-calling/about-call-types.md)
