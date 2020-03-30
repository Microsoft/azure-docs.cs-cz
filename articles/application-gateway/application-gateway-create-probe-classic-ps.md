---
title: Vytvoření vlastní sondy pomocí PowerShellu – Aplikační brána Azure
description: Zjistěte, jak vytvořit vlastní sondu pro aplikační bránu pomocí PowerShellu v klasickém modelu nasazení
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: e01a1cad98ded9d7ce8683b6adf38b5d53959774
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966800"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Vytvoření vlastní sondy pro Aplikační bránu Azure (klasická) pomocí PowerShellu

> [!div class="op_single_selector"]
> * [Portál Azure](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

V tomto článku přidáte vlastní sondu do existující aplikační brány s Prostředím PowerShell. Vlastní sondy jsou užitečné pro aplikace, které mají určitou stránku kontroly stavu nebo pro aplikace, které neposkytují úspěšnou odpověď na výchozí webové aplikace.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití modelu klasické nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak [provést tento postup pomocí modelu Resource Manageru](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Pro vytvoření nové aplikační brány:

1. Vytvořte prostředek aplikační brány.
2. Vytvořte konfigurační soubor XML nebo objekt konfigurace.
3. Potvrďte konfiguraci nově vytvořeného prostředku aplikační brány.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Vytvoření prostředku aplikační brány pomocí vlastní sondy

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
> Výchozí hodnota pro *InstanceCount* je 2 s maximální hodnotou 10. Výchozí hodnota *GatewaySize* je Medium (Střední). Můžete si vybrat mezi malými, středními a velkými.
> 
> 

Hodnoty *VirtualIPs* a *DnsName* se zobrazují jako prázdné, protože se brána ještě nespustila. Tyto hodnoty jsou vytvořeny, jakmile je brána ve spuštěném stavu.

### <a name="configure-an-application-gateway-by-using-xml"></a>Konfigurace aplikační brány pomocí xml

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

Následující příklad ukazuje, jak pomocí konfiguračního souboru nastavit aplikační bránu pro vyrovnávání zatížení http provoz na veřejném portu 80 a odeslat síťový provoz na back-end port 80 mezi dvěma adresami IP pomocí vlastní sondy.

> [!IMPORTANT]
> Položka protokolu Http nebo Https rozlišuje velká a malá písmena.

Nová konfigurační položka \<Sonda je přidána\> ke konfiguraci vlastních sond.

Konfigurační parametry jsou:

|Parametr|Popis|
|---|---|
|**Název** |Název odkazu pro vlastní sondu. |
| **Protokol** | (možné hodnoty jsou HTTP nebo HTTPS).|
| **Hostitel** a **cesta** | Dokončit cestu URL, která je vyvolána aplikační bránou k určení stavu instance. Například pokud máte web http:\//contoso.com/, pak vlastní sonda může být\/nakonfigurován pro "http: /contoso.com/path/custompath.htm" pro kontroly sondy mít úspěšnou odpověď HTTP.|
| **Interval** | Konfiguruje kontroly intervalu sondy v sekundách.|
| **Časový limit** | Definuje časový rozsah sondy pro kontrolu odpovědi HTTP.|
| **Prahová hodnota není v pořádku** | Počet neúspěšných odpovědí HTTP potřebných k označení instance back-end jako *není v pořádku*.|

Název sondy je \<odkazován\> v konfiguraci BackendHttpSettings pro přiřazení fondu back-end, který používá vlastní nastavení sondy.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Přidání vlastní sondy do existující aplikační brány

Změna aktuální konfigurace aplikační brány vyžaduje tři kroky: Získat aktuální konfigurační soubor XML, upravit tak, aby měla vlastní sondu, a nakonfigurovat aplikační bránu s novým nastavením XML.

1. Získejte soubor XML `Get-AzureApplicationGatewayConfig`pomocí aplikace . Tato rutina exportuje konfigurační XML, který má být upraven pro přidání nastavení sondy.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Otevřete soubor XML v textovém editoru. Za `<probe>` soubor `<frontendport>`přidejte oddíl .

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

   V části back-endHttpSettings xml přidejte název sondy, jak je znázorněno v následujícím příkladu:

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

1. Aktualizujte konfiguraci aplikační brány pomocí `Set-AzureApplicationGatewayConfig`nového souboru XML pomocí aplikace . Tato rutina aktualizuje bránu aplikace s novou konfigurací.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Další kroky

Pokud chcete nakonfigurovat redukce ssl (Secure Sockets Layer), přečtěte si část [Konfigurace aplikační brány pro přepětí ssl](application-gateway-ssl.md).

Pokud chcete provést konfiguraci aplikační brány pro použití s interním nástrojem pro vyrovnávání zatížení, přečtěte si část [Vytvoření aplikační brány s interním nástrojem pro vyrovnávání zatížení (ILB)](application-gateway-ilb.md).

