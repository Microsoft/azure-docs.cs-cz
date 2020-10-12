---
title: Průběžný export může posílat výstrahy Azure Security Center a doporučení Log Analytics pracovní prostory nebo Azure Event Hubs
description: Naučte se konfigurovat průběžný export výstrah a doporučení zabezpečení pro Log Analytics pracovní prostory nebo Azure Event Hubs
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 8b27c3d0982e945fcabc6e7748646ea2ee1a4184
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945265"
---
# <a name="continuously-export-security-alerts-and-recommendations"></a>Průběžně exportovat výstrahy a doporučení zabezpečení

Azure Security Center generuje podrobné výstrahy a doporučení zabezpečení. Můžete je zobrazit na portálu nebo prostřednictvím programových nástrojů. Je také možné, že budete muset některé nebo všechny tyto informace exportovat pro sledování s dalšími nástroji pro monitorování ve vašem prostředí. 

**Průběžný export** umožňuje plně přizpůsobit, *co* se bude exportovat a *kde* se bude přecházet. Můžete ho například nakonfigurovat tak, aby:

- Všechny výstrahy s vysokou závažností se odesílají do centra událostí Azure.
- Všechny střední nebo větší závěry závažnosti z kontroly ohrožení zabezpečení prověřování vašich serverů SQL se odesílají do konkrétního pracovního prostoru Log Analytics
- Konkrétní doporučení se doručí do centra událostí nebo do pracovního prostoru Log Analytics, kdykoli budou vygenerována. 

Tento článek popisuje, jak nakonfigurovat průběžný export na Log Analytics pracovní prostory nebo Azure Event Hubs.

> [!NOTE]
> Pokud potřebujete integrovat Security Center s SIEM, zkontrolujte [výstrahy streamování do Siem](export-to-siem.md) pro vaše možnosti.

> [!TIP]
> Security Center také nabízí možnost jednorázového ručního exportu do sdíleného svazku clusteru. Další informace najdete v [ručním jednorázovém exportu výstrah a doporučení](#manual-one-time-export-of-alerts-and-recommendations).


## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|Free|
|Požadované role a oprávnění:|<ul><li>**Správce zabezpečení** nebo **vlastník** skupiny prostředků</li><li>Oprávnění k zápisu pro cílový prostředek</li><li>Pokud používáte zásady Azure Policy ' DeployIfNotExist ' popsané níže, budete také potřebovat oprávnění k přiřazování zásad.</li></ul>|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![Yes](./media/icons/yes-icon.png) Čína gov (do centra událostí), ostatní gov|
|||





## <a name="set-up-a-continuous-export"></a>Nastavení průběžného exportu 

Průběžný export můžete nakonfigurovat ze Security Center stránek v Azure Portal, prostřednictvím REST API Security Center nebo ve velkém měřítku pomocí dodaných Azure Policy šablon. Níže můžete vybrat příslušnou kartu pro podrobnosti.

### <a name="use-the-azure-portal"></a>[**Použití Azure Portal**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-security-center-pages-in-azure-portal"></a>Konfigurace průběžného exportu ze stránek Security Center v Azure Portal

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

### <a name="use-the-rest-api"></a>[**Použití rozhraní REST API**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>Konfigurace průběžného exportu pomocí REST API

Průběžný export se dá nakonfigurovat a spravovat prostřednictvím [rozhraní API pro automatizaci](https://docs.microsoft.com/rest/api/securitycenter/automations)Azure Security Center. Pomocí tohoto rozhraní API můžete vytvářet nebo aktualizovat pravidla pro export do libovolného z následujících možných cílů:

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





### <a name="deploy-at-scale-with-azure-policy"></a>[**Nasazení ve velkém měřítku pomocí Azure Policy**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>Konfigurace průběžného exportu ve velkém měřítku pomocí zadaných zásad

Automatizace procesů monitorování a reakce na incidenty vaší organizace může výrazně zlepšit dobu potřebnou k prošetření a zmírnění incidentů zabezpečení.

K nasazení konfigurací průběžného exportu v celé organizaci použijte uvedené Azure Policy níže popsané zásady "DeployIfNotExist" a vytvořte a nakonfigurujte postupy průběžného exportu.

**Postup implementace těchto zásad**

1. V následující tabulce vyberte zásadu, kterou chcete použít:

    |Cíl  |Zásady  |ID zásady  |
    |---------|---------|---------|
    |Průběžný export do centra událostí|[Nasazení exportu do centra událostí pro Azure Security Center výstrahy a doporučení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |Průběžný export do pracovního prostoru Log Analytics|[Nasazení exportu do Log Analytics pracovního prostoru pro Azure Security Center výstrahy a doporučení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > Můžete je také najít hledáním Azure Policy:
    > 1. Otevřete Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Zahrnout zjištění zabezpečení – přepnout do konfigurace průběžného exportu":::
    > 2. V nabídce Azure Policy vyberte **definice** a vyhledejte je podle názvu. 

1. Na stránce příslušné Azure Policy vyberte **přiřadit**.
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="Zahrnout zjištění zabezpečení – přepnout do konfigurace průběžného exportu":::

1. Otevřete každou kartu a nastavte požadované parametry:
    1. Na kartě **základy** nastavte obor pro zásadu. Chcete-li použít centralizovanou správu, přiřaďte tuto zásadu ke skupině pro správu obsahující předplatná, která budou používat konfiguraci průběžného exportu. 
    1. Na kartě **parametry** nastavte podrobnosti skupiny prostředků a datového typu. 
        > [!TIP]
        > Každý parametr obsahuje popis, který vysvětluje možnosti, které máte k dispozici.
        >
        > Karta parametrů Azure Policy (1) poskytuje přístup k podobným možnostem konfigurace jako stránka průběžného exportu Security Center (2).
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="Zahrnout zjištění zabezpečení – přepnout do konfigurace průběžného exportu" lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. Pokud chcete toto přiřazení použít u stávajících předplatných, otevřete kartu **náprava** a vyberte možnost vytvoření úlohy nápravy.
1. Zkontrolujte stránku Souhrn a vyberte **vytvořit**.

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>Informace o exportu do pracovního prostoru Log Analytics

Pokud chcete analyzovat Azure Security Center Data v pracovním prostoru Log Analytics nebo použít výstrahy Azure společně s výstrahami Security Center, nastavte průběžný export do pracovního prostoru Log Analytics.

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics tabulek a schémat

Výstrahy a doporučení zabezpečení se ukládají do tabulek *SecurityAlert* a *SecurityRecommendations* . 

Název Log Analyticsho řešení obsahujícího tyto tabulky závisí na tom, jestli máte povolený Azure Defender: Security (' Security and Audit ') nebo SecurityCenterFree. 

> [!TIP]
> Chcete-li zobrazit data v cílovém pracovním prostoru, je nutné povolit jedno z těchto řešení **Security and Audit** nebo **SecurityCenterFree**.

![Tabulka * SecurityAlert * v Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Chcete-li zobrazit schémata událostí exportovaných datových typů, navštivte [Log Analytics schémat tabulek](https://aka.ms/ASCAutomationSchemas).


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>Zobrazení exportovaných výstrah a doporučení v Azure Monitor

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

Nyní se zobrazí nové výstrahy Azure Security Center nebo doporučení (v závislosti na konfigurovaných pravidlech průběžného exportu a podmínky, které jste definovali v pravidle Azure Monitor výstrahy), v části Azure Monitor výstrahy s automatickou aktivací skupiny akcí (Pokud je k dispozici).

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>Ruční export výstrah a doporučení v jednorázovém čase

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

- Přečtěte si další informace o [šablonách automatizace pracovních postupů](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).
- [Dokumentace ke službě Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Dokumentace ke službě Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Dokumentace k Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Export schémat datových typů](https://aka.ms/ASCAutomationSchemas)
