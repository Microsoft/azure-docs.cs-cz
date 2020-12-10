---
title: Model fakturace ceny &
description: Přehled o tom, jak model cen a fakturace funguje Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 520b4a0e87f27a90a604947ae0b558066b4ab82f
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937589"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Cenový model pro Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) vám pomůže vytvářet a spouštět pracovní postupy automatizované integrace, které se můžou škálovat v cloudu. Tento článek popisuje, jak fakturace a cenová práce pro Azure Logic Apps. Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Cenový model spotřeby

Pro nové aplikace logiky, které běží ve veřejné, víceklientské Azure Logic Apps službě, platíte jenom za to, co využijete. Tyto aplikace logiky využívají plán a cenový model založený na spotřebě. V aplikaci logiky je každý krok akce a Azure Logic Apps měřiče všech akcí, které běží ve vaší aplikaci logiky.

Například akce zahrnují:

* [Triggery](#triggers), což jsou speciální akce. Všechny Logic Apps vyžadují Trigger jako první krok.

* ["Předdefinované" nebo nativní akce](../connectors/apis-list.md#built-in) , jako je například http, volání Azure Functions a API Management atd.

* Volání [spravovaných konektorů](../connectors/apis-list.md#managed-connectors) , jako je například Outlook 365, Dropbox atd.

* [Řízení akcí pracovního postupu](../connectors/apis-list.md#control-workflow) , jako jsou smyčky, podmíněné příkazy a tak dále

[Standardní konektory](../connectors/apis-list.md#managed-connectors) se účtují podle [ceny za konektor Standard](https://azure.microsoft.com/pricing/details/logic-apps). Všeobecně dostupné [podnikové konektory](../connectors/apis-list.md#managed-connectors) se účtují podle [ceny za konektor Enterprise](https://azure.microsoft.com/pricing/details/logic-apps), zatímco konektory Enterprise ve verzi Public Preview se účtují podle [ceny za konektor Standard](https://azure.microsoft.com/pricing/details/logic-apps).

Přečtěte si další informace o tom, jak účtování funguje na úrovních [triggerů](#triggers) a [akcí](#actions) . Další informace o omezeních najdete v tématu [omezení a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Pevný cenový model

[ *Prostředí ISE (Integration Service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) nabízí izolovaný způsob, jak vytvářet a spouštět aplikace logiky, které mají přístup k prostředkům ve službě Azure Virtual Network. Logic Apps, které běží ve ISE, neúčtují náklady na uchovávání dat. Když vytvoříte ISE a jenom během vytváření, můžete zvolit [úroveň ISE nebo "SKU"](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), která má různé [cenové sazby](https://azure.microsoft.com/pricing/details/logic-apps):

* Úroveň **Premium** ISE: základní jednotka SKU má pevnou kapacitu, ale pokud potřebujete více propustnosti, můžete [Přidat další jednotky škálování](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) během vytváření ISE nebo později. Omezení ISE najdete v tématu [omezení a konfigurace pro Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise).

* **Vývojář** ISE: Tato SKU neobsahuje žádné možnosti pro vertikální navýšení kapacity, žádnou smlouvu o úrovni služeb (SLA) a žádné publikované limity. Tuto SKU použijte pouze pro experimentování, vývoj a testování, nikoli pro produkční nebo výkonnostní testování.

Pro aplikace logiky, které vytvoříte a spustíte v ISE, platíte za tyto funkce [pevnou cenu](https://azure.microsoft.com/pricing/details/logic-apps) (oproti platbám za použití):

* [Předdefinované](../connectors/apis-list.md#built-in) triggery a akce

  V rámci ISE se předdefinované aktivační události a akce zobrazují jako **základní** popisek a spouštějí se ve stejném ISE jako vaše aplikace logiky.

* [Standardní](../connectors/apis-list.md#managed-connectors) konektory a [podnikové](../connectors/apis-list.md#enterprise-connectors) konektory, které umožňují mít tolik podnikových připojení, kolik potřebujete

   Konektory Standard a Enterprise, které zobrazují popisek **ISE** , se spouštějí ve stejném ISE jako vaše aplikace logiky. Konektory, které neobsahují ISE, běží ve veřejné, víceklientské Logic Apps službě. Pevné ceny platí taky pro konektory, které běží ve víceklientské službě, když je používáte s Logic Apps, které běží v ISE.

* Využití [účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) bez dalších poplatků na základě [SKU ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):

  * Úroveň **Premium** ISE SKU: jeden účet pro integraci úrovně [Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  * **Vývojář** ISE SKU: jeden účet pro integraci [bezplatné úrovně](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Bez ohledu na SKLADOVOU položku můžou mít jednotlivé ISE [omezený počet účtů pro integraci](logic-apps-limits-and-config.md#integration-account-limits). Tento limit můžete zvýšit na další náklady:

  * Úroveň **Premium** ISE SKU: až čtyři další standardní účty. Žádné účty zdarma nebo Basic.

  * **Vývojář** ISE SKU: buď až 4 účty na úrovni Standard, nebo až 5 celkových standardních účtů. Žádné základní účty.

  Další informace o omezeních účtu pro integraci najdete v tématu [omezení a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Další informace o [úrovních účtu integrace a jejich cenovém modelu](#integration-accounts) najdete dále v tomto tématu.

<a name="connectors"></a>

## <a name="connectors"></a>Konektory

Konektory Azure Logic Apps vám pomůžou aplikace logiky přistupovat k aplikacím, službám a systémům v cloudu nebo místně tím, že poskytují [triggery](#triggers), [Akce](#actions)nebo obojí. Konektory jsou klasifikovány buď jako standard, nebo jako podnikové. Přehled těchto konektorů najdete v tématu [konektory pro Azure Logic Apps](../connectors/apis-list.md). Pokud pro rozhraní REST API, která chcete použít ve svých aplikacích logiky, nejsou k dispozici žádné předem připravené konektory, můžete vytvořit [vlastní konektory](/connectors/custom-connectors), které jsou kolem těchto rozhraní REST API jenom obálkami. Vlastní konektory se účtují jako standardní konektory. V následujících částech najdete další informace o tom, jak fakturace pro aktivační události a akce fungují.

<a name="triggers"></a>

## <a name="triggers"></a>Aktivační procedury

Trigger je vždy prvním krokem v pracovním postupu aplikace logiky a jedná se o speciální akci, která vytvoří a spustí instanci aplikace logiky, když jsou splněna určitá kritéria nebo dojde ke konkrétní události. Triggery fungují různými způsoby, které mají vliv na měření aplikace logiky. Tady jsou různé druhy aktivačních událostí, které existují v Azure Logic Apps:

* **Aktivační událost opakování**: můžete použít tuto obecnou triggerovou proceduru, která není specifická pro žádnou službu nebo systém, aby se spustila pracovní postup aplikace logiky a vytvořila se instance aplikace logiky, která se spouští na základě intervalu opakování, který jste nastavili v aktivační události. Můžete například nastavit Trigger opakování, který se spouští každé tři dny nebo složitějším plánem.

* **Aktivační událost cyklického dotazování**: můžete použít konkrétnější aktivační událost opakování, která je obvykle přidružená ke spravovanému konektoru pro konkrétní službu nebo systém, a kontrolovat události nebo zprávy, které splňují kritéria pro vytvoření a spuštění instance aplikace logiky, a to na základě intervalu opakování, který jste nastavili v aktivační události. I když se nevytvoří žádná instance aplikace logiky, například když se triggery přeskočí, služba Logic Apps každou žádost o cyklické dotazování vyřadí jako spuštění. Pokud chcete zadat interval dotazování, nastavte Trigger pomocí návrháře aplikace logiky.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Trigger Webhooku**: místo použití triggeru cyklického dotazování můžete použít Trigger Webhooku, který počká, až klient pošle žádost do vaší aplikace logiky na konkrétní adresu URL koncového bodu. Každý požadavek, který se odešle na koncový bod Webhooku, se počítá jako provedení akce. Například triggerem Webhooku žádosti a HTTP jsou oba triggery obecného Webhooku. Některé konektory pro služby nebo systémy také mají triggery Webhooku.

<a name="actions"></a>

## <a name="actions"></a>Akce

"Integrované" akce Azure Logic Apps měřičů, jako jsou například HTTP, jako nativní akce. Například integrované akce zahrnují volání HTTP, volání z Azure Functions nebo API Management a kroky toku řízení, jako jsou podmínky, smyčky a příkazy Switch. Každá akce má svůj vlastní typ akce. Například akce, které volají [konektory](/connectors) , mají typ "vstupech apiconnection". Tyto konektory jsou klasifikované jako konektory Standard nebo Enterprise, které se měří na základě příslušných [cen](https://azure.microsoft.com/pricing/details/logic-apps). Podnikové konektory ve *verzi Preview* se účtují jako standardní konektory.

Azure Logic Apps měřiče všech úspěšných a neúspěšných akcí jako spuštění. Logic Apps ale neměří na tyto akce:

* Akce, které se přeskočily kvůli nesplnění podmínkám
* Akce, které nespustíme, protože aplikace logiky se zastavila před dokončením

Pro akce, které se spouští uvnitř smyček, Azure Logic Apps počítá každou akci pro každý cyklus ve smyčce. Předpokládejme například, že máte smyčku "for each", která zpracovává seznam. Logic Apps měřiče v této smyčce akci vynásobením počtu položek seznamu s počtem akcí ve smyčce a přidá akci, která spustí smyčku. Proto je výpočet pro seznam 10 položek (10 * 1) + 1, což vede ke zpracování 11 akcí.

## <a name="disabled-logic-apps"></a>Zakázané aplikace logiky

Zakázané aplikace logiky se neúčtují, protože nemůžou vytvářet nové instance, když jsou zakázané. Po zakázání aplikace logiky může kterákoli z aktuálně spuštěných instancí nějakou dobu trvat, než se úplně zastaví.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Účty pro integraci

[Pevný cenový model](https://azure.microsoft.com/pricing/details/logic-apps) se vztahuje na [účty pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , kde můžete prozkoumat, vyvíjet a testovat funkce [B2B a EDI](logic-apps-enterprise-integration-b2b.md) a [zpracování XML](logic-apps-enterprise-integration-xml.md) v Azure Logic Apps bez dalších nákladů. Každé předplatné Azure může mít až [určitý limit integračních účtů](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Každý účet pro integraci může ukládat až na určitý [limit artefaktů](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits), mezi které patří obchodní partneři, smlouvy, mapy, schémata, sestavení, certifikáty, konfigurace dávek a tak dále.

Azure Logic Apps nabízí účty pro integraci Free, Basic a Standard. Úrovně Basic a Standard jsou podporovány Logic Apps smlouvou o úrovni služeb (SLA), zatímco úroveň Free není podporována smlouvou SLA a má omezení dostupnosti, propustnosti a využití oblasti. S výjimkou účtů pro integraci bezplatné úrovně můžete mít v každé oblasti Azure více než jeden účet pro integraci. Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps/).

Pokud máte [ *prostředí ISE (Integration Service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)bez ohledu na [skladovou](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)položku, vaše ISE může mít [omezený počet účtů pro integraci](logic-apps-limits-and-config.md#integration-account-limits), ale [Toto omezení můžete zvýšit na další náklady](#fixed-pricing). Další informace o tom, jak model fixního cenového modelu funguje pro ISE, najdete v části předchozí [pevný cenový model](#fixed-pricing) v tomto tématu. Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps).

Pokud si chcete vybrat mezi účtem pro integraci Free, Basic nebo Standard, přečtěte si tyto popisy případů použití:

* **Zdarma**: v případě, že chcete vyzkoušet průzkumné scénáře, nikoli produkční scénáře. Tato úroveň je dostupná jenom pro veřejné oblasti v Azure, například Západní USA nebo jihovýchodní Asie, ale ne pro [Azure Čína 21Vianet](/azure/china/overview-operations) nebo [Azure Government](../azure-government/documentation-government-welcome.md).

* **Basic**: Pokud chcete, aby zpracování zpráv fungovalo jako malý obchodní partner, který má vztah obchodního partnera s větší obchodní entitou

* **Standard**: Pokud máte SLOŽITĚJŠÍ vztahy B2B a vyšší počet entit, které musíte spravovat

<a name="data-retention"></a>

## <a name="data-retention"></a>Uchovávání dat

S výjimkou aplikací logiky, které běží v prostředí ISE (Integration Service Environment), se všechny vstupy a výstupy, které jsou uložené v historii spuštění vaší aplikace logiky, účtují na základě [doby uchování běhu](logic-apps-limits-and-config.md#run-duration-retention-limits)aplikace logiky. Logic Apps, které běží ve ISE, neúčtují náklady na uchovávání dat. Cenové sazby najdete v tématu [Logic Apps ceny](https://azure.microsoft.com/pricing/details/logic-apps).

Abyste mohli monitorovat spotřebu úložiště vaší aplikace logiky, můžete:

* Podívejte se na počet jednotek úložiště v GB, které vaše aplikace logiky používá měsíčně.

* Zobrazení velikosti pro vstupy a výstupy konkrétní akce v historii spuštění aplikace logiky

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Zobrazit spotřebu úložiště aplikace logiky

1. V Azure Portal Najděte a otevřete aplikaci logiky.

1. V nabídce aplikace logiky v části **monitorování** vyberte **metriky**.

1. V pravém podokně v části **název grafu** vyberte v seznamu **metrika** možnost **využití fakturace pro spouštění spotřeby úložiště**.

   Tato metrika poskytuje počet jednotek spotřeby úložiště v GB za měsíc, které se účtují.

   > [!NOTE]
   > Spuštění, které spotřebovávají méně než 500 MB v úložišti, se nemusí zobrazit v zobrazení monitorování, ale pořád se účtují.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Zobrazit vstupní a výstupní velikosti akce

1. V Azure Portal Najděte a otevřete aplikaci logiky.

1. V nabídce aplikace logiky zvolte **Přehled**.

1. V pravém podokně v části **historie spuštění** vyberte běh, který obsahuje vstupy a výstupy, které chcete kontrolovat.

1. V části **spuštění aplikace logiky** klikněte na tlačítko **Spustit podrobnosti**.

1. V podokně s **podrobnostmi o spuštění aplikace logiky** v tabulce akce, které uvádí stav a dobu trvání jednotlivých akcí, vyberte akci, kterou chcete zobrazit.

1. V podokně **Akce aplikace logiky** Najděte velikosti pro vstupy a výstupy této akce. V části **vstupy odkaz** a **výstupy** vyhledejte odkazy na tyto vstupy a výstupy.

   > [!NOTE]
   > U smyček se pouze akce nejvyšší úrovně zobrazují velikosti pro jejich vstupy a výstupy. Pro akce uvnitř vnořených smyček se vstupy a výstupy zobrazují nulové velikosti a žádné odkazy.

## <a name="next-steps"></a>Další kroky

* [Další informace o Azure Logic Apps](logic-apps-overview.md)
* [Vytvoření první aplikace logiky](quickstart-create-first-logic-app-workflow.md)
