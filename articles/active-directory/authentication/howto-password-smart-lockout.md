---
title: Prevence útoků hrubou silou pomocí Azure AD inteligentní uzamčení
description: Azure Active Directory inteligentní uzamčení pomáhá chránit vaše organizace před útoky hrubou silou uhádnutí hesla
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: d5beb5ce6e167cd100bec2ed54dc6ea0e78ba37b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035747"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory inteligentní uzamčení

Inteligentní uzamčení používá intelligence cloudu na uzamčení nesprávnými účastníky, kteří se snaží uhodnout hesla uživatelů nebo k získání v použít metody hrubou silou. Tento intelligence rozpoznají přihlášení pocházejících z platné uživatele a pracovat s nimi jinak než ty, které útočníci a další neznámé zdroje. Inteligentní uzamčení můžete zablokovat útočníci přitom vaši uživatelé dál přistupovat ke své účty a produktivitu.

Inteligentní uzamčení je vždycky zapnuté pro všechny zákazníky Azure AD s výchozím nastavením, které nabízejí správné směs zabezpečení a použitelnost. Přizpůsobení nastavení inteligentního uzamčení s hodnotami, které jsou specifické pro vaši organizaci, vyžaduje Azure AD Basic nebo vyšší licencí pro vaše uživatele.

Inteligentní uzamčení lze integrovat s hybridní nasazení, pomocí synchronizace hodnot hash hesel nebo předávací ověřování k ochraně místních účtů služby Active Directory z uzamknutí útočníků. Nastavením zásad inteligentní uzamčení ve službě Azure AD správně, je možné filtrovat útoků než dosáhnou místní služby Active Directory.

Při použití [předávací ověřování](../connect/active-directory-aadconnect-pass-through-authentication.md), budete muset Ujistěte se, že:

   * Prahová hodnota pro uzamčení Azure AD je **méně** než prahovou hodnotu uzamknutí účtu služby Active Directory. Nastavte hodnoty tak, aby prahovou hodnotu uzamknutí účtu služby Active Directory je minimálně dvě nebo tři dobu delší než prahové hodnoty počtu uzamčení Azure AD. 
   * Doba trvání uzamčení Azure AD **v sekundách** je **delší** než služby Active Directory resetu čítače uzamčení účtu po dobu trvání **minut**.

> [!IMPORTANT]
> Správce aktuálně nemůžete odemknout uživatelské účty cloudu, pokud budou mít byl uzamčen schopnosti inteligentní uzamčení. Správce musí počkat doba trvání uzamčení vyprší.

## <a name="verify-on-premises-account-lockout-policy"></a>Zkontrolujte místní zásada pro uzamčení účtu

Chcete-li ověřit vaše místní zásady uzamčení účtů služby Active Directory, postupujte podle následujících pokynů:

1. Spusťte nástroj pro správu zásad skupiny.
2. Upravit zásady skupiny, které zahrnuje zásada pro uzamčení účtu vaší organizace, například **výchozí zásady domény**.
3. Přejděte do **konfigurace počítače** > **zásady** > **nastavení systému Windows** > **nastavení zabezpečení**   >  **Zásady účtů** > **zásada pro uzamčení účtu**.
4. Ověřte vaší **prahovou hodnotu uzamknutí účtu** a **resetování čítače uzamčení účtu po** hodnoty.

![Upravit pomocí objektu zásad skupiny zásady uzamčení účtu místní služby Active Directory](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Správa Azure AD inteligentní uzamčení hodnot

Podle požadavků vaší organizace, inteligentní uzamčení hodnoty možná muset přizpůsobit. Přizpůsobení nastavení inteligentního uzamčení s hodnotami, které jsou specifické pro vaši organizaci, vyžaduje Azure AD Basic nebo vyšší licencí pro vaše uživatele.

Pokud chcete zkontrolovat nebo změnit hodnoty inteligentní uzamčení pro vaši organizaci, použijte následující kroky:

1. Přihlaste se k [portál Azure](https://portal.azure.com)a klikněte na **Azure Active Directory**, pak **metody ověřování**.
1. Nastavte **prahové hodnoty počtu uzamčení**, závislosti na tom, kolik neúspěšných přihlášení jsou povoleny pro účet před jeho první uzamčení. Výchozí hodnota je 10.
1. Nastavte **doba trvání uzamčení v sekundách**, délce každý uzamčení v sekundách.

> [!NOTE]
> Pokud první přihlášení po uzamčení se také nezdaří, účet zamezí znovu. Pokud účet zamkne opakovaně, zvýší se doba trvání uzamčení.

![Přizpůsobit zásady inteligentní uzamčení Azure AD na portálu Azure](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)
## <a name="next-steps"></a>Další postup

[Zjistěte, jak zakázat chybná hesla ve vaší organizaci pomocí služby Azure AD.](howto-password-ban-bad.md)

[Konfigurace samoobslužné služby heslo resetovat, aby uživatelé mohli odemknout svoje vlastní účty.](quickstart-sspr.md)