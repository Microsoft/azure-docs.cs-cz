---
title: Integrace služby Azure Event Hubs se službou privátního propojení Azure
description: Naučte se integrovat Azure Event Hubs se službou Azure Private Link.
ms.date: 08/22/2020
ms.topic: article
ms.openlocfilehash: 59167635cfc0d8c1123a47410c87d6b9151f6f62
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334238"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-via-private-endpoints"></a>Povolení přístupu k oborům názvů Azure Event Hubs prostřednictvím privátních koncových bodů 
Služba privátního propojení Azure umožňuje přístup ke službám Azure (například k Azure Event Hubs, Azure Storage a Azure Cosmos DB) a hostovaným zákaznickým a partnerským službám Azure prostřednictvím **privátního koncového bodu** ve vaší virtuální síti.

Privátní koncový bod je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služby se dá směrovat prostřednictvím privátního koncového bodu, takže se nevyžadují žádné brány, zařízení NAT, ExpressRoute, připojení VPN ani veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, která poskytuje nejvyšší úroveň členitosti v řízení přístupu.

Další informace najdete v tématu [co je privátní propojení Azure?](../private-link/private-link-overview.md)

> [!IMPORTANT]
> Tato funkce je podporovaná pro **standardní** i **vyhrazené** úrovně. Na úrovni **Basic** se nepodporuje.
>
> Povolení privátních koncových bodů může ostatním službám Azure zabránit v interakci s Event Hubs.  Blokované požadavky zahrnují ty z jiných služeb Azure, od Azure Portal, ze služeb protokolování a metriky atd. 
> 
> Zde jsou některé ze služeb, které nemůžou přistupovat k Event Hubs prostředkům, když jsou povolené privátní koncové body. Všimněte si, že seznam **není vyčerpávající.**
>
> - Trasy k Azure IoT Hub
> - Device Explorer Azure IoT
> - Azure Event Grid
> - Azure Monitor (nastavení diagnostiky)
>
> V případě výjimky můžete povolit přístup k Event Hubs prostředkům z určitých důvěryhodných služeb i v případě, že jsou povolené soukromé koncové body. Seznam důvěryhodných služeb najdete v tématu [důvěryhodné služby](#trusted-microsoft-services).

## <a name="add-a-private-endpoint-using-azure-portal"></a>Přidání privátního koncového bodu pomocí Azure Portal

### <a name="prerequisites"></a>Požadavky

K integraci oboru názvů Event Hubs s privátním propojením Azure budete potřebovat následující entity nebo oprávnění:

- Event Hubs obor názvů.
- Virtuální síť Azure.
- Podsíť ve virtuální síti. Můžete použít **výchozí** podsíť. 
- Oprávnění vlastníka nebo přispěvatele pro obor názvů i virtuální síť.

Váš privátní koncový bod a virtuální síť musí být ve stejné oblasti. Když vyberete oblast pro soukromý koncový bod pomocí portálu, automaticky se vyfiltrují jenom virtuální sítě, které jsou v této oblasti. Váš obor názvů může být v jiné oblasti.

Váš privátní koncový bod používá privátní IP adresu ve vaší virtuální síti.

### <a name="steps"></a>Postup
Pokud již máte obor názvů Event Hubs, můžete vytvořit připojení k privátnímu propojení pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. Na panelu hledání zadejte do pole **centra událostí**.
3. V seznamu vyberte **obor názvů** , do kterého chcete přidat privátní koncový bod.
4. V části **Nastavení** v nabídce vlevo vyberte **sítě** .

    > [!NOTE]
    > Karta **síť** se zobrazí jenom pro **standardní** nebo **vyhrazené** obory názvů. 

    :::image type="content" source="./media/private-link-service/selected-networks-page.png" alt-text="Karta sítě – volba vybraných sítí" lightbox="./media/private-link-service/selected-networks-page.png":::    

    > [!NOTE]
    > Ve výchozím nastavení je vybraná možnost **vybrané sítě** . Pokud nezadáte pravidlo brány firewall protokolu IP nebo přidáte virtuální síť, bude k oboru názvů možné přistupovat prostřednictvím veřejného Internetu. 
1. V horní části stránky vyberte kartu **připojení privátního koncového bodu** . 
1. V horní části stránky vyberte tlačítko **+ soukromý koncový bod** .

    :::image type="content" source="./media/private-link-service/private-link-service-3.png" alt-text="Stránka síť – karta připojení privátního koncového bodu – přidat odkaz na privátní koncový bod":::
7. Na stránce **základy** proveďte tyto kroky: 
    1. Vyberte **předplatné Azure** , ve kterém chcete vytvořit privátní koncový bod. 
    2. Vyberte **skupinu prostředků** pro prostředek privátního koncového bodu.
    3. Zadejte **název** privátního koncového bodu. 
    5. Vyberte **oblast** pro soukromý koncový bod. Váš privátní koncový bod musí být ve stejné oblasti jako vaše virtuální síť, ale může být v jiné oblasti než prostředek privátního propojení, ke kterému se připojujete. 
    6. V dolní části stránky vyberte tlačítko **Další: >prostředku ** .

        ![Stránka pro vytvoření privátního koncového bodu – základy](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na stránce **prostředek** použijte následující postup:
    1. V případě metody připojení vyberte **v adresáři připojit k prostředku Azure**, a to následujícím způsobem: 
        1. Vyberte **předplatné Azure** , ve kterém existuje **Event Hubs obor názvů** . 
        2. Jako **typ prostředku**vyberte **Microsoft. EventHub/obory názvů** pro **typ prostředku**.
        3. V části **prostředek**vyberte obor názvů Event Hubs v rozevíracím seznamu. 
        4. Potvrďte, že je **cílový podprostředek** nastavený na **obor názvů**.
        5. V dolní části stránky vyberte tlačítko **Další: >konfigurace ** . 
        
            ![Vytvoření stránky privátního koncového bodu – prostředek](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Pokud vyberete **připojit k prostředku Azure podle ID prostředku nebo aliasu**, postupujte podle těchto kroků:
        1. Zadejte **ID prostředku** nebo **alias**. Může to být ID prostředku nebo alias, který s vámi někdo sdílí. Nejjednodušší způsob, jak získat ID prostředku, je přejít na obor názvů Event Hubs v Azure Portal a zkopírovat část identifikátoru URI od `/subscriptions/` . Příklad najdete na následujícím obrázku. 
        2. Pro **cílový dílčí prostředek**zadejte **obor názvů**. Je to typ dílčího prostředku, ke kterému má privátní koncový bod přístup.
        3. volitelné Zadejte **zprávu požadavku**. Vlastník prostředku se zobrazí tato zpráva během správy připojení privátního koncového bodu.
        4. Potom v dolní části stránky vyberte tlačítko **Další: >konfigurace ** .

            ![Vytvoření privátního koncového bodu – připojení pomocí ID prostředku](./media/private-link-service/connect-resource-id.png)
9. Na stránce **Konfigurace** vyberte podsíť ve virtuální síti, do které chcete nasadit privátní koncový bod. 
    1. Vyberte **virtuální síť**. V rozevíracím seznamu jsou uvedené jenom virtuální sítě v aktuálně vybraném předplatném a umístění. 
    2. Vyberte **podsíť** ve virtuální síti, kterou jste vybrali. 
    3. V dolní části stránky vyberte tlačítko **Další: značky >** . 

        ![Vytvořit privátní koncový bod – konfigurační stránka](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na stránce **značky** vytvořte všechny značky (názvy a hodnoty), které chcete přidružit k prostředku privátního koncového bodu. Potom v dolní části stránky vyberte tlačítko **Revize + vytvořit** . 
11. Na stránce **zkontrolovat + vytvořit**Zkontrolujte všechna nastavení a výběrem možnosti **vytvořit** vytvořte privátní koncový bod.
    
    ![Vytvoření privátního koncového bodu – kontrola a vytvoření stránky](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Ověřte, že se v seznamu koncových bodů zobrazuje připojení privátního koncového bodu, které jste vytvořili. V tomto příkladu je soukromý koncový bod automaticky schválen, protože jste se připojili k prostředku Azure v adresáři a máte dostatečná oprávnění. 

    ![Privátní koncový bod byl vytvořen.](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

Pokud chcete, aby důvěryhodné služby měly přístup k vašemu oboru názvů, přejděte na kartu **brány firewall a virtuální sítě** na stránce **síť** a vyberte **Ano** , pokud **chcete, aby důvěryhodné služby Microsoftu tuto bránu firewall vynechal?**. 

## <a name="add-a-private-endpoint-using-powershell"></a>Přidání privátního koncového bodu pomocí prostředí PowerShell
Následující příklad ukazuje, jak použít Azure PowerShell k vytvoření připojení privátního koncového bodu. Nevytváří pro vás vyhrazený cluster. Pokud chcete vytvořit vyhrazený cluster Event Hubs, postupujte podle kroků v [tomto článku](event-hubs-dedicated-cluster-create-portal.md) . 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>Konfigurace privátní zóny DNS
Vytvořte privátní zónu DNS pro Event Hubs doménu a vytvořte propojení přidružení s virtuální sítí:

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>Správa privátních koncových bodů pomocí Azure Portal

Při vytváření privátního koncového bodu musí být připojení schváleno. Pokud je prostředek, pro který vytváříte privátní koncový bod, ve vašem adresáři, můžete žádost o připojení schválit, pokud máte dostatečná oprávnění. Pokud se připojujete k prostředku Azure v jiném adresáři, musíte počkat, až vlastník tohoto prostředku schválí žádost o připojení.

Existují čtyři stavy zřizování:

| Akce služby | Stav privátního koncového bodu příjemce služby | Popis |
|--|--|--|
| Žádné | Čekající | Připojení je vytvořeno ručně a čeká na schválení vlastníkem prostředku privátního odkazu. |
| Schválení | Schválené | Připojení bylo automaticky nebo ručně schváleno a je připraveno k použití. |
| Odmítnout | Zamítnuto | Připojení bylo odmítnuto vlastníkem prostředku privátního odkazu. |
| Odebrat | Propojení | Připojení bylo odebráno vlastníkem prostředku privátního propojení, soukromý koncový bod bude informativní a měl by být odstraněn pro vyčištění. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Schválení, zamítnutí nebo odebrání připojení privátního koncového bodu

1. Přihlaste se k portálu Azure.
2. Na panelu hledání zadejte do pole **centra událostí**.
3. Vyberte **obor názvů** , který chcete spravovat.
4. Vyberte kartu **síť** .
5. V závislosti na operaci, kterou chcete provést: schválit, zamítnout nebo odebrat, postupujte podle příslušného oddílu.

### <a name="approve-a-private-endpoint-connection"></a>Schválení připojení privátního koncového bodu
1. Pokud existují nějaká připojení, která čekají na **vyřízení, zobrazí se ve stavu** zřizování stav připojení. 
2. Vyberte **privátní koncový bod** , který chcete schválit.
3. Vyberte tlačítko **schválit** .

    ![Schválit soukromý koncový bod](./media/private-link-service/approve-private-endpoint.png)
4. Na stránce **schválit připojení** přidejte komentář (volitelné) a vyberte **Ano**. Pokud vyberete **ne**, nic se nestane. 
5. V seznamu byste měli vidět stav připojení privátního koncového bodu, které se změnilo na **schváleno**. 

### <a name="reject-a-private-endpoint-connection"></a>Zamítnutí připojení privátního koncového bodu

1. Pokud existují nějaká připojení privátního koncového bodu, která chcete zamítnout, ať už se jedná o nevyřízenou žádost nebo existující připojení, vyberte připojení a klikněte na tlačítko **odmítnout** .

    ![Odmítnout privátní koncový bod](./media/private-link-service/private-endpoint-reject-button.png)
2. Na stránce **odmítnout připojení** zadejte komentář (volitelné) a vyberte **Ano**. Pokud vyberete **ne**, nic se nestane. 
3. V seznamu byste měli vidět stav připojení privátního koncového bodu, které se změnilo na **Odmítnuto**. 

### <a name="remove-a-private-endpoint-connection"></a>Odebrání připojení privátního koncového bodu

1. Pokud chcete odebrat připojení privátního koncového bodu, vyberte ho v seznamu a na panelu nástrojů vyberte **Odebrat** .
2. Na stránce **Odstranit připojení** vyberte **Ano** a potvrďte odstranění privátního koncového bodu. Pokud vyberete **ne**, nic se nestane.
3. Měl by se zobrazit stav změněno na **Odpojeno**. Pak se v seznamu zobrazí koncový bod.

## <a name="validate-that-the-private-link-connection-works"></a>Ověření, že připojení privátního propojení funguje

Měli byste ověřit, že prostředky v rámci virtuální sítě privátního koncového bodu se připojují k vašemu Event Hubs oboru názvů přes soukromou IP adresu a že mají správnou integraci privátní zóny DNS.

Nejdřív vytvořte virtuální počítač podle kroků v [části Vytvoření virtuálního počítače s Windows v Azure Portal](../virtual-machines/windows/quick-create-portal.md)

Na kartě **síť** : 

1. Zadejte **virtuální síť** a **podsíť**. Musíte vybrat Virtual Network, na které jste nasadili privátní koncový bod.
2. Zadejte prostředek **veřejné IP adresy** .
3. V případě **skupiny zabezpečení sítě síťové karty**vyberte **žádné**.
4. V případě **Vyrovnávání zatížení**vyberte možnost **ne**.

Připojte se k virtuálnímu počítači, otevřete příkazový řádek a spusťte následující příkaz:

```console
nslookup <event-hubs-namespace-name>.servicebus.windows.net
```

Měl by se zobrazit výsledek, který vypadá nějak takto: 

```console
Non-authoritative answer:
Name:    <event-hubs-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <event-hubs-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Omezení a faktory návrhu

**Ceny**: informace o cenách najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link/).

**Omezení**: Tato funkce je dostupná ve všech veřejných oblastech Azure.

**Maximální počet privátních koncových bodů na obor názvů Event Hubs**: 120.

Další informace najdete v tématu [Služba privátního propojení Azure: omezení](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Další kroky

- Další informace o [privátním propojení Azure](../private-link/private-link-service-overview.md)
- Další informace o [Azure Event Hubs](event-hubs-about.md)
