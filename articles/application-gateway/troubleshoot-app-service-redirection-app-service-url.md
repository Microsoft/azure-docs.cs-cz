---
title: Poradce při potížích s přesměrováním na adresu URL služby App Service
titleSuffix: Azure Application Gateway
description: Tento článek obsahuje informace o tom, jak vyřešit problém s přesměrováním při použití Azure Application Gateway se službou Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 961ed17bcef19b445c2546a557725bb6bd8653cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293541"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Poradce při potížích se službou App Service v bráně aplikace

Zjistěte, jak diagnostikovat a řešit problémy, se kterými se můžete setkat, když se služba Azure App Service používá jako back-endový cíl s Azure Application Gateway.

## <a name="overview"></a>Přehled

V tomto článku se dozvíte, jak řešit následující problémy:

> [!div class="checklist"]
> * Adresa URL služby aplikace je v prohlížeči vystavena při přesměrování.
> * Aplikační služba ARRAffinity cookie doména je nastavena na název hostitele služby aplikace, example.azurewebsites.net, namísto původního hostitele.

Když back-endová aplikace odešle odpověď na přesměrování, můžete klienta přesměrovat na jinou adresu URL, než kterou zadala aplikace back-end. Můžete to provést, když je služba aplikace hostovaná za aplikační bránou a vyžaduje, aby klient přesměroval na relativní cestu. Příkladem je přesměrování z contoso.azurewebsites.net/path1 do contoso.azurewebsites.net/path2. 

Když služba aplikace odešle odpověď na přesměrování, použije stejný název hostitele v hlavičce umístění své odpovědi jako ten v požadavku, který obdrží z aplikační brány. Klient například provede požadavek přímo contoso.azurewebsites.net/path2 namísto procházení contoso.com/path2 brány aplikace. Nechcete obejít aplikační bránu.

K tomuto problému může dojít z následujících hlavních důvodů:

- Máte přesměrování nakonfigurované ve vaší službě aplikace. Přesměrování může být stejně jednoduché jako přidání koncového lomítka do požadavku.
- Máte Azure Active Directory ověřování, které způsobuje přesměrování.

Když používáte služby aplikace za aplikační bránou, název domény přidružený k aplikační bráně (example.com) se liší od názvu domény aplikační služby (řekněme example.azurewebsites.net). Hodnota domény pro soubor cookie ARRAffinity nastavený službou aplikace nese example.azurewebsites.net název domény, což není žádoucí. Původní název hostitele, example.com, by měl být hodnota názvu domény v souboru cookie.

## <a name="sample-configuration"></a>Ukázková konfigurace

- Http naslouchací proces: Základní nebo více stránek
- Fond adres back-endu: Služba App Service
- Nastavení PROTOKOLU HTTP: **Vyberte položku Hostname z povolené adresy Back-end.**
- Sonda: **Vyberte název hostitele z nastavení PROTOKOLU HTTP** povoleno.

## <a name="cause"></a>Příčina

Služba App Service je víceklientská služba, takže používá hlavičku hostitele v požadavku ke směrování požadavku do správného koncového bodu. Výchozí název domény App Services, *.azurewebsites.net (řekněme contoso.azurewebsites.net) se liší od názvu domény aplikační brány (řekněme contoso.com). 

Původní požadavek klienta má název domény aplikační brány, contoso.com jako název hostitele. Je třeba nakonfigurovat bránu aplikace změnit název hostitele v původním požadavku na název hostitele služby aplikace, když směruje požadavek na back-end služby aplikace. Použijte přepínač **Vybrat název hostitele z back-endové adresy** v konfiguraci nastavení HTTP aplikační brány. Použijte přepínač **Pick Hostname z nastavení HTTP back-endu** v konfiguraci sondy stavu.



![Aplikační brána změní název hostitele](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Když služba aplikace provede přesměrování, použije přepsaný název hostitele contoso.azurewebsites.net v hlavičce umístění namísto původního názvu hostitele contoso.com, pokud není nakonfigurovánjinak. Zkontrolujte následující ukázkové hlavičky požadavků a odpovědí.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
V předchozím příkladu všimněte si, že hlavička odpovědi má stavový kód 301 pro přesměrování. Hlavička umístění má název hostitele služby app service `www.contoso.com`namísto původního názvu hostitele .

## <a name="solution-rewrite-the-location-header"></a>Řešení: Přepsání hlavičky umístění

Nastavte název hostitele v hlavičce umístění na název domény aplikační brány. Chcete-li to provést, [vytvořte pravidlo přepsání](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) s podmínkou, která vyhodnotí, zda hlavička umístění v odpovědi obsahuje azurewebsites.net. Musí také provést akci k přepsání hlavičky umístění, aby měl název hostitele aplikační brány. Další informace naleznete v pokynech [k přepsání hlavičky umístění](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> Podpora přepisu záhlaví PROTOKOLU HTTP je k dispozici pouze pro [Standard_v2 a WAF_v2 skladové položky](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) aplikační brány. Pokud používáte v1 SKU, doporučujeme [migrovat z v1 na v2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2). Chcete použít přepis a další [pokročilé funkce,](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) které jsou k dispozici s v2 SKU.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternativní řešení: Použití vlastního názvu domény

Pokud používáte v1 SKU, nelze přepsat záhlaví umístění. Tato funkce je k dispozici pouze pro skladovou položku v2. Chcete-li vyřešit problém přesměrování, předejte stejný název hostitele, který obdrží aplikační brána také do služby aplikace, namísto provedení přepsání hostitele.

Služba aplikace nyní provádí přesměrování (pokud existuje) na stejné původní záhlaví hostitele, který odkazuje na aplikační bránu a ne na vlastní.

Vlastní doménu musíte vlastnit a postupovat podle tohoto postupu:

- Zaregistrujte doménu do seznamu vlastnídomény aplikační služby. Musíte mít CNAME ve vaší vlastní doméně, která odkazuje na fQDN služby aplikace. Další informace najdete [v tématu Mapování existujícího vlastního názvu DNS na službu Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

    ![Seznam vlastních domén služby App service](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Vaše služba aplikace je připravena `www.contoso.com`přijmout název hostitele . Změňte položku CNAME ve službě DNS tak, aby napočit `appgw.eastus.cloudapp.azure.com`zpět na fQDN brány aplikace, například .

- Ujistěte se, `www.contoso.com` že vaše doména překládá na fQDN aplikační brány při provádění dotazu DNS.

- Nastavte vlastní sondu tak, aby **zakázala funkci Vybrat název hostitele z nastavení HTTP back-endu**. Na webu Azure Portal zrušte zaškrtnutí políčka v nastavení sondy. V PowerShellu nepoužívejte přepínač **-PickHostNameFromBackendHttpSettings** v příkazu **Set-AzApplicationGatewayProbeConfig.** Do pole název hostitele sondy zadejte hlavní název názvu služby aplikace, example.azurewebsites.net. Požadavky na sondu odeslané z aplikační brány přenášejí tento hlavní název v hlavičce hostitele.

  > [!NOTE]
  > V dalším kroku se ujistěte, že vlastní sonda není přidružena k nastavení protokolu HTTP back-endu. V nastavení protokolu HTTP je v tomto okamžiku stále povolen přepínač **Vybrat název hostitele z back-endové adresy.**

- Nastavte nastavení HTTP vaší aplikační brány tak, aby **zakázala funkci Vybrat název hostitele z back-endové adresy**. Na webu Azure Portal zrušte zaškrtnutí políčka. V PowerShellu nepoužívejte přepínač **-PickHostNameFromBackendAddress** v příkazu **Set-AzApplicationGatewayBackendHttpSettings.**

- Přidružte vlastní sondu zpět k nastavení protokolu HTTP back-end a ověřte, zda je back-end v pořádku.

- Aplikační brána by teď měla `www.contoso.com`předávat stejný název hostitele , službě aplikace. Přesměrování se děje na stejný název hostitele. Zkontrolujte následující ukázkové hlavičky požadavků a odpovědí.

Chcete-li implementovat předchozí kroky pomocí PowerShellu pro existující nastavení, použijte ukázkový skript Prostředí PowerShell, který následuje. Všimněte si, jak jsme nepoužili přepínače **-PickHostname** v konfiguraci nastavení sondy a HTTP.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Další kroky

Pokud předchozí kroky problém nevyřešily, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).
