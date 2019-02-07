---
title: Řešení potíží s chybami Azure Application Gateway Chybná brána (502) | Dokumentace Microsoftu
description: Informace o řešení potíží s chybami Application Gateway 502
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 1d431ead-d318-47d8-b3ad-9c69f7e08813
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: amsriva
ms.openlocfilehash: 1db16f203755f9afc265495daba056313138a5dc
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819440"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Řešení chyb Chybná brána ve službě Application Gateway

Informace o řešení chyb Chybná brána (502) přijal při použití služba application gateway.

## <a name="overview"></a>Přehled

Po nakonfigurování služby application gateway, jednou z chyb, na které uživatelé je "Chyba serveru: 502 – Webový server obdržel neplatnou odpověď v době, kdy fungoval jako brána nebo proxy server. K této chybě může dojít z následujících hlavních důvodů:

* Skupina zabezpečení sítě, směrování definovaného uživatelem nebo vlastního DNS blokuje přístup do členů fondu back-endu.
* Back-end virtuální počítače nebo instance škálovací sady virtuálních počítačů neodpovídají na výchozí kontroly stavu.
* Neplatné nebo nesprávné konfigurace vlastních testů stavu paměti.
* Azure Application Gateway [back endový fond není nakonfigurované nebo je prázdný](#empty-backendaddresspool).
* Žádný z virtuálních počítačů nebo instancí v [škálovací sadu virtuálních počítačů jsou v pořádku](#unhealthy-instances-in-backendaddresspool).
* [Požádat o vypršení časového limitu nebo připojení problémy](#request-time-out) s uživatelskými požadavky.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Skupina zabezpečení sítě, uživatelem definovaná trasa nebo vlastního DNS problém

### <a name="cause"></a>Příčina

Pokud z důvodu přítomnosti skupiny zabezpečení sítě, směrování definovaného uživatelem nebo vlastní DNS je blokovaný přístup k back-endu, instancí Application Gateway nebudou moct kontaktovat back-endový fond a způsobí selhání testu, které jsou příčinou 502 chyb. Všimněte si, že NSG/UDR může být k dispozici ve službě Application Gateway podsítě nebo podsíť ve které jsou nasazené virtuální počítače aplikací. Podobně přítomnost vlastního DNS ve virtuální síti může také způsobit problémy, pokud se používá pro členy fondu back-end plně kvalifikovaný název domény a není správně přeložit pomocí DNS serveru nakonfigurovaného uživatele pro virtuální síť.

### <a name="solution"></a>Řešení

Ověření konfigurace skupiny zabezpečení sítě, směrování definovaného uživatelem a DNS tak, že přejdete pomocí následujících kroků:
* Zkontrolujte skupiny zabezpečení sítě přidružená k podsíti Application Gateway. Ujistěte se, že nebude blokovat komunikaci do back-endu.
* Zkontrolujte směrování definovaného uživatelem spojená s podsítí Application Gateway. Ujistěte se, že uživatelem definovaná TRASA není směrování provozu mimo podsíť back-endu – třeba zkontrolovat pro směrování do síťových virtuálních zařízení nebo výchozí trasy, inzerované do podsítě Application Gateway přes síť ExpressRoute nebo VPN.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzureRmVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Zkontrolujte efektivní skupiny zabezpečení sítě a trasu s back-endového virtuálního počítače

```powershell
Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzureRmEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Kontrola přítomnosti vlastního DNS ve virtuální síti. DNS můžete zkontrolovat pohledem na podrobnosti o vlastnostech virtuálních sítí ve výstupu.

```json
Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Pokud jsou k dispozici, ujistěte se, že je schopen správně přeložit plně kvalifikovaný název domény člena fondu back-end DNS server.

## <a name="problems-with-default-health-probe"></a>Problémy s výchozí kontroly stavu

### <a name="cause"></a>Příčina

502 chyby může být také častým indikátory, že výchozí kontroly stavu není schopen kontaktovat back endové virtuální počítače. Při zřízení instance Application Gateway automaticky nakonfiguruje výchozí kontroly stavu pro každý BackendAddressPool pomocí vlastností nastavení BackendHttpSetting. Nastavit tento test se vyžaduje zásah uživatele. Konkrétně Pokud je nakonfigurovaná pravidla Vyrovnávání zatížení, lze vytvářet spojení mezi nastavení BackendHttpSetting a BackendAddressPool. Výchozí kontroly je nakonfigurovat pro každou z těchto přidružení a pravidelné stavu kontroly připojení ke každé instanci v BackendAddressPool na port zadaný v elementu nastavení BackendHttpSetting zahájí Application Gateway. Následující tabulka uvádí hodnot spojené s výchozí kontroly stavu.

| Vlastnosti testu | Hodnota | Popis |
| --- | --- | --- |
| Adresa URL testu |http://127.0.0.1/ |Cesta URL |
| Interval |30 |Interval testu v sekundách |
| Časový limit |30 |Časový limit testu v sekundách |
| Prahová hodnota špatného stavu |3 |Počet opakování testu. Back endového serveru je označena po počet selhání testu po sobě jdoucích dosáhne prahová hodnota špatného stavu. |

### <a name="solution"></a>Řešení

* Ujistěte se, že výchozí web je nakonfigurován a naslouchá na 127.0.0.1.
* Pokud nastavení BackendHttpSetting určuje jiný port než 80, výchozí server musí být nakonfigurovaný tak, aby naslouchala na tento port.
* Volání http://127.0.0.1:port by měla vrátit výsledek kód HTTP 200. To má být vrácen v rámci časového limitu 30 sekund.
* Ujistěte se, že je otevřený port nakonfigurovaný a, že neexistují žádná pravidla brány firewall nebo skupin zabezpečení sítě Azure, které blokovat příchozí nebo odchozí provoz přes port nakonfigurovaný.
* Pokud virtuální počítače Azure classic nebo Cloudovou službu se používá s plně kvalifikovaný název domény nebo veřejné IP adresy, ujistěte se, že odpovídající [koncový bod](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) je otevřen.
* Pokud bude virtuální počítač je nakonfigurovaný přes Azure Resource Manageru a mimo virtuální síť nasazená služba Application Gateway, [skupinu zabezpečení sítě](../virtual-network/security-overview.md) musí být nakonfigurována pro povolení přístupu na požadovaný port.

## <a name="problems-with-custom-health-probe"></a>Problémy s vlastní stav testu

### <a name="cause"></a>Příčina

Povolit vlastních testů stavu paměti větší flexibilitu pro výchozí chování zjišťování. Pokud používáte vlastní sondy, uživatelé můžou konfigurovat interval testu, adresu URL a cesty k testování a počet neúspěšných odpovědí tak, aby přijímal před označením instance back endovém fondu jako není v pořádku. Tyto další vlastnosti jsou přidány.

| Vlastnosti testu | Popis |
| --- | --- |
| Název |Název testu. Tento název se používá k odkazování na test v nastavení HTTP back-end. |
| Protocol (Protokol) |Protokol používaný k posílání sondy. Test paměti používá protokol definované v nastavení HTTP back-end |
| Hostitel |Název hostitele k odeslání testu. Vztahuje se pouze v případě, že je nakonfigurovaný více webů ve službě Application Gateway. Tím se liší od názvu hostitele virtuálního počítače. |
| Cesta |Relativní cesta testu. Platná cesta začíná od "/". Sonda se odesílá do \<protokol\>://\<hostitele\>:\<port\>\<cesta\> |
| Interval |Interval testu paměti v sekundách. Toto je časový interval mezi dvěma po sobě jdoucích sondy. |
| Časový limit |Časový limit testu v sekundách. Pokud není přijetí platné odpovědi během tohoto období časového limitu testu označen jako neúspěšný. |
| Prahová hodnota špatného stavu |Počet opakování testu. Back endového serveru je označena po počet selhání testu po sobě jdoucích dosáhne prahová hodnota špatného stavu. |

### <a name="solution"></a>Řešení

Ověřte, že vlastní sondu stavu je nakonfigurované správně jako v předchozí tabulce. Kromě předchozích kroků Ujistěte se také následující:

* Zajistěte, aby tato kontrola je správně zadán jak je uvedeno [průvodce](application-gateway-create-probe-ps.md).
* Pokud služba Application Gateway je nakonfigurována pro jednu lokalitu, ve výchozím nastavení hostitele název musí být zadán jako "127.0.0.1", pokud nebudou jinak nakonfigurovaná v vlastní test paměti.
* Ujistěte se, že volání http://\<hostitele\>:\<port\>\<cesta\> vrátí výsledek kód HTTP 200.
* Ujistěte se, že Interval, časový limit a UnhealtyThreshold jsou v přijatelných rozsazích.
* Pokud pomocí sondu protokolu HTTPS, ujistěte se, že back-end server nevyžaduje SNI nakonfigurováním certifikát pro použití náhradní lokality na vlastním serveru back-endu.

## <a name="request-time-out"></a>Časový limit požadavku

### <a name="cause"></a>Příčina

Při přijetí požadavku uživatele Application Gateway použije nakonfigurovaná pravidla k požadavku a směruje do instance back endového fondu. Čeká s konfigurovatelným intervalem doby na odpověď z back-end instance. Ve výchozím nastavení, je tento interval **30 sekund**. Pokud služba Application Gateway neobdrží odpověď z back endové aplikace v tomto intervalu, zobrazoval požadavek uživatele 502 chyby.

### <a name="solution"></a>Řešení

Služba Application Gateway umožňuje uživatelům ke konfiguraci tohoto nastavení prostřednictvím nastavení BackendHttpSetting, můžete následně použije na různých fondů. Různých fondů back-end může mít různé nastavení BackendHttpSetting a nakonfigurovat vypršení časového limitu proto různé žádosti.

```powershell
    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Prázdný BackendAddressPool

### <a name="cause"></a>Příčina

V případě Application Gateway nemá žádné virtuální počítače nebo škálovací sady virtuálních počítačů nakonfigurované ve fondu back endových adres ho nelze směrovat všechny požadavky zákazníků a vyvolá chybu Chybná brána.

### <a name="solution"></a>Řešení

Ujistěte se, že fond back endových adres není prázdný. To můžete udělat přes PowerShell, rozhraní příkazového řádku nebo portálu.

```powershell
Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Výstup z předchozí rutiny by měl obsahovat neprázdný back endovém fondu adres. Tady je příklad, kde, dva fondy jsou vráceny, které jsou nakonfigurovány s plně kvalifikovaný název domény nebo IP adres back-endový virtuální počítače. Stav zřizování BackendAddressPool musí být 'bylo úspěšné".

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

## <a name="unhealthy-instances-in-backendaddresspool"></a>Instance v nedobrém stavu v BackendAddressPool

### <a name="cause"></a>Příčina

Pokud všechny výskyty BackendAddressPool nejsou v pořádku, služba Application Gateway nemusí libovolným back endem pro směrování požadavku uživatele na. Také to může být případ, kdy back endových instancí jsou v pořádku, ale nemáte požadovanou aplikaci nasadit.

### <a name="solution"></a>Řešení

Ujistěte se, že instance jsou v pořádku a je aplikace správně nakonfigurována. Zkontrolujte, jestli back endových instancí jsou schopné reagovat na příkaz ping z jiného virtuálního počítače ve stejné virtuální síti. Pokud nakonfigurované veřejný koncový bod, ujistěte se, že je možnost změny žádost prohlížeče na webovou aplikaci.

## <a name="next-steps"></a>Další postup

Pokud předchozí kroky není problém vyřešit, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).

