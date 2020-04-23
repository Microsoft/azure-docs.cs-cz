---
title: Aktualizace cenové úrovně tématu Azure Event Grid nebo domény
description: Tento článek popisuje, jak aktualizovat cenovou úroveň Azure Event Gridho tématu nebo domény (Basic to Premium, Premium na Basic) pomocí Azure Portal, Azure CLI a Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: caea8d515964510fce432eb0497e3af19ecc1369
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101633"
---
# <a name="update-pricing-tier"></a>Aktualizace cenové úrovně 
V tomto článku se dozvíte, jak aktualizovat cenovou úroveň Azure Event Grid tématu nebo domény pomocí Azure Portal, Azure CLI a Azure PowerShell. 

## <a name="use-azure-portal"></a>Použití webu Azure Portal
V této části se dozvíte, jak změnit cenovou úroveň tématu nebo domény v Azure Portal. 

### <a name="overview-page"></a>Stránka Přehled
Cenovou úroveň tématu nebo domény můžete změnit na stránce **Přehled** . Následující příklad ukazuje, jak upgradovat téma z úrovně Basic na úroveň Premium. Postup pro přechod z úrovně Premium na úroveň Basic je podobný.

1. V [Azure Portal](https://portal.azure.com)přejděte na téma nebo na stránku domény. 
2. Na stránce **Přehled** vyberte aktuální cenovou úroveň (v následujícím příkladu je to **základní**).
    
    ![Výběr aktuální cenové úrovně](./media/update-tier/select-tier.png)
3. Na stránce **cenová úroveň** změňte úroveň a vyberte **OK**. 

    ![Aktualizace cenové úrovně](./media/update-tier/change-tier.png)
4. Stav operace ověřte na stránce **oznámení** .

    ![Stav aktualizace](./media/update-tier/status.png)    
5. Ověřte, že se na stránce **Přehled** zobrazuje aktualizovaná úroveň. 

    ![Stav aktualizace](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Stránka sítě
Můžete **upgradovat** z úrovně Basic na úroveň Premium na stránce **síť** . Na této stránce se ale nedá downgradovat z úrovně Premium na úroveň Basic. 

1. V [Azure Portal](https://portal.azure.com)přejděte na téma nebo na stránku domény. 
2. Na stránce **síť** přepněte na kartu **připojení privátního koncového bodu (Preview)** . 
3. Pokud je aktuální cenová úroveň **Basic**, zobrazí se následující zpráva. Vyberte ji. 

    ![Stránka aktualizace úrovně na privátních připojeních koncových bodů](./media/update-tier/private-endpoint-connections-page.png)
4. Na stránce **aktualizace cenové úrovně Premium** vyberte **Ano**. 
    
    ![Potvrdit upgrade](./media/update-tier/message-private-endpoint-connection.png)
5. Stav operace ověřte na stránce **oznámení** .

    ![Stav aktualizace](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Použití Azure CLI
V této části se dozvíte, jak použít příkazy rozhraní příkazového řádku Azure ke změně cenové úrovně tématu. Pokud chcete aktualizovat cenovou úroveň domény, použijte `az eventgrid domain update` příkaz podobným způsobem.

### <a name="prerequisites"></a>Požadované součásti
Aktualizujte rozšíření Azure Event Grid pro rozhraní příkazového řádku spuštěním následujícího příkazu: 

```azurecli-interactive
az extension update -n eventgrid
```

Pokud rozšíření není nainstalované, nainstalujte ho spuštěním následujícího příkazu: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Upgrade tématu z úrovně Basic na Premium

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Premium" 
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Downgrade tématu z úrovně Premium na Basic

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Basic" 
```


## <a name="use-azure-powershell"></a>Použití Azure Powershell
V této části se dozvíte, jak pomocí příkazů PowerShellu změnit cenovou úroveň tématu nebo domény. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Příprava tokenu a hlaviček pro volání REST API 
Spusťte následující příkazy, abyste získali ověřovací token pro použití s REST API voláními a autorizací a další informace hlavičky. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Upgrade tématu z úrovně Basic na Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Downgrade tématu z úrovně Premium na Basic

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Upgrade domény z úrovně Basic na Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Downgrade domény z úrovně Premium na Basic

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Další kroky
Pro témata a domény úrovně Premium můžete nakonfigurovat privátní koncové body, aby se omezil přístup jenom z vybraných virtuálních sítí. Podrobné pokyny najdete v tématu [Konfigurace privátních koncových bodů](configure-private-endpoints.md).
