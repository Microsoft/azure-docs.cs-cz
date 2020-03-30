---
title: Generovat certifikát podepsaný svým držitelem s vlastní kořenovou certifikační autoritou
titleSuffix: Azure Application Gateway
description: Přečtěte si, jak generovat certifikát podepsaný vlastními podpisy brány Aplikace Azure s vlastní kořenovou certifikační autoritou.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 0447e87fd8685188af8008995ba938092f2b87fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293597"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Generovat certifikát podepsaný vlastními podpisy brány Aplikace Azure s vlastní kořenovou certifikační autoritou

Aplikační brána v2 SKU zavádí použití důvěryhodných kořenových certifikátů povolit back-endové servery. Tím odeberete ověřovací certifikáty, které byly požadovány ve skladové jednotce v1. *Kořenový certifikát* je Kódovaný X.509(. CER) formátuje kořenový certifikát ze serveru back-endového certifikátu. Identifikuje kořenovou certifikační autoritu (CA), která vydala certifikát serveru, a certifikát serveru se pak použije pro komunikaci SSL.

Aplikace Gateway ve výchozím nastavení důvěřuje certifikátu vašeho webu, pokud je podepsán známou certifikační autoritou (například GoDaddy nebo DigiCert). V takovém případě nemusíte explicitně nahrát kořenový certifikát. Další informace naleznete v [tématu Přehled ukončení SSL a od konce do konce SSL s aplikační bránou](ssl-overview.md). Pokud však máte prostředí pro vývoj a testování a nechcete si zakoupit ověřený certifikát podepsaný certifikační autoritou, můžete vytvořit vlastní certifikační autoritu a vytvořit s ním certifikát podepsaný svým držitelem. 

> [!NOTE]
> Certifikáty podepsané svým držitelem nejsou ve výchozím nastavení důvěryhodné a jejich údržba může být obtížně udržovatelné. Také mohou používat zastaralé hash a šifrovací sady, které nemusí být silné. Pro lepší zabezpečení si zakupte certifikát podepsaný známou certifikační autoritou.

V tomto článku se dozvíte, jak:

- Vytvoření vlastní certifikační autority
- Vytvoření certifikátu podepsaného svým držitelem podepsaného vlastní certifikační autoritou
- Nahrání kořenového certifikátu podepsaného svým držitelem do brány aplikace k ověření back-endového serveru

## <a name="prerequisites"></a>Požadavky

- **[OpenSSL](https://www.openssl.org/) v počítači se systémem Windows nebo Linux** 

   Zatímco pro správu certifikátů mohou být k dispozici další nástroje, tento kurz používá OpenSSL. Najdete OpenSSL svázaný s mnoha distribucemi Linuxu, jako je Ubuntu.
- **Webový server**

   Například Apache, IIS nebo NGINX otestovat certifikáty.

- **Aplikační brána v2 skladová položka**
   
  Pokud nemáte existující aplikační bránu, přečtěte [si úvodní příručku: Přímý webový provoz s Azure Application Gateway – portál Azure](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Vytvoření kořenového certifikátu certifikační autority

Vytvořte kořenový certifikát certifikační autority pomocí openssl.

### <a name="create-the-root-key"></a>Vytvoření kořenového klíče

1. Přihlaste se k počítači, kde je nainstalován openssl a spusťte následující příkaz. Tím se vytvoří klíč chráněný heslem.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. Na výzvu zadejte silné heslo. Například alespoň devět znaků s použitím velkých, malých písmen, čísel a symbolů.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Vytvoření kořenového certifikátu a jeho vlastní podepsání

1. Ke generování csr a certifikátu použijte následující příkazy.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Předchozí příkazy vytvoří kořenový certifikát. Tento certifikát použijete k podepsání certifikátu serveru.

1. Po zobrazení výzvy zadejte heslo pro kořenový klíč a organizační informace pro vlastní certifikační autoritu, jako je země, stát, organizace, organizační jednotky a plně kvalifikovaný název domény (toto je doména vystavittele).

   ![vytvořit kořenový certifikát](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Vytvoření certifikátu serveru

Dále vytvoříte certifikát serveru pomocí OpenSSL.

### <a name="create-the-certificates-key"></a>Vytvoření klíče certifikátu

Pomocí následujícího příkazu vygenerujte klíč pro certifikát serveru.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Vytvoření zástupce společnosti (žádost o podpis certifikátu)

Zástupce odpovědnosti za zástupce je veřejný klíč, který je přidělen certifikační autoritě při žádosti o certifikát. Certifikační autorita vydává certifikát pro tento konkrétní požadavek.

> [!NOTE]
> Cn (Common Name) pro certifikát serveru se musí lišit od domény vystavittele. Například v tomto případě je `www.contoso.com` kn pro vystavittele a certifikát `www.fabrikam.com`serveru cn je .


1. Ke generování zástupce počítače použijte následující příkaz:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Po zobrazení výzvy zadejte heslo pro kořenový klíč a informace o organizaci pro vlastní certifikační autoritu: Země, Stát, Organizace, Organizační složka a plně kvalifikovaný název domény. Jedná se o doménu webových stránek a měla by se lišit od vydavatele.

   ![Certifikát serveru](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Generovat certifikát pomocí zástupce a klíče a podepsat jej pomocí kořenového klíče certifikační autority

1. K vytvoření certifikátu použijte následující příkaz:

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Ověření nově vytvořeného certifikátu

1. Pomocí následujícího příkazu vytiskněte výstup souboru CRT a ověřte jeho obsah:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Ověření certifikátu](media/self-signed-certificates/verify-cert.png)

1. Ověřte soubory v adresáři a ujistěte se, že máte následující soubory:

   - contoso.crt
   - contoso.key
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-ssl-settings"></a>Konfigurace certifikátu v nastavení SSL webového serveru

Na webovém serveru nakonfigurujte protokol SSL pomocí souborů fabrikam.crt a fabrikam.key. Pokud váš webový server nemůže převzít dva soubory, můžete je kombinovat s jedním souborem .pem nebo .pfx pomocí příkazů OpenSSL.

### <a name="iis"></a>IIS

Pokyny k importu certifikátu a jejich nahrání jako certifikátu serveru ve službě IIS naleznete v [tématu HOW: Install Imported Certificates on a Web Server in Windows Server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server).

Pokyny pro vazbu SSL naleznete v tématu [Jak nastavit protokol SSL ve službě IIS 7](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

Následující konfigurace je příkladem [virtuálního hostitele nakonfigurovaného pro SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) v Apache:

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

Následující konfigurace je příklad [bloku serveru NGINX](https://nginx.org/docs/http/configuring_https_servers.html) s konfigurací SSL:

![NGINX s SSL](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Přístup k serveru k ověření konfigurace

1. Přidejte kořenový certifikát do důvěryhodného kořenového úložiště počítače. Při přístupu na web se ujistěte, že se v prohlížeči zobrazí celý řetězec certifikátů.

   ![Důvěryhodné kořenové certifikáty](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Předpokládá se, že služba DNS byla nakonfigurována tak, aby nasulala název webového serveru (v tomto příkladu www.fabrikam.com) na adresu IP webového serveru. Pokud tomu tak není, můžete upravit [soubor hosts](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) a přeložit název.
1. Přejděte na svůj web a kliknutím na ikonu zámku v poli adresy prohlížeče ověřte informace o webu a certifikátu.

## <a name="verify-the-configuration-with-openssl"></a>Ověření konfigurace pomocí openssl

Nebo můžete použít OpenSSL k ověření certifikátu.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![Ověření certifikátu OpenSSL](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Nahrání kořenového certifikátu do nastavení HTTP brány aplikace

Chcete-li certifikát odeslat do brány aplikace, musíte exportovat certifikát .crt do formátu .cer Base-64 kódovaného. Vzhledem k tomu, že .crt již obsahuje veřejný klíč v kódu base-64, stačí přejmenovat příponu souboru z .crt na .cer. 

### <a name="azure-portal"></a>portál Azure

Chcete-li nahrát důvěryhodný kořenový certifikát z portálu, vyberte **nastavení PROTOKOLU HTTP** a zvolte protokol **HTTPS.**

![Přidání certifikátu pomocí portálu](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Nebo můžete použít Azure CLI nebo Azure PowerShell k nahrání kořenového certifikátu. Následující kód je ukázka Prostředí Azure PowerShell.

> [!NOTE]
> Následující ukázka přidá důvěryhodný kořenový certifikát do brány aplikace, vytvoří nové nastavení HTTP a přidá nové pravidlo za předpokladu, že back-endový fond a naslouchací proces již existují.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that SSL handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```
### <a name="verify-the-application-gateway-backend-health"></a>Ověření stavu back-endu brány aplikace

1. Kliknutím na zobrazení **Stavu back-endu** v bráně aplikace zkontrolujte, jestli je sonda v pořádku.
1.    Měli byste vidět, že stav je **v pořádku** pro sondu HTTPS.

    ![Sonda HTTPS](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Další kroky

Další informace o ssl\tls v aplikační bráně najdete v [tématu Přehled ukončení ssl a od konce ssl s aplikační bránou](ssl-overview.md).

