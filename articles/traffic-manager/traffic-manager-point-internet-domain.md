---
title: Nasměrování internetové domény na Traffic Manager – Azure Traffic Manager
description: Tento článek vám pomůže nasměrovat název domény společnosti na název domény Traffic Manageru.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: rohink
ms.openlocfilehash: 4b0f79ba072c813a7639e87025c19a34e65df023
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86183456"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Nasměrování internetové domény společnosti na doménu Azure Traffic Manageru

Když vytvoříte profil Traffic Manageru, Azure mu automaticky přiřadí název DNS. Pokud chcete použít název z vaší zóny DNS, vytvořte záznam DNS CNAME, který se namapuje na název domény vašeho profilu Traffic Manageru. Název domény Traffic Manageru najdete v části **Obecné** na stránce Konfigurace pro příslušný profil Traffic Manageru.

Pokud například chcete, aby název `www.contoso.com` odkazoval na název DNS Traffic Manageru `contoso.trafficmanager.net`, vytvořte následující záznam prostředku DNS:

`www.contoso.com IN CNAME contoso.trafficmanager.net`

Všechny požadavky na provoz na *webové \. contoso.com* se přesměrují na *contoso.trafficmanager.NET*.

> [!IMPORTANT]
> Doménu druhé úrovně, například *contoso.com*, nelze nasměrovat na doménu Traffic Manageru. Standardy protokolu DNS nepovolují záznamy CNAME pro názvy domén druhé úrovně.

## <a name="next-steps"></a>Další kroky

* [Metody směrování Traffic Manageru](traffic-manager-routing-methods.md)
* [Traffic Manager – Zakázání, povolení nebo odstranění profilu](disable-enable-or-delete-a-profile.md)
* [Traffic Manager – Zakázání nebo povolení koncového bodu](disable-or-enable-an-endpoint.md)
