---
title: Řešení potíží s přesměrováním na adresu URL App Service
titleSuffix: Azure Application Gateway
description: Tento článek poskytuje informace o tom, jak řešit potíže s přesměrováním při použití Azure Application Gateway s Azure App Service
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/15/2021
ms.author: jaysoni
ms.openlocfilehash: 6aad1cf1269a7c3dc082482c39fdc4a079fc3240
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514882"
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

Nastavte název hostitele v hlavičce umístění na název domény služby Application Gateway. Provedete to tak, že vytvoříte [pravidlo přepsání](./rewrite-http-headers.md) s podmínkou, která vyhodnotí, jestli hlavička umístění v odpovědi obsahuje azurewebsites.NET. Musí také provést akci, která přepíše hlavičku umístění, aby měl název hostitele služby Application Gateway. Další informace najdete v pokynech k [přepsání hlavičky umístění](./rewrite-http-headers.md#modify-a-redirection-url).

> [!NOTE]
> Podpora přepisování hlaviček protokolu HTTP je k dispozici pouze pro [Standard_v2 a WAF_V2 SKU](./application-gateway-autoscaling-zone-redundant.md) Application Gateway. Pro přepsání hlaviček a další [Pokročilé funkce](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku) , které jsou k dispozici s SKU v2, doporučujeme [migrovat na v2](./migrate-v1-v2.md) .

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternativní řešení: použijte vlastní název domény.

Použití funkce vlastní doména App Service je další řešení, které vždy přesměruje provoz na název domény Application Gateway ( `www.contoso.com` v našem příkladu). Tato konfigurace také slouží jako řešení problému s souborem cookie spřažení ARR. Ve výchozím nastavení je doména souborů cookie ARRAffinity nastavena na výchozí název hostitele (example.azurewebsites.net) App Service místo názvu domény Application Gateway. Proto prohlížeč v takových případech odmítne soubor cookie z důvodu rozdílu v názvech domény žádosti a souboru cookie.

Tuto metodu můžete použít jak pro problémy s přesměrováním, tak i pro problémy s doménou ARRAffinity souborů cookie. Tato metoda bude potřebovat přístup k zóně DNS vlastní domény.

**Krok 1**: v App Service nastavte vlastní doménu a ověřte vlastnictví domény přidáním [záznamů DNS & txt](../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id).
Záznamy by vypadaly podobně jako
-  `www.contoso.com` V CNAME `contoso.azurewebsite.net`
-  `asuid.www.contoso.com` v TXT " `<verification id string>` "


**STEP2**: záznam CNAME v předchozím kroku byl nutný jenom pro ověření domény. V konečném důsledku potřebujeme provoz směrovat prostřednictvím Application Gateway. CNAME teď můžete upravit `www.contoso.com` tak, aby odkazovalo na plně kvalifikovaný název domény Application Gateway. Chcete-li nastavit plně kvalifikovaný název domény pro váš Application Gateway, přejděte na prostředek s veřejnou IP adresou a přiřaďte mu označení "název DNS". Aktualizovaný záznam CNAME by teď měl vypadat takto. 
-  `www.contoso.com` V CNAME `contoso.eastus.cloudapp.azure.com`


**Step3**: pro přidružené nastavení http zakažte možnost "vybrat název hostitele z back-endu adresy".

V prostředí PowerShell Nepoužívejte `-PickHostNameFromBackendAddress` přepínač v `Set-AzApplicationGatewayBackendHttpSettings` příkazu.


**Step4**: aby testy zjistily back-end jako v pořádku a provozní provoz, nastavte vlastní sondu stavu s polem hostitel jako vlastní nebo výchozí doménu App Service.

V prostředí PowerShell Nepoužívejte `-PickHostNameFromBackendHttpSettings` přepínač v `Set-AzApplicationGatewayProbeConfig` příkazu a v přepínači-hostname pro test použijte buď vlastní nebo výchozí doménu App Service.

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
