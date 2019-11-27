---
title: Ochrana Zóny DNS a záznamů – Azure DNS
description: V této cestě výuky začněte chránit zóny a sady záznamů DNS v Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 12/4/2018
ms.author: allensu
ms.openlocfilehash: b84ba055dd8214ae18e76004671e3922e6f3b878
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211449"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Jak chránit záznamy a zóny DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zóny a záznamy DNS jsou důležité prostředky. Odstranění zóny DNS nebo dokonce pouhého jednoho záznamu DNS může způsobit výpadek služby.  Proto je důležité, aby byly kritické zóny DNS a záznamy chráněny před neoprávněnými nebo neúmyslnými změnami.

Tento článek vysvětluje, jak Azure DNS umožňuje chránit zóny DNS a záznamy s těmito změnami.  K dispozici jsou dvě výkonné funkce zabezpečení, které poskytuje Azure Resource Manager: [řízení přístupu na základě role](../role-based-access-control/overview.md) a [zámky prostředků](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Správa na Access Control základě rolí v Azure (RBAC) umožňuje jemně odstupňovanou správu přístupu pro uživatele, skupiny a prostředky Azure. Pomocí RBAC můžete přesně udělit množství přístupu, který uživatelé potřebují k provádění svých úloh. Další informace o tom, jak RBAC pomáhá spravovat přístup, najdete v tématu [co je Access Control na základě rolí](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>Role Přispěvatel zóny DNS

Role Přispěvatel zóny DNS je předdefinovaná role, kterou poskytuje Azure pro správu prostředků DNS.  Přiřazení oprávnění Přispěvatel zóny DNS uživateli nebo skupině umožňuje této skupině spravovat prostředky DNS, ale ne prostředky žádného jiného typu.

Předpokládejme například, že skupina prostředků *myzones* obsahuje pět zón pro společnost Contoso Corporation. Udělení oprávnění přispěvateli zóny DNS pro Správce DNS k této skupině prostředků umožňuje úplnou kontrolu nad těmito zónami DNS. Zabraňuje také udělení zbytečných oprávnění, například Správce DNS nemůže vytvořit nebo zastavit Virtual Machines.

Nejjednodušší způsob, jak přiřadit oprávnění RBAC [, je prostřednictvím Azure Portal](../role-based-access-control/role-assignments-portal.md).  Otevřete pro skupinu prostředků položku **řízení přístupu (IAM)** a pak vyberte **Přidat**, vyberte roli **Přispěvatel zóny DNS** a vyberte požadované uživatele nebo skupiny, kterým chcete udělit oprávnění.

![Úroveň skupiny prostředků RBAC prostřednictvím Azure Portal](./media/dns-protect-zones-recordsets/rbac1.png)

Oprávnění lze také [udělit pomocí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

Ekvivalentní příkaz je dostupný taky [prostřednictvím Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>RBAC úrovně zóny

Pravidla Azure RBAC se dají použít u předplatného, skupiny prostředků nebo samostatného prostředku. V případě Azure DNS může být tímto prostředkem samostatná zóna DNS nebo dokonce individuální sada záznamů.

Předpokládejme například, že skupina prostředků *myzones* obsahuje zónu *contoso.com* a podzónu *Customers.contoso.com* , ve které jsou vytvářeny záznamy CNAME pro každý účet zákazníka.  Účet, který se používá ke správě těchto záznamů CNAME, by měl mít přiřazená oprávnění k vytváření záznamů jenom v zóně *Customers.contoso.com* , neměl by mít přístup k ostatním zónám.

Oprávnění RBAC na úrovni zóny je možné udělit prostřednictvím Azure Portal.  Otevřete **řízení přístupu (IAM)** pro zónu a pak vyberte **Přidat**, vyberte roli **Přispěvatel zóny DNS** a vyberte požadované uživatele nebo skupiny, kterým chcete udělit oprávnění.

![RBAC úrovně zóny DNS prostřednictvím Azure Portal](./media/dns-protect-zones-recordsets/rbac2.png)

Oprávnění lze také [udělit pomocí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

Ekvivalentní příkaz je dostupný taky [prostřednictvím Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>Nastavení RBAC na úrovni sady záznamů

Můžeme se dál dostat na jeden krok. Vezměte v úvahu správce pošty pro společnost Contoso Corporation, který potřebuje přístup k záznamům MX a TXT na vrcholu zóny contoso.com.  Nepotřebuje přístup k žádným jiným záznamům MX nebo TXT ani k žádným záznamům jiného typu.  Azure DNS vám umožní přiřadit oprávnění na úrovni sady záznamů, aby přesně vyhovovaly záznamům, ke kterým správce pošty potřebuje přístup.  Správce pošty je udělen přesně ovládacím prvkem, který potřebuje, a nemůže provést žádné další změny.

Oprávnění RBAC na úrovni záznamů můžete nakonfigurovat prostřednictvím Azure Portal pomocí tlačítka **Uživatelé** na stránce sada záznamů:

![Zaznamenání RBAC na úrovni nastavení pomocí Azure Portal](./media/dns-protect-zones-recordsets/rbac3.png)

[Pomocí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)lze také udělit oprávnění RBAC na úrovni záznamů a nastavení:

```azurepowershell
# Grant permissions to a specific record set
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

Ekvivalentní příkaz je dostupný taky [prostřednictvím Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Vlastní role

Integrovaná role Přispěvatel zóny DNS umožňuje úplnou kontrolu nad prostředkem DNS. Je také možné vytvořit vlastní role zákazníků Azure a zajistit tak i přesnější kontrolu.

Znovu zvažte příklad, ve kterém se vytvoří záznam CNAME v zóně *Customers.contoso.com* pro každý účet zákazníka společnosti Contoso Corporation.  Účtu, který slouží ke správě těchto záznamů CNAME, by mělo být uděleno oprávnění pouze pro správu záznamů CNAME.  Pak není možné upravovat záznamy jiných typů (například měnit záznamy MX) nebo provádět operace na úrovni zóny, jako je odstranění zóny.

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
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Vlastnost Actions definuje následující oprávnění specifická pro službu DNS:

* `Microsoft.Network/dnsZones/CNAME/*` uděluje plnou kontrolu nad záznamy CNAME.
* `Microsoft.Network/dnsZones/read` uděluje oprávnění číst zóny DNS, ale neupravuje je, takže se zobrazí zóna, ve které se vytváří záznam CNAME.

Zbývající akce se zkopírují z [předdefinované role Přispěvatel zóny DNS](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Použití vlastní role RBAC k zamezení odstranění sad záznamů, zatímco jejich aktualizace je neefektivním ovládacím prvkem. Zabraňuje odstranění sad záznamů, ale nebrání jejich úpravám.  Mezi povolené úpravy patří přidávání a odebírání záznamů ze sady záznamů, včetně odebrání všech záznamů, které ponechávají prázdnou sadu záznamů. To má stejný účinek jako odstranění sady záznamů z pohledu na překlad DNS.

Vlastní definice rolí se aktuálně nedají definovat prostřednictvím Azure Portal. Vlastní role na základě této definice role se dá vytvořit pomocí Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Dá se taky vytvořit prostřednictvím Azure CLI:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

Roli je pak možné přiřadit stejným způsobem jako předdefinované role, jak je popsáno výše v tomto článku.

Další informace o tom, jak vytvářet, spravovat a přiřazovat vlastní role, najdete v tématu [vlastní role v Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Zámky prostředků

Kromě RBAC Azure Resource Manager podporuje jiný typ řízení zabezpečení, konkrétně možnost Uzamknout prostředky. Kde pravidla RBAC umožňují řídit akce konkrétních uživatelů a skupin, jsou na prostředku aplikovány zámky prostředků a jsou platné pro všechny uživatele a role. Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](../azure-resource-manager/resource-group-lock-resources.md).

Existují dva typy uzamčení prostředků: **CanNotDelete** a **ReadOnly**. Tato nastavení se dají použít buď na zónu DNS, nebo na jednotlivé sady záznamů.  Následující části popisují několik běžných scénářů a jejich podporu pomocí zámků prostředků.

### <a name="protecting-against-all-changes"></a>Ochrana před všemi změnami

Aby nedošlo k žádným změnám, aplikujte na zónu zámek jen pro čtení.  Tím zabráníte vytváření nových sad záznamů a existující sady záznamů se nemění nebo odstraňují.

Zámky prostředků na úrovni zóny lze vytvořit prostřednictvím Azure Portal.  Na stránce zóna DNS vyberte **zámky**a pak vyberte **+ Přidat**:

![Zámky prostředků na úrovni zóny prostřednictvím Azure Portal](./media/dns-protect-zones-recordsets/locks1.png)

Zámky prostředků na úrovni zóny je také možné vytvořit prostřednictvím Azure PowerShell:

```azurepowershell
# Lock a DNS zone
New-AzResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Konfigurace zámků prostředků Azure se v současné době nepodporuje prostřednictvím rozhraní příkazového řádku Azure CLI.

### <a name="protecting-individual-records"></a>Ochrana jednotlivých záznamů

Chcete-li zabránit existující sadě záznamů DNS proti úpravám, použijte k sadě záznamů zámek ReadOnly.

> [!NOTE]
> Použití zámku CanNotDelete na sadu záznamů není platným ovládacím prvkem. Brání tomu, aby se sada záznamů odstranila, ale nebrání její úpravě.  Mezi povolené úpravy patří přidávání a odebírání záznamů ze sady záznamů, včetně odebrání všech záznamů, které ponechávají prázdnou sadu záznamů. To má stejný účinek jako odstranění sady záznamů z pohledu na překlad DNS.

Zámky prostředků na úrovni sady záznamů se teď dají konfigurovat jenom pomocí Azure PowerShell.  Nejsou podporované v Azure Portal ani v rozhraní příkazového řádku Azure.

```azurepowershell
# Lock a DNS record set
New-AzResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Ochrana před odstraněním zóny

Když se zóna v Azure DNS odstraní, odstraní se i všechny sady záznamů v zóně.  Tato operace se nedá vrátit zpět.  Neúmyslné odstranění kritické zóny má za důsledek značný dopad na chod firmy.  Je proto velmi důležité chránit před náhodným odstraněním zóny.

Použití zámku CanNotDelete pro zónu brání odstranění zóny.  Vzhledem k tomu, že zámky jsou děděny pomocí podřízených prostředků, brání také v odstranění všech sad záznamů v zóně, což může být nežádoucí.  Podobně jak je popsáno výše v poznámce, je také neúčinná, protože záznamy je stále možné odebrat z existujících sad záznamů.

Alternativně zvažte použití zámku CanNotDelete na sadu záznamů v zóně, jako je například sada záznamů SOA.  Vzhledem k tomu, že zónu nelze odstranit, aniž by se zároveň odstranily sady záznamů, tato operace se chrání před odstraněním zóny a zároveň umožňuje, aby byly sady záznamů v rámci zóny změněny volně. Pokud dojde k pokusu o odstranění zóny, Azure Resource Manager ji také odstraní a zablokuje volání, protože je záznam SOA uzamčen.  Neodstranily se žádné sady záznamů.

Následující příkaz prostředí PowerShell vytvoří zámek CanNotDelete proti záznamu SOA dané zóny:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set
New-AzResourceLock -LockLevel CanNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Dalším způsobem, jak zabránit náhodnému odstranění zóny, je použití vlastní role k tomu, aby se zajistilo, že u operátorů a účtů služeb používaných ke správě zón nejsou oprávnění k odstranění zóny. Když budete potřebovat odstranit zónu, můžete vyhovět dvoustupňovému odstranění, nejdřív udělit oprávnění k odstranění zóny (v oboru zóny, aby nedošlo k odstranění nesprávné zóny), a druhé k odstranění zóny.

Tento druhý přístup má výhodu, že funguje pro všechny zóny, ke kterým přistupují tyto účty, aniž byste si museli popomenout vytvořit zámky. Má nevýhodu, že všechny účty s oprávněními k odstranění zóny, jako je vlastník předplatného, můžou pořád omylem odstranit kritickou zónu.

Současně je možné použít oba přístupy – zámky prostředků i vlastní role – ve stejnou dobu jako přístup k ochraně zóny DNS při důkladném podrobnějším přístupu.

## <a name="next-steps"></a>Další kroky

* Další informace o práci s RBAC najdete v tématu [Začínáme se správou přístupu v Azure Portal](../role-based-access-control/overview.md).
* Další informace o práci s zámky prostředků najdete v tématu [uzamčení prostředků pomocí Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).
