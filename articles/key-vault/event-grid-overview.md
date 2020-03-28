---
title: Monitorování trezoru klíčů pomocí Azure Event Grid
description: Přihlášení k odběru událostí trezoru klíčů pomocí Služby Azure Event Grid
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 039b7e57c1f98368ab33f9f17e19a741dc9eb5f4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78184906"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>Monitorování trezoru klíčů pomocí Azure Event Grid (preview)

Integrace trezoru klíčů s mřížkou událostí je aktuálně ve verzi preview. Umožňuje uživatelům být upozorněni, když se změní stav tajného klíče uloženého v trezoru klíčů. Změna stavu je definována jako tajný klíč, jehož platnost brzy vyprší (do 30 dnů od vypršení platnosti), tajný klíč, jehož platnost vypršela, nebo tajný klíč, který má k dispozici novou verzi. Oznámení pro všechny tři typy tajných kódů (klíč, certifikát a tajný klíč) jsou podporovány.

Aplikace mohou reagovat na tyto události pomocí moderních architektur bez serveru, bez nutnosti složitého kódu nebo nákladné a neefektivní dotazování služby. Události se přes [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) zasouvají obslužným rutinám událostí, jako jsou Azure [Functions](https://azure.microsoft.com/services/functions/), Azure [Logic Apps](https://azure.microsoft.com/services/logic-apps/)nebo dokonce do vlastního Webhooku, a platíte jenom za to, co používáte. Informace o cenách naleznete v [tématu Event Grid pricing](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Události a schémata úložiště klíčů

Mřížka událostí používá [odběry událostí](../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí odběratelům. Události úložiště klíčů obsahují všechny informace, které potřebujete k reakci na změny dat. Můžete identifikovat key vault událost, protože eventType vlastnost začíná "Microsoft.KeyVault".

Další informace naleznete ve [schématu událostí trezoru klíčů](../event-grid/event-schema-key-vault.md).

> [!WARNING]
> Události oznámení se aktivují pouze v nových verzích tajných kódů, klíčů a certifikátů a musíte se nejprve přihlásit k odběru události v trezoru klíčů, abyste tato oznámení obdrželi.
> 
> Události oznámení o certifikátech obdržíte pouze v případě, že je certifikát automaticky obnoven podle zásad, které jste pro certifikát zadali.

## <a name="practices-for-consuming-events"></a>Postupy pro náročné události

Aplikace, které zpracovávají události úložiště klíčů, by se měly řídit několika doporučenými postupy:

* Více předplatných lze nakonfigurovat pro směrování událostí na stejnou obslužnou rutinu události. Je důležité nepředpokládat, že události pocházejí z určitého zdroje, ale zkontrolujte téma zprávy, abyste se ujistili, že pochází z trezoru klíčů, který očekáváte.
* Podobně zkontrolujte, zda eventType je ten, který jste připraveni ke zpracování a nepředpokládejte, že všechny události, které obdržíte, budou typy, které očekáváte.
* Ignorujte pole, kterým nerozumíte.  Tento postup vám pomůže udržet odolnost vůči novým funkcím, které by mohly být přidány v budoucnu.
* Pomocí shody předpony a přípony předmětu omezte události na určitou událost.

## <a name="next-steps"></a>Další kroky

- [Azure Key Vault – přehled](key-vault-overview.md)
- [Přehled Azure Event Gridu](../event-grid/overview.md)
- Postup: [Trasa událostí trezoru klíčů do runbooku automation (preview)](event-grid-tutorial.md).
- Postup: [Příjem e-mailů při změně tajného klíče trezoru](event-grid-logicapps.md)
- [Schéma událostí Azure Event Grid pro Azure Key Vault (preview)](../event-grid/event-schema-key-vault.md)
- [Azure Automation – přehled](../automation/index.yml)
