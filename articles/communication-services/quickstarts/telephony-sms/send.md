---
title: Rychlý Start – odeslání zprávy SMS
titleSuffix: An Azure Communication Services quickstart
description: Přečtěte si, jak odeslat zprávu SMS pomocí komunikačních služeb Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: a325f218aecadceb0936d649ece6f5877fbfa5f1
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486471"
---
# <a name="quickstart-send-an-sms-message"></a>Rychlý Start: odeslání zprávy SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

> [!IMPORTANT]
> Zprávy SMS lze odesílat a přijímat z USA telefonní čísla. Telefonní čísla nacházející se v jiných geografických oblastech ještě nepodporují komunikační služby SMS.
> Další informace najdete v tématu **[typy telefonních čísel](../../concepts/telephony-sms/plan-solution.md)**.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET client library](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript client library](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python client library](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java client library](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>Řešení potíží

Pokud chcete řešit problémy související s doručováním SMS, můžete [Povolit vytváření sestav doručení pomocí Event Grid](./handle-sms-events.md) pro zachycení podrobností o doručení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak odesílat zprávy SMS pomocí komunikačních služeb Azure.

> [!div class="nextstepaction"]
> [Přihlášení k odběru událostí SMS](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [Typy telefonních čísel](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [Další informace o SMS](../../concepts/telephony-sms/concepts.md)
