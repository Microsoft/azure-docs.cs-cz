---
title: Ochrana zóny DNS a záznamy | Microsoft Docs
description: Jak chránit zóny DNS a sady záznamů v Microsoft Azure DNS.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 190e69eb-e820-4fc8-8e9a-baaf0b3fb74a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2016
ms.author: kumud
ms.openlocfilehash: d13556eb274990eadf58070c119a3e9960b90699
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="how-to-protect-dns-zones-and-records"></a>Jak chránit zóny DNS a záznamy

Zóny DNS a záznamy jsou důležité prostředky. Odstraňování zónu DNS, nebo jenom jeden záznam DNS, může mít za následek výpadku celkový služeb.  Proto je důležité, aby kritické zóny DNS a záznamy chráněná před neoprávněným nebo náhodné změny.

Tento článek vysvětluje, jak Azure DNS umožňuje chránit zóny DNS a záznamy proti tyto změny.  Jsme použít dvě funkce efektivní zabezpečení poskytované pomocí Správce prostředků Azure: [řízení přístupu na základě role](../role-based-access-control/overview.md) a [uzamčení prostředků](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Azure na základě rolí řízení přístupu (RBAC) umožňuje přesnou správu přístupu pro Azure uživatele, skupiny a prostředky. Pomocí RBAC, můžete udělit přesněji úroveň přístupu, aby uživatelé potřebují k provádění svých úloh. Další informace o tom, jak AZURE pomůže spravovat přístup, najdete v části [co je řízení přístupu na základě Role](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>Role, Přispěvatel zóny DNS.

Role, Přispěvatel zóny DNS, je předdefinovaná role, poskytovaný platformou Azure pro správu prostředků služby DNS.  Přiřazení oprávnění přispěvatele zóny DNS na uživatele nebo skupinu umožňuje této skupiny pro správu prostředků DNS, ale ne prostředky žádným jiným typem.

Předpokládejme například, že skupina prostředků 'myzones' obsahuje pět zóny pro společnost Contoso. Udělení Správce DNS, Přispěvatel zóny DNS, oprávnění k této skupině zdrojů, umožňuje plnou kontrolu nad tyto zóny DNS. Také se vyhnete, udělení nadbytečná oprávnění, například Správce DNS nelze vytvořit nebo zastavit virtuální počítače.

Nejjednodušší způsob, jak přiřadit oprávnění RBAC je [prostřednictvím portálu Azure](../role-based-access-control/role-assignments-portal.md).  Otevře se okno, řízení přístupu (IAM)' pro skupinu prostředků, pak klikněte na tlačítko "Přidat", pak vyberte roli, Přispěvatel zóny DNS a vyberte požadované uživatele nebo skupiny, které chcete udělit oprávnění.

![Úrovni skupiny prostředků RBAC prostřednictvím portálu Azure](./media/dns-protect-zones-recordsets/rbac1.png)

Oprávnění může být také [udělena pomocí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

Také je ekvivalentní příkaz [prostřednictvím rozhraní příkazového řádku Azure k dispozici](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>Úroveň zóny RBAC

Azure RBAC pravidla lze použít pro předplatné, skupinu prostředků nebo pro jednotlivé zdroje. V případě Azure DNS může být tento prostředek jednotlivé zóny DNS, nebo i jednotlivé sady záznamů.

Předpokládejme například, že skupiny prostředků, myzones' obsahuje zóny contoso.com a subzone 'customers.contoso.com, ve kterém jsou vytvořeny záznamy CNAME pro každou účtu zákazníka.  Účet použitý ke správě tyto záznamy CNAME by se měla přiřadit oprávnění k vytváření záznamů v zóně 'customers.contoso.com', by neměl mít přístup k jiné zóně.

Prostřednictvím portálu Azure můžete udělit oprávnění RBAC na úrovni zóny.  Otevře se okno, řízení přístupu (IAM)"pro zónu, pak klikněte na tlačítko"Přidat", pak vyberte roli, Přispěvatel zóny DNS a vyberte požadované uživatele nebo skupiny, které chcete udělit oprávnění.

![Zóna DNS úrovně RBAC prostřednictvím portálu Azure](./media/dns-protect-zones-recordsets/rbac2.png)

Oprávnění může být také [udělena pomocí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

Také je ekvivalentní příkaz [prostřednictvím rozhraní příkazového řádku Azure k dispozici](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>Úroveň RBAC sady záznamů

Jeden krok jsme může pokračovat dál. Vezměte v úvahu e-mailu správce pro společnosti Contoso Corporation, který potřebuje přístup k MX a TXT záznamů na vrcholu zónu "contoso.com".  Jana nepotřebuje přístup k jiné MX nebo TXT záznamů, nebo všechny záznamy o jiný typ.  Azure DNS umožňuje přiřadit oprávnění na úrovni sady záznamů, přesněji na záznamy, které potřebuje přístup k e-mailu správce.  E-mailu správce má oprávnění přesně řídit, která potřebuje a nemůže provést další změny.

Oprávnění na úrovni RBAC sadu záznamů, můžete nakonfigurovat prostřednictvím portálu Azure, pomocí tlačítka 'Uživatele' v okně Sada záznamů:

![Sady záznamů úroveň RBAC prostřednictvím portálu Azure](./media/dns-protect-zones-recordsets/rbac3.png)

Může být také sadu záznamů oprávnění na úrovni RBAC [udělena pomocí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```powershell
# Grant permissions to a specific record set
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

Také je ekvivalentní příkaz [prostřednictvím rozhraní příkazového řádku Azure k dispozici](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Vlastní role

Předdefinovaná role, Přispěvatel zóny DNS, umožňuje plnou kontrolu nad prostředků DNS. Je také možné vytvořit vlastní zákazník Azure role, které zajišťují řízení i citlivější.

Zvažte znovu příklad, ve kterém se vytvoří záznam CNAME v zóně 'customers.contoso.com' u každého účtu zákazníka Contoso Corporation.  Účet použitý ke správě těchto záznamů CNAME musí udělit oprávnění ke správě pouze záznamy CNAME.  Pak se nepodařilo upravit záznamy o dalších typů (jako je například změna záznamů MX) nebo provádět operace na úrovni zóny například odstranit zónu.

Následující příklad ukazuje definice vlastních rolí pro správu pouze záznamy CNAME:

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

Vlastnost akce definuje následující DNS konkrétní oprávnění:

* `Microsoft.Network/dnsZones/CNAME/*` uděluje plnou kontrolu nad záznamy CNAME
* `Microsoft.Network/dnsZones/read` uděluje oprávnění ke čtení zóny DNS, ale není o jejich úpravu, umožňuje najdete v části zónu, ve kterém se vytváří CNAME.

Zbývající akce, které jsou zkopírovány ze [předdefinovaná role Přispěvatel zóny DNS](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Aby se zabránilo odstranění sady záznamů, zatímco stále což jim umožní aktualizovat není efektivní řízení pomocí vlastní role RBAC. Sady záznamů zabrání odstraňuje, ale nezabrání je upravovat.  Povolené změny zahrnují přidávání a odebírání záznamů ze sady záznamů, včetně odebrat všechny záznamy chcete nechat 'prázdná' sada záznamů. Tato akce nemá stejný účinek jako odstranění sady z hlediska rozlišení DNS záznamů.

Definice vlastní role nemůže být definovaný aktuálně prostřednictvím portálu Azure. Pomocí Azure Powershellu lze vytvořit vlastní role založená na této definici role:

```powershell
# Create new role definition based on input file
New-AzureRmRoleDefinition -InputFile <file path>
```

Také možné vytvářet prostřednictvím rozhraní příkazového řádku Azure:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

Role pak lze přiřadit stejným způsobem jako pro předdefinované role, jak je popsáno výše v tomto článku.

Další informace o tom, jak vytvářet, spravovat a přiřadit vlastní role, naleznete v části [vlastní role v Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Zámky prostředků

Kromě RBAC Azure Resource Manager podporuje jiný typ řízení zabezpečení, a to možnost prostředky 'lock'. Kde RBAC pravidla umožňují řídit akce konkrétní uživatele a skupiny, uzamčení prostředků se použijí k prostředku a jsou platné ve všech uživatelů a rolí. Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](../azure-resource-manager/resource-group-lock-resources.md).

Existují dva typy prostředků zámku: **DoNotDelete** a **jen pro čtení**. Ty lze použít buď na zónu DNS, nebo na sadu záznamů jednotlivých.  Následující části popisují několik běžné scénáře a postupy, které je podporují pomocí uzamčení prostředků.

### <a name="protecting-against-all-changes"></a>Ochrana proti všechny změny

Pokud chcete zabránit změny prováděné, použije zámek jen pro čtení do zóny.  To brání tomu, aby nové sady záznamů se vytvořil a existující sady záznamů upravit nebo odstranit.

Zónu prostředků s úrovní zámky lze vytvořit prostřednictvím portálu Azure.  V okně zóny DNS, klikněte na tlačítko 'Zámky' pak přidejte:

![Zónu prostředků s úrovní zámky prostřednictvím portálu Azure](./media/dns-protect-zones-recordsets/locks1.png)

Úroveň zóny prostředku, který zámky. můžete také vytvořit prostředí Azure PowerShell:

```powershell
# Lock a DNS zone
New-AzureRmResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Konfigurace prostředků Azure zámků není aktuálně podporován prostřednictvím rozhraní příkazového řádku Azure.

### <a name="protecting-individual-records"></a>Ochrana jednotlivých záznamů

Aby se zabránilo existujícího záznamu DNS nastavit pro úpravy, použije zámek jen pro čtení do sady záznamů.

> [!NOTE]
> Použití DoNotDelete zámku na sadu záznamů není efektivní řízení. Zabraňuje před odstraněním sady záznamů, ale nezabrání ho upravovat.  Povolené změny zahrnují přidávání a odebírání záznamů ze sady záznamů, včetně odebrat všechny záznamy chcete nechat 'prázdná' sada záznamů. Tato akce nemá stejný účinek jako odstranění sady z hlediska rozlišení DNS záznamů.

Sady záznamů prostředků s úrovní zámky momentálně můžete být pouze nakonfigurovat pomocí prostředí Azure PowerShell.  Nejsou podporovány v portálu Azure nebo rozhraní příkazového řádku Azure.

```powershell
# Lock a DNS record set
New-AzureRmResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Ochrana proti odstranění zóny

Při odstranění zóny v Azure DNS se také odstraní všechny sady záznamů v zóně.  Tato operace se nedá vrátit zpět.  Nechtěnému odstranění kritické zóny se může mít významné obchodní dopad.  Je proto velmi důležité pro ochranu proti náhodnému zóny odstranění.

Před odstraněním použití DoNotDelete zámku na zónu zabrání zóny.  Ale vzhledem k tomu, že zámky jsou zdědí podřízené prostředky, zabrání také libovolné sady záznamů v zóně před odstraněním, což může být žádoucí.  Kromě toho jak je popsáno v poznámce výše, je také neúčinná vzhledem k tomu, že záznamy lze přesto odebrat z existující sady záznamů.

Jako alternativu zvažte použití DoNotDelete uzamčení záznamů v zóně, jako je například sady záznamů SOA.  Vzhledem k tomu, že zóna nelze odstranit bez také odstranění sady záznamů, je to ochrana proti odstranění zóny, zároveň umožňuje sad záznamů v rámci zóny volně upravovat. Pokud je proveden pokus o odstranit zónu, Azure Resource Manager zjistí, to by také odstranit sadu záznamů SOA a blokuje volání, protože je pevně nastavené SOA.  Žádné sady záznamů se odstraní.

Následující příkaz prostředí PowerShell vytvoří DoNotDelete zámku na záznam SOA dané zóny:

```powershell
# Protect against zone delete with DoNotDelete lock on the record set
New-AzureRmResourceLock -LockLevel DoNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Jiný způsob, jak zabránit náhodnému zóny odstranění je pomocí vlastní role zajistit operátor a účty služby pro správu zón nemáte oprávnění odstranit zónu. Pokud musíte odstranit zónu, můžete vynutit odstranění dvoustupňové, první udělení oprávnění zóny delete (v oboru zóny, aby se zabránilo odstraněním této zóny nesprávný) a druhý odstranit zónu.

Druhý přístup má výhodu, který funguje pro všechny zóny přístup tyto účty, aniž by museli nezapomeňte vytvořit žádné zámky. Obsahuje všechny účty s oprávněními odstranit zónu, jako je vlastník předplatného, můžete stále omylem odstranit zónu kritické nevýhodou.

Je možné použít obou přístupů - uzamčení prostředků a vlastní role - ve stejnou dobu jako obrany zabezpečení přístupu k ochraně zóny DNS.

## <a name="next-steps"></a>Další postup

* Další informace o práci s RBAC najdete v tématu [Začínáme se správou přístupu na portálu Azure](../role-based-access-control/overview.md).
* Další informace o práci s uzamčení prostředků najdete v tématu [zamknutí prostředků pomocí Azure Resource Manageru](../azure-resource-manager/resource-group-lock-resources.md).

