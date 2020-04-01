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
ms.openlocfilehash: f7a1eccd76313c5b3bc74a5b5ebdbcd202ca6841
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435754"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrace řešení zabezpečení v Azure Security Center
Tento dokument vám pomůže se správou řešení zabezpečení již propojených s Azure Security Center a s přidáním nových řešení.

> [!NOTE]
> 31. července 2019 byla vyřazena podmnožina bezpečnostních řešení. Další informace a alternativní služby naleznete v [tématu Retirement of Security Center features (Červenec 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Integrovaná řešení zabezpečení Azure
Security Center umožňuje snadné povolení integrovaných řešení zabezpečení v Azure. Mezi výhody patří:

- **Zjednodušené nasazení:** Security Center nabízí zjednodušené zřízení integrovaných partnerských řešení. U řešení, jako je antimalwarový program a posouzení ohrožení zabezpečení, může Centrum zabezpečení zřídit agenta ve virtuálních počítačích. U zařízení brány firewall se centrum zabezpečení může postarat o velkou část požadované konfigurace sítě.
- **Integrovaná zjišťování**: Události zabezpečení z partnerských řešení jsou automaticky shromažďovány, agregovány a zobrazovány jako součást výstrah a incidentů centra zabezpečení. Tyto události jsou také sloučeny s detekcemi z jiných zdrojů a poskytují pokročilé možnosti detekce hrozeb.
- **Sjednocená správa a monitorování stavu:** Zákazníci můžou využít integrované události týkající se stavu k monitorování všech partnerských řešení na první pohled. Je dostupná základní správa se snadným přístupem k pokročilému nastavení s použitím partnerského řešení.

V současné době integrovaná řešení zabezpečení zahrnují posouzení zranitelnosti společností [Qualys](https://www.qualys.com/public-cloud/#azure) a [Rapid7](https://www.rapid7.com/products/insightvm/) a brána firewall webových aplikací Microsoft Application Gateway.

> [!NOTE]
> Centrum zabezpečení nenainstaluje agenta Log Analytics do partnerských virtuálních zařízení, protože většina dodavatelů zabezpečení zakazuje externí agenty spuštěné na svých zařízeních.
>
>

## <a name="how-security-solutions-are-integrated"></a>Způsob integrace řešení zabezpečení
Řešení zabezpečení Azure nasazená ze služby Security Center se automaticky připojí. Můžete také připojit další zdroje dat zabezpečení, včetně počítačů spuštěných místně nebo v jiných cloudech.

![Integrace partnerských řešení](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Správa integrovaných řešení zabezpečení Azure a dalších zdrojů dat

1. Přihlaste se k [portálu Azure](https://azure.microsoft.com/features/azure-portal/).

2. V **nabídce Microsoft Azure** vyberte **Security Center**. Otevře se obrazovka **Security Center – Přehled**.

3. V nabídce služby Security Center vyberte **Řešení zabezpečení**.

   ![Přehled služby Security Center](./media/security-center-partner-integration/overview.png)

V **řešení zabezpečení**můžete zobrazit stav integrovaných řešení zabezpečení Azure a spustit základní úlohy správy.

### <a name="connected-solutions"></a>Připojená řešení

Část **Připojená řešení** obsahuje řešení zabezpečení, která jsou aktuálně připojena k centru zabezpečení. Zobrazuje také stav každého řešení.  

![Připojená řešení](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Stav partnerského řešení může být:

* Zdravé (zelené) - žádné zdravotní problémy.
* Nezdravé (červené) - je tu zdravotní problém, který vyžaduje okamžitou pozornost.
* Zdravotní problémy (oranžová) - řešení přestalo hlásit své zdraví.
* Není hlášeno (šedé) - řešení zatím nic nenahlásilo a nejsou k dispozici žádné údaje o zdravotním stavu. Stav řešení může být nehlášené, pokud byl připojen v poslední době a je stále nasazuje.

> [!NOTE]
> Pokud nejsou k dispozici údaje o stavu, centrum zabezpečení zobrazí datum a čas poslední přijaté události, které označují, zda se řešení hlásí či nikoli. Pokud nejsou k dispozici žádná data o stavu a během posledních 14 dnů nebyly přijaty žádné výstrahy, Centrum zabezpečení indikuje, že řešení není v pořádku nebo není nahlašováno.
>
>

1. Vyberte **ZOBRAZIT** pro další informace a možnosti, jako jsou:

   - **Konzola řešení**. Otevírá prostředí pro správu tohoto řešení.
   - **Virtuální hospova**propojení . Otevře stránku Aplikace propojení. Tady můžete ke svému partnerskému řešení připojit prostředky.
   - **Odstranit řešení**.
   - **Konfigurace**.

   ![Podrobné zobrazení partnerského řešení](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Zjištěná řešení

Security Center automaticky zjišťuje řešení zabezpečení spuštěná v Azure, ale není připojena k Centru zabezpečení, a zobrazí řešení v části **Zjištěná řešení.** Mezi tato řešení patří řešení Azure, jako je [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)a partnerská řešení.

> [!NOTE]
> Funkce Zjištěná řešení vyžaduje úroveň Standard služby Security Center na úrovni předplatného. Další informace o cenových úrovních najdete v [tématu Ceny.](security-center-pricing.md)
>
>

Vyberte **CONNECT** v části řešení pro integraci s Security Center a být upozorněni na výstrahy zabezpečení.

### <a name="add-data-sources"></a>Přidat zdroje dat

Část **Přidat zdroje dat** obsahuje další dostupné zdroje dat, které je možné připojit. Pokyny k přidání dat z některého z těchto zdrojů získáte kliknutím na **PŘIDAT**.

![Zdroje dat](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Export dat do SIEM

> [!NOTE]
> Podrobnosti o jednodušší metodě (aktuálně ve verzi Preview) pro export dat do SIEM naleznete v [tématu Export výstrah zabezpečení a doporučení (Náhled).](continuous-export.md) Nová metoda nepoužívá protokol aktivit jako intermediator a umožňuje přímý export z Centra zabezpečení do centra událostí (a potom do aplikace SIEM), podporuje také export doporučení zabezpečení.


Můžete nakonfigurovat siemnebo jiné nástroje pro monitorování pro příjem událostí Azure Security Center.

Všechny události z Azure Security Center se publikují do protokolu aktivit Azure [Azure monitoru](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)Azure . Azure Monitor používá [konsolidovaný kanál](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) k streamování dat do centra událostí, kde je pak možné navádět do vašeho monitorovacího nástroje.

Další části popisují, jak můžete nakonfigurovat data, která mají být přenášena datovým proudem do centra událostí. Postup předpokládá, že už máte Azure Security Center nakonfigurované ve vašem předplatném Azure.

### <a name="high-level-overview"></a>Podrobný přehled

![Přehled na vysoké úrovni](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Co jsou data zabezpečení Azure vystavená SIEM?

V této verzi zveřejňujeme [výstrahy zabezpečení.](../security-center/security-center-managing-and-responding-alerts.md) V nadcházejících verzích obohatíme datovou sadu o bezpečnostní doporučení.

### <a name="how-to-set-up-the-pipeline"></a>Jak nastavit potrubí

#### <a name="create-an-event-hub"></a>Vytvoření centra událostí

Než začnete, [vytvořte obor názvů Event Hubs](../event-hubs/event-hubs-create.md) – cíl pro všechna data monitorování.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Streamování protokolu aktivit Azure do center událostí

Podívejte se na následující článek [streamovat protokol aktivit do centra událostí](../azure-monitor/platform/activity-logs-stream-event-hubs.md).

#### <a name="install-a-partner-siem-connector"></a>Instalace partnerského konektoru SIEM 

Směrování dat monitorování do centra událostí pomocí Azure Monitoru umožňuje snadnou integraci s partnerským SIEM a nástroji pro monitorování.

Seznam [podporovaných siem naleznete](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)v následujícím článku .

### <a name="example-for-querying-data"></a>Příklad dotazování dat 

Zde jsou některé dotazy Splunk, které můžete použít k vytažení dat výstrah:

| **Popis dotazu** | **Dotaz** |
|----|----|
| All Alerts| index=hlavní Microsoft.Security/locations/alerts|
| Shrnout počet operací podle jejich názvu| index=main sourcetype="amal:security" \| operace \| tabulkyPočet statistik podle operationName|
| Informace o získání upozornění: Čas, název, stát, ID a předplatné | index=hlavní čas tabulky \| \_Microsoft.Security/locations/alerts, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili integrovat partnerská řešení do služby Security Center. Další informace o Centru zabezpečení naleznete v následujícím článku:

* [Monitorování stavu zabezpečení ve službě Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.