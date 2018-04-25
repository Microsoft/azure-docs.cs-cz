---
title: Vytvoření vazby existujícího vlastního certifikátu SSL k Azure Web Apps | Microsoft Docs
description: Zjistěte, jak vytvořit vazbu vlastního certifikátu SSL k webové aplikaci, back-endu mobilní aplikace nebo aplikaci API ve službě Azure App Service.
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
ms.date: 11/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 5a6fd54e4d20e55116bc0fa771e039e5ea2bb30b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>Kurz: Vytvoření vazby existujícího vlastního certifikátu SSL k Azure Web Apps

Azure Web Apps je vysoce škálovatelná služba s automatickými opravami pro hostování webů. V tomto kurzu se dozvíte, jak vytvořit vazbu vlastního certifikátu SSL, který jste zakoupili od důvěryhodné certifikační autority, k [Azure Web Apps](app-service-web-overview.md). Jakmile budete hotovi, budete ke své webové aplikaci mít přístup přes koncový bod HTTPS vaší vlastní domény DNS.

![Webová aplikace s vlastním certifikátem SSL](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Upgrade cenové úrovně aplikace
> * Vytvoření vazby vlastního certifikátu SSL ke službě App Service
> * Vynucování HTTPS pro aplikaci
> * Automatizace vytváření vazeb certifikátů SSL pomocí skriptů

> [!NOTE]
> V případě potřeby můžete vlastní certifikát SSL získat přímo na webu Azure Portal a vytvořit jeho vazbu ke své webové aplikaci. Postupujte podle [kurzu Certifikáty App Service](web-sites-purchase-ssl-web-site.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- [Vytvořit aplikaci App Service](/azure/app-service/)
- [Namapovat na svou webovou aplikaci vlastní název DNS](app-service-web-tutorial-custom-domain.md)
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

## <a name="prepare-your-web-app"></a>Příprava webové aplikace

Pokud chcete vytvořit vazbu vlastního certifikátu SSL ke své webové aplikaci, váš [plán služby App Service](https://azure.microsoft.com/pricing/details/app-service/) musí být na úrovni **Basic**, **Standard** nebo **Premium**. V tomto kroku se ujistíte, že je vaše webová aplikace na podporované cenové úrovni.

### <a name="log-in-to-azure"></a>Přihlášení k Azure

Otevřete web [Azure Portal](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Přechod do webové aplikace

V levé nabídce klikněte na **App Services** a pak klikněte na název vaší webové aplikace.

![Výběr webové aplikace](./media/app-service-web-tutorial-custom-ssl/select-app.png)

Byli jste přesměrování na stránku pro správu vaší webové aplikace.  

### <a name="check-the-pricing-tier"></a>Kontrola cenové úrovně

V levém navigačním panelu na stránce webové aplikace se posuňte do části **Nastavení** a vyberte **Vertikálně navýšit kapacitu (plán služby App Service)**.

![Nabídka Vertikálně navýšit kapacitu](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Zkontrolujte, že vaše webová aplikace není na úrovni **Free** nebo **Standard**. Aktuální úroveň webové aplikace je zvýrazněná tmavě modrým rámečkem.

![Kontrola cenové úrovně](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

Na úrovni **Free** a **Standard** se nepodporuje vlastní SSL. Pokud potřebujete vertikálně navýšit kapacitu, postupujte podle kroků v další části. Jinak zavřete stránku **Zvolte cenovou úroveň** a přeskočte k části [Nahrání a vytvoření vazby certifikátu SSL](#upload).

### <a name="scale-up-your-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

Vyberte některou z úrovní **Basic**, **Standard** nebo **Premium**.

Klikněte na **Vybrat**.

![Výběr cenové úrovně](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

Až se zobrazí následující oznámení, operace škálování je dokončená.

![Oznámení vertikálního navýšení kapacity](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Vytvoření vazby certifikátu SSL

Jste připraveni nahrát svůj certifikát SSL do webové aplikace.

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

Pokud chcete nahrát svůj certifikát SSL, v levém navigačním panelu vaší webové aplikace klikněte na **Nastavení SSL**.

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

V části **Typ SSL** vyberte, jestli se má použít SSL na základě **[Indikace názvu serveru (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** nebo IP adresy.

- **SSL na základě SNI** – Můžete přidat několik vazeb SSL na základě SNI. Tato možnost umožňuje zabezpečení několika domén na stejné IP adrese pomocí několika certifikátů SSL. Většina moderních prohlížečů (včetně prohlížečů Internet Explorer, Chrome, Firefox a Opera) podporuje SNI (ucelenější informace o podpoře prohlížečů najdete v článku o [Indikaci názvu serveru](http://wikipedia.org/wiki/Server_Name_Indication)).
- **SSL na základě IP adresy** – Můžete přidat pouze jednu vazbu SSL na základě IP adresy. Tato možnost umožňuje zabezpečení vyhrazené veřejné IP adresy pouze jedním certifikátem SSL. Pokud chcete zabezpečit více domén, musíte je všechny zabezpečit pomocí stejného certifikátu. Toto je tradiční možnost vytvoření vazby SSL.

Klikněte na **Přidat vazbu**.

![Vytvoření vazby certifikátu SSL](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

Jakmile App Service dokončí nahrávání certifikátu, zobrazí se certifikát v části **Vazby SSL**.

![Certifikát svázaný s webovou aplikací](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Přemapování záznamu A pro IP SSL

Pokud ve své webové aplikaci nepoužíváte SSL na základě IP adresy, přeskočte k části [Test HTTPS pro vlastní doménu](#test).

Ve výchozím nastavení vaše webová aplikace používá sdílenou veřejnou IP adresu. Pokud vytvoříte vazbu certifikátu s využitím SSL na základě IP adresy, App Service pro vaši webovou aplikaci vytvoří novou vyhrazenou IP adresu.

Pokud jste na svou webovou aplikaci namapovali záznam A, aktualizujte registr domény s použitím této nové vyhrazené IP adresy.

Stránka **Vlastní doména** vaší webové aplikace se aktualizuje a zobrazí novou vyhrazenou IP adresu. [Zkopírujte tuto IP adresu](app-service-web-tutorial-custom-domain.md#info) a pak [přemapujte záznam A](app-service-web-tutorial-custom-domain.md#map-an-a-record) na tuto novou IP adresu.

<a name="test"></a>

## <a name="test-https"></a>Test HTTPS

Teď už zbývá jen ověřit, že HTTPS na vaší vlastní doméně funguje. V různých prohlížečích přejděte na adresu `https://<your.custom.domain>` a zkontrolujte, že se zobrazí vaše webová aplikace.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Pokud webová aplikace zobrazí chyby ověření certifikátu, pravděpodobně používáte certifikát podepsaný svým držitelem.
>
> Pokud to není váš případ, možná jste při exportování certifikátu do souboru PFX vynechali zprostředkující certifikáty.

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Vynucení HTTPS

Ve výchozím nastavení mají všichni stále přístup k vaší webové aplikaci pomocí HTTP. Všechny požadavky HTTP můžete přesměrovat na port HTTPS.

V levém navigačním panelu na stránce vaší webové aplikace vyberte **Nastavení SSL**. Pak v části **Pouze HTTPS** vyberte **Zapnuto**.

![Vynucení HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Po dokončení operace přejděte na jakoukoli adresu URL HTTP odkazující na vaši aplikaci. Příklad:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-1112"></a>Vynucení protokolu TLS 1.1/1.2

Vaše aplikace ve výchozím nastavení povoluje protokol [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0, který už se z hlediska oborových standardů, například [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard), nepovažuje za bezpečný. Pokud chcete vynucovat vyšší verze protokolu TLS, postupujte následovně:

V levém navigačním panelu na stránce vaší webové aplikace vyberte **Nastavení SSL**. Pak v části **Verze protokolu TLS** vyberte minimální požadovanou verzi protokolu TLS.

![Vynucení HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

Po dokončení operace bude vaše aplikace odmítat všechna připojení využívající nižší verze protokolu TLS.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Vytváření vazeb SSL pro webovou aplikaci můžete automatizovat pomocí skriptů s využitím [Azure CLI](/cli/azure/install-azure-cli) nebo [Azure PowerShellu](/powershell/azure/overview).

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

### <a name="azure-powershell"></a>Azure PowerShell

Následující příkaz nahraje exportovaný soubor PFX a přidá vazbu SSL na základě SNI.

```PowerShell
New-AzureRmWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```
## <a name="public-certificates-optional"></a>Veřejné certifikáty (volitelné)
Do své webové aplikace můžete nahrát [veřejné certifikáty](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/). Veřejné certifikáty můžete použít také pro aplikace ve službě App Service Environments. Pokud potřebujete certifikát uložit do úložiště certifikátů na místním počítači, musíte použít webovou aplikaci ve službě App Service Environment. Další informace najdete v tématu [Konfigurace veřejných certifikátů pro webovou aplikaci](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer).

![Nahrání veřejného certifikátu](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Upgrade cenové úrovně aplikace
> * Vytvoření vazby vlastního certifikátu SSL ke službě App Service
> * Vynucování HTTPS pro aplikaci
> * Automatizace vytváření vazeb certifikátů SSL pomocí skriptů

V dalším kurzu se dozvíte, jak používat službu Azure Content Delivery Network.

> [!div class="nextstepaction"]
> [Přidání sítě Content Delivery Network (CDN) do služby Azure App Service](app-service-web-tutorial-content-delivery-network.md)

Další informace najdete v tématu [Použití certifikátu SSL v kódu aplikace ve službě Azure App Service](app-service-web-ssl-cert-load.md).