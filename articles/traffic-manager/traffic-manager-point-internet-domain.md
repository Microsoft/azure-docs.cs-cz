---
title: Nasměrování internetové domény společnosti na název domény Azure Traffic Manager | Dokumentace Microsoftu
description: Tento článek vám pomůže nasměrovat název domény společnosti na název domény Traffic Manageru.
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
ms.openlocfilehash: 2b9b718a61d13790de38ea82f9b479e372e9de63
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052679"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Nasměrování internetové domény společnosti na doménu Azure Traffic Manageru

Když vytvoříte profil Traffic Manageru, Azure mu automaticky přiřadí název DNS. Pokud chcete použít název z vaší zóny DNS, vytvořte záznam DNS CNAME, který se namapuje na název domény vašeho profilu Traffic Manageru. Název domény Traffic Manageru najdete v části **Obecné** na stránce Konfigurace pro příslušný profil Traffic Manageru.

Pokud například chcete, aby název `www.contoso.com` odkazoval na název DNS Traffic Manageru `contoso.trafficmanager.net`, vytvořte následující záznam prostředku DNS:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Veškeré žádosti o přenos na *www.contoso.com* se budou směrovat na *contoso.trafficmanager.net*.

> [!IMPORTANT]
> Doménu druhé úrovně, například *contoso.com*, nelze nasměrovat na doménu Traffic Manageru. Standardy protokolu DNS nepovolují záznamy CNAME pro názvy domén druhé úrovně.

## <a name="next-steps"></a>Další postup

* [Metody směrování Traffic Manageru](traffic-manager-routing-methods.md)
* [Traffic Manager – Zakázání, povolení nebo odstranění profilu](disable-enable-or-delete-a-profile.md)
* [Traffic Manager – Zakázání nebo povolení koncového bodu](disable-or-enable-an-endpoint.md)
