---
title: Vyžádání balíčku přístupu – správa oprávnění Azure AD
description: Přečtěte si, jak pomocí portálu Můj Přístup požádat o přístup k přístupovému balíčku ve správě nároků služby Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b1ccde2f1f92237978ab4e68acaa26393bbb9d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261746"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Žádost o přístup k balíčku přístupu ve správě oprávnění Azure AD

Díky správě oprávnění Azure AD umožňuje balíček přístupu jednorázové nastavení prostředků a zásad, které automaticky spravuje přístup po dobu životnosti balíčku přístupu. 

Správce balíčků přístupu může nakonfigurovat zásady tak, aby vyžadovaly schválení pro uživatele, aby měli přístup k balíčkům. Uživatel, který potřebuje přístup k přístupu balíček můžete odeslat žádost o získání přístupu. Tento článek popisuje, jak odeslat žádost o přístup.

## <a name="sign-in-to-the-my-access-portal"></a>Přihlášení k portálu Můj přístup

Prvním krokem je přihlášení k portálu Můj přístup, kde můžete požádat o přístup k balíčku přístupu.

**Předpokladová role:** Žadatele

1. Vyhledejte e-mail nebo zprávu od projektu nebo obchodního manažera, se kterým pracujete. E-mail by měl obsahovat odkaz na přístupový balíček, ke které budete potřebovat přístup. Odkaz začíná `myaccess`aplikací , obsahuje nápovědu k adresáři a končí ID přístupového balíčku.  (Pro vládu USA může `https://myaccess.microsoft.us` být doména místo.)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Otevřete odkaz.

1. Přihlaste se k portálu Můj přístup.

    Ujistěte se, že používáte účet organizace (práce nebo školy). Pokud si nejste jisti, obraťte se na svůj projekt nebo obchodního manažera.

## <a name="request-an-access-package"></a>Žádost o přístupový balíček

Jakmile najdete přístupový balíček na portálu Můj přístup, můžete odeslat žádost.

**Předpokladová role:** Žadatele

1. Najděte přístupový balíček v seznamu.  V případě potřeby můžete hledat zadáním vyhledávacího řetězce a výběrem filtru **Název**, **Katalog**nebo **Zdroje.**

    ![Portál Můj přístup – vyhledávání prostředků](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Klepnutím na zaškrtnutí vyberte přístupový balíček.

1. Kliknutím na **Požádat o přístup** otevřete podokno Požádat o přístup.

    ![Můj přístupový portál – přístupové balíčky](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Pokud se zobrazí pole **Zarovnání firmy,** zadejte odůvodnění, které potřebuje přístup.

1. Pokud je **možnost Žádost o určité období** povolena, vyberte možnost **Ano** nebo **Ne**.

1. V případě potřeby zadejte počáteční a koncové datum.

    ![Portál Můj přístup – žádost o přístup](./media/entitlement-management-shared/my-access-request-access.png)

1. Po dokončení odešlete požadavek na **Odeslat.**

1. Kliknutím na **Historie žádostí** zobrazíte seznam vašich požadavků a stav.

    Pokud balíček přístupu vyžaduje schválení, požadavek je nyní ve stavu čekající na schválení.

### <a name="select-a-policy"></a>Výběr zásady

Pokud požadujete přístup k přístupový balíček, který má více zásad, které platí, můžete být vyzváni k výběru zásad. Správce balíčků přístupu může například nakonfigurovat balíček přístupu se dvěma zásadami pro dvě skupiny interních zaměstnanců. První zásady mohou umožnit přístup po dobu 60 dnů a vyžadují schválení. Druhá zásada může umožnit přístup po dobu 2 dnů a nevyžaduje schválení. Pokud narazíte na tento scénář, musíte vybrat zásadu, kterou chcete použít.

![Portál Můj přístup – žádost o přístup – více zásad](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="resubmit-a-request"></a>Opětovné odeslání žádosti

Když požádáte o přístup k přístupovému balíčku, může být váš požadavek zamítnut nebo může vypršet platnost vaší žádosti, pokud schvalovatelé neodpoví včas. Pokud potřebujete přístup, můžete to zkusit znovu a žádost odeslat znovu. Následující postup vysvětluje, jak znovu odeslat žádost o přístup:

**Předpokladová role:** Žadatele

1. Přihlaste se k portálu **Můj přístup.**

1. V navigační nabídce vlevo klikněte na **Vyžádat historii.**

1. Najděte přístupový balíček, pro který znovu odesíláte žádost.

1. Kliknutím na zaškrtnutí vyberte přístupový balíček.

1. Klikněte na modrý odkaz **Zobrazení** vpravo od vybraného přístupového balíčku.
    
    ![Vybrat přístupový balíček a odkaz pro zobrazení](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    Vpravo se otevře podokno s historií požadavků na přístupový balíček.
    
    ![Tlačítko Vybrat znovu odeslat](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Klikněte na tlačítko **Znovu odeslat** v dolní části podokna.

## <a name="cancel-a-request"></a>Zrušení požadavku

Pokud odešlete žádost o přístup a žádost je stále ve stavu čekající na **schválení,** můžete žádost zrušit.

**Předpokladová role:** Žadatele

1. Na portálu Můj přístup klikněte vlevo na **Požádat o historii** a zozobrazujete seznam vašich požadavků a stavu.

1. Klikněte na odkaz **Zobrazit** pro požadavek, který chcete zrušit.

1. Pokud je požadavek stále ve stavu čekající na **schválení,** můžete požadavek zrušit klepnutím na tlačítko **Storno.**

    ![Můj přístupový portál – zrušit požadavek](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Chcete-li potvrdit, že žádost byla zrušena, klepněte na **tlačítko Vyžádat historii.**

## <a name="next-steps"></a>Další kroky

- [Schválení nebo zamítnutí žádostí o přístup](entitlement-management-request-approve.md)
- [Žádost o proces a e-mailová oznámení](entitlement-management-process.md)
