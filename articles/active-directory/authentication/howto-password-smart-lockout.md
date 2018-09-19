---
title: Prevence útoků hrubou silou pomocí inteligentního uzamčení Azure AD
description: Azure Active Directory inteligentním uzamčením pomáhá chránit vaši organizaci před útoky hrubou silou uhádnutí hesla
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 9ea91f70a72b812803a20244bb4445b76b133b0c
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46296155"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory inteligentní uzamčení

Inteligentní uzamčení používá inteligentní cloudové funkce uzamknete nesprávnými účastníky, kteří se snaží uhodnout hesla uživatelů nebo využít hrubou silou. Tento intelligence rozpoznají přihlášení pocházející z platných uživatelů a jinak než těch, které útočníci a dalších neznámé zdroje jsou s nimi zacházet. Inteligentní uzamčení k uzamčení útočníci při se vaši uživatelé i nadále přístup ke svým účtům a produktivitu.

Ve výchozím nastavení zamkne inteligentní uzamčení účtu z pokusů o přihlášení pro jednu minutu po 10 neúspěšných pokusech o. Uzamčení účtu po každé následné selhání pokus o přihlášení, na minutu na první a delší dobu v následné pokusy.

Inteligentní uzamčení je vždy pro všechny zákazníky Azure AD se tato výchozí nastavení, které nabízejí správné kombinace zabezpečení a použitelnost. Přizpůsobení nastavení inteligentním uzamčením, s hodnotami, které jsou specifické pro vaši organizaci, vyžaduje Azure AD Basic nebo vyšší licencí pro vaše uživatele.

Inteligentní uzamčení lze integrovat s hybridní nasazení pomocí synchronizace hodnot hash hesel nebo předávací ověřování ochrana místních účtů služby Active Directory zamknutí útočníci. Inteligentní uzamčení zásady nastavíte odpovídajícím způsobem ve službě Azure AD, dají se útoky odfiltrovat dřív, než dorazí v místní službě Active Directory.

Při použití [předávací ověřování](../hybrid/how-to-connect-pta.md), je třeba Ujistěte se, že:

   * Prahová hodnota pro uzamčení Azure AD je **méně** než prahovou hodnotu uzamknutí účtu služby Active Directory. Nastavte hodnoty tak, aby prahovou hodnotu uzamknutí účtu služby Active Directory je minimálně dvakrát nebo třikrát déle než prahová hodnota pro uzamčení Azure AD. 
   * Doba trvání uzamčení Azure AD **během několika sekund** je **delší** než Active Directory Vynulovat čítač uzamčení účtu po dobu trvání **minut**.

> [!IMPORTANT]
> Správce aktuálně nedá odemknout účtům cloudových uživatelů, pokud se mají byl uzamčen inteligentním uzamčením schopnosti. Správce musí počkat po dobu trvání uzamknutí vyprší.

## <a name="verify-on-premises-account-lockout-policy"></a>Zkontrolujte místní zásady uzamčení účtů

Chcete-li ověřit vaše místní zásady uzamčení účtů služby Active Directory použijte následující pokyny:

1. Spusťte nástroj pro správu zásad skupiny.
2. Upravit zásady skupiny, které obsahuje zásady uzamčení účtu vaší organizace, například **výchozí zásady domény**.
3. Přejděte do **konfigurace počítače** > **zásady** > **nastavení Windows** > **nastavení zabezpečení**   >  **Zásady účtů** > **zásady uzamčení účtů**.
4. Ověřte vaše **prahovou hodnotu uzamknutí účtu** a **resetování čítače uzamčení účtu po** hodnoty.

![Upravit pomocí objektu zásad skupiny zásady uzamčení účtu místní služby Active Directory](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Správa služby Azure AD inteligentním uzamčením hodnot

Podle požadavků vaší organizace, inteligentním uzamčením hodnot může být potřeba upravit. Přizpůsobení nastavení inteligentním uzamčením, s hodnotami, které jsou specifické pro vaši organizaci, vyžaduje Azure AD Basic nebo vyšší licencí pro vaše uživatele.

Chcete-li zkontrolovat nebo změnit inteligentním uzamčením hodnoty pro vaši organizaci, použijte následující kroky:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a klikněte na **Azure Active Directory**, pak **metody ověřování**.
1. Nastavte **prahová hodnota pro uzamčení**závislosti na počtu neúspěšných přihlášení jsou povoleny v rámci účtu před jeho první uzamčení. Výchozí hodnota je 10.
1. Nastavte **doba trvání uzamčení v řádu sekund**, délku každého uzamčení v řádu sekund. Výchozí hodnota je 60 sekund (jednu minutu).

> [!NOTE]
> Pokud první přihlášení po uzamčení je také neúspěšná, účet zamezí znovu. Pokud účet uzamkne opakovaně, doba trvání uzamčení se zvýší.

![Přizpůsobte si zásadu inteligentním uzamčením Azure AD na webu Azure Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)
## <a name="next-steps"></a>Další postup

[Zjistěte, jak zakázat chybná hesla ve vaší organizaci pomocí služby Azure AD.](howto-password-ban-bad.md)

[Nakonfigurujte samoobslužné resetování hesla a umožňují uživatelům odemknout své účty.](quickstart-sspr.md)