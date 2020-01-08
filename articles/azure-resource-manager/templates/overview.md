---
title: Přehled šablon
description: V této části najdete popis výhod používání šablon Azure Resource Manager pro nasazení prostředků.
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: a4b0dff4b351098095de0b98ede21d9af8a7eef9
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689704"
---
# <a name="azure-resource-manager-templates-overview"></a>Přehled šablon Azure Resource Manager

Díky přesunu do cloudu mnoho týmů přijalo agilní metody vývoje. Tyto týmy iterují rychle. Potřebují opakovaně nasadit svá řešení do cloudu a znáte jejich infrastrukturu v spolehlivém stavu. Jelikož se infrastruktura stala součástí iteračního procesu, dělení mezi operacemi a vývojem zmizelo. Týmy potřebují spravovat infrastrukturu a kód aplikace pomocí sjednoceného procesu.

Aby bylo možné tyto výzvy splnit, můžete automatizovat nasazení a používat postupy infrastruktury jako kód. V kódu definujete infrastrukturu, která musí být nasazena. Kód infrastruktury se stal součástí vašeho projektu. Stejně jako kód aplikace ukládáte kód infrastruktury do zdrojového úložiště a jeho verzi. Libovolný tým v týmu může spustit kód a nasadit podobná prostředí.

K implementaci infrastruktury jako kódu pro řešení Azure použijte šablony Azure Resource Manager. Šablona je soubor JavaScript Object Notation (JSON), který definuje infrastrukturu a konfiguraci pro váš projekt. Šablona používá deklarativní syntaxi, která umožňuje určit, co máte v úmyslu nasadit bez nutnosti napsat sekvenci programovacích příkazů k jeho vytvoření. V šabloně určíte prostředky, které chcete nasadit, a vlastnosti těchto prostředků.

## <a name="why-choose-resource-manager-templates"></a>Proč zvolit Správce prostředků šablony?

Pokud se snažíte rozhodnout mezi používáním šablon Správce prostředků a jednou z dalších infrastruktur jako kódových služeb, zvažte následující výhody používání šablon:

* **Deklarativní syntaxe**: šablony Správce prostředků umožňují deklarativní vytvoření a nasazení celé infrastruktury Azure. Například můžete nasadit nejen virtuální počítače, ale také síťovou infrastrukturu, úložné systémy a všechny další prostředky, které možná budete potřebovat.

* **Opakované výsledky**: opakovaně nasazovat infrastrukturu během životního cyklu vývoje a mít jistotu, že se prostředky nasadí konzistentně. Šablony jsou idempotentní, což znamená, že můžete nasadit stejnou šablonu mnohokrát a získat stejné typy prostředků ve stejném stavu. Můžete vyvíjet jednu šablonu, která představuje požadovaný stav, namísto vývoje spousty samostatných šablon, které by představovaly aktualizace.

* **Orchestrace**: nemusíte se starat o složitosti operací řazení. Správce prostředků orchestruje nasazení vzájemně závislých prostředků, aby byly vytvořeny ve správném pořadí. Pokud je to možné, Správce prostředků nasadí prostředky paralelně, takže nasazení dokončí rychleji než sériová nasazení. Šablonu nasadíte prostřednictvím jednoho příkazu, nikoli prostřednictvím několika imperativních příkazů.

   ![Porovnání Template deployment](./media/overview/template-processing.png)

* **Integrované ověřování**: šablona se nasadí až po úspěšném ověření. Správce prostředků před zahájením nasazení zkontroluje šablonu, aby se zajistilo, že bude nasazení úspěšné. Vaše nasazení je méně pravděpodobným zastavením v částečně dokončeném stavu.

* **Modulární soubory**: šablony můžete rozdělit do menších, opakovaně použitelných komponent a propojit je společně v době nasazení. Jednu šablonu můžete také vnořit do jiných šablon.

* **Vytvoření libovolného prostředku Azure**: v šablonách můžete hned používat nové služby a funkce Azure. Jakmile poskytovatel prostředků zavádí nové prostředky, můžete tyto prostředky nasadit prostřednictvím šablon. Než začnete používat nové služby, nemusíte čekat na aktualizaci nástrojů nebo modulů.

* **Sledovaná nasazení**: v Azure Portal můžete zkontrolovat historii nasazení a získat informace o nasazení šablony. Můžete zobrazit nasazenou šablonu, hodnoty parametrů předané a všechny výstupní hodnoty. Jiná infrastruktura jako kódové služby se na portálu nesleduje.

   ![Historie nasazení](./media/overview/deployment-history.png)

* **Zásada jako kód**: [Azure Policy](../../governance/policy/overview.md) je zásada jako rozhraní Code Framework pro automatizaci zásad správného řízení. Pokud používáte zásady Azure, náprava zásad se provádí při nasazení prostřednictvím šablon na nekompatibilních prostředcích.

* **Plány nasazení**: pomocí [plánů poskytovaných](../../governance/blueprints/overview.md) Microsoftem můžete vyhovět zákonným předpisům a standardům dodržování předpisů. Tyto plány obsahují předem připravené šablony pro různé architektury.

* **Integrace CI/CD**: šablony můžete integrovat do nástrojů pro průběžnou integraci a průběžné nasazování (CI/CD), které můžou automatizovat vaše kanály vydávání verzí pro rychlé a spolehlivé aktualizace aplikací a infrastruktury. Pomocí úlohy šablon Azure DevOps a Správce prostředků můžete pomocí Azure Pipelines průběžně sestavovat a nasazovat projekty šablon Azure Resource Manager. Další informace najdete v tématu [vs Project with Pipelines](add-template-to-azure-pipelines.md) and [Continuous integration with Azure Pipelines](template-tutorial-use-azure-pipelines.md).

* **Exportovatelné kódy**: šablonu pro stávající skupinu prostředků můžete získat buď tak, že exportujete aktuální stav skupiny prostředků, nebo si prohlížíte šablonu používanou pro konkrétní nasazení. Zobrazení [vyexportované šablony](export-template-portal.md) vám pomůže blíže se seznámit se syntaxí šablony.

* **Nástroje pro tvorbu**: můžete vytvářet šablony pomocí [Visual Studio Code](use-vs-code-to-create-template.md) a rozšíření nástroje šablony. Získáte IntelliSense, zvýrazňování syntaxe, online nápovědu a mnoho dalších funkcí jazyka. Kromě sady Visual Studio Code můžete také použít [Visual Studio](create-visual-studio-deployment-project.md).

## <a name="template-file"></a>Soubor šablony

V rámci šablony můžete napsat [výrazy šablony](template-expressions.md) , které přesahují možnosti formátu JSON. Tyto výrazy využívají [funkce](template-functions.md) poskytované správce prostředků.

Šablona obsahuje následující oddíly:

* [Parametry](template-parameters.md) – poskytněte hodnoty během nasazení, které umožňují používat stejnou šablonu v různých prostředích.

* [Proměnné](template-variables.md) – definujte hodnoty, které se znovu použijí v šablonách. Můžou být vytvořené z hodnot parametrů.

* [Uživatelsky definované funkce](template-user-defined-functions.md) – vytváření přizpůsobených funkcí, které zjednodušují vaši šablonu.

* [Prostředky](template-syntax.md#resources) – zadejte prostředky, které chcete nasadit.

* [Výstupy](template-outputs.md) – vrací hodnoty z nasazených prostředků.

## <a name="template-deployment-process"></a>Proces Template deployment

Když nasadíte šablonu, Správce prostředků převede šablonu na operace REST API. Když například Resource Manager obdrží šablonu s následující definicí prostředku:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

Převede definici na následující operaci rozhraní REST API, která se odešle do poskytovatele prostředků Microsoft.Storage:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage",
  "properties": {}
}
```

## <a name="template-design"></a>Návrh šablony

Záleží pouze na vás, jak definujete šablony a skupiny prostředků a jak chcete spravovat své řešení. Například můžete nasadit svou třívrstvou aplikaci prostřednictvím jediné šablony do jedné skupiny prostředků.

![třívrstvá šablona](./media/overview/3-tier-template.png)

Není však nutné definovat celou infrastrukturu v jediné šabloně. Často má smysl rozdělit požadavky nasazení do několika cílených šablon, které jsou zaměřené na konkrétní účel. Tyto šablony můžete snadno opakovaně použít pro různá řešení. Chcete-li nasadit konkrétní řešení, vytvoříte hlavní šablonu, která propojí všechny požadované šablony. Následující obrázek znázorňuje způsob nasazení třívrstvého řešení prostřednictvím nadřazené šablony, která obsahuje tři vnořené šablony.

![šablona vnořených vrstev](./media/overview/nested-tiers-template.png)

Pokud si představíte, že vaše vrstvy mají tři samostatné životní cykly, můžete tyto tři vrstvy nasadit do samostatných skupin prostředků. Všimněte si, že prostředky mohou být stále propojené s prostředky v jiných skupinách prostředků.

![šablona vrstvy](./media/overview/tier-templates.png)

Informace o vnořených šablonách najdete v tématu [Použití propojených šablon s Azure Resource Managerem](linked-templates.md).

## <a name="next-steps"></a>Další kroky

* Podrobný kurz, který vás provede procesem vytvoření šablony, najdete v tématu [kurz: vytvoření a nasazení první šablony Azure Resource Manager](template-tutorial-create-first-template.md).
* Informace o vlastnostech v souborech šablon naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](template-syntax.md).
* Další informace o exportu šablon najdete v tématu [rychlý Start: vytvoření a nasazení Azure Resource Manager šablon pomocí Azure Portal](quickstart-create-templates-use-the-portal.md).
