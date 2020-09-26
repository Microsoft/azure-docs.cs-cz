---
title: Konfigurace privátního odkazu Azure pro účet Azure Cosmos
description: Přečtěte si, jak nastavit privátní odkaz Azure pro přístup k účtu Azure Cosmos pomocí privátní IP adresy ve virtuální síti.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/18/2020
ms.author: thweiss
ms.custom: devx-track-azurecli
ms.openlocfilehash: dd1a59c2e6b0656233174c53b08ab013ce73d0f1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334425"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Konfigurace privátního odkazu Azure pro účet Azure Cosmos

Pomocí privátního odkazu Azure se můžete připojit k účtu Azure Cosmos prostřednictvím privátního koncového bodu. Soukromý koncový bod je sada privátních IP adres v podsíti v rámci vaší virtuální sítě. Pak můžete omezit přístup k účtu Azure Cosmos prostřednictvím privátních IP adres. Pokud se soukromé propojení spojí s omezenými NSG zásadami, pomáhá to snížit riziko exfiltrace dat. Další informace o privátních koncových bodech najdete v článku věnovaném [privátním odkazům Azure](../private-link/private-link-overview.md) .

> [!NOTE]
> Privátní odkaz nebrání tomu, aby se koncovým bodům Azure Cosmosy vyřešily veřejné DNS. Filtrování příchozích požadavků probíhá na úrovni aplikace, nikoli na úrovni přenosu nebo sítě.

Privátní odkaz umožňuje uživatelům přístup k účtu Azure Cosmos z virtuální sítě nebo z kterékoli z partnerských virtuálních sítí. Prostředky namapované na soukromé odkazy jsou k dispozici i místně přes privátní partnerský vztah prostřednictvím sítě VPN nebo Azure ExpressRoute. 

K účtu Azure Cosmos nakonfigurovanému pomocí privátního propojení se můžete připojit pomocí metody automatického nebo ručního schválení. Další informace najdete v části [schvalovací pracovní postup](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) v dokumentaci k privátnímu odkazu. 

Tento článek popisuje postup vytvoření privátního koncového bodu. Předpokládá, že používáte metodu automatického schvalování.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Vytvoření privátního koncového bodu pomocí Azure Portal

Pomocí následujících kroků můžete vytvořit privátní koncový bod pro existující účet Azure Cosmos pomocí Azure Portal:

1. V podokně **všechny prostředky** vyberte účet Azure Cosmos.

1. V seznamu nastavení vyberte **připojení privátního koncového bodu** a pak vyberte **privátní koncový bod**:

   :::image type="content" source="./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png" alt-text="Výběry pro vytvoření privátního koncového bodu v Azure Portal":::

1. V podokně **Vytvoření privátního koncového bodu – základy** zadejte nebo vyberte následující podrobnosti:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte předplatné. |
    | Skupina prostředků | Vyberte skupinu prostředků.|
    | **Podrobnosti o instancích** |  |
    | Name | Zadejte libovolný název vašeho privátního koncového bodu. Pokud se tento název povede, vytvořte jedinečný. |
    |Region| Vyberte oblast, ve které chcete nasadit privátní propojení. Vytvořte privátní koncový bod ve stejném umístění, kde existuje vaše virtuální síť.|
    |||
1. Vyberte **Další: prostředek**.
1. V **Vytvoření privátního koncového bodu – prostředek**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | **V adresáři vyberte připojit k prostředku Azure**. <br/><br/> Pak můžete zvolit jeden z vašich prostředků pro nastavení privátního propojení. Nebo se můžete připojit k prostředku jiné osoby pomocí ID prostředku nebo aliasu, který s vámi sdílí.|
    | Předplatné| Vyberte předplatné. |
    | Typ prostředku | Vyberte **Microsoft. AzureCosmosDB/databaseAccounts**. |
    | Prostředek |Vyberte svůj účet Azure Cosmos. |
    |Cílový dílčí prostředek |Vyberte Azure Cosmos DB typ rozhraní API, který chcete namapovat. Tato možnost je standardně jenom jedna volba pro rozhraní API SQL, MongoDB a Cassandra. Pro Gremlin a rozhraní Table API můžete také zvolit **SQL** , protože tato rozhraní API se vzájemně spolupracují s rozhraním SQL API. |
    |||

1. Vyberte **Další: Konfigurace**.
1. V **Vytvoření privátního koncového bodu – konfigurace**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**Sítě**| |
    | Virtuální síť| Vyberte svou virtuální síť. |
    | Podsíť | Vyberte podsíť. |
    |**Integrace Privátní DNS**||
    |Integrovat s privátní zónou DNS |Vyberte **Ano**. <br><br/> Abyste mohli soukromě propojit s vaším soukromým koncovým bodem, budete potřebovat záznam DNS. Doporučujeme integrovat privátní koncový bod s privátní zónou DNS. Můžete také použít vlastní servery DNS nebo vytvořit záznamy DNS pomocí hostitelských souborů na virtuálních počítačích. |
    |Zóna Privátního DNS |Vyberte **privatelink.documents.Azure.com**. <br><br/> Privátní zóna DNS je určena automaticky. Nemůžete ho změnit pomocí Azure Portal.|
    |||

1. Vyberte **Zkontrolovat a vytvořit**. Na stránce **Revize + vytvořit** Azure ověří vaši konfiguraci.
1. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**.

Pokud jste pro účet Azure Cosmos schválili privátní odkaz, v podokně Azure Portal není dostupná možnost **všechny sítě** v podokně **Brána firewall a virtuální sítě** .

Následující tabulka ukazuje mapování mezi různými typy rozhraní API účtu Azure Cosmos, podporovaných dílčími prostředky a odpovídajícími názvy privátních zón. K účtům Gremlin a rozhraní API pro tabulky můžete přistupovat prostřednictvím rozhraní SQL API, takže existují dvě položky pro tato rozhraní API.

|Typ rozhraní API účtu Azure Cosmos  |Podporované dílčí prostředky (nebo ID skupin) |Název privátní zóny  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Tabulka    |    Tabulka     |   privatelink.table.cosmos.azure.com    |
|Tabulka     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Načtení privátních IP adres

Po zřízení privátního koncového bodu můžete zadat dotaz na IP adresy. Postup zobrazení IP adres z Azure Portal:

1. Vyberte **Všechny prostředky**.
1. Vyhledejte privátní koncový bod, který jste vytvořili dříve. V tomto případě je to **cdbPrivateEndpoint3**.
1. Vyberte kartu **Přehled** a zobrazte tak nastavení DNS a IP adresy.

:::image type="content" source="./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png" alt-text="Privátní IP adresy v Azure Portal":::

Pro jeden privátní koncový bod se vytvoří víc IP adres:

* Jeden pro koncový bod globálního (region-nezávislá) účtu Azure Cosmos
* Jednu pro každou oblast, ve které je nasazený účet Azure Cosmos

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Vytvoření privátního koncového bodu pomocí Azure PowerShell

Spusťte následující skript prostředí PowerShell pro vytvoření privátního koncového bodu s názvem "MyPrivateEndpoint" pro existující účet Azure Cosmos. Hodnoty proměnných nahraďte podrobnostmi pro vaše prostředí.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrace privátního koncového bodu s privátní zónou DNS

Po vytvoření privátního koncového bodu ho můžete integrovat s privátní zónou DNS pomocí následujícího skriptu prostředí PowerShell:

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>Načtení privátních IP adres

Po zřízení privátního koncového bodu můžete zadat dotaz na IP adresy a mapování plně kvalifikovaného názvu domény pomocí následujícího skriptu prostředí PowerShell:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Vytvoření privátního koncového bodu pomocí Azure CLI

Spusťte následující skript Azure CLI pro vytvoření privátního koncového bodu s názvem "myPrivateEndpoint" pro existující účet Azure Cosmos. Hodnoty proměnných nahraďte podrobnostmi pro vaše prostředí.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrace privátního koncového bodu s privátní zónou DNS

Po vytvoření privátního koncového bodu ho můžete integrovat s privátní zónou DNS pomocí následujícího skriptu Azure CLI:

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Vytvoření privátního koncového bodu pomocí šablony Správce prostředků

Soukromý odkaz můžete nastavit vytvořením privátního koncového bodu v podsíti virtuální sítě. Dosáhnete toho pomocí šablony Azure Resource Manager.

Pomocí následujícího kódu vytvořte šablonu Správce prostředků s názvem "PrivateEndpoint_template.json". Tato šablona vytvoří privátní koncový bod pro existující účet rozhraní SQL API služby Azure Cosmos v existující virtuální síti.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**Definování souboru parametrů pro šablonu**

Vytvořte soubor parametrů pro šablonu a pojmenujte ji "PrivateEndpoint_parameters.json". Do souboru Parameters přidejte následující kód:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**Nasazení šablony pomocí skriptu PowerShellu**

Pomocí následujícího kódu vytvořte skript prostředí PowerShell. Před spuštěním skriptu nahraďte ID předplatného, název skupiny prostředků a další hodnoty proměnných podrobnostmi pro vaše prostředí.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

V PowerShellovém skriptu `GroupId` může proměnná obsahovat jenom jednu hodnotu. Tato hodnota je typ rozhraní API tohoto účtu. Povolené hodnoty jsou: `Sql` , `MongoDB` , `Cassandra` , `Gremlin` a `Table` . Některé typy účtů Azure Cosmos jsou přístupné prostřednictvím více rozhraní API. Příklad:

* K účtu rozhraní Gremlin API je možné přihlédnout z účtů Gremlin a SQL API.
* K účtu rozhraní API pro tabulky lze přihlédnout z tabulek i z účtů rozhraní SQL API.

Pro tyto účty musíte pro každý typ rozhraní API vytvořit jeden privátní koncový bod. V poli je uveden odpovídající typ rozhraní API `GroupId` .

Po úspěšném nasazení šablony se zobrazí výstup podobný následujícímu obrázku. `provisioningState`Hodnota je, `Succeeded` Pokud jsou privátní koncové body nastaveny správně.

:::image type="content" source="./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png" alt-text="Výstup nasazení pro šablonu Správce prostředků":::

Po nasazení šablony jsou privátní IP adresy rezervované v rámci podsítě. Pravidlo brány firewall účtu Azure Cosmos je nakonfigurované tak, aby přijímalo jenom připojení z privátního koncového bodu.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrace privátního koncového bodu se zónou Privátní DNS

Pomocí následujícího kódu vytvořte šablonu Správce prostředků s názvem "PrivateZone_template.json". Tato šablona vytvoří privátní zónu DNS pro existující účet rozhraní SQL API služby Azure Cosmos v existující virtuální síti.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

Pomocí následujícího kódu vytvořte šablonu Správce prostředků s názvem "PrivateZoneRecords_template.json".

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**Definování souboru parametrů pro šablonu**

Vytvořte pro šablonu následující dva soubory parametrů. Vytvoří PrivateZone_parameters.js. s následujícím kódem:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Vytvoří PrivateZoneRecords_parameters.js. s následujícím kódem:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**Nasazení šablony pomocí skriptu PowerShellu**

Pomocí následujícího kódu vytvořte skript prostředí PowerShell. Před spuštěním skriptu nahraďte ID předplatného, název skupiny prostředků a další hodnoty proměnných podrobnostmi pro vaše prostředí.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Konfigurace vlastního systému DNS

V podsíti, ve které jste vytvořili privátní koncový bod, byste měli použít privátní zónu DNS. Konfigurace koncových bodů tak, aby každá privátní IP adresa byla namapovaná na položku DNS. (Podívejte se na `fqdns` vlastnost v odpovědi uvedené dříve.)

Při vytváření privátního koncového bodu ho můžete integrovat s privátní zónou DNS v Azure. Pokud se rozhodnete místo toho použít vlastní zónu DNS, musíte ji nakonfigurovat tak, aby přidala záznamy DNS pro všechny privátní IP adresy rezervované pro soukromý koncový bod.

## <a name="private-link-combined-with-firewall-rules"></a>Privátní propojení v kombinaci s pravidly brány firewall

Při použití privátního odkazu v kombinaci s pravidly brány firewall jsou možné následující situace a výsledky:

* Pokud žádná pravidla brány firewall nenakonfigurujete, budou mít všechny přenosy ve výchozím nastavení přístup k účtu Azure Cosmos.

* Pokud nakonfigurujete veřejný provoz nebo koncový bod služby a vytvoříte privátní koncové body, budou se podle odpovídajícího typu pravidla brány firewall autorizovat různé typy příchozích přenosů. Pokud je privátní koncový bod nakonfigurovaný v podsíti, ve které je taky nakonfigurovaný koncový bod služby:
  * provoz do databázového účtu mapovaného privátním koncovým bodem je směrován prostřednictvím privátního koncového bodu,
  * provoz do jiných databázových účtů z podsítě je směrován prostřednictvím koncového bodu služby.

* Pokud neprovedete konfiguraci žádného veřejného provozu nebo koncového bodu služby a vytvoříte privátní koncové body, účet Azure Cosmos je přístupný jenom prostřednictvím privátních koncových bodů. Pokud nenastavíte veřejný provoz nebo koncový bod služby, po odmítnutí nebo odstranění všech schválených privátních koncových bodů se účet otevře v celé síti, pokud není PublicNetworkAccess nastavené na zakázáno (viz část níže).

## <a name="blocking-public-network-access-during-account-creation"></a>Blokování přístupu k veřejné síti při vytváření účtu

Jak je popsáno v předchozí části, a pokud nejsou nastavená konkrétní pravidla brány firewall, přidání privátního koncového bodu zpřístupňuje účet Azure Cosmos jenom prostřednictvím privátních koncových bodů. To znamená, že účet Azure Cosmos je možné dosáhnout z veřejného provozu po jeho vytvoření a před přidáním privátního koncového bodu. Abyste se ujistili, že je přístup k veřejné síti zakázán dokonce i před vytvořením privátních koncových bodů, můžete nastavit `publicNetworkAccess` příznak `Disabled` při vytváření účtu. Všimněte si, že tento příznak má přednost před jakýmkoli pravidlem IP nebo virtuální sítě. veškerý provoz veřejné a virtuální sítě se zablokuje, pokud je příznak nastavený na `Disabled` , i když je v konfiguraci brány firewall povolená zdrojová IP adresa nebo virtuální síť.

V [této Azure Resource Manager šabloně](https://azure.microsoft.com/resources/templates/101-cosmosdb-private-endpoint/) najdete příklad ukazující použití tohoto příznaku.

## <a name="adding-private-endpoints-to-an-existing-cosmos-account-with-no-downtime"></a>Přidání privátních koncových bodů do existujícího účtu Cosmos bez výpadků

Při přidání privátního koncového bodu do stávajícího účtu je výsledkem krátké výpadky přibližně 5 minut. Pokud se chcete vyhnout tomuto výpadku, postupujte podle následujících pokynů:

1. Přidejte do konfigurace brány firewall pravidla IP nebo virtuální sítě, aby bylo možné explicitně dovolit připojení klientů.
1. Počkejte 10 minut, než se zajistěte, aby se aktualizace konfigurace nastavila.
1. Nakonfigurujte nový privátní koncový bod.
1. Odeberte pravidla brány firewall nastavená v kroku 1.

## <a name="port-range-when-using-direct-mode"></a>Rozsah portů při použití přímého režimu

Pokud používáte privátní propojení s účtem Azure Cosmos prostřednictvím připojení k přímému režimu, musíte zajistit, aby byl otevřený plný rozsah portů TCP (0-65535).

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Aktualizace privátního koncového bodu při přidání nebo odebrání oblasti

Pokud nepoužíváte privátní skupinu zón DNS, přidání nebo odebrání oblastí pro účet Azure Cosmos vyžaduje, abyste přidali nebo odebrali položky DNS pro tento účet. Po přidání nebo odebrání oblastí můžete aktualizovat privátní zónu DNS podsítě tak, aby odrážela přidané nebo odebrané položky DNS a odpovídající privátní IP adresy.

Představte si například, že nasazujete účet Azure Cosmos ve třech oblastech: "Západní USA," Střed USA "a" Západní Evropa ". Při vytváření privátního koncového bodu pro svůj účet jsou v podsíti vyhrazené čtyři privátní IP adresy. Pro každou ze tří oblastí existuje jedna IP adresa a pro koncový bod Global/region-nezávislá je k dispozici jedna IP adresa.

Později můžete do účtu Azure Cosmos přidat novou oblast (například "Východní USA"). Po přidání nové oblasti je potřeba přidat odpovídající záznam DNS do privátní zóny DNS nebo vlastního serveru DNS.

Stejný postup můžete použít při odebrání oblasti. Po odebrání této oblasti je potřeba odebrat odpovídající záznam DNS z privátní zóny DNS nebo vlastního serveru DNS.

## <a name="current-limitations"></a>Aktuální omezení

Pokud používáte privátní propojení s účtem Azure Cosmos, platí následující omezení:

* V jednom účtu Azure Cosmos nemůžete mít více než 200 privátních koncových bodů.

* Pokud používáte privátní propojení s účtem Azure Cosmos prostřednictvím připojení přímého režimu, můžete použít jenom protokol TCP. Protokol HTTP se v tuto chvíli nepodporuje.

* Pokud používáte rozhraní API Azure Cosmos DB pro účty MongoDB, pro účty na serveru verze 3,6 se podporuje privátní koncový bod (tj. účty používající koncový bod ve formátu `*.mongo.cosmos.azure.com` ). Pro účty na serveru verze 3,2 (tj. účty používající koncový bod ve formátu) se nepodporuje privátní propojení `*.documents.azure.com` . Pro použití privátního odkazu byste měli migrovat staré účty na novou verzi.

* Pokud používáte rozhraní API Azure Cosmos DB pro účet MongoDB, který má privátní propojení, nemusí některé nástroje nebo knihovny fungovat, protože automaticky vyřadí `appName` parametr z připojovacího řetězce. Tento parametr se vyžaduje pro připojení k účtu přes soukromý koncový bod. Některé nástroje, například Visual Studio Code, neodstraňují tento parametr z připojovacího řetězce a jsou proto kompatibilní.

* Správce sítě by měl mít alespoň `Microsoft.DocumentDB/databaseAccounts/PrivateEndpointConnectionsApproval/action` oprávnění v oboru účtu Azure Cosmos k vytváření automaticky schválených privátních koncových bodů.

### <a name="limitations-to-private-dns-zone-integration"></a>Omezení integrace privátní zóny DNS

Pokud nepoužíváte privátní skupinu zón DNS, nebudou se záznamy DNS v privátní zóně DNS při odstraňování privátního koncového bodu automaticky odebírat, nebo odeberete oblast z účtu Azure Cosmos. Záznamy DNS musíte ručně odebrat předtím, než:

* Přidávání nového privátního koncového bodu připojeného k této soukromé zóně DNS.
* Přidání nové oblasti do libovolného databázového účtu s privátními koncovými body propojenými s touto privátní zónou DNS.

Pokud neprovedete vyčištění záznamů DNS, může dojít k neočekávaným problémům roviny dat. Tyto problémy zahrnují výpadek dat do oblastí přidaných po odebrání privátního koncového bodu nebo odebrání oblasti.

## <a name="next-steps"></a>Další kroky

Další informace o funkcích Azure Cosmos DB zabezpečení najdete v následujících článcích:

* Pokud chcete nakonfigurovat bránu firewall pro Azure Cosmos DB, přečtěte si téma [Podpora brány firewall](firewall-support.md).

* Informace o tom, jak nakonfigurovat koncový bod služby virtuální sítě pro účet Azure Cosmos, najdete v tématu [Konfigurace přístupu z virtuálních sítí](how-to-configure-vnet-service-endpoint.md).

* Další informace o privátních odkazech najdete v dokumentaci k [privátním odkazům Azure](../private-link/private-link-overview.md) .
