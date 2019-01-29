---
title: Řešení potíží s chybami přihlášení pomocí Azure Active Directory sestavy | Dokumentace Microsoftu
description: Zjistěte, jak řešit chyby přihlášení pomocí Azure Active Directory sestavy na webu Azure Portal
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7225ebd91c4c236b5f54574f1657239420c9f541
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165400"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Postup: Řešení potíží s chybami přihlášení pomocí sestav Azure Active Directory

[Sestavy přihlášení](concept-sign-ins.md) v Azure Active Directory (Azure AD) můžete najít odpovědi na otázky týkající se správy přístupu k aplikacím ve vaší organizaci, včetně:

- Jaký je vzorec přihlašování uživatele?
- Kolik uživatelů se přihlásilo za týden?
- Jaký je stav těchto přihlášení?


Kromě toho sestavy přihlášení také můžete řešení potíží se selháním přihlášení pro uživatele ve vaší organizaci. V této příručce se dozvíte, jak k izolaci selhání přihlášení v sestavě přihlášení a použít ho k najdete hlavní příčinu selhání.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat:

* Klient služby Azure AD s licenci úrovně premium (P1/P2). Zobrazit [Začínáme se službou Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) upgradovat edici Azure Active Directory.
* Uživatel, který je v **globálního správce**, **správce zabezpečení**, **Čtenář zabezpečení** nebo **čtečky sestav** role pro příslušného tenanta. Každý uživatel můžete navíc použít vlastní přihlášení. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Řešení potíží s chybami přihlášení pomocí sestavy přihlášení

1. Přejděte [webu Azure portal](https://portal.azure.com) a vyberte svůj adresář.
2. Vyberte **Azure Active Directory** a vyberte **přihlášení** z **monitorování** oddílu. 
3. Pomocí zadaného filtru zúžit selhání tak, že uživatelské jméno nebo identifikátor objektu, název aplikace nebo data. Kromě toho vyberte **selhání** z **stav** rozevíracího seznamu zobrazí pouze neúspěšných přihlášení. 

    ![Filtrování výsledků](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Určení neúspěšných přihlášení, které chcete prozkoumat a vyberte ji. Tím se otevře v okně Další podrobností s dalšími informacemi o neúspěšných přihlášení. Poznamenejte si **přihlášení kód chyby:** a **důvod selhání**. 

    ![Výběr záznamu](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Můžete také najít tyto informace **řešení potíží a podpora** karty v okně podrobností.

    ![Řešení potíží a podpora](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. Důvod selhání popisuje chybu. Například ve výše popsaném scénáři, příčina selhání je **neplatné uživatelské jméno nebo heslo nebo neplatné místní uživatelské jméno nebo heslo**. To znamená, že uživatel zadali jste nesprávné uživatelské jméno nebo heslo pro přihlášení k webu Azure portal. Oprava je jednoduše přihlásit znovu s správné uživatelské jméno a heslo.

7. Můžete získat další informace, včetně návrhů na odstranění problému, tak, že kód chyby: **50126** v tomto příkladu v [kódy chyb přihlášení odkaz](reference-sign-ins-error-codes.md). 

8. Pokud všechno ostatní selže, nebo problém nevyřeší, bez ohledu na převzetí kurzu doporučené akce, [vytvořit lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) podle kroků v **řešení potíží a podpora** kartu. 

## <a name="next-steps"></a>Další postup

* [Odkazovat na kódy chyb přihlášení](reference-sign-ins-error-codes.md)
* [Přehled sestavy přihlášení](concept-sign-ins.md)
