---
title: Přehled šablon
description: Popisuje výhody použití šablon Azure Resource Manager pro nasazení prostředků.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 4570f5471ef6baf6f3f4a920be4d93c3f5a90438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258120"
---
# <a name="what-are-arm-templates"></a>Co jsou šablony ARM?

S přechodem do cloudu, mnoho týmů přijaly agilní metody vývoje. Tyto týmy rychle zitýrá. Potřebují opakovaně nasazovat svá řešení do cloudu a vědět, že jejich infrastruktura je ve spolehlivém stavu. Vzhledem k tomu, že infrastruktura se stala součástí iterativního procesu, rozdělení mezi operacemi a vývojem zmizelo. Týmy potřebují spravovat infrastrukturu a kód aplikace prostřednictvím jednotného procesu.

Chcete-li splnit tyto výzvy, můžete automatizovat nasazení a používat praxi infrastruktury jako kód. V kódu definujete infrastrukturu, která musí být nasazena. Kód infrastruktury se stane součástí projektu. Stejně jako kód aplikace uložíte kód infrastruktury do zdrojového úložiště a uděláte jeho verzi. Každý, kdo ve vašem týmu můžete spustit kód a nasadit podobná prostředí.

K implementaci infrastruktury jako kódu pro vaše řešení Azure použijte šablony Azure Resource Manager (ARM). Šablona je soubor Zápisu objektu JavaScript (JSON), který definuje infrastrukturu a konfiguraci projektu. Šablona používá deklarativní syntaxi, která umožňuje uvést, co chcete nasadit, aniž byste museli psát posloupnost programovacích příkazů k jeho vytvoření. V šabloně zadáte prostředky k nasazení a vlastnosti pro tyto prostředky.

## <a name="why-choose-arm-templates"></a>Proč zvolit šablony ARM?

Pokud se pokoušíte rozhodnout mezi použitím šablon ARM a jedné z dalších infrastruktur jako kódových služeb, zvažte následující výhody použití šablon:

* **Deklarativní syntaxe**: ŠABLONY ARM umožňují deklarativně vytvářet a nasazovat celou infrastrukturu Azure. Můžete například nasadit nejen virtuální počítače, ale také síťovou infrastrukturu, systémy úložiště a další prostředky, které budete potřebovat.

* **Opakovatelné výsledky**: Opakovaně nasazujte infrastrukturu v průběhu celého životního cyklu vývoje a mějte jistotu, že vaše prostředky jsou naváděny konzistentním způsobem. Šablony jsou idempotentní, což znamená, že můžete nasadit stejnou šablonu mnohokrát a získat stejné typy prostředků ve stejném stavu. Můžete vytvořit jednu šablonu, která představuje požadovaný stav, spíše než vyvíjet velké množství samostatných šablon představujících aktualizace.

* **Orchestrace**: Nemusíte se starat o složitosti objednávání operací. Správce prostředků orchestruje nasazení vzájemně závislých prostředků tak, aby byly vytvořeny ve správném pořadí. Pokud je to možné, Správce prostředků nasazuje prostředky paralelně, takže vaše nasazení dokončit rychleji než sériová nasazení. Šablonu nasazujete pomocí jednoho příkazu, nikoli prostřednictvím více imperativních příkazů.

   ![Porovnání nasazení šablony](./media/overview/template-processing.png)

* **Integrované ověření**: Šablona se nasadí až po předání ověření. Správce prostředků zkontroluje šablonu před zahájením nasazení a ujistěte se, že nasazení bude úspěšné. Vaše nasazení je méně pravděpodobné, že se zastaví v polodokončeném stavu.

* **Modulární soubory**: Šablony můžete rozdělit na menší opakovaně použitelné součásti a propojit je v době nasazení. Můžete také vnořit jednu šablonu do jiné šablony.

* **Vytvoření libovolného prostředku Azure**: Můžete okamžitě použít nové služby Azure a funkce v šablonách. Jakmile poskytovatel prostředků zavede nové prostředky, můžete tyto prostředky nasadit prostřednictvím šablon. Před použitím nových služeb nemusíte čekat na aktualizaci nástrojů nebo modulů.

* **Sledování nasazení**: Na webu Azure Portal můžete zkontrolovat historii nasazení a získat informace o nasazení šablony. Můžete zobrazit šablonu, která byla nasazena, hodnoty parametrů předané a všechny výstupní hodnoty. Ostatní infrastruktury jako kódové služby nejsou sledovány prostřednictvím portálu.

   ![Historie nasazení](./media/overview/deployment-history.png)

* **Zásady jako kód**: [Zásady Azure](../../governance/policy/overview.md) je zásada jako rozhraní kódu pro automatizaci zásad správného řízení. Pokud používáte zásady Azure, náprava zásad se provádí u nekompatibilních prostředků při nasazení prostřednictvím šablon.

* **Podrobné plány nasazení**: Podrobné [plány](../../governance/blueprints/overview.md) poskytované společností Microsoft můžete využít ke splnění regulačních a standardů dodržování předpisů. Tyto podrobné plány zahrnují předem vytvořené šablony pro různé architektury.

* **Integrace CI/CD**: Šablony můžete integrovat do nástrojů průběžné integrace a průběžného nasazování (CI/CD), které mohou automatizovat kanály verzí pro rychlé a spolehlivé aktualizace aplikací a infrastruktury. Pomocí úlohy šablony Azure DevOps a Resource Manager můžete pomocí Azure Pipelines nepřetržitě vytvářet a nasazovat projekty šablon ARM. Další informace najdete [v tématu Projekt VS s kanály](add-template-to-azure-pipelines.md) a [průběžnou integraci s Azure Pipelines](template-tutorial-use-azure-pipelines.md).

* **Exportovatelný kód**: Šablonu pro existující skupinu prostředků můžete získat buď exportem aktuálního stavu skupiny prostředků, nebo zobrazením šablony použité pro konkrétní nasazení. Zobrazení [vyexportované šablony](export-template-portal.md) vám pomůže blíže se seznámit se syntaxí šablony.

* **Nástroje pro vytváření**: Šablony můžete vytvářet pomocí [kódu sady Visual Studio](use-vs-code-to-create-template.md) a rozšíření nástroje šablony. Získáte intellisense, zvýraznění syntaxe, in-line nápovědu a mnoho dalších jazykových funkcí. Kromě kódu sady Visual Studio můžete také použít [visual studio](create-visual-studio-deployment-project.md).

## <a name="template-file"></a>Soubor šablony

V šabloně můžete psát [výrazy šablony,](template-expressions.md) které rozšiřují možnosti JSON. Tyto výrazy využívají [funkce](template-functions.md) poskytované Správcem prostředků.

Šablona má následující části:

* [Parametry](template-parameters.md) – poskytují hodnoty během nasazení, které umožňují stejnou šablonu, která má být použita v různých prostředích.

* [Proměnné](template-variables.md) – Definujte hodnoty, které jsou znovu použity v šablonách. Mohou být vytvořeny z hodnot parametrů.

* [Uživatelem definované funkce](template-user-defined-functions.md) – vytvořte vlastní funkce, které zjednoduší vaši šablonu.

* [Prostředky](template-syntax.md#resources) – zadejte prostředky, které chcete nasadit.

* [Výstupy](template-outputs.md) – vrátí hodnoty z nasazených prostředků.

## <a name="template-deployment-process"></a>Proces nasazení šablony

Při nasazení šablony správce prostředků převede šablonu na operace rozhraní REST API. Když například Resource Manager obdrží šablonu s následující definicí prostředku:

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

* Podrobný kurz, který vás provede procesem vytváření šablony, najdete v [tématu Kurz: Vytvoření a nasazení první šablony ARM](template-tutorial-create-first-template.md).
* Informace o vlastnostech v souborech šablon naleznete v [tématu Principy struktury a syntaxe šablon ARM](template-syntax.md).
* Další informace o exportu šablon najdete v [tématu Úvodní příručka: Vytvoření a nasazení šablon ARM pomocí portálu Azure](quickstart-create-templates-use-the-portal.md).
