---
title: Azure ExpressRoute CrossConnnections API vývoj a integrace
description: Tento článek obsahuje podrobný přehled pro partnery ExpressRoute o typu prostředku expressRouteCrossConnections.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187013"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>Vývoj a integrace rozhraní API ExpressRoute CrossConnnections

Rozhraní API správce prostředků partnera ExpressRoute umožňuje partnerům ExpressRoute spravovat konfiguraci okruhů ExpressRoute zákazníka 2 a vrstvy 3. Rozhraní API správce prostředků partnera ExpressRoute zavádí nový typ prostředku **expressRouteCrossConnections**. Partneři používají tento prostředek ke správě okruhů ExpressRoute zákazníka.

## <a name="workflow"></a>Pracovní postup

Prostředek expressRouteCrossConnections je stínovým prostředkem okruhu ExpressRoute. Když zákazník Azure vytvoří okruh ExpressRoute a vybere konkrétního partnera ExpressRoute, Microsoft vytvoří prostředek expressRouteCrossConnections v předplatném správy Azure ExpressRoute partnera. Přitom Společnost Microsoft definuje skupinu prostředků k vytvoření prostředku expressRouteCrossConnections. Standard pojmenování pro skupinu prostředků je **CrossConnection-* PeeringLocation***; kde PeeringLocation = Umístění ExpressRoute. Například pokud zákazník vytvoří okruh ExpressRoute v Denveru, crossconnection bude vytvořen v předplnění partnera Azure v následující skupině prostředků: **CrossConnnection-Denver**.

Partneři ExpressRoute spravují konfiguraci vrstvy 2 a vrstvy 3 vydáním operací REST proti prostředku expressRouteCrossConnections.

## <a name="benefits"></a>Výhody

Výhody přechodu na prostředek expressRouteCrossConnections:

* Všechna budoucí vylepšení pro partnery ExpressRoute budou k dispozici v prostředku ExpressRouteCrossConnection.

* Partneři mohou použít [řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview) pro prostředek expressRouteCrossConnection. Tyto ovládací prvky mohou definovat oprávnění, pro která mohou účty uživatelů upravovat prostředek expressRouteCrossConnection a přidávat/aktualizovat/odstraňovat konfigurace partnerského vztahu.

* Prostředek expressRouteCrossConnection zveřejňuje api, která mohou být užitečná při odstraňování potíží s připojeními ExpressRoute. To zahrnuje tabulku ARP, souhrn tabulky postupu protokolu BGP a podrobnosti o tabulce směrování protokolu BGP. Tato funkce není podporována klasickými řešeními API nasazení.

* Partneři mohou také vyhledat inzerované komunity v partnerské společnosti Microsoft pomocí prostředku *RouteFilter.*

## <a name="api-development-and-integration-steps"></a>Kroky vývoje a integrace rozhraní API

K vývoji podle rozhraní API partnera využívají partneři ExpressRoute nastavení testovacího zákazníka a testovacího partnera. Nastavení testovacího zákazníka se použije k vytvoření okruhů ExpressRoute v testovacích partnerských umístěních, které jsou mapovány na fiktivní zařízení a porty. Nastavení testovacího partnera se používá ke správě okruhů ExpressRoute vytvořených v umístění partnerského vztahu testu.

### <a name="1-enlist-subscriptions"></a>1. Zařazení předplatného

Chcete-li požádat o testovacího partnera a otestovat nastavení zákazníka, zadejte dvě předplatná Azure s průběžným platbou do vašeho technického kontaktu ExpressRoute:
* **ExpressRoute_API_Dev_Provider_Sub:** Toto předplatné se použije ke správě okruhů ExpressRoute vytvořených v testovacích partnerských umístěních na fiktivních zařízeních a portech.

* **ExpressRoute_API_Dev_Customer_Sub:** Toto předplatné se použije k vytvoření okruhů ExpressRoute v testovacích partnerských umístěních, které jsou mapovány na fiktivní zařízení a porty.

Umístění partnerského vztahu testu: fiktivní zařízení a porty nejsou ve výchozím nastavení vystaveny produkčním zákazníkům. Chcete-li vytvořit okruhy ExpressRoute, které se mapují na testovací nastavení, je třeba povolit příznak funkce předplatného.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Registrace předplatného Dev_Provider pro přístup k rozhraní API expressRouteCrossConnections

Chcete-li získat přístup k rozhraní API expressRouteCrossConnections, musí být předplatné partnera zapsáno u **zprostředkovatele prostředků sítě Microsoft.Network**. Postupujte podle kroků v článku [poskytovatelé prostředků Azure a typy](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) k dokončení procesu registrace.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Nastavení ověřování pro volání rozhraní REST správce prostředků Azure

Většina služeb Azure vyžaduje klientský kód k ověření pomocí Správce prostředků pomocí platných přihlašovacích údajů před voláním řešení API služby. Ověřování je koordinováno mezi různými aktéry podle Azure AD a poskytuje klientovi přístupový token jako důkaz ověřování.

Proces ověřování zahrnuje dva hlavní kroky:

1. [Zaregistrujte klienta](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Vytvořte žádost o přístup](https://docs.microsoft.com/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. Poskytněte klientské aplikaci oprávnění síťového přispěvatele

Po úspěšné konfiguraci ověřování je třeba udělit přispěvateli sítě přístup k klientské aplikaci v rámci Dev_Provider_Sub. Chcete-li udělit oprávnění, přihlaste se na [portál Azure](https://ms.portal.azure.com/#home) a proveďte následující kroky:

1. Přejděte na Předplatná a vyberte Dev_Provider_Sub
2. Přejít na řízení přístupu (IAM)
3. Přidat přiřazení role
4. Vyberte roli síťového přispěvatele
5. Přiřazení přístupu uživateli, skupině nebo instančnímu objektu Azure AD
6. Výběr klientské aplikace
7. Uložit změny

### <a name="5-develop"></a>5. Rozvíjet

Vyvíjet proti [expressRouteCrossConnections API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections).

## <a name="rest-api"></a>REST API

Viz [ExpressRoute CrossConnections REST API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections) pro dokumentaci rozhraní REST API.

## <a name="next-steps"></a>Další kroky

Další informace o všech apich ExpressRoute REST naleznete v tématu [ExpressRoute REST API](https://docs.microsoft.com/rest/api/expressroute/).