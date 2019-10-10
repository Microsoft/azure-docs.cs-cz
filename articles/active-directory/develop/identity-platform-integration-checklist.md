---
title: Osvědčené postupy pro Microsoft Identity Platform | Azure
description: Seznamte se s osvědčenými postupy, doporučeními a běžnými dohledem při integraci s platformou Microsoft identity.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f7e9b738a55248678a207f0b298ef65e6c2761a4
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240156"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Osvědčené postupy a doporučení pro Microsoft Identity Platform

Tento článek popisuje osvědčené postupy, doporučení a běžné přehledy při integraci s platformou Microsoft identity.  Tento kontrolní seznam vás provede vysokou kvalitou a zabezpečenou integrací. Pravidelně kontrolujte tento seznam, abyste se ujistili, že udržujete kvalitu a zabezpečení integrace vaší aplikace s platformou identity. Kontrolní seznam není určený ke kontrole celé aplikace. Obsah kontrolního seznamu se může změnit, protože provádíme vylepšení platformy.

Pokud teprve začínáte, podívejte se na dokumentaci k [platformě Microsoft Identity](index.yml) , kde najdete informace o základech ověřování, scénářích aplikací v platformě Microsoft identity a dalších.

Pomocí následujícího kontrolního seznamu se ujistěte, že je vaše aplikace efektivně integrovaná s [platformou Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/).

## <a name="basics"></a>Práce

|   |   |
|---|---|
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Přečtěte si a pochopte [Zásady platformy Microsoft](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Ujistěte se, že vaše aplikace dodržuje podmínek, které jsou navržené tak, aby chránily uživatele a platformu. |

## <a name="ownership"></a>Jejíž

|   |   |
|---|---|
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Ujistěte se, že informace přidružené k účtu, který jste použili k registraci a správě aplikací, jsou aktuální. |

## <a name="branding"></a>Branding

|   |   |
|---|---|
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Dodržování [pokynů pro aplikace](howto-add-branding-in-azure-ad-apps.md). |
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Zadejte smysluplný název a logo pro vaši aplikaci. Tyto informace se zobrazí na [příkazovém řádku pro vyjádření souhlasu vaší aplikace](application-consent-experience.md). Ujistěte se, že vaše jméno a logo jsou zástupci vaší společnosti nebo produktu, aby uživatelé mohli dělat kvalifikovaná rozhodnutí. Ujistěte se, že nedošlo k porušení ochranných známek. |

## <a name="privacy"></a>Ochrana osobních údajů

|   |   |
|---|---|
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Poskytněte odkazy na požadavky služby a prohlášení o zásadách ochrany osobních údajů vaší aplikace. |

## <a name="security"></a>Zabezpečení

|   |   |
|---|---|
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Spravujte identifikátory URI pro přesměrování: <ul><li>Udržujte vlastnictví všech identifikátorů URI pro přesměrování a Udržujte záznamy DNS v aktuálním stavu.</li><li>V identifikátorech URI nepoužívejte zástupné znaky (*).</li><li>U webových aplikací zajistěte, aby všechny identifikátory URI byly zabezpečené a šifrované (například pomocí schémat protokolu HTTPS).</li><li>U veřejných klientů použijte identifikátory URI pro přesměrování specifické pro platformu, pokud je to možné (hlavně pro iOS a Android). V opačném případě použijte identifikátory URI přesměrování s vysokou mírou náhodnosti, abyste zabránili kolizím při volání zpět do aplikace.</li><li>Pokud se vaše aplikace používá z izolovaného webového agenta, můžete použít https://login.microsoftonline.com/common/oauth2/nativeclient.</li><li>Pravidelně kontrolujte a ořízněte všechny nepoužívané nebo zbytečné identifikátory URI pro přesměrování.</li></ul> |
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Pokud je vaše aplikace zaregistrovaná v adresáři, minimalizujte a ručně Sledujte seznam vlastníků registrace aplikací. |
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Nepovolujte podporu pro [tok implicitního udělení OAuth2](v2-oauth2-implicit-grant-flow.md) , pokud je explicitně nepožadujete. [Tady](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)najdete informace o platném scénáři. |
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Přesunutí za uživatelské jméno a heslo. Nepoužívejte [tok přihlašovacích údajů k heslům vlastníka prostředků (ROPC)](v2-oauth-ropc.md), který přímo zpracovává hesla uživatelů. Tento tok vyžaduje vysoký stupeň důvěry a ohrožení uživatele a měl by se používat jenom v případě, že se nedají použít jiné, bezpečnější toky. Tento tok je stále potřeba v některých scénářích (například DevOps), ale Upozorňujeme, že jeho použití způsobí omezení pro vaši aplikaci.  Pro moderní přístupy si můžete přečíst [toky ověřování a scénáře použití aplikací](authentication-flows-app-scenarios.md).|
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Chraňte a spravujte přihlašovací údaje důvěrných aplikací pro webové aplikace, webová rozhraní API a aplikace démona. Použijte [přihlašovací údaje certifikátu](active-directory-certificate-credentials.md), nikoli přihlašovací údaje hesla (tajné klíče klienta). Pokud je nutné použít přihlašovací údaje hesla, nenastavte ji ručně. Neukládejte přihlašovací údaje v kódu nebo konfiguraci a nikdy je nepovolíte, aby je lidé nepoužívali. Pokud je to možné, využijte [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) nebo [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) k ukládání a pravidelnému střídání přihlašovacích údajů. |
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Ujistěte se, že vaše aplikace požaduje nejnižší oprávnění. Zeptejte se jenom na oprávnění, která vaše aplikace naprosto potřebuje, a jenom když je potřebujete. Pochopení různých [typů oprávnění](v1-permissions-and-consent.md#types-of-permissions) V případě potřeby použijte pouze oprávnění aplikace; Pokud je to možné, použijte delegovaná oprávnění. Úplný seznam oprávnění Microsoft Graph najdete v tématu s [odkazem](https://docs.microsoft.com/graph/permissions-reference)na tato oprávnění. |
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Pokud zabezpečujete rozhraní API s využitím platformy Microsoft identity, pečlivě si promyslete oprávnění, která by měla zveřejnit. Zvažte, jaké jsou správné členitosti řešení a které oprávnění vyžadují souhlas správce. Před provedením jakýchkoli autorizačních rozhodnutí ověřte, zda jsou v příchozích tokenech očekávaná oprávnění. |

## <a name="implementation"></a>Implementace

|   |   |
|---|---|
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | K bezpečnému přihlášení uživatelů používejte řešení pro moderní ověřování (OAuth 2,0, [OpenID Connect](v2-protocols-oidc.md)). |
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) |  Neprovádějte program přímo proti protokolům, jako je OAuth 2,0 a Open ID. Místo toho využijte [Microsoft Authentication Library (MSAL)](msal-overview.md). Knihovny MSAL bezpečně zabalí protokoly zabezpečení do snadno použitelné knihovny a získáte integrovanou podporu pro scénáře [podmíněného přístupu](/azure/active-directory/conditional-access/overview) , [jednotné přihlašování (SSO) v rámci jednotného přihlašování (SSO)](/azure/active-directory/manage-apps/what-is-single-sign-on)a integrovanou podporu ukládání tokenů do mezipaměti. Další informace najdete v seznamu podporovaných [klientských knihoven](reference-v2-libraries.md#microsoft-supported-client-libraries) a [knihoven middlewaru](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) od Microsoftu a v seznamu [kompatibilních klientských knihoven třetích stran](reference-v2-libraries.md#compatible-client-libraries).<br/><br/>Pokud potřebujete pro ověřovací protokoly rukou, měli byste postupovat podle metodologie, jako je například [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx). Věnujte velkou pozornost hlediskům zabezpečení v specifikacích standardů pro jednotlivé protokoly.|
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) |  Migrujte stávající aplikace z [knihovny Azure Active Directory Authentication Library (ADAL)](active-directory-authentication-libraries.md) do [knihovny Microsoft Authentication Library](msal-overview.md). MSAL je řešení nejnovější platformy od Microsoftu a upřednostňuje se pro ADAL. Je k dispozici na platformě .NET a JavaScriptu a je také ve verzi Public Preview pro Android, iOS, Python a Java. Přečtěte si další informace o migraci aplikací [ADAL.NET](msal-net-migration.md), [ADAL. js](msal-compare-msal-js-and-adal-js.md)a [ADAL.NET a zprostředkovatelů iOS](msal-net-migration-ios-broker.md) .|
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) |  Pro mobilní aplikace nakonfigurujte jednotlivé platformy pomocí prostředí pro registraci aplikací. Aby vaše aplikace mohla využít výhod Microsoft Authenticator nebo Microsoft Portál společnosti pro jednotné přihlašování, vaše aplikace potřebuje nakonfigurovaný identifikátor URI pro přesměrování zprostředkovatele. To umožňuje, aby Microsoft po ověření vrátil kontrolu na vaši aplikaci. Při konfiguraci jednotlivých platforem vás proces registrace aplikace provede procesem. Pracovní příklad můžete stáhnout pomocí rychlého startu. Pokud je to možné, používejte v iOS zprostředkovatele a systémové webzobrazení.|
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) |  Ve webových aplikacích nebo webových rozhraních API ponechte jednu mezipaměť tokenů na účet.  Pro webové aplikace by měla být mezipaměť tokenů nastavená ID účtu.  Pro webová rozhraní API by měl být účet nastaven pomocí hash tokenu, který se používá k volání rozhraní API. MSAL.NET poskytuje serializaci mezipaměti vlastního tokenu v podplatformách .NET Framework a .NET Core. Z důvodu zabezpečení a výkonu doporučujeme, abyste měli v úmyslu serializovat jednu mezipaměť na uživatele. Další informace najdete v tématu o [serializaci mezipaměti tokenů](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).|
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Pokud jsou data, která vaše aplikace vyžaduje, dostupná prostřednictvím [Microsoft Graph](https://developer.microsoft.com/graph), požádejte o oprávnění pro tato data pomocí koncového bodu Microsoft Graph místo jednotlivého rozhraní API. |
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) |Nehlédněte do hodnoty přístupového tokenu nebo se pokuste o jeho analýzu jako klient.  Můžou měnit hodnoty, formáty nebo dokonce zašifrované bez upozornění – vždy použijte id_token, pokud se klient potřebuje dozvědět něco o uživateli nebo volat Microsoft Graph.  Přístupové tokeny by měly analyzovat jenom webová rozhraní API (protože jsou ta definující formát a nastavení šifrovacích klíčů). |

## <a name="end-user-experience"></a>Prostředí koncového uživatele

|   |   |
|---|---|
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Seznamte [se s vyjádřením souhlasu](application-consent-experience.md) a nakonfigurujte části výzvy pro vyjádření souhlasu vaší aplikace, aby koncoví uživatelé a správci měli dostatek informací, aby zjistili, jestli důvěřují vaší aplikaci. |
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Minimalizujte počet pokusů, které uživatel musí zadat přihlašovací údaje při použití vaší aplikace, tak, že před interaktivními toky zkusí spustit tiché ověření (získání tichého tokenu). |
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Pro každé přihlášení nepoužívejte "Prompt = souhlasu". Pokud jste zjistili, že potřebujete požádat o souhlas s dalšími oprávněními (například pokud jste změnili požadovaná oprávnění aplikace), použijte příkaz Prompt = souhlas. |
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | V případě potřeby obohacení aplikace o uživatelská data. Použití [rozhraní Microsoft Graph API](https://developer.microsoft.com/graph) představuje snadný způsob, jak to provést. Nástroj [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) , který vám může pomáhat začít. |
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Zaregistrujte úplnou sadu oprávnění, která vaše aplikace vyžaduje, aby správci mohli udělit souhlas svým klientům snadno. Pomocí [přírůstkového souhlasu](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) v době spuštění pomůžete uživatelům pochopit, proč vaše aplikace požaduje oprávnění, která se mohou v případě potřeby při prvním spuštění týkat nebo Zaměňujte uživatelům. |
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementujte [čisté prostředí pro jednotné odhlašování](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Je to ochrana osobních údajů a požadavky na zabezpečení a přináší dobrý dojem uživatelské prostředí. |

## <a name="testing"></a>Zkouší

|   |   |
|---|---|
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Otestujte [zásady podmíněného přístupu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) , které mohou mít vliv na schopnost uživatelů používat vaši aplikaci. |
| ![prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Otestujte svoji aplikaci se všemi možnými účty, které hodláte podporovat (například pracovní nebo školní účty, osobní účty Microsoft, podřízené účty a účty z svrchovaného účtu). |

## <a name="additional-resources"></a>Další materiály a zdroje informací

Prozkoumejte podrobné informace o verzi v 2.0:

* [Microsoft Identity Platform (přehled verze 2.0)](v2-overview.md)
* [Referenční informace o protokolech platformy Microsoft Identity Platform](active-directory-v2-protocols.md)
* [Reference přístupových tokenů](access-tokens.md)
* [Odkazy na tokeny ID](id-tokens.md)
* [Referenční dokumentace knihoven ověřování](reference-v2-libraries.md)
* [Oprávnění a souhlas na platformě Microsoft identity](v2-permissions-and-consent.md)
* [Rozhraní API pro Microsoft Graph](https://developer.microsoft.com/graph)
