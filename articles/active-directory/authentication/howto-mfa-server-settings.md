---
title: Konfigurace MFA serveru – Azure Active Directory
description: Naučte se konfigurovat nastavení pro Azure MFA Server v Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69733071c5b43ee9c8e6450e3a9924bc656d5c84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84485606"
---
# <a name="configure-mfa-server-settings"></a>Konfigurovat nastavení serveru MFA

Tento článek vám pomůže se správou nastavení serveru Azure MFA v Azure Portal.

> [!IMPORTANT]
> Od 1. července 2019 už Microsoft nenabídne MFA Server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat službu Multi-Factor Authentication od uživatelů, by měli používat cloudové Multi-Factor Authentication Azure. Stávající zákazníci, kteří mají aktivovaný MFA Server před 1. července, budou moci stáhnout nejnovější verzi, budoucí aktualizace a generovat přihlašovací údaje pro aktivaci obvyklým způsobem.

K dispozici jsou následující nastavení serveru MFA:

| Funkce | Popis |
| ------- | ----------- |
| Nastavení serveru | Stáhněte si MFA Server a vygenerujte aktivační přihlašovací údaje pro inicializaci vašeho prostředí. |
| [Jednorázové přihlášení](#one-time-bypass) | Povolí uživateli ověřování bez provádění služby Multi-Factor Authentication po dobu omezeného času. |
| [Pravidla ukládání do mezipaměti](#caching-rules) |  Ukládání do mezipaměti se primárně používá v případě, že místní systémy, jako je například síť VPN, odesílají více žádostí o ověření v době, kdy stále probíhá první žádost. Tato funkce umožňuje, aby následné požadavky byly automaticky úspěšné a poté, co uživatel úspěšně provedl první ověření. |
| Stav serveru | Podívejte se na stav místních MFA serverů, včetně verze, stavu, IP adresy a času poslední komunikace a data. |

## <a name="one-time-bypass"></a>Jednorázové přihlášení

Funkce jednorázového přihlášení umožňuje uživateli jednorázové ověřování, aniž by prováděl službu Multi-Factor Authentication. Vynechání je dočasné a po zadaném počtu sekund vyprší platnost. V situacích, kdy mobilní aplikace nebo telefon nepřijímá oznámení nebo telefonní hovor, můžete umožnit jednorázové přihlášení, aby uživatel mohl získat přístup k požadovanému prostředku.

Chcete-li vytvořit jednorázové přihlášení, proveďte následující kroky:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce.
1. Vyhledejte a vyberte **Azure Active Directory**a pak přejděte na **zabezpečení**  >  **MFA**  >  **jednorázového přihlášení**.
1. Vyberte **Přidat**.
1. V případě potřeby vyberte replikační skupinu pro vynechání.
1. Zadejte uživatelské jméno jako `username\@domain.com` . Zadejte počet sekund, po které má být vynechání trvat, a důvod pro vynechání.
1. Vyberte **Přidat**. Časový limit se projeví okamžitě. Uživatel se musí přihlásit předtím, než vyprší doba přihlášení k jednorázovému přihlášení.

Můžete také zobrazit sestavu jednorázového přihlášení z tohoto okna.

## <a name="caching-rules"></a>Pravidla ukládání do mezipaměti

Můžete nastavit časové období, které umožní pokusy o ověření po ověření uživatele pomocí funkce _ukládání do mezipaměti_ . Následné pokusy o ověření pro uživatele v zadaném časovém období jsou automaticky úspěšné.

Ukládání do mezipaměti se primárně používá v případě, že místní systémy, jako je například síť VPN, odesílají více žádostí o ověření v době, kdy stále probíhá první žádost. Tato funkce umožňuje, aby následné požadavky byly automaticky úspěšné a poté, co uživatel úspěšně provedl první ověření.

>[!NOTE]
> Funkce ukládání do mezipaměti není určena k použití pro přihlášení k Azure Active Directory (Azure AD).

Pokud chcete nastavit ukládání do mezipaměti, proveďte následující kroky:

1. Přejděte na **Azure Active Directory**  >  **zabezpečení**  >  **MFA**  >  **pravidel ukládání do mezipaměti**MFA.
1. Vyberte **Přidat**.
1. Z rozevíracího seznamu vyberte **typ mezipaměti** . Zadejte maximální počet **sekund mezipaměti**.
1. V případě potřeby vyberte typ ověřování a zadejte aplikaci.
1. Vyberte **Přidat**.

## <a name="next-steps"></a>Další kroky

Další možnosti konfigurace MFA serveru jsou dostupné z webové konzole samotného serveru MFA. Můžete taky [nakonfigurovat Azure MFA Server pro zajištění vysoké dostupnosti](howto-mfaserver-deploy-ha.md).
