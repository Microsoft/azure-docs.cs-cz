---
title: Spolupracovat s ostatními přispěvateli na LUIS aplikace v Azure | Dokumentace Microsoftu
description: Zjistěte, jak spolupracovat s ostatními přispěvateli na Language Understanding (LUIS) aplikace.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/31/2018
ms.author: diberry
ms.openlocfilehash: 99f37cb6dc5e05fc5eb4bde09685435ee57fecc6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397780"
---
# <a name="how-to-manage-authors-and-collaborators"></a>Jak spravovat autorů a spolupracovníci 

Můžete spolupracovat s ostatními uživateli ve vaší aplikaci LUIS společně. 

## <a name="owner-and-collaborators"></a>Vlastníka a spolupracovníky

Aplikace má jeden Autor, vlastník, ale může mít mnoho spolupracovníky. 

## <a name="add-collaborator"></a>Přidání spolupracovníků

Povolit spolupracovníky můžete upravovat aplikace LUIS **nastavení** stránky aplikace LUIS, zadat e-mail spolupracovníka a klikněte na tlačítko **přidat spolupracovníka**. Spolupracovníci přihlásit a upravovat aplikace LUIS ve stejnou dobu, kterou pracujete na aplikaci.

![Přidání spolupracovníků](./media/luis-how-to-collaborate/add-collaborator.png)

## <a name="transfer-of-ownership"></a>Převod vlastnictví

Zatímco LUIS v současné době nepodporuje převod vlastnictví, můžete exportovat aplikaci a jiný uživatel LUIS můžete importovat aplikaci. Můžou existovat drobné rozdíly v LUIS skóre mezi těmito dvěma aplikacemi. 

## <a name="azure-active-directory-tenant-user"></a>Uživatel tenanta Azure Active Directory

LUIS používá standardní toku souhlasu Azure Active Directory (Azure AD). 

Správce klienta by měl spolupracovat přímo s uživatele, který potřebuje udělení přístupu k použití LUIS ve službě Azure AD. 

Nejprve uživatel přihlásí do LUIS a vidí v místním dialogovém okně, které vyžadují schválení správce. Uživatel kontaktuje správce tenanta, než budete pokračovat. 

Druhý správce klienta do LUIS přihlásí a zobrazí automaticky otevíraný dialog toku souhlasu. Toto je dialogové okno Správce musí udělit oprávnění pro uživatele. Jakmile správce přijímá oprávnění, uživatel je moci pokračovat s LUIS.

Pokud správce klienta nebude Přihlaste se k LUIS, můžete přístup správce [souhlas](https://account.activedirectory.windowsazure.com/r#/applications) pro LUIS. 

![Oprávnění Azure active directory web aplikace](./media/luis-how-to-account-settings/tenant-permissions.png)

Pokud správce klienta chce pouze určitým uživatelům používat LUIS, podívejte se na to [identity blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Uživatelské účty s více e-mailů pro spolupracovníky.

Pokud chcete přidat spolupracovníky na aplikaci LUIS, zadáváte přesné e-mailovou adresu, musí používat službu LUIS jako spolupracovník spolupracovníka. Zatímco Azure Active Directory (Azure AD) umožňuje jednoho uživatele má více než jednu e-mailový účet používat Zaměnitelně, LUIS vyžaduje, aby uživatel přihlašovat se pomocí e-mailovou adresu, uvedený v seznamu spolupracovníka.