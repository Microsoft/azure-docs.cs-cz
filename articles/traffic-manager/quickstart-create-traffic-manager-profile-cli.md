---
title: 'Rychlý Start: vytvoření profilu pro HA aplikací – Azure CLI – Azure Traffic Manager'
description: Tento článek rychlý Start popisuje, jak vytvořit profil Traffic Manager pro sestavení webové aplikace s vysokou dostupností pomocí Azure CLI.
services: traffic-manager
author: duongau
mnager: kumud
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7dabf94c711972f9fe543edac0d7b95469fc2d35
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94661099"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Rychlý Start: vytvoření profilu Traffic Manager pro webovou aplikaci s vysokou dostupností pomocí Azure CLI

V tomto rychlém startu se dozvíte, jak vytvořit profil Traffic Manager, který poskytuje vysokou dostupnost vaší webové aplikace.

V tomto rychlém startu vytvoříte dvě instance webové aplikace. Každý z nich je spuštěný v jiné oblasti Azure. Vytvoříte profil Traffic Manager na základě [priority koncového bodu](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profil směruje uživatelský provoz do primární lokality, na které běží webová aplikace. Traffic Manager nepřetržitě monitoruje webovou aplikaci. Pokud primární lokalita není k dispozici, poskytuje automatické převzetí služeb při selhání pro záložní lokalitu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Vytvořte profil Traffic Manager pomocí [AZ Network Traffic-Manager Profile Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) , který přesměruje přenosy uživatelů na základě priority koncových bodů.

V následujícím příkladu nahraďte **<profile_name>** jedinečným názvem Traffic Manager profilu.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Vytváření webových aplikací

Pro účely tohoto rychlého startu budete potřebovat dvě instance webové aplikace nasazené ve dvou různých oblastech Azure (*východní USA* a *západní Evropa*). Každý bude sloužit jako primární koncová body a koncové body převzetí služeb při selhání pro Traffic Manager

### <a name="create-web-app-service-plans"></a>Vytvoření plánů služby Web App Service
Vytvořte plány služby Web App Service pomocí [AZ AppService Plan Create](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) pro dvě instance webové aplikace, kterou nasadíte ve dvou různých oblastech Azure.

V následujícím příkladu nahraďte **<appspname_eastus>** a **<appspname_westeurope>** s jedinečným názvem plánu App Service

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```

### <a name="create-a-web-app-in-the-app-service-plan"></a>Vytvoření webové aplikace v plánu služby App Service
Vytvořte dvě instance webové aplikace pomocí [AZ WebApp Create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) ve App Servicech plánech *východní USA* a *západní Evropa* oblastech Azure.

V následujícím příkladu nahraďte **<app1name_eastus>** a **<app2name_westeurope>** jedinečným názvem aplikace, a nahradíte **<** appspname_eastus>a **<** appspname_westeurope>s názvem použitým k vytvoření plánů App Service v předchozí části.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager
Přidejte dva Web Apps jako koncové body Traffic Manager pomocí příkazu [AZ Network Traffic-Manager Endpoint Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) do profilu Traffic Manager následujícím způsobem:

- Určete ID webové aplikace a přidejte webovou aplikaci umístěnou v *východní USA* oblasti Azure jako primární koncový bod pro směrování všech uživatelských přenosů. 
- Určete ID webové aplikace a přidejte webovou aplikaci, která se nachází v *západní Evropa* oblasti Azure jako koncový bod převzetí služeb při selhání. 

Když je primární koncový bod nedostupný, provoz se automaticky směruje na koncový bod převzetí služeb při selhání.

V následujícím příkladu nahraďte **<app1name_eastus>** a **<App2name_westeurope>** s názvy aplikací vytvořenými pro jednotlivé oblasti v předchozí části. Potom nahraďte **<profile_name>** názvem profilu použitým v předchozí části. 

**Východní USA koncový bod**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Poznamenejte si ID zobrazené ve výstupu a použijte následující příkaz pro přidání koncového bodu:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**Západní Evropa koncový bod**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

Poznamenejte si ID zobrazené ve výstupu a použijte následující příkaz pro přidání koncového bodu:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>Otestování profilu Traffic Manager

V této části zkontrolujete název domény vašeho profilu Traffic Manager. Také nakonfigurujete primární koncový bod jako nedostupný. Nakonec se zobrazí, že je webová aplikace stále k dispozici. Je to proto, že Traffic Manager odesílá provoz do koncového bodu převzetí služeb při selhání.

V následujícím příkladu nahraďte **<app1name_eastus>** a **<App2name_westeurope>** s názvy aplikací vytvořenými pro jednotlivé oblasti v předchozí části. Potom nahraďte **<profile_name>** názvem profilu použitým v předchozí části.

### <a name="determine-the-dns-name"></a>Určení názvu DNS

Pomocí [AZ Network Traffic-Manager Profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show)určete název DNS profilu Traffic Manager.

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Zkopírujte hodnotu **RelativeDnsName** . Název DNS vašeho profilu Traffic Manager je *http://<* relativednsname *>. trafficmanager.NET*. 

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci
1. Ve webovém prohlížeči zadejte název DNS profilu Traffic Manager (*http://<* relativednsname *>. trafficmanager.NET*), abyste si mohli zobrazit výchozí web vaší webové aplikace.

    > [!NOTE]
    > V tomto scénáři rychlého startu všechny požadavky směrují do primárního koncového bodu. Je nastavená na **priority 1**.
2. Pokud chcete zobrazit Traffic Manager převzetí služeb při selhání v akci, zakažte primární lokalitu pomocí [AZ Network Traffic-Manager Endpoint Update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Zkopírujte název DNS profilu Traffic Manager (*http://<* relativednsname *>. trafficmanager.NET*), abyste si web mohli zobrazit v nové relaci webového prohlížeče.
4. Ověřte, zda je webová aplikace stále k dispozici.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi, odstraňte skupiny prostředků, webové aplikace a všechny související prostředky pomocí [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili profil Traffic Manager, který poskytuje vysokou dostupnost vaší webové aplikace. Pokud se chcete dozvědět víc o provozu směrování, přejděte k Traffic Manager kurzům.

> [!div class="nextstepaction"]
> [Kurzy pro službu Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
