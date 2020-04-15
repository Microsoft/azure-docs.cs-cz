---
title: Co je nového v Azure Application Gateway
description: Zjistěte, co je nového s Azure Application Gateway, jako jsou nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 70014b5ab37a07e01eaa2db3d729b7d8af520842
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311766"
---
# <a name="whats-new-in-azure-application-gateway"></a>Co je nového v Azure Application Gateway?

Azure Application Gateway se průběžně aktualizuje. Chcete-li zůstat aktualizován s nejnovějším vývojem, tento článek vám poskytuje informace o:

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce

## <a name="new-features"></a>Nové funkce

|Funkce  |Popis  |Datum přidání  |
|---------|---------|---------|
| Uživatelem definované trasy (UDR) na verzi 2 (náhled) |Uživatelem definované trasy jsou nyní podporovány v některých scénářích na aplikační brány v2 SKU. Další informace naleznete v tématu [Přehled konfigurace aplikační brány](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet). |Březen 2020 |
|Změny souborů cookie spřažení |Pokud je povolena spřažení založené na souborech cookie, application gateway vloží další identické cookie s názvem *ApplicationGatewayAffinityCORS* kromě existující applicationgatewayAffinity cookie. *ApplicationGatewayAffinityCORS* má další dva atributy, které jsou k němu přidány (*SameSite=None; Zabezpečit*) tak, aby lepkavé relace jsou udržovány i pro požadavky napříč původem. Další informace naleznete [v tématu spřažení souborů cookie na základě aplikační](configuration-overview.md#cookie-based-affinity) brány. |Únor 2020 |
|Vylepšení sondy |S vlastní vylepšení sondy v Application Gateway v2 SKU, jsme zjednodušili [konfiguraci sondy](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku), [usnadnili na vyžádání back-endové testy stavu](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) a [přidali další diagnostické informace,](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) které vám pomohou vyřešit problémy se stavem back-endu.  |Říjen 2019 |
|Další metriky |Přidali jsme následující nové metriky, které vám pomohou sledovat vaši aplikační bránu v2 Skladovou [ku: metriky související s časováním](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), Stav back-endové odpovědi, Přijaté bajty, Odeslané bajty, Protokol TLS klienta a Aktuální výpočetní jednotky. Viz [Metriky podporované soupoložkou SKU aplikace V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Srpen 2019 |
|Vlastní pravidla WAF |WAF_v2 brány aplikací nyní podporuje vytváření vlastních pravidel. Viz [Vlastní pravidla brány aplikace](custom-waf-rules-overview.md). |Červen 2019 |
|Automatické škálování, redundance zóny, statická podpora VIP GA |Obecná dostupnost pro skladovou položku v2, která podporuje automatické škálování, redundanci zóny, zvýšení výkonu, statické VIP, trezor klíčů, přepsání záhlaví. Viz [Dokumentace k automatickému škálování aplikační brány](application-gateway-autoscaling-zone-redundant.md). |Duben 2019 |
|Integrace trezoru klíčů |Aplikační brána nyní podporuje integraci s trezorem klíčů (ve verzi Public Preview) pro serverové certifikáty, které jsou připojeny k naslouchacím líným a listenerům s povoleným protokolem HTTPS. Viz [Ukončení TLS s certifikáty trezoru klíčů](key-vault-certs.md). |Duben 2019 |
|Záhlaví CRUD/Přepisy     |Nyní můžete přepsat hlavičky HTTP. Viz [kurz: Vytvoření aplikační brány a přepsání hlaviček HTTP](tutorial-http-header-rewrite-powershell.md) pro další informace.|Prosinec 2018|
|Seznam konfigurace a vyloučení WAF     |Přidali jsme další možnosti, které vám pomohou nakonfigurovat waf a snížit falešně pozitivní výsledky. Další informace naleznete v tématu [Omezení velikosti a seznamy vyloučení brány firewall webových aplikací](application-gateway-waf-configuration.md).|Prosinec 2018|
|Automatické škálování, redundance zón, podpora statické houštin vip      |S v2 SKU existuje mnoho vylepšení, jako je automatické škálování, lepší výkon a další. Další informace najdete v tématu [Co je služba Azure Application Gateway?](overview.md)|Září 2018|
|Vyprázdnění připojení     |Vypouštění připojení umožňuje řádně odebrat členy z back-endových fondů. Další informace naleznete v tématu [Připojení vypouštění](features.md#connection-draining).|Září 2018|
|Vlastní chybové stránky     |Pomocí vlastních chybových stránek můžete vytvořit chybovou stránku ve formátu zbytku webů. Chcete-li to povolit, [přečtěte si téma Vytvoření vlastních chybových stránek brány aplikace](custom-error.md).|Září 2018|
|Vylepšení metrik     |Pomocí rozšířených metrik můžete získat lepší přehled o stavu vaší aplikační brány. Informace o povolení metrik ve vaší aplikační bráně najdete v [tématu Stav back-endu, diagnostické protokoly a metriky pro aplikační bránu](application-gateway-diagnostics.md).|Červen 2018|

## <a name="next-steps"></a>Další kroky

Další informace o Azure Application Gateway najdete v tématu [Co je Azure Application Gateway?](overview.md)
