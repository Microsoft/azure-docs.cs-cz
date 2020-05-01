---
title: Konfigurace koncových bodů sítě služby Azure Files | Microsoft Docs
description: Přehled možností sítě pro soubory Azure.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8ee9ddbd8a2d0ecbe8e2f13e6421cec177c7ce69
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594198"
---
# <a name="configuring-azure-files-network-endpoints"></a>Konfigurace koncových bodů sítě služby soubory Azure
Soubory Azure poskytují dva hlavní typy koncových bodů pro přístup ke sdíleným složkám Azure: 
- Veřejné koncové body, které mají veřejnou IP adresu a jsou přístupné odkudkoli na světě.
- Privátní koncové body, které existují v rámci virtuální sítě a které mají privátní IP adresu v adresním prostoru virtuální sítě.

V účtu služby Azure Storage existují veřejné a privátní koncové body. Účet úložiště je konstrukce správy, která představuje sdílený fond úložiště, ve kterém můžete nasadit více sdílených složek a další prostředky úložiště, jako jsou kontejnery nebo fronty objektů BLOB.

Tento článek se zaměřuje na konfiguraci koncových bodů účtu úložiště pro přímý přístup ke sdílené složce Azure. Většina podrobností uvedených v tomto dokumentu se vztahuje také na to, jak Azure File Sync spolupracují s veřejnými a soukromými koncovými body pro účet úložiště. Další informace o požadavcích na síť pro Azure File Sync nasazení najdete v tématu [konfigurace Azure File Sync proxy serveru a nastavení brány firewall](storage-sync-files-firewall-and-proxy.md).

Před načtením tohoto průvodce doporučujeme přečíst si [informace o sítích Azure Files](storage-files-networking-overview.md) .

## <a name="prerequisites"></a>Požadavky
- V tomto článku se předpokládá, že jste už vytvořili předplatné Azure. Pokud ještě nemáte předplatné, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- V tomto článku se předpokládá, že už máte vytvořenou sdílenou složku Azure v účtu úložiště, ke kterému se chcete připojit z místního prostředí. Informace o tom, jak vytvořit sdílenou složku Azure, najdete v tématu [Vytvoření sdílené složky Azure](storage-how-to-create-file-share.md).
- Pokud máte v úmyslu použít Azure PowerShell, [nainstalujte nejnovější verzi](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Pokud máte v úmyslu používat rozhraní příkazového řádku Azure, [nainstalujte nejnovější verzi](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu
Vytvořením privátního koncového bodu pro účet úložiště dojde k nasazení těchto prostředků Azure:

- **Privátní koncový bod**: prostředek Azure, který představuje privátní koncový bod účtu úložiště. Můžete si to představit jako prostředek, který připojuje účet úložiště a síťové rozhraní.
- **Síťové rozhraní (nic)**: síťové rozhraní, které udržuje privátní IP adresu v zadané virtuální síti nebo podsíti. Jedná se o stejný prostředek, který se nasadí při nasazení virtuálního počítače, ale místo aby se přiřadil k VIRTUÁLNÍmu počítači, je vlastníkem privátního koncového bodu.
- **Privátní zóna DNS**: Pokud jste ještě nikdy nasadili privátní koncový bod pro tuto virtuální síť, bude pro vaši virtuální síť nasazená nová privátní zóna DNS. Pro účet úložiště v této zóně DNS se vytvoří i záznam DNS. Pokud jste již v této virtuální síti nasadili privátní koncový bod, bude do existující zóny DNS přidán záznam nového záznamu pro účet úložiště. Nasazení zóny DNS je volitelné, ale důrazně se doporučuje a vyžaduje se, pokud připojujete sdílené složky Azure k instančnímu objektu služby AD nebo pomocí rozhraní REST API.

> [!Note]  
> Tento článek používá příponu DNS účtu úložiště pro veřejné oblasti Azure, `core.windows.net`. Tento komentář platí také pro cloudy Azure, jako je Cloud pro státní správu USA a Azure Čína, stačí nahradit příslušné přípony vašeho prostředí. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Přejděte do účtu úložiště, pro který chcete vytvořit privátní koncový bod. V obsahu pro účet úložiště vyberte **připojení privátního koncového bodu**a potom **+ privátní koncový bod** pro vytvoření nového privátního koncového bodu. 

![Snímek obrazovky s položkou připojení privátního koncového bodu v obsahu účtu úložiště](media/storage-files-networking-endpoints/create-private-endpoint-0.png)

Výsledný Průvodce má několik stránek, které je potřeba dokončit.

V okně **základy** vyberte požadovanou skupinu prostředků, název a oblast pro váš soukromý koncový bod. To může být cokoli, co potřebujete, ale nemusí se shodovat s účtem úložiště, i když musíte vytvořit privátní koncový bod ve stejné oblasti jako virtuální síť, ve které chcete vytvořit privátní koncový bod.

![Snímek obrazovky s oddílem základy oddílu Vytvoření privátního koncového bodu](media/storage-files-networking-endpoints/create-private-endpoint-1.png)

V okně **prostředek** vyberte přepínač pro **připojení k prostředku Azure ve složce Můj adresář**. V části **typ prostředku**vyberte pro typ prostředku možnost **Microsoft. Storage/storageAccounts** . Pole **prostředek** je účet úložiště se sdílenou složkou Azure, ke které se chcete připojit. Cílový dílčí prostředek je **soubor**, protože se jedná o soubory Azure.

Okno **Konfigurace** umožňuje vybrat konkrétní virtuální síť a podsíť, do které chcete přidat privátní koncový bod. Vyberte virtuální síť, kterou jste vytvořili výše. Musíte vybrat odlišnou podsíť z podsítě, do které jste přidali koncový bod služby. Okno konfigurace obsahuje také informace pro vytvoření nebo aktualizaci privátní zóny DNS. Doporučujeme používat výchozí `privatelink.file.core.windows.net` zónu.

![Snímek obrazovky konfiguračního oddílu](media/storage-files-networking-endpoints/create-private-endpoint-2.png)

Kliknutím na tlačítko **zkontrolovat + vytvořit** vytvořte privátní koncový bod. 

Pokud máte virtuální počítač ve vaší virtuální síti nebo jste nakonfigurovali předávání DNS, jak je popsáno [zde](storage-files-networking-dns.md), můžete otestovat, jestli jste privátní koncový bod správně nastavili, spuštěním následujících příkazů z PowerShellu, příkazového řádku nebo terminálu (funguje pro Windows, Linux nebo MacOS). Musíte nahradit `<storage-account-name>` odpovídajícím názvem účtu úložiště:

```
nslookup <storage-account-name>.file.core.windows.net
```

Pokud vše úspěšně fungovalo, měl by se zobrazit následující výstup, kde `192.168.0.5` je privátní IP adresa privátního koncového bodu ve vaší virtuální síti (výstup zobrazený pro Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pokud chcete pro svůj účet úložiště vytvořit privátní koncový bod, musíte nejdřív získat odkaz na svůj účet úložiště a podsíť virtuální sítě, ke které chcete privátní koncový bod přidat. `<storage-account-resource-group-name>`Nahraďte `<storage-account-name>`, `<vnet-resource-group-name>` `<vnet-name>`,, a `<vnet-subnet-name>` níže:

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group-name>"
$storageAccountName = "<storage-account-name>"
$virtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$virtualNetworkName = "<vnet-name>"
$subnetName = "<vnet-subnet-name>"

# Get storage account reference, and throw error if it doesn't exist
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction SilentlyContinue

if ($null -eq $storageAccount) {
    $errorMessage = "Storage account $storageAccountName not found "
    $errorMessage += "in resource group $storageAccountResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get virtual network reference, and throw error if it doesn't exist
$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $virtualNetworkName `
        -ErrorAction SilentlyContinue

if ($null -eq $virtualNetwork) {
    $errorMessage = "Virtual network $virtualNetworkName not found "
    $errorMessage += "in resource group $virtualNetworkResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get reference to virtual network subnet, and throw error if it doesn't exist
$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $virtualNetworkName." `
            -ErrorAction Stop
}
```

Pokud chcete vytvořit privátní koncový bod, musíte vytvořit připojení služby privátního propojení k účtu úložiště. Připojení služby privátního koncového bodu je vstupem k vytvoření privátního koncového bodu. 

```PowerShell
# Disable private endpoint network policies
$subnet.PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork | Out-Null

# Create a private link service connection to the storage account.
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name "$storageAccountName-Connection" `
        -PrivateLinkServiceId $storageAccount.Id `
        -GroupId "file"

# Create a new private endpoint.
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name "$storageAccountName-PrivateEndpoint" `
        -Location $virtualNetwork.Location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -ErrorAction Stop
```

Vytvořením privátní zóny DNS Azure povolíte původní název účtu úložiště, třeba `storageaccount.file.core.windows.net` ho přeložit na privátní IP adresu uvnitř virtuální sítě. I když volitelná z perspektivy vytváření privátního koncového bodu, je explicitně nutná pro připojení sdílené složky Azure pomocí uživatelského objektu služby AD nebo přístupu prostřednictvím REST API.  

```PowerShell
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
$storageAccountSuffix = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
$dnsZoneName = "privatelink.file.$storageAccountSuffix"

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

Teď, když máte odkaz na privátní zónu DNS, musíte pro svůj účet úložiště vytvořit záznam A.

```PowerShell
$privateEndpointIP = $privateEndpoint | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object @{ 
        Name = "NetworkInterfaces"; 
        Expression = { Get-AzNetworkInterface -ResourceId $_.Id } 
    } | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty IpConfigurations | `
    Select-Object -ExpandProperty PrivateIpAddress

$privateDnsRecordConfig = New-AzPrivateDnsRecordConfig `
        -IPv4Address $privateEndpointIP

New-AzPrivateDnsRecordSet `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $storageAccountName `
        -RecordType A `
        -ZoneName $dnsZoneName `
        -Ttl 600 `
        -PrivateDnsRecords $privateDnsRecordConfig `
        -ErrorAction Stop | `
    Out-Null
```

Pokud máte virtuální počítač ve vaší virtuální síti nebo jste nakonfigurovali předávání DNS, jak je popsáno [zde](storage-files-networking-dns.md), můžete otestovat, že váš privátní koncový bod byl nastaven správně pomocí následujících příkazů:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Pokud vše úspěšně fungovalo, měl by se zobrazit následující výstup, kde `192.168.0.5` je privátní IP adresa privátního koncového bodu ve vaší virtuální síti:

```Output
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
Pokud chcete pro svůj účet úložiště vytvořit privátní koncový bod, musíte nejdřív získat odkaz na svůj účet úložiště a podsíť virtuální sítě, ke které chcete privátní koncový bod přidat. `<storage-account-resource-group-name>`Nahraďte `<storage-account-name>`, `<vnet-resource-group-name>` `<vnet-name>`,, a `<vnet-subnet-name>` níže:

```bash
storageAccountResourceGroupName="<storage-account-resource-group-name>"
storageAccountName="<storage-account-name>"
virtualNetworkResourceGroupName="<vnet-resource-group-name>"
virtualNetworkName="<vnet-name>"
subnetName="<vnet-subnet-name>"

# Get storage account ID 
storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

# Get virtual network ID
virtualNetwork=$(az network vnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --name $virtualNetworkName \
        --query "id" | \
    tr -d '"')

# Get subnet ID
subnet=$(az network vnet subnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --vnet-name $virtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

Pokud chcete vytvořit privátní koncový bod, musíte nejdřív zkontrolovat, jestli je zásada sítě privátního koncového bodu podsítě nastavená na Disabled (zakázáno). Pak můžete vytvořit privátní koncový bod pomocí `az network private-endpoint create` příkazu

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
        --resource-group $virtualNetworkResourceGroupName \
        --name "$storageAccountName-PrivateEndpoint" \
        --location $region \
        --subnet $subnet \
        --private-connection-resource-id $storageAccount \
        --group-ids "file" \
        --connection-name "$storageAccountName-Connection" \
        --query "id" | \
    tr -d '"')
```

Vytvořením privátní zóny DNS Azure povolíte původní název účtu úložiště, třeba `storageaccount.file.core.windows.net` ho přeložit na privátní IP adresu uvnitř virtuální sítě. I když volitelná z perspektivy vytváření privátního koncového bodu, je explicitně nutná pro připojení sdílené složky Azure pomocí uživatelského objektu služby AD nebo přístupu prostřednictvím REST API.  

```bash
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
storageAccountSuffix=$(az cloud show \
        --query "suffixes.storageEndpoint" | \
    tr -d '"')

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
dnsZoneName="privatelink.file.$storageAccountSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
possibleDnsZones=$(az network private-dns zone list \
        --query "[?name == '$dnsZoneName'].id" \
        --output tsv)

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
        1 > /dev/null
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
            --resource-group $resourceGroupName \
            --zone-name $zoneName \
            --name "$virtualNetworkName-DnsLink" \
            --virtual-network $virtualNetwork \
            --registration-enabled false \
            --output none
fi
```

Teď, když máte odkaz na privátní zónu DNS, musíte pro svůj účet úložiště vytvořit záznam A.

```bash
privateEndpointNIC=$(az network private-endpoint show \
        --ids $privateEndpoint \
        --query "networkInterfaces[0].id" | \
    tr -d '"')

privateEndpointIP=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[0].privateIpAddress" | \
    tr -d '"')

az network private-dns record-set a create \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --name $storageAccountName \
        --output none

az network private-dns record-set a add-record \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --record-set-name $storageAccountName \
        --ipv4-address $privateEndpointIP \
        --output none
```

Pokud máte virtuální počítač ve vaší virtuální síti nebo jste nakonfigurovali předávání DNS, jak je popsáno [zde](storage-files-networking-dns.md), můžete otestovat, že váš privátní koncový bod byl nastaven správně pomocí následujících příkazů:

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

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

## <a name="restrict-access-to-the-public-endpoint"></a>Omezení přístupu k veřejnému koncovému bodu
Přístup k veřejnému koncovému bodu můžete omezit pomocí nastavení brány firewall účtu úložiště. Obecně platí, že většina zásad brány firewall pro účet úložiště omezí přístup k síti na jednu nebo více virtuálních sítí. Existují dva způsoby, jak omezit přístup k účtu úložiště na virtuální síť:

- [Vytvořte jeden nebo několik privátních koncových bodů pro účet úložiště](#create-a-private-endpoint) a omezte veškerý přístup k veřejnému koncovému bodu. Tím se zajistí, že budou mít přístup ke sdíleným složkám Azure v rámci účtu úložiště jenom přenosy pocházející z požadovaných virtuálních sítí.
- Omezte veřejný koncový bod na jednu nebo více virtuálních sítí. To funguje pomocí funkce virtuální sítě s názvem *koncové body služby*. Když omezíte provoz na účet úložiště prostřednictvím koncového bodu služby, stále přistupujete k účtu úložiště prostřednictvím veřejné IP adresy.

### <a name="restrict-all-access-to-the-public-endpoint"></a>Omezit veškerý přístup k veřejnému koncovému bodu
Když je veškerý přístup k veřejnému koncovému bodu omezený, k účtu úložiště se dá přistupovat přes soukromé koncové body. Jinak budou požadavky na veřejný koncový bod účtu úložiště odmítnuty. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Přejděte do účtu úložiště, pro který chcete omezit veškerý přístup k veřejnému koncovému bodu. V obsahu účtu úložiště vyberte **brány firewall a virtuální sítě**.

V horní části stránky vyberte přepínač **vybrané sítě** . Tím se zruší počet nastavení pro kontrolu omezení veřejného koncového bodu. Ověřte, zda **mají důvěryhodné služby Microsoftu přístup k tomuto účtu služby** , aby mohly být pro přístup k účtu úložiště důvěryhodné služby Microsoft, například Azure File Sync.

![Snímek obrazovky okna brány firewall a virtuální sítě s příslušnými omezeními](media/storage-files-networking-endpoints/restrict-public-endpoint-0.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Následující příkaz PowerShellu odmítne veškerý provoz do veřejného koncového bodu účtu úložiště. Všimněte si, že tento příkaz `-Bypass` má parametr nastavený `AzureServices`na. Tím umožníte důvěryhodným službám, jako je například Azure File Sync, získat přístup k účtu úložiště prostřednictvím veřejného koncového bodu.

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Následující příkaz rozhraní příkazového řádku zakáže veškerý provoz do veřejného koncového bodu účtu úložiště. Všimněte si, že tento příkaz `-bypass` má parametr nastavený `AzureServices`na. Tím umožníte důvěryhodným službám, jako je například Azure File Sync, získat přístup k účtu úložiště prostřednictvím veřejného koncového bodu.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```
---

### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Omezení přístupu k veřejnému koncovému bodu na konkrétní virtuální sítě
Když omezíte účet úložiště na konkrétní virtuální sítě, povolujete požadavky na veřejný koncový bod v rámci zadaných virtuálních sítí. To funguje pomocí funkce virtuální sítě s názvem *koncové body služby*. Tato možnost se dá použít s privátními koncovými body nebo bez nich.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Přejděte do účtu úložiště, pro který chcete omezit veřejný koncový bod na konkrétní virtuální sítě. V obsahu účtu úložiště vyberte **brány firewall a virtuální sítě**. 

V horní části stránky vyberte přepínač **vybrané sítě** . Tím se zruší počet nastavení pro kontrolu omezení veřejného koncového bodu. Kliknutím na **+ Přidat existující virtuální síť** vyberte konkrétní virtuální síť, která by měla mít povolený přístup k účtu úložiště prostřednictvím veřejného koncového bodu. To bude vyžadovat výběr virtuální sítě a podsítě pro tuto virtuální síť. 

Ověřte, zda **mají důvěryhodné služby Microsoftu přístup k tomuto účtu služby** , aby mohly být pro přístup k účtu úložiště důvěryhodné služby Microsoft, například Azure File Sync.

![Snímek obrazovky okna brány firewall a virtuální sítě s konkrétní virtuální sítí s povoleným přístupem k účtu úložiště prostřednictvím veřejného koncového bodu](media/storage-files-networking-endpoints/restrict-public-endpoint-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Abychom omezili přístup k veřejnému koncovému bodu účtu úložiště na konkrétní virtuální sítě pomocí koncových bodů služby, musíme nejdřív shromáždit informace o účtu úložiště a virtuální síti. K získání `<storage-account-resource-group>`těchto `<storage-account-name>`informací `<vnet-resource-group-name>`vyplňte `<vnet-name>`,, `<subnet-name>` , a.

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group>"
$storageAccountName = "<storage-account-name>"
$restrictToVirtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$restrictToVirtualNetworkName = "<vnet-name>"
$subnetName = "<subnet-name>"

$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction Stop

$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $restrictToVirtualNetworkResourceGroupName `
        -Name $restrictToVirtualNetworkName `
        -ErrorAction Stop

$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $restrictToVirtualNetworkName." `
            -ErrorAction Stop
}
```

Aby bylo možné provoz z virtuální sítě povolit síťovým prostředkům Azure v rámci veřejného koncového bodu účtu úložiště, musí mít podsíť virtuální sítě vystavený koncový bod `Microsoft.Storage` služby. Následující příkazy prostředí PowerShell přidají koncový bod `Microsoft.Storage` služby do podsítě, pokud tam ještě není.

```PowerShell
$serviceEndpoints = $subnet | `
    Select-Object -ExpandProperty ServiceEndpoints | `
    Select-Object -ExpandProperty Service

if ($serviceEndpoints -notcontains "Microsoft.Storage") {
    if ($null -eq $serviceEndpoints) {
        $serviceEndpoints = @("Microsoft.Storage")
    } elseif ($serviceEndpoints -is [string]) {
        $serviceEndpoints = @($serviceEndpoints, "Microsoft.Storage")
    } else {
        $serviceEndpoints += "Microsoft.Storage"
    }

    $virtualNetwork = $virtualNetwork | Set-AzVirtualNetworkSubnetConfig `
            -Name $subnetName `
            -AddressPrefix $subnet.AddressPrefix `
            -ServiceEndpoint $serviceEndpoints `
            -WarningAction SilentlyContinue `
            -ErrorAction Stop | `
        Set-AzVirtualNetwork `
            -ErrorAction Stop
}
```

Posledním krokem omezení provozu na účet úložiště je vytvoření síťového pravidla a přidání do sady pravidel sítě účtu úložiště.

```PowerShell
$networkRule = $storageAccount | Add-AzStorageAccountNetworkRule `
    -VirtualNetworkResourceId $subnet.Id `
    -ErrorAction Stop

$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -VirtualNetworkRule $networkRule `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Abychom omezili přístup k veřejnému koncovému bodu účtu úložiště na konkrétní virtuální sítě pomocí koncových bodů služby, musíme nejdřív shromáždit informace o účtu úložiště a virtuální síti. K získání `<storage-account-resource-group>`těchto `<storage-account-name>`informací `<vnet-resource-group-name>`vyplňte `<vnet-name>`,, `<subnet-name>` , a.

```bash
storageAccountResourceGroupName="<storage-account-resource-group>"
storageAccountName="<storage-account-name>"
restrictToVirtualNetworkResourceGroupName="<vnet-resource-group-name>"
restrictToVirtualNetworkName="<vnet-name>"
subnetName="<subnet-name>"

storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

virtualNetwork=$(az network vnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --name $restrictToVirtualNetworkName \
        --query "id" | \
    tr -d '"')

subnet=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

Aby bylo možné provoz z virtuální sítě povolit síťovým prostředkům Azure v rámci veřejného koncového bodu účtu úložiště, musí mít podsíť virtuální sítě vystavený koncový bod `Microsoft.Storage` služby. V následujících příkazech rozhraní příkazového řádku `Microsoft.Storage` se koncový bod služby přidá do podsítě, pokud tam ještě není.

```bash
serviceEndpoints=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "serviceEndpoints[].service" \
        --output tsv)

foundStorageServiceEndpoint=false
for serviceEndpoint in $serviceEndpoints
do
    if [ $serviceEndpoint = "Microsoft.Storage" ]
    then
        foundStorageServiceEndpoint=true
    fi
done

if [ $foundStorageServiceEndpoint = false ] 
then
    serviceEndpointList=""

    for serviceEndpoint in $serviceEndpoints
    do
        serviceEndpointList+=$serviceEndpoint
        serviceEndpointList+=" "
    done
    
    serviceEndpointList+="Microsoft.Storage"

    az network vnet subnet update \
            --ids $subnet \
            --service-endpoints $serviceEndpointList \
            --output none
fi
```

Posledním krokem omezení provozu na účet úložiště je vytvoření síťového pravidla a přidání do sady pravidel sítě účtu úložiště.

```bash
az storage account network-rule add \
        --resource-group $storageAccountResourceGroupName \
        --account-name $storageAccountName \
        --subnet $subnet \
        --output none

az storage account update \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --bypass "AzureServices" \
        --default-action "Deny" \
        --output none
```

---

## <a name="see-also"></a>Viz také
- [Požadavky na síť pro Azure Files](storage-files-networking-overview.md)
- [Konfigurace přesměrování DNS pro Azure Files](storage-files-networking-dns.md)
- [Konfigurace S2S VPN pro soubory Azure](storage-files-configure-s2s-vpn.md)
