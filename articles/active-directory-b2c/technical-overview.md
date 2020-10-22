---
title: Technický přehled a funkce – Azure Active Directory B2C
description: Podrobný Úvod k funkcím a technologiím v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80ae79f73a55d5feb1aede7ccc7d3b2b64682a24
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92364078"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Technický přehled funkcí Azure Active Directory B2C

Tento článek vás [seznámí s informacemi o Azure Active Directory B2C](overview.md), který poskytuje podrobnější Úvod k této službě. Zde jsou popsány primární prostředky, se kterými ve službě pracujete, její funkce a způsob, jakým vám umožňují poskytovat zákazníkům ve svých aplikacích plně vlastní prostředí identity.

## <a name="azure-ad-b2c-tenant"></a>Tenant Azure AD B2C

V Azure Active Directory B2C (Azure AD B2C) *tenant* představuje vaši organizaci a je adresářem uživatelů. Každý tenant Azure AD B2C se odlišuje a je oddělený od jiných tenantů Azure AD B2C. Tenant Azure AD B2C je jiný než tenant Azure Active Directory, který už možná máte.

K primárním prostředkům, se kterými pracujete ve Azure AD B2C tenantovi, patří:

* **Adresář** – *adresář* je místo, kde Azure AD B2C ukládá přihlašovací údaje a data profilu vašich uživatelů a také registrace vašich aplikací.
* **Registrace aplikací** – zaregistrujete své webové, mobilní a nativní aplikace pomocí Azure AD B2C, abyste mohli povolit správu identit. Také všechna rozhraní API, která chcete chránit pomocí Azure AD B2C.
* **Uživatelské toky** a **vlastní zásady** – integrované (uživatelské toky) a plně přizpůsobitelné (vlastní zásady) prostředí identit pro vaše aplikace.
  * Používejte *toky uživatelů* pro rychlou konfiguraci a povolte běžné úkoly identity, jako je registrace, přihlášení a úpravy profilu.
  * Pomocí *vlastních zásad* můžete povolit, aby uživatel nepoužíval jenom běžné úkoly identity, ale také pro vytváření podpory složitých pracovních postupů identit, které jsou jedinečné pro vaši organizaci, zákazníky, zaměstnance, partnery a občany.
* **Zprostředkovatelé identity** – nastavení federace pro:
  * Zprostředkovatelé *sociálních* identit, jako je Facebook, LinkedIn nebo Twitter, které chcete podporovat ve svých aplikacích.
  * *Externí* zprostředkovatelé identity, kteří podporují standardní protokoly identit, jako je OAuth 2,0, OpenID Connect a další.
  * *Místní* účty, které uživatelům umožňují registraci a přihlášení pomocí uživatelského jména (nebo e-mailové adresy nebo jiného ID) a hesla.
* **Klíče** – přidejte a spravujte šifrovací klíče pro podepisování a ověřování tokenů, tajných kódů klienta, certifikátů a hesel.

Azure AD B2C tenant je prvním prostředkem, který je potřeba vytvořit, abyste mohli začít s Azure AD B2C. Informace o tom, jak v [kurzu: vytvoření tenanta Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="accounts-in-azure-ad-b2c"></a>Účty v Azure AD B2C

Azure AD B2C definuje několik typů uživatelských účtů. Tyto typy účtů Azure Active Directory, Azure Active Directory B2B a Azure Active Directory B2C sdílet.

* **Pracovní účet** – uživatelé s pracovními účty můžou spravovat prostředky v tenantovi a s rolí správce může taky spravovat klienty. Uživatelé s pracovními účty můžou vytvářet nové uživatelské účty, obnovovat hesla, účty blokování a odblokování a nastavovat oprávnění nebo přiřazovat účet ke skupině zabezpečení.
* **Účet Guest** – externí uživatelé, které zvete do svého tenanta, jako hosty. Typický scénář pro pozvání uživatele typu Host ke klientovi Azure AD B2C je sdílet odpovědnosti správy.
* **Účet spotřebitele** – účty spotřebitelů jsou účty vytvořené v adresáři Azure AD B2C, když uživatelé dokončí cestu k registraci uživatele v aplikaci, kterou jste zaregistrovali ve vašem tenantovi.

![Stránka správy uživatelů Azure AD B2C v Azure Portal](media/technical-overview/portal-01-users.png)<br/>*Obrázek: adresář uživatele v rámci klienta Azure AD B2C v Azure Portal*

### <a name="consumer-accounts"></a>Uživatelské účty

Pomocí účtu *příjemce* se uživatelé můžou přihlásit k aplikacím, které jste zabezpečeni pomocí Azure AD B2C. Uživatelé s uživatelskými účty ale nemůžou mít přístup k prostředkům Azure, například k Azure Portal.

K těmto typům identit se dá přidružit účet příjemce:

* **Místní** identita s uživatelským jménem a heslem uloženým místně v adresáři Azure AD B2C. Na tyto identity často odkazujeme jako na místní účty.
* Identity v **sociálních** **sítích nebo podnikových** identitách, kde identity uživatele spravuje federovaný zprostředkovatel identity, jako je Facebook, Microsoft, ADFS nebo Salesforce.

Uživatel se zákaznickým účtem se může přihlásit s více identitami, například uživatelské jméno, e-mail, ID zaměstnance, ID státní správy a další. Jeden účet může mít víc identit, místní i sociální.

![Identity účtu příjemce](media/technical-overview/identities.png)<br/>*Obrázek: jeden účet příjemce s více identitami v Azure AD B2C*

Azure AD B2C vám umožní spravovat společné atributy profilů uživatelských účtů, jako je zobrazované jméno, příjmení, křestní jméno, město a další. Můžete také rozšíříte schéma Azure AD pro ukládání dalších informací o uživatelích. Například jejich země/oblast nebo umístění, preferovaný jazyk a předvolby, například zda se chtějí přihlásit k odběru bulletinu nebo povolit službu Multi-Factor Authentication.

Přečtěte si další informace o typech uživatelských účtů v Azure AD B2C v článku [Přehled uživatelských účtů v Azure Active Directory B2C](user-overview.md).

## <a name="external-identity-providers"></a>Zprostředkovatelé externích identit

Můžete nakonfigurovat Azure AD B2C, aby se uživatelé mohli přihlašovat k aplikaci s přihlašovacími údaji od externích poskytovatelů sociálních sítí nebo podnikových identit (IdP). Azure AD B2C podporuje externí zprostředkovatele identity, jako je Facebook, účet Microsoft, Google, Twitter a jakýkoli poskytovatel identity, který podporuje OAuth 1,0, OAuth 2,0, OpenID Connect a protokol SAML.

![Zprostředkovatelé externích identit](media/technical-overview/external-idps.png)

S externím federačním zprostředkovatelem identity můžete svým spotřebitelům nabídnout možnost se přihlásit pomocí svých stávajících sociálních nebo podnikových účtů, aniž byste museli vytvořit nový účet jenom pro vaši aplikaci.

Na přihlašovací stránce nebo na přihlašovací stránce Azure AD B2C prezentuje seznam externích zprostředkovatelů identity, které může uživatel zvolit pro přihlášení. Jakmile vyberete jednoho z externích zprostředkovatelů identity, přijímají se (přesměrované) na web vybraného poskytovatele, aby se dokončil proces přihlašování. Jakmile se uživatel úspěšně přihlásí, vrátí se Azure AD B2C pro ověřování účtu ve vaší aplikaci.

![Příklad mobilního přihlášení pomocí účtu sociální sítě (Facebook)](media/technical-overview/external-idp.png)

Pokud chcete zjistit, jak přidat zprostředkovatele identity v Azure AD B2C, přečtěte si téma [kurz: Přidání poskytovatelů identit do aplikací v Azure Active Directory B2C](tutorial-add-identity-providers.md).

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Prostředí identity: uživatelské toky nebo vlastní zásady

Rozšiřitelná architektura zásad Azure AD B2C je její základní silou. Zásady popisují prostředí identity uživatelů, jako je registrace, přihlášení a úpravy profilu.

V Azure AD B2C existují dvě primární cesty, které můžete využít k zajištění těchto identit: uživatelské toky a vlastní zásady.

* **Toky uživatelů** jsou předdefinované, předdefinované, konfigurovatelné zásady, které poskytujeme, abychom mohli vytvořit prostředí pro registraci, přihlašování a úpravu zásad během několika minut.

* **Vlastní zásady** umožňují vytvářet vlastní cesty uživatelů pro scénáře komplexního prostředí identity.

Uživatelské toky a vlastní zásady využívají *architekturu prostředí pro prostředí identity*, modul orchestrace zásad Azure AD B2C's.

### <a name="user-flow"></a>Tok uživatele

Aby vám pomohla rychle nastavit nejběžnější úlohy identity, Azure Portal obsahuje několik předdefinovaných a konfigurovatelných zásad nazývaných *uživatelské toky*.

Můžete nakonfigurovat nastavení toku uživatele tak, aby bylo možné řídit chování identity v aplikacích:

* Typy účtů používané pro přihlášení, jako jsou například účty na Facebooku nebo místní účty, které používají e-mailovou adresu a heslo pro přihlášení
* Atributy, které mají být shromažďovány od příjemce, jako je například křestní jméno, PSČ nebo země/oblast, kde sídlí
* Azure Multi-Factor Authentication (MFA)
* Přizpůsobení uživatelského rozhraní
* Sada deklarací identity v tokenu, který vaše aplikace obdrží poté, co uživatel dokončí tok uživatele
* Správa relací
* ... a další.

Většina běžných scénářů identit pro většinu mobilních a webových aplikací a aplikací na jedné straně se dá definovat a efektivně implementovat pomocí uživatelských toků. Doporučujeme, abyste používali předdefinované toky uživatelů, pokud nemáte složité scénáře pro práci s uživateli, které vyžadují plnou flexibilitu vlastních zásad.

Přečtěte si další informace o toku uživatelů v [toku uživatelů v Azure Active Directory B2C](user-flow-overview.md).

### <a name="custom-policy"></a>Vlastní zásady

Vlastní zásady odemkněte přístup k celému výkonu modulu pro orchestraci identit v architektuře IEF (identity Experience Framework). Díky vlastním zásadám můžete využít IEF k sestavení téměř všech ověřování, registrace uživatelů nebo možností úprav profilů, které si můžete představit.

Rozhraní identity Experience Framework poskytuje možnost vytvářet cesty uživatelů pomocí libovolné kombinace kroků. Příklad:

* Federovat s jinými zprostředkovateli identity
* První a další výzvy k Multi-Factor Authentication (MFA) třetích stran
* Shromažďování libovolných vstupů uživatele
* Integrace s externími systémy pomocí REST API komunikace

Každá taková cesta uživatele je definovaná zásadou a můžete vytvořit tolik zásad, kolik jich potřebujete k tomu, abyste mohli pro vaši organizaci povolit nejlepší uživatelské prostředí.

![Diagram znázorňující příklad složitá cesta uživatele povolená IEF](media/technical-overview/custom-policy.png)

Vlastní zásady jsou definovány několika soubory XML, které na sebe navzájem odkazují v hierarchickém řetězu. Prvky XML definují schéma deklarací identity, transformace deklarací identity, definice obsahu, zprostředkovatele deklarací identity, technické profily, kroky orchestrace cest uživatelů a další aspekty prostředí identity.

Výkonná flexibilita vlastních zásad je nejvhodnější pro případy, kdy potřebujete vytvořit komplexní scénáře identity. Vývojáři, kteří konfigurují vlastní zásady, musí podrobná nastavení důvěryhodných vztahů za účelem zahrnutí koncových bodů metadat, přesných definic deklarací identity a konfigurací tajných kódů, klíčů a certifikátů podle potřeby u každého poskytovatele identity.

Přečtěte si další informace o vlastních zásadách ve [vlastních zásadách v Azure Active Directory B2C](custom-policy-overview.md).

## <a name="protocols-and-tokens"></a>Protokoly a tokeny

- Pro aplikace Azure AD B2C podporuje protokoly [OAuth 2,0](protocols-overview.md), [OpenID Connect](openid-connect.md)a [SAML](connect-with-saml-service-providers.md) pro cesty uživatelů. Vaše aplikace spouští cestu uživatele tím, že vydává žádosti o ověření Azure AD B2C. Výsledkem požadavku na Azure AD B2C je token zabezpečení, jako je token [ID, přístupový token](tokens-overview.md)nebo token SAML. Tento token zabezpečení definuje identitu uživatele v rámci aplikace.

- Pro externí identity Azure AD B2C podporuje federaci s libovolnými zprostředkovateli identity OAuth 1,0, OAuth 2,0, OpenID Connect a SAML.

Následující diagram ukazuje, jak Azure AD B2C může komunikovat s různými protokoly v rámci stejného toku ověřování:

![Diagram klientské aplikace založené na OIDC federování s IdPem založeným na SAML](media/technical-overview/protocols.png)

1. Aplikace předávající strany zahájí žádost o autorizaci k Azure AD B2C pomocí nástroje OpenID Connect.
1. Když se uživatel aplikace rozhodne přihlásit pomocí externího zprostředkovatele identity, který používá protokol SAML, Azure AD B2C vyvolá protokol SAML ke komunikaci s tímto poskytovatelem identity.
1. Poté, co uživatel dokončí operaci přihlášení s externím zprostředkovatelem identity, Azure AD B2C pak vrátí token do aplikace předávající strany pomocí OpenID Connect.

## <a name="application-integration"></a>Integrace aplikací

Když se uživatel chce přihlásit ke svojí aplikaci, ať už se jedná o webovou, mobilní, desktopovou nebo jednostránkovou aplikaci (SPA), zahájí žádost o autorizaci pro koncový bod uživatele nebo vlastní zásady. Tok uživatele nebo vlastní zásady definují a řídí uživatelské prostředí. Když uživatel dokončí tok uživatele, například tok *registrace nebo přihlašování* , Azure AD B2C vygeneruje token a pak přesměruje uživatele zpět do vaší aplikace.

![Mobilní aplikace se šipkami ukazující tok mezi Azure AD B2C přihlašovací stránkou](media/technical-overview/app-integration.png)

Stejný tok uživatele nebo vlastní zásady můžou používat víc aplikací. Jedna aplikace může používat více uživatelských toků nebo vlastních zásad.

Například pro přihlášení k aplikaci používá aplikace tok uživatele *registrace nebo přihlášení* . Až se uživatel přihlásí, může chtít upravit svůj profil, takže aplikace zahájí jinou žádost o autorizaci, tentokrát pomocí *profilu upravit* tok uživatele.

## <a name="seamless-user-experiences"></a>Bezproblémové uživatelské prostředí

V Azure AD B2C můžete pracovat s identitou vašich uživatelů, aby se stránky, na které se zobrazují, plynule a vypadaly na vašich značkách. Získáte téměř úplnou kontrolu nad obsahem HTML a CSS prezentovaným uživatelům, když budou pokračovat prostřednictvím cest k identitě vaší aplikace. Díky této flexibilitě můžete udržovat značky a vizuální konzistenci mezi vaší aplikací a Azure AD B2C.

![Snímky obrazovky s přizpůsobenou přihlašovací stránkou značky](media/technical-overview/seamless-ux.png)

Informace o přizpůsobení uživatelského rozhraní najdete v tématu věnovaném [přizpůsobení uživatelského rozhraní v Azure Active Directory B2C](customize-ui-overview.md).

## <a name="localization"></a>Lokalizace

Přizpůsobení jazyka v Azure AD B2C umožňuje přizpůsobení různých jazyků vašim potřebám zákazníků. Společnost Microsoft poskytuje překlady pro jazyky 36, ale můžete také zadat vlastní překlady pro libovolný jazyk. I v případě, že je vaše prostředí k dispozici pouze pro jeden jazyk, můžete přizpůsobit libovolný text na stránkách.

![Tři přihlašovací stránky pro přihlášení zobrazující text uživatelského rozhraní v různých jazycích](media/technical-overview/localization.png)

Podívejte se, jak lokalizace funguje v [přizpůsobení jazyka v Azure Active Directory B2C](user-flow-language-customization.md).

## <a name="add-your-own-business-logic"></a>Přidání vlastní obchodní logiky

Pokud se rozhodnete použít vlastní zásady, můžete integrovat s rozhraním RESTful API v cestě uživatele a přidat tak vlastní obchodní logiku do cesty. Azure AD B2C například může vyměňovat data se službou RESTful k těmto akcím:

* Zobrazit vlastní uživatelsky přívětivé chybové zprávy.
* Ověřte vstup uživatele, aby nedocházelo k uchování poškozených dat ve vašem uživatelském adresáři. Můžete například změnit data zadaná uživatelem, jako je například velká písmena jejich křestní jméno, pokud je zadáte malými písmeny.
* Rozšiřte uživatelská data o další integraci s podnikovou firemní aplikací.
* Pomocí volání RESTful můžete odesílat nabízená oznámení, aktualizovat podnikové databáze, spouštět proces migrace uživatelů, spravovat oprávnění, auditovat databáze a další.

Věrnostní programy jsou jiným scénářem, který podpora Azure AD B2C's podporuje pro volání rozhraní REST API. Například vaše služba RESTful může obdržet e-mailovou adresu uživatele, zadat dotaz na databázi zákazníků a pak pro Azure AD B2C vrátit věrnostní číslo uživatele. Návratová data mohou být uložená v uživatelském účtu uživatele v Azure AD B2C, pak se dále vyhodnocovat v dalších krocích v zásadě nebo být zahrnutá do přístupového tokenu.

![Integrace obchodních aplikací v mobilní aplikaci](media/technical-overview/lob-integration.png)

Můžete přidat REST API volání v jakémkoli kroku v cestě uživatele definované vlastními zásadami. Například můžete volat REST API:

* Během přihlašování těsně před Azure AD B2C ověří přihlašovací údaje.
* Hned po přihlášení
* Před vytvořením nového účtu v adresáři Azure AD B2C
* Po Azure AD B2C vytvoří nový účet v adresáři.
* Než Azure AD B2C vydá přístupový token

Pokud chcete zjistit, jak používat vlastní zásady pro integraci rozhraní RESTful API v Azure AD B2C, přečtěte si téma [integrace REST API výměn deklarací identity ve vlastních zásadách Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="protect-customer-identities"></a>Ochrana zákaznických identit

Azure AD B2C v souladu se zabezpečením, ochranou osobních údajů a dalšími závazky popsanými v [centru pro Microsoft Azure Trust](https://www.microsoft.com/trustcenter/cloudservices/azure).

Relace se modelují jako zašifrovaná data a dešifrovací klíč je známý jenom pro Azure AD B2C službu tokenů zabezpečení. Používá se silný šifrovací algoritmus AES-192. Všechny komunikační cesty jsou chráněny protokolem TLS pro zajištění důvěrnosti a integrity. Naše služba tokenů zabezpečení používá certifikát rozšířené validace (EV) pro TLS. Obecně platí, že služba tokenů zabezpečení snižuje útoky skriptováním mezi weby (XSS) tím, že nevykresluje nedůvěryhodný vstup.

![Diagram zabezpečených dat při přenosu a v klidovém režimu](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Přístup k uživatelským datům

Klienti Azure AD B2C sdílejí mnoho vlastností s klienty Azure Active Directory klientů, kteří se používají pro zaměstnance a partnery. Mezi sdílené aspekty patří mechanismy pro zobrazení rolí pro správu, přiřazování rolí a auditování aktivit.

Role můžete přiřadit k řízení toho, kdo může provádět určité akce správy v Azure AD B2C, včetně:

* Vytváření a správa všech aspektů toků uživatelů
* Vytvoření a Správa schématu atributů dostupného pro všechny toky uživatelů
* Konfigurace zprostředkovatelů identity pro použití v přímé federaci
* Vytvoření a Správa zásad pro pravidla důvěryhodnosti v architektuře prostředí identity (vlastní zásady)
* Správa tajných kódů federace a šifrování v architektuře prostředí identity (vlastní zásady)

Další informace o rolích služby Azure AD, včetně podpory rolí Azure AD B2C správy, najdete [v tématu oprávnění role správce v Azure Active Directory](../active-directory/roles/permissions-reference.md).

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

Azure AD B2C Multi-Factor Authentication (MFA) pomáhá chránit přístup k datům a aplikacím a současně zachovává jednoduchost vašich uživatelů. Poskytuje dodatečné zabezpečení tím, že vyžaduje druhou formu ověřování a poskytuje silné ověřování tím, že nabízí řadu snadno použitelných metod ověřování. Uživatelé můžou nebo nemusí být v rámci VÍCEFAKTOROVÉHO ověřování vyučeni na základě rozhodnutí týkajících se konfigurace, která můžete nastavit jako správce.

Podívejte se, jak povolit VÍCEFAKTOROVÉ ověřování v tocích uživatelů v tématu [Povolení vícefaktorového ověřování v Azure Active Directory B2C](custom-policy-multi-factor-authentication.md).

### <a name="smart-account-lockout"></a>Uzamčení inteligentního účtu

Aby se zabránilo pokusům o vyhlašování nepřímých vynucení hesla, Azure AD B2C používá důmyslnou strategii k uzamčení účtů na základě IP adresy, zadaných hesel a několika dalších faktorů. Doba uzamčení se automaticky zvyšuje na základě rizika a počtu pokusů.

![Inteligentní uzamčení účtu](media/technical-overview/smart-lockout1.png)

Další informace o správě nastavení ochrany heslem najdete v tématu [Správa hrozeb pro prostředky a data v Azure Active Directory B2C](threat-management.md).

### <a name="password-complexity"></a>Složitost hesla

Při registraci nebo resetování hesla musí vaši uživatelé zadávat heslo, které splňuje pravidla složitosti. Ve výchozím nastavení Azure AD B2C vynutila zásady silného hesla. Azure AD B2C taky nabízí možnosti konfigurace pro určení požadavků na složitost hesel, která vaši zákazníci používají.

Požadavky na složitost hesla můžete nakonfigurovat jak v [toku uživatelů](user-flow-password-complexity.md) , tak i ve [vlastních zásadách](custom-policy-password-complexity.md).

## <a name="auditing-and-logs"></a>Auditování a protokoly

Azure AD B2C generuje protokoly auditu obsahující informace o aktivitách, vydané tokeny a přístup správce. Pomocí těchto protokolů auditu můžete pochopit aktivitu platformy a diagnostikovat problémy. Položky protokolu auditu jsou k dispozici brzy po aktivitě, která událost vygenerovala.

V protokolu auditu, který je k dispozici pro vašeho tenanta Azure AD B2C nebo konkrétního uživatele, můžete najít informace, například:

* Aktivity týkající se autorizace uživatele pro přístup k prostředkům B2C (například správce, který přistupuje k seznamu zásad B2C)
* Aktivity týkající se atributů adresáře načtené, když se správce přihlásí pomocí Azure Portal
* Operace vytvoření, čtení, aktualizace a odstranění (CRUD) v aplikacích B2C
* Operace CRUD na klíčích uložených v kontejneru klíčů B2C
* Operace CRUD na B2Cch prostředcích (například zásady a zprostředkovatelé identity)
* Ověření přihlašovacích údajů uživatele a vystavení tokenu

![Protokol auditu jednotlivých uživatelů zobrazený v Azure Portal](media/technical-overview/audit-log.png)

Další podrobnosti o protokolech auditu najdete v tématu [přístup k protokolům auditu Azure AD B2C](view-audit-logs.md).

### <a name="usage-insights"></a>Přehledy využití

Azure AD B2C vám umožní zjistit, kdy se uživatelé můžou zaregistrovat nebo přihlásit ke své webové aplikaci, kde se nacházejí uživatelé a jaké prohlížeče a operační systémy používají. Integrací Azure Application Insights do Azure AD B2C pomocí vlastních zásad můžete získat přehled o tom, jak se uživatelé registrují, přihlašovat, resetovat heslo nebo upravovat svůj profil. Díky takovému vědomí můžete provádět rozhodování na základě dat pro nadcházející vývojové cykly.

Další informace o analýze využití najdete v [Azure Active Directory B2C používání Application Insights](analytics-with-application-insights.md).

## <a name="next-steps"></a>Další kroky

Teď, když máte hlubší přehled o funkcích a technických aspektech Azure Active Directory B2C, začněte s naším [kurzem pro vytvoření tenanta Azure Active Directory B2C](tutorial-create-tenant.md).
