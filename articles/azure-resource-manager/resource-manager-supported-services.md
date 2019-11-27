---
title: Poskytovatelé prostředků a typy prostředků
description: Popisuje poskytovatele prostředků, kteří podporují Správce prostředků, jejich schémata a dostupné verze rozhraní API a oblasti, které mohou hostovat prostředky.
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 73cc053ab2ca19f42e3c45b8350d1e2baedfcc7a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422210"
---
# <a name="azure-resource-providers-and-types"></a>Poskytovatelé a typy prostředků Azure

Při nasazování prostředků často potřebujete získat informace o poskytovatelích a typech prostředků. Pokud například chcete ukládat klíče a tajné kódy, budete pracovat s poskytovatelem prostředků Microsoft. datatrezoru. Tento poskytovatel prostředků nabízí typ prostředku s názvem trezory pro vytvoření trezoru klíčů.

Název typu prostředku má formát: **{poskytovatel prostředku}/{typ prostředku}** . Typ prostředku pro Trezor klíčů je **trezor a trezory společnosti Microsoft.**

V tomto článku získáte informace o těchto tématech:

* Zobrazit všechny poskytovatele prostředků v Azure
* Zkontroluje stav registrace poskytovatele prostředků.
* Registrace poskytovatele prostředků
* Zobrazení typů prostředků pro poskytovatele prostředků
* Zobrazit platná umístění pro typ prostředku
* Zobrazit platné verze rozhraní API pro typ prostředku

Tyto kroky můžete provádět pomocí Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure.

Seznam, který mapuje poskytovatele prostředků na služby Azure, najdete v tématu [poskytovatelé prostředků pro služby Azure](azure-services-resource-providers.md).

## <a name="azure-portal"></a>portál Azure

Pokud chcete zobrazit všechny poskytovatele prostředků a stav registrace pro vaše předplatné:

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
2. V nabídce Azure Portal vyberte **všechny služby**.

    ![vybrat odběry](./media/resource-manager-supported-services/select-all-services.png)

3. V poli **všechny služby** zadejte **předplatné**a pak vyberte **předplatná**.
4. Vyberte předplatné ze seznamu odběrů, které chcete zobrazit.
5. Vyberte **poskytovatele prostředků** a zobrazte seznam dostupných poskytovatelů prostředků.

    ![Zobrazit poskytovatele prostředků](./media/resource-manager-supported-services/show-resource-providers.png)

6. Když zaregistrujete poskytovatele prostředků, nakonfigurujete vaše předplatné, aby fungovalo s poskytovatelem prostředků. Obor pro registraci je vždy předplatné. Ve výchozím nastavení je řada poskytovatelů prostředků zaregistrována automaticky. Je ale možné, že budete muset některé poskytovatele prostředků zaregistrovat ručně. Chcete-li zaregistrovat poskytovatele prostředků, musíte mít oprávnění k provedení operace `/register/action` pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník. Chcete-li zaregistrovat poskytovatele prostředků, vyberte možnost **Registrovat**. Na předchozím snímku obrazovky se zvýrazní odkaz **zaregistrovat** pro **Microsoft. detail**.

    Pokud v předplatném máte typy prostředků od tohoto poskytovatele prostředků, nemůžete zrušit registraci poskytovatele prostředků.

Chcete-li zobrazit informace o konkrétním poskytovateli prostředků:

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
2. V nabídce Azure Portal vyberte **všechny služby**.
3. V poli **všechny služby** zadejte **Průzkumník prostředků**a potom vyberte **Průzkumník prostředků**.

    ![Vybrat všechny služby](./media/resource-manager-supported-services/select-resource-explorer.png)

4. Rozbalte položku **poskytovatelé** a vyberte šipku doprava.

    ![Vybrat poskytovatele](./media/resource-manager-supported-services/select-providers.png)

5. Rozbalte poskytovatele prostředků a typ prostředku, který chcete zobrazit.

    ![Vybrat typ prostředku](./media/resource-manager-supported-services/select-resource-type.png)

6. Správce prostředků se podporují ve všech oblastech, ale prostředky, které nasadíte, nemusí být podporované ve všech oblastech. Kromě toho můžou mít vaše předplatné omezení, které vám brání v používání některých oblastí, které podporují daný prostředek. Průzkumník prostředků zobrazuje platná umístění pro daný typ prostředku.

    ![Zobrazit umístění](./media/resource-manager-supported-services/show-locations.png)

7. Verze rozhraní API odpovídá verzi REST API operací, které jsou vydány poskytovatelem prostředků. V případě, že poskytovatel prostředků poskytuje nové funkce, uvolňuje novou verzi REST API. Průzkumník prostředků zobrazuje platné verze rozhraní API pro daný typ prostředku.

    ![Zobrazit verze rozhraní API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="azure-powershell"></a>Azure Powershell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud chcete zobrazit všechny poskytovatele prostředků v Azure a stav registrace pro vaše předplatné, použijte:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Což vrátí výsledek podobný tomuto:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Když zaregistrujete poskytovatele prostředků, nakonfigurujete vaše předplatné, aby fungovalo s poskytovatelem prostředků. Obor pro registraci je vždy předplatné. Ve výchozím nastavení je řada poskytovatelů prostředků zaregistrována automaticky. Je ale možné, že budete muset některé poskytovatele prostředků zaregistrovat ručně. Chcete-li zaregistrovat poskytovatele prostředků, musíte mít oprávnění k provedení operace `/register/action` pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Což vrátí výsledek podobný tomuto:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Pokud v předplatném máte typy prostředků od tohoto poskytovatele prostředků, nemůžete zrušit registraci poskytovatele prostředků.

Pokud chcete zobrazit informace o konkrétním poskytovateli prostředků, použijte:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Což vrátí výsledek podobný tomuto:

```powershell
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

```powershell
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

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Správce prostředků se podporují ve všech oblastech, ale prostředky, které nasadíte, nemusí být podporované ve všech oblastech. Kromě toho můžou mít vaše předplatné omezení, které vám brání v používání některých oblastí, které podporují daný prostředek.

Chcete-li získat podporovaná umístění pro typ prostředku, použijte.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Který vrátí:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Pokud chcete zobrazit všechny poskytovatele prostředků v Azure a stav registrace pro vaše předplatné, použijte:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Což vrátí výsledek podobný tomuto:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Když zaregistrujete poskytovatele prostředků, nakonfigurujete vaše předplatné, aby fungovalo s poskytovatelem prostředků. Obor pro registraci je vždy předplatné. Ve výchozím nastavení je řada poskytovatelů prostředků zaregistrována automaticky. Je ale možné, že budete muset některé poskytovatele prostředků zaregistrovat ručně. Chcete-li zaregistrovat poskytovatele prostředků, musíte mít oprávnění k provedení operace `/register/action` pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník.

```azurecli
az provider register --namespace Microsoft.Batch
```

Vrátí zprávu o tom, že probíhá registrace.

Pokud v předplatném máte typy prostředků od tohoto poskytovatele prostředků, nemůžete zrušit registraci poskytovatele prostředků.

Pokud chcete zobrazit informace o konkrétním poskytovateli prostředků, použijte:

```azurecli
az provider show --namespace Microsoft.Batch
```

Což vrátí výsledek podobný tomuto:

```azurecli
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

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Který vrátí:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Verze rozhraní API odpovídá verzi REST API operací, které jsou vydány poskytovatelem prostředků. V případě, že poskytovatel prostředků poskytuje nové funkce, uvolňuje novou verzi REST API.

K získání dostupných verzí rozhraní API pro typ prostředku použijte:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Který vrátí:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Správce prostředků se podporují ve všech oblastech, ale prostředky, které nasadíte, nemusí být podporované ve všech oblastech. Kromě toho můžou mít vaše předplatné omezení, které vám brání v používání některých oblastí, které podporují daný prostředek.

Chcete-li získat podporovaná umístění pro typ prostředku, použijte.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Který vrátí:

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření šablon Správce prostředků najdete v tématu [vytváření šablon Azure Resource Manager](resource-group-authoring-templates.md). 
* Chcete-li zobrazit schémata šablon poskytovatele prostředků, přečtěte si téma [reference šablony](/azure/templates/).
* Seznam, který mapuje poskytovatele prostředků na služby Azure, najdete v tématu [poskytovatelé prostředků pro služby Azure](azure-services-resource-providers.md).
* Pokud chcete zobrazit operace pro poskytovatele prostředků, přečtěte si článek [Azure REST API](/rest/api/).
