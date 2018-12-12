---
title: Spolupráce s ostatními
titleSuffix: Language Understanding - Azure Cognitive Services
description: Vlastník aplikace můžete přidat spolupracovníky do aplikace. Tyto spolupracovníky můžete měnit model, natrénujete ho a publikovat aplikace.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: d1db8974ce134b50340db500c9ea1b00126fe10a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086415"
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

Pokud správce klienta chce pouze určitým uživatelům používat LUIS, podívejte se na to [identity blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Uživatelské účty s více e-mailů pro spolupracovníky.

Pokud chcete přidat spolupracovníky na aplikaci LUIS, zadáváte přesné e-mailovou adresu, musí používat službu LUIS jako spolupracovník spolupracovníka. Zatímco Azure Active Directory (Azure AD) umožňuje jednoho uživatele má více než jednu e-mailový účet používat Zaměnitelně, LUIS vyžaduje, aby uživatel přihlašovat se pomocí e-mailovou adresu, uvedený v seznamu spolupracovníka.

