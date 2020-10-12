---
title: Konfigurace zásad koncového bodu služby – Azure HDInsight
description: Naučte se konfigurovat zásady koncového bodu služby pro virtuální síť pomocí Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: f2752e5ab2bf7c2926ec9e2c0e4929eab91ed377
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88530966"
---
# <a name="configure-virtual-network-service-endpoint-policies-for-azure-hdinsight"></a>Konfigurace zásad koncového bodu služby virtuální sítě pro Azure HDInsight

Tento článek poskytuje informace o tom, jak implementovat zásady koncového bodu služby ve virtuálních sítích pomocí Azure HDInsight.

## <a name="background"></a>Pozadí

Azure HDInsight umožňuje vytvářet clustery ve vlastní virtuální síti. Pokud potřebujete povolený odchozí provoz z vaší virtuální sítě do jiných služeb Azure, jako jsou účty úložiště, můžete vytvořit [zásady koncového bodu služby](../virtual-network/virtual-network-service-endpoint-policies-overview.md). Zásady koncového bodu služby, které jsou vytvořené prostřednictvím Azure Portal, vám ale umožňují vytvořit zásadu jenom pro jeden účet, všechny účty v rámci předplatného nebo všechny účty ve skupině prostředků.

Jako spravovaná služba ale Azure HDInsight shromažďuje data a soubory protokolů z každého clusteru v jednotlivých oblastech. Aby tato data mohla být dostupná ve službě HDInsight z vaší virtuální sítě, je potřeba vytvořit zásady koncového bodu služby, které umožňují odchozí přenosy na konkrétní body shromažďování dat spravované službou Azure HDInsight.

## <a name="service-endpoint-policies-for-hdinsight"></a>Zásady koncového bodu služby pro HDInsight

Tyto zásady koncového bodu služby podporují následující funkce:

- Shromažďování protokolů a telemetrie při vytváření clusteru, provádění úloh a operace platforem, jako je například škálování.
- Připojení virtuálních pevných disků (VHD) k nově vytvořeným uzlům clusteru pro zřizování softwaru a knihoven v clusteru.

Pokud se nevytvoří zásady koncového bodu služby, které by umožňovaly tento tok dat, může dojít k selhání vytvoření clusteru a Azure HDInsight nebude moci poskytnout podporu pro vaše clustery.

## <a name="create-service-endpoint-policies-for-hdinsight"></a>Vytvoření zásad koncového bodu služby pro HDInsight

Před vytvořením nových clusterů se ujistěte, že jsou k virtuální síti připojené správné zásady koncového bodu služby. V opačném případě se může stát, že vytvoření clusteru selže nebo dojde k chybě.

K vytvoření potřebných zásad koncového bodu služby použijte následující postup:

1. Určete oblast, ve které budete cluster HDInsight vytvářet.
1. Vyhledá tuto oblast v [seznamu prostředků zásad koncového bodu služby](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json), který poskytuje všechny skupiny prostředků pro účty úložiště správy HDInsight.
1. Vyberte seznam skupin prostředků pro vaši oblast. Příklad prostředků pro `Canada Central` najdete tady:

    ```json
    "Canada Central":[
        "/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
        "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG"
    ],
    ```

1. Vložte tento seznam skupin prostředků do skriptu pro instalaci napsaného v Azure CLI nebo Azure PowerShell.

    ```azurecli
    $subscriptionId = "<subscription id>"
    $rgName="<resource group name> "
    $location="<location name>"
    $vnetName="<vnet name>"
    $subnetName="<subnet name>"
    $sepName="<service endpoint policy name>"
    $sepDefName="<service endpoint policy definition name>"
    
    # Set to the right subscription ID
    az account set --subscription $subscriptionId
    
    # setup service endpoint on the virtual network subnet
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoints Microsoft.Storage
    
    # Create Service Endpoint Policy
    az network service-endpoint policy create -g $rgName  -n $sepName -l $location
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",`
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Assign service resources to the SEP policy.
    az network service-endpoint policy-definition create -g $rgName --policy-name $sepName -n $sepDefName --service "Microsoft.Storage" --service-resources $resources
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoint-policy $sepName
    ```

    Pokud dáváte přednost nastavení zásad koncového bodu služby pomocí prostředí PowerShell, použijte následující fragment kódu.
    
    ```powershell
    #Script to assign SEP 
    $subscriptionId = "<subscription id>"
    $rgName = "<resource group name>"
    $vnetName = "<vnet name>"
    $subnetName = "<subnet Name"
    $location = "Canada Central"
    
    # Connect to your Azure Account
    Connect-AzAccount
    
    # Select the Subscription that you want to use
    Select-AzSubscription -SubscriptionId $subscriptionId
    
    # Retrieve VNet Config
    $vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
    
    # Retrieve Subnet Config
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Declare service endpoint policy definition
    $sepDef = New-AzServiceEndpointPolicyDefinition -Name "SEPHDICanadaCentral" -Description "Service Endpoint Policy Definition" -Service "Microsoft.Storage" -ServiceResource $resources
    
    # Service Endpoint Policy
    $sep= New-AzServiceEndpointPolicy -ResourceGroupName $rgName -Name "SEPHDICanadaCentral" -Location $location -ServiceEndpointPolicyDefinition $sepDef
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    Set-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet -AddressPrefix $subnet.AddressPrefix -ServiceEndpointPolicy $sep
    ```

## <a name="next-steps"></a>Další kroky

* [Architektura virtuální sítě Azure HDInsight](hdinsight-virtual-network-architecture.md)
* [Konfigurace síťového virtuálního zařízení](./network-virtual-appliance.md)
