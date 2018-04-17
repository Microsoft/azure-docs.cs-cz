---
title: Přidat vlastníků a uživatelé v Azure DevTest Labs | Microsoft Docs
description: Přidat vlastníků a uživatelé v Azure DevTest Labs buď pomocí portálu Azure nebo pomocí prostředí PowerShell
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: ''
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-craic
ms.openlocfilehash: d0fdd8441143e2d2eb49399a10dc12d38fbf6823
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Přidat vlastníků a uživatelé v Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Přístup v Azure DevTest Labs řídí [řízení řízení přístupu (RBAC)](../role-based-access-control/overview.md). Pomocí RBAC, můžete oddělit povinností v rámci týmu do *role* kde můžete poskytnout pouze takovou úroveň přístupu, které jsou nezbytné pro uživatele k provádění svých úloh. Jsou tři tyto role RBAC *vlastníka*, *uživatel DevTest Labs*, a *Přispěvatel*. V tomto článku se dozvíte, jaké akce lze provést v každé tři hlavní role RBAC. Odtud zjistíte, jak přidat uživatele do testovacího prostředí - prostřednictvím portálu i pomocí skriptu prostředí PowerShell a jak přidat uživatele na úrovni předplatného.

## <a name="actions-that-can-be-performed-in-each-role"></a>Akce, které mohou být prováděny v každé role
Existují tři hlavní role, můžete přiřadit uživatele:

* Vlastník
* Uživatel služby DevTest Labs
* Přispěvatel

Následující tabulka uvádí akce, které lze provést pomocí uživatelů v každé z těchto rolí:

| **Můžete provést akce, které uživatelé v této roli** | **Uživatel DevTest Labs** | **Vlastník** | **Přispěvatel** |
| --- | --- | --- | --- |
| **Úlohy testovacího prostředí** | | | |
| Přidání uživatelů do testovacího prostředí |Ne |Ano |Ne |
| Aktualizovat nastavení náklady |Ne |Ano |Ano |
| **Základní úkoly virtuálních počítačů** | | | |
| Přidání a odebrání vlastních bitových kopií |Ne |Ano |Ano |
| Přidat, aktualizovat a odstranit vzorce |Ano |Ano |Ano |
| Seznam povolených adres Azure Marketplace obrázků |Ne |Ano |Ano |
| **Úkoly virtuálních počítačů** | | | |
| Vytvoření virtuálních počítačů |Ano |Ano |Ano |
| Spuštění, zastavení a odstranění virtuální počítače |Vytvořený uživatelem jenom virtuální počítače. |Ano |Ano |
| Aktualizovat zásady virtuálních počítačů |Ne |Ano |Ano |
| Přidání nebo odebrání datových disků z virtuálních počítačů |Vytvořený uživatelem jenom virtuální počítače. |Ano |Ano |
| **Artefaktů úlohy** | | | |
| Přidání a odebrání úložiště artefaktů |Ne |Ano |Ano |
| Použít artefaktů |Ano |Ano |Ano |

> [!NOTE]
> Když uživatel vytvoří virtuální počítač, se automaticky přiřadí tohoto uživatele k **vlastníka** role vytvoření virtuálního počítače.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Přidat vlastníkem nebo uživateli na úrovni testovacího prostředí
Vlastníci a uživatelé mohou být přidány na úrovni testovacího prostředí prostřednictvím portálu Azure. To zahrnuje externí uživatele s platnou [účet Microsoft (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account).
Proces přidávání roli vlastníka nebo uživatele do testovacího prostředí v Azure DevTest Labs vás provede následující kroky:

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.
3. Ze seznamu labs vyberte požadované testovací prostředí.
4. V okně v prostředí, vyberte **konfigurace**. 
5. Na **konfigurace** vyberte **uživatelé**.
6. Na **uživatelé** vyberte **+ přidat**.
   
    ![Přidání uživatele](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
7. Na **vyberte roli** okno, vyberte požadovanou roli. V části [akce, které mohou být prováděny v každé role](#actions-that-can-be-performed-in-each-role) uvádí různé akce, které lze provést pomocí uživatelé v rolích vlastník, uživatel DevTest a Přispěvatel.
8. Na **přidat uživatele** okno, zadejte e-mailovou adresu nebo jméno uživatele, který chcete přidat v roli, která jste zadali. Pokud uživatel nebyl nalezen, chybová zpráva popisuje problém. Pokud je uživatel nalezen, je uvedena v seznamu a vybrané tohoto uživatele. 
9. Vyberte **vyberte**.
10. Vyberte **OK** zavřete **přidat přístup** okno.
11. Když se vrátíte **uživatelé** okně přidal uživatele.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Přidat externího uživatele k testovacím prostředí pomocí prostředí PowerShell
Kromě přidání uživatelů na portálu Azure, můžete přidat externího uživatele na vašem testovacím prostředí pomocí skriptu prostředí PowerShell. V následujícím příkladu, stačí upravit hodnoty parametru v části **hodnoty změnit** komentář.
Můžete získat `subscriptionId`, `labResourceGroup`, a `labName` hodnoty v okně prostředí na portálu Azure.

> [!NOTE]
> Ukázkový skript předpokládá, že zadaný uživatel byl přidán jako Host ke službě Active Directory a se nezdaří, pokud se nejedná o tento případ. K přidání uživatele není ve službě Active Directory do testovacího prostředí, použijte portál Azure přiřadit uživatele k roli podle pokynů v části [přidat vlastníkem nebo uživateli na úrovni testovacího prostředí](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/en-us/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Login-AzureRmAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzureRmADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Přidání vlastníkem nebo uživateli na úrovni předplatného
Azure oprávnění rozšířeny z nadřazeného oboru podřízeném oboru v Azure. Proto vlastníci předplatné Azure, který obsahuje labs jsou automaticky vlastníků těchto laboratoře. Také vlastní virtuální počítače a další prostředky vytvořené v prostředí uživatelů a službu Azure DevTest Labs. 

Můžete přidat další vlastníky prostřednictvím okna v prostředí v testovacím prostředí [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Vlastník přidaný oboru správy je však užším než oboru vlastník předplatného. Například přidané vlastníky nemají úplný přístup k některým z prostředků, které jsou vytvořené v rámci předplatného službou DevTest Labs. 

Pokud chcete přidat vlastníka k předplatnému Azure, postupujte takto:

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby**a potom vyberte **odběry** ze seznamu.
3. Vyberte požadované předplatné.
4. Vyberte **přístup** ikonu. 
   
    ![Uživatelé přístup](./media/devtest-lab-add-devtest-user/access-users.png)
5. Na **uživatelé** vyberte **přidat**.
   
    ![Přidání uživatele](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. Na **vyberte roli** okně vyberte **vlastníka**.
7. Na **přidat uživatele** okno, zadejte e-mailovou adresu nebo jméno uživatele, který chcete přidat jako vlastníka. Pokud uživatel nebyl nalezen, zobrazí chybovou zprávu vysvětlením problém. Pokud je uživatel nalezen, je tento uživatel uveden v části **uživatele** textové pole.
8. Vyberte umístění uživatelské jméno.
9. Vyberte **vyberte**.
10. Vyberte **OK** zavřete **přidat přístup** okno.
11. Když se vrátíte **uživatelé** okně Uživatel byl přidán jako vlastníka. Tento uživatel je teď vlastníkem žádné labs vytvořil v rámci tohoto předplatného a tak bude moci provádět úlohy vlastníka. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

