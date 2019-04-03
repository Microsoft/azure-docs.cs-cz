---
title: Co je nového ve službě Azure Application Gateway
description: Zjistěte, co je nového pomocí služby Azure Application Gateway, jako je nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: f686c8ac53db2d128cf5bb20f252c547348e5ac7
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863096"
---
# <a name="whats-new-in-azure-application-gateway"></a>Co je nového ve službě Azure Application Gateway?

Azure Application Gateway se průběžně aktualizuje. Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek poskytuje informace o:

- Nejnovější vydané verzi
- Známé problémy
- Opravy chyb
- Zastaralé funkce

## <a name="new-features"></a>Nové funkce

|Funkce  |Popis  |Datum přidání  |
|---------|---------|---------|
|Záhlaví CRUD/přepisů     |Nyní můžete přepsat hlavičky protokolu HTTP. Zobrazit [kurzu: Vytvoření služby application gateway a přepište hlavičky protokolu HTTP](tutorial-http-header-rewrite-powershell.md) Další informace.|Prosinec 2018|
|Konfigurace a vyloučení seznamu WAF     |Přidali jsme další možnosti, které vám pomohou konfigurovat bránu WAF a snížil počet falešných poplachů. Zobrazit [webových aplikací brány firewall na žádost o omezení velikosti a seznamy vyloučení](application-gateway-waf-configuration.md) Další informace.|Prosinec 2018|
|Automatické škálování, redundanci zón, statické virtuálních IP adres podpora ve verzi preview     |S v2 SKU je mnoho vylepšení, jako jsou automatické škálování, lepší výkon a další. Zobrazit [co je Azure Application Gateway?](overview.md#autoscaling-public-preview) Další informace.|Září 2018|
|Vyprázdnění připojení     |Vyprázdnění připojení umožňuje řádně odeberte členy ze back-endové fondy. Další informace najdete v tématu [vyprázdnění připojení](overview.md#connection-draining).|Září 2018|
|Vlastní chybové stránky     |S vlastní chybové stránky můžete vytvořit chybovou stránku v rámci formátu rest z vašich webů. Chcete-li povolit, přečtěte si téma [vytvořit Application Gateway vlastní chybové stránky](custom-error.md).|Září 2018|
|Vylepšení metriky     |Můžete získat lepší přehled o stavu vaše brána Application Gateway s rozšířené metriky. K zapnutí metrik ve službě Application Gateway, naleznete v tématu [stav Back endu, diagnostické protokoly a metriky pro službu Application Gateway](application-gateway-diagnostics.md).|Červen 2018|

## <a name="known-issues"></a>Známé problémy

- [Známé problémy v v2 SKU](application-gateway-autoscaling-zone-redundant.md#known-issues-and-limitations)

## <a name="next-steps"></a>Další postup

Další informace o službě Azure Application Gateway najdete v tématu [co je Azure Application Gateway?](overview.md)