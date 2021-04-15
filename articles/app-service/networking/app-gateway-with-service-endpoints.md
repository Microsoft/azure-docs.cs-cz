---
title: Application Gateway integrace s koncovými body služby – Azure App Service | Microsoft Docs
description: Popisuje, jak Application Gateway integrována s Azure App Service zabezpečenými koncovými body služby.
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
ms.openlocfilehash: f1d517ba37bbef95d1863485c8c3b6313f196c11
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374909"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Integrace Application Gateway s koncovými body služby
Existují tři variace App Service, které vyžadují mírně odlišnou konfiguraci integrace s Azure Application Gateway. Mezi varianty patří běžné App Service – také označované jako víceklientské, interní Load Balancer (interního nástroje) App Service Environment (pomocného mechanismu) a externí pomocný modul pro čtení. Tento článek vás seznámí s postupem, jak ho nakonfigurovat App Service (multi-tenant) a diskuze o požadavcích na interního nástroje a externím pomocném mechanismem.

## <a name="integration-with-app-service-multi-tenant"></a>Integrace s App Service (více tenantů)
App Service (víceklientské tenant) má veřejný internetový koncový bod. Pomocí [koncových bodů služby](../../virtual-network/virtual-network-service-endpoints-overview.md) můžete povolený provoz jenom z konkrétní podsítě v rámci Azure Virtual Network a zablokovat všechno ostatní. V následujícím scénáři použijeme tuto funkci k zajištění toho, aby instance App Service mohla přijímat jenom přenosy z konkrétní instance Application Gateway.

:::image type="content" source="./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png" alt-text="Diagram zobrazuje Internet, který se bude nacházet Application Gateway v Azure Virtual Network a prostřednictvím ikony brány firewall na instancích aplikací v App Service.":::

Existují dvě části této konfigurace, kromě vytvoření App Service a Application Gateway. První část povoluje koncové body služby v podsíti Virtual Network, kde je Application Gateway nasazený. Koncové body služby zajistí, že veškerý síťový provoz, který opouští podsíť, do App Service, bude označený IDENTIFIKÁTORem konkrétní podsítě. Druhá část je nastavit omezení přístupu konkrétní webové aplikace, aby se zajistilo, že bude povolený jenom provoz označený pomocí tohoto konkrétního ID podsítě. Můžete ji nakonfigurovat pomocí různých nástrojů v závislosti na preferencích.

## <a name="using-azure-portal"></a>Pomocí webu Azure Portal
V Azure Portal budete postupovat podle čtyř kroků, kterými instalaci zřídíte a nakonfigurujete. Pokud máte existující prostředky, můžete vynechat první kroky.
1. Vytvořte App Service pomocí některého z rychlých startů v dokumentaci k App Service, například [rychlý Start .NET Core](../quickstart-dotnetcore.md) .
2. Pomocí rychlého startu na [portálu](../../application-gateway/quick-create-portal.md)vytvořte Application Gateway, ale přeskočte část přidat cíle back-endu.
3. Nakonfigurujte [App Service jako back-end v Application Gateway](../../application-gateway/configure-web-app-portal.md), ale přeskočte část omezení přístupu.
4. Nakonec vytvořte [omezení přístupu pomocí koncových bodů služby](../../app-service/app-service-ip-restrictions.md#set-a-service-endpoint-based-rule).

Nyní můžete k App Service přistupovat prostřednictvím Application Gateway, ale pokud se pokusíte získat přístup k App Service přímo, měli byste obdržet chybu 403 HTTP s oznámením, že web je zastavený.

![Snímek obrazovky znázorňující text chyby 403 – zakázáno.](./media/app-gateway-with-service-endpoints/website-403-forbidden.png)

## <a name="using-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru
[Šablona nasazení Správce prostředků][template-app-gateway-app-service-complete] zřídí kompletní scénář. Scénář se skládá z instance App Service uzamčená s koncovými body služby a omezením přístupu pouze pro příjem provozu z Application Gateway. Šablona obsahuje mnoho inteligentních výchozích hodnot a jedinečné přípony, které jsou přidány do názvů prostředků, aby byly jednoduché. Pokud je chcete přepsat, budete muset klonovat úložiště nebo stáhnout šablonu a upravit ji. 

Pokud chcete šablonu použít, můžete použít tlačítko nasadit do Azure, které najdete v popisu šablony, nebo můžete použít odpovídající PowerShell/CLI.

## <a name="using-azure-command-line-interface"></a>Použití rozhraní příkazového řádku Azure
[Ukázka Azure CLI](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) zřídí App Service uzamčený s koncovými body služby a omezením přístupu jenom pro příjem provozu z Application Gateway. Pokud potřebujete jenom izolovat provoz do existující App Service z existující Application Gateway, je to pro tento příkaz dostačující.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

Ve výchozí konfiguraci příkaz zajistí obě nastavení konfigurace koncového bodu služby v podsíti a omezení přístupu v App Service.

## <a name="considerations-for-ilb-ase"></a>Předpoklady pro interního nástroje pomocného mechanismu
Služba interního nástroje pomocného mechanismu není dostupná pro Internet a provoz mezi instancí a Application Gateway je už v Virtual Network izolovaný. Následující [Příručka Průvodce](../environment/integrate-with-application-gateway.md) konfiguruje interního nástroje a integruje ji s Application Gateway pomocí Azure Portal. 

Pokud chcete zajistit, aby se pomocného mechanismu řízení přiNSG jenom provoz z podsítě Application Gateway, můžete nakonfigurovat skupinu zabezpečení sítě (), která bude mít vliv na všechny webové aplikace v pomocném panelu. Pro NSG je možné zadat rozsah IP adres podsítě a volitelně také porty (80/443). Ujistěte se, že nepřepisujete [požadovaná pravidla NSG](../environment/network-info.md#network-security-groups) pro funkci pomocného mechanismu pro správné fungování.

K izolaci provozu do jednotlivé webové aplikace budete muset použít omezení přístupu na základě IP adresy, protože koncové body služby nebudou fungovat pro pomocného uživatele. IP adresa by měla být privátní IP instance Application Gateway.

## <a name="considerations-for-external-ase"></a>Doporučení pro externí pomocného mechanismu
Externí pomocný přístup k nástroji pro vyrovnávání zatížení, jako je App Service více tenantů, má veřejný. Koncové body služby nefungují pro pomocná pomoc a proto budete muset použít omezení přístupu na základě protokolu IP pomocí veřejné IP adresy instance Application Gateway. Pokud chcete vytvořit externí pomocného průvodce pomocí Azure Portal, můžete postupovat podle tohoto [rychlého](../environment/create-external-ase.md) startu.

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Šablona Azure Resource Manager pro kompletní scénář"

## <a name="considerations-for-kuduscm-site"></a>Doporučení pro Kudu/web SCM
Web SCM, označovaný také jako Kudu, je lokalita pro správu, která existuje pro každou webovou aplikaci. Není možné se vrátit k proxy serveru SCM a pravděpodobně ho budete pravděpodobně chtít uzamknout na jednotlivé IP adresy nebo konkrétní podsíť.

Pokud chcete použít stejná omezení přístupu jako u hlavní lokality, můžete nastavení zdědit pomocí následujícího příkazu.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Pokud chcete nastavit jednotlivá omezení přístupu pro web SCM, můžete přidat omezení přístupu pomocí příznaku--SCM-site, jak je znázorněno níže.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Další kroky
Další informace o App Service Environment najdete v [dokumentaci k App Service Environment](/azure/app-service/environment).

Pro lepší zabezpečení webové aplikace najdete informace o firewallu webových aplikací v Application Gateway najdete v [dokumentaci k bráně firewall webových aplikací Azure](../../web-application-firewall/ag/ag-overview.md).
