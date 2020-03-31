---
title: Integrace aplikační brány s koncovými body služby – Služba Azure App Service | Dokumenty společnosti Microsoft
description: Popisuje, jak se aplikační brána integruje se službou Azure App Service zabezpečenou koncovými body služby.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 5e32baa10e98f0f57a861f8cebfb7506ad615631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980059"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Integrace aplikační brány s koncovými body služby
Existují tři varianty služby App Service, které vyžadují mírně odlišnou konfiguraci integrace s Azure Application Gateway. Varianty zahrnují pravidelné služby app service - označované také jako multi-tenant, Internal Load Balancer (ILB) App Service Environment (ASE) a externí služby ASE. Tento článek bude procházet, jak ji nakonfigurovat pomocí služby App Service (více klientů) a diskutovat o aspektech ilb a externí služby ASE.

## <a name="integration-with-app-service-multi-tenant"></a>Integrace se službou App Service (více klientů)
Služba App Service (multi-tenant) má veřejný koncový bod směřující k internetu. Pomocí [koncových bodů služby](../../virtual-network/virtual-network-service-endpoints-overview.md) můžete povolit provoz pouze z určité podsítě v rámci virtuální sítě Azure a blokovat vše ostatní. V následujícím scénáři použijeme tuto funkci k zajištění, že instance služby App Service může přijímat pouze provoz z konkrétní instance aplikační brány.

![Integrace aplikační brány se službou App Service](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

Kromě vytvoření služby App Service a aplikační brány existují dvě části této konfigurace. První část umožňuje koncové body služby v podsíti virtuální sítě, kde je nasazena aplikační brána. Koncové body služby zajistí, že veškerý síťový provoz opouštějící podsíť směrem ke službě App Service bude označen konkrétním ID podsítě. Druhá část je nastavit omezení přístupu konkrétní webové aplikace, aby bylo zajištěno, že je povolen pouze provoz označený tímto konkrétním ID podsítě. Můžete jej nakonfigurovat pomocí různých nástrojů v závislosti na předvolbách.

## <a name="using-azure-portal"></a>Pomocí webu Azure Portal
S portálem Azure postupujte podle čtyř kroků pro zřizování a konfiguraci nastavení. Pokud máte existující prostředky, můžete přeskočit první kroky.
1. Vytvoření služby App Service pomocí jednoho z rychlých startů v dokumentaci ke službě App Service, například [.Net Core Quickstart](../../app-service/app-service-web-get-started-dotnet.md)
2. Vytvořte aplikační bránu pomocí [portálu Quickstart](../../application-gateway/quick-create-portal.md), ale přeskočte část Přidat back-endové cíle.
3. Nakonfigurujte [službu App Service jako back-end v application gateway](../../application-gateway/configure-web-app-portal.md), ale přeskočte část Omezit přístup.
4. Nakonec vytvořte [omezení přístupu pomocí koncových bodů služby](../../app-service/app-service-ip-restrictions.md#service-endpoints).

Nyní můžete přistupovat ke službě App Service prostřednictvím aplikační brány, ale pokud se pokusíte o přístup ke službě Aplikace přímo, měli byste obdržet chybu 403 HTTP označující, že je web zastaven.

![Integrace aplikační brány se službou App Service](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru
[Šablona nasazení Správce prostředků][template-app-gateway-app-service-complete] zřídí úplný scénář. Scénář se skládá z instance služby App Service uzamčens koncovými body služby a omezení přístupu pouze přijímat provoz z aplikační brány. Šablona obsahuje mnoho inteligentních výchozích hodnot a jedinečných příponach přidaných k názvům prostředků, aby byla jednoduchá. Chcete-li je přepsat, budete muset naklonovat repo nebo stáhnout šablonu a upravit ji. 

Chcete-li použít šablonu, můžete použít tlačítko Nasadit do Azure, které se nachází v popisu šablony, nebo můžete použít příslušné PowerShell/CLI.

## <a name="using-azure-command-line-interface"></a>Použití rozhraní příkazového řádku Azure
[Ukázka Azure CLI](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) zřídí službu App Service uzamčenou koncovými body služby a omezením přístupu pouze pro příjem provozu z aplikační brány. Pokud potřebujete pouze izolovat provoz na existující službu aplikace z existující aplikační brány, následující příkaz je dostačující.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

Ve výchozí konfiguraci příkaz zajistí nastavení konfigurace koncového bodu služby v podsíti i omezení přístupu ve službě App Service.

## <a name="considerations-for-ilb-ase"></a>Důležité informace pro službu ILB ASE
Služba ASE ILB není vystavena internetu a provoz mezi instancí a aplikační bránou je tedy již izolován do virtuální sítě. Následující [návod](../environment/integrate-with-application-gateway.md) konfiguruje službu ASE ILB a integruje ji s aplikační bránou pomocí portálu Azure Portal. 

Pokud chcete zajistit, aby se do služby ASE dostal pouze provoz z podsítě Aplikační brána, můžete nakonfigurovat skupinu zabezpečení sítě (NSG), která ovlivní všechny webové aplikace v systému ASE. Pro skupinu sítě nsg můžete zadat rozsah IP podsítě a volitelně porty (80/443). Ujistěte se, že nepřepíšete [požadovaná pravidla nsg](../environment/network-info.md#network-security-groups) pro ase správně fungovat.

Chcete-li izolovat provoz na jednotlivé webové aplikace, budete muset použít omezení přístupu založené na IP jako koncové body služby nebude fungovat pro službu ASE. IP adresa by měla být privátní IP adresa instance Aplikační brána.

## <a name="considerations-for-external-ase"></a>Důležité informace pro externí ase
Externí služba ASE má veřejný čelí vyrovnávání zatížení, jako je víceklientské app service. Koncové body služby nefungují pro službu ASE, a proto budete muset použít omezení přístupu na základě IP adresy pomocí veřejné IP adresy instance Application Gateway. Chcete-li vytvořit externí službu ASE pomocí portálu Azure, můžete postupovat podle tohoto [úvodního](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Šablona Azure Resource Manager pro kompletní scénář"

## <a name="considerations-for-kuduscm-site"></a>Důležité informace pro kudu / scm stránky
SCM stránky, také známý jako kudu, je admin stránky, které existuje pro každou webovou aplikaci. Není možné obrátit proxy scm stránky a vy s největší pravděpodobností také chcete zamknout na jednotlivé IP adresy nebo konkrétní podsíť.

Pokud chcete použít stejná omezení přístupu jako hlavní web, můžete zdědit nastavení pomocí následujícího příkazu.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Pokud chcete nastavit individuální omezení přístupu pro web scm, můžete přidat omezení přístupu pomocí příznaku --scm-site, jak je znázorněno níže.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Další kroky
Další informace o prostředí služby App Service najdete v [dokumentaci k prostředí služby App Service](https://docs.microsoft.com/azure/app-service/environment).

Informace o bráně firewall webových aplikací v application gateway naleznete v [dokumentaci k bráně firewall webových aplikací Azure.](../../web-application-firewall/ag/ag-overview.md)