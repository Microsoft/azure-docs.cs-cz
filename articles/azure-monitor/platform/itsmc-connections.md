---
title: IT Service Management Connector v Azure Monitor
description: Tento článek poskytuje informace o tom, jak připojit ITSM produkty/služby ke konektoru IT Service Management Connector (ITSMC) v Azure Monitor centrálně monitorovat a spravovat pracovní položky ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: a372cdcd05267f3bdb093f676948a79c473ad955
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/29/2020
ms.locfileid: "97804016"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Propojení produktů nebo služeb ITSM s využitím ITSM konektoru
Tento článek poskytuje informace o tom, jak nakonfigurovat připojení mezi ITSM produktem/službou a konektorem Service Management Connector (ITSMC) v Log Analytics pro centrální správu vašich pracovních položek. Další informace o ITSMC najdete v tématu [Přehled](./itsmc-overview.md).

Podporují se tyto ITSM produkty nebo služby. Výběrem produktu zobrazíte podrobné informace o tom, jak tento produkt připojit k ITSMC.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Prov](./itsmc-connections-provance.md)

> [!NOTE]
> Naši zákazníci Cherwell a prov navrhují, aby používali [akci Webhooku](./action-groups.md#webhook) pro koncový bod Cherwell a prov jako jiné řešení pro integraci.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží v ITSM konektoru](./itsmc-resync-servicenow.md)