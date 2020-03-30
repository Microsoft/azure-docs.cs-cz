---
title: Ochrana privátních zón a záznamů DNS – Azure DNS
description: V tomto studijním programu můžete začít chránit privátní zóny DNS a sady záznamů v Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: da94c9aa97483ab5792e917d6a8f60f846b0722e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473061"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>Jak chránit soukromé zóny DNS a záznamy

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Soukromé zóny DNS a záznamy jsou kritické prostředky. Odstranění zóny DNS nebo jednoho záznamu DNS může vést k výpadku služby. Je důležité, aby zóny a záznamy DNS byly chráněny před neoprávněnými nebo náhodnými změnami.

Tento článek vysvětluje, jak Azure DNS umožňuje chránit vaše soukromé zóny DNS a záznamy před těmito změnami.  Používáme dvě výkonné funkce cenných papírů poskytované správcem prostředků Azure: [řízení přístupu na základě rolí](../role-based-access-control/overview.md) a [uzamčení prostředků](../azure-resource-manager/management/lock-resources.md).

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Řízení přístupu na základě rolí Azure (RBAC) umožňuje jemně odstupňovanou správu přístupu pro uživatele, skupiny a prostředky Azure. S RBAC můžete udělit úroveň přístupu, kterou uživatelé potřebují. Další informace o tom, jak rbac pomáhá spravovat přístup, naleznete [v tématu Co je řízení přístupu na základě rolí](../role-based-access-control/overview.md).

### <a name="the-private-dns-zone-contributor-role"></a>Role přispěvatele zóny DNS soukromého DNS

Role přispěvatele zóny DNS je předdefinovaná role pro správu soukromých prostředků DNS. Tato role použitá pro uživatele nebo skupinu umožňuje spravovat soukromé prostředky DNS.

Skupina prostředků *myPrivateDNS* obsahuje pět zón pro contoso Corporation. Udělení oprávnění správce DNS Privátní dns zóny přispěvatele pro tuto skupinu prostředků, umožňuje úplnou kontrolu nad těmito zónami DNS. Zabraňuje udělení zbytečných oprávnění. Správce DNS nemůže vytvářet nebo zastavovat virtuální počítače.

Nejjednodušší způsob, jak přiřadit oprávnění RBAC je [prostřednictvím portálu Azure](../role-based-access-control/role-assignments-portal.md).  

Otevřete **ovládací prvek přístupu (IAM)** pro skupinu prostředků, vyberte **Přidat**a vyberte roli **přispěvatele zóny SOUKROMÉ DNS.** Vyberte požadované uživatele nebo skupiny, kterým chcete udělit oprávnění.

![RBAC na úrovni skupiny prostředků prostřednictvím portálu Azure](./media/dns-protect-private-zones-recordsets/rbac1.png)

Oprávnění lze [také udělit pomocí Azure PowerShellu](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Ekvivalentní příkaz je také [k dispozici prostřednictvím rozhraní příkazového příkazu Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-rbac"></a>RBAC na úrovni soukromé zóny

Pravidla Azure RBAC lze použít pro předplatné, skupinu prostředků nebo pro jednotlivé prostředky. Tento prostředek může být jednotlivá zóna DNS nebo jednotlivá sada záznamů.

Například skupina prostředků *myPrivateDNS* obsahuje *zónu private.contoso.com* a podzónu *customers.private.contoso.com*. Záznamy CNAME jsou vytvářeny pro každý účet odběratele. Účtu správce používanému ke správě záznamů CNAME jsou přiřazena oprávnění k vytváření záznamů v zóně *customers.private.contoso.com.* Účet může spravovat pouze *customers.private.contoso.com.*

Oprávnění RBAC na úrovni zóny lze udělit prostřednictvím portálu Azure.  Otevřete **ovládací prvek přístupu (IAM)** pro zónu, vyberte **Přidat**a vyberte roli **přispěvatele zóny SOUKROMÉ DNS.** Vyberte požadované uživatele nebo skupiny, kterým chcete udělit oprávnění.

![RBAC na úrovni zóny DNS prostřednictvím portálu Azure](./media/dns-protect-private-zones-recordsets/rbac2.png)

Oprávnění lze [také udělit pomocí Azure PowerShellu](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

Ekvivalentní příkaz je také [k dispozici prostřednictvím rozhraní příkazového příkazu Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>Úroveň nastavení záznamu RBAC

Oprávnění jsou použita na úrovni sady záznamů.  Uživateli je udělen ovládací prvek pro položky, které potřebují, a nemůže provádět žádné další změny.

Oprávnění RBAC na úrovni záznamu lze nakonfigurovat prostřednictvím portálu Azure pomocí tlačítka **Řízení přístupu (IAM)** na stránce sady záznamů:

![Nastavení záznamu RBAC na úrovni prostřednictvím portálu Azure](./media/dns-protect-private-zones-recordsets/rbac3.png)

![Nastavení záznamu RBAC na úrovni prostřednictvím portálu Azure](./media/dns-protect-private-zones-recordsets/rbac4.png)

Oprávnění RBAC na úrovni sady záznamů lze také [udělit pomocí prostředí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Ekvivalentní příkaz je také [k dispozici prostřednictvím rozhraní příkazového příkazu Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Vlastní role

Integrovaná role přispěvatele zóny DNS umožňuje plnou kontrolu nad prostředkem DNS. Je možné vytvořit vlastní role Azure poskytovat jemnější odstupňované řízení.

Účet, který se používá ke správě CNAMEs je uděleno oprávnění ke správě cname záznamy pouze. Účet nemůže upravovat záznamy jiných typů. Účet nemůže provést operace na úrovni zóny, jako je odstranění zóny.

Následující příklad ukazuje vlastní definici role pouze pro správu záznamů CNAME:

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Vlastnost Actions definuje následující oprávnění specifická pro DNS:

* `Microsoft.Network/privateDnsZones/CNAME/*`uděluje plnou kontrolu nad záznamy CNAME
* `Microsoft.Network/privateDNSZones/read`uděluje oprávnění ke čtení soukromých zón DNS, ale nikoli k jejich úpravám, což umožňuje zobrazit zónu, ve které je cname vytvářen.

> [!NOTE]
> Použití vlastní role RBAC zabránit odstranění sady záznamů a zároveň umožňuje jejich aktualizaci není efektivní ovládací prvek. Zabraňuje odstranění sad záznamů, ale nebrání jejich úpravám.  Povolené úpravy zahrnují přidání a odebrání záznamů ze sady záznamů, včetně odebrání všech záznamů, aby byla sada prázdných záznamů zobrazena. To má stejný účinek jako odstranění sady záznamů z hlediska rozlišení DNS.

Vlastní definice rolí nelze aktuálně definovat prostřednictvím portálu Azure. Vlastní roli založenou na této definici role lze vytvořit pomocí Azure PowerShellu:

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

Lze jej také vytvořit prostřednictvím příkazového příkazového příkazu k dispozici azure:

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

Roli pak lze přiřadit stejným způsobem jako předdefinované role, jak je popsáno výše v tomto článku.

Další informace o tom, jak vytvářet, spravovat a přiřazovat vlastní role, najdete [v tématu Vlastní role v Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Zámky prostředků

Azure Resource Manager podporuje jiný typ ovládacího prvku zabezpečení, možnost uzamknout prostředky. Uzamčení prostředků jsou použity pro prostředek a jsou účinné ve všech uživatelů a rolí. Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](../azure-resource-manager/management/lock-resources.md).

Existují dva typy uzamčení prostředků: **CanNotDelete** a **ReadOnly**. Tyto typy zámků lze použít buď na soukromou zónu DNS, nebo na jednotlivé sady záznamů.  Následující části popisují několik běžných scénářů a jak je podporovat pomocí uzamčení prostředků.

### <a name="protecting-against-all-changes"></a>Ochrana před všemi změnami

Chcete-li zabránit provádění změn, použijte zámek Jen pro čtení zóny. Tento zámek zabraňuje vytváření nových sad záznamů a úpravám nebo odstraňování existujících sad záznamů.

Uzamčení prostředků na úrovni zóny lze vytvořit prostřednictvím portálu Azure.  Na stránce zóny DNS vyberte **Zámky**a pak vyberte **+Přidat**:

![Uzamčení prostředků na úrovni zóny prostřednictvím portálu Azure](./media/dns-protect-private-zones-recordsets/locks1.png)

Uzamčení prostředků na úrovni zóny lze také vytvořit prostřednictvím [Prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest):

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Ekvivalentní příkaz je také [k dispozici prostřednictvím rozhraní příkazového příkazu Azure](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create):

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>Ochrana jednotlivých záznamů

Chcete-li zabránit existujícímu záznamu DNS nastaveného proti úpravám, použijte na sadu záznamů zámek Jen pro čtení.

> [!NOTE]
> Použití zámku CanNotDelete na sadu záznamů není účinnýovládací prvek. Zabrání odstranění sady záznamů, ale nezabrání jeho úpravám.  Povolené úpravy zahrnují přidání a odebrání záznamů ze sady záznamů, včetně odebrání všech záznamů, aby byla sada prázdných záznamů zobrazena. To má stejný účinek jako odstranění sady záznamů z hlediska rozlišení DNS.

Uzamčení prostředků na úrovni sady záznamů lze aktuálně nakonfigurovat jenom pomocí Azure PowerShellu.  Nejsou podporované na webu Azure Portal nebo Azure CLI.

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>Ochrana proti odstranění zóny

Když se zóna odstraní ve službě Azure DNS, odstraní se všechny sady záznamů v zóně.  Tuto operaci nelze vrátit zpět. Náhodné odstranění kritické zóny může mít významný obchodní dopad.  Je důležité chránit před náhodným odstraněním zóny.

Použití zámku CanNotDelete na zónu zabrání odstranění zóny. Zámky jsou zděděny podřízenými prostředky. Zámek zabrání odstranění všech sad záznamů v zóně. Jak je popsáno ve výše uvedené poznámce, je neefektivní, protože záznamy lze stále odebrat ze stávajících sad záznamů.

Jako alternativu použijte zámek CanNotDelete na záznam nastavený v zóně, jako je například sada záznamů SOA. Zóna se neodstraní, aniž by byla odstraněna také sada záznamů. Tento zámek chrání před odstraněním zóny a přitom stále umožňuje volně upravovat sady záznamů v rámci zóny. Pokud dojde k pokusu o odstranění zóny, Azure Resource Manager zjistí toto odebrání. Odebrání by také odstranit sadu záznamů SOA, Azure Resource Manager blokuje volání, protože SOA je uzamčen.  Nebudou odstraněny žádné sady záznamů.

Následující příkaz prostředí PowerShell vytvoří zámek CanNotDelete proti záznamu SOA dané zóny:

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
Další možností, jak zabránit náhodnému odstranění zóny, je použití vlastní role. Tato role zajišťuje, že účty používané ke správě zón nemají oprávnění k odstranění zóny. 

Pokud potřebujete odstranit zónu, můžete vynutit odstranění ve dvou krocích:

 - Nejprve udělit oprávnění k odstranění zóny
 - Za druhé udělte oprávnění k odstranění zóny.

Vlastní role funguje pro všechny zóny přístupné těmito účty. Účty s oprávněními k odstranění zóny, jako je například vlastník předplatného, mohou zónu stále omylem odstranit.

Je možné použít oba přístupy - uzamčení prostředků a vlastní role - současně jako obranný přístup k ochraně zóny DNS.

## <a name="next-steps"></a>Další kroky

* Další informace o práci s RBAC najdete [v tématu Začínáme se správou přístupu na webu Azure Portal](../role-based-access-control/overview.md).
* Další informace o práci se zámky prostředků najdete v [tématu Uzamčení prostředků pomocí Správce prostředků Azure](../azure-resource-manager/management/lock-resources.md).
