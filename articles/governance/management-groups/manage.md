---
title: Jak pracovat se skupinami pro správu – Zásadsprávné řízení Azure
description: Přečtěte si, jak zobrazit, udržovat, aktualizovat a odstranit hierarchii skupin pro správu.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: 423d1837c3d5710e24abb94f5411200319e8a8aa
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381667"
---
# <a name="manage-your-resources-with-management-groups"></a>Správa zdrojů pomocí skupin pro správu

Pokud má vaše organizace mnoho předplatných, možná budete potřebovat způsob, jak efektivně spravovat přístup, zásady a dodržování předpisů pro tato předplatná. Skupiny pro správu Azure představují úroveň rozsahu nad předplatnými. Předplatná uspořádáte do kontejnerů označovaných jako skupiny pro správu a na tyto skupiny pro správu použijete své zásady správného řízení. Všechna předplatná v rámci skupiny pro správu automaticky dědí podmínky, které se na příslušnou skupinu pro správu vztahují.

Skupiny pro správu poskytují správu na podnikové úrovni ve velkém měřítku bez ohledu na to, jaké typy předplatného případně máte. Další informace o skupinách pro správu najdete [v tématu Uspořádání prostředků pomocí skupin pro správu Azure](./overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

> [!IMPORTANT]
> Tokeny uživatelů Azure Resource Manager a mezipaměti skupiny pro správu trvá 30 minut, než budou vynucené k aktualizaci. Po provedení jakékoli akce, jako je přesunutí skupiny pro správu nebo předplatného, může trvat až 30 minut zobrazení. Chcete-li zobrazit aktualizace dříve, musíte aktualizovat token aktualizací prohlížeče, přihlášením a odhlášením nebo vyžádáním nového tokenu.  

## <a name="change-the-name-of-a-management-group"></a>Změna názvu skupiny pro správu

Název skupiny pro správu můžete změnit pomocí portálu, Prostředí PowerShell nebo azure CLI.

### <a name="change-the-name-in-the-portal"></a>Změna názvu na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny skupiny správy služeb** > **Management groups**.

1. Vyberte skupinu pro správu, kterou chcete přejmenovat.

1. Vyberte **podrobnosti**.

1. V horní části stránky vyberte možnost **Přejmenovat skupinu.**

   :::image type="content" source="./media/detail_action_small.png" alt-text="Přejmenovat možnost Skupiny na stránce skupiny pro správu" border="false":::

1. Po otevření nabídky zadejte nový název, který chcete zobrazit.

   :::image type="content" source="./media/rename_context.png" alt-text="Přejmenování podokna skupiny na přejmenování skupiny pro správu" border="false":::

1. Vyberte **Uložit**.

### <a name="change-the-name-in-powershell"></a>Změna názvu v PowerShellu

Chcete-li aktualizovat zobrazovaný název, použijte **update-AzManagementGroup**. Chcete-li například změnit zobrazovaný název skupin pro správu z "Contoso IT" na "Contoso Group", spusťte následující příkaz:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Změna názvu v azure cli

Pro azure cli použijte příkaz aktualizace.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Odstranění skupiny pro správu

Chcete-li odstranit skupinu pro správu, musí být splněny následující požadavky:

1. V rámci skupiny pro správu nejsou žádné podřízené skupiny pro správu nebo předplatná.

   - Pokud chcete přesunout předplatné nebo skupinu pro správu do jiné skupiny pro správu, [přečtěte si obrázek Přesunutí skupin pro správu a předplatných v hierarchii.](#moving-management-groups-and-subscriptions)

1. Potřebujete oprávnění k zápisu do skupiny pro správu ("Vlastník", "Přispěvatel" nebo "Přispěvatel skupiny pro správu"). Chcete-li zjistit, jaká oprávnění máte, vyberte skupinu pro správu a pak vyberte **IAM**. Další informace o rolích RBAC najdete v tématu  
   [Správa přístupu a oprávnění pomocí RBAC](../../role-based-access-control/overview.md).

### <a name="delete-in-the-portal"></a>Odstranit na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny skupiny správy služeb** > **Management groups**.

1. Vyberte skupinu pro správu, kterou chcete odstranit.

1. Vyberte **podrobnosti**.

1. Vybrat **Odstranit**

   :::image type="content" source="./media/delete.png" alt-text="Možnost Odstranit skupinu" border="false":::

   > [!TIP]
   > Pokud je ikona zakázaná, zobrazí se u ní ukazatel myši, který na ni najede myší.

1. Otevře se okno s potvrzením, že chcete odstranit skupinu pro správu.

   :::image type="content" source="./media/delete_confirm.png" alt-text="Okno potvrzení odstranění skupiny" border="false":::

1. Vyberte **ano**.

### <a name="delete-in-powershell"></a>Odstranit v PowerShellu

K odstranění skupin pro správu použijte příkaz **Remove-AzManagementGroup** v prostředí PowerShell.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Odstranění v Azure CLI

Pomocí příkazového příkazu Azure CLI použijte příkaz az odstranění skupiny účtů.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Zobrazit skupiny pro správu

Můžete zobrazit libovolnou skupinu pro správu, ve které máte přímou nebo zděděnou roli RBAC.  

### <a name="view-in-the-portal"></a>Zobrazit na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny skupiny správy služeb** > **Management groups**.

1. Načte se stránka hierarchie skupiny pro správu. Na této stránce můžete prozkoumat všechny skupiny pro správu a předplatná, ke které máte přístup. Výběrnázvu skupiny vás zavede o úroveň nižší než v hierarchii. Navigace funguje stejně jako průzkumník souborů.

1. Chcete-li zobrazit podrobnosti o skupině pro správu, vyberte odkaz **(podrobnosti)** vedle názvu skupiny pro správu. Pokud tento odkaz není k dispozici, nemáte oprávnění k zobrazení této skupiny pro správu.

   :::image type="content" source="./media/main.png" alt-text="Hlavní" border="false":::

### <a name="view-in-powershell"></a>Zobrazení v PowerShellu

Příkaz Get-AzManagementGroup slouží k načtení všech skupin. Úplný seznam příkazů get powershellu skupiny pro správu najdete v modulech [Az.Resources.](/powershell/module/az.resources/Get-AzManagementGroup)  

```azurepowershell-interactive
Get-AzManagementGroup
```

Pro informace jedné skupiny pro správu použijte parametr -GroupName.

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Chcete-li vrátit konkrétní skupinu pro správu a všechny úrovně hierarchie pod ní, použijte parametry **-Expand** a **-Recurse.**  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Zobrazení v azure cli

Příkaz seznamu se používá k načtení všech skupin.  

```azurecli-interactive
az account management-group list
```

Informace o jedné skupině pro správu získáte pomocí příkazu show

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Chcete-li vrátit konkrétní skupinu pro správu a všechny úrovně hierarchie pod ní, použijte parametry **-Expand** a **-Recurse.**

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>Přesouvání skupin pro správu a předplatných   

Jedním z důvodů, proč vytvořit skupinu pro správu, je seslovky předplatných. Pouze skupiny pro správu a předplatná mohou být podřízené z jiné skupiny pro správu. Předplatné, které se přesune do skupiny pro správu, zdědí veškerý přístup uživatelů a zásady z nadřazené skupiny pro správu.

Při přesouvání skupiny pro správu nebo předplatného za podřízenou podřízenou jiné skupině pro správu je třeba vyhodnotit tři pravidla jako pravdivá.

Pokud akci přesunu provádíte, potřebujete: 

- Oprávnění k zápisu skupiny pro správu a přiřazení rolí pro podřízené předplatné nebo skupinu pro správu.
  - Předdefinovaný příklad role **Vlastník**
- Přístup pro zápis skupiny managementu v cílové nadřazené skupině pro správu.
  - Příklad předdefinované role: **Vlastník**, **Přispěvatel**, **Přispěvatel skupiny pro správu**
- Přístup pro zápis skupiny pro správu v existující nadřazené skupině pro správu.
  - Příklad předdefinované role: **Vlastník**, **Přispěvatel**, **Přispěvatel skupiny pro správu**

**Výjimka**: Pokud je cílovou nebo existující nadřazenou skupinou pro správu kořenová skupina pro správu, požadavky na oprávnění se nevztahují. Vzhledem k tomu, že skupina pro správu kořenového adresáře je výchozím místem pro místo přistání pro všechny nové skupiny pro správu a předplatná, nepotřebujete k přesunutí položky oprávnění.

Pokud je role vlastníka v předplatném zděděna z aktuální skupiny pro správu, jsou cíle přesunu omezené. Předplatné můžete přesunout pouze do jiné skupiny pro správu, kde máte roli vlastníka. Nemůžete ji přesunout do skupiny pro správu, kde jste přispěvatelem, protože byste ztratili vlastnictví předplatného. Pokud jste přímo přiřazeni k roli vlastníka předplatného (nezděděné ze skupiny pro správu), můžete ji přesunout do libovolné skupiny pro správu, kde jste přispěvatelem.

Pokud chcete zjistit, jaká oprávnění máte na webu Azure Portal, vyberte skupinu pro správu a pak vyberte **IAM**. Další informace o rolích RBAC naleznete v [tématu Správa přístupu a oprávnění pomocí rbac](../../role-based-access-control/overview.md).

## <a name="move-subscriptions"></a>Přesun předplatných 

### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>Přidání existujícího předplatného do skupiny pro správu na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny skupiny správy služeb** > **Management groups**.

1. Vyberte skupinu pro správu, kterou plánujete jako nadřazenou.

1. V horní části stránky vyberte **Přidat předplatné**.

1. Vyberte předplatné v seznamu se správným ID.

   :::image type="content" source="./media/add_context_sub.png" alt-text="Dostupná předplatná, která chcete přidat do skupiny pro správu" border="false":::

1. Vyberte možnost "Uložit".

### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>Odebrání předplatného ze skupiny pro správu na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny skupiny správy služeb** > **Management groups**.

1. Vyberte skupinu pro správu, kterou plánujete, která je aktuální nadřazenou položkou.  

1. Vyberte elipsu na konci řádku pro odběr v seznamu, který chcete přesunout.

   :::image type="content" source="./media/move_small.png" alt-text="Možnost přesunutí ve skupině pro správu" border="false":::

1. Vyberte **Přesunout**.

1. V nabídce, která se otevře, vyberte **nadřazenou skupinu pro správu**.

   :::image type="content" source="./media/move_small_context.png" alt-text="Přesunout podokno pro změnu nadřazené skupiny" border="false":::

1. Vyberte **Uložit**.

### <a name="move-subscriptions-in-powershell"></a>Přesunutí předplatných v PowerShellu

Chcete-li přesunout předplatné v prostředí PowerShell, použijte příkaz New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Chcete-li odebrat propojení mezi a odběr a skupiny pro správu pomocí příkazu Remove-AzManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Přesunutí předplatných v azure cli

Chcete-li přesunout odběr v příkazu příkazu cli, použijte příkaz add.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Chcete-li odebrat předplatné ze skupiny pro správu, použijte příkaz odebrat odběr.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups"></a>Přesunutí skupin pro správu 

### <a name="move-management-groups-in-the-portal"></a>Přesunutí skupin pro správu na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny skupiny správy služeb** > **Management groups**.

1. Vyberte skupinu pro správu, kterou plánujete jako nadřazenou.

1. V horní části stránky vyberte **Přidat skupinu pro správu**.

1. V nabídce, která se otevře, vyberte, jestli chcete novou nebo použijte existující skupinu pro správu.

   - Výběrem nového vytvoříte novou skupinu pro správu.
   - Výběrem existujícího systému se zobrazí rozevírací nabídka všech skupin pro správu, které můžete přesunout do této skupiny pro správu.  

   :::image type="content" source="./media/add_context_MG.png" alt-text="Přesunutí skupiny pro správu do nové nebo existující skupiny" border="false":::

1. Vyberte **Uložit**.

### <a name="move-management-groups-in-powershell"></a>Přesunutí skupin pro správu v PowerShellu

Pomocí příkazu Update-AzManagementGroup v prostředí PowerShell přesuňte skupinu pro správu pod jinou skupinu.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Přesunutí skupin pro správu v azure cli

Pomocí příkazu update přesuňte skupinu pro správu pomocí azure CLI.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Audit skupin pro správu s využitím protokolů aktivit

Skupiny pro správu se podporují v rámci [protokolu aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md). Můžete dotazovat všechny události, které se stalo pro skupinu pro správu ve stejném centrálním umístění jako ostatní prostředky Azure. Pro konkrétní skupinu pro správu si můžete si zobrazit všechny změny přiřazení zásad nebo přiřazení rolí.

:::image type="content" source="./media/al-mg.png" alt-text="Protokoly aktivit se skupinami pro správu" border="false":::

Pokud se chcete na skupiny pro správu dotazovat mimo Azure Portal, cílový obor pro skupiny pro správu vypadá takto: **"/providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="referencing-management-groups-from-other-resource-providers"></a>Odkazování na skupiny pro správu od jiných poskytovatelů prostředků

Při odkazování na skupiny pro správu z akcí jiného poskytovatele prostředků použijte jako obor následující cestu. Tato cesta se používá při použití PowerShellu, Azure CLI a REST API.  

`/providers/Microsoft.Management/managementGroups/{yourMgID}`

Příkladem použití této cesty je přiřazení nového přiřazení role skupině pro správu v prostředí PowerShell:

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Stejná cesta oboru se používá při načítání definice zásad y ve skupině pro správu.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>Další kroky

Další informace o řešeních pro správu najdete v následujících tématech:

- [Vytváření skupin pro správu pro organizaci prostředků Azure](./create.md)
- [Jak měnit, odstraňovat nebo spravovat skupiny pro správu](./manage.md)
- [Kontrola skupin pro správu v modulu Prostředky Azure PowerShellu](/powershell/module/az.resources#resources)
- [Kontrola skupin pro správu v rozhraní REST API](/rest/api/resources/managementgroups)
- [Kontrola skupin pro správu v Azure CLI](/cli/azure/account/management-group)
