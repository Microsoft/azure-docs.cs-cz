---
title: Šablony Azure Resource Manageru
description: Popisuje způsob použití šablon Azure Resource Manager pro nasazení prostředků.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 4f273a04322246a2b4112c0b5b8a062732bab555
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390717"
---
# <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Díky přesunu do cloudu mnoho týmů přijalo agilní metody vývoje. Tyto týmy iterují rychle. Potřebují opakovaně a spolehlivě nasazovat svá řešení do cloudu. Rozdělení mezi operacemi a vývojem zmizelo, protože týmy potřebují spravovat infrastrukturu a zdrojový kód v jednom procesu.

Jedním z řešení těchto problémů je automatizace nasazení a použití postupů infrastruktury jako kódu. Použijte kód k definování toho, co je potřeba nasadit, a spravujte tento kód pomocí stejného procesu jako zdrojový kód. Infrastrukturu ukládáte jako kód ve zdrojovém úložišti a verzi.

Azure Resource Manager umožňuje implementovat infrastrukturu jako kód prostřednictvím šablon Správce prostředků. Šablona je soubor JavaScript Object Notation (JSON), který obsahuje infrastrukturu a konfiguraci pro vaše řešení Azure. Šablona používá deklarativní syntaxi, která umožňuje určit, co máte v úmyslu nasadit bez nutnosti napsat sekvenci programovacích příkazů k jeho vytvoření. V šabloně určíte prostředky, které chcete nasadit, a vlastnosti těchto prostředků.

## <a name="benefits-of-resource-manager-templates"></a>Výhody šablon Správce prostředků

Šablony Správce prostředků poskytují několik výhod. Můžete:

* Nasaďte, spravujte a monitorujte všechny prostředky pro vaše řešení jako skupinu, místo toho, aby se tyto prostředky nemusely zpracovávat jednotlivě.

* Opakovaně nasaďte řešení v průběhu životního cyklu vývoje a měli byste mít jistotu, že se prostředky nasazují v konzistentním stavu.

* Spravujte svoji infrastrukturu prostřednictvím deklarativních šablon místo skriptů.

* Definujte závislosti mezi prostředky tak, aby byly nasazeny ve správném pořadí.

* Využijte výhod nových verzí a služeb hned, protože jiné strany nevyžadují žádnou mezilehlou práci.

## <a name="template-file"></a>Soubor šablony

V rámci šablony můžete napsat [výrazy šablony](template-expressions.md) , které přesahují možnosti formátu JSON. Tyto výrazy využívají [funkce](resource-group-template-functions.md) poskytované správce prostředků.

Šablona obsahuje následující oddíly:

* [Parametry](template-parameters.md) – poskytněte hodnoty během nasazení, které umožňují používat stejnou šablonu v různých prostředích.

* [Proměnné](template-variables.md) – definujte hodnoty, které se používají v šablonách.

* [Uživatelsky definované funkce](template-user-defined-functions.md) – vytváření přizpůsobených funkcí, které zjednodušují vaši šablonu.

* [Prostředky](resource-group-authoring-templates.md#resources) – zadejte prostředky, které chcete nasadit.

* [Výstupy](template-outputs.md) – vrací hodnoty z nasazených prostředků.

## <a name="dependencies"></a>Závislosti

Azure Resource Manager analyzuje závislosti a zajistí, že se prostředky vytvoří ve správném pořadí. Pokud jeden prostředek závisí na hodnotě z jiného prostředku (například virtuální počítač potřebuje účet úložiště pro disky), nastavíte závislost. Další informace najdete v tématu [Definování závislostí v šablonách Azure Resource Manageru](resource-group-define-dependencies.md).

## <a name="conditional-deployment"></a>Podmíněné nasazení

Prostředek můžete přidat do šablony a případně ho nasadit. Obvykle předáte hodnotu parametru, která určuje, zda prostředek musí být nasazen. Další informace najdete v tématu [podmíněné nasazení v šablonách správce prostředků](conditional-resource-deployment.md).

## <a name="create-multiple-instances"></a>Vytvoření několika instancí

Namísto opakujících se bloků JSON ve vaší šabloně je možné použít kopírovací element k určení více než jedné instance proměnné, vlastnosti nebo prostředku. Další informace naleznete v tématu [iterace prostředků, vlastností nebo proměnných v šablonách Azure Resource Manager](resource-group-create-multiple.md).

## <a name="export-templates"></a>Export šablon

Šablonu pro stávající skupinu prostředků můžete získat buď exportováním aktuálního stavu skupiny prostředků, nebo zobrazením šablony použité pro konkrétní nasazení. Zobrazení [vyexportované šablony](export-template-portal.md) vám pomůže blíže se seznámit se syntaxí šablony.

Když vytvoříte řešení z portálu, bude toto řešení automaticky zahrnovat šablonu nasazení. Šablonu nemusíte vytvářet od začátku, protože můžete začít se šablonou pro své řešení a upravit ji tak, aby vyhovovala vašim konkrétním potřebám. Ukázku najdete v tématu [rychlý Start: Vytváření a nasazování šablon Azure Resource Manager pomocí Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md).

## <a name="template-deployment-process"></a>Proces Template deployment

Když nasadíte šablonu, Správce prostředků analyzuje šablonu a převede její syntaxi na operace REST API. Když například Resource Manager obdrží šablonu s následující definicí prostředku:

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

Pokud prostředek ve skupině prostředků již existuje a požadavek neobsahuje žádné aktualizace vlastností, není provedena žádná akce. Pokud prostředek existuje, ale změnily se vlastnosti, je existující prostředek aktualizovaný. Pokud prostředek neexistuje, vytvoří se nový prostředek. Tento přístup usnadňuje opětovné nasazení šablony a ví, že prostředky zůstávají v konzistentním stavu.

## <a name="template-design"></a>Návrh šablony

Záleží pouze na vás, jak definujete šablony a skupiny prostředků a jak chcete spravovat své řešení. Například můžete nasadit svou třívrstvou aplikaci prostřednictvím jediné šablony do jedné skupiny prostředků.

![třívrstvá šablona](./media/template-deployment-overview/3-tier-template.png)

Není však nutné definovat celou infrastrukturu v jediné šabloně. Často má smysl rozdělit požadavky nasazení do několika cílených šablon, které jsou zaměřené na konkrétní účel. Tyto šablony můžete snadno opakovaně použít pro různá řešení. Chcete-li nasadit konkrétní řešení, vytvoříte hlavní šablonu, která propojí všechny požadované šablony. Následující obrázek znázorňuje způsob nasazení třívrstvého řešení prostřednictvím nadřazené šablony, která obsahuje tři vnořené šablony.

![šablona vnořených vrstev](./media/template-deployment-overview/nested-tiers-template.png)

Pokud si představíte, že vaše vrstvy mají tři samostatné životní cykly, můžete tyto tři vrstvy nasadit do samostatných skupin prostředků. Všimněte si, že prostředky mohou být stále propojené s prostředky v jiných skupinách prostředků.

![šablona vrstvy](./media/template-deployment-overview/tier-templates.png)

Informace o vnořených šablonách najdete v tématu [Použití propojených šablon s Azure Resource Managerem](resource-group-linked-templates.md).

## <a name="next-steps"></a>Další postup

* Informace o vlastnostech v souborech šablon naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).
* Chcete-li začít s vývojem šablony, přečtěte si téma [použití Visual Studio Code k vytvoření šablon Azure Resource Manager](resource-manager-tools-vs-code.md).


