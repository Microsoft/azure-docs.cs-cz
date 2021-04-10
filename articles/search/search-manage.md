---
title: Správa služeb na portálu
titleSuffix: Azure Cognitive Search
description: Pomocí Azure Portalu Spravujte službu Azure Kognitivní hledání hostovaná cloudová vyhledávací služba na Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 98e0137c8e48696737cd5d8d1fd4d3de925b9f7f
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579780"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Správa služeb pro Azure Kognitivní hledání v Azure Portal

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Azure Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Kognitivní hledání je plně spravovaná cloudová vyhledávací služba, která se používá k vytváření bohatě vyhledávaného prostředí pro vlastní aplikace. Tento článek popisuje úlohy správy, které můžete provádět v [Azure Portal](https://portal.azure.com) pro vyhledávací službu, kterou jste už vytvořili. Portál umožňuje provádět všechny [úlohy správy](#management-tasks) související se službou a také správu obsahu a průzkum. V takovém případě portál poskytuje široké spektrum přístupu ke všem aspektům operací služby Search Service.

Každá vyhledávací služba je spravovaná jako samostatný prostředek. Na následujícím obrázku vidíte stránky portálu pro jednu bezplatnou vyhledávací službu s názvem "demo-Search-SVC". I když jste zvyklí používat Azure PowerShell nebo Azure CLI pro správu služeb, je vhodné se seznámit s nástroji a možnostmi, které poskytují stránky portálu. Některé úkoly jsou na portálu mnohem jednodušší a rychlejší, než prostřednictvím kódu. 

## <a name="overview-home-page"></a>Stránka Přehled (domů)

Stránka s přehledem je Domovská stránka každé služby. Níže jsou oblasti na obrazovce, které jsou uzavřeny ve červených polích, označeny úkoly, nástroji a dlaždicemi, které můžete často používat, zejména pokud jste pro službu novinkou.

:::image type="content" source="media/search-manage/search-portal-overview-page.png" alt-text="Stránky portálu pro vyhledávací službu" border="true":::

| Plošný | Description |
|------|-------------|
| 1  | V části **základy** se zobrazují vlastnosti služby, včetně koncového bodu, který se používá při nastavování připojení. Zobrazuje také na první pohled úroveň, replika a počty oddílů. |
| 2 | V horní části stránky jsou série příkazů pro vyvolání interaktivních nástrojů nebo správy služby. [Import dat](search-get-started-portal.md) i [Průzkumník služby Search](search-explorer.md) se běžně používají k vytváření prototypů a průzkumu. |
| 3 | Pod oddílem **základy** najdete řadu podstránek s kartami pro rychlý přístup ke statistikám využití, metrikám stavu služeb a přístupu ke všem existujícím indexům, indexerům, zdrojům dat a dovednosti ve vaší službě. Pokud vyberete index nebo jiný objekt, budou k dispozici další stránky, aby se zobrazilo složení objektu, nastavení a stav (Pokud je k dispozici). |
| 4 | Vlevo získáte přístup k odkazům, které otevřou další stránky pro přístup k klíčům rozhraní API použitým v připojeních, konfiguraci služby, konfiguraci zabezpečení, monitorovacích operacích, automatizaci úloh a získání podpory. |

### <a name="read-only-service-properties"></a>Vlastnosti služby jen pro čtení

Při zřizování služby je určeno několik aspektů služby vyhledávání a nelze je změnit:

* Název služby (vyhledávací služba se nedá přejmenovat.)
* Umístění služby (nedotčená vyhledávací služba se nedá snadno přesunout do jiné oblasti. I když existuje šablona, přesunutí obsahu je ruční proces).
* Úroveň služby (například nelze přepnout z S1 na S2, například bez vytváření nové služby)

## <a name="management-tasks"></a>Úlohy správy

Správa služeb je odlehčená návrhem a je často definována úlohami, které můžete provádět relativně vůči samotné službě:

* [Úprava kapacity](search-capacity-planning.md) přidáním nebo odebráním replik a oddílů
* [Správa klíčů rozhraní API](search-security-api-keys.md) používaných pro operace správy a dotazů
* [Řízení přístupu k operacím správce](search-security-rbac.md) prostřednictvím zabezpečení založeného na rolích
* [Konfigurace pravidel brány firewall protokolu IP](service-configure-firewall.md) pro omezení přístupu podle IP adresy
* [Konfigurace privátního koncového bodu](service-create-private-endpoint.md) pomocí privátního propojení Azure a privátní virtuální sítě
* [Monitorování stavu služby](search-monitor-usage.md): úložiště, svazky dotazů a latence

Můžete také vytvořit výčet všech objektů vytvořených ve službě: indexy, indexery, zdroje dat, dovednosti a tak dále. Na stránce Přehled na portálu se zobrazí veškerý obsah, který ve vaší službě existuje. Velká většina operací ve službě vyhledávání se týká obsahu.

Stejné úlohy správy provedené na portálu můžete také zpracovat programově prostřednictvím [rozhraní REST API pro správu](/rest/api/searchmanagement/), [AZ. Search Module](search-manage-powershell.md), [AZ Search Azure CLI Module](search-manage-azure-cli.md)a Azure SDK for .NET, Python, Java a JavaScript. Úlohy správy jsou plně reprezentovány na portálu i v všech programových rozhraních. Neexistuje žádný konkrétní úkol správy, který je k dispozici pouze v jedné z nich.

Kognitivní hledání využívá další služby Azure pro hlubší monitorování a správu. Samotná data, která jsou uložená v rámci vyhledávací služby, jsou obsahem objektu (indexy, indexery a definice zdrojů dat a další objekty). Metriky hlášené na stránkách portálu jsou načítány z interních protokolů na více než 30 dní cyklů. V případě uchovávání protokolů řízených uživatelem a dalších událostí budete potřebovat [Azure monitor](../azure-monitor/index.yml). Další informace o nastavení protokolování diagnostiky pro vyhledávací službu najdete v tématu [shromažďování a analýza dat protokolu](search-monitor-logs.md).

## <a name="administrator-permissions"></a>Oprávnění správce

Když otevřete stránku Přehled služby Search, oprávnění přiřazená vašemu účtu určují, jaké stránky máte k dispozici. Na stránce Přehled na začátku článku vidíte stránky portálu, které uvidí správce nebo Přispěvatel.

V prostředku Azure jsou práva správce udělována prostřednictvím přiřazení rolí. V souvislosti s Kognitivní hledání Azure se [přiřazení rolí](search-security-rbac.md) určí, kdo může přidělovat repliky a oddíly nebo spravovat klíče rozhraní API bez ohledu na to, jestli používají portál, [PowerShell](search-manage-powershell.md), rozhraní příkazového [řádku Azure](search-manage-azure-cli.md)nebo [rozhraní REST API pro správu](/rest/api/searchmanagement/search-howto-management-rest-api):

> [!TIP]
> Zřizování nebo vyřazení samotné služby může udělat Správce předplatného Azure nebo spolusprávce. Pomocí mechanismů pro práci v rámci Azure můžete uzamknout předplatné nebo prostředek, abyste zabránili nechtěnému nebo neautorizovanému odstranění služby vyhledávání uživatelů s právy správce. Další informace najdete v tématu [uzamčení prostředků, aby se zabránilo neočekávanému odstranění](../azure-resource-manager/management/lock-resources.md).

## <a name="next-steps"></a>Další kroky

* Kontrola [možností monitorování](search-monitor-usage.md) dostupných na portálu
* Automatizace pomocí [PowerShellu](search-manage-powershell.md) nebo [Azure CLI](search-manage-azure-cli.md)
* Kontrola [funkcí zabezpečení](search-security-overview.md) pro ochranu obsahu a operací
* Povolení [protokolování diagnostiky](search-monitor-logs.md) pro monitorování úloh dotazů a indexování