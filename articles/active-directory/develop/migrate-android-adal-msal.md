---
title: Příručka k migraci ADAL do MSAL pro Android | Azure
titleSuffix: Microsoft identity platform
description: Naučte se migrovat aplikaci pro Android Azure Active Directory Authentication Library (ADAL) do knihovny Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 10/14/2020
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: bf9b3a154e19fab08c46f9838f555e223f10e8a0
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672283"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Příručka k migraci ADAL do MSAL pro Android

Tento článek popisuje změny, které je třeba provést při migraci aplikace, která používá knihovnu Azure Active Directory Authentication Library (ADAL) k použití knihovny Microsoft Authentication Library (MSAL).

## <a name="difference-highlights"></a>Rozdíly – světla

ADAL funguje s koncovým bodem Azure Active Directory v 1.0. Knihovna Microsoft Authentication Library (MSAL) spolupracuje s platformou Microsoft identity, která se dřív jmenovala jako koncový bod Azure Active Directory v 2.0. Platforma Microsoft identity se liší od Azure Active Directory v 1.0 v tom, že:

Podporovaných
  - Identita organizace (Azure Active Directory)
  - Neorganizační identity, jako jsou Outlook.com, Xbox Live atd.
  - (Jenom Azure AD B2C) Federované přihlášení pomocí Google, Facebook, Twitteru a Amazon

- Jsou kompatibilní se standardy:
  - OAuth v 2.0
  - OpenID Connect (OIDC)

Veřejné rozhraní API MSAL přináší důležité změny, včetně:

- Nový model pro přístup k tokenům:
  - ADAL poskytuje přístup k tokenům prostřednictvím `AuthenticationContext` , který představuje server. MSAL poskytuje přístup k tokenům prostřednictvím `PublicClientApplication` , který představuje klienta. Vývojáři klientů nepotřebují vytvářet novou `PublicClientApplication` instanci pro všechny autority, se kterými potřebují pracovat. `PublicClientApplication`Vyžaduje se jenom jedna konfigurace.
  - Podpora pro vyžádání přístupových tokenů pomocí oborů Kromě identifikátorů prostředků.
  - Podpora pro přírůstkové vyjádření souhlasu. Vývojáři můžou vyžádat obory, protože uživatel přistupuje k více a více funkcím aplikace, včetně těch, které nejsou zahrnuté při registraci aplikace.
  - Po dobu běhu již nejsou ověřeny autority. Místo toho vývojář deklaruje seznam známých autorit během vývoje.
- Změny rozhraní API tokenu:
  - V ADAL `AcquireToken()` nejprve vytvoří tichý požadavek. V takovém případě se to provede interaktivním požadavkem. Výsledkem tohoto chování je, že někteří vývojáři se spoléhají jenom na `AcquireToken` , což by způsobilo, že se uživatel neočekávaně vyzve k zadání přihlašovacích údajů. MSAL vyžaduje, aby vývojáři byli úmyslné, kdy uživatel obdrží výzvu k zadání uživatelského rozhraní.
    - `AcquireTokenSilent` vždy má za následek tichou žádost, která se buď zdaří, nebo selže.
    - `AcquireToken` vždy má za následek požadavek, který vyzývá uživatele prostřednictvím uživatelského rozhraní.
- MSAL podporuje přihlášení buď z výchozího prohlížeče, nebo z vloženého webového zobrazení:
  - Ve výchozím nastavení se použije výchozí prohlížeč v zařízení. Díky tomu může MSAL používat stav ověřování (soubory cookie), které už mohou být k dispozici pro jeden nebo více přihlášených účtů. Pokud není k dispozici žádný stav ověřování, ověřování během autorizace prostřednictvím MSAL má za následek vytvoření stavu ověřování (cookies) pro výhody dalších webových aplikací, které budou použity ve stejném prohlížeči.
- Nový model výjimky:
  - Výjimky jasně definují typ chyby, ke které došlo, a to, co vývojář potřebuje k vyřešení.
- MSAL podporuje objekty parametrů pro `AcquireToken` `AcquireTokenSilent` volání a.
- MSAL podporuje deklarativní konfiguraci pro:
  - ID klienta, identifikátor URI přesměrování.
  - Vložený a výchozí prohlížeč
  - Autority
  - Nastavení HTTP, jako je například čtení a časový limit připojení

## <a name="your-app-registration-and-migration-to-msal"></a>Registrace a migrace vaší aplikace do MSAL

Pro použití MSAL nemusíte měnit stávající registraci aplikace. Pokud chcete využít výhod přírůstkového a postupného souhlasu, možná budete muset zkontrolovat registraci a identifikovat konkrétní obory, které chcete vyžádat přírůstkově. Další informace o oborech a postupu pro přírůstkové vyjádření.

V registraci vaší aplikace na portálu se zobrazí karta **oprávnění rozhraní API** . Tato část poskytuje seznam rozhraní API a oprávnění (oborů), pro které je vaše aplikace momentálně nakonfigurovaná na požadování přístupu. Zobrazuje také seznam názvů oborů přidružených ke každému oprávnění rozhraní API.

### <a name="user-consent"></a>Souhlas uživatele

Pomocí ADAL a koncového bodu Azure AD v1 bylo při prvním použití udělené uživatelské vyjádření souhlasu uživatele s prostředky, které vlastní. S MSAL a platformou Microsoft identity se dá souhlas vyžádat přírůstkově. Přírůstkové vyjádření souhlasu je užitečné pro oprávnění, která může uživatel zvážit při vysokém oprávnění, nebo může jinak klást otázky, pokud není k dispozici jasné vysvětlení, proč se oprávnění vyžaduje. V ADAL mohla být tato oprávnění způsobena tím, že uživatel přejímá přihlášení do vaší aplikace.

> [!TIP]
> Doporučujeme použití přírůstkového souhlasu ve scénářích, kdy potřebujete poskytnout uživateli další kontext o tom, proč vaše aplikace potřebuje oprávnění.

### <a name="admin-consent"></a>Souhlas správce

Správci organizace můžou udělit souhlas s oprávněními, které vaše aplikace vyžaduje, jménem všech členů jejich organizace. Některé organizace umožňují správcům pouze udělit souhlas s aplikacemi. Souhlas správce vyžaduje, abyste v registraci vaší aplikace zahrnuli všechna oprávnění a rozsahy rozhraní API, které vaše aplikace používá.

> [!TIP]
> I když si můžete vyžádat rozsah pomocí MSAL pro něco, co není součástí registrace vaší aplikace, doporučujeme, abyste aktualizovali registraci aplikace tak, aby zahrnovala všechny prostředky a rozsahy, kterým by mohl uživatel někdy udělit oprávnění.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migrace z ID prostředků do oborů

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Ověřování a vyžádat autorizaci pro všechna oprávnění při prvním použití

Pokud aktuálně používáte ADAL a nepotřebujete používat přírůstkový souhlas, nejjednodušší způsob, jak začít používat MSAL, je vytvořit požadavek s `acquireToken` použitím nového `AcquireTokenParameter` objektu a nastavením hodnoty ID prostředku.

> [!CAUTION]
> Není možné nastavit oba obory a ID prostředku. Pokus o nastavení obou výsledků bude `IllegalArgumentException` .

Výsledkem bude stejné chování V1, které jste použili. Všechna oprávnění požadovaná v registraci vaší aplikace jsou během první interakce požadována od uživatele.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Ověření a vyžádání oprávnění pouze podle potřeby

Pokud chcete využít výhod přírůstkového souhlasu, vytvořte seznam oprávnění (oborů), které vaše aplikace používá k registraci vaší aplikace, a uspořádejte je do dvou seznamů na základě:

- Které obory si chcete během první interakce uživatele s vaší aplikací během přihlašování vyžádat.
- Oprávnění, která jsou přidružená k důležité funkci aplikace, kterou budete muset také vysvětlit uživateli.

Po uspořádání oborů uspořádejte jednotlivé seznamy podle toho, podle kterého prostředku (API) chcete požádat o token. Stejně jako všechny ostatní obory, které chcete uživateli autorizovat.

Objekt Parameters, který slouží k vytvoření požadavku na MSAL, podporuje:

- `Scope`: Seznam oborů, pro které chcete požádat o autorizaci a získat přístupový token.
- `ExtraScopesToConsent`: Další seznam oborů, pro které chcete požádat o autorizaci v případě, že budete požadovat přístupový token pro jiný prostředek. Tento seznam oborů vám umožní minimalizovat počet pokusů o autorizaci uživatelů. To znamená méně výzvy k autorizaci nebo souhlasu uživatele.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Migrace z AuthenticationContext na PublicClientApplications

### <a name="constructing-publicclientapplication"></a>Sestavování PublicClientApplication

Při použití MSAL se vytváří instance `PublicClientApplication` . Tento objekt modeluje identitu vaší aplikace a slouží k provádění požadavků jednomu nebo více autoritám. Pomocí tohoto objektu nakonfigurujete identitu klienta, identifikátor URI pro přesměrování, výchozí autoritu, jestli se má používat prohlížeč zařízení vs. vložené webové zobrazení, úroveň protokolování a další.

Můžete deklarativně nakonfigurovat tento objekt pomocí formátu JSON, který můžete buď zadat jako soubor, nebo Uložit jako prostředek v rámci APK.

I když tento objekt není typu Singleton, interně používá sdílené `Executors` pro interaktivní i tiché požadavky.

### <a name="business-to-business"></a>Firmy do firmy

V ADAL každá organizace, které požadujete přístupové tokeny, vyžaduje samostatnou instanci `AuthenticationContext` . V MSAL už to není potřeba. Můžete zadat autoritu, ze které chcete požádat o token v rámci vaší tiché nebo interaktivní žádosti.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Migrace ověření autority na známé úřady

MSAL nemá příznak pro povolení nebo zakázání ověřování autority. Ověřování autority je funkce v ADAL a v dřívějších verzích MSAL, která brání vašemu kódu v žádosti o tokeny od potenciálně škodlivé autority. MSAL nyní načte seznam autorit známých společnosti Microsoft a sloučí tento seznam s autoritami, které jste zadali v konfiguraci.

> [!TIP]
> Pokud jste uživatelem Azure Business to Consumer (B2C), znamená to, že už nemusíte ověřování autority vypínat. Místo toho Zahrňte všechny podporované zásady Azure AD B2C jako autority v konfiguraci MSAL.

Pokud se pokusíte použít autoritu, která není známá na Microsoftu, a není zahrnutá do vaší konfigurace, zobrazí se `UnknownAuthorityException` .

### <a name="logging"></a>Protokolování
Nyní můžete v rámci konfigurace deklarativně nakonfigurovat protokolování, například takto:

```json
"logging": {
  "pii_enabled": false,
  "log_level": "WARNING",
  "logcat_enabled": true
}
```

## <a name="migrate-from-userinfo-to-account"></a>Migrace z UserInfo na účet

V ADAL `AuthenticationResult` poskytuje objekt, který `UserInfo` slouží k načtení informací o ověřeném účtu. Pojem "uživatel", který byl určen jako lidský nebo softwarový agent, byl použit způsobem, který bylo obtížné oznámit, že některé aplikace podporují jediného uživatele (ať už jde o lidského nebo softwarový agent) s více účty.

Uvažujte o bankovním účtu. Můžete mít více než jeden účet ve více než jedné finanční instituci. Po otevření účtu jste vy (uživatel) vystavili přihlašovací údaje, jako je například karta ATM & kód PIN, která se používá pro přístup k vašemu zůstatku, fakturaci a tak dále, pro každý účet. Tyto přihlašovací údaje se dají použít jenom na finanční instituci, která je vystavila.

Obdobně, jako jsou účty na finanční instituci, se k účtům na platformě Microsoft Identity přistupovaly pomocí přihlašovacích údajů. Tyto přihlašovací údaje jsou buď registrované v, nebo vydané společností Microsoft. Nebo Microsoft jménem organizace.

V takovém případě se platforma Microsoft Identity liší od finanční instituce. v této analogii je to, že platforma Microsoft Identity poskytuje rozhraní, které umožňuje uživateli používat jeden účet a jeho přidružené přihlašovací údaje pro přístup k prostředkům, které patří více jednotlivcům a organizacím. To znamená, že je možné využít kartu vydanou jednou bankou, ale ještě jinou finanční instituci. To funguje, protože všechny příslušné organizace používají platformu Microsoft identity, která umožňuje použití jednoho účtu v různých organizacích. Tady je příklad:

Sam funguje pro Contoso.com, ale spravuje virtuální počítače Azure, které patří do Fabrikam.com. Aby mohl správce Sam spravovat virtuální počítače společnosti Fabrikam, musí mít oprávnění pro přístup k nim. Tento přístup je možné udělit přidáním účtu SAM do Fabrikam.com a přidělením jeho účtu roli, která mu umožní pracovat s virtuálními počítači. To se provádí s Azure Portal.

Když se účet Contoso.com účtu SAM přidá jako člen služby Fabrikam.com, vytvoří se nový záznam v Azure Active Directory Fabrikam. com pro Sam. Záznam Sam v Azure Active Directory je známý jako objekt uživatele. V takovém případě by objekt uživatele odkazoval zpátky na objekt uživatele Sam v Contoso.com. Objekt uživatele Fabrikam Sam je místní reprezentace Sam a slouží k ukládání informací o účtu přidruženém k Sam v kontextu Fabrikam.com. V Contoso.com je název SAM hlavní DevOps konzultant. Ve společnosti Fabrikam je název SAM Contractor-Virtual počítačů. V Contoso.com není pro správu virtuálních počítačů správce Sam zodpovědný ani autorizovaný. V Fabrikam.com se jedná o jeho jedinou pracovní funkci. I když Sam stále obsahuje jenom jednu sadu přihlašovacích údajů, která bude sledovat, které přihlašovací údaje vystavil Contoso.com.

Po úspěšném `acquireToken` volání se zobrazí odkaz na `IAccount` objekt, který lze použít v pozdějších `acquireTokenSilent` požadavcích.

### <a name="imultitenantaccount"></a>IMultiTenantAccount

Pokud máte aplikaci, která přistupuje k deklaracím účtu ze všech tenantů, ve kterých je účet zastoupený, můžete `IAccount` objekty přetypovat na `IMultiTenantAccount` . Toto rozhraní poskytuje mapu `ITenantProfiles` podle ID tenanta, která umožňuje přístup k deklaracím, které patří k účtu v každém z klientů, od kterých jste si vyžádali token, vzhledem k aktuálnímu účtu.

Deklarace identity v kořenovém adresáři `IAccount` a `IMultiTenantAccount` vždy obsahují deklarace z domovského tenanta. Pokud jste ještě nevytvořili žádost o token v rámci domovského tenanta, tato kolekce bude prázdná.

## <a name="other-changes"></a>Další změny

### <a name="use-the-new-authenticationcallback"></a>Použití nového AuthenticationCallback

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}
```

## <a name="migrate-to-the-new-exceptions"></a>Migrovat na nové výjimky

V ADAL existuje jeden typ výjimky, `AuthenticationException` která obsahuje metodu pro načtení `ADALError` hodnoty výčtu.
V MSAL existuje hierarchie výjimek a každá má vlastní sadu souvisejících specifických kódů chyb.

| Výjimka                                        | Popis                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| `MsalArgumentException`                          | Vyvoláno, pokud je jeden nebo více argumentů vstupů neplatných.                 |
| `MsalClientException`                            | Vyvolána, pokud se jedná o chybu na straně klienta.                                 |
| `MsalDeclinedScopeException`                     | Vyvoláno, pokud server odmítl jeden nebo více požadovaných oborů. |
| `MsalException`                                  | Výchozí vyzkoušená výjimka vyvolaná nástrojem MSAL.                           |
| `MsalIntuneAppProtectionPolicyRequiredException` | Vyvoláno, pokud má prostředek zapnutou zásadu ochrany MAMCA.         |
| `MsalServiceException`                           | Vyvolána, pokud je chyba na straně serveru.                                 |
| `MsalUiRequiredException`                        | Vyvoláno, pokud token nelze aktualizovat tiše.                    |
| `MsalUserCancelException`                        | Vyvolána, pokud uživatel zrušil tok ověřování.                |

### <a name="adalerror-to-msalexception-translation"></a>ADALError do překladu MsalException

| Pokud jsou tyto chyby zachyceny v ADAL...  | ... zachytit tyto výjimky MSAL:                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| *Žádný ekvivalentní ADALError* | `MsalArgumentException`                          |
| <ul><li>`ADALError.ANDROIDKEYSTORE_FAILED`<li>`ADALError.AUTH_FAILED_USER_MISMATCH`<li>`ADALError.DECRYPTION_FAILED`<li>`ADALError.DEVELOPER_AUTHORITY_CAN_NOT_BE_VALIDED`<li>`ADALError.EVELOPER_AUTHORITY_IS_NOT_VALID_INSTANCE`<li>`ADALError.DEVELOPER_AUTHORITY_IS_NOT_VALID_URL`<li>`ADALError.DEVICE_CONNECTION_IS_NOT_AVAILABLE`<li>`ADALError.DEVICE_NO_SUCH_ALGORITHM`<li>`ADALError.ENCODING_IS_NOT_SUPPORTED`<li>`ADALError.ENCRYPTION_ERROR`<li>`ADALError.IO_EXCEPTION`<li>`ADALError.JSON_PARSE_ERROR`<li>`ADALError.NO_NETWORK_CONNECTION_POWER_OPTIMIZATION`<li>`ADALError.SOCKET_TIMEOUT_EXCEPTION`</ul> | `MsalClientException`                            |
| *Žádný ekvivalentní ADALError* | `MsalDeclinedScopeException`                     |
| <ul><li>`ADALError.APP_PACKAGE_NAME_NOT_FOUND`<li>`ADALError.BROKER_APP_VERIFICATION_FAILED`<li>`ADALError.PACKAGE_NAME_NOT_FOUND`</ul> | `MsalException`                                  |
| *Žádný ekvivalentní ADALError* | `MsalIntuneAppProtectionPolicyRequiredException` |
| <ul><li>`ADALError.SERVER_ERROR`<li>`ADALError.SERVER_INVALID_REQUEST`</ul> | `MsalServiceException`                           |
| <ul><li>`ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED` | `MsalUiRequiredException`</ul>                        |
| *Žádný ekvivalentní ADALError* | `MsalUserCancelException`                        |

### <a name="adal-logging-to-msal-logging"></a>Protokolování ADAL k protokolování MSAL

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
      @Override
      public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
          logs.append(message).append('\n');
      }
  });

// New Log Levels:
public enum LogLevel
{
    /**
     * Error level logging.
     */
    ERROR,
    /**
     * Warning level logging.
     */
    WARNING,
    /**
     * Info level logging.
     */
    INFO,
    /**
     * Verbose level logging.
     */
    VERBOSE
}
```
