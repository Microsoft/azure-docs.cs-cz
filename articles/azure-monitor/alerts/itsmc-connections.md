---
title: IT Service Management Connector v Azure Monitor
description: Tento článek poskytuje informace o tom, jak připojit ITSM produkty/služby ke konektoru IT Service Management Connector (ITSMC) v Azure Monitor centrálně monitorovat a spravovat pracovní položky ITSM.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 40e737a1ec5fb34cd22a08925143a100d36cdc6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009313"
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

## <a name="ip-ranges-for-itsm-partners-connections"></a>Rozsahy IP adres pro připojení partnerů ITSM
Aby bylo možné zobrazit seznam IP adres ITSM, aby bylo možné ITSM připojení od partnerů ITSM Tools, doporučujeme zobrazit seznam celých veřejných IP adres oblasti Azure, ve které patří pracovní prostor LogAnalytics. [Podrobnosti](https://www.microsoft.com/en-us/download/details.aspx?id=56519) Pro oblasti EUS/ZEU/EUS2/WUS2/USA (střed) – jih může zákazník zobrazit pouze značku Network ve službě Action.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží v ITSM konektoru](./itsmc-resync-servicenow.md)
