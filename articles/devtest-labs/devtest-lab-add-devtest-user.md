---
title: Přidat vlastníky a uživatele v Azure DevTest Labs | Microsoft Docs
description: Přidání vlastníků a uživatelů v Azure DevTest Labs pomocí Azure Portal nebo PowerShellu
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2ca6a1ffa66ab294e34a1b4866953a393aba4d6d
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511973"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Přidat vlastníky a uživatele v Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Přístup v Azure DevTest Labs řídí [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md). Pomocí služby Azure RBAC můžete oddělit povinnosti v rámci týmu na *role* , ve kterých udělíte jenom množství přístupu, které uživatelé potřebují k provádění svých úloh. Tři z těchto rolí Azure jsou *vlastník*, *uživatel DevTest Labs* a *Přispěvatel*. V tomto článku se dozvíte, jaké akce se dají provádět v každé ze tří hlavních rolí Azure. Odtud se naučíte, jak přidat uživatele do testovacího prostředí prostřednictvím portálu i prostřednictvím skriptu PowerShellu a jak přidat uživatele na úrovni předplatného.

## <a name="actions-that-can-be-performed-in-each-role"></a>Akce, které je možné provést v každé roli
Existují tři hlavní role, které můžete přiřadit uživateli:

* Vlastník
* Uživatel DevTest Labs
* Přispěvatel

Následující tabulka ilustruje akce, které mohou provádět uživatelé v každé z těchto rolí:

| **Akce, které mohou uživatelé v této roli provádět** | **Uživatel DevTest Labs** | **Vlastník** | **Přispěvatel** |
| --- | --- | --- | --- |
| **Úlohy testovacího prostředí** | | | |
| Přidání uživatelů do testovacího prostředí |Ne |Ano |Ne |
| Aktualizovat nastavení nákladů |Ne |Ano |Ano |
| **Základní úlohy virtuálních počítačů** | | | |
| Přidání a odebrání vlastních imagí |Ne |Ano |Ano |
| Přidávání, aktualizace a odstraňování vzorců |Ano |Ano |Ano |
| Povolit image Marketplace |Ne |Ano |Ano |
| **Úlohy virtuálních počítačů** | | | |
| Vytvoření virtuálních počítačů |Ano |Ano |Ano |
| Spuštění, zastavení a odstranění virtuálních počítačů |Jenom virtuální počítače vytvořené uživatelem |Ano |Ano |
| Aktualizace zásad virtuálních počítačů |Ne |Ano |Ano |
| Přidání/odebrání datových disků do/z virtuálních počítačů |Jenom virtuální počítače vytvořené uživatelem |Ano |Ano |
| **Úlohy artefaktů** | | | |
| Přidání a odebrání úložišť artefaktů |Ne |Ano |Ano |
| Použít artefakty |Ano |Ano |Ano |

> [!NOTE]
> Když uživatel vytvoří virtuální počítač, tento uživatel se automaticky přiřadí do role **vlastníka** vytvořeného virtuálního počítače.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Přidání vlastníka nebo uživatele na úrovni testovacího prostředí
Vlastníky a uživatele je možné přidat na úrovni testovacího prostředí prostřednictvím Azure Portal. Uživatel může být externím uživatelem s platným [účet Microsoft (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account).
Následující kroky vás provedou procesem přidání vlastníka nebo uživatele do testovacího prostředí v Azure DevTest Labs:

1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
3. V seznamu cvičení vyberte požadované testovací prostředí.
4. V okně testovacího prostředí vyberte **Konfigurace a zásady**. 
5. Na stránce **Konfigurace a zásady** vyberte v nabídce na levé straně položku **řízení přístupu (IAM)** . 
6. Pokud chcete přidat uživatele k roli, vyberte **Přidat přiřazení role** na panelu nástrojů.
1. V okně **Přidat oprávnění** proveďte následující akce: 
    1. Vyberte roli (například: uživatel DevTest Labs). V části [akce, které lze provést v jednotlivých rolích](#actions-that-can-be-performed-in-each-role) , jsou uvedeny různé akce, které mohou uživatelé provádět v rolích vlastník, uživatel DevTest a přispěvatel.
    2. Vyberte uživatele, kterého chcete přidat do role. 
    3. Vyberte **Uložit**. 
11. Když se vrátíte do okna **Uživatelé** , uživatel byl přidán.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Přidání externího uživatele do testovacího prostředí pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kromě přidávání uživatelů v Azure Portal můžete do testovacího prostředí přidat externího uživatele pomocí skriptu PowerShellu. V následujícím příkladu upravte hodnoty parametrů v části **hodnoty na změnit** komentář.
`subscriptionId`Hodnoty, a můžete načíst `labResourceGroup` `labName` z okna Lab v Azure Portal.

> [!NOTE]
> Vzorový skript předpokládá, že zadaný uživatel byl přidán jako host do služby Active Directory a selže, pokud se nejedná o tento případ. Pokud chcete do testovacího prostředí přidat uživatele, který není součástí služby Active Directory, použijte Azure Portal k přiřazení uživatele k roli, jak je znázorněno v části, na [úrovni testovacího prostředí přidejte vlastníka nebo uživatele](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

```azurepowershell
# Add an external user in DevTest Labs user role to a lab
# Ensure that guest users can be added to the Azure Active directory:
# https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

# Values to change
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource name here>"
$labName = "<Enter lab name here>"
$userDisplayName = "<Enter user's display name here>"

# Log into your Azure account
Connect-AzAccount

# Select the Azure subscription that contains the lab. 
# This step is optional if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Retrieve the user object
$adObject = Get-AzADUser -SearchString $userDisplayName

# Create the role assignment. 
$labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId
```

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Přidání vlastníka nebo uživatele na úrovni předplatného
Oprávnění Azure se šíří z nadřazeného oboru do podřízeného oboru v Azure. Proto vlastníci předplatného Azure, který obsahuje Labs, jsou automaticky vlastníci těchto cvičení. Také vlastní virtuální počítače a další prostředky vytvořené uživateli testovacího prostředí a službu Azure DevTest Labs. 

Do testovacího prostředí můžete přidat další vlastníky přes okno testovacího prostředí v [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040). Rozsah správy přidaných vlastníků je ale užší než obor vlastníka předplatného. Přidaní vlastníci například nemají úplný přístup k některým prostředkům vytvořeným v rámci předplatného služby DevTest Labs. 

K přidání vlastníka do předplatného Azure použijte tento postup:

1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby** a potom v seznamu vyberte **odběry** .
3. Vyberte požadované předplatné.
4. Vyberte ikonu **přístupu** . 
   
    ![Přístup k uživatelům](./media/devtest-lab-add-devtest-user/access-users.png)
5. V okně **Uživatelé** vyberte **Přidat**.
   
    ![Přidat uživatele](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. V okně **Vybrat roli** vyberte **Owner (vlastník**).
7. V okně **Přidat uživatele** zadejte e-mailovou adresu nebo jméno uživatele, kterého chcete přidat jako vlastníka. Pokud se uživatel nenajde, zobrazí se chybová zpráva s vysvětlením problému. Pokud je uživatel nalezen, je tento uživatel uveden v textovém poli **uživatel** .
8. Vyberte nalezené uživatelské jméno.
9. Vyberte **Vybrat**.
10. Výběrem **OK** zavřete okno **Přidat přístup** .
11. Když se vrátíte do okna **Uživatelé** , uživatel byl přidán jako vlastník. Tento uživatel je teď vlastníkem všech laboratoří vytvořených v rámci tohoto předplatného, takže může provádět úkoly vlastníka. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
