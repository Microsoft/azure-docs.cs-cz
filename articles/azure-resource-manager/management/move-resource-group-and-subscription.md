---
title: Přesunutí prostředků do nového předplatného nebo skupiny prostředků
description: Pomocí Správce prostředků Azure přesuňte prostředky do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: 40432c55a7f7e289d2e5cbc8afe94847074e4ca8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248850"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Přesun prostředků do nové skupiny prostředků nebo předplatného

Tento článek ukazuje, jak přesunout prostředky Azure do jiného předplatného Azure nebo jiné skupiny prostředků v rámci stejného předplatného. K přesunu prostředků můžete použít Azure Portal, Azure PowerShell, Azure CLI nebo rozhraní REST API.

Zdrojová i cílová skupina jsou během operace přesunutí uzamčeny. Operace zápisu a odstranění jsou blokovány ve skupinách prostředků, dokud není přesun dokončen. Toto uzamčení znamená, že nelze přidat, aktualizovat nebo odstranit prostředky ve skupinách prostředků. To neznamená, že zdroje jsou zmrazeny. Pokud například přesunete SQL Server a jeho databázi do nové skupiny prostředků, aplikace, která používá databázi, neobsahuje žádné prostoje. Stále může číst a zapisovat do databáze. Zámek může trvat maximálně čtyři hodiny, ale většina tahů dokončena v mnohem kratším čase.

Přesunutím prostředku dojde pouze k jeho přesunu do nové skupiny prostředků nebo do nového předplatného. Umístění prostředku se nezmění.

## <a name="checklist-before-moving-resources"></a>Kontrolní seznam před přesunutím prostředků

Před přesunem prostředků je potřeba provést několik důležitých kroků. Ověřením těchto podmínek se můžete vyhnout chybám.

1. Prostředky, které chcete přesunout, musí podporovat operaci přesunutí. Seznam, které prostředky podporují přesunout, naleznete v tématu [Přesunutí podpory operace pro zdroje](move-support-resources.md).

1. Některé služby mají zvláštní omezení nebo požadavky při přesouvání prostředků. Pokud přesouváte některou z následujících služeb, zkontrolujte, že pokyny před přesunutím.

   * [Pokyny pro přesun služeb aplikací](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services přesunout pokyny](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Pokyny pro přesun modelu klasického nasazení](./move-limitations/classic-model-move-limitations.md) – klasické výpočetní prostředky, klasické úložiště, klasické virtuální sítě a cloudové služby
   * [Navádění k přesunu sítí](./move-limitations/networking-move-limitations.md)
   * [Pokyny pro přesun služby pro obnovení](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Virtuální počítače přesunout navádění](./move-limitations/virtual-machines-move-limitations.md)

1. Zdrojové a cílové odběry musí být aktivní. Pokud máte potíže s povolením účtu, který byl zakázán, [vytvořte žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Vyberte **službu Správa předplatného** pro typ problému.

1. Zdrojová a cílová předplatná musí existovat v rámci stejného [klienta služby Azure Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md). Chcete-li zkontrolovat, že obě předplatná mají stejné ID klienta, použijte Azure PowerShell nebo Azure CLI.

   Pro Azure PowerShell použijte:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Pokud používáte Azure CLI, použijte:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Pokud ID klienta pro zdrojové a cílové předplatná nejsou stejné, použijte následující metody k odsouhlasení ID klienta:

   * [Přenos vlastnictví předplatného služby Azure na jiný účet](../../billing/billing-subscription-transfer.md)
   * [Přiřazení nebo přidání předplatného Azure do služby Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Cílové předplatné musí být registrováno pro poskytovatele přesouvaného prostředku. Pokud tomu tak není, zobrazí se chyba oznamující, že **odběr není registrován pro typ prostředku**. Tato chyba se může zobrazit při přesunu prostředku do nového předplatného, ale toto předplatné nebylo nikdy použito s tímto typem prostředku.

   Pro Prostředí PowerShell použijte následující příkazy k získání stavu registrace:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Chcete-li zaregistrovat zprostředkovatele prostředků, použijte:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Pro azure cli použijte následující příkazy k získání stavu registrace:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Chcete-li zaregistrovat zprostředkovatele prostředků, použijte:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Účet, který přemisťuje prostředky, musí mít alespoň následující oprávnění:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** ve skupině zdrojových prostředků.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** na cílové skupině prostředků.

1. Před přesunutím prostředků zkontrolujte kvóty předplatného pro předplatné, do které prostředky přesouváte. Pokud přesunutí prostředků znamená, že předplatné překročí své limity, je třeba zkontrolovat, zda můžete požádat o zvýšení kvóty. Seznam omezení a způsob, jak požádat o zvýšení, najdete v [tématu Azure předplatné a omezení služeb, kvóty a omezení](../../azure-resource-manager/management/azure-subscription-service-limits.md).

1. **Pro přesun mezi předplatnými musí být prostředek a jeho závislé prostředky umístěny ve stejné skupině prostředků a musí být přesunuty společně.** Například virtuální počítače se spravovanými disky by vyžadovalo, aby virtuální počítače a spravované disky byly přesunuty společně spolu s dalšími závislými prostředky.

   Pokud přesouváte prostředek do nového předplatného, zkontrolujte, zda má prostředek nějaké závislé prostředky a zda jsou umístěny ve stejné skupině prostředků. Pokud prostředky nejsou ve stejné skupině prostředků, zkontrolujte, zda lze prostředky konsolidovat do stejné skupiny prostředků. Pokud ano, přenesete všechny tyto prostředky do stejné skupiny prostředků pomocí operace přesunutí napříč skupinami prostředků.

   Další informace naleznete v [tématu Scénář pro přesun mezi předplatnými](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Scénář pro přesun mezi předplatnými

Přesunutí prostředků z jednoho předplatného do druhého je třístupňový proces:

![scénář přesunutí mezi předplatnými](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Pro ilustrační účely máme pouze jeden závislý prostředek.

* Krok 1: Pokud jsou závislé prostředky distribuovány mezi různé skupiny prostředků, nejprve je přesuňte do jedné skupiny prostředků.
* Krok 2: Přesuňte prostředky a závislé prostředky společně ze zdrojového předplatného do cílového předplatného.
* Krok 3: Volitelně redistribuovat závislé prostředky do různých skupin prostředků v rámci cílového předplatného. 

## <a name="validate-move"></a>Ověřit přesun

[Operace přesunutí ověření](/rest/api/resources/resources/validatemoveresources) umožňuje otestovat scénář přesunutí bez skutečného přesunutí prostředků. Pomocí této operace zkontrolujte, zda bude přesun úspěšný. Ověření je automaticky voláno při odeslání požadavku na přesunutí. Tuto operaci použijte pouze v případě, že potřebujete předem určit výsledky. Chcete-li spustit tuto operaci, potřebujete:

* název zdrojové skupiny prostředků
* ID prostředku cílové skupiny prostředků
* ID prostředku každého zdroje k přesunutí
* [přístupový token](/rest/api/azure/#acquire-an-access-token) pro váš účet

Odešlete následující požadavek:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

S tělem požadavku:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Pokud je požadavek správně formátován, operace vrátí:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Stavový kód 202 označuje, že žádost o ověření byla přijata, ale ještě nebyla určena, pokud bude operace přesunutí úspěšná. Hodnota `location` obsahuje adresu URL, kterou používáte ke kontrole stavu dlouhotrvající operace.  

Chcete-li zkontrolovat stav, odešlete následující požadavek:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Zatímco operace je stále spuštěna, budete i nadále přijímat stavový kód 202. Před opakováním vyčkejte `retry-after` počet sekund uvedený v hodnotě. Pokud se operace přesunutí úspěšně ověří, obdržíte stavový kód 204. Pokud se ověření přesunutí nezdaří, zobrazí se chybová zpráva, například:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Použití portálu

Chcete-li přesunout prostředky, vyberte skupinu prostředků s těmito prostředky a pak vyberte tlačítko **Přesunout.**

![přesunutí zdrojů](./media/move-resource-group-and-subscription/select-move.png)

Vyberte, jestli přesouváte prostředky do nové skupiny prostředků nebo do nového předplatného.

Vyberte prostředky, které chcete přesunout, a cílovou skupinu prostředků. Potvrďte, že je třeba aktualizovat skripty pro tyto prostředky a vyberte **ok**. Pokud jste v předchozím kroku vybrali ikonu upravit odběr, musíte také vybrat cílové předplatné.

![vybrat cíl](./media/move-resource-group-and-subscription/select-destination.png)

V **oznámení**, uvidíte, že operace přesunutí je spuštěna.

![zobrazit stav přesunutí](./media/move-resource-group-and-subscription/show-status.png)

Po dokončení budete upozorněni na výsledek.

![zobrazit výsledek přesunutí](./media/move-resource-group-and-subscription/show-result.png)

Pokud se zobrazí chyba, [přečtěte si článek Poradce při potížích s přesunutím prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Použití Azure Powershell

Chcete-li přesunout existující prostředky do jiné skupiny prostředků nebo předplatného, použijte příkaz [Přesunout AzResource.](/powershell/module/az.resources/move-azresource) Následující příklad ukazuje, jak přesunout několik prostředků do nové skupiny prostředků.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Chcete-li přejít na nové předplatné, uveďte hodnotu parametru. `DestinationSubscriptionId`

Pokud se zobrazí chyba, [přečtěte si článek Poradce při potížích s přesunutím prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Použití Azure CLI

Chcete-li přesunout existující prostředky do jiné skupiny prostředků nebo předplatného, použijte příkaz [az resource move.](/cli/azure/resource?view=azure-cli-latest#az-resource-move) Zadejte ID prostředků zdrojů, které chcete přesunout. Následující příklad ukazuje, jak přesunout několik prostředků do nové skupiny prostředků. V `--ids` parametru zadejte seznam ID prostředků oddělených mezerami, který chcete přesunout.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Chcete-li přejít na `--destination-subscription-id` nové předplatné, zadejte parametr.

Pokud se zobrazí chyba, [přečtěte si článek Poradce při potížích s přesunutím prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="use-rest-api"></a>Použití rozhraní REST API

Chcete-li přesunout existující prostředky do jiné skupiny prostředků nebo předplatného, použijte operaci [Přesunout prostředky.](/rest/api/resources/Resources/MoveResources)

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

V těle požadavku určíte cílovou skupinu prostředků a prostředky, které chcete přesunout.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Pokud se zobrazí chyba, [přečtěte si článek Poradce při potížích s přesunutím prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Operace přesunu prostředků, která obvykle trvá několik minut, běží již téměř hodinu. Děje se něco?**

Přesunutí prostředku je komplexní operace, která má různé fáze. Může zahrnovat více než jen poskytovatele prostředků prostředku, který se pokoušíte přesunout. Z důvodu závislostí mezi poskytovateli prostředků Azure Resource Manager umožňuje 4 hodiny pro operaci k dokončení. Toto časové období dává poskytovatelům prostředků možnost zotavit se z přechodných problémů. Pokud je váš požadavek na přesunutí v období 4 hodin, operace se stále pokouší dokončit a může být stále úspěšná. Skupiny zdrojových a cílových prostředků jsou během této doby uzamčeny, aby se zabránilo problémům s konzistencí.

**Otázka: Proč je moje skupina prostředků uzamčena po dobu 4 hodin během přesunu prostředků?**

4hodinové okno je maximální doba povolená pro přesun zdroje. Chcete-li zabránit změnám zdrojů, které jsou přesouvány, jsou skupiny zdrojových i cílových zdrojů uzamčeny po dobu trvání přesunu zdroje.

V požadavku na přesunutí existují dvě fáze. V první fázi je prostředek přesunut. Ve druhé fázi jsou oznámení odesílána jiným poskytovatelům prostředků, kteří jsou závislí na zdroj přemísťují. Skupinu prostředků lze uzamknout pro celé 4hodinové okno, pokud poskytovatel prostředků selže v obou fázích. Během povoleného času správce prostředků opakuje neúspěšný krok.

Pokud prostředek nelze přesunout v rámci 4hodinového okna, Správce prostředků odemkne obě skupiny prostředků. Prostředky, které byly úspěšně přesunuty, jsou ve skupině cílových prostředků. Prostředky, které se nepodařilo přesunout, jsou ponechány ve skupině zdrojových prostředků.

**Otázka: Jaké jsou důsledky uzamčení zdrojových a cílových skupin prostředků během přesunu prostředku?**

Zámek zabraňuje odstranění skupiny prostředků, vytvoření nového prostředku ve skupině prostředků nebo odstranění všech prostředků zapojených do přesunu.

Následující obrázek ukazuje chybovou zprávu z portálu Azure, když se uživatel pokusí odstranit skupinu prostředků, která je součástí probíhajícího přesunu.

![Přesunutí chybové zprávy při pokusu o odstranění](./media/move-resource-group-and-subscription/move-error-delete.png)

**Otázka: Co znamená kód chyby "MissingMoveDependentResources"?**

Při přesouvání prostředku musí jeho závislé prostředky existovat buď v cílové skupině prostředků nebo předplatném, nebo musí být zahrnuty do požadavku na přesunutí. Chybový kód MissingMoveDependentResources získáte, když závislý prostředek tento požadavek nesplňuje. Chybová zpráva obsahuje podrobnosti o závislém prostředku, který musí být zahrnut do požadavku na přesunutí.

Například přesunutí virtuálního počítače může vyžadovat přesunutí sedmi typů prostředků se třemi různými poskytovateli prostředků. Tito poskytovatelé a typy prostředků jsou:

* Microsoft.Compute
   * virtualMachines
   * Disky
* Microsoft.Network
  * síťová rozhraní
  * publicIPAdresy
  * síťSecuritygroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

Dalším běžným příkladem je přesunutí virtuální sítě. Bude pravděpodobně muset přesunout několik dalších prostředků přidružených k této virtuální síti. Požadavek na přesunutí může vyžadovat přesunutí veřejných IP adres, směrovacích tabulek, bran virtuální sítě, skupin zabezpečení sítě a dalších.

**Otázka: Proč nemůžu přesunout některé prostředky v Azure?**

V současné době se ne všechny prostředky v podpoře Azure přesunou. Seznam zdrojů, které podporují přesunutí, naleznete v tématu [Přesunutí podpory operace pro prostředky](move-support-resources.md).

## <a name="next-steps"></a>Další kroky

Seznam, které prostředky podporují přesunout, naleznete v tématu [Přesunutí podpory operace pro zdroje](move-support-resources.md).
