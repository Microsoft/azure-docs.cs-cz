---
title: Poskytovatelé prostředků a typy prostředků
description: Popisuje poskytovatele prostředků, kteří podporují Azure Resource Manager. Popisuje jejich schémata, dostupné verze rozhraní API a oblasti, které mohou hostovat prostředky.
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 702836e0dc98b06ccf6e0eeb0d0f373374c4e783
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426455"
---
# <a name="azure-resource-providers-and-types"></a>Poskytovatelé a typy prostředků Azure

Při nasazování prostředků často potřebujete získat informace o poskytovatelích a typech prostředků. Pokud například chcete ukládat klíče a tajné klíče, využijete poskytovatele prostředků Microsoft.KeyVault. Tento poskytovatel prostředků nabízí typ prostředků nazvaný trezory pro vytvoření trezoru klíčů.

Název typu prostředku má formát: **{poskytovatel prostředku}/{typ prostředku}**. Typ prostředku pro Trezor klíčů je **trezor a trezory společnosti Microsoft.**

V tomto článku získáte informace o těchto tématech:

* Zobrazit všechny poskytovatele prostředků v Azure
* Zkontroluje stav registrace poskytovatele prostředků.
* Registrace poskytovatele prostředků
* Zobrazení typů prostředků pro poskytovatele prostředků
* Zobrazit platná umístění pro typ prostředku
* Zobrazit platné verze rozhraní API pro typ prostředku

Tyto kroky můžete provádět pomocí Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure.

Seznam, který mapuje poskytovatele prostředků na služby Azure, najdete v tématu [poskytovatelé prostředků pro služby Azure](azure-services-resource-providers.md).

## <a name="register-resource-provider"></a>Registrace poskytovatele prostředků

Před použitím poskytovatele prostředků musí být vaše předplatné Azure zaregistrované pro poskytovatele prostředků. Registrace nakonfiguruje vaše předplatné, aby spolupracovalo s poskytovatelem prostředků. Někteří poskytovatelé prostředků jsou registrováni ve výchozím nastavení. Jiní poskytovatelé prostředků jsou registrováni automaticky při provedení určitých akcí. Například při vytváření prostředku prostřednictvím portálu je poskytovatel prostředků obvykle zaregistrován za vás. Pro jiné scénáře možná budete muset ručně zaregistrovat poskytovatele prostředků.

V tomto článku se dozvíte, jak ověřit stav registrace poskytovatele prostředků a jak ho podle potřeby zaregistrovat. Musíte mít oprávnění k provedení `/register/action` operace pro poskytovatele prostředků. Oprávnění je obsaženo v rolích přispěvatel a Owner.

Kód vaší aplikace by neměl blokovat vytváření prostředků pro poskytovatele prostředků, který je ve stavu **registrace** . Když zaregistrujete poskytovatele prostředků, operace se provádí individuálně pro každou podporovanou oblast. Chcete-li v oblasti vytvořit prostředky, musí být registrace dokončena pouze v této oblasti. Při neblokování poskytovatele prostředků ve stavu registrace může aplikace pokračovat mnohem dřív než čekání na dokončení všech oblastí.

Pokud v předplatném máte typy prostředků od tohoto poskytovatele prostředků, nemůžete zrušit registraci poskytovatele prostředků.

## <a name="azure-portal"></a>portál Azure

Pokud chcete zobrazit všechny poskytovatele prostředků a stav registrace pro vaše předplatné:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. V nabídce webu Azure Portal vyberte **Všechny služby**.

    ![vybrat odběry](./media/resource-providers-and-types/select-all-services.png)

3. V poli **všechny služby** zadejte **předplatné** a pak vyberte **předplatná**.
4. Vyberte předplatné ze seznamu odběrů, které chcete zobrazit.
5. Vyberte **poskytovatele prostředků** a zobrazte seznam dostupných poskytovatelů prostředků.

    ![Zobrazit poskytovatele prostředků](./media/resource-providers-and-types/show-resource-providers.png)

6. Chcete-li zaregistrovat poskytovatele prostředků, vyberte možnost **Registrovat**. Na předchozím snímku obrazovky se zvýrazní odkaz **zaregistrovat** pro **Microsoft. detail**.

Chcete-li zobrazit informace o konkrétním poskytovateli prostředků:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. V nabídce webu Azure Portal vyberte **Všechny služby**.
3. V poli **všechny služby** zadejte **Průzkumník prostředků** a potom vyberte **Průzkumník prostředků**.

    ![vybrat všechny služby](./media/resource-providers-and-types/select-resource-explorer.png)

4. Rozbalte položku **poskytovatelé** a vyberte šipku doprava.

    ![Vybrat poskytovatele](./media/resource-providers-and-types/select-providers.png)

5. Rozbalte poskytovatele prostředků a typ prostředku, který chcete zobrazit.

    ![Vybrat typ prostředku](./media/resource-providers-and-types/select-resource-type.png)

6. Správce prostředků se podporují ve všech oblastech, ale prostředky, které nasadíte, nemusí být podporované ve všech oblastech. U předplatného může být taky omezení, která vám zabrání v používání některých oblastí, které podporují daný prostředek. Průzkumník prostředků zobrazuje platná umístění pro daný typ prostředku.

    ![Zobrazit umístění](./media/resource-providers-and-types/show-locations.png)

7. Verze rozhraní API odpovídá verzi REST API operací, které jsou vydány poskytovatelem prostředků. V případě, že poskytovatel prostředků poskytuje nové funkce, uvolňuje novou verzi REST API. Průzkumník prostředků zobrazuje platné verze rozhraní API pro daný typ prostředku.

    ![Zobrazit verze rozhraní API](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

Pokud chcete zobrazit všechny poskytovatele prostředků v Azure a stav registrace pro vaše předplatné, použijte:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Což vrátí výsledek podobný tomuto:

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Pokud chcete zobrazit všechny registrované poskytovatele prostředků pro vaše předplatné, použijte:

```azurepowershell-interactive
 Get-AzResourceProvider -ListAvailable | Where-Object RegistrationState -eq "Registered" | Select-Object ProviderNamespace, RegistrationState | Sort-Object ProviderNamespace
```

Chcete-li zaregistrovat poskytovatele prostředků, použijte:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Což vrátí výsledek podobný tomuto:

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Pokud chcete zobrazit informace o konkrétním poskytovateli prostředků, použijte:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Což vrátí výsledek podobný tomuto:

```output
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Chcete-li zobrazit typy prostředků pro poskytovatele prostředků, použijte:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Který vrátí:

```output
batchAccounts
operations
locations
locations/quotas
```

Verze rozhraní API odpovídá verzi REST API operací, které jsou vydány poskytovatelem prostředků. V případě, že poskytovatel prostředků poskytuje nové funkce, uvolňuje novou verzi REST API.

K získání dostupných verzí rozhraní API pro typ prostředku použijte:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Který vrátí:

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Správce prostředků se podporují ve všech oblastech, ale prostředky, které nasadíte, nemusí být podporované ve všech oblastech. U předplatného může být taky omezení, která vám zabrání v používání některých oblastí, které podporují daný prostředek.

Chcete-li získat podporovaná umístění pro typ prostředku, použijte.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Který vrátí:

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Pokud chcete zobrazit všechny poskytovatele prostředků v Azure a stav registrace pro vaše předplatné, použijte:

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Což vrátí výsledek podobný tomuto:

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Pokud chcete zobrazit všechny registrované poskytovatele prostředků pro vaše předplatné, použijte:

```azurecli-interactive
az provider list --query "sort_by([?registrationState=='Registered'].{Provider:namespace, Status:registrationState}, &Provider)" --out table
```

Chcete-li zaregistrovat poskytovatele prostředků, použijte:

```azurecli-interactive
az provider register --namespace Microsoft.Batch
```

Vrátí zprávu o tom, že probíhá registrace.

Pokud chcete zobrazit informace o konkrétním poskytovateli prostředků, použijte:

```azurecli-interactive
az provider show --namespace Microsoft.Batch
```

Což vrátí výsledek podobný tomuto:

```output
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Chcete-li zobrazit typy prostředků pro poskytovatele prostředků, použijte:

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Který vrátí:

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Verze rozhraní API odpovídá verzi REST API operací, které jsou vydány poskytovatelem prostředků. V případě, že poskytovatel prostředků poskytuje nové funkce, uvolňuje novou verzi REST API.

K získání dostupných verzí rozhraní API pro typ prostředku použijte:

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Který vrátí:

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Správce prostředků se podporují ve všech oblastech, ale prostředky, které nasadíte, nemusí být podporované ve všech oblastech. U předplatného může být taky omezení, která vám zabrání v používání některých oblastí, které podporují daný prostředek.

Chcete-li získat podporovaná umístění pro typ prostředku, použijte.

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Který vrátí:

```output
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření šablon Správce prostředků najdete v tématu [vytváření šablon Azure Resource Manager](../templates/template-syntax.md). 
* Chcete-li zobrazit schémata šablon poskytovatele prostředků, přečtěte si téma [reference šablony](/azure/templates/).
* Seznam, který mapuje poskytovatele prostředků na služby Azure, najdete v tématu [poskytovatelé prostředků pro služby Azure](azure-services-resource-providers.md).
* Pokud chcete zobrazit operace pro poskytovatele prostředků, přečtěte si článek [Azure REST API](/rest/api/).
