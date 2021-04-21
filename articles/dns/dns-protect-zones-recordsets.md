---
title: Ochrana Zóny DNS a záznamů – Azure DNS
description: V této cestě výuky začněte chránit zóny a sady záznamů DNS v Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: 9d65e024e9efa3ad2bcb1c70d44360c8bd0de384
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785826"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Jak chránit záznamy a zóny DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zóny a záznamy DNS jsou důležité prostředky. Odstranění zóny DNS nebo jednoho záznamu DNS může mít za následek výpadek služby. Je důležité, aby zóny a záznamy DNS byly chráněné před neoprávněnými nebo neúmyslnými změnami.

Tento článek vysvětluje, jak Azure DNS umožňuje chránit privátní zóny DNS a záznamy s těmito změnami.  K dispozici jsou dvě výkonné funkce cenných papírů poskytované Azure Resource Manager: [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md) a [zámky prostředků](../azure-resource-manager/management/lock-resources.md).

## <a name="azure-role-based-access-control"></a>Řízení přístupu na základě role v Azure

Řízení přístupu na základě role v Azure (Azure RBAC) umožňuje jemně odstupňovanou správu přístupu pro uživatele, skupiny a prostředky Azure. Pomocí Azure RBAC můžete udělit úroveň přístupu, kterou uživatelé potřebují. Další informace o tom, jak Azure RBAC pomáhá spravovat přístup, najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>Role Přispěvatel zóny DNS

Role Přispěvatel zóny DNS je integrovaná role pro správu privátních prostředků DNS. Tato role použitá pro uživatele nebo skupinu jim umožní spravovat prostředky DNS.

Skupina prostředků *myResourceGroup* obsahuje pět zón pro společnost Contoso Corporation. Udělení oprávnění přispěvateli zóny DNS pro Správce DNS k této skupině prostředků umožňuje úplnou kontrolu nad těmito zónami DNS. Zabraňuje udělení zbytečných oprávnění. Správce DNS nemůže vytvářet ani zastavovat virtuální počítače.

Nejjednodušším způsobem přiřazení oprávnění Azure RBAC je [prostřednictvím Azure Portal](../role-based-access-control/role-assignments-portal.md).  

Otevřete pro skupinu prostředků položku **řízení přístupu (IAM)** a pak vyberte **Přidat** a potom vyberte roli **Přispěvatel zóny DNS** . Vyberte požadované uživatele nebo skupiny, kterým chcete udělit oprávnění.

![Úroveň skupiny prostředků Azure RBAC prostřednictvím Azure Portal](./media/dns-protect-zones-recordsets/rbac1.png)

Oprávnění lze také [udělit pomocí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Ekvivalentní příkaz je dostupný taky [prostřednictvím Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-azure-rbac"></a>Úroveň zóny Azure RBAC

Pravidla Azure RBAC se dají použít u předplatného, skupiny prostředků nebo samostatného prostředku. Tento prostředek může být jednotlivou zónou DNS nebo individuální sada záznamů.

Například skupina prostředků *myResourceGroup* obsahuje zónu *contoso.com* a podzónu *Customers.contoso.com*. Pro každý účet zákazníka se vytvoří záznamy CNAME. Účet správce, který se používá ke správě záznamů CNAME, má přiřazená oprávnění k vytváření záznamů v zóně *Customers.contoso.com* . Účet může spravovat jenom *Customers.contoso.com* .

Oprávnění Azure RBAC na úrovni zóny je možné udělit prostřednictvím Azure Portal.  Otevřete **řízení přístupu (IAM)** pro zónu, vyberte **Přidat**, vyberte roli **Přispěvatel zóny DNS** a vyberte požadované uživatele nebo skupiny, kterým chcete udělit oprávnění.

![Úroveň zóny DNS Azure RBAC prostřednictvím Azure Portal](./media/dns-protect-zones-recordsets/rbac2.png)

Oprávnění lze také [udělit pomocí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

Ekvivalentní příkaz je dostupný taky [prostřednictvím Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>Úroveň sady záznamů Azure RBAC

Oprávnění se aplikují na úrovni sady záznamů.  Uživateli je uděleno řízení k záznamům, které potřebují, a nelze provádět žádné další změny.

Úroveň sady záznamů Azure RBAC lze nakonfigurovat prostřednictvím Azure Portal pomocí tlačítka **Access Control (IAM)** na stránce sada záznamů:

![Úroveň sady záznamů Azure RBAC prostřednictvím Azure Portal](./media/dns-protect-zones-recordsets/rbac3.png)

Oprávnění Azure RBAC na úrovni záznamů lze také [udělit pomocí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Ekvivalentní příkaz je dostupný taky [prostřednictvím Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Vlastní role

Integrovaná role Přispěvatel zóny DNS umožňuje úplnou kontrolu nad prostředkem DNS. Je možné vytvořit vlastní role Azure pro zajištění jemnějšího řízení.

Účtu, který se používá ke správě záznamů CNAME, je uděleno oprávnění pouze ke správě záznamů CNAME. Účet nemůže měnit záznamy jiných typů. Účet nemůže provádět operace na úrovni zóny, jako je například odstranění zóny.

Následující příklad ukazuje definici vlastní role pro správu pouze záznamů CNAME:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
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

Vlastnost Actions definuje následující oprávnění specifická pro službu DNS:

* `Microsoft.Network/dnsZones/CNAME/*` udělí plnou kontrolu nad záznamy CNAME.
* `Microsoft.Network/dnsZones/read` udělí oprávnění číst zóny DNS, ale neupravuje je, takže se zobrazí zóna, ve které se vytváří záznam CNAME.

Zbývající akce se zkopírují z [předdefinované role Přispěvatel zóny DNS](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Pomocí vlastní role Azure zabráníte odstranění sad záznamů, ale pořád jejich aktualizace není efektivním ovládacím prvkem. Zabraňuje odstranění sad záznamů, ale nebrání jejich úpravám.  Mezi povolené úpravy patří přidávání a odebírání záznamů ze sady záznamů, včetně odebrání všech záznamů, které ponechávají prázdnou sadu záznamů. To má stejný účinek jako odstranění sady záznamů z pohledu na překlad DNS.

Vlastní definice rolí nejdou aktuálně definovat prostřednictvím Azure Portal. Vlastní role na základě této definice role se dá vytvořit pomocí Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Dá se taky vytvořit prostřednictvím Azure CLI:

```azurecli
# Create new role definition based on input file
az role create -inputfile <file path>
```

Roli je pak možné přiřadit stejným způsobem jako předdefinované role, jak je popsáno výše v tomto článku.

Další informace o tom, jak vytvářet, spravovat a přiřazovat vlastní role, najdete v tématu [vlastní role Azure](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Zámky prostředků

Azure Resource Manager podporuje jiný typ řízení zabezpečení, schopnost uzamknout prostředky. V prostředku se aplikují zámky prostředků, které jsou platné pro všechny uživatele a role. Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](../azure-resource-manager/management/lock-resources.md).

Existují dva typy uzamčení prostředků: **CanNotDelete** a **ReadOnly**. Tyto typy zámků se dají použít buď na zónu Privátní DNS, nebo na jednotlivé sady záznamů. Následující části popisují několik běžných scénářů a jejich podporu pomocí zámků prostředků.

### <a name="protecting-against-all-changes"></a>Ochrana před všemi změnami

Aby nedocházelo k provedeným změnám, aplikujte na zónu zámek jen pro čtení. Tento zámek zabraňuje vytváření nových sad záznamů a existující sady záznamů z úprav nebo odstranění.

Zámky prostředků na úrovni zóny lze vytvořit prostřednictvím Azure Portal.  Na stránce zóna DNS vyberte **zámky** a pak vyberte **+ Přidat**:

![Zámky prostředků na úrovni zóny prostřednictvím Azure Portal](./media/dns-protect-zones-recordsets/locks1.png)

Zámky prostředků na úrovni zóny je také možné vytvořit prostřednictvím [Azure PowerShell](/powershell/module/az.resources/new-azresourcelock):

```azurepowershell
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Ekvivalentní příkaz je dostupný taky [prostřednictvím Azure CLI](/cli/azure/lock#az_lock_create):

```azurecli
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>Ochrana jednotlivých záznamů

Chcete-li zabránit existující sadě záznamů DNS proti úpravám, použijte k sadě záznamů zámek ReadOnly.

> [!NOTE]
> Použití zámku CanNotDelete na sadu záznamů není platným ovládacím prvkem. Brání tomu, aby se sada záznamů odstranila, ale nebrání její úpravě.  Mezi povolené úpravy patří přidávání a odebírání záznamů ze sady záznamů, včetně odebrání všech záznamů, které ponechávají prázdnou sadu záznamů. To má stejný účinek jako odstranění sady záznamů z pohledu na překlad DNS.

Zámky prostředků na úrovni sady záznamů se teď dají konfigurovat jenom pomocí Azure PowerShell.  Nejsou podporované v Azure Portal ani v rozhraní příkazového řádku Azure CLI.

```azurepowershell
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>Ochrana před odstraněním zóny

Když se zóna v Azure DNS odstraní, odstraní se všechny sady záznamů v zóně.  Tuto operaci nelze vrátit zpět. Neúmyslné odstranění kritické zóny má za důsledek značný dopad na chod firmy.  Je důležité chránit před náhodným odstraněním zóny.

Použití zámku CanNotDelete pro zónu brání odstranění zóny. Zámky jsou děděny podřízenými prostředky. Zámek zabraňuje odstranění všech sad záznamů v zóně. Jak je popsáno výše v poznámce, je neúčinná, protože záznamy je stále možné odebrat z existujících sad záznamů.

Alternativně můžete použít CanNotDelete zámek pro sadu záznamů v zóně, jako je například sada záznamů SOA. Zóna se neodstraní, aniž by se zároveň odstranily sady záznamů. Tento zámek chrání před odstraněním zóny a zároveň povoluje, aby byly sady záznamů v rámci zóny volně upravovány. Pokud se pokusíte odstranit zónu, Azure Resource Manager detekuje toto odebrání. Odebrání by také odstranilo sadu záznamů SOA, Azure Resource Manager blokuje volání, protože je záznam SOA uzamčen.  Neodstranily se žádné sady záznamů.

Následující příkaz prostředí PowerShell vytvoří zámek CanNotDelete proti záznamu SOA dané zóny:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Další možností, jak zabránit náhodnému odstranění zóny, je použití vlastní role. Tato role zajišťuje, že účty používané ke správě zón nemají oprávnění k odstranění zóny. 

Pokud budete potřebovat odstranit zónu, můžete vyhovět dvěma kroky odstranit:

 - Nejdřív udělte oprávnění k odstranění zóny.
 - Potom udělte oprávnění k odstranění zóny.

Vlastní role funguje pro všechny zóny, ke kterým přistupovaly tyto účty. Účty s oprávněními k odstranění zóny, jako je vlastník předplatného, můžou pořád omylem odstranit zónu.

Současně je možné použít oba přístupy – zámky prostředků i vlastní role – ve stejnou dobu jako přístup k ochraně zóny DNS při důkladném podrobnějším přístupu.

## <a name="next-steps"></a>Další kroky

* Další informace o práci s Azure RBAC najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md).
* Další informace o práci s zámky prostředků najdete v tématu [uzamčení prostředků pomocí Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
