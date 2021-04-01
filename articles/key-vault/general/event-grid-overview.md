---
title: Monitorování Key Vault s využitím Azure Event Grid
description: Použití Azure Event Grid k přihlášení k odběru Key Vaultch událostí
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 1ee38196f0b1a6e00d385dc0d2c88d45a4291d3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90087432"
---
# <a name="monitoring-key-vault-with-azure-event-grid"></a>Monitorování Key Vault s využitím Azure Event Grid

Key Vault integrace s Event Grid umožňuje uživatelům upozornit na změnu stavu tajného klíče uloženého v trezoru klíčů. Změna stavu se definuje jako tajný kód, jehož platnost brzy vyprší (30 dní před vypršením platnosti), tajného kódu, jehož platnost vypršela, nebo tajný klíč, který má k dispozici novou verzi. Jsou podporovány oznámení pro všechny tři tajné typy (klíč, certifikát a tajný kód).

Aplikace můžou na tyto události reagovat pomocí moderních architektur bez serveru, aniž by museli mít složitý kód nebo nákladné a neefektivní služby cyklického dotazování. Události jsou odesílány prostřednictvím [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do obslužných rutin událostí, jako jsou [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)nebo dokonce vlastního Webhooku, a platíte jenom za to, co využijete. Informace o cenách najdete v tématu [Event Grid ceny](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Key Vault události a schémata

Event Grid používá [odběry událostí](../../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí odběratelům. Key Vault události obsahují všechny informace, které potřebujete k reakci na změny ve vašich datech. Můžete identifikovat událost Key Vault, protože vlastnost eventType začíná na "Microsoft. klíčů trezor".

Další informace najdete v tématu [schéma události Key Vault](../../event-grid/event-schema-key-vault.md).

> [!WARNING]
> Události oznámení se aktivují jenom v nových verzích tajných klíčů, klíčů a certifikátů a při přijímání těchto oznámení se musíte nejdřív přihlásit ke svému trezoru klíčů.

## <a name="practices-for-consuming-events"></a>Postupy pro náročné události

Aplikace, které zpracovávají Key Vault události, by měly dodržovat několik doporučených postupů:

* Více předplatných lze nakonfigurovat pro směrování událostí do stejné obslužné rutiny události. Je důležité, aby se události nepředpokládaly z konkrétního zdroje, ale pokud chcete zkontrolovat téma zprávy, abyste měli jistotu, že pochází z trezoru klíčů, který očekáváte.
* Podobně ověřte, zda je typ eventType, který je připraven ke zpracování, a nepředpokládá se, že všechny události, které obdržíte, budou takové typy, které očekáváte.
* Ignorujte pole, která nerozumíte.  Tento postup vám pomůže zajistit odolný přístup k novým funkcím, které se v budoucnu můžou přidat.
* Pokud chcete omezit události na konkrétní událost, použijte předponu a příponu "Subject".

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Key Vault](overview.md)
- [Přehled Azure Event Gridu](../../event-grid/overview.md)
- Postupy: [Směrování událostí Key Vault do Runbooku služby Automation](event-grid-tutorial.md).
- Postupy: [příjem e-mailu, když se změní tajný kód trezoru klíčů](event-grid-logicapps.md)
- [Azure Event Grid schéma událostí pro Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Přehled Azure Automation](../../automation/index.yml)
