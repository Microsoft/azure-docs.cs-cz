---
title: IT Service Management Connector v Azure Monitor
description: Tento článek poskytuje informace o tom, jak připojit ITSM produkty/služby ke konektoru IT Service Management Connector (ITSMC) v Azure Monitor centrálně monitorovat a spravovat pracovní položky ITSM.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 046fcb9d7473de5666b3acb25cbcaa1f9549e679
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039491"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Propojení produktů nebo služeb ITSM s využitím ITSM konektoru
Tento článek poskytuje informace o tom, jak nakonfigurovat připojení mezi ITSM produktem/službou a konektorem Service Management Connector (ITSMC) v Log Analytics pro centrální správu vašich pracovních položek. Další informace o ITSMC najdete v tématu [Přehled](./itsmc-overview.md).

Podporují se tyto ITSM produkty nebo služby. Výběrem produktu zobrazíte podrobné informace o tom, jak tento produkt připojit k ITSMC.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> Naši zákazníci Cherwell a prov navrhují, aby používali [akci Webhooku](./action-groups.md#webhook) pro koncový bod Cherwell a prov jako jiné řešení pro integraci.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží v ITSM konektoru](./itsmc-resync-servicenow.md)