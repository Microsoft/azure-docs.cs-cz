---
title: Přihlášení bez hesla pomocí aplikace Microsoft Authenticator Azure Active Directory
description: Povolení přihlášení neheslem do Azure AD pomocí aplikace Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/11/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e6cd7efcd0e851c15975aba5ff9b99c615eb7d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653469"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app"></a>Povolení přihlášení bez hesla pomocí aplikace Microsoft Authenticator 

Aplikaci Microsoft Authenticator můžete použít k přihlášení k jakémukoli účtu Azure AD bez použití hesla. Microsoft Authenticator používá ověřování na základě klíčů k povolení přihlašovacích údajů uživatele, které jsou vázané na zařízení, kde zařízení používá kód PIN nebo biometrické údaje. [Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-identity-verification) používá podobnou technologii.

Tato technologie ověřování se dá použít na libovolné platformě zařízení, včetně mobilních. Tuto technologii je taky možné použít u libovolné aplikace nebo webu, která se integruje s knihovnami ověřování Microsoftu.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="Příklad přihlašování prohlížeče, které žádá uživatele o schválení přihlášení.":::

Lidé, kteří povolili telefonické přihlášení z aplikace Microsoft Authenticator uvidí zprávu s žádostí o klepnutí na číslo ve své aplikaci. Nepožaduje se žádné uživatelské jméno ani heslo. K dokončení procesu přihlašování v aplikaci musí uživatel dále provádět tyto akce:

1. Odpovídá číslu.
2. Zvolte **Schválit**.
3. Zadejte kód PIN nebo biometrické údaje.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat přihlášení bez hesla pro telefonickou práci s aplikací Microsoft Authenticator, musí být splněné následující předpoklady:

- Multi-Factor Authentication Azure AD s nabízenými oznámeními, která jsou povolená jako metoda ověřování.
- Na zařízeních se systémem iOS 8,0 nebo vyšším je nainstalovaná nejnovější verze Microsoft Authenticator nebo Android 6,0 nebo novější.

> [!NOTE]
> Pokud jste povolili Microsoft Authenticator přihlašování bez hesla pomocí Azure AD PowerShellu, je povolený pro celý adresář. Pokud povolíte použití této nové metody, bude nahrazena zásadou PowerShellu. Doporučujeme povolit pro všechny uživatele ve vašem tenantovi prostřednictvím nové nabídky *metody ověřování* , jinak se uživatelé, kteří nejsou v nové zásadě, už nebudou moct přihlásit bez hesla.

## <a name="enable-passwordless-authentication-methods"></a>Povolit metody ověřování nejenom pro hesla

Pokud chcete ve službě Azure AD používat ověřování bez hesla, nejdřív povolte kombinované možnosti registrace a pak uživatelům povolte metodu less pro heslo.

### <a name="enable-the-combined-registration-experience"></a>Povolit kombinované možnosti registrace

Funkce registrace pro metody ověřování nevyužívající hesla se spoléhají na funkci kombinované registrace. Pokud chcete, aby uživatelé mohli provést kombinovanou registraci sami, postupujte podle pokynů pro [Povolení registrace kombinovaných informací o zabezpečení](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Povolit metody ověřování přihlašování bezheslem pro telefonování

Azure AD umožňuje vybrat, které metody ověřování se dají použít během procesu přihlašování. Potom si zaregistrujte metody, které chce použít.

Pokud chcete metodu ověřování povolit pro přihlášení telefon bez hesla, proveďte následující kroky:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu *globálního správce* .
1. Vyhledejte a vyberte *Azure Active Directory* a pak přejděte do zásad **zabezpečení**  >  **metody ověřování**  >  .
1. V části **Microsoft Authenticator** vyberte následující možnosti:
   1. **Povolit** – Ano nebo ne
   1. **Cíl** – všichni uživatelé nebo vybrat uživatele
1. Každá přidaná skupina nebo uživatel je ve výchozím nastavení povolená, aby používala Microsoft Authenticator v režimech nabízených oznámení bez hesla i v režimu nabízených oznámení ("libovolný"). To můžete změnit pro každý řádek:
   1. Přejít na **...**  >  **Nakonfigurujte**.
   1. **Režim ověřování** – libovolná, bez hesla nebo nabízená oznámení
1. Chcete-li použít novou zásadu, vyberte možnost **Uložit**.

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Registrace a Správa uživatelů Microsoft Authenticator

Uživatelé se sami registrují pro metodu ověřování typu Azure AD s neheslem pomocí následujících kroků:

1. Přejděte na [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Přihlaste se a pak přidejte ověřovací aplikaci výběrem **Přidat metodu > ověřovací aplikace** a pak **Přidat**.
1. Podle pokynů nainstalujte a nakonfigurujte aplikaci Microsoft Authenticator na svém zařízení.
1. Vyberte **Hotovo** , aby se konfigurace ověřovatele dokončila.
1. V **Microsoft Authenticator** v rozevírací nabídce pro registrovaný účet vyberte **Povolit přihlášení přes telefon** .
1. Podle pokynů v aplikaci dokončete registraci účtu pro přihlášení přes telefon s neheslem.

Organizace může směrovat své uživatele, aby se přihlásili pomocí telefonů bez použití hesla. Další pomoc při konfiguraci Microsoft Authenticator aplikace a povolení přihlášení k telefonu najdete v tématu [přihlášení ke svým účtům pomocí aplikace Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

> [!NOTE]
> Uživatelé, kterým zásada nepovoluje používat přihlášení telefonem, už je nepůjde povolit v aplikaci Microsoft Authenticator.

## <a name="sign-in-with-passwordless-credential"></a>Přihlášení pomocí přihlašovacích údajů bez hesla

Uživatel může začít využívat přihlášení neplatným heslem po dokončení všech následujících akcí:

- Správce povolil tenanta daného uživatele.
- Uživatel aktualizoval svoji aplikaci Microsoft Authenticator, aby povolila přihlašování telefonem.

Když uživatel poprvé spustí proces přihlášení k telefonu, provede následující kroky:

1. Na přihlašovací stránku se vloží jeho jméno.
2. Vybere **Další**.
3. V případě potřeby si vyberou **jiné způsoby, jak se přihlásit**.
4. Vybere **v aplikaci Microsoft Authenticator schválit požadavek**.

Uživateli se pak zobrazí číslo. Aplikace vyzve uživatele k ověření tak, že vybere odpovídající číslo místo zadáním hesla.

Když uživatel použije přihlašování pomocí telefonu bez hesla, aplikace bude tuto metodu i nadále řídit uživatelem. Uživateli se ale zobrazí možnost zvolit jinou metodu.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Příklad přihlášení prohlížeče pomocí aplikace Microsoft Authenticator":::

## <a name="known-issues"></a>Známé problémy

Existují následující známé problémy.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Nezobrazuje se možnost pro přihlášení bez hesla pro telefonování

V jednom scénáři může uživatel mít nezodpovězené ověřování telefonického přihlášení k telefonu bez hesla, které čeká na vyřízení. Uživatel se ale může pokusit znovu přihlásit. Pokud k tomu dojde, může se uživateli zobrazit jenom možnost zadat heslo.

Tento scénář můžete vyřešit pomocí následujících kroků:

1. Otevřete aplikaci Microsoft Authenticator.
2. Reagovat na výzvy oznámení.

Uživatel pak může i nadále používat přihlašování přes telefon s neheslem.

### <a name="federated-accounts"></a>Federované účty

Když uživatel povolí jakékoli přihlašovací údaje bez hesla, proces přihlášení Azure AD přestane používat \_ pomocný parametr pro přihlášení. Proto proces již nezrychluje uživatele směrem k umístění federovaného přihlášení.

Tato logika obecně brání uživateli v hybridním tenantovi směrovat se na federované služby Active Directory (AD FS) pro ověření přihlášení. Uživatel ale uchová možnost **použít místo toho heslo**.

### <a name="azure-mfa-server"></a>Azure MFA Server

Koncový uživatel může povolit službu Multi-Factor Authentication (MFA) prostřednictvím místního Azure MFA serveru. Uživatel může stále vytvořit a používat jedno přihlašovací údaje pro přihlášení k telefonickému přihlášení k neheslu.

Pokud se uživatel pokusí upgradovat více instalací (5 +) aplikace Microsoft Authenticator s přihlašovacími údaji pro přihlášení bez hesla pro telefon, může tato změna způsobit chybu.

### <a name="device-registration"></a>Registrace zařízení

Než budete moct vytvořit toto nové silné přihlašovací údaje, existují nějaké požadavky. Jednou z předpokladů je, že zařízení, na kterém je nainstalovaná aplikace Microsoft Authenticator, musí být zaregistrované v rámci tenanta Azure AD pro jednotlivé uživatele.

V současné době je možné zařízení zaregistrovat pouze v jednom tenantovi. Tento limit znamená, že pro přihlašování telefonem se dá povolit jenom jeden pracovní nebo školní účet v aplikaci Microsoft Authenticator.

> [!NOTE]
> Registrace zařízení není stejná jako Správa zařízení nebo Správa mobilních zařízení (MDM). Registrace zařízení přidružuje pouze ID zařízení a ID uživatele společně v adresáři Azure AD.

## <a name="next-steps"></a>Další kroky

Další informace o ověřování Azure AD a metodách bez hesla najdete v následujících článcích:

- [Informace o tom, jak funguje ověřování heslem](concept-authentication-passwordless.md)
- [Další informace o registraci zařízení](../devices/overview.md#getting-devices-in-azure-ad)
- [Přečtěte si o Multi-Factor Authentication Azure AD](../authentication/howto-mfa-getstarted.md)
