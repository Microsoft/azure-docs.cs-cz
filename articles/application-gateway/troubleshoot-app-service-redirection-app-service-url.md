---
title: Řešení potíží s Azure Application Gateway pomocí služby App Service – přesměrování na adresu URL služby App Service
description: Tento článek obsahuje informace o tom, jak vyřešit problém přesměrování, když se používá Azure Application Gateway pomocí Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 7a645574a75a040c3b0218714363cf85e0384e68
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959829"
---
# <a name="troubleshoot-application-gateway-with-app-service--redirection-to-app-services-url"></a>Řešení potíží s Application Gateway s využitím služby App Service – přesměrování na adresu URL služby App Service

 Zjistěte, jak diagnostikovat a vyřešit problémy přesměrování pomocí služby Application Gateway, kde získat adresu URL služby App Service zpřístupněn.

## <a name="overview"></a>Přehled

Když nakonfigurujete veřejnou směřující služby App Service ve fondu back-endu služby Application Gateway a pokud máte přesměrování nakonfigurovaný v kódu aplikace, může se zobrazit, který při přístupu k Application Gateway, budete přesměrováni přímo do aplikace v prohlížeči Adresa URL služby.

Tento problém může dojít z následujících hlavních důvodů:

- Máte přesměrování nakonfigurované ve službě App Service. Přesměrování může být stejně jednoduché jako přidání koncové lomítko k požadavku.
- Máte ověřování Azure AD, což způsobí, že přesměrování.
- Povolili jste přepínač "Vyberte hostitele název z back-end adres" v nastavení protokolu HTTP služby Application Gateway.
- Nemáte vlastní doménu zaregistrována ve službě App Service.

## <a name="sample-configuration"></a>Ukázky konfigurace

- Naslouchací proces protokolu HTTP: Základní nebo multi-Site
- Fond back-end adres: App Service
- Nastavení protokolu HTTP: "Si můžete vybrat název hostitele z back-endová adresa" povoleno
- Test: "Si můžete vybrat název hostitele z nastavení HTTP" povoleno

## <a name="cause"></a>Příčina

Služby App Service je přístupný pouze s názvy hostitelů nakonfigurované v nastavení vlastní domény ve výchozím nastavení je to "example.azurewebsites.net" a pokud budete chtít přístup ke službě App Service s názvem hostitele není zaregistrovaný ve službě App Service nebo s využitím Application Gateway Služba Application Gateway plně kvalifikovaný název domény, je nutné přepsat jako název hostitele v původní požadavek na název hostitele služby App Service.

Chcete-li toho dosáhnout pomocí služby Application Gateway, používáme přepínače "Vybrat název hostitele z back-end adres" v nastavení HTTP a kontroly pro práci, používáme "Vybrat název hostitele z back-Endového nastavení HTTP" v konfiguraci sondy.

![appservice-1](.\media\troubleshoot-app-service-redirection-app-service-url\appservice-1.png)

Kvůli tomu když služby App Service nemá přesměrování, používá název hostitele "example.azurewebsites.net" v hlavičce Location namísto původní název hostitele Pokud se nenakonfiguruje. Můžete zkontrolovat příkladu hlaviček žádostí a odpovědí níže.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://example.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=example.azurewebsites.net

X-Powered-By: ASP.NET
```
V předchozím příkladu si všimnete, že hlavička odpovědi se stavovým kódem 301 pro přesměrování a hlavičku location "www.contoso.com" má název hostitele služby App Service namísto původní název hostitele.

## <a name="solution"></a>Řešení

Tento problém lze vyřešit tím, že přesměrování na straně aplikace, ale pokud to není možné, že jsme musíte předat stejnou hlavičku hostitele, která přijímá Application Gateway k App Service také namísto provádění přepsání hostitele.

Jakmile to služby App Service bude proveďte přesměrování (pokud existuje) na stejném hlavičku původního hostitele, která odkazuje na aplikační bránu, ne své vlastní.

K dosažení tohoto musí vlastní doménu a pokračujte v procesu uvedených níže.

- Registrace domény do seznamu vlastních domén služby App Service. V takovém případě musí mít záznam CNAME ve vaší vlastní doméně, přejdete na plně kvalifikovaný název domény služby App Service. Další informace najdete v tématu [mapování existujícího vlastního názvu DNS do služby Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

![appservice-2](.\media\troubleshoot-app-service-redirection-app-service-url\appservice-2.png)

- Po dokončení, který je připraven přijmout hostname "www.contoso.com" na službu App Service. Teď změňte váš záznam CNAME v DNS tak, aby odkazovala zpátky na plně kvalifikovaný název domény Application Gateway. Například "appgw.eastus.cloudapp.azure.com".

- Ujistěte se, že "www.contoso.com" doména překládá na plně kvalifikovaný název domény Application Gateway použijete dotaz DNS.

- Nastavte vaše vlastní test paměti zakázat "Vybrat název hostitele z back-Endového nastavení HTTP". To můžete udělat z portálu zrušení zaškrtnutí políčka v nastavení testu a v prostředí PowerShell nepoužíváte - PickHostNameFromBackendHttpSettings v příkazu Set-AzApplicationGatewayProbeConfig přepínač. V poli Název hostitele testu zadejte váš plně kvalifikovaný název domény služby App Service "example.azurewebsites.net", sondy požadavky odeslané ze služby Application Gateway to budou mít v hlavičce hostitele.

  > [!NOTE]
  > Při dalším krokem, ujistěte se prosím, že vlastní test paměti není přidružen k nastavení HTTP back-endu, protože vaši dál nastavení protokolu HTTP má přepínač "Vybrat název hostitele z back-end adres" v tuto chvíli povoleno.

- Nastavte nastavení HTTP Application Gateway zakázat "Vybrat název hostitele z back-endová adresa". To můžete udělat z portálu, že zrušíte zaškrtnutí políčka a v prostředí PowerShell nepoužíváte - PickHostNameFromBackendAddress v příkazu Set-AzApplicationGatewayBackendHttpSettings přepínač.

- Přidružení vlastní test paměti nastavení HTTP back-endu a ověřte stav back-endu, pokud je v pořádku.

- Až to uděláte, služba Application Gateway nyní předávat stejný název hostitele "www.contoso.com" do služby App Service a provede přesměrování na stejný název hostitele. Můžete zkontrolovat příkladu hlaviček žádostí a odpovědí níže.
```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: [www.contoso.com](http://www.contoso.com)

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
```
## <a name="next-steps"></a>Další postup

Pokud předchozí kroky není problém vyřešit, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).
