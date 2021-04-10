---
title: Chyby stavu konektoru na řídicím panelu ITSMC
description: Seznamte se s běžnými chybami, které existují na řídicím panelu IT Service Management Connector.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 727e744c59d0a8d90cf320e1ee2e2a17e10ff847
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103471525"
---
# <a name="connector-status-errors-in-the-itsmc-dashboard"></a>Chyby stavu konektoru na řídicím panelu ITSMC

Řídicí panel ITSMC (IT Service Management Connector) obsahuje chyby, které vám můžou pomoct při řešení problémů ve vašem konektoru.

V následujících částech jsou popsány běžné chyby, které se zobrazí v části stav konektoru na řídicím panelu a jak je můžete vyřešit.

## <a name="unexpected-response"></a>Neočekávaná odpověď

**Chyba**: "Neočekávaná odpověď z ServiceNow spolu s kódem stavu úspěch. Odpověď: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "Result": [{"transform_map": "incident OMS", "Tabulka": "incident", "stav": "Chyba", "error_message": "{cílový záznam nebyl nalezen | Neplatná tabulka | Neplatná pracovní tabulka "}"

**Příčina**: ServiceNow vrátí tuto chybu, když:

* Vlastní skript nasazený v instanci ServiceNow způsobí, že se incidenty budou ignorovat.
* Kód aplikace pro integrátory OMS byl změněn na straně ServiceNow (například prostřednictvím `onBefore` skriptu).

**Řešení**: zakažte všechny vlastní skripty nebo změny kódu.

## <a name="exception-update-failure"></a>Chyba aktualizace pro výjimku

**Chyba**: "{" Chyba ": {" zpráva ":" operace selhala "," Detail ":" aktualizace výjimky ACL se nezdařila z důvodu omezení zabezpečení "}"

**Příčina**: ServiceNow oprávnění jsou nesprávně nakonfigurovaná.

**Řešení**: Ověřte, zda jsou všechny role správně přiřazeny podle [zadání](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).

## <a name="problem-sending-a-request"></a>Problém s odesláním žádosti

**Chyba**: při odesílání žádosti došlo k chybě.

**Příčina**: instance ServiceNow není k dispozici.

**Řešení**: Podívejte se na vaši instanci v ServiceNow. Může být odstraněn nebo není k dispozici.

## <a name="servicenow-rate-problem"></a>Problém ServiceNow míry

**Chyba**: ServiceDeskHttpBadRequestException: StatusCode = 429

**Příčina**: omezení četnosti ServiceNow jsou příliš vysoká nebo příliš nízká.

**Řešení**: Zvyšte nebo zrušte omezení přenosové rychlosti v instanci ServiceNow, jak je vysvětleno v [dokumentaci k ServiceNow](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html).

## <a name="invalid-refresh-token"></a>Neplatný obnovovací token

**Chyba**: 
  * "AccessToken a Refreshtoken kontextového tokenu jsou neplatné. Uživatel se musí znovu ověřit. "
  * "Nepovedlo se synchronizovat konfiguraci šablon pro událost, výstrahu, incident. Další podrobnosti naleznete ve zprávě výjimky. "

**Příčina**: platnost obnovovacího tokenu vypršela.

**Řešení**: Sync ITSMC k vygenerování nového obnovovacího tokenu, jak je vysvětleno v tématu [Ruční oprava problémů s synchronizací](./itsmc-resync-servicenow.md).

## <a name="missing-connector"></a>Chybějící konektor

**Chyba**: nepovedlo se vytvořit/aktualizovat pracovní položku pro upozornění {Alert}. Konektor ITSM {connectionIdentifier} neexistuje nebo byl odstraněn. "

**Příčina**: ITSMC se odstranil.

**Řešení**: ITSMC se odstranilo, ale definovali jste taky skupiny akcí správy služeb IT (ITSM), které jsou k němu pořád přidružené. Existují tři možnosti, jak tento problém vyřešit:

* Vyhledejte a zakažte nebo odstraňte tyto skupiny akcí.
* [Překonfigurujte skupiny akcí](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) tak, aby používaly existující instanci ITSMC.
* [Vytvořte novou instanci ITSMC](./itsmc-definition.md#create-an-itsm-connection) a [překonfigurujte skupiny akcí tak, aby se použily](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="lack-of-connection-details"></a>Nedostatek podrobností o připojení

**Chyba**: něco se pokazilo. Nepovedlo se získat podrobnosti o připojení. Tato chyba se zobrazí při definování skupiny akcí ITSM.

**Příčina**: Tato chyba se zobrazí v některé z těchto situací:

* Nově vytvořená instance konektoru ITSM ještě před dokončením počáteční synchronizace.
* Konektor nebyl správně definován.

**Řešení**: 

* Při vytvoření nové instance ITSMC spustí synchronizaci informací ze systému ITSM, jako jsou šablony pracovních položek a pracovní položky. [Synchronizuje ITSMC pro vygenerování nového obnovovacího tokenu](./itsmc-resync-servicenow.md).
* [Zkontrolujte podrobnosti o připojení v ITSMC](./itsmc-connections-servicenow.md#create-a-connection) a zkontrolujte, jestli se ITSMC může úspěšně [synchronizovat](./itsmc-resync-servicenow.md).


## <a name="ip-restrictions"></a>Omezení IP adresy
**Chyba**: nepovedlo se přidat připojení ITSM s názvem xxx z důvodu chybné žádosti. Chyba: Chybný požadavek. Pro připojení byly zadány neplatné parametry. Výjimka protokolu http: kód stavu je zakázán.

**Příčina**: IP adresa aplikace ITSM nepovoluje připojení ITSM od partnerů ITSM Tools.

**Řešení**: Pokud chcete zobrazit seznam IP adres ITSM, aby bylo možné ITSM připojení od partnerů ITSM Tools, doporučujeme, abyste si vyhodnotili celý rozsah veřejných IP adres v oblasti Azure, do které patří pracovní prostor LogAnalytics. [Podrobnosti](https://www.microsoft.com/download/details.aspx?id=56519) Pro oblasti EUS/ZEU/EUS2/WUS2/USA (střed) – jih může zákazník zobrazit pouze značku Network ve službě Action.
