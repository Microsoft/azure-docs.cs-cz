---
title: Vývoj a integrace rozhraní API Azure ExpressRoute CrossConnnections
description: Tento článek poskytuje podrobný přehled pro partnery ExpressRoute o typu prostředku expressRouteCrossConnections.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: b80392231dba26a10141dcd8247b092e8171894c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011978"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>Vývoj a integrace rozhraní ExpressRoute CrossConnnections API

ExpressRoute partner Správce prostředků API umožňuje partnerům ExpressRoute spravovat konfiguraci ExpressRoute okruhů ve vrstvě 2 a 3. ExpressRoute partner Správce prostředků rozhraní API zavádí nový typ prostředku **expressRouteCrossConnections**. Partneři používají tento prostředek k řízení okruhů zákaznických ExpressRoute.

## <a name="workflow"></a>Pracovní postup

Prostředek expressRouteCrossConnections je stínový prostředek k okruhu ExpressRoute. Když zákazník Azure vytvoří okruh ExpressRoute a vybere konkrétního partnera ExpressRoute, Microsoft vytvoří prostředek expressRouteCrossConnections v předplatném Azure ExpressRoute Management partnera. V takovém případě Microsoft definuje skupinu prostředků, ve které se vytvoří prostředek expressRouteCrossConnections. Standardní pojmenování pro skupinu prostředků je **CrossConnection-* PeeringLocation * * *; kde PeeringLocation = umístění ExpressRoute. Pokud třeba zákazník vytvoří okruh ExpressRoute v Denver, vytvoří se CrossConnection v předplatném Azure partnera v následující skupině prostředků: **CrossConnnection-Denver**.

ExpressRoute partneři spravují konfiguraci vrstvy 2 a vrstvy 3 tím, že vydávají operace REST v prostředku expressRouteCrossConnections.

## <a name="benefits"></a>Výhody

Výhody přechodu na prostředek expressRouteCrossConnections:

* Všechna budoucí vylepšení pro partnery ExpressRoute budou zpřístupněna v prostředku ExpressRouteCrossConnection.

* Partneři můžou použít [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md) na prostředek expressRouteCrossConnection. Tyto ovládací prvky mohou definovat oprávnění, pro které mohou uživatelské účty upravovat prostředek expressRouteCrossConnection a konfigurace partnerských vztahů Přidat/aktualizovat/odstranit.

* Prostředek expressRouteCrossConnection zpřístupňuje rozhraní API, která můžou být užitečná při řešení potíží s připojením ExpressRoute. Patří sem tabulka ARP, Souhrn tabulky směrování BGP a podrobnosti tabulky směrování protokolu BGP. Tato schopnost není podporována rozhraními API pro nasazení Classic.

* Partneři mohou také vyhledat inzerované komunity na partnerském vztahu Microsoftu pomocí prostředku *RouteFilter* .

## <a name="api-development-and-integration-steps"></a>Postup pro vývoj a integraci rozhraní API

Pro vývoj na partnerském rozhraní API partneři ExpressRoute využívají testovacího zákazníka a testovací nastavení partnerů. Nastavení test zákazníka se použije k vytvoření okruhů ExpressRoute v umístěních s testovacím partnerským vztahem, které se mapují na fiktivní zařízení a porty. Testovací nastavení partnera se používá ke správě okruhů ExpressRoute vytvořených v umístění testovacího partnerského vztahu.

### <a name="1-enlist-subscriptions"></a>1. zařazení předplatných

Pokud chcete požádat o test partnera a otestovat zákaznickou instalaci, zaúčtujte dva předplatná Azure s průběžnými platbami na váš kontakt ExpressRoute Engineering:
* **ExpressRoute_API_Dev_Provider_Sub:** Toto předplatné se bude používat ke správě okruhů ExpressRoute vytvořených v umístěních pro testování partnerských vztahů na fiktivních zařízeních a portech.

* **ExpressRoute_API_Dev_Customer_Sub:** Toto předplatné se použije k vytvoření okruhů ExpressRoute v umístěních s testovacím partnerským vztahem, které se mapují na fiktivní zařízení a porty.

Umístění pro testování partnerských vztahů: fiktivní zařízení a porty nejsou ve výchozím nastavení vystaveny zákazníkům v produkčním prostředí. Aby bylo možné vytvořit okruhy ExpressRoute, které jsou mapovány na nastavení testu, je nutné povolit příznak funkce předplatného.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Zaregistrujte si předplatné Dev_Provider pro přístup k rozhraní expressRouteCrossConnections API

Aby bylo možné získat přístup k rozhraní expressRouteCrossConnections API, musí být partnerský odběr zaregistrovaný v **poskytovateli prostředků Microsoft. Network**. K dokončení procesu registrace použijte postup v článku [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) .

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. nastavení ověřování pro Azure Resource Manager volání REST API

Většina služeb Azure vyžaduje klientský kód k ověření pomocí Správce prostředků pomocí platných přihlašovacích údajů před voláním rozhraní API služby. Ověřování je koordinováno mezi různými aktéry pomocí Azure AD a poskytuje klientovi přístupový token jako ověření ověřování.

Proces ověřování zahrnuje dva hlavní kroky:

1. [Zaregistrujte klienta](/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Vytvořte žádost o přístup](/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. Poskytněte klientské aplikaci oprávnění přispěvatele sítě.

Po úspěšném nakonfigurování ověření musíte v rámci Dev_Provider_Sub udělit přístup přispěvateli sítě ke své klientské aplikaci. Pokud chcete udělit oprávnění, přihlaste se k [Azure Portal](https://ms.portal.azure.com/#home) a proveďte následující kroky:

1. Přejděte na předplatná a vyberte Dev_Provider_Sub
2. Přejít na Access Control (IAM)
3. Přidat přiřazení role
4. Vybrat roli Přispěvatel sítě
5. Přiřazení přístupu k uživateli, skupině nebo instančnímu objektu služby Azure AD
6. Vybrat klientskou aplikaci
7. Uložit změny

### <a name="5-develop"></a>5. vývoj

Vývoj s využitím [rozhraní expressRouteCrossConnections API](/rest/api/expressroute/expressroutecrossconnections)

## <a name="rest-api"></a>REST API

Dokumentaci k REST API najdete v tématu [ExpressRoute CrossConnections REST API](/rest/api/expressroute/expressroutecrossconnections) .

## <a name="next-steps"></a>Další kroky

Další informace o všech ExpressRoute REST API najdete v tématu [EXPRESSROUTE REST API](/rest/api/expressroute/).