---
title: Integrace řešení zabezpečení v Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak Azure Security Center umožňuje integrací s partnerskými řešeními zvýšit celkové zabezpečení vašich prostředků Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: d94567800a9fd020784c9cb07b2c6824cd032509
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064277"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrace řešení zabezpečení v Azure Security Center
Tento dokument vám pomůže se správou řešení zabezpečení již propojených s Azure Security Center a s přidáním nových řešení.

> [!NOTE]
> Podmnožinu řešení zabezpečení se vyřadí dne 31. května 2019. Další informace a alternativní služby najdete v tématu [vyřazení Security Center nabízí (červenec 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Integrovaná řešení zabezpečení Azure
Security Center umožňuje snadné povolení integrovaných řešení zabezpečení v Azure. Mezi výhody patří:

- **Zjednodušené nasazení**: Security Center nabízí zjednodušené zřízení integrovaných partnerských řešení. Pro řešení, jako jsou antimalware a hodnocení ohrožení zabezpečení, může Security Center zřídit potřebného agenta na virtuálních počítačích, a pro zařízení brány firewall může Security Center zařídit většinu požadované konfigurace sítě.
- **Integrované detekce**: Události zabezpečení z partnerských řešení se automaticky shromažďují, agregují a zobrazují v rámci výstrah a incidentů služby Security Center. Tyto události jsou také sloučeny s detekcemi z jiných zdrojů a poskytují pokročilé možnosti detekce hrozeb.
- **Sjednocená Správa a sledování stavu**: Zákazníci můžou pomocí integrovaných událostí stavu monitorovat všechna partnerská řešení na první pohled. Je dostupná základní správa se snadným přístupem k pokročilému nastavení s použitím partnerského řešení.

V současné době integrovaných řešení zabezpečení zahrnují posouzení ohrožení zabezpečení podle [Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) a [Rapid7](https://www.rapid7.com/products/insightvm/) a Microsoft Application Gateway Web application firewall.

> [!NOTE]
> Security Center neinstaluje agenta Microsoft Monitoring Agent na partnerská virtuální zařízení, protože většina dodavatelů zabezpečení na svých zařízeních zakazuje spouštění externích agentů.
>
>

## <a name="how-security-solutions-are-integrated"></a>Způsob integrace řešení zabezpečení
Řešení zabezpečení Azure nasazená ze služby Security Center se automaticky připojí. Můžete také připojit další zdroje dat zabezpečení, včetně počítačů, které jsou spuštěné místně nebo v jiných cloudech.

![Integrace partnerských řešení](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Správa integrovaných řešení zabezpečení Azure a dalších zdrojů dat

1. Přihlaste se k webu [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

2. V **nabídce Microsoft Azure** vyberte **Security Center**. Otevře se obrazovka **Security Center – Přehled**.

3. V nabídce služby Security Center vyberte **Řešení zabezpečení**.

   ![Přehled služby Security Center](./media/security-center-partner-integration/overview.png)

V části **Řešení zabezpečení** můžete zobrazit informace o stavu integrovaných řešení zabezpečení Azure a provádět základní úlohy správy. Můžete také připojit další typy zdrojů dat zabezpečení, jako jsou upozornění služby Azure Active Directory Identity Protection a protokoly brány firewall ve formátu CEF (Common Event Format).

### <a name="connected-solutions"></a>Připojená řešení

Část **Připojená řešení** obsahuje řešení zabezpečení, která jsou aktuálně připojená ke službě Security Center, a informace o stavu jednotlivých řešení.  

![Připojená řešení](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Stav partnerského řešení může být:

* V pořádku (zelený) – není žádný problém se stavem.
* Není v pořádku (červená) – Existuje problém stavu, které si žádá okamžitou pozornost.
* Problémy se stavem (oranžová) – řešení přestalo hlásit svůj stav.
* Neuveden (šedá) – řešení neohlásil cokoli, ale stav řešení nemusí být uvedený, pokud byl nedávno připojený a ještě probíhá jeho nasazení, nebo je k dispozici žádná data o stavu.

> [!NOTE]
> Pokud není k dispozici data stavu, Security Center zobrazuje datum a čas poslední událost přijata označující, zda je řešení hlásí, nebo ne. Pokud je k dispozici žádná data o stavu a nejsou během posledních 14 dní přijaty žádné výstrahy, Security Center upozorňuje, že řešení není v pořádku, nebo není generování sestav.
>
>

1. Vyberte **zobrazení** pro další informace a možnosti, které obsahuje:

   - **Konzola řešení**. Otevře prostředí pro správu pro toto řešení.
   - **Propojení virtuálních počítačů**. Otevřete okno propojit aplikace. Tady můžete ke svému partnerskému řešení připojit prostředky.
   - **Odstranit řešení**.
   - **Konfigurace**.

   ![Podrobné zobrazení partnerského řešení](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Zjištěná řešení

Security Center automaticky vyhledává řešení zabezpečení spuštěná v Azure, která však nejsou připojená ke službě Security Center, a zobrazuje je v části **Zjištěná řešení**. To zahrnuje řešení Azure, jako je [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), i partnerská řešení.

> [!NOTE]
> Funkce Zjištěná řešení vyžaduje úroveň Standard služby Security Center na úrovni předplatného. Další informace o cenových úrovních služby Security najdete na stránce s [cenami](security-center-pricing.md).
>
>

Pod řešením vyberte **PŘIPOJIT**, aby se řešení integrovalo se službou Security Center a vy jste dostávali upozornění na výstrahy zabezpečení.

![Zjištěná řešení](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center také vyhledává řešení nasazená v předplatném, která dokážou přesměrovat protokoly CEF (Common Event Format). Zjistěte, jak ke službě Security Center [připojit řešení zabezpečení](quick-security-solutions.md) využívající protokoly CEF.

### <a name="add-data-sources"></a>Přidat zdroje dat

Část **Přidat zdroje dat** obsahuje další dostupné zdroje dat, které je možné připojit. Pokyny k přidání dat z některého z těchto zdrojů získáte kliknutím na **PŘIDAT**.

![Zdroje dat](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Export dat do SIEM

Zpracované události vytvořené službou Azure Security Center se publikují do Azure [protokolu aktivit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), jeden z protokolu typy dostupné v nástroji Azure Monitor. Azure Monitor nabízí konsolidované kanálu pro směrování všech dat monitorování do nástroje SIEM. To se provádí Streamovat data do centra událostí, kde ji potom je mohly vyžádat do nástroje partnera.

Tento kanál používá [Azure Monitoring jeden kanál](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) pro získání přístupu k data monitorování z vašeho prostředí Azure. To umožňuje snadno nastavit sady Siem a monitorovací nástroje tato data využívají.

Tento oddíl popisuje, jak nakonfigurovat data Streamovat do centra událostí. Kroky předpokládají, že už máte Azure Security Center nakonfigurovat ve svém předplatném Azure.

Podrobný přehled

![Základní přehled](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Co je Azure security data vystavená do systému SIEM?

V této verzi jsme vystavit [výstrahy zabezpečení.](../security-center/security-center-managing-and-responding-alerts.md) V nadcházejících vydáních jsme se rozšířit datové sady s doporučení týkající se zabezpečení.

### <a name="how-to-setup-the-pipeline"></a>Nastavení kanálu

#### <a name="create-an-event-hub"></a>Vytvoření centra událostí

Než začnete, budete muset [vytvořte obor názvů služby Event Hubs](../event-hubs/event-hubs-create.md). Tento obor názvů a centra událostí je cílem pro všemi daty monitorování.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Stream protokolů aktivit Azure do služby Event Hubs

Najdete v následujícím článku [streamování protokolu aktivit do služby Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Nainstalovat partnerský konektor SIEM 

Směrování dat monitorování do centra událostí pomocí Azure Monitor umožňuje snadnou integraci s partnerem SIEM a monitorovací nástroje.

Odkazovat na následující odkaz zobrazíte seznam [podporovány systémy Siem](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

### <a name="example-for-querying-data"></a>Příklad pro dotazování na data 

Tady je několik Splunk dotazů, které můžete použít k načítání dat výstrah:

| **Popis dotazu** | **Dotaz** |
|----|----|
| Všechny výstrahy| index = hlavní Microsoft.Security/locations/alerts|
| Shrnout počet operací podle názvu| index = hlavní sourcetype = "amal: zabezpečení" \| tabulky operationName \| statistiky count podle operationName|
| Získání informací o výstrahy: Čas, název, stavu, ID a předplatné | index = hlavní Microsoft.Security/locations/alerts \| tabulky \_čas, properties.eventName, stavu, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Další postup

V tomto článku jste se naučili integrovat partnerská řešení do služby Security Center. Další informace o službě Security Center najdete v následujících článcích:

* [Monitorování stavu zabezpečení ve službě Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Získejte odpovědi na nejčastější dotazy k používání služby Security Center.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
