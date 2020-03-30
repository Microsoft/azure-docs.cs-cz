---
title: Jak povolit cross-app sso v systému Android pomocí ADAL | Dokumenty společnosti Microsoft
description: Jak používat funkce sady ADAL SDK k povolení jednotného přihlašování ve všech aplikacích.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 0b87a9cd0ae29281faad4209f4449d547921835d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154810"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Postup: Povolení přijíní k aplikacím pro více aplikací v systému Android pomocí ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Jednotné přihlašování (SSO) umožňuje uživatelům zadat jejich přihlašovací údaje pouze jednou a mají tyto přihlašovací údaje automaticky pracovat napříč aplikacemi a napříč platformami, které mohou používat jiné aplikace (například účty Microsoft nebo pracovní účet od Microsoft 365) ne záležitost vydavatele.

Platforma identit společnosti Microsoft spolu s sadami SDK usnadňuje povolení vlastního přihlašování v rámci vlastní sady aplikací nebo s možností zprostředkovatele a aplikacemi Authenticator v celém zařízení.

V tomto návodu se dozvíte, jak nakonfigurovat sdk v rámci vaší aplikace poskytovat přihlašující služby pro zákazníky.

## <a name="prerequisites"></a>Požadavky

Tento návod předpokládá, že víte, jak:

- Zřídit aplikaci pomocí staršího portálu pro Azure Active Directory (Azure AD). Další informace najdete [v tématu Registrace aplikace](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
- Integrujte svou aplikaci s [sadou Azure AD Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Koncepce jednotného přihlášení

### <a name="identity-brokers"></a>Zprostředkovatelé identit

Společnost Microsoft poskytuje aplikace pro každou mobilní platformu, které umožňují přemostění pověření mezi aplikacemi od různých dodavatelů a pro rozšířené funkce, které vyžadují jediné zabezpečené místo, odkud chcete ověřit pověření. Tyto se nazývají **makléři**.

V systémech iOS a Android jsou zprostředkovatelé poskytováni prostřednictvím aplikací ke stažení, které zákazníci nainstalují nezávisle nebo zasouvají do zařízení společností, která spravuje některá nebo všechna zařízení pro své zaměstnance. Makléři podporují správu zabezpečení jen pro některé aplikace nebo celé zařízení založené na konfiguraci správce IT. V systému Windows je tato funkce poskytována výběrem účtu integrovaným do operačního systému, který je technicky známý jako Zprostředkovatel webového ověřování.

#### <a name="broker-assisted-login"></a>Broker asistované přihlášení

Broker asistované přihlášení jsou přihlašovací prostředí, ke kterým dochází v rámci aplikace broker a pomocí úložiště a zabezpečení zprostředkovatele sdílet přihlašovací údaje napříč všemi aplikacemi na zařízení, které používají platformu identity. Důsledkem je vaše aplikace budou spoléhat na zprostředkovatele přihlásit uživatele. V systémech iOS a Android jsou tyto zprostředkovatelé poskytovány prostřednictvím aplikací ke stažení, které zákazníci instalují nezávisle nebo mohou být do zařízení zatlačeni společností, která spravuje zařízení pro svého uživatele. Příkladem tohoto typu aplikace je aplikace Microsoft Authenticator v systému iOS. V systému Windows je tato funkce poskytována výběrem účtu integrovaným do operačního systému, který je technicky známý jako Zprostředkovatel webového ověřování.
Prostředí se liší podle platformy a může být někdy rušivé pro uživatele, pokud není správně spravováno. Pravděpodobně jste nejvíce obeznámeni s tímto vzorem, pokud máte nainstalovanou aplikaci Facebook a používáte Facebook Connect z jiné aplikace. Platforma identity používá stejný vzor.

V systému Android se v horní části aplikace zobrazí výběr účtu, což je pro uživatele méně rušivé.

#### <a name="how-the-broker-gets-invoked"></a>Jak se zprostředkovatel dostane vyvolána

Pokud je v zařízení nainstalován kompatibilní zprostředkovatel, jako je aplikace Microsoft Authenticator, sady SDK identity automaticky odvedou práci s vyvoláním zprostředkovatele za vás, když uživatel označí, že se chce přihlásit pomocí libovolného účtu z platformy identity.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Jak společnost Microsoft zajišťuje platnost aplikace

Potřeba zajistit identitu aplikace volající makléře je zásadní pro zabezpečení poskytované v makléři asistované přihlášení. iOS a Android nevynucují jedinečné identifikátory, které jsou platné pouze pro danou aplikaci, takže škodlivé aplikace mohou "falšovat" identifikátor legitimní aplikace a přijímat tokeny určené pro legitimní aplikaci. Chcete-li zajistit, aby společnost Microsoft vždy komunikovala se správnou aplikací za běhu, je vývojář při registraci své aplikace u společnosti Microsoft požádán o poskytnutí vlastního rozhraní redirectURI. **Jak by vývojáři měli vytvořit tento identifikátor URI přesměrování, je podrobně popsán níže.** Tento vlastní redirectURI obsahuje kryptografický otisk certifikátu aplikace a je zajištěno, že je jedinečný pro aplikaci v Obchodě Google Play. Když aplikace volá zprostředkovatele, zprostředkovatel požádá operační systém Android, aby mu poskytl kryptografický otisk certifikátu, který volal zprostředkovatele. Zprostředkovatel poskytuje tento kryptografický otisk certifikátu společnosti Microsoft při volání systému identit. Pokud kryptografický otisk certifikátu aplikace neodpovídá kryptografickému otisku certifikátu, který nám poskytl vývojář během registrace, je přístup odepřen tokenům pro prostředek, který aplikace požaduje. Tato kontrola zajišťuje, že pouze aplikace registrovaná vývojářobdrží tokeny.

Zprostředkované přihlášení s přihlašovaným pomocí služby ASO mají následující výhody:

* Uživatelské prostředí s vlastním dodavatelem ve všech svých aplikacích bez ohledu na dodavatele.
* Vaše aplikace může používat pokročilejší obchodní funkce, jako je podmíněný přístup a podpora intune scénářů.
* Vaše aplikace může podporovat ověřování na základě certifikátu pro podnikové uživatele.
* Bezpečnější prostředí pro přihlášení jako identitu aplikace a uživatele ověřuje aplikace brokera s dalšími algoritmy zabezpečení a šifrováním.

Zde je znázornění, jak sady SDK pracovat s makléřské aplikace povolit přihlašování k zapisování:

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

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Zapnutí spřiby pro zprostředkovatele asistované spřibyt

Možnost aplikace používat libovolného zprostředkovatele, který je nainstalován v zařízení, je ve výchozím nastavení vypnuta. Chcete-li použít aplikaci s brokerem, musíte provést nějakou další konfiguraci a přidat do aplikace nějaký kód.

Postupujte takto:

1. Povolit režim brokeru při volání kódu aplikace do sady MS SDK
2. Vytvoření nového identifikátoru URI přesměrování a zajištění, že pro aplikaci i registraci aplikace
3. Nastavení správných oprávnění v manifestu Androidu

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1: Povolit režim broker ve vaší aplikaci

Možnost aplikace používat zprostředkovatele je zapnuta při vytváření "nastavení" nebo počáteční nastavení instance ověřování. Jak to udělat v aplikaci:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 2: Vytvoření nového identifikátoru URI přesměrování pomocí schématu adres URL

Aby bylo zajištěno, že správná aplikace obdrží vrácené tokeny pověření, je třeba se ujistit, že volání zpět do vaší aplikace způsobem, který operační systém Android můžete ověřit. Operační systém Android používá hash certifikátu v obchodě Google Play. Tento hash certifikátu nemůže být zfalšován neautorizovaný aplikace. Spolu s URI aplikace zprostředkovatele, Microsoft zajišťuje, že tokeny jsou vráceny do správné aplikace. Jedinečný identifikátor URI přesměrování je nutné zaregistrovat v aplikaci.

Identifikátor URI přesměrování musí být ve správné podobě:

`msauth://packagename/Base64UrlencodedSignature`

ex: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Tento identifikátor URI přesměrování můžete zaregistrovat v registraci aplikace pomocí [portálu Azure](https://portal.azure.com/). Další informace o registraci aplikací Azure AD najdete [v tématu Integrace s Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Krok 3: Nastavení správných oprávnění v aplikaci

Broker aplikace v systému Android používá account manager funkce operačního systému Android pro správu přihlašovacích údajů napříč aplikacemi. Aby bylo možné používat makléře v Systému Android, musí mít manifest aplikace oprávnění k používání účtů AccountManager. Tato oprávnění jsou podrobně popsána v [dokumentaci Google pro Správce účtů zde](https://developer.android.com/reference/android/accounts/AccountManager.html)

Tato oprávnění jsou zejména:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Nakonfigurovali jste spřijit sazí!

Nyní identity SDK automaticky obě sdílet přihlašovací údaje ve vašich aplikacích a vyvolat zprostředkovatele, pokud je k dispozici na jejich zařízení.

## <a name="next-steps"></a>Další kroky

* Další informace o [protokolu SAML jednotného přihlášení](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
