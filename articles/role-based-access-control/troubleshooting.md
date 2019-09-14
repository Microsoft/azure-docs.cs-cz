---
title: Řešení potíží s RBAC pro prostředky Azure | Microsoft Docs
description: Řešení potíží s řízením přístupu na základě role (RBAC) pro prostředky Azure.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: eb7c2cb4a20c89f3a54f23e354d56e5dc3711ef0
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967103"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Řešení potíží s RBAC pro prostředky Azure

Tento článek obsahuje odpovědi na běžné dotazy týkající se řízení přístupu na základě role (RBAC) pro prostředky Azure, takže víte, co očekávat při používání rolí v Azure Portal a který může řešit problémy s přístupem.

## <a name="problems-with-rbac-role-assignments"></a>Potíže s přiřazováním rolí RBAC

- Pokud nemůžete přidat přiřazení role v Azure Portal na **řízení přístupu (IAM)** , protože možnost **Přidat** > **přiřazení role přidání** je zakázaná nebo se zobrazí chyba oprávnění "klient s ID objektu nemá autorizace k provedení akce: Ověřte, že jste aktuálně přihlášeni jako uživatel, kterému je přiřazena role s `Microsoft.Authorization/roleAssignments/write` oprávněním, jako je [vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatele](built-in-roles.md#user-access-administrator) v oboru, ke kterému se snažíte přiřadit roli.
- Pokud se zobrazí chybová zpráva "nelze vytvořit další přiřazení rolí (kód: RoleAssignmentLimitExceeded), zkuste snížit počet přiřazení rolí tím, že místo toho přiřadíte role ke skupinám. Azure podporuje až **2 000** přiřazení rolí na jedno předplatné.

## <a name="problems-with-custom-roles"></a>Potíže s vlastními rolemi

- Pokud potřebujete postup, jak vytvořit vlastní roli, přečtěte si kurzy k vlastním rolím pomocí [Azure PowerShell](tutorial-custom-role-powershell.md) nebo [Azure CLI](tutorial-custom-role-cli.md).
- Pokud nemůžete aktualizovat existující vlastní roli, ověřte, že jste aktuálně přihlášeni jako uživatel, kterému je přiřazena role s `Microsoft.Authorization/roleDefinition/write` oprávněním, jako je [vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator).
- Pokud se vám nedaří odstranit vlastní roli a zobrazuje se chybová zpráva Na roli odkazují stávající přiřazení rolí (kód: RoleDefinitionHasAssignments), znamená to, že vlastní roli stále používají některá přiřazení rolí. Odeberte tato přiřazení rolí a zkuste vlastní roli odstranit znovu.
- Pokud se při pokusu o vytvoření nové vlastní role zobrazí chybová zpráva Došlo k překročení limitu definic rolí. Nelze vytvořit žádné další definice rolí (kód: RoleDefinitionLimitExceeded) "při pokusu o vytvoření nové vlastní role odstraňte všechny vlastní role, které se nepoužívají. Azure podporuje v tenantovi až **5000** vlastních rolí. (V případě specializovaných cloudů, jako je Azure Government, Azure Germany a Azure China 21Vianet, platí omezení 2 000 vlastních rolí.)
- Pokud se zobrazí chybová zpráva podobná "" klient má oprávnění k provedení akce "Microsoft. Authorization/roleDefinitions/Write" v oboru "/Subscriptions/{SubscriptionId}", ale propojené předplatné nebylo nalezeno "při pokusu o aktualizaci vlastní role, ověřte zda byl v tenantovi odstraněn jeden nebo více [oborů](role-definitions.md#assignablescopes) s přiřazením. Pokud se obor odstranil, vytvořte lístek podpory, protože v současné době není k dispozici žádné samoobslužné řešení.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Obnovení RBAC při přesouvání předplatných mezi tenanty

- Pokud potřebujete postup, jak přenést předplatné do jiného tenanta Azure AD, přečtěte si téma [přenos vlastnictví předplatného Azure na jiný účet](../billing/billing-subscription-transfer.md).
- Když převedete předplatné do jiného tenanta Azure AD, všechna přiřazení rolí se odstraní ze zdrojového tenanta Azure AD a nedojde k jejich migraci do cílového tenanta Azure AD. Přiřazení rolí je potřeba v cílovém tenantovi znovu vytvořit. Je také nutné ručně znovu vytvořit spravované identity pro prostředky Azure. Další informace najdete v tématu [Nejčastější dotazy a známé problémy se spravovanými identitami](../active-directory/managed-identities-azure-resources/known-issues.md).
- Pokud jste globálním správcem služby Azure AD a nemáte přístup k předplatnému po jeho přesunutí mezi klienty, použijte přepínač **Správa přístupu pro prostředky Azure** , abyste dočasně nastavili přístup [](elevate-access-global-admin.md) k předplatnému, abyste získali přístup k předplatnému.

## <a name="issues-with-service-admins-or-co-admins"></a>Problémy se správci služeb nebo spolusprávci

- Pokud máte problémy se správcem a spolusprávci služeb, přečtěte si téma [Přidání nebo změna správců předplatného Azure](../billing/billing-add-change-azure-subscription-administrator.md) a [role správce pro klasický odběr, role Azure RBAC a role správce Azure AD](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Odepřený přístup nebo chyby oprávnění

- Pokud při pokusu o vytvoření prostředku dojde k chybě oprávnění Klient s ID objektu nemá oprávnění k provedení akce v oboru (kód: AuthorizationFailed), zkontrolujte, že jste přihlášeni jako uživatel s přiřazenou rolí s oprávněním k zápisu pro prostředek ve vybraném oboru. Pokud například chcete spravovat virtuální počítače ve skupině prostředků, měli byste mít roli [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) pro danou skupinu prostředků (nebo nadřazený obor). Seznam oprávnění jednotlivých předdefinovaných rolí najdete v tématu [Předdefinované role pro prostředky Azure](built-in-roles.md).
- Pokud se zobrazí chyba oprávnění "nemáte oprávnění k vytvoření žádosti o podporu" při pokusu o vytvoření nebo aktualizaci lístku podpory, ověřte, zda jste aktuálně přihlášeni pomocí uživatele, kterému je přiřazena role `Microsoft.Support/supportTickets/write` s oprávněním, například [ Přispěvatel žádostí o podporu](built-in-roles.md#support-request-contributor).

## <a name="role-assignments-without-a-security-principal"></a>Přiřazení rolí bez objektu zabezpečení

Když vypíšete přiřazení rolí pomocí Azure PowerShell, můžou se zobrazit přiřazení s prázdným `DisplayName` `ObjectType` a nastaveným na neznámý. Například [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) vrátí přiřazení role, které je podobné následujícímu:

```azurepowershell
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Podobně platí, že při zobrazení seznamu přiřazení rolí pomocí rozhraní příkazového řádku Azure se může zobrazit přiřazení `principalName`s prázdným řetězcem. Například [AZ role Assignment list](/cli/azure/role/assignment#az-role-assignment-list) vrátí přiřazení role, které je podobné následujícímu:

```azurecli
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Tato přiřazení rolí nastávají, když přiřadíte roli objektu zabezpečení (uživatele, skupiny, instančnímu objektu nebo spravované identitě) a později tento objekt zabezpečení odstraníte. Tato přiřazení rolí nejsou zobrazena v Azure Portal a nejedná se o problém, který by je mohl opustit. Pokud ale chcete, můžete tato přiřazení rolí odebrat.

Pokud chcete tato přiřazení role odebrat, použijte příkazy [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) nebo [AZ role Assignment Delete](/cli/azure/role/assignment#az-role-assignment-delete) .

Pokud se v prostředí PowerShell pokusíte odebrat přiřazení rolí pomocí názvu objektu a definice role a víc než jedno přiřazení role odpovídá vašim parametrům, zobrazí se chybová zpráva: "Zadané informace nejsou namapovány na přiřazení role". V následujícím příkladu vidíte příklad chybové zprávy:

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Pokud se zobrazí tato chybová zpráva, ujistěte se, že jste zadali `-Scope` také `-ResourceGroupName` parametry nebo.

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>Nezjišťují se změny RBAC.

Azure Resource Manager někdy ukládá do mezipaměti konfigurace a data pro zlepšení výkonu. Při vytváření nebo odstraňování přiřazení rolí může trvat až 30 minut, než se změny projeví. Pokud používáte Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure, můžete vynutit aktualizaci změn přiřazení role odhlášením a přihlášením. Pokud provádíte změny přiřazení rolí pomocí REST API volání, můžete vynutit aktualizaci pomocí aktualizace přístupového tokenu.

## <a name="web-app-features-that-require-write-access"></a>Funkce webové aplikace, které vyžadují přístup pro zápis

Pokud uživateli udělíte přístup jen pro čtení k jedné webové aplikaci, některé funkce jsou zakázané, které nemůžete očekávat. Následující funkce správy vyžadují přístup pro **zápis** do webové aplikace (přispěvatel nebo vlastník) a nejsou k dispozici ve scénáři jen pro čtení.

* Příkazy (například Start, stop atd.)
* Změna nastavení, jako je Obecná konfigurace, nastavení škálování, nastavení zálohování a nastavení monitorování
* Přístup k přihlašovacím údajům pro publikování a dalším tajným klíčům, jako je nastavení aplikace
* Protokoly streamování
* Konfigurace diagnostických protokolů
* Konzola (příkazový řádek)
* Aktivní a poslední nasazení (pro místní průběžné nasazování Git)
* Předpokládaná útrata
* Webové testy
* Virtuální síť (viditelná jenom pro čtenáře, pokud je virtuální síť dřív nakonfigurovaná uživatelem s přístupem pro zápis).

Pokud nemůžete získat přístup k žádné z těchto dlaždic, musíte požádat správce o přístup k webové aplikaci přes přispěvatele.

## <a name="web-app-resources-that-require-write-access"></a>Prostředky webové aplikace, které vyžadují přístup pro zápis

Webové aplikace jsou komplikované o přítomnost několika různých prostředků, které souhře. Tady je typickou skupinou prostředků s několika weby:

![Skupina prostředků webové aplikace](./media/troubleshooting/website-resource-model.png)

Výsledkem je, že pokud udělíte někomu přístup jenom k webové aplikaci, je většina funkcí v okně webu v Azure Portal zakázaná.

Tyto položky vyžadují přístup pro **zápis** do **plánu App Service** , který odpovídá vašemu webu:  

* Zobrazení cenové úrovně webové aplikace (zdarma nebo Standard)  
* Konfigurace škálování (počet instancí, velikost virtuálního počítače, nastavení automatického škálování)  
* Kvóty (úložiště, Šířka pásma, CPU)  

Tyto položky vyžadují přístup pro **zápis** do celé **skupiny prostředků** , která obsahuje váš web:  

* Certifikáty a vazby SSL (certifikáty SSL se dají sdílet mezi lokalitami ve stejné skupině prostředků a geografickým umístěním)  
* Pravidla výstrah  
* nastavení automatického škálování  
* Komponenty Application Insights  
* Webové testy  

## <a name="virtual-machine-features-that-require-write-access"></a>Funkce virtuálních počítačů, které vyžadují přístup pro zápis

Podobně jako u Web Apps vyžadují některé funkce v okně virtuálního počítače přístup pro zápis k virtuálnímu počítači nebo jiným prostředkům ve skupině prostředků.

Virtuální počítače se týkají názvů domén, virtuálních sítí, účtů úložiště a pravidel výstrah.

Tyto položky vyžadují pro **virtuální počítač**přístup pro **zápis** :

* Koncové body  
* IP adresy  
* Disky  
* Rozšíření  

Tyto požadavky vyžadují přístup pro **zápis** k **virtuálnímu počítači**a **skupině prostředků** (spolu s názvem domény), ve kterém se nachází:  

* Skupina dostupnosti  
* Skupina s vyrovnáváním zatížení  
* Pravidla výstrah  

Pokud nemůžete získat přístup k žádné z těchto dlaždic, požádejte správce, aby přístup přispěvatele k této skupině prostředků.

## <a name="azure-functions-and-write-access"></a>Azure Functions a přístup pro zápis

Některé funkce [Azure Functions](../azure-functions/functions-overview.md) vyžadují přístup pro zápis. Například pokud je uživateli přiřazena role [čtenáře](built-in-roles.md#reader) , nebude moci zobrazit funkce v rámci aplikace Function App. Portál se zobrazí **(bez přístupu)** .

![Aplikace Function App bez přístupu](./media/troubleshooting/functionapps-noaccess.png)

Čtenář může kliknout na kartu **funkce platformy** a potom kliknout na **všechna nastavení** a zobrazit některá nastavení týkající se aplikace Function App (podobně jako u webové aplikace), ale nemůžou upravovat žádná z těchto nastavení. Pro přístup k těmto funkcím budete potřebovat roli [Přispěvatel](built-in-roles.md#contributor) .

## <a name="next-steps"></a>Další kroky

- [Řešení potíží pro uživatele typu Host](role-assignments-external-users.md#troubleshoot)
- [Správa přístupu k prostředkům Azure pomocí RBAC a webu Azure Portal](role-assignments-portal.md)
- [Zobrazení protokolů aktivit pro změny RBAC v prostředcích Azure](change-history-report.md)

