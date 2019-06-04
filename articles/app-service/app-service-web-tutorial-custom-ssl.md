---
title: Nahrát a svázat certifikát SSL – Azure App Service | Dokumentace Microsoftu
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
ms.openlocfilehash: e0ee1e7c652ddf4126fc9658bf17d3e919d7a5c8
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475347"
---
# <a name="tutorial-upload-and-bind-ssl-certificates-to-azure-app-service"></a>Kurz: Nahrání a vytvoření vazby certifikátů SSL do služby Azure App Service

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. V tomto kurzu se dozvíte, jak zabezpečit vlastní domény ve službě App Service s certifikátem, který jste si koupili od důvěryhodné certifikační autority. Je také ukazuje, jak nahrát libovolný privátní a veřejné certifikáty požadavkům vašich aplikací. Jakmile budete hotovi, budete mít přístup k aplikaci přes koncový bod HTTPS vlastní domény DNS.

![Webová aplikace s vlastním certifikátem SSL](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Upgrade cenové úrovně aplikace
> * Zabezpečte vlastní doménu s certifikátem
> * Odeslání privátního certifikátu
> * Odeslat veřejný certifikát
> * Obnovení certifikátů
> * Vynucení HTTPS
> * Vynucení protokolu TLS 1.1/1.2
> * Automatizace správy protokolu TLS pomocí skriptů

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- [Vytvořit aplikaci App Service](/azure/app-service/)
- [Mapování vlastního názvu DNS do aplikace služby App Service](app-service-web-tutorial-custom-domain.md) (je-li zabezpečení vlastní domény)
- Získat certifikát od důvěryhodné certifikační autority
- Privátní klíč, který jste použili k podepsání žádosti o certifikát (pro privátní certifikáty)

<a name="requirements"></a>

## <a name="prepare-a-private-certificate"></a>Příprava privátního certifikátu

K zabezpečení domény, certifikát musí splňovat následující požadavky:

* Nakonfigurováno pro ověřování serveru
* Podepsaný důvěryhodnou certifikační autoritou
* Exportovaný jako soubor PFX chráněný heslem
* Obsahuje privátní klíč dlouhý alespoň 2 048 bitů
* Obsahuje v řetězu certifikátů všechny zprostředkující certifikáty

> [!TIP]
> Pokud je potřeba získat vlastního certifikátu SSL, můžete získat na webu Azure Portal přímo a importujte ho do své aplikace. Postupujte podle [kurzu Certifikáty App Service](web-sites-purchase-ssl-web-site.md).

> [!NOTE]
> **Certifikáty ECC (elliptic curve cryptography)** můžou fungovat se službou App Service, ale tento článek se jimi nezabývá. Konkrétní pokyny k vytvoření certifikátů ECC vám sdělí vaše certifikační autorita.

Po získání certifikátu od poskytovatele certifikát, postupujte podle kroků v této části, aby byla připravena pro službu App Service.

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

Jste nyní připraveno nahrajte certifikát do služby App Service.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Zabezpečte vlastní doménu

> [!TIP]
> Pokud je potřeba získat vlastního certifikátu SSL, můžete získat na webu Azure Portal přímo a vázat na vaši aplikaci. Postupujte podle [kurzu Certifikáty App Service](web-sites-purchase-ssl-web-site.md).

K zabezpečení [vlastní doménu](app-service-web-tutorial-custom-domain.md) certifikátem třetích stran, nahrajete [připravili privátního certifikátu](#prepare-a-private-certificate) a pak vytvořit vazbu k vlastní doméně, ale služby App Service zjednodušuje proces za vás. Proveďte následující kroky:

Klikněte na tlačítko **vlastní domény** v levém navigačním panelu vaší aplikace, pak klikněte na tlačítko **přidat vazbu** pro doménu, kterou chcete zabezpečit. Pokud nevidíte **přidat vazbu** pro doménu, pak je již zabezpečená a musí mít **Secure** stav protokolu SSL.

![Přidat vazbu k doméně](./media/app-service-web-tutorial-custom-ssl/secure-domain-launch.png)

Klikněte na **Nahrát certifikát**.

V části **Soubor certifikátu PFX** vyberte svůj soubor PFX. Do pole **Heslo certifikátu** zadejte heslo, které jste vytvořili při exportování souboru PFX.

Klikněte na **Odeslat**.

![Nahrát certifikát pro doménu](./media/app-service-web-tutorial-custom-ssl/secure-domain-upload.png)

Počkejte, Azure, nahrát svůj certifikát a spusťte dialogové okno vazby SSL.

V dialogovém okně vazby SSL, vyberte certifikát, který jste nahráli a typ SSL a pak klikněte na tlačítko **přidat vazbu**.

> [!NOTE]
> Jsou podporovány následující typy SSL:
>
> - **[Na základě SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)**  -lze přidat více SSL typu sni vazby. Tato možnost umožňuje zabezpečení několika domén na stejné IP adrese pomocí několika certifikátů SSL. Většina moderních prohlížečů (včetně prohlížečů Internet Explorer, Chrome, Firefox a Opera) podporuje SNI (ucelenější informace o podpoře prohlížečů najdete v článku o [Indikaci názvu serveru](https://wikipedia.org/wiki/Server_Name_Indication)).
> - **SSL na základě IP adresy** – Můžete přidat pouze jednu vazbu SSL na základě IP adresy. Tato možnost umožňuje zabezpečení vyhrazené veřejné IP adresy pouze jedním certifikátem SSL. Pokud chcete zabezpečit více domén, musíte je všechny zabezpečit pomocí stejného certifikátu. Toto je tradiční možnost vytvoření vazby SSL.

![Vytvoření vazby SSL k doméně](./media/app-service-web-tutorial-custom-ssl/secure-domain-bind.png)

Stav SSL domény by měl nyní být změněn na **Secure**.

![Domain secured](./media/app-service-web-tutorial-custom-ssl/secure-domain-finished.png)

> [!NOTE]
> A **Secure** stát **vlastní domény** prostředky, které je zabezpečený pomocí certifikátu, ale neprovádí kontrolu služby App Service, pokud je certifikát podepsaný svým držitelem nebo vypršela platnost, například, které může taky způsobovat prohlížeče Zobrazte chybu nebo upozornění.

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

## <a name="renew-certificates"></a>Obnovení certifikátů

Vaše příchozí IP adresa se může změnit při odstranění vazby, a to i v případě, že tato vazba je založená na protokolu IP. To je zvlášť důležité při obnovení certifikátu, který už vazbu založenou na protokolu IP má. Pokud chcete zabránit změně IP adresy vaší aplikace IP, postupujte podle těchto kroků v uvedeném pořadí:

1. Nahrajte nový certifikát.
2. Vytvořte vazbu nového certifikátu k požadované vlastní doméně. Starý certifikát neodstraňujte. Tato akce nahradí tuto vazbu (místo aby odebrala vazbu původní).
3. Odstraňte starý certifikát. 

<a name="bkmk_enforce"></a>

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

```azurecli-interactive
thumbprint=$(az webapp config ssl upload \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-file <path-to-PFX-file> \
    --certificate-password <PFX-password> \
    --query thumbprint \
    --output tsv)
```

Následující příkaz s použitím kryptografického otisku z předchozího příkazu přidá vazbu SSL na základě SNI.

```azurecli-interactive
az webapp config ssl bind \
    --name <app-name> \
    --resource-group <resource-group-name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

Následující příkaz vynutí minimální verzi protokolu TLS 1.2.

```azurecli-interactive
az webapp config set \
    --name <app-name> \
    --resource-group <resource-group-name>
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující příkaz nahraje exportovaný soubor PFX a přidá vazbu SSL na základě SNI.

```powershell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```

## <a name="use-certificates-in-your-code"></a>Používání certifikátů v kódu

Pokud vaše aplikace potřebuje k připojení ke vzdáleným prostředkům a vzdáleného prostředku vyžaduje ověřování pomocí certifikátu, můžete nahrát veřejný nebo privátní certifikáty do vaší aplikace. Není nutné k vytvoření vazby tyto certifikáty žádné vlastní domény ve vaší aplikaci. Další informace najdete v tématu [Použití certifikátu SSL v kódu aplikace ve službě Azure App Service](app-service-web-ssl-cert-load.md).

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
