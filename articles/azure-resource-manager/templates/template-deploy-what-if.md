---
title: Template deployment co-if (Preview)
description: Než nasadíte šablonu Azure Resource Manager, určete, jaké změny se budou probíhat u vašich prostředků.
author: mumian
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jgao
ms.openlocfilehash: edb9f5e35008b1270031d8e2d5c8a5efa37cb554
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484165"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Operace s citlivostí pro nasazení šablony Správce prostředků (Preview)

Před nasazením šablony můžete chtít zobrazit náhled změn, ke kterým dojde. Azure Resource Manager poskytuje operaci citlivostní operace, která vám umožní zjistit, jak se prostředky změní, pokud šablonu nasadíte. Operace citlivosti neprovede žádné změny stávajících prostředků. Místo toho odhadne změny, pokud je zadaná šablona nasazena.

> [!NOTE]
> Operace citlivosti je aktuálně ve verzi Preview. Pokud ho chcete použít, musíte se [zaregistrovat ve verzi Preview](https://aka.ms/armtemplatepreviews). Ve verzi Preview můžou výsledky někdy Ukázat, že se prostředek změní, když se ve skutečnosti žádná změna nestane. Pracujeme na tom, abychom tyto problémy snížili, ale potřebujeme vaši technickou podporu. Ohlaste prosím tyto problémy na [https://aka.ms/whatifissues](https://aka.ms/whatifissues).

Můžete použít operaci citlivostní operace s příkazem `New-AzDeploymentWhatIf` PowerShellu nebo [nasazením what if](/rest/api/resources/deployments/whatif) REST.

Výstup v PowerShellu vypadá takto:

![Správce prostředků fullresourcepayload a typ změny nasazení šablony](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

## <a name="change-types"></a>Změnit typy

Operace citlivostní zpracování seznamu obsahuje šest různých typů změn:

- **Vytvořit**: prostředek aktuálně neexistuje, ale je definován v šabloně. Prostředek se vytvoří.

- **Odstranit**: Tento typ změny platí pouze v případě, že je pro nasazení použit [režim úplného režimu](deployment-modes.md) . Prostředek existuje, ale není definovaný v šabloně. V režimu úplného režimu se prostředek odstraní. V tomto typu změny jsou zahrnuty pouze prostředky, které [podporují odstranění režimu dokončení](complete-mode-deletion.md) .

- **Ignore**: prostředek existuje, ale není definovaný v šabloně. Prostředek se nebude nasazovat ani upravovat.

- Žádná **Změna**: prostředek existuje a je definován v šabloně. Prostředek se znovu nasadí, ale vlastnosti prostředku se nezmění. Tento typ změny se vrátí, pokud je [ResultFormat](#result-format) nastaveno na `FullResourcePayloads`, což je výchozí hodnota.

- **Upravit**: prostředek existuje a je definován v šabloně. Prostředek se znovu nasadí a změní se vlastnosti prostředku. Tento typ změny se vrátí, pokud je [ResultFormat](#result-format) nastaveno na `FullResourcePayloads`, což je výchozí hodnota.

- **Nasazení**: prostředek existuje a je definován v šabloně. Prostředek se znovu nasadí. Vlastnosti prostředku mohou nebo nemusí být změněny. Tato operace vrátí tento typ změny, pokud nemá dostatek informací k určení, zda se změní některé vlastnosti. Tato podmínka se zobrazí jenom v případě, že je [ResultFormat](#result-format) nastavené na `ResourceIdOnly`.

## <a name="deployment-scope"></a>Rozsah nasazení

Operaci s citlivostí pro nasazení můžete použít buď na úrovni předplatného nebo skupiny prostředků. Rozsah nasazení nastavíte pomocí parametru `-ScopeType`. Přijaté hodnoty jsou `Subscription` a `ResourceGroup`. Tento článek popisuje nasazení skupin prostředků.

Další informace o nasazeních na úrovni předplatného najdete v tématu [Vytvoření skupin prostředků a prostředků na úrovni předplatného](deploy-to-subscription.md#).

## <a name="result-format"></a>Výsledný formát

Můžete řídit úroveň podrobností vrácených o předpokládaných změnách. Nastavením parametru `ResultFormat` `FullResourcePayloads` získáte seznam prostředků, které se změní a zobrazí se podrobnosti o vlastnostech, které se změní. Nastavením parametru `ResultFormat` `ResourceIdOnly` zobrazíte seznam prostředků, které se změní. Výchozí hodnota je `FullResourcePayloads`.  

Následující snímky obrazovky znázorňují dva různé formáty výstupu:

- Úplné datové vytížení prostředků

    ![Správce prostředků výstupem operace fullresourcepayloads Deployment Template](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-fullresourcepayload.png)

- Pouze ID prostředku

    ![Správce prostředků výstupem operace resourceidonly Deployment Template](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-resourceidonly.png)

## <a name="run-what-if-operation"></a>Spustit operaci s citlivostí

### <a name="set-up-environment"></a>Nastavení prostředí

Pokud chcete zjistit, jak to funguje, spustíme některé testy. Nejdřív nasaďte šablonu ze [šablon Azure pro rychlý Start, které vytvoří účet úložiště](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json). Výchozí typ účtu úložiště je `Standard_LRS`. Tento účet úložiště použijete k otestování toho, jak jsou změny hlášeny podle toho, co je potřeba.

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

### <a name="test-modification"></a>Změna testu

Po dokončení nasazení budete připraveni k otestování operace citlivostní zpracování. Spusťte příkaz příkazového řádku, ale změňte typ účtu úložiště na `Standard_GRS`.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" `
  -storageAccountType Standard_GRS
```

Výstup citlivosti je podobný následujícímu:

![Výstup operace s popisem operace s Správce prostředků šablonou](./media/template-deploy-what-if/resource-manager-deployment-whatif-output.png)

V horní části výstupu si všimněte, že jsou definovány barvy, aby označovaly typ změn.

V dolní části výstupu se zobrazí název SKU (typ účtu úložiště), který bude změněn z **Standard_LRS** na **Standard_GRS**.

Některé vlastnosti, které jsou uvedené jako odstraněné, se ve skutečnosti nezmění. Na předchozím obrázku jsou tyto vlastnosti accessTier, Encryption. Source a dalšími v této části. Vlastnosti mohou být nesprávně hlášeny jako odstraněné, pokud nejsou v šabloně, ale budou automaticky nastaveny během nasazování jako výchozí hodnoty. Tento výsledek se považuje za "hluk" v reakci citlivosti. Konečný nasazený prostředek bude mít nastavené hodnoty pro vlastnosti. V případě, že se operace co-if dospěla, tyto vlastnosti se vyfiltrují z výsledku.

### <a name="test-deletion"></a>Odstranění testu

Operace citlivosti podporuje použití [režimu nasazení](deployment-modes.md). Při nastavení na režim úplné se odstraní prostředky, které nejsou v šabloně. Následující příklad nasadí [šablonu, která nemá definované žádné prostředky](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) v režimu úplného zobrazení.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Protože v šabloně nejsou definovány žádné prostředky a režim nasazení je nastavený na dokončeno, účet úložiště se odstraní.

![Správce prostředků režim nasazení výstupu operace citlivosti nasazení šablon](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Je důležité pamatovat na to, co dělat, když nedělá žádné skutečné změny. Účet úložiště ve vaší skupině prostředků stále existuje.

## <a name="next-steps"></a>Další kroky

- Pokud si všimnete špatných výsledků z verze Preview co-if, nahlaste prosím problémy na [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Postup nasazení šablon pomocí Azure PowerShell najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](deploy-powershell.md).
- Pokud chcete nasadit šablony s REST, přečtěte si téma [nasazení prostředků pomocí šablon Správce prostředků a Správce prostředků REST API](deploy-rest.md).
- Chcete-li se vrátit k úspěšnému nasazení, když se zobrazí chyba, přečtěte si téma [vrácení chyby při úspěšném nasazení](rollback-on-error.md).
