---
title: Začínáme s týmy spolupráce na komunikačních službách Azure
titleSuffix: An Azure Communication Services quickstart
description: V tomto rychlém startu se dozvíte, jak se připojit k týmům na schůzce s klientskou knihovnou služby Azure Communications chat.
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 336b12f7000b70c424f1165976370300bc8c85e0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656110"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Rychlý Start: připojení aplikace chat k týmu schůzky

> [!IMPORTANT]
> Pokud chcete povolit nebo zakázat [interoperabilitu tenanta týmů](../concepts/teams-interop.md), vyplňte [Tento formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Začněte s komunikačními službami Azure připojením vašeho řešení chatu k Microsoft Teams pomocí klientské knihovny JavaScriptu. 

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