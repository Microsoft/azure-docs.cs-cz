---
title: Nasazení služby Azure API Management Services do několika oblastí Azure
titleSuffix: Azure API Management
description: Naučte se, jak nasadit instanci služby Azure API Management Service do několika oblastí Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 427ebfe865002612be2f9aeb9db416f5c2f41e52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88065450"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Postup při nasazení instance služby Azure API Management do několika oblastí Azure

Azure API Management podporuje nasazení ve více oblastech, což umožňuje vydavatelům rozhraní API distribuovat jednu službu Azure API Management napříč libovolným počtem podporovaných oblastí Azure. Funkce více oblastí pomáhá snižovat latenci požadavku vnímanou uživateli geograficky distribuovaného rozhraní API a vylepšuje dostupnost služby v případě, že jedna oblast přejde do režimu offline.

Nová služba Azure API Management zpočátku obsahuje jenom jednu [jednotku][unit] v jedné oblasti Azure, primární oblasti. Další jednotky lze přidat do primární nebo sekundární oblasti. Komponenta API Management Gateway je nasazená do všech vybraných primárních a sekundárních oblastí. Příchozí požadavky rozhraní API se automaticky přesměrují do nejbližší oblasti. Pokud se oblast přepne do režimu offline, požadavky rozhraní API se automaticky přesměrují kolem neúspěšné oblasti do nejbližší nejbližší brány.

> [!NOTE]
> Do všech oblastí je nasazená jenom součást brány API Management. Komponenta správy služby a portál pro vývojáře se hostují jenom v primární oblasti. Proto se v případě výpadku primární oblasti přístup k portálu pro vývojáře a možnosti změny konfigurace (např. Přidání rozhraní API, použití zásad) ztratí, dokud se primární oblast znovu nevrátí do režimu online. I když je primární oblast offline dostupná, sekundární oblasti budou dál obsluhovat přenosy rozhraní API, a to pomocí nejnovější konfigurace, která je pro ně dostupná.

>[!IMPORTANT]
> Funkce pro ukládání zákaznických dat v jedné oblasti je v tuto chvíli dostupná jenom v oblasti jihovýchodní Asie (Singapur) Asie a Tichomoří geografické oblasti. Pro všechny ostatní oblasti jsou zákaznická data uložená v geograficky.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-api-management-service-to-a-new-region"></a><a name="add-region"> </a>Nasazení služby API Management Service do nové oblasti

> [!NOTE]
> Pokud jste ještě nevytvořili instanci služby API Management, přečtěte si téma [vytvoření instance služby API Management][create an api management service instance].

1. V Azure Portal přejděte do služby API Management a v nabídce klikněte na položku **umístění** .
2. Klikněte na tlačítko **+ Přidat** v horním panelu.
3. V rozevíracím seznamu vyberte umístění a nastavte počet jednotek pomocí posuvníku.
4. Potvrďte kliknutím na tlačítko **Přidat** .
5. Tento postup opakujte, dokud nebudete konfigurovat všechna umístění.
6. Kliknutím na **Uložit** na horním panelu spusťte proces nasazení.

## <a name="delete-an-api-management-service-location"></a><a name="remove-region"> </a>Odstranění umístění služby API Management

1. V Azure Portal přejděte do služby API Management a v nabídce klikněte na položku **umístění** .
2. Pro umístění, které chcete odebrat, otevřete místní nabídku pomocí tlačítka **...** na pravé straně tabulky. Vyberte možnost **Odstranit** .
3. Potvrďte odstranění a klikněte na **Uložit** , aby se změny projevily.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>Směrování volání rozhraní API do regionálních back-end služeb

Azure API Management nabízí jenom jednu adresu URL back-end služby. I když v různých oblastech existují instance Azure API Management, brána API dál předává požadavky do stejné back-endové služby, která je nasazená jenom v jedné oblasti. V takovém případě se zisk výkonu dostane jenom z odpovědí uložených v mezipaměti v rámci Azure API Management v oblasti specifické pro požadavek, ale při kontaktování back-endu na celém světě může stále dojít k vysoké latenci.

Aby bylo možné plně využít geografickou distribuci vašeho systému, měli byste mít nasazené back-end služby ve stejných oblastech jako instance Azure API Management. Potom můžete pomocí zásad a `@(context.Deployment.Region)` vlastností směrovat provoz do místních instancí vašeho back-endu.

1. Přejděte do instance Azure API Management a v nabídce vlevo klikněte na **rozhraní API** .
2. Vyberte požadované rozhraní API.
3. V rozevíracím seznamu šipky v **příchozím zpracování** klikněte na **Editor kódu** .

    ![Editor kódu rozhraní API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Použijte `set-backend` kombinaci s podmíněnými `choose` zásadami k vytvoření správné zásady směrování v `<inbound> </inbound>` části souboru.

    Například následující soubor XML by fungoval pro Západní USA a Východní Asie oblasti:

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
> Back-endové služby můžete také napředt pomocí [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), nasměrovat volání rozhraní API do Traffic Manager a nechat ho automaticky vyřešit.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Použití vlastního směrování k API Management regionálních bran

API Management směruje požadavky na místní _bránu_ na základě [nejnižší latence](../traffic-manager/traffic-manager-routing-methods.md#performance). I když není možné toto nastavení v API Management přepsat, můžete použít vlastní Traffic Manager s vlastními pravidly směrování.

1. Vytvořte si vlastní [Traffic Manager Azure](https://azure.microsoft.com/services/traffic-manager/).
1. Pokud používáte vlastní doménu, [použijte ji s Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) namísto služby API Management.
1. [Nakonfigurujte API Management místní koncové body v Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). Regionální koncové body následují jako vzor adresy URL `https://<service-name>-<region>-01.regional.azure-api.net` , například `https://contoso-westus2-01.regional.azure-api.net` .
1. [Konfigurace API Managementch koncových bodů regionálního stavu v Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). Místní koncové body stavu se řídí vzorem adresy URL `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef` , například `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef` .
1. Zadejte [metodu směrování](../traffic-manager/traffic-manager-routing-methods.md) Traffic Manager.

[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
