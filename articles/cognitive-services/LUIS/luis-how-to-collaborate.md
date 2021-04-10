---
title: Spolupráce s ostatními – LUIS
titleSuffix: Azure Cognitive Services
description: Vlastník aplikace může přidat přispěvatele do prostředku pro vytváření obsahu. Tito přispěvatelé můžou upravit model, výuku a publikování aplikace.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 5ca13784fe2f9a6a5b448bc838bf508f01b0a9fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "101095191"
---
# <a name="add-contributors-to-your-app"></a>Přidání přispěvatelů do aplikace

Vlastník aplikace může přidat přispěvatele do aplikací. Tito přispěvatelé můžou upravit model, výuku a publikování aplikace. Po dokončení [migrace](luis-migration-authoring.md) účtu se _přispěvatelé_ spravují v Azure Portal pro prostředek pro vytváření obsahu pomocí stránky **řízení přístupu (IAM)** . Přidejte uživatele pomocí e-mailové adresy spolupracovníka a role _přispěvatele_ .

## <a name="add-contributor-to-azure-authoring-resource"></a>Přidat přispěvatele do prostředku pro vytváření obsahu Azure

Migrovali jste, pokud je vaše prostředí pro vytváření LUIS vázané na prostředek vytváření na stránce **> Správa prostředků Azure** na portálu Luis.

1. V Azure Portal vyhledejte prostředek pro vytváření obsahu Language Understanding (LUIS). Má typ `LUIS.Authoring` .
1. Na stránce **Access Control (IAM)** tohoto prostředku vyberte **+ Přidat** a vyberte **Přidat přiřazení role**.

    ![V Azure Portal přidejte přiřazení role při vytváření prostředku.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. V okně **Přidat přiřazení role** vyberte **roli** Přispěvatel. V možnosti **přiřadit přístup k** vyberte možnost **uživatel, skupina nebo instanční objekt služby Azure AD**. V možnosti **Vybrat** zadejte e-mailovou adresu uživatele. Pokud je uživatel znám více než 1 e-mailovou adresou pro stejnou doménu, ujistěte se, že je zadán _primární_ e-mailový účet.

    ![Přidání e-mailu uživatele do role přispěvatele pro Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Po nalezení e-mailu uživatele vyberte účet a vyberte **Uložit**.

    Pokud máte s přiřazením této role problémy, přečtěte si téma [přiřazení rolí Azure](../../role-based-access-control/role-assignments-portal.md) a [řešení potíží s řízením přístupu v Azure](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments).

## <a name="view-the-app-as-a-contributor"></a>Zobrazení aplikace jako přispěvatele

Až budete přidáni jako Přispěvatel, [Přihlaste se k portálu Luis](sign-in-luis-portal.md).

[!INCLUDE [switch azure directories](includes/switch-azure-directories.md)]

### <a name="users-with-multiple-emails"></a>Uživatelé s více e-maily

Pokud přidáte přispěvatele do aplikace LUIS, zadáváte přesnou e-mailovou adresu. I když Azure Active Directory (Azure AD) umožňuje, aby jeden uživatel měl zaměnitelné více e-mailových účtů, LUIS vyžaduje, aby se uživatel přihlásil pomocí e-mailové adresy zadané při přidávání přispěvatele.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Prostředky Azure Active Directory

Pokud ve vaší organizaci používáte [Azure Active Directory](../../active-directory/index.yml) (Azure AD), Language UNDERSTANDING (Luis) potřebuje oprávnění k informacím o přístupu vašich uživatelů, když chtějí používat Luis. Prostředky, které LUIS vyžaduje, jsou minimální.

V případě, že se pokusíte zaregistrovat účet, který má souhlas správce nebo nevyžaduje souhlas správce, jako je třeba souhlas správce, se zobrazí podrobný popis.

* Umožňuje přihlásit se k aplikaci pomocí účtu organizace a nechat si aplikaci číst váš profil. Umožňuje také aplikaci číst základní informace o společnosti. To dává LUIS oprávnění ke čtení dat základních profilů, jako je ID uživatele, e-mail, jméno
* Umožňuje aplikaci zobrazovat a aktualizovat vaše data, i když tuto aplikaci momentálně nepoužíváte. K aktualizaci přístupového tokenu uživatele se vyžaduje oprávnění.


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory uživatel tenanta

LUIS používá tok souhlasu Standard Azure Active Directory (Azure AD).

Správce tenanta by měl spolupracovat přímo s uživatelem, který potřebuje udělený přístup k používání LUIS ve službě Azure AD.

* Nejprve se uživatel přihlásí k LUIS a zobrazí se automaticky otevírané okno, které vyžaduje schválení správcem. Před pokračováním uživatel kontaktuje správce tenanta.
* Za druhé se správce tenanta přihlásí do LUIS a zobrazí se místní okno s tokem souhlasu. Toto je dialogové okno, které správce potřebuje k udělení oprávnění uživateli. Jakmile správce přijme oprávnění, může pokračovat v LUIS. Pokud se správce tenanta nebude přihlašovat k LUIS, může správce získat přístup k [souhlasu](https://account.activedirectory.windowsazure.com/r#/applications) Luis. Na této stránce můžete filtrovat seznam na položky, které obsahují název `LUIS` .

Pokud chce správce tenanta používat LUIS jenom někteří uživatelé, existuje několik možných řešení:
* Udělení souhlasu správce (souhlasu se všemi uživateli Azure AD), ale pak nastavte na Ano, aby se přiřazení uživatelů vyžadovalo v části vlastnosti podnikové aplikace, a nakonec přiřaďte nebo přidejte do aplikace jenom ty, které jsou k tomu potřeba. V této metodě správce stále poskytuje aplikaci souhlas správce, ale je možné řídit uživatele, kteří k nim mají přístup.
* Druhým řešením je použití [rozhraní API pro správu identit a přístupu služby Azure AD v Microsoft Graph](/graph/azuread-identity-access-management-concept-overview) k poskytnutí souhlasu každému konkrétnímu uživateli.

Další informace o uživatelích a souhlasu Azure Active Directory:
* [Omezení aplikace](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) na sadu uživatelů

## <a name="next-steps"></a>Další kroky

* Naučte se [používat verze](luis-how-to-manage-versions.md) k řízení životního cyklu aplikace.
* Seznamte se s koncepty, včetně [prostředků pro vytváření](luis-how-to-azure-subscription.md#authoring-key) a [přispěvatelů](luis-how-to-azure-subscription.md#contributions-from-other-authors) na tomto prostředku.
* Naučte [se vytvářet prostředky pro](luis-how-to-azure-subscription.md) vytváření obsahu a modul runtime
* Migrace na nový [prostředek pro vytváření](luis-migration-authoring.md)