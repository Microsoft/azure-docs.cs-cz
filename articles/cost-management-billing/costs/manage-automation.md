---
title: Správa nákladů na Azure s využitím automatizace
description: Tento článek vysvětluje, jak můžete spravovat náklady na Azure s využitím automatizace.
author: bandersmsft
ms.author: banders
ms.date: 04/15/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 0727f98b917944f3721c6c6758fde05c2efd8773
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449827"
---
# <a name="manage-costs-with-automation"></a>Správa nákladů s využitím automatizace

Automatizaci služby Cost Management můžete využít k vytvoření vlastní sady řešení pro načítání a správu informací o nákladech. Tento článek se zabývá běžnými scénáři pro automatizaci služby Cost Management a možnostmi, které jsou dostupné v závislosti na vaší situaci. Pokud chcete vyvíjet s využitím rozhraní API, využijete příklady běžných požadavků na rozhraní API, které vám pomůžou urychlit proces vývoje.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>Automatizace načítání údajů o nákladech pro offline analýzu

Možná potřebujete stáhnout údaje o nákladech na Azure a sloučit je s jinými datovými sadami. Nebo údaje o nákladech potřebujete integrovat do vlastních systémů. V závislosti na objemu dat jsou k dispozici různé možnosti. V každém případě je pro použití rozhraní API a nástrojů potřeba, abyste měli oprávnění služby Cost Management v odpovídajícím rozsahu. Další informace najdete v tématu [Přiřazení přístupu k datům](https://docs.microsoft.com/azure/cost-management-billing/costs/assign-access-acm-data).

## <a name="suggestions-for-handling-large-datasets"></a>Návrhy pro zpracování velkých datových sad

Pokud vaše organizace značně využívá Azure napříč mnoha prostředky nebo předplatnými, budete mít velké množství podrobných údajů o využití. Excel často takto velké soubory nedokáže načíst. V této situaci doporučujeme následující možnosti:

**Power BI**

Power BI slouží k ingestování a zpracování velkých objemů dat. Pokud jste zákazník se smlouvou Enterprise, můžete k analýze nákladů pro fakturační účet použít šablonu Power BI. Tato sestava obsahuje klíčová zobrazení používaná zákazníky. Další informace najdete v tématu [Analýza nákladů na Azure s využitím aplikace šablony Power BI](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).

**Konektor dat Power BI**

Pokud chcete data analyzovat denně, doporučujeme k získání dat pro podrobnou analýzu použít [konektor dat Power BI](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management). Všechny sestavy, které vytvoříte, budou díky konektoru stále aktuální, jak budou nabíhat další náklady.

**Exporty služby Cost Management**

Možná nepotřebovat data analyzovat každý den. Pokud tomu tak je, zvažte použití funkce [Exporty](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) ve službě Cost Management k plánování exportů dat do účtu služby Azure Storage. Potom můžete data načíst do Power BI podle potřeby, nebo je analyzovat v Excelu, pokud je soubor dostatečně malý. Exporty jsou k dispozici na webu Azure Portal nebo je můžete nakonfigurovat pomocí [Rozhraní API pro export](https://docs.microsoft.com/rest/api/cost-management/exports).

**Rozhraní API pro podrobnosti využití**

Použití [rozhraní API pro podrobnosti využití](https://docs.microsoft.com/rest/api/consumption/usageDetails) zvažte, pokud máte malou sadu údajů o nákladech. Pokud máte velké množství nákladových dat, měli byste si pro konkrétní období vyžádat co nejmenší objem údajů o využití. Provedete to tak, že buď zadáte krátký časový rozsah, nebo v požadavku použijete filtr. Například ve scénáři, kdy potřebujete nákladová data za tři roky, bude rozhraní API fungovat lépe, pokud použijete několik volání pro různé časové rozsahy, nikoli jedno volání. Potom můžete data načíst do Excelu pro další analýzu.

## <a name="automate-retrieval-with-usage-details-api"></a>Automatizace načítání pomocí rozhraní API pro podrobnosti využití

[Rozhraní API pro podrobnosti využití](https://docs.microsoft.com/rest/api/consumption/usageDetails) poskytuje snadný způsob získání nezpracovaných a neagregovaných nákladových dat, která odpovídají vyúčtování Azure. Toto rozhraní API je užitečné v případě, že vaše organizace potřebuje řešení pro načítání dat prostřednictvím kódu programu. Použití tohoto rozhraní API zvažte, pokud chcete analyzovat menší sady nákladových dat. Pokud ale máte rozsáhlejší datové sady, měli byste použít jiná řešení uvedená výše. Data v podrobnostech o využití se poskytují na základě měřičů pro jednotlivé dny. Používají se při výpočtu měsíčního vyúčtování. Všeobecně dostupná verze (GA) těchto rozhraní API je `2019-10-01`. Verzi `2019-04-01-preview` použijte pro přístup k verzi Preview pro rezervace a nákupy na Azure Marketplace s využitím rozhraní API.

### <a name="usage-details-api-suggestions"></a>Návrhy rozhraní API pro podrobnosti využití

**Plán požadavků**

Doporučujeme zadávat _maximálně jeden požadavek_ na rozhraní API pro podrobnosti využití za den. Další informace o tom, jak často se aktualizují nákladová data a jak se zachází se zaokrouhlováním, najdete v tématu [Vysvětlení dat služby Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data#rated-usage-data-refresh-schedule).

**Cílení na hlavní rozsahy bez filtrování**

Rozhraní API využijte k získání všech potřebných dat na nejvyšší úrovni, která je k dispozici. Před jakýmkoli filtrováním, seskupováním nebo agregovanou analýzou počkejte, než se všechna potřebná data ingestují. Toto rozhraní API je optimalizované speciálně pro poskytování velkých objemů neagregovaných nezpracovaných nákladových dat. Další informace o rozsazích dostupných ve službě Cost Management najdete v tématu [Vysvětlení a práce s rozsahy](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-work-scopes). Po stažení potřebných dat pro příslušný rozsah použijte aplikaci Excel k analýze těchto dat s využitím filtrů a kontingenčních tabulek.

## <a name="example-usage-details-api-requests"></a>Příklady požadavků na rozhraní API pro podrobnostmi využití

Následující ukázkové požadavky používají zákazníci Microsoftu k řešení běžných scénářů, se kterými se můžete setkat.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>Získání podrobností o využití pro daný rozsah v rámci konkrétního časového intervalu

Data vrácená požadavkem odpovídají datu, kdy bylo využití přijato fakturačním systémem. Mohou zahrnovat náklady z více faktur.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01

```

### <a name="get-amortized-cost-details"></a>Získání podrobných informací o amortizovaných nákladech

Pokud potřebujete skutečné náklady se zobrazením nákupů tak, jak postupně nabíhají, změňte *metriku* na `ActualCost`. K získání amortizovaných a skutečných nákladů je potřeba verze `2019-04-01-preview`. Aktuální verze rozhraní API funguje stejně jako verze `2019-10-01`, až na nový atribut type/metric a změněné názvy vlastností. Pokud máte Smlouvu se zákazníkem Microsoftu, vaše filtry v následujícím příkladu jsou `startDate` a `endDate`.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Opakované načítání rozsáhlých datových sad s využitím exportů

Funkce Exporty je řešením pro plánování pravidelných výpisů nákladových dat. Představuje doporučený způsob načítání neagregovaných nákladových dat pro organizace, jejichž soubory využití jsou příliš velké, než aby se pro volání a stahování dat mohlo spolehlivě využívat rozhraní API pro podrobnosti využití. Data se umístí do účtu služby Azure Storage podle vašeho výběru. Odsud je můžete načíst do vlastních systémů a analyzovat podle potřeb vašich týmů. Pokud chcete nakonfigurovat exporty na webu Azure Portal, přečtěte si téma věnované [exportu dat](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data).

## <a name="data-latency-and-rate-limits"></a>Omezení četnosti a latence dat

Doporučujeme, abyste tato rozhraní API nevolali víckrát než jednou denně. Data služby Cost Management se aktualizují každé čtyři hodiny tak, jak jsou od poskytovatelů prostředků Azure přijímána nová data o využití. Častější volání neposkytuje žádná další data. Místo toho by vedlo ke zvýšení zatížení. Další informace o tom, jak často se mění data a jak se nakládá s latencí dat, najdete v tématu [Vysvětlení dat služby Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data).

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>Kód chyby 429 – počet volání překročil omezení četnosti

Pro zajištění konzistentního prostředí pro všechny předplatitele služby Cost Management je pro rozhraní API této služby nastavené omezení četnosti. Po dosažení tohoto limitu obdržíte stavový kód HTTP `429: Too many requests`. Aktuální limity propustnosti pro naše rozhraní API jsou následující:

- 30 volání za minutu – pro rozsah, uživatele nebo aplikaci.
- 200 volání za minutu – pro tenanta, uživatele nebo aplikaci.

## <a name="next-steps"></a>Další kroky

- [Analýza nákladů na Azure s využitím aplikace šablony Power BI](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)
- [Vytvoření a správa exportovaných dat](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) s využitím funkce Exporty
- Další informace o [rozhraní API pro podrobnosti využití](https://docs.microsoft.com/rest/api/consumption/usageDetails)