---
title: Úvodní příručka:Vytvoření profilu pro informace o projektu HA aplikací – Azure CLI – Azure Traffic Manager
description: Tento článek rychlého startu popisuje, jak vytvořit profil Traffic Manager vytvořit vysoce dostupné webové aplikace.
services: traffic-manager
author: rohinkoul
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: rohink
ms.openlocfilehash: e19850243498fc24c9a726f4603590df15f3a046
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79531511"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Úvodní příručka: Vytvoření profilu Traffic Manageru pro vysoce dostupnou webovou aplikaci pomocí rozhraní příkazového příkazu Azure

Tento rychlý start popisuje, jak vytvořit profil Traffic Manager, který poskytuje vysokou dostupnost pro webovou aplikaci.

V tomto rychlém startu vytvoříte dvě instance webové aplikace. Každý z nich běží v jiné oblasti Azure. Vytvoříte profil Traffic Manageru na základě [priority koncového bodu](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profil směruje uživatelský provoz na primární web se spuštěnou webovou aplikací. Traffic Manager průběžně monitoruje webovou aplikaci. Pokud primární lokalita není k dispozici, poskytuje automatické převzetí služeb při selhání pro záložní lokalitu.

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Vytvořte profil Traffic Manager pomocí [az sítě traffic-manager vytvořit,](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) který řídí provoz uživatelů na základě priority koncového bodu.

V následujícím příkladu nahraďte **<profile_name profile_name profile_name>profile_name** jedinečným názvem profilu traffic manageru.

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

Pro tento rychlý start budete potřebovat dvě instance webové aplikace nasazené ve dvou různých oblastech Azure *(Východní USA* a *Západní Evropa).* Každý bude sloužit jako primární a převzetí služeb při selhání koncové body pro Traffic Manager.

### <a name="create-web-app-service-plans"></a>Vytvoření plánů služby webové aplikace
Vytvořte plány služby webové aplikace pomocí [az appservice plán vytvořit](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) pro dvě instance webové aplikace, které budete nasadit ve dvou různých oblastech Azure.

V následujícím příkladu nahraďte **<appspname_eastus>** a<appspname_westeurope **>** jedinečným názvem plánu služby App Service.

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
Vytvořte dvě instance, které webová aplikace vytvoří pomocí [az webapp, které vytvářejí](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) v plánech služby App Service v oblastech *Azure ve východní* USA a západní *Evropě.*

V následujícím příkladu nahraďte **<app1name_eastus>** a<app2name_westeurope **>** jedinečným názvem aplikace a nahraďte<**appspname_eastus>** a<**appspname_westeurope>** názvem použitým k vytvoření plánů služby App Service v předchozí části.

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
Přidejte dvě webové aplikace jako koncové body Traffic Manageru pomocí [koncového bodu správce síťových přenosů az vytvořit](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) do profilu Traffic Manageru takto:

- Určete ID webové aplikace a přidejte webovou aplikaci umístěnou v oblasti *East US* Azure jako primární koncový bod pro směrování veškerého provozu uživatelů. 
- Určete ID webové aplikace a přidejte webovou aplikaci umístěnou v oblasti Azure *v západní Evropě* jako koncový bod převzetí služeb při selhání. 

Pokud primární koncový bod není k dispozici, provoz automaticky směruje do koncového bodu převzetí služeb při selhání.

V následujícím příkladu nahraďte **<app1name_eastus>** a<app2name_westeurope **>** názvy aplikací vytvořené pro každou oblast v předchozí části, nahraďte<**appspname_eastus>** a<appspname_westeurope **>** názvem použitým k vytvoření plánů služby App Service v předchozí části a<**profile_name>** s názvem profilu použitým v předchozí části. 

**Koncový bod východní USA**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Poznamenejte si id zobrazené ve výstupu a použijte v následujícím příkazu přidat koncový bod:

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

**Koncový bod západní Evropy**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

Poznamenejte si id zobrazené ve výstupu a použijte v následujícím příkazu přidat koncový bod:

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

## <a name="test-your-traffic-manager-profile"></a>Otestujte svůj profil Traffic Manageru

V této části zkontrolujete název domény vašeho profilu Traffic Manageru. Nakonfigurujete také primární koncový bod tak, aby nebyl k dispozici. Nakonec uvidíte, že webová aplikace je stále k dispozici. Je to proto, že Traffic Manager odesílá provoz do koncového bodu převzetí služeb při selhání.

V následujícím příkladu nahraďte **<app1name_eastus>** a<app2name_westeurope **>** názvy aplikací vytvořené pro každou oblast v předchozí části, nahraďte<**appspname_eastus>** a<appspname_westeurope **>** názvem použitým k vytvoření plánů služby App Service v předchozí části a<**profile_name>** s názvem profilu použitým v předchozí části.

### <a name="determine-the-dns-name"></a>Určení názvu DNS

Pomocí zobrazení profilu správce provozu [sítě AZ](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show)určete název DNS profilu Traffic Manageru .

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Zkopírujte hodnotu **RelativeDnsName.** Název DNS profilu traffic manageru je *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci
1. Ve webovém prohlížeči zadejte název DNS profilu Traffic Manageru (*http://<* relativednsname *>.trafficmanager.net)* a zobrazte výchozí web webové aplikace Web Appu.

    > [!NOTE]
    > V tomto scénáři rychlého startu všechny požadavky směrovat k primární koncový bod. Je nastavena na **prioritu 1**.
2. Chcete-li zobrazit převzetí služeb při selhání traffic manageru v akci, zakažte primární lokalitu pomocí [aktualizace koncového bodu správce síťového provozu AZ](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Zkopírujte název DNS profilu Traffic Manageru (*http://<* relativednsname *>.trafficmanager.net)* a prohlédněte si web v nové relaci webového prohlížeče.
4. Ověřte, zda je webová aplikace stále dostupná.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení odstraňte skupiny prostředků, webové aplikace a všechny související prostředky pomocí [odstranění skupiny az](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili profil Traffic Manageru, který poskytuje vysokou dostupnost pro webovou aplikaci. Další informace o směrování provozu, pokračujte v kurzech Traffic Manager.

> [!div class="nextstepaction"]
> [Kurzy pro službu Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
