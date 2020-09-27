---
title: Co je Azure Active Directory B2C?
description: Naučte se, jak můžete použít Azure Active Directory B2C k podpoře externích identit ve vašich aplikacích, včetně sociálního přihlášení s Facebooku, Google a dalšími poskytovateli identity.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7126cd38a9d0f9ed0079a73e1235cb08a99bcbcf
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396531"
---
# <a name="what-is-azure-active-directory-b2c"></a>Co je Azure Active Directory B2C?

Azure Active Directory B2C poskytuje jako službu identitu od firmy po zákazníka. Vaši zákazníci používají preferované identity sociálních, podnikových nebo místních účtů k získání přístupu k aplikacím a rozhraním API pomocí jednotného přihlašování.

![Azure AD B2C infografika zprostředkovatelů identit a podřízených aplikací](./media/overview/azureadb2c-overview.png)

Azure Active Directory B2C (Azure AD B2C) je řešení pro správu přístupu k identitám zákazníků (CIAM), které podporuje miliony uživatelů a miliard ověřování za den. Postará o škálování a bezpečnost ověřovací platformy, monitorování a automatické zpracování hrozeb, jako je například odepření služby, postřik hesel nebo útoky hrubou silou.

## <a name="custom-branded-identity-solution"></a>Řešení identity vlastní značky

Azure AD B2C je řešení ověřování typu bílá-Label. Pomocí vaší značky můžete přizpůsobit celé uživatelské prostředí, aby se s vašimi webovými a mobilními aplikacemi plynule.

Umožňuje přizpůsobit všechny stránky zobrazené Azure AD B2C, když se uživatelé přihlásí, přihlásí a upraví své informace o profilu. Přizpůsobte si HTML, CSS a JavaScript v rámci cest uživatelů, aby se Azure AD B2C zkušenost vypadala a vypadala jako nativní součást vaší aplikace.

![Přizpůsobení přihlašovacích a přihlašovacích stránek a obrázku na pozadí](./media/overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Přístup s jednotným přihlašováním pomocí uživatelsky poskytnuté identity

Azure AD B2C používá ověřovací protokoly založené na standardech, včetně připojení OpenID Connect, OAuth 2,0 a SAML. Integruje se s většinou moderních aplikací a komerčního softwaru mimo polici.

![Diagram identit třetích stran federování k Azure AD B2C](./media/overview/scenario-singlesignon.png)

Díky tomu, že slouží jako centrální ověřovací autorita pro vaše webové aplikace, mobilní aplikace a rozhraní API, Azure AD B2C umožňuje vytvořit pro ně řešení jednotného přihlašování (SSO). Centralizovat shromažďování profilů a informací o preferencích uživatelů a zachytit detailní analýzy týkající se chování přihlášení a převodu.

## <a name="integrate-with-external-user-stores"></a>Integrace s úložišti externích uživatelů

Azure AD B2C poskytuje adresář, který může obsahovat 100 vlastních atributů na uživatele. Můžete je ale integrovat i s externími systémy. Můžete například použít Azure AD B2C pro ověřování, ale delegovat externí databázi pro řízení vztahů se zákazníky (CRM) nebo věrnostní databáze zákazníků jako zdroj pravdy pro zákaznická data.

Dalším scénářem úložiště externího uživatele je, že Azure AD B2C zpracovávat ověřování pro vaši aplikaci, ale integrovat s externím systémem, který ukládá profil uživatele nebo osobní údaje. Například pro splnění požadavků na zaregistrování dat, například místních nebo místních zásad úložiště dat.

![Logický diagram Azure AD B2C komunikaci s externím uživatelským úložištěm](./media/overview/scenario-remoteprofile.png)

Azure AD B2C může umožnit shromažďování informací od uživatele během registrace nebo úprav profilu a pak tato data z externího systému předat. Během budoucích ověřování pak Azure AD B2C může načíst data z externího systému a v případě potřeby je zahrnout jako součást odpovědi ověřovacího tokenu, kterou posílá do vaší aplikace.

## <a name="progressive-profiling"></a>Progresivní profilace

Jiná možnost cesty uživatele zahrnuje postupné profilace. Progresivní profilace umožňuje zákazníkům rychle dokončit první transakci shromažďováním minimálního množství informací. Pak postupně Shromážděte více dat profilu od zákazníka v budoucích přihlášeních.

![Vizuální znázornění postupného profilace](./media/overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>Ověřování identity a kontrola identity od jiných výrobců

Pomocí Azure AD B2C můžete usnadnit ověřování identity a kontrolu tím, že shromažďujete uživatelská data a pak je předáte do systému třetí strany, abyste mohli ověřit, vyhodnocování vztahů důvěryhodnosti a schválení pro vytváření uživatelských účtů.

![Diagram znázorňující tok uživatele pro kontrolu identity od jiného výrobce](./media/overview/scenario-idproofing.png)

Jsou to jenom některé z věcí, které můžete provádět s Azure AD B2C jako vaše podniková zákaznická platforma. Následující části tohoto přehledu vás provedou ukázkovou aplikací, která používá Azure AD B2C. Vítá vás také přechod přímo na podrobnější [technický přehled Azure AD B2C](technical-overview.md).

## <a name="example-woodgrove-groceries"></a>Příklad: WoodGrove nákupy

[Woodgrove][woodgrove] je živá webová aplikace vytvořená Microsoftem, která předvádí několik Azure AD B2Cch funkcí. V následujících částech najdete některé možnosti ověřování poskytované Azure AD B2C na webu WoodGrove.

### <a name="business-overview"></a>Přehled podnikání

WoodGrove je online prodejnový obchod, který prodává prodejny jednotlivcům i obchodním zákazníkům. Jejich obchodní zákazníci kupují své zákazníky jménem své společnosti nebo firmy, které spravují.

### <a name="sign-in-options"></a>Možnosti přihlášení

WoodGroveé prodejny nabízejí několik možností přihlašování na základě vztahů jejich zákazníků s obchodem:

* **Jednotliví** zákazníci se můžou zaregistrovat nebo přihlašovat pomocí jednotlivých účtů, jako je například se zprostředkovatelem sociálních identit nebo e-mailovou adresou a heslem.
* **Podnikoví** zákazníci se můžou zaregistrovat nebo se přihlásit pomocí svých podnikových přihlašovacích údajů.
* **Partneři** a dodavatelé jsou jednotlivci, kteří poskytují prodejny obchod s produkty k prodeji. Identitu partnera poskytuje [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).

![Jednotlivé přihlašovací stránky (B2C), obchodní (B2C) a partneři (B2B)](./media/overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Ověřování jednotlivých zákazníků

Když zákazník vybere **přihlášení pomocí svého osobního účtu**, přesměruje se na přizpůsobenou přihlašovací stránku, která je hostovaná Azure AD B2C. Můžete vidět na následujícím obrázku, který jsme přizpůsobili uživatelské rozhraní (UI), aby vypadal stejně jako web WoodGrove. Zákazníci WoodGrove by neměli vědět, že prostředí ověřování je hostované a zabezpečené pomocí Azure AD B2C.

![Vlastní přihlašovací stránka WoodGrove, jejímž hostitelem je Azure AD B2C](./media/overview/sign-in.png)

WoodGrove umožňuje svým zákazníkům registraci a přihlašování pomocí účtů Google, Facebook nebo Microsoft jako jejich zprostředkovatele identity. Nebo se můžou zaregistrovat pomocí své e-mailové adresy a hesla a vytvořit tak, co se nazývá *místní účet*.

Když zákazník vybere **registraci pomocí osobního účtu** a pak se **zaregistruje**, zobrazí se mu vlastní registrační stránka.

![Vlastní registrační stránka WoodGrove, jejímž hostitelem je Azure AD B2C](./media/overview/sign-up.png)

Po zadání e-mailové adresy a výběru možnosti **Odeslat ověřovací kód**Azure AD B2C jim pošle kód. Po zadání kódu vyberte možnost **ověřit kód**a potom zadejte další informace ve formuláři, musí také souhlasit s podmínkami služby.

Kliknutím na tlačítko **vytvořit** dojde Azure AD B2C k přesměrování uživatele zpět na web nákupů v Woodgrove. Při přesměrování Azure AD B2C předá webové aplikaci WoodGrove ověřovací token OpenID Connect. Uživatel je teď přihlášený a připravený k použití, zobrazovaný název zobrazený v pravém horním rohu, který označuje, že jsou přihlášeni.

![Záhlaví webu WoodGrove nákupů ukazující, že uživatel je přihlášený](./media/overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>Ověřování obchodních zákazníků

Když zákazník vybere jednu z možností v části **Obchodní zákazníci**, vyvolá web Woodgrove nákupů jinou zásadu Azure AD B2C, než má jednotliví zákazníci.

Tato zásada prezentuje uživateli možnost použít své podnikové přihlašovací údaje pro registraci a přihlášení. V WoodGrove příkladu se uživatelům zobrazí výzva, abyste se přihlásili pomocí pracovního nebo školního účtu. Tato zásada využívá [víceklientské aplikace Azure AD](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) a `/common` koncový bod Azure ad k federovat Azure AD B2C s jakýmkoli Microsoft 365 zákazníkem na světě.

### <a name="authenticate-partners"></a>Ověřování partnerů

**Přihlášení pomocí odkazu na účet dodavatele** používá funkce spolupráce Azure Active Directory B2B's. Azure AD B2B je rodina funkcí v Azure Active Directory pro správu identit partnerů. Tyto identity můžou být federované z Azure Active Directory pro přístup do aplikací chráněných Azure AD B2C.

Přečtěte si další informace o Azure AD B2B v [tom, co je přístup uživatelů typu Host v Azure Active Directory B2B?](../active-directory/b2b/what-is-b2b.md).

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>Další kroky

Teď, když máte představu o tom, co Azure AD B2C, a některé z scénářů, které vám může pomáhat, dig o jeho funkcích a technických aspektech trochu hlubší.

> [!div class="nextstepaction"]
> [Azure AD B2C Technical Overview >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo
