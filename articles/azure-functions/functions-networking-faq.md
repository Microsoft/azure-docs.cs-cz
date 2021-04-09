---
title: Nejčastější dotazy týkající se sítě v Azure Functions
description: Odpovědi na některé z nejběžnějších otázek a scénářů pro síť s Azure Functions.
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.reviewer: glenga
ms.openlocfilehash: 24afeeee3207127bb9404156dc390433671dd5da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592298"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Nejčastější dotazy týkající se sítě v Azure Functions

V tomto článku jsou uvedeny nejčastější dotazy týkající se sítě v Azure Functions. Komplexnější přehled najdete v tématu [funkce síťových možností](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Návody nastavit statickou IP adresu ve funkcích Functions?

Nasazení funkce v App Service Environment je primárním způsobem, jak mít statické příchozí a odchozí IP adresy pro vaše funkce. Podrobné informace o použití App Service Environment začněte s článkem [Vytvoření a použití interního nástroje pro vyrovnávání zatížení s App Service Environment](../app-service/environment/create-ilb-ase.md).

Můžete také použít bránu NAT virtuální sítě ke směrování odchozího provozu prostřednictvím veřejné IP adresy, kterou ovládáte. Další informace najdete v tématu [kurz: řízení Azure Functions odchozí IP adresy pomocí brány NAT služby Azure Virtual Network](functions-how-to-use-nat-gateway.md). 

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Návody omezit přístup k Internetu na moji funkci?

Přístup k Internetu můžete omezit několika způsoby:

* [Omezení IP adres](../app-service/app-service-ip-restrictions.md): umožňuje omezit příchozí provoz do aplikace Function za rozsah IP adres.
    * V části omezení protokolu IP můžete také nakonfigurovat [koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md), které omezují, aby funkce přijímala příchozí provoz jenom z konkrétní virtuální sítě.
* Odebrání všech triggerů HTTP. U některých aplikací je stačí jednoduše zabránit aktivačním událostem HTTP a použít k aktivaci funkce jiný zdroj událostí.

Mějte na paměti, že editor Azure Portal vyžaduje přímý přístup ke spuštěným funkcím. Jakékoli změny kódu prostřednictvím Azure Portal budou vyžadovat, aby zařízení, které používáte k procházení portálu, mělo své IP adresy přidané do seznamu schválených. Můžete ale pořád použít cokoli na kartě funkce platformy se zavedenými omezeními sítě.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Návody omezit aplikaci Function App na virtuální síť?

**Příchozí** provoz pro aplikaci Function App můžete omezit na virtuální síť pomocí [koncových bodů služby](./functions-networking-options.md#use-service-endpoints). Tato konfigurace pořád umožňuje, aby aplikace Functions provedla odchozí volání na Internet.

Pokud chcete úplně omezit funkci tak, aby všechny přenosy přes virtuální síť prošly, můžete použít [privátní koncové body](./functions-networking-options.md#private-endpoint-connections) s integrací virtuální sítě nebo App Service Environment. Další informace najdete v tématu věnovaném [integraci Azure Functions s Azure Virtual Network pomocí privátních koncových bodů](functions-create-vnet.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Jak můžu získat přístup k prostředkům ve virtuální síti z aplikace Function App?

K prostředkům ve virtuální síti můžete přistupovat ze spuštěné funkce pomocí integrace virtuální sítě. Další informace najdete v tématu [Integrace virtuální sítě](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Návody přístup k prostředkům chráněným koncovými body služby?

Pomocí integrace virtuální sítě můžete získat přístup k prostředkům zabezpečeným koncovým bodem z běžící funkce. Další informace najdete v tématu [Integrace virtuální sítě](functions-networking-options.md#virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Jak můžu aktivovat funkci z prostředku ve virtuální síti?

Můžete umožnit volání aktivačních událostí HTTP z virtuální sítě pomocí [koncových bodů služby](./functions-networking-options.md#use-service-endpoints) nebo [připojení privátního koncového bodu](./functions-networking-options.md#private-endpoint-connections). 

Můžete také aktivovat funkci ze všech ostatních prostředků ve virtuální síti nasazením aplikace Function App do plánu Premium, App Service plánu nebo App Service Environment. Další informace najdete v tématu [triggery jiné virtuální sítě než http](./functions-networking-options.md#virtual-network-triggers-non-http) .

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Jak můžu nasadit aplikaci Function App ve virtuální síti?

Nasazení do App Service Environment je jediným způsobem, jak vytvořit aplikaci Function App, která je zcela uvnitř virtuální sítě. Podrobnosti o používání interního nástroje pro vyrovnávání zatížení s App Service Environment můžete začít s článkem [Vytvoření a použití interního nástroje pro vyrovnávání zatížení s App Service Environment](../app-service/environment/create-ilb-ase.md).

V případě scénářů, kdy potřebujete pouze jednosměrný přístup k prostředkům virtuální sítě nebo méně komplexní izolaci sítě, přečtěte si téma [Přehled funkcí sítě](functions-networking-options.md).

## <a name="next-steps"></a>Další kroky

Další informace o sítích a funkcích: 

* [Postup Začínáme s integrací virtuální sítě najdete v kurzu.](./functions-create-vnet.md)
* [Přečtěte si další informace o možnostech sítě v Azure Functions](./functions-networking-options.md)
* [Další informace o integraci virtuální sítě s App Service a funkcemi](../app-service/web-sites-integrate-with-vnet.md)
* [Další informace o virtuálních sítích v Azure](../virtual-network/virtual-networks-overview.md)
* [Povolení více síťových funkcí a řízení pomocí App Servicech prostředí](../app-service/environment/intro.md)
