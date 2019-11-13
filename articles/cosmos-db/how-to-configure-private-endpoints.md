---
title: Konfigurace privátního odkazu Azure pro účet Azure Cosmos
description: Přečtěte si, jak nastavit privátní odkaz Azure pro přístup k účtu Azure Cosmos pomocí privátní IP adresy ve virtuální síti.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 826fe1195a142bd0826d6311eab5eb208bbc7e35
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007423"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Konfigurace privátního odkazu Azure pro účet Azure Cosmos (Preview)

Pomocí privátního odkazu Azure se můžete připojit k účtu Azure Cosmos prostřednictvím privátního koncového bodu. Soukromý koncový bod je sada privátních IP adres v podsíti v rámci vaší virtuální sítě. Pak můžete omezit přístup k účtu Azure Cosmos prostřednictvím privátních IP adres. Pokud se soukromé propojení spojí s omezenými NSG zásadami, pomáhá to snížit riziko exfiltrace dat. Další informace o privátních koncových bodech najdete v článku věnovaném [privátním odkazům Azure](../private-link/private-link-overview.md) .

Privátní odkaz umožňuje uživatelům přístup k účtu Azure Cosmos z virtuální sítě nebo z kterékoli z partnerských virtuálních sítí. Prostředky namapované na soukromé odkazy jsou k dispozici i místně přes privátní partnerský vztah prostřednictvím sítě VPN nebo Azure ExpressRoute. 

K účtu Azure Cosmos nakonfigurovanému pomocí privátního propojení se můžete připojit pomocí metody automatického nebo ručního schválení. Další informace najdete v části [schvalovací pracovní postup](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) v dokumentaci k privátnímu odkazu. 

Tento článek popisuje postup vytvoření privátního koncového bodu. Předpokládá, že používáte metodu automatického schvalování.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Vytvoření privátního koncového bodu pomocí Azure Portal

Pomocí následujících kroků můžete vytvořit privátní koncový bod pro existující účet Azure Cosmos pomocí Azure Portal:

1. V podokně **všechny prostředky** vyberte účet Azure Cosmos.

1. V seznamu nastavení vyberte **připojení privátního koncového bodu** a pak vyberte **privátní koncový bod**:

   ![Výběry pro vytvoření privátního koncového bodu v Azure Portal](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. V podokně **základy vytvoření privátního koncového bodu (Preview)** zadejte nebo vyberte následující podrobnosti:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte skupinu prostředků.|
    | **Podrobnosti instance** |  |
    | Název | Zadejte libovolný název vašeho privátního koncového bodu. Pokud se tento název povede, vytvořte jedinečný. |
    |Region (Oblast)| Vyberte oblast, ve které chcete nasadit privátní propojení. Vytvořte privátní koncový bod ve stejném umístění, kde existuje vaše virtuální síť.|
    |||
1. Vyberte **Další: prostředek**.
1. V **Vytvoření privátního koncového bodu – prostředek**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | **V adresáři vyberte připojit k prostředku Azure**. <br/><br/> Pak můžete zvolit jeden z vašich prostředků pro nastavení privátního propojení. Nebo se můžete připojit k prostředku jiné osoby pomocí ID prostředku nebo aliasu, který s vámi sdílí.|
    | Předplatné| Vyberte své předplatné. |
    | Typ prostředku | Vyberte **Microsoft. AzureCosmosDB/databaseAccounts**. |
    | Prostředek |Vyberte svůj účet Azure Cosmos. |
    |Cílový dílčí prostředek |Vyberte Azure Cosmos DB typ rozhraní API, který chcete namapovat. Tato možnost je standardně jenom jedna volba pro rozhraní API SQL, MongoDB a Cassandra. Pro Gremlin a rozhraní Table API můžete také zvolit **SQL** , protože tato rozhraní API se vzájemně spolupracují s rozhraním SQL API. |
    |||

1. Vyberte **Další: Konfigurace**.
1. V **Vytvoření privátního koncového bodu (Preview) – konfigurace**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**Sítě**| |
    | Virtuální síť| Vyberte svou virtuální síť. |
    | Podsíť | Vyberte podsíť. |
    |**Integrace Privátní DNS**||
    |Integrace s privátní zónou DNS |Vyberte **Ano**. <br><br/> Abyste mohli soukromě propojit s vaším soukromým koncovým bodem, budete potřebovat záznam DNS. Doporučujeme integrovat privátní koncový bod s privátní zónou DNS. Můžete také použít vlastní servery DNS nebo vytvořit záznamy DNS pomocí hostitelských souborů na virtuálních počítačích. |
    |Zóna Privátní DNS |Vyberte **privatelink.Documents.Azure.com**. <br><br/> Privátní zóna DNS je určena automaticky. Nemůžete ho změnit pomocí Azure Portal.|
    |||

1. Vyberte **Zkontrolovat a vytvořit**. Na stránce **Revize + vytvořit** Azure ověří vaši konfiguraci.
1. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**.

Pokud jste pro účet Azure Cosmos schválili privátní odkaz, v podokně Azure Portal není dostupná možnost **všechny sítě** v podokně **Brána firewall a virtuální sítě** .

Následující tabulka ukazuje mapování mezi různými typy rozhraní API účtu Azure Cosmos, podporovaných dílčími prostředky a odpovídajícími názvy privátních zón. K účtům Gremlin a rozhraní API pro tabulky můžete přistupovat prostřednictvím rozhraní SQL API, takže existují dvě položky pro tato rozhraní API.

|Typ rozhraní API účtu Azure Cosmos  |Podporované dílčí prostředky (nebo ID skupin) |Název privátní zóny  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Table    |    Table     |   privatelink.table.cosmos.azure.com    |
|Table     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Načtení privátních IP adres

Po zřízení privátního koncového bodu můžete zadat dotaz na IP adresy. Postup zobrazení IP adres z Azure Portal:

1. Vyberte **Všechny prostředky**.
1. Vyhledejte privátní koncový bod, který jste vytvořili dříve. V tomto případě je to **cdbPrivateEndpoint3**.
1. Vyberte kartu **Přehled** a zobrazte tak nastavení DNS a IP adresy.

![Privátní IP adresy v Azure Portal](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

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

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Vytvoření privátního koncového bodu pomocí šablony Správce prostředků

Soukromý odkaz můžete nastavit vytvořením privátního koncového bodu v podsíti virtuální sítě. Dosáhnete toho pomocí šablony Azure Resource Manager. 

Pomocí následujícího kódu vytvořte šablonu Správce prostředků s názvem "PrivateEndpoint_template. JSON". Tato šablona vytvoří privátní koncový bod pro existující účet rozhraní SQL API služby Azure Cosmos v existující virtuální síti.

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

### <a name="define-the-parameters-file-for-the-template"></a>Definování souboru parametrů pro šablonu

Vytvořte soubor parametrů pro šablonu a pojmenujte ji "PrivateEndpoint_parameters. JSON". Do souboru Parameters přidejte následující kód:

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

### <a name="deploy-the-template-by-using-a-powershell-script"></a>Nasazení šablony pomocí skriptu PowerShellu

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

V PowerShellovém skriptu může proměnná `GroupId` obsahovat jenom jednu hodnotu. Tato hodnota je typ rozhraní API tohoto účtu. Povolené hodnoty jsou: `Sql`, `MongoDB`, `Cassandra`, `Gremlin`a `Table`. Některé typy účtů Azure Cosmos jsou přístupné prostřednictvím více rozhraní API. Příklad:

* K účtu rozhraní Gremlin API je možné přihlédnout z účtů Gremlin a SQL API.
* K účtu rozhraní API pro tabulky lze přihlédnout z tabulek i z účtů rozhraní SQL API.

Pro tyto účty musíte pro každý typ rozhraní API vytvořit jeden privátní koncový bod. Odpovídající typ rozhraní API je určený v poli `GroupId`.

Po úspěšném nasazení šablony se zobrazí výstup podobný následujícímu obrázku. Hodnota `provisioningState` je `Succeeded`, pokud jsou privátní koncové body nastaveny správně.

![Výstup nasazení pro šablonu Správce prostředků](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Po nasazení šablony jsou privátní IP adresy rezervované v rámci podsítě. Pravidlo brány firewall účtu Azure Cosmos je nakonfigurované tak, aby přijímalo jenom připojení z privátního koncového bodu.

## <a name="configure-custom-dns"></a>Konfigurace vlastního systému DNS

V podsíti, ve které jste vytvořili privátní koncový bod, byste měli použít privátní zónu DNS. Konfigurace koncových bodů tak, aby každá privátní IP adresa byla namapovaná na položku DNS. (Podívejte se na vlastnost `fqdns` v odpovědi uvedené dříve.)

Při vytváření privátního koncového bodu ho můžete integrovat s privátní zónou DNS v Azure. Pokud se rozhodnete místo toho použít vlastní zónu DNS, musíte ji nakonfigurovat tak, aby přidala záznamy DNS pro všechny privátní IP adresy rezervované pro soukromý koncový bod.

## <a name="private-link-combined-with-firewall-rules"></a>Privátní propojení v kombinaci s pravidly brány firewall

Při použití privátního odkazu v kombinaci s pravidly brány firewall jsou možné následující situace a výsledky:

* Pokud žádná pravidla brány firewall nenakonfigurujete, budou mít všechny přenosy ve výchozím nastavení přístup k účtu Azure Cosmos.

* Pokud nakonfigurujete veřejný provoz nebo koncový bod služby a vytvoříte privátní koncové body, budou se podle odpovídajícího typu pravidla brány firewall autorizovat různé typy příchozích přenosů.

* Pokud neprovedete konfiguraci žádného veřejného provozu nebo koncového bodu služby a vytvoříte privátní koncové body, účet Azure Cosmos je přístupný jenom prostřednictvím privátních koncových bodů. Pokud nenastavíte veřejný provoz nebo koncový bod služby, po odmítnutí nebo odstranění všech schválených privátních koncových bodů se účet otevře v celé síti.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Aktualizace privátního koncového bodu při přidání nebo odebrání oblasti

Přidání nebo odebrání oblastí pro účet Azure Cosmos vyžaduje, abyste přidali nebo odebrali položky DNS pro tento účet. Aktualizujte tyto změny odpovídajícím způsobem v privátním koncovém bodu pomocí následujících kroků:

1. Když správce Azure Cosmos DB přidá nebo odebere oblasti, správce sítě obdrží oznámení o probíhajících změnách. U privátního koncového bodu namapovaného na účet Azure Cosmos se hodnota vlastnosti `ActionsRequired` změní z `None` na `Recreate`. Správce sítě pak aktualizuje privátní koncový bod tím, že vydá požadavek PUT se stejnou Správce prostředků datovou část, která se použila k jeho vytvoření.

1. Po aktualizaci privátního koncového bodu můžete aktualizovat privátní zónu DNS podsítě tak, aby odrážela přidané nebo odebrané položky DNS a odpovídající privátní IP adresy.

Představte si například, že nasazujete účet Azure Cosmos ve třech oblastech: "Západní USA," Střed USA "a" Západní Evropa ". Při vytváření privátního koncového bodu pro svůj účet jsou v podsíti vyhrazené čtyři privátní IP adresy. Pro každou ze tří oblastí existuje jedna IP adresa a pro koncový bod Global/region-nezávislá je k dispozici jedna IP adresa.

Později můžete do účtu Azure Cosmos přidat novou oblast (například "Východní USA"). Ve výchozím nastavení není nová oblast dostupná z existujícího privátního koncového bodu. Správce účtu Azure Cosmos by měl aktualizovat připojení privátního koncového bodu předtím, než k němu přistupuje z nové oblasti. 

Při spuštění příkazu ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>` obsahuje výstup příkazu `actionsRequired` parametr. Tento parametr je nastaven na `Recreate`. Tato hodnota označuje, že by se měl aktualizovat privátní koncový bod. V dalším kroku správce účtu Azure Cosmos spustí příkaz `Set-AzPrivateEndpoint`, který aktivuje aktualizaci privátního koncového bodu.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

V rámci tohoto privátního koncového bodu je automaticky rezervována nová privátní IP adresa v podsíti. Hodnota pro `actionsRequired` se bude `None`. Pokud nemáte žádnou privátní integraci zóny DNZ (jinými slovy, pokud používáte vlastní privátní zónu DNS), musíte nakonfigurovat privátní zónu DNS a přidat nový záznam DNS pro privátní IP adresu, která odpovídá nové oblasti.

Stejný postup můžete použít při odebrání oblasti. Privátní IP adresa odebrané oblasti se automaticky uvolní a příznak `actionsRequired` se `None`. Pokud nemáte žádnou privátní integraci zóny DNZ, musíte nakonfigurovat privátní zónu DNS, aby se odebral záznam DNS pro odebraný region.

Záznamy DNS v privátní zóně DNS se neodstraňují automaticky při odstranění privátního koncového bodu nebo odebrání oblasti z účtu Azure Cosmos. Záznamy DNS je nutné odebrat ručně.

## <a name="current-limitations"></a>Aktuální omezení

Pokud používáte privátní propojení s účtem Azure Cosmos, platí následující omezení:

* Podpora privátních odkazů pro účty Azure Cosmos a virtuální sítě je dostupná jenom v konkrétních oblastech. Seznam podporovaných oblastí najdete v části [dostupné oblasti](../private-link/private-link-overview.md#availability) v článku věnovaném privátním odkazům. 

  > [!NOTE]
  > Pokud chcete vytvořit privátní koncový bod, ujistěte se, že virtuální síť i účet Azure Cosmos jsou v podporovaných oblastech.

* Pokud používáte privátní propojení s účtem Azure Cosmos pomocí připojení přímého režimu, můžete použít jenom protokol TCP. Protokol HTTP ještě není podporovaný.

* Pokud používáte Azure Cosmos DB API pro účty MongoDB, pro účty na serveru verze 3,6 se podporuje privátní koncový bod (účty používající koncový bod ve formátu `*.mongo.cosmos.azure.com`). U účtů na serveru verze 3,2 se nepodporuje privátní propojení (to znamená, že účty používající koncový bod ve formátu `*.documents.azure.com`). Pro použití privátního odkazu byste měli migrovat staré účty na novou verzi.

* Pokud používáte rozhraní API Azure Cosmos DB pro účty MongoDB, které mají privátní odkaz, nemůžete používat nástroje jako Robo 3T, Studio 3T a Mongoose. Koncový bod může obsahovat podporu privátních odkazů pouze v případě, že je zadán parametr `appName=<account name>`. Příklad: `replicaSet=globaldb&appName=mydbaccountname`. Vzhledem k tomu, že tyto nástroje nepředá název aplikace v připojovacím řetězci ke službě, nemůžete použít privátní odkaz. K těmto účtům ale můžete i nadále přistupovat pomocí ovladačů SDK s verzí 3,6.

* Virtuální síť se nedá přesunout ani odstranit, pokud obsahuje privátní odkaz.

* Účet Azure Cosmos nemůžete odstranit, pokud je připojený k privátnímu koncovému bodu.

* Nemůžete převzít služby při selhání účtu Azure Cosmos do oblasti, která není namapovaná na všechny privátní koncové body připojené k účtu.

* Správce sítě by měl mít alespoň oprávnění "*/PrivateEndpointConnectionsApproval" v oboru účtu Azure Cosmos pro vytváření automaticky schválených privátních koncových bodů.

### <a name="limitations-to-private-dns-zone-integration"></a>Omezení integrace privátní zóny DNS

Záznamy DNS v privátní zóně DNS se při odstranění privátního koncového bodu neodstraní automaticky, nebo odeberete oblast z účtu Azure Cosmos. Záznamy DNS musíte ručně odebrat předtím, než:

* Přidávání nového privátního koncového bodu připojeného k této soukromé zóně DNS.
* Přidání nové oblasti do libovolného databázového účtu s privátními koncovými body propojenými s touto privátní zónou DNS.

Pokud neprovedete vyčištění záznamů DNS, může dojít k neočekávaným problémům roviny dat. Tyto problémy zahrnují výpadek dat do oblastí přidaných po odebrání privátního koncového bodu nebo odebrání oblasti.

## <a name="next-steps"></a>Další kroky

Další informace o funkcích Azure Cosmos DB zabezpečení najdete v následujících článcích:

* Pokud chcete nakonfigurovat bránu firewall pro Azure Cosmos DB, přečtěte si téma [Podpora brány firewall](firewall-support.md).

* Informace o tom, jak nakonfigurovat koncový bod služby virtuální sítě pro účet Azure Cosmos, najdete v tématu [Konfigurace přístupu z virtuálních sítí](how-to-configure-vnet-service-endpoint.md).

* Další informace o privátních odkazech najdete v dokumentaci k [privátním odkazům Azure](../private-link/private-link-overview.md) .
