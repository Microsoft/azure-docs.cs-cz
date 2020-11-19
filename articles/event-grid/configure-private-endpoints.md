---
title: Konfigurace privátních koncových bodů pro Azure Event Grid témata nebo domény
description: Tento článek popisuje, jak nakonfigurovat soukromé koncové body pro Azure Event Grid témata nebo doménu.
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f8e0cfc0a850ae15ea6d03ff6ca8b90003adbfc9
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916936"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Konfigurace privátních koncových bodů pro Azure Event Grid témata nebo domény
Pomocí [privátních koncových bodů](../private-link/private-endpoint-overview.md) můžete v případě, že chcete přijímat události přímo z vaší virtuální sítě, zabezpečit vaše témata a domény přes [privátní propojení](../private-link/private-link-overview.md) , aniž byste museli procházet veřejným internetem. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro vaše téma nebo doménu. Další koncepční informace najdete v tématu [zabezpečení sítě](network-security.md).

Tento článek popisuje, jak nakonfigurovat privátní koncové body pro témata nebo domény.

## <a name="use-azure-portal"></a>Použití webu Azure Portal 
V této části se dozvíte, jak použít Azure Portal k vytvoření privátního koncového bodu pro téma nebo doménu.

> [!NOTE]
> Kroky uvedené v této části jsou většinou pro témata. Podobný postup můžete použít k vytvoření privátních koncových bodů pro **domény**. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k tématu nebo doméně.
2. Přepněte na kartu **sítě** stránky tématu. Na panelu nástrojů vyberte **+ soukromý koncový bod** .

    ![Přidat privátní koncový bod](./media/configure-private-endpoints/add-button.png)
2. Na stránce **základy** proveďte tyto kroky: 
    1. Vyberte **předplatné Azure** , ve kterém chcete vytvořit privátní koncový bod. 
    2. Vyberte **skupinu prostředků Azure** pro privátní koncový bod. 
    3. Zadejte **název** koncového bodu. 
    4. Vyberte **oblast** pro koncový bod. Váš privátní koncový bod musí být ve stejné oblasti jako vaše virtuální síť, ale může v jiné oblasti než prostředek privátního odkazu (v tomto příkladu téma Event gridu). 
    5. Potom v dolní části stránky vyberte tlačítko **Další: >prostředku** . 

      ![Stránka základy privátního koncového bodu](./media/configure-private-endpoints/basics-page.png)
3. Na stránce **prostředek** použijte následující postup: 
    1. V případě metody připojení vyberte **v adresáři připojit k prostředku Azure** a postupujte podle těchto kroků. Tento příklad ukazuje, jak se připojit k prostředku Azure ve vašem adresáři. 
        1. Vyberte **předplatné Azure** , ve kterém existuje vaše **téma nebo doména** . 
        1. V části **typ prostředku** vyberte **Microsoft. EventGrid/témata** nebo **Microsoft. EventGrid/domény** pro **typ prostředku**.
        2. V části **prostředek** vyberte v rozevíracím seznamu téma/doména. 
        3. Potvrďte, že je **cílový podprostředek** nastavený na **téma** nebo **doménu** (na základě vybraného typu prostředku).    
        4. V dolní části stránky vyberte tlačítko **Další: >konfigurace** . 

            ![Snímek obrazovky se stránkou vytvořit privátní koncový bod – prostředek](./media/configure-private-endpoints/resource-page.png)
    2. Pokud vyberete **připojit k prostředku pomocí ID prostředku nebo aliasu**, postupujte podle následujících kroků:
        1. Zadejte ID prostředku. Příklad: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. Jako **prostředek** zadejte **téma** nebo **doména**. 
        3. volitelné Přidejte zprávu požadavku. 
        4. V dolní části stránky vyberte tlačítko **Další: >konfigurace** . 

            ![Privátní koncový bod – stránka prostředků](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. Na stránce **Konfigurace** vyberte podsíť ve virtuální síti, do které chcete nasadit privátní koncový bod. 
    1. Vyberte **virtuální síť**. V rozevíracím seznamu jsou uvedené jenom virtuální sítě v aktuálně vybraném předplatném a umístění. 
    2. Vyberte **podsíť** ve virtuální síti, kterou jste vybrali. 
    3. V dolní části stránky vyberte tlačítko **Další: značky >** . 

    ![Soukromý koncový bod – konfigurační stránka](./media/configure-private-endpoints/configuration-page.png)
5. Na stránce **značky** vytvořte všechny značky (názvy a hodnoty), které chcete přidružit k prostředku privátního koncového bodu. Potom v dolní části stránky vyberte tlačítko **Revize + vytvořit** . 
6. Na stránce **zkontrolovat + vytvořit** Zkontrolujte všechna nastavení a výběrem možnosti **vytvořit** vytvořte privátní koncový bod. 

    ![Soukromý koncový bod – kontrola & vytvoření stránky](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>Spravovat připojení privátního propojení

Při vytváření privátního koncového bodu musí být připojení schváleno. Pokud je prostředek, pro který vytváříte privátní koncový bod, ve vašem adresáři, můžete žádost o připojení schválit, pokud máte dostatečná oprávnění. Pokud se připojujete k prostředku Azure v jiném adresáři, musíte počkat, až vlastník tohoto prostředku schválí žádost o připojení.

Existují čtyři stavy zřizování:

| Akce služby | Stav privátního koncového bodu příjemce služby | Description |
|--|--|--|
| Žádná | Čekající | Připojení je vytvořeno ručně a čeká na schválení vlastníkem prostředku privátního odkazu. |
| Schválení | Schválené | Připojení bylo automaticky nebo ručně schváleno a je připraveno k použití. |
| Odmítnout | Zamítnuto | Připojení bylo odmítnuto vlastníkem prostředku privátního odkazu. |
| Odebrat | Propojení | Připojení bylo odebráno vlastníkem prostředku privátního propojení, soukromý koncový bod bude informativní a měl by být odstraněn pro vyčištění. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Jak spravovat připojení privátního koncového bodu
V následujících částech se dozvíte, jak schválit nebo odmítnout připojení privátního koncového bodu. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu hledání zadejte **Event Grid témata** nebo **Event Grid domény**.
1. Vyberte **téma** nebo **doménu** , kterou chcete spravovat.
1. Vyberte kartu **Sítě**.
1. Pokud existují nějaká připojení, která čekají na **vyřízení, zobrazí se ve stavu** zřizování stav připojení. 

### <a name="to-approve-a-private-endpoint"></a>Schválení privátního koncového bodu
Můžete schválit soukromý koncový bod, který je ve stavu čekání na vyřízení. Ke schválení použijte následující postup: 

> [!NOTE]
> Kroky uvedené v této části jsou většinou pro témata. Podobný postup můžete použít ke schválení privátních koncových bodů pro **domény**. 

1. Vyberte **soukromý koncový bod** , který chcete schválit, a na panelu nástrojů vyberte **schválit** .

    ![Soukromý koncový bod – čeká se na stav](./media/configure-private-endpoints/pending.png)
1. V dialogovém okně **schválit připojení** zadejte komentář (volitelné) a vyberte **Ano**. 

    ![Soukromý koncový bod – schválit](./media/configure-private-endpoints/approve.png)
1. Potvrďte, že se stav koncového bodu zobrazuje jako **schváleno**. 

    ![Stav Schváleno soukromým koncovým bodem](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Zamítnutí privátního koncového bodu
Můžete odmítat soukromý koncový bod, který je ve stavu čekání nebo schválení. K odmítnutí použijte následující postup: 

> [!NOTE]
> Postup uvedený v této části najdete v tématu. Podobný postup můžete použít k odmítnutí privátních koncových bodů pro **domény**. 

1. Vyberte **soukromý koncový bod** , který chcete odmítnout, a na panelu nástrojů vyberte **odmítnout** .

    ![Snímek obrazovky zobrazující, že je vybrána možnost síť – privátní připojení ke koncovému bodu s názvem odmítnout.](./media/configure-private-endpoints/reject-button.png)
1. V dialogovém okně **odmítnout připojení** zadejte komentář (volitelné) a vyberte **Ano**. 

    ![Soukromý koncový bod – zamítnout](./media/configure-private-endpoints/reject.png)
1. Ověřte, že se stav koncového bodu zobrazuje jako **zamítnutý**. 

    ![Soukromý koncový bod – zamítnutý stav](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Po zamítnutí privátního koncového bodu v Azure Portal nemůžete schválit. 


## <a name="use-azure-cli"></a>Použití Azure CLI
Pokud chcete vytvořit privátní koncový bod, použijte metodu [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create) , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Popisy parametrů použitých v příkladu najdete v dokumentaci k [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create). Poznámka v tomto příkladu je několik bodů: 

- V případě `private-connection-resource-id` Zadejte ID prostředku **tématu** nebo **domény**. Předchozí příklad používá typ: téma.
- pro `group-ids` Zadejte `topic` nebo `domain` . V předchozím příkladu `topic` je použit. 

K odstranění privátního koncového bodu použijte metodu [AZ Network Private-Endpoint Delete](/cli/azure/network/private-endpoint?#az-network-private-endpoint-delete) , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Postup uvedený v této části najdete v tématu. Podobný postup můžete použít k vytvoření privátních koncových bodů pro **domény**. 



### <a name="prerequisites"></a>Požadavky
Aktualizujte rozšíření Azure Event Grid pro rozhraní příkazového řádku spuštěním následujícího příkazu: 

```azurecli-interactive
az extension update -n eventgrid
```

Pokud rozšíření není nainstalované, nainstalujte ho spuštěním následujícího příkazu: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu
Pokud chcete vytvořit privátní koncový bod, použijte metodu [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create) , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Popisy parametrů použitých v příkladu najdete v dokumentaci k [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create). Poznámka v tomto příkladu je několik bodů: 

- V případě `private-connection-resource-id` Zadejte ID prostředku **tématu** nebo **domény**. Předchozí příklad používá typ: téma.
- pro `group-ids` Zadejte `topic` nebo `domain` . V předchozím příkladu `topic` je použit. 

K odstranění privátního koncového bodu použijte metodu [AZ Network Private-Endpoint Delete](/cli/azure/network/private-endpoint?#az-network-private-endpoint-delete) , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Postup uvedený v této části najdete v tématu. Podobný postup můžete použít k vytvoření privátních koncových bodů pro **domény**. 

#### <a name="sample-script"></a>Ukázkový skript
Tady je ukázkový skript, který vytvoří následující prostředky Azure:

- Skupina prostředků
- Virtuální síť
- Podsíť ve virtuální síti
- Azure Event Grid téma
- Privátní koncový bod pro téma

> [!NOTE]
> Postup uvedený v této části najdete v tématu. Podobný postup můžete použít k vytvoření privátních koncových bodů pro domény.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# verify that the topic was created.
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

```

### <a name="approve-a-private-endpoint"></a>Schválení privátního koncového bodu
Následující ukázkový fragment kódu CLI ukazuje, jak schválit připojení privátního koncového bodu. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>Odmítnutí privátního koncového bodu
Následující ukázkový fragment kódu CLI ukazuje, jak odmítat připojení privátního koncového bodu. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>Zakázat přístup k veřejné síti
Ve výchozím nastavení je přístup k veřejné síti povolený pro Event Grid téma nebo doménu. Pokud chcete povolit přístup jenom přes soukromé koncové body, zakažte přístup k veřejné síti spuštěním tohoto příkazu:  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>Použití prostředí PowerShell
V této části se dozvíte, jak vytvořit privátní koncový bod pro téma nebo doménu pomocí prostředí PowerShell. 

### <a name="prerequisite"></a>Požadavek
Postupujte podle pokynů v tématu [Postupy: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům,](../active-directory/develop/howto-create-service-principal-portal.md) k vytvoření aplikace Azure Active Directory a poznamenejte si hodnoty pro **Directory (ID tenanta)**, **aplikace (klienta)** a **tajný kód aplikace (klienta)**. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Příprava tokenu a hlaviček pro volání REST API 
Spusťte následující příkazy, abyste získali ověřovací token pro použití s REST API voláními a autorizací a další informace hlavičky. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Vytvoření podsítě se zakázanými zásadami sítě koncového bodu

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Vytvoření tématu Event gridu s privátním koncovým bodem

> [!NOTE]
> Postup uvedený v této části najdete v tématu. Podobný postup můžete použít k vytvoření privátních koncových bodů pro **domény**. 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-06-01"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

Když ověříte, že byl koncový bod vytvořen, měl by se zobrazit výsledek podobný následujícímu:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Schválení připojení privátního koncového bodu
Následující ukázkový fragment kódu prostředí PowerShell ukazuje, jak schválit soukromý koncový bod. 

> [!NOTE]
> Postup uvedený v této části najdete v tématu. Podobný postup můžete použít ke schválení privátních koncových bodů pro **domény**. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Zamítnutí připojení privátního koncového bodu
Následující příklad ukazuje, jak odmítat privátní koncový bod pomocí prostředí PowerShell. Identifikátor GUID privátního koncového bodu můžete získat z výsledku předchozího příkazu GET. 

> [!NOTE]
> Postup uvedený v této části najdete v tématu. Podobný postup můžete použít k odmítnutí privátních koncových bodů pro **domény**. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01" ` 
    -Headers $Headers
```

Připojení můžete schválit i po jeho zamítnutí prostřednictvím rozhraní API. Pokud používáte Azure Portal, nemůžete schválit koncový bod, který byl odmítnut. 

## <a name="next-steps"></a>Další kroky
* Další informace o tom, jak nakonfigurovat nastavení brány firewall protokolu IP, najdete v tématu [Konfigurace brány firewall protokolu IP pro Azure Event Grid témat nebo domén](configure-firewall.md).
* Problémy s připojením k síti najdete v tématu řešení potíží s [připojením k síti](troubleshoot-network-connectivity.md) .
