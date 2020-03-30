---
title: ADAL na MSAL migrace průvodce pro Android | Azure
description: Zjistěte, jak migrovat aplikaci Azure Active Directory Authentication Library (ADAL) pro Android do knihovny Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 21866bb7dab3d5a093ffc4655161b80853eadfc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084049"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Průvodce migrací ADAL na MSAL pro Android

Tento článek upozorňuje na změny, které je třeba provést k migraci aplikace, která používá Azure Active Directory Authentication Library (ADAL) k použití Knihovny ověřování Microsoft (MSAL).

## <a name="difference-highlights"></a>Zvýraznění rozdílů

ADAL pracuje s koncovým bodem Azure Active Directory v1.0. Knihovna ověřování Microsoft (MSAL) pracuje s platformou identit Microsoftu – dříve označovanou jako koncový bod Azure Active Directory v2.0. Platforma identit Microsoftu se liší od Azure Active Directory v1.0 v tom, že:

Podporuje:
  - Identita organizace (Azure Active Directory)
  - Neorganizační identity, jako jsou Outlook.com, Xbox Live a tak dále
  - (pouze B2C) Federované přihlášení s Google, Facebook, Twitter a Amazon

- Jsou standardy kompatibilní s:
  - OAuth v2.0
  - OpenID Connect (OIDC)

Veřejné rozhraní API MSAL zavádí důležité změny, včetně:

- Nový model pro přístup k tokenům:
  - ADAL poskytuje přístup k `AuthenticationContext`tokenům prostřednictvím , který představuje server. MSAL poskytuje přístup k `PublicClientApplication`tokeny prostřednictvím , který představuje klienta. Vývojáři klientů nemusí vytvářet `PublicClientApplication` novou instanci pro každý úřad, se kterým potřebují komunikovat. Je `PublicClientApplication` vyžadována pouze jedna konfigurace.
  - Podpora pro vyžádání přístupových tokenů pomocí oborů kromě identifikátorů prostředků.
  - Podpora pro přírůstkový souhlas. Vývojáři mohou požadovat obory, protože uživatel přistupuje k dalším funkcím v aplikaci, včetně těch, které nejsou zahrnuty při registraci aplikace.
  - Úřady se již neověřují za běhu. Místo toho vývojář deklaruje seznam "známých autorit" během vývoje.
- Změny rozhraní API tokenu:
  - V ADAL `AcquireToken()` nejprve provede tichý požadavek. V opačném případě vytvoří interaktivní požadavek. Toto chování mělo za následek, `AcquireToken`že někteří vývojáři spoléhají pouze na , což mělo za následek, že uživatel byl někdy neočekávaně vyzván k zadání pověření. MSAL vyžaduje, aby vývojáři byli záměrní o tom, kdy uživatel obdrží výzvu uživatelského rozhraní.
    - `AcquireTokenSilent`vždy vede k tichému požadavku, který buď uspěje, nebo se nezdaří.
    - `AcquireToken`vždy vede k požadavku, který vyzve uživatele prostřednictvím uživatelského rozhraní.
- MSAL podporuje přihlášení z výchozího prohlížeče nebo z vloženého webového zobrazení:
  - Ve výchozím nastavení se používá výchozí prohlížeč v zařízení. To umožňuje MSAL používat stav ověřování (soubory cookie), které již mohou být k dispozici pro jeden nebo více přihlášených účtů. Pokud není k dispozici žádný stav ověřování, ověřování během autorizace prostřednictvím služby MSAL má za následek vytvoření stavu ověřování (soubory cookie) ve prospěch jiných webových aplikací, které budou použity ve stejném prohlížeči.
- Nový model výjimky:
  - Výjimky jasněji definovat typ chyby, ke které došlo a co vývojář musí udělat, aby ji vyřešit.
- MSAL podporuje parametry a `AcquireToken` `AcquireTokenSilent` volání.
- MSAL podporuje deklarativní konfiguraci pro:
  - ID klienta, identifikátor URI přesměrování.
  - Vložený vs výchozí prohlížeč
  - Orgány
  - Nastavení protokolu HTTP, například časový limit čtení a připojení

## <a name="your-app-registration-and-migration-to-msal"></a>Registrace aplikace a migrace do MSAL

Nemusíte měnit stávající registraci aplikace, abyste měli používat MSAL. Pokud chcete využít přírůstkového/progresivního souhlasu, možná budete muset zkontrolovat registraci, abyste identifikovali konkrétní obory, které chcete požádat postupně. Následuje další informace o oborech a přírůstkovém souhlasu.

V registraci aplikace na portálu se zobrazí karta **oprávnění rozhraní API.** To poskytuje seznam api a oprávnění (obory), které vaše aplikace je aktuálně nakonfigurován pro žádost o přístup. Zobrazuje také seznam názvů oborů přidružených ke každému oprávnění rozhraní API.

### <a name="user-consent"></a>Souhlas uživatele

S ADAL a koncový bod AAD v1, souhlas uživatele s prostředky, které vlastní byla udělena při prvním použití. S MSAL a platformou identit microsoftu lze požádat o souhlas postupně. Přírůstkový souhlas je užitečný pro oprávnění, která může uživatel považovat za vysoké oprávnění, nebo může jinak zpochybnit, pokud není poskytnuto jasné vysvětlení, proč je vyžadováno oprávnění. V ADAL tato oprávnění může mít za následek, že uživatel opustil přihlášení k vaší aplikaci.

> [!TIP]
> Doporučujeme použít přírůstkový souhlas ve scénářích, kde je potřeba poskytnout uživateli další kontext o tom, proč vaše aplikace potřebuje oprávnění.

### <a name="admin-consent"></a>Souhlas správce

Správci organizace mohou souhlasit s oprávněními, která vaše aplikace vyžaduje jménem všech členů organizace. Některé organizace umožňují správcům pouze souhlas s aplikacemi. Souhlas správce vyžaduje, abyste do registrace aplikace zahrnuli všechna oprávnění rozhraní API a obory používané vaší aplikací.

> [!TIP]
> I když můžete požádat o obor pomocí MSAL pro něco, co není součástí registrace aplikace, doporučujeme aktualizovat registraci aplikace tak, aby zahrnovala všechny prostředky a obory, které uživatel může někdy udělit oprávnění.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migrace z ID prostředků do oborů

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Ověření a vyžádání autorizace pro všechna oprávnění při prvním použití

Pokud aktuálně používáte ADAL a nepotřebujete použít přírůstkový souhlas, nejjednodušší způsob, jak začít používat `acquireToken` MSAL `AcquireTokenParameter` je podat žádost pomocí nového objektu a nastavení hodnoty ID prostředku.

> [!CAUTION]
> Není možné nastavit obory i ID prostředku. Pokus o nastavení obou bude `IllegalArgumentException`mít za následek .

 Výsledkem bude stejné chování v1, které se používá. Všechna oprávnění požadovaná při registraci aplikace jsou požadována od uživatele během jeho první interakce.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Ověřování a vyžádání oprávnění pouze podle potřeby

Pokud chcete využít výhod přírůstkového souhlasu, vytvořte seznam oprávnění (oborů), která vaše aplikace používá z registrace aplikace, a uspořádejte je do dvou seznamů na základě:

- Jaké obory chcete požádat během první interakce uživatele s vaší aplikací během přihlášení.
- Oprávnění, která jsou přidružena k důležité funkci vaší aplikace, která budete muset také vysvětlit uživateli.

Po uspořádání oborů uspořádejte každý seznam, podle kterého prostředek (API) chcete požádat o token. Stejně jako všechny ostatní obory, které chcete, aby uživatel schválil současně.

Parametry objekt použitý k požadavku na MSAL podporuje:

- `Scope`: Seznam oborů, pro které chcete požádat o autorizaci a získat přístupový token.
- `ExtraScopesToConsent`: Další seznam oborů, pro které chcete požádat o autorizaci, když požadujete přístupový token pro jiný prostředek. Tento seznam oborů umožňuje minimalizovat počet, kolikrát je třeba požádat o autorizaci uživatele. Což znamená méně uživatelských oprávnění nebo výzev k souhlasu.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Migrace z AuthenticationContext do PublicClientApplications

### <a name="constructing-publicclientapplication"></a>Vytváření aplikace PublicClientApplication

Při použití MSAL, konkretizovat `PublicClientApplication`. Tento objekt modeluje identitu vaší aplikace a používá se k pořizovat požadavky na jeden nebo více úřadů. Pomocí tohoto objektu nakonfigurujete identitu klienta, přesměrujete identifikátor URI, výchozí autoritu, zda chcete použít prohlížeč zařízení vs. vložené webové zobrazení, úroveň protokolu a další.

Tento objekt můžete deklarativně nakonfigurovat pomocí služby JSON, kterou zadáte jako soubor nebo uložíte jako prostředek v rámci sady APK.

Přestože tento objekt není singleton, interně používá sdílené `Executors` pro interaktivní a tiché požadavky.

### <a name="business-to-business"></a>Business to Business

V ADAL každá organizace, které požadujete přístupové tokeny od vyžaduje samostatnou instanci `AuthenticationContext`. V MSAL, to již není požadavek. Můžete určit autoritu, od které chcete požádat o token jako součást vašeho tichého nebo interaktivního požadavku.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Migrace z ověření autority na známé úřady

MSAL nemá příznak pro povolení nebo zakázání ověření autority. Ověření autority je funkce v ADAL a v časných verzích MSAL, která zabraňuje váš kód v vyžádání tokenů z potenciálně škodlivé autority. MSAL nyní načte seznam úřadů známých společnosti Microsoft a sloučí tento seznam s úřady, které jste zadali v konfiguraci.

> [!TIP]
> Pokud jste uživatelem Azure Business to Consumer (B2C), znamená to, že už nemusíte zakazovat ověřování autority. Místo toho zahrňte všechny podporované zásady Azure AD B2C jako autority v konfiguraci MSAL.

Pokud se pokusíte použít autoritu, která není společnosti Microsoft známa a není zahrnuta ve vaší konfiguraci, získáte . `UnknownAuthorityException`

### <a name="logging"></a>protokolování
Nyní můžete deklarativně nakonfigurovat protokolování jako součást konfigurace, například takto:

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>Migrace z UserInfo do účtu

V ADAL `AuthenticationResult` poskytuje `UserInfo` objekt používaný k načtení informací o ověřeném účtu. Termín "uživatel", což znamenalo lidského nebo softwarového agenta, byl použit způsobem, který ztěžoval komunikaci, že některé aplikace podporují jednoho uživatele (ať už člověka nebo softwarového agenta), který má více účtů.

Zvažte bankovní účet. Můžete mít více než jeden účet u více než jedné finanční instituce. Při otevření účtu vám (uživateli) budou vydána pověření, například kód PIN & karty ATM, která se používají pro přístup k zůstatku, platbám faktur a tak dále pro každý účet. Tyto pověřovací listiny lze použít pouze u finanční instituce, která je vydala.

Analogicky, stejně jako účty u finanční instituce, účty v platformě identit Microsoft jsou přístupné pomocí pověření. Tato pověření jsou registrována nebo vydána společností Microsoft. Nebo společností Microsoft jménem organizace.

Pokud se platforma identit společnosti Microsoft liší od finanční instituce, je v této analogii to, že platforma identit společnosti Microsoft poskytuje rámec, který uživateli umožňuje používat jeden účet a přidružená pověření pro přístup k prostředkům, do kterých patří více jednotlivců a organizací. Je to jako být schopen používat kartu vydanou jednou bankou, v další finanční instituci. To funguje, protože všechny dotyčné organizace používají platformu identit společnosti Microsoft, která umožňuje použití jednoho účtu ve více organizacích. Tady je příklad:

Sam pracuje pro Contoso.com ale spravuje virtuální počítače Azure, které patří do Fabrikam.com. Aby sam mohl spravovat fabrikamovy virtuální počítače, musí mít oprávnění k přístupu k nim. Tento přístup lze udělit přidáním Účtu Sam Fabrikam.com a udělením jeho účtu roli, která mu umožňuje pracovat s virtuálními počítači. To by se stalo s portálem Azure.

Přidání Samova účtu Contoso.com jako člena Fabrikam.com by mělo za následek vytvoření nového záznamu ve službě Azure Active Directory společnosti Fabrikam.com pro Sama. Samův záznam ve službě Azure Active Directory se označuje jako objekt uživatele. V tomto případě by tento objekt uživatele směřovat zpět na objekt uživatele Sam v Contoso.com. Sam je Fabrikam uživatelský objekt je místní reprezentace Sam, a bude použit k ukládání informací o účtu spojené se Sam v kontextu Fabrikam.com. V Contoso.com, Sam titul je Senior DevOps Konzultant. Ve společnosti Fabrikam je Samův titul Contractor-Virtual Machines. V Contoso.com Sam není zodpovědný, ani oprávněn, ke správě virtuálních počítačů. V Fabrikam.com je to jeho jediná práce. Přesto Sam má stále jen jednu sadu pověření sledovat, které jsou pověření vydané Contoso.com.

Po úspěšném `acquireToken` volání se zobrazí odkaz na `IAccount` objekt, který lze `acquireTokenSilent` použít v pozdějších požadavcích.

### <a name="imultitenantaccount"></a>iVíceklientského účtu

Pokud máte aplikaci, která přistupuje k deklaracím o účtu od `IAccount` každého `IMultiTenantAccount`z klientů, ve kterém je účet reprezentován, můžete přetypovat objekty do . Toto rozhraní poskytuje `ITenantProfiles`mapu , zakódované podle ID klienta, která umožňuje přístup k deklaracím, které patří do účtu v každém z klientů, od kterých jste požadovali token, vzhledem k aktuálnímu účtu.

Nároky v kořenovém adresáři `IAccount` a `IMultiTenantAccount` vždy obsahují nároky od domácího nájemce. Pokud jste ještě neprovedli žádost o token v rámci domácího tenanta, bude tato kolekce prázdná.

## <a name="other-changes"></a>Další změny

### <a name="use-the-new-authenticationcallback"></a>Použití nového zpětného volání Authentication

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

## <a name="migrate-to-the-new-exceptions"></a>Migrace na nové výjimky

V ADAL existuje jeden typ výjimky , `AuthenticationException`který zahrnuje metodu pro načtení hodnoty `ADALError` výčtu.
V MSAL existuje hierarchie výjimek a každý má vlastní sadu přidružených specifických kódů chyb.

Seznam výjimek MSAL

|Výjimka  | Popis  |
|---------|---------|
| `MsalException`     | Výchozí zaškrtnutá výjimka vyvolána MSAL.  |
| `MsalClientException`     | Vyvolána, pokud je chyba na straně klienta. |
| `MsalArgumentException`     | Vyvolána, pokud jeden nebo více argumentů vstupů jsou neplatné. |
| `MsalClientException`     | Vyvolána, pokud je chyba na straně klienta. |
| `MsalServiceException`     | Vyvolána, pokud je chyba na straně serveru. |
| `MsalUserCancelException`     | Vyvolána, pokud uživatel zrušil tok ověřování.  |
| `MsalUiRequiredException`     | Vyvolána, pokud token nelze aktualizovat tiše.  |
| `MsalDeclinedScopeException`     | Vyvolána, pokud jeden nebo více požadovaných oborů byly odmítnuty serverem.  |
| `MsalIntuneAppProtectionPolicyRequiredException` | Vyvolána, pokud má prostředek povolenou zásadu ochrany MAMCA. |

### <a name="adalerror-to-msalexception-errorcode"></a>ADALError Code aDALErrorError

### <a name="adal-logging-to-msal-logging"></a>Protokolování ADAL do protokolování MSAL

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
