---
title: Ceny a fakturace – Azure Logic Apps | Dokumentace Microsoftu
description: Zjistěte, jak funguje ceny a fakturace pro Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 05/22/2019
ms.openlocfilehash: 776f79d7f32cf23943ecab4133e055993d30c7cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075066"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Cenový model pro Azure Logic Apps

[Služba Azure Logic Apps](../logic-apps/logic-apps-overview.md) pomáhá vytvářet a spouštět pracovní postupy automatizované integrace, který můžete škálovat v cloudu. Tento článek popisuje, jak fungují fakturaci a cenách pro Azure Logic Apps. Konkrétní informace o cenách najdete v části [cenami Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Cenový model spotřeby

Pro nové aplikace logiky, které běží ve službě Azure Logic Apps veřejné nebo "globální" platíte jenom za využité. Tyto aplikace logiky použít plán založený na spotřebě a cenový model. V definici aplikace logiky je každý krok akce. Například akce zahrnují:

* Aktivační události, které jsou zvláštní akce. Všechny aplikace logiky vyžaduje aktivační událost jako první krok.
* "Integrované" nebo nativní akce, třeba HTTP, volání funkce Azure API Management a tak dále
* Volání konektorech, jako je Outlook 365, Dropbox atd.
* Kroky toku, jako je například smyčky, podmíněné příkazy a řídí atd.

Služba Azure Logic Apps měření všechny akce, které běží ve vaší aplikaci logiky. Další informace o tom, jak funguje fakturace pro [triggery](#triggers) a [akce](#actions).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Oprava cenový model

[ *Prostředí integrační služby* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) zajišťuje privátní, izolované a vyhrazené pro vytvoření a spuštění aplikace logiky, které mají přístup k prostředkům ve virtuální síti Azure. Pro nové aplikace logiky, na kterých běží uvnitř ISE, platíte [pevná měsíční cena](https://azure.microsoft.com/pricing/details/logic-apps) pro integrované akce a triggery a také pro konektory úrovně Standard.

Vaše ISE také zahrnuje jeden konektor free Enterprise, který zahrnuje tolik *připojení* chcete. Využití dalších podnikových konektorů se účtuje na základě [cena spotřeby Enterprise](https://azure.microsoft.com/pricing/details/logic-apps). Pouze všeobecně dostupné podnikové konektory se účtuje cena spotřeby Enterprise. Podnikové konektory ve verzi Public preview se účtuje [standardní konektor míra](https://azure.microsoft.com/pricing/details/logic-apps).

> [!NOTE]
> V rámci ISE, integrované aktivační události a akce zobrazení **Core** označovat popisky a spouštění v prostředí ISE stejné jako aplikace logiky. Standard a podnikové konektory, které zobrazují **ISE** popisek spouštění v prostředí ISE stejné jako aplikace logiky. Konektory, které nechcete zobrazit popisek ISE spustit v globální službě Logic Apps.

Základní jednotka ISE chyba opravena kapacity, takže pokud potřebujete větší propustnost, můžete [přidávat další jednotky škálování](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), buď při vytváření nebo později. Aplikace logiky, které běží v prostředí ISE zbytečně náklady na uchovávání dat.

Konkrétní informace o cenách najdete v části [cenami Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="connectors"></a>

## <a name="connectors"></a>Konektory

Konektorů Azure Logic Apps pomůže tím, že poskytuje přístup k aplikacím logiky aplikace, služby a systémy v cloudu nebo lokálně [triggery](#triggers), [akce](#actions), nebo obojí. Konektory jsou klasifikovány jako Standard nebo Enterprise. Přehled o tyto konektory, naleznete v tématu [konektory pro Azure Logic Apps](../connectors/apis-list.md). Následující části obsahují další informace o jak fakturace se aktivuje a akce fungují.

<a name="triggers"></a>

## <a name="triggers"></a>Aktivační procedury

Aktivační události představují zvláštní akce, které vytvoří instanci aplikace logiky, když dojde k určité události. Aktivační události fungují různými způsoby, které ovlivňují, jak se měří aplikace logiky. Tady jsou různé druhy aktivačních událostí, které existují v Azure Logic Apps:

* **Cyklického dotazování aktivační událost**: Tato aktivační událost neustále kontroluje koncový bod pro zprávy, které splňují kritéria pro instanci aplikace logiky vytváření a spouštění pracovního postupu. I když žádné instanci aplikace logiky se vytvoří, Logic Apps měření každého požadavku dotazování služby jako spuštění. Zadejte interval dotazování, nastavte aktivační událost pomocí návrháře aplikace logiky.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Trigger Webhooku**: Tato aktivační událost čeká klientovi umožní odeslat žádost o konkrétní koncový bod. Jednotlivé požadavky odeslané na koncový bod webhooku se počítá jako spuštění akce. Například aktivační událost požadavek a HTTP Webhook jsou obě triggerů webhooků.

* **Trigger opakování**: Tento trigger vytvoří instanci aplikace logiky na základě intervalu opakování, které jste nastavili v aktivační události. Můžete například nastavit trigger opakování, která se spouští za tři dny nebo podle plánu složitější.

<a name="actions"></a>

## <a name="actions"></a>Akce

Služba Azure Logic Apps měření "integrované" akce, jako je například HTTP, jako nativní akce. Například integrované akce zahrnují HTTP volání, volání z Azure Functions nebo rozhraní API pro správu a řídí kroky toku, jako jsou podmínky, smyčky a příkazy switch. Každá akce má své vlastní typ akce. Například akce, které volají [konektory](https://docs.microsoft.com/connectors) typu "ApiConnection". Tyto konektory jsou klasifikovány jako standardní nebo podnikové konektory, které se měří na jejich základě [ceny](https://azure.microsoft.com/pricing/details/logic-apps). Podnikové konektory v *ve verzi Preview* se účtují jako konektory úrovně Standard.

Služba Azure Logic Apps měření všech úspěšných a neúspěšných akcí jako spuštění. Logic Apps však nebude monitorovat tyto akce:

* Akce, které získáte přeskočeno z důvodu nesplnění podmínky
* Akce, které nechcete spustit, protože aplikace logiky zastavil před dokončením instalace

Pro akce, které běží uvnitř smyčky Azure Logic Apps se počítá každé akce pro každý cyklus ve smyčce. Předpokládejme například, že máte smyčka "for each", který zpracovává seznam. Logic Apps měřiče akce v této smyčce vynásobením počtu seznam položek se počet akcí ve smyčce a přidá akci, která spustí smyčky. Ano, výpočtu seznam 10 položek se (10 * 1) + 1, což vede k 11 provedení akcí.

## <a name="disabled-logic-apps"></a>Zakázané logic apps

Zakázat logika, kterou aplikace se neúčtují, protože nelze vytvořit nové instance už zakázáno.
Když zakážete, aplikace logiky, všechny aktuálně spuštěné instance může trvat nějakou dobu, než úplně zastavit.

## <a name="integration-accounts"></a>Účty pro integraci

Oprava cenový model se vztahuje na [účty pro integraci](logic-apps-enterprise-integration-create-integration-account.md) kde můžete prozkoumat, vývoj a testování [B2B a EDI](logic-apps-enterprise-integration-b2b.md) a [zpracování XML](logic-apps-enterprise-integration-xml.md) funkce v Azure Logic Apps bez Další poplatky.
Může mít jeden účet pro integraci v každé oblasti Azure. Každý účet pro integraci můžete ukládat konkrétní [počet artefaktů](../logic-apps/logic-apps-limits-and-config.md), mezi které patří obchodní partnery, smlouvy, map, schémata, sestavení, certifikáty, konfigurace dávek a tak dále.

Služba Azure Logic Apps nabízí účty pro integraci Free, Basic a Standard. Na úrovních Basic a Standard jsou podporovány Logic Apps smlouvu o úrovni služeb (SLA), zatímco úroveň Free není podporováno smlouvou SLA a má omezení propustnosti a využití.

Volba mezi Free, Basic nebo Standard účet integrace:

* **Bezplatné**: Pro když budete chtít zkusit průzkumného testování scénářů, nikoli produkčních scénářů.

* **Základní**: Má pouze zpracování zpráv nebo tak, aby fungoval jako partnerský server pro malé firmy, která má vztah obchodního partnera s větší obchodní entitě.

* **Standard**: Když máte složitější vztahy B2B a vyšší počet entit, které je potřeba spravujte.

Konkrétní informace o cenách najdete v části [cenami Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="data-retention"></a>

## <a name="data-retention"></a>Uchovávání dat

S výjimkou logic apps, spusťte v prostředí integrační služby (ISE), všechny vstupy a výstupy, které jsou uloženy v historii spuštění aplikace logiky bude účtovat podle vaší aplikace logiky [spustit dobu uchování](logic-apps-limits-and-config.md#run-duration-retention-limits). Aplikace logiky, které běží v prostředí ISE zbytečně náklady na uchovávání dat. Konkrétní informace o cenách najdete v části [cenami Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

Chcete-li vám pomohou monitorovat spotřebu úložiště vaší aplikace logiky, můžete:

* Zobrazí počet jednotek úložiště v GB měsíčně používající vaši aplikaci logiky.
* Zobrazení historie spouštění vaší aplikace logiky velikosti pro vstupy a výstupy určité akce.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Spotřeba úložiště aplikace logiky zobrazení

1. Na webu Azure Portal najít a otevřít aplikaci logiky.

1. V nabídce aplikace logiky podle **monitorování**vyberte **metriky**.

1. V pravém podokně v části **název grafu**, z **metrika** seznamu vyberte **fakturaci využití pro spuštění spotřebu úložiště**.

   Tato metrika poskytuje počet využití jednotek úložiště v GB za měsíc, které se získávání účtují.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Zobrazit akce vstup a výstup velikosti

1. Na webu Azure Portal najít a otevřít aplikaci logiky.

1. V nabídce aplikace logiky, vyberte **přehled**.

1. V pravém podokně v části **historie běhů**, vyberte spuštění, které má vstupy a výstupy, které chcete zkontrolovat.

1. V části **běh aplikace logiky**, zvolte **detaily spuštění**.

1. V **detaily spuštění aplikace logiky** podokně, v tabulce akce, která obsahuje všechny akce stavu a doby trvání, vyberte akce, které chcete zobrazit.

1. V **akce aplikace logiky** podokno, najít velikosti pro vstupy a výstupy této akce se zobrazí pod **odkaz na vstupy** a **odkaz na výstupy**.

## <a name="next-steps"></a>Další postup

* [Další informace o Azure Logic Apps](logic-apps-overview.md)
* [Vytvoření první aplikace logiky](quickstart-create-first-logic-app-workflow.md)
