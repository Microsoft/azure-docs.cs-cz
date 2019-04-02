---
title: Jak změnit, odstranit nebo Správa skupin pro správu – zásady správného řízení Azure
description: Zjistěte, jak zobrazit, udržovat, aktualizovat a odstraňovat vaše hierarchie skupin správy.
author: rthorn17
ms.service: azure-resource-manager
ms.date: 02/20/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: f9e9bf51d50863f2e37a2f579c2db0edcfeca634
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801760"
---
# <a name="manage-your-resources-with-management-groups"></a>Správa vašich prostředků pomocí skupin pro správu

Pokud má vaše organizace mnoho předplatných, možná budete potřebovat způsob, jak efektivně spravovat přístup, zásady a dodržování předpisů pro tato předplatná. Skupiny pro správu Azure představují úroveň rozsahu nad předplatnými. Předplatná uspořádáte do kontejnerů označovaných jako skupiny pro správu a na tyto skupiny pro správu použijete své zásady správného řízení. Všechna předplatná v rámci skupiny pro správu automaticky dědí podmínky, které se na příslušnou skupinu pro správu vztahují.

Skupiny pro správu poskytují správu na podnikové úrovni ve velkém měřítku bez ohledu na to, jaké typy předplatného případně máte.  Další informace o skupinách správy, najdete v článku [uspořádání prostředků se skupinami pro správu Azure](overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="change-the-name-of-a-management-group"></a>Změňte název skupiny pro správu

Název skupiny pro správu můžete změnit pomocí portálu, Powershellu nebo rozhraní příkazového řádku Azure.

### <a name="change-the-name-in-the-portal"></a>Změňte název na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny služby** > **skupin pro správu**.

1. Vyberte skupiny pro správu, kterou chcete přejmenovat.

1. Vyberte **přejmenovat skupinu** možnosti v horní části stránky.

   ![Přejmenovat skupinu možnost na stránce skupiny správy](./media/detail_action_small.png)

1. Po otevření nabídky, zadejte nový název, který chcete zobrazit.

   ![Podokno přejmenovat skupinu a přejmenovat skupinu pro správu](./media/rename_context.png)

1. Vyberte **Uložit**.

### <a name="change-the-name-in-powershell"></a>Změňte název v prostředí PowerShell

Aktualizovat pomocí názvu zobrazení **aktualizace AzManagementGroup**. Například změnit správy skupin zobrazované jméno z "Contoso IT" do "Skupiny společnosti Contoso", spusťte následující příkaz:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Změňte název v rozhraní příkazového řádku Azure

Azure CLI použijte příkaz update.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Odstranit skupinu pro správu

Pokud chcete odstranit skupinu pro správu, musí být splněny následující požadavky:

1. Neexistují žádné podřízené skupiny pro správu nebo předplatná v rámci skupiny pro správu.

   - Pokud chcete přejít u určitého předplatného mimo skupinu pro správu, přečtěte si téma [přesunout do jiné skupiny pro správu předplatného](#move-subscriptions-in-the-hierarchy).

   - Přesunout skupinu pro správu do jiné skupiny pro správu, najdete v článku [přesunutí správy skupin v hierarchii](#move-management-groups-in-the-hierarchy).

1. Máte oprávnění k zápisu ve skupině pro správu ("Vlastník", "Přispěvatel" nebo "Přispěvatel skupiny Management"). A zjistit, jaká oprávnění máte, vyberte skupinu pro správu a pak vyberte **IAM**. Další informace o rolích RBAC najdete v tématu [spravovat přístup a oprávnění pomocí RBAC](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Odstranit na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny služby** > **skupin pro správu**.

1. Vyberte skupiny pro správu, kterou chcete odstranit.

1. Vyberte **odstranit**

    > [!TIP]
    > Pokud ikona je zakázaná, váš výběr myš najede myší na ikonu se dozvíte z důvodu.

   ![Skupina možností odstranit](./media/delete.png)

1. Je okno, které se otevře, potvrzení, že chcete odstranit skupinu pro správu.

   ![Odstranit skupinu potvrzovacím okně](./media/delete_confirm.png)

1. Vyberte **Ano**.

### <a name="delete-in-powershell"></a>Odstranit v prostředí PowerShell

Použití **odebrat AzManagementGroup** příkazu v Powershellu k odstranění skupin pro správu.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Odstranění v Azure CLI

Pomocí Azure CLI použijte příkaz az účet skupiny pro správu odstranit.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Zobrazení skupin pro správu

Můžete zobrazit všechny skupiny pro správu, kterou máte v roli RBAC s přímým přístupem nebo zděděná.  

### <a name="view-in-the-portal"></a>Zobrazit na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny služby** > **skupin pro správu**.

1. Se načítání stránky hierarchie skupiny správy. Tato stránka je, kde můžete prozkoumat všechny skupiny pro správu a předplatné, abyste měli přístup k. Výběr názvu skupiny přejdete dolů úrovně v hierarchii. Navigační funguje stejně jako Průzkumník souborů.

1. Pokud chcete zobrazit podrobnosti o skupině pro správu, vyberte **(podrobnosti)** odkaz vedle názvu skupiny pro správu. Pokud tento odkaz není k dispozici, nemáte oprávnění k zobrazení této skupiny pro správu.

   ![Hlavní](./media/main.png)

### <a name="view-in-powershell"></a>Zobrazení v prostředí PowerShell

Pomocí příkazu Get-AzManagementGroup k načtení všech skupin.  Zobrazit [Az.Resources](/powershell/module/az.resources/Get-AzManagementGroup) moduly pro úplný seznam správy skupiny příkazů GET Powershellu.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Jedna skupina pro správu vaší informace použijte parametr - GroupName

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Pokud chcete vrátit konkrétní skupině pro správu a všechny úrovně v hierarchii pod ním, použijte **-rozbalte** a **-Recurse** parametry.  

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

### <a name="view-in-azure-cli"></a>Zobrazení v rozhraní příkazového řádku Azure

Pomocí příkazu seznamu k načtení všech skupin.  

```azurecli-interactive
az account management-group list
```

Jedna skupina pro správu vaší informace použijte příkaz show

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Pokud chcete vrátit konkrétní skupině pro správu a všechny úrovně v hierarchii pod ním, použijte **-rozbalte** a **-Recurse** parametry.

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="move-subscriptions-in-the-hierarchy"></a>Přesunout předplatná v hierarchii

Jedním z důvodů, chcete-li vytvořit skupinu pro správu je pohromadě předplatných. Podřízené položky z jiné skupiny pro správu lze provést pouze skupiny pro správu a předplatných. Předplatné, které se přesune do skupiny pro správu dědí všechny zásady a přístup uživatelů z nadřazené skupiny pro správu.

Pokud chcete přesunout předplatné, všech následujících oprávnění RBAC musí být splněny:

- "Vlastník" role v podřízených předplatného.
- "Vlastník", "Přispěvatel" nebo "Správa skupiny přispěvatelů" role na group.* cílového nadřazeného správy
- "Vlastník", "Přispěvatel" nebo "Správa skupiny přispěvatelů" role na existující group.* nadřazené správy

*: Pokud cíl nebo existující skupiny pro správu nadřazené není kořenovou skupinu správy. Protože kořenové skupině pro správu je výchozí úvodní přímé pro všechny nové skupiny pro správu a předplatných, uživatelé oprávnění na něm přesunout položku nepotřebují.

Chcete-li zjistit, jaká oprávnění máte na webu Azure Portal, vyberte správu skupin a potom vyberte **IAM**. Další informace o rolích RBAC najdete v tématu [spravovat přístup a oprávnění pomocí RBAC](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Přesunout předplatná na portálu

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Přidání existujícího předplatného do skupiny pro správu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny služby** > **skupin pro správu**.

1. Vyberte skupiny pro správu, které máte v plánu má být nadřazená.

1. V horní části stránky vyberte **přidat předplatné**.

1. Vyberte předplatné, v seznamu správné ID.

   ![Dostupná předplatná pro přidání do skupiny pro správu](./media/add_context_sub.png)

1. Vyberte možnost "Uložit".

#### <a name="remove-a-subscription-from-a-management-group"></a>Odebrat ze skupiny pro správu předplatného

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny služby** > **skupin pro správu**.

1. Vyberte skupiny pro správu plánování, který je aktuálně nadřazeného.  

1. Vyberte tři tečky na konci řádku pro předplatné v seznamu, který chcete přesunout.

   ![Možnost přesunout na skupinu pro správu](./media/move_small.png)

1. Vyberte **přesunout**.

1. V nabídce, která se otevře, vyberte **nadřazená skupina pro správu**.

   ![Přesunout podokně nadřazenou skupinu změnit](./media/move_small_context.png)

1. Vyberte **Uložit**.

### <a name="move-subscriptions-in-powershell"></a>Přesunout předplatná v prostředí PowerShell

Pokud chcete přesunout předplatné v prostředí PowerShell, použijte příkaz New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Chcete-li odebrat propojení mezi a předplatném a skupině pro správu použijte příkaz Remove-AzManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Přesun předplatných v Azure CLI

Pokud chcete přesunout předplatné v rozhraní příkazového řádku, použijte příkaz Přidat.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Odebrat ze skupiny pro správu předplatného, použijte příkaz remove předplatného.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>Přesunutí správy skupin v hierarchii  

Při přesunutí nadřazená skupina pro správu hierarchie v této skupině se přesune s ním.

### <a name="move-management-groups-in-the-portal"></a>Přesunutí skupin pro správu na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny služby** > **skupin pro správu**.

1. Vyberte skupiny pro správu, které máte v plánu má být nadřazená.

1. V horní části stránky vyberte **přidat skupinu pro správu**.

1. V nabídce, která se otevře vyberte, pokud chcete novou, nebo použijte existující skupinu pro správu.

   - Nový výběr se vytvoří nová skupina pro správu.
   - Výběr existujícího zobrazíte rozevírací seznam všech skupin pro správu, které můžete přesunout do této skupiny pro správu.  

   ![Přesunout skupinu pro správu do nové nebo stávající skupiny](./media/add_context_MG.png)

1. Vyberte **Uložit**.

### <a name="move-management-groups-in-powershell"></a>Přesunutí skupin pro správu v prostředí PowerShell

Pomocí příkazu Update-AzManagementGroup v prostředí PowerShell přesunout skupinu pro správu v jiné skupině.

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'Contoso' -ParentId '/providers/Microsoft.Management/managementGroups/ContosoIT'
```  

### <a name="move-management-groups-in-azure-cli"></a>Přesunutí správy skupin v Azure CLI

Přesunout skupinu pro správu pomocí Azure CLI pomocí příkazu update.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent-id '/providers/Microsoft.Management/managementGroups/ContosoIT'
```

## <a name="audit-management-groups-using-activity-logs"></a>Audit skupin pro správu s využitím protokolů aktivit

Skupiny pro správu se podporují v rámci [protokolu aktivit Azure](../../azure-monitor/platform/activity-logs-overview.md). Všechny události, ke kterým dochází ve skupině pro správu ve stejném centrální umístění jako ostatní prostředky Azure se můžete dotazovat.  Pro konkrétní skupinu pro správu si můžete si zobrazit všechny změny přiřazení zásad nebo přiřazení rolí.

![Protokoly aktivit se skupiny pro správu](media/al-mg.png)

Pokud se chcete na skupiny pro správu dotazovat mimo Azure Portal, cílový obor pro skupiny pro správu vypadá takto: **"/providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="referencing-management-groups-from-other-resource-providers"></a>Odkazování na skupiny pro správu od jiných poskytovatelů prostředků

Při odkazování na skupiny pro správu z akce jiný poskytovatel prostředků, použijte následující cestu jako obor. Tato cesta se používá při použití prostředí PowerShell, rozhraní příkazového řádku Azure a rozhraní REST API.  

>"/providers/Microsoft.Management/managementGroups/{yourMgID}"

Příkladem použití této cesty je při přiřazování přiřazení nové role pro skupinu pro správu v prostředí PowerShell

```powershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Při načítání definice zásady na skupinu pro správu, se používá stejná cesta oboru.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>Další postup

Další informace o řešeních pro správu najdete v následujících tématech:

- [Vytváření skupin pro správu pro organizaci prostředků Azure](create.md)
- [Jak měnit, odstraňovat nebo spravovat skupiny pro správu](manage.md)
- [Kontrola skupin pro správu v modulu Prostředky Azure PowerShellu](/powershell/module/az.resources#resources)
- [Kontrola skupin pro správu v rozhraní REST API](/rest/api/resources/managementgroups)
- [Kontrola skupin pro správu v Azure CLI](/cli/azure/account/management-group)