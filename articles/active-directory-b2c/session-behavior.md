---
title: Konfigurace chování relace – Služba Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Konfigurace chování relace ve službě Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c1d39fdbca9484f47ce0c8537c82247b75b2e3db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186807"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Konfigurace chování relace ve službě Azure Active Directory B2C

Tato funkce poskytuje jemně odstupňovanou kontrolu [na základě toku pro jednotlivé uživatele](user-flow-overview.md)z:

- Životnost relací webových aplikací spravovaných azure ad B2C.
- Chování jednotného přihlašování (SSO) napříč různými aplikacemi a toky uživatelů ve vašem tenantovi Azure AD B2C.

Tato nastavení nejsou k dispozici pro toky uživatelů resetování hesla.

Azure AD B2C podporuje [ověřovací protokol OpenID Connect](openid-connect.md) pro povolení zabezpečeného přihlášení k webovým aplikacím. Ke správě relací webových aplikací můžete použít následující vlastnosti:

## <a name="session-behavior-properties"></a>Vlastnosti chování relace

- **Životnost relace webové aplikace (minuty)** – životnost souboru cookie relace Azure AD B2C uloženého v prohlížeči uživatele po úspěšném ověření.
    - Výchozí = 1440 minut.
    - Minimální (včetně) = 15 minut.
    - Maximální (včetně) = 1440 minut.
- **Časový limit relace webové aplikace** – Pokud je tento přepínač nastaven na **hodnotu Absolutní**, je uživatel nucen znovu se ověřit po uplynutí doby určené **prodloužením relace webové aplikace (minuty).** Pokud je tento přepínač nastaven na **Rolling** (výchozí nastavení), uživatel zůstane přihlášen tak dlouho, dokud je uživatel neustále aktivní ve webové aplikaci.
- **Konfigurace jednotného přihlášení** Pokud máte více aplikací a toků uživatelů ve vašem tenantovi B2C, můžete spravovat interakce uživatelů mezi nimi pomocí vlastnosti **konfigurace jednotného přihlášení.** Vlastnost můžete nastavit na jedno z následujících nastavení:
    - **Tenant** - Toto nastavení je výchozí. Pomocí tohoto nastavení umožňuje více aplikací a toků uživatelů v tenantovi B2C sdílet stejnou relaci uživatele. Například, jakmile se uživatel přihlásí do aplikace, uživatel může také bezproblémově přihlásit do jiné, Contoso Lékárna, při přístupu k němu.
    - **Aplikace** – toto nastavení umožňuje udržovat relaci uživatele výhradně pro aplikaci, nezávisle na jiných aplikacích. Například pokud chcete, aby se uživatel přihlásit do Contoso Lékárna (se stejnými přihlašovacími údaji), i v případě, že uživatel je již přihlášen i contoso nakupování, jiné aplikace na stejném tenantovi B2C.
    - **Zásady** – toto nastavení umožňuje udržovat relaci uživatele výhradně pro tok uživatele, nezávisle na aplikacích, které jej používají. Například pokud uživatel již přihlášen a dokončil krok vícefaktorového ověřování (MFA), uživatel může mít přístup k vyšším bezpečnostním částem více aplikací, pokud relace vázaná na tok uživatele nevyprší.
    - **Zakázáno** – Toto nastavení vynutí, aby uživatel běžel přes celý tok uživatele při každém spuštění zásady.

Následující případy použití jsou povoleny pomocí těchto vlastností:

- Splňte požadavky na zabezpečení a dodržování předpisů vašeho odvětví nastavením příslušné doby trvání relace webové aplikace.
- Vynutit ověřování po uplynutí nastaveného časového období během interakce uživatele s vysoce zabezpečenou částí webové aplikace.

## <a name="configure-the-properties"></a>Konfigurace vlastností

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje klienta Azure AD B2C.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů (zásady).**
5. Otevřete tok uživatele, který jste dříve vytvořili.
6. Vyberte **vlastnosti**.
7. Podle potřeby nakonfigurujte **životnost relace webové aplikace (minuty),** časový limit relace webové **aplikace**, **konfiguraci jednotného přihlášení**a **požadavek na vyžadování tokenu ID v požadavcích na odhlášení.**

    ![Nastavení vlastností chování relace na webu Azure Portal](./media/session-behavior/session-behavior.png)

8. Klikněte na **Uložit**.
