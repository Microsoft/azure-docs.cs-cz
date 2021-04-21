---
title: Fakturační & – cenové modely
description: Přehled o tom, jak ceny a modely fakturace pracují v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: a3c20dd85c94c359259cf69e25bb9083d56857fc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777144"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Cenové a fakturační modely pro Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) vám pomůže vytvářet a spouštět pracovní postupy automatizované integrace, které se můžou škálovat v cloudu. Tento článek popisuje, jak fakturační a cenové modely fungují pro službu Logic Apps a související prostředky. Konkrétní cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps). Informace o tom, jak plánovat, spravovat a monitorovat náklady, najdete v tématu [plánování a Správa nákladů pro Azure Logic Apps](plan-manage-costs.md).

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Ceny pro více tenantů

Cenový model spotřeby s platbou za použití se vztahuje na aplikace logiky, které běží ve veřejné, víceklientské Logic Apps službě. Všechna úspěšná a neúspěšná spuštění se měří a účtují.

Například požadavek, který aktivuje Trigger cyklického dotazování, je stále měřen jako spuštění i v případě, že tato aktivační událost byla vynechána a není vytvořena žádná instance pracovního postupu aplikace logiky.

| Položky | Description |
|-------|-------------|
| [Předdefinované](../connectors/built-in.md) triggery a akce | Spouštějte nativně ve službě Logic Apps a měří se za použití [ceny **akcí**](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>Například Trigger HTTP a Trigger požadavku jsou vestavěné triggery a akce HTTP a akce odpovědi jsou vestavěné akce. Integrované akce jsou také operace s daty, operace dávky, operace s proměnnými a [Akce řízení pracovního postupu](../connectors/built-in.md), jako jsou například smyčky, podmínky, přepínače, paralelní větve atd. |
| Triggery a akce [konektoru Standard](../connectors/managed.md) <p><p>Triggery a akce [vlastního konektoru](../connectors/apis-list.md#custom-apis-and-connectors) | Měřeno pomocí [ceny za konektor Standard](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Aktivační události a akce [Enterprise Connectoru](../connectors/managed.md) | Měřeno pomocí [ceny za konektor Enterprise](https://azure.microsoft.com/pricing/details/logic-apps/). Během veřejné verze Preview se ale podnikové konektory měří podle ceny za [konektor *Standard*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Akce uvnitř [smyček](logic-apps-control-flow-loops.md) | Každá akce, která je spuštěna ve smyčce, je měřena pro každý cyklus cyklů, který běží. <p><p>Předpokládejme například, že máte smyčku "for each", která obsahuje akce, které zpracovávají seznam. Služba Logic Apps měřiče každé akce spuštěné v této smyčce vynásobením počtu položek seznamu s počtem akcí ve smyčce a přidá akci, která spustí smyčku. Proto je výpočet pro seznam 10 položek (10 * 1) + 1, což vede ke zpracování 11 akcí. |
| Opakované pokusy | Chcete-li zpracovat základní výjimky a chyby, můžete nastavit [zásady opakování](logic-apps-exception-handling.md#retry-policies) pro aktivační události a akce, kde jsou podporovány. Tyto pokusy spolu s původní žádostí se účtují podle sazeb na základě toho, jestli aktivační událost nebo akce má vestavěný, standardní nebo Podnikový typ. Například akce, která se spouští se dvěma opakovanými pokusy, se účtuje za 3 provádění akcí. |
| [Uchovávání dat a spotřeba úložiště](#data-retention) | Měřeno pomocí ceny za uchování dat, kterou najdete na stránce s cenami za [Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)v tabulce s **podrobnostmi o cenách** . |
|||

Další informace najdete v následujících článcích:

* [Zobrazit metriky pro spuštění a spotřebu úložiště](plan-manage-costs.md#monitor-billing-metrics)
* [Omezení v Azure Logic Apps](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Bez měření

* Triggery, které se přeskočily kvůli nesplnění podmínkám
* Akce, které se nespustily, protože aplikace logiky se zastavila před dokončením
* [Zakázané aplikace logiky](#disabled-apps)

### <a name="other-related-resources"></a>Další související prostředky

Logic Apps fungují s ostatními souvisejícími prostředky, jako jsou účty pro integraci, místní brány dat a prostředí ISEs (Integration Service Environment). Další informace o cenách těchto prostředků najdete v těchto částech dále v tomto tématu:

* [On-premises data gateway (Místní brána dat)](#data-gateway)
* [Cenový model účtu pro integraci](#integration-accounts)
* [Cenový model ISE](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Tipy pro odhad nákladů na spotřebu

Pokud vám pomůžete odhadnout náklady na přesnější spotřebu, přečtěte si tyto tipy:

* Vezměte v úvahu možný počet zpráv nebo událostí, které mohou být doručeny v libovolném dni, nikoli na základě výpočtů pouze v intervalu dotazování.

* Když událost nebo zpráva splňuje kritéria triggeru, mnoho triggerů se okamžitě pokusí přečíst všechny ostatní čekající události nebo zprávy, které splňují kritéria. To znamená, že i když vyberete delší interval dotazování, Trigger se aktivuje na základě počtu čekajících událostí nebo zpráv, které jsou způsobilé pro spouštění pracovních postupů. Mezi triggery, které následují toto chování, patří Azure Service Bus a Azure Event hub.

  Předpokládejme například, že jste nastavili aktivační událost, která každý den kontroluje koncový bod. Když aktivační událost zkontroluje koncový bod a najde 15 událostí, které splňují kritéria, Trigger se aktivuje a spustí odpovídající Workflow 15 krát. Služba Logic Apps měřiče všech akcí, které provádí tyto 15 pracovních postupů, včetně požadavků na triggery.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>Ceny ISE

Pevný cenový model se vztahuje na aplikace logiky, které běží v [ *prostředí ISE (Integration Service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). ISE se účtuje pomocí [ceny za prostředí integrační služby](https://azure.microsoft.com/pricing/details/logic-apps), která závisí na [úrovni ISE nebo *skladové jednotky*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) , kterou vytvoříte. Tyto ceny se liší od cen za více tenantů, protože platíte za rezervovanou kapacitu a vyhrazené prostředky bez ohledu na to, jestli je používáte, nebo ne.

| SKU ISE | Description |
|---------|-------------|
| **Premium** | Základní jednotka má [pevnou kapacitu](logic-apps-limits-and-config.md#integration-service-environment-ise) a účtuje se [za hodinovou SAZBu za SKU úrovně Premium](https://azure.microsoft.com/pricing/details/logic-apps). Pokud potřebujete větší propustnost, můžete [Přidat další jednotky škálování](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) při vytváření ISE nebo později. Každá jednotka škálování se účtuje [hodinovou sazbou, která je přibližně polovinu základní jednotkové sazby](https://azure.microsoft.com/pricing/details/logic-apps). <p><p>Informace o kapacitě a omezeních najdete [v tématu omezení ISE v Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Vývojář** | Základní jednotka má [pevnou kapacitu](logic-apps-limits-and-config.md#integration-service-environment-ise) a účtuje se [za hodinovou sazbu za SKU pro vývojáře](https://azure.microsoft.com/pricing/details/logic-apps). Tato SKU ale nemá žádnou smlouvu o úrovni služeb (SLA), schopnost horizontálního navýšení kapacity ani redundanci během recyklace, což znamená, že se můžete setkat s prodlevami nebo výpadky. Aktualizace back-endu můžou službu obcházet bez výpadků. <p><p>**Důležité**: Ujistěte se, že jste tuto skladovou položku používali jenom pro zkoumání, experimenty, vývoj a testování – ne pro produkční nebo výkonnostní testování. <p><p>Informace o kapacitě a omezeních najdete [v tématu omezení ISE v Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

### <a name="included-at-no-extra-cost"></a>Zahrnuto bez dalších poplatků

| Položky | Description |
|-------|-------------|
| [Předdefinované](../connectors/built-in.md) triggery a akce | Zobrazit **základní** popisek a spustit ve stejném ISE jako vaše aplikace logiky |
| [Standardní konektory](../connectors/managed.md) <p><p>[Podnikové konektory](../connectors/managed.md#enterprise-connectors) | -Spravované konektory, které zobrazují popisek **ISE** , jsou speciálně navržené tak, aby fungovaly bez místní brány dat a běžely ve stejném ISE jako vaše aplikace logiky. Ceny ISE zahrnují tolik podnikových připojení, kolik potřebujete. <p><p>– Konektory, které nezobrazují ISE, se spouštějí ve službě víceklientské Logic Apps. Ceny ISE ale zahrnují tato spuštění pro Logic Apps, která běží v ISE. |
| Akce uvnitř [smyček](logic-apps-control-flow-loops.md) | Ceny ISE zahrnují každou akci, která běží ve smyčce pro každý cyklus smyčky. <p><p>Předpokládejme například, že máte smyčku "for each", která obsahuje akce, které zpracovávají seznam. Chcete-li získat celkový počet provedených akcí, vynásobte počet položek seznamu počtem akcí ve smyčce a přidejte akci, která spustí smyčku. Proto je výpočet pro seznam 10 položek (10 * 1) + 1, což vede ke zpracování 11 akcí. |
| Opakované pokusy | Chcete-li zpracovat základní výjimky a chyby, můžete nastavit [zásady opakování](logic-apps-exception-handling.md#retry-policies) pro aktivační události a akce, kde jsou podporovány. Ceny ISE zahrnují opakování společně s původní žádostí. |
| [Uchovávání dat a spotřeba úložiště](#data-retention) | Logic Apps v ISE neúčtují náklady na uchovávání dat a úložiště. |
| [Účty pro integraci](#integration-accounts) | Zahrnuje použití pro jednu vrstvu integračního účtu na základě SKU ISE, bez dalších poplatků. |
|||

Omezení informací najdete v tématu [omezení ISE v Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise).

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Účty pro integraci

[Účet pro integraci](../logic-apps/logic-apps-pricing.md#integration-accounts) je samostatný prostředek, který vytvoříte a propojíte s Logic Apps, abyste mohli prozkoumat, sestavovat a testovat integrační řešení B2B, která používají funkce [EDI](logic-apps-enterprise-integration-b2b.md) a [XML pro zpracování](logic-apps-enterprise-integration-xml.md) .

Azure Logic Apps nabízí tyto úrovně integračních účtů nebo vrstvy, které se [liší v cenách](https://azure.microsoft.com/pricing/details/logic-apps/) a [modelu fakturace](logic-apps-pricing.md#integration-accounts), a to na základě toho, jestli vaše aplikace logiky jsou založené na spotřebě nebo ISE:

| Úroveň | Description |
|------|-------------|
| **Basic** | U scénářů, kde chcete, aby zpracování zpráv fungovalo jako malý obchodní partner, který má vztah obchodního partnera s větší obchodní entitou. <p><p>Podporováno Logic Apps smlouvou SLA. |
| **Standard** | V případech, kdy máte složitější vztahy B2B a větší počet entit, které musíte spravovat. <p><p>Podporováno Logic Apps smlouvou SLA. |
| **Free** | V případě průzkumnéch scénářů, nikoli v produkčních scénářích. Tato úroveň má omezení dostupnosti, propustnosti a využití oblasti. Například úroveň Free je k dispozici pouze pro veřejné oblasti v Azure, například Západní USA nebo jihovýchodní Asie, ale ne pro [Azure Čína 21Vianet](/azure/china/overview-operations) nebo [Azure Government](../azure-government/documentation-government-welcome.md). <p><p>**Poznámka**: není podporována smlouvou SLA Logic Apps. |
|||

Informace o omezeních účtu pro integraci najdete v tématu [omezení a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits), jako například:

* [Omezení účtů pro integraci na předplatné Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Omezuje různé artefakty na účet pro integraci](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). Mezi artefakty patří obchodní partneři, smlouvy, mapy, schémata, sestavení, certifikáty, konfigurace dávek a tak dále.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Účty pro integraci pro Logic Apps založené na spotřebě

Účty pro integraci se účtují pomocí pevného [účtu pro integraci](https://azure.microsoft.com/pricing/details/logic-apps/) , který je založený na úrovni účtu, kterou používáte.

### <a name="ise-based-logic-apps"></a>Logic Apps založené na ISE

ISE zahrnuje jeden účet pro integraci na základě skladové položky ISE bez dalších poplatků. V případě dalších poplatků můžete vytvořit další účty pro integraci pro ISE, abyste mohli využít [Celkový limit ISE](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Přečtěte si další informace o [cenovém modelu ISE](#fixed-pricing) výše v tomto tématu.

| SKU ISE | Zahrnutý účet integrace | Další náklady |
|---------|------------------------------|-----------------|
| **Premium** | Jediný účet pro integraci [Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Až 19 dalších standardních účtů. Nejsou povoleny žádné bezplatné nebo základní účty. |
| **Vývojář** | Jeden [bezplatný](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) účet pro integraci | Až 19 dalších standardních účtů, pokud už máte bezplatný účet, nebo 20 celkových standardních účtů, pokud nemáte bezplatný účet. Nejsou povoleny žádné základní účty. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Uchovávání dat a spotřeba úložiště

Všechny vstupy a výstupy v historii spuštění aplikace logiky jsou uložené a měřené na základě [doby trvání běhu aplikace a doby uchování historie](logic-apps-limits-and-config.md#run-duration-retention-limits).

* U Logic Apps ve víceklientské Logic Apps službě se spotřeba úložiště účtuje za pevnou cenu, kterou najdete na stránce s cenami [Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps)v tabulce s **podrobnostmi o cenách** .

* Pro Logic Apps v ISEs využití úložiště neúčtují náklady na uchovávání dat.

Pokud chcete monitorovat využití spotřeby úložiště, přečtěte si téma [zobrazení metrik pro spouštění a spotřebu úložiště](plan-manage-costs.md#monitor-billing-metrics).

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Místní brána dat

Místní [Brána dat](../logic-apps/logic-apps-gateway-install.md) je samostatný prostředek, který vytvoříte, aby vaše aplikace logiky měly přístup k místním datům pomocí specifických konektorů podporovaných branou. Na operace konektorů, které se spouštějí prostřednictvím brány, se účtují poplatky, ale samotná brána neúčtuje žádné poplatky.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>Zakázané aplikace logiky

Zakázané aplikace logiky se neúčtují, protože nemůžou vytvářet nové instance, když jsou zakázané. Po zakázání aplikace logiky může kterákoli z aktuálně spuštěných instancí nějakou dobu trvat, než se úplně zastaví.

## <a name="next-steps"></a>Další kroky

* [Plánování a Správa nákladů na Azure Logic Apps](plan-manage-costs.md)
