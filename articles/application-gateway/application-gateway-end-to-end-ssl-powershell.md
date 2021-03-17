---
title: Konfigurace kompletního TLS pomocí Azure Application Gateway
description: Tento článek popisuje, jak nakonfigurovat kompletní protokol TLS s Azure Application Gateway pomocí prostředí PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 47891dfa7fc0c9b30ccdbf2ed7710125eb36e4a3
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397803"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-powershell"></a>Konfigurace koncového protokolu TLS pomocí Application Gateway s využitím PowerShellu

## <a name="overview"></a>Přehled

Azure Application Gateway podporuje komplexní šifrování provozu. Application Gateway ukončí připojení TLS/SSL ve službě Application Gateway. Brána pak použije pravidla směrování na provoz, znovu zašifruje paket a přepošle paket na příslušný back-end Server na základě definovaných pravidel směrování. Každá odpověď webového serveru prochází ke koncovému uživateli stejným procesem.

Application Gateway podporuje definování vlastních možností TLS. Také podporuje zakázání následujících verzí protokolů: **tlsv 1.0** , **Tlsv 1.1** a **tlsv 1.2** , a také definování, které šifrovací sady použít a pořadí priorit. Další informace o konfigurovatelných možnostech TLS najdete v tématu [Přehled zásad protokolu TLS](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> SSL 2,0 a SSL 3,0 jsou ve výchozím nastavení zakázány a nelze je povolit. Jsou považovány za nezabezpečené a nelze je použít s Application Gateway.

![Obrázek scénáře][scenario]

## <a name="scenario"></a>Scénář

V tomto scénáři se dozvíte, jak vytvořit Aplikační bránu pomocí kompletního protokolu TLS s prostředím PowerShell.

Tento scénář bude:

* Vytvořte skupinu prostředků s názvem **appgw-RG**.
* Vytvořte virtuální síť s názvem **appgwvnet** s adresním prostorem **10.0.0.0/16**.
* Vytvořte dvě podsítě s názvem **appgwsubnet** a **appsubnet**.
* Vytvořte malou Aplikační bránu, která bude podporovat komplexní šifrování TLS, které omezuje verze protokolu TLS a šifrovací sady.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud chcete nakonfigurovat kompletní protokol TLS s aplikační bránou, vyžaduje se pro tuto bránu certifikát a pro back-end servery se vyžadují certifikáty. Certifikát brány se používá k odvození symetrického klíče podle specifikace protokolu TLS. Symetrický klíč se pak použije k zašifrování a dešifrování provozu odeslaného do brány. Certifikát brány musí být ve formátu PFX (Personal Information Exchange). Tento formát souboru umožňuje exportovat privátní klíč, který služba Application Gateway vyžaduje k provádění šifrování a dešifrování provozu.

Pro komplexní šifrování TLS musí být back-end od služby Application Gateway výslovně povolen. Nahrajte veřejný certifikát back-end serverů do služby Application Gateway. Přidáním certifikátu zajistíte, aby brána Application Gateway komunikovala pouze se známými back-end instancemi. Tím se dále zabezpečuje koncová komunikace.

Proces konfigurace je popsaný v následujících částech.

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

V této části se seznámíte s vytvořením skupiny prostředků, která obsahuje aplikační bránu.

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

Následující příklad vytvoří virtuální síť a dvě podsítě. Jedna podsíť se používá k uchování aplikační brány. Druhá podsíť se používá pro back-endy, které hostují webovou aplikaci.

1. Přiřaďte rozsah adres pro podsíť, která se má použít pro aplikační bránu.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Podsítě nakonfigurované pro aplikační bránu by měly mít správnou velikost. Aplikační bránu je možné nakonfigurovat až na 10 instancí. Každá instance přebírá jednu IP adresu z podsítě. Příliš malá podsíť může negativně ovlivnit škálování aplikační brány.
   >

2. Přiřaďte rozsah adres, který se má použít pro fond back-end adres.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Vytvořte virtuální síť s podsítěmi, které jsou definovány v předchozích krocích.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Načtěte prostředky prostředku virtuální sítě a podsítě, které se mají použít v následujících krocích.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Vytvoření veřejné IP adresy pro front-end konfiguraci

Vytvořte prostředek veřejné IP adresy, který se bude používat pro aplikační bránu. Tato veřejná IP adresa se používá v jednom z následujících kroků.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Application Gateway nepodporuje použití veřejné IP adresy vytvořené s popiskem definované domény. Podporovaná je jenom veřejná IP adresa s názvem dynamicky vytvořenou doménou. Pokud pro aplikační bránu potřebujete popisný název DNS, doporučujeme použít záznam CNAME jako alias.

## <a name="create-an-application-gateway-configuration-object"></a>Vytvořte objekt konfigurace aplikační brány 

Všechny položky konfigurace jsou nastaveny před vytvořením aplikační brány. Následující kroky slouží k vytvoření položek konfigurace potřebné pro prostředek služby Application Gateway.

1. Vytvořte konfiguraci protokolu IP brány Application Gateway. Toto nastavení určuje, které z podsítí aplikační brány používá. Když se Aplikační brána spustí, vytvoří IP adresu z nakonfigurované podsítě a směruje síťový provoz na IP adresy ve fondu back-end IP adres. Uvědomte si, že každá instance vyžaduje jednu IP adresu.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Vytvořte konfiguraci front-end IP adresy. Toto nastavení mapuje soukromou nebo veřejnou IP adresu na front-end aplikační brány. Následující krok přidruží veřejnou IP adresu v předchozím kroku s konfigurací front-end IP adresy.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Nakonfigurujte fond back-end IP adres s použitím IP adres back-endové webové servery. Tyto IP adresy jsou IP adresy, které přijímají síťový provoz, který přichází z koncového bodu front-end IP adresy. Nahraďte IP adresy v ukázce vlastními koncovými body IP adres aplikace.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Plně kvalifikovaný název domény (FQDN) je také platná hodnota pro použití místo IP adresy pro back-endové servery. Povolíte ji pomocí přepínače **-BackendFqdns** . 

4. Nakonfigurujte port front-end IP adresy pro koncový bod veřejné IP adresy. Tento port je port, ke kterému se koncoví uživatelé připojují.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Nakonfigurujte certifikát pro aplikační bránu. Tento certifikát slouží k dešifrování a přešifrování provozu služby Application Gateway.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > V této ukázce se nakonfiguruje certifikát používaný pro připojení TLS. Certifikát musí být ve formátu. pfx a heslo musí mít 4 až 12 znaků.

6. Vytvořte naslouchací proces HTTP pro službu Application Gateway. Přiřaďte k použití certifikát konfigurace front-end IP adresy, portu a protokolu TLS/SSL.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Nahrajte certifikát, který se má použít u prostředků fondu back-endu s povoleným protokolem TLS.

   > [!NOTE]
   > Výchozí sonda získá veřejný klíč z *výchozí* vazby TLS na IP adrese back-endu a porovná hodnotu veřejného klíče, kterou přijme, k hodnotě veřejného klíče, kterou tady zadáte. 
   > 
   > Pokud používáte hlavičky hostitele a Indikace názvu serveru (SNI) na back-endu, načtený veřejný klíč nemusí být zamýšlenou lokalitou, na kterou přenosové toky. Pokud nejste jistí, navštivte https://127.0.0.1/ back-endové servery a ověřte, který certifikát se používá pro *výchozí* vazbu TLS. Použijte veřejný klíč z tohoto požadavku v této části. Pokud používáte pro vazby HTTPS možnost hostitel-Headers a SNI a neobdržíte odpověď a certifikát od ručního požadavku prohlížeče na https://127.0.0.1/ back-endové servery, musíte pro ně nastavit výchozí vazbu TLS. Pokud to neuděláte, sondy selžou a back-end není povolen.
   
   Další informace o SNI v Application Gateway najdete v tématu [Přehled ukončení protokolu TLS a koncového šifrování TLS s Application Gateway](ssl-overview.md).

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > Certifikát, který jste zadali v předchozím kroku, by měl být veřejným klíčem certifikátu. pfx, který je přítomný v back-endu. Exportujte certifikát (nikoli kořenový certifikát) nainstalovaný na záložním serveru ve formátu deklarace identity, legitimace a důvod (CER) a použijte ho v tomto kroku. Tento krok povolí back-end s aplikační bránou.

   Pokud používáte SKU Application Gateway v2, pak místo ověřovacího certifikátu vytvořte důvěryhodný kořenový certifikát. Další informace najdete v tématu [Přehled koncového protokolu TLS s Application Gateway](ssl-overview.md#end-to-end-tls-with-the-v2-sku):

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Nakonfigurujte nastavení HTTP pro back-end služby Application Gateway. Přiřaďte certifikát odeslaný v předchozím kroku nastavení HTTP.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   V případě SKU Application Gateway v2 použijte následující příkaz:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Vytvořte směrovací pravidlo nástroje pro vyrovnávání zatížení, které konfiguruje chování nástroje pro vyrovnávání zatížení. V tomto příkladu se vytvoří základní pravidlo kruhového dotazování.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Nakonfigurujte velikost instance služby Application Gateway. Dostupné velikosti jsou **standardní \_ malé** , **standardní \_ střední** a **standardní \_**.  V případě kapacity mají dostupné hodnoty **1** až **10**.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Pro účely testování lze zvolit počet instancí 1. Je důležité znát, že se smlouvou SLA nepokrývá žádný počet instancí pod dvěma instancemi a proto se nedoporučuje. Malé brány se použijí pro vývoj pro vývoj a ne pro produkční účely.

11. Nakonfigurujte zásady TLS pro použití ve službě Application Gateway. Application Gateway podporuje možnost nastavit minimální verzi pro verze protokolu TLS.

    Následující hodnoty jsou seznam verzí protokolu, které lze definovat:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    Následující příklad nastaví minimální verzi protokolu na **TLSv1_2** a povolí **TLS \_ ecdh \_ ECDSA \_ s \_ AES \_ 128 \_ GCM \_ SHA256** , **TLS \_ ECDH \_ ECDSA \_ with \_ AES \_ 256 \_ GCM \_ SHA384** a **TLS \_ RSA \_ s \_ AES \_ 128 pouze \_ GCM \_** SHA256.

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Pomocí všech předchozích kroků vytvořte Aplikační bránu. Vytvoření brány je proces, který trvá dlouhou dobu, než se spustí.

Pro SKU v1 použijte následující příkaz.
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

Pro SKU v2 použijte následující příkaz.
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Použití nového certifikátu v případě vypršení platnosti back-endu certifikátu

Tento postup použijte, pokud chcete použít nový certifikát, pokud vypršela platnost back-endu certifikátu.

1. Načtěte Aplikační bránu, která se má aktualizovat.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Přidejte nový prostředek certifikátu ze souboru. CER, který obsahuje veřejný klíč certifikátu, a může to být také stejný certifikát přidaný do naslouchacího procesu pro ukončení TLS ve službě Application Gateway.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Získejte nový objekt ověřovacího certifikátu do proměnné (TypeName: Microsoft. Azure. Commands. Network. Models. PSApplicationGatewayAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Přiřaďte nový certifikát do nastavení **BackendHttp** a přečtěte ho pomocí proměnné $AuthCert. (Zadejte název nastavení HTTP, který chcete změnit.)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Potvrďte změnu v aplikační bráně a předejte novou konfiguraci obsaženou v proměnné $out.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Odebrat nepoužitý certifikát s vypršenou platností z nastavení HTTP

Pomocí tohoto postupu můžete z nastavení HTTP odebrat nepoužitý certifikát s vypršenou platností.

1. Načtěte Aplikační bránu, která se má aktualizovat.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Vypíše název ověřovacího certifikátu, který chcete odebrat.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Odeberte ověřovací certifikát z aplikační brány.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Potvrďte změnu.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-tls-protocol-versions-on-an-existing-application-gateway"></a>Omezení verzí protokolu TLS v existující aplikační bráně

Předchozí kroky vás provedly vytvořením aplikace s koncovým protokolem TLS a zakázáním určitých verzí protokolu TLS. Následující příklad zakáže určité zásady TLS pro existující Aplikační bránu.

1. Načtěte Aplikační bránu, která se má aktualizovat.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Definujte zásadu TLS. V následujícím příkladu jsou **tlsv 1.0** a **tlsv 1.1** zakázané a šifrovací sady **TLS \_ ECDH \_ ECDSA \_ s \_ AES \_ 128 \_ GCM \_ SHA256** , **TLS \_ ECDH \_ ECDSA \_ s \_ AES \_ 256 \_ GCM \_ SHA384** a **TLS \_ RSA \_ s \_ AES \_ 128 \_ GCM \_** SHA256 jsou jenom ty povolené.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Nakonec aktualizujte bránu. Tento poslední krok je dlouhodobě spuštěný úkol. Až to bude hotové, bude na aplikační bráně nakonfigurovaná koncová TLS.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Získat název DNS služby Application Gateway

Po vytvoření brány je dalším krokem konfigurace front-endu pro komunikaci. Application Gateway vyžaduje dynamicky přiřazený název DNS při použití veřejné IP adresy, která není uživatelsky přívětivá. Chcete-li zajistit, aby koncoví uživatelé mohli dosáhnout služby Application Gateway, můžete použít záznam CNAME, který odkazuje na veřejný koncový bod služby Application Gateway. Další informace najdete v tématu [Konfigurace vlastního názvu domény pro v Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Pokud chcete nakonfigurovat alias, načtěte podrobnosti o aplikační bráně a její přidružené IP adrese nebo názvu DNS pomocí elementu **PublicIPAddress** připojeného ke službě Application Gateway. Pomocí názvu DNS služby Application Gateway vytvořte záznam CNAME, který bude odkazovat na tyto dvě webové aplikace na tento název DNS. Nedoporučujeme používat záznam A – protože virtuální IP adresa se může změnit při restartování služby Application Gateway.

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

Další informace o posílení zabezpečení webových aplikací pomocí brány firewall webových aplikací prostřednictvím Application Gateway najdete v tématu [Přehled firewallu webových aplikací](../web-application-firewall/ag/ag-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png