---
title: Protokolování a auditování zabezpečení Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o protokolech dostupných v Azure a přehledech zabezpečení, které můžete získat.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: bd0f42507e22559690e2682a391c53b9c090aa6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750787"
---
# <a name="azure-security-logging-and-auditing"></a>Protokolování a auditování zabezpečení Azure

Azure poskytuje širokou škálu konfigurovatelných možností auditování a protokolování zabezpečení, které vám pomohou identifikovat mezery v zásadách a mechanismech zabezpečení. Tento článek popisuje generování, shromažďování a analýzu protokolů zabezpečení ze služeb hostovaných v Azure.

> [!Note]
> Některá doporučení v tomto článku může mít za následek zvýšení využití dat, sítě nebo výpočetní prostředky a zvýšit náklady na licenci nebo předplatné.

## <a name="types-of-logs-in-azure"></a>Typy protokolů v Azure

Cloudové aplikace jsou složité s mnoha pohyblivými částmi. Data protokolování mohou poskytnout přehled o vašich aplikacích a pomoci vám:

- Poradce při potížích s minulostí nebo předcházení případným problémům
- Zlepšení výkonu nebo udržovatelnosti aplikací
- Automatizace akcí, které by jinak vyžadovaly ruční zásah

Protokoly Azure jsou rozděleny do kategorií do následujících typů:
* **Protokoly řízení a správy** poskytují informace o operacích Azure Resource Manager CREATE, UPDATE a DELETE. Další informace naleznete v [tématu Protokoly aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md).

* **Protokoly roviny dat** poskytují informace o událostech vyzdvižených jako součást využití prostředků Azure. Příklady tohoto typu protokolu jsou systém událostí systému Windows, zabezpečení a protokoly aplikací ve virtuálním počítači (VM) a [protokoly diagnostiky,](../../azure-monitor/platform/platform-logs-overview.md) které jsou nakonfigurované prostřednictvím Služby Azure Monitor.

* **Zpracované události** poskytují informace o analyzovaných událostech nebo výstrahách, které byly zpracovány vaším jménem. Příklady tohoto typu jsou [výstrahy Azure Security Center,](../../security-center/security-center-managing-and-responding-alerts.md) kde [Azure Security Center](../../security-center/security-center-intro.md) zpracoval a analyzoval vaše předplatné a poskytuje stručné výstrahy zabezpečení.

V následující tabulce jsou uvedeny nejdůležitější typy protokolů dostupných v Azure:

| Kategorie protokolu | Typ protokolu | Využití | Integrace |
| ------------ | -------- | ------ | ----------- |
|[Protokoly aktivit](../../azure-monitor/platform/platform-logs-overview.md)|Události roviny řízení ve prostředcích Azure Resource Manageru|  Poskytuje přehled o operacích, které byly provedeny na prostředky ve vašem předplatném.|    Rozhraní Rest API, [Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md)|
|[Protokoly prostředků Azure](../../azure-monitor/platform/platform-logs-overview.md)|Častá data o provozu prostředků Azure Resource Manageru v předplatném|   Poskytuje přehled o operacích, které provedl váš prostředek sám.| Azure Monitor|
|[Generování sestav v Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)|Protokoly a sestavy | Hlásí aktivity přihlášení uživatelů a informace o systémových aktivitách o uživatelích a správě skupin.|[Rozhraní API pro grafy](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[Virtuální počítače a cloudové služby](../../azure-monitor/learn/quick-collect-azurevm.md)|Služba Windows Event Log a Syslog linuxu|  Zachytí systémová data a protokolování dat na virtuálních počítačích a přenáší je do účtu úložiště podle vašeho výběru.|   Windows (pomocí úložiště Diagnostika Windows Azure [[WAD]](../../monitoring-and-diagnostics/azure-diagnostics.md)a Linux v Azure Monitoru|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Protokolování úložiště, poskytuje data metrik pro účet úložiště|Poskytuje přehled o požadavcích na trasování, analyzuje trendy využití a diagnostikuje problémy s účtem úložiště.|   ROZHRANÍ REST API nebo [klientská knihovna](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Protokoly toku skupiny zabezpečení sítě (NSG)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|Formát JSON, zobrazuje odchozí a příchozí toky na základě pravidla|Zobrazí informace o příchozím přenosu dat a odchozím přenosech IP prostřednictvím skupiny zabezpečení sítě.|[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Přehled aplikací](../../azure-monitor/app/app-insights-overview.md)|Protokoly, výjimky a vlastní diagnostika|  Poskytuje službu sledování výkonu aplikací (APM) pro webové vývojáře na více platformách.| ROZHRANÍ REST API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[Procesní data / výstrahy zabezpečení](../../security-center/security-center-intro.md)|  Výstrahy Azure Security Center, Azure Monitor zaznamenává výstrahy|    Obsahuje informace o zabezpečení a výstrahy.|  API REST, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>Integrace protokolů s místními systémy SIEM
[Integrace výstrah Centra zabezpečení](../../security-center/security-center-export-data-to-siem.md) popisuje, jak synchronizovat výstrahy Centra zabezpečení, události zabezpečení virtuálních strojů shromážděné protokoly diagnostiky Azure a protokoly auditu Azure s protokoly Azure Monitor nebo řešením SIEM.

## <a name="next-steps"></a>Další kroky

- [Auditování a protokolování](management-monitoring-overview.md): Chraňte data udržováním viditelnosti a rychlou reakcí na včasná výstraha zabezpečení.

- [Protokolování zabezpečení a kolekce protokolů auditu v rámci Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Vynuťte tato nastavení, abyste zajistili, že vaše instance Azure shromažďují správné protokoly zabezpečení a auditu.

- [Konfigurace nastavení auditu pro kolekci webů](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Pokud jste správcem kolekce webů, načtěte historii akcí jednotlivých uživatelů a historii akcí prováděných během určitého období.

- [Prohledejte protokol auditu v Centru dodržování předpisů zabezpečení & Office 365](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Pomocí Centra dodržování předpisů zabezpečení Office 365 & prohledáváte jednotný protokol auditu a zobrazte aktivitu uživatelů a správců ve vaší organizaci Office 365.
