---
title: Poradce při potížích s Azure RBAC
description: Řešení problémů s řízením přístupu na základě rolí Azure (Azure RBAC).
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 8aedc78772858815a18425fb1e6cb36a4600f647
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385091"
---
# <a name="troubleshoot-azure-rbac"></a>Poradce při potížích s Azure RBAC

Tento článek odpovídá na některé běžné otázky týkající se řízení přístupu na základě rolí Azure (Azure RBAC), takže víte, co očekávat při používání rolí a můžete řešit problémy s přístupem.

## <a name="azure-role-assignments-limit"></a>Omezení přiřazení rolí Azure

Azure podporuje až **2 000** přiřazení rolí na jedno předplatné. Pokud se zobrazí chybová zpráva "Nelze vytvořit žádné další přiřazení rolí (kód: RoleAssignmentLimitExceeded)" při pokusu o přiřazení role, zkuste snížit počet přiřazení rolí v předplatném.

> [!NOTE]
> Limit přiřazení rolí **2000** na jedno předplatné je pevný a nelze jej zvýšit.

Pokud se blížíte k tomuto limitu, můžete snížit počet přiřazení rolí:

- Přidejte uživatele do skupin a místo toho přiřaďte skupinám role. 
- Zkombinujte více předdefinovaných rolí s vlastní rolí. 
- Proveďte společná přiřazení rolí ve vyšším oboru, jako je například předplatné nebo skupina pro správu.
- Pokud máte Azure AD Premium P2, aby přiřazení rolí způsobilé v [Azure AD Privilegované Správy identit](../active-directory/privileged-identity-management/pim-configure.md) namísto trvale přiřazené. 
- Přidejte další předplatné. 

Chcete-li získat počet přiřazení rolí, můžete graf zobrazit [na stránce řízení přístupu (IAM) na](role-assignments-list-portal.md#list-number-of-role-assignments) webu Azure Portal. Můžete taky použít následující příkazy Azure PowerShellu:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problémy s přiřazením rolí Azure

- Pokud nemůžete přidat přiřazení role na portálu Azure na **řízení přístupu (IAM),** protože možnost **Přidat** > **přiřazení role** je zakázána nebo protože se zobrazí chyba oprávnění "Klient s ID objektu nemá oprávnění `Microsoft.Authorization/roleAssignments/write` k provedení akce", zkontrolujte, že jste aktuálně přihlášeni k uživateli, kterému je přiřazena role, která má oprávnění, [jako](built-in-roles.md#owner) je vlastník nebo správce [přístupu uživatele](built-in-roles.md#user-access-administrator) v oboru, který se pokoušíte přiřadit roli.

## <a name="problems-with-custom-roles"></a>Potíže s vlastními rolemi

- Pokud potřebujete kroky, jak vytvořit vlastní roli, podívejte se na kurzy vlastní role pomocí [portálu Azure](custom-roles-portal.md) (aktuálně ve verzi), [Azure PowerShell](tutorial-custom-role-powershell.md)nebo [Azure CLI](tutorial-custom-role-cli.md).
- Pokud nemůžete aktualizovat existující vlastní roli, zkontrolujte, zda jste aktuálně přihlášeni k uživateli, kterému je přiřazena role, která má `Microsoft.Authorization/roleDefinition/write` oprávnění, například [Vlastník](built-in-roles.md#owner) nebo Správce [přístupu uživatele](built-in-roles.md#user-access-administrator).
- Pokud se vám nedaří odstranit vlastní roli a zobrazí se chybová zpráva "Existují existující přiřazení rolí odkazující na roli (kód: RoleDefinitionHasAssignments)", pak jsou přiřazení rolí stále používající vlastní roli. Odeberte tato přiřazení rolí a zkuste vlastní roli odstranit znovu.
- Pokud se při pokusu o vytvoření nové vlastní role zobrazí chybová zpráva Došlo k překročení limitu definic rolí. Nelze vytvořit žádné další definice rolí (kód: RoleDefinitionLimitExceeded)" při pokusu o vytvoření nové vlastní role odstraňte všechny vlastní role, které nejsou používány. Azure podporuje až **5000** vlastních rolí v adresáři. (Pro Azure Germany a Azure China 21Vianet je limit 2000 vlastnírole.)
- Pokud se zobrazí chyba podobná "Klient má oprávnění k provedení akce Microsoft.Authorization/roleDefinitions/write' na oboru '/subscriptions/{subscriptionid}', ale propojené předplatné nebylo nalezeno při pokusu o aktualizaci vlastní role, zkontrolujte, zda byl odstraněn jeden nebo více [přiřaditelných oborů](role-definitions.md#assignablescopes) v adresáři. Pokud se obor odstranil, vytvořte lístek podpory, protože v současné době není k dispozici žádné samoobslužné řešení.

## <a name="custom-roles-and-management-groups"></a>Vlastní role a skupiny pro správu

- Ve vlastní roli můžete `AssignableScopes` definovat pouze jednu skupinu pro správu. Přidání skupiny `AssignableScopes` pro správu do aplikace je aktuálně ve verzi Preview.
- Vlastní role `DataActions` s nelze přiřadit v oboru skupiny pro správu.
- Azure Resource Manager neověřuje existenci skupiny pro správu v přiřaditelném oboru definice role.
- Další informace o vlastních rolích a skupinách pro správu najdete [v tématu Uspořádání prostředků pomocí skupin pro správu Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="transferring-a-subscription-to-a-different-directory"></a>Přenos předplatného do jiného adresáře

- Pokud potřebujete kroky, jak převést předplatné do jiného adresáře Azure AD, přečtěte si část [Převést vlastnictví předplatného Azure na jiný účet](../cost-management-billing/manage/billing-subscription-transfer.md).
- Pokud přenesete předplatné do jiného adresáře Azure AD, všechna přiřazení rolí se **trvale** odstraní ze zdrojového adresáře Azure AD a nejsou migrovány do cílového adresáře Azure AD. Přiřazení rolí je nutné znovu vytvořit v cílovém adresáři. Taky je potřeba ručně znovu vytvořit spravované identity pro prostředky Azure. Další informace naleznete v [častých dotazech a známých problémech se spravovanými identitami](../active-directory/managed-identities-azure-resources/known-issues.md).
- Pokud jste globální správce Azure AD a nemáte přístup k předplatnému poté, co byl převeden mezi adresáři, použijte správce **přístupu pro prostředky Azure** přepnout dočasně [zvýšit váš přístup](elevate-access-global-admin.md) k získání přístupu k předplatnému.

## <a name="issues-with-service-admins-or-co-admins"></a>Problémy se správci služeb nebo spolusprávci

- Pokud máte problémy se správcem služby nebo spolusprávci, přečtěte si informace o [přidání nebo změně správců předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) a [klasických rolí správce předplatného, rolí Azure a rolí správce Azure AD](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Odepření přístupu nebo chyby oprávnění

- Pokud se při pokusu o vytvoření prostředku zobrazí chyba oprávnění "Klient s ID objektu nemá oprávnění k provádění akce nad oborem (kód: AuthorizationFailed)", zkontrolujte, zda jste aktuálně přihlášeni k uživateli, kterému je přiřazena role, která má zápis oprávnění k prostředku ve vybraném oboru. Pokud například chcete spravovat virtuální počítače ve skupině prostředků, měli byste mít roli [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) pro danou skupinu prostředků (nebo nadřazený obor). Seznam oprávnění jednotlivých předdefinovaných rolí najdete v tématu [Předdefinované role pro prostředky Azure](built-in-roles.md).
- Pokud se při pokusu o vytvoření nebo aktualizaci lístku podpory zobrazí chyba oprávnění "Nemáte oprávnění k vytvoření žádosti o podporu", zkontrolujte, `Microsoft.Support/supportTickets/write` zda jste aktuálně přihlášeni k uživateli, kterému je přiřazena role, která má oprávnění, například [Přispěvatel žádosti o podporu](built-in-roles.md#support-request-contributor).

## <a name="role-assignments-with-unknown-security-principal"></a>Přiřazení rolí s neznámým objektem zabezpečení

Pokud přiřadíte roli objektu zabezpečení (uživatel, skupina, instanční objekt nebo spravovaná identita) a později tento objekt zabezpečení odstraníte bez odebrání přiřazení role, bude typ objektu zabezpečení pro přiřazení role uveden jako **Neznámý**. Následující snímek obrazovky ukazuje příklad na webu Azure Portal. Hlavní název zabezpečení je uveden jako **identita odstraněna** a **identita již neexistuje**. 

![Skupina prostředků webové aplikace](./media/troubleshooting/unknown-security-principal.png)

Pokud uvedete toto přiřazení role pomocí Azure `DisplayName` PowerShellu, zobrazí se prázdná a nastavená `ObjectType` na Neznámý. [Například Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) vrátí přiřazení role, která je podobná následující:

```
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

Podobně pokud uvedete toto přiřazení role pomocí azure `principalName`CLI, zobrazí se prázdný . [Například seznam přiřazení role az](/cli/azure/role/assignment#az-role-assignment-list) vrátí přiřazení role, které je podobné následujícímu:

```
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

Ponechat tato přiřazení rolí není problém, ale můžete je odebrat pomocí kroků, které jsou podobné jiným přiřazením rolí. Informace o tom, jak odebrat přiřazení rolí, najdete [v tématu Azure Portal](role-assignments-portal.md#remove-a-role-assignment), Azure [PowerShell](role-assignments-powershell.md#remove-a-role-assignment)nebo [Azure CLI](role-assignments-cli.md#remove-a-role-assignment)

Pokud se v prostředí PowerShell pokusíte odebrat přiřazení rolí pomocí ID objektu a názvu definice role a více než jedno přiřazení role odpovídá vašim parametrům, zobrazí se chybová zpráva"Poskytnuté informace se nemapují na přiřazení role". Následující příklad chybové zprávy:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Pokud se zobrazí tato chybová zpráva, `-Scope` `-ResourceGroupName` ujistěte se, že také zadáte parametry nebo.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Změny přiřazení rolí nejsou zjištěny

Azure Resource Manager někdy ukládá do mezipaměti konfigurace a data pro zlepšení výkonu. Když přidáte nebo odeberete přiřazení rolí, může trvat až 30 minut, než se změny projeví. Pokud používáte portál Azure, Azure PowerShell nebo Azure CLI, můžete vynutit aktualizaci změn přiřazení role po přihlášení a přihlášení. Pokud provádíte změny přiřazení rolí pomocí volání rozhraní REST API, můžete vynutit aktualizaci aktualizací přístupového tokenu.

Pokud přidáte nebo odeberete přiřazení role v oboru `DataActions`skupiny pro správu a role má , nemusí být přístup k rovině dat aktualizován několik hodin. To platí pouze pro obor skupiny pro správu a rovinu dat.

## <a name="web-app-features-that-require-write-access"></a>Funkce webové aplikace, které vyžadují přístup pro zápis

Pokud uživateli udělíte přístup jen pro čtení k jedné webové aplikaci, některé funkce jsou zakázány, což neočekáváte. Následující možnosti správy vyžadují přístup **pro zápis** do webové aplikace (přispěvatel nebo vlastník) a nejsou k dispozici v žádném scénáři jen pro čtení.

* Příkazy (jako start, stop atd.)
* Změna nastavení, jako je obecná konfigurace, nastavení škálování, nastavení zálohování a nastavení monitorování
* Přístup k přihlašovacím údajům pro publikování a dalším tajným klíčům, jako je nastavení aplikace a připojovací řetězce
* Streamovací protokoly
* Konfigurace diagnostických protokolů
* Konzola (příkazový řádek)
* Aktivní a poslední nasazení (pro lokální git průběžné nasazení)
* Odhadované výdaje
* Webové testy
* Virtuální síť (viditelné pouze pro čtenáře, pokud virtuální síť byla dříve nakonfigurována uživatelem s přístupem pro zápis).

Pokud nemáte přístup k žádné z těchto dlaždic, musíte požádat správce o přístup přispěvatele k webové aplikaci.

## <a name="web-app-resources-that-require-write-access"></a>Prostředky webové aplikace, které vyžadují přístup pro zápis

Webové aplikace jsou komplikovány přítomností několika různých prostředků, které se souhrají. Zde je typická skupina prostředků s několika webovými stránkami:

![Skupina prostředků webové aplikace](./media/troubleshooting/website-resource-model.png)

V důsledku toho pokud někomu udělíte přístup pouze k webové aplikaci, velká část funkcí v okně webu na webu na portálu Azure je zakázána.

Tyto položky vyžadují přístup **pro zápis** do **plánu služby App Service,** který odpovídá vašemu webu:  

* Zobrazení cenové úrovně webové aplikace (zdarma nebo standardní)  
* Konfigurace škálování (počet instancí, velikost virtuálního počítače, nastavení automatického škálování)  
* Kvóty (úložiště, šířka pásma, CPU)  

Tyto položky vyžadují přístup **pro zápis** do celé **skupiny prostředků,** která obsahuje váš web:  

* Certifikáty a vazby SSL (certifikáty SSL lze sdílet mezi sítěmi ve stejné skupině prostředků a geografickém umístění)  
* Pravidla výstrah  
* Nastavení automatického škálování  
* Součásti přehledů aplikací  
* Webové testy  

## <a name="virtual-machine-features-that-require-write-access"></a>Funkce virtuálního počítače, které vyžadují přístup pro zápis

Podobně jako webové aplikace, některé funkce v okně virtuálního počítače vyžadují přístup pro zápis do virtuálního počítače nebo k jiným prostředkům ve skupině prostředků.

Virtuální počítače se vztahují k názvy domén, virtuální sítě, účty úložiště a pravidla výstrah.

Tyto položky vyžadují přístup **pro zápis** do **virtuálního počítače**:

* Koncové body  
* IP adresy  
* Disky  
* Rozšíření  

Ty vyžadují přístup **pro zápis** do **virtuálního počítače**a **skupiny prostředků** (spolu s názvem domény), ve které se nachází:  

* Skupina dostupnosti  
* Sada pro vyrovnávání zatížení  
* Pravidla výstrah  

Pokud nemáte přístup k žádné z těchto dlaždic, požádejte správce o přístup přispěvatele ke skupině prostředků.

## <a name="azure-functions-and-write-access"></a>Azure Functions a přístup pro zápis

Některé funkce [Azure Functions](../azure-functions/functions-overview.md) vyžadují přístup pro zápis. Pokud je například uživateli přiřazena role [Čtečka,](built-in-roles.md#reader) nebude moci zobrazit funkce v rámci aplikace funkce. Na portálu se zobrazí **(Bez přístupu).**

![Aplikace pro funkce bez přístupu](./media/troubleshooting/functionapps-noaccess.png)

Čtenář může kliknout na kartu **Funkce platformy** a potom kliknutím na **Všechna nastavení** zobrazit některá nastavení související s aplikací funkcí (podobně jako webová aplikace), ale nemůže změnit žádné z těchto nastavení. Chcete-li získat přístup k těmto funkcím, budete potřebovat roli [přispěvatele.](built-in-roles.md#contributor)

## <a name="next-steps"></a>Další kroky

- [Poradce při potížích pro uživatele typu Host](role-assignments-external-users.md#troubleshoot)
- [Správa přístupu k prostředkům Azure pomocí RBAC a webu Azure Portal](role-assignments-portal.md)
- [Zobrazení protokolů aktivit pro změny RBAC prostředků Azure](change-history-report.md)
