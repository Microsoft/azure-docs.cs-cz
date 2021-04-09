---
title: Integrace Azure Service Bus se službou Azure Private Link
description: Naučte se integrovat Azure Service Bus se službou Azure Private Link.
author: spelluru
ms.author: spelluru
ms.date: 03/29/2021
ms.topic: article
ms.openlocfilehash: 833d7e9fb4d517b71aab5039ae9081407eed84cd
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960533"
---
# <a name="allow-access-to-azure-service-bus-namespaces-via-private-endpoints"></a>Povolení přístupu k Azure Service Bus obory názvů prostřednictvím privátních koncových bodů
Služba privátního propojení Azure vám umožňuje přístup ke službám Azure (například Azure Service Bus, Azure Storage a Azure Cosmos DB) a hostovaným zákaznickým a partnerským službám Azure prostřednictvím **privátního koncového bodu** ve vaší virtuální síti.

Privátní koncový bod je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služby se dá směrovat prostřednictvím privátního koncového bodu, takže se nevyžadují žádné brány, zařízení NAT, ExpressRoute, připojení VPN ani veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, která poskytuje nejvyšší úroveň členitosti v řízení přístupu.

Další informace najdete v tématu [co je privátní propojení Azure?](../private-link/private-link-overview.md)

## <a name="important-points"></a>Důležité body
- Tato funkce je podporovaná s úrovní **premium** Azure Service Bus. Další informace o úrovni Premium najdete v článku [Service Bus úrovně pro zasílání zpráv na úrovni Premium a Standard](service-bus-premium-messaging.md) .
- Implementace privátních koncových bodů může ostatním službám Azure zabránit v interakci s Service Bus. V případě výjimky můžete povolit přístup k Service Bus prostředkům z určitých **důvěryhodných služeb** i v případě, že jsou povolené soukromé koncové body. Seznam důvěryhodných služeb najdete v tématu [důvěryhodné služby](#trusted-microsoft-services).

    Následující služby společnosti Microsoft musí být ve virtuální síti.
    - Azure App Service
    - Azure Functions
- Zadejte **alespoň jedno pravidlo IP nebo pravidlo virtuální sítě** pro obor názvů, aby bylo možné provozovat pouze ze zadaných IP adres nebo podsítě virtuální sítě. Pokud neexistují žádná pravidla IP a virtuální sítě, můžete k oboru názvů přistupovat prostřednictvím veřejného Internetu (pomocí přístupového klíče). 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Přidání privátního koncového bodu pomocí Azure Portal

### <a name="prerequisites"></a>Požadavky

Pokud chcete integrovat obor názvů Service Bus s privátním propojením Azure, budete potřebovat následující entity nebo oprávnění:

- Service Bus obor názvů.
- Virtuální síť Azure.
- Podsíť ve virtuální síti. Můžete použít **výchozí** podsíť. 
- Oprávnění vlastníka nebo přispěvatele pro obor názvů Service Bus i pro virtuální síť.

Váš privátní koncový bod a virtuální síť musí být ve stejné oblasti. Když vyberete oblast pro soukromý koncový bod pomocí portálu, automaticky se vyfiltrují jenom virtuální sítě, které jsou v této oblasti. Váš Service Bus obor názvů může být v jiné oblasti. A váš privátní koncový bod používá privátní IP adresu ve vaší virtuální síti.

### <a name="steps"></a>kroky

Pokud již máte existující obor názvů, můžete vytvořit privátní koncový bod pomocí následujících kroků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 
2. Na panelu hledání zadejte **Service Bus**.
3. V seznamu vyberte **obor názvů** , do kterého chcete přidat privátní koncový bod.
2. V nabídce vlevo vyberte v části **Nastavení** možnost **sítě** .     Ve výchozím nastavení je vybraná možnost **vybrané sítě** .
 
    > [!NOTE]
    > Karta **síť** se zobrazí jenom pro obory názvů úrovně **Premium** .  
   
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Stránka sítě – výchozí" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::

    > [!WARNING]
    > Pokud na tuto stránku nepřidáte aspoň jedno pravidlo firewallu protokolu IP nebo virtuální síť, můžete k oboru názvů přistupovat přes veřejný Internet (pomocí přístupového klíče).
   
    Pokud vyberete možnost **všechny sítě** , obor názvů Service Bus akceptuje připojení z libovolné IP adresy (pomocí přístupového klíče). Toto výchozí nastavení odpovídá pravidlu, které přijímá rozsah IP adres 0.0.0.0/0. 

    ![Firewall – vybraná možnost všechny sítě](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
5. Pokud chcete přístup k oboru názvů udělit prostřednictvím privátních koncových bodů, vyberte kartu **připojení privátního koncového bodu** v horní části stránky.
6. V horní části stránky vyberte tlačítko **+ soukromý koncový bod** .

    ![Tlačítko pro přidání privátního koncového bodu](./media/private-link-service/private-link-service-3.png)
7. Na stránce **základy** proveďte tyto kroky: 
    1. Vyberte **předplatné Azure** , ve kterém chcete vytvořit privátní koncový bod. 
    2. Vyberte **skupinu prostředků** pro prostředek privátního koncového bodu.
    3. Zadejte **název** privátního koncového bodu. 
    5. Vyberte **oblast** pro soukromý koncový bod. Váš privátní koncový bod musí být ve stejné oblasti jako vaše virtuální síť, ale může být v jiné oblasti než prostředek privátního propojení, ke kterému se připojujete. 
    6. V dolní části stránky vyberte tlačítko **Další: >prostředku** .

        ![Stránka pro vytvoření privátního koncového bodu – základy](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na stránce **prostředek** použijte následující postup:
    1. V případě metody připojení vyberte **v adresáři připojit k prostředku Azure**, a to následujícím způsobem:   
        1. Vyberte **předplatné Azure** , ve kterém existuje **Service Bus obor názvů** . 
        2. Jako **typ prostředku** vyberte **Microsoft. ServiceBus/obory názvů** pro **typ prostředku**.
        3. V části **prostředek** vyberte obor názvů Service Bus v rozevíracím seznamu. 
        4. Potvrďte, že je **cílový podprostředek** nastavený na **obor názvů**.
        5. V dolní části stránky vyberte tlačítko **Další: >konfigurace** . 
        
            ![Vytvoření stránky privátního koncového bodu – prostředek](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. Pokud vyberete **připojit k prostředku Azure podle ID prostředku nebo aliasu**, postupujte podle těchto kroků:
        1. Zadejte **ID prostředku** nebo **alias**. Může to být ID prostředku nebo alias, který s vámi někdo sdílí. Nejjednodušší způsob, jak získat ID prostředku, je přejít na obor názvů Service Bus v Azure Portal a zkopírovat část identifikátoru URI od `/subscriptions/` . Příklad najdete na následujícím obrázku. 
        2. Pro **cílový dílčí prostředek** zadejte **obor názvů**. Je to typ dílčího prostředku, ke kterému má privátní koncový bod přístup. 
        3. volitelné Zadejte **zprávu požadavku**. Vlastník prostředku se zobrazí tato zpráva během správy připojení privátního koncového bodu. 
        4. Potom v dolní části stránky vyberte tlačítko **Další: >konfigurace** . 

            ![Vytvoření privátního koncového bodu – připojení pomocí ID prostředku](./media/private-link-service/connect-resource-id.png)
9. Na stránce **Konfigurace** vyberte podsíť ve virtuální síti, do které chcete nasadit privátní koncový bod. 
    1. Vyberte **virtuální síť**. V rozevíracím seznamu jsou uvedené jenom virtuální sítě v aktuálně vybraném předplatném a umístění. 
    2. Vyberte **podsíť** ve virtuální síti, kterou jste vybrali. 
    3. V dolní části stránky vyberte tlačítko **Další: značky >** . 

        ![Vytvořit privátní koncový bod – konfigurační stránka](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na stránce **značky** vytvořte všechny značky (názvy a hodnoty), které chcete přidružit k prostředku privátního koncového bodu. Potom v dolní části stránky vyberte tlačítko **Revize + vytvořit** . 
11. Na stránce **zkontrolovat + vytvořit** Zkontrolujte všechna nastavení a výběrem možnosti **vytvořit** vytvořte privátní koncový bod.
    
    ![Vytvoření privátního koncového bodu – kontrola a vytvoření stránky](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Potvrďte, že je vytvořen soukromý koncový bod. Pokud jste vlastníkem prostředku a zvolili jste pro **metodu připojení** možnost **připojit k prostředku Azure v adresáři** , je nutné připojení ke koncovému bodu **automaticky schválit**. Pokud je ve stavu **čekání na vyřízení** , přečtěte si část [Správa privátních koncových bodů pomocí Azure Portal](#manage-private-endpoints-using-azure-portal) .

    ![Privátní koncový bod byl vytvořen.](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="add-a-private-endpoint-using-powershell"></a>Přidání privátního koncového bodu pomocí prostředí PowerShell
Následující příklad ukazuje, jak použít Azure PowerShell k vytvoření připojení privátního koncového bodu k oboru názvů Service Bus.

Váš privátní koncový bod a virtuální síť musí být ve stejné oblasti. Váš Service Bus obor názvů může být v jiné oblasti. A váš privátní koncový bod používá privátní IP adresu ve vaší virtuální síti.

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

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

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```


## <a name="manage-private-endpoints-using-azure-portal"></a>Správa privátních koncových bodů pomocí Azure Portal

Při vytváření privátního koncového bodu musí být připojení schváleno. Pokud je prostředek, pro který vytváříte privátní koncový bod, ve vašem adresáři, můžete žádost o připojení schválit, pokud máte dostatečná oprávnění. Pokud se připojujete k prostředku Azure v jiném adresáři, musíte počkat, až vlastník tohoto prostředku schválí žádost o připojení.

Existují čtyři stavy zřizování:

| Akce služby | Stav privátního koncového bodu příjemce služby | Description |
|--|--|--|
| Žádná | Čekající | Připojení je vytvořeno ručně a čeká na schválení vlastníkem prostředku privátního odkazu. |
| Schválení | Schválené | Připojení bylo automaticky nebo ručně schváleno a je připraveno k použití. |
| Odmítnout | Zamítnuto | Připojení bylo odmítnuto vlastníkem prostředku privátního odkazu. |
| Odebrat | Odpojeno | Připojení bylo odebráno vlastníkem prostředku privátního propojení, soukromý koncový bod bude informativní a měl by být odstraněn pro vyčištění. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Schválení, zamítnutí nebo odebrání připojení privátního koncového bodu

1. Přihlaste se k webu Azure Portal.
1. Na panelu hledání zadejte **Service Bus**.
1. Vyberte **obor názvů** , který chcete spravovat.
1. Vyberte kartu **Sítě**.
5. V závislosti na operaci, kterou chcete provést: schválit, zamítnout nebo odebrat, postupujte podle příslušného oddílu. 

### <a name="approve-a-private-endpoint-connection"></a>Schválení připojení privátního koncového bodu

1. Pokud existují nějaká připojení, která čekají na **vyřízení, zobrazí se ve stavu** zřizování stav připojení. 
2. Vyberte **privátní koncový bod** , který chcete schválit.
3. Vyberte tlačítko **schválit** .

    ![Schválit soukromý koncový bod](./media/private-link-service/private-endpoint-approve.png)
4. Na stránce **schválit připojení** zadejte volitelný **Komentář** a vyberte **Ano**. Pokud vyberete **ne**, nic se nestane. 

    ![Stránka pro schválení připojení](./media/private-link-service/approve-connection-page.png)
5. V seznamu by se měl zobrazit stav připojení se změnilo na **schváleno**. 

    ![Stav připojení – schváleno](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Zamítnutí připojení privátního koncového bodu

1. Pokud existují nějaká připojení privátního koncového bodu, která chcete odmítnout, ať už se jedná o nevyřízenou žádost nebo existující připojení, které bylo schváleno dříve, vyberte připojení ke koncovému bodu a klikněte na tlačítko **odmítnout** .

    ![Tlačítko odmítnout](./media/private-link-service/private-endpoint-reject.png)
2. Na stránce **odmítnout připojení** zadejte volitelný komentář a vyberte **Ano**. Pokud vyberete **ne**, nic se nestane. 

    ![Odmítat stránku připojení](./media/private-link-service/reject-connection-page.png)
3. V seznamu by se měl zobrazit stav připojení byl **odmítnut**. 

    ![Koncový bod byl odmítnut](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Odebrání připojení privátního koncového bodu

1. Pokud chcete odebrat připojení privátního koncového bodu, vyberte ho v seznamu a na panelu nástrojů vyberte **Odebrat** . 

    ![Tlačítko odebrat](./media/private-link-service/remove-endpoint.png)
2. Na stránce **Odstranit připojení** vyberte **Ano** a potvrďte odstranění privátního koncového bodu. Pokud vyberete **ne**, nic se nestane. 

    ![Stránka pro odstranění připojení](./media/private-link-service/delete-connection-page.png)
3. Měl by se zobrazit stav změněno na **Odpojeno**. Pak se v seznamu zobrazí koncový bod. 

## <a name="validate-that-the-private-link-connection-works"></a>Ověření, že připojení privátního propojení funguje

Měli byste ověřit, že prostředky v rámci virtuální sítě privátního koncového bodu se připojují k vašemu Service Bus oboru názvů přes soukromou IP adresu a že mají správnou integraci privátní zóny DNS.

Nejdřív vytvořte virtuální počítač podle kroků v [části Vytvoření virtuálního počítače s Windows v Azure Portal](../virtual-machines/windows/quick-create-portal.md)

Na kartě **síť** : 

1. Zadejte **virtuální síť** a **podsíť**. Musíte vybrat Virtual Network, na které jste nasadili privátní koncový bod.
2. Zadejte prostředek **veřejné IP adresy** .
3. V případě **skupiny zabezpečení sítě síťové karty** vyberte **žádné**.
4. V případě **Vyrovnávání zatížení** vyberte možnost **ne**.

Připojte se k virtuálnímu počítači, otevřete příkazový řádek a spusťte následující příkaz:

```console
nslookup <service-bus-namespace-name>.servicebus.windows.net
```

Měl by se zobrazit výsledek, který vypadá nějak takto: 

```console
Non-authoritative answer:
Name:    <service-bus-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Omezení a faktory návrhu

**Ceny**: informace o cenách najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link/).

**Omezení**: Tato funkce je dostupná ve všech veřejných oblastech Azure.

**Maximální počet privátních koncových bodů na obor názvů Service Bus**: 120.

Další informace najdete v tématu [Služba privátního propojení Azure: omezení](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Další kroky

- Další informace o [privátním propojení Azure](../private-link/private-link-service-overview.md)
- Další informace o [Azure Service Bus](service-bus-messaging-overview.md)
