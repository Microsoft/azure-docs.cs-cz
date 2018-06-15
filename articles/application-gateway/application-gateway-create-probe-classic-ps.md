---
title: Vytvořit vlastní test paměti - Azure Application Gateway - klasický prostředí PowerShell | Microsoft Docs
description: Naučte se vytvářet vlastní test paměti pro službu Application Gateway pomocí prostředí PowerShell v modelu nasazení classic
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: 338a7be1-835c-48e9-a072-95662dc30f5e
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: 97d1376dc7908b72d8e8ec15145229cf3cf4acae
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
ms.locfileid: "33201942"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Vytvoření vlastní test paměti pro Azure Application Gateway (klasické pomocí prostředí PowerShell)

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

V tomto článku přidejte vlastní test paměti existující application gateway pomocí prostředí PowerShell. Vlastní testy paměti jsou užitečné pro aplikace, které mají na stránce Kontrola konkrétní stav nebo pro aplikace, které neposkytuje úspěšné odpovědi na výchozí webovou aplikaci.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak [provést tento postup pomocí modelu Resource Manageru](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Pro vytvoření nové aplikační brány:

1. Vytvořte prostředek aplikační brány.
2. Vytvořte konfigurační soubor XML nebo objekt konfigurace.
3. Potvrďte konfiguraci nově vytvořeného prostředku aplikační brány.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Vytvořte prostředek aplikační brány s vlastní test paměti

Pokud chcete vytvořit bránu, použijte rutinu `New-AzureApplicationGateway` a zadejte vlastní hodnoty. Fakturace brány se nespustí v tomhle okamžiku. Fakturace začíná v pozdější fázi, po úspěšném spuštění brány.

Následující příklad vytvoří aplikační bránu pomocí virtuální sítě s názvem „testvnet1“ a podsítě s názvem „subnet-1“.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Pokud chcete ověřit vytvoření brány, můžete použít rutinu `Get-AzureApplicationGateway`.

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> Výchozí hodnota *InstanceCount* je 2, přičemž maximální hodnota je 10. Výchozí hodnota *GatewaySize* je Medium (Střední). Můžete zvolit malá, střední a velké.
> 
> 

Hodnoty *VirtualIPs* a *DnsName* se zobrazují jako prázdné, protože se brána ještě nespustila. Tyto hodnoty se vytvoří, jakmile je brána v běžícím stavu.

### <a name="configure-an-application-gateway-by-using-xml"></a>Nakonfigurujte aplikační bránu pomocí XML

V následujícím příkladu použijete soubor XML k nakonfigurování všech nastavení aplikační brány a potvrdíte je pro prostředek aplikační brány.  

Zkopírujte následující text do Poznámkového bloku.

```xml
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Upravte hodnoty položek konfigurace v závorkách. Uložte soubor s příponou .xml.

Následující příklad ukazuje, jak pomocí konfiguračního souboru k nastavení aplikační brány, aby vyrovnávala zatížení provozu HTTP na veřejném portu 80 a odesílat přenos v síti na back-end port 80 mezi dvě IP adresy pomocí vlastní test paměti.

> [!IMPORTANT]
> Položka protokolu Http nebo Https rozlišuje velká a malá písmena.

Nová položka konfigurace \<testu\> se přidá do nakonfigurovat vlastní testy paměti.

Parametry konfigurace jsou:

|Parametr|Popis|
|---|---|
|**Název** |Název odkazu pro vlastní test paměti. |
* **Protokol** | Protokol použitý (možné hodnoty jsou protokolu HTTP nebo HTTPS).|
| **Hostitele** a **cesta** | Dokončete cestu adresy URL, která je volána bránou aplikace k určení stavu instance. Například, pokud máte web http://contoso.com/, pak je možné nakonfigurovat vlastní test paměti pro "http://contoso.com/path/custompath.htm" pro test kontroluje tak, aby měl úspěšné odpovědi HTTP.|
| **Interval** | Nakonfiguruje interval kontroly testu v sekundách.|
| **Časový limit** | Definuje časový limit testu pro kontrolu odpovědi HTTP.|
| **UnhealthyThreshold** | Počet neúspěšných odpovědí HTTP, které jsou potřebné pro příznak instance back-end jako *není v pořádku*.|

Název sondy odkazuje \<BackendHttpSettings\> konfigurace přiřadit kterému fondu back-end používá nastavení vlastní test paměti.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Přidat vlastní test paměti existující aplikační brány

Změna aktuální konfiguraci služby application gateway vyžaduje tři kroky: získání aktuálního konfiguračního souboru XML, upravit tak, aby měl vlastní test paměti a nakonfigurujte aplikační bránu s novým nastavením XML.

1. Načíst soubor XML s použitím `Get-AzureApplicationGatewayConfig`. Tato rutina exportuje konfiguraci XML tak, aby upravit tak, aby přidat nastavení testu.

  ```powershell
  Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
  ```

1. V textovém editoru otevřete soubor XML. Přidat `<probe>` části po `<frontendport>`.

  ```xml
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
</Probes>
  ```

  V oddílu backendHttpSettings XML přidejte název sondy, jak je znázorněno v následujícím příkladu:

  ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
  ```

  Uložte soubor XML.

1. Aktualizace konfigurace brány aplikace pomocí nového souboru XML s použitím `Set-AzureApplicationGatewayConfig`. Tato rutina aktualizuje aplikační bránu s novou konfiguraci.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Další postup

Pokud chcete konfigurovat přesměrování zpracování Secure Sockets Layer (SSL), přečtěte si téma [konfigurace aplikační brány pro přesměrování zpracování SSL](application-gateway-ssl.md).

Pokud chcete provést konfiguraci aplikační brány pro použití s interním nástrojem pro vyrovnávání zatížení, přečtěte si část [Vytvoření aplikační brány s interním nástrojem pro vyrovnávání zatížení (ILB)](application-gateway-ilb.md).

