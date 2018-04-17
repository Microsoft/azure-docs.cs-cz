---
title: Vytvořit vlastní přístupu na základě rolí role řízení a přiřadit uživatelům interních a externích v Azure | Microsoft Docs
description: Přiřadit vlastní role RBAC vytvořené pomocí prostředí PowerShell a rozhraní příkazového řádku pro interních a externích uživatelů
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: kgremban
ms.assetid: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: d2eb39aa0a3fda7b543b6989cda937559f4d09ea
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="intro-on-role-based-access-control"></a>Úvod na řízení přístupu na základě rolí

Řízení přístupu na základě role je Azure portálu pouze funkce povolení vlastníky předplatného přiřadit granulární role jiným uživatelům, kteří mohou spravovat konkrétní prostředek obory ve svém prostředí.

RBAC umožňuje lepší zabezpečení správy pro velké organizace a pro SMB práce s externími spolupracovníky, dodavatele nebo freelancers, kteří potřebují přístup ke konkrétním prostředkům ve vašem prostředí, ale nemusí nutně prokázat celé infrastruktury nebo žádné související fakturace obory. RBAC umožňuje flexibilitu vlastnící jedno předplatné, které spravuje správce účtu (role Správce služby na úrovni předplatného) a pro práci v rámci stejného předplatného, ale bez jakékoli práva správce pro ni pozvali více uživatelů . Ze správy a fakturace perspektivy funkci RBAC prokáže, že se možnost efektivní čas a správy pro používání Azure v různých situacích.

## <a name="prerequisites"></a>Požadavky
Používání RBAC v prostředí Azure vyžaduje:

* Nutnosti samostatné předplatné přiřazeny uživateli jako vlastník (předplatné role)
* Mít roli vlastníka předplatného Azure
* K dispozici [portálu Azure](https://portal.azure.com)
* Zajistěte si následující zprostředkovatelé prostředků zaregistrovat pro předplatné uživatele: **Microsoft.Authorization**. Další informace o postupu při registraci zprostředkovatele prostředků najdete v tématu [zprostředkovatelé Resource Manager, oblastí, verzí rozhraní API a schémat](../azure-resource-manager/resource-manager-supported-services.md).

> [!NOTE]
> Předplatná Office 365 nebo Azure Active Directory licence (například: přístup k Azure Active Directory) zajištěného z správu Office 365 není pro použití RBAC kvalifikaci center.

## <a name="how-can-rbac-be-used"></a>RBAC použití
RBAC lze použít na tři různé rozsahy v Azure. Z oboru nejvyšší nejnižší tomu, že jsou následující:

* Předplatné (nejvyšší)
* Skupina prostředků
* Prostředek oboru (nejnižší úroveň přístupu nabídky cílové oprávnění v oboru jednotlivých prostředků Azure)

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Přiřadit role RBAC v oboru předplatného
Existují dvě běžných příkladů, když RBAC je použít (ale mimo jiné):

* Že externí uživatelé organizací pozvat (není součástí uživatele správce klienta Azure Active Directory) ke správě určitých prostředků nebo celý předplatného
* Práce s uživateli uvnitř organizace (jsou součástí klienta Azure Active Directory uživatele), ale součást různé týmy nebo skupiny, které potřebují granulární přístup k celé předplatné nebo pro určité skupiny prostředků nebo prostředek oborů v prostředí

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Udělení přístupu na úrovni předplatného pro uživatele mimo Azure Active Directory
Role RBAC lze udělit pouze systémem **vlastníky** předplatného. Proto správce, musíte být přihlášení jako uživatel, který má tato role předběžně zařazená nebo vytvořil předplatné Azure.

Z portálu Azure vyberte po přihlášení jako správce "Odběry" a vyberte požadované.
![okno odběru na portálu Azure](./media/role-assignments-external-users/0.png) ve výchozím nastavení, pokud uživatel s oprávněními správce koupil předplatné Azure, uživateli se zobrazí jako **správce účtu**, tím se roli předplatného. Další informace o rolích předplatného Azure najdete v tématu [přidání nebo změna role Správce služby Azure, které spravují předplatné nebo služby](../billing/billing-add-change-azure-subscription-administrator.md).

V tomto příkladu uživatel "alflanigan@outlook.com" je **vlastníka** z "Bezplatnou zkušební verzi" předplatné v AAD klienta "Výchozí klienta Azure". Vzhledem k tomu, že je tento uživatel Tvůrce předplatného Azure se počáteční Account Microsoft "Outlook" (Account Microsoft = Outlook, Live atd.) bude výchozí název domény pro všechny uživatele přidán do tohoto klienta **"@alflaniganuoutlook.onmicrosoft.com"**. Návrh syntaxe nové domény je tvořen uvedení společně název uživatelské jméno a doménu uživatele, který vytvořil klienta a přidání rozšíření **". onmicrosoft.com"**.
Kromě toho uživatelé mohou přihlásit pomocí vlastního názvu domény v klientovi po přidání a ověření pro nového klienta. Další informace o tom, jak ověřit vlastní název domény v klienta služby Azure Active Directory najdete v tématu [přidání vlastního názvu domény do adresáře](/active-directory/active-directory-add-domain).

V tomto příkladu adresáři "Výchozí klient Azure" obsahuje pouze uživatele s názvem domény "@alflanigan.onmicrosoft.com".

Po výběru předplatného, musíte kliknout na uživatel s oprávněními správce **řízení přístupu (IAM)** a potom **přidat novou roli**.





![Funkce IAM řízení přístupu na portálu Azure](./media/role-assignments-external-users/1.png)





![Přidání nového uživatele v IAM funkce řízení přístupu na portálu Azure](./media/role-assignments-external-users/2.png)

Dalším krokem je vybrat role, kterou chcete přiřadit a uživatel, kterému se přiřadí RBAC role. V **Role** rozevírací nabídce správce uživateli se zobrazí pouze integrované RBAC role, které jsou dostupné v Azure. Podrobné vysvětlení jednotlivých rolí a jejich přiřaditelnými obory, najdete v části [předdefinované role pro řízení přístupu](built-in-roles.md).

Pak musí přidat e-mailovou adresu externího uživatele, uživatel s oprávněními správce. Očekávané chování je externí uživatel není zobrazena v existujícího klienta. Po pozval externí uživatel zadá budou viditelné v rámci **odběry > řízení přístupu (IAM)** s aktuálního uživatele, kteří jsou přiřazeny role RBAC v obor předplatného.





![Přidejte oprávnění do nové role RBAC](./media/role-assignments-external-users/3.png)





![seznam rolí RBAC na úrovni předplatného](./media/role-assignments-external-users/4.png)

Uživatel "chessercarlton@gmail.com" pozval být **vlastníka** pro předplatné "Bezplatnou zkušební verzi". Po odeslání pozvánky, obdrží externího uživatele potvrzení e-mailu s odkazem k aktivaci.
![e-mailová pozvánka pro RBAC role](./media/role-assignments-external-users/5.png)

Probíhá mimo organizaci, nový uživatel nemá žádné existující atributy v adresáři "Výchozí klient Azure". Budou vytvořeny po externího uživatele poskytl souhlas zaznamenávají v adresáři, který je přidružený k předplatnému byl přiřazen k roli.





![e-mailové zprávě pozvánky pro RBAC role](./media/role-assignments-external-users/6.png)

Zobrazuje externí uživatel v klientovi Azure Active Directory od této chvíle jako externí uživatel a tato lze zobrazit na portálu Azure.





![uživatelé okno azure active directory portálu Azure](./media/role-assignments-external-users/7.png)



V **uživatelé** zobrazení, externí uživatele umožňuje rozpoznat typ vlastní ikonu na portálu Azure.

Ale udělení **vlastníka** nebo **Přispěvatel** přístup k externím uživatelem v **předplatné** obor, neumožňuje přístup k adresáři uživatele správce, pokud **Globálního správce** to umožňuje. Ve vlastnosti uživatele **typ uživatele**, který má dvě společné parametry, **člen** a **hosta** lze identifikovat. Člen je uživatel, který je zaregistrován v adresáři, zatímco hosta je uživatel vyzván k adresáři z externího zdroje. Další informace najdete v tématu [jak správci Azure Active Directory přidat uživatele spolupráce B2B](../active-directory/active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Ujistěte se, že po zadání přihlašovacích údajů na portálu, externí uživatel vybere správný adresář pro přihlášení k aplikaci. Stejný uživatel můžete mít přístup k více adresářů a můžete vybrat některý z nich kliknutím uživatelské jméno v vpravo nahoře na portálu Azure a potom z rozevíracího seznamu vyberte příslušného adresáře.

Při se hostovaného v adresáři, externího uživatele můžete spravovat všechny prostředky pro předplatné Azure, ale nemůže získat přístup k adresáři.





![přístup omezen na portálu Azure azure active directory](./media/role-assignments-external-users/9.png)

Azure Active Directory a předplatné Azure, nemají vztah nadřazený podřízený jako ostatní prostředky služby Azure (například: virtuálních počítačů, virtuálních sítí, webové aplikace, úložiště atd.) s předplatné Azure. Všechny pozdější vytvořit, spravovat a účtují pod předplatným Azure, zatímco předplatné služby Azure se používá ke správě přístupu ke službě Azure directory. Další informace najdete v tématu [předplatné jak Azure souvisí s Azure AD](/active-directory/active-directory-how-subscriptions-associated-directory).

Ze všech předdefinovaných rolí RBAC **vlastníka** a **Přispěvatel** nabízejí úplné správy přístup ke všem prostředkům v prostředí, rozdíl, že Přispěvatel nelze vytvářet a odstraňovat nové role RBAC . Mezi integrované role jako **Přispěvatel virtuálních počítačů** nabízejí úplné správy přístup jen k prostředkům uvádí název, bez ohledu na to **skupiny prostředků** během vytváření do.

Přiřazení předdefinované role RBAC **Přispěvatel virtuálních počítačů** na úrovni předplatného, znamená, že uživatel přiřazenou roli:

* Můžete zobrazit všechny virtuální počítače bez ohledu na to datum jejich nasazení a skupiny prostředků, které jsou součástí
* Má úplné správy přístup k virtuálním počítačům v rámci předplatného
* Nelze zobrazit u jiných typů prostředků v předplatném
* Všechny změny z hlediska fakturační nemůže pracovat.

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Předdefinovaná role RBAC přiřadit externího uživatele
Pro různé scénáře v tomto testu, externí uživatele "alflanigan@gmail.com" se přidá jako **Přispěvatel virtuálních počítačů**.




![předdefinované role Přispěvatel virtuálního počítače](./media/role-assignments-external-users/11.png)

Normální chování pro tento externí uživatele s tato předdefinovaná role je chcete zobrazit a spravovat pouze virtuální počítače a jejich přiléhající Resource Manager pouze prostředky potřebné při nasazování. Návrh nabízí tyto role omezený přístup jenom k jejich příslušné prostředky, které jsou vytvořené na portálu Azure.



![Přehled role Přispěvatel virtuálních počítačů na portálu Azure](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Udělení přístupu na úrovni předplatného pro uživatele ve stejném adresáři
Tok procesu je stejný jako při přidávání externího uživatele, z pohledu správce udělení RBAC role, jakož i uživatele i udělení přístupu k roli. Rozdíl je, že pozvané uživatele neobdrží žádné pozvánek e-mailu jako všechny obory prostředků v rámci předplatného. bude k dispozici v řídicím panelu po přihlášení.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Přiřazení role RBAC v oboru skupiny prostředků
Přiřazení na role RBAC **skupiny prostředků** oboru má identické proces pro přiřazení role na úrovni předplatného, pro oba typy uživatelů - externí nebo interní (součást stejný adresář). Uživatelé, které jsou přiřazené RBAC role je zobrazíte ve svém prostředí pouze skupinu prostředků mají přiřazený přístup z **skupiny prostředků** ikonu na portálu Azure.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Přiřadit role RBAC v oboru prostředků
Přiřazení role RBAC v oboru prostředků v Azure má identické proces pro přiřazení role na úrovni předplatného nebo na úrovni skupiny prostředků, následující témže pracovním postupu pro oba scénáře. Znovu, můžete uživatele, kteří jsou přiřazeny RBAC role zobrazení pouze těch položek, které mají přiřazený přístup k, buď v **všechny prostředky** kartě nebo přímo v jejich řídicího panelu.

Důležitým aspektem pro RBAC jak v oboru skupiny prostředků nebo prostředek oboru je pro uživatele a ujistěte se, zda jste přihlášení k správném adresáři.





![přihlášení Directory na portálu Azure](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Přiřazení role RBAC pro skupinu služby Azure Active Directory
Všechny scénáře pomocí RBAC na tři různé rozsahy v Azure nabízí oprávnění, nasazení a správě různé prostředky jako uživatel s přiřazenou bez nutnosti správy osobních předplatné. Bez ohledu na to je přiřazena RBAC role pro předplatné, skupinu prostředků nebo prostředek oboru, všechny prostředky, které jsou přiřazené uživatelé vytvořili na další se fakturují v rámci jednoho předplatného Azure, kde mají uživatelé přístup k. Tímto způsobem, uživatelů, kteří mají oprávnění správce pro toto předplatné celý Azure fakturace má úplný přehled o spotřebě, bez ohledu na to kdo spravuje prostředky.

Stejným způsobem jako pro skupiny Azure Active Directory s perspektivy, že uživatel s oprávněními správce chce zajistit granulární přístup pro týmy nebo celý oddělení, není jednotlivě pro každého uživatele, takže vzhledem k tomu lze použít pro větší organizace role RBAC jej jako velmi čas a správu efektivní možnost. Pro ilustraci v tomto příkladu **Přispěvatel** role je přidaný do jedné ze skupin v klientovi na úrovni předplatného.





![Přidání role RBAC pro skupiny AAD](./media/role-assignments-external-users/14.png)

Tyto skupiny jsou skupiny zabezpečení, které jsou zřizovat a spravovat pouze v rámci Azure Active Directory.

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>Vytvořit vlastní role RBAC otevření žádosti o podporu pomocí prostředí PowerShell
Předdefinované role, které jsou dostupné v Azure zkontrolujte určité úrovně oprávnění na základě dostupných prostředků v prostředí. Ale pokud předdefinované role nevyhovuje vašim potřebám, můžete vytvořit vlastní role.

Pokud chcete vytvořit vlastní roli, může začínat předdefinovaná role, upravit a poté vytvořit novou roli. V tomto příkladu integrované **čtečky** role byl přizpůsoben, aby uživatel povolit možnost otevření žádosti o podporu.

V prostředí PowerShell, použijte [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) příkaz pro export **čtečky** role ve formátu JSON.

```powershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

Na obrázku je výstup JSON pro roli čtečky.

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Dále můžete upravit výstup vytvořit vlastní roli ve formátu JSON.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

Typické role se skládá ze tří hlavních částí **akce**, **NotActions**, a **AssignableScopes**.

**Akce** části je uveden seznam povolených operací pro roli. V takovém případě vytvářet podporu lístků, **Microsoft.Support/&ast;**  operace musí být přidán. Je důležité si uvědomit, že každou operaci je k dispozici od zprostředkovatele prostředků. Chcete-li získat seznam operací pro poskytovatele prostředků, můžete použít [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) příkaz nebo najdete [poskytovatel prostředků Azure Resource Manager operations](resource-provider-operations.md).

Pokud chcete omezit všechny akce pro konkrétní roli, zprostředkovatelé prostředků jsou uvedeny v seznamu **NotActions** části.
Je povinný, že role obsahuje explicitní předplatné ID, kde se používá. ID předplatného jsou uvedeny v části **AssignableScopes**, v opačném případě můžete nebude moct importovat role do vašeho předplatného.

Chcete-li vytvořit vlastní roli, je použít [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) příkazů a poskytnout aktualizovaný soubor definice role JSON.

```powershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

V tomto příkladu je název pro tuto vlastní roli, "úroveň přístupu lístky žádostí o podporu čtečky". Umožňuje uživatelům zobrazit vše, co v dané předplatné a také žádosti o podporu otevřete.

> [!NOTE]
> Pouze dva předdefinované role, které umožňují uživatele k otevření žádosti o podporu jsou **vlastníka** a **Přispěvatel**. Uživatel nebude moci otevřít žádosti o podporu musí mohl být přiřazena role v oboru předplatné všechny žádosti o podporu se vytváří podle předplatného Azure.

Nové vlastní role je nyní k dispozici na webu Azure portal a můžete přiřadit uživatelům.

![snímek obrazovky vlastní roli, které jsou importovány na portálu Azure](./media/role-assignments-external-users/18.png)

![snímek obrazovky přiřazení vlastní importované role pro uživatele ve stejném adresáři](./media/role-assignments-external-users/19.png)

![snímek obrazovky oprávnění pro vlastní importované role](./media/role-assignments-external-users/20.png)

Uživatelé s tuto vlastní roli, mohou nyní vytvářet nové žádosti o podporu.

![snímek obrazovky vytváření žádosti o podporu vlastní role](./media/role-assignments-external-users/21.png)

Uživatelé s tuto vlastní roli nelze provádět další akce, jako například vytvořit virtuální počítače nebo skupiny prostředků.

![snímek obrazovky vlastní roli nelze vytvořit virtuální počítače](./media/role-assignments-external-users/22.png)

![snímek obrazovky vlastní roli nelze vytvořit nové RGs](./media/role-assignments-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>Vytvořit vlastní role RBAC otevření žádosti o podporu pomocí rozhraní příkazového řádku Azure

Postup vytvoření vlastní role pomocí rozhraní příkazového řádku Azure jsou podobná pomocí prostředí PowerShell, s tím rozdílem, že výstup JSON se liší.

V tomto příkladu můžete spustit pomocí integrované **čtečky** role. K zobrazení seznamu akce role Čtenář, použijte [seznamu definice role az](/cli/azure/role/definition#az_role_definition_list) příkaz.

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": []
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Vytvořte soubor JSON v následujícím formátu. **Microsoft.Support/&ast;**  operaci byla přidána do **akce** částech, aby tento uživatel může otevřít žádosti o podporu přitom dál být čtečka čipových karet. Je nutné přidat kde tato role se použije v ID předplatného **AssignableScopes** části.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

Chcete-li vytvořit vlastní roli, použijte [vytvoření definice role az](/cli/azure/role/definition#az_role_definition_create) příkaz.

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

Nové vlastní role je nyní k dispozici na webu Azure portal a proces pomocí této role je stejné jako v předchozí části prostředí PowerShell.

![Azure portálu snímek obrazovky vlastní roli, které jsou vytvořené pomocí rozhraní příkazového řádku 1.0](./media/role-assignments-external-users/26.png)
