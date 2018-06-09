---
title: Protokoly z prostředků Azure integrovat vaše systémy SIEM | Microsoft Docs
description: Další informace o protokolu integrace se službou Azure, jejích klíčových funkcích a jak to funguje.
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
ms.date: 06/07/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 3c875060a7abdf4431026e79ce966efdc89e4e77
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236174"
---
# <a name="introduction-to-azure-log-integration"></a>Úvod do integrace protokolů Azure

>[!IMPORTANT]
> Funkce integrace protokolu Azure bude 06/01/2019 zastaralá. AzLog stahování bude ve 27. června 2018 zakázáno. Pokyny k tomu, co udělat přesunutí dopředného zkontrolujte v příspěvku [monitorování pomocí Azure, který chcete integrovat s nástroji SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Integrace se službou Azure protokolu byla k dispozici pro zjednodušení úlohy integrace Azure protokoly s místní informace o zabezpečení a událostí správy (SIEM) systému.

 Doporučené metody pro integrační Azure protokoly se má používat od dodavatele SIEM konektory. Azure monitorování poskytuje schopnost stream protokoly do centra událostí a dodavatelé SIEM může zapisovat konektory k další integraci protokoly z centra událostí do systému SIEM.  Popis toho, jak to funguje, postupujte podle pokynů v [datového proudu monitorování monitorování pro data služby event hubs](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). Článek obsahuje také seznam systémů Siem, pro které přímé Azure konektory jsou již k dispozici.  

> [!IMPORTANT]
> Pokud vaše primární týkající se shromažďování protokolů virtuálního počítače, většina dodavatelů SIEM zahrnout tuto možnost jejich řešení. Použití systému SIEM dodavatele konektor je vždy upřednostňované alternativní.

V dokumentaci na funkci Integrace protokolu Azure je stále neudržují, dokud je zastaralé funkce.

Přečtěte si další na další informace o funkci Integrace protokolu Azure:

Integrace se službou Azure protokolu událostí systému Windows shromažďuje z protokoly Prohlížeče událostí systému Windows, [protokoly Azure aktivity](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [výstrahy Azure Security Center](../security-center/security-center-intro.md), a [protokolů Azure Diagnostics](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) z Prostředky Azure. Integrace pomáhá řešení SIEM poskytnout jednotný řídicí panel pro vaše prostředky, jestli místně nebo v cloudu. Řídicí panel můžete přijmout, agregace, korelaci a analyzovat výstrahy pro události zabezpečení.

> [!NOTE]
> Integrace se službou protokolu Azure v současné době podporuje pouze komerční Azure a Azure Government cloudy. Ostatních cloudů nejsou podporovány.

![Proces protokolu integrace se službou Azure][1]

## <a name="what-logs-can-i-integrate"></a>Jaké protokoly můžete integrovat?

Azure vytvoří rozsáhlé protokolování pro každou službu Azure. Protokoly představují tři typy protokolu:

* **Řízení nebo protokoly**: poskytují přehled o jednotlivých [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) operace vytvoření, aktualizace a odstranění. Protokol činnosti Azure je příklad tohoto typu protokolu.
* **Data roviny protokoly**: poskytují přehled o události, které se vyvolá, když používáte prostředek služby Azure. Prohlížeč událostí systému Windows je například tento typ protokolu **systému**, **zabezpečení**, a **aplikace** kanálů v virtuálního počítače s Windows. Dalším příkladem je protokolování diagnostiky Azure, které se konfigurují prostřednictvím Azure monitorování.
* **Zpracování události**: Zadejte analyzovaných událostí a informace o výstrahách, které jsou zpracovány za vás. Příkladem tohoto typu události je výstrahy Azure Security Center. Azure Security Center zpracovává a analyzuje předplatného poskytují výstrahy, které jsou relevantní pro aktuální lepšímu zabezpečení.

Integrace se službou Azure protokolu podporuje ArcSight, QRadar a Splunk. Poraďte s dodavatelem SIEM k vyhodnocení, zda má dodavatel nativní konektor. Pokud je k dispozici nativní konektor, nepoužívejte protokolu integrace se službou Azure.

Pokud jsou k dispozici žádné další možnosti, zvažte použití protokolu integrace se službou Azure. Následující tabulka obsahuje Naše doporučení:

|SIEM | Zákazník už používá integrátor protokolů Azure | Zákazník pracuje na odstranění příčin možností integrace systému SIEM|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Začít s migrací do [rozšíření monitorování Azure pro Splunk](https://splunkbase.splunk.com/app/3534/). | Použití [Splunk konektor](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Migrace na nebo začít používat konektor QRadar, která je popsána v poslední část [data do centra událostí pro používání monitorování pomocí externího nástroje Azure datový proud](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). | Použití QRadar konektoru, která je popsána v poslední části [data do centra událostí pro používání monitorování pomocí externího nástroje Azure datový proud](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). |
|**ArcSight** | Pokračovat v používání integrátor protokolů Azure, dokud nebude k dispozici konektor a následná migrace na řešení na základě konektor.  | Zvažte použití Azure Log Analytics jako alternativu. Program si zaváděním integrace protokolu Azure Pokud chcete absolvovat proces migrace, až konektor k dispozici. |

> [!NOTE]
> Přestože se integrace protokolu Azure bezplatné řešení, jsou náklady na úložiště Azure přidružený úložiště informace souboru protokolu.

Pokud potřebujete pomoc, můžete vytvořit [žádost o podporu](../azure-supportability/how-to-create-azure-support-request.md). Pro službu, vyberte **integrace protokolu**.

## <a name="next-steps"></a>Další postup

Tento článek seznámili protokolu integrace se službou Azure. Další informace o protokolu integrace se službou Azure a typy protokolů, které jsou podporovány, naleznete v následujících článcích:

* [Začínáme s Azure protokolu integrace](security-azure-log-integration-get-started.md). Tento kurz vás provede procesem instalace protokolu integrace se službou Azure. Také popisuje, jak integrovat protokoly z úložiště služby Windows Azure Diagnostics (WAD), protokoly aktivita Azure, Azure Security Center výstrahy a protokoly auditu Azure Active Directory.
* [Integrace se službou Azure protokolu nejčastější dotazy (FAQ)](security-azure-log-integration-faq.md). Tyto nejčastější dotazy odpovídá na běžné dotazy týkající se integrace se službou Azure protokolu.
* Další informace o tom, jak [stream monitorování data do centra událostí pro používání pomocí externího nástroje Azure](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
