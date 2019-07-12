---
title: Přehled služby Azure Application Gateway Resource Health
description: Tento článek představuje přehled funkce stavu prostředků pro službu Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659499"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Přehled služby Azure Application Gateway Resource Health

[Azure Resource Health](../service-health/resource-health-overview.md) pomáhá při diagnostice a získání podpory v případě, že kvůli problému služba Azure ovlivňuje vaše prostředky. Informuje vás o aktuálním a minulém stavu vašich prostředků. A poskytuje technickou podporu, abychom vám zmírnit problémy.

Pro službu Application Gateway Resource Health spoléhá na signály, protože ho vygeneroval brány k vyhodnocení, zda je v pořádku, či nikoli. Pokud brána není v pořádku, Resource Health analyzuje dodatečné informace k určení příčiny problému. Také identifikuje akce, které společnost Microsoft provádí nebo co můžete udělat, abyste tento problém vyřešit.

Pro další podrobnosti o tom, jak se bude hodnotit stavu, zkontrolujte úplný seznam typů prostředků a kontrolách stavu ve službě [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


Stav služby Application Gateway se zobrazí jako jedna z následujících stavů:

## <a name="available"></a>K dispozici

**Dostupné** stav znamená, že služba nezjistila žádné události, které ovlivňují stav prostředku. Zobrazí se vám **nedávno Vyřešeno** oznámení v případech, kde se brána zotavil z neplánovaných výpadků za posledních 24 hodin.

![K dispozici stav](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Není dostupný

**Není k dispozici** stav znamená, že služba zjistila aktuálnímu platformu nebo jiných platforem událost, která má vliv na stav brány.

### <a name="platform-events"></a>Události platformy

Události platformy jsou aktivovány více komponent infrastruktury Azure. Patří mezi ně naplánované akce (například plánovaná údržba) i neočekávané incidentů (například restartování neplánované hostitele).

Služba Resource Health poskytuje další podrobnosti o události a proces obnovení. Také umožňuje i v případě, že nemáte k dispozici aktivní smlouvu podpory společnosti Microsoft, kontaktujte podporu.

![Stav není k dispozici](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Neznámé

**Neznámý** označuje stav Resource Health neobdržel informace o bráně pro více než 10 minut. Tento stav není úplným a rozhodujícím Indikace stavu brány. Ale to je důležitý datový bod v procesu odstraňování potíží.

Pokud je brána spuštěná podle očekávání, stav se změní na **dostupné** za pár minut.

Pokud máte problémy, **neznámý** stav může naznačovat, že událost platformy ovlivňuje brány.

![Neznámý stav](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Sníženo

**Snížený** stav znamená bránu byl nalezen ke ztrátě výkonu, i když je stále k dispozici pro použití.

![Degrated stav](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží s Application Gateway Firewall webových aplikací (WAF), najdete v článku [řešení Firewall webových aplikací (WAF) služby Azure Application Gateway](web-application-firewall-troubleshoot.md).