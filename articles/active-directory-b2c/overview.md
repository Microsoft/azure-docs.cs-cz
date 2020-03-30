---
title: Co je Azure Active Directory B2C?
description: Zjistěte, jak můžete pomocí služby Azure Active Directory B2C podporovat externí identity ve vašich aplikacích, včetně přihlášení na sociální chod s Facebookem, Googlem a dalšími poskytovateli identit.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 61b6d5ea903d00519c58556bc99da7065741a6e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78184056"
---
# <a name="what-is-azure-active-directory-b2c"></a>Co je Azure Active Directory B2C?

Azure Active Directory B2C poskytuje identitu mezi podniky jako službu. Vaši zákazníci používají své upřednostňované identity účtů pro sociální sítě, organizace nebo místní účty, aby získali přístup k vašim aplikacím a webovým i napasek.

![Infografika zprostředkovatelů identit Azure AD B2C a podřízené aplikace](./media/overview/azureadb2c-overview.png)

Azure Active Directory B2C (Azure AD B2C) je řešení pro správu přístupu k identitám zákazníků (CIAM), které podporuje miliony uživatelů a miliardy ověřování denně. Stará se o škálování a bezpečnost ověřovací platformy, monitoruje a automaticky zpracovává hrozby, jako je odmítnutí služby, rozprašování heslem nebo útoky hrubou silou.

## <a name="custom-branded-identity-solution"></a>Řešení identity na zakázku

Azure AD B2C je řešení ověřování white label. Můžete přizpůsobit celou uživatelskou zkušenost s vaší značkou tak, aby se hladce prolínaly s webovými a mobilními aplikacemi.

Přizpůsobte si každou stránku zobrazenou službou Azure AD B2C, když se uživatelé zaregistrují, přihlásí a upraví informace o svém profilu. Přizpůsobte si HTML, CSS a JavaScript ve svých cestách uživatelů tak, aby prostředí Azure AD B2C vypadalo a vypadalo jako nativní součást vaší aplikace.

![Přizpůsobené přihlašovací a přihlašovací stránky a obrázek na pozadí](./media/overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Přístup k jednotnému přihlášení s identitou zajišťovnou uživatelem

Azure AD B2C používá ověřovací protokoly založené na standardech, včetně OpenID Connect, OAuth 2.0 a SAML. Integruje se s většinou moderních aplikací a komerčním softwarem.

![Diagram identit třetích stran federating do Azure AD B2C](./media/overview/scenario-singlesignon.png)

Tím, že slouží jako centrální ověřovací autorita pro vaše webové aplikace, mobilní aplikace a API, Azure AD B2C umožňuje vytvořit řešení jednotného přihlašování (SSO) pro všechny. Centralilizujte shromažďování informací o uživatelském profilu a předvolbách a zachyťte podrobné analýzy chování při přihlašování a konverze přihlášení.

## <a name="integrate-with-external-user-stores"></a>Integrace s externími uživatelskými obchody

Azure AD B2C poskytuje adresář, který může obsahovat 100 vlastní atributy na uživatele. Můžete však také integrovat s externími systémy. Například použijte Azure AD B2C pro ověřování, ale delegovat na externí řízení vztahů se zákazníky (CRM) nebo databáze loajality zákazníků jako zdroj pravdy pro zákaznická data.

Jiný scénář úložiště externích uživatelů je mít Azure AD B2C zpracovat ověřování pro vaši aplikaci, ale integrovat s externím systémem, který ukládá profil uživatele nebo osobní data. Například pro splnění požadavků na rezidenci dat, jako jsou zásady místního nebo místního úložiště dat.

![Logický diagram Azure AD B2C komunikující s externím úložištěm uživatelů](./media/overview/scenario-remoteprofile.png)

Azure AD B2C můžete usnadnit shromažďování informací od uživatele při registraci nebo úpravy profilu, pak předat data do externího systému. Potom během budoucí ověřování Azure AD B2C můžete načíst data z externího systému a v případě potřeby zahrnout jako součást odpovědi ověřovací token, který odešle do vaší aplikace.

## <a name="progressive-profiling"></a>Progresivní profilování

Další možnost cesty uživatele zahrnuje progresivní profilování. Progresivní profilování umožňuje zákazníkům rychle dokončit svou první transakci shromažďováním minimálního množství informací. Potom postupně shromažďovat další údaje o profilu od zákazníka na budoucí přihlášení.

![Vizuální zobrazení progresivního profilování](./media/overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>Ověření a kontrola identity třetí stranou

Azure AD B2C můžete usnadnit ověřování identity a kontrolu identity shromažďováním uživatelských dat a jejich předáním systému třetí strany k ověření, vyhodnocování důvěryhodnosti a schválení pro vytvoření uživatelského účtu.

![Diagram znázorňující tok uživatele pro ověření identity třetích stran](./media/overview/scenario-idproofing.png)

To jsou jen některé z věcí, které můžete dělat s Azure AD B2C jako vaše business-to-customer identity platformy. V následujících částech tohoto přehledu vás provede ukázková aplikace, která používá Azure AD B2C. Můžete také přejít přímo k podrobnějšímu [technickému přehledu Azure AD B2C](technical-overview.md).

## <a name="example-woodgrove-groceries"></a>Příklad: Potraviny z woodgrove

[WoodGrove Potraviny][woodgrove] je živá webová aplikace vytvořená společností Microsoft k předvedení několika funkcí Azure AD B2C. V několika následujících částech zkontrolujte některé možnosti ověřování poskytované Azure AD B2C na webu WoodGrove.

### <a name="business-overview"></a>Obchodní přehled

WoodGrove je on-line obchod s potravinami, který prodává potraviny jak pro jednotlivé spotřebitele a obchodní zákazníky. Jejich obchodní zákazníci nakupují potraviny jménem své společnosti nebo podniků, které spravují.

### <a name="sign-in-options"></a>Možnosti přihlášení

WoodGrove Potraviny nabízí několik možností přihlášení na základě vztahu svých zákazníků s obchodem:

* **Jednotliví** zákazníci se mohou zaregistrovat nebo přihlásit pomocí jednotlivých účtů, například pomocí poskytovatele sociální identity nebo e-mailové adresy a hesla.
* **Firemní** zákazníci se mohou zaregistrovat nebo přihlásit pomocí svých podnikových přihlašovacích údajů.
* **Partneři** a dodavatelé jsou jednotlivci, kteří dodávají obchodu s potravinami produkty k prodeji. Identitu partnera poskytuje [služba Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).

![Individuální (B2C), obchodní (B2C) a partnerské (B2B) přihlašovací stránky](./media/overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Ověření jednotlivých zákazníků

Když zákazník vybere **Přihlásit se pomocí vašeho osobního účtu**, přesměrována na vlastní přihlašovací stránku hostovanou Azure AD B2C. Můžete vidět na následujícím obrázku, že jsme přizpůsobili uživatelské rozhraní (UI) vypadat a cítit se stejně jako woodgrove potraviny webové stránky. Zákazníci WoodGrove by neměli vědět, že prostředí ověřování je hostované a zabezpečené službou Azure AD B2C.

![Vlastní přihlašovací stránka WoodGrove hostovaná azure ad b2c](./media/overview/sign-in.png)

WoodGrove umožňuje svým zákazníkům zaregistrovat se a přihlásit pomocí svých účtů Google, Facebook nebo Microsoft jako jejich poskytovatele identity. Nebo se mohou zaregistrovat pomocí své e-mailové adresy a hesla k vytvoření takzvaného *místního účtu*.

Když zákazník vybere **Zaregistrovat se pomocí svého osobního účtu** a pak **se zaregistruje ,** zobrazí se mu vlastní registrační stránka.

![Vlastní stránka registrace WoodGrove hostovaná azure ad b2c](./media/overview/sign-up.png)

Po zadání e-mailové adresy a výběru **odeslat ověřovací kód**, Azure AD B2C odešle jim kód. Jakmile zadají svůj kód, vyberte **Ověřit kód**a potom zadejte další informace ve formuláři, musí také souhlasit s podmínkami služby.

Kliknutím na tlačítko **Vytvořit** způsobí, že Azure AD B2C přesměrovat uživatele zpět na web WoodGrove Potraviny. Když přesměruje, Azure AD B2C předá ověřovací token OpenID Connect webové aplikaci WoodGrove. Uživatel je nyní přihlášen a připraven k odeslání, jeho zobrazované jméno je zobrazeno v pravém horním rohu, což znamená, že je přihlášen.

![Záhlaví webových stránek WoodGrove Potraviny zobrazující uživatele je přihlášen](./media/overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>Ověření firemních zákazníků

Když zákazník vybere jednu z možností v rámci **obchodní zákazníky**, WoodGrove Potraviny webu vyvolá jinou zásadu Azure AD B2C, než je tomu u jednotlivých zákazníků.

Tato zásada nabízí uživateli možnost použít jejich podniková pověření pro registraci a přihlášení. V příkladu WoodGrove se uživatelé zobrazí výzva k přihlášení pomocí libovolného účtu Office 365 nebo Azure AD. Tato zásada používá [víceklientskou aplikaci Azure AD](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) a koncový bod `/common` Azure AD k federate Azure AD B2C s libovolným zákazníkem Office 365 na světě.

### <a name="authenticate-partners"></a>Ověření partnerů

Odkaz **Přihlásit se pomocí vašeho dodavatele účtu** používá funkci spolupráce služby Azure Active Directory B2B. Azure AD B2B je rodina funkcí ve službě Azure Active Directory pro správu identit partnerů. Tyto identity můžete federovat z Azure Active Directory pro přístup do aplikací chráněných Azure AD B2C.

Další informace o Azure AD B2B v [co je přístup uživatele typu Host ve službě Azure Active Directory B2B?](../active-directory/b2b/what-is-b2b.md).

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>Další kroky

Teď, když máte představu o tom, co je Azure AD B2C a některé scénáře, které může pomoci s, kopat trochu hlouběji do jeho funkcí a technických aspektů.

> [!div class="nextstepaction"]
> [Technický přehled Azure AD B2C >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo
