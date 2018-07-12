---
title: Přidat vlastníky a uživatele ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Přidat vlastníky a uživatele ve službě Azure DevTest Labs pomocí webu Azure portal nebo Powershellu
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: spelluru
ms.openlocfilehash: 8f9504458b1f332193e8457bcc9cf41e85fd6aca
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38573396"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Přidat vlastníky a uživatele ve službě Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Přístup ve službě Azure DevTest Labs je řízen [Azure Role-Based řízení přístupu (RBAC)](../role-based-access-control/overview.md). Pomocí RBAC, které můžete oddělit úlohy v rámci vašeho týmu do *role* kde udělit jenom potřebnou úroveň přístupu uživatelům k provádění svých úloh. Tři z těchto rolí RBAC jsou *vlastníka*, *uživatel služby DevTest Labs*, a *Přispěvatel*. V tomto článku se dozvíte, jaké akce lze provádět v každé tři hlavní role RBAC. Odtud zjistíte, jak přidat uživatele do testovacího prostředí – prostřednictvím portálu a pomocí skriptu prostředí PowerShell a jak přidat uživatele na úrovni předplatného.

## <a name="actions-that-can-be-performed-in-each-role"></a>Akce, které je možné provádět každou roli
Existují tři hlavní role, kterou můžete přiřadit uživatele:

* Vlastník
* Uživatel služby DevTest Labs
* Přispěvatel

Následující tabulka popisuje akce, které mohou provádět uživatelé v každé z těchto rolí:

| **Můžete provádět akce, které uživatelé v této roli** | **Uživatel služby DevTest Labs** | **Vlastník** | **Přispěvatel** |
| --- | --- | --- | --- |
| **Úlohy testovacího prostředí** | | | |
| Přidání uživatelů do testovacího prostředí |Ne |Ano |Ne |
| Aktualizovat nastavení náklady |Ne |Ano |Ano |
| **Základní úkoly virtuálních počítačů** | | | |
| Přidání a odebrání vlastních imagí |Ne |Ano |Ano |
| Přidání, aktualizace a odstranění vzorce |Ano |Ano |Ano |
| Image Azure Marketplace seznamu povolených IP adres |Ne |Ano |Ano |
| **Úlohy virtuálních počítačů** | | | |
| Vytvoření virtuálních počítačů |Ano |Ano |Ano |
| Spuštění, zastavení a odstranění virtuálních počítačů |Pouze virtuální počítače vytvořené uživatelem. |Ano |Ano |
| Aktualizovat zásady pro virtuální počítače |Ne |Ano |Ano |
| Přidání nebo odebrání datových disků do a z virtuálních počítačů |Pouze virtuální počítače vytvořené uživatelem. |Ano |Ano |
| **Artefakt úlohy** | | | |
| Přidání a odebrání úložiště artefaktů |Ne |Ano |Ano |
| Použít artefakty |Ano |Ano |Ano |

> [!NOTE]
> Pokud uživatel vytvoří virtuální počítač, tento uživatel je automaticky přiřazen **vlastníka** role vytvořenému virtuálnímu počítači.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Přidání vlastníkem nebo uživateli na úrovni testovacího prostředí
Uživatelů a vlastníků lze přidat na úrovni prostředí prostřednictvím webu Azure portal. Uživatel může být externím uživatelem s platným [účet Microsoft (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account).
Následující postup vás provede procesem přidávání roli vlastníka nebo uživatele do testovacího prostředí ve službě Azure DevTest Labs:

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
3. V seznamu testovacích prostředí vyberte požadované prostředí.
4. V okně testovacího prostředí, vyberte **konfigurace a zásad**. 
5. Na **konfigurace a zásad** stránce **řízení přístupu (IAM)** z nabídky na levé straně. 
6. Vyberte **přidat** na panelu nástrojů můžete přidat uživatele k roli.

    ![Přidání uživatele](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
1. V **přidat oprávnění** okno, proveďte následující akce: 
    1. Vyberte roli (Příklad: uživatel služby DevTest Labs). V části [akce, které lze provádět v každé role](#actions-that-can-be-performed-in-each-role) uvádí různé akce, které mohou provádět uživatelé v rolích vlastník, uživatel služby DevTest a Přispěvatel.
    2. Vyberte uživatele, který má být přidáni do role. 
    3. Vyberte **Uložit**. 

        ![Přidání uživatele do role](./media/devtest-lab-add-devtest-user/add-user.png) 
11. Po návratu k **uživatelé** okně se uživateli přidala.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Přidat externího uživatele do testovacího prostředí pomocí Powershellu
Kromě přidání uživatelů na webu Azure Portal, můžete přidat externího uživatele do testovacího prostředí pomocí Powershellového skriptu. V následujícím příkladu, upravte hodnoty parametrů v části **hodnoty změnit** komentář.
Můžete načíst `subscriptionId`, `labResourceGroup`, a `labName` hodnoty v okně testovacího prostředí na webu Azure Portal.

> [!NOTE]
> Ukázkový skript předpokládá, že zadaný uživatel se přidala jako hosta ke službě Active Directory a selže, pokud se nejedná o tento případ. Chcete-li přidat uživatele do testovacího prostředí není ve službě Active Directory, pomocí webu Azure portal přiřadit uživatele k roli, jak je znázorněno v části [přidání vlastníkem nebo uživateli na úrovni prostředí](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Connect-AzureRmAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzureRmADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Přidání vlastníkem nebo uživateli na úrovni předplatného
Oprávnění Azure přecházejí z nadřazeného oboru na podřízeném oboru v Azure. Vlastníci předplatného Azure, který obsahuje testovací prostředí se proto automaticky vlastníků těchto testovacích prostředí. Také vlastní virtuální počítače a další prostředky vytvořené v rámci testovacího prostředí uživatele a služba Azure DevTest Labs. 

Můžete přidat další vlastníky do testovacího prostředí prostřednictvím okna testovacího prostředí portálu [webu Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040). Přidání vlastníka obor správy je však užším než vlastník předplatného oboru. Například přidání vlastníků nemají úplný přístup k některé z prostředků, které jsou vytvořeny v rámci předplatného služba DevTest Labs. 

Přidání vlastníka k předplatnému Azure, postupujte podle těchto kroků:

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby**a pak vyberte **předplatná** ze seznamu.
3. Vyberte požadované předplatné.
4. Vyberte **přístup** ikonu. 
   
    ![Přístup uživatelů](./media/devtest-lab-add-devtest-user/access-users.png)
5. Na **uživatelé** okně vyberte **přidat**.
   
    ![Přidání uživatele](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. Na **vybrat roli** okno, vyberte **vlastníka**.
7. Na **přidat uživatele** okně zadejte e-mailovou adresu nebo jméno uživatele, které chcete přidat jako vlastníka. Pokud uživatel nebyl nalezen, získáte chybová zpráva s vysvětlením problému. Pokud je uživatel nalezen, tento uživatel je uveden v části **uživatele** textového pole.
8. Vyberte umístěny uživatelské jméno.
9. Vyberte **vyberte**.
10. Vyberte **OK** zavřete **přidat přístup** okno.
11. Po návratu k **uživatelé** okně přidal uživatele jako vlastníka. Tento uživatel je teď vlastníkem žádné testovací prostředí vytvořené v rámci tohoto předplatného a proto se bude moct provádět úlohy vlastníka. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

