---
title: Konfigurace koncových bodů Synchronizace souborů Azure sítě | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat Synchronizace souborů Azure koncové body sítě.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 5/11/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 64d66e1b9eab225b38ee21306fea6f9534a708f3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673840"
---
# <a name="configuring-azure-file-sync-network-endpoints"></a>Konfigurace koncových bodů sítě pro Synchronizaci souborů Azure
Soubory Azure a Synchronizace souborů Azure poskytují dva hlavní typy koncových bodů pro přístup ke sdíleným složkám Azure: 
- Veřejné koncové body, které mají veřejnou IP adresu a jsou přístupné odkudkoli na světě.
- Privátní koncové body, které existují v rámci virtuální sítě a které mají privátní IP adresu v adresním prostoru virtuální sítě.

Pro soubory a Synchronizace souborů Azure Azure, účet úložiště Azure, účet úložiště a službu synchronizace úložiště, řídí veřejné i privátní koncové body. Účet úložiště je konstrukce správy, která představuje sdílený fond úložiště, ve kterém můžete nasadit více sdílených složek a další prostředky úložiště, jako jsou například kontejnery nebo fronty objektů BLOB. Služba synchronizace úložiště je konstrukce správy, která představuje registrované servery, což jsou souborové servery Windows s vytvořeným vztahem důvěryhodnosti s Synchronizace souborů Azure a skupiny synchronizace, které definují topologii relace synchronizace. 

Tento článek se zaměřuje na konfiguraci koncových bodů sítě pro soubory Azure a Synchronizace souborů Azure. Další informace o tom, jak nakonfigurovat koncové body sítě pro přímý přístup ke sdíleným složkám Azure, místo ukládání do mezipaměti v místním prostředí pomocí Synchronizace souborů Azure najdete v tématu [Konfigurace koncových bodů sítě Azure Files](storage-files-networking-endpoints.md).

Před načtením tohoto průvodce doporučujeme přečíst si [informace o synchronizace souborů Azurech sítích](storage-sync-files-networking-overview.md) .

## <a name="prerequisites"></a>Požadavky 
V tomto článku se předpokládá, že:
- Máte předplatné Azure. Pokud ještě nemáte předplatné, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- Sdílenou složku Azure jste už vytvořili v účtu úložiště, ke kterému se chcete připojit z místního prostředí. Informace o tom, jak vytvořit sdílenou složku Azure, najdete v tématu [Vytvoření sdílené složky Azure](storage-how-to-create-file-share.md).
- Už jste vytvořili službu synchronizace úložiště a zaregistrovali jste souborový server s Windows. Další informace o nasazení Synchronizace souborů Azure najdete v tématu [nasazení synchronizace souborů Azure](storage-sync-files-deployment-guide.md).

Dále:
- Pokud máte v úmyslu použít Azure PowerShell, [nainstalujte nejnovější verzi](/powershell/azure/install-az-ps).
- Pokud máte v úmyslu používat rozhraní příkazového řádku Azure, [nainstalujte nejnovější verzi](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

## <a name="create-the-private-endpoints"></a>Vytvoření privátních koncových bodů
Při vytváření privátního koncového bodu pro prostředek Azure jsou nasazené tyto prostředky:

- **Privátní koncový bod**: prostředek Azure, který představuje privátní koncový bod pro účet úložiště nebo službu synchronizace úložiště. Můžete si to představit jako prostředek, který připojuje vaše prostředky Azure a síťové rozhraní.
- **Síťové rozhraní (nic)**: síťové rozhraní, které udržuje privátní IP adresu v zadané virtuální síti nebo podsíti. Jedná se o stejný prostředek, který se nasadí při nasazení virtuálního počítače, ale místo aby se přiřadil k VIRTUÁLNÍmu počítači, je vlastníkem privátního koncového bodu.
- **Privátní zóna DNS**: Pokud jste ještě nikdy nasadili privátní koncový bod pro tuto virtuální síť, bude pro vaši virtuální síť nasazená nová privátní zóna DNS. Pro prostředek Azure v této zóně DNS se vytvoří i záznam DNS. Pokud jste už v této virtuální síti nasadili privátní koncový bod, přidá se do existující zóny DNS nový záznam A pro prostředek Azure. Nasazení zóny DNS je volitelné, ale důrazně se doporučuje, abyste zjednodušili potřebnou správu DNS.

> [!Note]  
> Tento článek používá přípony DNS pro veřejné oblasti Azure `core.windows.net` pro účty úložiště a `afs.azure.net` služby synchronizace úložiště. Tento komentář platí také pro cloudy Azure svrchované jako Azure USA pro státní správu – stačí nahradit příslušné přípony vašeho prostředí.

### <a name="create-the-storage-account-private-endpoint"></a>Vytvoření privátního koncového bodu účtu úložiště
# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

Pokud máte virtuální počítač ve vaší virtuální síti nebo jste nakonfigurovali předávání DNS, jak je popsáno v tématu [konfigurace předávání DNS pro soubory Azure](storage-files-networking-dns.md), můžete otestovat správné nastavení privátního koncového bodu spuštěním následujících příkazů z PowerShellu, příkazového řádku nebo terminálu (funguje pro Windows, Linux nebo MacOS). Musíte nahradit `<storage-account-name>` odpovídajícím názvem účtu úložiště:

```console
nslookup <storage-account-name>.file.core.windows.net
```

Pokud vše úspěšně fungovalo, měl by se zobrazit následující výstup, kde `192.168.0.5` je privátní IP adresa privátního koncového bodu ve vaší virtuální síti (výstup zobrazený pro Windows):

```output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

Pokud máte virtuální počítač ve vaší virtuální síti nebo jste nakonfigurovali předávání DNS, jak je popsáno v tématu [konfigurace předávání DNS pro soubory Azure](storage-files-networking-dns.md), můžete otestovat správné nastavení privátního koncového bodu s následujícími příkazy:

```powershell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Pokud vše úspěšně fungovalo, měl by se zobrazit následující výstup, kde `192.168.0.5` je privátní IP adresa privátního koncového bodu ve vaší virtuální síti:

```output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]

Pokud máte virtuální počítač ve vaší virtuální síti nebo jste nakonfigurovali předávání DNS, jak je popsáno v tématu [konfigurace předávání DNS pro soubory Azure](storage-files-networking-dns.md), můžete otestovat správné nastavení privátního koncového bodu s následujícími příkazy:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Pokud vše úspěšně fungovalo, měl by se zobrazit následující výstup, kde `192.168.0.5` je privátní IP adresa privátního koncového bodu ve vaší virtuální síti:

```output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

### <a name="create-the-storage-sync-private-endpoint"></a>Vytvoření privátního koncového bodu synchronizace úložiště
> [!Important]  
> Aby bylo možné používat privátní koncové body v prostředku služby synchronizace úložiště, je nutné použít agenta Synchronizace souborů Azure verze 10,1 nebo vyšší. Verze agenta starší než 10,1 nepodporují privátní koncové body ve službě synchronizace úložiště. Všechny předchozí verze agenta podporují soukromé koncové body v prostředku účtu úložiště.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Přejděte do **centra pro soukromé spojení** zadáním *privátního odkazu* do panelu hledání v horní části Azure Portal. V obsahu pro centrum privátních odkazů vyberte **privátní koncové body** a potom **+ Přidat** pro vytvoření nového privátního koncového bodu.

[![Snímek obrazovky se soukromým centrem odkazů](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png)](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png#lightbox)

Výsledný Průvodce má několik stránek, které je potřeba dokončit.

V okně **základy** vyberte požadovanou skupinu prostředků, název a oblast pro váš soukromý koncový bod. To může být cokoli, co potřebujete, ale nemusí se shodovat s synchronizační službou úložiště jakýmkoli způsobem, i když musíte vytvořit privátní koncový bod ve stejné oblasti jako virtuální síť, ve které chcete vytvořit privátní koncový bod.

![Snímek obrazovky s oddílem základy oddílu Vytvoření privátního koncového bodu](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-1.png)

V okně **prostředek** vyberte přepínač pro **připojení k prostředku Azure ve složce Můj adresář**. V části **typ prostředku** vyberte pro typ prostředku možnost **Microsoft. StorageSync/storageSyncServices** . 

Okno **Konfigurace** umožňuje vybrat konkrétní virtuální síť a podsíť, do které chcete přidat privátní koncový bod. Vyberte stejnou virtuální síť jako ta, kterou jste použili pro výše uvedený účet úložiště. Okno konfigurace obsahuje také informace pro vytvoření nebo aktualizaci privátní zóny DNS.

Kliknutím na tlačítko **zkontrolovat + vytvořit** vytvořte privátní koncový bod.

Pomocí následujících příkazů z PowerShellu můžete otestovat, jestli je váš privátní koncový bod správně nastavený. 

```powershell
$privateEndpointResourceGroupName = "<your-private-endpoint-resource-group>"
$privateEndpointName = "<your-private-endpoint-name>"

Get-AzPrivateEndpoint `
        -ResourceGroupName $privateEndpointResourceGroupName `
        -Name $privateEndpointName `
        -ErrorAction Stop | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty Id | `
    ForEach-Object { Get-AzNetworkInterface -ResourceId $_ } | `
    Select-Object -ExpandProperty IpConfigurations | `
    Select-Object -ExpandProperty PrivateLinkConnectionProperties | `
    Select-Object -ExpandProperty Fqdns | `
    ForEach-Object { Resolve-DnsName -Name $_ } | `
    Format-List
```

Pokud vše fungovalo správně, měli byste vidět následující výstup, ve `192.168.1.4` kterém `192.168.1.5` jsou,, `192.168.1.6` a `192.168.1.7` jsou privátními IP adresami přiřazenými k privátnímu koncovému bodu:

```output
Name     : mysssmanagement.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : mysssmanagement.westus2.privatelink.afs.azure.net


Name       : mysssmanagement.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.4

Name     : myssssyncp.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : myssssyncp.westus2.privatelink.afs.azure.net


Name       : myssssyncp.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.5

Name     : myssssyncs.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : myssssyncs.westus2.privatelink.afs.azure.net


Name       : myssssyncs.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.6

Name     : mysssmonitoring.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : mysssmonitoring.westus2.privatelink.afs.azure.net


Name       : mysssmonitoring.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.7

```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pro vytvoření privátního koncového bodu pro službu synchronizace úložiště nejprve budete muset získat odkaz na službu synchronizace úložiště. Nezapomeňte nahradit `<storage-sync-service-resource-group>` a `<storage-sync-service>` správnými hodnotami pro vaše prostředí. Následující příkazy prostředí PowerShell předpokládají, že používáte již vyplněné informace o virtuální síti. 

```powershell
$storageSyncServiceResourceGroupName = "<storage-sync-service-resource-group>"
$storageSyncServiceName = "<storage-sync-service>"

$storageSyncService = Get-AzStorageSyncService `
        -ResourceGroupName $storageSyncServiceResourceGroupName `
        -Name $storageSyncServiceName `
        -ErrorAction SilentlyContinue

if ($null -eq $storageSyncService) {
    $errorMessage = "Storage Sync Service $storageSyncServiceName not found "
    $errorMessage += "in resource group $storageSyncServiceResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}
```

Chcete-li vytvořit privátní koncový bod, je nutné vytvořit připojení služby privátního propojení se službou synchronizace úložiště. Připojení privátního propojení je vstupem pro vytvoření privátního koncového bodu.

```powershell 
# Disable private endpoint network policies
$subnet.PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork = $virtualNetwork | `
    Set-AzVirtualNetwork -ErrorAction Stop

# Create a private link service connection to the storage account.
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name "$storageSyncServiceName-Connection" `
        -PrivateLinkServiceId $storageSyncService.ResourceId `
        -GroupId "Afs" `
        -ErrorAction Stop

# Create a new private endpoint.
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $storageSyncServiceResourceGroupName `
        -Name "$storageSyncServiceName-PrivateEndpoint" `
        -Location $virtualNetwork.Location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -ErrorAction Stop
```

Vytvoření privátní zóny DNS Azure povolí názvy hostitelů pro službu synchronizace úložiště, například `mysssmanagement.westus2.afs.azure.net` , aby se přeložily na správné privátní IP adresy služby synchronizace úložiště v rámci virtuální sítě. I když je volitelné z perspektivy vytváření privátního koncového bodu, je explicitně vyžadováno, aby agent Synchronizace souborů Azure k přístupu ke službě synchronizace úložiště. 

```powershell
# Get the desired Storage Sync Service suffix (afs.azure.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
$azureEnvironment = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

switch($azureEnvironment) {
    "AzureCloud" {
        $storageSyncSuffix = "afs.azure.net"
    }

    "AzureUSGovernment" {
        $storageSyncSuffix = "afs.azure.us"
    }
    
    default {
        Write-Error 
                -Message "The Azure environment $_ is not currently supported by Azure File Sync." `
                -ErrorAction Stop
    }
}

# For public cloud, this will generate the following DNS suffix:
# privatelink.afs.azure.net
$dnsZoneName = "privatelink.$storageSyncSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
$dnsZone = Get-AzPrivateDnsZone | `
    Where-Object { $_.Name -eq $dnsZoneName } | `
    Where-Object {
        $privateDnsLink = Get-AzPrivateDnsVirtualNetworkLink `
                -ResourceGroupName $_.ResourceGroupName `
                -ZoneName $_.Name `
                -ErrorAction SilentlyContinue
        
        $privateDnsLink.VirtualNetworkId -eq $virtualNetwork.Id
    }

if ($null -eq $dnsZone) {
    # No matching DNS zone attached to virtual network, so create new one.
    $dnsZone = New-AzPrivateDnsZone `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -Name $dnsZoneName `
            -ErrorAction Stop

    $privateDnsLink = New-AzPrivateDnsVirtualNetworkLink `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name "$virtualNetworkName-DnsLink" `
            -VirtualNetworkId $virtualNetwork.Id `
            -ErrorAction Stop
}
```
Teď, když máte odkaz na privátní zónu DNS, musíte pro službu synchronizace úložiště vytvořit záznamy A.

```powershell 
$privateEndpointIpFqdnMappings = $privateEndpoint | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty Id | `
    ForEach-Object { Get-AzNetworkInterface -ResourceId $_ } | `
    Select-Object -ExpandProperty IpConfigurations | `
    ForEach-Object { 
        $privateIpAddress = $_.PrivateIpAddress; 
        $_ | `
            Select-Object -ExpandProperty PrivateLinkConnectionProperties | `
            Select-Object -ExpandProperty Fqdns | `
            Select-Object `
                @{ 
                    Name = "PrivateIpAddress"; 
                    Expression = { $privateIpAddress } 
                }, `
                @{ 
                    Name = "FQDN"; 
                    Expression = { $_ } 
                } 
    }

foreach($ipFqdn in $privateEndpointIpFqdnMappings) {
    $privateDnsRecordConfig = New-AzPrivateDnsRecordConfig `
        -IPv4Address $ipFqdn.PrivateIpAddress
    
    $dnsEntry = $ipFqdn.FQDN.Substring(0, 
        $ipFqdn.FQDN.IndexOf(".", $ipFqdn.FQDN.IndexOf(".") + 1))

    New-AzPrivateDnsRecordSet `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -Name $dnsEntry `
            -RecordType A `
            -ZoneName $dnsZoneName `
            -Ttl 600 `
            -PrivateDnsRecords $privateDnsRecordConfig `
            -ErrorAction Stop | `
        Out-Null
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pro vytvoření privátního koncového bodu pro službu synchronizace úložiště nejprve budete muset získat odkaz na službu synchronizace úložiště. Nezapomeňte nahradit `<storage-sync-service-resource-group>` a `<storage-sync-service>` správnými hodnotami pro vaše prostředí. V následujících příkazech rozhraní příkazového řádku se předpokládá, že používáte již vyplněné informace o virtuální síti. 

```bash
storageSyncServiceResourceGroupName="<storage-sync-service-resource-group>"
storageSyncServiceName="<storage-sync-service>"

storageSyncService=$(az resource show \
        --resource-group $storageSyncServiceResourceGroupName \
        --name $storageSyncServiceName \
        --resource-type "Microsoft.StorageSync/storageSyncServices" \
        --query "id" | \
    tr -d '"')

storageSyncServiceRegion=$(az resource show \
        --resource-group $storageSyncServiceResourceGroupName \
        --name $storageSyncServiceName \
        --resource-type "Microsoft.StorageSync/storageSyncServices" \
        --query "location" | \
    tr -d '"')
```

Pokud chcete vytvořit privátní koncový bod, musíte nejdřív zkontrolovat, jestli je zásada sítě privátního koncového bodu podsítě nastavená na Disabled (zakázáno). Pak můžete pomocí příkazu vytvořit privátní koncový bod `az network private-endpoint create` .

```bash
# Disable private endpoint network policies
az network vnet subnet update \
        --ids $subnet \
        --disable-private-endpoint-network-policies \
        --output none

# Get virtual network location
region=$(az network vnet show \
        --ids $virtualNetwork \
        --query "location" | \
    tr -d '"')

# Create a private endpoint
privateEndpoint=$(az network private-endpoint create \
        --resource-group $storageSyncServiceResourceGroupName \
        --name "$storageSyncServiceName-PrivateEndpoint" \
        --location $region \
        --subnet $subnet \
        --private-connection-resource-id $storageSyncService \
        --group-id "Afs" \
        --connection-name "$storageSyncServiceName-Connection" \
        --query "id" | \
    tr -d '"')
```

Vytvoření privátní zóny DNS Azure povolí názvy hostitelů pro službu synchronizace úložiště, například `mysssmanagement.westus2.afs.azure.net` , aby se přeložily na správné privátní IP adresy služby synchronizace úložiště v rámci virtuální sítě. I když je volitelné z perspektivy vytváření privátního koncového bodu, je explicitně vyžadováno, aby agent Synchronizace souborů Azure k přístupu ke službě synchronizace úložiště. 

```bash
# Get the desired storage account suffix (afs.azure.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
azureEnvironment=$(az cloud show \
        --query "name" |
    tr -d '"')

storageSyncSuffix=""
if [ $azureEnvironment == "AzureCloud" ]
then
    storageSyncSuffix="afs.azure.net"
elif [ $azureEnvironment == "AzureUSGovernment" ]
then
    storageSyncSuffix="afs.azure.us"
else
    echo "Unsupported Azure environment $azureEnvironment."
fi

# For public cloud, this will generate the following DNS suffix:
# privatelinke.afs.azure.net.
dnsZoneName="privatelink.$storageSyncSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
possibleDnsZones=""
possibleDnsZones=$(az network private-dns zone list \
        --query "[?name == '$dnsZoneName'].id" \
        --output tsv)

dnsZone=""
possibleDnsZone=""
for possibleDnsZone in $possibleDnsZones
do
    possibleResourceGroupName=$(az resource show \
            --ids $possibleDnsZone \
            --query "resourceGroup" | \
        tr -d '"')
    
    link=$(az network private-dns link vnet list \
            --resource-group $possibleResourceGroupName \
            --zone-name $dnsZoneName \
            --query "[?virtualNetwork.id == '$virtualNetwork'].id" \
            --output tsv)
    
    if [ -z $link ]
    then
        echo "1" > /dev/null
    else 
        dnsZoneResourceGroup=$possibleResourceGroupName
        dnsZone=$possibleDnsZone
        break
    fi  
done

if [ -z $dnsZone ]
then
    # No matching DNS zone attached to virtual network, so create a new one
    dnsZone=$(az network private-dns zone create \
            --resource-group $virtualNetworkResourceGroupName \
            --name $dnsZoneName \
            --query "id" | \
        tr -d '"')
    
    az network private-dns link vnet create \
            --resource-group $virtualNetworkResourceGroupName \
            --zone-name $dnsZoneName \
            --name "$virtualNetworkName-DnsLink" \
            --virtual-network $virtualNetwork \
            --registration-enabled false \
            --output none
    
    dnsZoneResourceGroup=$virtualNetworkResourceGroupName
fi
```

Teď, když máte odkaz na privátní zónu DNS, musíte pro službu synchronizace úložiště vytvořit záznamy A.

```bash
privateEndpointNIC=$(az network private-endpoint show \
        --ids $privateEndpoint \
        --query "networkInterfaces[0].id" | \
    tr -d '"')

privateIpAddresses=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[].privateIpAddress" \
        --output tsv) 

hostNames=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[].privateLinkConnectionProperties.fqdns[]" \
        --output tsv)

i=0
for privateIpAddress in $privateIpAddresses
do
    j=0
    targetHostName=""
    for hostName in $hostNames
    do
        if [ $i == $j ]
        then
            targetHostName=$hostName
            break
        fi

        j=$(expr $j + 1)
    done

    endpointName=$(echo $targetHostName | \
        cut -c1-$(expr $(expr index $targetHostName ".") - 1))

    az network private-dns record-set a create \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --name "$endpointName.$storageSyncServiceRegion" \
        --output none
    
    az network private-dns record-set a add-record \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --record-set-name "$endpointName.$storageSyncServiceRegion" \
        --ipv4-address $privateIpAddress \
        --output none

    i=$(expr $i + 1)
done
```
---

## <a name="restrict-access-to-the-public-endpoints"></a>Omezení přístupu k veřejným koncovým bodům
Můžete omezit přístup k veřejným koncovým bodům účtu úložiště i služby synchronizace úložiště. Omezení přístupu k veřejnému koncovému bodu poskytuje dodatečné zabezpečení tím, že zajistíte, aby byly síťové pakety přijímány pouze ze schválených umístění. 

### <a name="restrict-access-to-the-storage-account-public-endpoint"></a>Omezení přístupu k veřejnému koncovému bodu účtu úložiště
Omezení přístupu k veřejnému koncovému bodu se provádí pomocí nastavení brány firewall účtu úložiště. Obecně platí, že většina zásad brány firewall pro účet úložiště omezí přístup k síti na jednu nebo více virtuálních sítí. Existují dva způsoby, jak omezit přístup k účtu úložiště na virtuální síť:

- [Vytvořte jeden nebo několik privátních koncových bodů pro účet úložiště](#create-the-storage-account-private-endpoint) a zakažte přístup k veřejnému koncovému bodu. Tím se zajistí, že budou mít přístup ke sdíleným složkám Azure v rámci účtu úložiště jenom přenosy pocházející z požadovaných virtuálních sítí.
- Omezte veřejný koncový bod na jednu nebo více virtuálních sítí. To funguje pomocí funkce virtuální sítě s názvem *koncové body služby*. Když omezíte provoz na účet úložiště prostřednictvím koncového bodu služby, stále přistupujete k účtu úložiště prostřednictvím veřejné IP adresy.

#### <a name="disable-access-to-the-storage-account-public-endpoint"></a>Zakázat přístup k veřejnému koncovému bodu účtu úložiště
Když je zakázaný přístup k veřejnému koncovému bodu, je možné, že k účtu úložiště budete mít přístup prostřednictvím svých privátních koncových bodů. Jinak budou požadavky na veřejný koncový bod účtu úložiště odmítnuty. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-storage-account-public-endpoint-to-specific-virtual-networks"></a>Omezte přístup k veřejnému koncovému bodu účtu úložiště na konkrétní virtuální sítě.
Když omezíte účet úložiště na konkrétní virtuální sítě, povolujete požadavky na veřejný koncový bod v rámci zadaných virtuálních sítí. To funguje pomocí funkce virtuální sítě s názvem *koncové body služby*. Tato možnost se dá použít s privátními koncovými body nebo bez nich.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

### <a name="disable-access-to-the-storage-sync-service-public-endpoint"></a>Zakázat přístup ke veřejnému koncovému bodu služby synchronizace úložiště
Synchronizace souborů Azure umožňuje omezit přístup ke konkrétním virtuálním sítím jenom prostřednictvím privátních koncových bodů. Synchronizace souborů Azure nepodporuje koncové body služby pro omezení přístupu k veřejnému koncovému bodu pro konkrétní virtuální sítě. To znamená, že jsou povolené a zakázané dva stavy pro veřejný koncový bod služby synchronizace úložiště.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
To není možné prostřednictvím Azure Portal. Pokud chcete získat pokyny, jak zakázat veřejný koncový bod služby synchronizace úložiště, vyberte prosím kartu Azure PowerShell. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pokud chcete zakázat přístup ke veřejnému koncovému bodu služby synchronizace úložiště, nastavili jsme `incomingTrafficPolicy` vlastnost ve službě synchronizace úložiště na `AllowVirtualNetworksOnly` . Pokud chcete povolit přístup k veřejnému koncovému bodu služby synchronizace úložiště, nastavte `incomingTrafficPolicy` na `AllowAllTraffic` místo. Nezapomeňte nahradit `<storage-sync-service-resource-group>` a `<storage-sync-service>` .

```powershell
$storageSyncServiceResourceGroupName = "<storage-sync-service-resource-group>"
$storageSyncServiceName = "<storage-sync-service>"

$storageSyncService = Get-AzResource `
        -ResourceGroupName $storageSyncServiceResourceGroupName `
        -ResourceName $storageSyncServiceName `
        -ResourceType "Microsoft.StorageSync/storageSyncServices"

$storageSyncService.Properties.incomingTrafficPolicy = "AllowVirtualNetworksOnly"
$storageSyncService = $storageSyncService | Set-AzResource -Confirm:$false -Force -UsePatchSemantics
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Rozhraní příkazového řádku Azure nepodporuje nastavení `incomingTrafficPolicy` vlastnosti ve službě synchronizace úložiště. Pokud chcete získat pokyny, jak zakázat veřejný koncový bod služby synchronizace úložiště, vyberte prosím kartu Azure PowerShell.

---

## <a name="see-also"></a>Viz také
- [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
- [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)
