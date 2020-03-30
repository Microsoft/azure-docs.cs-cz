---
title: Zprostředkovatelé prostředků a typy prostředků
description: Popisuje zprostředkovatele prostředků, kteří podporují Správce prostředků, jejich schémata a dostupné verze rozhraní API a oblasti, které mohou hostovat prostředky.
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 82b8251006a1a2d4edd198eca843489d3720f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273979"
---
# <a name="azure-resource-providers-and-types"></a>Poskytovatelé a typy prostředků Azure

Při nasazování prostředků často potřebujete načíst informace o zprostředkovatelích a typech prostředků. Pokud například chcete ukládat klíče a tajné klíče, využijete poskytovatele prostředků Microsoft.KeyVault. Tento poskytovatel prostředků nabízí typ prostředků nazvaný trezory pro vytvoření trezoru klíčů.

Název typu prostředku má formát: **{poskytovatel prostředku}/{typ prostředku}**. Typ prostředku pro trezor klíčů je **Microsoft.KeyVault/vaults**.

V tomto článku získáte informace o těchto tématech:

* Zobrazení všech poskytovatelů prostředků v Azure
* Kontrola stavu registrace poskytovatele prostředků
* Registrace poskytovatele prostředků
* Zobrazit typy prostředků pro poskytovatele prostředků
* Zobrazit platná umístění pro typ prostředku
* Zobrazení platných verzí rozhraní API pro typ prostředku

Tyto kroky můžete provést prostřednictvím portálu Azure, Azure PowerShell nebo Azure CLI.

Seznam, který mapuje poskytovatele prostředků na služby Azure, najdete v [tématu zprostředkovatelé prostředků pro služby Azure](azure-services-resource-providers.md).

## <a name="azure-portal"></a>portál Azure

Zobrazení všech poskytovatelů prostředků a stav registrace předplatného:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V nabídce webu Azure Portal vyberte **Všechny služby**.

    ![vybrat odběry](./media/resource-providers-and-types/select-all-services.png)

3. Do pole **Všechny služby** zadejte **předplatné**a pak vyberte **Předplatná**.
4. Vyberte předplatné ze seznamu odběrů, které chcete zobrazit.
5. Vyberte **zprostředkovatele prostředků** a zobrazte seznam dostupných poskytovatelů prostředků.

    ![zobrazit poskytovatele prostředků](./media/resource-providers-and-types/show-resource-providers.png)

6. Registrace poskytovatele prostředků nakonfiguruje vaše předplatné tak, aby fungovalo s poskytovatelem prostředků. Obor pro registraci je vždy předplatné. Ve výchozím nastavení je mnoho poskytovatelů prostředků automaticky registrováno. Může však být nutné ručně zaregistrovat některé zprostředkovatele prostředků. Chcete-li zaregistrovat zprostředkovatele prostředků, `/register/action` musíte mít oprávnění k provádění operace pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník. Chcete-li zaregistrovat zprostředkovatele prostředků, vyberte **možnost Registrovat**. Na předchozím snímku obrazovky je zvýrazněn odkaz **Registrovat** pro **Microsoft.Blueprint**.

    Nelze zrušit registraci poskytovatele prostředků, pokud máte stále typy prostředků od tohoto poskytovatele prostředků ve vašem předplatném.

Chcete-li zobrazit informace pro konkrétního poskytovatele prostředků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V nabídce webu Azure Portal vyberte **Všechny služby**.
3. Do pole **Všechny služby** zadejte **Průzkumník prostředků**a vyberte **Průzkumník prostředků**.

    ![vybrat všechny služby](./media/resource-providers-and-types/select-resource-explorer.png)

4. Rozbalte **zprostředkovatele** výběrem šipky vpravo.

    ![Vybrat poskytovatele](./media/resource-providers-and-types/select-providers.png)

5. Rozbalte poskytovatele prostředků a typ prostředku, který chcete zobrazit.

    ![Vybrat typ zdroje](./media/resource-providers-and-types/select-resource-type.png)

6. Správce prostředků je podporován ve všech oblastech, ale prostředky, které nasazujete, nemusí být podporovány ve všech oblastech. Kromě toho může být omezení na vaše předplatné, které vám brání v použití některých oblastí, které podporují prostředek. Průzkumník prostředků zobrazí platná umístění pro typ prostředku.

    ![Zobrazit místa](./media/resource-providers-and-types/show-locations.png)

7. Verze rozhraní API odpovídá verzi operací rozhraní REST API, které jsou vydány poskytovatelem prostředků. Jako poskytovatel prostředků umožňuje nové funkce, vydává novou verzi rozhraní REST API. Průzkumník prostředků zobrazí platné verze rozhraní API pro typ prostředku.

    ![Zobrazit verze rozhraní API](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete zobrazit všechny poskytovatele prostředků v Azure a stav registrace pro vaše předplatné, použijte:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Který vrací výsledky podobné:

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Registrace poskytovatele prostředků nakonfiguruje vaše předplatné tak, aby fungovalo s poskytovatelem prostředků. Obor pro registraci je vždy předplatné. Ve výchozím nastavení je mnoho poskytovatelů prostředků automaticky registrováno. Může však být nutné ručně zaregistrovat některé zprostředkovatele prostředků. Chcete-li zaregistrovat zprostředkovatele prostředků, `/register/action` musíte mít oprávnění k provádění operace pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Který vrací výsledky podobné:

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Nelze zrušit registraci poskytovatele prostředků, pokud máte stále typy prostředků od tohoto poskytovatele prostředků ve vašem předplatném.

Chcete-li zobrazit informace pro konkrétního poskytovatele prostředků, použijte:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Který vrací výsledky podobné:

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

Verze rozhraní API odpovídá verzi operací rozhraní REST API, které jsou vydány poskytovatelem prostředků. Jako poskytovatel prostředků umožňuje nové funkce, vydává novou verzi rozhraní REST API.

Chcete-li získat dostupné verze rozhraní API pro typ prostředku, použijte:

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

Správce prostředků je podporován ve všech oblastech, ale prostředky, které nasazujete, nemusí být podporovány ve všech oblastech. Kromě toho může být omezení na vaše předplatné, které vám brání v použití některých oblastí, které podporují prostředek.

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

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Který vrací výsledky podobné:

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Registrace poskytovatele prostředků nakonfiguruje vaše předplatné tak, aby fungovalo s poskytovatelem prostředků. Obor pro registraci je vždy předplatné. Ve výchozím nastavení je mnoho poskytovatelů prostředků automaticky registrováno. Může však být nutné ručně zaregistrovat některé zprostředkovatele prostředků. Chcete-li zaregistrovat zprostředkovatele prostředků, `/register/action` musíte mít oprávnění k provádění operace pro poskytovatele prostředků. Tato operace je součástí rolí Přispěvatel a Vlastník.

```azurecli
az provider register --namespace Microsoft.Batch
```

Který vrátí zprávu, že registrace probíhá.

Nelze zrušit registraci poskytovatele prostředků, pokud máte stále typy prostředků od tohoto poskytovatele prostředků ve vašem předplatném.

Chcete-li zobrazit informace pro konkrétního poskytovatele prostředků, použijte:

```azurecli
az provider show --namespace Microsoft.Batch
```

Který vrací výsledky podobné:

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

```azurecli
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

Verze rozhraní API odpovídá verzi operací rozhraní REST API, které jsou vydány poskytovatelem prostředků. Jako poskytovatel prostředků umožňuje nové funkce, vydává novou verzi rozhraní REST API.

Chcete-li získat dostupné verze rozhraní API pro typ prostředku, použijte:

```azurecli
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

Správce prostředků je podporován ve všech oblastech, ale prostředky, které nasazujete, nemusí být podporovány ve všech oblastech. Kromě toho může být omezení na vaše předplatné, které vám brání v použití některých oblastí, které podporují prostředek.

Chcete-li získat podporovaná umístění pro typ prostředku, použijte.

```azurecli
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

* Další informace o vytváření šablon Správce prostředků najdete v [tématu Vytváření šablon Azure Resource Manageru](../templates/template-syntax.md). 
* Pokud chcete zobrazit schémata šablon zprostředkovatele prostředků, přečtěte si [odkaz na šablonu](/azure/templates/).
* Seznam, který mapuje poskytovatele prostředků na služby Azure, najdete v [tématu zprostředkovatelé prostředků pro služby Azure](azure-services-resource-providers.md).
* Informace o operacích pro zprostředkovatele prostředků najdete v [tématu Rozhraní API Azure REST](/rest/api/).
