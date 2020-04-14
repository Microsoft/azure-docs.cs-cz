---
title: Konfigurace privátního spojení Azure pro účet Azure Cosmos
description: Zjistěte, jak nastavit Azure Private Link pro přístup k účtu Azure Cosmos pomocí privátní IP adresy ve virtuální síti.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: thweiss
ms.openlocfilehash: 4b49d2aa61587d0156755bdd5c47b3eeb90090a5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270685"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Konfigurace privátního spojení Azure pro účet Azure Cosmos

Pomocí Azure Private Link se můžete připojit k účtu Azure Cosmos prostřednictvím privátního koncového bodu. Soukromý koncový bod je sada privátních IP adres v podsíti ve vaší virtuální síti. Pak můžete omezit přístup k účtu Azure Cosmos přes privátní IP adresy. Pokud je private link kombinován s omezenými zásadami nsg, pomáhá snížit riziko exfiltrace dat. Další informace o privátní koncové body, najdete v článku [Azure Private Link.](../private-link/private-link-overview.md)

Privátní odkaz umožňuje uživatelům přístup k účtu Azure Cosmos z v rámci virtuální sítě nebo z jakékoli partnerské virtuální sítě. Prostředky namapované na privátní propojení jsou také přístupné místně prostřednictvím privátního partnerského vztahu prostřednictvím sítě VPN nebo Azure ExpressRoute. 

K účtu Azure Cosmos nakonfigurovanému pomocí private link se můžete připojit pomocí metody automatického nebo ručního schvalování. Další informace naleznete v části [Schválení pracovního postupu](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) v dokumentaci k soukromému odkazu. 

Tento článek popisuje kroky k vytvoření privátní koncový bod. Předpokládá, že používáte metodu automatického schvalování.

> [!NOTE]
> Podpora privátního koncového bodu je aktuálně obecně dostupná pouze pro režim připojení brány. V přímém režimu je k dispozici jako funkce náhledu.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Vytvoření privátního koncového bodu pomocí portálu Azure

Pomocí následujících kroků vytvořte privátní koncový bod pro existující účet Azure Cosmos pomocí portálu Azure:

1. V podokně **Všechny prostředky** zvolte účet Azure Cosmos.

1. Ze seznamu nastavení vyberte **Soukromá připojení koncového bodu** a pak vyberte **Soukromý koncový bod**:

   ![Výběry pro vytvoření privátního koncového bodu na webu Azure Portal](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. V **podokně Vytvořit soukromý koncový bod – základy** zadejte nebo vyberte následující podrobnosti:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte skupinu prostředků.|
    | **Podrobnosti instance** |  |
    | Name (Název) | Zadejte libovolný název privátního koncového bodu. Pokud je tento název přijat, vytvořte jedinečný název. |
    |Region (Oblast)| Vyberte oblast, do které chcete nasadit privátní spojení. Vytvořte soukromý koncový bod ve stejném umístění, kde existuje vaše virtuální síť.|
    |||
1. Vyberte **další: Zdroj**.
1. V **části Vytvořit soukromý koncový bod – prostředek**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | V **yberte Připojit k prostředku Azure v mém adresáři**. <br/><br/> Potom můžete zvolit jeden z vašich prostředků pro nastavení private link. Nebo se můžete připojit k prostředku někoho jiného pomocí ID prostředku nebo aliasu, který s vámi sdílí.|
    | Předplatné| Vyberte své předplatné. |
    | Typ prostředku | Vyberte **Microsoft.AzureCosmosDB/databaseAccounts**. |
    | Prostředek |Vyberte svůj účet Azure Cosmos. |
    |Cílový dílčí zdroj |Vyberte typ rozhraní API Db Azure Cosmos, který chcete mapovat. To to ve výchozím nastavení pouze jednu volbu pro SQL, MongoDB a Cassandra rozhraní API. Pro rozhraní API Gremlin a tabulka můžete také zvolit **sql,** protože tato rozhraní API jsou interoperabilní s rozhraním SQL API. |
    |||

1. Vyberte **další: Konfigurace**.
1. V **části Vytvořit soukromý koncový bod – konfigurace**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**Sítě**| |
    | Virtuální síť| Vyberte virtuální síť. |
    | Podsíť | Vyberte podsíť. |
    |**Privátní integrace DNS**||
    |Integrace se soukromou zónou DNS |Vyberte **ano**. <br><br/> Chcete-li se soukromě připojit k privátnímu koncovému bodu, potřebujete záznam DNS. Doporučujeme integrovat soukromý koncový bod se soukromou zónou DNS. Můžete také použít vlastní servery DNS nebo vytvořit záznamy DNS pomocí hostitelských souborů ve virtuálních počítačích. |
    |Soukromá zóna DNS |Vyberte **privatelink.documents.azure.com**. <br><br/> Soukromá zóna DNS je určena automaticky. Nemůžete změnit pomocí portálu Azure.|
    |||

1. Vyberte **Zkontrolovat a vytvořit**. Na stránce **Revize + vytvořit** Azure ověří vaši konfiguraci.
1. Když se zobrazí zpráva **Ověření předáno,** vyberte **vytvořit**.

Když jste schválili privátní spojení pro účet Azure Cosmos, na webu Azure Portal není možnost **Všechny sítě** v podokně **Brána firewall a virtuální sítě** k dispozici.

V následující tabulce je uvedeno mapování mezi různými typy rozhraní API účtu Azure Cosmos, podporovanými dílčími prostředky a odpovídajícími názvy privátní zóny. Můžete také přistupovat k účtům Gremlin a Table API prostřednictvím rozhraní SQL API, takže existují dvě položky pro tato rozhraní API.

|Typ rozhraní API účtu Azure Cosmos  |Podporované podzdroje (id skupiny) |Název soukromé zóny  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Table    |    Table     |   privatelink.table.cosmos.azure.com    |
|Table     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Načtení privátních IP adres

Po zřízení privátníkoncový bod, můžete dotaz IP adresy. Zobrazení IP adres z webu Azure Portal:

1. Vyberte **Všechny prostředky**.
1. Vyhledejte soukromý koncový bod, který jste vytvořili dříve. V tomto případě je **cdbPrivateEndpoint3**.
1. Výběrem karty **Přehled** zobrazíte nastavení DNS a adresy IP.

![Privátní IP adresy na webu Azure Portal](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Pro jeden soukromý koncový bod se vytvoří více ADRES IP:

* Jeden pro globální (oblast-agnostik) koncový bod účtu Azure Cosmos
* Jeden pro každou oblast, kde se nasazuje účet Azure Cosmos

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Vytvoření privátního koncového bodu pomocí Azure PowerShellu

Spusťte následující skript Prostředí PowerShell a vytvořte privátní koncový bod s názvem "MyPrivateEndpoint" pro existující účet Azure Cosmos. Nahraďte hodnoty proměnných podrobnostmi pro vaše prostředí.

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

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrace privátního koncového bodu se soukromou zónou DNS

Po vytvoření privátního koncového bodu jej můžete integrovat se soukromou zónou DNS pomocí následujícího skriptu prostředí PowerShell:

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

Po zřízení privátního koncového bodu můžete zadat dotaz na adresy IP a mapování vícenežvitných jmen pomocí následujícího skriptu prostředí PowerShell:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Vytvoření privátního koncového bodu pomocí azure cli

Spusťte následující skript Azure CLI a vytvořte privátní koncový bod s názvem "myPrivateEndpoint" pro existující účet Azure Cosmos. Nahraďte hodnoty proměnných podrobnostmi pro vaše prostředí.

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

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrace privátního koncového bodu se soukromou zónou DNS

Po vytvoření privátního koncového bodu ho můžete integrovat se soukromou zónou DNS pomocí následujícího skriptu Azure CLI:

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

Privátní propojení můžete nastavit vytvořením privátního koncového bodu v podsíti virtuální sítě. Toho dosáhnete pomocí šablony Azure Resource Manager.

Následující kód slouží k vytvoření šablony Správce prostředků s názvem "PrivateEndpoint_template.json". Tato šablona vytvoří privátní koncový bod pro existující účet rozhraní SQL API Azure Cosmos v existující virtuální síti.

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

Vytvořte soubor parametrů pro šablonu a pojmenujte jej "PrivateEndpoint_parameters.json." Do souboru parametrů přidejte následující kód:

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

Vytvořte skript prostředí PowerShell pomocí následujícího kódu. Před spuštěním skriptu nahraďte ID předplatného, název skupiny prostředků a další hodnoty proměnných podrobnostmi pro vaše prostředí.

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

Ve skriptu Prostředí `GroupId` PowerShell může proměnná obsahovat pouze jednu hodnotu. Tato hodnota je typ rozhraní API účtu. Povolené hodnoty `Sql`jsou: `Cassandra` `Gremlin`, `MongoDB` `Table`, , a . Některé typy účtů Azure Cosmos jsou přístupné prostřednictvím více api. Příklad:

* Účet rozhraní GREMLIN API je přístupný z účtů Gremlin i SQL API.
* Účet rozhraní TABLE API lze přistupovat z účtů Table i SQL API.

Pro tyto účty je nutné vytvořit jeden soukromý koncový bod pro každý typ rozhraní API. Odpovídající typ rozhraní API `GroupId` je zadán v poli.

Po úspěšném nasazení šablony můžete zobrazit výstup podobný tomu, co ukazuje následující obrázek. Hodnota `provisioningState` je, `Succeeded` pokud jsou soukromé koncové body nastaveny správně.

![Výstup nasazení pro šablonu Správce prostředků](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Po nasazení šablony jsou privátní IP adresy rezervovány v rámci podsítě. Pravidlo brány firewall účtu Azure Cosmos je nakonfigurované tak, aby přijímalo připojení pouze z privátního koncového bodu.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrace privátního koncového bodu se soukromou zónou DNS

Následující kód slouží k vytvoření šablony Správce prostředků s názvem "PrivateZone_template.json". Tato šablona vytvoří privátní zónu DNS pro existující účet rozhraní SQL API Azure Cosmos v existující virtuální síti.

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

Následující kód slouží k vytvoření šablony Správce prostředků s názvem "PrivateZoneRecords_template.json".

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

Vytvořte následující soubor dvou parametrů pro šablonu. Vytvořte "PrivateZone_parameters.json." s následujícím kódem:

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

Vytvořte "PrivateZoneRecords_parameters.json." s následujícím kódem:

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

Vytvořte skript prostředí PowerShell pomocí následujícího kódu. Před spuštěním skriptu nahraďte ID předplatného, název skupiny prostředků a další hodnoty proměnných podrobnostmi pro vaše prostředí.

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

Měli byste použít soukromou zónu DNS v podsíti, kde jste vytvořili soukromý koncový bod. Nakonfigurujte koncové body tak, aby každá privátní ADRESA IP byla mapována na položku DNS. (Viz `fqdns` vlastnost v odpovědi uvedené výše.)

Když vytváříte soukromý koncový bod, můžete ho integrovat s privátní zónou DNS v Azure. Pokud se rozhodnete místo toho použít vlastní zónu DNS, musíte ji nakonfigurovat tak, aby přidala záznamy DNS pro všechny soukromé IP adresy vyhrazené pro soukromý koncový bod.

## <a name="private-link-combined-with-firewall-rules"></a>Soukromé propojení v kombinaci s pravidly brány firewall

Následující situace a výsledky jsou možné při použití private link v kombinaci s pravidly brány firewall:

* Pokud nenakonfigurujete žádná pravidla brány firewall, pak ve výchozím nastavení veškerý provoz přístup k účtu Azure Cosmos.

* Pokud nakonfigurujete veřejný provoz nebo koncový bod služby a vytvoříte soukromé koncové body, pak jsou různé typy příchozích přenosů autorizovány odpovídajícím typem pravidla brány firewall.

* Pokud nenakonfigurujete žádný veřejný provoz nebo koncový bod služby a vytvoříte privátní koncové body, pak je účet Azure Cosmos přístupný jenom prostřednictvím privátních koncových bodů. Pokud nenakonfigurujete veřejný provoz nebo koncový bod služby, po odmítnutí nebo odstranění všech schválených soukromých koncových bodů je účet otevřen pro celou síť.

## <a name="blocking-public-network-access-during-account-creation"></a>Blokování přístupu k veřejné síti během vytváření účtu

Jak je popsáno v předchozí části a pokud konkrétní pravidla brány firewall byly nastaveny, přidání privátní koncový bod zpřístupní váš účet Azure Cosmos přístupné prostřednictvím privátních koncových bodů pouze. To znamená, že účet Azure Cosmos lze dosáhnout z veřejného provozu po jeho vytvoření a před přidáním privátní koncový bod. Chcete-li se ujistit, že přístup k veřejné síti je `publicNetworkAccess` zakázán `Disabled` ještě před vytvořením soukromých koncových bodů, můžete nastavit příznak během vytváření účtu. Podívejte se na [tuto šablonu Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-cosmosdb-private-endpoint/) příklad ukazuje, jak používat tento příznak.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Aktualizace soukromého koncového bodu při přidání nebo odebrání oblasti

Přidání nebo odebrání oblastí do účtu Azure Cosmos vyžaduje přidání nebo odebrání položek DNS pro tento účet. Po přidání nebo odebrání oblastí můžete aktualizovat privátní zónu DNS podsítě tak, aby odrážela přidané nebo odebrané položky DNS a jejich odpovídající privátní IP adresy.

Představte si například, že nasadíte účet Azure Cosmos ve třech oblastech: "Západní USA", "Střední USA" a "Západní Evropa". Při vytvoření soukromého koncového bodu pro váš účet jsou v podsíti rezervovány čtyři soukromé IP adresy. Pro každou ze tří oblastí je jedna IP adresa a jedna IP adresa pro koncový bod globální/nezávislá na oblasti.

Později můžete přidat novou oblast (například "Východní USA") do účtu Azure Cosmos. Po přidání nové oblasti je třeba přidat odpovídající záznam DNS do privátní zóny DNS nebo do vlastní služby DNS.

Stejné kroky můžete použít při odebrání oblasti. Po odebrání oblasti je třeba odebrat odpovídající záznam DNS ze soukromé zóny DNS nebo z vlastní služby DNS.

## <a name="current-limitations"></a>Aktuální omezení

Následující omezení platí, když používáte Private Link s účtem Azure Cosmos:

* Pokud používáte private link s účtem Azure Cosmos pomocí přímého režimu připojení, můžete použít pouze protokol TCP. Protokol HTTP ještě není podporován.

* Podpora privátního koncového bodu je aktuálně obecně dostupná pouze pro režim připojení brány. V přímém režimu je k dispozici jako funkce náhledu.

* Když používáte rozhraní API Azure Cosmos DB pro účty MongoDB, soukromý koncový bod je podporován pro účty pouze na serveru `*.mongo.cosmos.azure.com`verze 3.6 (to znamená účty pomocí koncového bodu ve formátu). Private Link není podporován pro účty na serveru verze 3.2 (to `*.documents.azure.com`znamená účty pomocí koncového bodu ve formátu ). Chcete-li použít private link, měli byste migrovat staré účty na novou verzi.

* Když používáte rozhraní API Azure Cosmos DB pro účty MongoDB, které mají private link, nemůžete používat nástroje, jako je Robo 3T, Studio 3T a Mongoose. Koncový bod může mít podporu private `appName=<account name>` link pouze v případě, že je zadán parametr. Příklad: `replicaSet=globaldb&appName=mydbaccountname`. Vzhledem k tomu, že tyto nástroje nepředají název aplikace v připojovacím řetězci službě, nelze použít private link. Ale stále můžete přistupovat k těmto účtům pomocí ovladačů sady SDK s verzí 3.6.

* Virtuální síť nelze přesunout ani odstranit, pokud obsahuje privátní propojení.

* Účet Azure Cosmos nelze odstranit, pokud je připojený k privátnímu koncovému bodu.

* Účet Azure Cosmos nelze převést na služby při selhání do oblasti, která není namapována na všechny soukromé koncové body připojené k účtu.

* Správce sítě by měla být udělena alespoň "*/PrivateEndpointConnectionsApproval" oprávnění v oboru účtu Azure Cosmos k vytvoření automaticky schválené soukromé koncové body.

### <a name="limitations-to-private-dns-zone-integration"></a>Omezení integrace privátní zóny DNS

Záznamy DNS v privátní zóně DNS se neodeberou automaticky, když odstraníte soukromý koncový bod nebo odeberete oblast z účtu Azure Cosmos. Záznamy DNS je nutné ručně odebrat dříve, než:

* Přidání nového soukromého koncového bodu propojeného s touto soukromou zónou DNS.
* Přidání nové oblasti do libovolného databázového účtu, který má privátní koncové body propojené s touto privátní zónou DNS.

Pokud záznamy DNS neuklidíte, může dojít k neočekávaným problémům s rovinou dat. Mezi tyto problémy patří výpadek dat do oblastí přidané po odebrání soukromého koncového bodu nebo odebrání oblasti.

## <a name="next-steps"></a>Další kroky

Další informace o funkcích zabezpečení Azure Cosmos DB najdete v následujících článcích:

* Pokud chcete nakonfigurovat bránu firewall pro Azure Cosmos DB, přečtěte si článek [Podpora brány firewall](firewall-support.md).

* Informace o konfiguraci koncového bodu virtuální síťové služby pro váš účet Azure Cosmos najdete v [tématu Konfigurace přístupu z virtuálních sítí](how-to-configure-vnet-service-endpoint.md).

* Další informace o privátní link najdete v dokumentaci [k Privátní odkaz Azure.](../private-link/private-link-overview.md)
