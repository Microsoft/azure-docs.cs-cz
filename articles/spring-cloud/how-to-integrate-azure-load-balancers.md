---
title: Kurz – integrace jarního cloudu Azure s řešeními pro vyrovnávání zatížení Azure
description: Jak integrovat Azure jaře Cloud s řešeními vyrovnávání zatížení Azure
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 50b09fd82461221ae6cd008f6918ac2f3a26fd94
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877834"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>Integrace Azure Spring Cloudu se řešeními pro vyrovnávání zatížení Azure

**Tento článek se týká:** ✔️ Java ✔️ C #

Azure jaře Cloud podporuje mikroslužby v Azure.  Zvýšení firmy může vyžadovat více datových center se správou více instancí Azure Pramenitého cloudu.

Azure už poskytuje různá řešení pro vyrovnávání zatížení. Existují tři možnosti, jak integrovat Azure jaře Cloud s řešeními vyrovnávání zatížení Azure:

1.  Integrace jarního cloudu Azure s využitím Azure Traffic Manager
2.  Integrace jarního cloudu Azure s využitím služby Azure App Gateway
3.  Integrace jarního cloudu Azure s využitím front-dveří Azure

## <a name="prerequisites"></a>Předpoklady

* Azure jaře Cloud: [jak vytvořit cloudovou službu Azure na jaře](./spring-cloud-quickstart.md)
* Azure Traffic Manager: [Postup vytvoření Traffic Manageru](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* Azure App Gateway: [jak vytvořit Aplikační bránu](../application-gateway/quick-create-portal.md)
* Přední dvířka Azure: [jak vytvořit frontu](../frontdoor/quickstart-create-front-door.md)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>Integrace jarního cloudu Azure s využitím Azure Traffic Manager

Pokud chcete integrovat Azure jaře Cloud s Traffic Manager, přidejte jeho veřejné koncové body jako koncové body Traffic Manageru a pak nakonfigurujte vlastní doménu pro Traffic Manager i pro jarní cloud Azure.

### <a name="add-endpoint-in-traffic-manager"></a>Přidat koncový bod v Traffic Manager
Přidat koncové body v Traffic Manageru:
1.  Zadejte **typ** , který bude *externím koncovým bodem*.
1.  Zadejte plně kvalifikovaný název domény (FQDN) každého veřejného koncového bodu cloudu služby Azure jaře.
1. Klikněte na **OK**.

    ![Traffic Manager 1 ](media/spring-cloud-load-balancers/traffic-manager-1.png) ![ Traffic Manager 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>Konfigurace vlastní domény
Dokončení konfigurace:
1.  Přihlaste se k webu vašeho poskytovatele domény a vytvořte mapování záznamů CNAME z vlastní domény na výchozí název domény Azure Traffic Manageru.
1.  Postupujte podle pokynů, [jak přidat vlastní doménu do jarního cloudu Azure](spring-cloud-tutorial-custom-domain.md).
1. Přidejte výše vlastní vazbu domény do Traffic Manageru do služby Azure jaře Cloud odpovídající App Service a nahrajte do něj certifikát SSL.

    ![Traffic Manager 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>Integrace jarního cloudu Azure s využitím služby Azure App Gateway

Pokud chcete integrovat se službou Azure jaře Cloud Service, dokončete následující konfigurace:

### <a name="configure-backend-pool"></a>Konfigurovat back-end fond
1. Zadejte **cílový typ** jako *IP adresu* nebo *plně kvalifikovaný název domény*.
1. Zadejte svoje veřejné koncové body cloudového cloudu Azure.

    ![Aplikační brána 1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>Přidat vlastní test paměti
1. Vyberte **sondy stavu** a potom **Přidat** k otevření dialogového okna vlastní **test paměti** . 
1. Klíčovým bodem je výběr možnosti *Ano* pro možnost vybrat **název hostitele z nastavení http back-endu** .

    ![Aplikační brána 2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>Konfigurovat nastavení http
1.  Vyberte **Nastavení http** a pak **Přidat** a přidejte nastavení http.
1.  **Přepsat novým názvem hostitele:** vyberte *Ano*.
1.  **Přepsání názvu hostitele**: vyberte vybrat **název hostitele z back-endu cíle**.
1.  **Použít vlastní test paměti**: vyberte *Ano* a vyberte vlastní test, který jste vytvořili výše.

    ![Aplikační brána 3](media/spring-cloud-load-balancers/app-gateway-3.png)

### <a name="configure-rewrite-set"></a>Konfigurovat sadu přepsání
1.  Vyberte **přepsání** a potom **sadu přepište** a přidejte sadu přepsání.
1.  Vyberte pravidla směrování, která směrují požadavky na veřejné koncové body služby Azure jaře Cloud.
1.  Na kartě **Konfigurace pravidla přepsaní** vyberte **Přidat pravidlo přepsání**.
1.  **Typ přepsání**: vyberte **hlavičku požadavku** .
1.  **Typ akce**: vyberte **Odstranit** .
1.  **Název záhlaví**: vyberte **společné záhlaví** .
1.  **Společné záhlaví**: vyberte **X-předané – proto**

    ![Brána aplikace 4](media/spring-cloud-load-balancers/app-gateway-4.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>Integrace jarního cloudu Azure s využitím front-dveří Azure

Integrace se službou Azure jarní Cloud Service a konfigurací back-end fondu 
1. **Přidejte back-end fond**.
1. Zadejte koncový bod back-end přidáním hostitele.

    ![Přední dveře 1](media/spring-cloud-load-balancers/front-door-1.png)

1.  Jako *vlastní hostitel* zadejte **typ hostitele back-endu** .
1.  Zadejte plně kvalifikovaný název domény pro veřejné koncové body cloudového cloudu Azure v **názvu hostitele back-endu**.
1.  Přijměte výchozí **hlavičku hostitele back-end** , která je stejná jako **název hostitele back-endu**.

    ![Přední dveře 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>Další kroky
* [Postup vytvoření Traffic Manageru](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* [Postup vytvoření aplikační brány](../application-gateway/quick-create-portal.md)
* [Vytvoření front-dveří](../frontdoor/quickstart-create-front-door.md)