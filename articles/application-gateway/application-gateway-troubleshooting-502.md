---
title: Poradce při potížích s chybami brány – Azure Application Gateway
description: 'Zjistěte, jak řešit potíže s chybou serveru aplikační brány: 502 – webový server obdržel neplatnou odpověď při působení jako brána nebo proxy server.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 17bed17b536f6e88fc821fd83e09a1d6ea218bc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130485"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Řešení chyb Chybná brána ve službě Application Gateway

Zjistěte, jak řešit chybné chyby brány (502), které se při používání brány Aplikace Azure dostaly.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled

Po konfiguraci aplikační brány je jednou z chyb, které se mohou zobrazit, "Chyba serveru: 502 - webový server obdržel neplatnou odpověď při působení jako brána nebo proxy server". K této chybě může dojít z následujících hlavních důvodů:

* NSG, UDR nebo vlastní DNS blokuje přístup ke členům back-endového fondu.
* Back-endové virtuální počítače nebo instance škálovací sady virtuálních strojů nereagují na výchozí sondu stavu.
* Neplatná nebo nesprávná konfigurace vlastních sond stavu.
* Back-endový fond Azure Application Gateway [není nakonfigurovaný ani prázdný](#empty-backendaddresspool).
* Žádný z virtuálních počítače nebo instancí ve [škálovací sadě virtuálních strojů není v pořádku](#unhealthy-instances-in-backendaddresspool).
* [Vyžádat si časový čas nebo problémy](#request-time-out) s připojením s požadavky uživatelů.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Skupina zabezpečení sítě, uživatelem definovaná trasa nebo vlastní problém dns

### <a name="cause"></a>Příčina

Pokud je přístup k back-endu blokován z důvodu nsg, UDR nebo vlastní DNS, instance brány aplikace se nemohou dostat do back-endového fondu. To způsobí selhání sondy, výsledkem 502 chyb.

NSG/UDR může být k dispozici buď v podsíti aplikační brány nebo v podsíti, kde jsou nasazeny virtuální počítače aplikace.

Podobně přítomnost vlastní DNS ve virtuální síti může také způsobit problémy. FQDN používaný pro členy back-endového fondu nemusí správně vyřešit uživatelem nakonfigurovaný server DNS pro virtuální síť.

### <a name="solution"></a>Řešení

Ověřte konfiguraci nsg, UDR a DNS pomocí následujících kroků:

* Zkontrolujte skupiny nsg přidružené k podsíti aplikační brány. Ujistěte se, že komunikace s back-endem není blokována.
* Zkontrolujte UDR přidružené k podsíti aplikační brány. Ujistěte se, že UDR nesměruje provoz mimo podsíť back-endu. Můžete například zkontrolovat směrování do síťových virtuálních zařízení nebo výchozích tras inzerovaných do podsítě aplikační brány prostřednictvím služby ExpressRoute/VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Zkontrolujte efektivní nsg a trasu pomocí back-endového virtuálního mísy

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Zkontrolujte přítomnost vlastních DNS ve virtuální síti. DNS lze zkontrolovat podle podrobností o vlastnostech virtuální sítě ve výstupu.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Pokud je k dispozici, ujistěte se, že server DNS může správně vyřešit fqdn člena back-endového fondu.

## <a name="problems-with-default-health-probe"></a>Problémy s výchozí sondou stavu

### <a name="cause"></a>Příčina

502 chyby mohou být také časté ukazatele, že výchozí sonda stavu nemůže dosáhnout back-end virtuálních připojení.

Když je zřízena instance aplikační brány, automaticky nakonfiguruje výchozí sondu stavu pro každý BackendAddressPool pomocí vlastností BackendHttpSetting. K nastavení této sondy není vyžadován žádný vstup uživatele. Konkrétně při konfiguraci pravidla vyrovnávání zatížení je provedeno přidružení mezi BackendHttpSetting a BackendAddressPool. Výchozí sonda je nakonfigurována pro každé z těchto přidružení a aplikační brána spustí pravidelné připojení kontroly stavu ke každé instanci v backendaddresspoolu na portu určeném v elementu BackendHttpSetting. 

V následující tabulce jsou uvedeny hodnoty přidružené k výchozí sondě stavu:

| Vlastnost sondy | Hodnota | Popis |
| --- | --- | --- |
| Adresa URL sondy |`http://127.0.0.1/` |Cesta url |
| Interval |30 |Interval sondy v sekundách |
| Časový limit |30 |Časový rozsah sondy v sekundách |
| Prahová hodnota pro poškozený stav |3 |Počet opakování sondy. Server back-end je označen dolů po sobě jdoucích počet selhání sondy dosáhne prahovou hodnotu není v pořádku. |

### <a name="solution"></a>Řešení

* Ujistěte se, že je nakonfigurován výchozí web a naslouchá na 127.0.0.1.
* Pokud BackendHttpSetting určuje jiný port než 80, výchozí web by měl být nakonfigurován tak, aby naslouchal na tomto portu.
* Volání `http://127.0.0.1:port` by měl vrátit kód výsledků PROTOKOLU HTTP 200. To by měla být vrácena v rámci časového období 30 sekund.
* Ujistěte se, že nakonfigurovaný port je otevřený a že neexistují žádná pravidla brány firewall nebo skupiny zabezpečení sítě Azure, které blokují příchozí nebo odchozí provoz na nakonfigurovaném portu.
* Pokud se klasické virtuální počítače Azure nebo cloudová služba používají s vícenežečným právem nebo veřejnou IP adresou, ujistěte se, že se otevře odpovídající [koncový bod.](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json)
* Pokud je virtuální počítač nakonfigurovaný přes Správce prostředků Azure a je mimo virtuální síť, kde se nasadí aplikační brána, musí být nakonfigurovaná [skupina zabezpečení sítě,](../virtual-network/security-overview.md) která umožňuje přístup na požadovaný port.

## <a name="problems-with-custom-health-probe"></a>Problémy s vlastní zdravotní sondou

### <a name="cause"></a>Příčina

Vlastní sondy stavu umožňují další flexibilitu výchozího chování zjišťování. Při použití vlastních sond můžete nakonfigurovat interval sondy, adresu URL, cestu k testování a počet neúspěšných odpovědí, které chcete přijmout před označením instance back-endového fondu jako nefunkční.

Jsou přidány další další vlastnosti:

| Vlastnost sondy | Popis |
| --- | --- |
| Name (Název) |Název sondy. Tento název se používá k odkazování na sondu v nastavení http back-endu. |
| Protocol (Protokol) |Protokol použitý k odeslání sondy. Sonda používá protokol definovaný v nastavení http back-endu. |
| Hostitel |Název hostitele pro odeslání sondy. Platí pouze v případě, že je v aplikační bráně nakonfigurováno více lokalit. To se liší od názvu hostitele virtuálního serveru. |
| Cesta |Relativní cesta sondy. Platná cesta začíná od '/'. Sonda je odeslána \<\>do\<\>protokolu\<\>\<:// host : cesta k portu\> |
| Interval |Interval sondy v sekundách. Toto je časový interval mezi dvěma po sobě jdoucími sondami. |
| Časový limit |Časový čas sondy v sekundách. Pokud není přijata platná odpověď v tomto časovém období, sonda je označena jako neúspěšná. |
| Prahová hodnota pro poškozený stav |Počet opakování sondy. Server back-end je označen dolů po sobě jdoucích počet selhání sondy dosáhne prahovou hodnotu není v pořádku. |

### <a name="solution"></a>Řešení

Ověřte, zda je sonda vlastního stavu správně nakonfigurována jako předchozí tabulka. Kromě předchozích kroků řešení potíží také zajistěte následující:

* Ujistěte se, že sonda je správně zadána podle [vodítka](application-gateway-create-probe-ps.md).
* Pokud je aplikační brána nakonfigurována pro jednu lokalitu, měl by být ve výchozím nastavení název hostitele zadán jako `127.0.0.1`, pokud není ve vlastní sondě nakonfigurováno jinak.
* Ujistěte se,\<že\>\<volání\>\<\> http:// hostitele : cesta k portu vrátí kód výsledků PROTOKOLU HTTP 200.
* Ujistěte se, že Interval, Časový limit a UnhealtyThreshold jsou v přijatelných rozsahech.
* Pokud používáte sondu HTTPS, ujistěte se, že back-endový server nevyžaduje SNI konfigurací záložního certifikátu na samotném back-endovém serveru.

## <a name="request-time-out"></a>Požadavek na časový opovce

### <a name="cause"></a>Příčina

Když je přijat požadavek uživatele, aplikační brána použije nakonfigurovaná pravidla pro požadavek a směruje ji do instance fondu back-end. Čeká na konfigurovatelný časový interval pro odpověď z instance back-end. Ve výchozím nastavení je tento interval **20** sekund. Pokud brána aplikace neobdrží odpověď z back-endové aplikace v tomto intervalu, požadavek uživatele získá chybu 502.

### <a name="solution"></a>Řešení

Aplikační brána umožňuje konfigurovat toto nastavení pomocí BackendHttpSetting, které lze pak použít pro různé fondy. Různé back-endové fondy mohou mít různé BackendHttpSetting a jiný časový čas požadavku nakonfigurován.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Vyprázdnit fond backendaddresspool

### <a name="cause"></a>Příčina

Pokud brána aplikace nemá žádné virtuální počítače nebo škálovací sadu virtuálních strojů nakonfigurovanou ve fondu adres back-end, nemůže směrovat žádný požadavek zákazníka a odešle chybu chybné brány.

### <a name="solution"></a>Řešení

Ujistěte se, že fond adres back-end není prázdný. To lze provést prostřednictvím prostředí PowerShell, CLI nebo portálu.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Výstup z předchozí rutiny by měl obsahovat neprázdný fond adres back-end. Následující příklad ukazuje dva vrácené fondy, které jsou nakonfigurovány s vícenežvištěm nebo IP adresami pro back-endové virtuální počítače. Stav zřizování backendaddresspool musí být "Úspěšné".

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Instance Není v pořádku v backendaddresspoolu

### <a name="cause"></a>Příčina

Pokud všechny instance BackendAddressPool nejsou v pořádku, pak brána aplikace nemá žádný back-end pro směrování požadavku uživatele. To může být také případ, kdy back-end instance jsou v pořádku, ale nemají požadované aplikace nasazené.

### <a name="solution"></a>Řešení

Ujistěte se, že instance jsou v pořádku a aplikace je správně nakonfigurována. Zkontrolujte, jestli back-endové instance můžou reagovat na příkaz ping z jiného virtuálního virtuálního serveru ve stejné virtuální síti. Pokud je nakonfigurován s veřejným koncovým bodem, ujistěte se, že požadavek prohlížeče na webovou aplikaci je opravitelný.

## <a name="next-steps"></a>Další kroky

Pokud předchozí kroky problém nevyřeší, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).

