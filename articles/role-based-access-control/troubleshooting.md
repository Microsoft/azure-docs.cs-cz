---
title: Řešení potíží s Azure RBAC
description: Řešení potíží s řízením přístupu na základě role Azure (Azure RBAC)
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1, devx-track-azurecli
ms.openlocfilehash: 53628f5aa0bc5ab5dedde5deb9950c7b13fb4bf6
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490742"
---
# <a name="troubleshoot-azure-rbac"></a>Řešení potíží s Azure RBAC

Tento článek obsahuje odpovědi na některé běžné dotazy týkající se řízení přístupu na základě role Azure (Azure RBAC), abyste věděli, co byste měli očekávat při používání rolí a mohli řešit problémy s přístupem.

## <a name="azure-role-assignments-limit"></a>Omezení přiřazení rolí Azure

Azure podporuje až **2 000** přiřazení rolí na jedno předplatné. Toto omezení zahrnuje přiřazení rolí v rámci předplatného, skupiny prostředků a oborů prostředků. Pokud se zobrazí chybová zpráva "při pokusu o přiřazení role nemůžete vytvořit další přiřazení rolí (Code: RoleAssignmentLimitExceeded)", zkuste snížit počet přiřazení rolí v předplatném.

> [!NOTE]
> Limit přiřazení role **2000** na předplatné je pevně stanoven a nelze ho zvýšit.

Pokud se blížíte k tomuto limitu, tady je několik způsobů, jak můžete snížit počet přiřazení rolí:

- Přidejte uživatele do skupin a místo toho přiřaďte role k těmto skupinám. 
- Kombinací několika předdefinovaných rolí s vlastní rolí. 
- Společná přiřazení rolí je třeba provést v širším rozsahu, jako je například předplatné nebo skupina pro správu.
- Pokud máte Azure AD Premium P2, vyřadí se přiřazení rolí oprávněná v [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) namísto trvale přiřazeno. 
- Přidejte další předplatné. 

Chcete-li získat počet přiřazení rolí, můžete zobrazit [graf na stránce řízení přístupu (IAM)](role-assignments-list-portal.md#list-number-of-role-assignments) v Azure Portal. Můžete také použít následující příkazy Azure PowerShell:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problémy s přiřazením rolí Azure

- Pokud nemůžete přidat přiřazení role v Azure Portal v **řízení přístupu (IAM)** , protože možnost **Přidat**  >  **přiřazení role přidání** je zakázaná nebo se zobrazí chyba oprávnění "klient s ID objektu nemá autorizaci k provedení akce", ověřte, že jste aktuálně přihlášení pomocí uživatele, kterému je přiřazena role s oprávněním, jako je `Microsoft.Authorization/roleAssignments/write` [vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatele](built-in-roles.md#user-access-administrator) v oboru, ke kterému se snažíte přiřadit roli.
- Pokud k přiřazení rolí používáte instanční objekt, může se zobrazit chyba "nedostatečná oprávnění k dokončení operace". Řekněme například, že máte instanční objekt, kterému byla přiřazena role vlastníka, a pokusíte se vytvořit následující přiřazení role jako instanční objekt pomocí Azure CLI:

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    Pokud se zobrazí chybová zpráva "nedostatečná oprávnění k dokončení operace", je pravděpodobně způsobeno tím, že rozhraní příkazového řádku Azure se snaží vyhledat identitu nabyvatele v Azure AD a instanční objekt ve výchozím nastavení nemůže službu Azure AD přečíst.

    Existují dva způsoby, jak potenciálně vyřešit tuto chybu. Prvním způsobem je přiřazení role [čtenáři adresáře](../active-directory/roles/permissions-reference.md#directory-readers) k instančnímu objektu, aby bylo možné číst data v adresáři.

    Druhým způsobem, jak tuto chybu vyřešit, je vytvořit přiřazení role pomocí `--assignee-object-id` parametru místo `--assignee` . Pomocí rozhraní `--assignee-object-id` příkazového řádku Azure CLI přeskočí vyhledávání Azure AD. Budete muset získat ID objektu uživatele, skupiny nebo aplikace, ke které chcete přiřadit roli. Další informace najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure CLI](role-assignments-cli.md#add-role-assignment-for-a-new-service-principal-at-a-resource-group-scope).

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```
- Pokud se pokusíte odebrat přiřazení role posledního vlastníka pro předplatné, může se zobrazit chyba "nelze odstranit poslední přiřazení správce RBAC". Odebrání přiřazení role posledního vlastníka pro předplatné se nepodporuje, aby se předešlo osamocení předplatného. Pokud chcete zrušit předplatné, přečtěte si téma [zrušení předplatného Azure](../cost-management-billing/manage/cancel-azure-subscription.md).

## <a name="problems-with-custom-roles"></a>Potíže s vlastními rolemi

- Pokud potřebujete postup, jak vytvořit vlastní roli, přečtěte si kurzy k vlastním rolím pomocí [Azure Portal](custom-roles-portal.md) (aktuálně ve verzi Preview), [Azure PowerShell](tutorial-custom-role-powershell.md)nebo rozhraní příkazového [řádku Azure CLI](tutorial-custom-role-cli.md).
- Pokud nemůžete aktualizovat existující vlastní roli, ověřte, že jste aktuálně přihlášeni jako uživatel, kterému je přiřazena role s oprávněním, jako je `Microsoft.Authorization/roleDefinition/write` [vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator).
- Pokud se vám nedaří odstranit vlastní roli a zobrazuje se chybová zpráva Na roli odkazují stávající přiřazení rolí (kód: RoleDefinitionHasAssignments), znamená to, že vlastní roli stále používají některá přiřazení rolí. Odeberte tato přiřazení rolí a zkuste vlastní roli odstranit znovu.
- Pokud se při pokusu o vytvoření nové vlastní role zobrazí chybová zpráva Došlo k překročení limitu definic rolí. Při pokusu o vytvoření nové vlastní role se nedají vytvářet žádné další definice rolí (kód: RoleDefinitionLimitExceeded). Odstraňte všechny vlastní role, které se nepoužívají. Azure podporuje v adresáři až **5000** vlastních rolí. (Pro Azure Německo a Azure Čína 21Vianet je limit 2000 vlastních rolí.)
- Pokud se zobrazí chybová zpráva podobná "klient má oprávnění k provedení akce" Microsoft. Authorization/roleDefinitions/Write "v oboru"/Subscriptions/{SubscriptionId} ", ale propojený odběr nebyl nalezen" při pokusu o aktualizaci vlastní role ověřte, zda byly v adresáři odstraněny nejméně jeden přizpůsobený [Rozsah](role-definitions.md#assignablescopes) . Pokud se obor odstranil, vytvořte lístek podpory, protože v současné době není k dispozici žádné samoobslužné řešení.

## <a name="custom-roles-and-management-groups"></a>Vlastní role a skupiny pro správu

- V rámci vlastní role můžete definovat jenom jednu skupinu pro správu `AssignableScopes` . Přidání skupiny pro správu do `AssignableScopes` je aktuálně ve verzi Preview.
- Vlastní role se `DataActions` nedají přiřadit v oboru skupiny pro správu.
- Azure Resource Manager neověřuje existenci skupiny pro správu v oboru přiřazení definice role.
- Další informace o vlastních rolích a skupinách pro správu najdete v tématu [uspořádání prostředků pomocí skupin pro správu Azure](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment).

## <a name="transferring-a-subscription-to-a-different-directory"></a>Převod předplatného do jiného adresáře

- Pokud potřebujete postup, jak přenést předplatné do jiného adresáře služby Azure AD, přečtěte si téma [přenos předplatného Azure do jiného adresáře Azure AD](transfer-subscription.md).
- Pokud přenesete předplatné do jiného adresáře služby Azure AD, všechna přiřazení rolí se **trvale** odstraní ze zdrojového adresáře služby Azure AD a nemigrují se do cílového adresáře služby Azure AD. V cílovém adresáři musíte znovu vytvořit přiřazení rolí. Je také nutné ručně znovu vytvořit spravované identity pro prostředky Azure. Další informace najdete v tématu [Nejčastější dotazy a známé problémy se spravovanými identitami](../active-directory/managed-identities-azure-resources/known-issues.md).
- Pokud jste globálním správcem služby Azure AD a nemáte přístup k předplatnému po jeho přenosu mezi adresáři, použijte přepínač **Správa přístupu pro prostředky Azure** a dočasně [zvyšte přístup](elevate-access-global-admin.md) k předplatnému, abyste získali přístup k předplatnému.

## <a name="issues-with-service-admins-or-co-admins"></a>Problémy se správci služeb nebo spolusprávci

- Pokud máte problémy se správcem a spolusprávci služeb, přečtěte si téma [Přidání nebo změna správců předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) a [rolí klasických správců předplatného, role Azure a role Azure AD](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Odepřený přístup nebo chyby oprávnění

- Pokud při pokusu o vytvoření prostředku dojde k chybě oprávnění Klient s ID objektu nemá oprávnění k provedení akce v oboru (kód: AuthorizationFailed), zkontrolujte, že jste přihlášeni jako uživatel s přiřazenou rolí s oprávněním k zápisu pro prostředek ve vybraném oboru. Pokud například chcete spravovat virtuální počítače ve skupině prostředků, měli byste mít roli [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) pro danou skupinu prostředků (nebo nadřazený obor). Seznam oprávnění pro jednotlivé předdefinované role najdete v tématu [předdefinované role Azure](built-in-roles.md).
- Pokud se zobrazí chyba oprávnění "nemáte oprávnění k vytvoření žádosti o podporu" při pokusu o vytvoření nebo aktualizaci lístku podpory, ověřte, že jste aktuálně přihlášeni jako uživatel, kterému je přiřazena role s `Microsoft.Support/supportTickets/write` oprávněním, jako je například [Přispěvatel žádostí o podporu](built-in-roles.md#support-request-contributor).

## <a name="move-resources-with-role-assignments"></a>Přesunutí prostředků s přiřazením rolí

Pokud přesunete prostředek, který má přiřazenou roli Azure, přímo k prostředku (nebo podřízenému prostředku), přiřazení role se nepřesune a bude osamocené. Po přesunutí musíte znovu vytvořit přiřazení role. Nakonec se automaticky odebere přiřazení osamocené role, ale je osvědčeným postupem odebrání přiřazení role před přesunutím prostředku.

Informace o tom, jak přesunout prostředky, najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="role-assignments-with-identity-not-found"></a>Přiřazení rolí s identitou se nenašlo.

V seznamu přiřazení rolí pro Azure Portal můžete všimnout, že je objekt zabezpečení (uživatel, skupina, instanční objekt nebo spravovaná identita) uvedený jako **Identita nenalezena** s **neznámým** typem.

![V seznamu přiřazení rolí Azure se nenašla identita.](./media/troubleshooting/unknown-security-principal.png)

Identita se nemusí najít ze dvou důvodů:

- Při vytváření přiřazení role jste nedávno pozvaní uživatele.
- Odstranili jste objekt zabezpečení, který měl přiřazení role.

Pokud jste při vytváření přiřazení role nedávno pozvali uživatele, tento objekt zabezpečení může být stále v procesu replikace napříč různými oblastmi. Pokud ano, počkejte chvíli a aktualizujte seznam přiřazení rolí.

Nicméně pokud tento objekt zabezpečení není nedávno pozvaníný uživatel, může se jednat o odstraněný objekt zabezpečení. Pokud přiřadíte roli objektu zabezpečení a později odstraníte tento objekt zabezpečení bez prvotního odebrání přiřazení role, bude objekt zabezpečení uveden jako **Identita nebyl nalezen** a je **neznámého** typu.

Pokud toto přiřazení role vypíšete pomocí Azure PowerShell, může se zobrazit prázdná `DisplayName` a je `ObjectType` nastavená na **Neznámý**. Například [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) vrátí přiřazení role, které je podobné následujícímu výstupu:

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

Podobně platí, že pokud toto přiřazení role vypíšete pomocí Azure CLI, může se zobrazit prázdná `principalName` . Například [AZ role Assignment list](/cli/azure/role/assignment#az-role-assignment-list) vrátí přiřazení role, které je podobné následujícímu výstupu:

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

Nejedná se o problém s ponechání těchto přiřazení rolí, kde byl odstraněn objekt zabezpečení. Pokud chcete, můžete tato přiřazení role odebrat pomocí kroků, které jsou podobné jiným přiřazením rolí. Informace o tom, jak odebrat přiřazení rolí, najdete v tématu [Azure Portal](role-assignments-portal.md#remove-a-role-assignment), [Azure POWERSHELL](role-assignments-powershell.md#remove-a-role-assignment)nebo [Azure CLI](role-assignments-cli.md#remove-role-assignment) .

Pokud se v prostředí PowerShell pokusíte odstranit přiřazení rolí pomocí ID objektu a definice role a na základě parametrů se shoduje více než jedno přiřazení role, zobrazí se chybová zpráva: "zadané informace nejsou namapovány na přiřazení role". Následující výstup ukazuje příklad chybové zprávy:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Pokud se zobrazí tato chybová zpráva, ujistěte se, že jste zadali `-Scope` také `-ResourceGroupName` parametry nebo.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Změny přiřazení role se nezjišťují.

Azure Resource Manager někdy ukládá do mezipaměti konfigurace a data pro zlepšení výkonu. Když přidáváte nebo odebíráte přiřazení rolí, může trvat až 30 minut, než se změny projeví. Pokud používáte Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure, můžete vynutit aktualizaci změn přiřazení role odhlášením a přihlášením. Pokud provádíte změny přiřazení rolí pomocí REST API volání, můžete vynutit aktualizaci pomocí aktualizace přístupového tokenu.

Pokud přidáváte nebo odebíráte přiřazení role v oboru skupiny pro správu a role má `DataActions` , nemusí být přístup k rovině dat aktualizován po dobu několika hodin. To platí jenom pro rozsah skupiny pro správu a rovinu dat.

## <a name="web-app-features-that-require-write-access"></a>Funkce webové aplikace, které vyžadují přístup pro zápis

Pokud uživateli udělíte přístup jen pro čtení k jedné webové aplikaci, některé funkce jsou zakázané, které nemůžete očekávat. Následující funkce správy vyžadují přístup pro **zápis** do webové aplikace (přispěvatel nebo vlastník) a nejsou k dispozici ve scénáři jen pro čtení.

* Příkazy (například Start, stop atd.)
* Změna nastavení, jako je Obecná konfigurace, nastavení škálování, nastavení zálohování a nastavení monitorování
* Přístup k přihlašovacím údajům pro publikování a dalším tajným klíčům, jako je nastavení aplikace
* Protokoly streamování
* Konfigurace protokolů prostředků
* Konzola (příkazový řádek)
* Aktivní a poslední nasazení (pro místní průběžné nasazování Git)
* Odhadované výdaje
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

* Certifikáty a vazby TLS/SSL (certifikáty TLS/SSL se dají sdílet mezi lokalitami ve stejné skupině prostředků a geografickým umístěním)  
* Pravidla výstrah  
* Nastavení automatického škálování  
* Součásti Application Insights  
* Webové testy  

## <a name="virtual-machine-features-that-require-write-access"></a>Funkce virtuálních počítačů, které vyžadují přístup pro zápis

Podobně jako u Web Apps vyžadují některé funkce v okně virtuálního počítače přístup pro zápis k virtuálnímu počítači nebo jiným prostředkům ve skupině prostředků.

Virtuální počítače se týkají názvů domén, virtuálních sítí, účtů úložiště a pravidel výstrah.

Tyto položky vyžadují pro **virtuální počítač** přístup pro **zápis** :

* Koncové body  
* IP adresy  
* Disky  
* Rozšíření  

Tyto požadavky vyžadují přístup pro **zápis** k **virtuálnímu počítači** a **skupině prostředků** (spolu s názvem domény), ve kterém se nachází:  

* Skupina dostupnosti  
* Sada s vyrovnáváním zatížení  
* Pravidla výstrah  

Pokud nemůžete získat přístup k žádné z těchto dlaždic, požádejte správce, aby přístup přispěvatele k této skupině prostředků.

## <a name="azure-functions-and-write-access"></a>Azure Functions a přístup pro zápis

Některé funkce [Azure Functions](../azure-functions/functions-overview.md) vyžadují přístup pro zápis. Například pokud je uživateli přiřazena role [čtenáře](built-in-roles.md#reader) , nebude moci zobrazit funkce v rámci aplikace Function App. Portál se zobrazí **(bez přístupu)**.

![Aplikace Function App bez přístupu](./media/troubleshooting/functionapps-noaccess.png)

Čtenář může kliknout na kartu **funkce platformy** a potom kliknout na **všechna nastavení** a zobrazit některá nastavení týkající se aplikace Function App (podobně jako u webové aplikace), ale nemůžou upravovat žádná z těchto nastavení. Pro přístup k těmto funkcím budete potřebovat roli [Přispěvatel](built-in-roles.md#contributor) .

## <a name="next-steps"></a>Další kroky

- [Řešení potíží pro uživatele typu Host](role-assignments-external-users.md#troubleshoot)
- [Přidání nebo odebrání přiřazení rolí Azure pomocí portálu Azure Portal](role-assignments-portal.md)
- [Zobrazení protokolů aktivit pro změny v Azure RBAC](change-history-report.md)
