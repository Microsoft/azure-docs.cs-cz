---
title: Přehled Event Grid mezipaměti Azure pro Redis
description: K publikování mezipaměti Azure pro události Redis použijte Azure Event Grid.
author: curib
ms.author: cauribeg
ms.date: 12/21/2020
ms.topic: conceptual
ms.service: cache
ms.openlocfilehash: 0a0809076367356739dfeadcf8dd63f88866a987
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055840"
---
# <a name="azure-cache-for-redis-event-grid-overview"></a>Přehled Event Grid mezipaměti Azure pro Redis 

Mezipaměť Azure pro události Redis, jako jsou třeba opravy, škálování, import/export (RDB), se připravují pomocí [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) pro předplatitele, jako je Azure Functions, Azure Logic Apps nebo dokonce vlastní naslouchací proces http. Event Grid poskytuje aplikacím spolehlivé doručování událostí prostřednictvím zásad opakovaného opakování a nedoručených zpráv.

Úplný seznam událostí, které podporuje mezipaměť Azure pro Redis, najdete v článku věnovaném [schématu Azure cache pro události Redis](../event-grid/event-schema-azure-cache.md) .

Pokud chcete vyzkoušet Azure cache pro události Redis, podívejte se na některé z těchto rychlých startů:

|Pokud chcete použít tento nástroj:    |Přečtěte si tento rychlý Start: |
|--|-|
|portál Azure    |[Rychlý Start: směrování mezipaměti Azure pro události Redis do webového koncového bodu pomocí Azure Portal](cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Rychlý Start: směrování mezipaměti Azure pro události Redis do webového koncového bodu pomocí PowerShellu](cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[Rychlý Start: směrování mezipaměti Azure pro události Redis do webového koncového bodu pomocí Azure CLI](cache-event-grid-quickstart-cli.md)|

## <a name="the-event-model"></a>Model události

Event Grid používá [odběry událostí](../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí předplatitelům. Tento obrázek znázorňuje vztah mezi vydavateli událostí, odběry událostí a obslužnými rutinami událostí.

:::image type="content" source="media/cache-event-grid/event-grid-model.png" alt-text="Model Event Grid.":::

Nejdřív se přihlaste k odběru koncového bodu události. Po aktivaci události pak služba Event Grid odešle data o této události do koncového bodu.

Podívejte se na článek o [schématu Azure cache pro události Redis](../event-grid/event-schema-azure-cache.md) , který se zobrazí:

> [!div class="checklist"]
> * Úplný seznam mezipaměti Azure pro události Redis a způsob aktivace každé události.
> * Příklad dat, která Event Grid odeslat pro každou z těchto událostí.
> * Účel dvojice klíč-hodnota, která se zobrazí v datech.


## <a name="best-practices-for-consuming-events"></a>Osvědčené postupy pro využívání událostí

Aplikace, které zpracovávají službu Azure cache pro události Redis, by měly dodržovat několik doporučených postupů:
> [!div class="checklist"]
> * Vzhledem k tomu, že je možné nakonfigurovat více předplatných pro směrování událostí ke stejné obslužné rutině události, je důležité, aby události nevznikly z konkrétního zdroje, ale pokud chcete zkontrolovat téma zprávy, abyste měli jistotu, že pochází z mezipaměti Azure pro Redis instanci, kterou očekáváte.
> * Podobně ověřte, zda je typ eventType, který je připraven ke zpracování, a nepředpokládá se, že všechny události, které obdržíte, budou takové typy, které očekáváte.
> * Azure cache pro události Redis garantuje pro předplatitele alespoň jedno doručení, což zajišťuje, aby byly všechny zprávy vydány. Vzhledem k opakování nebo dostupnosti předplatných ale může občas docházet k duplicitním zprávám. Pokud se chcete dozvědět víc o doručování zpráv a zkusit to znovu, přečtěte si téma [Event Grid doručování zpráv](../event-grid/delivery-and-retry.md).


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o Event Grid a poskytněte Azure cache pro události Redis a zkuste:

- [Informace o službě Event Grid](../event-grid/overview.md)
- [Mezipaměť Azure pro schéma událostí Redis](../event-grid/event-schema-azure-cache.md)
- [Směrování Azure cache pro události Redis do webového koncového bodu pomocí Azure CLI](cache-event-grid-quickstart-cli.md)
- [Směrování Azure cache pro události Redis do webového koncového bodu pomocí Azure Portal](cache-event-grid-quickstart-portal.md)
- [Směrování Azure cache pro události Redis do webového koncového bodu pomocí PowerShellu](cache-event-grid-quickstart-powershell.md)
