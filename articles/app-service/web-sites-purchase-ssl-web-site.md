---
title: Koupě a konfigurace certifikátu SSL pro službu Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak zakoupit certifikát App Service a jeho vazbu na vaše aplikace app Service
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: apurvajo;cephalin
ms.openlocfilehash: 85d0c91a0b1cdf5703b394d6d232ab9cee72ee0c
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627140"
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Koupě a konfigurace certifikátu SSL pro službu Azure App Service

V tomto kurzu se dozvíte, jak zabezpečit webovou aplikaci tím, že si koupíte certifikát SSL pro vaše  **[služby Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)**, bezpečně uloží ji do [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis), a jeho přidružením k vlastní doméně.

## <a name="step-1---sign-in-to-azure"></a>Krok 1 – přihlášení v Azure

Přihlaste se k webu Azure portal na http://portal.azure.com

## <a name="step-2---place-an-ssl-certificate-order"></a>Krok 2: učinění objednávky certifikátu SSL

Objednávku certifikátu SSL můžete umístit tak, že vytvoříte nový [App Service Certificate](https://portal.azure.com/#create/Microsoft.SSL) v **webu Azure portal**.

![Vytvoření certifikátu](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Zadejte popisný **název** certifikátů pro zabezpečení SSL a zadejte **název domény**

> [!NOTE]
> Tento krok je jednou z nejdůležitějších částí procesu nákupu. Ujistěte se, že k zadání správného názvu hostitele (vlastní doména), který chcete chránit pomocí tohoto certifikátu. **Ne** předřaďte název hostitele řetězcem WWW. 
>

Vyberte vaše **předplatné**, **skupiny prostředků**, a **certifikátu SKU**

> [!TIP]
> Certifikáty služby App Service můžete použít pro Azure nebo Azure Services a není omezena pouze na App Services. Uděláte to tak, musíte vytvořit místní kopii PFX certifikátu služby App Service můžete jej můžete použít všude, kde chcete. Další informace najdete v článku [vytváření místní kopie PFX App Service Certificate](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>Krok 3: Store certifikátu ve službě Azure Key Vault

> [!NOTE]
> [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) je služba Azure, která pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami.
>

Po dokončení nákupu certifikát SSL, budete muset otevřít [služby App Service Certificate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) stránky.

![Vložit obrázek připraven k uložení v KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

Stav certifikátu je **"Čeká na vydání"** jsou několik kroků je potřeba dokončit před zahájením používání tohoto certifikátu.

Klikněte na tlačítko **konfigurace certifikátu** uvnitř vlastnosti certifikátu stránky a klikněte na **krok 1: Store** pro uložení tohoto certifikátu ve službě Azure Key Vault.

Z **Key Vault stav** klikněte na **úložiště Key Vault** zvolit existující služby Key Vault pro uložení tohoto certifikátu **nebo vytvořit novou službu Key Vault** vytvořit novou službu Key Vault ve stejném předplatném a skupině prostředků.

> [!NOTE]
> Služba Azure Key Vault má minimální poplatky za uložení tohoto certifikátu.
> Další informace najdete v tématu  **[Azure Key Vault – podrobnosti o cenách](https://azure.microsoft.com/pricing/details/key-vault/)**.
>

Po výběru trezoru klíč úložiště pro uložení tohoto certifikátu, **Store** možnost by se měla zobrazit úspěch.

![Vložit obrázek úložiště úspěch v KV](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>Krok 4 – ověření vlastnictví domény

Ze stejného **konfigurace certifikátu** stránky, které jste použili v kroku 3 klikněte na tlačítko **krok 2: ověření**.

Zvolte metodu ověření upřednostňované domény. 

Existují čtyři typy ověření domény nepodporuje certifikáty App Service: služby App Service, domény a ruční ověření. Tyto typy ověřování jsou vysvětlené v další podrobnosti najdete [Advanced části](#advanced).

> [!NOTE]
> **Ověřování App Service** je nejpohodlnější možnost, pokud chcete provést ověření domény už je namapovaný na aplikaci služby App Service ve stejném předplatném. Využívá skutečnost, že aplikace služby App Service už ověřit vlastnictví domény.
>

Klikněte na **ověřte** tlačítko pro dokončení tohoto kroku.

![Vložit obrázek ověření domény](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

Po kliknutí na tlačítko **ověřte**, použijte **aktualizovat** tlačítko až do **ověřte** možnost by se měla zobrazit úspěch.

![Vložit obrázek ověřili úspěšné dokončení v KV](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>Krok 5: přiřadit certifikát k aplikaci služby App Service

> [!NOTE]
> Před provedením kroků v této části, musí mít přiřazena vlastního názvu domény vaší aplikace. Další informace najdete v tématu  **[konfigurace vlastního názvu domény pro webovou aplikaci.](app-service-web-tutorial-custom-domain.md)**
>

V  **[webu Azure portal](https://portal.azure.com/)**, klikněte na tlačítko **služby App Service** možnost na levé straně stránky.

Klikněte na název aplikace, ke které chcete přiřadit certifikát.

V **nastavení**, klikněte na tlačítko **nastavení SSL**.

Klikněte na tlačítko **Import App Service Certificate** a vyberte certifikát, který jste si právě koupili.

![Vložit obrázek importovat certifikát](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

V **vazby ssl** části klikněte na **přidat vazby**a pomocí rozevíracích seznamech vyberte název domény zabezpečit protokolem SSL a certifikát používat. Můžete také vybrat, jestli se má použít **[indikace názvu serveru (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** nebo SSL na základě IP adresy.

![Vložit obrázek vazby SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

Klikněte na tlačítko **přidat vazbu** uložte změny a povolte protokol SSL.

> [!NOTE]
> Pokud jste vybrali **založený na protokolu IP SSL** a vaší vlastní domény je nakonfigurovaný pomocí záznamu, je třeba provést následující kroky. Tyto informace jsou vysvětleny v další podrobnosti najdete [Advanced části](#Advanced).

V tomto okamžiku byste měli moct navštivte vaši aplikaci s použitím `HTTPS://` místo `HTTP://` k ověření, že certifikát nakonfigurovaný správně.

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>Krok 6: úlohy správy

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>Advanced

### <a name="verifying-domain-ownership"></a>Ověření vlastnictví domény

Existují dva typy ověřování domény nepodporuje certifikáty App service: ověřování domén a ruční ověření.

#### <a name="domain-verification"></a>Ověřování domény

Tato možnost jenom pro [doménu služby App Service, který jste si koupili z Azure.](custom-dns-web-site-buydomains-web-app.md). Azure automaticky přidá ověřovacím záznamem TXT za vás a dokončí proces.

#### <a name="manual-verification"></a>Ruční ověření

> [!IMPORTANT]
> Ověření webové stránky HTML (funguje pouze u standardní SKU certifikátu)
>

1. Vytvořte soubor HTML s názvem **"starfield.html"**

1. Obsah tohoto souboru by měla být přesným názvem tokenu pro ověření domény. (Token, který můžete zkopírovat ze stránky stav ověření domény)

1. Nahrajte tento soubor v kořenové složce webový server, který hostuje vaši doménu `/.well-known/pki-validation/starfield.html`

1. Klikněte na tlačítko **aktualizovat** aktualizovat stav certifikátu po dokončení ověření. Může trvat několik minut, než se ověřování dokončí.

> [!TIP]
> Ověřte, v terminálu pomocí `curl -G http://<domain>/.well-known/pki-validation/starfield.html` by měla obsahovat odpovědi `<verification-token>`.

#### <a name="dns-txt-record-verification"></a>Ověření záznam DNS TXT

1. Pomocí Správce DNS vytvořit záznam TXT na `@` subdoménu s hodnotou rovná tokenu pro ověření domény.
1. Klikněte na tlačítko **"Obnovit"** aktualizovat stav certifikátu po dokončení ověření.

> [!TIP]
> Je potřeba vytvořit záznam TXT na `@.<domain>` s hodnotou `<verification-token>`.

### <a name="assign-certificate-to-app-service-app"></a>Přiřadit certifikát k aplikaci služby App Service

Pokud jste vybrali **založený na protokolu IP SSL** a vaší vlastní domény je nakonfigurovaný pomocí záznamu, je třeba provést následující kroky:

Po konfiguraci IP adresy na základě vazby SSL, je vyhrazené IP adresy přiřazené vaší aplikaci. Tuto IP adresu můžete najít na **vlastní domény** stránce v části Nastavení aplikace, vpravo nahoře **názvy hostitelů** oddílu. Je hodnota uvedena jako **externí IP adresa**

![Vložit obrázek IP SSL.](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

Tato IP adresa se liší od virtuální IP adresu použitou dříve nakonfigurovat záznam A pro vaši doménu. Pokud jsou nakonfigurovány pro použití SNI SSL na základě, nebo nejsou nakonfigurovaná pro používání protokolu SSL, žádná adresa je uvedena pro tuto položku.

Pomocí nástrojů podle vašeho registrátora názvu domény, upravte záznam A pro vlastní název domény tak, aby odkazoval na IP adresu z předchozího kroku.

## <a name="rekey-and-sync-the-certificate"></a>Obnovit klíč a synchronizovat certifikát

Pokud byste někdy potřebovali obnovit klíč certifikátu, vyberte **obnovení klíče a synchronizace** možnost **vlastnosti certifikátu** stránky.

Klikněte na tlačítko **opětovné vytvoření** tlačítko k zahájení procesu. Tento proces může trvat 1 až 10 minut na dokončení.

![Vložit obrázek opětovné vytvoření protokolu SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Obnovení klíče vrátí certifikát pomocí nového certifikátu vydaného certifikační autoritou.

## <a name="renew-the-certificate"></a>Obnovení certifikátu

Kdykoli zapnout automatické prodloužení platnosti vašeho certifikátu, klikněte na tlačítko **nastavení automatického obnovení** na stránce Správa certifikátů. Vyberte **na** a klikněte na tlačítko **Uložit**. Certifikáty můžete spustit automaticky prodlužuje se platnost 90 dní před vypršením platnosti používáte automatické obnovení zapnuté.

![](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Jako ručně obnovit certifikát, klikněte na tlačítko **ruční obnovení** místo. Můžete požádat o certifikát ručně obnovit 60 dní před vypršením platnosti.

> [!NOTE]
> Obnoveného certifikátu není automaticky svázán s vaší aplikace, ať už ručně obnovit nebo ho obnovit automaticky. Vytvořte jeho vazbu do vaší aplikace, najdete v článku [prodloužit platnost certifikátů](./app-service-web-tutorial-custom-ssl.md#renew-certificates). 

<a name="notrenewed"></a>
## <a name="why-is-my-certificate-not-auto-renewed"></a>Proč se tento certifikát automaticky obnoví?

Pokud certifikát SSL, je nakonfigurovaný pro automatické obnovení, ale se automaticky neobnoví, bude pravděpodobně domény čeká na ověření. Poznámky: 

- GoDaddy, který generuje certifikáty služby App Service, vyžaduje ověření domény jednou za dva roky. Správce domény obdrží e-mailu jednou za tři roky k ověření domény. Zkontrolujte e-mail nebo ověřte svoji doménu selhání brání automaticky obnovuje certifikát App Service. 
- Z důvodu změny v zásadách GoDaddy všechny certifikáty služby App Service vydané před 1. března 2018 vyžadují reverification domény v době další obnovení (i když je povolená automatické obnovení certifikátu). Zkontrolujte e-mailu a dokončete ověření tohoto jednorázového domény pokračujte automatické obnovení certifikátu služby App Service. 

## <a name="more-resources"></a>Další zdroje informací

* [Vynucení HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Vynucení protokolu TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-1112)
* [Použití certifikátu SSL v kódu aplikace ve službě Azure App Service](app-service-web-ssl-cert-load.md)
* [– Nejčastější dotazy: Certifikáty App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)
