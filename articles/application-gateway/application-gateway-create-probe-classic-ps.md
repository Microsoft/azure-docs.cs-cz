---
title: Vytvoření vlastní sondy pomocí modelu nasazení Classic – Azure Application Gateway
description: Zjistěte, jak vytvořit vlastní test pro Application Gateway pomocí prostředí PowerShell v modelu nasazení Classic.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 13441899eeb5ca2b7c60977ab2858fe40a398d1a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397854"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Vytvoření vlastní sondy pro Azure Application Gateway (Classic) pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

V tomto článku přidáte vlastní test paměti do existující aplikační brány pomocí PowerShellu. Vlastní sondy jsou užitečné pro aplikace, které mají konkrétní stránku kontroly stavu nebo pro aplikace, které neposkytují úspěšnou odpověď na výchozí webovou aplikaci.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a Classic](../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak [provést tento postup pomocí modelu Resource Manageru](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Vytvoření brány Application Gateway

Pro vytvoření nové aplikační brány:

1. Vytvořte prostředek aplikační brány.
2. Vytvořte konfigurační soubor XML nebo objekt konfigurace.
3. Potvrďte konfiguraci nově vytvořeného prostředku aplikační brány.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Vytvoření prostředku aplikační brány s vlastní sondou

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
> Výchozí hodnota pro *InstanceCount* je 2 s maximální hodnotou 10. Výchozí hodnota *GatewaySize* je Medium (Střední). Můžete si vybrat mezi malým, středním a velkým.
> 
> 

Hodnoty *VirtualIPs* a *DnsName* se zobrazují jako prázdné, protože se brána ještě nespustila. Tyto hodnoty se vytvoří, jakmile je brána ve stavu spuštěno.

### <a name="configure-an-application-gateway-by-using-xml"></a>Konfigurace aplikační brány pomocí XML

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

Následující příklad ukazuje, jak použít konfigurační soubor k nastavení aplikační brány pro vyrovnávání zatížení provozu HTTP na veřejném portu 80 a odesílání síťového provozu do back-endového portu 80 mezi dvěma IP adresami pomocí vlastního testu.

> [!IMPORTANT]
> Položka protokolu Http nebo Https rozlišuje velká a malá písmena.

Přidala se nová položka konfigurace \<Probe\> , která konfiguruje vlastní sondy.

Konfigurační parametry jsou:

|Parametr|Popis|
|---|---|
|**Název** |Referenční název pro vlastní test paměti |
| **Protokol** | Použitý protokol (možné hodnoty jsou HTTP nebo HTTPS).|
| **Hostitel** a **cesta** | Dokončete cestu adresy URL vyvolanou aplikační bránou a určete stav instance. Například pokud máte webovou stránku http: \/ /contoso.com/, pak se vlastní test paměti dá nakonfigurovat na http: \/ /contoso.com/Path/custompath.htm, aby testy testů měly ÚSPĚŠNOU odpověď HTTP.|
| **Interval** | Nakonfiguruje kontrolu intervalu sondy v sekundách.|
| **Prodlev** | Definuje časový limit testu pro kontrolu odezvy protokolu HTTP.|
| **UnhealthyThreshold** | Počet neúspěšných odpovědí HTTP nutných k označení back-endové instance jako *chybného*.|

V konfiguraci je odkazováno na název sondy, \<BackendHttpSettings\> které přiřadí, který fond back-end používá vlastní nastavení sondy.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Přidání vlastního testu do existující služby Application Gateway

Změna aktuální konfigurace služby Application Gateway vyžaduje tři kroky: Získejte aktuální konfigurační soubor XML, upravte ho tak, aby měl vlastní test paměti, a nakonfigurujte Aplikační bránu pomocí nového nastavení XML.

1. Získejte soubor XML pomocí `Get-AzureApplicationGatewayConfig` . Tato rutina exportuje konfigurační soubor XML, který se má upravit, aby se přidalo nastavení sondy.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Otevřete soubor XML v textovém editoru. Přidejte `<probe>` oddíl za `<frontendport>` .

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

   V části backendHttpSettings XML přidejte název sondy, jak je znázorněno v následujícím příkladu:

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

1. Aktualizujte konfiguraci aplikační brány novým souborem XML pomocí `Set-AzureApplicationGatewayConfig` . Tato rutina aktualizuje Aplikační bránu s novou konfigurací.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Další kroky

Pokud chcete nakonfigurovat protokol TLS (Transport Layer Security), dříve označovaný SSL (Secure Sockets Layer) jako přesměrování zpracování SSL (Transport Layer), přečtěte si téma [Konfigurace aplikační brány pro přesměrování zpracování TLS](./tutorial-ssl-powershell.md).

Pokud chcete provést konfiguraci aplikační brány pro použití s interním nástrojem pro vyrovnávání zatížení, přečtěte si část [Vytvoření aplikační brány s interním nástrojem pro vyrovnávání zatížení (ILB)](./application-gateway-ilb-arm.md).