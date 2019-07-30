---
title: Spolupráce s ostatními – LUIS
titleSuffix: Azure Cognitive Services
description: Vlastník aplikace můžete přidat spolupracovníky do aplikace. Tyto spolupracovníky můžete měnit model, natrénujete ho a publikovat aplikace.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dbc27176e7a300c0799e326acb10b99ac663fa89
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638116"
---
# <a name="how-to-manage-authors-and-collaborators"></a>Jak spravovat autorů a spolupracovníci 

Vlastník aplikace můžete přidat spolupracovníky do aplikace. Tyto spolupracovníky můžete měnit model, natrénujete ho a publikovat aplikace. 

<a name="owner-and-collaborators"></a>

## <a name="add-collaborator"></a>Přidání spolupracovníků

Aplikace má jeden Autor, vlastník, ale může mít mnoho spolupracovníky. Povolit spolupracovníci LUIS aplikaci upravovat, je nutné přidat e-mailu, které používají pro přístup k portálu služby LUIS do seznamu spolupracovníky. Jakmile jsou přidány, aplikace bude zobrazovat na portálu služby LUIS.

1. Vyberte **spravovat** v pravé horní nabídce pak vyberte **spolupracovníci** v levé nabídce.

2. Vyberte **přidat spolupracovníka** z panelu nástrojů.

    [![Přidat spolupracovníka](./media/luis-how-to-collaborate/add-collaborator.png "přidat spolupracovníka")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

3. Zadejte e-mailovou adresu, kterou spolupracovníka používá k přihlášení k portálu služby LUIS.

    ![Přidat spolupracovníka vaší e-mailovou adresu](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)

## <a name="transfer-of-ownership"></a>Převod vlastnictví

Zatímco LUIS v současné době nepodporuje převod vlastnictví, můžete exportovat aplikaci a jiný uživatel LUIS můžete importovat aplikaci. Můžou existovat drobné rozdíly v LUIS skóre mezi těmito dvěma aplikacemi. 

## <a name="azure-active-directory-resources"></a>Prostředky služby Azure Active Directory

Pokud ve vaší organizaci používáte [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD), Language UNDERSTANDING (Luis) potřebuje oprávnění k informacím o přístupu vašich uživatelů, když chtějí používat Luis. Prostředky, které vyžaduje služba LUIS jsou minimální. 

Podrobný popis zobrazí při pokusu o přihlášení pomocí účtu, který má souhlas správce, nebo není vyžadují souhlas správce, jako je například souhlas správce:

* Umožňuje přihlášení k aplikaci pomocí účtu organizace a umožňuje aplikaci číst váš profil. Také to umožňuje aplikaci číst základní informace o firmě. To dává LUIS oprávnění ke čtení dat základních profilů, jako je ID uživatele, e-mail, jméno
* Umožňuje aplikaci zobrazovat a aktualizovat data, i když se aplikace nejsou aktuálně používá. K aktualizaci přístupového tokenu uživatele se vyžaduje oprávnění.


## <a name="azure-active-directory-tenant-user"></a>Uživatel tenanta Azure Active Directory

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

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Uživatelské účty s více e-mailů pro spolupracovníky.

Pokud chcete přidat spolupracovníky na aplikaci LUIS, zadáváte přesné e-mailovou adresu, musí používat službu LUIS jako spolupracovník spolupracovníka. Zatímco Azure Active Directory (Azure AD) umožňuje jednoho uživatele má více než jednu e-mailový účet používat Zaměnitelně, LUIS vyžaduje, aby uživatel přihlašovat se pomocí e-mailovou adresu, uvedený v seznamu spolupracovníka.

