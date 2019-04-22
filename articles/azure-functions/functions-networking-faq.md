---
title: Nejčastější dotazy o možnostech sítě v Azure Functions
description: Odpovědi na některé nejčastější dotazy a scénáře pro sítě s využitím Azure Functions.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 3cf6a0d080e2d8cafcab8e69a614b59a470c7aba
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682198"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Nejčastější dotazy o možnostech sítě v Azure Functions

Tento článek obsahuje nejčastější dotazy o možnostech sítě v Azure Functions. Komplexnější přehled najdete v tématu [funkce možností sítě](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Nastavení statické IP adresy ve funkcích

Nasazení funkce ve službě App Service Environment je aktuálně jediný způsob, jak mít statické příchozí a odchozí IP adresy pro vaši funkci. Podrobnosti o používání služby App Service Environment, začněte s článkem [vytvoření a použití interního nástroje load balancer pomocí služby App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Jak omezit přístup k Internetu pro moje funkce?

Můžete omezit přístup k Internetu v několika způsoby:

* [Omezení IP adres](../app-service/app-service-ip-restrictions.md): Omezte příchozí provoz do aplikace funkce tak, že rozsah IP adres.
* Odebrání všechny aktivační události HTTP. U některých aplikací stačí jednoduše vyhnout triggerů HTTP a použít jiný zdroj událostí k aktivaci funkce.

Uvědomte si, že editoru webu Azure portal vyžaduje přímý přístup ke spuštěné funkce. Změny kódu na webu Azure portal bude vyžadovat, aby zařízení, které používáte, přejděte na portál a mají svůj seznam povolených IP adres. Ale můžete pořád používat nic na kartě funkce platformy síťových omezení na místě.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Jak omezit své aplikaci function app k virtuální síti?

Jediný způsob, jak úplně omezit funkce tak, aby veškerý provoz prochází přes virtuální síť je použití interním vyrovnáváním zatížení služby App Service Environment. Tato možnost nasadí svůj web v vyhrazená infrastruktura ve virtuální síti a odešle všechny aktivační události a provozu přes virtuální síť. 

Podrobnosti o používání služby App Service Environment, začněte s článkem [vytvoření a použití interního nástroje load balancer pomocí služby App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Jak z aplikace function app dostanete prostředky ve virtuální síti?

Prostředky ve virtuální síti ze spuštěné funkce můžete přistupovat pomocí integrace služby virtual network. Další informace najdete v tématu [integrace virtuální sítě](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Jak můžu přístup k prostředkům chráněným koncové body služby?

Pomocí integrace služby virtual network (aktuálně ve verzi preview) můžete přístup k prostředkům zabezpečeným koncový bod služby ze spuštěné funkce. Další informace najdete v tématu [ve verzi Preview integrace služby virtual network](functions-networking-options.md#preview-version-of-virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Jak můžete aktivovat funkci z prostředků ve virtuální síti?

Funkce z prostředků ve virtuální síti můžete aktivovat pouze nasazením aplikace function app do služby App Service Environment. Podrobnosti o používání služby App Service Environment najdete v tématu [vytvoření a použití interního nástroje load balancer pomocí služby App Service Environment](../app-service/environment/create-ilb-ase.md).


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
