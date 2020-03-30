---
title: Technický přehled a přehled funkcí – Azure Active Directory B2C
description: Podrobný úvod k funkcím a technologiím ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d3d6b33211f6f247d9f30c0f162b388085faabe6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80332535"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Technický přehled a přehled funkcí služby Azure Active Directory B2C

Tento článek poskytuje podrobnější úvod ke službě [o službě Azure Active Directory B2C.](overview.md) Zde jsou popsány primární prostředky, se kterými pracujete ve službě, její funkce a jak umožňují poskytovat plně vlastní prostředí identity pro vaše zákazníky ve vašich aplikacích.

## <a name="azure-ad-b2c-tenant"></a>Klient Azure AD B2C

Ve službě Azure Active Directory B2C (Azure AD B2C) *představuje klient* vaši organizaci a je adresář uživatelů. Každý tenant Azure AD B2C se odlišuje a je oddělený od jiných tenantů Azure AD B2C. Tenant Azure AD B2C se liší od klienta Služby Azure Active Directory, který už možná máte.

Primární prostředky, se kterými pracujete v tenantovi Azure AD B2C jsou:

* **Adresář** – *adresář* je místo, kde Azure AD B2C ukládá přihlašovací údaje uživatelů a data profilu, stejně jako registrace aplikací.
* **Registrace aplikací** – zaregistrujete své webové, mobilní a nativní aplikace pomocí Azure AD B2C, abyste povolili správu identit. Také všechna řešení API, které chcete chránit pomocí Azure AD B2C.
* **Toky uživatelů** a **vlastní zásady** – integrované (toky uživatelů) a plně přizpůsobitelné (vlastní zásady) identity prostředí pro vaše aplikace.
  * Toky *uživatelů* slouží k rychlé konfiguraci a povolení běžných úloh identity, jako je registrace, přihlášení a úpravy profilu.
  * Pomocí *vlastních zásad* můžete uživatelům povolit prostředí nejen pro běžné úlohy identity, ale také pro vytváření podpory složitých pracovních postupů identit, které jsou jedinečné pro vaši organizaci, zákazníky, zaměstnance, partnery a občany.
* **Zprostředkovatelé identit –** nastavení federace pro:
  * *Poskytovatelé sociální* identity, jako je Facebook, LinkedIn nebo Twitter, které chcete ve svých aplikacích podporovat.
  * *Externí* zprostředkovatelé identity, kteří podporují standardní protokoly identity, jako je OAuth 2.0, OpenID Connect a další.
  * *Místní* účty, které uživatelům umožňují zaregistrovat se a přihlásit pomocí uživatelského jména (nebo e-mailové adresy nebo jiného ID) a hesla.
* **Klíče** – Přidejte a spravujte šifrovací klíče pro podepisování a ověřování tokenů.

Tenant Azure AD B2C je první prostředek, který potřebujete vytvořit, abyste mohli začít s Azure AD B2C. Přečtěte si, jak v [kurzu: Vytvoření klienta Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="accounts-in-azure-ad-b2c"></a>Účty ve službě Azure AD B2C

Azure AD B2C definuje několik typů uživatelských účtů. Azure Active Directory, Azure Active Directory B2B a Azure Active Directory B2C sdílejí tyto typy účtů.

* **Pracovní účet** – uživatelé s pracovními účty mohou spravovat prostředky v tenantovi a s rolí správce mohou také spravovat klienty. Uživatelé s pracovními účty mohou vytvářet nové spotřebitelské účty, resetovat hesla, blokovat/odblokovat účty a nastavovat oprávnění nebo přiřazovat účet skupině zabezpečení.
* **Účet hosta** – externí uživatelé, které pozvete ke svému tenantovi jako hosty. Typický scénář pro pozvání uživatele typu Host do vašeho klienta Azure AD B2C je sdílení odpovědnosti za správu.
* **Spotřebitelský účet** – spotřebitelské účty jsou účty vytvořené v adresáři Azure AD B2C, když uživatelé dokončí cestu uživatele registrace v aplikaci, kterou jste zaregistrovali ve vašem tenantovi.

![Stránka správy uživatelů Azure AD B2C na webu Azure Portal](media/technical-overview/portal-01-users.png)<br/>*Obrázek: Adresář uživatelů v rámci klienta Azure AD B2C na webu Azure Portal*

### <a name="consumer-accounts"></a>Spotřebitelské účty

Pomocí *spotřebitelského* účtu se uživatelé můžou přihlásit k aplikacím, které jste zabezpečili pomocí Azure AD B2C. Uživatelé se spotřebitelskými účty však nemají přístup k prostředkům Azure, například na portál Azure.

Spotřebitelský účet může být přidružen k těmto typům identit:

* **Místní** identita s uživatelským jménem a heslem uloženým místně v adresáři Azure AD B2C. Často označujeme tyto identity jako "místní účty".
* **Sociální** nebo **podnikové** identity, kde identitu uživatele spravuje poskytovatel federované identity, jako je Facebook, Microsoft, ADFS nebo Salesforce.

Uživatel se spotřebitelským účtem se může přihlásit pomocí více identit, například uživatelského jména, e-mailu, ID zaměstnance, ID státní správy a dalších. Jeden účet může mít více identit, místních i sociálních.

![Identity spotřebitelských účtů](media/technical-overview/identities.png)<br/>*Obrázek: Jeden účet příjemce s více identitami v Azure AD B2C*

Azure AD B2C umožňuje spravovat běžné atributy profilů spotřebitelských účtů, jako je zobrazované jméno, příjmení, křestní jméno, město a další. Můžete také rozšířit schéma Azure AD pro ukládání dalších informací o vašich uživatelích. Například jejich země nebo bydliště, preferovaný jazyk a předvolby, jako je to, zda se chtějí přihlásit k odběru bulletinu nebo povolit vícefaktorové ověřování.

Další informace o typech uživatelských účtů v Azure AD B2C najdete v [části Přehled uživatelských účtů ve službě Azure Active Directory B2C](user-overview.md).

## <a name="external-identity-providers"></a>Zprostředkovatelé externích identit

Azure AD B2C můžete nakonfigurovat tak, aby se uživatelé mohli přihlásit k vaší aplikaci pomocí přihlašovacích údajů od externích poskytovatelů sociálních nebo podnikových identit (IdP). Azure AD B2C podporuje externí poskytovatele identit, jako je Facebook, účet Microsoft, Google, Twitter a jakýkoli poskytovatel identity, který podporuje protokoly OAuth 1.0, OAuth 2.0, OpenID Connect, SAML nebo WS-Federation.

![Zprostředkovatelé externích identit](media/technical-overview/external-idps.png)

Díky federaci externích poskytovatelů identit můžete svým zákazníkům nabídnout možnost přihlásit se pomocí svých stávajících účtů sociálních sítí nebo podniku, aniž byste museli vytvářet nový účet pouze pro vaši aplikaci.

Na přihlašovací nebo přihlašovací stránce Azure AD B2C představuje seznam externích poskytovatelů identity, které si uživatel může vybrat pro přihlášení. Jakmile vyberou jednoho z externích poskytovatelů identity, přejdou (přesměrováni) na web vybraného poskytovatele, aby dokončili proces přihlášení. Poté, co se uživatel úspěšně přihlásí, vrátí se zpět do Azure AD B2C pro ověření účtu ve vaší aplikaci.

![Příklad mobilního přihlášení se sociálním účtem (Facebook)](media/technical-overview/external-idp.png)

Informace o tom, jak přidat zprostředkovatele identit v Azure AD B2C, najdete [v tématu Kurz: Přidání zprostředkovatelů identit do vašich aplikací ve službě Azure Active Directory B2C](tutorial-add-identity-providers.md).

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Prostředí identity: toky uživatelů nebo vlastní zásady

Rozšiřitelné zásady rámce Azure AD B2C je jeho hlavní síla. Zásady popisují prostředí identity uživatelů, jako je registrace, přihlášení a úpravy profilu.

Ve službě Azure AD B2C existují dvě primární cesty, které můžete použít k poskytování těchto prostředí identity: toky uživatelů a vlastní zásady.

* **Toky uživatelů** jsou předdefinované, integrované, konfigurovatelné zásady, které poskytujeme, takže můžete během několika minut vytvořit možnosti registrace, přihlášení a úprav zásad.

* **Vlastní zásady** umožňují vytvářet vlastní cesty uživatelů pro složité scénáře prostředí identity.

Toky uživatelů i vlastní zásady jsou napájeny *rozhraním Identity Experience Framework*, modul orchestrace zásad Azure AD B2C.

### <a name="user-flow"></a>Tok uživatele

Abychom vám pomohli rychle nastavit nejběžnější úlohy identity, obsahuje portál Azure několik předdefinovaných a konfigurovatelných zásad *nazývaných toky uživatelů*.

Můžete nakonfigurovat nastavení toku uživatelů, jako jsou tyto řídit chování prostředí identity ve vašich aplikacích:

* Typy účtů používané pro přihlášení, jako jsou sociální účty, jako je Facebook, nebo místní účty, které používají e-mailovou adresu a heslo pro přihlášení
* Atributy, které mají být shromažďovány od spotřebitele, jako je jméno, PSČ nebo země trvalého pobytu
* Azure Multi-Factor Authentication (MFA)
* Přizpůsobení uživatelského rozhraní
* Sada deklarací v tokenu, který vaše aplikace obdrží po dokončení toku uživatele uživatelem
* Správa relací
* ... a další.

Nejběžnější scénáře identit pro většinu mobilních, webových a jednostránkových aplikací lze definovat a efektivně implementovat pomocí toků uživatelů. Doporučujeme použít integrované toky uživatelů, pokud nemáte složité scénáře cesty uživatele, které vyžadují plnou flexibilitu vlastních zásad.

Další informace o tocích uživatelů v [tocích uživatelů ve službě Azure Active Directory B2C](user-flow-overview.md).

### <a name="custom-policy"></a>Vlastní zásady

Vlastní zásady odemknout přístup k plné muzice modulu orchestrace rozhraní IEF (Identity Experience Framework). Pomocí vlastních zásad můžete pomocí protokolu IEF vytvořit téměř jakékoli ověřování, registraci uživatelů nebo prostředí pro úpravy profilu, které si dokážete představit.

Rozhraní identity experience framework umožňuje vytvářet cesty uživatele s libovolnou kombinací kroků. Například:

* Federate s jinými poskytovateli identit
* Výzvy pro vícefaktorové ověřování první a třetí strany (MFA)
* Shromáždit libovolný uživatelský vstup
* Integrace s externími systémy pomocí komunikace REST API

Každá taková cesta uživatele je definována zásadou a můžete vytvořit tolik nebo tolik zásad, kolik potřebujete k povolení nejlepšího uživatelského prostředí pro vaši organizaci.

![Diagram znázorňující příklad složité cesty uživatele povolené protokolem IEF](media/technical-overview/custom-policy.png)

Vlastní zásady jsou definovány několika soubory XML, které na sebe odkazují v hierarchickém řetězci. Elementy XML definují schéma deklarací identity, transformace deklarací identity, definice obsahu, zprostředkovatele deklarací, technické profily, kroky orchestrace cesty uživatele a další aspekty prostředí identity.

Výkonná flexibilita vlastních zásad je nejvhodnější, když potřebujete vytvořit složité scénáře identity. Vývojáři konfigurující vlastní zásady musí pečlivě definovat důvěryhodné vztahy tak, aby zahrnovaly koncové body metadat, definice výměny přesných deklarací identity a podle potřeby nakonfigurovaly tajné klíče, klíče a certifikáty podle potřeby každého poskytovatele identity.

Další informace o vlastních zásadách najdete v [seznamu Vlastních zásad ve službě Azure Active Directory B2C](custom-policy-overview.md).

## <a name="protocols-and-tokens"></a>Protokoly a tokeny

Azure AD B2C podporuje [protokoly OpenID Connect a OAuth 2.0](protocols-overview.md) pro cesty uživatelů. V implementaci OpenID Connect v Azure AD B2C zahajuje aplikace tuto cestu uživatele zasíláním žádostí o ověření do Azure AD B2C.

Výsledkem požadavku na Azure AD B2C je token zabezpečení, jako je například [token ID nebo přístupový token](tokens-overview.md). Tento token zabezpečení definuje identitu uživatele. Tokeny jsou přijímány z koncových bodů `/token` Azure `/authorize` AD B2C, jako je koncový bod nebo. S těmito tokeny můžete přistupovat k deklaracím, které lze použít k ověření identity a povolit přístup k zabezpečeným prostředkům.

Pro externí identity Azure AD B2C podporuje federaci s libovolným OAuth 1.0, OAuth 2.0, OpenID Connect, SAML a WS-Fed zprostředkovatelidentity.

![Diagram klientské aplikace založené na OIDC federace s idp založené na SAML](media/technical-overview/protocols.png)

Předchozí diagram ukazuje, jak Azure AD B2C může komunikovat pomocí různých protokolů v rámci stejného toku ověřování:

1. Aplikace předávající strany iniciuje žádost o autorizaci pro Azure AD B2C pomocí OpenID Connect.
1. Když se uživatel aplikace rozhodne přihlásit pomocí externího zprostředkovatele identity, který používá protokol SAML, Azure AD B2C vyvolá protokol SAML ke komunikaci s tímto poskytovatelem identity.
1. Poté, co uživatel dokončí operaci přihlášení s externím poskytovatelem identity, Azure AD B2C pak vrátí token do aplikace předávající strany pomocí OpenID Connect.

## <a name="application-integration"></a>Integrace aplikací

Když se uživatel chce přihlásit k vaší aplikaci, ať už se jedná o webovou, mobilní, desktopovou nebo jednostránkovou aplikaci (SPA), aplikace zahájí žádost o autorizaci koncového bodu poskytovaného tokem uživatele nebo vlastními zásadami. Tok uživatele nebo vlastní zásady definuje a řídí uživatelské prostředí. Po dokončení toku uživatele, například *registrace nebo přihlášení* toku, Azure AD B2C generuje token, pak přesměruje uživatele zpět do vaší aplikace.

![Mobilní aplikace se šipkami zobrazujícími tok mezi přihlašovací stránkou Azure AD B2C](media/technical-overview/app-integration.png)

Více aplikací můžete použít stejný tok uživatele nebo vlastní zásady. Jedna aplikace můžete použít více toků uživatelů nebo vlastní zásady.

Například pro přihlášení k aplikaci, aplikace používá *zaregistrovat nebo přihlásit* tok uživatele. Poté, co se uživatel přihlásí, může chtít upravit svůj profil, takže aplikace zahájí další žádost o autorizaci, tentokrát pomocí toku uživatele *upravit profil.*

## <a name="seamless-user-experiences"></a>Bezproblémové uživatelské prostředí

Ve službě Azure AD B2C můžete vytvářet prostředí identit y vašich uživatelů tak, aby se stránky, které jsou zobrazeny, plynule propojou se vzhledem a chováním vaší značky. Získáte téměř plnou kontrolu nad obsahem HTML a CSS prezentovaným uživatelům, když pokračují v cestách identity vaší aplikace. Díky této flexibilitě můžete udržovat značkovou a vizuální konzistenci mezi vaší aplikací a Azure AD B2C.

![Snímky obrazovky přihlašovací stránky přizpůsobené značce](media/technical-overview/seamless-ux.png)

Informace o přizpůsobení uživatelského rozhraní naleznete [v tématu O přizpůsobení uživatelského rozhraní ve službě Azure Active Directory B2C](customize-ui-overview.md).

## <a name="localization"></a>Lokalizace

Přizpůsobení jazyka v Azure AD B2C umožňuje přizpůsobit různé jazyky tak, aby vyhovovaly potřebám vašich zákazníků. Společnost Microsoft poskytuje překlady pro 36 jazyků, ale můžete také poskytnout vlastní překlady pro libovolný jazyk. I když je vaše prostředí poskytováno pouze pro jeden jazyk, můžete přizpůsobit libovolný text na stránkách.

![Tři přihlašovací stránky pro přihlášení s textem ui v různých jazycích](media/technical-overview/localization.png)

Podívejte se, jak funguje lokalizace v [přizpůsobení jazyka ve službě Azure Active Directory B2C](user-flow-language-customization.md).

## <a name="add-your-own-business-logic"></a>Přidání vlastní obchodní logiky

Pokud se rozhodnete používat vlastní zásady, můžete integrovat s RESTful API v cestě uživatele přidat vlastní obchodní logiku na cestu. Azure AD B2C může například vyměňovat data se službou RESTful na:

* Zobrazte vlastní uživatelsky přívětivé chybové zprávy.
* Ověřte vstup uživatele, abyste zabránili uchování poškozených dat v adresáři uživatele. Můžete například upravit data zadaná uživatelem, například velká písmena jejich křestního jména, pokud je zadali velkými písmeny.
* Obohaťte uživatelská data další integrací s podnikovou obchodní aplikací.
* Pomocí restful volání můžete odesílat nabízená oznámení, aktualizovat podnikové databáze, spouštět proces migrace uživatelů, spravovat oprávnění, databáze auditování a další.

Věrnostní programy jsou dalším scénářem povoleným podporou Azure AD B2C pro volání rozhraní REST API. Vaše služba RESTful může například obdržet e-mailovou adresu uživatele, zadat dotaz na zákaznickou databázi a potom vrátit věrnostní číslo uživatele do služby Azure AD B2C. Vrácená data mohou být uložena v účtu adresáře uživatele v Azure AD B2C, pak dále vyhodnocovat v následujících krocích v zásadách nebo být zahrnuty do přístupového tokenu.

![Podniková integrace v mobilní aplikaci](media/technical-overview/lob-integration.png)

Můžete přidat volání rozhraní REST API v libovolném kroku v cestě uživatele definované vlastní zásady. Můžete například volat rozhraní REST API:

* Během přihlášení těsně před Azure AD B2C ověří přihlašovací údaje
* Ihned po přihlášení
* Před Azure AD B2C vytvoří nový účet v adresáři
* Po Azure AD B2C vytvoří nový účet v adresáři
* Než Azure AD B2C vydá přístupový token

Postup použití vlastních zásad pro integraci rozhraní API RESTful v Azure AD B2C najdete [v tématu Integrace výměny deklarací rozhraní REST API ve vlastních zásadách Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="protect-customer-identities"></a>Ochrana identit zákazníků

Azure AD B2C splňuje zabezpečení, ochranu osobních údajů a další závazky popsané v [Centru zabezpečení Microsoft Azure](https://www.microsoft.com/trustcenter/cloudservices/azure).

Relace jsou modelovány jako šifrovaná data, s dešifrovací klíč známý jenom služby Token zabezpečení Azure AD B2C. Používá se silný šifrovací algoritmus AES-192. Všechny komunikační cesty jsou chráněny tls pro zachování důvěrnosti a integrity. Naše služba tokenů zabezpečení používá certifikát rozšířeného ověření (EV) pro TLS. Služba Token y security token obecně zmírňuje útoky skriptování mezi pracemi (XSS) tím, že nevykresluje nedůvěryhodný vstup.

![Diagram zabezpečených dat při přenosu a v klidovém stavu](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Přístup k uživatelským datům

Tenanti Azure AD B2C sdílejí mnoho charakteristik s podnikovými tenanty Azure Active Directory používanými pro zaměstnance a partnery. Sdílené aspekty zahrnují mechanismy pro zobrazení rolí správy, přiřazení rolí a auditování aktivit.

Role můžete přiřadit k řízení, kdo může provádět určité akce správy v Azure AD B2C, včetně:

* Vytvoření a správa všech aspektů toků uživatelů
* Vytvoření a správa schématu atributů, které je k dispozici pro všechny toky uživatelů
* Konfigurace zprostředkovatelů identit pro použití v přímé federaci
* Vytvoření a správa rámcových zásad důvěryhodnosti v rámci prostředí identit (vlastní zásady)
* Správa tajných kódů pro federaci a šifrování v rozhraní Identity Experience Framework (vlastní zásady)

Další informace o rolích Azure AD, včetně podpory role správy Azure AD B2C, najdete [v tématu oprávnění role správce ve službě Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

Azure AD B2C vícefaktorové ověřování (MFA) pomáhá chránit přístup k datům a aplikacím při zachování jednoduchosti pro vaše uživatele. Poskytuje další zabezpečení tím, že vyžaduje druhou formu ověřování a poskytuje silné ověřování tím, že nabízí řadu snadno použitelných metod ověřování. Vaši uživatelé mohou nebo nemusí být napadena pro vícefaktorové řízení na základě rozhodnutí o konfiguraci, které můžete provést jako správce.

Podívejte se, jak povolit vícefaktorové ověřování v tocích uživatelů v [tématu Povolit vícefaktorové ověřování ve službě Azure Active Directory B2C](custom-policy-multi-factor-authentication.md).

### <a name="smart-account-lockout"></a>Uzamčení inteligentního účtu

Chcete-li zabránit pokusům o hádání hrubou silou hesla, azure ad b2c používá propracovanou strategii pro uzamčení účtů na základě IP požadavku, zadaných hesel a několika dalších faktorů. Doba uzamčení se automaticky zvyšuje na základě rizika a počtu pokusů.

![Inteligentní uzamčení účtu](media/technical-overview/smart-lockout1.png)

Další informace o správě nastavení ochrany heslem najdete [v tématu Správa hrozeb pro prostředky a data ve službě Azure Active Directory B2C](threat-management.md).

### <a name="password-complexity"></a>Složitost hesla

Během registrace nebo resetování hesla musí uživatelé zadat heslo, které splňuje pravidla složitosti. Ve výchozím nastavení Azure AD B2C vynucuje silné zásady hesel. Azure AD B2C také poskytuje možnosti konfigurace pro určení požadavků na složitost hesel, která zákazníci používají.

Požadavky na složitost hesla můžete konfigurovat jak v [tocích uživatelů,](user-flow-password-complexity.md) tak v [vlastních zásadách](custom-policy-password-complexity.md).

## <a name="auditing-and-logs"></a>Auditování a protokoly

Azure AD B2C vydává protokoly auditu obsahující informace o aktivitách o svých prostředcích, vydaných tokenech a přístupu správce. Tyto protokoly auditu můžete použít k pochopení aktivity platformy a diagnostikovat problémy. Položky protokolu auditu jsou k dispozici brzy po aktivitě, která vygenerovala událost.

V protokolu auditu, který je k dispozici pro vašeho klienta Azure AD B2C nebo pro konkrétního uživatele, můžete najít informace, včetně:

* Aktivity týkající se autorizace uživatele pro přístup k prostředkům B2C (například správce, který přistupuje k seznamu zásad B2C)
* Aktivity související s atributy adresáře načtenými při přihlášení správce pomocí portálu Azure
* Vytváření, čtení, aktualizace a odstraňování operací (CRUD) v aplikacích B2C
* Operace CRUD na klíčích uložených v kontejneru klíčů B2C
* Operace CRUD s prostředky B2C (například zásady a poskytovatelé identit)
* Ověření pověření uživatele a vystavování tokenů

![Protokol auditování jednotlivých uživatelů zobrazený na webu Azure Portal](media/technical-overview/audit-log.png)

Další podrobnosti o protokolech auditu naleznete [v tématu Přístup k protokolům auditu Azure AD B2C](view-audit-logs.md).

### <a name="usage-insights"></a>Přehledy využití

Azure AD B2C umožňuje zjistit, kdy se lidé zaregistrují nebo přihlásí do vaší webové aplikace, kde se vaši uživatelé nacházejí a jaké prohlížeče a operační systémy používají. Integrací Přehledů aplikací Azure do Azure AD B2C pomocí vlastních zásad můžete získat přehled o tom, jak se lidé zaregistrují, přihlásí, resetují své heslo nebo upravují svůj profil. S těmito znalostmi můžete provádět rozhodnutí založená na datech pro nadcházející vývojové cykly.

Další informace o analýzách využití v [aplikaci Sledování chování uživatelů ve službě Azure Active Directory B2C najdete pomocí přehledů aplikací](analytics-with-application-insights.md).

## <a name="next-steps"></a>Další kroky

Teď, když máte hlubší přehled o funkcích a technických aspektech služby Azure Active Directory B2C, můžete začít se službou vytvořením klienta B2C:

> [!div class="nextstepaction"]
> [Kurz: Vytvoření >klienta Služby Azure Active Directory B2C](tutorial-create-tenant.md)