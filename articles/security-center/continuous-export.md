---
title: Exportovat výstrahy Azure Security Center a doporučení do systémů Siem | Microsoft Docs
description: Tento článek vysvětluje, jak nastavit průběžný export výstrah a doporučení zabezpečení do systémů Siem
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: cf8fdd8d91c035d374277c4752fb761c0c4e72c7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905632"
---
# <a name="export-security-alerts-and-recommendations"></a>Export doporučení a výstrah zabezpečení

Azure Security Center generuje podrobné výstrahy a doporučení zabezpečení. Můžete je zobrazit na portálu nebo prostřednictvím programových nástrojů. Je také možné, že budete muset tyto informace exportovat nebo je odeslat do jiných monitorovacích nástrojů ve vašem prostředí. 

Tento článek popisuje sadu nástrojů, které umožňují exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžně.

Pomocí těchto nástrojů můžete:

* Průběžný export do Log Analytics pracovních prostorů
* Průběžný export do Azure Event Hubs (pro integrace s systémů Siem třetích stran)
* Exportovat do sdíleného svazku clusteru (jednorázový)



## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|Free|
|Požadované role a oprávnění:|**Role správce zabezpečení** ve skupině prostředků (nebo **vlastníkovi**)<br>Musí mít taky oprávnění k zápisu pro cílový prostředek.|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![Yes](./media/icons/yes-icon.png) Čína gov (do centra událostí), ostatní gov|
|||



## <a name="set-up-a-continuous-export"></a>Nastavení průběžného exportu

Následující postup je nezbytný, ať už nastavujete průběžný export do Log Analyticsho pracovního prostoru nebo Azure Event Hubs.

1. Z bočního panelu Security Center vyberte **cenové & nastavení**.

1. Vyberte konkrétní předplatné, pro které chcete exportovat data.
    
1. Z postranního panelu stránky nastavení daného předplatného vyberte **průběžný export**.

    [ ![ Možnosti exportu v Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) tady vidíte možnosti exportu. Pro každý dostupný cíl exportu je k dispozici karta. 

1. Vyberte datový typ, který chcete exportovat, a vyberte filtry u jednotlivých typů (například exportovat pouze upozornění s vysokou závažností).

1. Pokud chcete, volitelně i když váš výběr obsahuje jedno z těchto čtyř doporučení, můžete do nich zahrnout výsledky posouzení ohrožení zabezpečení:

    - U vašich databází SQL by se měly opravit výsledky posouzení ohrožení zabezpečení.
    - Na počítačích s SQL serverem by se měly opravit výsledky posouzení ohrožení zabezpečení (Preview)
    - Ohrožení zabezpečení v Azure Container Registry imagí by se mělo opravit (používá se Qualys).
    - Ohrožení zabezpečení ve vašich virtuálních počítačích by se mělo opravit.

    Chcete-li zahrnout zjištění s těmito doporučeními, povolte možnost **Zahrnout zjištění zabezpečení** .

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Zahrnout zjištění zabezpečení – přepnout do konfigurace průběžného exportu" :::


1. V oblasti exportovat cíl vyberte, kam chcete ukládat data. Data je možné uložit v cíli v jiném předplatném (například v centrální instanci centra událostí nebo v centrálním Log Analyticsm pracovním prostoru).

1. Vyberte **Uložit**.


## <a name="set-up-continuous-export-via-the-rest-api"></a>Nastavení průběžného exportu pomocí REST API

Funkci průběžného exportu lze nakonfigurovat a spravovat prostřednictvím [rozhraní API pro automatizaci](https://docs.microsoft.com/rest/api/securitycenter/automations)Azure Security Center. Pomocí tohoto rozhraní API můžete vytvářet nebo aktualizovat automatizace pro export do libovolného z následujících možných cílů:

- Azure Event Hub
- Pracovní prostor služby Log Analytics
- Azure Logic Apps 

Rozhraní API poskytuje další funkce, které nejsou z Azure Portal k dispozici, například:

* **Větší objem** – rozhraní API umožňuje vytvořit více konfigurací exportu v rámci jednoho předplatného. Stránka **průběžného exportu** v uživatelském rozhraní portálu Security Center podporuje pouze jednu konfiguraci exportu pro každé předplatné.

* **Další funkce** – rozhraní API nabízí další parametry, které nejsou zobrazené v uživatelském rozhraní. Můžete například přidat značky do prostředku automatizace a také definovat svůj export na základě širší škály vlastností výstrah a doporučení než ty, které jsou nabízeny na stránce **průběžného exportu** v uživatelském rozhraní portálu Security Center.

* **Lépe zaměřený rozsah** – rozhraní API poskytuje podrobnější úroveň pro rozsah konfigurací exportu. Při definování exportu pomocí rozhraní API můžete to udělat na úrovni skupiny prostředků. Pokud používáte stránku **průběžného exportu** v uživatelském rozhraní portálu Security Center, je nutné ji definovat na úrovni předplatného.

    > [!TIP]
    > Pokud jste nastavili více konfigurací exportu pomocí rozhraní API nebo pokud jste použili parametry pouze rozhraní API, tyto funkce navíc nebudou zobrazeny v uživatelském rozhraní Security Center. Místo toho se zobrazí banner s tím, že existují další konfigurace.

Další informace o rozhraní API pro automatizaci najdete v [dokumentaci k REST API](https://docs.microsoft.com/rest/api/securitycenter/automations).



## <a name="configure-siem-integration-via-azure-event-hubs"></a>Konfigurace integrace SIEM pomocí Azure Event Hubs

Azure Event Hubs je skvělé řešení pro programově využívající všechna streamovaná data. Pro výstrahy a doporučení Azure Security Center se jedná o preferovaný způsob, jak integrovat s SIEM třetí strany.

> [!NOTE]
> Nejúčinnější metodou pro streamování dat monitorování do externích nástrojů ve většině případů je použití Azure Event Hubs. [Tento článek](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) obsahuje stručný popis toho, jak můžete streamovat data monitorování z různých zdrojů do centra událostí a odkazy na podrobné pokyny.

> [!NOTE]
> Pokud jste dříve exportovali výstrahy Security Center do SIEM pomocí protokolu aktivit Azure, tento postup nahrazuje tuto metodologii.

Chcete-li zobrazit schémata událostí exportovaných datových typů, navštivte [schéma událostí centra událostí](https://aka.ms/ASCAutomationSchemas).


### <a name="to-integrate-with-a-siem"></a>Integrace s SIEM 

Po nakonfigurování průběžného exportu vybraných Security Center dat do Azure Event Hubs můžete nastavit příslušný konektor pro SIEM:

* **Azure Sentinel** – použijte k dispozici nativní [datový konektor](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) pro výstrahy Azure Security Center.
* **Splunk** – použití [doplňku Azure monitor pro Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** – použití [ručně nakonfigurovaného zdroje protokolu](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** – použití [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Pokud byste chtěli nepřetržitě exportovaná data automaticky přesunout z nakonfigurovaného centra událostí do Azure Průzkumník dat, postupujte podle pokynů v tématu ingestování [dat z centra událostí do azure Průzkumník dat](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Průběžný export do pracovního prostoru Log Analytics

Pokud chcete analyzovat Azure Security Center Data v pracovním prostoru Log Analytics nebo použít výstrahy Azure společně s Security Center, nastavte průběžný export do pracovního prostoru Log Analytics.

Pokud chcete exportovat do Log Analytics pracovního prostoru, musíte mít v pracovním prostoru povolená Security Center Log Analytics řešení. Pokud používáte Azure Portal, je řešení Free Security Center v případě, že povolíte průběžný export, povoleno automaticky. Pokud ale konfigurujete nastavení průběžného exportu programově, je nutné ručně zapnout nebo vypnout Azure Defender ze stránky **Nastavení cenové &** .

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics tabulek a schémat

Výstrahy a doporučení zabezpečení se ukládají do tabulek *SecurityAlert* a *SecurityRecommendations* . Název Log Analyticsho řešení obsahujícího tyto tabulky závisí na tom, jestli máte povolený Azure Defender: Security (' Security and Audit ') nebo SecurityCenterFree.

![Tabulka * SecurityAlert * v Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Chcete-li zobrazit schémata událostí exportovaných datových typů, navštivte [Log Analytics schémat tabulek](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Zobrazení exportovaných výstrah zabezpečení a doporučení v Azure Monitor

V některých případech můžete zvolit zobrazení exportovaných výstrah zabezpečení nebo doporučení v [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Azure Monitor poskytuje jednotné prostředí pro upozorňování na nejrůznější výstrahy Azure, včetně diagnostického protokolu, výstrah metrik a vlastních výstrah, a to na základě Log Analytics dotazů v pracovním prostoru.

Chcete-li zobrazit výstrahy a doporučení z Security Center v Azure Monitor, nakonfigurujte pravidlo výstrahy založené na Log Analyticsch dotazech (výstraha protokolu):

1. Na stránce **výstrahy** Azure Monitor vyberte **nové pravidlo výstrahy**.

    ![Stránka s výstrahami Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Na stránce Vytvořit pravidlo nakonfigurujte nové pravidlo (stejným způsobem, jako byste nakonfigurovali [pravidlo upozornění protokolu v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)):

    * Jako **prostředek**vyberte pracovní prostor Log Analytics, do kterého jste exportovali výstrahy a doporučení zabezpečení.

    * V případě **podmínky**vyberte **vlastní prohledávání protokolu**. Na zobrazené stránce nakonfigurujte dotaz, období lookback a periodu četnosti. Do vyhledávacího dotazu můžete zadat *SecurityAlert* nebo *SecurityRecommendation* pro dotazování datových typů, které Security Center průběžně exportovat do funkce průběžný export do Log Analytics. 
    
    * Volitelně můžete nakonfigurovat [skupinu akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) , kterou chcete aktivovat. Skupiny akcí můžou aktivovat odesílání e-mailů, lístky ITSM, Webhooky a další.
    ![Azure Monitor pravidlo výstrahy](./media/continuous-export/azure-monitor-alert-rule.png)

V závislosti na konfiguraci se teď v Azure Monitor výstrahy zobrazí nové výstrahy Azure Security Center nebo doporučení (v závislosti na vaší konfiguraci) a automaticky se aktivuje skupina akcí (Pokud je k dispozici).

## <a name="manual-one-time-export-of-security-alerts"></a>Ruční export výstrah zabezpečení v jednorázovém čase

Chcete-li stáhnout sestavu CSV pro výstrahy nebo doporučení, otevřete stránku **výstrahy zabezpečení** nebo **doporučení** a vyberte tlačítko **Stáhnout sestavu CSV** .

[![Stáhnout data výstrah jako soubor CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Tyto sestavy obsahují výstrahy a doporučení pro prostředky z aktuálně vybraných předplatných.



## <a name="faq---continuous-export"></a>Nejčastější dotazy – průběžný export

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Jaké jsou náklady spojené s exportem dat?

Pro povolení průběžného exportu se neúčtují žádné náklady. Náklady mohou být vynaloženy na příjem a uchovávání dat v pracovním prostoru Log Analytics v závislosti na vaší konfiguraci. 

Přečtěte si další informace o [cenách Log Analytics pracovního prostoru](https://azure.microsoft.com/pricing/details/monitor/).

Přečtěte si další informace o [cenách služby Azure Event hub](https://azure.microsoft.com/pricing/details/event-hubs/).


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak nakonfigurovat průběžné export vašich doporučení a upozornění. Zjistili jste také, jak si data výstrah stáhnout jako soubor CSV. 

Související materiály najdete v následující dokumentaci: 

- [Dokumentace ke službě Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Dokumentace k ověřovacím službám Azure](https://docs.microsoft.com/azure/sentinel/)
- [Dokumentace k Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Schémata datových typů automatizace a průběžného exportu pracovního postupu](https://aka.ms/ASCAutomationSchemas)
