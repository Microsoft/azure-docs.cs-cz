---
title: Šablony Azure Resource Manageru
description: Popisuje způsob použití šablon Azure Resource Manager pro nasazení prostředků.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: tomfitz
ms.openlocfilehash: 95c127b3a7c9c47c96b292066bf1597a02896806
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166521"
---
# <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Pomocí Azure Resource Manager můžete vytvořit šablony, které definují, co chcete nasadit do Azure. Šablona je soubor JavaScript Object Notation (JSON), který obsahuje infrastrukturu a konfiguraci vašeho řešení Azure. Pomocí šablony můžete řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně.

Šablona používá deklarativní syntaxi, která umožňuje určit, co máte v úmyslu nasadit bez nutnosti napsat sekvenci programovacích příkazů k jeho vytvoření. V šabloně určíte prostředky, které chcete nasadit, a vlastnosti těchto prostředků.

## <a name="benefits-of-resource-manager-templates"></a>Výhody šablon Správce prostředků

Šablony Správce prostředků poskytují několik výhod:

* Můžete všechny prostředky pro vaše řešení nasadit, spravovat a monitorovat jako skupinu a nemusíte je zpracovávat jednotlivě.

* Můžete svoje řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně.

* Infrastrukturu můžete spravovat pomocí deklarativních šablon místo skriptů.

* Můžete definovat závislosti mezi prostředky, takže se nasadí ve správném pořadí.

Následující návrhy vám pomohou při práci s vašimi řešeními plně využít výhod Resource Manageru.

* K definování a nasazení infrastruktury využijte deklarativní syntaxi v šablonách Resource Manageru, nikoli imperativní příkazy.

* V šabloně definujte všechny kroky nasazení a konfigurace. K nastavení svého řešení byste neměli využívat žádné ruční kroky.

* Ke správě vašich prostředků využijte imperativní příkazy, například pro spuštění nebo zastavení aplikace nebo počítače.

* Postupujte podle [osvědčených postupů pro šablonu Azure Resource Manager](template-best-practices.md).

## <a name="template-deployment-process"></a>Proces Template deployment

Když nasadíte šablonu, Správce prostředků analyzuje šablonu a převede její syntaxi na operace REST API. Odesílá tyto operace příslušným poskytovatelům prostředků. Když například Resource Manager obdrží šablonu s následující definicí prostředku:

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

## <a name="template-structure"></a>Struktura šablon

Záleží pouze na vás, jak definujete šablony a skupiny prostředků a jak chcete spravovat své řešení. Například můžete nasadit svou třívrstvou aplikaci prostřednictvím jediné šablony do jedné skupiny prostředků.

![třívrstvá šablona](./media/resource-group-overview/3-tier-template.png)

Není však nutné definovat celou infrastrukturu v jediné šabloně. Často má smysl rozdělit požadavky nasazení do několika cílených šablon, které jsou zaměřené na konkrétní účel. Tyto šablony můžete snadno opakovaně použít pro různá řešení. Chcete-li nasadit konkrétní řešení, vytvoříte hlavní šablonu, která propojí všechny požadované šablony. Následující obrázek znázorňuje způsob nasazení třívrstvého řešení prostřednictvím nadřazené šablony, která obsahuje tři vnořené šablony.

![šablona vnořených vrstev](./media/resource-group-overview/nested-tiers-template.png)

Pokud si představíte, že vaše vrstvy mají tři samostatné životní cykly, můžete tyto tři vrstvy nasadit do samostatných skupin prostředků. Všimněte si, že prostředky mohou být stále propojené s prostředky v jiných skupinách prostředků.

![šablona vrstvy](./media/resource-group-overview/tier-templates.png)

Informace o vnořených šablonách najdete v tématu [Použití propojených šablon s Azure Resource Managerem](resource-group-linked-templates.md).

Azure Resource Manager analyzuje závislosti a zajistí, že se prostředky vytvoří ve správném pořadí. Pokud jeden prostředek závisí na hodnotě z jiného prostředku (například virtuální počítač potřebuje účet úložiště pro disky), nastavíte závislost. Další informace najdete v tématu [Definování závislostí v šablonách Azure Resource Manageru](resource-group-define-dependencies.md).

Šablony můžete také využít pro aktualizace infrastruktury. Můžete například ke svému řešení přidat prostředek a konfigurační pravidla pro prostředky, které jsou už nasazené. Pokud šablona definuje prostředek, který již existuje, Správce prostředků aktualizuje existující prostředek namísto vytvoření nového.

Resource Manager poskytuje rozšíření pro scénáře, kdy potřebujete další operace, jako je například instalace konkrétního softwaru, který není zahrnutý v původní instalaci. Pokud již využíváte službu pro správu konfigurace, jako je DSC, Chef nebo Puppet, můžete tuto službu s pomocí rozšíření používat i nadále. Informace o rozšířeních virtuálních počítačů najdete v tématu [Funkce a rozšíření virtuálních počítačů](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Když vytvoříte řešení z portálu, bude toto řešení automaticky zahrnovat šablonu nasazení. Šablonu nemusíte vytvářet od začátku, protože můžete začít se šablonou pro své řešení a upravit ji tak, aby vyhovovala vašim konkrétním potřebám. Ukázku najdete v tématu [rychlý Start: Vytváření a nasazování šablon Azure Resource Manager pomocí Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md). Šablonu pro stávající skupinu prostředků můžete také získat tak, že vyexportujete aktuální stav této skupiny prostředků nebo zobrazíte šablonu použitou pro konkrétní nasazení. Zobrazení [vyexportované šablony](./manage-resource-groups-portal.md#export-resource-groups-to-templates) vám pomůže blíže se seznámit se syntaxí šablony.

Šablona se také stane součástí zdrojového kódu vaší aplikace. Můžete ji vrátit se změnami do vašeho úložiště zdrojového kódu a aktualizovat ji podle toho, jak se bude vaše aplikace vyvíjet. K úpravě šablony můžete použít Visual Studio.

## <a name="next-steps"></a>Další postup

Další informace o souborech šablon naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).

Po definování šablony jste připraveni k nasazení prostředků do Azure. Informace o nasazení prostředků najdete v těchto tématech:

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Resource Manageru a rozhraní příkazového řádku Azure](resource-group-template-deploy-cli.md)
* [Nasazení prostředků pomocí šablon Resource Manageru a webu Azure Portal](resource-group-template-deploy-portal.md)
* [Nasazení prostředků pomocí šablon Resource Manageru a jeho rozhraní REST API](resource-group-template-deploy-rest.md)

