---
title: Úvod do automatizace ve službě Azure Sentinel | Microsoft Docs
description: Tento článek představuje možnosti orchestrace, automatizace a reakce na zabezpečení (společnosti) služby Azure Sentinel a popisuje své komponenty společnosti – pravidla automatizace a playbooky.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 54d7c997ce17c927a692e84f6094e3a08f707af7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609358"
---
# <a name="security-orchestration-automation-and-response-soar-in-azure-sentinel"></a>Orchestrace, automatizace a odezva zabezpečení (společnosti) ve službě Azure Sentinel

Tento článek popisuje možnosti orchestrace, automatizace a reakce na zabezpečení (společnosti) služby Azure Sentinel a ukazuje, jak používání pravidel automatizace a playbooky v reakci na hrozby zabezpečení zvyšuje efektivitu vašeho SOC a šetří vaše čas a prostředky.

## <a name="azure-sentinel-as-a-soar-solution"></a>SPOLEČNOSTI řešení Azure Sentinel

### <a name="the-problem"></a>Problém

Týmy SIEM/SOC se obvykle inundated s výstrahami zabezpečení a incidenty v pravidelných intervalech, a to na svazcích, aby byli zaměstnanci, kteří jsou k dispozici, velké množství. To je velmi často v situacích, kdy se několik výstrah ignoruje a mnoho incidentů se nezkoumá, takže organizace bude zranitelná vůči útokům, které se neprojevily.

### <a name="the-solution"></a>Řešení

Azure Sentinel, kromě toho, že se jedná o systém SIEM (Security Information and Event Management), je také platforma pro orchestraci zabezpečení, automatizaci a reakci (společnosti). Jedním z jeho primárních účelů je automatizovat jakékoli opakující se a předvídatelné úkoly pro obohacení a nápravu, které odpovídají vašemu centru zabezpečení a pracovníkům (SOC/SecOps), a tím se uvolní čas a prostředky pro podrobnější šetření a lov pro pokročilé hrozby. Automatizace ve službě Azure Sentinel používá několik různých forem, od pravidel automatizace, která centrálně spravují automatizaci zpracování a reakce na incidenty, a playbooky tak, že spustí předem určené sekvence akcí, které zajistí výkonnou a flexibilní pokročilou automatizaci vašich úloh reakce na hrozby.

## <a name="automation-rules"></a>Pravidla automatizace

Pravidla automatizace jsou novým konceptem v rámci Azure Sentinel. Tato funkce umožňuje uživatelům centrálně spravovat automatizaci zpracování incidentů. Kromě toho, že vám umožní přiřazovat playbooky incidentům (ne jen k výstrahám jako dřív), pravidla automatizace také umožňují automatizovat odpovědi na více pravidel analýzy najednou, automaticky označovat, přiřazovat nebo uzavřít incidenty bez nutnosti playbooky a řídit pořadí akcí, které se spustí. Pravidla automatizace zjednodušují použití automatizace v Azure Sentinel a umožní vám zjednodušit složité pracovní postupy pro procesy orchestrace incidentů.

Seznamte se s [úplnými vysvětlení pravidel automatizace](automate-incident-handling-with-automation-rules.md).

> [!IMPORTANT]
>
> - Funkce **pravidel automatizace** je aktuálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

## <a name="playbooks"></a>Playbooky

PlayBook je kolekce akcí reakce a oprav a logiky, které je možné spustit z Azure Sentinel jako rutiny. PlayBook může přispět k automatizaci a orchestraci vaší odpovědi na hrozbu. může se tak integrovat s jinými systémy interní i externí a může být nastavená tak, aby se automaticky spouštěla v reakci na konkrétní výstrahy nebo incidenty, když se aktivuje pomocí pravidla analýzy nebo pravidla automatizace. Můžete ho také spustit ručně na vyžádání, a to v reakci na výstrahy na stránce incidenty.

Playbooky ve službě Azure Sentinel vychází z pracovních postupů vytvořených v [Azure Logic Apps](../logic-apps/logic-apps-overview.md), cloudové služby, která pomáhá plánovat, automatizovat a orchestrovat úlohy a pracovní postupy napříč systémy v celém podniku. To znamená, že playbooky může využít výhod veškerého výkonu a úprav možností integrace a orchestrace Logic Apps a snadno použitelné nástroje pro návrh a škálovatelnost, spolehlivost a úroveň služeb služby Azure úrovně 1.

Seznamte se s [úplnými vysvětleními playbooky](automate-responses-with-playbooks.md).

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak Azure Sentinel používá automatizaci, aby vašemu SOC mohli efektivněji a efektivně fungovat.

- Další informace o automatizaci zpracování incidentů najdete v tématu [Automatizace zpracování incidentů v Azure Sentinel](automate-incident-handling-with-automation-rules.md).
- Další informace o rozšířených možnostech automatizace najdete v tématu [Automatizace reakce na hrozby pomocí playbooky ve službě Azure Sentinel](automate-responses-with-playbooks.md).
- Nápovědu k implementaci pravidel automatizace a playbooky najdete v tématu [kurz: použití playbooky k automatizaci odpovědí na hrozby v Azure Sentinel](tutorial-respond-threats-playbook.md).
