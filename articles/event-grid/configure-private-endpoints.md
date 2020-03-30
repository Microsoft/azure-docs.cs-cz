---
title: Konfigurace privátních koncových bodů pro témata nebo domény Azure Event Grid
description: Tento článek popisuje, jak nakonfigurovat soukromé koncové body pro témata nebo doménu Služby Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: d08afe00c13a3f96b9526c3cb29804cfad688ddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299904"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains-preview"></a>Konfigurace privátních koncových bodů pro témata nebo domény Azure Event Grid (Preview)
[Privátní koncové body](../private-link/private-endpoint-overview.md) můžete povolit příchozí události přímo z vaší virtuální sítě do témat a domén bezpečně přes [privátní odkaz](../private-link/private-link-overview.md) bez nutnosti prostřednictvím veřejného internetu. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro vaše téma nebo doménu. Další rámcové informace naleznete v [tématu Zabezpečení sítě](network-security.md).

Tento článek popisuje, jak nakonfigurovat soukromé koncové body pro témata nebo domény.

> [!IMPORTANT]
> Funkce privátní koncové body je k dispozici pro témata a domény pouze v prémiové vrstvě. Pokud chcete upgradovat ze základní úrovně na úroveň Premium, přečtěte si článek [Aktualizovat cenovou úroveň.](update-tier.md) 

## <a name="use-azure-portal"></a>Použití webu Azure Portal 
Tato část ukazuje, jak pomocí portálu Azure vytvořit privátní koncový bod pro téma nebo doménu.

> [!NOTE]
> Kroky uvedené v této části jsou většinou pro témata. Podobné kroky můžete použít k vytvoření privátní koncové body pro **domény**. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a přejděte na téma nebo doménu.
2. Přepněte na kartu **Síť** na stránce tématu. Vyberte **+ Soukromý koncový bod** na panelu nástrojů.

    ![Přidání soukromého koncového bodu](./media/configure-private-endpoints/add-button.png)
2. Jedna stránka **Základy,** postupujte takto: 
    1. Vyberte **předplatné Azure,** ve kterém chcete vytvořit privátní koncový bod. 
    2. Vyberte **skupinu prostředků Azure** pro privátní koncový bod. 
    3. Zadejte **název** koncového bodu. 
    4. Vyberte **oblast** pro koncový bod. Váš soukromý koncový bod musí být ve stejné oblasti jako virtuální síť, ale může v jiné oblasti než prostředek privátní propojení (v tomto příkladu téma mřížky událostí). 
    5. Potom vyberte **Další: Tlačítko >zdrojů** v dolní části stránky. 

      ![Soukromý koncový bod - stránka základů](./media/configure-private-endpoints/basics-page.png)
3. Na stránce **Zdroj** postupujte takto: 
    1. Pokud pro metodu připojení vyberete **možnost Připojit k prostředku Azure v mém adresáři**, postupujte takto. Tento příklad ukazuje, jak se připojit k prostředku Azure ve vašem adresáři. 
        1. Vyberte **předplatné Azure,** ve kterém vaše **téma nebo doména** existuje. 
        1. Pro **typ prostředku**vyberte **Microsoft.EventGrid/topics** nebo **Microsoft.EventGrid/domains** pro **typ prostředku**.
        2. V **poli Zdroj**vyberte téma nebo doménu z rozevíracího seznamu. 
        3. Zkontrolujte, zda je **cílový podprostředek** nastaven na **téma** nebo **doménu** (na základě vybraného typu prostředku).    
        4. Vyberte **další: Tlačítko >konfigurace** v dolní části stránky. 

            ![Soukromý koncový bod – stránka prostředků](./media/configure-private-endpoints/resource-page.png)
    2. Pokud vyberete **Připojit k prostředku pomocí ID prostředku nebo aliasu**, postupujte takto:
        1. Zadejte ID zdroje. Například: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. V **poli Prostředek**zadejte **téma** nebo **doménu**. 
        3. (nepovinné) Přidejte zprávu požadavku. 
        4. Vyberte **další: Tlačítko >konfigurace** v dolní části stránky. 

            ![Soukromý koncový bod – stránka prostředků](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. Na stránce **Konfigurace** vyberete podsíť ve virtuální síti, kam chcete nasadit privátní koncový bod. 
    1. Vyberte **virtuální síť**. V rozevíracím seznamu jsou uvedeny pouze virtuální sítě v aktuálně vybraném předplatném a umístění. 
    2. Vyberte **podsíť** ve virtuální síti, kterou jste vybrali. 
    3. Vyberte **Další: Značky >** tlačítko v dolní části stránky. 

    ![Soukromý koncový bod – konfigurační stránka](./media/configure-private-endpoints/configuration-page.png)
5. Na stránce **Značky** vytvořte všechny značky (názvy a hodnoty), které chcete přidružit k prostředku soukromého koncového bodu. Potom vyberte **tlačítko Revize + vytvořit** v dolní části stránky. 
6. V části **Revize + vytvořit**zkontrolujte všechna nastavení a vyberte **Vytvořit,** chcete-li vytvořit soukromý koncový bod. 

    ![Soukromý koncový bod – kontrola & vytvoření stránky](./media/configure-private-endpoints/review-create-page.png)
    

## <a name="manage-private-link-connection"></a>Správa připojení k soukromému propojení

Při vytváření soukromého koncového bodu musí být připojení schváleno. Pokud je prostředek, pro který vytváříte soukromý koncový bod, ve vašem adresáři, můžete žádost o připojení schválit za předpokladu, že máte dostatečná oprávnění. Pokud se připojujete k prostředku Azure v jiném adresáři, musíte počkat na vlastníka tohoto prostředku schválit váš požadavek na připojení.

Existují čtyři stavy zřizování:

| Akce služby | Stav koncového bodu příjemce služby | Popis |
|--|--|--|
| Žádný | Čekající na vyřízení | Připojení je vytvořeno ručně a čeká na schválení od vlastníka soukromého prostředku propojení. |
| Schválení | Schválené | Připojení bylo automaticky nebo ručně schváleno a je připraveno k použití. |
| Odmítnout | Rejected | Připojení bylo odmítnuto vlastníkem prostředku soukromého propojení. |
| Odebrat | Odpojen | Připojení bylo odebráno vlastníkem prostředku soukromého propojení, soukromý koncový bod se stane informativní a měl by být odstraněn pro vyčištění. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Správa privátního připojení koncového bodu
Následující části ukazují, jak schválit nebo odmítnout připojení soukromého koncového bodu. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Do vyhledávacího panelu zadejte **témata event gridu** nebo **domény Mřížky událostí**.
1. Vyberte **téma** nebo **doménu,** kterou chcete spravovat.
1. Vyberte kartu **Síť.**
1. Pokud existují všechna připojení, která čekají na vyřízení, uvidíte připojení uvedené s Čekající na **vyřízení** ve stavu zřizování. 

### <a name="to-approve-a-private-endpoint"></a>Schválení soukromého koncového bodu
Můžete schválit soukromý koncový bod, který je ve stavu čekající na vyřízení. Chcete-li schválit, postupujte takto: 

> [!NOTE]
> Kroky uvedené v této části jsou většinou pro témata. Podobné kroky můžete použít ke schválení soukromých koncových bodů pro **domény**. 

1. Vyberte **soukromý koncový bod,** který chcete schválit, a na panelu nástrojů vyberte **Schválit.**

    ![Soukromý koncový bod – stav čekající na vyřízení](./media/configure-private-endpoints/pending.png)
1. V dialogovém okně **Schválit připojení** zadejte komentář (volitelný) a vyberte **Ano**. 

    ![Soukromý koncový bod - schválit](./media/configure-private-endpoints/approve.png)
1. Zkontrolujte, zda se stav koncového bodu zobrazuje jako **schválený**. 

    ![Soukromý koncový bod - schválený stav](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Odmítnutí soukromého koncového bodu
Můžete odmítnout soukromý koncový bod, který je ve stavu čekající na vyřízení nebo schváleného stavu. Chcete-li odmítnout, postupujte takto: 

> [!NOTE]
> Kroky uvedené v této části jsou určeny pro témata. Podobné kroky můžete použít k odmítnutí soukromých koncových bodů pro **domény**. 

1. Vyberte **soukromý koncový bod,** který chcete odmítnout, a na panelu nástrojů vyberte **Odmítnout.**

    ![Soukromý koncový bod - odmítnout](./media/configure-private-endpoints/reject-button.png)
1. V dialogovém okně **Odmítnout připojení** zadejte komentář (volitelný) a vyberte **Ano**. 

    ![Soukromý koncový bod - odmítnout](./media/configure-private-endpoints/reject.png)
1. Potvrďte, že se zobrazí stav koncového bodu jako **Odmítnuto**. 

    ![Soukromý koncový bod - odmítnutý stav](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Soukromý koncový bod na webu Azure Portal nelze schválit, jakmile je odmítnut. 


## <a name="use-azure-cli"></a>Použití Azure CLI
Chcete-li vytvořit soukromý koncový bod, použijte metodu [vytvoření privátního koncového bodu sítě AZ,](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) jak je znázorněno v následujícím příkladu:

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

Popis parametrů použitých v příkladu naleznete v dokumentaci k [vytvoření privátního koncového bodu sítě AZ](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create). Několik bodů na vědomí v tomto příkladu jsou: 

- V `private-connection-resource-id`aplikaci zadejte ID prostředku **tématu** nebo **domény**. Předchozí příklad používá typ: téma.
- pro `group-ids`, `topic` `domain`uveďte nebo . V předchozím příkladu `topic` se používá. 

Chcete-li odstranit soukromý koncový bod, použijte metodu [odstranění privátního koncového bodu sítě AZ,](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Kroky uvedené v této části jsou určeny pro témata. Podobné kroky můžete použít k vytvoření privátní koncové body pro **domény**. 

### <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu
Tady je ukázkový skript, který vytvoří následující prostředky Azure:

- Skupina prostředků
- Virtuální síť
- Podsíť ve virtuální síti
- Téma Azure Event Grid (úroveň premium)
- Soukromý koncový bod pro téma

> [!NOTE]
> Kroky uvedené v této části jsou určeny pro témata. Podobné kroky můžete použít k vytvoření privátní koncové body pro domény.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# URI for the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicUri="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>?api-version=2020-04-01-preview"

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
az rest --method put \
    --uri $topicUri \
    --body "{\""location\"":\""LOCATION\"", \""sku\"": {\""name\"": \""premium\""}, \""properties\"": {\""publicNetworkAccess\"":\""Disabled\""}}"

# verify that the topic was created.
az rest --method get \
    --uri $topicUri

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
az rest --method get \
    --uri $topicUri

```

### <a name="approve-a-private-endpoint-connection"></a>Schválení připojení privátního koncového bodu
Následující ukázkový fragment příkazového příkazového příkazu ukazuje, jak schválit připojení soukromého koncového bodu. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""approved\"",\""description\"":\""connection approved\"", \""actionsRequired\"": \""none\""}}}"
```


### <a name="reject-a-private-endpoint-connection"></a>Odmítnutí připojení privátního koncového bodu
Následující ukázkový fragment příkazového příkazového příkazu ukazuje, jak odmítnout připojení soukromého koncového bodu. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""rejected\"",\""description\"":\""connection rejected\"", \""actionsRequired\"": \""none\""}}}"
```


## <a name="use-powershell"></a>Použití prostředí PowerShell
Tato část ukazuje, jak vytvořit soukromý koncový bod pro téma nebo doménu pomocí PowerShellu. 

### <a name="prerequisite"></a>Požadavek
Postupujte podle pokynů z [postup: Pomocí portálu vytvořte instanční objekt aplikace azure a služby, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md) k vytvoření aplikace Azure Active Directory a poznamenejte si hodnoty **adresáře (tenanta), ID,** **ID aplikace (klienta)** a **tajný klíč aplikace (klienta).** 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Příprava tokenu a záhlaví pro volání rozhraní REST API 
Spusťte následující příkazy předpokladů, abyste získali ověřovací token pro použití s voláním rozhraní REST API a autorizací a dalšími informacemi záhlaví. 

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

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Vytvoření tématu mřížky událostí se soukromým koncovým bodem

> [!NOTE]
> Kroky uvedené v této části jsou určeny pro témata. Podobné kroky můžete použít k vytvoření privátní koncové body pro **domény**. 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="premium"}; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"   `
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
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-04-01-preview"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

Když ověříte, že koncový bod byl vytvořen, měli byste vidět výsledek podobný následující:

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
Následující ukázkový fragment prostředí PowerShell ukazuje, jak schválit soukromý koncový bod. 

> [!NOTE]
> Kroky uvedené v této části jsou určeny pro témata. Podobné kroky můžete použít ke schválení soukromých koncových bodů pro **domény**. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Odmítnutí připojení privátního koncového bodu
Následující příklad ukazuje, jak odmítnout soukromý koncový bod pomocí prostředí PowerShell. Identifikátor GUID pro soukromý koncový bod můžete získat z výsledku předchozího příkazu GET. 

> [!NOTE]
> Kroky uvedené v této části jsou určeny pro témata. Podobné kroky můžete použít k odmítnutí soukromých koncových bodů pro **domény**. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" ` 
    -Headers $Headers
```

Připojení můžete schválit i poté, co je odmítnuto prostřednictvím rozhraní API. Pokud používáte portál Azure, nemůžete schválit koncový bod, který byl odmítnut. 

## <a name="next-steps"></a>Další kroky
Informace o konfiguraci nastavení brány firewall IP najdete [v tématu Konfigurace brány firewall IP pro témata nebo domény služby Azure Event Grid](configure-firewall.md).