---
title: Integrace řešení zabezpečení v Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak Azure Security Center umožňuje integrací s partnerskými řešeními zvýšit celkové zabezpečení vašich prostředků Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 0479918ad2afa99dcd9f18d3ba81f94a3e262e9e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479376"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrace řešení zabezpečení v Azure Security Center
Tento dokument vám pomůže se správou řešení zabezpečení již propojených s Azure Security Center a s přidáním nových řešení.

> [!NOTE]
> Podmnožina řešení zabezpečení byla vyřazena z 31. července 2019. Další informace a alternativní služby najdete v tématu [vyřazení funkcí Security Center (červenec 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Integrovaná řešení zabezpečení Azure
Security Center umožňuje snadné povolení integrovaných řešení zabezpečení v Azure. Mezi výhody patří:

- **Zjednodušené nasazení:** Security Center nabízí zjednodušené zřízení integrovaných partnerských řešení. Pro řešení, jako jsou antimalware a posouzení ohrožení zabezpečení, Security Center může zřídit agenta na virtuálních počítačích. U zařízení s bránou firewall může Security Center pořídit většinu požadovaných konfigurací sítě.
- **Integrované detekce:** Události zabezpečení z partnerských řešení se automaticky shromažďují, agregují a zobrazují v rámci výstrah a incidentů služby Security Center. Tyto události jsou také sloučeny s detekcemi z jiných zdrojů a poskytují pokročilé možnosti detekce hrozeb.
- **Sjednocená správa a monitorování stavu:** Zákazníci můžou využít integrované události týkající se stavu k monitorování všech partnerských řešení na první pohled. Je dostupná základní správa se snadným přístupem k pokročilému nastavení s použitím partnerského řešení.

Integrovaná řešení zabezpečení v současné době zahrnují posouzení ohrožení zabezpečení [Qualys](https://www.qualys.com/public-cloud/#azure) a [Rapid7](https://www.rapid7.com/products/insightvm/) a firewall webových aplikací Microsoft Application Gateway.

> [!NOTE]
> Security Center neinstaluje Microsoft Monitoring Agent na Partnerská virtuální zařízení, protože většina dodavatelů zabezpečení brání externím agentům běžícím na jejich zařízeních.
>
>

## <a name="how-security-solutions-are-integrated"></a>Způsob integrace řešení zabezpečení
Řešení zabezpečení Azure nasazená ze služby Security Center se automaticky připojí. Můžete taky připojit další zdroje dat zabezpečení, včetně počítačů, které jsou spuštěné místně nebo v jiných cloudech.

![Integrace partnerských řešení](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Správa integrovaných řešení zabezpečení Azure a dalších zdrojů dat

1. Přihlaste se na web [Azure Portal ](https://azure.microsoft.com/features/azure-portal/).

2. V **nabídce Microsoft Azure** vyberte **Security Center**. Otevře se obrazovka **Security Center – Přehled**.

3. V nabídce služby Security Center vyberte **Řešení zabezpečení**.

   ![Přehled služby Security Center](./media/security-center-partner-integration/overview.png)

V **řešení zabezpečení**uvidíte stav integrovaných řešení zabezpečení Azure a spouštějte základní úlohy správy.

### <a name="connected-solutions"></a>Připojená řešení

Oddíl **připojená řešení** obsahuje řešení zabezpečení, která jsou aktuálně připojená k Security Center. Zobrazuje také stav jednotlivých řešení.  

![Připojená řešení](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Stav partnerského řešení může být:

* V pořádku (zelená) – žádné problémy se stavem.
* Chybné (červené) – existuje problém se stavem, který vyžaduje okamžitou pozornost.
* Problémy se stavem (oranžová) – řešení zastavilo hlášení stavu.
* Nehlášeno (šedá) – řešení ještě neohlásilo nic a nejsou k dispozici žádná data o stavu. Stav řešení může být neohlášený, pokud byl v poslední době připojen a stále ještě probíhá jeho nasazení.

> [!NOTE]
> Pokud data o stavu nejsou k dispozici, Security Center zobrazuje datum a čas poslední přijaté události, která označuje, jestli řešení hlásí nebo ne. Pokud nejsou k dispozici žádná data o stavu a během posledních 14 dnů nebyla přijata žádná výstraha, Security Center znamená, že řešení není v pořádku nebo není hlášeno.
>
>

1. Vyberte **zobrazení** pro další informace a možnosti, jako například:

   - **Konzola řešení**. Otevře prostředí pro správu tohoto řešení.
   - **Propojte virtuální počítač**. Otevře stránku propojit aplikace. Tady můžete ke svému partnerskému řešení připojit prostředky.
   - **Odstranit řešení**
   - **Nakonfigurujte**.

   ![Podrobné zobrazení partnerského řešení](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Zjištěná řešení

Security Center automaticky vyhledá řešení zabezpečení běžící v Azure, ale nepřipojená k Security Center a zobrazí řešení v části **zjištěná řešení** . Mezi tato řešení patří řešení Azure, jako jsou [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)a partnerská řešení.

> [!NOTE]
> Funkce Zjištěná řešení vyžaduje úroveň Standard služby Security Center na úrovni předplatného. Další informace o cenových úrovních najdete v tématu [ceny](security-center-pricing.md) .
>
>

Vyberte **připojit** v rámci řešení, které chcete integrovat s Security Center a upozorňovat na výstrahy zabezpečení.

### <a name="add-data-sources"></a>Přidat zdroje dat

Část **Přidat zdroje dat** obsahuje další dostupné zdroje dat, které je možné připojit. Pokyny k přidání dat z některého z těchto zdrojů získáte kliknutím na **PŘIDAT**.

![Zdroje dat](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Export dat do SIEM

> [!NOTE]
> Podrobnosti o jednodušší metodě (aktuálně ve verzi Preview) pro export dat do SIEM najdete v tématu [Export výstrah zabezpečení a doporučení (Preview)](continuous-export.md). Nová metoda nepoužívá protokol aktivit jako prostředníka a umožňuje přímé exportu z Security Center Event Hubs (a následně k vašemu SIEM). podporuje také export doporučení zabezpečení.


Můžete nakonfigurovat systémů Siem nebo jiné nástroje pro monitorování, které budou přijímat události Azure Security Center.

Všechny události z Azure Security Center jsou publikované v [protokolu aktivit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)Azure Azure monitor. Azure Monitor používá [konsolidovaný kanál](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) ke streamování dat do centra událostí, kde se pak může získat do nástroje pro monitorování.

Následující části popisují, jak můžete nakonfigurovat data pro streamování do centra událostí. Tento postup předpokládá, že už máte Azure Security Center nakonfigurovaných ve vašem předplatném Azure.

### <a name="high-level-overview"></a>Podrobný přehled

![Přehled vysoké úrovně](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Jaká jsou data zabezpečení Azure vystavená pro SIEM?

V této verzi zveřejňujeme [výstrahy zabezpečení.](../security-center/security-center-managing-and-responding-alerts.md) V nadcházejících verzích rozšiřujeme datovou sadu o doporučení zabezpečení.

### <a name="how-to-set-up-the-pipeline"></a>Jak nastavit kanál

#### <a name="create-an-event-hub"></a>Vytvoření centra událostí

Než začnete, [vytvořte obor názvů Event Hubs](../event-hubs/event-hubs-create.md) – cíl pro všechna data monitorování.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Streamování protokolu aktivit Azure do služby Event Hubs

V následujícím článku se zobrazí [Protokol aktivit streamování, který Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Instalace konektoru SIEM partnera 

Směrování dat monitorování do centra událostí pomocí Azure Monitor umožňuje snadnou integraci s nástroji pro SIEM a monitorování partnerů.

Seznam [podporovaných systémů Siem](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-resource-logs-sent-to-an-event-hub) najdete v následujícím článku.

### <a name="example-for-querying-data"></a>Příklad pro dotazování dat 

Tady jsou některé dotazy Splunk, které můžete použít k získání údajů o výstrahách:

| **Popis dotazu** | **Dotaz** |
|----|----|
| Všechny výstrahy| index = hlavní Microsoft. Security/umístění/výstrahy|
| Shrnout počet operací podle jejich názvu| index = Main sourceType = "Amal: Security" \| Table NázevOperace \| stat Count by OperationName|
| Získat informace o výstrahách: čas, název, stav, ID a předplatné | index = hlavní Microsoft. Security/umístění/výstrahy \| tabulce \_čas, vlastnosti. eventName, State, Properties. operationId, am_subscriptionId |


## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili integrovat partnerská řešení do služby Security Center. Další informace o službě Security Center najdete v následujících článcích:

* [Monitorování stavu zabezpečení ve službě Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Získejte odpovědi na nejčastější dotazy k používání služby Security Center.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
