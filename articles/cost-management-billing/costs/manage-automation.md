---
title: Správa nákladů na Azure s využitím automatizace
description: Tento článek vysvětluje, jak můžete spravovat náklady na Azure s využitím automatizace.
author: bandersmsft
ms.author: banders
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.openlocfilehash: 2bf28384ae672440a18331cad8ac95f6ea051b85
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372183"
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

Pomocí exportů z Cost Managementu můžete pravidelně exportovat velké objemy dat. Export je doporučený způsob načítání neagregovaných informací o nákladech. Platí to zejména v případě, že soubory s daty o využití jsou příliš rozsáhlé a nejde je spolehlivě volat a stahovat pomocí rozhraní API pro podrobnosti využití. Exportovaná data se uloží do účtu Azure Storage, který zvolíte. Odsud je můžete načítat do vlastních systémů a analyzovat podle vašich potřeb. Pokud chcete nakonfigurovat exporty na webu Azure Portal, přečtěte si téma věnované [exportu dat](tutorial-export-acm-data.md).

Pokud chcete automatizovat exporty pro různé rozsahy, vhodným výchozím bodem je ukázkový požadavek rozhraní API uvedený v další části. Můžete využít rozhraní API pro export a vytvořit automatické exporty jako součást obecné konfigurace prostředí. Automatické exporty vám pomohou zajistit, že máte potřebná data. Při rozšiřování využití Azure je můžete využít ve vlastních systémech vaší organizace.

### <a name="common-export-configurations"></a>Běžné konfigurace exportu

Před vytvořením prvního exportu se zamyslete nad scénářem a možnostmi konfigurace, které jsou pro něj potřeba. Zvažte následující možnosti exportu:

- **Opakování:** Určuje, jak často běží úlohy exportu a kdy se do účtu Azure Storage umístí výsledný soubor. Možnosti jsou každý den, každý týden nebo každý měsíc. Zkuste nakonfigurovat opakování tak, aby odpovídalo úlohám importu dat používaným interním systémem vaší organizace.
- **Období opakování:** Určuje, po jak dlouhou dobu zůstane export platný. Soubory se exportují jenom během období opakování.
- **Časový rámec:** Určuje množství dat vygenerovaných exportem při konkrétním spuštění. Běžné možnosti jsou od začátku měsíce a od začátku týdne.
- **Datum zahájení:** Určuje, odkdy má začít platit rozvrh pro export. Export se vytvoří v den zahájení a následně na základě určeného opakování.
- **Typ:** Existují tři typy exportu:
  - ActualCost: Celkové využití a náklady za zadané období tak, jak se vyúčtují a zobrazí na faktuře.
  - AmortizedCost: Celkové využití a náklady za zadané období. U nákladů na nákup rezervací se přitom použije odpovídající amortizace.
  - Usage: Typu Usage jsou všechny exporty vytvořené do 20. července 2020. Všechny naplánované exporty aktualizujte jako ActualCost nebo AmortizedCost.
- **Sloupce:** Definuje datová pole, která chcete zahrnout do souboru exportu. Odpovídají polím, která jsou k dispozici v rozhraní API pro podrobnosti využití. Další informace najdete v tématu věnovaném [rozhraní API pro podrobnosti využití](/rest/api/consumption/usagedetails/list).

### <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Vytvoření denního exportu od začátku měsíce pro předplatné

Adresa URL požadavku: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

### <a name="automate-alerts-and-actions-with-budgets"></a>Automatizace upozornění a akcí s využitím rozpočtů

Maximalizace využití investic do cloudu má dvě důležité součásti. Jednou z nich je automatické vytváření rozpočtu. Druhou je konfigurace orchestrace na základě nákladů v rámci reakce na upozornění rozpočtu. Existují různé způsoby automatizace vytváření rozpočtů Azure. Při překročení nakonfigurovaných prahových hodnot upozornění dochází k různým reakcím.

Následující části obsahují přehled dostupných možností a poskytují ukázkové požadavky rozhraní API, které vám pomohou s automatizací rozpočtů začít.

#### <a name="how-costs-are-evaluated-against-your-budget-threshold"></a>Jak se vyhodnocují náklady vzhledem k rozpočtovým prahům

Vaše náklady se vzhledem k rozpočtovým prahům vyhodnocují jednou za den. V den vytvoření nového rozpočtu nebo den resetování rozpočtu budou náklady v porovnání s prahovou hodnotou rovné nule, protože k vyhodnocení nemuselo dojít.

Když Azure zjistí, že vaše náklady překročily prahovou hodnotu, během jedné hodiny se aktivuje oznámení.

#### <a name="view-your-current-cost"></a>Zobrazení aktuálních nákladů

K zobrazení aktuálních nákladů je potřeba použít volání GET s využitím [rozhraní API pro dotazy](/rest/api/cost-management/query).

Volání GET pro rozhraní API pro rozpočty nevrátí aktuální náklady zobrazené v analýze nákladů. Místo toho toto volání vrátí vaše poslední vyhodnocené náklady.

### <a name="automate-budget-creation"></a>Automatizace vytváření rozpočtů

Vytváření rozpočtu můžete automatizovat pomocí [rozhraní API pro rozpočty](/rest/api/consumption/budgets). Rozpočet si také můžete vytvořit pomocí [šablony rozpočtu](quick-create-budget-template.md). Šablony představují snadný způsob, jak standardizovat nasazení Azure a současně zajistit správnou konfiguraci a prosazování řízení nákladů.

#### <a name="supported-locales-for-budget-alert-emails"></a>Podporovaná národní prostředí pro e-maily s upozorněními na rozpočet

Pokud používáte rozpočty a náklady překročí nastavenou prahovou hodnotu, dostanete upozornění. Pro každý rozpočet můžete nastavit až pět příjemců e-mailu. Příjemci dostanou e-mailová upozornění do 24 hodin od překročení prahové hodnoty rozpočtu. Příjemce ale může potřebovat, aby e-maily, které dostává, byly v jiném jazyce. S rozhraním API pro rozpočty můžete využívat následující kódy jazykové verze. Kód jazykové verze můžete nastavit pomocí parametru `locale` (podobně jako v následujícím příkladu).

```json
{
  "eTag": "\"1d681a8fc67f77a\"",
  "properties": {
    "timePeriod": {
      "startDate": "2020-07-24T00:00:00Z",
      "endDate": "2022-07-23T00:00:00Z"
    },
    "timeGrain": "BillingMonth",
    "amount": 1,
    "currentSpend": {
      "amount": 0,
      "unit": "USD"
    },
    "category": "Cost",
    "notifications": {
      "actual_GreaterThan_10_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 20,
        "locale": "en-us",
        "contactEmails": [
          "user@contoso.com"
        ],
        "contactRoles": [],
        "contactGroups": [],
        "thresholdType": "Actual"
      }
    }
  }
}

```

Jazyky podporované kódem jazykové verze:

| Kód jazykové verze| Jazyk |
| --- | --- |
| cs-cz | Angličtina (Spojené státy) |
| ja-jp | japonština (Japonsko) |
| zh-cn | Čínština (zjednodušená, Čína) |
| de-de | němčina (Německo) |
| es-es | španělština (Španělsko, mezinárodní) |
| fr-fr | francouzština (Francie) |
| it-it | italština (Itálie) |
| ko-kr | korejština (Jižní Korea) |
| pt-br | Portugalština (Brazílie) |
| ru-ru | ruština (Rusko) |
| zh-tw | Čínština (tradiční, Tchaj-wan) |
| cs-cz | čeština (Česká republika) |
| pl-pl | polština (Polsko) |
| tr-tr | turečtina (Turecko) |
| da-dk | dánština (Dánsko) |
| dn-gb | Angličtina (Spojené království) |
| hu-hu | Maďarština (Maďarsko) |
| nb-bo | norština bokmal (Norsko) |
| nl-nl | nizozemština (Nizozemsko) |
| pt-pt | portugalština (Portugalsko) |
| sv-se | švédština (Švédsko) |

#### <a name="common-budgets-api-configurations"></a>Běžné konfigurace rozhraní API pro rozpočty

Existuje mnoho způsobů, jak nakonfigurovat rozpočet v prostředí Azure. Nejdřív se zamyslete nad scénářem a možnostmi konfigurace, které jsou pro něj potřeba. Zkontrolujte následující možnosti:

- **Časový úsek:** Reprezentuje opakované období, které váš rozpočet využívá ke kumulaci a vyhodnocení nákladů. Nejběžnější možnosti jsou jednou za měsíc, jednou za čtvrtletí a jednou za rok.
- **Časové obdob:í** Představuje dobu, po kterou je rozpočet platný. Rozpočet aktivně monitoruje a upozorňuje jenom po dobu, po kterou zůstává platný.
- **Oznámení**
  - Kontaktní e-mailové adresy: Tyto e-mailové adresy dostávají upozornění, když rozpočet nakumuluje náklady a překročí se definované prahové hodnoty.
  - Kontaktní role: Při použití této možnosti dostanou e-mailová upozornění všichni uživatelé s odpovídající rolí Azure pro příslušný obor. Například vlastníci předplatného by dostali upozornění na rozpočet vytvořený v oboru předplatného.
  - Kontaktní skupiny: Při překročení prahové hodnoty pro upozornění rozpočet volá nakonfigurované skupiny akcí.
- **Filtry dimenze nákladů:** Stejné filtrování jako při analýze nákladů nebo v rozhraní API pro dotazy můžete také využít ve vašem rozpočtu. Pomocí tohoto filtru můžete omezit rozsah nákladů, které v rámci rozpočtu monitorujete.

Po identifikaci možností vytvoření rozpočtu, které vyhovují vašim potřebám, vytvořte rozpočet pomocí rozhraní API. Následující příklad vám pomůže začít s běžnou konfigurací rozpočtu.

**Vytvoření rozpočtu vyfiltrovaného na několik prostředků a značek**

Adresa URL požadavku: `PUT https://management.azure.com/subscriptions/{SubscriptionId} /providers/Microsoft.Consumption/budgets/{BudgetName}/?api-version=2019-10-01`

```json
{
  "eTag": "\"1d34d016a593709\"",
  "properties": {
    "category": "Cost",
    "amount": 100.65,
    "timeGrain": "Monthly",
    "timePeriod": {
      "startDate": "2017-10-01T00:00:00Z",
      "endDate": "2018-10-31T00:00:00Z"
    },
    "filter": {
      "and": [
        {
          "dimensions": {
            "name": "ResourceId",
            "operator": "In",
            "values": [
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}",
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}"
            ]
          }
        },
        {
          "tags": {
            "name": "category",
            "operator": "In",
            "values": [
              "Dev",
              "Prod"
            ]
          }
        },
        {
          "tags": {
            "name": "department",
            "operator": "In",
            "values": [
              "engineering",
              "sales"
            ]
          }
        }
      ]
    },
    "notifications": {
      "Actual_GreaterThan_80_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 80,
        "contactEmails": [
          "user1@contoso.com",
          "user2@contoso.com"
        ],
        "contactRoles": [
          "Contributor",
          "Reader"
        ],
        "contactGroups": [
          "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.insights/actionGroups/{actionGroupName}
        ],
        "thresholdType": "Actual"
      }
    }
  }
}
```

### <a name="configure-cost-based-orchestration-for-budget-alerts"></a>Konfigurace orchestrace na základě nákladů pro upozornění rozpočtu

Rozpočty můžete nakonfigurovat tak, aby spouštěly automatizované akce pomocí skupin akcí Azure. Další informace o automatizaci akcí s využitím rozpočtů najdete v tématu věnovaném [automatizaci s využitím rozpočtů Azure](../manage/cost-management-budget-scenario.md).

## <a name="data-latency-and-rate-limits"></a>Omezení četnosti a latence dat

Doporučujeme, abyste tato rozhraní API nevolali víckrát než jednou denně. Data služby Cost Management se aktualizují každé čtyři hodiny tak, jak jsou od poskytovatelů prostředků Azure přijímána nová data o využití. Častější volání neposkytuje žádná další data. Místo toho by vedlo ke zvýšení zatížení. Další informace o tom, jak často se mění data a jak se nakládá s latencí dat, najdete v tématu [Vysvětlení dat služby Cost Management](understand-cost-mgt-data.md).

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>Kód chyby 429 – počet volání překročil omezení četnosti

Pro zajištění konzistentního prostředí pro všechny předplatitele služby Cost Management je pro rozhraní API této služby nastavené omezení četnosti. Po dosažení tohoto limitu obdržíte stavový kód HTTP `429: Too many requests`. Aktuální limity propustnosti pro naše rozhraní API jsou následující:

- 30 volání za minutu – pro rozsah, uživatele nebo aplikaci.
- 200 volání za minutu – pro tenanta, uživatele nebo aplikaci.

## <a name="next-steps"></a>Další kroky

- [Analýza nákladů na Azure s využitím aplikace šablony Power BI](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)
- [Vytvoření a správa exportovaných dat](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) s využitím funkce Exporty
- Další informace o [rozhraní API pro podrobnosti využití](https://docs.microsoft.com/rest/api/consumption/usageDetails)