---
title: Přehled IT Service Management Connectoru
description: Tento článek poskytuje přehled služby IT Service Management Connector (ITSMC).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: 93759cf239a2e7ef79c719c83299740ea3722130
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614553"
---
# <a name="it-service-management-connector-overview"></a>Přehled IT Service Management Connectoru

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

IT Service Management Connector (ITSMC) umožňuje připojit Azure k podporovanému produktu nebo službě IT Service Management (ITSM).

Služby Azure, jako je Azure Log Analytics a Azure Monitor poskytují nástroje pro detekci, analýzu a řešení problémů s prostředky Azure a mimo Azure. Pracovní položky, které se k problému vztahují, se obvykle nacházejí v ITSMm produktu nebo ve službě. ITSMC poskytuje obousměrné propojení mezi nástroji Azure a ITSM, které vám pomůžou rychleji řešit problémy.

## <a name="configuration-steps"></a>Postup konfigurace

ITSMC podporuje připojení s následujícími ITSM nástroji:

-   ServiceNow
-   System Center Service Manager
-   Prov
-   Cherwell

   >[!NOTE]
> Naši zákazníci Cherwell a prov navrhují, aby používali [akci Webhooku](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) pro koncový bod Cherwell a prov jako jiné řešení pro integraci.

Pomocí ITSMC můžete:

-  Vytvořte pracovní položky v nástroji ITSM na základě výstrah Azure (výstrahy metrik, výstrahy protokolu aktivit a výstrahy Log Analytics).
-  Volitelně můžete svůj incident a data žádostí o změnu z nástroje ITSM na pracovní prostor služby Azure Log Analytics synchronizovat.

Informace o právních výrazech a zásadách ochrany osobních údajů najdete v tématu [prohlášení o zásadách ochrany osobních údajů společnosti Microsoft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Můžete začít používat ITSMC provedením následujících kroků:

1. [Připojte ITSM produkty/služby ke službě IT Service Management Connector.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections)
2. [Přidat ITSMC.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#add-it-service-management-connector)
3. [Vytvořte připojení ITSM.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#create-an-itsm-connection)
4. [Použijte připojení.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#use-itsmc)

## <a name="next-steps"></a>Další kroky

[Přidat ITSM produkty/služby do konektoru správy služeb IT](./itsmc-connections.md)
