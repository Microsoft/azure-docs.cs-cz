---
title: Běžné chyby
description: Tento dokument obsahuje informace o běžných chybách, které existují na řídicím panelu.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: be6d47d8f40746bfb2154ddb62cf2e9ce93e74aa
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955679"
---
# <a name="errors-in-the-connector-status-section"></a>Chyby v sekci stav konektoru

V části seznam stavů konektoru na řídicím panelu najdete chyby, které vám můžou pomoct vyřešit problémy v konektoru ITSM.

## <a name="status-common-errors"></a>Běžné chyby stavu

V této části najdete běžné chyby, které se zobrazují v části stav konektoru, a způsob jejich řešení:

* **Chyba**: "Neočekávaná odpověď z ServiceNow spolu s kódem stavu úspěch. Odpověď: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "Result": [{"transform_map": "incident OMS", "Tabulka": "incident", "stav": "Chyba", "error_message": "{cílový záznam nebyl nalezen | Neplatná tabulka | Neplatná pracovní tabulka "}"

    **Příčina**: Tato chyba se vrátí z ServiceNow, pokud:
  * Vlastní skript nasazený v instanci ServiceNow způsobí, že se incidenty budou ignorovat.
  * Samotný kód aplikace pro integrátory OMS byl změněn na straně ServiceNow, například skript před.

  **Řešení**: zakažte všechny vlastní skripty nebo změny kódu.

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
  * Najde a zakáže nebo odstraní tyto skupiny akcí.
  * [Překonfigurujte skupinu akcí](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) tak, aby používala existující konektor ITSM.
  * [Vytvořte nový konektor ITSM](./itsmc-definition.md#create-an-itsm-connection) a [znovu nakonfigurujte skupinu akcí, kterou chcete použít](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="ui-common-errors"></a>Běžné chyby uživatelského rozhraní

* **Chyba**: něco se pokazilo. Nepovedlo se získat podrobnosti o připojení. Tato chyba se zobrazí, když zákazník definuje skupinu akcí ITSM.

    **Příčina**: Tato chyba se zobrazí, když:
    * Nově vytvořený konektor ITSM ještě pro dokončení počáteční synchronizace.
    * Konektor nebyl správně definován.

    **Řešení**: 
    * Při vytvoření nového konektoru ITSM spustí konektor ITSM synchronizaci informací ze systému ITSM, jako jsou šablony pracovních položek a pracovní položky. Synchronizujte konektor ITSM pro vygenerování nového obnovovacího tokenu, jak je vysvětleno [zde](./itsmc-resync-servicenow.md).
    * Projděte si podrobnosti o připojení v konektoru ITSM, jak je popsáno [zde](./itsmc-connections-servicenow.md#create-a-connection) , a ověřte, že se váš konektor ITSM může úspěšně [synchronizovat](./itsmc-resync-servicenow.md).
