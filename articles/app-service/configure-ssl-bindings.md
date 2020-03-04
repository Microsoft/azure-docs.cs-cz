---
title: Zabezpečení vlastního serveru DNS pomocí vazby SSL
description: Zabezpečte přístup HTTPS k vlastní doméně vytvořením vazby TLS/SSL s certifikátem. Vylepšete zabezpečení svého webu vynucením protokolu HTTPS nebo TLS 1,2.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 263b4e76d334aab82f6bbac9aa268a50f4dd3784
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255178"
---
# <a name="secure-a-custom-dns-name-with-an-ssl-binding-in-azure-app-service"></a>Zabezpečení vlastního názvu DNS s vazbou SSL v Azure App Service

V tomto článku se dozvíte, jak zabezpečit [vlastní doménu](app-service-web-tutorial-custom-domain.md) ve vaší [aplikaci App Service](https://docs.microsoft.com/azure/app-service/) nebo [aplikaci Function App](https://docs.microsoft.com/azure/azure-functions/) vytvořením vazby certifikátu. Až budete hotovi, můžete k aplikaci App Service přistupovat na `https://` koncový bod pro vlastní název DNS (například `https://www.contoso.com`). 

![Webová aplikace s vlastním certifikátem SSL](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Zabezpečení [vlastní domény](app-service-web-tutorial-custom-domain.md) s certifikátem se skládá ze dvou kroků:

- [Přidejte privátní certifikát do App Service](configure-ssl-certificate.md) , který splňuje všechny požadavky na [vazby SSL](configure-ssl-certificate.md#private-certificate-requirements).
-  Vytvořte vazbu SSL k odpovídající vlastní doméně. Tento druhý krok je popsaný v tomto článku.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Upgrade cenové úrovně aplikace
> * Zabezpečení vlastní domény pomocí certifikátu
> * Vynucení HTTPS
> * Vynucení protokolu TLS 1.1/1.2
> * Automatizace správy protokolu TLS pomocí skriptů

## <a name="prerequisites"></a>Předpoklady

Postup při použití tohoto průvodce:

- [Vytvořit aplikaci App Service](/azure/app-service/)
- [Namapujte název domény na aplikaci](app-service-web-tutorial-custom-domain.md) nebo [kupte a nakonfigurujte ji v Azure](manage-custom-dns-buy-domain.md) .
- [Přidání privátního certifikátu do aplikace](configure-ssl-certificate.md)

> [!NOTE]
> Nejjednodušší způsob, jak přidat privátní certifikát, je [vytvořit bezplatný App Service spravovaný certifikát](configure-ssl-certificate.md#create-a-free-certificate-preview) (Preview).

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Zabezpečení vlastní domény

Proveďte následující kroky:

V <a href="https://portal.azure.com" target="_blank">Azure Portal</a>v nabídce vlevo vyberte **App Services** >  **\<název aplikace >** .

V levém navigačním panelu aplikace spusťte dialog **vazby TLS/SSL** :

- Výběr **vlastních domén** > **Přidání vazby**
- Výběr **Nastavení TLS/ssl** > **Přidání vazby TLS/SSL**

![Přidat vazbu k doméně](./media/configure-ssl-bindings/secure-domain-launch.png)

V části **vlastní doména**vyberte vlastní doménu, pro kterou chcete přidat vazbu.

Pokud už vaše aplikace obsahuje certifikát pro vybranou vlastní doménu, pokračujte na [vytvoření vazby](#create-binding) přímo. V opačném případě pokračujte dál.

### <a name="add-a-certificate-for-custom-domain"></a>Přidat certifikát pro vlastní doménu

Pokud vaše aplikace nemá žádný certifikát pro vybranou vlastní doménu, máte dvě možnosti:

- **Nahrát certifikát PFX** – postupujte podle pracovního postupu při [nahrávání privátního certifikátu](configure-ssl-certificate.md#upload-a-private-certificate)a pak vyberte tuto možnost.
- **Import App Service Certificate** – Sledujte pracovní postup při [importu certifikátu App Service](configure-ssl-certificate.md#import-an-app-service-certificate)a pak vyberte tuto možnost.

> [!NOTE]
> Můžete také [vytvořit bezplatný certifikát](configure-ssl-certificate.md#create-a-free-certificate-preview) (Preview) nebo [importovat certifikát Key Vault](configure-ssl-certificate.md#import-a-certificate-from-key-vault), ale musíte to udělat samostatně a pak se vrátit k dialogovému OKNU **vazby TLS/SSL** .

### <a name="create-binding"></a>Vytvořit vazbu

Pomocí následující tabulky můžete nakonfigurovat vazbu SSL v dialogovém okně **vazby TLS/SSL** a pak kliknout na **Přidat vazbu**.

| Nastavení | Popis |
|-|-|
| Vlastní doména | Název domény, pro kterou chcete přidat vazbu SSL. |
| Kryptografický otisk privátního certifikátu | Certifikát, který se má vytvořit. |
| Typ TLS/SSL | <ul><li>**[Sni SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** – přidat lze více vazeb sni SSL. Tato možnost umožňuje zabezpečení několika domén na stejné IP adrese pomocí několika certifikátů SSL. Většina moderních prohlížečů (včetně aplikací Internet Explorer, Chrome, Firefox a Opera) podporuje SNI (Další informace najdete v tématu [indikace názvu serveru](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**IP SSL** – dá se přidat jenom jedna vazba IP SSL. Tato možnost umožňuje zabezpečení vyhrazené veřejné IP adresy pouze jedním certifikátem SSL. Po nakonfigurování vazby použijte postup v části [přemapování záznamu pro IP SSL](#remap-a-record-for-ip-ssl).<br/>IP SSL se podporuje jenom v produkčních nebo izolovaných vrstvách. </li></ul> |

Po dokončení operace se stav protokolu SSL vlastní domény změní na **zabezpečeno**.

![Vazba SSL byla úspěšná.](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> **Zabezpečený** stav ve **vlastních doménách** znamená, že je zabezpečený certifikátem, ale App Service nekontroluje, jestli je certifikát podepsaný svým držitelem nebo pokud vypršela jeho platnost, což může taky způsobit, že se v prohlížečích zobrazí chyba nebo upozornění.

## <a name="remap-a-record-for-ip-ssl"></a>Přemapování záznamu A pro IP SSL

Pokud ve své aplikaci nepoužíváte IP SSL, přeskočte na [test HTTPS pro vaši vlastní doménu](#test-https).

Ve výchozím nastavení vaše aplikace používá sdílenou veřejnou IP adresu. Když svážete certifikát s IP SSL, App Service vytvoří novou vyhrazenou IP adresu pro vaši aplikaci.

Pokud jste namapovali záznam A na svou aplikaci, aktualizujte svůj registr domény pomocí této nové vyhrazené IP adresy.

Stránka **vlastní doména** vaší aplikace se aktualizuje o novou vyhrazenou IP adresu. [Zkopírujte tuto IP adresu](app-service-web-tutorial-custom-domain.md#info) a pak [přemapujte záznam A](app-service-web-tutorial-custom-domain.md#map-an-a-record) na tuto novou IP adresu.

## <a name="test-https"></a>Test HTTPS

V různých prohlížečích vyhledejte `https://<your.custom.domain>` a ověřte, že aplikace funguje.

![Přechod do aplikace Azure na portálu](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Kód aplikace může zkontrolovat protokol přes hlavičku "x-AppService-proto". Záhlaví bude mít hodnotu `http` nebo `https`. 

> [!NOTE]
> Pokud vaše aplikace poskytuje chyby ověřování certifikátů, pravděpodobně používáte certifikát podepsaný svým držitelem.
>
> Pokud to není váš případ, možná jste při exportování certifikátu do souboru PFX vynechali zprostředkující certifikáty.

## <a name="prevent-ip-changes"></a>Zabránit změnám IP adres

Vaše příchozí IP adresa se může při odstranění vazby změnit, i když je tato vazba IP SSL. To je obzvláště důležité, když obnovujete certifikát, který je již ve IP SSL vazbě. Pokud chcete zabránit změně IP adresy vaší aplikace IP, postupujte podle těchto kroků v uvedeném pořadí:

1. Nahrajte nový certifikát.
2. Vytvořte vazbu nového certifikátu k požadované vlastní doméně. Starý certifikát neodstraňujte. Tato akce nahradí tuto vazbu (místo aby odebrala vazbu původní).
3. Odstraňte starý certifikát. 

## <a name="enforce-https"></a>Vynucení HTTPS

Ve výchozím nastavení může kdokoli k vaší aplikaci přistupovat pomocí protokolu HTTP. Všechny požadavky HTTP můžete přesměrovat na port HTTPS.

V levém navigačním panelu na stránce aplikace vyberte **Nastavení SSL**. Pak v části **Pouze HTTPS** vyberte **Zapnuto**.

![Vynucení HTTPS](./media/configure-ssl-bindings/enforce-https.png)

Po dokončení operace přejděte na jakoukoli adresu URL HTTP odkazující na vaši aplikaci. Příklad:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Vynucení verzí protokolu TLS

Vaše aplikace ve výchozím nastavení povoluje protokol [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2, což je doporučená úroveň protokolu TLS z hlediska oborových standardů, například [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Pokud chcete vynucovat jiné verze protokolu TLS, postupujte následovně:

V levém navigačním panelu na stránce aplikace vyberte **Nastavení SSL**. Pak v části **Verze protokolu TLS** vyberte minimální požadovanou verzi protokolu TLS. Toto nastavení řídí pouze příchozí volání. 

![Vynucení protokolu TLS 1.1 nebo 1.2](./media/configure-ssl-bindings/enforce-tls1-2.png)

Po dokončení operace bude vaše aplikace odmítat všechna připojení využívající nižší verze protokolu TLS.

## <a name="handle-ssl-termination"></a>Zpracování ukončení SSL

V App Service dojde k [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v nástrojích pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS dosáhnou vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje zkontrolovat, jestli jsou požadavky uživatele zašifrované, nebo ne, zkontrolujte `X-Forwarded-Proto` záhlaví.

Konfigurační příručky specifické pro jazyk, jako je například Průvodce [konfigurací Node. js pro Linux](containers/configure-language-nodejs.md#detect-https-session) , ukazují, jak detekovat relaci HTTPS v kódu aplikace.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Další zdroje informací

* [Použití certifikátu SSL v kódu aplikace](configure-ssl-certificate-in-code.md)
* [Nejčastější dotazy: App Service certifikátů](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
