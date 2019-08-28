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
ms.openlocfilehash: 9d52526f87f20a855c045f4e565793ac29abf04a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101001"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrace řešení zabezpečení v Azure Security Center
Tento dokument vám pomůže se správou řešení zabezpečení již propojených s Azure Security Center a s přidáním nových řešení.

> [!NOTE]
> Podmnožina řešení zabezpečení byla vyřazena z 31. července 2019. Další informace a alternativní služby najdete v tématu [vyřazení funkcí Security Center (červenec 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Integrovaná řešení zabezpečení Azure
Security Center umožňuje snadné povolení integrovaných řešení zabezpečení v Azure. Mezi výhody patří:

- **Zjednodušené nasazení**: Security Center nabízí zjednodušené zřizování integrovaných partnerských řešení. Pro řešení, jako jsou antimalware a hodnocení ohrožení zabezpečení, může Security Center zřídit potřebného agenta na virtuálních počítačích, a pro zařízení brány firewall může Security Center zařídit většinu požadované konfigurace sítě.
- **Integrované detekce**: Události zabezpečení z partnerských řešení se automaticky shromažďují, agregují a zobrazují v rámci výstrah a incidentů služby Security Center. Tyto události jsou také sloučeny s detekcemi z jiných zdrojů a poskytují pokročilé možnosti detekce hrozeb.
- **Sjednocené monitorování stavu a Správa**: Zákazníci můžou pomocí integrovaných událostí stavu monitorovat všechna partnerská řešení na první pohled. Je dostupná základní správa se snadným přístupem k pokročilému nastavení s použitím partnerského řešení.

Integrovaná řešení zabezpečení v současné době zahrnují posouzení ohrožení zabezpečení [Qualys](https://www.qualys.com/public-cloud/#azure) a [Rapid7](https://www.rapid7.com/products/insightvm/) a firewall webových aplikací Microsoft Application Gateway.

> [!NOTE]
> Security Center neinstaluje agenta Microsoft Monitoring Agent na partnerská virtuální zařízení, protože většina dodavatelů zabezpečení na svých zařízeních zakazuje spouštění externích agentů.
>
>

## <a name="how-security-solutions-are-integrated"></a>Způsob integrace řešení zabezpečení
Řešení zabezpečení Azure nasazená ze služby Security Center se automaticky připojí. Můžete taky připojit další zdroje dat zabezpečení, včetně počítačů, které jsou spuštěné místně nebo v jiných cloudech.

![Integrace partnerských řešení](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Správa integrovaných řešení zabezpečení Azure a dalších zdrojů dat

1. Přihlaste se k webu [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

2. V **nabídce Microsoft Azure** vyberte **Security Center**. Otevře se obrazovka **Security Center – Přehled**.

3. V nabídce služby Security Center vyberte **Řešení zabezpečení**.

   ![Přehled služby Security Center](./media/security-center-partner-integration/overview.png)

V části **Řešení zabezpečení** můžete zobrazit informace o stavu integrovaných řešení zabezpečení Azure a provádět základní úlohy správy.

### <a name="connected-solutions"></a>Propojená řešení

Část **Připojená řešení** obsahuje řešení zabezpečení, která jsou aktuálně připojená ke službě Security Center, a informace o stavu jednotlivých řešení.  

![Propojená řešení](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Stav partnerského řešení může být:

* V pořádku (zelená) – nedochází k žádnému problému se stavem.
* Není v pořádku (červená) – Existuje problém stavu, které si žádá okamžitou pozornost.
* Problémy se stavem (oranžová) – řešení zastavilo hlášení stavu.
* Neuvedeno (šedá) – řešení ještě neohlásilo nic, stav řešení může být neohlášený, pokud byl nedávno připojen a stále je nasazený, nebo nejsou k dispozici žádná data o stavu.

> [!NOTE]
> Pokud data o stavu nejsou k dispozici, Security Center zobrazuje datum a čas poslední přijaté události, která označuje, jestli řešení hlásí nebo ne. Pokud nejsou k dispozici žádná data o stavu a během posledních 14 dnů nejsou přijímány žádné výstrahy, Security Center znamená, že řešení není v pořádku nebo není hlášeno.
>
>

1. Vyberte **zobrazení** pro další informace a možnosti, které zahrnují:

   - **Konzola řešení**. Otevře prostředí pro správu tohoto řešení.
   - **Propojte virtuální počítač**. Otevře okno propojit aplikace. Tady můžete ke svému partnerskému řešení připojit prostředky.
   - **Odstranit řešení**
   - **Nakonfigurujte**.

   ![Podrobné zobrazení partnerského řešení](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Zjištěná řešení

Security Center automaticky vyhledává řešení zabezpečení spuštěná v Azure, která však nejsou připojená ke službě Security Center, a zobrazuje je v části **Zjištěná řešení**. To zahrnuje řešení Azure, jako je [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), i partnerská řešení.

> [!NOTE]
> Funkce Zjištěná řešení vyžaduje úroveň Standard služby Security Center na úrovni předplatného. Další informace o cenových úrovních služby Security najdete na stránce s [cenami](security-center-pricing.md).
>
>

Pod řešením vyberte **PŘIPOJIT**, aby se řešení integrovalo se službou Security Center a vy jste dostávali upozornění na výstrahy zabezpečení.

![Zjištěná řešení](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

### <a name="add-data-sources"></a>Přidat zdroje dat

Část **Přidat zdroje dat** obsahuje další dostupné zdroje dat, které je možné připojit. Pokyny k přidání dat z některého z těchto zdrojů získáte kliknutím na **PŘIDAT**.

![Zdroje dat](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Export dat do SIEM

Zpracované události vytvořené pomocí Azure Security Center jsou publikovány v [protokolu aktivit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)Azure, což je jeden z typů protokolů, který je k dispozici prostřednictvím Azure monitor. Azure Monitor nabízí konsolidovaný kanál pro směrování všech dat monitorování do nástroje SIEM. To je prováděno streamování dat do centra událostí, kde je pak možné ho načíst do partnerského nástroje.

Tento kanál používá pro získání přístupu k datům monitorování z prostředí Azure [jeden kanál monitorování Azure](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) . Díky tomu můžete snadno nastavit nástroje pro systémů Siem a monitorování, které budou data využívat.

Následující části popisují, jak můžete nakonfigurovat data pro streamování do centra událostí. Tento postup předpokládá, že už máte Azure Security Center nakonfigurovaných ve vašem předplatném Azure.

Podrobný přehled

![Přehled vysoké úrovně](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Jaká jsou data zabezpečení Azure vystavená pro SIEM?

V této verzi zveřejňujeme [výstrahy zabezpečení.](../security-center/security-center-managing-and-responding-alerts.md) V nadcházejících verzích rozšiřujeme datovou sadu o doporučení zabezpečení.

### <a name="how-to-setup-the-pipeline"></a>Postup nastavení kanálu

#### <a name="create-an-event-hub"></a>Vytvoření centra událostí

Než začnete, je potřeba [vytvořit obor názvů Event Hubs](../event-hubs/event-hubs-create.md). Tento obor názvů a centrum událostí slouží jako cíl pro všechna data monitorování.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Streamování protokolu aktivit Azure do služby Event Hubs

Informace o Event Hubs najdete v následujícím článku v [protokolu aktivit streamování](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Instalace konektoru SIEM partnera 

Směrování dat monitorování do centra událostí pomocí Azure Monitor umožňuje snadnou integraci s nástroji pro SIEM a monitorování partnerů.

Seznam [podporovaných systémů Siem](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub) najdete na následujícím odkazu.

### <a name="example-for-querying-data"></a>Příklad pro dotazování dat 

Tady je několik dotazů Splunk, které můžete použít k získání dat výstrah:

| **Popis dotazu** | **Dotaz** |
|----|----|
| Všechny výstrahy| index = hlavní Microsoft. Security/umístění/výstrahy|
| Shrnout počet operací podle jejich názvu| index = Main sourceType = "Amal: Security" \| Table \| NázevOperace stat Count podle OperationName|
| Získat informace o výstrahách: Čas, název, stav, ID a předplatné | index = hlavní čas v tabulce \| \_Microsoft. Security/Locations/Alerts, vlastnosti. EventName, State, Properties. operationId, am_subscriptionId |


## <a name="next-steps"></a>Další postup

V tomto článku jste se naučili integrovat partnerská řešení do služby Security Center. Další informace o službě Security Center najdete v následujících článcích:

* [Monitorování stavu zabezpečení ve službě Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Získejte odpovědi na nejčastější dotazy k používání služby Security Center.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
