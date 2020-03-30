---
title: Integrace Azure Service Bus se službou Azure Private Link Service
description: Zjistěte, jak integrovat Azure Service Bus se službou Azure Private Link Service
services: service-bus-messaging
author: spelluru
ms.author: spelluru
ms.date: 03/13/2020
ms.service: service-bus-messaging
ms.topic: article
ms.openlocfilehash: b8c4248b7275ac96acce96f890f6ff0148116f48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478003"
---
# <a name="integrate-azure-service-bus-with-azure-private-link-preview"></a>Integrace Azure Service Bus s Privátním propojením Azure (preview)

Služba Azure Private Link Service umožňuje přístup ke službám Azure (například Azure Service Bus, Azure Storage a Azure Cosmos DB) a službám hostovaným zákazníkům/partnerům Azure přes **privátní koncový bod** ve vaší virtuální síti.

Privátní koncový bod je síťové rozhraní, které vás soukromě a bezpečně připojí ke službě využívající Azure Private Link. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě, efektivně uvedení služby do virtuální sítě. Veškerý provoz na službu lze směrovat přes soukromý koncový bod, takže nejsou potřeba žádné brány, zařízení NAT, připojení ExpressRoute nebo VPN nebo veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, což vám nejvyšší úroveň rozlišovací schopnost v řízení přístupu.

Další informace najdete v tématu [Co je privátní propojení Azure?](../private-link/private-link-overview.md)

> [!NOTE]
> Tato funkce je podporovaná s **prémiovou** úrovní Azure Service Bus. Další informace o úrovni premium najdete v článku [úrovně service bus premium a standardní zasílání zpráv.](service-bus-premium-messaging.md)
>
> Tato funkce je aktuálně ve **verzi Preview**. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Přidání privátního koncového bodu pomocí portálu Azure

### <a name="prerequisites"></a>Požadavky

Chcete-li integrovat obor názvů Service Bus s Privátním propojením Azure, budete potřebovat následující entity nebo oprávnění:

- Obor názvů služby Service Bus.
- Virtuální síť Azure.
- Podsíť ve virtuální síti.
- Oprávnění vlastníka nebo přispěvatele pro obor názvů Service Bus i virtuální síť.

Váš soukromý koncový bod a virtuální síť musí být ve stejné oblasti. Když vyberete oblast pro soukromý koncový bod pomocí portálu, bude automaticky filtrovat pouze virtuální sítě, které jsou v této oblasti. Obor názvů service bus může být v jiné oblasti. A váš soukromý koncový bod používá privátní IP adresu ve vaší virtuální síti.

### <a name="steps"></a>kroky

Pokud již máte existující obor názvů, můžete vytvořit soukromý koncový bod následujícím postupem:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
2. Na panelu hledání zadejte **service bus**.
3. Vyberte **obor názvů** ze seznamu, do kterého chcete přidat soukromý koncový bod.
4. V části **Nastavení**vyberte kartu **Síť** .
5. Výběr karty **Soukromá připojení koncového bodu (náhled)** v horní části stránky
6. V horní části stránky vyberte tlačítko **+ Soukromý koncový bod.**

    ![Tlačítko Přidat soukromý koncový bod](./media/private-link-service/private-link-service-3.png)
7. Na stránce **Základy** postupujte takto: 
    1. Vyberte **předplatné Azure,** ve kterém chcete vytvořit privátní koncový bod. 
    2. Vyberte **skupinu prostředků** pro prostředek soukromého koncového bodu.
    3. Zadejte **název** privátního koncového bodu. 
    5. Vyberte **oblast** pro soukromý koncový bod. Váš soukromý koncový bod musí být ve stejné oblasti jako virtuální síť, ale může být v jiné oblasti, protože prostředek privátního propojení, ke kterému se připojujete. 
    6. Vyberte **další: Tlačítko >zdroje** v dolní části stránky.

        ![Vytvořit soukromý koncový bod – stránku Základy](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na stránce **Zdroj** postupujte takto:
    1. Pokud pro metodu připojení vyberete **možnost Připojit se k prostředku Azure v mém adresáři**, postupujte takto:   
        1. Vyberte **předplatné Azure,** ve kterém existuje **obor názvů Service Bus.** 
        2. V **případě typu Resource**vyberte **položku Microsoft.ServiceBus/namespaces** pro **typ prostředku**.
        3. V **části Prostředek**vyberte obor názvů služby Service Bus z rozevíracího seznamu. 
        4. Zkontrolujte, zda je **cílový podprostředek** nastaven na **obor názvů**.
        5. Vyberte **další: Tlačítko >konfigurace** v dolní části stránky. 
        
            ![Vytvořit soukromý koncový bod – stránku prostředku](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. Pokud vyberete **Připojit k prostředku Azure podle ID prostředku nebo aliasu**, postupujte takto:
        1. Zadejte **ID prostředku** nebo **alias**. Může se jedná o ID prostředku nebo alias, který s vámi někteří sdílejí.
        2. Do **pole Cílový dílčí prostředek**zadejte obor **názvů**. Jedná se o typ dílčího prostředku, ke kterému má váš soukromý koncový bod přístup. 
        3. (nepovinné) Zadejte **zprávu o požadavku**. Vlastník prostředku vidí tuto zprávu při správě připojení soukromého koncového bodu. 
        4. Potom vyberte **Další: Tlačítko >konfigurace** v dolní části stránky. 

            ![Vytvořit soukromý koncový bod – připojení pomocí ID prostředku](./media/private-link-service/connect-resource-id.png)
9. Na stránce **Konfigurace** vyberete podsíť ve virtuální síti, kam chcete nasadit privátní koncový bod. 
    1. Vyberte **virtuální síť**. V rozevíracím seznamu jsou uvedeny pouze virtuální sítě v aktuálně vybraném předplatném a umístění. 
    2. Vyberte **podsíť** ve virtuální síti, kterou jste vybrali. 
    3. Vyberte **Další: Značky >** tlačítko v dolní části stránky. 

        ![Vytvořit soukromý koncový bod – stránku konfigurace](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na stránce **Značky** vytvořte všechny značky (názvy a hodnoty), které chcete přidružit k prostředku soukromého koncového bodu. Potom vyberte **tlačítko Revize + vytvořit** v dolní části stránky. 
11. V části **Revize + vytvořit**zkontrolujte všechna nastavení a vyberte **Vytvořit,** chcete-li vytvořit soukromý koncový bod.
    
    ![Vytvořit soukromý koncový bod – zkontrolovat a vytvořit stránku](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Zkontrolujte, zda je vytvořen soukromý koncový bod. Pokud jste vlastníkem prostředku a měl vybrané **Připojit k prostředku Azure v mém adresáři** možnost pro **metodu připojení**, připojení koncového bodu by měl být automaticky **schválen**. Pokud je ve stavu čekající na **vyřízení,** podívejte se na [část Správa privátních koncových bodů pomocí portálu Azure.](#manage-private-endpoints-using-azure-portal)

    ![Byl vytvořen soukromý koncový bod.](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Přidání privátního koncového bodu pomocí PowerShellu
Následující příklad ukazuje, jak pomocí Azure PowerShell uvytvořit privátní koncového bodu připojení k oboru názvů Service Bus.

Váš soukromý koncový bod a virtuální síť musí být ve stejné oblasti. Obor názvů service bus může být v jiné oblasti. A váš soukromý koncový bod používá privátní IP adresu ve vaší virtuální síti.

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


## <a name="manage-private-endpoints-using-azure-portal"></a>Správa privátních koncových bodů pomocí Portálu Azure

Při vytváření soukromého koncového bodu musí být připojení schváleno. Pokud je prostředek, pro který vytváříte soukromý koncový bod, ve vašem adresáři, můžete žádost o připojení schválit za předpokladu, že máte dostatečná oprávnění. Pokud se připojujete k prostředku Azure v jiném adresáři, musíte počkat na vlastníka tohoto prostředku schválit váš požadavek na připojení.

Existují čtyři stavy zřizování:

| Akce služby | Stav koncového bodu příjemce služby | Popis |
|--|--|--|
| Žádný | Čekající na vyřízení | Připojení je vytvořeno ručně a čeká na schválení od vlastníka prostředku private link. |
| Schválení | Schválené | Připojení bylo automaticky nebo ručně schváleno a je připraveno k použití. |
| Odmítnout | Rejected | Připojení bylo odmítnuto vlastníkem prostředku soukromého propojení. |
| Odebrat | Odpojen | Připojení bylo odebráno vlastníkem prostředku soukromého propojení, soukromý koncový bod se stane informativní a měl by být odstraněn pro vyčištění. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Schválení, odmítnutí nebo odebrání připojení soukromého koncového bodu

1. Přihlaste se k portálu Azure.
1. Na panelu hledání zadejte **service bus**.
1. Vyberte **obor názvů,** který chcete spravovat.
1. Vyberte kartu **Síť.**
5. Přejděte do příslušné části níže na základě operace, kterou chcete schválit, odmítnout nebo odebrat. 

### <a name="approve-a-private-endpoint-connection"></a>Schválení připojení privátního koncového bodu

1. Pokud existují všechna připojení, která čekají na vyřízení, zobrazí se připojení uvedené s **Čekající** na vyřízení ve stavu zřizování. 
2. Vyberte **soukromý koncový bod,** který chcete schválit.
3. Vyberte **tlačítko Schválit.**

    ![Schválit soukromý koncový bod](./media/private-link-service/private-endpoint-approve.png)
4. Na stránce **Schválit připojení** zadejte volitelný **komentář**a vyberte **Ano**. Pokud vyberete **možnost Ne**, nic se nestane. 

    ![Stránka Schválit připojení](./media/private-link-service/approve-connection-page.png)
5. Stav připojení v seznamu by měl být změněn na **Schváleno**. 

    ![Stav připojení - schváleno](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Odmítnutí připojení privátního koncového bodu

1. Pokud existují nějaká soukromá připojení koncového bodu, která chcete odmítnout, ať už se jedná o nevyřízený požadavek nebo existující připojení, které bylo schváleno dříve, vyberte připojení koncového bodu a klepněte na tlačítko **Odmítnout.**

    ![Tlačítko Odmítnout](./media/private-link-service/private-endpoint-reject.png)
2. Na stránce **Odmítnout připojení** zadejte volitelný komentář a vyberte **Ano**. Pokud vyberete **možnost Ne**, nic se nestane. 

    ![Odmítnout stránku připojení](./media/private-link-service/reject-connection-page.png)
3. Měl by se zobrazit stav připojení v seznamu **změněno Odmítnuto**. 

    ![Koncový bod byl odmítnut.](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Odebrání privátního připojení koncového bodu

1. Chcete-li odebrat soukromé připojení koncového bodu, vyberte ho v seznamu a na panelu nástrojů vyberte **Odebrat.** 

    ![Tlačítko Odebrat](./media/private-link-service/remove-endpoint.png)
2. Na stránce **Odstranit připojení** vyberte **Ano,** chcete-li potvrdit odstranění soukromého koncového bodu. Pokud vyberete **možnost Ne**, nic se nestane. 

    ![Odstranit stránku připojení](./media/private-link-service/delete-connection-page.png)
3. Měl by se stav změnit na **Odpojeno**. Potom uvidíte koncový bod zmizí ze seznamu. 

## <a name="validate-that-the-private-link-connection-works"></a>Ověření, zda funguje privátní připojení

Měli byste ověřit, že prostředky v rámci stejné podsítě prostředku soukromého koncového bodu se připojují k oboru názvů Service Bus přes privátní IP adresu a že mají správnou privátní integraci zóny DNS.

Nejdřív vytvořte virtuální počítač podle kroků v [části Vytvoření virtuálního počítače s Windows na webu Azure Portal.](../virtual-machines/windows/quick-create-portal.md)

Na kartě **Síť:**

1. Zadejte **virtuální síť** a **podsíť**. Můžete vytvořit novou virtuální síť nebo vybrat existující. Pokud vyberete existující, ujistěte se, že se oblast shoduje.
1. Zadejte veřejný prostředek **IP.**
1. V **případě skupiny zabezpečení sítě NIC**vyberte možnost **Žádný**.
1. V **části Vyrovnávání zatížení**vyberte možnost **Ne**.

Otevřete příkazový řádek a spusťte následující příkaz:

```console
nslookup <your-service-bus-namespace-name>.servicebus.windows.net
```

Pokud spustíte příkaz vyhledávání ns k vyřešení ip adresy oboru názvů service bus přes veřejný koncový bod, zobrazí se výsledek, který vypadá takto:

```console
c:\ >nslookup <your-service-bus-namespace-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

Pokud spustíte příkaz vyhledávání ns k vyřešení ip adresy oboru názvů service bus přes soukromý koncový bod, zobrazí se výsledek, který vypadá takto:

```console
c:\ >nslookup your_service-bus-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Omezení a aspekty návrhu

**Ceny**: Informace o cenách najdete v článku [Ceny Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

**Omezení:** Privátní koncový bod pro Azure Service Bus je ve verzi Public Preview. Tato funkce je dostupná ve všech veřejných oblastech Azure.

**Maximální počet soukromých koncových bodů na obor názvů služby Service Bus:** 120.

Další informace najdete v tématu [Azure Private Link service: Omezení](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Další kroky

- Další informace o [privátním propojení Azure](../private-link/private-link-service-overview.md)
- Další informace o [Azure Service Bus](service-bus-messaging-overview.md)
