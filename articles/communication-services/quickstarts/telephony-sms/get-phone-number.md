---
title: Rychlý Start – Správa telefonních čísel pomocí komunikačních služeb Azure
description: Naučte se spravovat telefonní čísla pomocí komunikačních služeb Azure.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: e28b8581342824032921ad75e0297b3604fc0bb4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629339"
---
# <a name="quickstart-manage-phone-numbers"></a>Rychlý Start: Správa telefonních čísel

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Začněte s komunikačními službami Azure pomocí Azure Portal nebo telefonních služeb telefonní čísla ke správě telefonních čísel.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/phone-numbers-portal.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Azure portal](./includes/phone-numbers-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/phone-numbers-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/phone-numbers-python.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/phone-numbers-js.md)]
::: zone-end

## <a name="troubleshooting"></a>Řešení potíží

Běžné otázky a problémy:

- Nákup telefonu se podporuje jenom v USA. Pokud si chcete koupit telefonní čísla, ujistěte se, že:
  - Přidružená fakturační adresa předplatného Azure se nachází v USA. V tuto chvíli nemůžete přesunout prostředek do jiného předplatného.
  - Prostředek služby Communications se zřídí v umístění USA dat. V tuto chvíli nemůžete přesunout prostředek do jiného umístění dat.

- Po uvolnění telefonního čísla se telefonní číslo uvolní nebo nebude možné ho znovu koupit až do konce fakturačního cyklu.

- Po odstranění prostředku služby komunikace se budou automaticky vydávat telefonní čísla přidružená k tomuto prostředku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak:

> [!div class="checklist"]
> * Koupit telefonní číslo
> * Správa telefonního čísla
> * Vydání telefonního čísla

> [!div class="nextstepaction"]
> [Odeslat SMS](../telephony-sms/send.md) 
>  [Začínáme s voláním](../voice-video-calling/getting-started-with-calling.md)
