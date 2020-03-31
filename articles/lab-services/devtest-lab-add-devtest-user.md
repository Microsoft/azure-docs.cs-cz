---
title: Přidání vlastníků a uživatelů do azure devtest labs| Dokumenty společnosti Microsoft
description: Přidání vlastníků a uživatelů v azure devtest labs pomocí portálu Azure nebo PowerShellu
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
ms.openlocfilehash: a9426c20ae23fd3dad4cdba25590ff2eac271896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284275"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Přidání vlastníků a uživatelů do azure devtest labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Přístup v laboratořích Azure DevTest je řízen [řízením přístupu na základě rolí Azure (RBAC).](../role-based-access-control/overview.md) Pomocí RBAC, můžete oddělit povinnosti v rámci týmu do *rolí,* kde udělíte pouze množství přístupu nezbytné pro uživatele k provádění jejich úloh. Tři z těchto rolí RBAC jsou *vlastník*, *DevTest Labs uživatel*a *přispěvatel*. V tomto článku se dozvíte, jaké akce lze provést v každé ze tří hlavních rolí RBAC. Odtud se dozvíte, jak přidat uživatele do testovacího prostředí – a to jak prostřednictvím portálu a prostřednictvím skriptu Prostředí PowerShell a jak přidat uživatele na úrovni předplatného.

## <a name="actions-that-can-be-performed-in-each-role"></a>Akce, které lze provést v každé roli
Existují tři hlavní role, které můžete přiřadit uživateli:

* Vlastník
* Uživatel devTest Labs
* Přispěvatel

Následující tabulka znázorňuje akce, které mohou provádět uživatelé v každé z těchto rolí:

| **Akce, které mohou uživatelé v této roli provádět.** | **Uživatel devTest Labs** | **Vlastník** | **Přispěvatel** |
| --- | --- | --- | --- |
| **Úkoly v laboratoři** | | | |
| Přidání uživatelů do testovacího prostředí |Ne |Ano |Ne |
| Aktualizovat nastavení nákladů |Ne |Ano |Ano |
| **Základní úkoly virtuálního virtuálního mě** | | | |
| Přidání a odebrání vlastních obrázků |Ne |Ano |Ano |
| Přidání, aktualizace a odstranění vzorců |Ano |Ano |Ano |
| IFotky Azure Marketplace v seznamu povolených |Ne |Ano |Ano |
| **Úkoly virtuálních ms** | | | |
| Vytvoření virtuálních počítačů |Ano |Ano |Ano |
| Spuštění, zastavení a odstranění virtuálních mích |Jenom virtuální uživatele vytvořené uživatelem |Ano |Ano |
| Aktualizace zásad virtuálních ms |Ne |Ano |Ano |
| Přidání nebo odebrání datových disků do nebo z virtuálních počítačů |Jenom virtuální uživatele vytvořené uživatelem |Ano |Ano |
| **Úlohy artefaktů** | | | |
| Přidání a odebrání úložišť artefaktů |Ne |Ano |Ano |
| Použití artefaktů |Ano |Ano |Ano |

> [!NOTE]
> Když uživatel vytvoří virtuální hod, je mu automaticky přiřazena role **vlastníka** vytvořeného virtuálního soudu.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Přidání vlastníka nebo uživatele na úrovni testovacího prostředí
Vlastníci a uživatelé můžete přidat na úrovni testovacího prostředí prostřednictvím portálu Azure. Uživatelem může být externí uživatel s platným [účtem Microsoft (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account).
Následující kroky vás provedou procesem přidání vlastníka nebo uživatele do testovacího prostředí v laboratořích Azure DevTest:

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
3. Ze seznamu testovacích prostředí vyberte požadovanou testovací prostředí.
4. Na okně testovacího prostředí vyberte **Konfigurace a zásady**. 
5. Na stránce **Konfigurace a zásady** vyberte v nabídce vlevo **ovládací prvek Přístupu (IAM).** 
6. Chcete-li přidat uživatele do role, vyberte **Přidat přiřazení role** na panelu nástrojů.
1. V okně **Přidat oprávnění** proveďte následující akce: 
    1. Vyberte roli (například: Uživatel devTest Labs). V části [Akce, které lze provést v každé roli,](#actions-that-can-be-performed-in-each-role) jsou uvedeny různé akce, které mohou provádět uživatelé v rolích Vlastník, Uživatel DevTest a Přispěvatel.
    2. Vyberte uživatele, který má být přidán do role. 
    3. Vyberte **Uložit**. 
11. Když se vrátíte do **users** okno, uživatel byl přidán.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Přidání externího uživatele do testovacího prostředí pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kromě přidávání uživatelů na webu Azure Portal můžete do testovacího prostředí přidat externího uživatele pomocí skriptu PowerShellu. V následujícím příkladu upravte hodnoty parametrů v části **Hodnoty pro změnu** komentáře.
Můžete načíst `subscriptionId` `labResourceGroup`, `labName` a hodnoty z okna testovacího prostředí na webu Azure Portal.

> [!NOTE]
> Ukázkový skript předpokládá, že zadaný uživatel byl přidán jako host do služby Active Directory a selže, pokud tomu tak není. Chcete-li přidat uživatele, který není ve službě Active Directory, do testovacího prostředí, přiřaďte uživatele k roli, jak je znázorněno v [části, Přidejte vlastníka nebo uživatele na úrovni testovacího prostředí](#add-an-owner-or-user-at-the-lab-level).   
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
    Connect-AzAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Přidání vlastníka nebo uživatele na úrovni předplatného
Oprávnění Azure se v Azure šíří z nadřazeného oboru do podřízeného oboru. Proto vlastníci předplatného Azure, který obsahuje laboratoře jsou automaticky vlastníky těchto testovacích prostředí. Jsou také vlastní virtuální počítače a další prostředky vytvořené uživateli testovacího prostředí a služby Azure DevTest Labs. 

Další vlastníky můžete přidat do testovacího prostředí prostřednictvím okna testovacího prostředí na [webu Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040). Rozsah správy přidaného vlastníka je však užší než rozsah vlastníka předplatného. Například přidaní vlastníci nemají úplný přístup k některým prostředkům, které jsou vytvořeny v předplatném službou DevTest Labs. 

Pokud chcete do předplatného Azure přidat vlastníka, postupujte takto:

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **Všechny služby**a ze seznamu vyberte **Předplatná.**
3. Vyberte požadované předplatné.
4. Vyberte ikonu **Accessu.** 
   
    ![Přístup k uživatelům](./media/devtest-lab-add-devtest-user/access-users.png)
5. V okně **Uživatelé** vyberte **Přidat**.
   
    ![Přidání uživatele](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. V okně **Vybrat roli** vyberte **položku Vlastník**.
7. V okně **Přidat uživatele** zadejte e-mailovou adresu nebo jméno uživatele, kterého chcete přidat jako vlastníka. Pokud se uživatel nemůže najít, zobrazí se chybová zpráva vysvětlující problém. Pokud je uživatel nalezen, je tento uživatel uveden pod textovým polem **Uživatel.**
8. Vyberte lokalované uživatelské jméno.
9. Vyberte **Vybrat**.
10. Výběrem **možnosti OK** zavřete **přistupovací** okno.
11. Když se vrátíte do **users** okno, uživatel byl přidán jako vlastník. Tento uživatel je nyní vlastníkem všech testovacích prostředí vytvořených v rámci tohoto předplatného, a proto je schopen provádět úkoly vlastníka. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

