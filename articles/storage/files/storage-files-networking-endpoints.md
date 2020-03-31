---
title: Konfigurace síťových koncových bodů azure files | Dokumenty společnosti Microsoft
description: Přehled možností sítě pro soubory Azure.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cc487e8def180735606aa010651dde40ef93908e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80082504"
---
# <a name="configuring-azure-files-network-endpoints"></a>Konfigurace síťových koncových bodů souborů Azure
Azure Files poskytuje dva hlavní typy koncových bodů pro přístup ke sdíleným složkám Azure: 
- Veřejné koncové body, které mají veřejnou IP adresu a lze k nim přistupovat z libovolného místa na světě.
- Privátní koncové body, které existují v rámci virtuální sítě a mají privátní IP adresu z v adresním prostoru této virtuální sítě.

Veřejné a soukromé koncové body existují na účtu úložiště Azure. Účet úložiště je konstrukce správy, která představuje sdílený fond úložiště, ve kterém můžete nasadit více sdílených složek, stejně jako další prostředky úložiště, jako jsou kontejnery objektů blob nebo fronty.

Tento článek se zaměřuje na to, jak nakonfigurovat koncové body účtu úložiště pro přímý přístup ke sdílené složce Azure. Většina podrobností poskytovaných v tomto dokumentu se vztahuje také na to, jak Azure File Sync spolupracuje s veřejnými a soukromými koncovými body pro účet úložiště, ale další informace o aspektech sítě pro nasazení Synchronizace souborů Azure najdete [v tématu konfigurace nastavení proxy serveru Azure File Sync a brány firewall](storage-sync-files-firewall-and-proxy.md).

Doporučujeme si přečíst [aspekty sítě Azure Files](storage-files-networking-overview.md) před přečtením tohoto návodu.

## <a name="prerequisites"></a>Požadavky
- Tento článek předpokládá, že jste už vytvořili předplatné Azure. Pokud ještě nemáte předplatné, vytvořte si před zahájením [bezplatný účet.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Tento článek předpokládá, že jste už vytvořili sdílenou složku Azure v účtu úložiště, ke kterému se chcete připojit z místního prostředí. Informace o tom, jak vytvořit sdílenou složku Azure, najdete [v tématu Vytvoření sdílené složky Azure](storage-how-to-create-file-share.md).
- Pokud máte v úmyslu používat Azure PowerShell, [nainstalujte nejnovější verzi](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Pokud máte v úmyslu používat azure cli, [nainstalujte nejnovější verzi](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu
Vytvoření privátního koncového bodu pro váš účet úložiště bude mít za následek nasazení následujících prostředků Azure:

- **Soukromý koncový bod**: Prostředek Azure představující soukromý koncový bod účtu úložiště. Můžete si to myslet jako prostředek, který spojuje účet úložiště a síťové rozhraní.
- **Síťové rozhraní (NIC):** Síťové rozhraní, které udržuje privátní adresu IP v zadané virtuální síti nebo podsíti. Jedná se o přesně stejný prostředek, který se nasazuje při nasazení virtuálního počítače, ale místo toho, aby byl přiřazen k virtuálnímu počítači, je vlastněn soukromým koncovým bodem.
- **Privátní zóna DNS**: Pokud jste pro tuto virtuální síť nikdy předtím nenasadili privátní koncový bod, bude pro vaši virtuální síť nasazena nová privátní zóna DNS. Záznam DNS A bude vytvořen také pro účet úložiště v této zóně DNS. Pokud jste již v této virtuální síti nasadili privátní koncový bod, bude do existující zóny DNS přidán nový záznam A pro účet úložiště. Nasazení zóny DNS je volitelné, ale vysoce doporučeno, a povinné, pokud interujete sdílené složky Azure se zaregistrovaným objektem služby Služby AD nebo používáte rozhraní API FileREST.

> [!Note]  
> Tento článek používá příponu DNS účtu úložiště `core.windows.net`pro oblasti Azure Public . Tento komentář se vztahuje také na cloudy Azure Sovereign, jako je cloud Azure US Government a cloud Azure China – stačí nahradit příslušné přípony pro vaše prostředí. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Přejděte na účet úložiště, pro který chcete vytvořit soukromý koncový bod. V obsahu účtu úložiště vyberte **Soukromá připojení koncového bodu**a potom **+ Private koncový bod** vytvořte nový soukromý koncový bod. 

![Snímek obrazovky s položkou připojení privátního koncového bodu v obsahu účtu úložiště](media/storage-files-networking-endpoints/create-private-endpoint-0.png)

Výsledný průvodce má více stránek k dokončení.

V okně **Základy** vyberte požadovanou skupinu prostředků, název a oblast pro soukromý koncový bod. Může to být cokoli, co chcete, nemusí odpovídat účtu úložiště v každém případě, i když je nutné vytvořit soukromý koncový bod ve stejné oblasti jako virtuální síť, ve které chcete vytvořit soukromý koncový bod.

![Snímek obrazovky s částí Základy v části Vytvořit soukromý koncový bod](media/storage-files-networking-endpoints/create-private-endpoint-1.png)

V okně **Prostředek** vyberte přepínací tlačítko pro **připojení k prostředku Azure v mém adresáři**. V části **Typ prostředku**vyberte **microsoft.storage/storageAccounts** pro typ prostředku. Pole **Prostředek** je účet úložiště se sdílenou složkou Azure, ke které se chcete připojit. Cílový dílčí prostředek je **soubor**, protože se jedná o soubory Azure.

Okno **Konfigurace** umožňuje vybrat konkrétní virtuální síť a podsíť, do které chcete přidat privátní koncový bod. Vyberte virtuální síť, kterou jste vytvořili výše. Je nutné vybrat odlišnou podsíť z podsítě, do které jste přidali koncový bod služby. Okno Konfigurace také obsahuje informace pro vytvoření nebo aktualizaci privátní zóny DNS. Doporučujeme použít `privatelink.file.core.windows.net` výchozí zónu.

![Snímek obrazovky s oddílem Konfigurace](media/storage-files-networking-endpoints/create-private-endpoint-2.png)

Kliknutím na **Revize + vytvoření** vytvořte soukromý koncový bod. 

Pokud máte virtuální počítač uvnitř virtuální sítě nebo jste nakonfigurovali předávání DNS, jak je popsáno [zde](storage-files-networking-dns.md), můžete otestovat, že váš soukromý koncový bod byl správně nastaven spuštěním následujících příkazů z prostředí PowerShell, příkazového řádku nebo terminálu (funguje pro Windows, Linux nebo macOS). Je nutné `<storage-account-name>` nahradit příslušný název účtu úložiště:

```
nslookup <storage-account-name>.file.core.windows.net
```

Pokud vše fungovalo úspěšně, měli byste vidět `192.168.0.5` následující výstup, kde je privátní IP adresa privátní koncový bod ve vaší virtuální síti (výstup je zobrazen pro Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Chcete-li vytvořit soukromý koncový bod pro váš účet úložiště, musíte nejprve získat odkaz na účet úložiště a podsíť virtuální sítě, do které chcete přidat soukromý koncový bod. `<storage-account-resource-group-name>`Nahraďte `<vnet-resource-group-name>` `<vnet-name>`, `<storage-account-name>` `<vnet-subnet-name>` , , , a níže:

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

Chcete-li vytvořit soukromý koncový bod, musíte vytvořit připojení služby privátní ho propojení k účtu úložiště. Připojení služby privátní ho propojení je vstupem pro vytvoření privátního koncového bodu. 

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

Vytvoření privátní zóny DNS Azure umožňuje původní `storageaccount.file.core.windows.net` název účtu úložiště, například přeložit na privátní IP uvnitř virtuální sítě. I když volitelné z hlediska vytvoření privátní koncový bod, je explicitně nutné pro připojení sdílené složky Azure pomocí objektu uživatele Služby AD nebo přístup prostřednictvím rozhraní REST API.  

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

Teď, když máte odkaz na soukromou zónu DNS, musíte vytvořit záznam A pro váš účet úložiště.

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

Pokud máte virtuální počítač uvnitř virtuální sítě nebo jste nakonfigurovali předávání DNS, jak je popsáno [zde](storage-files-networking-dns.md), můžete otestovat, že váš soukromý koncový bod byl správně nastaven pomocí následujících příkazů:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.File) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Pokud vše fungovalo úspěšně, měli byste vidět `192.168.0.5` následující výstup, kde je privátní IP adresa privátní koncový bod ve vaší virtuální síti:

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
Chcete-li vytvořit soukromý koncový bod pro váš účet úložiště, musíte nejprve získat odkaz na účet úložiště a podsíť virtuální sítě, do které chcete přidat soukromý koncový bod. `<storage-account-resource-group-name>`Nahraďte `<vnet-resource-group-name>` `<vnet-name>`, `<storage-account-name>` `<vnet-subnet-name>` , , , a níže:

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

Chcete-li vytvořit soukromý koncový bod, musíte nejprve zajistit, aby zásady sítě privátní koncového bodu podsítě byly nastaveny na zakázané. Pak můžete vytvořit soukromý koncový `az network private-endpoint create` bod s příkazem

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

Vytvoření privátní zóny DNS Azure umožňuje původní `storageaccount.file.core.windows.net` název účtu úložiště, například přeložit na privátní IP uvnitř virtuální sítě. I když volitelné z hlediska vytvoření privátní koncový bod, je explicitně nutné pro připojení sdílené složky Azure pomocí objektu uživatele Služby AD nebo přístup prostřednictvím rozhraní REST API.  

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

Teď, když máte odkaz na soukromou zónu DNS, musíte vytvořit záznam A pro váš účet úložiště.

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

Pokud máte virtuální počítač uvnitř virtuální sítě nebo jste nakonfigurovali předávání DNS, jak je popsáno [zde](storage-files-networking-dns.md), můžete otestovat, že váš soukromý koncový bod byl správně nastaven pomocí následujících příkazů:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.File" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Pokud vše fungovalo úspěšně, měli byste vidět `192.168.0.5` následující výstup, kde je privátní IP adresa privátní koncový bod ve vaší virtuální síti:

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
Přístup k veřejnému koncovému bodu můžete omezit pomocí nastavení brány firewall účtu úložiště. Obecně platí, že většina zásad brány firewall pro účet úložiště omezí přístup k síti do jedné nebo více virtuálních sítí. Existují dva přístupy k omezení přístupu k účtu úložiště na virtuální síť:

- [Vytvořte jeden nebo více soukromých koncových bodů pro účet úložiště](#create-a-private-endpoint) a omezit veškerý přístup k veřejnému koncovému bodu. Tím zajistíte, že pouze provoz pocházející z v rámci požadovaných virtuálních sítí přístup ke sdíleným položkám souborů Azure v rámci účtu úložiště.
- Omezte veřejný koncový bod na jednu nebo více virtuálních sítí. To funguje pomocí funkce virtuální sítě nazývané *koncové body služby*. Když omezíte provoz na účet úložiště prostřednictvím koncového bodu služby, stále přistupujete k účtu úložiště prostřednictvím veřejné IP adresy.

### <a name="restrict-all-access-to-the-public-endpoint"></a>Omezení veškerého přístupu k veřejnému koncovému bodu
Při všech přístup k veřejnému koncovému bodu je omezen, účet úložiště stále přístupprostřednictvím je soukromé koncové body. V opačném případě budou platné požadavky na veřejný koncový bod účtu úložiště odmítnuty. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Přejděte na účet úložiště, pro který chcete omezit veškerý přístup k veřejnému koncovému bodu. V obsahu účtu úložiště vyberte **brány firewall a virtuální sítě**.

V horní části stránky vyberte **přepínací** tlačítko Vybrané sítě. Tím se odkryje počet nastavení pro řízení omezení veřejného koncového bodu. Zaškrtněte **políčko Povolit důvěryhodným službám Microsoftu přístup k tomuto účtu služby,** aby důvěryhodné služby Microsoftu první strany, jako je Azure File Sync, měly přístup k účtu úložiště.

![Snímek obrazovky s rozhraním Firewall s virtuálními sítěmi s příslušnými omezeními](media/storage-files-networking-endpoints/restrict-public-endpoint-0.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Následující příkaz Prostředí PowerShell odepře veškerý provoz na veřejný koncový bod účtu úložiště. Všimněte si, `-Bypass` že tento `AzureServices`příkaz má parametr nastaven na . To umožní důvěryhodným službám první strany, jako je Azure File Sync, přístup k účtu úložiště prostřednictvím veřejného koncového bodu.

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
Následující příkaz příkazu příkazu příkazu cli odepře veškerý provoz na veřejný koncový bod účtu úložiště. Všimněte si, `-bypass` že tento `AzureServices`příkaz má parametr nastaven na . To umožní důvěryhodným službám první strany, jako je Azure File Sync, přístup k účtu úložiště prostřednictvím veřejného koncového bodu.

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
Když omezíte účet úložiště na konkrétní virtuální sítě, povolíte požadavky na veřejný koncový bod z určených virtuálních sítí. To funguje pomocí funkce virtuální sítě nazývané *koncové body služby*. To lze použít s nebo bez soukromých koncových bodů.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Přejděte na účet úložiště, pro který chcete omezit veřejný koncový bod na konkrétní virtuální sítě. V obsahu účtu úložiště vyberte **brány firewall a virtuální sítě**. 

V horní části stránky vyberte **přepínací** tlačítko Vybrané sítě. Tím se odkryje počet nastavení pro řízení omezení veřejného koncového bodu. Kliknutím na **+Přidat existující virtuální síť** vyberte konkrétní virtuální síť, která by měla mít povolený přístup k účtu úložiště prostřednictvím veřejného koncového bodu. To bude vyžadovat výběr virtuální sítě a podsítě pro tuto virtuální síť. 

Zaškrtněte **políčko Povolit důvěryhodným službám Microsoftu přístup k tomuto účtu služby,** aby důvěryhodné služby Microsoftu první strany, jako je Azure File Sync, měly přístup k účtu úložiště.

![Snímek obrazovky s programem Firewalls a virtuálních sítí s konkrétní virtuální sítí, která má přístup k účtu úložiště prostřednictvím veřejného koncového bodu](media/storage-files-networking-endpoints/restrict-public-endpoint-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Chcete-li omezit přístup k veřejnému koncovému bodu účtu úložiště na konkrétní virtuální sítě pomocí koncových bodů služby, musíme nejprve shromažďovat informace o účtu úložiště a virtuální síti. Vyplňte `<storage-account-resource-group>` `<storage-account-name>`, `<vnet-resource-group-name>` `<vnet-name>`, `<subnet-name>` , , a shromažďovat tyto informace.

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

Aby se provoz z virtuální sítě povolil prostředkům sítě Azure, aby se dostal do veřejného koncového `Microsoft.Storage` bodu účtu úložiště, musí mít podsíť virtuální sítě vystavený koncový bod služby. Následující příkazy prostředí PowerShell `Microsoft.Storage` přidají koncový bod služby do podsítě, pokud ještě neexistuje.

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

Posledním krokem při omezení provozu na účet úložiště je vytvoření pravidla sítě a přidání do sady síťových pravidel účtu úložiště.

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
Chcete-li omezit přístup k veřejnému koncovému bodu účtu úložiště na konkrétní virtuální sítě pomocí koncových bodů služby, musíme nejprve shromažďovat informace o účtu úložiště a virtuální síti. Vyplňte `<storage-account-resource-group>` `<storage-account-name>`, `<vnet-resource-group-name>` `<vnet-name>`, `<subnet-name>` , , a shromažďovat tyto informace.

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

Aby se provoz z virtuální sítě povolil prostředkům sítě Azure, aby se dostal do veřejného koncového `Microsoft.Storage` bodu účtu úložiště, musí mít podsíť virtuální sítě vystavený koncový bod služby. Následující příkazy příkazu příkazu `Microsoft.Storage` příkazu příkazu příkazu cli přidají koncový bod služby do podsítě, pokud již neexistuje.

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

Posledním krokem při omezení provozu na účet úložiště je vytvoření pravidla sítě a přidání do sady síťových pravidel účtu úložiště.

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
- [Důležité informace o vytváření sítí Azure Files](storage-files-networking-overview.md)
- [Konfigurace předávání DNS pro soubory Azure](storage-files-networking-dns.md)
- [Konfigurace s2S VPN pro soubory Azure](storage-files-configure-s2s-vpn.md)