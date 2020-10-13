---
title: Integrace Azure Relay se službou Azure Private Link
description: Naučte se integrovat Azure Relay se službou Azure Private Link.
ms.date: 09/24/2020
ms.topic: article
ms.openlocfilehash: 10d82fe8e272ed18dcc339830dfef0f71d4b2ddb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91263836"
---
# <a name="integrate-azure-relay-with-azure-private-link-preview"></a>Integrace Azure Relay s privátním odkazem Azure (Preview)
**Služba privátního propojení** Azure umožňuje přístup ke službám Azure (například Azure Relay, Azure Service Bus, Azure Event Hubs, Azure Storage a Azure Cosmos DB) a hostovaným zákaznickým a partnerským službám Azure prostřednictvím privátního koncového bodu ve vaší virtuální síti. Další informace najdete v tématu [co je to Azure Private Link (Preview)?](../private-link/private-link-overview.md)

**Privátní koncový bod** je síťové rozhraní, které umožňuje, aby vaše úlohy spuštěné ve virtuální síti připojovaly soukromě a bezpečně ke službě, která má **prostředek privátního propojení** (například obor názvů přenosu). Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služby se dá směrovat prostřednictvím privátního koncového bodu, takže se nevyžadují žádné brány, zařízení NAT, ExpressRoute, připojení VPN ani veřejné IP adresy. Přenosy mezi vaší virtuální sítí a službou procházejí přes páteřní síť Microsoftu, která eliminuje expozici veřejného Internetu. Úroveň členitosti můžete v řízení přístupu poskytnout tím, že povolíte připojení ke konkrétním oborům názvů Azure Relay. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Přidání privátního koncového bodu pomocí Azure Portal

### <a name="prerequisites"></a>Požadavky
Pokud chcete integrovat obor názvů Azure Relay pomocí privátního propojení Azure (Preview), budete potřebovat následující entity nebo oprávnění:

- Azure Relay obor názvů.
- Virtuální síť Azure.
- Podsíť ve virtuální síti.
- Oprávnění vlastníka nebo přispěvatele ve virtuální síti.

Váš privátní koncový bod a virtuální síť musí být ve stejné oblasti. Když vyberete oblast pro soukromý koncový bod pomocí portálu, automaticky se vyfiltrují jenom virtuální sítě, které jsou v této oblasti. Váš obor názvů může být v jiné oblasti.

Váš privátní koncový bod používá privátní IP adresu ve vaší virtuální síti.

### <a name="steps"></a>Postup
Podrobné pokyny k vytvoření nového oboru názvů Azure Relay a entit v této části najdete v tématu [Vytvoření oboru názvů Azure Relay pomocí Azure Portal](relay-create-namespace-portal.md).

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). 
2. Do vyhledávacího panelu zadejte text **Relay**.
3. V seznamu vyberte **obor názvů** , do kterého chcete přidat privátní koncový bod.
4. V části **Nastavení**vyberte kartu **síť** .
5. V horní části stránky vyberte kartu **připojení privátního koncového bodu (Preview)** .
6. V horní části stránky vyberte tlačítko **+ soukromý koncový bod** .

    ![Tlačítko pro přidání privátního koncového bodu](./media/private-link-service/add-private-endpoint-button.png)
7. Na stránce **základy** proveďte tyto kroky: 
    1. Vyberte **předplatné Azure** , ve kterém chcete vytvořit privátní koncový bod. 
    2. Vyberte **skupinu prostředků** pro prostředek privátního koncového bodu.
    3. Zadejte **název** privátního koncového bodu. 
    5. Vyberte **oblast** pro soukromý koncový bod. Váš privátní koncový bod musí být ve stejné oblasti jako vaše virtuální síť, ale může být v jiné oblasti než obor názvů Azure Relay, ke kterému se připojujete. 
    6. V dolní části stránky vyberte tlačítko **Další: >prostředku ** .

        ![Stránka pro vytvoření privátního koncového bodu – základy](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na stránce **prostředek** použijte následující postup:
    1. Pokud pro metodu připojení vyberete **připojit k prostředku Azure v adresáři my**, máte k oboru názvů oprávnění vlastníka nebo přispěvatele a tento obor názvů je ve stejném adresáři jako soukromý koncový bod, postupujte takto: 
        1. Vyberte **předplatné Azure** , ve kterém existuje **Azure Relay obor názvů** . 
        2. Jako **typ prostředku**vyberte **Microsoft. Relay/obory názvů** pro **typ prostředku**.
        3. V části **prostředek**vyberte obor názvů Relay z rozevíracího seznamu. 
        4. Potvrďte, že je **cílový podprostředek** nastavený na **obor názvů**.
        5. V dolní části stránky vyberte tlačítko **Další: >konfigurace ** . 
        
            ![Vytvoření stránky privátního koncového bodu – prostředek](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Pokud vyberete **připojit k prostředku Azure podle ID prostředku nebo aliasu** , protože obor názvů není ve stejném adresáři jako soukromý koncový bod, postupujte takto:
        1. Zadejte **ID prostředku** nebo **alias**. Může to být ID prostředku nebo alias, který s vámi někdo sdílí. Nejjednodušší způsob, jak získat ID prostředku, je přejít na obor názvů Azure Relay v Azure Portal a zkopírovat část identifikátoru URI od `/subscriptions/` . Tady je příklad: `/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` 
        2. Pro **cílový dílčí prostředek**zadejte **obor názvů**. Je to typ dílčího prostředku, ke kterému má privátní koncový bod přístup.
        3. volitelné Zadejte **zprávu požadavku**. Vlastník prostředku se zobrazí tato zpráva během správy připojení privátního koncového bodu.
        4. Potom v dolní části stránky vyberte tlačítko **Další: >konfigurace ** .

            ![Vytvoření privátního koncového bodu – připojení pomocí ID prostředku](./media/private-link-service/connect-resource-id.png)
9. Na stránce **Konfigurace** vyberte podsíť ve virtuální síti, do které chcete nasadit privátní koncový bod. 
    1. Vyberte **virtuální síť**. V rozevíracím seznamu jsou uvedené jenom virtuální sítě v aktuálně vybraném předplatném a umístění. 
    2. Vyberte **podsíť** ve virtuální síti, kterou jste vybrali. 
    3. Povolte **integraci s privátní zónou DNS** , pokud chcete integrovat svůj privátní koncový bod s privátní zónou DNS. 
    
        Abyste mohli soukromě propojit s vaším soukromým koncovým bodem, budete potřebovat záznam DNS. Doporučujeme integrovat privátní koncový bod s **privátní zónou DNS**. Můžete také využít vlastní servery DNS nebo vytvořit záznamy DNS pomocí hostitelských souborů na virtuálních počítačích. Další informace najdete v tématu [Konfigurace DNS privátního koncového bodu Azure](../private-link/private-endpoint-dns.md). V tomto příkladu je vybraná možnost **integrovat s privátní zónou DNS** a vytvoří se pro vás privátní zóna DNS. 
    3. V dolní části stránky vyberte tlačítko **Další: značky >** . 

        ![Vytvořit privátní koncový bod – konfigurační stránka](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na stránce **značky** vytvořte všechny značky (názvy a hodnoty), které chcete přidružit k privátnímu koncovému bodu, a privátní zóně DNS (Pokud jste povolili možnost). Potom v dolní části stránky vyberte tlačítko **Revize + vytvořit** . 
11. Na stránce **zkontrolovat + vytvořit**Zkontrolujte všechna nastavení a výběrem možnosti **vytvořit** vytvořte privátní koncový bod.
    
    ![Vytvoření privátního koncového bodu – kontrola a vytvoření stránky](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Na stránce **privátní koncový bod** můžete zobrazit stav připojení privátního koncového bodu. Pokud jste vlastníkem oboru názvů Relay nebo máte oprávnění spravovat přístup a v možnosti adresář pro **metodu připojení**jste zvolili možnost **připojit k prostředku Azure v adresáři** , je nutné připojení ke koncovému bodu **automaticky schválit**. Pokud je ve stavu **čekání na vyřízení** , přečtěte si část [Správa privátních koncových bodů pomocí Azure Portal](#manage-private-endpoints-using-azure-portal) .

    ![Stránka privátního koncového bodu](./media/private-link-service/private-endpoint-page.png)
13. Přejděte zpět na stránku **síť** **oboru názvů**a přepněte na kartu **připojení privátního koncového bodu (Preview)** . Měl by se zobrazit soukromý koncový bod, který jste vytvořili. 

    ![Privátní koncový bod byl vytvořen.](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Přidání privátního koncového bodu pomocí prostředí PowerShell
Následující příklad ukazuje, jak použít Azure PowerShell k vytvoření připojení privátního koncového bodu k oboru názvů Azure Relay.

Váš privátní koncový bod a virtuální síť musí být ve stejné oblasti. Váš Azure Relay obor názvů může být v jiné oblasti. A váš privátní koncový bod používá privátní IP adresu ve vaší virtuální síti.

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

# create a relay namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Properties @{} -ResourceType "Microsoft.Relay/namespaces" 

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

Při vytváření privátního koncového bodu musí být připojení schváleno. Pokud se prostředek (obor názvů přenosu), pro který vytváříte privátní koncový bod, nachází ve vašem adresáři, můžete žádost o připojení schválit v případě, že máte oprávnění ke správě v oboru názvů přenosu. Pokud se připojujete k oboru názvů přenosu, pro který nemáte přístup pro správu, musíte počkat, až vlastník tohoto prostředku schválí žádost o připojení.

Existují čtyři stavy zřizování:

| Akce služby | Stav privátního koncového bodu příjemce služby | Description |
|--|--|--|
| Žádná | Čekající | Připojení je vytvořeno ručně a čeká na schválení od Azure Relay vlastníka oboru názvů. |
| Schválení | Schválené | Připojení bylo automaticky nebo ručně schváleno a je připraveno k použití. |
| Odmítnout | Zamítnuto | Připojení Azure Relay vlastník oboru názvů odmítnuto. |
| Odebrat | Propojení | Připojení odebral vlastník oboru názvů Azure Relay, privátní koncový bod se bude zobrazovat jako informativní a měl by se odstranit pro vyčištění. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Schválení, zamítnutí nebo odebrání připojení privátního koncového bodu

1. Přihlaste se k portálu Azure.
1. Do panelu hledání zadejte text **Relay**.
1. Vyberte **obor názvů** , který chcete spravovat.
1. Vyberte kartu **síť** .
5. V závislosti na operaci, kterou chcete provést: schválit, zamítnout nebo odebrat, postupujte podle příslušného oddílu. 

### <a name="approve-a-private-endpoint-connection"></a>Schválení připojení privátního koncového bodu

1. Pokud existují nějaká připojení, která čekají na **vyřízení, zobrazí se ve stavu** zřizování stav připojení. 
2. Vyberte **privátní koncový bod** , který chcete schválit.
3. Vyberte tlačítko **schválit** .

    ![Schválit soukromý koncový bod](./media/private-link-service/private-endpoint-approve.png)
4. Na stránce **schválit připojení** zadejte volitelný **Komentář**a vyberte **Ano**. Pokud vyberete **ne**, nic se nestane. 

    ![Stránka pro schválení připojení](./media/private-link-service/approve-connection-page.png)
5. V seznamu by se měl zobrazit stav připojení se změnilo na **schváleno**.

### <a name="reject-a-private-endpoint-connection"></a>Zamítnutí připojení privátního koncového bodu

1. Pokud existují nějaká připojení privátního koncového bodu, která chcete odmítnout, ať už se jedná o nevyřízenou žádost nebo existující připojení, které bylo schváleno dříve, vyberte připojení ke koncovému bodu a klikněte na tlačítko **odmítnout** .

    ![Tlačítko odmítnout](./media/private-link-service/private-endpoint-reject.png)
2. Na stránce **odmítnout připojení** zadejte volitelný komentář a vyberte **Ano**. Pokud vyberete **ne**, nic se nestane. 

    ![Odmítat stránku připojení](./media/private-link-service/reject-connection-page.png)
3. V seznamu by se měl zobrazit stav připojení byl **odmítnut**.


### <a name="remove-a-private-endpoint-connection"></a>Odebrání připojení privátního koncového bodu

1. Pokud chcete odebrat připojení privátního koncového bodu, vyberte ho v seznamu a na panelu nástrojů vyberte **Odebrat** . 

    ![Tlačítko odebrat](./media/private-link-service/remove-endpoint.png)
2. Na stránce **Odstranit připojení** vyberte **Ano** a potvrďte odstranění privátního koncového bodu. Pokud vyberete **ne**, nic se nestane. 

    ![Stránka pro odstranění připojení](./media/private-link-service/delete-connection-page.png)
3. Měl by se zobrazit stav změněno na **Odpojeno**. Pak se v seznamu zobrazí koncový bod. 

## <a name="validate-that-the-private-link-connection-works"></a>Ověření, že připojení privátního propojení funguje
Měli byste ověřit, že prostředky v rámci virtuální sítě privátního koncového bodu se připojují k vašemu oboru názvů Azure Relay přes jeho privátní IP adresu.

Pro tento test vytvořte virtuální počítač podle kroků uvedených v části [Vytvoření virtuálního počítače s Windows v Azure Portal](../virtual-machines/windows/quick-create-portal.md)

Na kartě **síť** : 

1. Zadejte **virtuální síť** a **podsíť**. Musíte vybrat Virtual Network, na které jste nasadili privátní koncový bod.
2. Zadejte prostředek **veřejné IP adresy** .
3. V případě **skupiny zabezpečení sítě síťové karty**vyberte **žádné**.
4. V případě **Vyrovnávání zatížení**vyberte možnost **ne**.

Připojte se k virtuálnímu počítači a otevřete příkazový řádek a spusťte následující příkaz:

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

Měl by se zobrazit výsledek, který vypadá nějak takto: 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Omezení a faktory návrhu

### <a name="design-considerations"></a>Na co dát pozor při navrhování
- Privátní koncový bod pro Azure Relay je ve **verzi Public Preview**. 
- Informace o cenách najdete v tématu [ceny pro privátní propojení Azure (Preview)](https://azure.microsoft.com/pricing/details/private-link/).

### <a name="limitations"></a>Omezení 
- Maximální počet privátních koncových bodů na obor názvů Azure Relay: 64.
- Maximální počet Azure Relay oborů názvů se soukromými koncovými body na předplatné: 64.
- Pravidla skupiny zabezpečení sítě (NSG) a trasy User-Defined se nevztahují na soukromý koncový bod. Další informace najdete v tématu [Služba pro privátní připojení Azure: omezení](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Další kroky

- Další informace o [privátním propojení Azure (Preview)](../private-link/private-link-service-overview.md)
- Další informace o [Azure Relay](relay-what-is-it.md)
