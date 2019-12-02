---
title: Exportovat výstrahy Azure Security Center a doporučení do systémů Siem | Microsoft Docs
description: Tento článek vysvětluje, jak nastavit průběžný export výstrah a doporučení zabezpečení do systémů Siem
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: f994f4ec6d41fa0aab37e36d713eaefb22e85b28
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665051"
---
# <a name="export-security-alerts-and-recommendations-preview"></a>Exportovat výstrahy a doporučení zabezpečení (Preview)

Azure Security Center generuje podrobné výstrahy a doporučení zabezpečení. Můžete je zobrazit na portálu nebo prostřednictvím programových nástrojů. Je také možné, že budete muset tyto informace exportovat nebo je odeslat do jiných monitorovacích nástrojů ve vašem prostředí. 

Tento článek popisuje sadu nástrojů (ve verzi Preview), které umožňují exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem.

Pomocí těchto nástrojů můžete:

* Generování podrobných sestav jako sdílených svazků clusteru
* Export do Log Analytics pracovních prostorů
* Export do Azure Event Hubs (pro integrace s systémů Siem třetích stran)

## <a name="setting-up-a-continuous-export"></a>Nastavení průběžného exportu

1. Na bočním panelu Security Center klikněte na **ceny & nastavení**.

1. Vyberte konkrétní předplatné, pro které chcete exportovat data.
    
1. Z postranního panelu stránky nastavení pro toto předplatné vyberte **průběžné exportování (Preview)** .

    [![možností exportu v Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Tady vidíte možnosti exportu. Pro každý dostupný cíl exportu je k dispozici karta. 

1. Vyberte datový typ, který chcete exportovat, a vyberte filtry u jednotlivých typů (například exportovat pouze upozornění s vysokou závažností).

1. V oblasti exportovat cíl vyberte, kam chcete ukládat data. Data je možné uložit v cíli v jiném předplatném (například v centrální instanci centra událostí nebo v centrálním Log Analyticsm pracovním prostoru).

1. Klikněte na **Uložit**.

## <a name="continuous-export-through-azure-event-hubs"></a>Průběžný export prostřednictvím Azure Event Hubs  

> [!NOTE]
> Nejúčinnější metodou pro streamování dat monitorování do externích nástrojů ve většině případů je použití Azure Event Hubs. [Tento článek](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) obsahuje stručný popis toho, jak můžete streamovat data monitorování z různých zdrojů do centra událostí a odkazy na podrobné pokyny.

> [!NOTE]
> Pokud jste dříve exportovali výstrahy Security Center do SIEM pomocí protokolu aktivit Azure, tento postup nahrazuje tuto metodologii.

### <a name="to-integrate-with-a-siem"></a>Integrace s SIEM 

Po nakonfigurování průběžného exportu vybraných Security Center dat do Azure Event Hubs můžete nastavit příslušný konektor na SIEM podle následujících pokynů.

Postupujte podle pokynů týkajících se SIEM z [této stránky](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/?cdn=disable) a použijte relevantní konektor:

* **Splunk** – použití [doplňku Azure monitor pro Splunk](https://splunkbase.splunk.com/app/3534/)
* **IBM QRadar** – použití [ručně nakonfigurovaného zdroje protokolu](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_logsource_microsoft_azure_event_hubs.html)
* **ArcSight** – použití [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Pokud používáte službu **Azure Sentinel**, použijte [datový konektor](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) nativní Azure Security Center výstrahy, který tam nabízí.

Pokud byste chtěli nepřetržitě exportovaná data automaticky přesunout z nakonfigurovaného centra událostí do Azure Průzkumník dat, postupujte podle pokynů v tématu ingestování [dat z centra událostí do azure Průzkumník dat](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).


## <a name="continuous-export-to-log-analytics-workspace"></a>Průběžný export do pracovního prostoru Log Analytics

Pokud chcete exportovat do Log Analytics pracovního prostoru, musíte mít v pracovním prostoru povolená řešení Log Analytics na úrovni Security Center úrovně Free nebo Standard. Pokud používáte Azure Portal, řešení na úrovni Free Security Center se automaticky povolí, když povolíte průběžný export. Pokud ale konfigurujete nastavení průběžného exportu programově, musíte ručně vybrat cenovou úroveň Free nebo standard pro požadovaný pracovní prostor v rámci **Nastavení cenové &** .  

Výstrahy a doporučení zabezpečení se ukládají do tabulek *SecurityAlert* a *SecurityRecommendations* . Název Log Analytics řešení obsahující tyto tabulky závisí na tom, jestli jste na úrovni Free nebo Standard (viz [ceny](security-center-pricing.md)): Security nebo SecurityCenterFree.

![Tabulka * SecurityAlert * v Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Zobrazení exportovaných výstrah zabezpečení a doporučení v Azure Monitor

V některých případech můžete zvolit zobrazení exportovaných výstrah zabezpečení nebo doporučení v [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Azure Monitor poskytuje jednotné prostředí pro upozorňování na nejrůznější výstrahy Azure, včetně diagnostického protokolu, výstrah metrik a vlastních výstrah, a to na základě Log Analytics dotazů v pracovním prostoru.

Chcete-li zobrazit výstrahy a doporučení z Security Center v Azure Monitor, nakonfigurujte pravidlo výstrahy založené na Log Analyticsch dotazech (výstraha protokolu):

1. Na stránce **výstrahy** Azure Monitor klikněte na **nové pravidlo výstrahy**.

    ![Stránka s výstrahami Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Na stránce Vytvořit pravidlo nakonfigurujte nové pravidlo (stejným způsobem, jako byste nakonfigurovali [pravidlo upozornění protokolu v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log):

    * Jako **prostředek**vyberte pracovní prostor Log Analytics, do kterého jste exportovali výstrahy a doporučení zabezpečení.

    * V případě **podmínky**vyberte **vlastní prohledávání protokolu**. Na zobrazené stránce nakonfigurujte dotaz, období lookback a periodu četnosti. Do vyhledávacího dotazu můžete zadat *SecurityAlert* nebo *SecurityRecommendation* pro dotazování datových typů, které Security Center průběžně exportovat do funkce průběžný export do Log Analytics. 
    
    * Volitelně můžete nakonfigurovat [skupinu akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) , kterou chcete aktivovat. Skupiny akcí můžou aktivovat odesílání e-mailů, lístky ITSM, Webhooky a další.
    pravidlo výstrahy ![Azure Monitor](./media/continuous-export/azure-monitor-alert-rule.png)

V závislosti na konfiguraci se teď v Azure Monitor výstrahy zobrazí nové výstrahy Azure Security Center nebo doporučení (v závislosti na vaší konfiguraci) a automaticky se aktivuje skupina akcí (Pokud je k dispozici).

## <a name="manual-one-time-export-of-security-alerts"></a>Ruční export výstrah zabezpečení v jednorázovém čase

Chcete-li stáhnout sestavu CSV pro výstrahy nebo doporučení, otevřete stránku **výstrahy zabezpečení** nebo **doporučení** a klikněte na tlačítko **Stáhnout sestavu CSV (Náhled)** .

[![stahovat data výstrah jako soubor CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Tyto sestavy obsahují výstrahy a doporučení pro prostředky z aktuálně vybraných předplatných ve filtru Directory + Subscription na webu Azure Portal: ![filtr pro výběr možnosti adresář a předplatné](./media/continuous-export/filter-for-export-csv.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak nakonfigurovat průběžné export vašich doporučení a upozornění. Zjistili jste také, jak si data výstrah stáhnout jako soubor CSV. 

Související materiály najdete v následující dokumentaci: 

- [Dokumentace k Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Dokumentace k ověřovacím službám Azure](https://docs.microsoft.com/azure/sentinel/)
- [Dokumentace ke službě Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)