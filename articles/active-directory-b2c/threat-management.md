---
title: Správa hrozeb pro zdroje a data
titleSuffix: Azure AD B2C
description: Další informace o technikách zjišťování a zmírňování útoků typu denial-of-service a útoků hesel ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a438363b054361420222804dffac7973470e82e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183597"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Správa hrozeb pro prostředky a data ve službě Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) má integrované funkce, které vám pomůžou chránit před ohrožením vašich prostředků a dat. Mezi tyto hrozby patří útoky typu denial of service a útoky heslem. Útoky s cílem dosazovat službu mohou znepřístupnit prostředky zamýšleným uživatelům. Útoky heslem vedou k neoprávněnému přístupu k prostředkům.

## <a name="denial-of-service-attacks"></a>Útoky s cílem odepření služby

Azure AD B2C brání proti útokům syn povodňové pomocí souboru cookie SYN. Azure AD B2C také chrání před útoky odmítnutí služby pomocí omezení pro sazby a připojení.

## <a name="password-attacks"></a>Útoky heslem

Hesla, která jsou nastavena uživateli, musí být přiměřeně složitá. Azure AD B2C má zmírnění techniky na místě pro útoky heslem. Zmírnění zahrnuje detekci útoků hrubou silou hesla a slovníkových hesel útoků. Pomocí různých signálů Azure AD B2C analyzuje integritu požadavků. Azure AD B2C je navržený tak, aby inteligentně odlišit zamýšlené uživatele od hackerů a botnetů.

Azure AD B2C používá propracovanou strategii pro uzamčení účtů. Účty jsou uzamčeny na základě IP adresy požadavku a zadaných hesel. Doba trvání uzamčení se také zvyšuje na základě pravděpodobnosti, že se jedná o útok. Po pokusu o heslo 10 krát neúspěšně (výchozí prahová hodnota pokusu) dojde k jednominutovéuzamčení. Při příštím přihlášení je neúspěšný po odemknutí účtu (to znamená, že poté, co byl účet automaticky odemknut službou po vypršení doby uzamčení), dojde k dalšímu jednominutovému uzamčení a pokračuje pro každé neúspěšné přihlášení. Opakované zadání stejného hesla se nepočítá jako více neúspěšných přihlášení.

Prvních 10 výlukových období je dlouhá jedna minuta. Dalších 10 období uzamčení je o něco delší chod a po každých 10 obdobích uzamčení se jejich trvání zvětšuje. Počítadlo uzamčení se po úspěšném přihlášení resetuje na nulu, když účet není uzamčen. Výluka může trvat až pět hodin.

## <a name="manage-password-protection-settings"></a>Správa nastavení ochrany heslem

Správa nastavení ochrany heslem, včetně prahové hodnoty uzamčení:

1. Přihlášení k [portálu Azure](https://portal.azure.com)
1. Pomocí filtru **Directory + odběr** v horní nabídce vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. V části **Zabezpečení**vyberte **Metody ověřování (Preview)** a pak vyberte **Ochrana heslem**.
1. Zadejte požadované nastavení ochrany heslem a pak vyberte **Uložit**.

    ![Stránka ochrany heslem na portálu Azure v nastavení Azure AD](./media/threat-management/portal-02-password-protection.png)
    <br />*Nastavení prahové hodnoty uzamčení na hodnotu 5 v nastavení **ochrany heslem** *.

## <a name="view-locked-out-accounts"></a>Zobrazení uzamčených účtů

Chcete-li získat informace o uzamčených účtech, můžete zkontrolovat [sestavu aktivit přihlášení](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)služby Active Directory . V části **Stav**vyberte **Možnost Selhání**. Neúspěšné pokusy o přihlášení s **kódem chyby** `50053` přihlášení označující uzamčený účet:

![Část sestavy přihlášení Azure AD zobrazující uzamčený účet](./media/threat-management/portal-01-locked-account.png)

Informace o zobrazení sestavy přihlašovacích aktivit ve službě Azure Active Directory najdete v [tématu Chybové kódy sestavy přihlašovacích aktivit](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
