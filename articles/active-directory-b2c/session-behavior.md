---
title: Konfigurace chování relace – Azure Active Directory B2C | Microsoft Docs
description: Nakonfigurujte chování relace v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 249b9bb282024431d0ecd38c62d8d780602e6709
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229965"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Konfigurace chování relace v Azure Active Directory B2C

Tato funkce poskytuje jemně odstupňovaný ovládací prvek na [základě toku pro jednotlivé uživatele](user-flow-overview.md):

- Životnost relací webových aplikací, které jsou spravovány Azure AD B2C.
- Chování jednotného přihlašování (SSO) napříč několika aplikacemi a toky uživatelů ve vašem Azure AD B2C tenantovi.

Tato nastavení nejsou k dispozici pro uživatelské toky resetování hesla.

Azure AD B2C podporuje [ověřovací protokol OpenID Connect](openid-connect.md) pro povolení zabezpečeného přihlašování k webovým aplikacím. Pomocí následujících vlastností můžete spravovat relace webových aplikací:

## <a name="session-behavior-properties"></a>Vlastnosti chování relace

- **Doba života relace webové aplikace (minuty)** – doba života souboru cookie relace Azure AD B2C's, která se po úspěšném ověření uloží v prohlížeči uživatele.
    - Výchozí hodnota = 1440 minut.
    - Minimální (včetně) = 15 minut.
    - Maximum (včetně) = 1440 minut.
- **Časový limit relace webové aplikace** – Pokud je tento přepínač nastavený na **absolutní**, uživatel se po uplynutí časového intervalu zadaného v době **Trvání relace webové aplikace (minuty)** bude muset znovu ověřit. Pokud je tento přepínač nastavený na **vracení** (výchozí nastavení), zůstane uživatel přihlášený, dokud bude uživatel ve vaší webové aplikaci neustále aktivní.
- **Konfigurace jednotného přihlašování** Pokud máte ve svém tenantovi B2C více aplikací a toků uživatelů, můžete pomocí vlastnosti **Konfigurace jednotného přihlašování** spravovat interakce uživatelů napříč nimi. Vlastnost můžete nastavit na jedno z následujících nastavení:
    - **Tenant** – toto nastavení je výchozí. Pomocí tohoto nastavení může více aplikací a uživatelských toků v tenantovi B2C sdílet stejnou relaci uživatele. Když se například uživatel přihlásí do aplikace, může se při přístupu uživatele bez problémů přihlásit k jiné společnosti Contoso farmacie.
    - **Aplikace** – toto nastavení umožňuje udržovat uživatelskou relaci exkluzivně pro aplikaci, nezávisle na jiných aplikacích. Například pokud chcete, aby se uživatel přihlásil do společnosti Contoso Farmaci (se stejnými přihlašovacími údaji), i když je uživatel již přihlášen k nákupu společnosti Contoso, jiná aplikace ve stejném tenantovi B2C.
    - **Zásady** – toto nastavení umožňuje udržovat relaci uživatele výhradně pro uživatelský tok, a to nezávisle na aplikacích, které ji používají. Pokud se například uživatel už přihlásil a dokončil krok vícefaktorového ověřování (MFA), může se uživateli udělit přístup k několika částem aplikace s vyšším zabezpečením, pokud relace vázaná na tok uživatele nevyprší.
    - **Disabled** – toto nastavení vynutí, aby uživatel při každém spuštění zásady spouštěl celý tok uživatele.

Následující případy použití jsou povolené pomocí těchto vlastností:

- Splnění požadavků na zabezpečení a dodržování předpisů v oboru nastavením příslušných životností relací webových aplikací.
- Vynutí ověřování po nastaveném časovém období během interakce uživatele s vysokou zabezpečením části vaší webové aplikace.

## <a name="configure-the-properties"></a>Konfigurace vlastností

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a vyberete adresář, který obsahuje vašeho Azure AD B2Cho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů (zásady)**.
5. Otevřete uživatelský tok, který jste vytvořili dříve.
6. Vyberte **Vlastnosti**.
7. Nakonfigurujte **dobu života relace webové aplikace (minuty)**, **časový limit relace webové aplikace**, **konfiguraci jednotného přihlašování**a **v případě potřeby vyžadovat token ID v žádostech o odhlášení** .

    ![Nastavení vlastností chování relace v Azure Portal](./media/session-behavior/session-behavior.png)

8. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure AD B2C relaci](session-overview.md).