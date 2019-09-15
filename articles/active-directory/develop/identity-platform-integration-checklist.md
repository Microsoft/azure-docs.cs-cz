---
title: Integrace s Microsoft Identity platformou | Azure
description: Seznamte se s osvědčenými postupy a běžnými přehledy při integraci s platformou Microsoft identity (v 2.0).
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
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c01f695210ebbd69a26b957e846ff645b1dea5bb
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999009"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Microsoft Identity Platform Integration – kontrolní seznam

Kontrolní seznam integrace platformy Microsoft Identity Platform je určený k zajištění vysoce kvalitní a zabezpečené integrace. Vysvětlují osvědčené postupy a běžné přehledy při integraci s platformou Microsoft identity, takže seznam pravidelně Prohlédněte, abyste měli jistotu, že udržujete kvalitu a zabezpečení integrace vaší aplikace s platformou identity. Kontrolní seznam není určený ke kontrole celé aplikace. Obsah kontrolního seznamu se může změnit, protože provádíme vylepšení platformy.

Pokud teprve začínáte, přečtěte si [dokumentaci](index.yml) , kde najdete informace o základech ověřování, scénářích aplikací v platformě Microsoft Identity Platform a dalších.

## <a name="testing-your-integration"></a>Testování integrace

Pomocí následujícího kontrolního seznamu se ujistěte, že je vaše aplikace efektivně integrovaná s [platformou Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/).

### <a name="basics"></a>Základní informace

|   |   |
|---|---|
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Přečtěte si a pochopte [Zásady platformy Microsoft](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Ujistěte se, že vaše aplikace dodržuje podmínek, které jsou navržené tak, aby chránily uživatele a platformu. |

### <a name="ownership"></a>Jejíž

|   |   |
|---|---|
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Ujistěte se, že informace přidružené k účtu, který jste použili k registraci a správě aplikací, jsou aktuální. |

### <a name="branding"></a>Branding

|   |   |
|---|---|
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Dodržování [pokynů pro aplikace](howto-add-branding-in-azure-ad-apps.md). |
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Zadejte smysluplný název a logo pro vaši aplikaci. Tyto informace se zobrazí na příkazovém řádku pro vyjádření souhlasu vaší aplikace. Ujistěte se, že vaše jméno a logo jsou zástupci vaší společnosti nebo produktu, aby uživatelé mohli dělat kvalifikovaná rozhodnutí. Ujistěte se, že nedošlo k porušení ochranných známek. |

### <a name="privacy"></a>Ochrana osobních údajů

|   |   |
|---|---|
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Poskytněte odkazy na požadavky služby a prohlášení o zásadách ochrany osobních údajů vaší aplikace. |

### <a name="security"></a>Zabezpečení

|   |   |
|---|---|
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Udržujte vlastnictví všech identifikátorů URI pro přesměrování a Udržujte záznamy DNS v aktuálním stavu. V identifikátorech URI nepoužívejte zástupné znaky (*). U webových aplikací zajistěte, aby všechny identifikátory URI byly zabezpečené a šifrované (například pomocí schémat protokolu HTTPS). U veřejných klientů použijte identifikátory URI pro přesměrování specifické pro platformu, pokud je to možné (hlavně pro iOS a Android). V opačném případě použijte identifikátory URI přesměrování s vysokou mírou náhodnosti, abyste zabránili kolizím při volání zpět do aplikace. Pokud se vaše aplikace používá z izolovaného webového agenta, můžete použít https://login.microsoftonline.com/nativeclient. Pravidelně kontrolujte a ořízněte všechny nepoužívané nebo zbytečné identifikátory URI pro přesměrování. |
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Pokud je vaše aplikace zaregistrovaná v adresáři, minimalizujte a ručně Sledujte seznam vlastníků registrace aplikací. |
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Nepovolujte podporu pro [tok implicitního udělení OAuth2](v2-oauth2-implicit-grant-flow.md) , pokud je explicitně nepožadujete. [Tady](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)najdete informace o platném scénáři. |
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Nepoužívejte [tok přihlašovacích údajů k heslům vlastníka prostředků (ROPC)](v2-oauth-ropc.md), který přímo zpracovává hesla uživatelů. Tento tok vyžaduje vysoký stupeň důvěry a ohrožení uživatele a měl by se používat jenom v případě, že se nedají použít jiné, bezpečnější toky. |
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Chránit a spravovat přihlašovací údaje vaší aplikace Použijte [přihlašovací údaje certifikátu](active-directory-certificate-credentials.md), nikoli přihlašovací údaje hesla (tajné klíče klienta). Pokud je nutné použít přihlašovací údaje hesla, nenastavte ji ručně. Neukládejte přihlašovací údaje v kódu nebo konfiguraci a nikdy je nepovolíte, aby je lidé nepoužívali. Pokud je to možné, využijte [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) nebo [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) k ukládání a pravidelnému střídání přihlašovacích údajů. |
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Ujistěte se, že vaše aplikace požaduje nejnižší oprávnění. Zeptejte se jenom na oprávnění, která vaše aplikace naprosto potřebuje, a jenom když je potřebujete. Pochopení různých [typů oprávnění](v1-permissions-and-consent.md#types-of-permissions) V případě potřeby použijte pouze oprávnění aplikace. Pokud je to možné, použijte delegovaná oprávnění. Úplný seznam oprávnění Microsoft Graph najdete v tématu s [odkazem](https://docs.microsoft.com/graph/permissions-reference)na tato oprávnění. |
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Pokud zabezpečujete rozhraní API s využitím platformy Microsoft identity, pečlivě si promyslete oprávnění, která by měla zveřejnit. Zvažte, jaké jsou správné členitosti řešení a které oprávnění vyžadují souhlas správce. Před provedením jakýchkoli autorizačních rozhodnutí ověřte, zda jsou v příchozích tokenech očekávaná oprávnění. |

### <a name="implementation"></a>Implementace

|   |   |
|---|---|
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | K bezpečnému přihlášení uživatelů používejte řešení pro moderní ověřování (OAuth 2,0, [OpenID Connect](v2-protocols-oidc.md)). |
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Neimplementujte protokoly sami – použijte [ověřovací knihovny podporované Microsoftem](reference-v2-libraries.md) (MSAL, middleware serveru). Ujistěte se, že používáte nejnovější verzi knihovny ověřování, do které jste provedli integraci. |
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Pokud jsou data, která vaše aplikace vyžaduje, dostupná prostřednictvím [Microsoft Graph](https://developer.microsoft.com/graph), požádejte o oprávnění pro tato data pomocí koncového bodu Microsoft Graph místo jednotlivého rozhraní API. |

### <a name="end-user-experience"></a>Činnost koncového uživatele

|   |   |
|---|---|
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Seznamte [se s vyjádřením souhlasu](application-consent-experience.md) a nakonfigurujte části výzvy pro vyjádření souhlasu vaší aplikace, aby koncoví uživatelé a správci měli dostatek informací, aby zjistili, jestli důvěřují vaší aplikaci. |
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Minimalizujte počet pokusů, které uživatel musí zadat přihlašovací údaje při použití vaší aplikace, tak, že před interaktivními toky zkusí spustit tiché ověření (získání tichého tokenu). |
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Pro každé přihlášení nepoužívejte "Prompt = souhlasu". Pokud jste zjistili, že potřebujete požádat o souhlas s dalšími oprávněními (například pokud jste změnili požadovaná oprávnění aplikace), použijte příkaz Prompt = souhlas. |
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | V případě potřeby obohacení aplikace o uživatelská data. K tomu slouží [Microsoft Graph API](https://developer.microsoft.com/graph) . Nástroj [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) , který vám může pomáhat začít. |
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Zaregistrujte úplnou sadu oprávnění, která vaše aplikace vyžaduje, aby správci mohli udělit souhlas svým klientům snadno. Pomocí [přírůstkového souhlasu](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) v době spuštění pomůžete uživatelům pochopit, proč vaše aplikace požaduje oprávnění, která se mohou v případě potřeby při prvním spuštění týkat nebo Zaměňujte uživatelům. |
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementujte [čisté prostředí pro jednotné odhlašování](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Je to ochrana osobních údajů a požadavky na zabezpečení a přináší dobrý dojem uživatelské prostředí. |

### <a name="testing"></a>Testování

|   |   |
|---|---|
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Otestujte [zásady podmíněného přístupu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) , které mohou mít vliv na schopnost uživatelů používat vaši aplikaci. |
| ![Prvku](./media/active-directory-integration-checklist/checkbox-two.svg) | Otestujte svoji aplikaci se všemi možnými účty, které hodláte podporovat (například pracovní nebo školní účty, osobní účty Microsoft, podřízené účty a účty z svrchovaného účtu). |

## <a name="additional-resources"></a>Další zdroje

Prostudujte si podrobné informace týkající se v2.0:

* [Microsoft Identity Platform (přehled verze 2.0)](v2-overview.md)
* [Referenční informace o protokolech platformy Microsoft Identity Platform](active-directory-v2-protocols.md)
* [informace o přístupových tokenech](access-tokens.md)
* [informace o tokenech ID](id-tokens.md)
* [Referenční dokumentace knihoven ověřování](reference-v2-libraries.md)
* [Oprávnění a souhlas v platformě Microsoft Identity Platform](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
