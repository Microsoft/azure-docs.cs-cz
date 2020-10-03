---
title: Rychlý Start – přidání chatu do aplikace
titleSuffix: An Azure Communication Services quickstart
description: V tomto rychlém startu se dozvíte, jak do aplikace přidat chat komunikačních služeb.
author: fanche
manager: phans
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 2d0d80be367dda3689566dec2ade6fd7fc7c01fc
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665357"
---
# <a name="quickstart-add-chat-to-your-app"></a>Rychlý Start: Přidání chatu do aplikace

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Začněte s komunikačními službami Azure pomocí klientské knihovny služby Communications chat k přidání chatu do aplikace v reálném čase. V tomto rychlém startu budeme pomocí klientské knihovny chatu vytvářet vlákna chatu, která uživatelům umožňují vzájemnou konverzaci. Další informace o konceptech chatu najdete v [Koncepční dokumentaci k chatu](../../concepts/chat/concepts.md).

::: zone pivot="programming-language-javascript"
[!INCLUDE [Chat with JavaScript client library](./includes/chat-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Chat with Python client library](./includes/chat-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Chat with Java client library](./includes/chat-java.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Chat with C# client library](./includes/chat-csharp.md)]
::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak:

> [!div class="checklist"]
> * Vytvoření chatového klienta
> * Vytvoření vlákna se dvěma uživateli
> * Odeslat zprávu do vlákna
> * Přijímání zpráv z vlákna
> * Odebrání uživatelů z vlákna

> [!div class="nextstepaction"]
> [Vyzkoušet aplikaci Chat Hero](../../samples/chat-hero-sample.md)

Můžete také chtít:

 - Další informace o [konceptech chatu](../../concepts/chat/concepts.md)
 - Seznámení s [knihovnou klienta chat](../../concepts/chat/sdk-features.md)
