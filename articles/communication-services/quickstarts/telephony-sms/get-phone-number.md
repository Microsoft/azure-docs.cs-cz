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
ms.openlocfilehash: 19bb79f9a4deaebfacc75918c46a5516d2d398be
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498185"
---
# <a name="quickstart-manage-phone-numbers"></a>Rychlý Start: Správa telefonních čísel

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

[!INCLUDE [Bulk Acquisition Instructions](../../includes/phone-number-special-order.md)]

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
