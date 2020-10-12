---
title: Dosažení geografické redundance pro úlohy Azure Stream Analytics
description: Tento článek popisuje, jak dosáhnout geografické redundance Azure Stream Analytics úloh místo geografického převzetí služeb při selhání.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/26/2020
ms.openlocfilehash: 44b0394773485f8054eddc94c7d7e9b91baebc6d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88951090"
---
# <a name="achieve-geo-redundancy-for-azure-stream-analytics-jobs"></a>Dosažení geografické redundance pro úlohy Azure Stream Analytics

Azure Stream Analytics nezajišťuje automatické geografické převzetí služeb při selhání, ale můžete dosáhnout geografické redundance tím, že nasadíte stejné Stream Analytics úlohy do několika oblastí Azure. Každá úloha se připojuje k místnímu vstupnímu a místnímu výstupnímu zdroji. Je zodpovědností vaší aplikace na posílání vstupních dat do dvou regionálních vstupů a sladění mezi dvěma místními výstupy. Úlohy Stream Analytics jsou dvě samostatné entity.

Následující diagram znázorňuje ukázkovou geograficky redundantní Stream Analyticsou úlohu nasazení pomocí vstupu centra událostí a výstupu Azure Database.

:::image type="content" source="media/geo-redundancy/geo-redundant-jobs.png" alt-text="Diagram geograficky redundantních úloh Stream Analytics":::

## <a name="primarysecondary-strategy"></a>Primární a sekundární strategie

Vaše aplikace musí spravovat výstupní databázi této oblasti, která je považována za primární a která je považována za sekundární. Při selhání primární oblasti se aplikace přepne do sekundární databáze a začne číst aktualizace z této databáze. Skutečný mechanismus, který umožňuje minimalizaci duplicitních čtení, závisí na vaší aplikaci.Tento proces můžete zjednodušit zapsáním dalších informací do výstupu. Můžete například přidat časové razítko nebo pořadové číslo do každého výstupu, aby bylo přeskočení duplicitních řádků. Po obnovení primární oblasti se zachytí se sekundární databází pomocí podobného mechanismu.

I když různé vstupní a výstupní typy umožňují různé možnosti geografické replikace, doporučujeme použít vzor, který je popsaný v tomto článku, abyste dosáhli geografické redundance, protože poskytuje flexibilitu a kontrolu nad výrobci událostí i spotřebiteli událostí.

## <a name="next-steps"></a>Další kroky

* [Monitorování a správa úloh Azure Stream Analytics pomocí prostředí PowerShell](stream-analytics-monitor-and-manage-jobs-use-powershell.md)
* [Ladění řízené daty v Azure Stream Analytics](stream-analytics-job-diagram-with-metrics.md)