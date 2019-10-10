---
title: Nahrání a vytvoření vazby certifikátu SSL-Azure App Service | Microsoft Docs
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
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c5095bc8c274ef0985b00459b0d088371ab24d88
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177045"
---
# <a name="tutorial-upload-and-bind-ssl-certificates-to-azure-app-service"></a>Kurz: nahrávání a vázání certifikátů SSL pro Azure App Service

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. V tomto kurzu se dozvíte, jak zabezpečit vlastní doménu v App Service s certifikátem zakoupeným od důvěryhodné certifikační autority. Také se dozvíte, jak nahrát všechny soukromé a veřejné certifikáty, které vaše aplikace potřebuje. Až budete hotovi, budete mít přístup k aplikaci na koncovém bodu HTTPS vaší vlastní domény DNS.

![Webová aplikace s vlastním certifikátem SSL](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Upgrade cenové úrovně aplikace
> * Zabezpečení vlastní domény pomocí certifikátu
> * Nahrání privátního certifikátu
> * Nahrajte veřejný certifikát.
> * Obnovení certifikátů
> * Vynucení HTTPS
> * Vynucení protokolu TLS 1.1/1.2
> * Automatizace správy protokolu TLS pomocí skriptů

## <a name="prerequisites"></a>Předpoklady

K provedení kroků v tomto kurzu je potřeba:

- [Vytvořit aplikaci App Service](/azure/app-service/)
- [Mapování vlastního názvu DNS na aplikaci App Service](app-service-web-tutorial-custom-domain.md) (při zabezpečení vlastní domény)
- Získat certifikát od důvěryhodné certifikační autority
- Máte privátní klíč, který jste použili k podepsání žádosti o certifikát (u privátních certifikátů).

<a name="requirements"></a>

## <a name="prepare-a-private-certificate"></a>Příprava privátního certifikátu

Pro zabezpečení domény musí certifikát splňovat následující požadavky:

* Nakonfigurováno pro ověřování serveru
* Podepsaný důvěryhodnou certifikační autoritou
* Exportovaný jako soubor PFX chráněný heslem
* Obsahuje privátní klíč dlouhý alespoň 2 048 bitů
* Obsahuje v řetězu certifikátů všechny zprostředkující certifikáty

> [!TIP]
> Pokud potřebujete získat vlastní certifikát SSL, můžete ho získat přímo v Azure Portal a naimportovat ho do vaší aplikace. Postupujte podle [kurzu Certifikáty App Service](web-sites-purchase-ssl-web-site.md).

> [!NOTE]
> **Certifikáty ECC (elliptic curve cryptography)** můžou fungovat se službou App Service, ale tento článek se jimi nezabývá. Konkrétní pokyny k vytvoření certifikátů ECC vám sdělí vaše certifikační autorita.

Jakmile obdržíte certifikát od poskytovatele certifikátů, postupujte podle kroků v této části, aby byl připravený na App Service.

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

Nyní jste si certifikát nahráli do App Service.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Zabezpečení vlastní domény

> [!TIP]
> Pokud potřebujete získat vlastní certifikát SSL, můžete ho získat přímo v Azure Portal a vytvořit jeho propojení s vaší aplikací. Postupujte podle [kurzu Certifikáty App Service](web-sites-purchase-ssl-web-site.md).

Pokud chcete zabezpečit [vlastní doménu](app-service-web-tutorial-custom-domain.md) s certifikátem třetí strany, nahrajete [připravený privátní certifikát](#prepare-a-private-certificate) a potom ho svážete s vlastní doménou, ale App Service tento proces zjednodušuje. Proveďte následující kroky:

V levém navigačním panelu aplikace klikněte na **vlastní domény** a pak klikněte na **Přidat vazbu** pro doménu, kterou chcete zabezpečit. Pokud nevidíte **Přidat vazbu** pro doménu, pak je už zabezpečená a měla by mít **zabezpečený** stav SSL.

![Přidat vazbu k doméně](./media/app-service-web-tutorial-custom-ssl/secure-domain-launch.png)

Klikněte na **Nahrát certifikát**.

V části **Soubor certifikátu PFX** vyberte svůj soubor PFX. Do pole **Heslo certifikátu** zadejte heslo, které jste vytvořili při exportování souboru PFX.

Klikněte na **Odeslat**.

![Nahrát certifikát pro doménu](./media/app-service-web-tutorial-custom-ssl/secure-domain-upload.png)

Počkejte, až Azure nahraje váš certifikát a spustí dialogové okno vazby SSL.

V dialogovém okně vazby SSL vyberte certifikát, který jste nahráli, a typ SSL a pak klikněte na **Přidat vazbu**.

> [!NOTE]
> Podporují se tyto typy SSL:
>
> - **[SSL založené na sni](https://en.wikipedia.org/wiki/Server_Name_Indication)** – můžete přidat několik vazeb SSL založených na sni. Tato možnost umožňuje zabezpečení několika domén na stejné IP adrese pomocí několika certifikátů SSL. Většina moderních prohlížečů (včetně prohlížečů Internet Explorer, Chrome, Firefox a Opera) podporuje SNI (ucelenější informace o podpoře prohlížečů najdete v článku o [Indikaci názvu serveru](https://wikipedia.org/wiki/Server_Name_Indication)).
> - **SSL na základě IP adresy** – Můžete přidat pouze jednu vazbu SSL na základě IP adresy. Tato možnost umožňuje zabezpečení vyhrazené veřejné IP adresy pouze jedním certifikátem SSL. Pokud chcete zabezpečit více domén, musíte je všechny zabezpečit pomocí stejného certifikátu. Toto je tradiční možnost vytvoření vazby SSL.

![Vytvoření vazby SSL k doméně](./media/app-service-web-tutorial-custom-ssl/secure-domain-bind.png)

Stav protokolu SSL domény by se teď měl změnit na **zabezpečený**.

![Zabezpečená doména](./media/app-service-web-tutorial-custom-ssl/secure-domain-finished.png)

> [!NOTE]
> **Zabezpečený** stav ve **vlastních doménách** znamená, že je zabezpečený certifikátem, ale App Service nekontroluje, jestli je certifikát podepsaný svým držitelem nebo pokud vypršela jeho platnost, což může taky způsobit, že se v prohlížečích zobrazí chyba nebo upozornění.

## <a name="remap-a-record-for-ip-ssl"></a>Přemapování záznamu A pro IP SSL

Pokud v aplikaci nepoužíváte protokol SSL založený na protokolu IP, přejděte k [testu HTTPS pro vaši vlastní doménu](#test).

Ve výchozím nastavení vaše aplikace používá sdílenou veřejnou IP adresu. Když svážete certifikát s protokolem SSL na základě IP adresy, App Service pro vaši aplikaci vytvoří novou vyhrazenou IP adresu.

Pokud jste namapovali záznam A na svou aplikaci, aktualizujte svůj registr domény pomocí této nové vyhrazené IP adresy.

Stránka **vlastní doména** vaší aplikace se aktualizuje o novou vyhrazenou IP adresu. [Zkopírujte tuto IP adresu](app-service-web-tutorial-custom-domain.md#info) a pak [přemapujte záznam A](app-service-web-tutorial-custom-domain.md#map-an-a-record) na tuto novou IP adresu.

<a name="test"></a>

## <a name="test-https"></a>Test HTTPS

Teď už zbývá jen ověřit, že HTTPS na vaší vlastní doméně funguje. V různých prohlížečích přejděte na `https://<your.custom.domain>` a podívejte se, že aplikace funguje.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Pokud vaše aplikace poskytuje chyby ověřování certifikátů, pravděpodobně používáte certifikát podepsaný svým držitelem.
>
> Pokud to není váš případ, možná jste při exportování certifikátu do souboru PFX vynechali zprostředkující certifikáty.

## <a name="renew-certificates"></a>Obnovení certifikátů

Vaše příchozí IP adresa se může změnit při odstranění vazby, a to i v případě, že tato vazba je založená na protokolu IP. To je zvlášť důležité při obnovení certifikátu, který už vazbu založenou na protokolu IP má. Pokud chcete zabránit změně IP adresy vaší aplikace IP, postupujte podle těchto kroků v uvedeném pořadí:

1. Nahrajte nový certifikát.
2. Vytvořte vazbu nového certifikátu k požadované vlastní doméně. Starý certifikát neodstraňujte. Tato akce nahradí tuto vazbu (místo aby odebrala vazbu původní).
3. Odstraňte starý certifikát. 

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Vynucení HTTPS

Ve výchozím nastavení může kdokoli k vaší aplikaci přistupovat pomocí protokolu HTTP. Všechny požadavky HTTP můžete přesměrovat na port HTTPS.

V levém navigačním panelu na stránce aplikace vyberte **Nastavení SSL**. Pak v části **Pouze HTTPS** vyberte **Zapnuto**.

![Vynucení HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Po dokončení operace přejděte na jakoukoli adresu URL HTTP odkazující na vaši aplikaci. Například:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Vynucení verzí protokolu TLS

Vaše aplikace ve výchozím nastavení povoluje protokol [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2, což je doporučená úroveň protokolu TLS z hlediska oborových standardů, například [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Pokud chcete vynucovat jiné verze protokolu TLS, postupujte následovně:

V levém navigačním panelu na stránce aplikace vyberte **Nastavení SSL**. Pak v části **Verze protokolu TLS** vyberte minimální požadovanou verzi protokolu TLS. Toto nastavení řídí pouze příchozí volání. 

![Vynucení protokolu TLS 1.1 nebo 1.2](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

Po dokončení operace bude vaše aplikace odmítat všechna připojení využívající nižší verze protokolu TLS.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

K automatizaci vazeb SSL pro vaši aplikaci můžete použít skripty pomocí [Azure CLI](/cli/azure/install-azure-cli) nebo [Azure PowerShell](/powershell/azure/overview).

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
    --resource-group <resource-group-name> \
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

Následující příkaz vynutí, aby aplikace používala protokol HTTPS.

```azurecli-interactive
az webapp update \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --https-only true
```

Následující příkaz vynutí minimální verzi protokolu TLS 1.2.

```azurecli-interactive
az webapp config set \
    --name <app-name> \
    --resource-group <resource-group-name> \
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

## <a name="use-certificates-in-your-code"></a>Použití certifikátů v kódu

Pokud se vaše aplikace potřebuje připojit ke vzdáleným prostředkům a vzdálený prostředek vyžaduje ověření certifikátu, můžete do aplikace nahrát veřejné nebo privátní certifikáty. Tyto certifikáty nemusíte Přivážete k žádné vlastní doméně ve vaší aplikaci. Další informace najdete v tématu [Použití certifikátu SSL v kódu aplikace ve službě Azure App Service](app-service-web-ssl-cert-load.md).

## <a name="next-steps"></a>Další kroky

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
