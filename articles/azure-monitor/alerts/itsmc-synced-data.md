---
title: Data synchronizovaná z vašeho produktu ITSM do pracovního prostoru LA
description: Tento článek poskytuje přehled dat synchronizovaných z vašeho ITSM produktu do pracovního prostoru LA.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: fd570950190ceabac413aca2d68368e5e722a3da
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609531"
---
# <a name="data-synced-from-your-itsm-product"></a>Data synchronizovaná z produktu ITSM

Incidenty a žádosti o změnu se synchronizují z nástroje ITSM do pracovního prostoru Log Analytics v závislosti na konfiguraci připojení (pomocí pole pro synchronizaci dat):
* [ServiceNow](./itsmc-connections-servicenow.md)
* [System Center Service Manager](./itsmc-connections-scsm.md)
* [Cherwell](./itsmc-connections-cherwell.md)
* [Provance](./itsmc-connections-provance.md)

## <a name="synced-data"></a>Synchronizovaná data

V této části jsou uvedeny některé příklady dat shromažďovaných nástrojem ITSMC.

Pole v **ServiceDesk_CL** se liší v závislosti na typu pracovní položky, který importujete do Log Analytics. Zde je seznam polí pro dva typy pracovních položek:

**Pracovní položka:** **incidenty**  
ServiceDeskWorkItemType_s = incident

**Pole**

- ServiceDeskConnectionName
- ID oddělení služeb
- State
- Naléhavost
- Dopad
- Priorita
- Eskalace
- Created By
- Vyřešil
- Uzavřel
- Zdroj
- Přiřazený pro
- Kategorie
- Nadpis
- Popis
- Datum vytvoření
- Datum uzavření
- Datum vyřešení
- Datum poslední změny
- Počítač

**Pracovní položka:** **žádosti o změnu**

ServiceDeskWorkItemType_s = "žádost o změnu"

**Pole**
- ServiceDeskConnectionName
- ID oddělení služeb
- Created By
- Uzavřel
- Zdroj
- Přiřazený pro
- Nadpis
- Typ
- Kategorie
- State
- Eskalace
- Stav konfliktu
- Naléhavost
- Priorita
- Riziko
- Dopad
- Přiřazený pro
- Datum vytvoření
- Datum uzavření
- Datum poslední změny
- Požadované datum
- Plánované počáteční datum
- Plánované koncové datum
- Datum zahájení práce
- Datum ukončení práce
- Description
- Počítač

## <a name="servicenow-example"></a>Příklad ServiceNow 
### <a name="output-data-for-a-servicenow-incident"></a>Výstupní data pro incident ServiceNow

| Log Analytics pole | Pole ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Číslo |
| IncidentState_s | State |
| Urgency_s |Naléhavost |
| Impact_s |Dopad|
| Priority_s | Priorita |
| CreatedBy_s | Otevřel |
| ResolvedBy_s | Vyřešil|
| ClosedBy_s  | Uzavřel |
| Source_s| Typ kontaktu |
| AssignedTo_s | Přiřazeno  |
| Category_s | Kategorie |
| Title_s|  Krátký popis |
| Description_s|  Poznámky |
| CreatedDate_t|  Otevřít |
| ClosedDate_t| closed|
| ResolvedDate_t|Vyřešeno|
| Počítač  | Položka konfigurace |

### <a name="output-data-for-a-servicenow-change-request"></a>Výstupní data pro žádost o změnu ServiceNow

| Log Analytics | Pole ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Číslo |
| CreatedBy_s | Požadoval (a) |
| ClosedBy_s | Uzavřel |
| AssignedTo_s | Přiřazeno  |
| Title_s|  Krátký popis |
| Type_s|  Typ |
| Category_s|  Kategorie |
| CRState_s|  State|
| Urgency_s|  Naléhavost |
| Priority_s| Priorita|
| Risk_s| Riziko|
| Impact_s| Dopad|
| RequestedDate_t  | Požadováno podle data |
| ClosedDate_t | Datum uzavření |
| PlannedStartDate_t  | Plánované počáteční datum |
| PlannedEndDate_t  | Plánované koncové datum |
| WorkStartDate_t  | Skutečné datum zahájení |
| WorkEndDate_t | Skutečné datum ukončení|
| Description_s | Description |
| Počítač  | Položka konfigurace |

## <a name="next-steps"></a>Další kroky

* [Řešení potíží v ITSM konektoru](./itsmc-resync-servicenow.md)
