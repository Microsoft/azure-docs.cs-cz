---
title: Odstranění historie nasazení
description: Popisuje, jak Azure Resource Manager automaticky odstranit nasazení z historie nasazení. Nasazení se odstraní, když se historie blíží k překročení limitu 800.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: fc4f7f33cdd7ccce3158aa95bd002f12c8c44c00
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951959"
---
# <a name="automatic-deletions-from-deployment-history"></a>Automatické odstraňování z historie nasazení

Pokaždé, když nasadíte šablonu, informace o nasazení se zapisují do historie nasazení. Každá skupina prostředků je v historii nasazení omezená na 800 nasazení.

Azure Resource Manager automaticky odstraní nasazení z historie, protože se blíží limitu. Automatické odstranění je změna oproti dřívějšímu chování. Dřív jste museli ručně odstranit nasazení z historie nasazení, aby nedošlo k chybě. Tato změna byla implementována 6. srpna 2020.

**Pro nasazení skupin prostředků se podporují automatické odstraňování. V současné době se nasazení v historii pro [předplatné](deploy-to-subscription.md), [skupinu pro správu](deploy-to-management-group.md)a nasazení [klientů](deploy-to-tenant.md) neodstraní automaticky.**

> [!NOTE]
> Odstranění nasazení z historie neovlivní žádné z nasazených prostředků.

## <a name="when-deployments-are-deleted"></a>Po odstranění nasazení

Nasazení se odstraní z historie, pokud překročíte 775 nasazení. Azure Resource Manager odstraní nasazení, dokud nebude historie nižší až 750. Nejstarší nasazení se vždy odstraní jako první.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Odstranění z historie nasazení":::

> [!NOTE]
> Počáteční číslo (775) a koncové číslo (750) se může změnit.
>
> Pokud vaše skupina prostředků už je 800, při dalším nasazení dojde k chybě. Proces automatického odstranění se spustí okamžitě. Můžete vyzkoušet nasazení znovu po krátkém čekání.

Kromě nasazení se také aktivují odstranění při spuštění [operace citlivosti](template-deploy-what-if.md) nebo ověření nasazení.

Když zadáte stejnému nasazení stejný název jako v historii, resetujete jeho místo v historii. Nasazení se přesune na nejnovější místo v historii. Také obnovíte místo nasazení v případě, že se vrátíte [k nasazení](rollback-on-error.md) po chybě.

## <a name="remove-locks-that-block-deletions"></a>Odebrat zámky, které blokují odstranění

Pokud máte [CanNotDelete zámek](../management/lock-resources.md) skupiny prostředků, nasazení pro tuto skupinu prostředků nejde odstranit. Abyste mohli využít automatické odstraňování v historii nasazení, musíte odebrat zámek.

Pokud chcete pomocí PowerShellu odstranit zámek, spusťte následující příkazy:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

Pokud chcete pomocí Azure CLI odstranit zámek, spusťte následující příkazy:

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="required-permissions"></a>Požadovaná oprávnění

Odstranění jsou požadována v rámci identity uživatele, který šablonu nasadil. Chcete-li odstranit nasazení, musí mít uživatel přístup k akci **Microsoft. Resources/Deployments/Delete** . Pokud uživatel nemá požadovaná oprávnění, nasazení se z historie neodstraní.

Pokud aktuální uživatel nemá požadovaná oprávnění, při příštím nasazení se znovu pokusí o automatické odstranění.

## <a name="opt-out-of-automatic-deletions"></a>Odhlásit automatické odstranění

Automatické odstranění můžete odhlásit z historie. **Tuto možnost použijte pouze v případě, že chcete historii nasazení spravovat sami.** Limit nasazení 800 v historii se pořád vynutil. Pokud překročíte 800 nasazení, obdržíte chybu a nasazení se nezdaří.

Chcete-li zakázat automatické odstranění, zaregistrujte `Microsoft.Resources/DisableDeploymentGrooming` příznak funkce. Při registraci příznaku funkce se odhlásíte k automatickým odstraněním pro celé předplatné Azure. Nemůžete odhlásit jenom konkrétní skupinu prostředků. Chcete-li znovu povolit automatické odstranění, zrušte registraci příznaku funkce.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pro prostředí PowerShell použijte rutinu [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Pokud chcete zobrazit aktuální stav předplatného, použijte:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Pokud chcete znovu povolit automatické odstranění, použijte Azure REST API nebo Azure CLI.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pro rozhraní příkazového řádku Azure CLI použijte [AZ Feature Registry](/cli/azure/feature#az-feature-register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Pokud chcete zobrazit aktuální stav předplatného, použijte:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Chcete-li znovu povolit automatické odstranění, použijte příkaz [AZ Feature Unregister](/cli/azure/feature#az-feature-unregister).

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

Pro REST API použijte [funkce – registrovat](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Pokud chcete zobrazit aktuální stav předplatného, použijte:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Chcete-li znovu povolit automatické odstranění, použijte [funkce – zrušit registraci](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Další kroky

* Další informace o zobrazení historie nasazení najdete v tématu [zobrazení historie nasazení pomocí Azure Resource Manager](deployment-history.md).
