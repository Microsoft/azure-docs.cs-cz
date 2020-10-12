---
title: Řešení chyb chybných bran – Azure Application Gateway
description: 'Přečtěte si, jak řešit potíže s Application Gatewaym serverem: 502 – webový server obdržel neplatnou odpověď v době, kdy funguje jako brána nebo proxy server.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 1b0abe998540c4fcc0a9b83f6d1175e18a560871
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84808158"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Řešení chyb Chybná brána ve službě Application Gateway

Naučte se řešit chybné chyby brány (502) přijaté při použití Azure Application Gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled

Po nakonfigurování služby Application Gateway je jednou z chyb, které se mohou zobrazit, chybová zpráva "Chyba serveru: 502-webový server obdržel neplatnou odpověď v době, kdy funguje jako brána nebo proxy server". K této chybě může dojít z následujících hlavních důvodů:

* NSG, UDR nebo vlastní DNS blokují přístup do členů fondu back-end.
* Back-endové virtuální počítače nebo instance sady škálování virtuálních počítačů nereagují na výchozí sondu stavu.
* Neplatná nebo nesprávná konfigurace vlastních sond stavu.
* [Fond back-end služby Azure Application Gateway není nakonfigurovaný ani prázdný](#empty-backendaddresspool).
* Žádný z virtuálních počítačů ani instancí v rámci [sady škálování virtuálních počítačů](#unhealthy-instances-in-backendaddresspool)není v pořádku.
* [Požadavek na vypršení časového limitu nebo problémy s připojením](#request-time-out) pro požadavky uživatelů

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Skupina zabezpečení sítě, trasa definovaná uživatelem nebo vlastní problém DNS

### <a name="cause"></a>Příčina

Pokud se přístup k back-endu zablokoval kvůli NSG, UDR nebo vlastnímu DNS, instance služby Application Gateway se nedostanou do back-endu fondu. Tím dojde k selhání testu, což vede k chybám 502.

NSG/UDR může být přítomná v podsíti služby Application Gateway nebo v podsíti, ve které jsou nasazené virtuální počítače aplikace.

Podobně může přítomnost vlastního DNS ve virtuální síti taky způsobovat problémy. Plně kvalifikovaný název domény, který se používá pro členy fondu back-end, se nemusí správně přeložit uživatelem konfigurovaným serverem DNS pro virtuální síť.

### <a name="solution"></a>Řešení

Pomocí následujících kroků Ověřte konfiguraci NSG, UDR a DNS:

* Ověřte skupin zabezpečení sítě přidružené k podsíti aplikační brány. Ujistěte se, že komunikace do back-endu není blokovaná.
* Ověřte UDR přidružené k podsíti aplikační brány. Ujistěte se, že UDR nesměruje provoz mimo podsíť back-endu. Můžete třeba vyhledat směrování na síťová virtuální zařízení nebo výchozí trasy inzerované do podsítě aplikační brány přes ExpressRoute/VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Podívejte se na virtuální počítač back-endu efektivní NSG a Route

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Ověřte přítomnost vlastního DNS ve virtuální síti. DNS se dá zkontrolovat tak, že ve výstupu prohlížíte podrobnosti vlastností virtuální sítě.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Pokud je k dispozici, zajistěte, aby server DNS mohl správně přeložit plně kvalifikovaný název domény člena back-end fondu.

## <a name="problems-with-default-health-probe"></a>Problémy s výchozí sondou stavu

### <a name="cause"></a>Příčina

chyby 502 můžou být taky časté indikátory, že výchozí sonda stavu nemůže mít přístup k back-endovému virtuálnímu počítači.

Při zřizování instance služby Application Gateway automaticky nakonfiguruje výchozí sondu stavu pro každý BackendAddressPool pomocí vlastností BackendHttpSetting. K nastavení tohoto testu není nutný žádný vstup uživatele. Konkrétně je-li nakonfigurováno pravidlo vyrovnávání zatížení, je vytvořeno přidružení mezi BackendHttpSetting a BackendAddressPool. Pro každé z těchto přidružení je nakonfigurován výchozí test a brána Application Gateway spustí pravidelné připojení kontroly stavu ke každé instanci v BackendAddressPool na portu zadaném v elementu BackendHttpSetting. 

Následující tabulka uvádí hodnoty spojené s výchozím testem stavu:

| Vlastnost sondy | Hodnota | Popis |
| --- | --- | --- |
| Adresa URL testu paměti |`http://127.0.0.1/` |Cesta URL |
| Interval |30 |Interval testu paměti v sekundách |
| Časový limit |30 |Časový limit testu v sekundách |
| Prahová hodnota pro poškozený stav |3 |Počet opakování testu Back-end Server je označený po po sobě jdoucí počet po sobě jdoucích selhání testu dosáhne prahové hodnoty, která není v pořádku. |

### <a name="solution"></a>Řešení

* Ujistěte se, že je nakonfigurovaná výchozí lokalita a naslouchá na adrese 127.0.0.1.
* Pokud BackendHttpSetting určuje jiný port než 80, měla by být výchozí lokalita nakonfigurována tak, aby naslouchala na tomto portu.
* Volání metody `http://127.0.0.1:port` by mělo vracet kód výsledku HTTP 200. To by mělo být vráceno v průběhu 30 sekund časového limitu.
* Ujistěte se, že je port nakonfigurovaný a že nejsou k dispozici žádná pravidla brány firewall nebo skupiny zabezpečení sítě Azure, které blokují příchozí nebo odchozí provoz na konfigurovaném portu.
* Pokud se virtuální počítače Azure Classic nebo cloudová služba používají s plně kvalifikovaným názvem domény nebo veřejnou IP adresou, ujistěte se, že je otevřený odpovídající [koncový bod](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) .
* Pokud je virtuální počítač nakonfigurovaný přes Azure Resource Manager a je mimo virtuální síť, ve které je nasazená Aplikační brána, musí být nakonfigurovaná [Skupina zabezpečení sítě](../virtual-network/security-overview.md) , aby se povolil přístup na požadovaném portu.

## <a name="problems-with-custom-health-probe"></a>Problémy s vlastním sondou stavu

### <a name="cause"></a>Příčina

Vlastní sondy stavu umožňují větší flexibilitu při výchozím chování při zjišťování. Pokud používáte vlastní testy, můžete nakonfigurovat interval sondy, adresu URL, cestu k otestování a počet neúspěšných odpovědí, které se mají přijmout před označením instance back-end fondu jako chybné.

Jsou přidány následující další vlastnosti:

| Vlastnost sondy | Popis |
| --- | --- |
| Název |Název sondy. Tento název se používá k odkazování na test v nastavení back-endu protokolu HTTP. |
| Protokol |Protokol použitý k odeslání testu. Sonda používá protokol definovaný v nastavení back-endu HTTP. |
| Hostitel |Název hostitele, který má odeslat test. Dá se použít jenom v případě, že je na aplikační bráně nakonfigurovaný vícenásobný Web. To se liší od názvu hostitele virtuálního počítače. |
| Cesta |Relativní cesta sondy. Platná cesta začíná znakem/. Sonda se posílá do \<protocol\> :// \<host\> :\<port\>\<path\> |
| Interval |Interval testu paměti v sekundách. Toto je časový interval mezi dvěma po sobě jdoucími sondami. |
| Časový limit |Časový limit testu v sekundách. Pokud v tomto časovém limitu neobdrží platná odpověď, sonda je označena jako neúspěšná. |
| Prahová hodnota pro poškozený stav |Počet opakování testu Back-end Server je označený po po sobě jdoucí počet po sobě jdoucích selhání testu dosáhne prahové hodnoty, která není v pořádku. |

### <a name="solution"></a>Řešení

Ověřte, jestli je vlastní sonda stavu správně nakonfigurovaná jako předchozí tabulka. Kromě předchozích kroků pro řešení potíží také zajistěte následující:

* Ujistěte se, že je test správně zadaný podle [Průvodce](application-gateway-create-probe-ps.md).
* Pokud je Aplikační brána nakonfigurovaná pro jednu lokalitu, ve výchozím nastavení by měl být název hostitele zadaný jako `127.0.0.1` , pokud není v rámci vlastního testu nakonfigurovaný jinak.
* Ujistěte se, že volání http:// \<host\> : \<port\> \<path\> vrátí kód výsledku HTTP 200.
* Zajistěte, aby interval, časový limit a UnhealtyThreshold byly v přijatelných oblastech.
* Pokud používáte test HTTPS, ujistěte se, že back-end server nevyžaduje SNI konfigurací záložního certifikátu na back-end serveru.

## <a name="request-time-out"></a>Časový limit požadavku

### <a name="cause"></a>Příčina

Po přijetí požadavku uživatele použije Aplikační brána konfigurovaná pravidla pro požadavek a směruje je do instance back-end fondu. Čeká na nastavitelný časový interval pro odpověď z back-endové instance. Ve výchozím nastavení je tento interval **20** sekund. Pokud Aplikační brána neobdrží odpověď z back-endové aplikace v tomto intervalu, požadavek uživatele získá chybu 502.

### <a name="solution"></a>Řešení

Application Gateway vám umožní nakonfigurovat toto nastavení přes BackendHttpSetting, která se pak dá použít u různých fondů. Různé fondy back-endu mohou mít různé BackendHttpSetting a nakonfigurován jiný časový limit požadavku.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Prázdné BackendAddressPool

### <a name="cause"></a>Příčina

Pokud Aplikační brána nemá žádné virtuální počítače nebo sadu škálování virtuálních počítačů nakonfigurované ve fondu back-end adres, nemůže směrovat žádnou žádost o zákazníky a pošle chybnou bránu.

### <a name="solution"></a>Řešení

Ujistěte se, že fond adres back-endu není prázdný. To lze provést prostřednictvím PowerShellu, CLI nebo portálu.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Výstup z předchozí rutiny by měl obsahovat neprázdný fond back-end adres. Následující příklad ukazuje dva vrácené fondy, které jsou nakonfigurovány s plně kvalifikovaným názvem domény nebo IP adresami pro virtuální počítače back-endu. Stav zřizování BackendAddressPool musí být úspěšné.

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

## <a name="unhealthy-instances-in-backendaddresspool"></a>Poškozené instance v BackendAddressPool

### <a name="cause"></a>Příčina

Pokud všechny instance BackendAddressPool nejsou v pořádku, pak Aplikační brána nemá žádný back-end ke směrování požadavku uživatele na. Může to také být případ, kdy jsou instance back-endu v pořádku, ale nemáte nasazenou požadovanou aplikaci.

### <a name="solution"></a>Řešení

Ujistěte se, že jsou instance v pořádku a že je aplikace správně nakonfigurovaná. Ověřte, jestli back-endové instance můžou reagovat na příkazy příkazového testu z jiného virtuálního počítače ve stejné virtuální síti. Pokud je nakonfigurovaná s veřejným koncovým bodem, ujistěte se, že požadavek na webovou aplikaci je v prohlížeči.

## <a name="next-steps"></a>Další kroky

Pokud předchozí kroky problém nevyřeší, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).

