---
title: Konfigurace vlastního názvu domény pro webovou aplikaci v Azure App Service, která používá Traffic Manager pro vyrovnávání zatížení.
description: Vlastní název domény pro použití webové aplikace ve službě Azure App Service, která zahrnuje Traffic Manager pro vyrovnávání zatížení.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.openlocfilehash: c78fb7883559e46ebaa1d8dab59a15c55fb76fdf
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2018
ms.locfileid: "27713847"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Konfigurace vlastního názvu domény pro webovou aplikaci v Azure App Service pomocí Traffic Manager
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Tento článek obsahuje obecné pokyny pro používání vlastního názvu domény s [služby App Service](app-service-web-overview.md) aplikace, která je integrovaná s [Traffic Manager](../traffic-manager/traffic-manager-overview.md) pro vyrovnávání zatížení.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Principy záznamy DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Konfigurace webových aplikacích pro standardní režim
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Přidejte záznam DNS pro vaši vlastní doménu.
> [!NOTE]
> Pokud jste zakoupili domény prostřednictvím Azure App Service Web Apps přeskočit následující kroky a odkazovat na posledním kroku [koupit domény pro webové aplikace](custom-dns-web-site-buydomains-web-app.md) článku.
> 
> 

Pokud chcete přiřadit vlastní domény webové aplikace ve službě Azure App Service, musíte přidat nový záznam v tabulce DNS pro vaši vlastní doménu. Můžete to provést pomocí nástroje pro správu od poskytovatele domény.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Při specifika jednotlivých poskytovatelů domény se liší, namapujete *z* vlastního názvu domény (například **contoso.com**) *k* název domény Traffic Manageru ( **contoso.trafficmanager.NET**) je integrována vaší webové aplikace.
   
> [!NOTE]
> Pokud záznam se už používá a je nutné ho preventivně svázat aplikace k němu, můžete vytvořit další záznam CNAME. Například ho preventivně vazby **www.contoso.com** do webové aplikace, vytvořte záznam CNAME z **awverify.www** k **contoso.trafficmanager.net**. Potom můžete přidat "www.contoso.com" do vaší webové aplikace beze změny záznam CNAME "www". Další informace najdete v tématu [záznamy DNS vytvořit pro webové aplikace ve vlastní doménu][CREATEDNS].
> 
> 

Jakmile dokončíte přidání nebo úprava záznamů DNS u svého poskytovatele domény, uložte změny.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Povolit správce provozu
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Další postup
Další informace najdete ve [Středisku pro vývojáře Node.js](/develop/nodejs/).

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
