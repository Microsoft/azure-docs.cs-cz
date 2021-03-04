---
title: Konfigurace brány firewall protokolu IP pro Azure Event Grid témata nebo domény
description: Tento článek popisuje, jak nakonfigurovat nastavení brány firewall pro Event Grid témata nebo domény.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 178b9d84ea8b2e0f764f7584526db8dbcf5284f3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031833"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains"></a>Konfigurace brány firewall protokolu IP pro Azure Event Grid témata nebo domény 
Ve výchozím nastavení je k tématům a doménám přístup z Internetu, pokud požadavek přichází s platným ověřováním a autorizací. Pomocí brány firewall protokolu IP je můžete omezit na další jenom na sadu IPv4 adres nebo rozsahů IPv4 adres v zápisu [CIDR (bez třídy) (směrování Inter-Domain)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Vydavatelé pocházející z jakékoli jiné IP adresy se odmítnou a obdrží odpověď 403 (zakázáno). Další informace o funkcích zabezpečení sítě podporovaných nástrojem Event Grid najdete v tématu [zabezpečení sítě pro Event Grid](network-security.md).

Tento článek popisuje, jak nakonfigurovat nastavení brány firewall protokolu IP pro Azure Event Grid témata nebo domény.

## <a name="use-azure-portal"></a>Použití webu Azure Portal
V této části se dozvíte, jak pomocí Azure Portal vytvořit pravidla brány firewall pro příchozí IP adresy. Postup uvedený v této části najdete v tématu. Pomocí podobných kroků můžete vytvořit pravidla příchozího přenosu IP adres pro **domény**. 

1. V [Azure Portal](https://portal.azure.com)přejděte na téma nebo doménu služby Event Grid a přepněte na kartu **síť** .
2. Vyberte **veřejné sítě** , pokud chcete, aby všechny sítě, včetně Internetu, umožňovaly přístup k prostředku. 

    Přenos můžete omezit pomocí pravidel brány firewall založené na protokolu IP. Zadejte jednu adresu IPv4 nebo rozsah IP adres v zápisu CIDR (Classless Inter-Domain Routing). 

    ![Snímek obrazovky zobrazující stránku přístup k veřejné síti s vybranými možnostmi veřejné sítě.](./media/configure-firewall/public-networks-page.png)
3. Vyberte **soukromé koncové body jenom** pro přístup k tomuto prostředku jenom připojením privátního koncového bodu. Pomocí karty **připojení privátního koncového bodu** na této stránce můžete spravovat připojení. 

    ![Stránka veřejné sítě](./media/configure-firewall/private-endpoints-page.png)
4. Na panelu nástrojů vyberte **Uložit**. 



## <a name="use-azure-cli"></a>Použití Azure CLI
V této části se dozvíte, jak pomocí příkazů rozhraní příkazového řádku Azure vytvářet témata s pravidly příchozích IP adres. Postup uvedený v této části najdete v tématu. Pomocí podobných kroků můžete vytvořit pravidla příchozího přenosu IP adres pro **domény**. 


### <a name="enable-or-disable-public-network-access"></a>Povolit nebo zakázat přístup k veřejné síti
Ve výchozím nastavení je přístup k veřejné síti povolený pro témata a domény. Můžete ho taky povolit explicitně nebo zakázat. Můžete omezit provoz konfigurací pravidel brány firewall pro příchozí IP adresy. 

#### <a name="enable-public-network-access-while-creating-a-topic"></a>Povolit přístup k veřejné síti při vytváření tématu

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled
```


#### <a name="disable-public-network-access-while-creating-a-topic"></a>Zakázat přístup k veřejné síti při vytváření tématu

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access disabled
```

> [!NOTE]
> Když je přístup k veřejné síti pro téma nebo doménu zakázaný, provoz přes veřejný Internet není povolený. Přístup k těmto prostředkům budou mít jenom připojení privátního koncového bodu. 


#### <a name="enable-public-network-access-for-an-existing-topic"></a>Povolit přístup k veřejné síti pro existující téma

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled 
```

#### <a name="disable-public-network-access-for-an-existing-topic"></a>Zakázat přístup k veřejné síti pro existující téma 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```

### <a name="create-a-topic-with-single-inbound-ip-rule"></a>Vytvoření tématu s pravidlem jednoho příchozího IP adres
V následujícím ukázkovém příkazu rozhraní příkazového řádku se vytvoří téma Event Grid s pravidly příchozího přenosu IP adres. 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow 
```

### <a name="create-a-topic-with-multiple-inbound-ip-rules"></a>Vytvoření tématu s více pravidly příchozí IP adresy

Následující ukázkový příkaz rozhraní příkazového řádku vytvoří v jednom kroku téma Event Grid – dvě pravidla příchozích IP adres: 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="update-an-existing-topic-to-add-inbound-ip-rules"></a>Aktualizace existujícího tématu pro přidání pravidel příchozího přenosu IP adres
V tomto příkladu se nejdřív vytvoří téma Event Grid a pak se do samostatného příkazu přidá pravidla příchozího přenosu IP adres pro toto téma. Aktualizuje taky pravidla příchozího přenosu IP, která se nastavila ve druhém příkazu. 

```azurecli-interactive

# create the event grid topic first
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# add inbound IP rules to an existing topic
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow

# later, update topic with additional ip rules
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="remove-an-inbound-ip-rule"></a>Odebrat pravidlo příchozí IP adresy
Následující příkaz odebere druhé pravidlo, které jste vytvořili v předchozím kroku, zadáním pouze prvního pravidla při aktualizaci nastavení. 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow
```


## <a name="use-powershell"></a>Použití prostředí PowerShell
V této části se dozvíte, jak pomocí Azure PowerShellch příkazů vytvářet Azure Event Grid témata s pravidly brány firewall pro příchozí IP adresy. Postup uvedený v této části najdete v tématu. Pomocí podobných kroků můžete vytvořit pravidla příchozího přenosu IP adres pro **domény**. 

### <a name="prerequisites"></a>Požadavky
Postupujte podle pokynů v tématu [Postupy: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md) k vytvoření aplikace Azure Active Directory a poznamenejte si následující hodnoty:

- ID adresáře (tenanta)
- ID aplikace (klienta)
- Tajný kód aplikace (klienta)

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

### <a name="enable-public-network-access-for-an-existing-topic"></a>Povolit přístup k veřejné síti pro existující téma
Ve výchozím nastavení je přístup k veřejné síti povolený pro témata a domény. Můžete omezit provoz konfigurací pravidel brány firewall pro příchozí IP adresy. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Zakázat přístup k veřejné síti pro existující téma
Když je přístup k veřejné síti pro téma nebo doménu zakázaný, provoz přes veřejný Internet není povolený. Přístup k těmto prostředkům budou mít jenom připojení privátního koncového bodu. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Vytvoření tématu Event gridu s příchozími pravidly v jednom kroku

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Nejprve vytvořte téma Event Grid a pak přidejte pravidla příchozího přenosu adres IP.

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature is available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"}, @{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Další kroky

* Informace o sledování doručení událostí najdete v tématu [monitorování Event Grid doručování zpráv](monitor-event-delivery.md).
* Další informace o ověřovacím klíči najdete v tématu [Event Grid Security and Authentication](security-authentication.md).
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).
* Problémy s připojením k síti najdete v tématu řešení potíží s [připojením k síti](troubleshoot-network-connectivity.md) .
