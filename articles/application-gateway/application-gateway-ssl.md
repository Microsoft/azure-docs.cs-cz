---
title: Přesměrování zpracování SSL pomocí PowerShellu – Azure Application Gateway
description: Tento článek poskytuje pokyny k vytvoření aplikační brány s přesměrováním SSL pomocí modelu nasazení Azure Classic.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: c456a0856adb0d36349b5f96ba0ab8bab3eec5c9
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74047924"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Konfigurace aplikační brány pro přesměrování zpracování SSL pomocí modelu nasazení Classic

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure Classic PowerShell](application-gateway-ssl.md)
> * [Azure CLI](application-gateway-ssl-cli.md)

Služba Azure Application Gateway se dá nakonfigurovat k ukončení relace Secure Sockets Layer (SSL) v bráně, vyhnete se tak nákladným úlohám dešifrování SSL na webové serverové farmě. Přesměrování zpracování SSL zjednodušuje i nastavení a správu front-end serverů webových aplikací.

## <a name="before-you-begin"></a>Než začnete

1. Nainstalujte nejnovější verzi rutin prostředí Azure PowerShell pomocí instalační služby webové platformy. Můžete stáhnout a nainstalovat nejnovější verzi **Windows PowerShell** z oddílu [Stránka se soubory ke stažení](https://azure.microsoft.com/downloads/).
2. Ověřte, že máte funkční virtuální síť s platnou podsítí. Ujistěte se, že žádné virtuální počítače nebo cloudová nasazení nepoužívají podsíť. Služba Application Gateway musí být sama o sobě v podsíti virtuální sítě.
3. Servery, které nakonfigurujete pro použití služby Application Gateway, musí existovat nebo mít své koncové body vytvořené buď ve virtuální síti, nebo s přiřazenou veřejnou IP adresou nebo virtuální IP adresou (VIP).

Pokud chcete na aplikační bráně nakonfigurovat přesměrování zpracování SSL, proveďte následující kroky v uvedeném pořadí:

1. [Vytvoření aplikační brány](#create-an-application-gateway)
2. [Nahrání certifikátů SSL](#upload-ssl-certificates)
3. [Konfigurace brány](#configure-the-gateway)
4. [Nastavení konfigurace brány](#set-the-gateway-configuration)
5. [Spustit bránu](#start-the-gateway)
6. [Ověření stavu brány](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Bránu vytvoříte tak, že zadáte rutinu `New-AzureApplicationGateway` a nahradíte hodnoty vlastními. Fakturace brány se nespustí v tomhle okamžiku. Fakturace začíná v pozdější fázi, po úspěšném spuštění brány.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Pokud chcete ověřit, jestli se brána vytvořila, můžete zadat rutinu `Get-AzureApplicationGateway`.

Ve vzorku, **Description**, **InstanceCount**a **GatewaySize** jsou volitelné parametry. Výchozí hodnota pro **InstanceCount** je **2**, maximální hodnota je **10**. Výchozí hodnota pro **GatewaySize** je **střední**. Malá a velká jsou další dostupné hodnoty. **VirtualIPs** a **DnsName** se zobrazí jako prázdné, protože brána ještě není spuštěná. Tyto hodnoty se vytvoří po tom, co je brána ve stavu spuštěno.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Nahrání certifikátů SSL

Zadejte `Add-AzureApplicationGatewaySslCertificate` pro nahrání certifikátu serveru ve formátu PFX do aplikační brány. Název certifikátu je název zvolený uživatelem a musí být jedinečný v rámci služby Application Gateway. Tento certifikát se na tento název odkazuje ve všech operacích správy certifikátů ve službě Application Gateway.

Následující ukázka znázorňuje rutinu. Hodnoty v ukázce nahraďte vlastními.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Dále ověřte odeslání certifikátu. Zadejte rutinu `Get-AzureApplicationGatewayCertificate`.

Následující ukázka znázorňuje rutinu na prvním řádku, následovanou výstupem:

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
> Heslo certifikátu musí mít 4 až 12 znaků, které se skládají z písmen nebo číslic. Speciální znaky nejsou přijímány.

## <a name="configure-the-gateway"></a>Konfigurace brány

Konfigurace služby Application Gateway se skládá z více hodnot. Hodnoty mohou být spojeny dohromady, aby bylo možné vytvořit konfiguraci.

Hodnoty jsou:

* **Fond back-end serverů**: seznam IP adres back-end serverů. Uvedené IP adresy by měly patřit do podsítě virtuální sítě, nebo by měly být veřejné IP adresy nebo adresy VIP.
* **Nastavení fondu back-end serverů**: každý fond má nastavení, jako je port, protokol a spřažení na základě souborů cookie. Tato nastavení se vážou na fond a používají se na všechny servery v rámci fondu.
* **Front-end port**: Tento port je veřejný port, který je otevřen ve službě Application Gateway. Když datový přenos dorazí na tento port, přesměruje se na některý back-end server.
* **Naslouchací proces**: naslouchací proces má front-end port, protokol (http nebo https; u těchto hodnot se rozlišují malá a velká písmena) a název certifikátu SSL (Pokud se konfiguruje přesměrování zpracování SSL).
* **Pravidlo**: pravidlo váže naslouchací proces a fond back-end serverů a definuje, ke kterému fondu back-end serverů se má směrovat provoz, když narazí na konkrétní naslouchací proces. V tuhle chvíli se podporuje jenom *základní* pravidlo. *Základní* pravidlo je distribuce zatížení pomocí kruhového dotazování.

**Další poznámky ke konfiguraci**

Pro konfiguraci certifikátů SSL by se měl změnit protokol v **HttpListener** na **Https** (rozlišování velkých a malých písmen). Přidejte element **SslCert** do **HttpListener** s hodnotou nastavenou na stejný název, který se používá v části [nahrát certifikáty SSL](#upload-ssl-certificates) . Front-end port by měl být aktualizován na **443**.

**Povolení spřažení na základě souborů cookie**: můžete nakonfigurovat službu Application Gateway, aby se zajistilo, že žádost z relace klienta bude vždycky směrována na stejný virtuální počítač ve webové farmě. Chcete-li to provést, vložte soubor cookie relace, který umožní bráně správně směrovat provoz. Když chcete povolit spřažení na základě souboru cookie, nastavte **CookieBasedAffinity** na **Povoleno** v elementu **BackendHttpSettings**.

Konfiguraci můžete vytvořit buď vytvořením objektu konfigurace, nebo pomocí konfiguračního souboru XML.
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

Dále nastavte aplikační bránu. Rutinu `Set-AzureApplicationGatewayConfig` můžete zadat buď pomocí konfiguračního objektu, nebo konfiguračního souboru XML.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Spusťte bránu

Po nakonfigurování brány zadejte rutinu `Start-AzureApplicationGateway` pro spuštění brány. Fakturace aplikační brány se spustí až po úspěšném spuštění brány.

> [!NOTE]
> Dokončení rutiny `Start-AzureApplicationGateway` může trvat 15-20 minut.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Ověřte stav brány.

Zadejte rutinu `Get-AzureApplicationGateway`, abyste zkontrolovali stav brány. Pokud se v předchozím kroku `Start-AzureApplicationGateway` úspěšně, **stav** by měl být **spuštěný**a **VirtualIPs** a **DnsName** by měly mít platné položky.

V této ukázce vidíte Aplikační bránu, která je spuštěná, spuštěná a připravená k přijetí provozu:

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

Další informace o možnostech vyrovnávání zatížení obecně najdete v těchto tématech:

* [Nástroj pro vyrovnávání zatížení Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
