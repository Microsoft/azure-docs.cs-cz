---
title: Přehled IT Service Management Connectoru
description: Tento článek poskytuje přehled služby IT Service Management Connector (ITSMC).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: ed13837eabadc38cee50efac3b9340c973c8ea70
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492429"
---
# <a name="it-service-management-connector-overview"></a>Přehled IT Service Management Connectoru

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

IT Service Management Connector (ITSMC) umožňuje připojit Azure k podporovanému produktu nebo službě IT Service Management (ITSM).

Služby Azure, jako je Azure Log Analytics a Azure Monitor poskytují nástroje pro detekci, analýzu a řešení problémů s prostředky Azure a mimo Azure. Pracovní položky, které se k problému vztahují, se obvykle nacházejí v ITSMm produktu nebo ve službě. ITSMC poskytuje obousměrné propojení mezi nástroji Azure a ITSM, které vám pomůžou rychleji řešit problémy.

## <a name="configuration-steps"></a>Postup konfigurace

ITSMC podporuje připojení s následujícími ITSM nástroji:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> Naši zákazníci Cherwell a prov navrhují, aby používali [akci Webhooku](./action-groups.md#webhook) pro koncový bod Cherwell a prov jako jiné řešení pro integraci.

Pomocí ITSMC můžete:

-  Vytvořte pracovní položky v nástroji ITSM na základě výstrah Azure (výstrahy metrik, výstrahy protokolu aktivit a výstrahy Log Analytics).
-  Volitelně můžete svůj incident a data žádostí o změnu z nástroje ITSM na pracovní prostor služby Azure Log Analytics synchronizovat.

Informace o právních výrazech a zásadách ochrany osobních údajů najdete v tématu [prohlášení o zásadách ochrany osobních údajů společnosti Microsoft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Můžete začít používat ITSMC provedením následujících kroků:

1. [Nastavte prostředí ITSM tak, aby přijímalo výstrahy z Azure.](./itsmc-connections.md)
1. [Konfigurace řešení Azure ITSM](./itsmc-definition.md#add-it-service-management-connector)
1. [Nakonfigurujte konektor Azure ITSM pro vaše prostředí ITSM.](./itsmc-definition.md#create-an-itsm-connection)
1. [Nakonfigurujte skupinu akcí, která bude využívat konektor ITSM.](./itsmc-definition.md#define-a-template)

## <a name="next-steps"></a>Další kroky

* [Řešení potíží v ITSM konektoru](./itsmc-resync-servicenow.md)