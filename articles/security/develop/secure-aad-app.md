---
title: Vývoj zabezpečené webové aplikace Azure AD | Microsoft Docs
description: Tato jednoduchá ukázková aplikace implementuje osvědčené postupy zabezpečení, které zlepšují vaši aplikaci a zabezpečení stav vaší organizace při vývoji v Azure.
keywords: ná
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
ms.openlocfilehash: 11bf7c0ae05c2e52d59efb32be47ce6bd96fac4f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937984"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Vývoj zabezpečené aplikace pro aplikaci Azure AD
## <a name="overview"></a>Přehled

Tato ukázka je jednoduchá Azure Active Directory s webovou aplikací, která odkazuje na prostředky zabezpečení pro vývoj aplikací v Azure. Aplikace implementuje osvědčené postupy zabezpečení, které vám při vývoji aplikací v Azure můžou zlepšit zabezpečení vaší aplikace a stav zabezpečení vaší organizace.

Skripty nasazení nastavily infrastrukturu. Po spuštění skriptů nasazení budete muset v Azure Portal provést několik ručních konfigurací, aby se komponenty a služby vzájemně procházely. Tato ukázka je zaměřená na zkušené vývojáře v Azure, kteří pracují v rámci maloobchodního průmyslu a chtějí vytvořit zabezpečený Azure Active Directory s zabezpečenou infrastrukturou Azure. 


Při vývoji a nasazení této aplikace se naučíte, jak 
- Vytvoření instance Azure Key Vault, ukládání a načítání tajných kódů z ní.
- Nasaďte webovou aplikaci Azure, která je vyhrazená izolovaně s přístupem front-end k bráně firewall. 
- Vytvořte a nakonfigurujte instanci Azure Application Gateway s bránou firewall, která používá OWASP prvních 10 RuleSet. 
- Povolí šifrování dat při přenosu a v klidovém provozu pomocí služeb Azure. 
- Nastavte službu Azure Policy a Security Center k vyhodnocení compliancies. 

Po vývoji a nasazení této aplikace budete mít k dispozici následující ukázkovou webovou aplikaci spolu s popsanými opatřeními konfigurace a zabezpečení.

## <a name="architecture"></a>Architektura
Aplikace je typickou n-vrstvou aplikací se třemi úrovněmi. Rozhraní front-end, back-end a databázová vrstva s integrovanými součástmi pro monitorování a správu tajného kódu se zobrazují tady:

![Architektura aplikací](./media/secure-aad-app/architecture.png)

Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení najdete v části architektura nasazení. 

Tato architektura se skládá z těchto součástí

- [Application Gateway Azure](../../application-gateway/index.yml). Poskytuje bránu a bránu firewall pro naši aplikační architekturu.
- [Application Insights](../../azure-monitor/app/app-insights-overview.md). Poskytuje rozšiřitelnou službu správy výkonu aplikací (APM) na různých platformách.
- [Azure Key Vault](../../key-vault/index.yml). Ukládá a šifruje tajné kódy naší aplikace a spravuje vytváření zásad přístupu.
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Poskytuje cloudovou službu pro správu identit a přístupu, přihlašování a přístup k prostředkům.
- [Azure Domain Name System](../../dns/dns-overview.md). Zadejte službu, která bude hostovat doménu.
- [Azure Load Balancer:](../../load-balancer/load-balancer-overview.md) Umožňuje škálování aplikací a vytváření vysoké dostupnosti pro vaše služby.
- [Webová aplikace Azure](../../app-service/overview.md).  Poskytuje službu založenou na protokolu HTTP pro hostování webových aplikací.
- [Azure Security Center](../../security-center/index.yml). poskytuje rozšířenou ochranu před hrozbami napříč vašimi hybridními úlohami v cloudu.
- [Azure Policy](../../governance/policy/overview.md). Poskytuje vyhodnocení prostředků pro nedodržování předpisů pomocí přiřazených zásad.

## <a name="threat-model"></a>Model hrozeb
Modelování hrozeb je proces identifikace potenciálních bezpečnostních hrozeb pro vaši firmu a aplikaci a následnému zajištění správného plánu zmírnění.

Tato ukázka používá [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) k implementaci modelování hrozeb pro zabezpečenou ukázkovou aplikaci. Díky vytváření diagramů komponent a toků dat můžete identifikovat problémy a hrozby včas v procesu vývoje. Čas a peníze budou později uloženy pomocí tohoto.

Toto je model hrozeb pro ukázkovou aplikaci.

![Model hrozeb](./media/secure-aad-app/threat-model.png)

Některé ukázkové hrozby a potenciální ohrožení zabezpečení, které nástroj pro modelování hrozeb generuje, se zobrazují na následujícím snímku obrazovky. Model hrozeb poskytuje přehled o zpřístupnění plochy pro útok a vyzývá vývojáře, aby si myslí, jak tyto problémy zmírnit.

![Výstup modelu hrozeb](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Požadavky
Chcete-li aplikaci nainstalovat a spustit, je nutné nainstalovat tyto nástroje:

- Editor kódu pro úpravu a zobrazení kódu aplikace. [Visual Studio Code](https://code.visualstudio.com/) je open-source možnost.
- Rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) ve vývojovém počítači.
- V systému [Git](https://git-scm.com/) . Git se používá ke klonování zdrojového kódu místně.
- [JQ](https://stedolan.github.io/jq/), nástroj pro systém UNIX pro dotazování formátu JSON uživatelsky přívětivým způsobem.

K nasazení prostředků ukázkové aplikace potřebujete předplatné Azure. Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/) pro otestování ukázkové aplikace.

Po instalaci těchto nástrojů jste připraveni aplikaci nasadit do Azure.

### <a name="implementation-guidance"></a>Pokyny k implementaci
Skript nasazení je jeden skript, který může být rozdělen do čtyř fází. Každá fáze nasadí a nakonfiguruje prostředek Azure, který je v [diagramu architektury](#architecture).

Čtyři fáze jsou

- Nasazení Azure Key Vault.
- Nasazení Azure Web Apps.
- Nasaďte Application Gateway s bránou firewall webových aplikací.
- Nakonfigurujte Azure AD s nasazenou aplikací.

Každá fáze sestaví na předchozí straně pomocí konfigurace z dříve nasazených prostředků.

K dokončení kroků implementace se ujistěte, že jste nainstalovali nástroje uvedené v části [požadavky](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Nasazení Azure Key Vault
V této části vytvoříte a nasadíte instanci Azure Key Vault, která se používá k ukládání tajných klíčů a certifikátů.

Po dokončení nasazení budete mít na Azure nasazenou instanci Azure Key Vault.

Nasazení Azure Key Vault pomocí prostředí PowerShell
 
1. Deklarujte proměnné pro Azure Key Vault.
2. Zaregistrujte poskytovatele Azure Key Vault.
3. Vytvořte skupinu prostředků pro instanci.
4. Vytvořte instanci Azure Key Vault ve skupině prostředků vytvořené v kroku 3.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>Níže uvedený uživatel Azure AD bude mít oprávnění správce k Key Vault
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Zaregistrujte poskytovatele AZ.
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Vytvoření instance Azure Key Vault
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Přidání zásad správce do Key Vault
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Vytvoření zásady přístupu, která umožní uživateli získat a vypsat kryptografické klíče, certifikáty a tajné klíče, pokud znáte hlavní název uživatele:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

Osvědčeným postupem je použití spravovaných identit pro prostředky Azure v aplikacích, které používají Key Vault k přístupu k prostředkům. Stav zabezpečení se zvyšuje, když se přístupové klíče Key Vault neukládají v kódu nebo v konfiguraci.

V kontejneru je zahrnutý kořenový certifikát. Postup pro získání certifikátu je

1. Stáhněte si soubor certifikátu od [certifikační autority](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. Dekódování souboru certifikátu:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Tento skript vytvoří přiřazenou identitu pro instanci App Service, kterou lze použít s balíčkem MSI k interakci s Azure Key Vault bez tajných tajných kódů v kódu nebo konfiguraci.

Na portálu přejděte na instanci Azure Key Vault a autorizujte přiřazenou identitu na kartě zásady přístupu. Vyberte **Přidat nové zásady přístupu**. V části **Vybrat objekt zabezpečení**vyhledejte název aplikace, který se podobá názvu vytvořené instance App Service.
Objekt služby připojený k aplikaci by měl být viditelný. Vyberte stránku a uložte si zásadu přístupu, jak je znázorněno na následujícím snímku obrazovky.

Vzhledem k tomu, že aplikace potřebuje jenom načíst klíče, vyberte v možnostech tajných kódů oprávnění **získat** a umožněte přístup při omezení udělených oprávnění.

![Zásada přístupu Key Vault](./media/secure-aad-app/kv-access-policy.png)

*Vytvoření zásady přístupu Key Vault*

Uložte zásady přístupu a pak uložte novou změnu na kartě **zásady přístupu** a aktualizujte zásady.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Nasazení Application Gateway s povoleným firewallem webových aplikací
Ve službě Web Apps nedoporučujeme vystavovat služby přímo na světovém světě na internetu.
Vyrovnávání zatížení a pravidla brány firewall poskytují lepší zabezpečení a kontrolu nad příchozím provozem a umožňují vám ho spravovat.

Nasazení instance Application Gateway

1. Vytvořte skupinu prostředků, která bude obsahovat Aplikační bránu.
2. Zajistěte, aby se virtuální síť připojila k bráně.
3. Vytvořte podsíť pro bránu ve virtuální síti.
4. Zřízení veřejné IP adresy.
5. Zřídit Aplikační bránu.
6. Povolte v bráně firewall webových aplikací.

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

#Upload the certificate to be used on the SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Nasazení Azure Web Apps
Azure App Service umožňuje sestavovat a hostovat webové aplikace s použitím jazyků, jako jsou Python, C#Ruby, a Java. Azure podporuje také vlastní kontejnery, které umožňují spouštět prakticky všechny programovací jazyky na platformě Azure App Service.

#### <a name="create-an-app-service-plan-in-free-tier"></a>Vytvoření plánu App Service na bezplatné úrovni
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Vytvořte webovou aplikaci
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>Než budete pokračovat, přejděte do uživatelského rozhraní konfigurace Azure Domain Name System pro vaši vlastní doménu a podle pokynů v tématu https://aka.ms/appservicecustomdns nakonfigurujte záznam CNAME pro název hostitele "www" a nasměrujte ho na výchozí název domény vaší webové aplikace.

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>Upgradovat App Service plán na sdílenou úroveň (minimální počet vyžadovaná vlastními doménami)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Přidání vlastního názvu domény do webové aplikace
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení

### <a name="network"></a>Network (Síť)
Po dokončení nasazení máte bránu Application Gateway s povoleným firewallem webových aplikací.

Instance brány zveřejňuje port 443 pro protokol HTTPS. Tato konfigurace zajišťuje, že naše aplikace je přístupná jenom na portu 443 přes protokol HTTPS.

Osvědčeným postupem zabezpečení blokování nepoužívaných portů a omezení expozice prostoru pro útok.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Přidání skupin zabezpečení sítě do instance App Service

Instance App Service lze integrovat s virtuálními sítěmi. Tato integrace umožňuje nakonfigurovat zásady skupiny zabezpečení sítě, které spravují příchozí a odchozí provoz aplikace.

1. Pokud chcete tuto funkci povolit, v okně instance služby Azure App Service v části **nastavení vybere možnost** **sítě**. V pravém podokně nakonfigurujte **integraci virtuální**sítě.

   ![Nová integrace virtuální sítě](./media/secure-web-app/app-vnet-menu.png)

    *Nová integrace virtuální sítě pro App Service*
1. Na další stránce vyberte **Přidat virtuální síť (Preview)** .

1. V další nabídce vyberte virtuální síť vytvořenou v nasazení, která začíná na `aad-vnet`. Můžete buď vytvořit novou podsíť, nebo vybrat některou z existujících.
   V takovém případě vytvořte novou podsíť. Nastavte **Rozsah adres** na **10.0.3.0/24** a pojmenujte podsíť **AppDomain App-Subnet**.

   ![Konfigurace App Service virtuální sítě](./media/secure-web-app/app-vnet-config.png)

    *Konfigurace virtuální sítě pro App Service*

Teď, když jste povolili integraci virtuální sítě, můžete do naší aplikace přidat skupiny zabezpečení sítě.

1. Pomocí vyhledávacího pole vyhledejte **skupiny zabezpečení sítě**. Ve výsledcích vyberte **skupiny zabezpečení sítě** .

    ![Vyhledat skupiny zabezpečení sítě](./media/secure-web-app/nsg-search-menu.png)

    *Vyhledat skupiny zabezpečení sítě*

2. V další nabídce vyberte **Přidat**. Zadejte **název** NSG a **skupinu prostředků** , ve které se má umístit. Tento NSG se použije pro podsíť služby Application Gateway.

    ![Vytvoření NSG](./media/secure-web-app/nsg-create-new.png)

    *Vytvoření NSG*

3. Po vytvoření NSG ho vyberte. V okně v části **Nastavení**vyberte **příchozí pravidla zabezpečení**. Nakonfigurujte tato nastavení tak, aby umožňovala připojení přicházející do aplikační brány přes port 443.

   ![Konfigurace NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Konfigurace NSG*

4. V odchozích pravidlech pro bránu NSG přidejte pravidlo, které umožňuje odchozí připojení k instanci App Service vytvořením pravidla, které cílí na tag služby `AppService`

   ![Přidat odchozí pravidla pro NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Přidat odchozí pravidla pro NSG*

    Přidejte další odchozí pravidlo, které povolí bráně odesílat odchozí pravidla do virtuální sítě.

   ![Přidat další odchozí pravidlo](./media/secure-web-app/nsg-outbound-vnet.png)

    *Přidat další odchozí pravidlo*

5. V okně podsítě v NSG vyberte **přidružit**, vyberte virtuální síť vytvořenou v nasazení a vyberte podsíť brány s názvem **GS-Subnet**. NSG se aplikuje na podsíť.

6. Vytvořte další NSG jako v předchozím kroku, tentokrát pro instanci App Service. Zadejte název. Přidejte příchozí pravidlo pro port 443 jako u služby Application Gateway NSG.

   Pokud máte nasazenou instanci App Service v instanci služby App Service Environment, která není pro tuto aplikaci případ, můžete přidat příchozí pravidla a povolit Azure Service Health sondy otevřením portů 454-455 v příchozích skupinách zabezpečení App Service NSG. Tady je konfigurace:

   ![Přidat pravidla pro Azure Service Health sondy](./media/secure-web-app/nsg-create-healthprobes.png)

    *Přidat pravidla pro Azure Service Health sondy (jenom App Service Environment)*

Chcete-li omezit plochu pro útok, upravte nastavení App Service sítě tak, aby umožňovalo přístup k aplikaci pouze bráně aplikace.
Chcete-li použít nastavení, přejděte na kartu App Service síť, vyberte kartu **omezení IP adres** a vytvořte pravidlo Povolit, které umožňuje, aby k této službě měl přímý přístup jenom IP adresa služby Application Gateway. IP adresu brány můžete načíst ze své stránky s přehledem. Na kartě **IP adresa CIDR** zadejte IP adresu v tomto formátu: `<GATEWAY_IP_ADDRESS>/32`.

![Povolí jenom bránu.](./media/secure-web-app/app-allow-gw-only.png)

*Pro přístup k App Service Povolte jenom IP adresu brány.*

### <a name="azure-domain-name-system"></a>Azure Domain Name System 
K překladu (nebo překladu) názvu webu nebo služby na jeho IP adresu zodpovídá Azure Domain Name System nebo Azure Domain Name System. Azure Domain Name System (https://docs.microsoft.com/azure/dns/dns-overview) je hostitelská služba pro domény Domain Name System, která poskytuje překlad adres IP pomocí infrastruktury Azure. Díky hostování domén v Azure můžou uživatelé spravovat záznamy DNS v systému pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturace jako jiné služby Azure. Azure Domain Name System podporuje také domény systému privátních názvů domén.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption využívá funkci nástroje BitLocker systému Windows k poskytování šifrování svazku pro datové disky. Řešení se integruje s Azure Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče disku.

### <a name="identity-management"></a>Správa identit
Následující technologie poskytují možnosti pro správu přístupu k datům držitele v prostředí Azure.
- Azure Active Directory je víceklientské cloudová služba Microsoftu a služba pro správu identit, která je založená na víceklientské architektuře. Všichni uživatelé tohoto řešení se vytvářejí v Azure Active Directory, včetně uživatelů, kteří přistupují k WebApp Azure.
- Řízení přístupu na základě role v Azure umožňuje správcům definovat jemně odstupňovaná přístupová oprávnění, která jim umožňují udělit jenom přístup, který uživatelé potřebují k provádění svých úloh. Místo udělení oprávnění pro všechny uživatele bez omezení přístupu k prostředkům Azure můžou správci pro přístup k datům na držiteli aplikace použít jenom určité akce. Přístup k předplatnému je omezený na správce předplatného.
- Azure Active Directory Privileged Identity Management zákazníkům umožňuje minimalizovat počet uživatelů, kteří mají přístup k určitým informacím, jako jsou například data o držiteli. Správci můžou pomocí Azure Active Directory Privileged Identity Management zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce se dá v případě potřeby použít taky k prosazování přístupu pro správu za běhu na vyžádání.
- Azure Active Directory Identity Protection detekuje potenciální ohrožení zabezpečení, která ovlivňují identity organizace, nakonfiguruje automatizované odezvy na zjištěné podezřelé akce související s identitami organizace a prošetří podezřelé incidenty, které mají přijmout vhodná opatření k jejich vyřešení.
### <a name="secrets-management"></a>Správa tajných kódů
Řešení používá Azure Key Vault pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující Azure Key Vault možnosti zákazníkům umožňují chránit taková data a přistupovat k nim.
   - Zásady pokročilého přístupu se konfigurují podle potřeby.
   - Zásady přístupu Key Vault jsou definované s minimálními požadovanými oprávněními pro klíče a tajné kódy.
   - Všechny klíče a tajné kódy v Key Vault mají datum vypršení platnosti.
   - Všechny klíče v Key Vault jsou chráněny specializovanými moduly hardwarového zabezpečení. Typ klíče je hardwarový klíč chráněný modulem hardwarového zabezpečení (HSM), který je chráněný 2048 klíčem RSA.
   - Pomocí Key Vault můžete šifrovat klíče a tajné klíče (například ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat). Soubory PFX a hesla) pomocí klíčů chráněných moduly hardwarového zabezpečení (HSM). 
   - K přiřazení oprávnění uživatelům, skupinám a aplikacím v určitém oboru použijte Access Control na základě rolí (RBAC).     
   - Pomocí Key Vault můžete spravovat certifikáty TLS pomocí automatické obnovy. 
   - Diagnostické protokoly pro Key Vault jsou povolené s dobou uchování minimálně 365 dní.
   - Povolené kryptografické operace pro klíče jsou omezené na ty, které jsou povinné.
### <a name="azure-security-center"></a>Centrum zabezpečení Azure
Díky Azure Security Center můžou zákazníci centrálně používat a spravovat zásady zabezpečení napříč úlohami, omezovat vystavení hrozeb a rozpoznávat a reagovat na útoky. Také 
   - Azure Security Center přistupuje k existujícím konfiguracím služeb Azure a poskytuje doporučení ke konfiguraci a službám, které vám pomůžou zlepšit zabezpečení stav a chránit data.
   - Azure Security Center využívá celou řadu možností detekce pro upozornění zákazníků na potenciální útoky, které cílí na jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Azure Security Center má sadu předdefinovaných výstrah zabezpečení, které se aktivují při výskytu hrozby nebo podezřelé aktivity. Vlastní pravidla výstrah v Azure Security Center umožňují zákazníkům definovat nové výstrahy zabezpečení na základě dat, která už jsou z jejich prostředí shromážděná.
   - Azure Security Center poskytuje prioritní výstrahy zabezpečení a incidenty, které zákazníkům zjednodušují zjišťování a řešení potenciálních problémů se zabezpečením. Pro každou zjištěnou hrozbu se vygeneruje sestava analýzy hrozeb, která pomáhá týmům reakce na incidenty při zkoumání a opravai hrozeb.
### <a name="azure-application-gateway"></a>Azure Application Gateway 
   Architektura snižuje riziko ohrožení zabezpečení pomocí Application Gateway Azure s nakonfigurovanou bránou firewall webových aplikací a povoleným rulesetem OWASP. Mezi další možnosti patří
   - Koncová SSL.
   - Zakažte TLS v 1.0 a v 1.1.
   - Povolte TLSv 1.2.
   - Firewall webových aplikací (režim prevence).
   - Režim prevence s OWASP 3,0 RuleSet.
   - Povolit protokolování diagnostiky.
   - Vlastní sondy stavu.
   - Azure Security Center a Azure Advisor poskytují dodatečnou ochranu a oznámení. Azure Security Center také nabízí systém reputace.
### <a name="logging-and-auditing"></a>Protokolování a auditování
Služba Azure výrazně zaznamenává činnost systému a uživatele a také stav systému:
   - Protokoly aktivit: [protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operacích provedených u prostředků v rámci předplatného. Protokoly aktivit můžou přispět k určení iniciátoru operace, času výskytu a stavu.
   - Diagnostické protokoly: [protokoly diagnostiky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obsahují všechny protokoly emitované každým prostředkem. Mezi tyto protokoly patří protokoly systému událostí Windows, protokoly Azure Storage, Key Vault protokoly auditu a protokoly Application Gateway přístupu a brány firewall. Všechny diagnostické protokoly zapisují do centralizovaného a šifrovaného účtu Azure Storage pro účely archivace. Uchovávání dat je uživatelsky konfigurovatelné, až 730 dní, aby se splnily požadavky na uchovávání specifické pro konkrétní organizaci.
### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor
   Tyto protokoly jsou konsolidovány v [protokolech Azure monitor](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicích panelů. Po shromáždění jsou data uspořádaná do samostatných tabulek pro každý datový typ v rámci Log Analyticsch pracovních prostorů, což umožňuje analyzovat všechna data bez ohledu na její původní zdroj. Kromě toho Azure Security Center integruje s protokoly Azure Monitor a umožňuje zákazníkům používat dotazy Kusto pro přístup k datům událostí zabezpečení a kombinovat je s daty z jiných služeb.

   Součástí této architektury jsou tato [řešení monitorování](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Azure.

   - [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): řešení kontroly stavu služby Active Directory posuzuje rizika a stav serverových prostředí v pravidelných intervalech a nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
   - [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): řešení agent Health zaznamenává počet nasazených agentů a jejich geografickou distribuci, jakož i počet agentů, kteří nereagují a počet agentů, kteří odesílají provozní data.
   - [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure pro zákazníka.
### <a name="azure-monitor"></a>Azure Monitor
   [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)pomáhá uživatelům sledovat výkon, udržovat zabezpečení a identifikovat trendy tím, že umožňují organizacím auditovat, vytvářet výstrahy a archivovat data, včetně sledovacích volání rozhraní API ve svých prostředcích Azure.
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je rozšiřitelná služba pro správu výkonu aplikací pro webové vývojáře na různých platformách. Application Insights detekuje anomálie výkonu a zákazníci je můžou použít k monitorování živé webové aplikace. Obsahuje výkonné analytické nástroje, které zákazníkům pomohou diagnostikovat problémy a porozumět tomu, co uživatelé skutečně dělají s aplikací. Je navržený tak, aby pomohla zákazníkům neustále zlepšovat výkon a použitelnost.

### <a name="azure-key-vault"></a>Azure Key Vault
   Vytvořte trezor pro organizaci, ve kterém se mají ukládat klíče, a udržujte zodpovědnost za provozní úlohy, jako jsou níže.

   - Data uložená v Key Vault zahrnují   
   - Klíč Application Insight
   - Přístupový klíč k úložišti dat
   - Připojovací řetězec
   - Název tabulky dat
   - Přihlašovací údaje uživatele
   - Zásady pokročilého přístupu jsou nakonfigurované podle potřeby.
   - Zásady přístupu Key Vault jsou definované s minimálními požadovanými oprávněními pro klíče a tajné kódy.
   - Všechny klíče a tajné kódy v Key Vault mají datum vypršení platnosti.
   - Všechny klíče v Key Vault chráněny modulem hardwarového zabezpečení (HSM) [typ klíče = chráněný modul hardwarového zabezpečení (HSM).       
     2048. bit RSA Key]
   - Všem uživatelům/identitám se uděluje minimální požadovaná oprávnění pomocí Access Control na základě rolí (RBAC).
   - Aplikace nesdílejí Key Vault, pokud mezi sebou vzájemně nedůvěřují a potřebují přístup ke stejným tajným klíčům za běhu.
   - Diagnostické protokoly pro Key Vault jsou povolené s dobou uchování minimálně 365 dní.
   - Povolené kryptografické operace pro klíče jsou omezené na ty, které jsou povinné.

### <a name="vpn-and-expressroute"></a>VPN a ExpressRoute
   Zabezpečené tunelové připojení VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) je nutné nakonfigurovat zabezpečeným navázáním připojení k prostředkům nasazeným jako součást referenční architektury webové aplikace PaaS. Díky správnému nastavení sítě VPN nebo ExpressRoute můžou zákazníci přidat vrstvu ochrany dat při přenosu.

   Díky implementaci zabezpečeného tunelu VPN s Azure je možné vytvořit virtuální privátní připojení mezi místní sítí a službou Azure Virtual Network. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "Tunnel" informace v rámci šifrovaného propojení mezi sítí zákazníka a Azure. Síť Site-to-Site VPN je zabezpečená a Vyspělá technologie, kterou vyvinuly podniky všech velikostí po desetiletí. Režim tunelového propojení IPsec se v této možnosti používá jako šifrovací mechanismus.

   Vzhledem k tomu, že provoz v rámci tunelu VPN prochází Internetem pomocí sítě VPN typu Site-to-site, společnost Microsoft nabízí jinou, ještě bezpečnější možnost připojení. Azure ExpressRoute je vyhrazené propojení WAN mezi Azure a místním umístěním nebo poskytovatelem hostingu Exchange. Vzhledem k tomu, že připojení ExpressRoute nepřecházejí přes Internet, tato připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes Internet. Vzhledem k tomu, že se jedná o přímé připojení zprostředkovatele telekomunikačních komunikací zákazníka, data necestují přes Internet, a proto se k nim nezveřejňují.

   [K dispozici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)jsou osvědčené postupy pro implementaci zabezpečené hybridní sítě, která rozšiřuje místní síť do Azure.

#### <a name="implement-azure-active-directory-oidc"></a>Implementovat Azure Active Directory OIDC

1. Pokud chcete klonovat úložiště zdrojového kódu, použijte tento příkaz git.

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>Aktualizace adres URL pro přesměrování
1.  Přejděte zpět na Azure Portal. V levém navigačním podokně vyberte službu Azure Active Directory a pak vyberte Registrace aplikací.
2.  Na výsledné obrazovce vyberte WebApp-OpenIDConnect-DotNet-Code-v2 aplikace.
3.  Na kartě ověřování v části identifikátory URI přesměrování vyberte možnost Web v poli se seznamem a přidejte následující identifikátory URI pro přesměrování.
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o v části Upřesnit nastavení nastavte adresu URL pro odhlášení na https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  Na kartě značky o aktualizujte adresu URL domovské stránky na adresu vaší služby App Service, například https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net.
        o uložení konfigurace.
5.  Pokud vaše aplikace volá webové rozhraní API, nezapomeňte použít potřebné změny v projektu appSettings. JSON, takže volá publikovanou adresu URL API namísto localhost.
Publikování ukázky
    1.  Na kartě Přehled App Service stáhněte profil publikování kliknutím na odkaz získat profil publikování a uložte ho. Lze také použít jiné mechanismy nasazení, jako je například ze správy zdrojového kódu.
    2.  Přepněte do sady Visual Studio a přejděte do projektu WebApp-OpenIDConnect-DotNet-Code-v2. Klikněte pravým tlačítkem na projekt v Průzkumník řešení a vyberte publikovat. Klikněte na tlačítko Importovat profil na dolním panelu a importujte profil publikování, který jste si stáhli dříve.
    3.  Klikněte na tlačítko konfigurovat a na kartě připojení aktualizujte cílovou adresu URL tak, aby se jedná o protokol HTTPS v adrese URL domovské stránky, například https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net. Klikněte na Další.
    4.  Na kartě nastavení se ujistěte, že není vybraná možnost povolit ověřování organizace. Klikněte na Uložit. Na hlavní obrazovce klikněte na publikovat.
    5.  Visual Studio projekt publikuje a automaticky otevře prohlížeč na adrese URL projektu. Pokud se zobrazí výchozí webová stránka projektu, publikace byla úspěšně dokončena.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementovat Multi-Factor Authentication pro Azure Active Directory
   Správci musí zajistit, aby byly účty předplatného na portálu chráněné. Předplatné je zranitelné vůči útokům, protože spravuje prostředky, které jste vytvořili. Pokud chcete předplatné chránit, povolte Multi-Factor Authentication na kartě **Azure Active Directory** předplatného.

   Služba Azure AD funguje na základě zásad, které se aplikují na uživatele nebo skupiny uživatelů, kteří odpovídají určitým kritériím.
Azure vytvoří výchozí zásadu, která určuje, že správci pro přihlášení k portálu potřebují ověřování pomocí dvou faktorů.
Po povolení této zásady se může zobrazit výzva, abyste se odhlásili a znovu přihlásili do Azure Portal.

Povolení MFA pro přihlášení správce

   1. Přejít na kartu **Azure Active Directory** v Azure Portal
   2. V kategorii zabezpečení vyberte podmíněný přístup. Tato obrazovka se zobrazí

       ![Podmíněný přístup – zásady](./media/secure-aad-app/ad-mfa-conditional-add.png)

Pokud nemůžete vytvořit novou zásadu

   1. Přejít na kartu **MFA**
   2. Vyberte odkaz Azure AD Premium **bezplatnou zkušební verzi** , abyste se přihlásili k odběru bezplatné zkušební verze.

   ![Azure AD Premium bezplatné zkušební verze](./media/secure-aad-app/ad-trial-premium.png)

Vraťte se na obrazovku podmíněného přístupu.

   1. Vyberte kartu nová zásada.
   2. Zadejte název zásady.
   3. Vyberte uživatele nebo skupiny, pro které chcete povolit MFA.
   4. V části **řízení přístupu**vyberte kartu **udělení** a potom v případě potřeby vyberte **vyžadovat vícefaktorové ověřování** (a další nastavení).

   ![Vyžadování MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   Zásadu můžete povolit zaškrtnutím políčka v horní části obrazovky nebo na kartě **podmíněný přístup** . Pokud je tato zásada povolená, uživatelé se k portálu budou potřebovat MFA pro přihlášení k portálu.

   Existují základní zásady, které vyžadují MFA pro všechny správce Azure. Můžete ji povolit hned na portálu. Když se tyto zásady povolí, může se zrušit platnost aktuální relace a znovu se přihlašujete.

   Pokud není zásada standardních hodnot povolená
   1.   Vyberte **vyžadovat MFA pro správce**.
   2.   Vyberte možnost **použít zásadu hned**.

   ![Vybrat použít zásadu hned](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Použití služby Azure Sentinel k monitorování aplikací a prostředků

   Vzhledem k tomu, že aplikace roste, je obtížné agregovat všechny signály zabezpečení a metriky obdržené z prostředků a učinit je užitečné v cestě orientované na akce.

   Sentinel Azure je navržená tak, aby shromáždila data, zjistila možné typy hrozeb a poskytovala přehled o incidentech zabezpečení.
I když čeká na ruční zásah, může Azure Sentinel spoléhat na předem zapsané playbooky, aby se aktivovaly výstrahy a procesy správy incidentů.

   Ukázková aplikace se skládá z několika prostředků, které může sledovat Azure Sentinel.
Pokud chcete nastavit službu Azure Sentinel, musíte nejdřív vytvořit Log Analytics pracovní prostor, ve kterém se budou ukládat všechna data shromážděná z různých prostředků.

Vytvoření tohoto pracovního prostoru

   1. Do vyhledávacího pole v Azure Portal vyhledejte **Log Analytics**. Vyberte **Log Analytics pracovní prostory**.

   ![Hledání Log Analytics pracovních prostorů](./media/secure-aad-app/sentinel-log-analytics.png)

   *Hledání Log Analytics pracovních prostorů*

   2. Na další stránce vyberte **Přidat** a potom zadejte název, skupinu prostředků a umístění pro pracovní prostor.
   ![Vytvoření pracovního prostoru Log Analytics](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Vytvoření pracovního prostoru Log Analytics*

   3. Pomocí vyhledávacího pole vyhledejte službu **Azure Sentinel**.

   ![Vyhledat Sentinel Azure](./media/secure-aad-app/sentinel-add.png)

   *Vyhledat Sentinel Azure*

   4. Vyberte **Přidat** a potom vyberte pracovní prostor Log Analytics, který jste vytvořili dříve.

   ![Přidání pracovního prostoru Log Analytics](./media/secure-aad-app/sentinel-workspace-add.png)

   *Přidání pracovního prostoru Log Analytics*

   5. Na stránce **datové konektory Azure Sentinel** v části **Konfigurace**vyberte **datové konektory**. Zobrazí se pole služeb Azure, které můžete propojit s instancí úložiště Log Analytics pro analýzu ve službě Azure Sentinel.

   ![Log Analytics datových konektorů](./media/secure-aad-app/sentinel-connectors.png)

      *Přidání datového konektoru do Azure Sentinel*

   Chcete-li například připojit Aplikační bránu, proveďte tyto kroky:

   1. Otevřete okno instance služby Azure Application Gateway.
   2. V části **monitorování**vyberte **nastavení diagnostiky**.
   3. Vyberte **Přidat nastavení diagnostiky**.

   ![Přidat diagnostiku Application Gateway](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Přidat diagnostiku Application Gateway*

   4. Na stránce **nastavení diagnostiky** vyberte pracovní prostor Log Analytics, který jste vytvořili, a pak vyberte všechny metriky, které chcete shromáždit a odeslat do Azure Sentinel. Vyberte **Uložit**.

   ![Nastavení konektoru služby Azure Sentinel](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Důležité informace o nákladech
   Pokud ještě nemáte účet Azure, můžete si vytvořit nějaký bezplatný. Přejděte na [stránku bezplatný účet](https://azure.microsoft.com/free/) , abyste mohli začít, podívejte se, co můžete dělat s bezplatným účtem Azure, a zjistěte, které produkty jsou zdarma po dobu 12 měsíců.

   Pokud chcete nasadit prostředky v ukázkové aplikaci s funkcemi zabezpečení, musíte platit za některé prémiové funkce. Vzhledem k tomu, že se aplikace škáluje a úrovně Free a zkušební verze, které nabízí Azure, je potřeba upgradovat, aby splňovaly požadavky na aplikace, může dojít ke zvýšení nákladů. Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) Azure můžete odhadnout náklady.

## <a name="next-steps"></a>Další kroky
   Následující články vám pomůžou při návrhu, vývoji a nasazení zabezpečených aplikací.

- [Vytvořit](secure-design.md)
- [Vývoj](secure-develop.md)
- [Nasazení](secure-deploy.md)
