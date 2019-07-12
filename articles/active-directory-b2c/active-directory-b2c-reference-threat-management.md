---
title: Správa hrozeb k prostředkům a datům v Azure Active Directory B2C
description: Další informace o detekci a zmírnění distribuovaných útoků techniky pro útoky s cílem odepření služeb a útoky hesel v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7232917df6018c9c8afc7e7edd3730a277b193f4
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798219"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Správa hrozeb k prostředkům a datům v Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C má integrované funkce, které vám umožní chránit proti hrozbám k prostředkům a datům. Tyto hrozby zahrnout heslo útokům a útoky s cílem odepření služeb. Útoky s cílem odepření služeb může vytvářet prostředky k dispozici pro odpovídající uživatelé. Heslo útoky vést k neoprávněnému přístupu k prostředkům.

## <a name="denial-of-service-attacks"></a>Útoky s cílem odepření služeb

Azure AD B2C systémů proti útokům zahlcení SYN pomocí souboru cookie SYN. Azure AD B2C taky chrání před útoky DOS pomocí omezení pro míry a připojení.

## <a name="password-attacks"></a>Heslo útoky

Hesla, které jsou nastaveny uživatelé musejí být poměrně složité. Azure AD B2C zavedl techniky omezení rizik útoků heslo. Zmírnění dopadů zahrnuje detekce útoků hrubou silou heslo a slovníkové útoky heslo. Azure AD B2C s využitím různých signály, analyzuje integritu požadavků. Azure AD B2C je navržená k inteligentně rozlišení odpovídající uživatelé před hackery a botnetů.

Azure AD B2C používá sofistikované strategii pro uzamčení účtů. Účty jsou uzamčené na základě na adrese IP požadavku a zadaných heslech. Doba trvání uzamčení se taky zvyšuje podle pravděpodobnosti, že se jedná o útoku. Po heslo se neúspěšně pokusila 10krát (výchozí hodnota pokus o), nastane minutových uzamčení. Při příštím přihlášení neproběhne po účet odemknut (po účet byl automaticky odemčení službou po vypršení platnosti doby uzamčení), další minutu trvající uzamčení dochází a bude pokračovat pro každé neúspěšné přihlášení. Opakovaně zadávat stejné heslo nebude počítat jako několik neúspěšných přihlášení.

První období 10 uzamčení jsou dlouhé jednu minutu. Období dalších 10 uzamčení se trochu delší a zvýšení doby trvání za každých 10 uzamčení období. Resetování čítače uzamčení hodnotě nula. Po úspěšném přihlášení, když není účet uzamčen. Uzamčení období může trvat až pět hodin.

## <a name="manage-password-protection-settings"></a>Správa nastavení ochrany hesla

Správa nastavení ochrany hesla, včetně prahová hodnota pro uzamčení:

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Vyberte **adresář a předplatné** filtru v nabídce pravém horním rohu portálu a potom vyberte tenanta Azure AD B2C.
1. Vyberte **Azure Active Directory** v levé nabídce (nebo vyberte **všechny služby** v levé horní části portálu, vyhledejte a vyberte *Azure Active Directory*).
1. V části **zabezpečení**vyberte **metody ověřování**a pak vyberte **ochrana heslem**.
1. Zadejte požadované heslo nastavení ochrany a pak vyberte **Uložit**.

    ![Azure portal stránka heslo ochrany v nastavení Azure AD](media/active-directory-b2c-reference-threat-management/portal-02-password-protection.png)
    <br />*Nastavení prahová hodnota pro uzamčení na 5 v **ochrana heslem** nastavení*.

## <a name="view-locked-out-accounts"></a>Zobrazit uzamčený účty

Pokud chcete získat informace o účtech uzamčený, můžete zkontrolovat služby Active Directory [sestavy aktivit přihlašování](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md). V části **stav**vyberte **selhání**. Pokusů o přihlášení se nepovedlo **přihlášení kód chyby:** z `50053` označení uzamčené účtu:

![Část sestavy Azure AD přihlášení zobrazující uzamčený účet](media/active-directory-b2c-reference-threat-management/portal-01-locked-account.png)

Další informace o zobrazení sestavy aktivit přihlašování v Azure Active Directory najdete v tématu [přihlášení kódy chyb aktivit sestavy](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
