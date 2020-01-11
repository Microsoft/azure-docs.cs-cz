---
title: Model fakturace ceny &
description: Přehled o tom, jak model cen a fakturace funguje Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 1c21a84bd9aaa259d0459b4e16c7a62aabaa615d
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896384"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Cenový model pro Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) vám pomůže vytvářet a spouštět pracovní postupy automatizované integrace, které se můžou škálovat v cloudu. Tento článek popisuje, jak fakturace a cenová práce pro Azure Logic Apps. Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Cenový model spotřeby

Pro nové aplikace logiky, které běží na veřejné nebo "globální" Azure Logic Apps službě, platíte jenom za to, co využijete. Tyto aplikace logiky využívají plán a cenový model založený na spotřebě. V aplikaci logiky je každý krok akce a Azure Logic Apps měřiče všech akcí, které běží ve vaší aplikaci logiky.

Například akce zahrnují:

* Triggery, což jsou speciální akce. Všechny Logic Apps vyžadují Trigger jako první krok.
* ["Předdefinované" nebo nativní akce](../connectors/apis-list.md#built-in) , jako je například http, volání Azure Functions a API Management atd.
* Volání [spravovaných konektorů](../connectors/apis-list.md#managed-connectors) , jako je například Outlook 365, Dropbox atd.
* Postup řízení kroků toku, jako jsou smyčky, podmíněné příkazy atd.

[Standardní konektory](../connectors/apis-list.md#managed-connectors) se účtují podle [ceny za konektor Standard](https://azure.microsoft.com/pricing/details/logic-apps). Všeobecně dostupné [podnikové konektory](../connectors/apis-list.md#managed-connectors) se účtují podle [ceny za konektor Enterprise](https://azure.microsoft.com/pricing/details/logic-apps), zatímco konektory Enterprise ve verzi Public Preview se účtují podle [ceny za konektor Standard](https://azure.microsoft.com/pricing/details/logic-apps).

Přečtěte si další informace o tom, jak účtování funguje pro [aktivační události](#triggers) a [Akce](#actions).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Pevný cenový model

[ *Prostředí ISE (Integration Service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) poskytuje soukromý, izolovaný a vyhrazený způsob, jak vytvářet a spouštět aplikace logiky, které mají přístup k prostředkům ve službě Azure Virtual Network. Pro nové aplikace logiky, které běží v rámci ISE, platíte [pevnou měsíční cenu](https://azure.microsoft.com/pricing/details/logic-apps) za tyto funkce:

* [Předdefinované triggery a akce](../connectors/apis-list.md#built-in)

* [Standardní konektory](../connectors/apis-list.md#managed-connectors)

* [Podnikové konektory](../connectors/apis-list.md#enterprise-connectors) s libovolným počtem připojení, kolik potřebujete

* Využití [účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) bez dalších poplatků na základě [SKU ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):

  * **SKU úrovně Premium**: jeden účet pro integraci na úrovni [Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  * **SKU pro vývojáře**: jeden účet pro integraci [bezplatné úrovně](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Každá SKU ISE je omezená na 5 celkových integračních účtů. Další náklady získáte tak, že budete mít na základě SKU ISE k dispozici víc účtů pro integraci:

  * **SKU úrovně Premium**: až čtyři další standardní účty. Žádné účty zdarma nebo Basic.

  * **SKU pro vývojáře**: buď až 4 účty na úrovni Standard, nebo až 5 celkových standardních účtů. Žádné základní účty.

Další informace o omezeních účtu pro integraci najdete v tématu [omezení Logic Apps a konfigurace](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Další informace o [úrovních účtu integrace a jejich cenovém modelu](#integration-accounts) najdete dále v tomto tématu.

V případě SKU ISE úrovně Premium má základní jednotka pevnou kapacitu, takže pokud potřebujete větší propustnost, můžete [Přidat další jednotky škálování](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity)buď během vytváření, nebo později. ISE SKU pro vývojáře nemá schopnost přidávat další jednotky škálování. Logic Apps, které běží ve ISE, neúčtují náklady na uchovávání dat.

> [!NOTE]
> V rámci ISE se předdefinované aktivační události a akce zobrazují jako **základní** popisek a spouštějí se ve stejném ISE jako vaše aplikace logiky. Konektory Standard a Enterprise, které zobrazují popisek **ISE** , se spouštějí ve stejném ISE jako vaše aplikace logiky. Konektory, které nezobrazují ISE, se spouštějí v globálním Logic Apps službě.

Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="connectors"></a>

## <a name="connectors"></a>Konektory

Konektory Azure Logic Apps vám pomůžou aplikace logiky přistupovat k aplikacím, službám a systémům v cloudu nebo místně tím, že poskytují [triggery](#triggers), [Akce](#actions)nebo obojí. Konektory jsou klasifikovány buď jako standard, nebo jako podnikové. Přehled těchto konektorů najdete v tématu [konektory pro Azure Logic Apps](../connectors/apis-list.md). Pokud pro rozhraní REST API, která chcete použít ve svých aplikacích logiky, nejsou k dispozici žádné předem připravené konektory, můžete vytvořit [vlastní konektory](https://docs.microsoft.com/connectors/custom-connectors), které jsou kolem těchto rozhraní REST API jenom obálkami. Vlastní konektory se účtují jako standardní konektory. V následujících částech najdete další informace o tom, jak fakturace pro aktivační události a akce fungují.

<a name="triggers"></a>

## <a name="triggers"></a>Aktivační události

Triggery jsou speciální akce, které vytvoří instanci aplikace logiky, když dojde ke konkrétní události. Triggery fungují různými způsoby, které mají vliv na měření aplikace logiky. Tady jsou různé druhy aktivačních událostí, které existují v Azure Logic Apps:

* **Aktivační událost cyklického dotazování**: Tato aktivační událost nepřetržitě kontroluje koncový bod pro zprávy, které odpovídají kritériím pro vytvoření instance aplikace logiky a spuštění pracovního postupu. I v případě, že se nevytvoří žádná instance aplikace logiky, Logic Apps měřiče dotazovat jednotlivé požadavky jako spuštění. Pokud chcete zadat interval dotazování, nastavte Trigger pomocí návrháře aplikace logiky.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Trigger Webhooku**: Tato aktivační událost čeká, až klient pošle požadavek do konkrétního koncového bodu. Každý požadavek odeslaný na koncový bod Webhooku se počítá jako provedení akce. Například triggerem Webhooku žádosti a HTTP jsou triggery Webhooku.

* **Trigger opakování**: Tato aktivační událost vytvoří instanci aplikace logiky založenou na intervalu opakování, který jste nastavili v aktivační události. Můžete například nastavit Trigger opakování, který se spouští každé tři dny nebo složitějším plánem.

<a name="actions"></a>

## <a name="actions"></a>Akce

"Integrované" akce Azure Logic Apps měřičů, jako jsou například HTTP, jako nativní akce. Například integrované akce zahrnují volání HTTP, volání z Azure Functions nebo API Management a kroky toku řízení, jako jsou podmínky, smyčky a příkazy Switch. Každá akce má svůj vlastní typ akce. Například akce, které volají [konektory](https://docs.microsoft.com/connectors) , mají typ "vstupech apiconnection". Tyto konektory jsou klasifikované jako konektory Standard nebo Enterprise, které se měří na základě příslušných [cen](https://azure.microsoft.com/pricing/details/logic-apps). Podnikové konektory ve *verzi Preview* se účtují jako standardní konektory.

Azure Logic Apps měřiče všech úspěšných a neúspěšných akcí jako spuštění. Logic Apps ale neměří na tyto akce:

* Akce, které se přeskočily kvůli nesplnění podmínkám
* Akce, které nespustíme, protože aplikace logiky se zastavila před dokončením

Pro akce, které se spouští uvnitř smyček, Azure Logic Apps počítá každou akci pro každý cyklus ve smyčce. Předpokládejme například, že máte smyčku "for each", která zpracovává seznam. Logic Apps měřiče v této smyčce akci vynásobením počtu položek seznamu s počtem akcí ve smyčce a přidá akci, která spustí smyčku. Proto je výpočet pro seznam 10 položek (10 * 1) + 1, což vede ke zpracování 11 akcí.

## <a name="disabled-logic-apps"></a>Zakázané aplikace logiky

Zakázané aplikace logiky se neúčtují, protože nemůžou vytvářet nové instance, když jsou zakázané. Po zakázání aplikace logiky může kterákoli z aktuálně spuštěných instancí nějakou dobu trvat, než se úplně zastaví.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Účty pro integraci

[Pevný cenový model](https://azure.microsoft.com/pricing/details/logic-apps) se vztahuje na [účty pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , kde můžete prozkoumat, vyvíjet a testovat funkce [B2B a EDI](logic-apps-enterprise-integration-b2b.md) a [zpracování XML](logic-apps-enterprise-integration-xml.md) v Azure Logic Apps bez dalších nákladů. Každé předplatné Azure může mít až [určitý limit integračních účtů](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Každý účet pro integraci může ukládat až do konkrétního [limitu artefaktů](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits), včetně obchodních partnerů, smluv, map, schémat, sestavení, certifikátů, konfigurací dávek a tak dále.

Azure Logic Apps nabízí účty pro integraci Free, Basic a Standard. Úrovně Basic a Standard jsou podporovány Logic Apps smlouvou o úrovni služeb (SLA), zatímco úroveň Free není podporována smlouvou SLA a má omezení propustnosti a využití. S výjimkou účtů pro integraci bezplatné úrovně můžete mít v každé oblasti Azure více než jeden účet pro integraci. Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps/).

Pokud máte [ *prostředí ISE (Integration Service Environment* ) (](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) [Premium nebo Developer](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)), vaše ISE může mít 5 celkových účtů pro integraci. Další informace o tom, jak model fixního cenového modelu funguje pro ISE, najdete v části předchozí [pevný cenový model](#fixed-pricing) v tomto tématu. Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps).

Pokud si chcete vybrat mezi účtem pro integraci Free, Basic nebo Standard, přečtěte si tyto popisy případů použití:

* **Zdarma**: v případě, že chcete vyzkoušet průzkumné scénáře, nikoli produkční scénáře

* **Basic**: Pokud chcete, aby zpracování zpráv fungovalo jako malý obchodní partner, který má vztah obchodního partnera s větší obchodní entitou

* **Standard**: Pokud máte SLOŽITĚJŠÍ vztahy B2B a vyšší počet entit, které musíte spravovat

<a name="data-retention"></a>

## <a name="data-retention"></a>Uchování dat

S výjimkou aplikací logiky, které běží v prostředí ISE (Integration Service Environment), se všechny vstupy a výstupy, které jsou uložené v historii spuštění vaší aplikace logiky, účtují na základě [doby uchování běhu](logic-apps-limits-and-config.md#run-duration-retention-limits)aplikace logiky. Logic Apps, které běží ve ISE, neúčtují náklady na uchovávání dat. Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps).

Abyste mohli monitorovat spotřebu úložiště vaší aplikace logiky, můžete:

* Podívejte se na počet jednotek úložiště v GB, které vaše aplikace logiky používá měsíčně.
* Zobrazení velikosti pro vstupy a výstupy konkrétní akce v historii spuštění aplikace logiky

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Zobrazit spotřebu úložiště aplikace logiky

1. V Azure Portal Najděte a otevřete aplikaci logiky.

1. V nabídce aplikace logiky v části **monitorování**vyberte **metriky**.

1. V pravém podokně v části **název grafu**vyberte v seznamu **metrika** možnost **využití fakturace pro spouštění spotřeby úložiště**.

   Tato metrika poskytuje počet jednotek spotřeby úložiště v GB za měsíc, které se účtují.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Zobrazit vstupní a výstupní velikosti akce

1. V Azure Portal Najděte a otevřete aplikaci logiky.

1. V nabídce aplikace logiky zvolte **Přehled**.

1. V pravém podokně v části **historie spuštění**vyberte běh, který obsahuje vstupy a výstupy, které chcete kontrolovat.

1. V části **spuštění aplikace logiky**klikněte na tlačítko **Spustit podrobnosti**.

1. V podokně s **podrobnostmi o spuštění aplikace logiky** v tabulce akce, které uvádí stav a dobu trvání jednotlivých akcí, vyberte akci, kterou chcete zobrazit.

1. V podokně **Akce aplikace logiky** vyhledejte v části **vstupy odkaz** a **výstupy**odkazy velikosti pro vstupy a výstupy této akce.

## <a name="next-steps"></a>Další kroky

* [Další informace o Azure Logic Apps](logic-apps-overview.md)
* [Vytvoření první aplikace logiky](quickstart-create-first-logic-app-workflow.md)
