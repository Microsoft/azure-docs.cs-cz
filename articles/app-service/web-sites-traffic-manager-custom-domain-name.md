---
title: Konfigurovat názvy DNS pro aplikace, které používají Traffic Manager – Azure App Service
description: Použijte název vlastní domény webové aplikace ve službě Azure App Service, včetně služby Traffic Manager pro vyrovnávání zatížení.
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
ms.custom: seodec18
ms.openlocfilehash: 5431372abb9a1157b322a8af4a07ba5fa15d8e8e
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720455"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Konfigurace vlastního názvu domény pro webovou aplikaci ve službě Azure App Service pomocí Traffic Manageru
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Tento článek obsahuje obecné pokyny k používání vlastního názvu domény pomocí [služby App Service](overview.md) aplikaci, která je integrovaná s [Traffic Manageru](../traffic-manager/traffic-manager-overview.md) pro vyrovnávání zatížení.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Principy záznamů DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Konfigurace webových aplikací pro standardní režim
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Přidejte záznam DNS pro vaši vlastní doménu
> [!NOTE]
> Pokud jste si koupili domény prostřednictvím Azure App Service Web Apps pak přeskočit následující kroky a odkazovat na poslední krok [koupit domény pro Web Apps](manage-custom-dns-buy-domain.md) článku.
> 
> 

Přidružení vlastní domény s webovou aplikací ve službě Azure App Service, musíte přidat nový záznam v tabulce DNS pro vaši vlastní doménu. Můžete to provést pomocí nástroje pro správu z vašeho poskytovatele domény.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Zatímco podrobností o jednotlivých poskytovatelů domény se liší, můžete namapovat *z* vlastní název domény (například **contoso.com**) *k* název domény Traffic Manageru ( **contoso.trafficmanager.NET**), která je integrovaná s vaší webovou aplikací.
   
> [!NOTE]
> Pokud záznam se už používá a je potřeba preventivně spojit své aplikace, můžete vytvořit další záznam CNAME. Například preventivně svázat **www.contoso.com** do webové aplikace, vytvořte záznam CNAME z **awverify.www** k **contoso.trafficmanager.net**. Potom přidáte "www.contoso.com" do vaší webové aplikace beze změny záznamu CNAME "www". Další informace najdete v tématu [záznamy DNS vytvořit pro webové aplikace ve vlastní doméně][CREATEDNS].
> 
> 

Po dokončení přidávání nebo úprava záznamů DNS u vašeho poskytovatele domény, uložte změny.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Povolit Traffic Manageru
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Další postup
Další informace najdete ve [Středisku pro vývojáře Node.js](https://azure.microsoft.com/develop/nodejs/).

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
