---
title: Azure Application Gateway pomocí nástroje pro vyrovnávání zatížení pro vnitřní | Microsoft Docs
description: Tato stránka obsahuje pokyny ke konfiguraci služby Azure Application Gateway s vyrovnáváním zatížení se vnitřní koncový bod
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 7403d28e-909f-46a2-b282-43a8e942f53c
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: victorh
ms.openlocfilehash: 5d5e70d46121252c616d77d33ee82397e3a3ca84
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Vytvoření brány Application Gateway s interním nástrojem Load Balancer (ILB)

> [!div class="op_single_selector"]
> * [Azure Classic PowerShell](application-gateway-ilb.md)
> * [Azure Resource Manager PowerShell](application-gateway-ilb-arm.md)

Application Gateway se dá nakonfigurovat s internetovým virtuální IP adresy nebo se vnitřní koncový bod není přístup k Internetu, také známé jako koncový bod interní nástroj pro vyrovnávání zatížení (ILB). Konfigurace brány pomocí ILB je užitečná pro interní-obchodní aplikace, které nejsou viditelné k Internetu. Je také užitečné pro/úrovně služeb v rámci vícevrstvé aplikace, která je umístěna v rámci hranice zabezpečení nejsou viditelné k Internetu, ale stále vyžadují distribuci zatížení kruhové dotazování, dlouhodobost relace nebo ukončení protokolu SSL. Tenhle článek vás provede kroky konfigurace aplikační brány s ILB.

## <a name="before-you-begin"></a>Než začnete

1. Nainstalujte nejnovější verzi rutin prostředí Azure PowerShell pomocí služby instalace webové platformy. Můžete stáhnout a nainstalovat nejnovější verzi **prostředí Windows PowerShell** části [stránky pro stažení](https://azure.microsoft.com/downloads/).
2. Ověřte, zda máte funkční virtuální síť s platnou podsítí.
3. Ověřte, zda máte back-end serverů ve virtuální síti nebo s veřejné nebo virtuálními IP Adresami přiřazen.

K vytvoření aplikační brány, proveďte následující kroky v uvedeném pořadí. 

1. [Vytvoření služby application gateway](#create-a-new-application-gateway)
2. [Konfigurace brány](#configure-the-gateway)
3. [Nastavení konfigurace brány](#set-the-gateway-configuration)
4. [Spusťte bránu](#start-the-gateway)
5. [Ověření brány](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Vytvoření služby application gateway:

**Chcete-li vytvořit bránu**, použijte `New-AzureApplicationGateway` rutinu a nahraďte hodnoty vlastními. Všimněte si, že fakturace brány se nespustí v tomhle okamžiku. Fakturace začíná v pozdější fázi, po úspěšném spuštění brány.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

```
VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399
```

**K ověření** , vytvoření brány, můžete použít `Get-AzureApplicationGateway` rutiny. 

V ukázce *popis*, *InstanceCount*, a *GatewaySize* jsou volitelné parametry. Výchozí hodnota *InstanceCount* je 2, přičemž maximální hodnota je 10. Výchozí hodnota *GatewaySize* je Medium (Střední). Malých a velkých jsou ostatní dostupné hodnoty. *VIP* a *DnsName* se zobrazují jako prázdné, protože brána se ještě nespustilo. Vytvoří se, jakmile bude brána v běžícím stavu. 

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 4:39:39 PM - Begin Operation:
Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
Operation: Get-AzureApplicationGateway
Name: AppGwTest    
Description: 
VnetName: testvnet1 
Subnets: {Subnet-1} 
InstanceCount: 2 
GatewaySize: Medium 
State: Stopped 
VirtualIPs: 
DnsName:
```

## <a name="configure-the-gateway"></a>Konfigurace brány
Konfigurace brány aplikace se skládá z více hodnot. Může být vázáno hodnoty společně k vytvoření konfigurace.

Hodnoty jsou:

* **Fond back-end serverů:** seznam IP adres back-end serverů. Uvedené IP adresy by měly buď patřit do podsítě virtuální sítě, nebo musí být veřejné IP Adrese nebo VIP. 
* **Nastavení fondu serverů back-end:** Každý fond má nastavení, jako je port, protokol a spřažení na základě souborů cookie. Tato nastavení se vážou na fond a používají se na všechny servery v rámci fondu.
* **Front-end Port:** Toto je veřejný port otevřít ve službě application gateway. Když provoz dorazí na tento port, přesměruje se na některý ze serverů back-end.
* **Naslouchací proces:** naslouchací proces má front-end port, protokol (Http nebo Https, ty jsou malá a velká písmena) a název certifikátu SSL (Pokud se konfiguruje přesměrování zpracování SSL). 
* **Pravidlo:** pravidlo váže naslouchací proces a fond back-end serverů a definuje, kterému fondu back-end serverů se provoz směrovat při volání příslušného naslouchacího procesu. V tuhle chvíli se podporuje jenom *základní* pravidlo. *Základní* pravidlo je distribuce zatížení pomocí kruhového dotazování.

Konfiguraci můžete vytvořit buď tak, že vytvoříte objekt konfigurace, nebo pomocí konfiguračního souboru XML. Chcete-li vytvořit konfiguraci pomocí konfiguračního souboru XML, použijte následující ukázka.

Je třeba počítat s následujícím:

* *FrontendIPConfigurations* element popisuje relevantní pro konfiguraci aplikační brány s ILB ILB podrobnosti. 
* IP front-endu *typu* musí být nastavena na "Privátní"
* *StaticIPAddress* musí být nastavená na požadované interních IP, na kterém přijímá brána provoz. Všimněte si, že *StaticIPAddress* prvek je volitelný. Pokud není sady, je zvolen k dispozici interní IP adresy z nasazené podsítě. 
* Hodnota *název* zadaný v elementu *FrontendIPConfiguration* by měly být použity HTTPListener *FrontendIP* element, který bude odkazovat na FrontendIPConfiguration.
  
  **Ukázkový kód XML konfigurace**
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name> 
            <Type>Private</Type> 
            <StaticIPAddress>10.0.0.10</StaticIPAddress> 
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>
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
            <FrontendIP>fip1</FrontendIP>
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


## <a name="set-the-gateway-configuration"></a>Nastavení konfigurace brány
Budete dále nastavte aplikační bránu. Můžete použít `Set-AzureApplicationGatewayConfig` rutiny objekt konfigurace, nebo s konfiguračním souborem XML. 

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

```
VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d
```

## <a name="start-the-gateway"></a>Spusťte bránu

Jakmile se brána nakonfiguruje, pomocí rutiny `Start-AzureApplicationGateway` ji spusťte. Fakturace aplikační brány se spustí až po úspěšném spuštění brány. 

> [!NOTE]
> `Start-AzureApplicationGateway` Rutiny může trvat až 15-20 minut. 
> 
> 

```powershell
Start-AzureApplicationGateway AppGwTest 
```

```
VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b
```

## <a name="verify-the-gateway-status"></a>Ověřte stav brány.

Použití `Get-AzureApplicationGateway` rutiny a zkontrolujte stav brány. Pokud `Start-AzureApplicationGateway` byly úspěšné v předchozím kroku, musí být stav *systémem*, a virtuální IP adresy a DnsName musí obsahovat platné položky. Tento příklad ukazuje rutinu na prvním řádku, následovanou výstupem. V této ukázce brána je spuštěná a je připraven přijmout provoz. 

> [!NOTE]
> Application gateway je nakonfigurovat tak, aby přijímal provoz na nakonfigurovaný koncový bod ILB 10.0.0.10 v tomto příkladu.

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
VirtualIPs    : {10.0.0.10}
DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net
```

## <a name="next-steps"></a>Další postup
Pokud chcete další informace o obecných možnostech vyrovnávání zatížení, přečtěte si část:

* [Nástroj pro vyrovnávání zatížení Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

