---
title: Prostředky Azure přejděte nové předplatné nebo skupinu prostředků | Dokumentace Microsoftu
description: Použití Azure Resource Manageru k přesunutí prostředků do nové skupiny prostředků nebo předplatného.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 01ec8facf2771de9ec01b9470521340a59ee4d0d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721384"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Přesunutí prostředků do nové skupiny prostředků nebo předplatného

Tento článek ukazuje, jak Azure prostředky přesunout do jiného předplatného Azure nebo jiné skupiny prostředků ve stejném předplatném. Webu Azure portal, prostředí Azure PowerShell, rozhraní příkazového řádku Azure nebo rozhraní REST API můžete použít k přesunutí prostředků.

Během operace přesunu jsou zamknuté skupiny zdrojové i cílové skupině. Zápis a odstranění operace jsou blokovány o skupinách prostředků, až do dokončení přechodu. Tento Zámek znamená, že nelze přidat, aktualizovat nebo odstranit prostředky ve skupinách prostředků, ale neznamená, že prostředky jsou zmražená. Například při přesunutí serveru SQL Server a jeho databázi do nové skupiny prostředků, aplikace, která používá databázi prostředí bez výpadků. Můžete nadále číst a zapisovat do databáze.

Přesunutí prostředku pouze přesune do nové skupiny prostředků nebo předplatného. Umístění prostředku nezmění.

## <a name="checklist-before-moving-resources"></a>Kontrolní seznam před přesunutím prostředků

Existuje několik důležitých kroků provedete před přesunutím prostředku je. Ověřením těchto podmínek se můžete vyhnout chybám.

1. Prostředky, které chcete přesunout musí podporovat operaci přesunutí. Seznam, z nichž prostředků podporují přesunutí najdete v tématu [přesunout operace Podpora prostředků](move-support-resources.md).

1. Některé služby mají určitá omezení nebo požadavky při přesouvání prostředků. Pokud jste přesunete z následujících služeb, zkontrolujte tyto pokyny před přesunutím.

   * [Pokyny k přesunutí App Services](./move-limitations/app-service-move-limitations.md)
   * [Pokyny k přesunutí služeb Azure DevOps](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Pokyny k přesunutí modelu nasazení Classic](./move-limitations/classic-model-move-limitations.md) – klasické výpočetní klasického úložiště, klasické virtuální sítě a cloudové služby
   * [Pokyny k přesunutí služby Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Pokyny k přesunutí virtuálních počítačů](./move-limitations/virtual-machines-move-limitations.md)
   * [Pokyny k přesunutí virtuálních sítí](./move-limitations/virtual-network-move-limitations.md)

1. Zdrojové a cílové předplatné musí být aktivní. Pokud máte potíže s povolením účtu, který byl zakázán, [vytvoření žádosti o podporu Azure](../azure-supportability/how-to-create-azure-support-request.md). Vyberte **správy předplatných** typ problému.

1. Zdrojové a cílové předplatné musí existovat v rámci stejného [tenanta Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md). Pokud chcete zkontrolovat, že oba odběry obsahují stejné ID tenanta, pomocí Azure Powershellu nebo rozhraní příkazového řádku Azure.

   Pro prostředí Azure PowerShell použijte:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Pokud používáte Azure CLI, použijte:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Pokud ID tenantů pro zdrojové a cílové předplatné nejsou stejné, použijte následující metody k odsouhlasení ID tenantů:

   * [Přenos vlastnictví předplatného služby Azure na jiný účet](../billing/billing-subscription-transfer.md)
   * [Přiřazení nebo přidání předplatného Azure do služby Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Cílové předplatné musí být registrováno pro poskytovatele přesouvaného prostředku. Pokud ne, zobrazí chybová zpráva oznamující, že **předplatné není zaregistrované pro typ prostředku**. Tato chyba může zobrazit při přesunutí prostředku do nového předplatného, ale, že předplatné nikdy použito příslušný typ prostředku.

   Pokud používáte PowerShell, použijte následující příkazy získat stav registrace:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Zaregistrovat poskytovatele prostředků, použijte:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Azure CLI použijte následující příkazy se získat stav registrace:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Zaregistrovat poskytovatele prostředků, použijte:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Účet přesunutí prostředků musí mít alespoň následující oprávnění:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** na zdrojovou skupinu prostředků.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** na cílová skupina prostředků.

1. Před přesunutím prostředků, zkontrolujte kvóty předplatného pro předplatné, které přesouváte prostředky, které. Přesunutí prostředků znamená, že předplatné překročí maximum, budete muset zkontrolovat, jestli můžete požádat o zvýšení kvóty. Seznam omezení a tom, jak požádat o zvýšení najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).

## <a name="validate-move"></a>Ověří přesun

[Ověření operace přesunu](/rest/api/resources/resources/validatemoveresources) umožňuje testovat váš scénář přesunu bez skutečně přesouvání prostředků. Pomocí této operace můžete zkontrolovat, pokud bude úspěšné přesunutí. Ověření se automaticky volá, když odešlete žádost o přesunutí. Pomocí této operace jenom v případě, že je nutné určit výsledky. K provedení této operace, je nutné:

* Název zdrojové skupiny prostředků
* ID prostředku cílová skupina prostředků
* ID prostředku každého prostředku přesunout
* [přístupový token](/rest/api/azure/#acquire-an-access-token) pro váš účet

Odeslání žádosti o následující:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

S hlavní část žádosti:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Pokud je požadavek správně naformátován, operace vrátí:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 stavový kód označuje byla přijata žádost o ověření, ale ještě nebyl určen, pokud bude úspěšné operaci přesunutí. `location` Hodnota obsahuje adresu URL, kterou lze použít ke kontrole stavu dlouhotrvající operace.  

Pokud chcete zkontrolovat stav, odešlete požadavek na následující:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Během operace je stále spuštěna, můžete nadále přijímat 202 stavový kód. Počkejte, počet sekund, které jsou uvedené v `retry-after` hodnotu než to zkusíte znovu. Pokud operace přesunu ověří úspěšně, obdržíte 204 stavový kód. Pokud selže ověření přesunu, obdržíte chybovou zprávu, jako například:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Použití portálu

Přesunout prostředky, vyberte skupinu prostředků pomocí těchto prostředků a pak vyberte **přesunout** tlačítko.

![přesunutí prostředků](./media/resource-group-move-resources/select-move.png)

Vyberte, jestli přesouváte prostředky do nové skupiny prostředků nebo si nové předplatné.

Vyberte prostředky k přesunutí a cílová skupina prostředků. Potvrďte, že je potřeba aktualizovat skripty pro tyto prostředky a vyberte **OK**. Pokud vyberete ikonu pro úpravy odběru v předchozím kroku, musíte také vybrat cílové předplatné.

![Vyberte cíl](./media/resource-group-move-resources/select-destination.png)

V **oznámení**, uvidíte, že je spuštěná operace přesunutí.

![Zobrazit stav přesunu](./media/resource-group-move-resources/show-status.png)

Po jeho dokončení budete informováni o výsledek.

![Zobrazit výsledek přesunutí](./media/resource-group-move-resources/show-result.png)

Pokud dojde k chybě, přečtěte si téma [Poradce při potížích přesun prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Použití Azure Powershell

K přesunutí stávajících prostředků do jiné skupiny prostředků nebo předplatného, použijte [přesunout AzResource](/powershell/module/az.resources/move-azresource) příkazu. Následující příklad ukazuje, jak několika prostředky přesunout do nové skupiny prostředků.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Chcete-li přesunout do nového předplatného, zahrnout hodnotu pro `DestinationSubscriptionId` parametru.

Pokud dojde k chybě, přečtěte si téma [Poradce při potížích přesun prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Použití Azure CLI

Chcete-li stávající prostředky přesunout do jiné skupiny prostředků nebo předplatného, použijte [přesunutí prostředku az](/cli/azure/resource?view=azure-cli-latest#az-resource-move) příkaz. Zadejte ID prostředků pro přesun prostředků. Následující příklad ukazuje, jak několika prostředky přesunout do nové skupiny prostředků. V `--ids` parametr, zadejte místo oddělený seznam ID pro přesun prostředků.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Pokud chcete přesunout do nového předplatného, zadejte `--destination-subscription-id` parametru.

Pokud dojde k chybě, přečtěte si téma [Poradce při potížích přesun prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="use-rest-api"></a>Použití rozhraní REST API

Chcete-li stávající prostředky přesunout do jiné skupiny prostředků nebo předplatného, použijte [přesunutí prostředků](/rest/api/resources/Resources/MoveResources) operace.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

V textu požadavku zadejte cílovou skupinu prostředků a prostředky, které chcete přesunout.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Pokud dojde k chybě, přečtěte si téma [Poradce při potížích přesun prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="next-steps"></a>Další postup

Seznam, z nichž prostředků podporují přesunutí najdete v tématu [přesunout operace Podpora prostředků](move-support-resources.md).
