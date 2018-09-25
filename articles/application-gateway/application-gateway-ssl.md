---
title: Konfigurace SSL snižování zátěže - Azure Application Gateway – PowerShell classic | Dokumentace Microsoftu
description: Tento článek poskytuje pokyny k vytvoření služby application gateway s protokolem SSL snižování zátěže pomocí modelu nasazení Azure classic
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
ms.openlocfilehash: 782e5c4b33cc62ab5af80e823dc63b3e79a707b3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980521"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Konfigurace aplikační brány pro přesměrování zpracování SSL pomocí modelu nasazení classic

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure classic PowerShell](application-gateway-ssl.md)
> * [Azure CLI](application-gateway-ssl-cli.md)

Služba Azure Application Gateway se dá nakonfigurovat k ukončení relace Secure Sockets Layer (SSL) v bráně, vyhnete se tak nákladným úlohám dešifrování SSL na webové serverové farmě. Přesměrování zpracování SSL zjednodušuje i nastavení a správu front-end serverů webových aplikací.

## <a name="before-you-begin"></a>Než začnete

1. Nainstalujte nejnovější verzi rutin prostředí Azure PowerShell pomocí instalační služby webové platformy. Nejnovější verzi můžete stáhnout a nainstalovat v části **Windows PowerShell** na stránce [Položky ke stažení](https://azure.microsoft.com/downloads/).
2. Ověřte, že máte funkční virtuální síť s platnou podsítí. Ujistěte se, že žádné virtuální počítače nebo cloudová nasazení nepoužívají podsíť. Služba Application Gateway musí být sama o sobě v podsíti virtuální sítě.
3. Servery, které nakonfigurujete pro použití služby application gateway musí existovat nebo mít své koncové body vytvořené buď ve virtuální síti nebo veřejné IP adresy nebo virtuální IP adresa (VIP) přiřazen.

Pokud chcete konfigurovat přesměrování zpracování SSL ve službě application gateway, proveďte následující kroky v uvedeném pořadí:

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

Chcete ověřit vytvoření brány, můžete zadat `Get-AzureApplicationGateway` rutiny.

V ukázce **popis**, **InstanceCount**, a **GatewaySize** jsou volitelné parametry. Výchozí hodnota pro **InstanceCount** je **2**, s maximální hodnotou **10**. Výchozí hodnota pro **GatewaySize** je **střední**. Malé a velké jsou další dostupné hodnoty. **Rezervovaná** a **DnsName** se zobrazují jako prázdné, protože brána ještě nespustila. Tyto hodnoty se vytvoří, jakmile je brána v běžícím stavu.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Nahrát certifikáty SSL

Zadejte `Add-AzureApplicationGatewaySslCertificate` nahrát certifikát serveru ve formátu PFX ke službě application gateway. Název certifikátu je zvolené uživatelské jméno a musí být jedinečné v rámci application gateway. Tento certifikát se označuje s tímto názvem v všechny operace správy certifikátů ve službě application gateway.

Následující příklad ukazuje rutinu. Nahraďte hodnotami v ukázce vlastní.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

V dalším kroku ověřte nahrání certifikátu. Zadejte `Get-AzureApplicationGatewayCertificate` rutiny.

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
> Heslo certifikátu musí být mezi 4 až 12 znaků z písmen nebo číslic. Speciálních znaků se nepřijímají.

## <a name="configure-the-gateway"></a>Konfigurace brány

Konfigurace aplikační brány se skládá z více hodnot. Hodnotami může být vázán společně k vytvoření konfigurace.

Hodnoty jsou:

* **Fond back-end serverů**: seznam IP adres back-end serverů. Uvedené IP adresy by měly patřit do podsítě virtuální sítě nebo by měla být veřejná adresa IP nebo virtuální IP adresy.
* **Nastavení fondu back endového serveru**: každý fond má nastavení, jako je port, protokol a spřažení na základě souborů cookie. Tato nastavení se vážou na fond a používají se na všechny servery v rámci fondu.
* **Front-endový port**: Toto je veřejný port, který se otevírá ve službě application gateway. Když datový přenos dorazí na tento port, přesměruje se na některý back-end server.
* **Naslouchací proces**: naslouchací proces má front-end port, protokol (Http nebo Https; tyto hodnoty jsou malá a velká písmena) a název certifikátu SSL (Pokud je konfigurace přesměrování zpracování SSL).
* **Pravidlo**: pravidlo váže naslouchací proces a fond back-end serverů a definuje, kterému fondu back endového serveru pro směrování provozu do při volání příslušného naslouchacího procesu. V tuhle chvíli se podporuje jenom *základní* pravidlo. *Základní* pravidlo je distribuce zatížení pomocí kruhového dotazování.

**Další poznámky ke konfiguraci**

Pro konfiguraci certifikátů SSL by se měl změnit protokol v **HttpListener** na **Https** (rozlišování velkých a malých písmen). Přidat **SslCert** elementu **HttpListener** s hodnotou nastavenou na stejný název používá v [certifikáty SSL nahrát](#upload-ssl-certificates) oddílu. Front-end port se musí aktualizovat na **443**.

**Chcete povolit spřažení na základě souborů cookie**: můžete nakonfigurovat službu application gateway k zajištění, že žádost od klientské relace vždy směrovala na stejný virtuální počítač ve webové farmě. Chcete-li to provést, vložte souboru cookie relace, který umožňuje bráně řízení provozu odpovídajícím způsobem. Když chcete povolit spřažení na základě souboru cookie, nastavte **CookieBasedAffinity** na **Povoleno** v elementu **BackendHttpSettings**.

Konfiguraci lze vytvořit buď tím, že vytvoříte objekt konfigurace, nebo pomocí konfiguračního souboru XML.
Vytvořit konfiguraci pomocí konfiguračního souboru XML, zadejte následující ukázce:


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

Dále nastavte aplikační bránu. Můžete zadat `Set-AzureApplicationGatewayConfig` rutinu s konfiguračním souborem XML nebo objekt konfigurace.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Spusťte bránu

Po dokončení konfigurace brány, zadejte `Start-AzureApplicationGateway` ke spuštění brány. Fakturace aplikační brány se spustí až po úspěšném spuštění brány.

> [!NOTE]
> `Start-AzureApplicationGateway` Rutiny může trvat 15 až 20 minut na dokončení.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Ověřte stav brány.

Zadejte `Get-AzureApplicationGateway` rutiny a zkontrolujte stav brány. Pokud `Start-AzureApplicationGateway` v předchozím kroku podařilo úspěšně **stavu** by měl být **systémem**a **rezervovaná** a **DnsName** by měl obsahovat platné položky.

Tento příklad ukazuje aplikační bránu, která je v provozu, spuštěný a připravená přijmout provoz:

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

Další informace o službě Vyrovnávání zatížení možnosti naleznete v tématu:

* [Nástroj pro vyrovnávání zatížení Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
