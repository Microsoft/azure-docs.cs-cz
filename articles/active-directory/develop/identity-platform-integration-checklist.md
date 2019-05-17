---
title: Integrace s platformou identity Microsoft | Azure
description: Další informace o osvědčených postupech a běžné opominutí při integraci s platformou identity Microsoft (verze 2.0).
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
ms.custom: aaddev
ms.openlocfilehash: 0d44e5d9f0d1ed893a16c318e3cfa0ee1bfc123b
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823349"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Kontrolní seznam integrace Microsoft identity platform

Integrace kontrolního seznamu Microsoft identity platform je určena a provede vás s integrací vysoké kvality a zabezpečení. Zvýrazní osvědčené postupy a běžné opominutí při integraci s platformou identity Microsoft proto zkontrolujte v seznamu v pravidelných intervalech, abyste měli jistotu, že zachování kvality a zabezpečení vaší aplikace integraci s platformou identity. Kontrolní seznam není určena pro vaši celou aplikaci. Obsah kontrolního seznamu se mohou změnit při vylepšování do platformy.

Pokud jste právě začínáte, podívejte se [dokumentaci](index.yml) Další informace o základní informace o ověřování, scénáře aplikací v Microsoft identity platform a další.

## <a name="testing-your-integration"></a>Testování integraci vašich

Pomocí následujícího kontrolního seznamu ujistěte se, že aplikace účinně integrován [platforma identit Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).

### <a name="basics"></a>Základy

|   |   |
|---|---|
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Přečtěte si [zásady platformy Microsoftu](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Ujistěte se, že aplikace splňuje podmínky uvedené jako jsou navržená k ochraně uživatelů a platformu. |

### <a name="ownership"></a>Vlastnictví

|   |   |
|---|---|
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Ujistěte se, že je aktuální informace přidružené k účtu, kterou jste použili k registraci a správu aplikací. |

### <a name="branding"></a>Branding

|   |   |
|---|---|
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Dodržovat [pokyny pro aplikace pro Branding](howto-add-branding-in-azure-ad-apps.md). |
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Zadejte smysluplný název a logo pro vaši aplikaci. Tyto informace se zobrazí na výzva k povolení spuštění vaší aplikace. Ujistěte se, že vaše jméno a logo se zástupcem společnosti/produkt tak, aby uživatelé mohli kvalifikovaně rozhodnout. Ujistěte se, že nejsou porušení žádné ochranné známky. |

### <a name="privacy"></a>Soukromí

|   |   |
|---|---|
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Poskytuje odkazy na podmínky služby a o ochraně osobních údajů vaší aplikace. |

### <a name="security"></a>Zabezpečení

|   |   |
|---|---|
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Udržovat vlastnictví všech vašich identifikátory URI pro přesměrování a aktualizovat záznamy DNS pro ně. Nepoužívejte zástupné znaky (*) ve vaší identifikátorů URI. Pro web apps Ujistěte se, že všechny identifikátory URI jsou zabezpečená a šifrovaná (například pomocí protokolu https schémat). Pro veřejné klienty používejte identifikátory URI pro přesměrování specifické pro platformu, pokud je k dispozici (hlavně pro iOS a Android) připojení. V opačném případě použijte identifikátory URI pro přesměrování s vysoké množství náhodnost, abyste zabránili kolizím při volání zpátky do vaší aplikace. Pokud vaše aplikace se používá v rámci izolovaného webového agenta, můžete použít https://login.microsoftonline.com/nativeclient. Zkontrolujte a uvolnění paměti všechny nepoužívané nebo nepotřebné identifikátory URI pro přesměrování v pravidelných intervalech. |
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Pokud vaše aplikace je registrován v adresáři, minimalizace a ručně monitorovat seznam vlastníci registrace aplikací. |
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Nemáte povolení podpory pro [OAuth2 implicitní tok poskytování](v2-oauth2-implicit-grant-flow.md) Pokud to není explicitně vyžadováno. Další informace o platný scénář [tady](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Nepoužívejte [tok přihlašovacích údajů heslo vlastníka prostředku (ROPC)](v2-oauth-ropc.md), který přímo zpracovává hesla uživatelů. Tento tok vyžaduje vysoký stupeň důvěryhodnosti a uživatel vystavení, by měla sloužit pouze v případě jiných, bezpečnější, toky se nedají použít. |
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Chránit a spravovat svoje přihlašovací údaje aplikace. Použití [certifikát přihlašovacích údajů](active-directory-certificate-credentials.md), ne heslo pověření (tajné údaje klienta). Pokud je nutné použít heslo pověření, nenastavujte ho ručně. Nepoužívejte uložení přihlašovacích údajů v kódu nebo konfigurace a nikdy mohly být zpracovány lidí. Pokud je to možné, používejte [spravovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) nebo [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) k ukládání a pravidelně otočit svoje přihlašovací údaje. |
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Zajistěte, aby že vaše aplikace požaduje oprávnění nejnižší oprávnění. Pouze požádat o oprávnění, která vaše aplikace potřebuje naprosto a pouze tehdy, když je potřebujete. Vysvětlení různých [typy oprávnění](v1-permissions-and-consent.md#types-of-permissions). Používat jenom oprávnění aplikace v případě potřeby; kde je to možné, použijte delegovaná oprávnění. Úplný seznam oprávnění Microsoft Graphu, najdete v tomto [referenční dokumentace k oprávněním](https://docs.microsoft.com/graph/permissions-reference). |
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Pokud řešíte zabezpečení rozhraní API s použitím platforma identit Microsoft, pečlivě promyslet oprávnění, která by měly vystavit. Zvažte, co je správnou úroveň podrobností pro vaše řešení a která oprávnění vyžadují souhlas správce. Zkontrolujte oprávnění očekávaná v příchozích tokenech před provedením jakékoli autorizačních rozhodnutích. |

### <a name="implementation"></a>Implementace

|   |   |
|---|---|
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Používání řešení pro moderní ověřování (OAuth 2.0, [OpenID Connect](v2-protocols-oidc.md)) bezpečně přihlásit uživatele. |
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Nemusíte tyto protokoly implementují sami – můžete ji [knihoven ověřování Microsoftu nepodporuje](reference-v2-libraries.md) (MSAL middlewaru serveru). Ujistěte se, že používáte nejnovější verzi knihovny pro ověřování, které jste integrovali se službou. |
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Pokud je k dispozici prostřednictvím dat vaše aplikace vyžaduje [Microsoft Graphu](https://developer.microsoft.com/graph), požádat o oprávnění pro tato data pomocí koncového bodu Microsoft Graph místo jednotlivých rozhraní API. |

### <a name="end-user-experience"></a>Činnost koncového uživatele

|   |   |
|---|---|
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | [Principy prostředí pro vyjádření souhlasu](application-consent-experience.md) a konfigurace, které byly výzva k povolení spuštění vaší aplikace tak, aby koncoví uživatelé a správci mají dostatek informací k určení, pokud důvěřují vaší aplikace. |
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Minimalizovat počet, kolikrát uživatel potřebuje k zadání přihlašovacích údajů při používání aplikace pokusem o bezobslužném (tichém získání tokenu) ověřování před interaktivní toky. |
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Nepoužívejte "řádku = souhlasu" pro každé přihlášení. Pouze pomocí řádku = souhlasu, pokud zjistíte, že budete muset požádat o souhlas pro další oprávnění (například, pokud jste změnili vaší aplikace požadovaná oprávnění). |
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Kde je to možné, rozšířit vaše aplikace s uživatelskými daty. Použití [Microsoft Graph API](https://developer.microsoft.com/graph) je snadný způsob, jak to provést. [Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer) nástroj, který může pomoci vám začít. |
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Zaregistrujte úplnou sadu oprávnění, která vaše aplikace vyžaduje, správci můžou snadno udělit souhlas pro svého tenanta. Použití [přírůstkové souhlasu](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) v době běhu k poskytování pomoci uživatelům pochopit, proč vaše aplikace požaduje oprávnění, která může problém nebo zmást uživatele při požadavku na první spuštění. |
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementace [vyčistit prostředí pro jednotné odhlašování](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Je ochranu osobních údajů a požadavek na zabezpečení a umožňuje bezproblémový. |

### <a name="testing"></a>Testování

|   |   |
|---|---|
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Test pro [zásady podmíněného přístupu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) , který může mít vliv na schopnost vaši uživatelé používají vaše aplikace. |
| ![Zaškrtávací políčko](./media/active-directory-integration-checklist/checkbox-two.svg) | Otestujte aplikaci se všemi možné účty, které plánujete podporu (pro příklad, pracovní nebo školní účty, osobní účty Microsoft, dětské účty a suverénních účty). |

## <a name="additional-resources"></a>Další materiály

Prostudujte si podrobné informace týkající se v2.0:

* [Platforma identit Microsoft (Přehled v2.0)](v2-overview.md)
* [Referenční informace k Microsoft identity platform protokoly](active-directory-v2-protocols.md)
* [informace o přístupových tokenech](access-tokens.md)
* [informace o tokenech ID](id-tokens.md)
* [Referenční dokumentace knihoven ověřování](reference-v2-libraries.md)
* [Oprávnění a vyjádření souhlasu v platforma identit Microsoft](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
