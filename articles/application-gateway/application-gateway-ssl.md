---
title: Přepětí ssl pomocí PowerShellu – aplikační brána Azure
description: Tento článek obsahuje pokyny k vytvoření aplikační brány s ssl zátěže pomocí modelu klasického nasazení Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: c456a0856adb0d36349b5f96ba0ab8bab3eec5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74047924"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Konfigurace aplikační brány pro přepětí ssl pomocí klasického modelu nasazení

> [!div class="op_single_selector"]
> * [Portál Azure](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Klasické PowerShell Azure](application-gateway-ssl.md)
> * [Azure CLI](application-gateway-ssl-cli.md)

Služba Azure Application Gateway se dá nakonfigurovat k ukončení relace Secure Sockets Layer (SSL) v bráně, vyhnete se tak nákladným úlohám dešifrování SSL na webové serverové farmě. Přesměrování zpracování SSL zjednodušuje i nastavení a správu front-end serverů webových aplikací.

## <a name="before-you-begin"></a>Než začnete

1. Nainstalujte nejnovější verzi rutin prostředí Azure PowerShell pomocí instalační služby webové platformy. Nejnovější verzi můžete stáhnout a nainstalovat v části **Windows PowerShell** na stránce [Položky ke stažení](https://azure.microsoft.com/downloads/).
2. Ověřte, že máte funkční virtuální síť s platnou podsítí. Ujistěte se, že tuto podsíť nepoužívají žádné virtuální počítače ani cloudová nasazení. Služba Application Gateway musí být sama o sobě v podsíti virtuální sítě.
3. Servery, které nakonfigurujete pro použití aplikační brány, musí existovat nebo jim musí být přiřazeny jejich koncové body, které jsou vytvořeny ve virtuální síti nebo s veřejnou IP adresou nebo virtuální IP adresou (VIP).

Chcete-li nakonfigurovat přepočet ssl na aplikační bráně, proveďte následující kroky v uvedeném pořadí:

1. [Vytvoření aplikační brány](#create-an-application-gateway)
2. [Nahrání certifikátů SSL](#upload-ssl-certificates)
3. [Konfigurace brány](#configure-the-gateway)
4. [Nastavení konfigurace brány](#set-the-gateway-configuration)
5. [Spusťte bránu](#start-the-gateway)
6. [Ověřte stav brány.](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Chcete-li vytvořit bránu, zadejte rutinu `New-AzureApplicationGateway` a nahraďte hodnoty vlastními. Fakturace brány se nespustí v tomhle okamžiku. Fakturace začíná v pozdější fázi, po úspěšném spuštění brány.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Chcete-li ověřit, zda byla brána vytvořena, můžete zadat rutinu. `Get-AzureApplicationGateway`

V ukázce jsou volitelné parametry **Description**, **InstanceCount**a **GatewaySize.** Výchozí hodnota pro **InstanceCount** je **2**s maximální hodnotou **10**. Výchozí hodnota pro **GatewaySize** je **Střední**. Malé a Velké jsou další dostupné hodnoty. **VirtualIP** a **DnsName** jsou zobrazeny jako prázdné, protože brána ještě nebyla spuštěna. Tyto hodnoty jsou vytvořeny po brány je ve spuštěném stavu.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Nahrání certifikátů SSL

Zadáním `Add-AzureApplicationGatewaySslCertificate` této možnosti nahrajete certifikát serveru ve formátu PFX do aplikační brány. Název certifikátu je uživatelem zvolený název a musí být jedinečný v rámci brány aplikace. Tento certifikát je označován tímto názvem ve všech operacích správy certifikátů v bráně aplikace.

Následující ukázka ukazuje rutinu. Nahraďte hodnoty v ukázce vlastními hodnotami.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Dále ověřte nahrání certifikátu. Zadejte `Get-AzureApplicationGatewayCertificate` rutinu.

Následující ukázka ukazuje rutinu na prvním řádku, následovanou výstupem:

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
> Heslo certifikátu musí být mezi 4 až 12 znaky tvořené písmeny nebo číslicemi. Speciální znaky nejsou akceptovány.

## <a name="configure-the-gateway"></a>Konfigurace brány

Konfigurace aplikační brány se skládá z více hodnot. Hodnoty mohou být spojeny dohromady k vytvoření konfigurace.

Hodnoty jsou:

* **Back-end server fond**: Seznam IP adres serverů back-end. Uvedené IP adresy by měly patřit do podsítě virtuální sítě nebo by měly být veřejnou IP nebo VIP adresou.
* **Nastavení fondu serverů back-end**: Každý fond má nastavení, jako je port, protokol a spřažení založené na souborech cookie. Tato nastavení se vážou na fond a používají se na všechny servery v rámci fondu.
* **Front-end port**: Tento port je veřejný port, který je otevřen v bráně aplikace. Když datový přenos dorazí na tento port, přesměruje se na některý back-end server.
* **Naslouchací proces**: Naslouchací proces má front-end port, protokol (Http nebo Https; tyto hodnoty jsou malá a velká písmena) a název certifikátu SSL (pokud konfigurace ssl offload).
* **Pravidlo**: Pravidlo váže naslouchací proces a fond serveru back-end a definuje, který back-end ový fond serveru směřuje k přenosu, když narazí na konkrétní naslouchací proces. V tuhle chvíli se podporuje jenom *základní* pravidlo. *Základní* pravidlo je distribuce zatížení pomocí kruhového dotazování.

**Další poznámky ke konfiguraci**

Pro konfiguraci certifikátů SSL by se měl změnit protokol v **HttpListener** na **Https** (rozlišování velkých a malých písmen). Přidejte element **SslCert** do **httplisteneru** s hodnotou nastavenou na stejný název použitý v části [Nahrát certifikáty SSL.](#upload-ssl-certificates) Front-end port by měl být aktualizován na **443**.

**Povolení spřažení založené na souborech cookie**: Můžete nakonfigurovat aplikační bránu, abyste zajistili, že požadavek z relace klienta bude vždy směrován na stejný virtuální počítač ve webové farmě. Chcete-li to provést, vložte soubor cookie relace, který umožňuje bráně správně směrovat provoz. Když chcete povolit spřažení na základě souboru cookie, nastavte **CookieBasedAffinity** na **Povoleno** v elementu **BackendHttpSettings**.

Konfiguraci můžete vytvořit buď vytvořením konfiguračního objektu, nebo pomocí konfiguračního souboru XML.
Chcete-li vytvořit konfiguraci pomocí konfiguračního souboru XML, zadejte následující ukázku:


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
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

Dále nastavte aplikační bránu. Rutinu `Set-AzureApplicationGatewayConfig` můžete zadat buď pomocí konfiguračního objektu, nebo pomocí konfiguračního souboru XML.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Spusťte bránu

Po konfiguraci brány zadejte `Start-AzureApplicationGateway` rutinu pro spuštění brány. Fakturace aplikační brány se spustí až po úspěšném spuštění brány.

> [!NOTE]
> Rutina `Start-AzureApplicationGateway` může trvat 15-20 minut.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Ověřte stav brány.

Zadáním `Get-AzureApplicationGateway` rutiny zkontrolujte stav brány. Pokud `Start-AzureApplicationGateway` byl v předchozím kroku úspěšný, **měl** by být **stav spuštěn**a **virtualipy** a **dnsname** by měly mít platné položky.

Tato ukázka ukazuje bránu aplikace, která je v provozu, běží a je připravena k přenosu:

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

## <a name="next-steps"></a>Další kroky

Další informace o možnostech vyrovnávání zatížení obecně naleznete v tématu:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
