---
title: Ochrana záznamů a zón DNS | Dokumentace Microsoftu
description: Nastaví ochrana záznamů a zón DNS v DNS Azure od Microsoftu.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.assetid: 190e69eb-e820-4fc8-8e9a-baaf0b3fb74a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2016
ms.author: victorh
ms.openlocfilehash: ff20c16c89ca5bf27bfddc654119b428cc425d2d
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173463"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Jak chránit záznamy a zóny DNS

Záznamy a zóny DNS jsou důležitým prostředkům. Odstraňuje se zóna DNS nebo jenom jeden záznam DNS může vést k výpadku celkové služby.  Proto je důležité, aby kritické záznamy a zóny DNS jsou chráněné proti neoprávněné nebo nechtěné změny.

Tento článek vysvětluje, jak Azure DNS umožňuje chránit proti takové změny záznamy a zóny DNS.  Můžeme použít dva výkonných bezpečnostních funkcí, které zajišťuje Azure Resource Manager k dispozici: [řízení přístupu na základě rolí](../role-based-access-control/overview.md) a [zámky prostředků](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Azure na základě rolí řízení přístupu (RBAC) umožňuje přesnou správu přístupu pro uživatele Azure, skupiny a prostředky. Pomocí RBAC, můžete udělit přesně takový přístup, že uživatelé potřebují k provádění svých úloh. Další informace o tom, jak AZURE pomůže spravovat přístup, najdete v části [co je řízení přístupu na základě Role](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>Role: Přispěvatel zóny DNS.

"Přispěvatel zóny DNS' role je předdefinovaná role pro správu prostředků služby DNS poskytovaný platformou Azure.  Přiřazení oprávnění Přispěvatel zóny DNS pro uživatele nebo skupiny umožňuje této skupiny pro správu prostředků DNS, ale ne prostředky jiného typu.

Předpokládejme například, že skupiny prostředků "myzones' obsahuje pět zóny pro společnost Contoso. Udělení oprávnění "Přispěvatel zóny DNS' k této skupině prostředků Správce DNS, umožňuje plnou kontrolu nad tyto zóny DNS. Také se vyhnete, poskytování nadbytečná oprávnění, například Správce DNS nejde vytvořit nebo zastavovat virtuální počítače.

Nejjednodušší způsob, jak přiřadit oprávnění RBAC je [prostřednictvím webu Azure portal](../role-based-access-control/role-assignments-portal.md).  Otevře se okno 'Řízení přístupu (IAM)' pro skupinu prostředků, pak klikněte na tlačítko 'Přidat', pak vyberte role: Přispěvatel zóny DNS"a vyberte požadované uživatele nebo skupiny, které chcete udělit oprávnění.

![Úrovni skupiny prostředků RBAC prostřednictvím webu Azure portal](./media/dns-protect-zones-recordsets/rbac1.png)

Oprávnění může být také [udělit prostřednictvím Azure Powershellu](../role-based-access-control/role-assignments-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

Je také ekvivalentní příkaz [dostupná přes rozhraní příkazového řádku Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>Zóna úroveň RBAC

Pravidla pro Azure RBAC můžete aplikovat na předplatné, skupinu prostředků nebo na samostatný prostředek. V případě Azure DNS může být tento prostředek jednotlivé zónu DNS, nebo dokonce jednotlivé záznamová sada.

Předpokládejme například, že skupiny prostředků "myzones' obsahuje zóně"contoso.com"a subzone"customers.contoso.com' ve kterém jsou vytvořeny záznamy CNAME pro každý účet zákazníka.  Účet použitý ke správě těchto záznamů CNAME by přiřadit oprávnění k vytváření záznamů v zóně "customers.contoso.com", nemůže mít přístup k jiné zóny.

Oprávnění na úrovni zóny RBAC lze udělit prostřednictvím webu Azure portal.  Otevře se okno "Řízení přístupu (IAM)" pro zónu, pak klikněte na tlačítko 'Přidat', pak vyberte role: Přispěvatel zóny DNS"a vyberte požadované uživatele nebo skupiny, které chcete udělit oprávnění.

![RBAC úrovni zóny DNS pomocí webu Azure portal](./media/dns-protect-zones-recordsets/rbac2.png)

Oprávnění může být také [udělit prostřednictvím Azure Powershellu](../role-based-access-control/role-assignments-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

Je také ekvivalentní příkaz [dostupná přes rozhraní příkazového řádku Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>Úroveň RBAC sady záznamů

Společnost Microsoft může přejděte o krok dál. Vezměte v úvahu e-mailu správce společnosti Contoso Corporation, který potřebuje přístup k MX a TXT záznamy na vrcholu zóny "contoso.com".  Nepotřebuje přístup k jiné MX nebo TXT záznamů, nebo záznamy, které z jakéhokoli jiného typu.  Azure DNS umožňuje přiřadit oprávnění na úrovni sadu záznamů na přesně záznamy, které potřebuje přístup k e-mailu správce.  E-mailu správce má oprávnění přesně řídit potřebuje a nemůže provádět žádné změny.

Úroveň oprávnění RBAC Record-set lze nakonfigurovat prostřednictvím webu Azure portal pomocí tlačítka 'Uživatele' v okně Sada záznamů:

![Sada záznamů úroveň RBAC prostřednictvím webu Azure portal](./media/dns-protect-zones-recordsets/rbac3.png)

Sada záznamů úrovně oprávnění RBAC může být také [udělit prostřednictvím Azure Powershellu](../role-based-access-control/role-assignments-powershell.md):

```powershell
# Grant permissions to a specific record set
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

Je také ekvivalentní příkaz [dostupná přes rozhraní příkazového řádku Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Vlastní role

Předdefinovaná role: Přispěvatel zóny DNS' umožňuje plnou kontrolu nad prostředku DNS. Je také možné vytvořit vlastní zákazník role služby Azure, k poskytování řízení i citlivější.

Zvažte znovu příklad, ve kterém se vytvoří záznam CNAME v zóně "customers.contoso.com" pro každý účet zákazníka Contoso Corporation.  Účet použitý ke správě těchto záznamů CNAME musí udělit oprávnění ke správě záznamů CNAME.  Potom se nepodařilo upravit záznamy z ostatních typů (jako je třeba Změna záznamů MX) nebo provádění operací úrovni zóny, jako jsou odstranění zóny.

Následující příklad ukazuje definice vlastních rolí pro správu záznamů CNAME:

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

Vlastnost akcí definuje následující DNS konkrétní oprávnění:

* `Microsoft.Network/dnsZones/CNAME/*` uděluje úplnou kontrolu nad záznamy CNAME
* `Microsoft.Network/dnsZones/read` uděluje oprávnění ke čtení zóny DNS, ale ne k jejich úpravu, umožní vám zobrazíte zónu, ve kterém se vytváří záznam CNAME.

Zbývající akce, které jsou zkopírovány ze [předdefinovaná role Přispěvatel zóny DNS](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Využívat vlastní roli RBAC, abyste zabránili odstranění sady záznamů je potřeba aktualizovat umožní není účinně ovládá. Sady záznamů zabraňuje odstranění, ale nezabrání je upravovat.  Povolené změny patří přidávání a odebírání záznamů ze sady záznamů, včetně odebrání všech záznamů ponechte "prázdný" sadu záznamů. To má stejný účinek jako odstranění záznamu sady z hlediska překlad DNS.

Vlastní definice rolí nejde definovat aktuálně prostřednictvím portálu Azure portal. Můžete vytvořit vlastní roli na základě této definice role pomocí Azure Powershellu:

```powershell
# Create new role definition based on input file
New-AzureRmRoleDefinition -InputFile <file path>
```

Také možné vytvářet přes rozhraní příkazového řádku Azure:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

Roli pak můžete přiřadit stejným způsobem jako předdefinované role, jak je popsáno výše v tomto článku.

Další informace o tom, jak vytvářet, spravovat a přiřazení vlastních rolí, najdete v části [vlastní role v Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Zámky prostředků

Azure Resource Manager podporuje kromě RBAC, jiný typ řízení zabezpečení, a to možnost prostředky 'lock'. Kde RBAC pravidla umožňují řídit akce konkrétních uživatelů a skupin, zámky prostředků se použijí k prostředku a platí ve všech uživatelů a rolí. Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](../azure-resource-manager/resource-group-lock-resources.md).

Existují dva typy prostředků zámku: **DoNotDelete** a **jen pro čtení**. Ty lze použít na zónu DNS, nebo na jednotlivé sady záznamů.  Následující části popisují několik běžných scénářů a jak podporovat jejich použití zámků prostředků.

### <a name="protecting-against-all-changes"></a>Ochrana proti všechny změny

Chcete-li brání veškerých změnách prováděných, použijte zámek ReadOnly do zóny.  To brání nové sady záznamů vytvořený a existující sady záznamů z se změnily nebo odstranily.

Uzamčení prostředků s úrovní zóny lze vytvořit pomocí webu Azure portal.  Z okna zóny DNS, klikněte na tlačítko 'Zámky' pak přidejte:

![Uzamčení prostředků s úrovní zóny prostřednictvím webu Azure portal](./media/dns-protect-zones-recordsets/locks1.png)

Zóna úrovni prostředku, který zámky lze také vytvořit pomocí Azure Powershellu:

```powershell
# Lock a DNS zone
New-AzureRmResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Konfigurace ámků prostředků Azure se momentálně nepodporuje prostřednictvím rozhraní příkazového řádku Azure.

### <a name="protecting-individual-records"></a>Ochrana jednotlivých záznamů

Aby se zabránilo nastavení pro úpravu existující záznam DNS, použijte zámek ReadOnly do sady záznamů.

> [!NOTE]
> Použití DoNotDelete zámek na sadu záznamů není efektivní ovládacího prvku. Ta brání odstranit sady záznamů, ale nezabrání ho nebylo možné měnit.  Povolené změny patří přidávání a odebírání záznamů ze sady záznamů, včetně odebrání všech záznamů ponechte "prázdný" sadu záznamů. To má stejný účinek jako odstranění záznamu sady z hlediska překlad DNS.

Uzamčení prostředků s úrovní sadu záznamů můžete nyní pouze být nakonfigurovaný pomocí Azure Powershellu.  Nejsou podporovány v portálu Azure portal nebo rozhraní příkazového řádku Azure.

```powershell
# Lock a DNS record set
New-AzureRmResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Ochrana před odstraněním zóny

Při odstranění zóny v Azure DNS se také odstraní všechny sady záznamů v zóně.  Tato operace se nedá vrátit zpět.  Náhodnému odstranění důležitých zóny se může mít významný dopad.  Proto je důležité k ochraně před náhodným odstraněním zóny.

Použití DoNotDelete zámek na zónu zabraňuje zóny odstraňuje.  Ale protože zámky jsou děděné podřízenými prostředky, rovněž nemůže žádné sady záznamů v zóně odstranit, což může mít nežádoucí.  Jak je popsáno v poznámce výše, je navíc také neúčinná vzhledem k tomu, že záznamy se stále dají odebírat z existující sady záznamů.

Jako alternativu jestli nebude lepší uplatňovat DoNotDelete zámek pro sady záznamů v zóně, jako jsou sady záznamů SOA.  Protože zóně není možné odstranit bez odstranění také sady záznamů, je to ochrana proti odstraněním zóny, zároveň umožní sady záznamů v zóně libovolně měnit. Pokud je proveden pokus o odstranění zóny, Azure Resource Manageru detekuje to by také odstranit sady záznamů SOA a blokuje volání, protože je uzamčen SOA.  Odstraní se žádné sady záznamů.

Následující příkaz Powershellu vytvoří DoNotDelete zámek proti záznamu SOA zadané zóny:

```powershell
# Protect against zone delete with DoNotDelete lock on the record set
New-AzureRmResourceLock -LockLevel DoNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Dalším způsobem, jak zabránit náhodným odstraněním zóny, je použití vlastní roli, abyste zajistili operátor a účty služeb používané ke správě zón nemáte oprávnění odstranit zónu. Když budete potřebovat k odstranění zóny, můžete vynutit odstranění dvoustupňové, první udělení oprávnění zóny delete (v oboru zóny, abyste zabránili odstranění nesprávné zóny) a druhé odstranění zóny.

Druhý přístup má výhodu, že funguje pro všechny zóny přistupuje tyto účty, aniž byste museli nezapomeňte vytvořit žádné zámky. Má nevýhodou, že všechny účty s oprávněními odstranění zóny, jako je například vlastník předplatného můžete stále omylem odstranit kritické zónu.

Je možné používat oba přístupy - zámky prostředků a vlastní role - ve stejnou dobu jako v obrany přístup k ochraně zóny DNS.

## <a name="next-steps"></a>Další postup

* Další informace o práci s RBAC najdete v tématu [Začínáme se správou přístupu na webu Azure Portal](../role-based-access-control/overview.md).
* Další informace o práci se zámky prostředků najdete v tématu [zamknutí prostředků pomocí Azure Resource Manageru](../azure-resource-manager/resource-group-lock-resources.md).

