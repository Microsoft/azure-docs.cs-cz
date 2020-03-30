---
title: Export výstrah a doporučení Centra zabezpečení Azure do siem | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak nastavit nepřetržitý export výstrah zabezpečení a doporučení pro siem
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: 19fdcc7b590c6ad6873c7808ae26d218bbda7f5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158961"
---
# <a name="export-security-alerts-and-recommendations"></a>Export doporučení a výstrah zabezpečení

Azure Security Center generuje podrobné výstrahy zabezpečení a doporučení. Můžete je zobrazit na portálu nebo pomocí programových nástrojů. Tyto informace může být také nutné exportovat nebo je odeslat do jiných nástrojů pro monitorování ve vašem prostředí. 

Tento článek popisuje sadu nástrojů, které umožňují exportovat výstrahy a doporučení ručně nebo průběžně, průběžně.

Pomocí těchto nástrojů můžete:

* Průběžně exportovat do pracovních prostorů Log Analytics
* Průběžně exportovat do Azure Event Hubs (pro integraci s siem třetích stran)
* Export do CSV (jednou)


## <a name="setting-up-a-continuous-export"></a>Nastavení nepřetržitého exportu

Následující kroky jsou nezbytné bez ohledu na to, zda nastavujete nepřetržitý export do pracovního prostoru Log Analytics nebo do centra událostí Azure.

1. V postranním panelu Centra zabezpečení vyberte **Nastavení & ceny**.

1. Vyberte konkrétní předplatné, pro které chcete nakonfigurovat export dat.
    
1. Na postranním panelu stránky nastavení pro toto předplatné vyberte **Možnost Nepřetržitý export**.

    [Možnosti exportu v Azure Security Center ![](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Zde vidíte možnosti exportu. K dispozici je karta pro každý dostupný cíl exportu. 

1. Vyberte datový typ, který chcete exportovat, a vyberte z filtrů u každého typu (například exportujte pouze výstrahy s vysokou závažností).

1. V oblasti Exportovat cíl zvolte, kam chcete uložená data. Data lze uložit v cíli na jiné předplatné (například na centrální instance Event Hub nebo centrální log analytics pracovního prostoru).

1. Klikněte na **Uložit**.



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>Konfigurace integrace SIEM prostřednictvím Azure Event Hubs

Azure Event Hubs je skvělé řešení pro programově náročné všechna streamovaná data. Pro výstrahy a doporučení Azure Security Center je to upřednostňovaný způsob integrace s siem třetí strany.

> [!NOTE]
> Nejúčinnější metodou pro streamování dat monitorování do externích nástrojů ve většině případů je použití Azure Event Hubs. [Tento článek](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) obsahuje stručný popis, jak můžete streamovat data monitorování z různých zdrojů do centra událostí a odkazy na podrobné pokyny.

> [!NOTE]
> Pokud jste dříve exportovali výstrahy Centra zabezpečení do SIEM pomocí protokolu aktivit Azure, níže uvedený postup tuto metodiku nahradí.

Chcete-li zobrazit schémata událostí exportovaných datových typů, navštivte [schémata událostí centra událostí](https://aka.ms/ASCAutomationSchemas).


### <a name="to-integrate-with-a-siem"></a>Integrace se SIEM 

Po konfiguraci průběžného exportu vybraných dat Centra zabezpečení do centra událostí Azure můžete nastavit příslušný konektor pro siem:

* **Azure Sentinel** – použijte nativní [datový konektor](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) výstrah Azure Security Center, který je zde nabízen.
* **Splunk** – použití [doplňku Azure Monitor pro splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** - Použití [ručně konfigurovaného zdroje protokolu](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** – použití [funkce SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Pokud chcete také automaticky přesunout průběžně exportovaná data z nakonfigurovaného centra událostí do Průzkumníka dat Azure, použijte pokyny v [centru Ingestz z Centra událostí do Průzkumníka dat Azure](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Nepřetržitý export do pracovního prostoru Analýzy protokolů

Pokud chcete analyzovat data Azure Security Center v pracovním prostoru Log Analytics nebo použít výstrahy Azure společně s Security Center, nastavte nepřetržitý export do pracovního prostoru Log Analytics.

Chcete-li exportovat do pracovního prostoru Analýzy protokolů, musíte mít na pracovním prostoru povolena řešení Analýzy protokolů Centra zabezpečení. Pokud používáte portál Azure, řešení bezplatné vrstvy Security Center se automaticky aktivuje, když povolíte nepřetržitý export. Pokud však programově konfigurujete nastavení průběžného exportu, musíte ručně vybrat bezplatnou nebo standardní cenovou úroveň pro požadovaný pracovní prostor v rámci **nastavení cenové &**.  

### <a name="log-analytics-tables-and-schemas"></a>Tabulky a schémata analýzy protokolů

Výstrahy zabezpečení a doporučení jsou uloženy v *securityalert* a *securityrecommendations* tabulky v uvedeném pořadí. Název řešení Log Analytics obsahující tyto tabulky závisí na tom, zda jste na bezplatné nebo standardní úrovni (viz [ceny):](security-center-pricing.md)Zabezpečení ("Zabezpečení a audit") nebo SecurityCenterFree.

![Tabulka *SecurityAlert* v log analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Chcete-li zobrazit schémata událostí exportovaných datových typů, navštivte [schémata tabulky Analýzy protokolů](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Zobrazení exportovaných výstrah zabezpečení a doporučení v Azure Monitoru

V některých případech můžete zobrazit exportované výstrahy zabezpečení a/nebo doporučení v [Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Azure Monitor poskytuje jednotné prostředí pro výstrahy Azure pro celou řadu výstrah Azure, včetně diagnostického protokolu, upozornění metriky a vlastnívýstrahy založené na dotazech pracovního prostoru Log Analytics.

Chcete-li zobrazit výstrahy a doporučení z Centra zabezpečení v Azure Monitor, nakonfigurujte pravidlo výstrahy na základě dotazů Log Analytics (výstraha protokolu):

1. Na stránce **Upozornění** služby Azure Monitor klikněte na **Nové pravidlo výstrah**.

    ![Stránka upozornění Azure Monitoru](./media/continuous-export/azure-monitor-alerts.png)

1. Na stránce vytvořit pravidlo nakonfigurujte nové pravidlo (stejným způsobem, jakým byste nakonfigurovali [pravidlo výstrahprotokolu v Azure Monitoru):](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)

    * V **části Zdroj**vyberte pracovní prostor Log Analytics, do kterého jste exportovali výstrahy zabezpečení a doporučení.

    * V **popřípadě Podmínka**vyberte **možnost Vlastní hledání protokolu**. Na stránce, která se zobrazí, nakonfigurujte dotaz, období zpětného pohledu a období frekvence. Ve vyhledávacím dotazu můžete zadat *SecurityAlert* nebo *SecurityRecommendation* a zadat dotaz na datové typy, do kterých centrum zabezpečení nepřetržitě exportuje, když povolíte nepřetržitý export do služby Log Analytics. 
    
    * Volitelně můžete nakonfigurovat [skupinu akcí,](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) kterou chcete aktivovat. Skupiny akcí mohou aktivovat odesílání e-mailů, lístky ITSM, WebHooks a další.
    ![Pravidlo výstrahy Azure Monitor](./media/continuous-export/azure-monitor-alert-rule.png)

Teď se ve výstrahách Azure Monitoru zobrazí nová upozornění nebo doporučení Azure Security Center (v závislosti na vaší konfiguraci) s automatickým aktivací skupiny akcí (pokud je k dispozici).

## <a name="manual-one-time-export-of-security-alerts"></a>Ruční jednorázový export výstrah zabezpečení

Chcete-li stáhnout zprávu CSV pro výstrahy nebo doporučení, otevřete stránku **Výstrahy zabezpečení** nebo **doporučení** a klepněte na tlačítko **Stáhnout sestavu CSV.**

[![Stažení dat upozornění jako souboru CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Tyto sestavy obsahují výstrahy a doporučení pro prostředky z aktuálně vybraných předplatných.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak nakonfigurovat nepřetržitý export doporučení a výstrah. Také jste se dozvěděli, jak stáhnout data upozornění jako soubor CSV. 

Související materiály naleznete v následující dokumentaci: 

- [Dokumentace k Centru událostí Azure](https://docs.microsoft.com/azure/event-hubs/)
- [Dokumentace k Azure Sentinelu](https://docs.microsoft.com/azure/sentinel/)
- [Dokumentace ke službě Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Automatizace pracovních postupů a schémata průběžných exportních datových typů](https://aka.ms/ASCAutomationSchemas)
