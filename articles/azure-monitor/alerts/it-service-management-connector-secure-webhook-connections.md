---
title: IT Service Management Connector – zabezpečený export v Azure Monitor
description: V tomto článku se dozvíte, jak připojit své ITSM produkty/služby k zabezpečenému exportu v Azure Monitor centrálně monitorovat a spravovat pracovní položky ITSM.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 330b97e66ba1a9b95f2ef7a34a7ea046414b8bd8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037508"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Připojení Azure k nástrojům ITSM pomocí zabezpečeného exportu

V tomto článku se dozvíte, jak nakonfigurovat připojení mezi produktem nebo službou IT Service Management (ITSM) pomocí zabezpečeného exportu.

Zabezpečený export je aktualizovaná verze [konektoru IT Service Management Connector (ITSMC)](./itsmc-overview.md). Obě verze umožňují vytvářet pracovní položky v nástroji ITSM, když Azure Monitor odesílá výstrahy. Mezi tyto funkce patří upozornění na metriky, protokoly a protokoly aktivit.

ITSMC používá přihlašovací údaje uživatelského jména a hesla. Zabezpečený export má silnější ověřování, protože používá Azure Active Directory (Azure AD). Azure AD je cloudová služba Microsoftu, která slouží ke správě identit a přístupu. Pomůže uživatelům přihlašovat se k interním nebo externím prostředkům a přistupovat k nim. Použití Azure AD s ITSM pomáhá identifikovat výstrahy Azure (prostřednictvím ID aplikace Azure AD), které se poslaly do externího systému.

> [!NOTE]
> Možnost připojit Azure k nástrojům ITSM pomocí zabezpečeného exportu je ve verzi Preview.

## <a name="secure-export-architecture"></a>Architektura zabezpečeného exportu

Architektura zabezpečeného exportu zavádí následující nové funkce:

* **Nová skupina akcí**: výstrahy se odesílají do nástroje ITSM prostřednictvím skupiny akcí zabezpečeného Webhooku místo skupiny akcí ITSM, kterou ITSMC používá.
* **Ověřování Azure AD**: ověřování probíhá přes Azure AD místo přihlašovacích údajů uživatelského jména a hesla.

## <a name="secure-export-data-flow"></a>Tok dat zabezpečeného exportu

Postup pro tok dat zabezpečeného exportu je následující:

1. Azure Monitor odešle výstrahu, která je nakonfigurována pro použití zabezpečeného exportu.
2. Datová část výstrahy je odeslána zabezpečenou akcí Webhooku nástroji ITSM.
3. Aplikace ITSM zkontroluje ve službě Azure AD, pokud je výstraha autorizována k zadání nástroje ITSM.
4. Pokud je výstraha autorizována, aplikace:
   
   1. Vytvoří pracovní položku (například incident) v nástroji ITSM.
   2. Váže ID položky konfigurace (CI) k databázi správy zákazníka (CMDB).

![Diagram, který ukazuje, jak nástroj ITSM komunikuje s Azure a D, upozorněními Azure a skupinou akcí.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>Výhody zabezpečeného exportu

Hlavní výhodou integrace jsou tyto:

* **Lepší ověřování**: Azure AD poskytuje bezpečnější ověřování bez časových limitů, které se běžně vyskytují v ITSMC.
* **Upozornění vyřešená v nástroji ITSM**: výstrahy metrik implementují stavy "Trigger" a "Vyřešeno". Při splnění podmínky je stav výstrahy "aktivováno". Pokud podmínka není splněna, stav výstrahy bude "Vyřešeno". V ITSMC se výstrahy nedají automaticky vyřešit. V případě zabezpečeného exportu převedený stav vyřešen do nástroje ITSM, a proto je automaticky aktualizován.
* **[Běžné schéma výstrah](./alerts-common-schema.md)**: v ITSMC se schéma datové části výstrahy liší v závislosti na typu výstrahy. V zabezpečeném exportu existuje společné schéma pro všechny typy výstrah. Toto společné schéma obsahuje CI pro všechny typy výstrah. Všechny typy výstrah budou moci navazovat svou CI pomocí CMDB.

## <a name="next-steps"></a>Další kroky

* [Vytváření pracovních položek ITSM z výstrah Azure](./itsmc-overview.md)