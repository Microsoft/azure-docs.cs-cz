---
title: Doporučené postupy pro platformu microsoftidentit | Azure
description: Seznamte se s doporučenými postupy, doporučeními a běžnými kontrolami při integraci s platformou identit Microsoftu.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 56975cebbfe4f6dd6452c850c338d431faea27bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050497"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Osvědčené postupy a doporučení platformy identit microsoftu

Tento článek upozorňuje na osvědčené postupy, doporučení a běžné přehlédnutí při integraci s platformou identit microsoftu.  Tento kontrolní seznam vás navede k vysoce kvalitní a bezpečné integraci. Pravidelně kontrolujte tento seznam, abyste měli jistotu, že budete udržovat kvalitu a zabezpečení integrace aplikace s platformou identit. Kontrolní seznam není určen ke kontrole celé aplikace. Obsah kontrolního seznamu se může změnit, protože platformu vylepšujeme.

Pokud teprve začínáte, podívejte se na [dokumentaci k platformě identit Microsoftu,](index.yml) kde najdete informace o základech ověřování, scénářích aplikací na platformě microsoftových identit a další informace.

Pomocí následujícího kontrolního seznamu můžete zajistit, aby byla vaše aplikace efektivně integrována s [platformou identit společnosti Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).

## <a name="basics"></a>Základy

|   |   |
|---|---|
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Přečtěte si [zásady platformy Microsoft](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409)a seznamte se s ně. Ujistěte se, že vaše aplikace dodržuje podmínky uvedené v tom smyslu, že jsou navrženy tak, aby chránily uživatele a platformu. |

## <a name="ownership"></a>Vlastnictví

|   |   |
|---|---|
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Ujistěte se, že informace spojené s účtem, který jste použili k registraci a správě aplikací, jsou aktuální. |

## <a name="branding"></a>Branding

|   |   |
|---|---|
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Dodržujte [pokyny pro branding aplikací](howto-add-branding-in-azure-ad-apps.md). |
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Zadejte smysluplný název a logo pro vaši aplikaci. Tyto informace se zobrazí na výzvu k [souhlasu vaší žádosti](application-consent-experience.md). Ujistěte se, že vaše jméno a logo jsou reprezentativní pro vaši společnost / produkt, aby uživatelé mohli činit informovaná rozhodnutí. Ujistěte se, že neporušujete žádné ochranné známky. |

## <a name="privacy"></a>Ochrana osobních údajů

|   |   |
|---|---|
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Poskytněte odkazy na smluvní podmínky aplikace a prohlášení o zásadách ochrany osobních údajů. |

## <a name="security"></a>Zabezpečení

|   |   |
|---|---|
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Správa identifikátorů URI přesměrování: <ul><li>Udržujte vlastnictví všech identifikátorů URI přesměrování a udržujte záznamy DNS pro ně aktuální.</li><li>Nepoužívejte zástupné znaky (*) v identifikátorech URI.</li><li>U webových aplikací zkontrolujte, jestli jsou všechny identifikátory URI zabezpečené a šifrované (například pomocí schémat https).</li><li>Pro veřejné klienty, použijte webové platformy přesměrování URI, pokud je to možné (hlavně pro iOS a Android). V opačném případě použijte přesměrování identifikátorů URI s vysokým množstvím náhodnosti, abyste zabránili kolizím při volání zpět do aplikace.</li><li>Pokud je vaše aplikace používána od izolovaného `https://login.microsoftonline.com/common/oauth2/nativeclient`webového agenta, můžete použít .</li><li>Pravidelně kontroluj a ořízněte všechny nepoužívané nebo zbytečné přesměrování identifikátorů URI.</li></ul> |
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Pokud je vaše aplikace registrovaná v adresáři, minimalizujte a ručně sledujte seznam vlastníků registrace aplikací. |
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Nepovolujte podporu pro [implicitní tok grantů OAuth2,](v2-oauth2-implicit-grant-flow.md) pokud není výslovně vyžadováno. Informace o platném scénáři [naleznete zde](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Přejděte za uživatelské jméno/heslo. Nepoužívejte [tok pověření hesla vlastníka prostředku (ROPC),](v2-oauth-ropc.md)který přímo zpracovává hesla uživatelů. Tento tok vyžaduje vysoký stupeň důvěryhodnosti a expozice uživatele a měl by být používán pouze v případě, že nelze použít jiné, bezpečnější toky. Tento tok je stále potřeba v některých scénářích (jako DevOps), ale pozor, že jeho použití bude ukládat omezení pro vaši aplikaci.  Pro modernější přístupy si [přečtěte ověřování toků a scénáře aplikací](authentication-flows-app-scenarios.md).|
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Chraňte a spravujte své důvěrné přihlašovací údaje k aplikacím pro webové aplikace, webová api a aplikace pro daemony. Použijte [pověření certifikátu](active-directory-certificate-credentials.md), nikoli pověření hesla (tajné klíče klienta). Pokud musíte použít přihlašovací údaje hesla, nenastavovali ho ručně. Neukládejte přihlašovací údaje v kódu nebo config a nikdy povolit, aby byly zpracovány lidmi. Pokud je to možné, použijte [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) nebo Azure Key [Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) k ukládání a pravidelné střídání přihlašovacích údajů. |
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Ujistěte se, že vaše aplikace požaduje nejnižší oprávnění oprávnění. Požádejte o oprávnění, která vaše aplikace absolutně potřebuje, a pouze v případě, že je potřebujete. Seznamte se s různými [typy oprávnění](v2-permissions-and-consent.md#permission-types). V případě potřeby používejte pouze oprávnění aplikací. pokud je to možné, používejte delegovaná oprávnění. Úplný seznam oprávnění aplikace Microsoft Graph naleznete v tomto [odkazu na oprávnění](https://docs.microsoft.com/graph/permissions-reference). |
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Pokud zabezpečujete rozhraní API pomocí platformy identit microsoftu, pečlivě si promyslet e-mailem oprávnění, která by měla vystavit. Zvažte, co je správné rozlišovací schopnost pro vaše řešení a která oprávnění vyžadují souhlas správce. Před provedením jakýchkoli rozhodnutí o autorizaci zkontrolujte očekávaná oprávnění v příchozích tokenech. |

## <a name="implementation"></a>Implementace

|   |   |
|---|---|
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | K bezpečnému přihlašování uživatelů používejte moderní řešení ověřování (OAuth 2.0, [OpenID Connect).](v2-protocols-oidc.md) |
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) |  Neprogramujte přímo protokoly, jako je OAuth 2.0 a Open ID. Místo toho využijte [knihovnu ověřování společnosti Microsoft (MSAL).](msal-overview.md) Knihovny MSAL bezpečně zabalit protokoly zabezpečení ve snadno použitelné knihovně a získáte integrovanou podporu pro scénáře [podmíněného přístupu,](/azure/active-directory/conditional-access/overview) [jednotné přihlašování celého zařízení (SSO)](/azure/active-directory/manage-apps/what-is-single-sign-on)a integrovanou podporu ukládání tokenů do mezipaměti. Další informace naleznete v seznamu [klientských knihoven](reference-v2-libraries.md#microsoft-supported-client-libraries) podporovaných společností Microsoft a [v knihovnách middlewaru](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) a v seznamu [kompatibilních klientských knihoven jiných výrobců](reference-v2-libraries.md#compatible-client-libraries).<br/><br/>Pokud je nutné předat kód pro ověřovací protokoly, měli byste postupovat podle metodiky, jako je [například Microsoft SDL](https://www.microsoft.com/sdl/default.aspx). Věnujte velkou pozornost bezpečnostním aspektům ve specifikacích norem pro každý protokol.|
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) |  Migrace existujících aplikací z [Knihovny ověřování Azure Active Directory (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) do [Knihovny ověřování Microsoftu](msal-overview.md). MSAL je nejnovější řešení platformy identity společnosti Microsoft a je upřednostňováno před ADAL. Je k dispozici na .NET, JavaScript, Android, iOS, macOS a je také ve veřejné verzi preview pro Python a Java. Přečtěte si další informace o migraci [aplikací ADAL.NET](msal-net-migration.md), [ADAL.js](msal-compare-msal-js-and-adal-js.md)a [ADAL.NET a iOS broker.](msal-net-migration-ios-broker.md)|
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) |  Pro mobilní aplikace nakonfigurujte každou platformu pomocí prostředí registrace aplikací. Aby vaše aplikace mohla využít microsoft authenticator nebo portál společnosti Microsoft portal pro jednotné přihlášení, vaše aplikace potřebuje nakonfigurovaný identifikátor URI přesměrování zprostředkovatele. To umožňuje společnosti Microsoft vrátit řízení do aplikace po ověření. Při konfiguraci jednotlivých platforem vás prostředí registrace aplikace provede procesem. Pomocí rychlého startu stáhněte pracovní příklad. V systému iOS používejte makléře a systémové webové zobrazení, kdykoli je to možné.|
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) |  Ve webových aplikacích nebo webových apich uchovávejte jednu mezipaměť tokenů na účet.  U webových aplikací by měla být mezipaměť tokenů zadána pomocí ID účtu.  Pro webová rozhraní API by měl být účet zadal pomocí hash tokenu používaného k volání rozhraní API. MSAL.NET poskytuje vlastní serializaci mezipaměti tokenů v rozhraní .NET Framework a podplatformách .NET Core. Z důvodů zabezpečení a výkonu doporučujeme serializovat jednu mezipaměť na uživatele. Další informace naleznete o [serializaci mezipaměti tokenů](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).|
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Pokud jsou data, která vaše aplikace vyžaduje, dostupná prostřednictvím [Microsoft Graphu](https://developer.microsoft.com/graph), požádejte o oprávnění pro tato data pomocí koncového bodu Microsoft Graphu, nikoli pomocí individuálního rozhraní API. |
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) |Nedívejte se na hodnotu přístupového tokenu ani se nepokoušejte ji analyzovat jako klienta.  Mohou změnit hodnoty, formáty nebo dokonce zašifrovat bez varování - vždy použijte id_token pokud váš klient potřebuje něco o uživateli zjistit, nebo zavolat Microsoft Graph.  Pouze webová api by měla analyzovat přístupové tokeny (protože jsou to ty, které definují formát a nastavení šifrovacích klíčů). |

## <a name="end-user-experience"></a>Prostředí koncového uživatele

|   |   |
|---|---|
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | [Seznamte se se zkušenostmi se souhlasem](application-consent-experience.md) a nakonfigurujte části výzvy k souhlasu aplikace tak, aby koncoví uživatelé a správci měli dostatek informací k určení, zda vaší aplikaci důvěřují. |
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Minimalizujte počet, kolikrát uživatel potřebuje zadat přihlašovací údaje při používání aplikace, a to pokusem o tiché ověřování (získání tichého tokenu) před interaktivními toky. |
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Nepoužívejte "prompt=consent" pro každé přihlášení. Výzvu=souhlas používejte pouze v případě, že jste zjistili, že potřebujete požádat o souhlas s dalšími oprávněními (například pokud jste změnili požadovaná oprávnění aplikace). |
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Pokud je to možné, obohaťte svou aplikaci o uživatelská data. Použití [rozhraní Microsoft Graph API](https://developer.microsoft.com/graph) je snadný způsob, jak to provést. Nástroj [Průzkumník grafů,](https://developer.microsoft.com/graph/graph-explorer) který vám pomůže začít. |
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Zaregistrujte úplnou sadu oprávnění, která vaše aplikace vyžaduje, aby správci mohli snadno udělit souhlas svému tenantovi. Pomocí [přírůstkového souhlasu](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) za běhu můžete uživatelům pomoci pochopit, proč vaše aplikace požaduje oprávnění, která mohou uživatele při prvním spuštění znepokojovat nebo zmást. |
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementujte [čisté prostředí pro jednotné odhlašování](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Je to požadavek na ochranu soukromí a zabezpečení a vytváří dobrou uživatelskou zkušenost. |

## <a name="testing"></a>Testování

|   |   |
|---|---|
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Otestujte [zásady podmíněného přístupu,](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) které mohou ovlivnit schopnost uživatelů používat vaši aplikaci. |
| ![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Otestujte svou aplikaci se všemi možnými účty, které máte v plánu podporovat (například pracovní nebo školní účty, osobní účty Microsoft, dětské účty a suverénní účty). |

## <a name="additional-resources"></a>Další zdroje

Prostudujte si podrobné informace týkající se v2.0:

* [Platforma identit Microsoftu (přehled v2.0)](v2-overview.md)
* [Odkaz na protokoly platformy identit společnosti Microsoft](active-directory-v2-protocols.md)
* [informace o přístupových tokenech](access-tokens.md)
* [informace o tokenech ID](id-tokens.md)
* [Odkaz na ověřovací knihovny](reference-v2-libraries.md)
* [Oprávnění a souhlas v platformě microsoft identity](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
