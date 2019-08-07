---
title: Nejčastější dotazy týkající se sítě v Azure Functions
description: Odpovědi na některé z nejběžnějších otázek a scénářů pro síť s Azure Functions.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 6f363003dc24509bd0b80922d9e34560250cc7ed
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779311"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Nejčastější dotazy týkající se sítě v Azure Functions

V tomto článku jsou uvedeny nejčastější dotazy týkající se sítě v Azure Functions. Komplexnější přehled najdete v tématu [funkce síťových možností](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Návody nastavit statickou IP adresu ve funkcích Functions?

Nasazení funkce v App Service Environment je v současné době jediným způsobem, jak pro vaši funkci vytvořit statickou příchozí a odchozí IP adresu. Podrobné informace o použití App Service Environment začněte s článkem [Vytvoření a použití interního nástroje pro vyrovnávání zatížení s App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Návody omezit přístup k Internetu na moji funkci?

Přístup k Internetu můžete omezit několika způsoby:

* [Omezení IP adresy](../app-service/app-service-ip-restrictions.md): Omezte příchozí provoz do aplikace Function za rozsah IP adres.
    * V části omezení protokolu IP můžete také nakonfigurovat [koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md), které omezují, aby funkce přijímala příchozí provoz jenom z konkrétní virtuální sítě.
* Odebrání všech triggerů HTTP. U některých aplikací je stačí jednoduše zabránit aktivačním událostem HTTP a použít k aktivaci funkce jiný zdroj událostí.

Mějte na paměti, že editor Azure Portal vyžaduje přímý přístup ke spuštěným funkcím. Jakékoli změny kódu prostřednictvím Azure Portal budou vyžadovat, aby zařízení, které používáte k procházení portálu, mělo jeho IP adresu povolenou. Můžete ale pořád použít cokoli na kartě funkce platformy se zavedenými omezeními sítě.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Návody omezit aplikaci Function App na virtuální síť?

**Příchozí** provoz pro aplikaci Function App můžete omezit na virtuální síť pomocí [koncových bodů služby](./functions-networking-options.md#private-site-access). Tato konfigurace pořád umožňuje, aby aplikace Functions provedla odchozí volání na Internet.

Jediným způsobem, jak úplně omezit funkci tak, aby všechny přenosy přes virtuální síť používaly interní App Service Environment s vyrovnáváním zatížení. Tato možnost nasadí váš web na vyhrazené infrastruktuře uvnitř virtuální sítě a pošle všechny aktivační události a přenosy přes virtuální síť. 

Podrobné informace o použití App Service Environment začněte s článkem [Vytvoření a použití interního nástroje pro vyrovnávání zatížení s App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Jak můžu získat přístup k prostředkům ve virtuální síti z aplikace Function App?

K prostředkům ve virtuální síti můžete přistupovat ze spuštěné funkce pomocí integrace virtuální sítě. Další informace najdete v tématu [Integrace virtuální sítě](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Návody přístup k prostředkům chráněným koncovými body služby?

Pomocí integrace virtuální sítě můžete získat přístup k prostředkům zabezpečeným koncovým bodem z běžící funkce. Další informace najdete v tématu [Integrace virtuální sítě](functions-networking-options.md#virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Jak můžu aktivovat funkci z prostředku ve virtuální síti?

Můžete umožnit volání aktivačních událostí HTTP z virtuální sítě pomocí [koncových bodů služby](./functions-networking-options.md#private-site-access). 

Můžete také aktivovat funkci z prostředku ve virtuální síti nasazením aplikace Function App do App Service Environment. Podrobnosti o používání App Service Environment najdete v tématu [Vytvoření a použití interního nástroje pro vyrovnávání zatížení s App Service Environment](../app-service/environment/create-ilb-ase.md).

Plán Premium a App Service podporuje triggery HTTP z virtuální sítě, ale jenom App Service prostředí podporuje všechny ostatní typy triggerů funkcí prostřednictvím virtuální sítě.

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Jak můžu nasadit aplikaci Function App ve virtuální síti?

Nasazení do App Service Environment je jediným způsobem, jak vytvořit aplikaci Function App, která je zcela uvnitř virtuální sítě. Podrobnosti o používání interního nástroje pro vyrovnávání zatížení s App Service Environment můžete začít s článkem [Vytvoření a použití interního nástroje pro vyrovnávání zatížení s App Service Environment](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

V případě scénářů, kdy potřebujete pouze jednosměrný přístup k prostředkům virtuální sítě nebo méně komplexní izolaci sítě, přečtěte si téma [Přehled funkcí sítě](functions-networking-options.md).

## <a name="next-steps"></a>Další postup

Další informace o sítích a funkcích: 

* [Postup Začínáme s integrací virtuální sítě najdete v kurzu.](./functions-create-vnet.md)
* [Přečtěte si další informace o možnostech sítě v Azure Functions](./functions-networking-options.md)
* [Další informace o integraci virtuální sítě s App Service a funkcemi](../app-service/web-sites-integrate-with-vnet.md)
* [Další informace o virtuálních sítích v Azure](../virtual-network/virtual-networks-overview.md)
* [Povolení více síťových funkcí a řízení pomocí App Servicech prostředí](../app-service/environment/intro.md)
