---
title: Spolupracovat s ostatními přispěvateli na LUIS aplikace v Azure | Dokumentace Microsoftu
description: Zjistěte, jak spolupracovat s ostatními přispěvateli na Language Understanding (LUIS) aplikace.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: v-geberr
ms.openlocfilehash: 55c50f5276e2cf97ac98d75b8fc2bd56f1c5d644
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37340409"
---
# <a name="collaborate-with-others-on-language-understanding-luis-apps"></a>Spolupráce s ostatními v aplikacích Language Understanding (LUIS)  

Můžete spolupracovat s ostatními uživateli ve vaší aplikaci LUIS společně. 

## <a name="owner-and-collaborators"></a>Vlastníka a spolupracovníky
Aplikace má jednoho vlastníka, ale může mít mnoho spolupracovníky. 

## <a name="add-collaborator"></a>Přidání spolupracovníků

Povolit spolupracovníky můžete upravovat aplikace LUIS **nastavení** stránky aplikace LUIS, zadat e-mail spolupracovníka a klikněte na tlačítko **přidat spolupracovníka**.

![Přidání spolupracovníků](./media/luis-how-to-collaborate/add-collaborator.png)

* Spolupracovníci přihlásit a upravovat aplikace LUIS ve stejnou dobu, kterou pracujete na aplikaci. <!--If a collaborator edits the LUIS app, you see a notification at the top of the browser.-->
* Další spolupracovníci nelze přidat spolupracovníky.

Zobrazit [uživatel tenanta Azure Active Directory](luis-how-to-account-settings.md#azure-active-directory-tenant-user) získat další informace o uživatelských účtů Active Directory. 

## <a name="set-application-as-public"></a>Nastavení aplikace jako veřejný
Zobrazit [přístup koncového bodu aplikace z veřejného app](luis-concept-security.md#public-app-endpoint-access) Další informace.

### <a name="transfer-of-ownership"></a>Převod vlastnictví
Zatímco LUIS v současné době nepodporuje převod vlastnictví, můžete exportovat aplikaci a jiný uživatel LUIS můžete importovat aplikaci. Můžou existovat drobné rozdíly v LUIS skóre mezi těmito dvěma aplikacemi. 
