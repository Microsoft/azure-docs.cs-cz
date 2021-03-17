---
title: Řešení potíží se zprávami o chybách přihlášení | Microsoft Docs
description: Přečtěte si, jak řešit chyby při přihlašování pomocí sestav Azure Active Directory v Azure Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de9f5a21f38f741dcf65d285446d9482716d4f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85608122"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Postupy: řešení chyb při přihlašování pomocí sestav Azure Active Directory

[Sestava přihlášení](concept-sign-ins.md) v Azure Active Directory (Azure AD) umožňuje najít odpovědi na otázky týkající se správy přístupu k aplikacím ve vaší organizaci, včetně těchto:

- Jaký je vzorec přihlašování uživatele?
- Kolik uživatelů se přihlásilo za týden?
- Jaký je stav těchto přihlášení?


Kromě toho sestava přihlášení také může pomoct při odstraňování potíží s přihlášením pro uživatele ve vaší organizaci. V této příručce se dozvíte, jak v sestavě přihlášení izolovat selhání při přihlašování a jak je použít k pochopení hlavní příčiny selhání.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat:

* Tenant Azure AD s licencí Premium (P1/P2). Pokud chcete upgradovat edici Azure Active Directory, přečtěte si téma [Začínáme se Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) .
* Uživatel, který je v roli **globální správce**, **Správce zabezpečení**, **Čtenář zabezpečení**nebo **Čtenář sestav** pro tenanta. Každý uživatel má navíc přístup ke svým vlastním přihlášením. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Řešení chyb při přihlašování pomocí sestavy přihlášení

1. Přejděte do [Azure Portal](https://portal.azure.com) a vyberte svůj adresář.
2. Vyberte **Azure Active Directory** a v části **monitorování** vyberte **přihlášení** . 
3. Použijte poskytnuté filtry k zúžení selhání, a to buď pomocí uživatelského jména nebo identifikátoru objektu, názvu aplikace nebo data. Kromě toho vyberte v rozevíracím seznamu **stav** možnost **selhání** , aby se zobrazila pouze neúspěšná přihlášení. 

    ![Filtrování výsledků](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Identifikujte neúspěšné přihlášení, které chcete prozkoumat. Vyberte ho a otevřete tak další okno podrobností s dalšími informacemi o neúspěšném přihlášení. Poznamenejte si **kód chyby přihlášení** a **důvod selhání**. 

    ![Vybrat záznam](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Tyto informace můžete najít také v okně podrobností v kartě **Poradce při potížích a podpoře** .

    ![Řešení potíží a podpora](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. Důvod selhání popisuje chybu. Ve výše uvedeném scénáři je například příčinou selhání **neplatné uživatelské jméno nebo heslo nebo neplatné místní uživatelské jméno nebo heslo**. Oprava se jednoduše přihlásí znovu se správným uživatelským jménem a heslem.

7. Další informace, včetně nápadů na nápravu, získáte tak, že v tomto příkladu vyhledáte kód chyby, **50126** v tomto příkladu, a to v [referenčních kódech chyb přihlášení](reference-sign-ins-error-codes.md). 

8. Pokud všechny ostatní selžou nebo problém přetrvává navzdory doporučenému postupu, [otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) podle kroků na kartě **Poradce při potížích a podpoře** . 

## <a name="next-steps"></a>Další kroky

* [Odkazy na kódy chyb přihlášení](reference-sign-ins-error-codes.md)
* [Přehled sestav přihlášení](concept-sign-ins.md)
