---
title: Řešení potíží s přesměrováním na adresu URL App Service
titleSuffix: Azure Application Gateway
description: Tento článek poskytuje informace o tom, jak řešit potíže s přesměrováním při použití Azure Application Gateway s Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: f3a3ba3ee908204668ad9d7201ddfddec0a26f28
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89595940"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Řešení potíží s App Service v Application Gateway

Naučte se diagnostikovat a řešit problémy, se kterými se můžete setkat při použití Azure App Service jako cíle back-endu s využitím Azure Application Gateway.

## <a name="overview"></a>Přehled

V tomto článku se dozvíte, jak řešit následující problémy:

* Adresa URL služby App Service se zveřejňuje v prohlížeči, když dojde k přesměrování.
* Doména souborů cookie ARRAffinity App Service je nastavená na název hostitele App Service (example.azurewebsites.net) místo původního hostitele.

Když aplikace back-end pošle odezvu přesměrování, může být vhodné přesměrovat klienta na jinou adresu URL, než která je určena v back-endové aplikaci. To může být vhodné, pokud je služba App Service hostována za aplikační bránou a vyžaduje, aby klient provedl přesměrování na jeho relativní cestu. Příkladem je přesměrování z contoso.azurewebsites.net/path1 na contoso.azurewebsites.net/path2. 

Když služba App Service pošle odezvu přesměrování, používá stejný název hostitele v hlavičce umístění odpovědi jako v žádosti, kterou přijímá z aplikační brány. Například klient provede požadavek přímo na contoso.azurewebsites.net/path2 místo průchodu contoso.com/path2 služby Application Gateway. Nechcete bránu Application Gateway obejít.

K tomuto problému může dojít z následujících hlavních důvodů:

- Ve službě App Service jste nakonfigurovali přesměrování. Přesměrování může být stejně jednoduché jako přidání koncového lomítka do požadavku.
- Máte Azure Active Directory ověřování, což způsobí přesměrování.

I když používáte App Services za aplikační bránou, název domény přidružený k aplikační bráně (example.com) se liší od názvu domény služby App Service (například example.azurewebsites.net). Hodnota domény pro soubor cookie ARRAffinity nastavená službou App Service přenáší název domény example.azurewebsites.net, což není žádoucí. Původní název hostitele example.com by měl být hodnota názvu domény v souboru cookie.

## <a name="sample-configuration"></a>Ukázková konfigurace

- Naslouchací proces HTTP: základní nebo více lokalit
- Fond back-endové adresy: App Service
- Nastavení HTTP: **Vyberte název hostitele z back-endu adresy** povolené.
- Test paměti: **Vyberte název hostitele z nastavení http** povoleno.

## <a name="cause"></a>Příčina

App Service je víceklientská služba, takže používá hlavičku hostitele v žádosti ke směrování požadavku do správného koncového bodu. Výchozí název domény App Services, *. azurewebsites.net (například contoso.azurewebsites.net), se liší od názvu domény služby Application Gateway (říká se contoso.com). 

Původní požadavek od klienta má jako název hostitele název domény služby Application Gateway (contoso.com). Službu Application Gateway musíte nakonfigurovat tak, aby při směrování žádosti do back-endu služby App Service změnila název hostitele v původní žádosti na název hostitele App Service. V konfiguraci nastavení HTTP služby Application Gateway použijte přepínač **Vybrat název hostitele z back-endu adresy** . V konfiguraci testu stavu použijte přepínač **Vybrat název hostitele z nastavení http back-endu** .



![Aplikační brána změní název hostitele](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Když služba App Service přesměruje, použije přepsaný název hostitele contoso.azurewebsites.net v hlavičce umístění místo původního názvu hostitele contoso.com, pokud není nakonfigurovaný jinak. Podívejte se na následující příklady hlaviček požadavků a odpovědí.
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
V předchozím příkladu si všimněte, že hlavička odpovědi má stavový kód 301 pro přesměrování. Hlavička umístění má místo původního názvu hostitele název hostitele služby App Service `www.contoso.com` .

## <a name="solution-rewrite-the-location-header"></a>Řešení: přepište hlavičku umístění

Nastavte název hostitele v hlavičce umístění na název domény služby Application Gateway. Provedete to tak, že vytvoříte [pravidlo přepsání](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) s podmínkou, která vyhodnotí, jestli hlavička umístění v odpovědi obsahuje azurewebsites.NET. Musí také provést akci, která přepíše hlavičku umístění, aby měl název hostitele služby Application Gateway. Další informace najdete v pokynech k [přepsání hlavičky umístění](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> Podpora přepisování hlaviček protokolu HTTP je k dispozici pouze pro [Standard_v2 a WAF_V2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) Application Gateway. Pokud používáte SKU V1, doporučujeme [migrovat z verze V1 na verzi v2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2). Chcete použít přepis a další [Pokročilé funkce](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) , které jsou k dispozici s SKU v2.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternativní řešení: použijte vlastní název domény.

Pokud použijete SKU V1, nemůžete přepsat hlavičku umístění. Tato funkce je k dispozici pouze pro SKU v2. Chcete-li vyřešit problém s přesměrováním, předejte stejný název hostitele, který služba Application Gateway přijímá službě App Service, a nikoli přepsání hostitele.

App Service teď provádí přesměrování (pokud existuje) ve stejné původní hlavičce hostitele, která odkazuje na aplikační bránu, a ne na svou vlastní.

Musíte vlastnit vlastní doménu a postupovat podle tohoto postupu:

- Zaregistrujte doménu do seznamu vlastních domén služby App Service. V vlastní doméně musíte mít záznam CNAME, který odkazuje na plně kvalifikovaný název domény služby App Service. Další informace najdete v tématu [Mapování existujícího vlastního názvu DNS na Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

    ![Seznam vlastních domén služby App Service](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Vaše služba App Service je připravena přijmout název hostitele `www.contoso.com` . Změňte záznam CNAME v DNS tak, aby odkazoval zpátky na plně kvalifikovaný název domény služby Application Gateway, například `appgw.eastus.cloudapp.azure.com` .

- Ujistěte se, že se vaše doména `www.contoso.com` při dotazech DNS překládá na plně kvalifikovaný název domény služby Application Gateway.

- Nastavením vlastního testu zakážete možnost **Vybrat název hostitele z nastavení http back-endu**. V Azure Portal zrušte zaškrtnutí políčka v nastavení sondy. V prostředí PowerShell nepoužívejte v příkazu **set-AzApplicationGatewayProbeConfig** přepínač **-PickHostNameFromBackendHttpSettings** . Do pole název hostitele testu zadejte plně kvalifikovaný název domény služby App Service, example.azurewebsites.net. Požadavky testu odeslané z aplikační brány přenesou tento plně kvalifikovaný název domény v hlavičce hostitele.

  > [!NOTE]
  > V dalším kroku se ujistěte, že váš vlastní test paměti není přidružený k vašemu nastavení back-endu HTTP. V tomto okamžiku má vaše nastavení HTTP stále povolený přepínač **Vybrat název hostitele z back-endu adresy** .

- Nastavte nastavení HTTP služby Application Gateway tak, aby se zakázalo **výběr názvu hostitele z back-endu adresy**. V Azure Portal zrušte zaškrtnutí políčka. V prostředí PowerShell nepoužívejte v příkazu **set-AzApplicationGatewayBackendHttpSettings** přepínač **-PickHostNameFromBackendAddress** .

- Přidružte vlastní test zpět k nastavení HTTP back-endu a ověřte, jestli je back-end v pořádku.

- Služba Application Gateway by teď měla předejte stejný název hostitele `www.contoso.com` službě App Service. Přesměrování proběhne na stejném názvu hostitele. Podívejte se na následující příklady hlaviček požadavků a odpovědí.

K implementaci předchozích kroků pomocí prostředí PowerShell pro existující instalaci použijte ukázkový skript PowerShellu, který následuje. Všimněte si, že jsme v konfiguraci testu a nastavení HTTP nepoužívali přepínače **-PickHostname** .

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
