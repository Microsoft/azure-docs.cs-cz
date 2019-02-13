---
title: Vytvoření vazby existujícího vlastního certifikátu SSL – Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak ve službě Azure App Service vytvořit vazbu vlastního certifikátu SSL k webové aplikaci, back-endu mobilní aplikace nebo aplikaci API.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: d627158744ae89e3c614d10a64886cee6879a3fb
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116045"
---
# <a name="tutorial-bind-an-existing-custom-ssl-certificate-to-azure-app-service"></a>Kurz: Vytvoření vazby existujícího vlastního certifikátu SSL do služby Azure App Service

Azure App Service poskytuje je vysoce škálovatelnou a automatickými opravami pro hostování webů služby. V tomto kurzu se dozvíte, jak vytvořit vazbu vlastního certifikátu SSL, který jste si koupili od důvěryhodné certifikační autority do [služby Azure App Service](overview.md). Jakmile budete hotovi, budete mít přístup k aplikaci přes koncový bod HTTPS vlastní domény DNS.

![Webová aplikace s vlastním certifikátem SSL](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Upgrade cenové úrovně aplikace
> * Vytvoření vazby vlastního certifikátu ke službě App Service
> * Obnovení certifikátů
> * Vynucení HTTPS
> * Vynucení protokolu TLS 1.1/1.2
> * Automatizace správy protokolu TLS pomocí skriptů

> [!NOTE]
> Pokud je potřeba získat vlastního certifikátu SSL, můžete získat na webu Azure Portal přímo a vázat na vaši aplikaci. Postupujte podle [kurzu Certifikáty App Service](web-sites-purchase-ssl-web-site.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- [Vytvořit aplikaci App Service](/azure/app-service/)
- [Mapování vlastního názvu DNS do aplikace služby App Service](app-service-web-tutorial-custom-domain.md)
- Získat certifikát SSL od důvěryhodné certifikační autority
- Privátní klíč, který jste použili k podepsání žádosti o certifikát SSL

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Požadavky na certifikát SSL

Pokud chcete ve službě App Service použít certifikát, musí splňovat všechny následující požadavky:

* Podepsaný důvěryhodnou certifikační autoritou
* Exportovaný jako soubor PFX chráněný heslem
* Obsahuje privátní klíč dlouhý alespoň 2 048 bitů
* Obsahuje v řetězu certifikátů všechny zprostředkující certifikáty

> [!NOTE]
> **Certifikáty ECC (elliptic curve cryptography)** můžou fungovat se službou App Service, ale tento článek se jimi nezabývá. Konkrétní pokyny k vytvoření certifikátů ECC vám sdělí vaše certifikační autorita.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Vytvoření vazby certifikátu SSL

Jste připraveni nahrát svůj certifikát SSL do vaší aplikace.

### <a name="merge-intermediate-certificates"></a>Sloučení zprostředkujících certifikátů

Pokud jste v řetězu certifikátů od své certifikační autority obdrželi více certifikátů, musíte certifikáty sloučit v daném pořadí. 

Pokud to chcete provést, otevřete všechny obdržené certifikáty v textovém editoru. 

Vytvořte soubor _mergedcertificate.crt_ pro sloučený certifikát. V textovém editoru zkopírujte do tohoto souboru obsah jednotlivých certifikátů. Pořadí certifikátů by mělo odpovídat jejich pořadí v řetězu certifikátů, počínaje vaším certifikátem a konče kořenovým certifikátem. Soubor bude vypadat jako v následujícím příkladu:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Export certifikátu do formátu PFX

Exportujte sloučený certifikát SSL s privátním klíčem, se kterým se vygenerovala vaše žádost o certifikát.

Pokud jste žádost o certifikát vygenerovali pomocí OpenSSL, vytvořili jste i soubor privátního klíče. Exportujte certifikát do formátu PFX spuštěním následujícího příkazu. Nahraďte zástupný text _&lt;private-key-file>_ cestou k souboru vašeho privátního klíče a _&lt;merged-certificate-file>_ cestou k souboru vašeho sloučeného certifikátu.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Po zobrazení výzvy definujte heslo pro export. Toto heslo použijete později při nahrávání certifikátu SSL do služby App Service.

Pokud jste k vygenerování žádosti o certifikát použili službu IIS nebo nástroj _Certreq.exe_, nainstalujte certifikát na místním počítači a pak [exportujte certifikát do formátu PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-your-ssl-certificate"></a>Nahrání certifikátu SSL

Pokud chcete nahrát svůj certifikát SSL, klikněte na tlačítko **nastavení SSL** v levém navigačním panelu vaší aplikace.

Klikněte na **Nahrát certifikát**. 

V části **Soubor certifikátu PFX** vyberte svůj soubor PFX. Do pole **Heslo certifikátu** zadejte heslo, které jste vytvořili při exportování souboru PFX.

Klikněte na **Odeslat**.

![Nahrání certifikátu](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

Jakmile App Service dokončí nahrávání certifikátu, zobrazí se certifikát na stránce **Nastavení SSL**.

![Nahraný certifikát](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Vytvoření vazby certifikátu SSL

V části **Vazby SSL** klikněte na **Přidat vazbu**.

Na stránce **Přidat vazbu SSL** pomocí rozevíracích seznamů vyberte název domény, kterou chcete zabezpečit, a certifikát, který chcete použít.

> [!NOTE]
> Pokud jste nahráli certifikát, ale v rozevíracím seznamu **Název hostitele** se nezobrazí názvy domén, zkuste aktualizovat stránku v prohlížeči.
>
>

V části **Typ SSL** vyberte, jestli se má použít SSL na základě **[Indikace názvu serveru (SNI)](https://en.wikipedia.org/wiki/Server_Name_Indication)** nebo IP adresy.

- **SSL na základě SNI** – Můžete přidat několik vazeb SSL na základě SNI. Tato možnost umožňuje zabezpečení několika domén na stejné IP adrese pomocí několika certifikátů SSL. Většina moderních prohlížečů (včetně prohlížečů Internet Explorer, Chrome, Firefox a Opera) podporuje SNI (ucelenější informace o podpoře prohlížečů najdete v článku o [Indikaci názvu serveru](https://wikipedia.org/wiki/Server_Name_Indication)).
- **SSL na základě IP adresy** – Můžete přidat pouze jednu vazbu SSL na základě IP adresy. Tato možnost umožňuje zabezpečení vyhrazené veřejné IP adresy pouze jedním certifikátem SSL. Pokud chcete zabezpečit více domén, musíte je všechny zabezpečit pomocí stejného certifikátu. Toto je tradiční možnost vytvoření vazby SSL.

Klikněte na **Přidat vazbu**.

![Vytvoření vazby certifikátu SSL](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

Jakmile App Service dokončí nahrávání certifikátu, zobrazí se certifikát v části **Vazby SSL**.

![Certifikát svázaný s webovou aplikací](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Přemapování záznamu A pro IP SSL

Pokud nepoužíváte SSL na základě IP adresy ve vaší aplikaci, přejděte k [Test HTTPS pro vlastní doménu](#test).

Ve výchozím nastavení vaše aplikace používá sdílenou veřejnou IP adresu. Když vytvoříte vazbu certifikátu SSL na základě IP adresy, App Service vytvoří novou vyhrazenou IP adresu pro vaši aplikaci.

Pokud jste namapovali záznam A do vaší aplikace, aktualizujte registr domény s tuto novou vyhrazenou IP adresu.

Vaše aplikace **vlastní domény** stránka je aktualizována novou vyhrazenou IP adresu. [Zkopírujte tuto IP adresu](app-service-web-tutorial-custom-domain.md#info) a pak [přemapujte záznam A](app-service-web-tutorial-custom-domain.md#map-an-a-record) na tuto novou IP adresu.

<a name="test"></a>

## <a name="test-https"></a>Test HTTPS

Teď už zbývá jen ověřit, že HTTPS na vaší vlastní doméně funguje. V různých prohlížečích přejděte do `https://<your.custom.domain>` k zkontrolujte, že se do vaší aplikace.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Pokud má vaše aplikace zobrazí chyby ověření certifikátu, pravděpodobně používáte certifikát podepsaný svým držitelem.
>
> Pokud to není váš případ, možná jste při exportování certifikátu do souboru PFX vynechali zprostředkující certifikáty.

<a name="bkmk_enforce"></a>

## <a name="renew-certificates"></a>Obnovení certifikátů

Vaše příchozí IP adresa se může změnit při odstranění vazby, a to i v případě, že tato vazba je založená na protokolu IP. To je zvlášť důležité při obnovení certifikátu, který už vazbu založenou na protokolu IP má. Pokud chcete zabránit změně IP adresy vaší aplikace IP, postupujte podle těchto kroků v uvedeném pořadí:

1. Nahrajte nový certifikát.
2. Vytvořte vazbu nového certifikátu k požadované vlastní doméně. Starý certifikát neodstraňujte. Tato akce nahradí tuto vazbu (místo aby odebrala vazbu původní).
3. Odstraňte starý certifikát. 

## <a name="enforce-https"></a>Vynucení HTTPS

Ve výchozím nastavení každý uživatel dál přístup k aplikaci pomocí protokolu HTTP. Všechny požadavky HTTP můžete přesměrovat na port HTTPS.

Na stránce vaší aplikace v levém navigačním panelu vyberte **nastavení SSL**. Pak v části **Pouze HTTPS** vyberte **Zapnuto**.

![Vynucení HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Po dokončení operace přejděte na jakoukoli adresu URL HTTP odkazující na vaši aplikaci. Příklad:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Vynucení verzí protokolu TLS

Vaše aplikace ve výchozím nastavení povoluje protokol [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2, což je doporučená úroveň protokolu TLS z hlediska oborových standardů, například [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Pokud chcete vynucovat jiné verze protokolu TLS, postupujte následovně:

Na stránce vaší aplikace v levém navigačním panelu vyberte **nastavení SSL**. Pak v části **Verze protokolu TLS** vyberte minimální požadovanou verzi protokolu TLS. Toto nastavení řídí pouze příchozí volání. 

![Vynucení protokolu TLS 1.1 nebo 1.2](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

Po dokončení operace bude vaše aplikace odmítat všechna připojení využívající nižší verze protokolu TLS.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Můžete automatizovat vazby SSL pro vaši aplikaci pomocí skriptů s využitím [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) nebo [prostředí Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

Následující příkaz nahraje exportovaný soubor PFX a získá jeho kryptografický otisk.

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

Následující příkaz s použitím kryptografického otisku z předchozího příkazu přidá vazbu SSL na základě SNI.

```bash
az webapp config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

Následující příkaz vynutí minimální verzi protokolu TLS 1.2.

```bash
az webapp config set \
    --name <app_name> \
    --resource-group <resource_group_name>
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující příkaz nahraje exportovaný soubor PFX a přidá vazbu SSL na základě SNI.

```PowerShell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```
## <a name="public-certificates-optional"></a>Veřejné certifikáty (volitelné)
Pokud vaše aplikace potřebuje pro přístup ke vzdáleným prostředkům jako klienta a vzdáleného prostředku vyžaduje ověřování pomocí certifikátu, můžete nahrát [veřejné certifikáty](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/) do vaší aplikace. Veřejné certifikáty nejsou požadována pro vazby SSL vaší aplikace.

Další informace o načtení a použití veřejného certifikátu v aplikaci najdete v tématu věnovaném [použití certifikátu SSL v kódu aplikace ve službě Azure App Service](app-service-web-ssl-cert-load.md). Veřejné certifikáty můžete použít příliš s aplikacemi ve službě App Service Environment. Pokud potřebujete uloží certifikát do úložiště LocalMachine certifikátů, musíte použít aplikaci ve službě App Service Environment. Další informace najdete v tématu [konfigurace veřejných certifikátů pro vaše aplikace app Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer).

![Nahrání veřejného certifikátu](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Upgrade cenové úrovně aplikace
> * Vytvoření vazby vlastního certifikátu ke službě App Service
> * Obnovení certifikátů
> * Vynucení HTTPS
> * Vynucení protokolu TLS 1.1/1.2
> * Automatizace správy protokolu TLS pomocí skriptů

V dalším kurzu se dozvíte, jak používat službu Azure Content Delivery Network.

> [!div class="nextstepaction"]
> [Přidání sítě Content Delivery Network (CDN) do služby Azure App Service](../cdn/cdn-add-to-web-app.md)

Další informace najdete v tématu [Použití certifikátu SSL v kódu aplikace ve službě Azure App Service](app-service-web-ssl-cert-load.md).
