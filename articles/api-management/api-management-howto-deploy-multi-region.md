---
title: Nasazení služeb Azure API Management do více oblastí Azure
titleSuffix: Azure API Management
description: Zjistěte, jak nasadit instanci služby Azure API Management do více oblastí Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 5c71f37741de06b8633e7eafaae2f29823214f74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442670"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Postup při nasazení instance služby Azure API Management do několika oblastí Azure

Azure API Management podporuje nasazení ve více oblastech, které umožňuje vydavatelům rozhraní API distribuovat jednu službu správy rozhraní Azure API v libovolném počtu podporovaných oblastí Azure. Funkce s více oblastmi pomáhá snížit latenci požadavků vnímanou geograficky distribuovanými příjemci rozhraní API a zlepšuje dostupnost služeb, pokud jedna oblast přejde do offline.

Nová služba Azure API Management zpočátku obsahuje pouze jednu [jednotku][unit] v jedné oblasti Azure, primární oblasti. Další oblasti lze přidat do primární nebo sekundární oblasti. Komponenta brány pro správu rozhraní API se nasadí do každé vybrané primární a sekundární oblasti. Příchozí požadavky rozhraní API jsou automaticky směrovány do nejbližší oblasti. Pokud oblast přejde do offline, požadavky rozhraní API budou automaticky směrovány kolem oblasti selhání na nejbližší nejbližší bránu.

> [!NOTE]
> Do všech oblastí se nasadí pouze součást brány správy rozhraní API. Součást správy služeb a portál pro vývojáře jsou hostovány pouze v oblasti Primární. Proto v případě výpadku primární oblasti, přístup k portálu pro vývojáře a možnost změnit konfiguraci (např. přidání api, použití zásad) bude narušena, dokud primární oblasti přejde zpět do režimu online. Zatímco primární oblast je k dispozici offline Sekundární oblasti budou i nadále obsluhovat provoz rozhraní API pomocí nejnovější konfigurace, která je jim k dispozici.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-an-api-management-service-instance-to-a-new-region"></a><a name="add-region"> </a>Nasazení instance služby Správa rozhraní API do nové oblasti

> [!NOTE]
> Pokud jste ještě nevytvořili instanci služby Api Management, přečtěte si informace [o vytvoření instance služby Správa rozhraní API][create an api management service instance].

Na webu Azure Portal přejděte na stránku **Škálování a ceny** pro instanci služby Správa rozhraní API.

![Karta Změnit velikost][api-management-scale-service]

Chcete-li se nasadit do nové oblasti, klikněte na **+ Přidat oblast** z panelu nástrojů.

![Přidat oblast][api-management-add-region]

Vyberte umístění z rozevíracího seznamu a nastavte počet jednotek pro pomocí posuvníku.

![Určení jednotek][api-management-select-location-units]

Kliknutím na **Přidat** umístěte výběr do tabulky Umístění.

Tento postup opakujte, dokud nebudete mít nakonfigurovaná všechna umístění a proces nasazení spustíte klepnutím na **tlačítko Uložit** z panelu nástrojů.

## <a name="delete-an-api-management-service-instance-from-a-location"></a><a name="remove-region"> </a>Odstranění instance služby Správa rozhraní API z umístění

Na webu Azure Portal přejděte na stránku **Škálování a ceny** pro instanci služby Správa rozhraní API.

![Karta Změnit velikost][api-management-scale-service]

Pro umístění, které chcete odebrat, otevřete kontextovou nabídku pomocí tlačítka **...** na pravém konci tabulky. Vyberte možnost **Odstranit.**

Potvrďte odstranění a kliknutím na **Uložit** aplikujte změny.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>Směrovat volání rozhraní API na místní back-endové služby

Azure API Management obsahuje jenom jednu adresu URL back-endové služby. I když existují instance Azure API Management v různých oblastech, brána rozhraní API bude stále předávat požadavky na stejnou back-endovou službu, která se nasadí pouze v jedné oblasti. V takovém případě zvýšení výkonu bude pocházet pouze z odpovědí uložených v rámci azure api management v oblasti specifické pro požadavek, ale kontaktování back-endu po celém světě může stále způsobit vysokou latenci.

Chcete-li plně využít geografické rozložení vašeho systému, měli byste mít back-endové služby nasazené ve stejných oblastech jako instance Azure API Management. Potom pomocí zásad `@(context.Deployment.Region)` a vlastnosti můžete směrovat provoz do místních instancí back-endu.

1. Přejděte na instanci Azure API Management a klikněte na **rozhraní API** z levé nabídky.
2. Vyberte požadované rozhraní API.
3. V rozevíracím seznamu se šipkou v části **Příchozí zpracování**klepněte na **editor kódu** .

    ![Editor kódu rozhraní API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. V `set-backend` kombinaci s `choose` podmíněnými zásadami vytvořte `<inbound> </inbound>` správnou zásadu směrování v části souboru.

    Níže uvedený soubor XML by například fungoval pro oblasti Západní USA a Východní Asie:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> Můžete také front your back-end služby s [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), přímé volání rozhraní API traffic manager a nechat ji vyřešit směrování automaticky.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Použití vlastního směrování k regionálním bránám správy rozhraní API

Správa rozhraní API směruje požadavky na místní _bránu_ na základě [nejnižší latence](../traffic-manager/traffic-manager-routing-methods.md#performance). I když toto nastavení nelze přepsat ve správě rozhraní API, můžete použít vlastní Traffic Manager s vlastními pravidly směrování.

1. Vytvořte si vlastní [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Pokud používáte vlastní doménu, [použijte ji se Správcem provozu](../traffic-manager/traffic-manager-point-internet-domain.md) namísto služby API Management.
1. [Konfigurace místních koncových bodů správy rozhraní API ve Správci provozu](../traffic-manager/traffic-manager-manage-endpoints.md). Místní koncové body se řídí `https://<service-name>-<region>-01.regional.azure-api.net`například `https://contoso-westus2-01.regional.azure-api.net`vzorem adresy URL .
1. [Konfigurace koncových bodů místního stavu správy rozhraní API ve Správci provozu](../traffic-manager/traffic-manager-monitoring.md). Koncové body místního stavu se `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`řídí `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`například vzorem adresy URL .
1. Zadejte [metodu směrování](../traffic-manager/traffic-manager-routing-methods.md) Traffic Manageru.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png
[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png
[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
