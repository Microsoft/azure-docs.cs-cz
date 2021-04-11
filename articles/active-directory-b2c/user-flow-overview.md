---
title: Toky uživatelů a vlastní zásady v Azure Active Directory B2C | Microsoft Docs
titleSuffix: Azure AD B2C
description: Přečtěte si další informace o předdefinovaných uživatelských tocích a o rozhraních .NET Framework pro rozšiřitelné zásady Azure Active Directory B2C pro vlastní zásady.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2e4dbc5178bec3a5b1f0931267465879f604f36f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107225993"
---
# <a name="user-flows-and-custom-policies-overview"></a>Přehled uživatelských toků a vlastních zásad

V Azure AD B2C můžete definovat obchodní logiku, kterou uživatelé sledují, aby získali přístup k vaší aplikaci. Můžete například určit posloupnost kroků, které uživatelé postupují při přihlašování, registraci, úpravě profilu nebo resetování hesla. Po dokončení sekvence uživatel získá token a získá přístup k vaší aplikaci. 

V Azure AD B2C existují dva způsoby, jak zajistit uživatelské prostředí identity:

* **Toky uživatelů** jsou předdefinované, předdefinované, konfigurovatelné zásady, které poskytujeme, abychom mohli vytvořit prostředí pro registraci, přihlašování a úpravu zásad během několika minut.

* **Vlastní zásady** umožňují vytvářet vlastní cesty uživatelů pro scénáře komplexního prostředí identity.

Na následujícím snímku obrazovky vidíte uživatelské rozhraní nastavení toku uživatele, srovnání s vlastními konfiguračními soubory zásad.

![Snímek obrazovky zobrazuje uživatelské rozhraní pro nastavení toku uživatele, a to oproti konfiguračním souborům vlastních zásad.](media/user-flow-overview/user-flow-vs-custom-policy.png)

Tento článek poskytuje stručný přehled uživatelských toků a vlastních zásad a pomůže vám určit, která metoda bude pro vaše obchodní potřeby fungovat nejlépe.

## <a name="user-flows"></a>Toky uživatele

K nastavení nejběžnějších úloh identity Azure Portal obsahuje několik předdefinovaných a konfigurovatelných zásad nazývaných *uživatelské toky*.

Můžete nakonfigurovat nastavení toku uživatele tak, aby bylo možné řídit chování identity v aplikacích:

* Typy účtů používané pro přihlášení, jako jsou například účty na Facebooku nebo místní účty, které používají e-mailovou adresu a heslo pro přihlášení
* Atributy, které mají být shromažďovány od příjemce, jako je například křestní jméno, PSČ nebo země/oblast, kde sídlí
* Multi-Factor Authentication Azure AD (MFA)
* Přizpůsobení uživatelského rozhraní
* Sada deklarací identity v tokenu, který vaše aplikace obdrží poté, co uživatel dokončí tok uživatele
* Správa relací
* ... a další

Většinu běžných scénářů identit pro aplikace je možné definovat a implementovat efektivně pomocí uživatelských toků. Doporučujeme, abyste používali předdefinované toky uživatelů, pokud nemáte složité scénáře pro práci s uživateli, které vyžadují plnou flexibilitu vlastních zásad.

## <a name="custom-policies"></a>Vlastní zásady

Vlastní zásady jsou konfigurační soubory, které definují chování Azure AD B2C uživatelského prostředí klienta. I když jsou toky uživatelů předdefinované na portálu Azure AD B2C pro nejběžnější úlohy identity, můžou vlastní zásady plně upravit vývojář identity, aby dokončili mnoho různých úloh.

Vlastní zásady jsou plně konfigurovatelné a řízené zásadami. Orchestruje vztah důvěryhodnosti mezi entitami ve standardních protokolech. Například OpenID Connect, OAuth, SAML a několik nestandardních, například REST API se výměnou deklarací ze systému na systém. Rozhraní vytváří uživatelsky přívětivé prostředí s bílým označením.

Vlastní zásady vám umožňují vytvářet cesty uživatelů pomocí libovolné kombinace kroků. Například:

* Federovat s jinými zprostředkovateli identity
* První a další výzvy k Multi-Factor Authentication (MFA) třetích stran
* Shromažďování libovolných vstupů uživatele
* Integrace s externími systémy pomocí REST API komunikace

Každou cestu uživatele definuje zásada. Můžete vytvořit tolik zásad, kolik jich potřebujete pro zajištění nejlepšího uživatelského prostředí pro vaši organizaci.

![Diagram znázorňující příklad složitá cesta uživatele povolená IEF](media/user-flow-overview/custom-policy-diagram.png)

Vlastní zásady jsou definovány několika soubory XML, které na sebe navzájem odkazují v hierarchickém řetězu. Prvky XML definují schéma deklarací identity, transformace deklarací identity, definice obsahu, zprostředkovatele deklarací identity, technické profily, kroky orchestrace cest uživatelů a další aspekty prostředí identity.

Výkonná flexibilita vlastních zásad je nejvhodnější pro případy, kdy potřebujete vytvořit komplexní scénáře identity. Vývojáři, kteří konfigurují vlastní zásady, musí podrobná nastavení důvěryhodných vztahů za účelem zahrnutí koncových bodů metadat, přesných definic deklarací identity a konfigurací tajných kódů, klíčů a certifikátů podle potřeby u každého poskytovatele identity.

Přečtěte si další informace o vlastních zásadách ve [vlastních zásadách v Azure Active Directory B2C](custom-policy-overview.md).

## <a name="comparing-user-flows-and-custom-policies"></a>Porovnání toků uživatelů a vlastních zásad

Následující tabulka obsahuje podrobné porovnání scénářů, které můžete s Azure AD B2Cmi uživatelskými toky a vlastními zásadami.

| Kontext | Toky uživatele | Vlastní zásady |
|-|-------------------|-----------------|
| Cíloví uživatelé | Všichni vývojáři aplikací s odbornými znalostmi identity nebo bez nich. | Specialisté na identity, systémy integrátorů, konzultantů a interních identit. Jsou pohodlné díky tokům OpenID Connect a pochopení zprostředkovatelů identit a ověřování založeného na deklaracích identity. |
| Metoda konfigurace | Azure Portal s uživatelsky přívětivým uživatelským rozhraním (UI). | Přímo upravování souborů XML a následné nahrávání do Azure Portal. |
| Přizpůsobení uživatelského rozhraní | [Úplné přizpůsobení uživatelského rozhraní](customize-ui-with-html.md) , včetně HTML, CSS a [JavaScript](javascript-and-page-layout.md).<br><br>[Vícejazyčná podpora](language-customization.md) s vlastními řetězci. | Stejné |
| Přizpůsobení atributu | Standardní a vlastní atributy. | Stejné |
| Správa tokenů a relací | [Přizpůsobení tokenů](configure-tokens.md) a [chování relací](session-behavior.md). | Stejné |
| Zprostředkovatelé identit | [Předdefinovaný místní](identity-provider-local.md) nebo [sociální poskytovatel](add-identity-provider.md), jako je například federace s Azure Active Directory klienty. | OIDC založené na standardech, OAUTH a SAML.  Ověřování je také možné pomocí integrace s rozhraními REST API. |
| Úkoly identity | [Zaregistrujte se nebo přihlaste](add-sign-up-and-sign-in-policy.md) pomocí místních nebo mnoha sociálních účtů.<br><br>[Samoobslužné resetování hesla](add-password-reset-policy.md).<br><br>[Úprava profilu](add-profile-editing-policy.md)<br><br>Multi-Factor Authentication.<br><br>Toky přístupového tokenu. | Dokončete stejné úlohy jako toky uživatelů pomocí vlastních zprostředkovatelů identity nebo použijte vlastní obory.<br><br>Zřídit uživatelský účet v jiném systému v době registrace.<br><br>Poslat uvítací e-mail pomocí vlastního poskytovatele e-mailové služby.<br><br>Použijte úložiště uživatele mimo Azure AD B2C.<br><br>Pomocí rozhraní API ověřte informace poskytnuté uživatelem s důvěryhodným systémem. |

## <a name="application-integration"></a>Integrace aplikací

Můžete vytvořit mnoho uživatelských toků nebo vlastní zásady různých typů ve vašem tenantovi a podle potřeby je používat ve svých aplikacích. V rámci aplikací lze znovu použít toky uživatelů a vlastní zásady. Tato flexibilita umožňuje definovat a upravovat prostředí identity s minimálními nebo žádnými změnami kódu. 

Když se uživatel chce přihlašovat k aplikaci, aplikace zahájí žádost o autorizaci koncovému bodu uživatele nebo vlastního koncového bodu zadaného zásadami. Tok uživatele nebo vlastní zásady definují a řídí uživatelské prostředí. Po dokončení toku uživatele Azure AD B2C vygeneruje token a pak přesměruje uživatele zpět do vaší aplikace.

![Mobilní aplikace se šipkami ukazující tok mezi Azure AD B2C přihlašovací stránkou](media/user-flow-overview/app-integration.png)

Stejný tok uživatele nebo vlastní zásady můžou používat víc aplikací. Jedna aplikace může používat více uživatelských toků nebo vlastních zásad.

Například pro přihlášení k aplikaci používá aplikace tok uživatele *registrace nebo přihlášení* . Až se uživatel přihlásí, může chtít upravit jeho profil. Chcete-li upravit profil, aplikace zahájí jinou žádost o autorizaci, tentokrát pomocí *profilu upravit* tok uživatele.

Vaše aplikace aktivuje tok uživatele pomocí standardní žádosti o ověření protokolu HTTP, která obsahuje název toku uživatele nebo vlastní zásady. Přizpůsobený [token](tokens-overview.md) se přijímá jako odpověď.


## <a name="next-steps"></a>Další kroky

- Pokud chcete vytvořit toky doporučeného uživatele, postupujte podle pokynů v tématu [kurz: vytvoření toku uživatele](tutorial-create-user-flows.md).
- Přečtěte si o [verzích toku uživatele v Azure AD B2C](user-flow-versions.md).
- Přečtěte si další informace o [Azure AD B2C vlastních zásadách](custom-policy-overview.md).
