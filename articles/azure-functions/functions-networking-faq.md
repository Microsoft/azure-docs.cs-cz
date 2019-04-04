---
title: Nejčastější dotazy o možnostech sítě v Azure Functions
description: Odpovědi na některé nejčastější dotazy a scénáře pro sítě s využitím Azure Functions.
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 2/26/2019
ms.author: alkarche
ms.openlocfilehash: 7946b7f45ff3df9225a27b70ccfbdb895bfd03c4
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896288"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Nejčastější dotazy o možnostech sítě v Azure Functions

Níže je seznam častých otázek sítě. Komplexnější přehled najdete v článku [funkce síťové možnosti dokumentu](functions-networking-options.md)

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Nastavení statické IP adresy ve funkcích

Nasazení funkce v App Service Environment (ASE) je aktuálně jediný způsob, jak mít statické příchozí a odchozí IP adresy pro vaši funkci. Podrobnosti o použití prostředí ASE začněte článku: [Vytváření a používání prostředí ILB ASE](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Jak omezit přístup k Internetu pro moje funkce?

Můžete omezit přístup k Internetu v celou řadou způsobů uvedených níže.

* [Omezení IP adres](../app-service/app-service-ip-restrictions.md): omezit příchozí provoz do aplikace funkce tak, že rozsah IP adres.
* Odeberte všechny aktivační události HTTP. U některých aplikací stačí jednoduše vyhnout triggerů HTTP a použít jiný zdroj událostí k aktivaci funkce.

Nejdůležitější zvážit, pokud je potřeba mít na paměti, vyžadující přímý přístup ke spuštěné funkce pomocí editoru webu Azure portal. Změny kódu na webu Azure portal bude vyžadovat, aby zařízení, které používáte, přejděte na portál a mají svůj seznam povolených IP adres. Stále však můžete cokoli na kartě funkce platformy s síťových omezení na místě.

## <a name="how-do-i-restrict-my-function-app-to-a-vnet"></a>Jak omezit své aplikaci function app k virtuální síti?

Jediný způsob, jak úplně omezit funkce tak, aby veškerý provoz prochází přes virtuální síť se má používat interně s vyrovnáváním zatížení (ILB) App Service Environment (ASE). Tato možnost nasadí svůj web v vyhrazená infrastruktura uvnitř virtuální sítě a odešle všechny aktivační události a provozu přes virtuální síť. Podrobnosti o použití prostředí ASE začněte článku: [Vytváření a používání prostředí ILB ASE](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-vnet-from-a-function-app"></a>Jak z aplikace function app dostanete prostředky ve virtuální síti?

Můžete přístup k prostředkům ve virtuální síti ze spuštěné funkce pomocí integrace virtuální sítě. Další informace najdete v tématu [integrace virtuální sítě](functions-networking-options.md#vnet-integration)

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Jak můžu přístup k prostředkům chráněným koncové body služby?

Pomocí nové integrace virtuální sítě (aktuálně ve verzi preview), můžete přístup k prostředkům ze spuštěné funkce zabezpečeného koncového bodu služby. Další informace najdete v tématu [integrace virtuální sítě ve verzi preview](functions-networking-options.md#preview-vnet-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-vnet"></a>Jak můžete aktivovat funkci z prostředků ve virtuální síti?

Funkce z prostředků ve virtuální síti pouze spustíte nasazení vaší aplikace function app do služby App Service Environment. Podrobnosti o použití prostředí ASE najdete v tématu [vytváření a používání prostředí ILB ASE](../app-service/environment/create-ilb-ase.md).


## <a name="how-can-i-deploy-my-function-app-in-a-vnet"></a>Jak můžete nasadit aplikaci funkce ve virtuální síti?

Nasazení do služby App Service Environment je jediný způsob, jak vytvořit aplikaci function app, který je zcela uvnitř podrobnosti virtuální sítě pro použití prostředí ILB ASE, začněte článkem tady: [Vytváření a používání prostředí ILB ASE](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Pro scénáře, kde potřebujete pouze jednosměrné přístup na prostředky virtuální sítě nebo méně komplexní síťovou izolaci, najdete v článku [funkce Přehled sítí](functions-networking-options.md).
