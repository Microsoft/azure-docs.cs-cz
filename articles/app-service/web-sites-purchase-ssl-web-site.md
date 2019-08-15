---
title: Koupit a nakonfigurovat certifikát SSL z Azure-App Service | Microsoft Docs
description: Naučte se koupit certifikát App Service a vytvořit jeho propojení s aplikací App Service.
services: app-service
documentationcenter: .net
author: cephalin
manager: jpconnoc
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: cephalin
ms.reviewer: apurvajo
ms.custom: seodec18
ms.openlocfilehash: 7675a22b4b2d8b13524f06f45d6bb805c1e2fad1
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019136"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Koupit a nakonfigurovat certifikát SSL pro Azure App Service

V tomto kurzu se dozvíte, jak zabezpečit svou aplikaci [App Service](https://docs.microsoft.com/azure/app-service/) nebo [aplikace Functions](https://docs.microsoft.com/azure/azure-functions/) tím, že vytvoříte (nakupujete) certifikát App Service v [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) a pak ho navážetete na App Service aplikaci.

> [!TIP]
> App Service certifikáty se dají použít pro jakékoli služby Azure nebo mimo Azure a není omezené na App Services. K tomu je potřeba vytvořit místní kopii PFX certifikátu App Service, kterou můžete použít kdekoli, kde chcete. Další informace najdete v tématu [Vytvoření místní kopie PFX App Service Certificate](https://blogs.msdn.microsoft.com/benjaminperkins/2017/04/12/export-an-azure-app-service-certificate-pfx-powershell/).
>

## <a name="prerequisites"></a>Požadavky

Postup při použití tohoto průvodce:

- [Vytvořit aplikaci App Service](/azure/app-service/)
- [Namapujte název domény na aplikaci](app-service-web-tutorial-custom-domain.md) nebo [kupte a nakonfigurujte ji v Azure](manage-custom-dns-buy-domain.md) .

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>Spustit pořadí certifikátů

Na <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">stránce vytvoření App Service Certificate</a>spusťte App Service pořadí certifikátů.

![Vytvoření certifikátu](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Pomocí následující tabulky můžete nakonfigurovat certifikát. Jakmile budete hotovi, klikněte na **Vytvořit**.

| Nastavení | Popis |
|-|-|
| Name | Popisný název certifikátu App Service. |
| Naked Domain Host Name | Sem zadejte kořenovou doménu. Vydaný certifikát zabezpečuje kořenovou doménu `www` i subdoménu. V vystaveném certifikátu obsahuje pole běžný název kořenovou doménu a pole Alternativní název subjektu obsahuje `www` doménu. Chcete-li zabezpečit všechny subdomény, zadejte plně kvalifikovaný název domény (například `mysubdomain.contoso.com`).|
| Subscription | Datacentrum, které je hostitelem webové aplikace. |
| Resource group | Skupina prostředků, která obsahuje certifikát. Můžete použít novou skupinu prostředků nebo vybrat stejnou skupinu prostředků jako aplikace App Service, například. |
| SKU certifikátu | Určuje typ certifikátu, který se má vytvořit, zda se jedná o standardní certifikát nebo [certifikát](https://wikipedia.org/wiki/Wildcard_certificate)se zástupným znakem. |
| Právní podmínky | Kliknutím potvrďte, že souhlasíte s právními podmínkami. Certifikáty se získávají z GoDaddy. |

## <a name="store-in-azure-key-vault"></a>Uložit v Azure Key Vault

Po dokončení procesu nákupu certifikátů je potřeba provést několik dalších kroků, než budete moct tento certifikát začít používat. 

Vyberte certifikát na stránce [App Service certifikáty](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) a pak klikněte na **Konfigurace** > **certifikátu krok 1: Uložit**.

![Vložit obrázek připraveného k uložení v KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) je služba Azure, která pomáhá chránit kryptografické klíče a tajné klíče používané v cloudových aplikacích a službách. Je to úložiště, které je vhodné pro App Service certifikátů.

Na stránce **stav Key Vault** klikněte na **úložiště Key Vault** a vytvořte nový trezor nebo vyberte existující trezor. Pokud se rozhodnete vytvořit nový trezor, použijte následující tabulku, která vám pomůžete nakonfigurovat trezor a kliknout na vytvořit. Další informace najdete v tématu Vytvoření nové Key Vault v rámci stejného předplatného a skupiny prostředků.

| Nastavení | Popis |
|-|-|
| Name | Jedinečný název, který se skládá pro alfanumerické znaky a pomlčky. |
| Resource group | Jako doporučení vyberte stejnou skupinu prostředků jako certifikát App Service. |
| Location | Vyberte stejné umístění jako aplikace App Service. |
| Cenová úroveň | Informace najdete v tématu informace [o cenách Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Zásady přístupu| Definuje aplikace a povolený přístup k prostředkům trezoru. Později ji můžete nakonfigurovat podle kroků uvedených v části [udělení několika aplikací přístup k trezoru klíčů](../key-vault/key-vault-group-permissions-for-apps.md). |
| Přístup k virtuální síti | Omezte přístup k trezoru na určité virtuální sítě Azure. Později ji můžete nakonfigurovat podle kroků v části [konfigurace Azure Key Vault brány firewall a virtuální sítě](../key-vault/key-vault-network-security.md) . |

Po výběru trezoru zavřete stránku **Key Vault úložiště** . Možnost **Store** by měla zobrazit zelenou značku zaškrtnutí pro úspěch. Nechejte stránku otevřenou pro další krok.

## <a name="verify-domain-ownership"></a>Ověřit vlastnictví domény

Ze stejné stránky **Konfigurace certifikátu** , kterou jste použili v posledním kroku, klikněte **na krok 2: Ověřte**.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

Vyberte **ověřování App Service**. Vzhledem k tomu, že jste už namapovali doménu na webovou aplikaci (viz [požadavky](#prerequisites)), je už ověřená. Stačí kliknout na **ověřit** , aby se tento krok dokončil. Klikněte na tlačítko **aktualizovat** , dokud se nezobrazí **certifikát zprávy ověřená doména** .

> [!NOTE]
> Podporovány jsou čtyři typy metod ověřování domény: 
> 
> - **App Service** – nejpohodlnější možnost, pokud je již doména namapována na App Service aplikace ve stejném předplatném. Využívá skutečnost, že aplikace App Service již ověřila vlastnictví domény.
> - **Doména** – ověřte [App Service doménu, kterou jste si koupili v Azure](manage-custom-dns-buy-domain.md). Azure pro vás automaticky přidá záznam ověřovacího TXT a proces se dokončí.
> - **E-mail** – ověřte doménu odesláním e-mailu správci domény. Pokyny jsou k dispozici, když vyberete možnost.
> - **Ručně** – ověřte doménu pomocí stránky HTML (jenom**standardní** certifikát) nebo záznamu TXT DNS. Pokyny jsou k dispozici, když vyberete možnost.

## <a name="bind-certificate-to-app"></a>Vytvoření vazby mezi certifikátem a aplikací

V **[Azure Portal](https://portal.azure.com/)** v nabídce vlevo vyberte **App Services** >  **\<your_ aplikace >** .

V levém navigačním panelu aplikace vyberte **Nastavení** > SSL import**privátních certifikátů (. pfx)**  > **App Service Certificate**.

![Vložit obrázek importovaného certifikátu](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Vyberte certifikát, který jste právě koupili.

Teď, když se certifikát naimportuje, je potřeba ho vytvořit v aplikaci s namapovaným názvem domény. Vyberte **vazby** > **Přidat vazbu SSL**. 

![Vložit obrázek importovaného certifikátu](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

Pomocí následující tabulky můžete nakonfigurovat vazbu v dialogovém okně **vazby SSL** a pak kliknout na **Přidat vazbu**.

| Nastavení | Popis |
|-|-|
| Název hostitele | Název domény, pro kterou chcete přidat vazbu SSL. |
| Kryptografický otisk privátního certifikátu | Certifikát, který se má vytvořit. |
| Typ SSL | <ul><li>**Sni SSL** – můžete přidat několik vazeb SSL založených na sni. Tato možnost umožňuje zabezpečení několika domén na stejné IP adrese pomocí několika certifikátů SSL. Většina moderních prohlížečů (včetně prohlížečů Internet Explorer, Chrome, Firefox a Opera) podporuje SNI (ucelenější informace o podpoře prohlížečů najdete v článku o [Indikaci názvu serveru](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**SSL na základě IP adresy** – Můžete přidat pouze jednu vazbu SSL na základě IP adresy. Tato možnost umožňuje zabezpečení vyhrazené veřejné IP adresy pouze jedním certifikátem SSL. Po nakonfigurování vazby použijte postup v části přemapování [záznamu pro IP SSL](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl). </li></ul> |

## <a name="verify-https-access"></a>Ověření přístupu HTTPS

Místo abyste ověřili, že byl certifikát správně nakonfigurovaný, přejděte do aplikace pomocí nástroje `HTTPS://<domain_name>`. `HTTP://<domain_name>`

## <a name="rekey-certificate"></a>Obnovit klíč certifikátu

Pokud se domníváte, že dojde k ohrožení bezpečnosti privátního klíče certifikátu, můžete svůj certifikát znovu zařadit do klíčů. Vyberte certifikát na stránce [App Service certifikáty](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) a pak v levém navigačním panelu vyberte obnovení **klíčů a synchronizaci** .

Kliknutím na obnovit **klíč** zahajte proces. Dokončení tohoto procesu může trvat 1-10 minut.

![Vložení obrázku SSL pro opětovné vytvoření klíče](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Po opětovném vytvoření klíče se certifikát zahrne do nového certifikátu vydaného certifikační autoritou.

Jakmile je operace obnovení klíčů dokončena, klikněte na tlačítko **synchronizovat**. Operace synchronizace automaticky aktualizuje vazby hostitelů pro certifikát v App Service, aniž by to mělo za následek jakékoliv výpadky aplikací.

> [!NOTE]
> Pokud nekliknete na **synchronizovat**, App Service certifikát automaticky synchronizuje během 48 hodin.

## <a name="renew-certificate"></a>Prodloužit platnost certifikátu

Pokud chcete kdykoli zapnout automatické obnovování certifikátu, vyberte certifikát na stránce [App Service certifikáty](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) a potom klikněte na tlačítko **automaticky obnovit nastavení** v levém navigačním panelu.

Vyberte **zapnuto** a klikněte na **Uložit**. Pokud máte zapnuté automatické obnovení, můžou se certifikáty spustit automaticky po 60 dnech před vypršením platnosti.

![obnovit certifikát automaticky](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Pokud chcete certifikát místo toho ručně obnovit, klikněte na **Ruční obnovení**. Můžete požádat o ruční obnovení certifikátu 60 dní před vypršením platnosti.

Po dokončení operace obnovení klikněte na **synchronizovat**. Operace synchronizace automaticky aktualizuje vazby hostitelů pro certifikát v App Service, aniž by to mělo za následek jakékoliv výpadky aplikací.

> [!NOTE]
> Pokud nekliknete na **synchronizovat**, App Service certifikát automaticky synchronizuje během 48 hodin.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Další materiály

* [Vynucení HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Vynutilit protokol TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Použijte certifikát SSL v kódu aplikace v Azure App Service](app-service-web-ssl-cert-load.md)
* [DOTAZ App Service certifikátů](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
