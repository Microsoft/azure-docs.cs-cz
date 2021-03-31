---
title: Postup povolení jednotného přihlašování pro různé aplikace v Androidu pomocí knihovny ADAL | Microsoft Docs
description: Použití funkcí sady ADAL SDK pro povolení jednotného přihlašování napříč aplikacemi.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev, devx-track-java
ROBOTS: NOINDEX
ms.openlocfilehash: 9d1faf7aed5cf4c4975925c7a56d169dc99a7bd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87313587"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Postupy: povolení jednotného přihlašování mezi aplikacemi v Androidu pomocí ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Jednotné přihlašování (SSO) umožňuje uživatelům zadat přihlašovací údaje jenom jednou a nechat tyto přihlašovací údaje automaticky fungovat napříč aplikacemi a na různých platformách, které můžou používat jiné aplikace (třeba účty Microsoft nebo pracovní účet z Microsoft 365) bez ohledu na vydavatele.

Platforma pro identitu od Microsoftu, společně se sadami SDK, usnadňuje povolení jednotného přihlašování v rámci vlastní sady aplikací nebo funkcí zprostředkovatele a ověřovacích aplikací v celém zařízení.

V tomto postupu se naučíte, jak v rámci aplikace nakonfigurovat sadu SDK, abyste svým zákazníkům mohli poskytovat jednotné přihlašování.

## <a name="prerequisites"></a>Požadavky

Tento postup předpokládá, že víte, jak:

- Zřiďte aplikaci pomocí starší verze portálu pro Azure Active Directory (Azure AD). Další informace najdete v tématu [Registrace aplikace](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) .
- Integrujte svou aplikaci s [Android SDK Azure AD](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Koncepce jednotného přihlašování

### <a name="identity-brokers"></a>Zprostředkovatelé identity

Společnost Microsoft poskytuje aplikace pro každou mobilní platformu, která umožňuje přemostění přihlašovacích údajů napříč aplikacemi od různých dodavatelů a pro rozšířené funkce, které vyžadují jediné zabezpečené místo, ze kterého se ověřují přihlašovací údaje. Nazývají se **Zprostředkovatelé**.

V systémech iOS a Android jsou zprostředkovatelé poskytovány prostřednictvím aplikací ke stažení, které zákazníci nainstalují nezávisle nebo na zařízení prostřednictvím společnosti, která spravuje některé nebo všechna zařízení pro své zaměstnance. Zprostředkovatelé podporují správu zabezpečení pouze pro některé aplikace nebo celé zařízení v závislosti na konfiguraci správce IT. V systému Windows je tato funkce poskytována výběrem možnosti účtu, který je součástí operačního systému, který je technicky známý jako zprostředkovatel webového ověřování.

#### <a name="broker-assisted-login"></a>Zprostředkovatel s asistencí – přihlášení

Zprostředkovatelé přihlašování s asistencí jsou přihlašovací prostředí, ke kterým dochází v rámci aplikace zprostředkovatele, a používají úložiště a zabezpečení služby Broker ke sdílení přihlašovacích údajů napříč všemi aplikacemi v zařízení, které používají platformu identity. Výsledkem je, že vaše aplikace budou spoléhat na to, že zprostředkovatel bude podepisovat uživatele v. V systémech iOS a Android jsou tito zprostředkovatelé k dispozici prostřednictvím aplikací ke stažení, které si zákazníci nainstalují samostatně, nebo je můžou do zařízení poslat společnost, která zařízení spravuje pro uživatele. Příkladem tohoto typu aplikace je Microsoft Authenticator aplikace v iOS. V systému Windows je tato funkce poskytována výběrem možnosti účtu, který je součástí operačního systému, který je technicky známý jako zprostředkovatel webového ověřování.
Prostředí se liší podle platformy a v případě, že se nespravuje správně, může být v některých případech rušivé pro uživatele. Tento model je pravděpodobně nejčastěji známý, pokud máte nainstalovanou aplikaci Facebook a používáte Facebook Connect z jiné aplikace. Platforma identity používá stejný vzor.

V Androidu se výběr účtu zobrazuje na aplikaci, která je pro uživatele méně nerušená.

#### <a name="how-the-broker-gets-invoked"></a>Způsob vyvolání zprostředkovatele

Pokud je na zařízení nainstalovaný kompatibilní zprostředkovatel, jako je Microsoft Authenticator aplikace, sady SDK identity automaticky provede práci vyvoláním zprostředkovatele za vás, když uživatel určí, že se chce přihlásit pomocí libovolného účtu z platformy identity.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Jak Microsoft zajišťuje platnost aplikace

Je nutné zajistit, aby identita aplikace volající zprostředkovateli byla zásadní pro zabezpečení poskytované v přihlašování s asistencí pro zprostředkovatele. iOS a Android vynutil jedinečné identifikátory, které jsou platné jenom pro danou aplikaci, takže škodlivé aplikace můžou mít falešný identifikátor aplikace a získají tokeny určené pro legitimní aplikaci. Pro zajištění, že společnost Microsoft vždy komunikuje se správnou aplikací za běhu, je vývojář požádán o poskytnutí vlastní redirectURI při registraci své aplikace v Microsoftu. **Jak by měli vývojáři vytvořit tento identifikátor URI přesměrování, je podrobněji popsán níže.** Tento vlastní redirectURI obsahuje kryptografický otisk certifikátu aplikace a je zajištěný jedinečným pro aplikaci Obchod Google Play. Když aplikace volá zprostředkovatele, vyžádá si operační systém Android, aby mu poskytl kryptografický otisk certifikátu, který se volal jako zprostředkovatel. Zprostředkovatel poskytuje tomuto kryptografickému otisku certifikátu Microsoftu volání do systému identit. Pokud kryptografický otisk certifikátu aplikace neodpovídá kryptografickému otisku certifikátu, který nám poskytl vývojář během registrace, přístup k tokenům pro prostředek, který aplikace požaduje, je odepřený. Tato kontrolu zajistí, že tokeny obdrží jenom aplikace zaregistrovaná vývojářem.

Zprostředkovaná přihlášení SSO mají následující výhody:

* Jednotné přihlašování uživatelů napříč všemi svými aplikacemi bez ohledu na dodavatele.
* Vaše aplikace může používat pokročilejší obchodní funkce, jako je podmíněný přístup a podpora scénářů Intune.
* Vaše aplikace může podporovat ověřování na základě certifikátů pro obchodní uživatele.
* Bezpečnější přihlašovací prostředí jako identita aplikace a uživatel ověřuje aplikace zprostředkovatele s dalšími algoritmy zabezpečení a šifrováním.

Tady je reprezentace způsobu, jakým sady SDK pracují s aplikacemi zprostředkovatele pro povolení jednotného přihlašování:

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

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Zapnutí jednotného přihlašování pro zprostředkovatele s asistencí pro jednotné přihlašování

Možnost použití libovolného zprostředkovatele nainstalovaného v zařízení je ve výchozím nastavení vypnutá. Chcete-li použít aplikaci se zprostředkovatelem, je nutné provést další konfiguraci a přidat do aplikace nějaký kód.

Postup je následující:

1. Povolit režim zprostředkovatele v volání sady MS SDK do kódu aplikace
2. Vytvořte nový identifikátor URI pro přesměrování a poskytněte aplikaci i registraci vaší aplikace.
3. Nastavení správných oprávnění v manifestu Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1: povolení režimu zprostředkovatele ve vaší aplikaci

Schopnost vaší aplikace používat zprostředkovatele je zapnutá, když vytvoříte nastavení nebo počáteční nastavení instance ověřování. Postup v aplikaci:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 2: vytvoření nového identifikátoru URI pro přesměrování pomocí schématu URL

Aby bylo zajištěno, že správná aplikace obdrží vrácené tokeny přihlašovacích údajů, je nutné zajistit, aby se volání vrátilo zpět do vaší aplikace způsobem, který může operační systém Android ověřit. Operační systém Android používá hodnotu hash certifikátu v úložišti Google Play. Tato hodnota hash certifikátu nemůže být falešná neoprávněnou aplikací. Společně s identifikátorem URI aplikace zprostředkovatele zajišťuje společnost Microsoft, aby se tokeny vrátily do správné aplikace. Pro registraci v aplikaci je vyžadován jedinečný identifikátor URI pro přesměrování.

Identifikátor URI pro přesměrování musí být ve správném formátu:

`msauth://packagename/Base64UrlencodedSignature`

např.: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Tento identifikátor URI přesměrování můžete zaregistrovat v registraci aplikace pomocí [Azure Portal](https://portal.azure.com/). Další informace o registraci aplikace služby Azure AD najdete v tématu [integrace s Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Krok 3: nastavení správných oprávnění ve vaší aplikaci

Aplikace zprostředkovatele v Androidu používá pro správu přihlašovacích údajů napříč aplikacemi funkci správce účtů v operačním systému Android. Aby bylo možné používat zprostředkovatele v Androidu, musí mít váš manifest aplikace oprávnění k používání účtů ke správci účtů. Tato oprávnění jsou podrobněji popsaná v [dokumentaci Google pro správce účtu](https://developer.android.com/reference/android/accounts/AccountManager.html) .

Konkrétně jsou to tato oprávnění:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Nakonfigurovali jste jednotné přihlašování.

Sada identity SDK teď automaticky sdílí přihlašovací údaje napříč vašimi aplikacemi a vyvolá zprostředkovatele, pokud se nachází na svém zařízení.

## <a name="next-steps"></a>Další kroky

* Další informace o [protokolu SAML jednotného přihlašování](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
