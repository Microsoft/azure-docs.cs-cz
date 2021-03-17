---
title: Osvědčené postupy pro Microsoft Identity Platform | Azure
description: Seznamte se s osvědčenými postupy, doporučeními a běžnými dohledem při integraci s platformou Microsoft identity.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: a6a7bf24571660d8e728c1acba29af2504539a18
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219956"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Osvědčené postupy a doporučení pro Microsoft Identity Platform

Tento článek popisuje osvědčené postupy, doporučení a běžné přehledy při integraci s platformou Microsoft identity.  Tento kontrolní seznam vás provede vysokou kvalitou a zabezpečenou integrací. Pravidelně kontrolujte tento seznam, abyste se ujistili, že udržujete kvalitu a zabezpečení integrace vaší aplikace s platformou identity. Kontrolní seznam není určený ke kontrole celé aplikace. Obsah kontrolního seznamu se může změnit, protože provádíme vylepšení platformy.

Pokud teprve začínáte, podívejte se na dokumentaci k [platformě Microsoft Identity](index.yml) , kde najdete informace o základech ověřování, scénářích aplikací v platformě Microsoft identity a dalších.

Pomocí následujícího kontrolního seznamu se ujistěte, že je vaše aplikace efektivně integrovaná s [platformou Microsoft Identity](./index.yml).

> [!TIP]
> *Pomocník pro integraci* v Azure Portal vám může pomáhat při použití řady osvědčených postupů a doporučení. V Azure Portal vyberte libovolnou [registraci vaší aplikace](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) a pak vyberte položku nabídky **Pomocník pro integraci** , abyste mohli začít s pomocníkem.

## <a name="basics"></a>Základy

![Zaškrtněte políčko ](./media/active-directory-integration-checklist/checkbox-two.svg) a pochopte si [Zásady platformy Microsoft](/legal/microsoft-identity-platform/terms-of-use). Ujistěte se, že vaše aplikace dodržuje podmínek, které jsou navržené tak, aby chránily uživatele a platformu.

## <a name="ownership"></a>Vlastnictví

![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) Ujistěte se, že informace přidružené k účtu, který jste použili k registraci a správě aplikací, jsou aktuální.

## <a name="branding"></a>Značka

![zaškrtávací políčko ](./media/active-directory-integration-checklist/checkbox-two.svg) se řídí [pokyny pro aplikace](howto-add-branding-in-azure-ad-apps.md).

![zaškrtávací políčko ](./media/active-directory-integration-checklist/checkbox-two.svg) pro aplikaci zadejte smysluplný název a logo. Tyto informace se zobrazí na [příkazovém řádku pro vyjádření souhlasu vaší aplikace](application-consent-experience.md). Ujistěte se, že vaše jméno a logo jsou zástupci vaší společnosti nebo produktu, aby uživatelé mohli dělat kvalifikovaná rozhodnutí. Ujistěte se, že nedošlo k porušení ochranných známek.

## <a name="privacy"></a>Ochrana osobních údajů

![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) Poskytněte odkazy na požadavky služby a prohlášení o zásadách ochrany osobních údajů vaší aplikace.

## <a name="security"></a>Zabezpečení

![zaškrtávací políčko ](./media/active-directory-integration-checklist/checkbox-two.svg) Spravovat identifikátory URI pro přesměrování: <ul><li>Udržujte vlastnictví všech identifikátorů URI pro přesměrování a Udržujte záznamy DNS v aktuálním stavu.</li><li>V identifikátorech URI nepoužívejte zástupné znaky (*).</li><li>U webových aplikací zajistěte, aby všechny identifikátory URI byly zabezpečené a šifrované (například pomocí schémat protokolu HTTPS).</li><li>U veřejných klientů použijte identifikátory URI pro přesměrování specifické pro platformu, pokud je to možné (hlavně pro iOS a Android). V opačném případě použijte identifikátory URI přesměrování s vysokou mírou náhodnosti, abyste zabránili kolizím při volání zpět do aplikace.</li><li>Pokud se vaše aplikace používá z izolovaného webového agenta, můžete použít `https://login.microsoftonline.com/common/oauth2/nativeclient` .</li><li>Pravidelně kontrolujte a ořízněte všechny nepoužívané nebo zbytečné identifikátory URI pro přesměrování.</li></ul>

![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) Pokud je vaše aplikace zaregistrovaná v adresáři, minimalizujte a ručně Sledujte seznam vlastníků registrace aplikací.

![zaškrtávací políčko ](./media/active-directory-integration-checklist/checkbox-two.svg) nepovoluje podporu pro [tok implicitního udělení OAuth2](v2-oauth2-implicit-grant-flow.md) , pokud není explicitně požadováno. [Tady](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)najdete informace o platném scénáři.

![zaškrtávací políčko se ](./media/active-directory-integration-checklist/checkbox-two.svg) přesune mimo uživatelské jméno a heslo. Nepoužívejte [tok přihlašovacích údajů k heslům vlastníka prostředků (ROPC)](v2-oauth-ropc.md), který přímo zpracovává hesla uživatelů. Tento tok vyžaduje vysoký stupeň důvěry a ohrožení uživatele a měl by se používat jenom v případě, že se nedají použít jiné, bezpečnější toky. Tento tok je stále potřeba v některých scénářích (například DevOps), ale Upozorňujeme, že jeho použití způsobí omezení pro vaši aplikaci.  Pro moderní přístupy si můžete přečíst [toky ověřování a scénáře použití aplikací](authentication-flows-app-scenarios.md).

![políčko ](./media/active-directory-integration-checklist/checkbox-two.svg) chránit a spravovat přihlašovací údaje důvěrných aplikací pro webové aplikace, webová rozhraní API a aplikace démona. Použijte [přihlašovací údaje certifikátu](active-directory-certificate-credentials.md), nikoli přihlašovací údaje hesla (tajné klíče klienta). Pokud je nutné použít přihlašovací údaje hesla, nenastavte ji ručně. Neukládejte přihlašovací údaje v kódu nebo konfiguraci a nikdy je nepovolíte, aby je lidé nepoužívali. Pokud je to možné, využijte [spravované identity pro prostředky Azure](../managed-identities-azure-resources/overview.md) nebo [Azure Key Vault](../../key-vault/general/basic-concepts.md) k ukládání a pravidelnému střídání přihlašovacích údajů.

![zaškrtávací políčko zajistěte ](./media/active-directory-integration-checklist/checkbox-two.svg) , aby vaše aplikace požádala o nejnižší oprávnění. Zeptejte se jenom na oprávnění, která vaše aplikace naprosto potřebuje, a jenom když je potřebujete. Pochopení různých [typů oprávnění](v2-permissions-and-consent.md#permission-types) V případě potřeby použijte pouze oprávnění aplikace; Pokud je to možné, použijte delegovaná oprávnění. Úplný seznam oprávnění Microsoft Graph najdete v tématu s [odkazem](/graph/permissions-reference)na tato oprávnění.

![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) Pokud zabezpečujete rozhraní API s využitím platformy Microsoft identity, pečlivě si promyslete oprávnění, která by měla zveřejnit. Zvažte, jaké jsou správné členitosti řešení a které oprávnění vyžadují souhlas správce. Před provedením jakýchkoli autorizačních rozhodnutí ověřte, zda jsou v příchozích tokenech očekávaná oprávnění.

## <a name="implementation"></a>Implementace

![zaškrtávací políčko ](./media/active-directory-integration-checklist/checkbox-two.svg) používá řešení pro moderní ověřování (OAuth 2,0, [OpenID Connect](v2-protocols-oidc.md)) k bezpečnému přihlášení uživatelů.

![zaškrtávací políčko ](./media/active-directory-integration-checklist/checkbox-two.svg) Neprogramuje přímo proti protokolům, jako je OAuth 2,0 a Open ID. Místo toho využijte [Microsoft Authentication Library (MSAL)](msal-overview.md). Knihovny MSAL bezpečně zabalí protokoly zabezpečení do snadno použitelné knihovny a získáte integrovanou podporu pro scénáře [podmíněného přístupu](../conditional-access/overview.md) , [jednotné přihlašování (SSO) v rámci jednotného přihlašování (SSO)](../manage-apps/what-is-single-sign-on.md)a integrovanou podporu ukládání tokenů do mezipaměti. Další informace najdete v seznamu [klientských knihoven](reference-v2-libraries.md)podporovaných Microsoftem. Pokud je nutné pro ověřovací protokoly předat kód, měli byste postupovat podle rozhraní [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx) nebo podobné metodologie vývoje. Věnujte velkou pozornost hlediskům zabezpečení v specifikacích standardů pro jednotlivé protokoly.

![zaškrtávací políčko ](./media/active-directory-integration-checklist/checkbox-two.svg) migruje stávající aplikace z [knihovny Azure Active Directory Authentication Library (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) do [knihovny Microsoft Authentication Library](msal-overview.md). MSAL je řešení nejnovější platformy od Microsoftu a upřednostňuje se pro ADAL. Je k dispozici pro .NET, JavaScript, Android, iOS, macOS a je také ve verzi Public Preview pro Python a Java. Přečtěte si další informace o migraci aplikací [ADAL.NET](msal-net-migration.md), [ADAL.js](msal-compare-msal-js-and-adal-js.md)a [ADAL.NET a zprostředkovatelů iOS](msal-net-migration-ios-broker.md) .

![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) Pro mobilní aplikace nakonfigurujte jednotlivé platformy pomocí prostředí pro registraci aplikací. Aby vaše aplikace mohla využít výhod Microsoft Authenticator nebo Microsoft Portál společnosti pro jednotné přihlašování, vaše aplikace potřebuje nakonfigurovaný identifikátor URI pro přesměrování zprostředkovatele. To umožňuje, aby Microsoft po ověření vrátil kontrolu na vaši aplikaci. Při konfiguraci jednotlivých platforem vás proces registrace aplikace provede procesem. Pracovní příklad můžete stáhnout pomocí rychlého startu. Pokud je to možné, používejte v iOS zprostředkovatele a systémové webzobrazení.

![zaškrtávací políčko ](./media/active-directory-integration-checklist/checkbox-two.svg) ve webových aplikacích nebo webových rozhraních API ponechte jednu mezipaměť tokenů na účet.  Pro webové aplikace by měla být mezipaměť tokenů nastavená ID účtu.  Pro webová rozhraní API by měl být účet nastaven pomocí hash tokenu, který se používá k volání rozhraní API. MSAL.NET poskytuje serializaci mezipaměti vlastního tokenu v podplatformách .NET Framework a .NET Core. Z důvodu zabezpečení a výkonu doporučujeme, abyste měli v úmyslu serializovat jednu mezipaměť na uživatele. Další informace najdete v tématu o [serializaci mezipaměti tokenů](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).

![zaškrtávací políčko ](./media/active-directory-integration-checklist/checkbox-two.svg) , pokud jsou data, která vaše aplikace vyžaduje, dostupná prostřednictvím [Microsoft Graph](https://developer.microsoft.com/graph), požádejte o oprávnění pro tato data pomocí koncového bodu Microsoft Graph místo jednotlivého rozhraní API.

![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) Nehlédněte do hodnoty přístupového tokenu nebo se pokuste o jeho analýzu jako klient.  Můžou měnit hodnoty, formáty nebo dokonce šifrované bez upozornění – vždy použijte id_token, pokud se klient potřebuje dozvědět něco o uživateli nebo volat Microsoft Graph.  Přístupové tokeny by měly analyzovat jenom webová rozhraní API (protože jsou ta definující formát a nastavení šifrovacích klíčů).

## <a name="end-user-experience"></a>Prostředí koncového uživatele

![Toto zaškrtávací políčko ](./media/active-directory-integration-checklist/checkbox-two.svg) vám [pomůže pochopit činnost](application-consent-experience.md) a nakonfigurovat části výzvy k vyjádření souhlasu vaší aplikace, aby koncoví uživatelé a správci měli dostatek informací, aby zjistili, jestli důvěřují vaší aplikaci.

![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) Minimalizujte počet pokusů, které uživatel musí zadat přihlašovací údaje při použití vaší aplikace, tak, že před interaktivními toky zkusí spustit tiché ověření (získání tichého tokenu).

![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) Pro každé přihlášení nepoužívejte "Prompt = souhlasu". Pokud jste zjistili, že potřebujete požádat o souhlas s dalšími oprávněními (například pokud jste změnili požadovaná oprávnění aplikace), použijte příkaz Prompt = souhlas.

![zaškrtávací políčko ](./media/active-directory-integration-checklist/checkbox-two.svg) , pokud je k dispozici, obohacení aplikaci pomocí uživatelských dat. Použití [rozhraní Microsoft Graph API](https://developer.microsoft.com/graph) představuje snadný způsob, jak to provést. Nástroj [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) , který vám může pomáhat začít.

![zaškrtávací políčko ](./media/active-directory-integration-checklist/checkbox-two.svg) registruje kompletní sadu oprávnění, které vaše aplikace vyžaduje, aby správci mohli udělit souhlas ke svému tenantovi snadno. Pomocí [přírůstkového souhlasu](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) v době spuštění pomůžete uživatelům pochopit, proč vaše aplikace požaduje oprávnění, která se mohou v případě potřeby při prvním spuštění týkat nebo Zaměňujte uživatelům.

![zaškrtávací políčko ](./media/active-directory-integration-checklist/checkbox-two.svg) implementuje [prostředí čistého jednotného odhlašování](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Je to ochrana osobních údajů a požadavky na zabezpečení a přináší dobrý dojem uživatelské prostředí.

## <a name="testing"></a>Testování

![Test zaškrtávacího políčka ](./media/active-directory-integration-checklist/checkbox-two.svg) pro [zásady podmíněného přístupu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) , které mohou ovlivnit schopnost uživatelů používat vaši aplikaci

![zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) Otestujte svoji aplikaci se všemi možnými účty, které hodláte podporovat (například pracovní nebo školní účty, osobní účty Microsoft, podřízené účty a účty z svrchovaného účtu).

## <a name="additional-resources"></a>Další zdroje informací

Prostudujte si podrobné informace týkající se v2.0:

* [Microsoft Identity Platform (přehled)](v2-overview.md)
* [Referenční informace o protokolech platformy Microsoft Identity Platform](active-directory-v2-protocols.md)
* [informace o přístupových tokenech](access-tokens.md)
* [informace o tokenech ID](id-tokens.md)
* [Referenční dokumentace knihoven ověřování](reference-v2-libraries.md)
* [Oprávnění a souhlas na platformě Microsoft identity](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
