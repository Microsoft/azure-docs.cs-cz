---
title: Vývoj zabezpečené webové aplikace Azure AD | Dokumenty společnosti Microsoft
description: Tato jednoduchá ukázková aplikace implementuje osvědčené postupy zabezpečení, které vylepšují vaši aplikaci a stav zabezpečení vaší organizace při vývoji v Azure.
keywords: není k dispozici
services: security
documentationcenter: na
author: TerryLanfear
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: terrylan
ms.openlocfilehash: 599c4a31840b47294b43c4c4d1f0200b17f04540
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810549"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Vývoj zabezpečené aplikace pro aplikaci Azure AD
## <a name="overview"></a>Přehled

Tato ukázka je jednoduchá služba Azure Active Directory s webovou aplikací, která odkazuje na prostředky zabezpečení pro vývoj aplikací v Azure. Aplikace implementuje osvědčené postupy zabezpečení, které vám pomůžou zlepšit vaši aplikaci a stav zabezpečení vaší organizace při vývoji aplikací v Azure.

Skripty nasazení nastavují infrastrukturu. Po spuštění skriptů nasazení budete muset provést nějakou ruční konfiguraci na webu Azure Portal, abyste propojili součásti a služby dohromady. Tato ukázka je zaměřená na zkušené vývojáře v Azure, kteří pracují v rámci maloobchodního průmyslu a chtějí vytvořit zabezpečenou službu Azure Active Directory se zabezpečenou infrastrukturou Azure. 


Při vývoji a nasazování této aplikace se dozvíte, jak 
- Vytvořte instanci Azure Key Vault, uložte a načtěte z ní tajné klíče.
- Nasaďte Azure Web App, který je vyhrazený izolovaný s přístupem front-endfirewall. 
- Vytvořte a nakonfigurujte instanci Aplikační brány Azure pomocí brány firewall, která používá sadu pravidel OWASP Top 10. 
- Povolte šifrování dat při přenosu a v klidovém stavu pomocí služeb Azure. 
- Nastavte centrum zásad a zabezpečení Azure pro vyhodnocení kompatibility. 

Po vývoji a nasazení této aplikace budete mít nastavena následující ukázková webová aplikace spolu s konfigurací a bezpečnostními opatřeními, která jsou popsána.

## <a name="architecture"></a>Architektura
Aplikace je typická n-vrstvá aplikace se třemi vrstvami. Front-end, back-end a databázová vrstva s integrovanými komponentami monitorování a správy tajných klíčů jsou zobrazeny zde:

![Architektura aplikací](./media/secure-aad-app/architecture.png)

Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení jsou v části Architektura nasazení. 

Architektura se skládá z těchto komponent

- [Brána aplikací Azure](../../application-gateway/index.yml). Poskytuje bránu a bránu firewall pro naši aplikační architekturu.
- [Přehledy aplikací](../../azure-monitor/app/app-insights-overview.md). Poskytuje rozšiřitelnou službu Správy výkonu aplikací (APM) na více platformách.
- [Azure Key Vault](../../key-vault/index.yml). Ukládá a šifruje tajné kódy naší aplikace a spravuje vytváření zásad přístupu kolem nich.
- [Služba Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Poskytuje cloudovou službu správy identit a přístupu, přihlášení a přístup k prostředkům.
- [Systém DNS Azure](../../dns/dns-overview.md). Poskytněte službu pro hostování domény.
- [Azure Load Balancer:](../../load-balancer/load-balancer-overview.md) Poskytuje škálování aplikací a vytváření vysoké dostupnosti pro vaše služby.
- [Azure Web App](../../app-service/overview.md).  Poskytuje službu založenou na protokolu HTTP pro hostování webových aplikací.
- [Azure Security Center](../../security-center/index.yml). poskytuje pokročilou ochranu před hrozbami napříč hybridními úlohami v cloudu.
- [Zásady Azure](../../governance/policy/overview.md). Poskytuje vyhodnocení prostředků pro nedodržování přiřazených zásad.

## <a name="threat-model"></a>Model hrozby
Modelování hrozeb je proces identifikace potenciálních bezpečnostních hrozeb pro vaši firmu a aplikaci a zajištění, že je zaveden správný plán zmírnění.

Tato ukázka používá [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) k implementaci modelování hrozeb pro zabezpečenou ukázkovou aplikaci. Pomocí diagramu komponent a toků dat můžete identifikovat problémy a hrozby v rané fázi procesu vývoje. Čas a peníze budou uloženy později pomocí tohoto.

Zde je model ohrožení pro ukázkovou aplikaci

![Model hrozby](./media/secure-aad-app/threat-model.png)

Některé ukázkové hrozby a potenciální chyby zabezpečení, které generuje nástroj pro modelování hrozeb, jsou zobrazeny na následujícím snímku obrazovky. Model hrozeb poskytuje přehled o vystaveném povrchu útoku a vyzve vývojáře, aby přemýšleli o tom, jak zmírnit problémy.

![Výstup modelu hrozby](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Požadavky
Chcete-li aplikaci zprovoznit, je třeba nainstalovat tyto nástroje:

- Editor kódu pro úpravu a zobrazení kódu aplikace. [Visual Studio Code](https://code.visualstudio.com/) je open source možnost.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) ve vývojovém počítači.
- [Git](https://git-scm.com/) ve vašem systému. Git se používá k místnímu klonování zdrojového kódu.
- [jq](https://stedolan.github.io/jq/), unixový nástroj pro dotazování JSON uživatelsky přívětivým způsobem.

K nasazení prostředků ukázkové aplikace potřebujete předplatné Azure. Pokud nemáte předplatné Azure, můžete [si vytvořit bezplatný účet](https://azure.microsoft.com/free/) pro testování ukázkové aplikace.

Po instalaci těchto nástrojů jste připraveni nasadit aplikaci v Azure.

### <a name="implementation-guidance"></a>Prováděcí pokyny
Skript nasazení je jeden skript, který lze rozdělit do čtyř fází. Každá fáze nasazuje a konfiguruje prostředek Azure, který je v [diagramu architektury](#architecture).

Čtyři fáze jsou

- Nasazení trezoru klíčů Azure.
- Nasaďte Azure Web Apps.
- Nasazení brány aplikace pomocí brány firewall webových aplikací
- Nakonfigurujte Azure AD s nasazenou aplikací.

Každá fáze vychází z předchozí ho pomocí konfigurace z dříve nasazených prostředků.

Chcete-li dokončit kroky implementace, ujistěte se, že jste nainstalovali nástroje uvedené v části [Požadavky](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Nasazení trezoru klíčů Azure
V této části vytvoříte a nasadíte instanci Azure Key Vault, která se používá k ukládání tajných kódů a certifikátů.

Po dokončení nasazení máte instanci Azure Key Vault nasazenou v Azure.

Nasazení úložiště klíčů Azure pomocí Powershellu
 
1. Deklarujte proměnné pro Azure Key Vault.
2. Zaregistrujte zprostředkovatele Azure Key Vault.
3. Vytvořte skupinu prostředků pro instanci.
4. Vytvořte instanci Azure Key Vault ve skupině prostředků vytvořené v kroku 3.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>Níže uvedený uživatel Azure AD bude mít oprávnění správce k trezoru klíčů
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Zaregistrujte poskytovatele Az
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Vytvoření instance trezoru klíčů Azure
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Přidání zásad správce do trezoru klíčů
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Chcete-li vytvořit zásady přístupu, které uživateli umožní získat a vypsat kryptografické klíče, certifikáty a tajné klíče, pokud znáte hlavní jméno uživatele:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

Je osvědčeným postupem používat spravované identity pro prostředky Azure v aplikacích, které používají Key Vault pro přístup k prostředkům. Stav zabezpečení se zvyšuje, když přístupové klíče k trezoru klíčů nejsou uloženy v kódu nebo v konfiguraci.

Kořenový certifikát je součástí kontejneru. Kroky podniknuté k získání certifikátu jsou

1. Stáhněte soubor certifikátu z [certifikační autority](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. Dekódujte soubor certifikátu:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Tento skript vytvoří přiřazenou identitu pro instanci služby App Service, kterou lze použít s MSI k interakci s trezorem klíčů Azure bez tajných kódů pevného kódování v kódu nebo konfiguraci.

Přejděte na instanci Azure Key Vault na portálu a autorizujte přiřazenou identitu na kartě zásad přístupu. **Add new access policy** V **části Vybrat hlavní objekt**vyhledejte název aplikace, který je podobný názvu vytvořené instance služby App Service.
Instanční objekt připojený k aplikaci by měl být viditelný. Vyberte ji a uložte stránku zásad přístupu, jak je znázorněno na následujícím snímku obrazovky.

Vzhledem k tomu, že aplikace potřebuje pouze načíst klíče, vyberte **získat** oprávnění v možnostech tajných klíčů, povolení přístupu při současném snížení udělených oprávnění.

![Zásady přístupu k trezoru klíčů](./media/secure-aad-app/kv-access-policy.png)

*Vytvoření zásad přístupu trezoru klíčů*

Uložte zásady přístupu a pak uložte novou změnu na kartě **Zásady přístupu** a aktualizujte zásady.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Nasazení aplikační brány s povolenou bránou firewall webových aplikací
Ve webových aplikacích se nedoporučuje vystavovat služby přímo vnějšímu světu na internetu.
Pravidla vyrovnávání zatížení a brány firewall poskytují větší zabezpečení a kontrolu nad příchozím provozem a pomáhají vám je spravovat.

Nasazení instance aplikační brány

1. Vytvořte skupinu prostředků pro použití aplikační brány.
2. Zřízení virtuální sítě připojit k bráně.
3. Vytvořte podsíť pro bránu ve virtuální síti.
4. Zřídit veřejnou IP adresu.
5. Zřízení brány aplikace.
6. Povolte bránu firewall webových aplikací na bráně.

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.3.11

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 


#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the TLS/SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the TLS/SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Nasazení webových aplikací Azure
Azure App Service umožňuje vytvářet a hostovat webové aplikace pomocí jazyků, jako je Python, Ruby, C# a Java. Azure také podporuje vlastní kontejnery, které můžou prakticky všechny programovací jazyky spouštět na platformě Azure App Service.

#### <a name="create-an-app-service-plan-in-free-tier"></a>Vytvoření plánu služby App Service na úrovni Free
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Vytvoření webové aplikace
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>Než budete pokračovat, přejděte do uživatelského rozhraní konfigurace systému Dns https://aka.ms/appservicecustomdns Azure pro vaši vlastní doménu a podle pokynů nakonfigurujte záznam CNAME pro název_hostitele "www" a namiřte ho na výchozí název domény webové aplikace.

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>Upgrade plánu služby App Service na sdílenou úroveň (minimálně vyžadováno vlastními doménami)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Přidání vlastního názvu domény do webové aplikace
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení

### <a name="network"></a>Síť
Po dokončení nasazení máte aplikační bránu s povolenou bránou firewall webové aplikace.

Instance brány zpřístupňuje port 443 pro protokol HTTPS. Tato konfigurace zajišťuje, že naše aplikace je přístupná pouze na portu 443 prostřednictvím protokolu HTTPS.

Blokování nepoužívaných portů a omezení expozice povrchu útoku je osvědčeným postupem zabezpečení.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Přidání skupin zabezpečení sítě do instance služby App Service

Instance služby App Service lze integrovat s virtuálními sítěmi. Tato integrace umožňuje jejich konfiguraci pomocí zásad skupiny zabezpečení sítě, které spravují příchozí a odchozí provoz aplikace.

1. Chcete-li tuto funkci povolit, v okně instance služby Azure App v části **Nastavení**vybere **možnost Síť**. V pravém podokně nakonfigurujte v části **Integrace virtuální sítě**.

   ![Nová integrace virtuální sítě](./media/secure-web-app/app-vnet-menu.png)

    *Nová integrace virtuální sítě pro službu App Service*
1. Na další stránce vyberte **Přidat virtuální síť (náhled).**

1. V další nabídce vyberte virtuální síť vytvořenou `aad-vnet`v nasazení, které začíná na . Můžete buď vytvořit novou podsíť, nebo vybrat existující síť.
   V takovém případě vytvořte novou podsíť. Nastavte **rozsah Adresa** na **10.0.3.0/24** a pojmenujte **podsíť podsítě podsítě**.

   ![Konfigurace virtuální sítě služby App Service](./media/secure-web-app/app-vnet-config.png)

    *Konfigurace virtuální sítě pro službu App Service*

Teď, když jste povolili integraci virtuální sítě, můžete do naší aplikace přidat skupiny zabezpečení sítě.

1. Použijte vyhledávací pole, vyhledejte **skupiny zabezpečení sítě**. Ve výsledcích vyberte **skupiny zabezpečení sítě.**

    ![Hledání skupin zabezpečení sítě](./media/secure-web-app/nsg-search-menu.png)

    *Hledání skupin zabezpečení sítě*

2. V další nabídce vyberte **Přidat**. Zadejte **název** skupiny nsg a **skupiny prostředků,** ve které by měl být umístěn. Tento soubor nsg bude použit v podsíti aplikační brány.

    ![Vytvoření nSG](./media/secure-web-app/nsg-create-new.png)

    *Vytvoření nSG*

3. Po vytvoření souboru nsg vyberte jej. V okně vyberte v části **Nastavení** **pravidla příchozího zabezpečení**. Nakonfigurujte tato nastavení tak, aby umožňovala připojení přicházející do brány aplikace přes port 443.

   ![Konfigurace sítě zabezpečení sítě](./media/secure-web-app/nsg-gateway-config.png)

   *Konfigurace sítě zabezpečení sítě*

4. Do odchozích pravidel pro službu nsg brány přidejte pravidlo, které umožňuje odchozí připojení k instanci služby App Service vytvořením pravidla, které cílí na značku služby.`AppService`

   ![Přidání odchozích pravidel pro zákon o neplnění pravidel silničního provozu](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Přidání odchozích pravidel pro zákon o neplnění pravidel silničního provozu*

    Přidejte další odchozí pravidlo, které povoluje bráně odesílat odchozí pravidla do virtuální sítě.

   ![Přidání dalšího odchozího pravidla](./media/secure-web-app/nsg-outbound-vnet.png)

    *Přidání dalšího odchozího pravidla*

5. V okně podsítí skupiny nsg vyberte **Přidružit**, vyberte virtuální síť vytvořenou v nasazení a vyberte podsíť brány s názvem **gw-podsíť**. Skupina nsg se použije v podsíti.

6. Vytvořte jiný soubor služeb nsg jako v předchozím kroku, tentokrát pro instanci služby App Service. Dejte mu jméno. Přidejte příchozí pravidlo pro port 443 stejně jako pro soubor NSG aplikační brány.

   Pokud máte instanci služby App Service nasazenou v instanci prostředí služby App Service, což není případ této aplikace, můžete přidat příchozí pravidla, která povolí sondy Azure Service Health otevřením portů 454-455 na příchozích skupinách zabezpečení vašeho nsg služby App Service. Zde je konfigurace:

   ![Přidání pravidel pro sondy stavu služby Azure](./media/secure-web-app/nsg-create-healthprobes.png)

    *Přidání pravidel pro sondy stavu služby Azure (jenom prostředí služby App Service)*

Chcete-li omezit prostor pro útok, upravte nastavení sítě služby App Service tak, aby přístup k aplikaci umožňovala pouze aplikační bráně.
Chcete-li použít nastavení, přejděte na kartu Síť služby App Service, vyberte kartu **Omezení IP** a vytvořte pravidlo povolení, které umožňuje přímo přistupovat ke službě pouze ip adresy brány aplikace. IP adresu brány můžete načíst z její stránky s přehledem. Na kartě **CIDR IP adresy** zadejte ADRESU `<GATEWAY_IP_ADDRESS>/32`IP v tomto formátu: .

![Povolit pouze bránu](./media/secure-web-app/app-allow-gw-only.png)

*Povolit přístup ke službě App Service pouze IP bráně*

### <a name="azure-domain-name-system"></a>Systém dns Azure 
Azure Domain Name System, neboli Azure Domain Name System, je zodpovědný za překlad (nebo překlad) názvu webu nebo služby na jeho IP adresu. Azure Domain Namehttps://docs.microsoft.com/azure/dns/dns-overview) System( je hostitelská služba pro domény DNS, která poskytuje překlad názvů pomocí infrastruktury Azure. Hostováním domén v Azure můžou uživatelé spravovat záznamy dns systému pomocí stejných přihlašovacích údajů, virtuálních api, nástrojů a fakturace jako ostatní služby Azure. Azure Domain Name System také podporuje privátní domény DNS.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption využívá funkci Nástroje bitlockeru systému Windows k zajištění šifrování svazků pro datové disky. Řešení se integruje s Azure Key Vault, které vám pomůže řídit a spravovat klíče šifrování disku.

### <a name="identity-management"></a>Správa identit
Následující technologie poskytují funkce pro správu přístupu k datům držitele karty v prostředí Azure
- Azure Active Directory je cloudová služba a služba správy identit společnosti Microsoft s více klienty. Všichni uživatelé pro toto řešení se vytvářejí ve službě Azure Active Directory, včetně uživatelů, kteří přistupují k Azure WebApp.
- Řízení přístupu na základě rolí Azure umožňuje správcům definovat jemně odstupňovaná přístupová oprávnění udělit pouze množství přístupu, které uživatelé potřebují k provádění svých úloh. Místo toho, aby každý uživatel neomezená oprávnění pro prostředky Azure, správci můžete povolit pouze určité akce pro přístup k datům držitele karty. Přístup k předplatnému je omezen na správce předplatného.
- Správa privilegovaných identit služby Azure Active Directory umožňuje zákazníkům minimalizovat počet uživatelů, kteří mají přístup k určitým informacím, jako jsou data držitele karty. Správci můžou pomocí správy privilegovaných identit služby Azure Active Directory zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tuto funkci lze také použít k vynucení přístupu pro správu na vyžádání, který je v případě potřeby v čase.
- Azure Active Directory Identity Protection detekuje potenciální chyby zabezpečení ovlivňující identity organizace, konfiguruje automatické odpovědi na zjištěné podezřelé akce související s identitami organizace a vyšetřuje podezřelé incidenty, aby podnikl a přijal vhodná opatření k jejich vyřešení.
### <a name="secrets-management"></a>Správa tajných kódů
Řešení používá Azure Key Vault pro správu klíčů a tajných klíčů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce služby Azure Key Vault pomáhají zákazníkům chránit tato data a získat k nim přístup
   - Rozšířené zásady přístupu jsou konfigurovány na základě potřeby.
   - Zásady přístupu trezoru klíčů jsou definovány s minimálními požadovanými oprávněními ke klíčům a tajným klíčům.
   - Všechny klíče a tajné klíče v trezoru klíčů mají data vypršení platnosti.
   - Všechny klíče v trezoru klíčů jsou chráněny specializovanými moduly hardwarového zabezpečení. Typ klíče je hardwarový bezpečnostní modul (HSM) Chráněný 2048bitový kód RSA.
   - Pomocí trezoru klíčů můžete šifrovat klíče a tajné klíče (například ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat, . PFX soubory a hesla) pomocí klíčů, které jsou chráněny moduly hardwarového zabezpečení (HSM). 
   - Pomocí řízení přístupu na základě rolí (RBAC) přiřaďte oprávnění uživatelům, skupinám a aplikacím v určitém oboru.     
   - Pomocí trezoru klíčů můžete spravovat certifikáty TLS pomocí automatického obnovení. 
   - Protokoly diagnostiky pro trezor klíčů jsou povoleny s dobou uchování alespoň 365 dnů.
   - Povolené kryptografické operace pro klíče jsou omezeny na ty, které jsou požadovány.
### <a name="azure-security-center"></a>Azure Security Center
Pomocí Azure Security Center můžou zákazníci centrálně používat a spravovat zásady zabezpečení napříč úlohami, omezovat vystavení hrozebm a zjišťovat útoky a reagovat na ně. Kromě toho 
   - Azure Security Center přistupuje k existujícím konfiguracím služeb Azure a poskytuje doporučení pro konfiguraci a služby, které pomáhají zlepšit stav zabezpečení a chránit data.
   - Azure Security Center používá celou řadu funkcí detekce k upozornění zákazníků na potenciální útoky zaměřené na jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Azure Security Center má sadu předdefinovaných výstrah zabezpečení, které se aktivují, když dojde k ohrožení nebo podezřelé aktivity. Vlastní pravidla výstrah v Azure Security Center umožňují zákazníkům definovat nové výstrahy zabezpečení na základě dat, která jsou již shromažďovány z jejich prostředí.
   - Azure Security Center poskytuje prioritní výstrahy zabezpečení a incidenty, takže je pro zákazníky jednodušší zjistit a řešit potenciální problémy se zabezpečením. Pro každou zjištěnou hrozbu je generována zpráva o zpravodajské službě hrozeb, která pomáhá týmům pro reakci na incidenty při vyšetřování a nápravě hrozeb.
### <a name="azure-application-gateway"></a>Azure Application Gateway 
   Architektura snižuje riziko ohrožení zabezpečení pomocí brány aplikace Azure s nakonfigurovanou bránou firewall webové aplikace a povolenou sadou pravidel OWASP. Mezi další možnosti patří
   - Komplexní TLS.
   - Zakažte TLS v1.0 a v1.1.
   - Povolte TLSv1.2.
   - Brána firewall webové aplikace (režim prevence).
   - Režim prevence se sadou pravidel OWASP 3.0.
   - Povolte protokolování diagnostiky.
   - Vlastní sondy stavu.
   - Azure Security Center a Azure Advisor poskytují další ochranu a oznámení. Azure Security Center také poskytuje systém reputace.
### <a name="logging-and-auditing"></a>Protokolování a auditování
Služby Azure rozsáhle zaznamenávají aktivity systému a uživatelů a také stav systému:
   - Protokoly aktivit: [Protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operacích prováděných s prostředky v předplatném. Protokoly aktivit mohou pomoci určit iniciátor operace, čas výskytu a stav.
   - Diagnostické protokoly: [Diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnují všechny protokoly vyzařované každým zdrojem. Tyto protokoly zahrnují protokoly systému událostí systému Windows, protokoly úložiště Azure, protokoly auditu trezoru klíčů a protokoly přístupu k bráně aplikace a brány firewall. Všechny diagnostické protokoly zapisují do centralizovaného a šifrovaného účtu úložiště Azure pro archivaci. Uchovávání informací lze konfigurovat uživatelem až 730 dní tak, aby splňovalo požadavky na uchovávání informací specifické pro danou organizaci.
### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor
   Tyto protokoly jsou konsolidovány v [protokolech Azure Monitor](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicího panelu. Po shromáždění jsou data uspořádána do samostatných tabulek pro každý datový typ v rámci pracovních prostorů Log Analytics, což umožňuje analyzovat všechna data společně bez ohledu na původní zdroj. Azure Security Center se navíc integruje s protokoly Azure Monitor, které zákazníkům umožňují používat dotazy Kusto k přístupu k datům událostí zabezpečení a kombinovat je s daty z jiných služeb.

   Následující [řešení monitorování](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Azure jsou součástí této architektury

   - [Vyhodnocení služby Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Řešení kontroly stavu služby Active Directory v pravidelných intervalech vyhodnocuje rizika a stav serverových prostředí a poskytuje seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
   - [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Řešení stavu agenta hlásí, kolik agentů je nasazeno a jejich geografické rozložení, a kolik agentů, které nereagují, a počet agentů, kteří odesílají provozní data.
   - [Analýza protokolu aktivit](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure ve všech předplatných Azure pro zákazníka.
### <a name="azure-monitor"></a>Azure Monitor
   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)pomáhá uživatelům sledovat výkon, udržovat zabezpečení a identifikovat trendy tím, že umožňuje organizacím auditovat, vytvářet výstrahy a archivovat data, včetně sledování volání rozhraní API v jejich prostředcích Azure.
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je rozšiřitelná služba Application Performance Management pro webové vývojáře na více platformách. Application Insights detekuje anomálie výkonu a zákazníci ji můžou použít ke sledování živé webové aplikace. Obsahuje výkonné analytické nástroje, které zákazníkům pomáhají diagnostikovat problémy a pochopit, co uživatelé se svou aplikací skutečně dělají. Je navržen tak, aby pomohl zákazníkům neustále zlepšovat výkon a použitelnost.

### <a name="azure-key-vault"></a>Azure Key Vault
   Vytvořte úschovnu pro organizaci, do které chcete ukládat klíče, a udržujte odpovědnost za provozní úkoly, jako je uvedeno níže

   - Data uložená v trezoru klíčů zahrnují   
   - Klíč přehledu aplikace
   - Klíč přístupu k úložišti dat
   - Připojovací řetězec
   - Název tabulky dat
   - Pověření uživatele
   - Rozšířené zásady přístupu jsou konfigurovány na základě potřeby
   - Zásady přístupu trezoru klíčů jsou definovány s minimálními požadovanými oprávněními ke klíčům a tajným klíčům.
   - Všechny klíče a tajné klíče v trezoru klíčů mají data vypršení platnosti
   - Všechny klíče v trezoru klíčů jsou chráněny modulem hardwarového zabezpečení (HSM) [Typ klíče = hardwarový modul zabezpečení (HSM) Protected       
     2048bitový klíč RSA]
   - Všem uživatelům/identitám jsou udělena minimální požadovaná oprávnění pomocí řízení přístupu na základě rolí (RBAC).
   - Aplikace nesdílejí trezor klíčů, pokud si vzájemně nedůvěřují a nepotřebují přístup ke stejným tajným kódům za běhu
   - Protokoly diagnostiky pro trezor klíčů jsou povoleny s dobou uchování alespoň 365 dnů.
   - Povolené kryptografické operace pro klíče jsou omezeny na ty, které jsou požadovány

### <a name="vpn-and-expressroute"></a>VPN a ExpressRoute
   Zabezpečené tunelové propojení VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) bylo potřeba nakonfigurovat bezpečným navázáním připojení k prostředkům nasazeným jako součást této referenční architektury webové aplikace PaaS. Vhodným nastavením sítě VPN nebo ExpressRoute mohou zákazníci přidat vrstvu ochrany dat při přenosu.

   Implementací zabezpečeného tunelového propojení VPN s Azure lze vytvořit virtuální privátní připojení mezi místní sítí a virtuální sítí Azure. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "tunelovat" informace uvnitř šifrovaného propojení mezi sítí zákazníka a Azure. Site-to-Site VPN je bezpečná, vyspělá technologie, která byla nasazena podniky všech velikostí po celá desetiletí. Režim tunelového propojení IPsec se používá v této možnosti jako šifrovací mechanismus.

   Vzhledem k tomu, že provoz v rámci tunelu VPN prochází Internet pomocí sítě VPN site-to-site, společnost Microsoft nabízí další, ještě bezpečnější možnost připojení. Azure ExpressRoute je vyhrazené propojení WAN mezi Azure a místním umístěním nebo poskytovatelem hostingu Exchange. Vzhledem k tomu, že připojení ExpressRoute neprocházejí přes Internet, tato připojení nabízejí vyšší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než běžná připojení přes Internet. Navíc vzhledem k tomu, že se jedná o přímé spojení poskytovatele telekomunikačních služeb zákazníka, data necestují přes internet, a proto mu nejsou vystavena.

   K [dispozici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)jsou doporučené postupy pro implementaci zabezpečené hybridní sítě, která rozšiřuje místní síť na Azure .

#### <a name="implement-azure-active-directory-oidc"></a>Implementace azure active directory OIDC

1. Chcete-li klonovat úložiště zdrojového kódu, použijte tento příkaz Git.

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>Aktualizace adres URL přesměrování
1.  Přejděte zpět na portál Azure. V levém navigačním podokně vyberte službu Azure Active Directory a pak vyberte Registrace aplikací.
2.  Na výsledné obrazovce vyberte aplikaci WebApp-OpenIDConnect-DotNet-code-v2.
3.  Na kartě Ověřování o V části Přesměrování identifikátorů URI vyberte v poli se seznamem možnost Web a přidejte následující identifikátory URI přesměrování.
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.nethttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o V části Upřesnit nastavení nastavte adresu URL odhlášení nahttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  Na kartě Branding o Aktualizujte například https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.netadresu URL domovské stránky na adresu služby aplikace .
        o Uložte konfiguraci.
5.  Pokud vaše aplikace volá webové rozhraní API, ujistěte se, že použít potřebné změny na projektu appsettings.json, tak volá publikovanou adresu URL rozhraní API namísto localhost.
Publikování ukázky
    1.  Na kartě Přehled služby App Service si stáhněte profil publikování kliknutím na odkaz Získat profil publikování a uložte ho. Lze také použít jiné mechanismy nasazení, například ze správy zdrojového kódu.
    2.  Přepněte do sady Visual Studio a přejděte do projektu WebApp-OpenIDConnect-DotNet-code-v2. Klikněte pravým tlačítkem myši na projekt v Průzkumníku řešení a vyberte Publikovat. Na dolním panelu klikněte na Importovat profil a importujte profil publikování, který jste stáhli dříve.
    3.  Klikněte na Konfigurovat a na kartě Připojení aktualizujte cílovou adresu URL tak, aby se například https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.netna adrese URL domovské stránky jelo https. Klikněte na Další.
    4.  Na kartě Nastavení zkontrolujte, zda není vybraná možnost Povolit organizační ověřování. Klikněte na Uložit. Klikněte na Publikovat na hlavní obrazovce.
    5.  Visual Studio publikuje projekt a automaticky otevře prohlížeč na adresu URL projektu. Pokud se zobrazí výchozí webová stránka projektu, publikace byla úspěšná.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementace vícefaktorového ověřování pro Azure Active Directory
   Správci musí zajistit, aby byly chráněny účty předplatného na portálu. Předplatné je zranitelné vůči útokům, protože spravuje prostředky, které jste vytvořili. Chcete-li předplatné chránit, povolte vícefaktorové ověřování na kartě **Azure Active Directory** předplatného.

   Azure AD funguje na základě zásad, které se používají pro uživatele nebo skupiny uživatelů, které odpovídají určitým kritériím.
Azure vytvoří výchozí zásadu určující, že správci potřebují dvoufaktorové ověřování pro přihlášení k portálu.
Po povolení této zásady se můžete zobrazit výzva k odhlášení a opětovnému přihlášení k portálu Azure.

Povolení vícefaktorové registrace pro přihlášení správce

   1. Přejděte na kartu **Azure Active Directory** na webu Azure Portal
   2. V kategorii zabezpečení vyberte podmíněný přístup. Zobrazí se tato obrazovka

       ![Podmíněný přístup – zásady](./media/secure-aad-app/ad-mfa-conditional-add.png)

Pokud nemůžete vytvořit novou zásadu

   1. Přejděte na kartu **Vícefaktorové.**
   2. Vyberte **bezplatný zkušební** odkaz Azure AD Premium a přihlaste se k odběru bezplatné zkušební verze.

   ![Bezplatná zkušební verze Azure AD Premium](./media/secure-aad-app/ad-trial-premium.png)

Vraťte se na obrazovku podmíněného přístupu.

   1. Vyberte novou kartu zásad.
   2. Zadejte název zásady.
   3. Vyberte uživatele nebo skupiny, pro které chcete povolit vícefaktorové povolení.
   4. V části **Access controls**vyberte kartu **Grant** a pak vyberte **Vyžadovat vícefaktorové ověřování** (a další nastavení, pokud chcete).

   ![Vyžadování MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   Zásadu můžete povolit zaškrtnutím políčka v horní části obrazovky nebo na kartě **Podmíněný přístup.** Pokud je zásada povolena, uživatelé potřebují vícefaktorové povolení k přihlášení k portálu.

   Existuje zásada směrného plánu, která vyžaduje vícefaktorové zabezpečení pro všechny správce Azure. Můžete ji okamžitě povolit na portálu. Povolení této zásady může zneplatnit aktuální relaci a přinutit vás znovu přihlásit.

   Pokud zásada směrného plánu není povolena
   1.   Vyberte **vyžadovat vícefaktorové finanční správy pro správce**.
   2.   Vyberte **možnost Použít zásady okamžitě**.

   ![Vyberte okamžitě použít zásady](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Monitorování aplikací a prostředků pomocí Azure Sentinelu

   Jak aplikace roste, je obtížné agregovat všechny signály zabezpečení a metriky přijaté z prostředků a učinit je užitečné způsobem orientovaným na akci.

   Azure Sentinel je navržený tak, aby shromažďoval data, zjišťoval možné typy hrozeb a poskytoval přehled o incidentech zabezpečení.
Zatímco čeká na ruční zásah, Azure Sentinel se může spolehnout na předem napsané playbooky k zahájení výstrah a procesů správy incidentů.

   Ukázková aplikace se skládá z několika prostředků, které azure sentinel můžete sledovat.
Chcete-li nastavit Azure Sentinel, musíte nejprve vytvořit pracovní prostor Analýzy protokolů, který ukládá všechna data shromážděná z různých prostředků.

Vytvoření tohoto pracovního prostoru

   1. Ve vyhledávacím poli na webu Azure Portal vyhledejte **službu Log Analytics**. Vyberte **pracovní prostory Analýzy protokolů**.

   ![Hledání pracovních prostorů Analýzy protokolů](./media/secure-aad-app/sentinel-log-analytics.png)

   *Hledání pracovních prostorů Analýzy protokolů*

   2. Na další stránce vyberte **Přidat** a zadejte název, skupinu prostředků a umístění pracovního prostoru.
   ![Vytvoření pracovního prostoru služby Log Analytics](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Vytvoření pracovního prostoru služby Log Analytics*

   3. Pomocí vyhledávacího pole vyhledejte **Azure Sentinel**.

   ![Vyhledání textu Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   *Vyhledání textu Azure Sentinel*

   4. Vyberte **Přidat** a pak vyberte pracovní prostor Analýzy protokolů, který jste vytvořili dříve.

   ![Přidání pracovního prostoru Analýzy protokolů](./media/secure-aad-app/sentinel-workspace-add.png)

   *Přidání pracovního prostoru Analýzy protokolů*

   5. Na stránce **Azure Sentinel – datové konektory** v části **Konfigurace**vyberte **Datové konektory**. Zobrazí se pole služeb Azure, které můžete propojit s instancí úložiště Log Analytics pro analýzu v Azure Sentinelu.

   ![Datové konektory Log Analytics](./media/secure-aad-app/sentinel-connectors.png)

      *Přidání datového konektoru do Azure Sentinelu*

   Chcete-li například připojit aplikační bránu, postupujte takto:

   1. Otevřete okno instance Aplikační brány Azure.
   2. V části **Monitorování** vyberte **Nastavení diagnostiky**.
   3. Vyberte **Přidat diagnostické nastavení**.

   ![Přidat diagnostiku aplikační brány](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Přidat diagnostiku aplikační brány*

   4. Na stránce **Nastavení diagnostiky** vyberte pracovní prostor Log Analytics, který jste vytvořili, a pak vyberte všechny metriky, které chcete shromáždit a odeslat do Azure Sentinelu. Vyberte **Uložit**.

   ![Nastavení konektoru Azure Sentinel](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Důležité informace o nákladech
   Pokud ještě nemáte účet Azure, můžete si vytvořit bezplatný účet. Na [stránce bezplatného účtu](https://azure.microsoft.com/free/) můžete začít, zjistit, co můžete dělat s bezplatným účtem Azure, a zjistit, které produkty jsou zdarma po dobu 12 měsíců.

   Chcete-li nasadit prostředky v ukázkové aplikaci s funkcemi zabezpečení, musíte zaplatit za některé prémiové funkce. Vzhledem k tomu, že aplikace se škáluje a bezplatné úrovně a zkušební verze nabízené Azure je třeba upgradovat tak, aby splňovaly požadavky aplikací, vaše náklady se mohou zvýšit. K odhadu nákladů použijte [cenovou kalkulačku](https://azure.microsoft.com/pricing/calculator/) Azure.

## <a name="next-steps"></a>Další kroky
   Následující články vám mohou pomoci navrhovat, vyvíjet a nasazovat zabezpečené aplikace.

- [Návrh](secure-design.md)
- [Vývoj](secure-develop.md)
- [Nasadit](secure-deploy.md)
