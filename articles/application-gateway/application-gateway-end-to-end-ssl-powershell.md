---
title: Konfigurace-kompletního protokolu SSL pomocí Azure Application Gateway
description: Tento článek popisuje, jak nakonfigurovat-kompletního protokolu SSL pomocí Azure Application Gateway pomocí Powershellu
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2018
ms.author: victorh
ms.openlocfilehash: f5b63c7448a502d681bbafb0ac80237a8e2c63c6
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650090"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Konfigurace-kompletního protokolu SSL pomocí Application Gateway pomocí Powershellu

## <a name="overview"></a>Přehled

Azure Application Gateway podporuje – koncové šifrování provozu. Služba Application Gateway ukončuje připojení protokolem SSL ve službě application gateway. Brána pak použije na provoz pravidla směrování, znovu zašifruje paket a předá tento paket k příslušnému back-end serveru, na základě pravidel směrování definované. Každá odpověď webového serveru prochází ke koncovému uživateli stejným procesem.

Služba Application Gateway podporuje, definování vlastních možností protokolu SSL. Také podporuje následující verze protokolu zakázání: **TLSv1.0**, **TLSv1.1**, a **TLSv1.2**a definování, které šifrovací sady používat a pořadí podle priority . Další informace o konfigurovatelných možností protokolu SSL, najdete v článku [přehled zásad protokolu SSL](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> Protokol SSL 2.0 a protokol SSL 3.0 jsou ve výchozím nastavení zakázané a nejde povolit. Tyto považovány za nezabezpečené a nelze použít s Application Gateway.

![scénář image][scenario]

## <a name="scenario"></a>Scénář

V tomto scénáři se dozvíte, jak vytvořit službu application gateway pomocí prostředí PowerShell-kompletního protokolu SSL.

Tento scénář bude:

* Vytvořte skupinu prostředků s názvem **appgw-rg**.
* Vytvoření virtuální sítě s názvem **appgwvnet** s adresním prostorem **10.0.0.0/16**.
* Vytvořit dvě podsítě, které volá **appgwsubnet** a **appsubnet**.
* Vytvoření malou aplikaci brány podpůrné začátku do konce šifrování SSL tohoto omezení verze protokolu SSL a šifrovacích sad.

## <a name="before-you-begin"></a>Než začnete

Ke konfiguraci-kompletního protokolu SSL s aplikační bránou, certifikát je vyžadován pro bránu a certifikáty jsou nutné k back-end serverů. Certifikát brány se používá k šifrování a dešifrování přenosů do něj odesílají pomocí protokolu SSL. Certifikát brány musí být ve formátu Personal Information Exchange (PFX). Tento formát souboru umožňuje exportovat soukromý klíč, který vyžaduje službu application gateway šifrování a dešifrování přenosů.

Pro šifrování SSL začátku do konce back-end musí být povolené ve službě application gateway. Budete muset nahrát veřejný certifikát back-end serverů ke službě application gateway. Přidání certifikátu se zajistí, že službu application gateway komunikuje pouze se známými back endových instancí. To dále zabezpečuje komunikaci začátku do konce.

Proces konfigurace je popsána v následujících částech.

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Tato část vás provede vytvořením skupinu prostředků, která obsahuje bránu application gateway.


   1. Přihlaste se ke svému účtu Azure.

   ```powershell
   Connect-AzureRmAccount
   ```


   2. Vyberte předplatné, které chcete použít pro tento scénář.

   ```powershell
   Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
   ```


   3. Vytvořte skupinu prostředků. (Tento krok přeskočte, pokud používáte některou ze stávajících skupin prostředků.)

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Vytvořte virtuální síť a podsíť pro aplikační bránu

Následující příklad vytvoří virtuální síť a dvě podsítě. Jednu podsíť se používá k uložení application gateway. Další podsítě se používá pro back-endů, které jsou hostiteli webové aplikace.


   1. Přiřaďte rozsah adres podsítě se použije pro službu application gateway.

   ```powershell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Podsítě konfigurované pro službu application gateway by měl být správnou velikost. Službu application gateway lze nakonfigurovat pro až 10 instancí. Každá instance vyžaduje jednu IP adresu z podsítě. Příliš malé podsítě, může nepříznivě ovlivnit horizontální navýšení kapacity pro službu application gateway.
   > 
   > 

   2. Přiřaďte rozsah adres pro fond back endových adres.

   ```powershell
   $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

   3. Vytvořte virtuální síť s podsítí definovaných v předchozích krocích.

   ```powershell
   $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

   4. Načíst prostředek virtuální sítě a podsítě prostředky, který se má použít v následujících kroků.

   ```powershell
   $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Vytvoření veřejné IP adresy pro front-end konfiguraci

Vytvořte prostředek veřejné IP se použije pro službu application gateway. Tato veřejná IP adresa se používá v jednom z následujících kroků.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Služba Application Gateway nepodporuje použijte veřejnou IP adresu vytvořenou s popiskem definované domény. Je podporován pouze veřejné IP adresy s popiskem dynamicky vytvořené domény. Pokud budete potřebovat popisný název DNS pro službu application gateway, doporučujeme že použít záznam CNAME jako alias.

## <a name="create-an-application-gateway-configuration-object"></a>Vytvoření objektu konfigurace služby Application Gateway

Všechny položky nastavené před vytvořením služby application gateway. Následující kroky slouží k vytvoření položek konfigurace potřebné pro prostředek služby Application Gateway.

1. Vytvoření konfigurace IP aplikační brány. Toto nastavení lze konfigurovat využívající podsítě application gateway. Při spuštění služby application gateway ji předá IP adresa z nakonfigurované podsítě a směrování síťového provozu na IP adresy v back endového fondu IP adres. Uvědomte si, že každá instance vyžaduje jednu IP adresu.

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```


2. Vytvořte konfiguraci front-end IP adresy. Namapuje se toto nastavení privátní nebo veřejnou IP adresu front-endu služby application gateway. Následující krok přiřadí veřejnou IP adresu v předchozím kroku s konfigurací front-end IP adresy.

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Nakonfigurujte fond back-end IP adres s IP adresami serverů back endové webové. Tyto IP adresy jsou IP adresy, které přijímají síťový provoz, který přichází z koncového bodu front-end IP adresy. Nahraďte IP adresy ve vzorku s vlastními koncovými body IP adresy typu aplikace.

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Použitím plně kvalifikovaného názvu domény (FQDN) je také platnou hodnotu oznamujícím IP adresu pro back-end serverů. Povolit pomocí **- BackendFqdns** přepnout. 


4. Nakonfigurujte port front-end IP pro koncový bod veřejné IP adresy. Tento port je port, který koncoví uživatelé připojit.

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Konfigurace certifikátu pro službu application gateway. Tento certifikát slouží k dešifrování a šifrovaly provozu ve službě application gateway.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzureRmApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Tato vzorovým kódem se nakonfiguruje certifikát používaný pro připojení SSL. Certifikát se musí být ve formátu .pfx a heslo musí obsahovat 4 až 12 znaků.

6. Vytvořte naslouchací proces protokolu HTTP služby application gateway. Přiřadíte konfiguraci front-end IP adresy, portu a certifikát protokolu SSL se má použít.

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Nahrajte certifikát, který se použije na prostředky s podporou protokolu SSL back endového fondu.

   > [!NOTE]
   > Získá výchozí kontroly veřejný klíč z *výchozí* vazby SSL back endové IP adresy a porovná hodnotu veřejného klíče přijímá hodnotě veřejného klíče je tady zadáte. 
   
   > Pokud používáte hlavičky hostitele a indikace názvu serveru (SNI) na back-endu, nemusí být načtený veřejný klíč zamýšlená lokalita, na které přenosové toky. Pokud už máte pochybnosti, navštivte https://127.0.0.1/ na back-end servery, které chcete potvrdit, který certifikát se používá pro *výchozí* vazby SSL. V této části použijte veřejný klíč z tohoto požadavku. Pokud používáte hlavičky hostitele a SNI na vazby HTTPS a neobdržíte odpověď a certifikátu ze žádosti o ruční prohlížeče k https://127.0.0.1/ na back-end serverech, musíte nastavit výchozí vazbu SSL na na ně. Pokud to neprovedete, selhání sondy a back-end není na seznamu povolených.

   ```powershell
   $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer
   ```

   > [!NOTE]
   > Certifikát zadaný v tomto kroku by měl být veřejný klíč certifikátu .pfx, který je k dispozici na back-endu. Exportujte certifikát nainstalovaný na back endového serveru ve formátu deklarace identity, legitimaci a odůvodnění (CER) (nikoliv kořenový certifikát) a použít ho v tomto kroku. Tento krok seznamů povolených back-end pomocí služby application gateway.

   Pokud používáte SKU v2 Application Gateway, vytvořte důvěryhodný kořenový certifikát namísto ověřovací certifikát. Další informace najdete v tématu [přehled koncového šifrování protokolu SSL pomocí Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku):

   ```powershell
   $trustedRootCert01 = New-AzureRmApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Konfigurace nastavení protokolu HTTP pro application gateway back-endu. Přiřadíte certifikát nahraný v předchozím kroku, a nastavení HTTP.

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Pro SKU v2 Application Gateway použijte následující příkaz:

   ```powershell
   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Vytvořte pravidlo směrování pro vyrovnávání zatížení, které konfiguruje chování nástroje pro vyrovnávání zatížení. V tomto příkladu se vytvoří základní pravidlo kruhové dotazování.

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Nakonfigurujte velikost instance služby Application Gateway. Dostupné velikosti jsou **standardní\_malé**, **standardní\_střední**, a **standardní\_velké**.  Pro kapacitu, jsou k dispozici hodnoty **1** prostřednictvím **10**.

    ```powershell
    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Pro účely testování můžete zvolit počet instancí 1. Je důležité vědět, že libovolný počet instancí v rámci dvou instancí nevztahuje Smlouva SLA a proto nedoporučujeme. Malé brány se mají použít pro vývoj testování a nikoli pro produkční účely.

11. Konfigurace zásad protokolu SSL, který se má použít ve službě application gateway. Služba Application Gateway podporuje možnost nastavit minimální verze pro verze protokolu SSL.

   Seznam verzí protokolu, které lze definovat jsou následující hodnoty:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
   Následující příklad nastaví minimální protocol verze **TLSv1_2** a umožňuje **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, a **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** pouze.

   ```powershell
   $SSLPolicy = New-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"
   ```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Pomocí všech předchozích kroků vytvořte službu application gateway. Vytvoření brány je proces, který trvá dlouhou dobu pro spuštění.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Omezení verze protokolu SSL v existující aplikační bráně

V předchozích krocích trvalo vás provedou vytvořením aplikace s protokolem SSL začátku do konce a zakázání určitých verzí protokolu SSL. Následující příklad zakazuje určitých zásad protokolu SSL v existující aplikační bráně.

   1. Získat application gateway se aktualizovat.

   ```powershell
   $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

   2. Definujte zásady protokolu SSL. V následujícím příkladu **TLSv1.0** a **TLSv1.1** jsou zakázána a šifrovacích sad **TLS\_ECDHE\_ECDSA\_WITH\_ AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, a **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** jsou pouze těch, které jsou povoleny.

   ```powershell
   Set-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

   3. Nakonec aktualizujte brány. Všimněte si, že tento poslední krok je dlouho běžící úlohy. Po dokončení, je nakonfigurované – kompletního protokolu SSL ve službě application gateway.

   ```powershell
   $gw | Set-AzureRmApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Získejte název DNS aplikace brány

Po vytvoření brány je dalším krokem konfigurace front-endu pro komunikaci. Application Gateway vyžaduje dynamicky přidělený název DNS, při použití veřejné IP adresy, které ale není popisný. K zajištění, že koncoví uživatelé mohli dostat application gateway, můžou použít záznam CNAME tak, aby odkazoval na veřejný koncový bod služby application gateway. Další informace najdete v tématu [konfigurace názvu vlastní domény v Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Konfigurace aliasu, načtěte podrobnosti o služby application gateway a název její přidružené IP adresy nebo DNS s využitím **PublicIPAddress** element připojený ke službě application gateway. Použijte službu application gateway název DNS vytvořit záznam CNAME, který ukazuje dvě webové aplikace na tento název DNS. Jsme není doporučujeme používat záznamy typu, protože virtuální IP adresu můžete změnit na restartování služby application gateway.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Další postup

Další informace o posílení zabezpečení webových aplikací pomocí firewallu webových aplikací pomocí Application Gateway najdete v tématu [přehled firewallu webových aplikací](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
