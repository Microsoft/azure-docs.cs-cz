---
title: Azure Firewall nastavení DNS
description: Azure Firewall můžete nakonfigurovat pomocí serveru DNS a nastavení serveru proxy DNS.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: d6a79e87e9999dd520358e0722011cf4e54d8c63
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546240"
---
# <a name="azure-firewall-dns-settings"></a>Azure Firewall nastavení DNS

Můžete nakonfigurovat vlastní server DNS a Povolit proxy server DNS pro Azure Firewall. Nakonfigurujte tato nastavení, když nasadíte bránu firewall, nebo je nakonfigurujte později ze stránky **nastavení DNS** .

## <a name="dns-servers"></a>Servery DNS

Server DNS udržuje a překládá názvy domén na IP adresy. Ve výchozím nastavení používá Azure Firewall k překladu názvů Azure DNS. Nastavení **serveru DNS** umožňuje konfigurovat vlastní servery DNS pro překlad Azure firewall názvů. Můžete nakonfigurovat jeden server nebo víc serverů.

> [!NOTE]
> V případě instancí Azure Firewall spravovaných pomocí nástroje Azure Firewall Manager jsou nastavení DNS nakonfigurovaná v přidružených zásadách Azure Firewall.

### <a name="configure-custom-dns-servers---azure-portal"></a>Konfigurace vlastních serverů DNS – Azure Portal

1. V části **nastavení** Azure firewall vyberte **nastavení DNS**.
2. V části **servery DNS** můžete zadat nebo přidat existující servery DNS, které jste dříve zadali ve vaší virtuální síti.
3. Vyberte **Uložit**.

Brána firewall nyní směruje provoz DNS na zadané servery DNS pro překlad názvů.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="Snímek obrazovky S nastavením pro servery D N S":::

### <a name="configure-custom-dns-servers---azure-cli"></a>Konfigurace vlastních serverů DNS – Azure CLI

Následující příklad aktualizuje Azure Firewall s vlastními servery DNS pomocí rozhraní příkazového řádku Azure CLI.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> Příkaz `az network firewall` vyžaduje, `azure-firewall` aby bylo nainstalované rozšíření Azure CLI. Můžete ji nainstalovat pomocí příkazu `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers---azure-powershell"></a>Konfigurace vlastních serverů DNS – Azure PowerShell

Následující příklad aktualizuje Azure Firewall s vlastními servery DNS pomocí Azure PowerShell.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>Proxy server DNS

Azure Firewall můžete nakonfigurovat tak, aby fungoval jako proxy server DNS. Proxy server DNS je prostředník pro žádosti DNS z klientských virtuálních počítačů na server DNS. Pokud nakonfigurujete vlastní server DNS, povolte proxy server DNS, aby se zabránilo neshodě překladu názvů DNS, a v síťových pravidlech povolte filtrování plně kvalifikovaného názvu domény (FQDN).

:::image type="content" source="media/dns-settings/dns-proxy-2.png" alt-text="Konfigurace proxy serveru d N S použitím vlastního serveru D N S.":::


Pokud proxy server DNS nepovolíte, můžou žádosti DNS z klienta přejít na server DNS v jinou dobu nebo vracet jinou odpověď v porovnání s bránou firewall. Proxy DNS umísťuje Azure Firewall v cestě klientských požadavků, aby nedocházelo k nekonzistenci.

Když je Azure Firewall proxy DNS, můžou být dostupné dva typy funkcí ukládání do mezipaměti:

- **Kladná mezipaměť**: překlad DNS byl úspěšný. Brána firewall používá hodnotu TTL (Time to Live) paketu nebo objektu. 

- **Záporná mezipaměť**: výsledkem překladu DNS není žádná odpověď nebo žádné řešení. Brána firewall tyto informace ukládá do mezipaměti po dobu jedné hodiny.

Proxy server DNS ukládá všechny vyřešené IP adresy z plně kvalifikovaných názvů domén v síťových pravidlech. Jako osvědčený postup použijte plně kvalifikované názvy domény, které se překládají na jednu IP adresu.  

### <a name="dns-proxy-configuration"></a>Konfigurace proxy serveru DNS

Konfigurace proxy serveru DNS vyžaduje tři kroky:
1. Povolte DNS proxy v Azure Firewall nastavení DNS.
2. Volitelně můžete nakonfigurovat vlastní server DNS nebo použít poskytnutý výchozí.
3. Nakonfigurujte Azure Firewall privátní IP adresu jako vlastní adresu DNS v nastavení serveru DNS virtuální sítě. Toto nastavení zajišťuje, aby se přenosy DNS směrovaly na Azure Firewall.

#### <a name="configure-dns-proxy---azure-portal"></a>Konfigurace proxy serveru DNS – Azure Portal

Pokud chcete nakonfigurovat proxy server DNS, musíte nakonfigurovat nastavení serveru DNS virtuální sítě tak, aby používalo privátní IP adresu brány firewall. Pak povolte DNS proxy v **nastavení Azure firewall DNS**.

##### <a name="configure-virtual-network-dns-servers"></a>Konfigurace serverů DNS virtuální sítě 

1. Vyberte virtuální síť, ve které budou přenosy dat DNS směrovány prostřednictvím instance Azure Firewall.
2. V části **Nastavení** vyberte **servery DNS**.
3. V části **servery DNS** vyberte **vlastní**.
4. Zadejte privátní IP adresu brány firewall.
5. Vyberte **Uložit**.
6. Restartujte virtuální počítače, které jsou připojené k virtuální síti, aby jim bylo přiřazeno nové nastavení serveru DNS. Virtuální počítače budou nadále používat aktuální nastavení DNS, dokud nebudou restartováni.

##### <a name="enable-dns-proxy"></a>Povolit proxy server DNS

1. Vyberte instanci Azure Firewall.
2. V části **Nastavení** vyberte **nastavení DNS**.
3. Ve výchozím nastavení je **proxy server DNS** zakázaný. Když je toto nastavení povolené, brána firewall naslouchá na portu 53 a přepošle požadavky DNS na nakonfigurované servery DNS.
4. Zkontrolujte konfiguraci **serverů DNS** a ujistěte se, že nastavení jsou vhodná pro vaše prostředí.
5. Vyberte **Uložit**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="Snímek obrazovky s nastavením pro proxy D N S":::

#### <a name="configure-dns-proxy---azure-cli"></a>Konfigurace proxy serveru DNS – Azure CLI

Pomocí rozhraní příkazového řádku Azure můžete nakonfigurovat nastavení proxy serveru DNS v Azure Firewall. Můžete ji také použít k aktualizaci virtuálních sítí na použití Azure Firewall jako serveru DNS.

##### <a name="configure-virtual-network-dns-servers"></a>Konfigurace serverů DNS virtuální sítě

Následující příklad nakonfiguruje virtuální síť tak, aby používala Azure Firewall jako server DNS.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>Povolit proxy server DNS

Následující příklad povolí funkci proxy serveru DNS v Azure Firewall.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>Konfigurace proxy serveru DNS – Azure PowerShell

Pomocí Azure PowerShell můžete nakonfigurovat nastavení proxy serveru DNS v Azure Firewall. Můžete ji také použít k aktualizaci virtuálních sítí na použití Azure Firewall jako serveru DNS.

##### <a name="configure-virtual-network-dns-servers"></a>Konfigurace serverů DNS virtuální sítě

Následující příklad nakonfiguruje virtuální síť tak, aby používala Azure Firewall jako server DNS.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>Povolit proxy server DNS

Následující příklad povolí funkci proxy serveru DNS v Azure Firewall.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Další kroky

[Filtrování plně kvalifikovaného názvu domény v síťových pravidlech](fqdn-filtering-network-rules.md)
