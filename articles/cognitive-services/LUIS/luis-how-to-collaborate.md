---
title: Spolupráce s ostatními – LUIS
titleSuffix: Azure Cognitive Services
description: Vlastník aplikace může přidat přispěvatele do prostředku pro vytváření obsahu. Tito přispěvatelé můžou upravit model, výuku a publikování aplikace.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: e69fea4d57aaf2a0b7d3615b1eecbf99a6b13ab8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256817"
---
# <a name="add-contributors-to-your-app"></a>Přidání přispěvatelů do aplikace

Vlastník aplikace může přidat přispěvatele do aplikací. Tyto spolupracovníky můžete měnit model, natrénujete ho a publikovat aplikace. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Přidat přispěvatele do prostředku pro vytváření obsahu Azure

Následující postup platí pro všechny uživatele, kteří se **migrovali** na používání prostředku pro vytváření obsahu Azure.

Migrovali jste, pokud je vaše prostředí pro vytváření LUIS vázané na prostředek vytváření na stránce **> Správa prostředků Azure** na portálu Luis.

1. V Azure Portal vyhledejte prostředek pro vytváření obsahu Language Understanding (LUIS). Má typ `LUIS.Authoring`.
1. Na stránce **Access Control (IAM)** tohoto prostředku vyberte **+ Přidat** a vyberte **Přidat přiřazení role**.

    ![V Azure Portal přidejte přiřazení role při vytváření prostředku.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. V okně **Přidat přiřazení role** vyberte **roli** Přispěvatel. V možnosti **přiřadit přístup k** vyberte možnost **uživatel, skupina nebo instanční objekt služby Azure AD**. V možnosti **Vybrat** zadejte e-mailovou adresu uživatele. Pokud je uživatel znám více než 1 e-mailovou adresou pro stejnou doménu, ujistěte se, že je zadán _primární_ e-mailový účet.

    ![Přidání e-mailu uživatele do role přispěvatele pro Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Po nalezení e-mailu uživatele vyberte účet a vyberte **Uložit**. 

    Pokud máte potíže s přiřazením této role, Projděte si téma [přiřazení rolí Azure](../../role-based-access-control/role-assignments-portal.md) a [řešení potíží s řízením přístupu v Azure](../../role-based-access-control/troubleshooting.md#problems-with-rbac-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Přidání spolupracovníka do aplikace LUIS

Následující postup platí pro všechny uživatele, kteří se **nemigrovali** , aby používali prostředek pro vytváření obsahu Azure.

Nemigrovali jste, pokud vaše prostředí pro vytváření LUIS není vázané na zdroj obsahu na stránce **Správa prostředků Azure >** na portálu Luis.

Aplikace má jeden Autor, vlastník, ale může mít mnoho spolupracovníky. Povolit spolupracovníci LUIS aplikaci upravovat, je nutné přidat e-mailu, které používají pro přístup k portálu služby LUIS do seznamu spolupracovníky. Jakmile jsou přidány, aplikace bude zobrazovat na portálu služby LUIS.

1. Vyberte **spravovat** v pravé horní nabídce pak vyberte **spolupracovníci** v levé nabídce.

1. Vyberte **přidat spolupracovníka** z panelu nástrojů.

    [![Přidat spolupracovníka](./media/luis-how-to-collaborate/add-collaborator.png "přidat spolupracovníka")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

1. Zadejte e-mailovou adresu, kterou spolupracovníka používá k přihlášení k portálu služby LUIS.

    ![Přidat spolupracovníka vaší e-mailovou adresu](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Uživatelé s více e-maily 

Pokud do aplikace LUIS přidáte přispěvatele nebo spolupracovníky, zadáváte přesnou e-mailovou adresu. I když Azure Active Directory (Azure AD) umožňuje, aby jeden uživatel měl zaměnitelné více e-mailových účtů, LUIS vyžaduje, aby se uživatel přihlásil pomocí e-mailové adresy zadané při přidávání Přispěvatel/spolupracovníka.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Prostředky služby Azure Active Directory

Pokud ve vaší organizaci používáte [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD), Language UNDERSTANDING (Luis) potřebuje oprávnění k informacím o přístupu vašich uživatelů, když chtějí používat Luis. Prostředky, které vyžaduje služba LUIS jsou minimální. 

Podrobný popis zobrazí při pokusu o přihlášení pomocí účtu, který má souhlas správce, nebo není vyžadují souhlas správce, jako je například souhlas správce:

* Umožňuje přihlášení k aplikaci pomocí účtu organizace a umožňuje aplikaci číst váš profil. Také to umožňuje aplikaci číst základní informace o firmě. To dává LUIS oprávnění ke čtení dat základních profilů, jako je ID uživatele, e-mail, jméno
* Umožňuje aplikaci zobrazovat a aktualizovat data, i když se aplikace nejsou aktuálně používá. K aktualizaci přístupového tokenu uživatele se vyžaduje oprávnění.


### <a name="azure-active-directory-tenant-user"></a>Uživatel tenanta Azure Active Directory

LUIS používá standardní toku souhlasu Azure Active Directory (Azure AD). 

Správce klienta by měl spolupracovat přímo s uživatele, který potřebuje udělení přístupu k použití LUIS ve službě Azure AD. 

* Nejprve uživatel přihlásí do LUIS a vidí v místním dialogovém okně, které vyžadují schválení správce. Uživatel kontaktuje správce tenanta, než budete pokračovat. 
* Druhý správce klienta do LUIS přihlásí a zobrazí automaticky otevíraný dialog toku souhlasu. Toto je dialogové okno Správce musí udělit oprávnění pro uživatele. Jakmile správce přijímá oprávnění, uživatel je moci pokračovat s LUIS. Pokud se správce tenanta nebude přihlašovat k LUIS, může správce získat přístup k [souhlasu](https://account.activedirectory.windowsazure.com/r#/applications) pro Luis, které vidíte na následujícím snímku obrazovky. Všimněte si, že seznam je filtrovaný na položky, které `LUIS`obsahují název.

![Oprávnění Azure active directory web aplikace](./media/luis-how-to-collaborate/tenant-permissions.png)

Pokud chce správce tenanta používat LUIS jenom někteří uživatelé, existuje několik možných řešení:
* Udělení souhlasu správce (souhlasu se všemi uživateli Azure AD), ale pak nastavte na Ano, aby se přiřazení uživatelů vyžadovalo v části vlastnosti podnikové aplikace, a nakonec přiřaďte nebo přidejte do aplikace jenom ty, které jsou k tomu potřeba. V této metodě správce stále poskytuje aplikaci souhlas správce, ale je možné řídit uživatele, kteří k nim mají přístup.
* Druhým řešením je použití [služby Azure AD Graph API](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) k poskytnutí souhlasu každému konkrétnímu uživateli. 

Další informace o uživatelích a souhlasu Azure Active Directory: 
* [Omezení aplikace](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) na sadu uživatelů

## <a name="next-steps"></a>Další postup

* Naučte se [používat verze](luis-how-to-manage-versions.md) k řízení životního cyklu aplikace.
* Seznamte se s koncepty, včetně [prostředků pro vytváření](/luis-concept-keys.md#authoring-key) a [přispěvatelů](luis-concept-keys.md#contributions-from-other-authors) na tomto prostředku.
* Naučte [se vytvářet prostředky pro](luis-how-to-azure-subscription.md) vytváření obsahu a modul runtime
* Migrace na nový [prostředek pro vytváření](luis-migration-authoring.md) 
