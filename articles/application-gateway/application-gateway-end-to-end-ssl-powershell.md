---
title: Konfigurace komplexního sssl s aplikační bránou Azure
description: Tento článek popisuje, jak nakonfigurovat komplexní ssl s Azure Application Gateway pomocí PowerShellu
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/8/2019
ms.author: victorh
ms.openlocfilehash: 7ba273cddb6cf41872c4db1c34560c104b992787
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72286468"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Konfigurace kompletního SSL ve službě Application Gateway pomocí PowerShellu

## <a name="overview"></a>Přehled

Azure Application Gateway podporuje end-to-end šifrování provozu. Aplikační brána ukončí připojení SSL na aplikační bráně. Brána pak použije pravidla směrování na přenos, znovu zašifruje paket a předá paket příslušnému serveru back-end na základě definovaných pravidel směrování. Každá odpověď webového serveru prochází ke koncovému uživateli stejným procesem.

Aplikační brána podporuje definování vlastních možností SSL. Podporuje také zakázání následujících verzí protokolu: **TLSv1.0**, **TLSv1.1**a **TLSv1.2**, a také definování, které šifrovací sady použít a pořadí preferencí. Další informace o konfigurovatelných možnostech ssl naleznete v [přehledu zásad SSL](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> SSL 2.0 a SSL 3.0 jsou ve výchozím nastavení zakázány a nelze je povolit. Jsou považovány za nezabezpečené a nelze je použít s aplikační bránou.

![obrázek scénáře][scenario]

## <a name="scenario"></a>Scénář

V tomto scénáři se dozvíte, jak vytvořit aplikační bránu pomocí ssl od konce s prostředím PowerShell.

Tento scénář bude:

* Vytvořte skupinu prostředků s názvem **appgw-rg**.
* Vytvořte virtuální síť s názvem **appgwvnet** s adresním prostorem **10.0.0.0/16**.
* Vytvořte dvě podsítě s názvem **appgwsubnet** a **appsubnet**.
* Vytvořte malou aplikační bránu podporující komplexní šifrování SSL, která omezuje verze protokolu SSL a šifrovací sady.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li nakonfigurovat protokol SSL od konce s aplikační bránou, je pro bránu vyžadován certifikát a pro servery back-end jsou vyžadovány certifikáty. Certifikát brány se používá k odvození symetrického klíče podle specifikace protokolu SSL. Symetrický klíč se pak používá šifrovat a dešifrovat provoz odeslaný do brány. Certifikát brány musí být ve formátu PFX (Personal Information Exchange). Tento formát souboru umožňuje exportovat soukromý klíč, který je vyžadován aplikační bránou k provedení šifrování a dešifrování provozu.

Pro šifrování ssl od konce musí být back-end explicitně povolen aplikační bránou. Nahrajte veřejný certifikát serverů back-end do aplikační brány. Přidání certifikátu zajistí, že aplikační brána komunikuje pouze se známými back-endovými instancemi. To dále zabezpečuje komunikaci mezi koncovými soubory.

Proces konfigurace je popsán v následujících částech.

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Tato část vás provede vytvořením skupiny prostředků, která obsahuje aplikační bránu.

1. Přihlaste se ke svému účtu Azure.

   ```powershell
   Connect-AzAccount
   ```

2. Vyberte předplatné, které chcete použít pro tento scénář.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. Vytvořte skupinu prostředků. (Tento krok přeskočte, pokud používáte některou ze stávajících skupin prostředků.)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Vytvoření virtuální sítě a podsítě pro službu Application Gateway

Následující příklad vytvoří virtuální síť a dvě podsítě. Jedna podsíť se používá k uložení aplikační brány. Druhá podsíť se používá pro back-endy, které jsou hostitelem webové aplikace.

1. Přiřaďte rozsah adres pro podsíť, která má být použita pro aplikační bránu.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Podsítě nakonfigurované pro aplikační bránu by měly mít správnou velikost. Aplikační bránu lze nakonfigurovat až pro 10 instancí. Každá instance přebírá jednu ADRESU IP z podsítě. Příliš malý podsítě může nepříznivě ovlivnit horizontální navýšení kapacity mimo aplikační bránu.
   >

2. Přiřaďte rozsah adres, který se má použít pro fond adres back-endu.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Vytvořte virtuální síť s podsítěmi definovanými v předchozích krocích.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Načtěte prostředky virtuální sítě a prostředky podsítě, které se použijí v následujících krocích.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Vytvoření veřejné IP adresy pro front-end konfiguraci

Vytvořte veřejný prostředek IP, který se bude používat pro aplikační bránu. Tato veřejná IP adresa se používá v jednom z následujících kroků.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Aplikační brána nepodporuje použití veřejné IP adresy vytvořené s definovaným popiskem domény. Je podporována pouze veřejná IP adresa s dynamicky vytvořeným popiskem domény. Pokud pro bránu aplikace požadujete popisný název DNS, doporučujeme použít jako alias záznam CNAME.

## <a name="create-an-application-gateway-configuration-object"></a>Vytvořte objekt konfigurace aplikační brány 

Všechny položky konfigurace jsou nastaveny před vytvořením aplikační brány. Následující kroky slouží k vytvoření položek konfigurace potřebné pro prostředek služby Application Gateway.

1. Vytvořte konfiguraci IP adresy aplikační brány. Toto nastavení konfiguruje, které z podsítí používá aplikační brána. Při spuštění brány aplikace vyzvedne IP adresu z nakonfigurované podsítě a směruje síťový provoz na adresy IP ve fondu ip adres back-endu. Uvědomte si, že každá instance vyžaduje jednu IP adresu.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Vytvořte konfiguraci IP adres front-endu. Toto nastavení mapuje privátní nebo veřejnou IP adresu na front-end aplikační brány. Následující krok přidruží veřejnou IP adresu v předchozím kroku k konfiguraci front-endIP.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Nakonfigurujte fond adres IP back-end s ADRESAMI IP webových serverů back-end. Tyto IP adresy jsou IP adresy, které přijímají síťový provoz, který přichází z koncového bodu front-end IP adresy. Nahraďte ip adresy v ukázce vlastními koncovými body ip adresy aplikace.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Plně kvalifikovaný název domény (FQDN) je také platnou hodnotou, která se používá místo adresy IP pro servery back-end. Povolíte jej pomocí přepínače **-BackendFqdns.** 

4. Nakonfigurujte port IP front-end pro veřejný koncový bod IP. Tento port je port, ke kterému se koncoví uživatelé připojují.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Nakonfigurujte certifikát pro aplikační bránu. Tento certifikát se používá k dešifrování a opětovnému šifrování provozu na aplikační bráně.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Tato ukázka konfiguruje certifikát použitý pro připojení SSL. Certifikát musí být ve formátu .pfx a heslo musí mít 4 až 12 znaků.

6. Vytvořte naslouchací proces HTTP pro aplikační bránu. Přiřaďte konfiguraci ip adres front-endu, port a certifikát SSL, který chcete použít.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Nahrajte certifikát, který se má použít na prostředky back-endového fondu s podporou SSL.

   > [!NOTE]
   > Výchozí sonda získá veřejný klíč z *výchozí* ssl vazby na adresu IP back-endu a porovná hodnotu veřejného klíče, kterou obdrží, s hodnotou veřejného klíče, kterou zde zadáte. 
   > 
   > Pokud používáte záhlaví hostitele a označení názvu serveru (SNI) na back-endu, načtený veřejný klíč nemusí být zamýšleným webem, do kterého toky provozu. Pokud máte pochybnosti, https://127.0.0.1/ navštivte servery back-end a ověřte, který certifikát se používá pro *výchozí* vazbu SSL. Použijte veřejný klíč z této žádosti v této části. Pokud používáte hlavičky hostitele a SNI na vazby HTTPS a neobdržíte odpověď https://127.0.0.1/ a certifikát z ručního požadavku prohlížeče na serverech back-end, je nutné nastavit výchozí vazbu SSL na nich. Pokud tak neučiníte, sondy se nezdaří a back-end není na seznamu povolených.

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > Certifikát uvedený v předchozím kroku by měl být veřejným klíčem certifikátu .pfx, který je k dispozici na back-endu. Exportujte certifikát (nikoli kořenový certifikát) nainstalovaný na serveru back-end ve formátu Deklarace, Evidence a Uvažování (CER) a použijte jej v tomto kroku. Tento krok zapisuje back-end s aplikační bránou.

   Pokud používáte sku sku aplikace v2, vytvořte důvěryhodný kořenový certifikát namísto ověřovacího certifikátu. Další informace naleznete v [tématu Přehled ssl od konce do konce s aplikační bránou](ssl-overview.md#end-to-end-ssl-with-the-v2-sku):

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Nakonfigurujte nastavení protokolu HTTP pro back-end aplikační brány. Přiřaďte certifikát nahraný v předchozím kroku nastavení protokolu HTTP.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Pro aplikační bránu v2 skladovou položku použijte následující příkaz:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Vytvořte pravidlo směrování vyrovnávání zatížení, které konfiguruje chování vykladače zatížení. V tomto příkladu je vytvořeno základní pravidlo kruhového dotazování.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Nakonfigurujte velikost instance služby Application Gateway. Dostupné velikosti jsou **\_Standardní malé**, Standardní **\_střední**a Standardní **\_velké**.  Pro kapacitu jsou dostupné hodnoty **1** až **10**.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Počet instancí 1 lze zvolit pro účely testování. Je důležité vědět, že počet instancí v rámci dvou instancí se nevztahuje na sla, a proto se nedoporučuje. Malé brány se mají používat pro dev test a ne pro výrobní účely.

11. Nakonfigurujte zásady SSL, které mají být použity v bráně aplikace. Aplikační brána podporuje možnost nastavit minimální verzi pro verze protokolu SSL.

    Následující hodnoty jsou seznam verzí protokolu, které lze definovat:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    Následující příklad nastaví minimální verzi protokolu na **TLSv1_2** a povolí **TLS\_\_ECDHE\_\_\_ECDSA\_s AES 128 GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_s\_AES\_256\_GCM\_SHA384**a **TLS\_RSA\_pouze s\_AES\_128\_GCM\_SHA256.**

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Pomocí všech předchozích kroků vytvořte aplikační bránu. Vytvoření brány je proces, který trvá dlouhou dobu ke spuštění.

Pro skladovou položku V1 použijte níže uvedený příkaz
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

Pro skladovou položku V2 použijte níže uvedený příkaz
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Použití nového certifikátu v případě vypršení platnosti certifikátu back-end

Tento postup použijte k použití nového certifikátu, pokud vypršela platnost certifikátu back-end.

1. Načíst bránu aplikace aktualizovat.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Přidejte nový prostředek certifikátu ze souboru CER, který obsahuje veřejný klíč certifikátu a může být také stejný certifikát přidaný do naslouchací proces u končit ssl v bráně aplikace.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Získejte nový objekt ověřovacího certifikátu do proměnné (TypeName: Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Přiřaďte nový certifikát do nastavení **BackendHttp** a odkažte jej s proměnnou $AuthCert. (Zadejte název nastavení HTTP, který chcete změnit.)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Potvrdí změnu do aplikační brány a předají novou konfiguraci obsaženou do proměnné $out.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Odebrání nepoužívaného certifikátu, jehož platnost vypršela, z nastavení protokolu HTTP

Tento postup slouží k odebrání nepoužívaného certifikátu, jehož platnost vypršela, z nastavení protokolu HTTP.

1. Načíst bránu aplikace aktualizovat.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Uveďte název ověřovacího certifikátu, který chcete odebrat.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Odeberte ověřovací certifikát z aplikační brány.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Potvrdí změnu.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Omezení verzí protokolu SSL na existující aplikační bráně

Předchozí kroky vás provedly vytvořením aplikace s protokolem SSL od konce do konce a zakázáním určitých verzí protokolu SSL. Následující příklad zakáže určité zásady SSL na existující aplikační bráně.

1. Načíst bránu aplikace aktualizovat.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Definujte zásadu SSL. V následujícím příkladu jsou **zakázány TLSv1.0** a **TLSv1.1** a šifrovací sady **\_TLS\_ECDHE ECDSA\_s\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_s\_AES\_256\_GCM\_SHA384**a **TLS\_RSA\_s\_AES\_128\_GCM\_SHA256** jsou jediné povolené.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Nakonec aktualizujte bránu. Tento poslední krok je dlouhotrvající úloha. Po dokončení je na aplikační bráně nakonfigurován osazený ssl od konce.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Získání názvu DNS pro bránu aplikace

Po vytvoření brány je dalším krokem konfigurace front-endu pro komunikaci. Aplikační brána vyžaduje dynamicky přiřazený název DNS při použití veřejné IP adresy, která není přívětivá. Chcete-li zajistit, aby koncoví uživatelé mohli zasáhnout aplikační bránu, můžete použít záznam CNAME k přejděte na veřejný koncový bod aplikační brány. Další informace najdete [v tématu Konfigurace vlastního názvu domény v Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Chcete-li nakonfigurovat alias, načtěte podrobnosti o aplikační bráně a jejím přidruženém názvu IP/DNS pomocí elementu **PublicIPAddress** připojeného k bráně aplikace. Pomocí názvu DNS brány aplikace vytvořte záznam CNAME, který na tento název DNS odkazuje na dvě webové aplikace. Nedoporučujeme používat A-záznamy, protože VIP může změnit při restartování brány aplikace.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
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

## <a name="next-steps"></a>Další kroky

Další informace o posílení zabezpečení webových aplikací pomocí brány firewall webových aplikací prostřednictvím brány aplikace naleznete v [přehledu brány brány firewall webové aplikace](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
