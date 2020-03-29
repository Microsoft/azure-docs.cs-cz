---
title: Nejčastější dotazy týkající se sítí ve funkcích Azure
description: Odpovědi na některé z nejčastějších otázek a scénářů pro vytváření sítí s Funkcemi Azure.
author: alexkarcher-msft
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: acb1e942c1f342ce6fee7d8aeacafcc1d7b6fd91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75409533"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Nejčastější dotazy týkající se sítí ve funkcích Azure

V tomto článku jsou uvedeny nejčastější dotazy týkající se sítí ve funkcích Azure. Podrobnější přehled naleznete v tématu [Funkce možnosti sítě](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Jak nastavím statickou IP adresu ve funkcích?

Nasazení funkce v prostředí služby App Service je aktuálně jediný způsob, jak mít statickou příchozí a odchozí IP adresu pro vaši funkci. Podrobnosti o používání prostředí služby App Service nastoupte v článku [Vytvoření a použití interního vyvyčovávače zatížení s prostředím služby App Service](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Jak mohu omezit přístup k internetu na svou funkci?

Přístup k internetu můžete omezit několika způsoby:

* [Omezení IP](../app-service/app-service-ip-restrictions.md)adres: Omezte příchozí provoz na aplikaci funkcí podle rozsahu IP adres.
    * V rámci omezení IP můžete také nakonfigurovat [koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md), které omezují funkci tak, aby přijímala pouze příchozí přenosy z určité virtuální sítě.
* Odebrání všech aktivačních událostí PROTOKOLU HTTP. U některých aplikací stačí jednoduše vyhnout aktivační události HTTP a použít jakýkoli jiný zdroj událostí k aktivaci funkce.

Mějte na paměti, že editor portálu Azure vyžaduje přímý přístup k vaší spuštěné funkci. Všechny změny kódu prostřednictvím portálu Azure budou vyžadovat, aby zařízení, které používáte k procházení portálu, bylo na seznamu ip povolených. Ale stále můžete použít cokoliv pod záložkou funkce platformy s síťovými omezeními na místě.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Jak můžu omezit svou aplikaci funkcí na virtuální síť?

Můžete omezit **příchozí** provoz pro aplikaci funkce na virtuální síť pomocí [koncových bodů služby](./functions-networking-options.md#private-site-access). Tato konfigurace stále umožňuje aplikaci funkce provádět odchozí volání na internet.

Jediný způsob, jak zcela omezit funkci tak, že veškerý provoz toky prostřednictvím virtuální sítě je použití prostředí služby App Service s vyrovnáváním zatížení. Tato možnost nasazuje váš web na vyhrazené infrastruktuře uvnitř virtuální sítě a odesílá všechny aktivační události a provoz prostřednictvím virtuální sítě. 

Podrobnosti o používání prostředí služby App Service nastoupte v článku [Vytvoření a použití interního vyvyčovávače zatížení s prostředím služby App Service](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Jak můžu získat přístup k prostředkům ve virtuální síti z aplikace funkcí?

K prostředkům ve virtuální síti můžete přistupovat z běžící funkce pomocí integrace virtuální sítě. Další informace naleznete v tématu [Integrace virtuální sítě](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Jak lze získat přístup k prostředkům chráněným koncovými body služby?

Pomocí integrace virtuální sítě můžete přistupovat k prostředkům zabezpečeným koncovým bodem služby z běžící funkce. Další informace naleznete v tématu [integrace virtuální sítě](functions-networking-options.md#virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Jak lze aktivovat funkci z prostředku ve virtuální síti?

Můžete povolit aktivační události PROTOKOLU HTTP, které mají být volány z virtuální sítě pomocí [koncových bodů služby](./functions-networking-options.md#private-site-access). 

Můžete také aktivovat funkci ze všech ostatních prostředků ve virtuální síti nasazením aplikace funkce do plánu Premium, plán služby App Service nebo prostředí služby App Service. Další informace naleznete v [tématu aktivační události virtuální sítě bez protokolu HTTP](./functions-networking-options.md#virtual-network-triggers-non-http)

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Jak můžu nasadit svou aplikaci funkcí ve virtuální síti?

Nasazení do prostředí služby App Service je jediný způsob, jak vytvořit aplikaci funkce, která je zcela uvnitř virtuální sítě. Podrobnosti o použití interního vyvyčovávače zatížení s prostředím služby App Service, začněte s [článkem Vytvořit a použít interní vyrovnávání zatížení s prostředím služby App Service](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Pro scénáře, kde potřebujete pouze jednosměrný přístup k prostředkům virtuální sítě nebo méně komplexní izolaci sítě, naleznete v [přehledu sítí Functions](functions-networking-options.md).

## <a name="next-steps"></a>Další kroky

Další informace o vytváření sítí a funkcích: 

* [Postupujte podle kurzu o začínáme s integrací virtuální sítě](./functions-create-vnet.md)
* [Další informace o možnostech sítě v Azure Functions](./functions-networking-options.md)
* [Další informace o integraci virtuálních sítí pomocí služby App Service a funkcí](../app-service/web-sites-integrate-with-vnet.md)
* [Další informace o virtuálních sítích v Azure](../virtual-network/virtual-networks-overview.md)
* [Povolení dalších síťových funkcí a řízení pomocí prostředí služby App Service](../app-service/environment/intro.md)
