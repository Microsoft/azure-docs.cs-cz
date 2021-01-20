---
title: Běžné chyby
description: Tento dokument obsahuje informace o běžných chybách, které existují na řídicím panelu.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 7240c1b0f19dc49ab4130c5ee2516dcfefb2e2c2
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602183"
---
# <a name="errors-in-the-connector-status"></a>Chyby ve stavu konektoru

V seznamu stav konektoru můžete najít chyby, které vám můžou pomoct vyřešit problémy v konektoru ITSM.

## <a name="status-common-errors"></a>Běžné chyby stavu

v této části najdete běžné chyby, které se zobrazují v části stav konektoru, a způsob jejich řešení:

* **Chyba**: "Neočekávaná odpověď z ServiceNow spolu s kódem stavu úspěch. Odpověď: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "Result": [{"transform_map": "incident OMS", "Tabulka": "incident", "stav": "Chyba", "error_message": "{cílový záznam nebyl nalezen | Neplatná tabulka | Neplatná pracovní tabulka "}"

    **Příčina**: Tato chyba se vrátí z ServiceNow, pokud:
  * Vlastní skript nasazený v instanci ServiceNow způsobí, že se incidenty budou ignorovat.
  * Samotný kód aplikace pro integrátory OMS byl změněn na straně ServiceNow, například skript před.

    **Řešení**: zakažte všechny vlastní skripty nebo změny kódu v cestě pro import dat.

* **Chyba**: "{" Chyba ": {" zpráva ":" operace selhala "," Detail ":" aktualizace výjimky ACL se nezdařila z důvodu omezení zabezpečení "}"

    **Příčina**: nesprávná konfigurace oprávnění ServiceNow

    **Řešení**: Ověřte, zda byly všechny role správně přiřazeny dle [zadání](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).

* **Chyba**: při odesílání žádosti došlo k chybě.

    **Příčina**: "ServiceNow instance není k dispozici"

    **Řešení**: Podívejte se na vaši instanci v ServiceNow, že se může odstranit nebo není k dispozici.

* **Chyba**: ServiceDeskHttpBadRequestException: StatusCode = 429

    **Příčina**: omezení četnosti ServiceNow jsou příliš vysoká/nízká.

    **Řešení**: Zvyšte nebo zrušte omezení přenosové rychlosti v instanci ServiceNow, jak je popsáno [zde](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html).

* **Chyba**: AccessToken a refreshtoken kontextového tokenu jsou neplatné. Uživatel se musí znovu ověřit. "

    **Příčina**: vypršela platnost tokenu pro aktualizaci.

    **Řešení**: synchronizace konektoru ITSM pro vygenerování nového obnovovacího tokenu, jak je vysvětleno [zde](./itsmc-resync-servicenow.md).

* **Chyba**: nepovedlo se vytvořit/aktualizovat pracovní položku pro upozornění {Alert}. Konektor ITSM {connectionIdentifier} neexistuje nebo byl odstraněn. "

    **Příčina**: konektor ITSM byl odstraněn.

    **Řešení**: konektor ITSM byl odstraněn, ale jsou stále definovány skupiny akcí ITSM, které jsou k němu přidruženy. Existují dvě možnosti, jak tento problém vyřešit:
  * Najde a zakáže nebo odstraní takovou akci.
  * [Překonfigurujte skupinu akcí](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) tak, aby používala existující konektor ITSM.
  * [Vytvořte nový konektor ITSM](./itsmc-definition.md#create-an-itsm-connection) a [znovu nakonfigurujte skupinu akcí, kterou chcete použít](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="ui-common-errors"></a>Běžné chyby uživatelského rozhraní

* **Chyba**: něco se pokazilo. Nepovedlo se získat podrobnosti o připojení. Tato chyba se zobrazí, když zákazník definuje skupinu akcí ITSM.

    **Příčina**: u nově vytvořeného KONEKTORu ITSM se ještě dokončila počáteční synchronizace.

    **Řešení**: při vytvoření nového konektoru ITSM SPUSTÍ konektor ITSM synchronizaci informací ze systému ITSM, jako jsou šablony pracovních položek a pracovní položky. Synchronizujte konektor ITSM pro vygenerování nového obnovovacího tokenu, jak je vysvětleno [zde](./itsmc-resync-servicenow.md).
