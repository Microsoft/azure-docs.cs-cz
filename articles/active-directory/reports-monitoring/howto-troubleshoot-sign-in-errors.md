---
title: Jak řešit zprávy o chybách přihlášení | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit chyby přihlášení pomocí sestav Služby Azure Active Directory na webu Azure Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec5fe7f62e8537a7f687202d365eb37d43b48b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008065"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Postup: Řešení potíží s chybami přihlášení pomocí sestav služby Azure Active Directory

[Sestava přihlášení ve](concept-sign-ins.md) službě Azure Active Directory (Azure AD) vám umožní najít odpovědi na otázky týkající se správy přístupu k aplikacím ve vaší organizaci, včetně:

- Jaký je vzorec přihlašování uživatele?
- Kolik uživatelů se přihlásilo za týden?
- Jaký je stav těchto přihlášení?


Sestava přihlášení vám navíc může pomoci při řešení potíží s chybami přihlášení pro uživatele ve vaší organizaci. V této příručce se dozvíte, jak izolovat selhání přihlášení v sestavě přihlášení a použít k pochopení hlavní příčiny selhání.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat:

* Tenant Azure AD s prémiovou licencí (P1/P2). Podívejte [se na téma Začínáme s Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) a upgradujte edici Azure Active Directory.
* Uživatel, který je v **globální správce**, **správce zabezpečení**, **čtečka zabezpečení**, nebo role **čtečky sestav** pro klienta. Kromě toho může každý uživatel přistupovat ke svým vlastním přihlášením. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Poradce při potížích s přihlášením pomocí sestavy přihlášení

1. Přejděte na [portál Azure a](https://portal.azure.com) vyberte svůj adresář.
2. V části Monitorování vyberte **Azure Active Directory** a v části **Monitorování** vyberte **Přihlášení.** 
3. Pomocí poskytnutých filtrů zúžíte selhání, a to buď podle uživatelského jména nebo identifikátoru objektu, názvu aplikace nebo data. Kromě toho vrozené možnost **Selhání** z rozevíracího **přehledu Stav** zobrazíte pouze neúspěšná přihlášení. 

    ![Filtrování výsledků](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Identifikujte neúspěšné přihlášení, které chcete prozkoumat. Vyberte ji, chcete-li otevřít okno další podrobnosti s dalšími informacemi o neúspěšném přihlášení. Poznamenejte si **kód chyby přihlášení** a důvod **selhání**. 

    ![Vybrat záznam](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Tyto informace najdete také na kartě **Poradce při potížích a podpoře** v okně podrobnosti.

    ![Řešení potíží a podpora](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. Důvod selhání popisuje chybu. Například ve výše uvedeném scénáři je důvodselhání **neplatné uživatelské jméno nebo heslo nebo neplatné místní uživatelské jméno nebo heslo**. Oprava je jednoduše přihlásit znovu se správným uživatelským jménem a heslem.

7. Další informace, včetně nápadů na nápravu, můžete získat vyhledáním kódu chyby **50126** v tomto příkladu v [odkazu na kódy chyb přihlášení](reference-sign-ins-error-codes.md). 

8. Pokud vše ostatní selže nebo problém přetrvává i přes provedení doporučeného postupu, [otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) podle kroků na kartě **Poradce při potížích a podpoře.** 

## <a name="next-steps"></a>Další kroky

* [Odkaz na kódy chyb přihlášení](reference-sign-ins-error-codes.md)
* [Přehled sestavy přihlášení](concept-sign-ins.md)
