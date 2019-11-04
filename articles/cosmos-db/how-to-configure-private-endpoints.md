---
title: Konfigurace privátního odkazu Azure pro účet Azure Cosmos
description: Přečtěte si, jak nastavit privátní odkaz Azure pro přístup k účtu Azure Cosmos pomocí privátní IP adresy ve virtuální síti.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 1eb769ec64e50be65d63be43d897c1190789e555
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518759"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Konfigurace privátního odkazu Azure pro účet Azure Cosmos (Preview)

Pomocí privátního odkazu Azure se můžete připojit k účtu Azure Cosmos prostřednictvím privátního koncového bodu. Soukromý koncový bod je sada privátních IP adres v podsíti v rámci vaší virtuální sítě. Pomocí privátního odkazu můžete omezit přístup k danému účtu Azure Cosmos prostřednictvím privátních IP adres. V kombinaci s omezenými zásadami NSG pomáhá soukromý odkaz snížit riziko dat exfiltrace. Další informace o privátních koncových bodech najdete v článku věnovaném [privátním odkazům Azure](../private-link/private-link-overview.md) .

Kromě toho privátní odkaz umožňuje přístup k účtu Azure Cosmos z virtuální sítě nebo libovolné partnerské virtuální sítě. Prostředky namapované na soukromé odkazy jsou dostupné taky z místních partnerských vztahů prostřednictvím sítě VPN nebo ExpressRoute. 

K účtu Azure Cosmos nakonfigurovanému pomocí privátního propojení se můžete připojit pomocí metod schvalování typu "automatické" nebo "ruční". Další informace najdete v části [schvalovací pracovní postup](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) v dokumentaci k privátnímu odkazu. Tento článek popisuje postup vytvoření privátního propojení za předpokladu, že používáte metodu automatického schvalování.

## <a name="create-a-private-link-using-the-azure-portal"></a>Vytvoření privátního odkazu pomocí Azure Portal

Pomocí následujícího postupu můžete vytvořit privátní odkaz pro existující účet Azure Cosmos pomocí Azure Portal:

1. V podokně **všechny prostředky** Najděte Azure Cosmos DB účet, který chcete zabezpečit.

1. V nabídce nastavení vyberte **připojení privátního koncového bodu** a vyberte možnost **privátní koncový bod** :

   ![Vytvoření privátního koncového bodu pomocí Azure Portal](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. V části **Vytvoření privátního koncového bodu (Preview) – základy**, podokno zadejte nebo vyberte následující podrobnosti:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte skupinu prostředků.|
    | **Podrobnosti instance** |  |
    | Name (Název) | Zadejte libovolný název vašeho privátního koncového bodu; Pokud se tento název povede, vytvořte jedinečný. |
    |Oblast| Vyberte oblast, do které chcete nasadit privátní propojení. Privátní koncový bod by měl být vytvořen ve stejném umístění, kde existuje vaše virtuální síť.|
    |||
1. Vyberte **Další: prostředek**.
1. V **Vytvoření privátního koncového bodu – prostředek**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | V adresáři vyberte připojit k prostředku Azure. <br/><br/> Tato možnost umožňuje zvolit jeden z vašich prostředků pro nastavení privátního propojení nebo připojení k prostředku někoho jiného s ID prostředku nebo aliasem, který s vámi sdílí.|
    | Předplatné| Vyberte své předplatné. |
    | Typ prostředku | Vyberte **Microsoft. AzureCosmosDB/databaseAccounts**. |
    | Prostředek |Vyberte svůj účet Azure Cosmos. |
    |Cílový dílčí prostředek |Vyberte Cosmos DB typ rozhraní API, který chcete namapovat. Tato možnost je standardně jenom jedna volba pro rozhraní API SQL, MongoDB a Cassandra. Pro Gremlin a rozhraní API tabulky můžete také zvolit *SQL* , protože tato rozhraní API se vzájemně spolupracují s rozhraním SQL API. |
    |||
1. Vyberte **Další: Konfigurace**.
1. V **Vytvoření privátního koncového bodu (Preview) – konfigurace**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**Networking**| |
    | Virtuální síť| Vyberte svou virtuální síť. |
    | Podsíť | Vyberte podsíť. |
    |**Integrace Privátní DNS**||
    |Integrace s privátní zónou DNS |Vyberte **Ano**. |
    |Zóna Privátní DNS |Vybrat *privatelink.Documents.Azure.com* |
    |||

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci.
1. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**.

Po schválení privátních odkazů pro účet Azure Cosmos je Azure Portal v podokně **Brána firewall a virtuální sítě** šedá možnost **všechny sítě** .

### <a name="fetch-the-private-ip-addresses"></a>Načtení privátních IP adres

Po zřízení privátního koncového bodu můžete zadat dotaz na IP adresy. Pro zobrazení IP adres z Azure Portal. Vyberte **všechny prostředky**, vyhledejte privátní koncový bod, který jste vytvořili dříve v tomto případě je to "dbPrivateEndpoint3" a vyberte kartu Přehled, abyste viděli nastavení DNS a IP adresy:

![Privátní IP adresy v Azure Portal](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Pro jeden privátní koncový bod se vytvoří víc IP adres:

* Jednu pro koncový bod globálního (region-nezávislá) účtu Azure Cosmos.
* Jednu pro každou oblast, ve které je nasazený účet Azure Cosmos.

## <a name="create-a-private-link-using-azure-powershell"></a>Vytvoření privátního odkazu pomocí Azure PowerShell

Spuštěním následujícího skriptu PowerSehll vytvořte privátní odkaz s názvem "MyPrivateEndpoint" pro existující účet Azure Cosmos. Nezapomeňte nahradit hodnoty proměnných podrobnostmi, které jsou specifické pro vaše prostředí.

```azurepowershell-interactive
Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Cosmos DB account: Sql or MongoDB or Cassandra or Gremlin or Table
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

### <a name="fetch-the-private-ip-addresses"></a>Načtení privátních IP adres

Po zřízení privátního koncového bodu můžete zadat dotaz na IP adresy a mapování plně kvalifikovaných názvů domény pomocí následujícího skriptu prostředí PowerShell:

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-link-using-a-resource-manager-template"></a>Vytvoření privátního odkazu pomocí šablony Správce prostředků

Soukromý odkaz můžete nastavit vytvořením privátního koncového bodu v podsíti virtuální sítě. Toho dosáhnete pomocí Azure Resource Manager šablony. Pomocí následujícího kódu vytvořte šablonu Správce prostředků s názvem "PrivateEndpoint_template. JSON". Tato šablona vytvoří privátní koncový bod pro existující účet rozhraní SQL API služby Azure Cosmos v existující virtuální síti:

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

### <a name="define-the-template-parameters-file"></a>Definice souboru parametrů šablony

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

Dále vytvořte PowerShellový skript s následujícím kódem. Před spuštěním skriptu se ujistěte, že jste nahradili ID předplatného, název skupiny prostředků a další hodnoty proměnných s podrobnostmi, které jsou specifické pro vaše prostředí:

```azurepowershell-interactive
### This script creates a private endpoint for an existing Cosmos DB account in an existing VNet

## Step 1: Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Cosmos DB account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

V PowerShellovém skriptu může proměnná "GroupId" obsahovat pouze jednu hodnotu, což je typ rozhraní API tohoto účtu. Povolené hodnoty jsou: SQL, MongoDB, Cassandra, Gremlin a Table. Některé typy účtů Azure Cosmos jsou přístupné prostřednictvím více rozhraní API. Například:

* K účtu rozhraní Gremlin API je možné přihlédnout z účtů Gremlin a SQL API.
* K účtu rozhraní API pro tabulky lze přihlédnout z tabulek i z účtů rozhraní SQL API.

Pro tyto účty musíte pro každý typ rozhraní API vytvořit jeden privátní koncový bod s odpovídajícím typem rozhraní API zadaným v poli GroupId.

Po úspěšném nasazení šablony uvidíte výstup podobný tomu, co se zobrazuje na následujícím obrázku. Hodnota provisioningState je "úspěšná", pokud jsou privátní koncové body nastaveny správně.

![Výstup nasazení Správce prostředků šablon](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Po nasazení šablony jsou privátní IP adresy rezervované v rámci podsítě. Pravidlo brány firewall účtu Azure Cosmos je nakonfigurované tak, aby přijímalo jenom připojení z privátního koncového bodu.

## <a name="configure-private-dns"></a>Konfigurace privátního serveru DNS

Během verze Preview privátního odkazu byste měli použít privátní DNS v podsíti, ve které byl vytvořen privátní koncový bod. A konfigurace koncových bodů tak, aby každá z privátních IP adres byla namapována na položku DNS (viz vlastnost "FQDN" v odpovědi zobrazené výše).

## <a name="firewall-configuration-with-private-link"></a>Konfigurace brány firewall s privátním odkazem

Následují různé situace a výsledky při použití privátního propojení v kombinaci s pravidly brány firewall:

* Pokud nejsou nakonfigurovaná žádná pravidla brány firewall, pak je účet Azure Cosmos ve výchozím nastavení přístupný pro veškerý provoz.

* Pokud je nakonfigurovaný veřejný provoz nebo koncový bod služby a vytvoří se privátní koncové body, pak odpovídající typ pravidla brány firewall autorizuje různé typy příchozích přenosů.

* Pokud není nakonfigurovaný žádný veřejný provoz nebo koncový bod služby a vytvoří se privátní koncové body, účet Azure Cosmos je přístupný jenom prostřednictvím privátních koncových bodů.

## <a name="update-private-endpoint-when-you-add-or-remove-a-region"></a>Aktualizace privátního koncového bodu při přidání nebo odebrání oblasti

Přidání nebo odebrání oblastí pro účet Azure Cosmos vyžaduje, abyste přidali nebo odebrali položky DNS pro tento účet. Tyto změny by se měly odpovídajícím způsobem aktualizovat v privátním koncovém bodě. V tuto chvíli byste měli tuto změnu provést ručně pomocí následujících kroků:

1. Azure Cosmos DB správce přidává nebo odebírá oblasti. Správci sítě pak budou upozorňováni na probíhající změny. Privátní koncový bod mapovaný na účet Azure Cosmos uvidí vlastnosti "ActionsRequired", které se změnily z možnosti "žádné" na "znovu vytvořit". Správce sítě pak aktualizuje privátní koncový bod tím, že vydá požadavek PUT se stejnou Správce prostředků datovou část použitou k jeho vytvoření.

1. Po provedení této operace je nutné aktualizovat privátní DNS podsítě také tak, aby odrážely přidané nebo odebrané položky DNS a odpovídající privátní IP adresy.

Například pokud nasadíte účet Azure Cosmos ve třech oblastech: "Západní USA", "Střed USA" a "Západní Evropa". Při vytváření privátního koncového bodu pro svůj účet jsou v podsíti vyhrazené 4 privátní IP adresy. Jednu pro každou oblast, která se počítá jako celkem 3 a jedna pro koncový bod Global/region-nezávislá.

Později, pokud do účtu Azure Cosmos přidáte novou oblast, například "Východní USA". Ve výchozím nastavení není nová oblast dostupná z existujícího privátního koncového bodu. Správce účtu Azure Cosmos by měl aktualizovat připojení privátního koncového bodu před tím, než bude formulář přistupovat k nové oblasti.

Při spuštění příkazu ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>` obsahuje výstup příkazu `ActionRequired` parametr, který je nastaven na hodnotu "znovu vytvořit". Tato hodnota označuje, že by se měl aktualizovat privátní koncový bod. Potom správce účtu Azure Cosmos spustí příkaz `Set-AzPrivateEndpoint`, který aktivuje aktualizaci privátního koncového bodu.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Nová privátní IP adresa je automaticky vyhrazena v podsíti v rámci tohoto privátního koncového bodu a hodnota `ActionRequired` bude `None`. Pokud nemáte žádnou privátní integraci zóny DNZ (jinými slovy, pokud používáte vlastní privátní DNS), musíte nakonfigurovat privátní DNS a přidat nový záznam DNS pro privátní IP adresu odpovídající nové oblasti.

Stejný postup můžete použít při odebrání oblasti. Privátní IP adresa odebrané oblasti se automaticky uvolní a příznak `ActionRequired` se `None`. Pokud nemáte žádnou privátní integraci zóny DNZ, musíte nakonfigurovat privátní DNS, aby se odebral záznam DNS pro odebraný region.

## <a name="current-limitations"></a>Aktuální omezení

Při použití privátního odkazu s účtem Azure Cosmos platí následující omezení:

* Při použití privátních odkazů s účtem Azure Cosmos pomocí přímého připojení v režimu můžete použít jenom protokol TCP. Protokol HTTP ještě není podporovaný.

* Pokud používáte rozhraní API Azure Cosmos DB pro účty MongoDB, pro účty na serveru verze 3,6 se podporuje privátní koncový bod (účty používající koncový bod ve formátu `*.mongo.cosmos.azure.com`). U účtů na serveru verze 3,2 se nepodporuje privátní propojení (účty používající koncový bod ve formátu `*.documents.azure.com`). Chcete-li použít privátní odkaz, je třeba migrovat staré účty do nové verze.

* Pokud používáte rozhraní API Azure Cosmos DB pro účty MongoDB, které mají privátní odkaz, nemůžete použít nástroje jako Robo 3T, Studio 3T, Mongoose atd. Koncový bod může obsahovat podporu privátních odkazů pouze v případě, že je zadán parametr appName =<account name>. Příklad: replicaSet = globaldb & appName = mydbaccountname. Vzhledem k tomu, že tyto nástroje nepředá název aplikace v připojovacím řetězci ke službě, takže nemůžete použít privátní odkaz. K těmto účtům ale budete mít přístup pomocí ovladačů SDK s 3,6 verzí.

* Podpora privátních odkazů pro účty Azure Cosmos a virtuální sítě je k dispozici pouze v konkrétních oblastech. Seznam podporovaných oblastí najdete v části [dostupné oblasti](../private-link/private-link-overview.md#availability) v článku věnovaném privátním odkazům.

* Virtuální síť nejde přesunout ani odstranit, pokud obsahuje privátní odkaz.

* Účet Azure Cosmos nejde odstranit, pokud je připojený k privátnímu koncovému bodu.

* U účtu Azure Cosmos nejde převzít služby při selhání do oblasti, která není namapovaná na všechny privátní koncové body, které jsou k ní připojené. Další informace najdete v tématu Přidání nebo odebrání oblastí v předchozí části.

* Správce sítě by měl udělit alespoň oprávnění "*/PrivateEndpointConnectionsApproval" v oboru účtu Azure Cosmos správcem k vytváření automaticky schválených privátních koncových bodů.

## <a name="next-steps"></a>Další kroky

Další informace o dalších funkcích Azure Cosmos DB zabezpečení najdete v následujícím článku:

* Pokud chcete nakonfigurovat bránu firewall pro Azure Cosmos DB, přečtěte si téma [Podpora brány firewall](firewall-support.md).

* [Jak nakonfigurovat koncový bod služby virtuální sítě pro účet Azure Cosmos.](how-to-configure-vnet-service-endpoint.md)

* Další informace o privátních odkazech najdete v dokumentaci k [privátním odkazům Azure](../private-link/private-link-overview.md) .
