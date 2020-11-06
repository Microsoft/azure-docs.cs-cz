---
title: Přehled služby Azure Application Gateway Resource Health
description: Tento článek je přehled funkcí stavu prostředků pro Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: 7e30a93f8270cfaf8910130cc1e2633bb80c2b8e
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397168"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Přehled služby Azure Application Gateway Resource Health

[Azure Resource Health](../service-health/resource-health-overview.md) pomáhá s diagnostikou a získáním podpory v případě, že problém se službou Azure ovlivňuje vaše prostředky. Informuje vás o aktuálním a minulém stavu vašich prostředků. A poskytuje technickou podporu, která vám může pomoci zmírnit problémy.

Pro Application Gateway Resource Health spoléhá na signály vysílané bránou k vyhodnocení toho, jestli je v pořádku. Pokud brána není v pořádku, Resource Health analyzuje další informace a určí zdroj problému. Také identifikuje akce, které Microsoft provádí, nebo co můžete udělat k vyřešení problému.

Další podrobnosti o tom, jak je stav hodnocen, najdete v úplném seznamu typů prostředků a kontrol stavu v [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


Stav pro Application Gateway se zobrazuje jako jeden z následujících stavů:

## <a name="available"></a>K dispozici

**Dostupný** stav znamená, že služba nezjistila žádné události, které mají vliv na stav prostředku. V případě, že se brána v posledních 24 hodinách obnovila z neplánovaného výpadku, uvidíte **nedávno vyřešené** oznámení.

![Dostupný stav](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Neaktivní

**Nedostupný** stav znamená, že služba zjistila trvalou platformu nebo jinou událost než platformu, která má vliv na stav brány.

### <a name="platform-events"></a>Události platformy

Události platformy se spouštějí více komponentami infrastruktury Azure. Zahrnují jak plánované akce (například plánovaná údržba), tak neočekávané incidenty (například neplánovaný restart hostitele).

Resource Health poskytuje další podrobnosti o události a procesu obnovení. Také vám umožňuje kontaktovat podporu i v případě, že nemáte aktivní smlouvu o podpoře Microsoftu.

![Nedostupný stav](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Neznámý

**Neznámý** stav stavu indikuje, Resource Health nedostaly informace o bráně déle než 10 minut. Tento stav není konečným náznakem stavu brány. Ale jedná se o důležitý datový bod v procesu řešení potíží.

Pokud brána běží podle očekávání, stav se změní na **k dispozici** po několika minutách.

Pokud se setkáváte s problémy, **Neznámý** stav může navrhnout, aby brána měla vliv na událost v dané platformě.

![Neznámý stav](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Snížený výkon

Stav **Degraded** znamená, že brána zjistila ztrátu výkonu, i když je stále k dispozici pro použití.

![Stav s odmřížkou](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží s Application Gateway firewallu webových aplikací (WAF) najdete v tématu [řešení potíží s bránou firewall webových aplikací (WAF) pro Azure Application Gateway](../web-application-firewall/ag/web-application-firewall-troubleshoot.md).