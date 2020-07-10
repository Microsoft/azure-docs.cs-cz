---
title: Co je nového v bráně firewall webových aplikací Azure
description: Zjistěte, co je nového u brány firewall webových aplikací Azure, jako jsou například nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 5033d19de2321e0dfd3b6d89d2da3306b1723bd0
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146591"
---
# <a name="whats-new-in-azure-web-application-firewall"></a>Co je nového v bráně firewall webových aplikací Azure?

Firewall webových aplikací Azure se průběžně aktualizuje. V tomto článku najdete informace o tom, jak se chcete zabývat aktuálním vývojem.

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce

## <a name="new-features"></a>Nové funkce

|Funkce  |Popis  |Datum přidání  |
|---------|---------|---------|
|Zásady pro jednotlivé lokality GA| Zásady pro jednotlivé lokality jsou teď všeobecně dostupné. | Červenec 2020 |
|RuleSet zmírnění hrozeb (Preview)|Můžete povolit RuleSet zmírnění hrozeb, a to i v případě, že jste si RuleSet počítačový počítač. | Listopad 2019 |
|Integrace GeoDB (Preview)|Nyní můžete vytvořit vlastní pravidla, která omezují provoz podle země nebo oblasti původu. | Listopad 2019 |
|Zásady WAF podle lokality/podle identifikátoru URI (Preview)|WAF-v2 teď podporuje aplikování zásad na naslouchací procesy a také pravidla na základě cest. Viz [Create WAF Policy](create-waf-policy-ag.md). | Listopad 2019 |
|Vlastní pravidla WAF |Application Gateway WAF_v2 teď podporuje vytváření vlastních pravidel. Viz [Application Gateway vlastní pravidla](custom-waf-rules-overview.md). |Červen 2019 |
|WAF konfigurace a seznam vyloučení     |Přidali jsme další možnosti, které vám pomůžou nakonfigurovat WAF a omezit falešně pozitivní výsledky. Další informace najdete v tématu [omezení velikosti požadavků a seznamů vyloučení v bráně firewall webové aplikace](application-gateway-waf-configuration.md) .|Prosinec 2018|

## <a name="next-steps"></a>Další kroky

Další informace o firewallu webových aplikací v Application Gateway najdete v tématu [Brána Firewall webových aplikací Azure v azure Application Gateway](ag-overview.md).
