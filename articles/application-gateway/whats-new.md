---
title: Co je nového v Azure Application Gateway
description: Seznamte se s novinkami v Azure Application Gateway, jako jsou například nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 6755825aa123b3d7d1eed23291790ff3dc477ac5
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382472"
---
# <a name="whats-new-in-azure-application-gateway"></a>Co je nového v Azure Application Gateway?

Azure Application Gateway se průběžně aktualizuje. Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek poskytuje informace o:

- Nejnovější vydané verzi
- Známé problémy
- Opravy chyb
- Zastaralé funkce

## <a name="new-features"></a>Nové funkce

|Funkce  |Popis  |Datum přidání  |
|---------|---------|---------|
|Další metriky |Přidali jsme následující nové metriky, které vám pomůžou monitorovat SKU Application Gateway v2: [Metriky související s časováním](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), stav odpovědi back-endu, přijaté bajty, odeslané bajty, klientský protokol TLS a aktuální výpočetní jednotky. Podívejte se [na metriky podporované Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Srpen 2019 |
|Vlastní pravidla WAF |Application Gateway WAF_v2 teď podporuje vytváření vlastních pravidel. Viz [Application Gateway vlastní pravidla](custom-waf-rules-overview.md). |Červeně 2019 |
|Automatické škálování, redundance zóny, statická VIP podpora GA |Obecná dostupnost pro SKU v2, která podporuje automatické škálování, redundanci zóny, zvýšení výkonu, statické VIP Key Vault, přepisování hlaviček. Viz [Application Gateway dokumentace automatického škálování](application-gateway-autoscaling-zone-redundant.md). |Duben 2019 |
|Integrace Key Vault |Application Gateway teď podporuje integraci s Key Vault (ve verzi Public Preview) pro certifikáty serveru, které jsou připojené k naslouchacím procesům s povoleným protokolem HTTPS. Přečtěte si téma [ukončení SSL s certifikáty Key Vault](key-vault-certs.md). |Duben 2019 |
|CRUD a Přepisy záhlaví     |Nyní můžete přepsat hlavičky HTTP. Viz [kurz: Pokud chcete získat další informace, vytvořte Aplikační bránu a přepište hlavičky](tutorial-http-header-rewrite-powershell.md) http.|Prosinec 2018|
|WAF konfigurace a seznam vyloučení     |Přidali jsme další možnosti, které vám pomůžou nakonfigurovat WAF a omezit falešně pozitivní výsledky. Další informace najdete v tématu [omezení velikosti požadavků a seznamů vyloučení v bráně firewall webové aplikace](application-gateway-waf-configuration.md) .|Prosinec 2018|
|Automatické škálování, redundance zóny, podpora statických virtuálních IP adres      |V případě SKU verze V2 existuje mnoho vylepšení, jako je automatické škálování, lepší výkon a další. Další informace najdete v tématu [co je Azure Application Gateway?](overview.md) .|Září 2018|
|Vyprazdňování připojení     |Vyprazdňování připojení umožňuje řádným odebráním členů ze svých back-end fondů. Další informace najdete v tématu [vyprazdňování připojení](overview.md#connection-draining).|Září 2018|
|Vlastní chybové stránky     |Pomocí vlastních chybových stránek můžete vytvořit chybovou stránku v rámci formátu zbývajících webů. Pokud to chcete povolit, přečtěte si téma [vytvoření Application Gateway vlastní chybové stránky](custom-error.md).|Září 2018|
|Vylepšení metrik     |Můžete získat lepší přehled o stavu Application Gateway s rozšířenými metrikami. Pokud chcete na svém Application Gateway povolit metriky, přečtěte si téma [stav back-endu, diagnostické protokoly a metriky pro Application Gateway](application-gateway-diagnostics.md).|Červen 2018|

## <a name="next-steps"></a>Další kroky

Další informace o Azure Application Gateway najdete v tématu [co je azure Application Gateway?](overview.md)
