---
title: Přehled stavu prostředků aplikační brány Azure
description: Tento článek je přehled funkce stavu prostředků pro Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67659499"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Přehled stavu prostředků aplikační brány Azure

[Azure Resource Health](../service-health/resource-health-overview.md) pomáhá s diagnostikou a získáním podpory v případě, že problém se službou Azure ovlivňuje vaše prostředky. Informuje vás o aktuálním a minulém stavu vašich zdrojů. A poskytuje technickou podporu, která vám pomůže zmírnit problémy.

Pro aplikační brány resource health spoléhá na signály vyzařované bránou k posouzení, zda je v pořádku nebo ne. Pokud brána není v pořádku, resource health analyzuje další informace k určení zdroje problému. Identifikuje také akce, které společnost Microsoft provádí, nebo co můžete udělat pro vyřešení problému.

Další podrobnosti o tom, jak se hodnotí stav, najděte úplný seznam typů prostředků a kontroly stavu v [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


Stav brány aplikace se zobrazí jako jeden z následujících stavů:

## <a name="available"></a>K dispozici.

Stav **K dispozici** znamená, že služba nezjistila žádné události, které ovlivňují stav prostředku. Oznámení **naposledy vyřešené** se zobrazí v případech, kdy se brána během posledních 24 hodin zotavila z neplánovaných prostojů.

![Dostupný stav](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Neaktivní

**Nedostupný** stav znamená, že služba zjistila probíhající událost platformy nebo jiné platformy, která ovlivňuje stav brány.

### <a name="platform-events"></a>Události platformy

Události platformy se aktivují více komponent infrastruktury Azure. Zahrnují jak naplánované akce (například plánovanou údržbu), tak neočekávané incidenty (například neplánované restartování hostitele).

Resource Health poskytuje další podrobnosti o události a procesu obnovení. Umožňuje také kontaktovat podporu i v případě, že nemáte aktivní smlouvu podpory společnosti Microsoft.

![Nedostupný stav](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Není známo

**Neznámý** stav označuje, že stav prostředků neobdržel informace o bráně déle než 10 minut. Tento stav není definitivní údaj o stavu brány. Ale je to důležitý datový bod v procesu řešení potíží.

Pokud brána běží podle očekávání, stav se po několika minutách změní na **K dispozici.**

Pokud máte problémy, neznámý **stav** může naznačovat, že událost na platformě ovlivňuje bránu.

![Neznámý stav](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Snížený výkon

**Stav zhoršený** označuje, že brána zjistila ztrátu výkonu, i když je stále k dispozici pro použití.

![Degratovaný stav](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží s bránou webových aplikací firewall (WAF), [najdete v tématu Poradce při potížích s bránou webových aplikací firewall (WAF) pro Azure Application Gateway](web-application-firewall-troubleshoot.md).