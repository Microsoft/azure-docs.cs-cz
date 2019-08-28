---
title: Konfigurace názvů DNS pro aplikace, které používají Traffic Manager-Azure App Service
description: Použijte vlastní název domény pro webovou aplikaci v Azure App Service, která zahrnuje Traffic Manager pro vyrovnávání zatížení.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e6a59d519ae8bb515ab16632bd39509682959f50
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074036"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Konfigurace vlastního názvu domény pro webovou aplikaci v Azure App Service pomocí Traffic Manager
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Tento článek poskytuje obecné pokyny k používání vlastního názvu domény s aplikací [App Service](overview.md) , která je integrovaná s [Traffic Manager](../traffic-manager/traffic-manager-overview.md) pro vyrovnávání zatížení.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Porozumění záznamům DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Konfigurace webových aplikací pro standardní režim
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Přidat záznam DNS pro vlastní doménu
> [!NOTE]
> Pokud jste si zakoupili doménu prostřednictvím Azure App Service Web Apps pak přeskočte následující kroky a přečtěte si článek o tom, jaký je v posledním kroku [koupit doménu pro Web Apps](manage-custom-dns-buy-domain.md) .
> 
> 

K přidružení vlastní domény k webové aplikaci v Azure App Service musíte přidat novou položku v tabulce DNS pro vaši vlastní doménu. Provedete to pomocí nástrojů pro správu od svého poskytovatele domény.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

I když se konkrétní poskytovatelé domény liší, namapujete si *z* vlastního názvu domény (například **contoso.com**) *na* název domény služby Traffic Manager (**contoso.trafficmanager.NET**), který je integrovaný do vaší webové aplikace.

> [!NOTE]
> Pokud se záznam už používá a vy k němu potřebujete na něj navazovat vazby, můžete vytvořit další záznam CNAME. Pokud třeba chcete do webové aplikace bez přerušení navazovat vazby **webové\.contoso.com** , vytvořte záznam CNAME z **awverify. www** do **contoso.trafficmanager.NET**. Do své webové aplikace pak můžete\.přidat "www contoso.com", aniž byste změnili záznam CNAME "www". Další informace najdete v tématu [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně][CREATEDNS].

Až dokončíte přidávání nebo úpravu záznamů DNS u svého poskytovatele domény, uložte změny.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Povolit Traffic Manager
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Další postup
Další informace najdete ve [Středisku pro vývojáře Node.js](https://azure.microsoft.com/develop/nodejs/).

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
