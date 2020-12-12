---
title: Rozsahy privátních IP adres Azure Firewall SNAT
description: Rozsahy IP adres můžete nakonfigurovat pro SNAT.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: c5613dda7adbbc47f989bc2a772777e716620b3c
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97348029"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Rozsahy privátních IP adres Azure Firewall SNAT

Azure Firewall poskytuje automatické SNAT pro veškerý odchozí provoz na veřejné IP adresy. Ve výchozím nastavení Azure Firewall nesnat s pravidly sítě, pokud je cílová IP adresa v rozsahu privátních IP adres na [úřad IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Pravidla aplikací se vždycky aplikují pomocí [transparentního proxy serveru](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) bez ohledu na cílovou IP adresu.

Tato logika funguje dobře při směrování provozu přímo na Internet. Pokud jste ale povolili [vynucené tunelování](forced-tunneling.md), před jejich vstupem se internetový provoz na jednu z privátních IP adres brány firewall v AzureFirewallSubnet, takže se tento zdroj skryje z místní brány firewall.

Pokud vaše organizace používá pro privátní sítě rozsah veřejných IP adres, Azure Firewall SNATs provoz na jednu z privátních IP adres brány firewall v AzureFirewallSubnet. Můžete ale nakonfigurovat Azure Firewall **, aby** nesnat na svůj rozsah veřejných IP adres. Pokud například chcete zadat jednotlivé IP adresy, můžete ji zadat takto: `192.168.1.10` . Pokud chcete zadat rozsah IP adres, můžete ho zadat takto: `192.168.1.0/24` .

- Pokud chcete nakonfigurovat Azure Firewall na **nikdy** SNAT bez ohledu na cílovou IP adresu, použijte jako rozsah privátních IP adres **0.0.0.0/0** . V této konfiguraci Azure Firewall moci nikdy směrovat přenosy přímo na Internet. 

- Pokud chcete nakonfigurovat bránu firewall tak, aby **vždycky** byla v nezávisle na cílové adrese, použijte jako rozsah privátních IP adres **255.255.255.255/32** .

> [!IMPORTANT]
> Pokud chcete zadat vlastní rozsahy privátních IP adres a zachovat výchozí rozsahy adres RFC 1918, ujistěte se, že vlastní seznam stále obsahuje rozsah RFC 1918 pro IANA. 

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Nakonfigurujte rozsahy privátních IP adres SNAT – Azure PowerShell

Pomocí Azure PowerShell můžete zadat rozsahy privátních IP adres pro bránu firewall.

### <a name="new-firewall"></a>Nová brána firewall

V případě nové brány firewall je rutina Azure PowerShell:

```azurepowershell
$azFw = @{
    Name               = '<fw-name>'
    ResourceGroupName  = '<resourcegroup-name>'
    Location           = '<location>'
    VirtualNetworkName = '<vnet-name>'
    PublicIpName       = '<public-ip-name>'
    PrivateRange       = @("IANAPrivateRanges", "192.168.1.0/24", "192.168.1.10")
}

New-AzFirewall @azFw
```
> [!NOTE]
> Nasazení Azure Firewall pomocí `New-AzFirewall` vyžaduje existující virtuální síť a veřejnou IP adresu. Průvodce úplným nasazením najdete v tématu [nasazení a konfigurace Azure firewall pomocí Azure PowerShell](deploy-ps.md) .

> [!NOTE]
> IANAPrivateRanges se rozšíří na aktuální výchozí hodnoty na Azure Firewall, zatímco se do nich přidají jiné rozsahy. Chcete-li zachovat výchozí IANAPrivateRanges ve specifikaci privátního rozsahu, musí zůstat ve vaší `PrivateRange` specifikaci, jak je znázorněno v následujících příkladech.

Další informace najdete v tématu [New-AzFirewall](/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Existující brána firewall

Ke konfiguraci existující brány firewall použijte následující rutiny Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>Konfigurace rozsahů privátních IP adres SNAT – Azure CLI

Pomocí Azure CLI můžete zadat rozsahy privátních IP adres pro bránu firewall.

### <a name="new-firewall"></a>Nová brána firewall

V případě nové brány firewall je příkaz Azure CLI:

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> Nasazení Azure Firewall pomocí příkazu rozhraní příkazového řádku Azure `az network firewall create` vyžaduje další kroky konfigurace pro vytvoření veřejných IP adres a konfigurace protokolu IP. Úplný Průvodce nasazením najdete v tématu [nasazení a konfigurace Azure firewall pomocí](deploy-cli.md) rozhraní příkazového řádku Azure.

> [!NOTE]
> IANAPrivateRanges se rozšíří na aktuální výchozí hodnoty na Azure Firewall, zatímco se do nich přidají jiné rozsahy. Chcete-li zachovat výchozí IANAPrivateRanges ve specifikaci privátního rozsahu, musí zůstat ve vaší `PrivateRange` specifikaci, jak je znázorněno v následujících příkladech.

### <a name="existing-firewall"></a>Existující brána firewall

K nakonfigurování existující brány firewall je příkaz Azure CLI:

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>Konfigurace rozsahů privátních IP adres SNAT – šablona ARM

Pokud chcete konfigurovat SNAT během Template deployment ARM, můžete do vlastnosti přidat následující `additionalProperties` :

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Nakonfigurujte rozsahy privátních IP adres SNAT – Azure Portal

Pomocí Azure Portal můžete pro bránu firewall zadat rozsahy privátních IP adres.

1. Vyberte skupinu prostředků a potom vyberte bránu firewall.
2. Na stránce **Přehled** **rozsahy privátních IP adres** vyberte výchozí hodnotu **IANA RFC 1918**.

   Otevře se stránka **Upravit předpony privátních IP adres** :

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Upravit předpony privátních IP adres":::

1. Ve výchozím nastavení je **IANAPrivateRanges** nakonfigurovaný.
2. Upravte rozsahy privátních IP adres pro vaše prostředí a pak vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [Azure firewall vynucené tunelování](forced-tunneling.md).