---
title: Firewall webových aplikací Azure na Application Gateway – Nejčastější dotazy
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se firewallu webových aplikací v Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 890688dba70a7fa654e97652b3e474b919f9a077
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585379"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Nejčastější dotazy k firewallu webových aplikací Azure na Application Gateway

Tento článek obsahuje odpovědi na běžné dotazy k firewallu webových aplikací Azure (WAF) na Application Gateway funkcích a funkcích. 

## <a name="what-is-azure-waf"></a>Co je Azure WAF?

Azure WAF je brána firewall webových aplikací, která pomáhá chránit webové aplikace před běžnými hrozbami, jako jsou například injektáže SQL, skriptování mezi weby a další webové zneužití. Můžete definovat zásady WAF, které se skládají z kombinace vlastních a spravovaných pravidel pro řízení přístupu k vašim webovým aplikacím.

Zásady Azure WAF je možné použít pro webové aplikace hostované v Application Gateway nebo na frontách Azure.

## <a name="what-features-does-the-waf-sku-support"></a>Jaké funkce WAF SKU podporuje?

SKU WAF podporuje všechny funkce, které jsou k dispozici ve standardní SKU Standard.

## <a name="how-do-i-monitor-waf"></a>Návody monitorování WAF?

Monitorujte WAF prostřednictvím diagnostického protokolování. Další informace najdete v tématu [protokolování diagnostiky a metriky pro Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

## <a name="does-detection-mode-block-traffic"></a>Blokuje režim detekce přenos dat?

No. V režimu detekce se protokoluje pouze provoz, který aktivuje pravidlo WAF.

## <a name="can-i-customize-waf-rules"></a>Můžu přizpůsobit pravidla WAF?

Ano. Další informace najdete v tématu [přizpůsobení skupin pravidel a pravidel pro WAF](application-gateway-customize-waf-rules-portal.md).

## <a name="what-rules-are-currently-available-for-waf"></a>Jaká pravidla jsou aktuálně k dispozici pro WAF?

WAF v současné době podporuje počítačový [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229), [3,0](application-gateway-crs-rulegroups-rules.md#owasp30)a [3,1](application-gateway-crs-rulegroups-rules.md#owasp31). Tato pravidla poskytují základní zabezpečení proti většině nejčastějších chyb zabezpečení, které otevřou projekt zabezpečení webových aplikací (OWASP): 

* Ochrana před útoky prostřednictvím injektáže SQL.
* Ochrana skriptování mezi weby
* Ochrana před běžnými webovými útoky, jako je vkládání příkazů, podvržení požadavků HTTP, rozdělování odpovědí HTTP a útok na vzdálené zahrnutí souborů
* Ochrana před narušením protokolu HTTP.
* Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.
* Ochrana před roboty, prohledávacími moduly a skenery.
* Detekce běžných neobvyklých konfigurací aplikací (tj. Apache, IIS atd.)

Další informace najdete v tématu [OWASP chyby zabezpečení nejvyšší úrovně 10](https://owasp.org/www-project-top-ten/).

## <a name="what-content-types-does-waf-support"></a>Jaké typy obsahu podporuje WAF?

Application Gateway WAF podporují následující typy obsahu pro spravovaná pravidla:

* application/json
* aplikace/XML
* Application/x-www-form-urlencoded
* multipart/form-data

A pro vlastní pravidla:

* Application/x-www-form-urlencoded
* multipart/form-data

## <a name="does-waf-support-ddos-protection"></a>Podporuje WAF ochranu DDoS?

Ano. Můžete povolit DDoS Protection ve virtuální síti, ve které je nasazená Aplikační brána. Toto nastavení zajišťuje, že služba Azure DDoS Protection také chrání virtuální IP adresu služby Application Gateway.

### <a name="does-waf-store-customer-data"></a>Ukládá WAF zákaznická data?

Ne, WAF data zákazníků neukládá.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [bráně firewall webových aplikací Azure](../overview.md).
- Přečtěte si další informace o [službě Azure front-dveří](../../frontdoor/front-door-overview.md).
