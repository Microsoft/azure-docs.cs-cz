---
title: Nejčastější dotazy o možnostech sítě v Azure Functions
description: Odpovědi na některé nejčastější dotazy a scénáře pro sítě s využitím Azure Functions.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0d352d61a971e289a6286c2d948c3f0869ddd8d2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706390"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Nejčastější dotazy o možnostech sítě v Azure Functions

Tento článek obsahuje nejčastější dotazy o možnostech sítě v Azure Functions. Komplexnější přehled najdete v tématu [funkce možností sítě](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Nastavení statické IP adresy ve funkcích

Nasazení funkce ve službě App Service Environment je aktuálně jediný způsob, jak mít statické příchozí a odchozí IP adresy pro vaši funkci. Podrobnosti o používání služby App Service Environment, začněte s článkem [vytvoření a použití interního nástroje load balancer pomocí služby App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Jak omezit přístup k Internetu pro moje funkce?

Můžete omezit přístup k Internetu v několika způsoby:

* [Omezení IP adres](../app-service/app-service-ip-restrictions.md): Omezte příchozí provoz do aplikace funkce tak, že rozsah IP adres.
    * V části omezení IP adres, je také možné nakonfigurovat [koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md), která omezují vaši funkci tak, aby přijímal pouze příchozí provoz z konkrétní virtuální sítě.
* Odebrání všechny aktivační události HTTP. U některých aplikací stačí jednoduše vyhnout triggerů HTTP a použít jiný zdroj událostí k aktivaci funkce.

Uvědomte si, že editoru webu Azure portal vyžaduje přímý přístup ke spuštěné funkce. Změny kódu na webu Azure portal bude vyžadovat, aby zařízení, které používáte, přejděte na portál a mají svůj seznam povolených IP adres. Ale můžete pořád používat nic na kartě funkce platformy síťových omezení na místě.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Jak omezit své aplikaci function app k virtuální síti?

Budete moct omezit **příchozí** provozu pro aplikace function app k virtuální síti pomocí [koncové body služby](./functions-networking-options.md#private-site-access). Tato konfigurace umožňuje stále aplikace function app pro odchozí volání k Internetu.

Jediný způsob, jak úplně omezit funkce tak, aby veškerý provoz prochází přes virtuální síť je použití interním vyrovnáváním zatížení služby App Service Environment. Tato možnost nasadí svůj web v vyhrazená infrastruktura ve virtuální síti a odešle všechny aktivační události a provozu přes virtuální síť. 

Podrobnosti o používání služby App Service Environment, začněte s článkem [vytvoření a použití interního nástroje load balancer pomocí služby App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Jak z aplikace function app dostanete prostředky ve virtuální síti?

Prostředky ve virtuální síti ze spuštěné funkce můžete přistupovat pomocí integrace služby virtual network. Další informace najdete v tématu [integrace virtuální sítě](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Jak můžu přístup k prostředkům chráněným koncové body služby?

Pomocí integrace služby virtual network (aktuálně ve verzi preview) můžete přístup k prostředkům zabezpečeným koncový bod služby ze spuštěné funkce. Další informace najdete v tématu [ve verzi Preview integrace služby virtual network](functions-networking-options.md#preview-version-of-virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Jak můžete aktivovat funkci z prostředků ve virtuální síti?

Budete moct povolit triggerů HTTP, která se má volat z virtuální sítě pomocí [koncové body služby](./functions-networking-options.md#private-site-access). 

Můžete také aktivovat funkce z prostředků ve virtuální síti a nasadit vaši aplikaci function app do služby App Service Environment. Podrobnosti o používání služby App Service Environment najdete v tématu [vytvoření a použití interního nástroje load balancer pomocí služby App Service Environment](../app-service/environment/create-ilb-ase.md).

Premium a App Service plán podpory protokolu HTTP aktivačních událostí z virtuální sítě, ale pouze služby App Service environment podporují všechny typy aktivační událost další funkce prostřednictvím virtuální sítě.

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Jak můžete nasadit aplikaci funkcí ve službě virtual network?

Nasazení do služby App Service Environment je jediný způsob, jak vytvořit aplikaci function app, který je zcela ve virtuální síti. Podrobnosti o službě interní služby load balancer pomocí služby App Service Environment, začněte s článkem [vytvoření a použití interního nástroje load balancer pomocí služby App Service Environment](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Pro scénáře, kdy potřebujete pouze jednosměrné přístup na prostředky virtuální sítě nebo méně komplexní síťovou izolaci, najdete v článku [funkce Přehled sítí](functions-networking-options.md).

## <a name="next-steps"></a>Další postup

Další informace o sítě a funkce: 

* [Postupujte podle kurzu o zahájení práce s integrace služby virtual network](./functions-create-vnet.md)
* [Další informace o možnosti sítě v Azure Functions](./functions-networking-options.md)
* [Další informace o integraci virtuální sítě s App Service a Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Další informace o virtuálních sítí v Azure](../virtual-network/virtual-networks-overview.md)
* [Povolit další síťové funkce a ovládací prvek s App Service Environment](../app-service/environment/intro.md)
