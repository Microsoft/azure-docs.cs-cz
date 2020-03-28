---
title: Zabezpečení vlastního DNS pomocí vazby SSL
description: Zabezpečte přístup HTTPS k vlastní doméně vytvořením vazby TLS/SSL s certifikátem. Zlepšete zabezpečení svého webu vynucením protokolu HTTPS nebo TLS 1.2.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 263b4e76d334aab82f6bbac9aa268a50f4dd3784
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239704"
---
# <a name="secure-a-custom-dns-name-with-an-ssl-binding-in-azure-app-service"></a>Zabezpečení vlastního názvu DNS s využitím vazby SSL ve službě Azure App Service

Tento článek ukazuje, jak zabezpečit [vlastní doménu](app-service-web-tutorial-custom-domain.md) v [aplikaci app service](https://docs.microsoft.com/azure/app-service/) nebo funkce [vytvořením](https://docs.microsoft.com/azure/azure-functions/) vazby certifikátu. Po dokončení můžete přistupovat k aplikaci App `https://` Service v koncovém bodě pro `https://www.contoso.com`vlastní název DNS (například). 

![Webová aplikace s vlastním certifikátem SSL](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Zabezpečení [vlastní domény](app-service-web-tutorial-custom-domain.md) certifikátem zahrnuje dva kroky:

- [Přidejte soukromý certifikát do služby App Service,](configure-ssl-certificate.md) který splňuje všechny [požadavky na vazby SSL](configure-ssl-certificate.md#private-certificate-requirements).
-  Vytvořte vazbu SSL pro odpovídající vlastní doménu. Tento druhý krok se vztahuje tento článek.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Upgrade cenové úrovně aplikace
> * Zabezpečení vlastní domény pomocí certifikátu
> * Vynucení HTTPS
> * Vynucení protokolu TLS 1.1/1.2
> * Automatizace správy protokolu TLS pomocí skriptů

## <a name="prerequisites"></a>Požadavky

Postupujte podle tohoto návodu:

- [Vytvořit aplikaci App Service](/azure/app-service/)
- [Namapujte název domény na aplikaci](app-service-web-tutorial-custom-domain.md) nebo [si ji nakupte a nakonfigurujte v Azure](manage-custom-dns-buy-domain.md)
- [Přidání soukromého certifikátu do aplikace](configure-ssl-certificate.md)

> [!NOTE]
> Nejjednodušší způsob, jak přidat soukromý certifikát, je [vytvořit bezplatný certifikát spravovaný službou App Service](configure-ssl-certificate.md#create-a-free-certificate-preview) (Preview).

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Zabezpečení vlastní domény

Postupujte takto:

Na <a href="https://portal.azure.com" target="_blank">webu Azure Portal</a>vyberte v levé nabídce název aplikace **App Services** > **\<>**.

V levém navigačním panelu aplikace spusťte dialogové okno **Vazby TLS/SSL** takto:

- Výběr **vlastních** > domén**Přidat vazbu**
- Výběr >  **nastavení TLS/SSL****Přidání vazby TLS/SSL**

![Přidání vazby do domény](./media/configure-ssl-bindings/secure-domain-launch.png)

Ve **vlastní doméně**vyberte vlastní doménu, pro kterou chcete přidat vazbu.

Pokud vaše aplikace už má certifikát pro vybranou vlastní doménu, přejděte přímo na [Vytvořit vazbu.](#create-binding) Jinak pokračuj.

### <a name="add-a-certificate-for-custom-domain"></a>Přidání certifikátu pro vlastní doménu

Pokud vaše aplikace nemá žádný certifikát pro vybranou vlastní doménu, máte dvě možnosti:

- **Nahrát pfx certifikát** - Postupujte podle pracovního postupu na [Upload soukromý certifikát](configure-ssl-certificate.md#upload-a-private-certificate), pak vyberte tuto možnost zde.
- **Importovat certifikát služby App Service** – postupujte podle pracovního postupu na [adrese Import certifikátu služby App Service](configure-ssl-certificate.md#import-an-app-service-certificate)a zde vyberte tuto možnost.

> [!NOTE]
> Můžete také [vytvořit bezplatný certifikát](configure-ssl-certificate.md#create-a-free-certificate-preview) (Náhled) nebo [importovat certifikát trezoru klíčů](configure-ssl-certificate.md#import-a-certificate-from-key-vault), ale musíte to udělat samostatně a potom se vrátit do dialogového okna **Vazba TLS/SSL.**

### <a name="create-binding"></a>Vytvořit vazbu

V následující tabulce můžete nakonfigurovat vazbu SSL v dialogovém okně **Vazba TLS/SSL** a klepněte na tlačítko **Přidat vazbu**.

| Nastavení | Popis |
|-|-|
| Vlastní doména | Název domény pro přidání vazby SSL. |
| Kryptografický otisk soukromého certifikátu | Certifikát svázat. |
| Typ TLS/SSL | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** - může být přidáno více ssl vazeb SNI. Tato možnost umožňuje zabezpečení několika domén na stejné IP adrese pomocí několika certifikátů SSL. Většina moderních prohlížečů (včetně Aplikací Internet Explorer, Chrome, Firefox a Opera) podporuje SNI (další informace naleznete v [tématu Označení názvu serveru](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**IP SSL** - může být přidána pouze jedna vazba IP SSL. Tato možnost umožňuje zabezpečení vyhrazené veřejné IP adresy pouze jedním certifikátem SSL. Po konfiguraci vazby postupujte podle pokynů v části [Přemapování záznamu a protokolu IP SSL](#remap-a-record-for-ip-ssl).<br/>Protokol IP SSL je podporován pouze v produkčních nebo izolovaných úrovních. </li></ul> |

Po dokončení operace se stav SSL vlastní domény změní na **Secure**.

![Vazba SSL byla úspěšná.](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> **Bezpečný** stav ve **vlastních doménách** znamená, že je zabezpečený certifikátem, ale služba App Service nekontroluje, zda je certifikát například podepsaný svým držitelem nebo vypršela jeho platnost, což může také způsobit, že prohlížeče zobrazí chybu nebo upozornění.

## <a name="remap-a-record-for-ip-ssl"></a>Přemapování záznamu A pro IP SSL

Pokud v aplikaci nepoužíváte protokol IP SSL, přejděte na [Test HTTPS pro vaši vlastní doménu](#test-https).

Ve výchozím nastavení aplikace používá sdílenou veřejnou IP adresu. Když svážete certifikát s PROTOKOLEM IP SSL, služba App Service vytvoří novou vyhrazenou IP adresu pro vaši aplikaci.

Pokud jste do aplikace namapovali záznam A, aktualizujte registr domény touto novou vyhrazenou IP adresou.

Stránka Vlastní **doména** aplikace se aktualizuje o novou vyhrazenou IP adresu. [Zkopírujte tuto IP adresu](app-service-web-tutorial-custom-domain.md#info) a pak [přemapujte záznam A](app-service-web-tutorial-custom-domain.md#map-an-a-record) na tuto novou IP adresu.

## <a name="test-https"></a>Test HTTPS

V různých prohlížečích `https://<your.custom.domain>` vyhledejte, zda se v aplikaci zobrazuje.

![Přechod do aplikace Azure na portálu](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Kód aplikace můžete zkontrolovat protokol prostřednictvím hlavičky "x-appservice-proto". Záhlaví bude mít `http` hodnotu `https`nebo . 

> [!NOTE]
> Pokud vaše aplikace poskytuje chyby ověření certifikátu, pravděpodobně používáte certifikát podepsaný svým držitelem.
>
> Pokud to není váš případ, možná jste při exportování certifikátu do souboru PFX vynechali zprostředkující certifikáty.

## <a name="prevent-ip-changes"></a>Zabránit změnám IP

Příchozí IP adresa se může změnit při odstranění vazby, i v případě, že tato vazba je IP SSL. To je obzvláště důležité při obnovení certifikátu, který je již ve vazbě IP SSL. Pokud chcete zabránit změně IP adresy vaší aplikace IP, postupujte podle těchto kroků v uvedeném pořadí:

1. Nahrajte nový certifikát.
2. Vytvořte vazbu nového certifikátu k požadované vlastní doméně. Starý certifikát neodstraňujte. Tato akce nahradí tuto vazbu (místo aby odebrala vazbu původní).
3. Odstraňte starý certifikát. 

## <a name="enforce-https"></a>Vynucení HTTPS

Ve výchozím nastavení může kdokoli přistupovat k vaší aplikaci pomocí protokolu HTTP. Všechny požadavky HTTP můžete přesměrovat na port HTTPS.

Na stránce aplikace vlevém na levé ho dispoziti vyberte **nastavení SSL**. Pak v části **Pouze HTTPS** vyberte **Zapnuto**.

![Vynucení HTTPS](./media/configure-ssl-bindings/enforce-https.png)

Po dokončení operace přejděte na jakoukoli adresu URL HTTP odkazující na vaši aplikaci. Například:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Vynucení verzí protokolu TLS

Vaše aplikace ve výchozím nastavení povoluje protokol [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2, což je doporučená úroveň protokolu TLS z hlediska oborových standardů, například [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Pokud chcete vynucovat jiné verze protokolu TLS, postupujte následovně:

Na stránce aplikace vlevém na levé ho dispoziti vyberte **nastavení SSL**. Pak v části **Verze protokolu TLS** vyberte minimální požadovanou verzi protokolu TLS. Toto nastavení řídí pouze příchozí volání. 

![Vynucení protokolu TLS 1.1 nebo 1.2](./media/configure-ssl-bindings/enforce-tls1-2.png)

Po dokončení operace bude vaše aplikace odmítat všechna připojení využívající nižší verze protokolu TLS.

## <a name="handle-ssl-termination"></a>Ukončení ssl popisovače

Ve službě App Service dojde k [ukončení SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v síťových nástrojích pro vyrovnávání zatížení, takže všechny požadavky HTTPS se dostanou do vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje zkontrolovat, jestli jsou požadavky `X-Forwarded-Proto` uživatelů zašifrované nebo ne, zkontrolujte záhlaví.

Jazykově specifické konfigurační příručky, jako je například [průvodce konfigurací Linux Node.js,](containers/configure-language-nodejs.md#detect-https-session) vám ukáže, jak zjistit relaci HTTPS v kódu aplikace.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Další zdroje informací

* [Použití certifikátu SSL v kódu aplikace](configure-ssl-certificate-in-code.md)
* [Časté otázky: Certifikáty služby App Service](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
