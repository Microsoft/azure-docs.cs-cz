---
title: Začínáme s týmy spolupráce na komunikačních službách Azure
titleSuffix: An Azure Communication Services quickstart
description: V tomto rychlém startu se dozvíte, jak se připojit k týmům, které se účastní sady Azure Communication Chat SDK.
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d7ea3b67c3ce85ce104d16785e4e5f4d45b138f6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105106784"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Rychlý Start: připojení aplikace chat k týmu schůzky

> [!IMPORTANT]
> Pokud chcete povolit nebo zakázat [interoperabilitu tenanta týmů](../../concepts/teams-interop.md), vyplňte [Tento formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Začněte s komunikačními službami Azure připojením svého řešení chat k Microsoft Teams pomocí sady JavaScript SDK. 

## <a name="prerequisites"></a>Požadavky 

1.  [Nasazení týmů](/deployoffice/teams-install). 
2. Pracovní [chatovací aplikace](./get-started.md). 

## <a name="enable-teams-interoperability"></a>Povolit interoperabilitu týmů 

Uživatel komunikačních služeb, který se připojí k týmům, kteří se účastní týmu jako uživatel typu Host, může přistupovat ke chatu schůzky pouze v případě, že se připojí k volání schůzky v týmu. Informace o tom, jak přidat uživatele komunikačních služeb do volání na schůzku, najdete v dokumentaci pro [týmy pro spolupráci](../voice-video-calling/get-started-teams-interop.md) .

Abyste mohli tuto funkci používat, musíte být členem vlastnící organizace obou entit.

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- Podívejte se na naši [ukázku chat Hero](../../samples/chat-hero-sample.md)
- Další informace o [tom, jak funguje chat](../../concepts/chat/concepts.md)