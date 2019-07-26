---
title: Řešení potíží s Azure Application Gateway s využitím App Service – přesměrování na adresu URL App Service
description: Tento článek poskytuje informace o tom, jak řešit potíže s přesměrováním při použití Azure Application Gateway s Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 07/19/2019
ms.author: absha
ms.openlocfilehash: 7baadfb549b19bb12757c82d723578202b5cf8ad
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347877"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Řešení potíží s App Service v Application Gateway

Naučte se diagnostikovat a řešit problémy zjištěné při použití aplikačního serveru jako cíle back-endu s Application Gateway

## <a name="overview"></a>Přehled

V tomto článku se dozvíte, jak řešit následující problémy:

> [!div class="checklist"]
> * Adresa URL App Service, která se zveřejňuje v prohlížeči v případě, že dojde k přesměrování
> * App Service doména souborů cookie ARRAffinity nastavenou na App Service název hostitele (example.azurewebsites.net) místo původního hostitele.

Když aplikace back-end pošle odezvu přesměrování, může být vhodné přesměrovat klienta na jinou adresu URL, než která je určena v back-endové aplikaci. Můžete to třeba udělat, když je služba App Service hostována za aplikační bránou a vyžaduje, aby klient provedl přesměrování na jeho relativní cestu. (Například přesměrování z contoso.azurewebsites.net/path1 na contoso.azurewebsites.net/path2.) Když služba App Service pošle odezvu přesměrování, používá stejný název hostitele v hlavičce umístění odpovědi jako v žádosti, kterou přijímá z aplikační brány. Proto klient provede požadavek přímo na contoso.azurewebsites.net/path2 namísto průchodu přes Aplikační bránu (contoso.com/path2). Obcházení aplikační brány není žádoucí.

K tomuto problému může dojít z následujících hlavních důvodů:

- Nakonfigurovali jste přesměrování na App Service. Přesměrování může být stejně jednoduché jako přidání koncového lomítka do požadavku.
- Máte ověřování Azure AD, které způsobuje přesměrování.

Pokud používáte App Services za Application Gateway, název domény přidružené k Application Gateway (example.com) se liší od názvu domény App Service (říká example.azurewebsites.net), protože si všimnete, že Hodnota domény pro soubor cookie ARRAffinity nastavená App Service bude mít název domény "example.azurewebsites.net", který není žádoucí. Původní název hostitele (example.com) by měl být hodnota názvu domény v souboru cookie.

## <a name="sample-configuration"></a>Ukázková konfigurace

- Naslouchací proces HTTP: Basic nebo Multi-Site
- Fond back-end adres: App Service
- Nastavení HTTP: "Výběr názvu hostitele z back-endu adresy" povolen
- Testu "Výběr názvu hostitele z nastavení HTTP" povoleno

## <a name="cause"></a>Příčina

Vzhledem k tomu, že App Service je víceklientské služba, používá v žádosti hlavičku hostitele ke směrování požadavku na správný koncový bod. Výchozí název domény služby App Services, *. azurewebsites.net (říká contoso.azurewebsites.net), se ale liší od názvu domény služby Application Gateway (řekněme contoso.com). Vzhledem k tomu, že původní požadavek od klienta má název domény služby Application Gateway (contoso.com) jako název hostitele, je třeba nakonfigurovat službu Application Gateway tak, aby při směrování požadavku na název hostitele služby App Service změnila název hostitele v původní žádosti na hostitele. back-end služby App Service.  Dosáhnete toho použitím přepínače "vybrat název hostitele z back-endu adresy" v konfiguraci nastavení HTTP Application Gateway a přepínač "vybrat název hostitele z nastavení HTTP back-endu" v konfiguraci sondy stavu.



![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Z tohoto důvodu, když App Service provede přesměrování, používá přepsaný název hostitele "contoso.azurewebsites.net" v hlavičce umístění namísto původního názvu hostitele "contoso.com", pokud není nakonfigurováno jinak. V níže uvedeném příkladu můžete zaškrtnout následující hlavičky žádosti a odpovědi.
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
V předchozím příkladu si všimněte, že hlavička odpovědi má stavový kód 301 pro přesměrování a záhlaví umístění má název hostitele App Service místo původního názvu hostitele "www.contoso.com".

## <a name="solution-rewrite-the-location-header"></a>Řešení: Přepsat hlavičku umístění

Název hostitele budete muset nastavit v hlavičce umístění na název domény služby Application Gateway. Provedete to tak, že vytvoříte [pravidlo přepsání](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) s podmínkou, která vyhodnotí, jestli hlavička umístění v odpovědi obsahuje azurewebsites.NET a provede akci pro přepsání hlavičky umístění, která má název hostitele aplikační brány.  Projděte si pokyny, [jak přepsat hlavičku umístění](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> Podpora přepisování hlaviček protokolu HTTP je k dispozici pouze pro Application Gateway [SKU Standard_V2 a WAF_v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) . V případě, že používáte SKU V1, důrazně doporučujeme, abyste provedli [migraci z verze V1 na verzi v2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2) , aby bylo možné používat přepis a další [Pokročilé možnosti](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) dostupné s SKU verze 2.

## <a name="alternate-solution-use-app-services-custom-domain-instead-of-default-domain-name"></a>Alternativní řešení: Místo výchozího názvu domény použijte vlastní doménu App Service.

Pokud používáte SKU verze V1, nebudete moci přepsat hlavičku umístění, protože tato funkce je k dispozici pouze pro SKU v2. Proto je potřeba předat stejný název hostitele, který Application Gateway obdrží App Service a místo toho, aby se provádělo přepsání hostitele, aby bylo možné problém s přesměrováním vyřešit.

Jakmile to uděláte, App Service provede přesměrování (pokud existuje) ve stejné původní hlavičce hostitele, která odkazuje na Application Gateway a ne na svou vlastní.

Chcete-li toho dosáhnout, musíte vlastnit vlastní doménu a postupovat podle níže uvedeného postupu.

- Zaregistrujte doménu do seznamu vlastních domén App Service. V takovém případě musíte mít v vlastní doméně záznam CNAME odkazující na plně kvalifikovaný název domény App Service. Další informace najdete v tématu [Mapování existujícího vlastního názvu DNS na Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Až to bude hotové, App Service je připravený přijmout název hostitele "www.contoso.com". Teď změňte položku CNAME v DNS tak, aby odkazovala zpátky na plně kvalifikovaný název domény Application Gateway. Například "appgw.eastus.cloudapp.azure.com".

- Ujistěte se, že se vaše doména "www.contoso.com" překládá na plně kvalifikovaný název domény Application Gateway v případě, že provedete dotaz DNS.

- Nastavením vlastního testu zakážete možnost "vybrat název hostitele z back-endu HTTP nastavení". To lze provést z portálu zrušením zaškrtnutí políčka v nastavení sondy a v PowerShellu tak, že v příkazu set-AzApplicationGatewayProbeConfig nepoužijete přepínač-PickHostNameFromBackendHttpSettings. Do pole název hostitele testu zadejte plně kvalifikovaný název domény App Service example.azurewebsites.net, protože požadavky testu odeslané z Application Gateway jsou v hlavičce hostitele.

  > [!NOTE]
  > Při provádění dalšího kroku se ujistěte, že vlastní test paměti není přidružený k nastavení HTTP back-endu, protože v tomto okamžiku má vaše nastavení HTTP stále povolený přepínač Vybrat název hostitele z back-endu adresy.

- Nastavením nastavení HTTP Application Gateway zakážete možnost vybrat název hostitele z back-endu adresy. To lze provést z portálu zrušením zaškrtnutí políčka a v PowerShellu tak, že v příkazu set-AzApplicationGatewayBackendHttpSettings nepoužijete přepínač-PickHostNameFromBackendAddress.

- Přidružte vlastní test zpátky k nastavení HTTP back-endu a ověřte stav back-endu, pokud je v pořádku.

- Až to uděláte, Application Gateway by teď měl přesměrovat stejný název hostitele "www.contoso.com" do App Service a přesměrování proběhne na stejném názvu hostitele. V níže uvedeném příkladu můžete zaškrtnout následující hlavičky žádosti a odpovědi.

Postup implementace kroků uvedených výše pomocí prostředí PowerShell pro existující instalaci získáte pomocí ukázkového skriptu PowerShellu. Všimněte si, že jsme v konfiguraci testu a nastavení HTTP nepoužívali přepínače-PickHostname.

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
  ## <a name="next-steps"></a>Další postup

Pokud předchozí kroky problém nevyřeší, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).
