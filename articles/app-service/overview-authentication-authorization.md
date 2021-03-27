---
title: Ověřování a autorizace
description: Přečtěte si informace o integrovaném ověřování a podpoře autorizace v Azure App Service a Azure Functions a o tom, jak vám může pomoci se zabezpečením aplikace před neoprávněným přístupem.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 35513abdfb61d889abdbd4af7125b1fbb556d7b8
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612751"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Ověřování a autorizace v Azure App Service a Azure Functions

Azure App Service poskytuje integrované možnosti ověřování a autorizace (někdy označované jako "snadné ověření"), takže se můžete přihlašovat uživatele a přistupovat k datům tak, že ve své webové aplikaci, rozhraní RESTful API a mobilním back-endu napíšete minimální nebo žádný kód a také [Azure Functions](../azure-functions/functions-overview.md). Tento článek popisuje, jak App Service pomáhá zjednodušit ověřování a autorizaci pro vaši aplikaci.

## <a name="why-use-the-built-in-authentication"></a>Proč používat integrované ověřování?

Tuto funkci nemusíte používat k ověřování a autorizaci. Ve vaší webové architektuře můžete použít sady funkcí zabezpečení, nebo můžete napsat vlastní nástroje. Budete ale muset zajistit, aby vaše řešení zůstalo v aktuálním stavu s nejnovějšími aktualizacemi zabezpečení, protokolu a prohlížeče.

Implementace zabezpečeného řešení pro ověřování (přihlašování uživatelů) a autorizaci (poskytnutí přístupu k zabezpečeným datům) může výrazně zvýšit úsilí. Musíte si projít osvědčenými postupy a standardy podle oboru, a zajistit aktuálnost vaší implementace. Integrovaná funkce ověřování pro App Service a Azure Functions vám může ušetřit čas a úsilí díky poskytování předem připraveného ověřování u federovaných zprostředkovatelů identity, což vám umožní soustředit se na zbytek vaší aplikace.

- Azure App Service umožňuje integrovat celou řadu možností ověřování do vaší webové aplikace nebo rozhraní API, aniž byste je museli implementovat sami.
- Je integrována přímo do platformy a nevyžaduje žádný konkrétní jazyk, sadu SDK, odborné znalosti zabezpečení ani žádný kód, který by mohl využívat.
- Můžete integrovat s více zprostředkovateli přihlášení. Například Azure AD, Facebook, Google, Twitter.

## <a name="identity-providers"></a>Zprostředkovatelé identit

App Service používá [federované identity](https://en.wikipedia.org/wiki/Federated_identity), ve kterém poskytovatel identity od jiného výrobce spravuje identity uživatelů a tok ověřování za vás. Ve výchozím nastavení jsou k dispozici následující zprostředkovatelé identity:

| Poskytovatel | Koncový bod přihlášení | Pokyny pro How-To |
| - | - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` | [App Service přihlášení k Azure AD](configure-authentication-provider-aad.md) |
| [Účet Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` | [App Service přihlášení k účtu Microsoft](configure-authentication-provider-microsoft.md) |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` | [Přihlášení k Facebooku App Service](configure-authentication-provider-facebook.md) |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` | [App Service přihlašovacích údajů Google](configure-authentication-provider-google.md) |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` | [App Service přihlášení k Twitteru](configure-authentication-provider-twitter.md) |
| Libovolný poskytovatel [OpenID Connect](https://openid.net/connect/) (Preview) | `/.auth/login/<providerName>` | [Přihlášení App Service OpenID Connect](configure-authentication-provider-openid-connect.md) |

Pokud povolíte ověřování a autorizaci jedním z těchto poskytovatelů, je k dispozici koncový bod přihlášení pro ověřování uživatelů a ověření tokenů ověřování od poskytovatele. Uživatelům můžete poskytnout libovolný počet těchto možností přihlášení.

## <a name="considerations-for-using-built-in-authentication"></a>Předpoklady pro používání integrovaného ověřování

Povolení této funkce způsobí, že všechny požadavky na vaši aplikaci budou automaticky přesměrovány na HTTPS bez ohledu na nastavení konfigurace App Service pro vymáhání protokolu HTTPS. Tuto možnost můžete zakázat s  `requireHttps` nastavením v konfiguraci v2. Doporučuje se ale používat protokol HTTPS a měli byste zajistit, aby se žádné tokeny zabezpečení nikdy nepřenesly přes nezabezpečená připojení HTTP.

App Service lze použít pro ověřování s nebo bez omezení přístupu k obsahu a rozhraním API vašeho webu. Pokud chcete omezit přístup aplikace jenom na ověřené uživatele, nastavte **akci, která se má provést, když se žádost neověřuje** , aby se přihlásila s některým z konfigurovaných zprostředkovatelů identity. Pokud chcete ověřovat, ale Neomezovat přístup, nastavte **akci, která se má provést, když se požadavek neověřuje** na Povolit anonymní požadavky (bez akce).

> [!NOTE]
> Každé aplikaci byste měli dát registraci své vlastní oprávnění a souhlas. Nepoužívejte sdílení oprávnění mezi prostředími pomocí samostatných registrací aplikací pro samostatné sloty nasazení. Při testování nového kódu Tento postup může přispět k tomu, aby se zabránilo problémům v ovlivnění produkční aplikace.

## <a name="how-it-works"></a>Jak to funguje

[Architektura funkcí v systému Windows (jiné nasazení než kontejner)](#feature-architecture-on-windows-non-container-deployment))

[Architektura funkcí v systémech Linux a kontejnerů](#feature-architecture-on-linux-and-containers)

[Tok ověřování](#authentication-flow)

[Chování autorizace](#authorization-behavior)

[Deklarace identity uživatelů a aplikací](#user-and-application-claims)

[Úložiště tokenů](#token-store)

[Protokolování a trasování](#logging-and-tracing)

#### <a name="feature-architecture-on-windows-non-container-deployment"></a>Architektura funkcí v systému Windows (nasazení bez kontejnerů)

Modul ověřování a autorizace se spouští ve stejném izolovaném prostoru jako váš kód aplikace. Pokud je povolena, každý příchozí požadavek HTTP projde před tím, než bude zpracován kódem vaší aplikace.

:::image type="content" source="media/app-service-authentication-overview/architecture.png" alt-text="Diagram architektury znázorňující požadavky, které jsou zachyceny procesem v izolovaném prostoru webu, který komunikuje s poskytovateli identity před povolením provozu do nasazené lokality" lightbox="media/app-service-authentication-overview/architecture.png":::

Tento modul zpracovává několik věcí pro vaši aplikaci:

- Ověřuje uživatele pomocí zadaného zprostředkovatele.
- Ověří, ukládá a aktualizuje tokeny.
- Spravuje ověřenou relaci.
- Vloží informace o identitě do hlaviček požadavků.

Modul se spouští odděleně od kódu aplikace a je nakonfigurovaný pomocí nastavení aplikace. Nevyžadují se žádné sady SDK, konkrétní jazyky ani změny kódu vaší aplikace. 

#### <a name="feature-architecture-on-linux-and-containers"></a>Architektura funkcí v systémech Linux a kontejnerů

Modul ověřování a autorizace se spouští v samostatném kontejneru izolovaném z kódu vaší aplikace. Pomocí toho, co je známo jako [vzorek velvyslanců](/azure/architecture/patterns/ambassador), komunikuje s příchozím provozem, aby prováděl podobné funkce jako ve Windows. Protože se nespustí v procesu, není možné žádná přímá integrace s konkrétními jazykovými rozhraními. relevantní informace, které vaše aplikace potřebuje, se ale předávají pomocí hlaviček požadavků, jak je vysvětleno níže.

#### <a name="authentication-flow"></a>Tok ověřování

Tok ověřování je stejný pro všechny poskytovatele, ale liší se v závislosti na tom, jestli se chcete přihlásit pomocí sady SDK pro poskytovatele:

- Bez sady Provider SDK: aplikace deleguje federované přihlašování k App Service. Většinou se jedná o prohlížeč pro aplikace v prohlížeči, který může uživateli prezentovat přihlašovací stránku poskytovatele. Serverový kód spravuje proces přihlášení, takže se také nazývá _tok směrovaného serveru_ nebo _tok serveru_. Tento případ se vztahuje na aplikace prohlížeče. Vztahuje se také na nativní aplikace, které uživatele podepisují pomocí Mobile Apps klientské sady SDK, protože sada SDK otevírá webové zobrazení k podepisování uživatelů pomocí ověřování App Service.
- Se sadou SDK pro poskytovatele: aplikace podepisuje uživatele do poskytovatele ručně a pak odešle ověřovací token, aby App Service k ověření. Většinou se jedná o aplikace bez prohlížeče, které nemůžou uživateli prezentovat přihlašovací stránku poskytovatele. Kód aplikace spravuje proces přihlášení, takže se také nazývá _tok směrovaného klientem_ nebo _tok klienta_. Tento případ se vztahuje na klienty rozhraní REST API, [Azure Functions](../azure-functions/functions-overview.md)a JavaScript, jakož i aplikace v prohlížeči, které v procesu přihlašování vyžadují větší flexibilitu. Vztahuje se také na nativní mobilní aplikace, které uživatele podepisují pomocí sady SDK poskytovatele.

Volání z důvěryhodné aplikace prohlížeče v App Service do jiného REST API v App Service nebo [Azure Functions](../azure-functions/functions-overview.md) lze ověřit pomocí toku směrovaného na server. Další informace najdete v tématu [Přizpůsobení ověřování a autorizace v App Service](app-service-authentication-how-to.md).

V následující tabulce jsou uvedené kroky toku ověřování.

| Krok | Bez sady Provider SDK | Se sadou SDK pro poskytovatele |
| - | - | - |
| 1. podepsat uživatele | Přesměrovává klienta na `/.auth/login/<provider>` . | Kód klienta podepisuje uživatele přímo se sadou SDK pro poskytovatele a přijímá ověřovací token. Informace najdete v dokumentaci k poskytovateli. |
| 2. po ověření | Zprostředkovatel přesměrovává klienta na `/.auth/login/<provider>/callback` . | [Token příspěvků klientských kódů od poskytovatele](app-service-authentication-how-to.md#validate-tokens-from-providers) k `/.auth/login/<provider>` ověření. |
| 3. vytvoření ověřené relace | App Service přidá ověřený soubor cookie pro odpověď. | App Service vrátí do klientského kódu svůj vlastní ověřovací token. |
| 4. zajišťovat ověřený obsah | Klient zahrnuje ověřovací soubor cookie v následných požadavcích (automaticky zpracovávaných prohlížečem). | Klientský kód prezentuje ověřovací token v `X-ZUMO-AUTH` hlavičce (automaticky se zpracovává Mobile Apps klientské sady SDK). |

Pro klientské prohlížeče App Service může automaticky směrovat všechny neověřené uživatele na `/.auth/login/<provider>` . Můžete taky prezentovat uživatele s jedním nebo více `/.auth/login/<provider>` odkazy pro přihlášení k aplikaci pomocí poskytovatele podle vlastního výběru.

<a name="authorization"></a>

#### <a name="authorization-behavior"></a>Chování autorizace

V [Azure Portal](https://portal.azure.com)můžete nakonfigurovat App Service autorizaci s řadou chování, když příchozí žádost není ověřena.

![Snímek obrazovky s rozevíracím seznamem "akce, která se má provést, když se žádost neověřuje"](media/app-service-authentication-overview/authorization-flow.png)

Tyto možnosti jsou popsány v následujících nadpisech.

**Povolení anonymních požadavků (žádná akce)**

Tato možnost odloží autorizaci neověřeného provozu do kódu aplikace. U ověřených požadavků App Service také společně s ověřovacími informacemi v hlavičkách protokolu HTTP.

Tato možnost nabízí větší flexibilitu při zpracování anonymních požadavků. Například umožňuje uživatelům [prezentovat více poskytovatelů přihlášení](app-service-authentication-how-to.md#use-multiple-sign-in-providers) . Je však nutné napsat kód.

**Povolení pouze ověřených požadavků**

Možnost se **přihlásí pomocí \<provider>**. App Service přesměruje všechny anonymní požadavky na `/.auth/login/<provider>` poskytovatele, kterého zvolíte. Pokud anonymní požadavek pochází z nativní mobilní aplikace, vrácená odpověď je `HTTP 401 Unauthorized` .

Pomocí této možnosti nemusíte v aplikaci psát žádný ověřovací kód. Přesnější autorizaci, například autorizaci specifickou pro role, je možné zpracovat kontrolou deklarací identity uživatele (viz [přístup k deklaracím uživatelů](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou.

> [!NOTE]
> Ve výchozím nastavení může každý uživatel ve vašem tenantovi Azure AD požádat o token vaší aplikace ze služby Azure AD. Pokud chcete omezit přístup k aplikaci na definovanou sadu uživatelů, můžete [aplikaci ve službě Azure AD nakonfigurovat](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) .


#### <a name="user-and-application-claims"></a>Deklarace identity uživatelů a aplikací

Pro všechny jazykové architektury App Service vytvoří deklarace v příchozím tokenu (ať už jsou z ověřeného koncového uživatele nebo klientské aplikace), a to tak, že je vloží do hlaviček požadavku. Pro App Service aplikace ASP.NET 4,6 naplní [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) deklarací identity ověřeného uživatele, takže můžete postupovat podle standardního vzoru kódu .NET, včetně `[Authorize]` atributu. Podobně pro aplikace PHP App Service naplní `_SERVER['REMOTE_USER']` proměnnou. Pro aplikace Java jsou deklarace identity [dostupné z servlet Tomcat](configure-language-java.md#authenticate-users-easy-auth).

Pro [Azure Functions](../azure-functions/functions-overview.md)není `ClaimsPrincipal.Current` vyplněno pro kód .NET, ale stále můžete najít deklarace identity uživatelů v hlavičce požadavku nebo získat `ClaimsPrincipal` objekt z kontextu požadavku nebo dokonce prostřednictvím parametru vazby. Další informace najdete v tématu [Working with identity klientů](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) .

Další informace najdete v tématu [přístup k deklaracím uživatelů](app-service-authentication-how-to.md#access-user-claims).

V tuto chvíli ASP.NET Core aktuálně nepodporuje naplnění aktuálního uživatele funkcí ověřování/autorizace. Existují však některé [třetí strany Open Source komponenty middlewaru,](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) které vám pomůžou tuto mezeru vyplnit.

#### <a name="token-store"></a>Úložiště tokenů

App Service poskytuje integrované úložiště tokenů, což je úložiště tokenů, které jsou přidružené k uživatelům webových aplikací, rozhraní API nebo nativních mobilních aplikací. Pokud povolíte ověřování u libovolného poskytovatele, je úložiště tokenů hned dostupné pro vaši aplikaci. Pokud kód aplikace potřebuje přístup k datům z těchto poskytovatelů jménem uživatele, například:

- odeslání příspěvku na časovou osu Facebooku ověřeného uživatele
- Přečtěte si podniková data uživatele pomocí rozhraní Microsoft Graph API.

Obvykle je nutné napsat kód pro shromažďování, ukládání a aktualizaci těchto tokenů ve vaší aplikaci. V úložišti tokenů stačí [načíst tokeny](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) , když je potřebujete, a [sdělit App Service, že je budou aktualizovat](app-service-authentication-how-to.md#refresh-identity-provider-tokens) , jakmile se stanou neplatnými. 

Tokeny ID, přístupové tokeny a aktualizační tokeny jsou uloženy v mezipaměti pro ověřenou relaci a jsou přístupné pouze přidruženému uživateli.  

Pokud nepotřebujete v aplikaci pracovat s tokeny, můžete úložiště tokenů zakázat na stránce **ověřování/autorizace** vaší aplikace.

#### <a name="logging-and-tracing"></a>Protokolování a trasování

Pokud [povolíte protokolování aplikací](troubleshoot-diagnostic-logs.md), zobrazí se přímo v souborech protokolu trasování pro ověřování a autorizaci. Pokud se zobrazí chyba ověřování, kterou jste neočekávali, můžete pohodlně vyhledat všechny podrobnosti, a to tak, že budete hledat v existujících protokolech aplikací. Pokud povolíte [trasování chybných požadavků](troubleshoot-diagnostic-logs.md), vidíte přesně to, jakou roli se modul ověřování a autorizace mohl přehrát v neúspěšném požadavku. V protokolech trasování vyhledejte odkazy na modul s názvem `EasyAuthModule_32/64` .

## <a name="more-resources"></a>Další zdroje informací

- [Postupy: Konfigurace App Service nebo Azure Functions aplikace pro použití přihlášení služby Azure AD](configure-authentication-provider-aad.md)
- [Rozšířené použití ověřování a autorizace v Azure App Service](app-service-authentication-how-to.md)

Vzory
- [Kurz: Přidání ověřování do webové aplikace běžící na Azure App Service](scenario-secure-app-authentication-app-service.md)
- [Kurz: ověřování a autorizace uživatelů na konci Azure App Service (Windows nebo Linux)](tutorial-auth-aad.md)
- [Integrace .NET Core pro Azure AppService EasyAuth (třetí strana)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
- [Získání Azure App Service ověřování v práci s .NET Core (třetí strana)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)
