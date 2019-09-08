---
title: Šablony Azure Resource Manageru
description: Popisuje způsob použití šablon Azure Resource Manager pro nasazení prostředků.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: tomfitz
ms.openlocfilehash: 61e9bbabee969280c07521edb05d67ba68c0c58e
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802018"
---
# <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Díky přesunu do cloudu mnoho týmů přijalo agilní metody vývoje. Tyto týmy iterují rychle. Potřebují opakovaně nasadit svá řešení do cloudu a znáte jejich infrastrukturu v spolehlivém stavu. Jelikož se infrastruktura stala součástí iteračního procesu, dělení mezi operacemi a vývojem zmizelo. Týmy potřebují spravovat infrastrukturu a kód aplikace pomocí sjednoceného procesu.

Aby bylo možné tyto výzvy splnit, můžete automatizovat nasazení a používat postupy infrastruktury jako kód. V kódu definujete infrastrukturu, která musí být nasazena. Kód infrastruktury se stal součástí vašeho projektu. Stejně jako kód aplikace ukládáte kód infrastruktury do zdrojového úložiště a jeho verzi. Libovolný tým v týmu může spustit kód a nasadit podobná prostředí.

K implementaci infrastruktury jako kódu pro řešení Azure použijte šablony Azure Resource Manager. Šablona je soubor JavaScript Object Notation (JSON), který definuje infrastrukturu a konfiguraci pro váš projekt. Šablona používá deklarativní syntaxi, která umožňuje určit, co máte v úmyslu nasadit bez nutnosti napsat sekvenci programovacích příkazů k jeho vytvoření. V šabloně určíte prostředky, které chcete nasadit, a vlastnosti těchto prostředků.

## <a name="benefits-of-resource-manager-templates"></a>Výhody šablon Správce prostředků

Šablony Správce prostředků poskytují následující výhody:

* Nasaďte, spravujte a monitorujte všechny prostředky pro vaše řešení jako skupinu, místo toho, aby se tyto prostředky nemusely zpracovávat jednotlivě.

* Opakovaně nasaďte řešení v průběhu životního cyklu vývoje a měli byste mít jistotu, že se prostředky nasazují v konzistentním stavu.

* Spravujte svoji infrastrukturu prostřednictvím deklarativních šablon místo skriptů.

Pokud se snažíte rozhodnout mezi používáním šablon Správce prostředků nebo některou jinou infrastrukturou jako Code Services, zvažte následující výhody, které tyto služby obsahují:

* Nové služby a funkce Azure jsou hned dostupné v šablonách. Jakmile poskytovatel prostředků zavádí nové prostředky, můžete tyto prostředky nasadit prostřednictvím šablon. U jiné infrastruktury jako kódové služby musíte počkat, až třetí strany implementují rozhraní pro nové prostředky.

* Nasazení šablon jsou zpracovávány prostřednictvím jednoho odeslání šablony, nikoli prostřednictvím několika imperativních příkazů. Správce prostředků orchestruje nasazení vzájemně závislých prostředků, aby byly vytvořeny ve správném pořadí. Analyzuje šablonu a určuje správné pořadí nasazení na základě odkazů mezi prostředky.

   ![Porovnání Template deployment](./media/template-deployment-overview/template-processing.png)

* Nasazení šablon jsou sledována v Azure Portal. Můžete zkontrolovat historii nasazení a získat informace o nasazení šablony. Můžete zobrazit nasazenou šablonu, hodnoty parametrů předané a všechny výstupní hodnoty. Jiná infrastruktura jako kódové služby se na portálu nesleduje.

   ![Historie nasazení](./media/template-deployment-overview/deployment-history.png)

* Nasazení šablon prošla ověřením před lety. Správce prostředků před zahájením nasazení zkontroluje šablonu, aby se zajistilo, že bude nasazení úspěšné. Vaše nasazení je méně pravděpodobným zastavením v částečně dokončeném stavu.

* Pokud používáte [zásady Azure](../governance/policy/overview.md), náprava zásad se provádí při nasazení prostřednictvím šablon na nekompatibilních prostředcích.

* Microsoft poskytuje [plány](../governance/blueprints/overview.md) nasazení pro splnění zákonných předpisů a standardů dodržování předpisů. Tyto plány obsahují předem připravené šablony pro různé architektury.

## <a name="idempotent"></a>Idempotentní

Idempotentní jednoduše znamená, že můžete spouštět stejné operace mnohokrát a získat stejný výsledek. Nasazení šablony Správce prostředků je idempotentní. Stejnou šablonu můžete nasadit mnohokrát a získat stejné typy prostředků ve stejném stavu. Tento koncept je důležitý, protože to znamená, že získáte konzistentní výsledky bez ohledu na to, jestli šablonu znovu nasadíte do existující skupiny prostředků, nebo nasadit šablonu a novou skupinu prostředků.

Řekněme, že jste do skupiny prostředků nasadili tři prostředky, a pak se rozhodnete, že potřebujete přidat čtvrtý prostředek. Místo vytvoření nové šablony, která obsahuje pouze nový prostředek, můžete přidat čtvrtý prostředek do existující šablony. Když nasadíte novou šablonu do skupiny prostředků, která už má tři prostředky, Správce prostředků si vyhodnotí, které akce se mají provést.

Pokud prostředek ve skupině prostředků existuje a požadavek neobsahuje žádné aktualizace vlastností, není provedena žádná akce. Pokud prostředek existuje, ale změnily se vlastnosti, je existující prostředek aktualizovaný. Pokud prostředek neexistuje, vytvoří se nový prostředek.

Máte jistotu, že po dokončení nasazení jsou prostředky vždycky v očekávaném stavu.

## <a name="template-file"></a>Soubor šablony

V rámci šablony můžete napsat [výrazy šablony](template-expressions.md) , které přesahují možnosti formátu JSON. Tyto výrazy využívají [funkce](resource-group-template-functions.md) poskytované správce prostředků.

Šablona obsahuje následující oddíly:

* [Parametry](template-parameters.md) – poskytněte hodnoty během nasazení, které umožňují používat stejnou šablonu v různých prostředích.

* [Proměnné](template-variables.md) – definujte hodnoty, které se znovu použijí v šablonách. Můžou být vytvořené z hodnot parametrů.

* [Uživatelsky definované funkce](template-user-defined-functions.md) – vytváření přizpůsobených funkcí, které zjednodušují vaši šablonu.

* [Prostředky](resource-group-authoring-templates.md#resources) – zadejte prostředky, které chcete nasadit.

* [Výstupy](template-outputs.md) – vrací hodnoty z nasazených prostředků.

## <a name="template-features"></a>Funkce šablon

Správce prostředků analyzuje závislosti, aby bylo zajištěno, že se prostředky vytvoří ve správném pořadí. Většina závislostí je určena implicitně. Nicméně můžete explicitně nastavit závislost, abyste se ujistili, že jeden prostředek je nasazen před jiným prostředkem. Další informace najdete v tématu [Definování závislostí v šablonách Azure Resource Manageru](resource-group-define-dependencies.md).

Prostředek můžete přidat do šablony a případně ho nasadit. Obvykle předáte hodnotu parametru, která určuje, zda prostředek musí být nasazen. Další informace najdete v tématu [podmíněné nasazení v šablonách správce prostředků](conditional-resource-deployment.md).

Namísto opakujících se bloků JSON ve vaší šabloně je možné použít kopírovací element k určení více než jedné instance proměnné, vlastnosti nebo prostředku. Další informace naleznete v tématu [iterace prostředků, vlastností nebo proměnných v šablonách Azure Resource Manager](resource-group-create-multiple.md).

## <a name="export-templates"></a>Export šablon

Šablonu pro stávající skupinu prostředků můžete získat buď exportováním aktuálního stavu skupiny prostředků, nebo zobrazením šablony použité pro konkrétní nasazení. Zobrazení [vyexportované šablony](export-template-portal.md) vám pomůže blíže se seznámit se syntaxí šablony.

Když vytvoříte řešení z portálu, bude toto řešení automaticky zahrnovat šablonu nasazení. Šablonu nemusíte vytvářet od začátku, protože můžete začít se šablonou pro své řešení a upravit ji tak, aby vyhovovala vašim konkrétním potřebám. Ukázku najdete v tématu [rychlý Start: Vytváření a nasazování šablon Azure Resource Manager pomocí Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md).

## <a name="template-deployment-process"></a>Proces Template deployment

Když nasadíte šablonu, Správce prostředků převede šablonu na operace REST API. Když například Resource Manager obdrží šablonu s následující definicí prostředku:

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
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
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

## <a name="template-design"></a>Návrh šablony

Záleží pouze na vás, jak definujete šablony a skupiny prostředků a jak chcete spravovat své řešení. Například můžete nasadit svou třívrstvou aplikaci prostřednictvím jediné šablony do jedné skupiny prostředků.

![třívrstvá šablona](./media/template-deployment-overview/3-tier-template.png)

Není však nutné definovat celou infrastrukturu v jediné šabloně. Často má smysl rozdělit požadavky nasazení do několika cílených šablon, které jsou zaměřené na konkrétní účel. Tyto šablony můžete snadno opakovaně použít pro různá řešení. Chcete-li nasadit konkrétní řešení, vytvoříte hlavní šablonu, která propojí všechny požadované šablony. Následující obrázek znázorňuje způsob nasazení třívrstvého řešení prostřednictvím nadřazené šablony, která obsahuje tři vnořené šablony.

![šablona vnořených vrstev](./media/template-deployment-overview/nested-tiers-template.png)

Pokud si představíte, že vaše vrstvy mají tři samostatné životní cykly, můžete tyto tři vrstvy nasadit do samostatných skupin prostředků. Všimněte si, že prostředky mohou být stále propojené s prostředky v jiných skupinách prostředků.

![šablona vrstvy](./media/template-deployment-overview/tier-templates.png)

Informace o vnořených šablonách najdete v tématu [Použití propojených šablon s Azure Resource Managerem](resource-group-linked-templates.md).

## <a name="next-steps"></a>Další kroky

* Informace o vlastnostech v souborech šablon naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).
* Chcete-li začít s vývojem šablony, přečtěte si téma [použití Visual Studio Code k vytvoření šablon Azure Resource Manager](resource-manager-tools-vs-code.md).
* Úvod ke službě Správce prostředků, včetně jejích funkcí správy, najdete v tématu [Azure Resource Manager Overview](resource-group-overview.md).

