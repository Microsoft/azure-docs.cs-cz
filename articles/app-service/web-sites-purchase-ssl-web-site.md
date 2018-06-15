---
title: Zakoupení a konfigurace certifikátu protokolu SSL pro vaši Azure App Service | Microsoft Docs
description: Zjistěte, jak koupit certifikát služby App Service a navázat jej aplikaci aplikační služby
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
ms.openlocfilehash: 8c1db4693c6816ca7c3cc5b3147c0e8f3f8179c5
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807454"
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Koupě a konfigurace certifikátu SSL pro službu Azure App Service

V tomto kurzu se dozvíte, jak zabezpečit webové aplikace prostřednictvím zakoupení certifikátu SSL pro vaše  **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)**, bezpečně uložením do [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis), a přiřadí se mu vlastní doménu.

## <a name="step-1---log-in-to-azure"></a>Krok 1 – přihlášení do Azure

Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="step-2---place-an-ssl-certificate-order"></a>Krok 2 – umístit pořadí certifikát SSL

Certifikát SSL pořadí můžete umístit tak, že vytvoříte novou [certifikát služby aplikace](https://portal.azure.com/#create/Microsoft.SSL) v **portál Azure**.

![Vytvoření certifikátu](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Zadejte popisný **název** certifikátů pro zabezpečení SSL a zadejte **název domény**

> [!NOTE]
> Tento krok je jedním z nejdůležitějších části procesu nákupu. Ujistěte se, že zadejte název hostitele správný (vlastní domény), který chcete chránit pomocí tohoto certifikátu. **NECHCETE** připojit název hostitele s WWW. 
>

Vyberte vaše **předplatné**, **skupiny prostředků**, a **certifikátu SKU**

> [!TIP]
> Certifikáty App Service lze použít pro všechny Azure nebo Azure Services a není omezen na aplikační služby. K tomu potřebujete vytvořit místní kopii PFX certifikátu služby App Service, můžete ji použít všude, kde chcete. Další informace najdete v tématu [vytváření místní kopie PFX certifikát služby aplikace](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>Krok 3 – uložení certifikátu v Azure Key Vault

> [!NOTE]
> [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) je služba Azure, která pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami.
>

Po dokončení nákupu certifikát SSL, je nutné otevřít [služby App Service Certificate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) stránky.

![Vložit obrázek připravena k uložení v KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

Stav certifikátu je **"Čekající vystavení"** jsou několik další kroky je potřeba provést před zahájením s použitím tohoto certifikátu.

Klikněte na tlačítko **konfigurace certifikátu** uvnitř vlastnosti certifikátu a klikněte na tlačítko na **krok 1: uložení** pro uložení tohoto certifikátu v Azure Key Vault.

Z **klíč trezoru stav** klikněte na tlačítko **klíč trezoru úložiště** zvolit existující trezor klíč pro uložení tohoto certifikátu **nebo vytvořit nový klíč trezoru** k vytvoření nové Key Vault ve stejném předplatném nebo skupině prostředků.

> [!NOTE]
> Azure Key Vault má minimální poplatky za uložení tohoto certifikátu.
> Další informace najdete v tématu  **[klíč trezoru podrobnostmi o cenách Azure](https://azure.microsoft.com/pricing/details/key-vault/)**.
>

Jakmile vyberete klíč trezoru úložiště pro uložení tohoto certifikátu, **ukládání** možnost by se měla zobrazit úspěch.

![Vložit obrázek úspěchu úložiště v KV](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>Krok 4 - ověřit vlastnictví domény

Ze stejné **konfigurace certifikátu** stránky, které jste použili v kroku 3 klikněte na tlačítko **krok 2: ověření**.

Vyberte metodu ověřování upřednostňované domény. 

Existují čtyři typy ověření domény nepodporuje App Service Certificate: služby App Service, domény, e-mailu a ruční ověření. Tyto typy ověření jsou vysvětleny v další podrobnosti najdete [Advanced části](#advanced).

> [!NOTE]
> **Aplikace služby ověření** je nejvhodnější možnost, pokud chcete ověřit doménu je již namapován na aplikaci služby App Service ve stejném předplatném. Provádí se fakt, že aplikace App Service již ověřit vlastnictví domény.
>

Klikněte na **ověřte** tlačítko k dokončení tohoto kroku.

![Vložit obrázek ověření domény](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

Po kliknutí na **ověřte**, použijte **aktualizovat** tlačítko až **ověřte** možnost by se měla zobrazit úspěch.

![Vložit obrázek ověřit úspěšné provedení v KV](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>Krok 5 – přiřadit certifikát k aplikaci služby App Service

> [!NOTE]
> Před provedením kroků v této části, musí mít přidružené vlastního názvu domény s vaší aplikací. Další informace najdete v tématu  **[konfigurace vlastního názvu domény pro webovou aplikaci.](app-service-web-tutorial-custom-domain.md)**
>

V  **[portál Azure](https://portal.azure.com/)**, klikněte **služby App Service** možnost na levé straně stránky.

Klikněte na název aplikace, ke které chcete přiřadit certifikát.

V **nastavení**, klikněte na tlačítko **nastavení SSL**.

Klikněte na tlačítko **importovat aplikaci služby certifikát** a vyberte certifikát, který jste zakoupili.

![Vložit obrázek importovat certifikát](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

V **vazby ssl** část kliknutím na **přidat vazby**a pomocí rozevíracích seznamů vyberte název domény pro zabezpečené pomocí protokolu SSL a certifikát používat. Může také vybrat, jestli se má používat **[indikace názvu serveru (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** nebo IP na základě protokolu SSL.

![Vložit obrázek vazby SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

Klikněte na tlačítko **přidat vazbu** uložte změny a povolit protokol SSL.

> [!NOTE]
> Pokud jste vybrali **IP na základě SSL** a vaše vlastní doména je nakonfigurována pomocí záznamu A, je třeba provést následující kroky. Tyto mechanismy jsou popsány v další podrobnosti najdete [Advanced části](#Advanced).

V tomto okamžiku nyní byste měli mít k navštívení vaší aplikace pomocí `HTTPS://` místo `HTTP://` k ověření, že je certifikát správně nakonfigurováno.

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>Krok 6: úlohy správy

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>Advanced

### <a name="verifying-domain-ownership"></a>Ověření vlastnictví domény

Existují dva další typy ověření domény nepodporuje App service Certificate: e-mailu a ruční ověření.

#### <a name="mail-verification"></a>Ověření poštou

Již byl odeslán ověřovací e-mail do e-mailové adresy, přidružené k této vlastní doménu.
K dokončení kroku ověření e-mailu, otevřete e-mailu a klikněte na odkaz ověření.

![Vložit obrázek ověření e-mailu](./media/app-service-web-purchase-ssl-web-site/KVVerifyEmailSuccess.png)

Pokud potřebujete znovu odeslat ověřovací e-mail, klikněte **znovu odeslat e-mailu** tlačítko.

#### <a name="domain-verification"></a>Ověřování domény

Tuto volbu vyberte pouze pro [domény služby App Service, které jste zakoupili z Azure.](custom-dns-web-site-buydomains-web-app.md). Azure automaticky přidá ověření záznam TXT pro vás a dokončí proces.

#### <a name="manual-verification"></a>Ruční ověření

> [!IMPORTANT]
> Ověření webové stránky HTML (funguje pouze u standardní SKU certifikát)
>

1. Vytvořte soubor HTML s názvem **"starfield.html"**

1. Obsah tohoto souboru musí být přesný název tokenu ověření domény. (Můžete zkopírovat token na stránce Stav ověření domény)

1. Odeslat tento soubor v kořenovém adresáři webového serveru, který je hostitelem vaší domény `/.well-known/pki-validation/starfield.html`

1. Klikněte na tlačítko **aktualizovat** aktualizovat stav certifikátu po dokončení ověření. Může trvat několik minut na dokončení ověření.

> [!TIP]
> Ověřte v terminálu pomocí `curl -G http://<domain>/.well-known/pki-validation/starfield.html` by měl obsahovat odpověď `<verification-token>`.

#### <a name="dns-txt-record-verification"></a>Záznam ověření DNS TXT

1. Pomocí Správce DNS vytvořit záznam TXT na `@` subdomény se hodnota rovná tokenu ověření domény.
1. Klikněte na tlačítko **"Aktualizovat"** aktualizovat stav certifikátu po dokončení ověření.

> [!TIP]
> Musíte vytvořit záznam TXT na `@.<domain>` s hodnotou `<verification-token>`.

### <a name="assign-certificate-to-app-service-app"></a>Přiřadit certifikát k aplikaci služby App Service

Pokud jste vybrali **IP na základě SSL** a vaše vlastní doména je nakonfigurována pomocí záznamu A, je třeba provést následující kroky:

Po dokončení konfigurace IP adresy na základě vazby SSL, vyhrazenou IP adresu je přiřazené vaší aplikaci. Tuto IP adresu můžete najít na **vlastní domény** stránky v části Nastavení aplikace, vpravo nahoře **názvy hostitelů** části. Je uveden jako **externí IP adresu**

![Vložit obrázek IP protokolu SSL](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

Tato IP adresa se liší od virtuální IP adresy, které byly dříve slouží ke konfiguraci záznamu A pro doménu. Pokud jsou nakonfigurovány pro použití SNI na základě protokolu SSL, nebo nejsou nakonfigurovaná pro používání protokolu SSL, je uvedena žádnou adresu pro tuto položku.

Pomocí nástrojů vaším registrátorem názvu domény, upravte záznamu A pro váš vlastní název domény tak, aby odkazoval na IP adresu z předchozího kroku.

## <a name="rekey-and-sync-the-certificate"></a>Změna hodnoty klíče a synchronizovat certifikátu

Pokud byste někdy potřebovali změna hodnoty klíče vašeho certifikátu, vyberte **opětovné vytvoření klíčů a synchronizaci** možnost z **vlastnosti certifikátu** stránky.

Klikněte na tlačítko **změna hodnoty klíče** tlačítko na zahájení procesu. Tento proces může trvat 1 až 10 minut na dokončení.

![Vložit obrázek změna hodnoty klíče protokolu SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Obnovení klíčů vašeho certifikátu vrátí certifikát s nový certifikát vydaný certifikační autority.

## <a name="renew-the-certificate"></a>Obnovení certifikátu

Chcete-li při kdykoli zapnout automatické obnovení certifikátu, klikněte na tlačítko **nastavení automatického obnovení** na stránce Správa certifikátů. Vyberte **na** a klikněte na tlačítko **Uložit**.

![](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Chcete-li jako ruční obnovení certifikátu, klikněte na tlačítko **ruční obnovení** místo.

> [!NOTE]
> Certifikát s prodlouženou platností není vázán automaticky do vaší aplikace, ať už ručně obnovit nebo obnovují automaticky. Pro vytvoření vazby do vaší aplikace, najdete v části [obnovení certifikátů](./app-service-web-tutorial-custom-ssl.md#renew-certificates). 

<a name="notrenewed"></a>
## <a name="why-is-my-certificate-not-auto-renewed"></a>Proč není můj auto obnovit certifikát?

Pokud váš certifikát SSL je nakonfigurovaný pro automatické obnovení, ale nebude automaticky obnovena, bude pravděpodobně ověření čekající domény. Poznámky: 

- GoDaddy, který generuje certifikáty App Service, vyžaduje ověření domény jednou za dva roky. Správce domény obdrží e-mail, jednou za tři roky ověřit doménu. Selhání zkontrolujte e-mailu nebo ověřte svoji doménu zabrání certifikát služby App Service se automaticky obnovují vždy. 
- Z důvodu změny v zásadách GoDaddy vyžadují všechny služby App Service certifikáty vydané před 1. března 2018 reverification domény v době obnovení Další (i když je povolená automatického obnovení certifikátu). Zkontrolujte e-mailu a dokončit ověření jednorázové domény, chcete-li pokračovat automatického obnovení certifikátu služby App Service. 

## <a name="more-resources"></a>Další zdroje informací

* [Vynucení HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Vynutit TLS 1.1 nebo 1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-1112)
* [Použít certifikát SSL v kódu aplikace v Azure App Service](app-service-web-ssl-cert-load.md)
* [– Nejčastější dotazy: Certifikáty App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)