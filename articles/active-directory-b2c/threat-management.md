---
title: Správa hrozeb pro prostředky a data
titleSuffix: Azure AD B2C
description: Seznamte se s technikami zjišťování a zmírnění útoků při odepření služby a útokech na heslo v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e5184698cab1874f327173fb30cf527feee48cad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85384970"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Správa hrozeb pro prostředky a data v Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) obsahuje integrované funkce, které vám pomůžou chránit před hrozbami vašich prostředků a dat. Mezi tyto hrozby patří útoky DoS (Denial-of-Service) a útoky na hesla. Útoky DoS (Denial-of-Service) mohou způsobit nedostupnost prostředků určeným uživatelům. Útoky na heslo mají za následek neoprávněný přístup k prostředkům.

## <a name="denial-of-service-attacks"></a>Útoky DoS (Denial-of-Service)

Azure AD B2C bránit útokům na zaplavení SYN pomocí souborů cookie SYN. Azure AD B2C také chrání před útoky DoS (Denial of-Service) pomocí limitů pro sazby a připojení.

## <a name="password-attacks"></a>Útoky prostřednictvím hesla

Hesla nastavená uživateli musí být rozumně složitá. Azure AD B2C jsou pro útoky na hesla zavedeny techniky zmírnění. Zmírnění rizik zahrnuje detekci útoků na heslo hrubou silou a hesla slovníku. Azure AD B2C analyzuje integritu požadavků pomocí různých signálů. Azure AD B2C je navržená tak, aby inteligentně rozlišila zamýšlené uživatele od hackerů a botnety.

Azure AD B2C používá k uzamčení účtů sofistikovanou strategii. Účty jsou uzamčené na základě IP adresy žádosti a hesla, která jste zadali. Doba trvání uzamčení se také zvyšuje na základě pravděpodobnosti, že se jedná o útok. Po neúspěšném pokusu o zadání hesla (výchozí prahová hodnota pro pokusy o výchozí hodnotě) dojde k uzamčení po dobu jednoho minuty. Až se příště po odemčení účtu neúspěšně přihlásí (tj. po vypršení platnosti účtu ho služba automaticky odemkne), dojde k dalšímu uzamčení a bude pokračovat pro každé neúspěšné přihlášení. Opakované zadání hesla se nepočítá jako vícenásobná neúspěšná přihlášení.

Prvních 10 intervalů uzamčení je jedna minuta. Příštích 10 období uzamčení je trochu delší a prodlouží se v době trvání po každých 10 intervalech uzamčení. Čítač uzamčení se po úspěšném přihlášení vynuluje, když účet není zamknutý. Doba uzamčení může trvat až pět hodin.

## <a name="manage-password-protection-settings"></a>Správa nastavení ochrany heslem

Správa nastavení ochrany heslem, včetně prahové hodnoty uzamčení:

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Pomocí filtru **adresář a odběr** v horní nabídce vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. V části **zabezpečení** vyberte **metody ověřování (Preview)** a pak vyberte **ochrana heslem**.
1. Zadejte požadovaná nastavení ochrany heslem a pak vyberte **Uložit**.

    ![Stránka Azure Portal ochrana heslem v nastavení Azure AD](./media/threat-management/portal-02-password-protection.png)
    <br />*Nastavení prahové hodnoty pro uzamčení na 5 v nastavení **ochrany heslem***.

## <a name="view-locked-out-accounts"></a>Zobrazit uzamčené účty

Chcete-li získat informace o uzamčených účtech, můžete zaškrtnout [sestavu aktivita přihlášení](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)služby Active Directory. V části **stav** vyberte **selhání**. Neúspěšné pokusy o přihlášení pomocí **kódu chyby přihlášení** `50053` indikující uzamčený účet:

![Část sestavy přihlášení ke službě Azure AD zobrazující účet uzamčeného účtu](./media/threat-management/portal-01-locked-account.png)

Další informace o zobrazení sestavy aktivit přihlašování v Azure Active Directory najdete v tématu [kódy chyb sestav aktivit přihlašování](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
