---
title: Spolupráce s ostatními – LUIS
titleSuffix: Azure Cognitive Services
description: Vlastník aplikace může přidat přispěvatele do vývojového prostředku. Tito přispěvatelé mohou upravit model, trénování a publikovat aplikaci.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 913a2b26f67773d9fafbc0a8430d121fbabb97cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053445"
---
# <a name="add-contributors-to-your-app"></a>Přidání přispěvatelů do aplikace

Vlastník aplikace může do aplikací přidávat přispěvatele. Tito spolupracovníci mohou upravit model, trénování a publikování aplikace. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Přidání přispěvatele do zdrojového prostředku Azure

Následující postup je pro všechny uživatele, kteří **se migrovali** používat prostředek vytváření Azure.

Jste migrovali, pokud vaše prostředí pro vytváření LUIS je vázána na vytváření prostředků na stránce **Spravovat > prostředky Azure** na portálu LUIS.

1. Na webu Azure Portal najdete prostředek vytváření language understanding (LUIS). Má typ `LUIS.Authoring`.
1. Na stránce Řízení **přístupu (IAM)** tohoto zdroje vyberte **možnost +Přidat** a pak vyberte **Přidat přiřazení role**.

    ![Na webu Azure Portal přidejte přiřazení role na vytváření prostředků.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. V okně **Přidat přiřazení role** vyberte **roli** přispěvatele. V možnosti **Přiřadit přístup k** možnosti vyberte **uživatele, skupinu nebo instanční objekt Azure AD**. V možnosti **Vybrat** zadejte e-mailovou adresu uživatele. Pokud je uživatel znám pod více než 1 e-mailovou adresou pro stejnou doménu, ujistěte se, že zadá _primární_ e-mailový účet.

    ![Přidání e-mailu uživatele do role přispěvatele pro Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Po nalezení e-mailu uživatele vyberte účet a vyberte **Uložit**. 

    Pokud máte potíže s tímto přiřazením role, zkontrolujte [přiřazení rolí Azure](../../role-based-access-control/role-assignments-portal.md) a řešení potíží s [řízením přístupu Azure](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Přidání spolupracovníka do aplikace LUIS

Následující postup je pro všechny uživatele, kteří **se nepodařilo použít** prostředek vytváření Azure.

Jste se nemigrovali, pokud vaše prostředí pro vytváření LUIS není vázána na zdrojového prostředku na stránce **Spravovat > prostředky Azure** na portálu LUIS.

Aplikace má jednoho autora, vlastníka, ale může mít mnoho spolupracovníků. Chcete-li povolit spolupracovníkům upravovat vaši aplikaci LUIS, musíte přidat e-mail, který používají pro přístup k portálu LUIS do seznamu spolupracovníků. Po jejich přidání se aplikace zobrazí na svém portálu LUIS.

1. V pravé horní nabídce vyberte **Spravovat** a v levé nabídce vyberte **Kolaboranti.**

1. Na panelu nástrojů vyberte **Přidat spolupracovníka.**

1. Zadejte e-mailovou adresu, kterou spolupracovník používá k přihlášení k portálu LUIS.

    ![Přidání e-mailové adresy spolupracovníka](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Uživatelé s více e-maily 

Pokud do aplikace LUIS přidáte přispěvatele nebo spolupracovníky, zadáte přesnou e-mailovou adresu. Zatímco Azure Active Directory (Azure AD) umožňuje jednomu uživateli mít více než jeden e-mailový účet používá zaměnitelně, LUIS vyžaduje, aby se uživatel přihlásit s e-mailovou adresu zadanou při přidávání přispěvatele/spolupracovníka.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Prostředky služby Azure Active Directory

Pokud používáte [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) ve vaší organizaci, language understanding (LUIS) potřebuje oprávnění k informacím o přístupu vašich uživatelů, když chtějí používat LUIS. Prostředky, které vyžaduje LUIS jsou minimální. 

Podrobný popis se zobrazí, když se pokusíte zaregistrovat účet, který má souhlas správce nebo nevyžaduje souhlas správce, například souhlas správce:

* Umožňuje přihlásit se k aplikaci pomocí účtu organizace a nechat aplikaci přečíst váš profil. To také umožňuje aplikaci číst základní informace o společnosti. To uděluje službě LUIS oprávnění ke čtení základních dat profilu, jako je ID uživatele, e-mail, jméno
* Umožňuje aplikaci zobrazit a aktualizovat data, i když aplikaci právě nepoužíváte. Oprávnění je nutné aktualizovat přístupový token uživatele.


### <a name="azure-active-directory-tenant-user"></a>Uživatel klienta Služby Azure Active Directory

Služba LUIS používá standardní tok souhlasu služby Azure Active Directory (Azure AD). 

Správce klienta by měl pracovat přímo s uživatelem, který potřebuje přístup udělený k použití SLUŽBY LUIS ve službě Azure AD. 

* Nejprve se uživatel přihlásí do služby LUIS a zobrazí se v automaticky otevíracím dialogovém okně, které potřebuje schválení správce. Uživatel kontaktuje správce klienta před pokračováním. 
* Za druhé, správce klienta se přihlásí do služby LUIS a zobrazí dialogové okno automaticky otevíraný dialog toku souhlasu. Toto je dialogové okno, které správce potřebuje k tomu, aby uživateli udělit oprávnění. Jakmile správce přijme oprávnění, uživatel může pokračovat s LUIS. Pokud se správce tenanta ke službě LUIS nepřihlásí, může správce získat přístup k [souhlasu](https://account.activedirectory.windowsazure.com/r#/applications) pro službu LUIS, který je uveden na následujícím snímku obrazovky. Všimněte si, že seznam je `LUIS`filtrován na položky, které obsahují název .

![Oprávnění k aktivnímu adresáři Azure podle webu aplikace](./media/luis-how-to-collaborate/tenant-permissions.png)

Pokud správce klienta chce jenom některé uživatele používat LUIS, existuje několik možných řešení:
* Udělení "souhlas správce" (souhlas všem uživatelům Azure AD), ale pak nastavte na "Ano" "přiřazení uživatele povinné" v rámci enterprise vlastnosti aplikace a nakonec přiřadit nebo přidat pouze požadované uživatele do aplikace. Pomocí této metody správce stále poskytuje "souhlas správce" aplikaci, nicméně je možné řídit uživatele, kteří k ní mají přístup.
* Druhým řešením je použití [rozhraní API pro správu identit azure a služby Azure AD v Microsoft Graphu](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) k poskytnutí souhlasu každému konkrétnímu uživateli. 

Přečtěte si další informace o uživatelích adresářů Azure a souhlasu: 
* [Omezení aplikace](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) na sadu uživatelů

## <a name="next-steps"></a>Další kroky

* Přečtěte [si, jak řídit](luis-how-to-manage-versions.md) životní cyklus aplikace pomocí verzí.
* Seznamte se s koncepty, včetně [zdrojového zdroje pro vytváření](luis-concept-keys.md#authoring-key) a [přispěvatelů](luis-concept-keys.md#contributions-from-other-authors) k tomuto prostředku.
* Informace [o vytváření](luis-how-to-azure-subscription.md) zdrojů pro vytváření a runtime
* Migrace do nového [zdrojového zdroje pro vytváření](luis-migration-authoring.md) 
