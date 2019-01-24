---
title: Zakoupení a konfigurace certifikátu SSL v Azure – službu App Service | Dokumentace Microsoftu
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
ms.date: 10/16/2018
ms.author: apurvajo;cephalin
ms.custom: seodec18
ms.openlocfilehash: 784cb5248dab2b9554c67347e1b9b848e1a9e985
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54820780"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Zakoupení a konfigurace certifikátu SSL pro službu Azure App Service

V tomto kurzu se dozvíte, jak zabezpečit webovou aplikaci tak, že vytvoříte (nákup) služby App Service certificate v [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) a jeho vazbu na aplikaci služby App Service.

> [!TIP]
> Certifikáty služby App Service můžete použít pro Azure nebo Azure Services a není omezena pouze na App Services. Uděláte to tak, musíte vytvořit místní kopii PFX certifikátu služby App Service můžete jej můžete použít všude, kde chcete. Další informace najdete v tématu [vytváření místní kopie PFX App Service Certificate](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).
>

## <a name="prerequisites"></a>Požadavky

Chcete postupovat podle příručky s postupy:

- [Vytvořit aplikaci App Service](/azure/app-service/)
- [Mapování názvu domény do webové aplikace](app-service-web-tutorial-custom-domain.md) nebo [koupě a konfigurace v Azure](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>Pořadí spouštění certifikátu

Spustit objednávky certifikátu služby App Service v <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">vytvořit stránku služby App Service Certificate</a>.

![Vytvoření certifikátu](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Pomocí následující tabulky můžete nakonfigurovat certifikát. Jakmile budete hotovi, klikněte na **Vytvořit**.

| Nastavení | Popis |
|-|-|
| Název | Popisný název služby App Service certificate. |
| Název hostitele základní domény | Pokud zadáte kořenovou doménu, můžete získat certifikát, který zabezpečuje *obě* kořenové domény a `www` subdomény. Pro zabezpečené jakákoli subdoména, zadat pouze plně kvalifikovaný název domény zde poddomény (například `mysubdomain.contoso.com`). |
| Předplatné | Datacentrum, které je hostitelem webové aplikace. |
| Skupina prostředků | Skupina prostředků, který obsahuje certifikát. Můžete použít novou skupinu prostředků nebo vyberte stejnou skupinu prostředků jako aplikace služby App Service, třeba. |
| SKU certifikátu | Určuje typ certifikátů k vytvoření, jestli certifikát standardní nebo [certifikát se zástupným znakem](https://wikipedia.org/wiki/Wildcard_certificate). |
| Právní podmínky | Klikněte na tlačítko potvrďte, že souhlasíte se smluvními podmínkami. |

## <a name="store-in-azure-key-vault"></a>Store ve službě Azure Key Vault

Po dokončení procesu nákupu certifikátů existuje několik kroků, které je potřeba dokončit před zahájením používání tohoto certifikátu. 

Vyberte certifikát v [služby App Service Certificate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) stránce a potom klikněte na **konfigurace certifikátu** > **krok 1: Store**.

![Vložit obrázek připraven k uložení v KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) je služba Azure, která pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Je úložiště volbou pro certifikáty služby App Service.

V **Key Vault stav** klikněte na **úložiště Key Vault** a vytvořte nový trezor nebo zvolte existující trezor. Pokud budete chtít vytvořit nový trezor, můžete v následující tabulce vám pomůžou nakonfigurovat trezor a klikněte na tlačítko vytvořit. naleznete v části vytvořit novou službu Key Vault ve stejném předplatném a skupině prostředků.

| Nastavení | Popis |
|-|-|
| Název | Jedinečný název, který se skládá alfanumerické znaky a pomlčky. |
| Skupina prostředků | Jako doporučení vyberte stejnou skupinu prostředků jako služby App Service certificate. |
| Umístění | Vyberte stejné umístění jako aplikace služby App Service. |
| Cenová úroveň | Informace najdete v tématu [podrobnosti o cenách služby Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Zásady přístupu| Definuje aplikace a povolený přístup k prostředkům úložiště. Můžete ho nakonfigurovat později, postupujte podle kroků v [udělit několik aplikací přístup k trezoru klíčů](../key-vault/key-vault-group-permissions-for-apps.md). |
| Přístup k virtuální síti | Omezení přístupu trezor k určité virtuálním sítím Azure. Můžete ho nakonfigurovat později, postupujte podle kroků v [konfigurace Azure Key Vault firewallů a virtuální sítě](../key-vault/key-vault-network-security.md) |

Po výběru trezoru, zavřete **úložiště Key Vault** stránky. **Store** možnost by se měla zobrazit zelená značka zaškrtnutí pro úspěch. Na stránce ponechte otevřené na další krok.

## <a name="verify-domain-ownership"></a>Ověřit vlastnictví domény

Ze stejného **konfigurace certifikátu** stránky, které jste použili v předchozím kroku, klikněte na tlačítko **krok 2: Ověřte**.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

Vyberte **ověřování App Service**. Protože je již namapována domény do webové aplikace (viz [požadavky](#prerequisites)), už je ověřený. Stačí kliknout na **ověřte** k dokončení tohoto kroku. Klikněte na tlačítko **aktualizovat** tlačítko, dokud se nezobrazí zpráva **certifikát má ověřenou doménu** se zobrazí.

> [!NOTE]
> Podporuje čtyři typy metod ověřování domény: 
> 
> - **App Service** -nejpohodlnější možnost, pokud doména už je namapovaný na aplikaci služby App Service ve stejném předplatném. Využívá skutečnost, že aplikace služby App Service už ověřit vlastnictví domény.
> - **Domény** – ověření [doména služby App Service, který jste si koupili z Azure](manage-custom-dns-buy-domain.md). Azure automaticky přidá ověřovacím záznamem TXT za vás a dokončí proces.
> - **E-mailu** – ověření domény e-mailem správci domény. Pokyny jsou k dispozici při výběru možnosti.
> - **Ruční** – ověření domény pomocí stránky HTML (**standardní** certifikátu pouze) nebo záznam DNS TXT. Pokyny jsou k dispozici při výběru možnosti.

## <a name="bind-certificate-to-app"></a>Vytvořte vazbu certifikátu k aplikaci

V  **[webu Azure portal](https://portal.azure.com/)**, v nabídce vlevo vyberte **App Services** > **\<your_ aplikace >**.

V levém navigačním panelu vaší aplikace, vyberte **nastavení SSL** > **privátní certifikáty (.pfx)** > **Import App Service Certificate**.

![Vložit obrázek importovat certifikát](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Vyberte certifikát, který jste si právě koupili.

Teď, když je certifikát importován, musíte ji vytvořit vazbu na název domény pro mapovanou ve vaší aplikaci. Vyberte **vazby** > **přidat vazbu SSL**. 

![Vložit obrázek importovat certifikát](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

Pomocí následující tabulky můžete nakonfigurovat vazby v **vazby SSL** dialogového okna, klikněte na **přidat vazbu**.

| Nastavení | Popis |
|-|-|
| Název hostitele | Chcete-li přidat vazbu SSL pro název domény. |
| Kryptografický otisk privátního certifikátu | Certifikát pro vazbu. |
| Typ SSL | <ul><li>**SNI SSL** -lze přidat více SSL typu sni vazby. Tato možnost umožňuje zabezpečení několika domén na stejné IP adrese pomocí několika certifikátů SSL. Většina moderních prohlížečů (včetně prohlížečů Internet Explorer, Chrome, Firefox a Opera) podporuje SNI (ucelenější informace o podpoře prohlížečů najdete v článku o [Indikaci názvu serveru](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**SSL na základě IP adresy** – Můžete přidat pouze jednu vazbu SSL na základě IP adresy. Tato možnost umožňuje zabezpečení vyhrazené veřejné IP adresy pouze jedním certifikátem SSL. Po konfiguraci vazby, postupujte podle kroků v [přemapování záznamu A pro IP SSL](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl). </li></ul> |

## <a name="verify-https-access"></a>Ověření přístupu pro protokol HTTPS

Navštivte vaši aplikaci s použitím `HTTPS://<domain_name>` místo `HTTP://<domain_name>` k ověření, že certifikát nakonfigurovaný správně.

## <a name="rekey-and-sync-certificate"></a>Obnovení klíče a certifikátu synchronizace

Pokud byste někdy potřebovali obnovit klíč certifikátu, vyberte certifikát v [služby App Service Certificate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) stránce a pak vyberte **obnovení klíče a synchronizace** z levé navigace.

Klikněte na tlačítko **opětovné vytvoření** tlačítko pro spuštění procesu. Tento proces může trvat 1 až 10 minut na dokončení.

![Vložit obrázek opětovné vytvoření protokolu SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Obnovení klíče vrátí certifikát pomocí nového certifikátu vydaného certifikační autoritou.

## <a name="renew-certificate"></a>Prodloužit platnost certifikátu

Zapnutí automatického obnovení certifikátu v okamžiku, vyberte certifikát v [služby App Service Certificate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) stránce a potom klikněte na **nastavení automatického obnovení** v levém navigačním panelu. 

Vyberte **na** a klikněte na tlačítko **Uložit**. Certifikáty můžete spustit automaticky obnovení 60 dní před vypršením platnosti, pokud máte zapnuté automatické obnovení.

![](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Jako ručně obnovit certifikát, klikněte na tlačítko **ruční obnovení**. Můžete požádat o certifikát ručně obnovit 60 dní před vypršením platnosti.

> [!NOTE]
> Obnoveného certifikátu není automaticky svázán s vaší aplikace, ať už ručně obnovit nebo ho obnovit automaticky. Vytvořte jeho vazbu do vaší aplikace, najdete v článku [prodloužit platnost certifikátů](./app-service-web-tutorial-custom-ssl.md#renew-certificates). 

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Další zdroje informací

* [Vynucení HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Vynucení protokolu TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Použití certifikátu SSL v kódu aplikace ve službě Azure App Service](app-service-web-ssl-cert-load.md)
* [NEJČASTĚJŠÍ DOTAZY: Certifikáty App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)
