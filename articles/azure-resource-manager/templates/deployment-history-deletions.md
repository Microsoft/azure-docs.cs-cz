---
title: Odstranění historie nasazení
description: Popisuje, jak Azure Resource Manager automaticky odstranit nasazení z historie nasazení. Nasazení se odstraní, když se historie blíží k překročení limitu 800.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: c16b71646e20b71c0d0ca8c9f8e028773983022f
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84673985"
---
# <a name="automatic-deletions-from-deployment-history"></a>Automatické odstraňování z historie nasazení

Pokaždé, když nasadíte šablonu, informace o nasazení se zapisují do historie nasazení. Každá skupina prostředků je v historii nasazení omezená na 800 nasazení.

Od června 2020 Azure Resource Manager automaticky odstraní nasazení z historie, protože se blížíte limitu. Automatické odstranění je změna oproti dřívějšímu chování. Dřív jste museli ručně odstranit nasazení z historie nasazení, aby nedošlo k chybě.

> [!NOTE]
> Odstranění nasazení z historie neovlivní žádné z nasazených prostředků.
>
> Pokud máte [CanNotDelete zámek](../management/lock-resources.md) skupiny prostředků, nasazení pro tuto skupinu prostředků nejde odstranit. Abyste mohli využít automatické odstraňování v historii nasazení, musíte odebrat zámek.

## <a name="when-deployments-are-deleted"></a>Po odstranění nasazení

Nasazení se při dosažení 790 nasazení odstraní z historie nasazení. Azure Resource Manager odstraní malou sadu nejstarší nasazení, aby se místo pro budoucí nasazení vymazalo místo. Většina vaší historie zůstane beze změny. Nejstarší nasazení se vždy odstraní jako první.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Odstranění z historie nasazení":::

Kromě nasazení se také aktivují odstranění při spuštění [operace citlivosti](template-deploy-what-if.md) nebo ověření nasazení.

Když zadáte stejnému nasazení stejný název jako v historii, resetujete jeho místo v historii. Nasazení se přesune na nejnovější místo v historii. Také obnovíte místo nasazení v případě, že se vrátíte [k nasazení](rollback-on-error.md) po chybě.

> [!NOTE]
> Pokud vaše skupina prostředků už je 800, při dalším nasazení dojde k chybě. Proces automatického odstranění se spustí okamžitě. Můžete vyzkoušet nasazení znovu po krátkém čekání.

## <a name="opt-out-of-automatic-deletions"></a>Odhlásit automatické odstranění

Automatické odstranění můžete odhlásit z historie. **Tuto možnost použijte pouze v případě, že chcete historii nasazení spravovat sami.** Limit nasazení 800 v historii se pořád vynutil. Pokud překročíte 800 nasazení, obdržíte chybu a nasazení se nezdaří.

Chcete-li zakázat automatické odstranění, zaregistrujte `Microsoft.Resources/DisableDeploymentGrooming` příznak funkce. Při registraci příznaku funkce se odhlásíte k automatickým odstraněním pro celé předplatné Azure. Nemůžete odhlásit jenom konkrétní skupinu prostředků.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pro prostředí PowerShell použijte rutinu [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Pokud chcete zobrazit aktuální stav předplatného, použijte:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pro rozhraní příkazového řádku Azure CLI použijte [AZ Feature Registry](/cli/azure/feature#az-feature-register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Pokud chcete zobrazit aktuální stav předplatného, použijte:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
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

---

## <a name="next-steps"></a>Další kroky

* Další informace o zobrazení historie nasazení najdete v tématu [zobrazení historie nasazení pomocí Azure Resource Manager](deployment-history.md).
