---
title: Postup povolení jednotného přihlašování napříč aplikacemi v Androidu pomocí knihovny ADAL | Dokumentace Microsoftu
description: Jak používat funkce knihovny ADAL sady SDK k povolení jednotného přihlašování napříč aplikacemi.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 2c7ccd8d2022631e32c240007a782d2382aac518
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422843"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Postupy: povolení jednotného přihlašování napříč aplikacemi v systému Android pomocí ADAL

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Jednotné přihlašování (SSO) umožňuje uživatelům pouze zadat své přihlašovací údaje jednou a mít těchto přihlašovacích údajů napříč aplikacemi a na různých platformách, které mohou používat další aplikace (například Accounts Microsoft nebo pracovní účet z Microsoft 365) mělo automaticky fungovat bez důležitá vydavatele.

Platforma identit společnosti Microsoft, spolu se sadami SDK usnadňuje povolení jednotného přihlašování v rámci vlastní sadu aplikací, nebo zprostředkovatel funkce a aplikace Authenticator napříč celé zařízení.

V tomto návodu budete zjistěte, jak nakonfigurovat sady SDK v rámci vaší aplikace k vašim zákazníkům poskytovat jednotné přihlašování.

## <a name="prerequisites"></a>Požadavky

Tento návod předpokládá, že víte, jak:

- Zřídíte aplikace pomocí starší verze portálu pro Azure Active Directory (Azure AD). Další informace najdete v tématu [registrace aplikace ke koncovému bodu Azure AD verze 1.0](quickstart-v1-add-azure-ad-app.md)
- Integrace aplikace pomocí [Azure AD Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Koncepty jednotné přihlašování

### <a name="identity-brokers"></a>Zprostředkovatelé identity

Společnost Microsoft poskytuje pro každou mobilní platformu aplikace, které umožňují přemostění přihlašovacích údajů napříč aplikacemi od různých dodavatelů a vylepšené funkce, které vyžadují jedno zabezpečené místo, odkud se ověřují přihlašovací údaje. Toto nastavení se nazývá **zprostředkovatelů**.

V Iosu a Androidu poskytují zprostředkovatele aplikace ke stažení, že zákazníci nainstalovat nezávisle na sobě, nebo přidat do zařízení ve společnosti, který spravuje některé nebo všechny, zařízení pro své zaměstnance. Správa zabezpečení můžou být zprostředkovatelé podporují jenom pro některé aplikace nebo celé zařízení v závislosti na konfiguraci správce IT. Ve Windows tato funkce poskytuje výběru účtu vestavěné do operačního systému, technicky označuje jako zprostředkovatel webového ověření.

#### <a name="broker-assisted-login"></a>Přihlášení zprostředkovatele s asistencí

S pomocí zprostředkovatele přihlášení jsou přihlašovací prostředí, ke kterým dojde v aplikaci zprostředkovatele, které používají úložiště a zabezpečení zprostředkovatele sdílet přihlašovací údaje na všechny aplikace na zařízení, které se vztahují platforma identit. Nepřímo se vaše aplikace bude záviset na zprostředkovatele přihlášení uživatele. V Iosu a Androidu tyto poskytují zprostředkovatele aplikace ke stažení, že zákazníci nainstalovat nezávisle na sobě, nebo můžete přidat do zařízení ve společnosti, který spravuje zařízení pro svoje uživatele. Příkladem takové aplikace je aplikace Microsoft Authenticator v Iosu. Ve Windows tato funkce poskytuje výběru účtu vestavěné do operačního systému, technicky označuje jako zprostředkovatel webového ověření.
Prostředí se liší podle platformy a může být někdy rušivá pro uživatele, pokud nebude správně spravovat. Jste pravděpodobně nejvíce obeznámeni s tímto modelem stačí Pokud máte nainstalovanou aplikací služby Facebook a použijete připojení k Facebooku z jiné aplikace. Platforma identit používá stejný vzor.

V Androidu výběr účtu se zobrazí nad vaší aplikace, což je méně rušivá pro uživatele.

#### <a name="how-the-broker-gets-invoked"></a>Jak se zprostředkovatel volán

Pokud je kompatibilní zprostředkovatel nainstalovaný na zařízení, jako je aplikace Microsoft Authenticator identita sady SDK automaticky provede práci při volání zprostředkovatele pro vás, když uživatel označuje, že se chcete přihlásit pomocí libovolného účtu z platforma identit.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Jak Microsoft zajišťuje aplikace je platný

Je potřeba zajistit zprostředkovatele identity aplikace volání je zásadní zabezpečení poskytované v přihlášení zprostředkovatele s asistencí. zařízení s iOS a Android Nevynucovat jedinečné identifikátory, které jsou platné pouze pro danou aplikaci, aby škodlivé aplikace může "zfalšovat" identifikátor oprávněné aplikace a přijímat tokeny určená pro oprávněné aplikace. K zajištění, že Microsoft vždy komunikuje s správné aplikace za běhu, je vývojář vyzváni k zadání vlastní redirectURI, při registraci svých aplikací s Microsoftem. **Jak by měly vývojářů si vytvořte tento identifikátor URI pro přesměrování je podrobněji popsána níže.** Tato vlastní identifikátor URI pro přesměrování obsahuje kryptografický otisk certifikátu aplikace a je zajištěno bude jedinečný pro aplikace Google Play Store. Když aplikace volá zprostředkovatele, žádá zprostředkovatele operačního systému Android poskytnout kryptografický otisk certifikátu, který volá zprostředkovatele. Zprostředkovatel poskytuje kryptografický otisk tohoto certifikátu společnosti Microsoft při volání funkce systému identit. Pokud kryptografický otisk certifikátu aplikace neodpovídá kryptografický otisk certifikátu, které nám poskytnete vývojářem během registrace, přístup byl odepřen k tokenům tlačítek pro prostředek, který požaduje aplikace. Tato kontrola se zajistí, že pouze registrovaný vývojář aplikace obdrží tokeny.

Zprostředkované jednotné přihlašování přihlášení mají následující výhody:

* Uživatelské prostředí jednotného přihlašování ve všech svých aplikacích bez ohledu na dodavatele.
* Aplikace můžete použít obchodní funkce, jako je například podmíněný přístup a podporu Intune scénářů.
* Vaše aplikace může podporovat ověřování prostřednictvím certifikátu pro obchodní uživatele.
* Bezpečnější přihlašování s identitou aplikace a uživatele byly ověřeny pomocí zprostředkovatele aplikace s využitím algoritmů zvýšení zabezpečení a šifrování.

Je zde reprezentace způsob práce se sadami SDK s zprostředkovatele aplikace, které chcete povolit jednotné přihlašování:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+

```

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Zapnutí jednotného přihlašování pro zprostředkovatele s asistencí jednotného přihlašování

Ve výchozím nastavení vypnuta možnost pro aplikace pro použití jakékoli zprostředkovatele, který je nainstalovaný na zařízení. Chcete-li použít aplikace pomocí zprostředkovatele, musíte provést další konfiguraci a přidat nějaký kód do vaší aplikace.

Jak postupovat, jsou:

1. Povolit režim zprostředkovatele v kódu aplikace volání sady SDK MS
2. Vytvořit nový identifikátor URI přesměrování a stanoví, že aplikace a registrace vaší aplikace
3. Nastavení správná oprávnění v manifestu Androidu

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1: Povolení režimu zprostředkovatele v aplikaci

Možnost pro aplikace pomocí zprostředkovatele zapnutý, při vytváření "nastavení" nebo počáteční nastavení vaše instance ověřování. K tomu ve vaší aplikaci:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 2: Vytvoření nové přesměrování identifikátor URI se schématem vaší adresy URL

Aby bylo možné zajistit správné aplikace recevies vrácený přihlašovací údaje, které tokeny, že je třeba Ujistěte se, že volání zpět do aplikace tak, aby operační systém Android můžete ověřit. Operační systém Android používá hodnotu hash certifikátu v obchodě Google Play. Tato hodnota hash certifikátu nemůže být falešné neautorizovaný aplikací. Spolu se identifikátor URI aplikace zprostředkovatele Microsoft zajišťuje, že tokeny jsou vráceny do správné aplikace. Přesměrování jedinečný identifikátor URI je potřeba možné zaregistrovat v aplikaci.

Váš identifikátor URI pro přesměrování musí být ve správné formu:

`msauth://packagename/Base64UrlencodedSignature`

Příklad: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Tento identifikátor URI pro přesměrování můžete zaregistrovat pomocí registrace aplikace [webu Azure portal](https://portal.azure.com/). Další informace o registraci aplikace Azure AD najdete v tématu [integraci se službou Azure Active Directory](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Krok 3: Nastavení správná oprávnění ve vaší aplikaci

Aplikace zprostředkovatele v Androidu používá funkci Správce účtů operačního systému Android ke správě přihlašovacích údajů napříč aplikacemi. Chcete-li použít zprostředkovatele v Androidu manifest aplikace musí mít oprávnění k používání účtů ke Správci účtů. Tato oprávnění jsou podrobně popsány v [Google dokumentaci pro správce účtu](https://developer.android.com/reference/android/accounts/AccountManager.html)

Konkrétně tato oprávnění jsou:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Dokončení konfigurace jednotného přihlašování!

Nyní identita sady SDK bude automaticky sdílet přihlašovací údaje v rámci vašich aplikací i vyvolat zprostředkovatele, pokud je k dispozici na svém zařízení.

## <a name="next-steps"></a>Další postup

* Další informace o [jednotné přihlašování – protokol SAML](single-sign-on-saml-protocol.md)