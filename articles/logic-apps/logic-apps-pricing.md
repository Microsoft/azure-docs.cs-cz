---
title: Cenová & fakturační model
description: Přehled o tom, jak funguje model cen a fakturace pro Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: f47c7412bdd5ada1e50d1005b8e740e3f46ffd8d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536229"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Cenový model pro aplikace Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) vám pomůže vytvářet a spouštět pracovní postupy automatizované integrace, které se můžou škálovat v cloudu. Tento článek popisuje, jak fakturace a ceny fungují pro Azure Logic Apps. Cenové sazby najdete v tématu [Logic Apps Pricing](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Cenový model spotřeby

Pro nové aplikace logiky, které běží ve veřejné, "globální", víceklientské služby Azure Logic Apps, platíte jenom za to, co používáte. Tyto aplikace logiky používají plán a cenový model založený na spotřebě. Ve vaší aplikaci logiky každý krok je akce a Azure Logic Apps měří všechny akce, které běží ve vaší aplikaci logiky.

Akce zahrnují například:

* [Aktivační události](#triggers), což jsou speciální akce. Všechny aplikace logiky vyžadují aktivační událost jako první krok.

* ["Předdefinované" nebo nativní akce,](../connectors/apis-list.md#built-in) jako je HTTP, volání funkcí Azure a správa rozhraní API a tak dále

* Volání [spravovaných konektorů,](../connectors/apis-list.md#managed-connectors) jako je Outlook 365, Dropbox a tak dále

* [Řízení akcí pracovního postupu,](../connectors/apis-list.md#control-workflow) jako jsou smyčky, podmíněné příkazy a tak dále

[Standardní konektory](../connectors/apis-list.md#managed-connectors) se nabíjejí za [standardní cenu konektoru](https://azure.microsoft.com/pricing/details/logic-apps). Obecně dostupné [podnikové konektory](../connectors/apis-list.md#managed-connectors) se účtují podle ceny [konektoru Enterprise](https://azure.microsoft.com/pricing/details/logic-apps), zatímco konektory Public Preview Enterprise se účtují [za standardní cenu konektoru](https://azure.microsoft.com/pricing/details/logic-apps).

Přečtěte si další informace o tom, jak fakturace funguje na [úrovních aktivačních událostí](#triggers) a [akcí.](#actions) Nebo informace o omezeních najdete v [tématu Omezení a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Pevný cenový model

[ *Prostředí integrační služby* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) poskytuje izolovaný způsob, jak vytvářet a spouštět aplikace logiky, které mají přístup k prostředkům ve virtuální síti Azure. Aplikace logiky, které běží v ISE nevznikají náklady na uchovávání dat. Při vytváření ise a pouze při vytváření můžete zvolit [úroveň ISE nebo "Skladovou jednotku"](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), které mají různé [cenové sazby](https://azure.microsoft.com/pricing/details/logic-apps):

* **Prémiové pojistné** ISE: Základní jednotka této skladové jednotky má pevnou kapacitu, ale pokud potřebujete větší propustnost, můžete [přidat další jednotky škálování](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) během vytváření ISE nebo později. Omezení služby ISE najdete v [tématu Limity a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise).

* **Vývojář** ISE: Tato skladová položka nemá žádné možnosti pro škálování, žádná smlouva o úrovni služeb (SLA) a žádná publikovaná omezení. Tuto skladovou položku použijte pouze pro experimentování, vývoj a testování, nikoli pro testování výroby nebo výkonu.

Za aplikace logiky, které vytvoříte a spustíte v systému ISE, zaplatíte [pevnou měsíční cenu](https://azure.microsoft.com/pricing/details/logic-apps) za tyto funkce:

* [Integrované](../connectors/apis-list.md#built-in) aktivační události a akce

  V rámci služby ISE integrované aktivační události a akce zobrazují popisek **Core** a běží ve stejné službě ISE jako vaše aplikace logiky.

* [Standardní](../connectors/apis-list.md#managed-connectors) konektory a [podnikové](../connectors/apis-list.md#enterprise-connectors) konektory, které umožňují mít libovolný počet připojení enterprise

   Standardní a podnikové konektory, které zobrazují popisek **ISE,** běží ve stejné službě ISE jako aplikace logiky. Konektory, které nezobrazují popisek ISE spustit ve veřejné, "globální", víceklientské služby Logic Apps. Pevné měsíční ceny platí také pro konektory, které běží ve službě s více tenanty, když je používáte s aplikacemi logiky, které běží v ISE.

* [Využití integračního účtu](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) bez dalších nákladů na základě vaší [skladové položky ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):

  * **Prémiové pojistné** Skladová položka ISE: Jeden účet pro integraci [úrovně Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  * **Vývojář** SKU ISE: Jeden účet pro integraci [úrovně Free](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Každá skladová položka ISE je omezena na 5 účtů celkové integrace. Za příplatek můžete mít více účtů integrace na základě sku služby ISE:

  * **Prémiové pojistné** Skladová položka ISE: Až čtyři další standardní účty. Žádné bezplatné nebo základní účty.

  * **Vývojář** Skladová položka ISE: Buď až 4 další standardní účty, nebo až 5 celkových standardních účtů. Žádné základní účty.

  Další informace o omezeníúčtů integrace najdete v [tématu Omezení a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Další informace o [úrovních účtů integrace a jejich cenovém modelu](#integration-accounts) najdete dále v tomto tématu.

<a name="connectors"></a>

## <a name="connectors"></a>Konektory

Konektory Azure Logic Apps pomáhají aplikacím logiky přistupovat k aplikacím, službám a systémům v cloudu nebo v místním prostředí tím, že poskytují [aktivační události](#triggers), [akce](#actions)nebo obojí. Konektory jsou klasifikovány jako standardní nebo enterprise. Přehled těchto konektorů najdete v tématu [Konektory pro Azure Logic Apps](../connectors/apis-list.md). Pokud žádné předem sestavené konektory jsou k dispozici pro rozhraní API REST, které chcete použít v [aplikacích logiky,](https://docs.microsoft.com/connectors/custom-connectors)můžete vytvořit vlastní konektory , které jsou pouze obálky kolem těchto rozhraní API REST. Vlastní konektory se účtují jako standardní konektory. V následujících částech jsou uvedeny další informace o tom, jak fakturace aktivačních událostí a akcí funguje.

<a name="triggers"></a>

## <a name="triggers"></a>Aktivační události

Aktivační události jsou speciální akce, které vytvářejí instanci aplikace logiky, když dojde k určité události. Aktivační události působí různými způsoby, které ovlivňují způsob, jakým se měří aplikace logiky. Tady jsou různé druhy aktivačních událostí, které existují v Aplikacích Azure Logic:

* **Aktivační událost dotazování**: Tato aktivační událost neustále kontroluje koncový bod pro zprávy, které splňují kritéria pro vytvoření instance aplikace logiky a spuštění pracovního postupu. I v případě, že se nevytvoří žádná instance aplikace logiky, aplikace logiky měří každý požadavek na dotazování jako spuštění. Chcete-li zadat interval dotazování, nastavte aktivační událost prostřednictvím Návrháře aplikace logiky.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Aktivační událost webhooku**: Tato aktivační událost čeká na odeslání požadavku klienta do určitého koncového bodu. Každý požadavek odeslaný do koncového bodu webhooku se počítá jako spuštění akce. Například request a HTTP Webhook aktivační události jsou obě webové hák aktivační události.

* **Aktivační událost opakování**: Tato aktivační událost vytvoří instanci aplikace logiky na základě intervalu opakování, který jste nastavili v aktivační události. Můžete například nastavit aktivační událost opakování, která se spouští každé tři dny nebo podle složitějšího plánu.

<a name="actions"></a>

## <a name="actions"></a>Akce

Azure Logic Apps měří "předdefinované" akce, jako je http, jako nativní akce. Integrované akce například zahrnují volání HTTP, volání z Azure Functions nebo API Management a kroky toku řízení, jako jsou podmínky, smyčky a příkazy switch. Každá akce má svůj vlastní typ akce. Například akce, které volají [konektory](https://docs.microsoft.com/connectors) mají typ "ApiConnection". Tyto konektory jsou klasifikovány jako standardní nebo podnikové konektory, které jsou měřeny na základě jejich příslušných [cen](https://azure.microsoft.com/pricing/details/logic-apps). Podnikové konektory ve *verzi Preview* se účtují jako standardní konektory.

Aplikace Azure Logic Apps měří všechny úspěšné a neúspěšné akce jako spuštění. Aplikace Logika však neměří tyto akce:

* Akce, které jsou přeskočeny kvůli nesplněných podmínek
* Akce, které se nespustí, protože aplikace logiky se zastavila před dokončením

Pro akce, které běží uvnitř smyčky Azure Logic Apps počítá každou akci pro každý cyklus ve smyčce. Předpokládejme například, že máte smyčku "pro každý", která zpracovává seznam. Logic Apps měří akci v této smyčce vynásobením počtu položek seznamu s počtem akcí ve smyčce a přidá akci, která spustí smyčku. Takže výpočet pro seznam 10 položek je (10 * 1) + 1, což má za následek 11 spuštění akce.

## <a name="disabled-logic-apps"></a>Zakázané aplikace logiky

Zakázané aplikace logiky se neúčtují, protože nemohou vytvářet nové instance, když jsou zakázané. Po zakázání aplikace logiky může trvat nějakou dobu, než se všechny instance zastaví.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Účty pro integraci

Pevný [cenový model](https://azure.microsoft.com/pricing/details/logic-apps) se vztahuje na [účty integrace,](logic-apps-enterprise-integration-create-integration-account.md) kde můžete prozkoumat, vyvíjet a testovat funkce zpracování [B2B a EDI](logic-apps-enterprise-integration-b2b.md) a [XML](logic-apps-enterprise-integration-xml.md) v Azure Logic Apps bez dalších nákladů. Každé předplatné Azure může mít až [určitý limit účtů integrace](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Každý účet integrace může ukládat až do [určitého limitu artefaktů](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits), které zahrnují obchodní partnery, dohody, mapy, schémata, sestavení, certifikáty, dávkové konfigurace a tak dále.

Azure Logic Apps nabízí bezplatné, základní a standardní integrační účty. Úrovně Basic a Standard jsou podporovány smlouvou o úrovni služeb Logic Apps (SLA), zatímco úroveň Free není podporována smlouvou SLA a má omezení propustnost a využití. S výjimkou účtů integrace úrovně Free můžete mít v každé oblasti Azure více než jeden účet integrace. Cenové sazby najdete v tématu [Logic Apps pricing](https://azure.microsoft.com/pricing/details/logic-apps/).

Pokud máte [ *prostředí integrační služby* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), [Premium nebo Developer](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), vaše ISE může mít 5 celkových integračních účtů. Informace o tom, jak pevný cenový model funguje pro ise, naleznete v předchozí části [Pevný cenový model](#fixed-pricing) v tomto tématu. Cenové sazby najdete v tématu [Logic Apps pricing](https://azure.microsoft.com/pricing/details/logic-apps).

Chcete-li si vybrat mezi účtem pro integraci zdarma, základní nebo standardní, přečtěte si tyto popisy případu použití:

* **Volný:** Pro když chcete vyzkoušet průzkumné scénáře, ne produkční scénáře

* **Základní**: Pokud chcete pouze zpracování zpráv nebo jako malý obchodní partner, který má obchodní vztah s větším obchodním subjektem

* **Standard**: Pro případy, kdy máte složitější vztahy B2B a zvýšený počet entit, které musíte spravovat

<a name="data-retention"></a>

## <a name="data-retention"></a>Uchovávání dat

S výjimkou aplikací logiky, které běží v prostředí služby integrace (ISE), všechny vstupy a výstupy, které jsou uloženy v historii spuštění aplikace logiky se účtují na základě [doby uchovávání spuštění](logic-apps-limits-and-config.md#run-duration-retention-limits)aplikace logiky . Aplikace logiky, které běží v ISE nevznikají náklady na uchovávání dat. Cenové sazby najdete v tématu [Logic Apps pricing](https://azure.microsoft.com/pricing/details/logic-apps).

Chcete-li vám pomoci sledovat spotřebu úložiště aplikace logiky, můžete:

* Zobrazení počtu jednotek úložiště v GB, které vaše aplikace logiky používá měsíčně.
* Zobrazení velikosti pro vstupy a výstupy konkrétní akce v historii spuštění aplikace logiky.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Zobrazit spotřebu úložiště aplikace logiky

1. Na webu Azure Portal najděte a otevřete aplikaci logiky.

1. V nabídce aplikace logiky vyberte v části **Sledování** **položku Metriky**.

1. V pravém podokně v části **Název grafu**v seznamu **Metrika** vyberte **možnost Fakturační využití pro spouštění spotřeby úložiště**.

   Tato metrika poskytuje počet jednotek spotřeby úložiště v GB za měsíc, které se účtují.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Zobrazit vstupní a výstupní velikosti akcí

1. Na webu Azure Portal najděte a otevřete aplikaci logiky.

1. V nabídce aplikace logiky vyberte **Přehled**.

1. V pravém podokně vyberte v části **Historie spuštění**spuštění, které má vstupy a výstupy, které chcete zkontrolovat.

1. V části **Spuštění aplikace logiky**zvolte **Spustit podrobnosti**.

1. V podokně **podrobností spuštění aplikace Logika** vyberte v tabulce akcí, která uvádí stav a dobu trvání jednotlivých akcí, akci, kterou chcete zobrazit.

1. V podokně **akcí aplikace Logika** najděte velikosti vstupů a výstupů této akce v části **Odkaz vstupy** a **Výstupy**.

## <a name="next-steps"></a>Další kroky

* [Další informace o aplikacích Azure Logic Apps](logic-apps-overview.md)
* [Vytvoření první aplikace logiky](quickstart-create-first-logic-app-workflow.md)
