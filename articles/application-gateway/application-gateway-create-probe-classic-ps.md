---
title: Vytvoření vlastní test paměti – Azure Application Gateway – PowerShell – classic | Dokumentace Microsoftu
description: Informace o vytvoření vlastního testu paměti pro službu Application Gateway pomocí prostředí PowerShell v modelu nasazení classic
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
ms.openlocfilehash: 2b661968fd64f4d2a61bc59f9b99b1eea6b01f86
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997271"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Vytvoření vlastního testu paměti pro službu Azure Application Gateway (classic pomocí prostředí PowerShell)

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

V tomto článku přidáte vlastní test paměti existující aplikační bráně pomocí Powershellu. Vlastní sondy jsou užitečné pro aplikace, které mají konkrétní stránky kontroly stavu nebo pro aplikace, které neposkytují úspěšné odpovědi na výchozí webovou aplikaci.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak [provést tento postup pomocí modelu Resource Manageru](application-gateway-create-probe-ps.md).

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
> Výchozí hodnota *InstanceCount* je 2, přičemž maximální hodnota je 10. Výchozí hodnota *GatewaySize* je Medium (Střední). Můžete vybrat mezi malá, střední a velké.
> 
> 

Hodnoty *VirtualIPs* a *DnsName* se zobrazují jako prázdné, protože se brána ještě nespustila. Tyto hodnoty se vytvoří, jakmile bude brána v běžícím stavu.

### <a name="configure-an-application-gateway-by-using-xml"></a>Nakonfigurujte aplikační bránu pomocí XML

V následujícím příkladu použijete soubor XML k nakonfigurování všech nastavení aplikační brány a potvrdíte je pro prostředek aplikační brány.  

Zkopírujte následující text do Poznámkového bloku.

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
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

Následující příklad ukazuje, jak použít konfigurační soubor k nastavení aplikační brány, aby vyrovnávala zatížení provozu HTTP na veřejném portu 80 a posílat síťový provoz do back-end port 80 mezi dvěma IP adresami s použitím vlastní test paměti.

> [!IMPORTANT]
> Položka protokolu Http nebo Https rozlišuje velká a malá písmena.

Nová položka konfigurace \<Probe\> se přidá ke konfiguraci vlastními testy paměti.

Konfigurační parametry jsou:

|Parametr|Popis|
|---|---|
|**Název** |Referenční název pro vlastní test paměti. |
* **Protokol** | Protokol použitý (možné hodnoty jsou HTTP nebo HTTPS).|
| **Hostitel** a **cesty** | Úplná cesta URL, která je volána službou application gateway určit stav instance. Například, pokud máte web http://contoso.com/, pak je možné nakonfigurovat vlastní test paměti pro "http://contoso.com/path/custompath.htm" pro test zkontroluje, že máte úspěšné odpovědi HTTP.|
| **Interval** | Nakonfiguruje interval kontroly testu nezdaří v řádu sekund.|
| **časový limit** | Definuje časový limit testu pro kontrolu odpovědi HTTP.|
| **UnhealthyThreshold** | Počet neúspěšných odpovědí HTTP, které jsou potřebné k nastavení příznaku instance back-end jako *není v pořádku*.|

Název sondy odkazuje \<BackendHttpSettings\> konfigurace přiřadit kterému fondu back-end využívá nastavení vlastní test paměti.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Přidat vlastní test paměti do existující aplikační bráně

Změna aktuální konfigurace služby application gateway vyžaduje tři kroky: získání aktuálního konfiguračního souboru XML, upravte mít vlastní test paměti a nakonfigurujte aplikační bránu s novým nastavením XML.

1. Získejte soubor XML s použitím `Get-AzureApplicationGatewayConfig`. Tato rutina exportuje konfiguraci XML upravena k přidání nastavení testu.

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

  V části backendHttpSettings XML přidáte název testu, jak je znázorněno v následujícím příkladu:

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

  Soubor XML uložte.

1. Aktualizace konfigurace aplikační brány pomocí nového souboru XML s použitím `Set-AzureApplicationGatewayConfig`. Tato rutina aktualizuje vaše brána application gateway s novou konfigurací.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Další postup

Pokud chcete konfigurovat přesměrování zpracování Secure Sockets Layer (SSL), přečtěte si téma [konfigurace aplikační brány pro přesměrování zpracování SSL](application-gateway-ssl.md).

Pokud chcete provést konfiguraci aplikační brány pro použití s interním nástrojem pro vyrovnávání zatížení, přečtěte si část [Vytvoření aplikační brány s interním nástrojem pro vyrovnávání zatížení (ILB)](application-gateway-ilb.md).

