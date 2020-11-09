---
title: Azure Firewall nastavení DNS
description: Azure Firewall můžete nakonfigurovat pomocí serveru DNS a nastavení serveru proxy DNS.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: ad0ac040b510783656617ddbf2063cd94c80aae7
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380942"
---
# <a name="azure-firewall-dns-settings"></a>Azure Firewall nastavení DNS

Můžete nakonfigurovat vlastní server DNS a Povolit proxy server DNS pro Azure Firewall. Tato nastavení můžete nakonfigurovat při nasazení brány firewall nebo později ze stránky **nastavení DNS** .

## <a name="dns-servers"></a>Servery DNS

Server DNS udržuje a překládá názvy domén na IP adresy. Ve výchozím nastavení používá Azure Firewall k překladu názvů Azure DNS. Nastavení **serveru DNS** umožňuje konfigurovat vlastní servery DNS pro překlad Azure firewall názvů. Můžete nakonfigurovat jeden nebo více serverů.

> [!NOTE]
> U bran Azure firewall spravovaných pomocí Azure Firewall Manageru jsou nastavení DNS nakonfigurovaná v přidružených zásadách Azure Firewall.

### <a name="configure-custom-dns-servers---azure-portal"></a>Konfigurace vlastních serverů DNS – Azure Portal

1. V části **nastavení** Azure firewall vyberte **nastavení DNS**.
2. V části **servery DNS** můžete zadat nebo přidat existující servery DNS, které jste dříve zadali v Virtual Network.
3. Vyberte **Uložit**.
4. Brána firewall nyní směruje provoz DNS na zadaný server DNS pro překlad IP adres.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="Servery DNS":::

### <a name="configure-custom-dns-servers---azure-cli"></a>Konfigurace vlastních serverů DNS – Azure CLI

Následující příklad aktualizuje Azure Firewall vlastními servery DNS pomocí rozhraní příkazového řádku Azure CLI.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> Příkaz `az network firewall` vyžaduje, `azure-firewall` aby bylo nainstalované rozšíření Azure CLI. Dá se nainstalovat pomocí příkazu `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers---azure-powershell"></a>Konfigurace vlastních serverů DNS – Azure PowerShell

Následující příklad aktualizuje Azure Firewall vlastními servery DNS pomocí Azure PowerShell.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>Proxy server DNS

Azure Firewall můžete nakonfigurovat tak, aby fungoval jako proxy server DNS. DNS proxy funguje jako prostředník pro žádosti DNS z klientských virtuálních počítačů na server DNS. Pokud nakonfigurujete vlastní server DNS, měli byste povolit proxy server DNS, aby nedocházelo k neshodě překladu názvů DNS, a povolit filtrování plně kvalifikovaného názvu domény v síťových pravidlech.

Pokud nepovolíte proxy server DNS, žádosti DNS z klienta se můžou cestovat na server DNS v jinou dobu nebo vracet jinou odpověď v porovnání s bránou firewall. Proxy DNS umísťuje Azure Firewall v cestě klientských požadavků, aby nedocházelo k nekonzistenci.

Existují dva typy funkcí ukládání do mezipaměti, ke kterým dochází, když Azure Firewall je proxy DNS:

- Kladná mezipaměť – překlad DNS je úspěšný. Brána firewall používá hodnotu TTL (Time-to-Live) paketu nebo objektu. 

- Negativní mezipaměť – překlad DNS má za následek neodpověď nebo žádné vyřešení. Brána firewall tyto informace ukládá do mezipaměti po dobu jedné hodiny.

Proxy server DNS ukládá všechny vyřešené IP adresy z plně kvalifikovaných názvů domén v síťových pravidlech. Jako osvědčený postup použijte plně kvalifikované názvy domény, které se překládají na jednu IP adresu.  

### <a name="dns-proxy-configuration"></a>Konfigurace proxy serveru DNS

Konfigurace proxy serveru DNS vyžaduje tři kroky:
1. Povolte DNS proxy v Azure Firewall nastavení DNS.
2. Volitelně můžete nakonfigurovat vlastní server DNS nebo použít poskytnutý výchozí.
3. Nakonec musíte nakonfigurovat privátní IP adresu Azure Firewall jako vlastní adresu DNS v nastavení serveru DNS virtuální sítě. Tím se zajistí přesměrování provozu DNS na Azure Firewall.

#### <a name="configure-dns-proxy---azure-portal"></a>Konfigurace proxy serveru DNS – Azure Portal

Pokud chcete nakonfigurovat proxy server DNS, musíte nakonfigurovat nastavení serveru DNS virtuální sítě tak, aby používalo privátní IP adresu brány firewall. Potom povolte DNS proxy v Azure Firewall **nastavení DNS**.

##### <a name="configure-virtual-network-dns-servers"></a>Konfigurace serverů DNS virtuální sítě 

1. Vyberte virtuální síť, ve které se budou směrovat přenosy DNS prostřednictvím Azure Firewall.
2. V části **Nastavení** vyberte **servery DNS**.
3. V části **servery DNS** vyberte **vlastní** .
4. Zadejte privátní IP adresu brány firewall.
5. Vyberte **Uložit**.
6. Restartujte virtuální počítače, které jsou připojené k virtuální síti, takže se jim přiřadí nové nastavení serveru DNS. Virtuální počítače budou nadále používat aktuální nastavení DNS, dokud nebudou restartováni.

##### <a name="enable-dns-proxy"></a>Povolit proxy server DNS

1. Vyberte Azure Firewall.
2. V části **Nastavení** vyberte **nastavení DNS**.
3. Ve výchozím nastavení je **proxy server DNS** zakázaný. Když je tato možnost povolená, brána firewall naslouchá na portu 53 a přepošle požadavky DNS na nakonfigurované servery DNS.
4. Zkontrolujte konfiguraci **serverů DNS** a ujistěte se, že nastavení jsou vhodná pro vaše prostředí.
5. Vyberte **Uložit**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="Proxy server DNS":::

#### <a name="configure-dns-proxy---azure-cli"></a>Konfigurace proxy serveru DNS – Azure CLI

Pomocí Azure CLI můžete nakonfigurovat nastavení proxy serveru DNS v Azure Firewall a aktualizovat virtuální sítě tak, aby používaly Azure Firewall jako server DNS.

##### <a name="configure-virtual-network-dns-servers"></a>Konfigurace serverů DNS virtuální sítě

Tento příklad nakonfiguruje virtuální síť tak, aby používala Azure Firewall jako server DNS.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>Povolit proxy server DNS

Tento příklad povoluje funkci proxy serveru DNS v Azure Firewall.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>Konfigurace proxy serveru DNS – Azure PowerShell

Pomocí Azure PowerShell můžete nakonfigurovat nastavení proxy serveru DNS v Azure Firewall a aktualizovat virtuální sítě tak, aby používaly Azure Firewall jako server DNS.

##### <a name="configure-virtual-network-dns-servers"></a>Konfigurace serverů DNS virtuální sítě

 Tento příklad nakonfiguruje virtuální síť tak, aby používala Azure Firewall jako server DNS.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>Povolit proxy server DNS

Tento příklad povoluje funkci proxy serveru DNS v Azure Firewall.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Další kroky

[Filtrování plně kvalifikovaného názvu domény v síťových pravidlech](fqdn-filtering-network-rules.md)
