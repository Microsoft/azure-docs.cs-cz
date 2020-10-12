---
title: Generování certifikátu podepsaného svým držitelem s vlastní kořenovou certifikační autoritou
titleSuffix: Azure Application Gateway
description: Naučte se generovat certifikát podepsaný svým držitelem Azure Application Gateway s vlastní kořenovou certifikační autoritou.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: de580d8f94292ae65769c696aa232f5b660bf414
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84806761"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Vygenerování certifikátu podepsaného svým držitelem Azure Application Gateway s vlastní kořenovou certifikační autoritou

SKU Application Gateway v2 zavádí použití důvěryhodných kořenových certifikátů k povolení back-end serverů. Tím dojde k odebrání ověřovacích certifikátů, které byly požadovány v SKU v1. *Kořenový certifikát* je X. 509 s kódováním Base-64 (. CER) naformátujte kořenový certifikát z back-endu Certificate serveru. Identifikuje kořenovou certifikační autoritu (CA), která vydala certifikát serveru, a certifikát serveru se pak používá pro komunikaci TLS/SSL.

Application Gateway důvěřuje certifikátu webu ve výchozím nastavení, pokud je podepsaný známou certifikační autoritou (například GoDaddy nebo DigiCert). V takovém případě nemusíte explicitně nahrávat kořenový certifikát. Další informace najdete v tématu [Přehled ukončení protokolu TLS a koncového šifrování TLS s Application Gateway](ssl-overview.md). Pokud ale máte vývojové a testovací prostředí a nechcete koupit ověřený certifikát podepsaný certifikační autoritou, můžete vytvořit vlastní certifikační autoritu a vytvořit certifikát podepsaný svým držitelem. 

> [!NOTE]
> Certifikáty podepsané svým držitelem nejsou ve výchozím nastavení důvěryhodné a jejich údržba může být obtížná. Můžou také používat zastaralé sady hash a šifrovací sady, které nemusí být silné. Pro lepší zabezpečení si kupte certifikát podepsaný známou certifikační autoritou.

V tomto článku se dozvíte, jak:

- Vytvoření vlastní certifikační autority
- Vytvoření certifikátu podepsaného svým držitelem podepsaného vaší vlastní certifikační autoritou
- Nahrajte kořenový certifikát podepsaný svým držitelem do Application Gateway k ověření serveru back-end.

## <a name="prerequisites"></a>Požadavky

- **[OpenSSL](https://www.openssl.org/) na počítači se systémem Windows nebo Linux** 

   I když mohou být k dispozici jiné nástroje pro správu certifikátů, v tomto kurzu se používá OpenSSL. Můžete najít OpenSSL sady s mnoha distribucemi systému Linux, jako je například Ubuntu.
- **Webový server**

   K testování certifikátů například Apache, IIS nebo NGINX.

- **SKU Application Gateway v2**
   
  Pokud nemáte existující Aplikační bránu, přečtěte si [rychlý Start: přímý webový provoz pomocí Azure Application Gateway-Azure Portal](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Vytvoření kořenového certifikátu certifikační autority

Vytvořte certifikát od kořenové certifikační autority pomocí OpenSSL.

### <a name="create-the-root-key"></a>Vytvoření kořenového klíče

1. Přihlaste se k počítači, kde je nainstalován OpenSSL, a spusťte následující příkaz. Tím se vytvoří klíč chráněný heslem.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. Na příkazovém řádku zadejte silné heslo. Například aspoň devět znaků s použitím velkých písmen, malých písmen, číslic a symbolů.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Vytvoření kořenového certifikátu a jeho samoobslužného podepsání

1. Pomocí následujících příkazů vygenerujte CSR a certifikát.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Předchozí příkazy vytvoří kořenový certifikát. Použijete ho k podepsání certifikátu serveru.

1. Po zobrazení výzvy zadejte heslo pro kořenový klíč a informace o organizaci vlastní certifikační autority, jako je například země/oblast, stát, organizace, organizační jednotka a plně kvalifikovaný název domény (Jedná se o doménu vystavitele).

   ![vytvořit kořenový certifikát](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Vytvoření certifikátu serveru

V dalším kroku vytvoříte certifikát serveru pomocí OpenSSL.

### <a name="create-the-certificates-key"></a>Vytvoření klíče certifikátu

K vygenerování klíče pro certifikát serveru použijte následující příkaz.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Vytvoření CSR (žádost o podepsání certifikátu)

CSR je veřejný klíč, který se udělí certifikační autoritě při žádosti o certifikát. Certifikační autorita vydá certifikát pro tento konkrétní požadavek.

> [!NOTE]
> CN (běžný název) pro certifikát serveru musí být jiný než doména vystavitele. V tomto případě je například CN pro vystavitele `www.contoso.com` a CN je certifikát serveru `www.fabrikam.com` .


1. K vygenerování CSR použijte následující příkaz:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Po zobrazení výzvy zadejte heslo pro kořenový klíč a informace o organizaci pro vlastní certifikační autoritu: země/oblast, stát, org, OU a plně kvalifikovaný název domény. Toto je doména webu a měla by se lišit od vystavitele.

   ![Certifikát serveru](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Vygenerujte certifikát pomocí CSR a klíče a podepište ho pomocí kořenového klíče certifikační autority.

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

1. Ověřte soubory ve vašem adresáři a ujistěte se, že máte následující soubory:

   - contoso. CRT
   - contoso. Key
   - Fabrikam. CRT
   - Fabrikam. Key

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>Konfigurace certifikátu v nastavení TLS webového serveru

Na webovém serveru nakonfigurujte TLS pomocí souborů Fabrikam. CRT a Fabrikam. Key. Pokud váš webový server nemůže převzít dva soubory, můžete je zkombinovat do jednoho souboru. pem nebo. pfx pomocí příkazů OpenSSL.

### <a name="iis"></a>IIS

Pokyny k importu certifikátu a jejich nahrání jako certifikátu serveru ve službě IIS najdete v tématu [Postup: instalace importovaných certifikátů na webový server ve Windows serveru 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server).

Pokyny k vytvoření vazby TLS najdete v tématu [jak nastavit SSL na IIS 7](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

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

Následující konfigurace je příkladem [Nginx serveru](https://nginx.org/docs/http/configuring_https_servers.html) s konfigurací TLS:

![NGINX s protokolem TLS](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Přístup k serveru pro ověření konfigurace

1. Přidejte kořenový certifikát do důvěryhodného kořenového úložiště vašeho počítače. Při přístupu k webu se ujistěte, že se v prohlížeči zobrazuje celý řetěz certifikátů.

   ![Důvěryhodné kořenové certifikáty](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Předpokládá se, že služba DNS je nakonfigurovaná tak, aby odkazovala název webového serveru (v tomto příkladu www.fabrikam.com) na IP adresu vašeho webového serveru. V takovém případě můžete upravit [soubor hostitelů](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) pro překlad názvu.
1. Přejděte na web a kliknutím na ikonu zámku v poli Adresa v prohlížeči ověřte informace o lokalitě a certifikátu.

## <a name="verify-the-configuration-with-openssl"></a>Ověření konfigurace pomocí OpenSSL

Nebo můžete certifikát ověřit pomocí OpenSSL.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![Ověření certifikátu OpenSSL](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Nahrajte kořenový certifikát do nastavení protokolu HTTP Application Gateway.

Pokud chcete nahrát certifikát v Application Gateway, musíte exportovat certifikát. CRT do formátu. cer Base-64 Encoded. Vzhledem k tomu, že. CRT již obsahuje veřejný klíč ve formátu kódování Base-64, stačí přejmenovat příponu souboru z. CRT na. cer. 

### <a name="azure-portal"></a>portál Azure

Důvěryhodný kořenový certifikát nahrajte z portálu tak, že vyberete **Nastavení http** a zvolíte protokol **https** .

![Přidání certifikátu pomocí portálu](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Nebo můžete k nahrání kořenového certifikátu použít Azure CLI nebo Azure PowerShell. Následující kód je Azure PowerShell ukázka.

> [!NOTE]
> Následující příklad přidá důvěryhodný kořenový certifikát do aplikační brány, vytvoří nové nastavení HTTP a přidá nové pravidlo, za předpokladu, že již existuje fond back-end a naslouchací proces.

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
## will be used to verify the backend server's certificate. Note that TLS handshake will
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

### <a name="verify-the-application-gateway-backend-health"></a>Ověření stavu back-endu služby Application Gateway

1. Kliknutím na zobrazení **stavu back-endu** služby Application Gateway zkontrolujte, jestli je sonda v pořádku.
1. Měli byste vidět, že stav je v **pořádku** pro test https.

![Test HTTPS](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Další kroky

Další informace o SSL\TLS v Application Gateway najdete v tématu [Přehled ukončení protokolu TLS a koncového šifrování TLS s Application Gateway](ssl-overview.md).

