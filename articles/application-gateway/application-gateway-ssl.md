---
title: Konfigurace protokolu SSL snižování zátěže - Azure Application Gateway - PowerShell classic | Microsoft Docs
description: Tento článek poskytuje pokyny pro vytvoření služby application gateway pomocí protokolu SSL, přesměrování zpracování úloh pomocí modelu nasazení Azure classic
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 63f28d96-9c47-410e-97dd-f5ca1ad1b8a4
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: victorh
ms.openlocfilehash: e620730b86d648c1ac9db7a9e6faa7a2d206b46e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
ms.locfileid: "33205376"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Konfigurace aplikační brány pro přesměrování zpracování SSL pomocí modelu nasazení classic

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure classic PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Služba Azure Application Gateway se dá nakonfigurovat k ukončení relace Secure Sockets Layer (SSL) v bráně, vyhnete se tak nákladným úlohám dešifrování SSL na webové serverové farmě. Přesměrování zpracování SSL zjednodušuje i nastavení a správu front-end serverů webových aplikací.

## <a name="before-you-begin"></a>Než začnete

1. Nainstalujte nejnovější verzi rutin prostředí Azure PowerShell pomocí instalační služby webové platformy. Nejnovější verzi můžete stáhnout a nainstalovat v části **Windows PowerShell** na stránce [Položky ke stažení](https://azure.microsoft.com/downloads/).
2. Ověřte, že máte funkční virtuální síť s platnou podsítí. Ujistěte se, že žádné virtuální počítače nebo cloudová nasazení nepoužívají podsíť. Služba Application Gateway musí být sama o sobě v podsíti virtuální sítě.
3. Servery, které nakonfigurujete pro použití služby application gateway musí existovat nebo musí mít své koncové body, které jsou vytvořené ve virtuální síti nebo s veřejnou IP adresu nebo virtuální adresy IP (VIP) přiřazené.

Pokud chcete konfigurovat přesměrování zpracování SSL na aplikační brány, proveďte následující kroky v uvedeném pořadí:

1. [Vytvoření služby application gateway](#create-an-application-gateway)
2. [Nahrát certifikáty SSL](#upload-ssl-certificates)
3. [Konfigurace brány](#configure-the-gateway)
4. [Nastavení konfigurace brány](#set-the-gateway-configuration)
5. [Spusťte bránu](#start-the-gateway)
6. [Ověřte stav brány.](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Chcete-li vytvořit bránu, zadejte `New-AzureApplicationGateway` rutinu a nahraďte hodnoty vlastními. Fakturace brány se nespustí v tomhle okamžiku. Fakturace začíná v pozdější fázi, po úspěšném spuštění brány.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Chcete-li ověřit, že vytvoření brány, můžete zadat `Get-AzureApplicationGateway` rutiny.

V ukázce **popis**, **InstanceCount**, a **GatewaySize** jsou volitelné parametry. Výchozí hodnota pro **InstanceCount** je **2**, s maximální hodnotou **10**. Výchozí hodnota pro **GatewaySize** je **střední**. Malých a velkých jsou ostatní dostupné hodnoty. **Rezervovaná** a **DnsName** se zobrazují jako prázdné, protože brána se ještě nespustilo. Tyto hodnoty jsou vytvořeny po brána je v běžícím stavu.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Nahrát certifikáty SSL

Zadejte `Add-AzureApplicationGatewaySslCertificate` nahrát certifikát serveru ve formátu PFX aplikační brány. Název certifikátu je název vybrané uživatelem a musí být jedinečný v rámci služby application gateway. Tento certifikát se označuje s tímto názvem v všechny operace správy certifikátů ve službě application gateway.

Následující příklad ukazuje rutinu. Nahraďte vlastními hodnotami v ukázce.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Dále ověřte nahrávání certifikátu. Zadejte `Get-AzureApplicationGatewayCertificate` rutiny.

Následující příklad ukazuje rutinu na prvním řádku, následovanou výstupem:

```powershell
Get-AzureApplicationGatewaySslCertificate AppGwTest
```

```
VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
Name           : SslCert
SubjectName    : CN=gwcert.app.test.contoso.com
Thumbprint     : AF5ADD77E160A01A6......EE48D1A
ThumbprintAlgo : sha1RSA
State..........: Provisioned
```

> [!NOTE]
> Heslo certifikátu musí být mezi 4 až 12 znaků skládá z písmen nebo číslic. Speciální znaky nejsou přijata.

## <a name="configure-the-gateway"></a>Konfigurace brány

Konfigurace brány aplikace se skládá z více hodnot. Může být vázáno hodnoty společně k vytvoření konfigurace.

Hodnoty jsou:

* **Fond back-end serverů**: seznam IP adres back-end serverů. Uvedené IP adresy by měly patřit do podsítě virtuální sítě nebo by měla být veřejnou adresu IP nebo virtuální IP adresy.
* **Nastavení fondu back-end serverů**: každý fond má nastavení, jako je port, protokol a spřažení na základě souboru cookie. Tato nastavení se vážou na fond a používají se na všechny servery v rámci fondu.
* **Front-end port**: Tento port je veřejný port, který se otevírá ve službě application gateway. Když datový přenos dorazí na tento port, přesměruje se na některý back-end server.
* **Naslouchací proces**: naslouchací proces má front-end port, protokol (Http nebo Https; tyto hodnoty jsou malá a velká písmena) a název certifikátu SSL (Pokud je konfigurace přesměrování zpracování SSL).
* **Pravidlo**: pravidlo váže naslouchací proces a fond back-end serverů a definuje, kterému fondu back-end serverů směrovat provoz při volání příslušného naslouchacího procesu. V tuhle chvíli se podporuje jenom *základní* pravidlo. *Základní* pravidlo je distribuce zatížení pomocí kruhového dotazování.

**Další poznámky ke konfiguraci**

Pro konfiguraci certifikátů SSL by se měl změnit protokol v **HttpListener** na **Https** (rozlišování velkých a malých písmen). Přidat **SslCert** element **HttpListener** hodnota nastavena na stejný název používaný v [certifikáty SSL nahrát](#upload-ssl-certificates) části. Front-end port se musí aktualizovat na **443**.

**Chcete-li povolit spřažení na základě souborů cookie**: můžete nakonfigurovat služby application gateway zajistit, že žádost od klientské relace vždy směrovala na stejný virtuální počítač ve webové farmě. K tomu, vložte soubor cookie relace, který umožňuje bráně řízení provozu odpovídajícím způsobem. Když chcete povolit spřažení na základě souboru cookie, nastavte **CookieBasedAffinity** na **Povoleno** v elementu **BackendHttpSettings**.

Konfiguraci můžete vytvořit buď pomocí vytvoření objektu konfigurace, nebo pomocí konfiguračního souboru XML.
Chcete-li vytvořit konfiguraci pomocí konfiguračního souboru XML, zadejte následující ukázka:


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations />
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>443</Port>
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
            <Protocol>Https</Protocol>
            <SslCert>GWCert</SslCert>
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

Dále nastavte aplikační bránu. Můžete zadat `Set-AzureApplicationGatewayConfig` rutiny s objekt konfigurace nebo konfigurační soubor XML.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Spusťte bránu

Když brána je nakonfigurovaná, zadejte `Start-AzureApplicationGateway` rutinu spustit bránu. Fakturace aplikační brány se spustí až po úspěšném spuštění brány.

> [!NOTE]
> `Start-AzureApplicationGateway` Rutiny může trvat 15-20 minut na dokončení.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Ověřte stav brány.

Zadejte `Get-AzureApplicationGateway` rutiny a zkontrolujte stav brány. Pokud `Start-AzureApplicationGateway` úspěšné v předchozím kroku, **stavu** by měla být **systémem**a **rezervovaná** a **DnsName** by měl obsahovat platné položky.

Tento příklad ukazuje aplikační bránu, která je v provozu, spuštěná a připravená přijmout provoz:

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest2
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {23.96.22.241}
DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net
```

## <a name="next-steps"></a>Další postup

Další informace o službě Vyrovnávání zatížení možnosti obecně naleznete v tématu:

* [Nástroj pro vyrovnávání zatížení Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
