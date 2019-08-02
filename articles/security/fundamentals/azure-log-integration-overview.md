---
title: Integrace protokolů z prostředků Azure se systémy SIEM | Microsoft Docs
description: Přečtěte si o Azure Log Integration, jeho klíčových funkcích a o tom, jak to funguje.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/28/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 9ba4a64268bcc57f04e92be83edb2ac71f18bcaf
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727598"
---
# <a name="introduction-to-azure-log-integration"></a>Úvod do Azure Log Integration

>[!IMPORTANT]
> Funkce integrace protokolu Azure bude zastaralá od 06/15/2019. Stahování AzLog bylo zakázáno 27. června 2018. Další informace o tom, co dělat, najdete v příspěvku [pomocí Azure monitoru pro integraci s nástroji Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) . 

Azure Log Integration k dispozici, aby se zjednodušila úloha integrace protokolů Azure s místním systémem správy událostí a zabezpečení SIEM (Security Information and Event Management).

 Doporučená metoda pro integraci protokolů Azure je použití konektorů od dodavatele SIEM. Azure Monitor poskytuje možnost streamování protokolů do centra událostí a dodavatelé SIEM můžou psát konektory k další integraci protokolů z centra událostí do SIEM.  Popis toho, jak to funguje, najdete podle pokynů v tématu [monitorování služby Stream monitor pro centra událostí dat](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). Článek také obsahuje seznam systémů Siem, pro které jsou již k dispozici přímé konektory Azure.  

> [!IMPORTANT]
> Pokud váš primární zájem shromažďuje protokoly virtuálních počítačů, většina dodavatelů SIEM zahrnuje tuto možnost do svého řešení. Použití konektoru dodavatele SIEM je vždy upřednostňovanou alternativou.

Dokumentace k funkci Azure Log Integration se pořád udržuje, dokud není tato funkce zastaralá.

Přečtěte si další informace o funkci Azure Log Integration:

Azure Log Integration shromažďuje události Windows z protokolů Prohlížeč událostí Windows, [protokolů aktivit Azure](/azure/azure-monitor/platform/activity-logs-overview), [výstrah Azure Security Center](/azure/security-center/security-center-intro)a [protokolů Azure Diagnostics](/azure/azure-monitor/platform/diagnostic-logs-overview) z prostředků Azure. Integrace pomáhá vašemu řešení SIEM poskytovat jednotný řídicí panel pro všechny vaše prostředky, ať už místně, nebo v cloudu. Řídicí panel můžete použít k přijímání, agregaci, korelaci a analýze výstrah pro události zabezpečení.

> [!NOTE]
> V současné době Azure Log Integration podporuje jenom cloudy Azure pro komerční a Azure Government. Ostatní cloudy nejsou podporovány.

![Proces Azure Log Integration](media/azure-log-integration-overview/azure-log-integration.png)

## <a name="what-logs-can-i-integrate"></a>Jaké protokoly můžu integrovat?

Azure vytváří rozsáhlé protokolování pro každou službu Azure. Protokoly reprezentují tři typy protokolů:

* **Protokoly pro řízení a správu**: Poskytněte přehled o [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) operace vytvoření, aktualizace a odstranění. Příkladem tohoto typu protokolu je protokol aktivit Azure.
* **Protokoly roviny dat**: Poskytují přehled o událostech, které jsou vyvolány při použití prostředku Azure. Příkladem tohoto typu protokolu jsou kanály **systému**Windows Prohlížeč událostí, **zabezpečení**a **aplikace** ve virtuálním počítači s Windows. Dalším příkladem je Azure Diagnostics protokolování, které konfigurujete prostřednictvím Azure Monitor.
* **Zpracované události**: Poskytněte analyzované informace o událostech a výstrahách, které se zpracují za vás. Příkladem tohoto typu události je Azure Security Center výstrahy. Azure Security Center procesy a analyzuje vaše předplatné a poskytuje výstrahy, které jsou relevantní pro vaše aktuální stav zabezpečení.

Azure Log Integration podporuje ArcSight, QRadar a Splunk. Obraťte se na dodavatele SIEM a vyhodnoťte, jestli má dodavatel nativní konektor. Nepoužívejte Azure Log Integration, je-li k dispozici nativní konektor.

Pokud nejsou k dispozici žádné další možnosti, zvažte použití Azure Log Integration. Následující tabulka obsahuje naše doporučení:

|SIEM | Zákazník už používá integrátor protokolu Azure. | Zákazník zkoumá možnosti integrace SIEM.|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Zahajte migraci na [doplněk Azure monitor pro Splunk](https://splunkbase.splunk.com/app/3534/). | Použijte [konektor Splunk](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Migrujte na nebo začněte používat konektor QRadar, který je popsaný v poslední části [streamování dat monitorování Azure do centra událostí pro využití externím nástrojem](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). | Pomocí konektoru QRadar popsaného v poslední části [Streamujte data monitorování Azure do centra událostí pro využití externím nástrojem](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). |
|**ArcSight** | Pokračujte v používání integrátoru protokolu Azure, dokud není k dispozici konektor a pak migrujte do řešení založeného na konektoru.  | Zvažte použití protokolů Azure Monitor jako alternativu. Nechci se připojit k Azure Log Integration, pokud nejste ochotni projít procesem migrace, když bude konektor k dispozici. |

> [!NOTE]
> I když Azure Log Integration je bezplatné řešení, jsou k dispozici náklady na úložiště Azure spojené s úložištěm informací o souboru protokolu.

Pokud potřebujete pomoc, můžete vytvořit [žádost o podporu](/azure/azure-supportability/how-to-create-azure-support-request). Pro tuto službu vyberte **log Integration**.

## <a name="next-steps"></a>Další postup

Tento článek vám představil, abyste Azure Log Integration. Další informace o Azure Log Integration a podporovaných typech protokolů najdete v následujících článcích:

* [Začínáme s Azure log Integration](azure-log-integration-get-started.md). Tento kurz vás provede instalací Azure Log Integration. Také popisuje, jak integrovat protokoly ze služby Windows Azure Diagnostics (WAD) Storage, protokolů aktivit Azure, výstrah Azure Security Center a Azure Active Directory protokolů auditu.
* Nejčastější [dotazy Azure log Integration](azure-log-integration-faq.md) Tato Nejčastější dotazy vám poodpoví na běžné otázky týkající se Azure Log Integration.
* Přečtěte si další informace o tom, jak [streamovat data monitorování Azure do centra událostí pro využití externím nástrojem](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs).

