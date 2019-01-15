---
title: Integrace protokolů z prostředků Azure se systémy SIEM | Dokumentace Microsoftu
description: Přečtěte si o Azure Log Integration, jejích klíčových funkcích a jak to funguje.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 93ed7620636535d45791a657d012a9c7056be09d
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54303186"
---
# <a name="introduction-to-azure-log-integration"></a>Úvod do integrace protokolů Azure

>[!IMPORTANT]
> Funkce integrace protokolů Azure se přestanou používat podle 06/01/2019. Soubory ke stažení AzLog byly deaktivovány 27. června 2018. Pokyny, jak postupovat přesun vpřed revizi, příspěvek [použití Azure monitoru k integraci s nástroji SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Integrace protokolů Azure byl k dispozici pro zjednodušení úloh s integrací Azure protokolů pomocí vašeho místního systému informace o zabezpečení a správu událostí (SIEM).

 Integrace protokolů Azure doporučujeme používat konektory dodavatele SIEM. Platforma Azure Monitor poskytuje možnost streamování protokolů do služby event hubs a dodavatelů SIEM můžete napsat konektory k další integraci protokoly z centra událostí do systému SIEM.  Popis toho, jak to funguje, postupujte podle pokynů v [monitorování datového proudu monitorování dat služby event hubs](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Tento článek obsahuje také seznam systémů Siem, pro které přímé Azure konektory jsou už k dispozici.  

> [!IMPORTANT]
> Pokud primárním zájmem je shromažďování protokolů virtuálního počítače, většina dodavatelů SIEM zahrnout tuto možnost jejich řešení. Použití systému SIEM od dodavatele konektoru je vždy upřednostňovaná alternativa.

Dokumentaci k funkci integrace protokolů Azure je stále udržována, dokud tato funkce je zastaralá.

Přečtěte si další získat další informace o funkci integrace protokolů Azure:

Integrace protokolů Azure shromažďuje události Windows z protokolů prohlížeče událostí Windows [protokolů aktivit Azure](../azure-monitor/platform/activity-logs-overview.md), [výstrahy Azure Security Center](../security-center/security-center-intro.md), a [protokoly diagnostiky Azure](../azure-monitor/platform/diagnostic-logs-overview.md) z Prostředky Azure. Pomáhá integrace řešení SIEM poskytují jednotný řídicí panel pro všechny vaše prostředky, ať už místní nebo v cloudu. Řídicí panel můžete použít pro příjem, agregaci, korelaci a analýzu výstrahy pro události zabezpečení.

> [!NOTE]
> Integrace protokolů Azure v současné době podporuje pouze Azure komerční a cloudy Azure Government. Nejsou podporovány jinými cloudy.

![Proces integrace protokolů Azure][1]

## <a name="what-logs-can-i-integrate"></a>Jaké protokoly, které můžete integrovat

Azure vytvoří rozsáhlé protokolování pro jednotlivé služby Azure. V protokolech představují tři typy protokolů:

* **Protokoly/řízení**: Poskytuje přehled o [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) operace CREATE, UPDATE a DELETE. Protokol aktivit Azure je příkladem tento typ protokolu.
* **Protokoly RP roviny dat**: Poskytuje přehled o událostech, které jsou vyvolány při použití prostředku Azure. Příkladem tohoto typu protokolu je prohlížeč událostí Windows **systému**, **zabezpečení**, a **aplikace** kanálů v rámci virtuálního počítače Windows. Dalším příkladem je protokolování diagnostiky Azure, které nakonfigurujete v nástroji Azure Monitor.
* **Zpracování události**: Zadejte analyzované události a informace o výstrahách, které jsou zpracovány za vás. Příkladem tento typ události je výstrahy Azure Security Center. Azure Security Center zpracuje a analyzuje vaše předplatné k poskytování výstrah, které jsou relevantní pro aktuální stav zabezpečení.

Integrace protokolů Azure podporuje ArcSight QRadar a Splunk. Zkontrolovat u dodavatele systému SIEM k vyhodnocení, zda má dodavatel nativní konektoru. Nepoužívejte integrace protokolů Azure, pokud nativní connector je k dispozici.

Pokud nejsou dostupné žádné další možnosti, zvažte použití integrace protokolů Azure. Následující tabulka obsahuje Naše doporučení:

|SIEM | Zákazník již používá integrátor protokolů Azure | Zákazník zkoumá možnosti integrace SIEM|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Začněte migrovat do [rozšíření Azure Monitor umožňující Splunk](https://splunkbase.splunk.com/app/3534/). | Použití [Splunk konektor](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Migrace na nebo začít používat QRadar konektoru, která je popsána v poslední části [pomocí externího nástroje pro monitorování data do centra událostí pro používání Azure Stream](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). | Použití konektoru QRadar, která je popsána v poslední části [pomocí externího nástroje pro monitorování data do centra událostí pro používání Azure Stream](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). |
|**ArcSight** | Pokračovat v používání integrátor protokolů Azure, dokud se konektor je k dispozici a potom migrovat do řešení založené na konektoru.  | Zvažte použití Azure Log Analytics jako alternativu. Program není připojení k Azure Log Integration Pokud se nechcete projít procesem migrace, jakmile je konektor k dispozici. |

> [!NOTE]
> Přestože integrace protokolů Azure je bezplatné řešení, jsou náklady na úložiště Azure spojené s úložištěm informace souboru protokolu.

Pokud potřebujete pomoc, můžete vytvořit [žádost o podporu](../azure-supportability/how-to-create-azure-support-request.md). Pro služby, vyberte **integrace protokolů**.

## <a name="next-steps"></a>Další postup

V tomto článku jste se seznámili s integrací protokolů Azure. Další informace o Azure Log Integration a typy protokolů, které jsou podporovány, naleznete v následujících článcích:

* [Začínáme s Azure Log Integration](security-azure-log-integration-get-started.md). Tento kurz vás provede instalací integrace protokolů Azure. Také popisuje, jak integrovat protokoly z úložiště Windows Azure Diagnostics (WAD), protokoly aktivit Azure, Azure Security Center upozornění a protokoly auditování Azure Active Directory.
* [Integrace protokolů Azure – nejčastější dotazy (FAQ)](security-azure-log-integration-faq.md). Tyto nejčastější dotazy odpovědi na běžné dotazy týkající se integrace protokolů Azure.
* Další informace o tom, jak [Streamovat data do centra událostí pro využití pomocí externího nástroje pro monitorování Azure](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
