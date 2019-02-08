---
title: Spolupráce s ostatními
titleSuffix: Language Understanding - Azure Cognitive Services
description: Vlastník aplikace můžete přidat spolupracovníky do aplikace. Tyto spolupracovníky můžete měnit model, natrénujete ho a publikovat aplikace.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 4ccf703ffde0d4a5a0961135e81259519b0a0e41
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878965"
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

Pokud používáte Azure Active Directory (Azure AD) ve vaší organizaci, LUIS potřebuje oprávnění přístupu k informacím o uživatelích, pokud chtějí používat službu LUIS. Prostředky, které vyžaduje služba LUIS jsou minimální. 

Podrobný popis zobrazí při pokusu o přihlášení pomocí účtu, který má souhlas správce, nebo není vyžadují souhlas správce, jako je například souhlas správce:

* Umožňuje přihlášení k aplikaci pomocí účtu organizace a umožňuje aplikaci číst váš profil. Také to umožňuje aplikaci číst základní informace o firmě.
* Umožňuje aplikaci zobrazovat a aktualizovat data, i když se aplikace nejsou aktuálně používá.

První oprávnění uděluje LUIS oprávnění ke čtení dat základního profilu, jako je například ID uživatele, e-mailu, název. Druhé oprávnění je potřeba aktualizovat přístupový token uživatele.

## <a name="azure-active-directory-tenant-user"></a>Uživatel tenanta Azure Active Directory

LUIS používá standardní toku souhlasu Azure Active Directory (Azure AD). 

Správce klienta by měl spolupracovat přímo s uživatele, který potřebuje udělení přístupu k použití LUIS ve službě Azure AD. 

Nejprve uživatel přihlásí do LUIS a vidí v místním dialogovém okně, které vyžadují schválení správce. Uživatel kontaktuje správce tenanta, než budete pokračovat. 

Druhý správce klienta do LUIS přihlásí a zobrazí automaticky otevíraný dialog toku souhlasu. Toto je dialogové okno Správce musí udělit oprávnění pro uživatele. Jakmile správce přijímá oprávnění, uživatel je moci pokračovat s LUIS.

Pokud správce klienta nebude Přihlaste se k LUIS, můžete přístup správce [souhlas](https://account.activedirectory.windowsazure.com/r#/applications) pro LUIS. 

![Oprávnění Azure active directory web aplikace](./media/luis-how-to-collaborate/tenant-permissions.png)

Pokud správce tenanta požaduje pouze určití uživatelé mohli používat službu LUIS, existuje několik možných řešení:
* Udělení souhlasu"admin" (souhlas pro všechny uživatele služby Azure AD), ale pak nastavte na "Ano" "přiřazení uživatelů povinné" v části Vlastnosti podnikové aplikace a nakonec přiřadit nebo přidat jenom požadovaného uživatele k aplikaci. Pomocí této metody správce je stále poskytuje "souhlas správce" do aplikace, je však možné spravovat uživatele, kteří k němu máte přístup.
* Druhým řešením je použití [Azure AD Graph API](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) poskytnout souhlas s konkrétním uživatelům. 

Další informace o uživatelů Azure active directory a vyjádření souhlasu: 
* [Omezit aplikace](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) pro skupinu uživatelů

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Uživatelské účty s více e-mailů pro spolupracovníky.

Pokud chcete přidat spolupracovníky na aplikaci LUIS, zadáváte přesné e-mailovou adresu, musí používat službu LUIS jako spolupracovník spolupracovníka. Zatímco Azure Active Directory (Azure AD) umožňuje jednoho uživatele má více než jednu e-mailový účet používat Zaměnitelně, LUIS vyžaduje, aby uživatel přihlašovat se pomocí e-mailovou adresu, uvedený v seznamu spolupracovníka.

