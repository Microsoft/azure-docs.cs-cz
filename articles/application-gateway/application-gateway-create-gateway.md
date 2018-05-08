---
title: Vytvoření, spuštění nebo odstranění aplikační brány | Dokumentace Microsoftu
description: Tahle stránka poskytuje pokyny pro vytvoření, konfiguraci, spuštění a odstranění služby Azure application gateway
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 577054ca-8368-4fbf-8d53-a813f29dc3bc
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: victorh
ms.openlocfilehash: a1cfd42f5b1c31b911005b2539047630c6d320dd
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="create-start-or-delete-an-application-gateway-with-powershell"></a>Vytvoření, spuštění nebo odstranění aplikační brány pomocí PowerShellu 

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Šablona Azure Resource Manageru](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

Služba Azure Application Gateway je nástroj pro vyrovnávání zatížení vrstvy 7. Poskytuje převzetí služeb při selhání, směrování výkonu požadavků HTTP mezi různými servery, ať už jsou místní nebo v cloudu. Application Gateway poskytuje mnoho funkcí Application Delivery Controlleru (ADC), včetně vyrovnávání zatížení protokolu HTTP, spřažení relace na základě souborů cookie, přesměrování zpracování SSL (Secure Sockets Layer), vlastních testů stavu, podpory více webů a mnoha dalších. Úplný seznam podporovaných funkcí najdete v tématu [Přehled služby Application Gateway](application-gateway-introduction.md).

Tenhle článek vás provede kroky k vytvoření, konfiguraci, spuštění a odstranění aplikační brány.

## <a name="before-you-begin"></a>Než začnete

1. Nainstalujte nejnovější verzi rutin prostředí Azure PowerShell pomocí instalační služby webové platformy. Nejnovější verzi můžete stáhnout a nainstalovat v části **Windows PowerShell** na stránce [Položky ke stažení](https://azure.microsoft.com/downloads/).
2. Pokud už máte virtuální síť, vyberte buď existující prázdnou podsíť, nebo vytvořte novou podsíť výhradně pro účely služby Application Gateway v existující virtuální síti. Službu Application Gateway nelze nasadit do jiné virtuální sítě, než prostředky, které máte v úmyslu nasadit za službou Application Gateway, pokud nepoužijete partnerský vztah virtuálních sítí. Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).
3. Ověřte, že máte funkční virtuální síť s platnou podsítí. Ujistěte se, že žádné virtuální počítače nebo cloudová nasazení nepoužívají podsíť. Služba Application Gateway musí být sama o sobě v podsíti virtuální sítě.
4. Servery, které nakonfigurujete pro použití služby Application Gateway, musí existovat nebo mít své koncové body vytvořené buď ve virtuální síti, nebo s přiřazenou veřejnou IP adresou nebo virtuální IP adresou.

## <a name="what-is-required-to-create-an-application-gateway"></a>Co je potřeba k vytvoření služby Application Gateway?

Když k vytvoření služby Application Gateway použijete příkaz `New-AzureApplicationGateway`, v tomto bodě se nenastaví žádná konfigurace a nově vytvořený prostředek se konfiguruje buď pomocí XML, nebo objektu konfigurace.

Hodnoty jsou:

* **Fond back-end serverů:** Seznam IP adres back-end serverů. Uvedené IP adresy by měly buď patřit do podsítě virtuální sítě, nebo by měly být veřejnými nebo virtuálními IP adresami.
* **Nastavení fondu back-end serverů:** Každý fond má nastavení, jako je port, protokol a spřažení na základě souborů cookie. Tato nastavení se vážou na fond a používají se na všechny servery v rámci fondu.
* **Front-end port:** Toto je veřejný port, který se otevírá ve službě Application Gateway. Když datový přenos dorazí na tento port, přesměruje se na některý back-end server.
* **Naslouchací proces:** Naslouchací proces má front-end port, protokol (Http nebo Https, u těchto hodnot se rozlišují malá a velká písmena) a název certifikátu SSL (pokud se konfiguruje přesměrování zpracování SSL).
* **Pravidlo:** Pravidlo váže naslouchací proces a fond back-end serverů a definuje, ke kterému fondu back-end serverů se má provoz směrovat při volání příslušného naslouchacího procesu.

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Pro vytvoření nové aplikační brány:

1. Vytvořte prostředek aplikační brány.
2. Vytvořte konfigurační soubor XML nebo objekt konfigurace.
3. Potvrďte konfiguraci nově vytvořeného prostředku aplikační brány.

> [!NOTE]
> Když potřebujete nakonfigurovat vlastní test paměti svojí aplikační brány, přečtěte si část [Vytvořit bránu s vlastními testy paměti pomocí prostředí PowerShell](application-gateway-create-probe-classic-ps.md). Další informace najdete v části [vlastní testy paměti a sledování stavu](application-gateway-probe-overview.md).

![Příklad scénáře][scenario]

### <a name="create-an-application-gateway-resource"></a>Vytvořte prostředek aplikační brány

Pokud chcete vytvořit bránu, použijte rutinu `New-AzureApplicationGateway` a zadejte vlastní hodnoty. Fakturace brány se nespustí v tomhle okamžiku. Fakturace začíná v pozdější fázi, po úspěšném spuštění brány.

Následující příklad vytvoří aplikační bránu pomocí virtuální sítě s názvem testvnet1 a podsítě s názvem subnet-1:

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

*Popis*, *InstanceCount* a *GatewaySize* jsou volitelné parametry.

Pokud chcete ověřit vytvoření brány, můžete použít rutinu `Get-AzureApplicationGateway`.

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Stopped
VirtualIPs    : {}
DnsName       :
```

> [!NOTE]
> Výchozí hodnota *InstanceCount* je 2, přičemž maximální hodnota je 10. Výchozí hodnota *GatewaySize* je Medium (Střední). Můžete vybrat mezi Malá, Střední a Velká.

Hodnoty *VirtualIPs* a *DnsName* se zobrazují jako prázdné, protože se brána ještě nespustila. Vytvoří se, jakmile bude brána v běžícím stavu.

## <a name="configure-the-application-gateway"></a>Nakonfigurujte aplikační bránu

Aplikační bránu můžete nakonfigurovat pomocí XML nebo objektu konfigurace.

### <a name="configure-the-application-gateway-by-using-xml"></a>Nakonfigurujte aplikační bránu pomocí XML

V následujícím příkladu použijete soubor XML k nakonfigurování všech nastavení aplikační brány a potvrdíte je pro prostředek aplikační brány.  

#### <a name="step-1"></a>Krok 1

Zkopírujte následující text do Poznámkového bloku.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendPorts>
        <FrontendPort>
            <Name>(name-of-your-frontend-port)</Name>
            <Port>(port number)</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>(name-of-your-backend-pool)</Name>
            <IPAddresses>
                <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
                <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>(backend-setting-name-to-configure-rule)</Name>
            <Port>80</Port>
            <Protocol>[Http|Https]</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>(name-of-the-listener)</Name>
            <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
            <Protocol>[Http|Https]</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>(name-of-load-balancing-rule)</Name>
            <Type>basic</Type>
            <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
            <Listener>(name-of-the-listener)</Listener>
            <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Upravte hodnoty položek konfigurace v závorkách. Uložte soubor s příponou .xml.

> [!IMPORTANT]
> Položka protokolu Http nebo Https rozlišuje velká a malá písmena.

Následující příklad ukazuje, jak použít konfigurační soubor k nastavení služby Application Gateway. V příkladu se vyrovnává zatížení provozu HTTP na veřejném portu 80 a síťový provoz mezi dvěma IP adresami se odesílá na port back-end 80.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

#### <a name="step-2"></a>Krok 2

Dále nastavte aplikační bránu. Použijte rutinu `Set-AzureApplicationGatewayConfig` s konfiguračním souborem XML.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"
```

### <a name="configure-the-application-gateway-by-using-a-configuration-object"></a>Nakonfigurujte aplikační bránu pomocí objektu konfigurace

Následující příklad ukazuje, jak se provádí konfigurace aplikační brány pomocí objektu konfigurace. Všechny položky konfigurace se musí nakonfigurovat individuálně a potom se musí přidat k objektu konfigurace aplikační brány. Po vytvoření objektu konfigurace použijte příkaz `Set-AzureApplicationGateway` pro potvrzení konfigurace k předem vytvořenému prostředku služby Application Gateway.

> [!NOTE]
> Před přiřazením hodnoty každému objektu konfigurace musíte deklarovat, který typ objektu používá prostředí PowerShell pro úložiště. První řádek vytvoření jednotlivých položek definuje, jaký model `Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model(object name)` se použije.

#### <a name="step-1"></a>Krok 1

Vytvořte všechny položky individuální konfigurace.

Vytvořte front-end IP adresu, jak je znázorněno v následujícím příkladu.

```powershell
$fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
$fip.Name = "fip1"
$fip.Type = "Private"
$fip.StaticIPAddress = "10.0.0.5"
```

Vytvořte front-end port, jak je znázorněno v následujícím příkladu.

```powershell
$fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
$fep.Name = "fep1"
$fep.Port = 80
```

Vytvořte fond back-end serveru.

Definujte IP adresy, které se přidají do fondu back-end serverů, jak je znázorněno v následujícím příkladu.

```powershell
$servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
$servers.Add("10.0.0.1")
$servers.Add("10.0.0.2")
```

Pomocí objektu $server přidejte hodnoty do objektu back-end fondu ($pool).

```powershell
$pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
$pool.BackendServers = $servers
$pool.Name = "pool1"
```

Vytvořte nastavení fondu back-end serverů.

```powershell
$setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
$setting.Name = "setting1"
$setting.CookieBasedAffinity = "enabled"
$setting.Port = 80
$setting.Protocol = "http"
```

Vytvořte naslouchací proces.

```powershell
$listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
$listener.Name = "listener1"
$listener.FrontendPort = "fep1"
$listener.FrontendIP = "fip1"
$listener.Protocol = "http"
$listener.SslCert = ""
```

Vytvořte pravidlo.

```powershell
$rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
$rule.Name = "rule1"
$rule.Type = "basic"
$rule.BackendHttpSettings = "setting1"
$rule.Listener = "listener1"
$rule.BackendAddressPool = "pool1"
```

#### <a name="step-2"></a>Krok 2

Přiřaďte všechny položky individuální konfigurace objektu konfigurace aplikační brány ($appgwconfig).

Přidejte front-end IP adresu ke konfiguraci.

```powershell
$appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
$appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
$appgwconfig.FrontendIPConfigurations.Add($fip)
```

Přidejte front-end port ke konfiguraci.

```powershell
$appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
$appgwconfig.FrontendPorts.Add($fep)
```
Přidejte fond back-end serverů ke konfiguraci.

```powershell
$appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
$appgwconfig.BackendAddressPools.Add($pool)
```

Přidejte nastavení back-end fondu ke konfiguraci.

```powershell
$appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
$appgwconfig.BackendHttpSettingsList.Add($setting)
```

Přidejte naslouchací proces ke konfiguraci.

```powershell
$appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
$appgwconfig.HttpListeners.Add($listener)
```

Přidejte pravidlo ke konfiguraci.

```powershell
$appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
$appgwconfig.HttpLoadBalancingRules.Add($rule)
```

### <a name="step-3"></a>Krok 3
Potvrďte objekt konfigurace k prostředku služby Application Gateway pomocí rutiny `Set-AzureApplicationGatewayConfig`.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig
```

## <a name="start-the-gateway"></a>Spusťte bránu

Jakmile se brána nakonfiguruje, pomocí rutiny `Start-AzureApplicationGateway` ji spusťte. Fakturace aplikační brány se spustí až po úspěšném spuštění brány.

> [!NOTE]
> Dokončení rutiny `Start-AzureApplicationGateway` může trvat 15 až 20 minut.

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Ověřte stav brány.

Pomocí rutiny `Get-AzureApplicationGateway` zkontrolujte stav brány. Pokud se v předcházejícím kroku podařilo úspěšně spustit rutinu `Start-AzureApplicationGateway`, položka *State* (Stav) by měla mít hodnotu Running (Spuštěno) a *Vip* a *DnsName* by měly obsahovat platné položky.

Následující příklad ukazuje aplikační bránu, která je aktivní, spuštěná a připravená přijmout provoz určený pro `http://<generated-dns-name>.cloudapp.net`.

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
Name          : AppGwTest
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
Vip           : 138.91.170.26
DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net
```

## <a name="delete-the-application-gateway"></a>Odstranění služby Application Gateway

Chcete-li odstranit službu Application Gateway:

1. Pomocí rutiny `Stop-AzureApplicationGateway` zastavte bránu.
2. Pomocí rutiny `Remove-AzureApplicationGateway` bránu odeberte.
3. Zkontrolujte odstranění brány pomocí rutiny `Get-AzureApplicationGateway`.

Následující příklad ukazuje rutinu `Stop-AzureApplicationGateway` na prvním řádku, následovanou výstupem.

```powershell
Stop-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Jakmile je služba Application Gateway v zastaveném stavu, pomocí rutiny `Remove-AzureApplicationGateway` službu odstraňte.

```powershell
Remove-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

Pokud chcete zkontrolovat, že se služba odstranila, můžete použít rutinu `Get-AzureApplicationGateway`. Tenhle krok není povinný.

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
.....
```

## <a name="next-steps"></a>Další kroky

Pokud chcete konfigurovat přesměrování zpracování SSL, přejděte do části [Konfigurace aplikační brány pro přesměrování zpracování SSL](application-gateway-ssl.md).

Pokud chcete provést konfiguraci aplikační brány pro použití s interním nástrojem pro vyrovnávání zatížení, přečtěte si část [Vytvoření aplikační brány s interním nástrojem pro vyrovnávání zatížení (ILB)](application-gateway-ilb.md).

Pokud chcete další informace o obecných možnostech vyrovnávání zatížení, přečtěte si část:

* [Nástroj pro vyrovnávání zatížení Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

[scenario]: ./media/application-gateway-create-gateway/scenario.png
