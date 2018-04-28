---
title: Konfigurace protokolu SSL začátku do konce s Azure Application Gateway
description: Tento článek popisuje, jak konfigurovat začátku do konce protokolu SSL s Azure Application Gateway pomocí prostředí PowerShell
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/27/2018
ms.author: victorh
ms.openlocfilehash: 7e259936dce433683dd135171ee1c5626bf23739
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Konfigurace protokolu SSL začátku do konce pomocí Application Gateway pomocí prostředí PowerShell

## <a name="overview"></a>Přehled

Služba Azure Application Gateway podporuje začátku do konce šifrování přenosů. Aplikační brána se ukončuje připojení SSL na aplikační brány. Brána pak platí pravidla směrování pro provoz, znovu zašifruje paketu a předá paket odpovídající back-end serverů, na základě pravidel směrování definované. Každá odpověď webového serveru prochází ke koncovému uživateli stejným procesem.

Aplikační brána podporuje, definování vlastních možností protokolu SSL. Mimoto podporuje i zakázání následující verze protokolu: **TLSv1.0**, **TLSv1.1**, a **TLSv1.2**, i definice, které šifrovací sady používat a v pořadí podle preference . Další informace o konfigurovatelných možností protokolu SSL, najdete v článku [přehled zásad SSL](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> Protokol SSL 2.0 a SSL 3.0 jsou ve výchozím nastavení zakázané a nemůže být povolena. Že považovány za nezabezpečená a nelze použít s aplikační brány.

![scénář image][scenario]

## <a name="scenario"></a>Scénář

V tomto scénáři zjistěte, jak k vytvoření služby application gateway pomocí prostředí PowerShell SSL začátku do konce.

Tento scénář se:

* Vytvořte skupinu prostředků s názvem **appgw-rg**.
* Vytvořit virtuální síť s názvem **appgwvnet** s adresním prostorem z **10.0.0.0/16**.
* Vytvořte dvě podsítě názvem **appgwsubnet** a **appsubnet**.
* Vytvořte malá aplikace brány podpůrné začátku do konce šifrování SSL tohoto omezení verzí protokolu SSL a šifrovacích sad.

## <a name="before-you-begin"></a>Než začnete

Konfigurace protokolu SSL začátku do konce s aplikační bránu, vyžaduje se certifikát pro bránu a certifikáty se vyžadují pro back-end serverů. Certifikát brány se používá k šifrování a dešifrování přenosů do něj odeslané prostřednictvím protokolu SSL. Certifikát brány musí být ve formátu Personal Information Exchange (PFX). Tento formát souboru umožňuje exportovat privátní klíč, který vyžaduje službu application gateway slouží k šifrování a dešifrování přenosů.

Pro šifrování SSL začátku do konce back-end musí být seznam povolených adres pomocí služby application gateway. Budete muset nahrát na server veřejný certifikát back-end serverů aplikační brány. Certifikát se přidává zajistí, že aplikační bránu pouze komunikuje s známé instance back-end. Tato další zabezpečuje komunikaci začátku do konce.

Proces konfigurace je popsaná v následující části.

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Tato část vás provede procesem vytvoření skupiny prostředků, který obsahuje službu application gateway.


   1. Přihlaste se k účtu Azure.

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

Následující příklad vytvoří virtuální síť a dvě podsítě. Jednu podsíť se používá k ukládání aplikační brány. Další podsítě se používá pro back elementy end, které jsou hostiteli webové aplikace.


   1. Přiřaďte rozsah adres podsítě, který se má použít pro službu application gateway.

   ```powershell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Podsítě konfigurované pro službu application gateway by měl být správnou velikost. Aplikační brány mohou být konfigurovány pro až 10 instancí. Každá instance trvá jednu IP adresu z podsítě. Příliš malé podsítě může nepříznivě ovlivnit škálování služby application gateway.
   > 
   > 

   2. Přiřaďte rozsah adres, který se má použít pro fond back-end adresy.

   ```powershell
   $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

   3. Vytvořte virtuální síť s podsítí definované v předchozích krocích.

   ```powershell
   $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

   4. Načtení prostředků virtuální síť a podsíť prostředky, který se má použít v krocích, které následují.

   ```powershell
   $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Vytvoření veřejné IP adresy pro front-end konfiguraci

Vytvořte prostředek veřejné IP, který se má použít pro službu application gateway. Tato veřejná IP adresa se používá v jednom z následujících kroků.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Aplikační brána nepodporuje použití veřejnou IP adresu vytvořit s popiskem definované domény. Je podporován pouze veřejné IP adresy s popiskem dynamicky vytvořené domény. Pokud budete potřebovat popisný název DNS pro službu application gateway, doporučujeme že použít záznam CNAME jako alias.

## <a name="create-an-application-gateway-configuration-object"></a>Vytvoření objektu konfigurace služby Application Gateway

Před vytvořením služby application gateway se nastavit všechny položky konfigurace. Následující kroky slouží k vytvoření položek konfigurace potřebné pro prostředek služby Application Gateway.

   1. Vytvoření konfigurace IP aplikační brány. Toto nastavení konfiguruje používaný podsítě služby application gateway. Při spuštění služby application gateway se vybere IP adresa z nakonfigurované podsítě a trasy síťový provoz na IP adresy ve fondu back-end IP adres. Uvědomte si, že každá instance vyžaduje jednu IP adresu.

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```


   2. Vytvořte konfiguraci front-end IP adresy. Toto nastavení mapuje privátní nebo veřejné IP adresa front-endu služby application gateway. Následující krok přidruží veřejnou IP adresu v předchozím kroku s konfigurací front-end IP adresy.

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

   3. Nakonfigurujte fond back-end IP adres s IP adresami back endové webové servery. Tyto IP adresy jsou IP adresy, které přijímají síťový provoz, který přichází z koncového bodu front-end IP adresy. Nahraďte IP adresy v ukázce koncovými body IP adresy vlastní aplikace.

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Platný plně kvalifikovaný název domény (FQDN) je také platnou hodnotu používejte místo IP adresy pro back-end serverů. Můžete povolit pomocí **- BackendFqdns** přepínače. 


   4. Nakonfigurujte port front-end IP pro koncový bod veřejné IP adresy. Tento port je port, který koncoví uživatelé připojit k.

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

   5. Nakonfigurujte certifikát pro službu application gateway. Tento certifikát se používá k dešifrování a reencrypt provoz ve službě application gateway.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzureRmApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Tato ukázka se nakonfiguruje certifikát používaný pro připojení SSL. Certifikát musí být ve formátu PFX a heslo musí obsahovat 4 až 12 znaků.

   6. Vytvořte naslouchací proces protokolu HTTP pro službu application gateway. Přiřaďte konfiguraci front-end IP adresy, portu a certifikátu SSL se má použít.

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

   7. Nahrajte certifikát, který chcete použít pro protokol SSL povolen fond back-end prostředky.

   > [!NOTE]
   > Získá výchozí kontroly veřejného klíče z *výchozí* vazby SSL na IP adrese a porovná ji sem zadáte hodnotu veřejného klíče obdrží hodnotě veřejných klíčů můžete back-end. 
   
   > Pokud používáte hlavičky hostitele a indikace názvu serveru (SNI) na back-end, nemusí být načtena veřejný klíč zamýšlená lokalita, na které přenosové toky. Pokud máte pochybnosti, navštivte https://127.0.0.1/ na back-end serverech k potvrzení, který certifikát se používá pro *výchozí* vazbu SSL. V této části použijte veřejný klíč z tohoto požadavku. Pokud používáte hlavičky hostitele a SNI na vazby HTTPS a neobdržíte odpověď a certifikát z ruční prohlížeče požadavek na https://127.0.0.1/ na back-end serverech, je potřeba nastavit výchozí vazbu SSL na ně. Pokud to neuděláte, selhání sondy a back-end není povolený.

   ```powershell
   $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer
   ```

   > [!NOTE]
   > Certifikát zadaný v tomto kroku musí být veřejný klíč certifikátu .pfx v back-end. Export certifikátu (ne kořenový certifikát), nainstalovaný na serveru back-end ve formátu deklarace identity, důkaz a důvody (CER) a použít ho v tomto kroku. Tento krok povolených programů back-end pomocí služby application gateway.

   8. Nakonfigurujte nastavení protokolu HTTP pro aplikace brány back-end. Přiřadíte certifikát odeslali v předchozím kroku nastavení protokolu HTTP.

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```
   9. Vytvořte pravidlo směrování pro vyrovnávání zatížení, které konfiguruje chování nástroje pro vyrovnávání zatížení. V tomto příkladu se vytvoří základní pravidlo kruhového dotazování.

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   10. Nakonfigurujte velikost instance služby Application Gateway. Dostupné velikosti jsou **standardní\_malé**, **standardní\_střední**, a **standardní\_velké**.  Pro kapacitu, dostupné hodnoty jsou **1** prostřednictvím **10**.

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   > [!NOTE]
   > Počet instancí 1 lze zvolit pro účely testování. Je důležité vědět, že počet všech instancí v rámci dvě instance není předmětem smlouvě SLA a proto nedoporučujeme. Malé brány se mají použít pro vývoj testování a ne pro produkční účely.

   11. Nakonfigurujte zásady protokolu SSL pro službu application gateway používat. Aplikační brána podporuje možnost nastavit minimální verze pro verze protokolu SSL.

   Seznam verzí protokolu, které lze definovat jsou následující hodnoty:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
   Následující příklad nastaví verzi protokolu minimální **TLSv1_2** a umožňuje **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, a **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** pouze.

   ```powershell
   $SSLPolicy = New-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"
   ```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Pomocí všech předchozích kroků vytvořte aplikační bránu. Vytvoření brány je proces, který trvá dlouhou dobu spuštění.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Omezit verzí protokolu SSL na existující aplikační brány

Předchozí kroky trvalo vás vytváření aplikací pomocí protokolu SSL začátku do konce a zakázání určitých verzí protokolu SSL. Následující příklad zakazuje určité zásady protokolu SSL na existující aplikační brány.

   1. Načtení služby application gateway k aktualizaci.

   ```powershell
   $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

   2. Definujte zásady protokolu SSL. V následujícím příkladu **TLSv1.0** a **TLSv1.1** jsou zakázány a šifrovací sada **TLS\_ECDHE\_ECDSA\_WITH\_ AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, a **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** jsou jediné těch, které jsou povoleny.

   ```powershell
   Set-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

   3. Nakonec aktualizujte bránu. Upozorňujeme, že tento poslední krok se dlouho běžící úlohy. Až skončíte, začátku do konce SSL je nakonfigurován ve službě application gateway.

   ```powershell
   $gw | Set-AzureRmApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Získat název DNS brány aplikace

Po vytvoření brány je dalším krokem je konfigurace front-endu pro komunikaci. Aplikační brána vyžaduje dynamicky přiřazené název DNS, při použití veřejnou IP adresu, která není popisný. Zajistěte, aby koncoví uživatelé mohou dosáhl aplikační bránu, můžete záznam CNAME přejděte na veřejný koncový bod služby application gateway. Další informace najdete v tématu [konfigurace vlastního názvu domény pro v Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Pokud chcete konfigurovat alias, načíst podrobnosti o aplikační brány a její přidružené název IP a DNS pomocí **PublicIPAddress** element připojený k službě application gateway. Použijte službu application gateway název DNS vytvořit záznam CNAME, který odkazuje dva webové aplikace k tomuto názvu DNS. Jsme nemáte doporučujeme použití záznamů A, protože virtuální IP adresu můžete změnit na restartovat aplikační brány.

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

Další informace o posílení zabezpečení webových aplikací pomocí brány Firewall webových aplikací prostřednictvím brány aplikací najdete v tématu [brány firewall webových aplikací – přehled](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
