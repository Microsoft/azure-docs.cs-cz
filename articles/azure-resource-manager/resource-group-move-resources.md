---
title: Přesunutí prostředků do nového předplatného nebo skupiny prostředků
description: Použití Azure Resource Manageru k přesunutí prostředků do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: f106de7fd35bdbe91033af173b1f338dd251f4e8
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149691"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Přesunutí prostředků do nové skupiny prostředků nebo předplatného

V tomto článku se dozvíte, jak přesunout prostředky Azure do jiného předplatného Azure nebo do jiné skupiny prostředků v rámci stejného předplatného. K přesunu prostředků můžete použít Azure Portal, Azure PowerShell, Azure CLI nebo rozhraní REST API.

Během operace přesunutí dojde ke zamčení zdrojové skupiny i cílové skupiny. Zápis a odstranění operace jsou blokovány o skupinách prostředků, až do dokončení přechodu. Tento zámek znamená, že nemůžete přidat, aktualizovat nebo odstranit prostředky ve skupinách prostředků. Neznamená to, že se prostředky zmrazují. Například při přesunutí serveru SQL Server a jeho databázi do nové skupiny prostředků, aplikace, která používá databázi prostředí bez výpadků. Můžete nadále číst a zapisovat do databáze. Zámek může být poslední po dobu maximálně čtyř hodin, ale většina přesunů se dokončí za mnohem kratší dobu.

Přesunutím prostředku dojde pouze k jeho přesunu do nové skupiny prostředků nebo do nového předplatného. Umístění prostředku se nezmění.

## <a name="checklist-before-moving-resources"></a>Kontrolní seznam před přesunutím prostředků

Před přesunutím prostředku je třeba provést několik důležitých kroků. Ověřením těchto podmínek se můžete vyhnout chybám.

1. Prostředky, které chcete přesunout, musí podporovat operaci přesunu. Seznam prostředků, které podporují přesun, najdete v tématu [Podpora operací přesunutí pro prostředky](move-support-resources.md).

1. Některé služby mají při přesouvání prostředků specifická omezení nebo požadavky. Pokud přesouváte některou z následujících služeb, před přesunem si Projděte tyto pokyny.

   * [Pokyny pro přesunutí App Services](./move-limitations/app-service-move-limitations.md)
   * [Pokyny pro přesunutí Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Model nasazení Classic – pokyny pro přesun](./move-limitations/classic-model-move-limitations.md) – klasický výpočetní prostředí, klasické úložiště, klasické virtuální sítě a Cloud Services
   * [Pokyny k přesunu sítě](./move-limitations/networking-move-limitations.md)
   * [Pokyny pro přesunutí Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Pokyny pro přesunutí Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)

1. Zdrojové a cílové odběry musí být aktivní. Pokud máte potíže s povolením zakázaného účtu, [vytvořte žádost o podporu Azure](../azure-supportability/how-to-create-azure-support-request.md). Vyberte **správy předplatných** typ problému.

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

1. Cílové předplatné musí být registrováno pro poskytovatele přesouvaného prostředku. Pokud ne, zobrazí chybová zpráva oznamující, že **předplatné není zaregistrované pro typ prostředku**. Tato chyba se může zobrazit při přesunu prostředku do nového předplatného, ale toto předplatné se pro tento typ prostředku nikdy nepoužilo.

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

1. **Pro přesun mezi předplatnými musí být prostředek a jeho závislé prostředky umístěny ve stejné skupině prostředků a musí být přesunuty dohromady.** Například virtuální počítač se spravovanými disky by vyžadoval, aby se virtuální počítač a spravované disky přesunuly společně s dalšími závislými prostředky.

   Pokud přesouváte prostředek do nového předplatného, zkontrolujte, zda prostředek obsahuje nějaké závislé prostředky a zda se nachází ve stejné skupině prostředků. Pokud prostředky nejsou ve stejné skupině prostředků, zkontrolujte, jestli se prostředky dají konsolidovat do stejné skupiny prostředků. Pokud ano, převeďte všechny tyto prostředky do stejné skupiny prostředků pomocí operace přesunutí napříč skupinami prostředků.

   Další informace najdete v tématu [scénář pro přesun mezi předplatnými](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Scénář pro přesun mezi předplatnými

Přesunutí prostředků z jednoho předplatného na jiný je proces se třemi kroky:

![scénář přesunutí mezi předplatnými](./media/resource-group-move-resources/cross-subscription-move-scenario.png)

Pro ilustraci máme jenom jeden závislý prostředek.

* Krok 1: Pokud jsou závislé prostředky distribuované napříč různými skupinami prostředků, přesuňte je nejdřív do jedné skupiny prostředků.
* Krok 2: přesunutí prostředku a závislých prostředků ze zdrojového předplatného do cílového předplatného.
* Krok 3: Volitelně můžete závislé prostředky znovu distribuovat do různých skupin prostředků v rámci cílového předplatného. 

## <a name="validate-move"></a>Ověří přesun

[Ověření operace přesunu](/rest/api/resources/resources/validatemoveresources) umožňuje testovat váš scénář přesunu bez skutečně přesouvání prostředků. Pomocí této operace zkontrolujete, zda se přesun nezdaří. Ověřování je automaticky voláno při odeslání žádosti o přesunutí. Tuto operaci použijte pouze v případě, že potřebujete předem určit výsledky. K provedení této operace, je nutné:

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

Chcete-li přesunout prostředky, vyberte skupinu prostředků s těmito prostředky a pak vyberte tlačítko **přesunout** .

![přesunutí prostředků](./media/resource-group-move-resources/select-move.png)

Vyberte, jestli přesouváte prostředky do nové skupiny prostředků nebo si nové předplatné.

Vyberte prostředky k přesunutí a cílová skupina prostředků. Potvrďte, že je potřeba aktualizovat skripty pro tyto prostředky a vyberte **OK**. Pokud vyberete ikonu pro úpravy odběru v předchozím kroku, musíte také vybrat cílové předplatné.

![Vyberte cíl](./media/resource-group-move-resources/select-destination.png)

V **oznámení**, uvidíte, že je spuštěná operace přesunutí.

![Zobrazit stav přesunu](./media/resource-group-move-resources/show-status.png)

Po jeho dokončení budete informováni o výsledek.

![Zobrazit výsledek přesunutí](./media/resource-group-move-resources/show-result.png)

Pokud se zobrazí chyba, přečtěte si téma [řešení potíží s přesunutím prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Použití Azure Powershell

Pokud chcete přesunout existující prostředky do jiné skupiny prostředků nebo předplatného, použijte příkaz [Move-AzResource](/powershell/module/az.resources/move-azresource) . Následující příklad ukazuje, jak přesunout několik prostředků do nové skupiny prostředků.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Chcete-li přesunout do nového předplatného, zahrnout hodnotu pro `DestinationSubscriptionId` parametru.

Pokud se zobrazí chyba, přečtěte si téma [řešení potíží s přesunutím prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Použití Azure CLI

Chcete-li stávající prostředky přesunout do jiné skupiny prostředků nebo předplatného, použijte [přesunutí prostředku az](/cli/azure/resource?view=azure-cli-latest#az-resource-move) příkaz. Zadejte ID prostředků pro přesun prostředků. Následující příklad ukazuje, jak přesunout několik prostředků do nové skupiny prostředků. V `--ids` parametr, zadejte místo oddělený seznam ID pro přesun prostředků.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Pokud chcete přesunout do nového předplatného, zadejte `--destination-subscription-id` parametru.

Pokud se zobrazí chyba, přečtěte si téma [řešení potíží s přesunutím prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="use-rest-api"></a>Použití rozhraní REST API

Pokud chcete přesunout existující prostředky do jiné skupiny prostředků nebo předplatného, použijte operaci [přesunout prostředky](/rest/api/resources/Resources/MoveResources) .

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

Pokud se zobrazí chyba, přečtěte si téma [řešení potíží s přesunutím prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: moje operace přesunutí prostředků, která obvykle trvá několik minut, je spuštěná skoro po celou hodinu. Je něco špatné?**

Přesunutí prostředku je složitá operace, která má různé fáze. Může zahrnovat víc než jenom poskytovatele prostředků u prostředku, který se pokoušíte přesunout. Vzhledem k závislostem mezi poskytovateli prostředků Azure Resource Manager umožňuje dokončení operace 4 hodiny. Toto časové období dává poskytovatelům prostředků možnost obnovení z přechodných problémů. Pokud je vaše žádost o přesun v průběhu 4 hodiny, operace se bude pokoušet dokončit a může být stále úspěšná. Zdrojové a cílové skupiny prostředků jsou během této doby uzamčeny, aby nedocházelo k problémům s konzistencí.

**Otázka: Proč se skupina prostředků uzamkl po dobu 4 hodin během přesunu prostředků?**

Okno o velikosti 4 hodiny je maximální doba, po kterou je možné přesunout prostředek. Aby se zabránilo změnám přesouvaných prostředků, jsou zdrojové i cílové skupiny prostředků zamčené po dobu trvání přesunutí prostředku.

V žádosti o přesunutí jsou dvě fáze. V první fázi se prostředek přesune. Ve druhé fázi se oznámení odesílají jiným poskytovatelům prostředků závislým na přemístění prostředku. Skupina prostředků může být uzamčena pro celé 4 hodiny, když poskytovatel prostředků dojde v obou fázích k chybě. Během povoleného času se Správce prostředků opakuje neúspěšný krok.

Pokud prostředek nejde přesunout v rámci 4 hodinového okna, Správce prostředků odemkne obě skupiny prostředků. Prostředky, které se úspěšně přesunuly, se nacházejí v cílové skupině prostředků. Prostředky, které se nepodařilo přesunout, jsou ponechány ve zdrojové skupině prostředků.

**Otázka: Jaké jsou důsledky uzamčení zdrojových a cílových skupin prostředků během přesunu prostředku?**

Zámek vám znemožní odstranit buď skupinu prostředků, vytvoření nového prostředku v obou skupinách prostředků, nebo odstranit některý z prostředků, které jsou součástí přesunutí.

Následující obrázek ukazuje chybovou zprávu z Azure Portal, když se uživatel pokusí odstranit skupinu prostředků, která je součástí probíhajícího přesunu.

![Přesunout chybovou zprávu pokus o odstranění](./media/resource-group-move-resources/move-error-delete.png)

**Otázka: co znamená kód chyby "MissingMoveDependentResources"?**

Při přesunu prostředku musí být závislé prostředky buď v cílové skupině prostředků nebo předplatném, nebo být součástí žádosti o přesun. Kód chyby MissingMoveDependentResources se zobrazí, když závislý prostředek nesplňuje tento požadavek. Chybová zpráva obsahuje podrobnosti o závislém prostředku, který musí být zahrnut v žádosti o přesunutí.

Například přesun virtuálního počítače může vyžadovat přesun sedmi typů prostředků se třemi různými poskytovateli prostředků. Poskytovatelé prostředků a typy jsou:

* Microsoft.Compute
   * virtualMachines
   * disks
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

Další běžný příklad zahrnuje přesun virtuální sítě. Možná budete muset přesunout několik dalších prostředků přidružených k této virtuální síti. Žádost o přesun by mohla vyžadovat přesunutí veřejných IP adres, směrovacích tabulek, bran virtuální sítě, skupin zabezpečení sítě a dalších.

**Otázka: Proč nemůžu přesunout některé prostředky v Azure?**

V současné době se nepodporují přesun všech prostředků v Azure. Seznam prostředků, které podporují přesun, najdete v tématu [o podpoře operací přesunutí pro prostředky](move-support-resources.md).

## <a name="next-steps"></a>Další kroky

Seznam prostředků, které podporují přesun, najdete v tématu [Podpora operací přesunutí pro prostředky](move-support-resources.md).
