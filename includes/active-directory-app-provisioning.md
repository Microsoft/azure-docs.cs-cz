---
ms.openlocfilehash: c400856546142353a7294a03fce6bbff1c258cc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95554626"
---
V Azure Active Directory (Azure AD) pojem **zřizování aplikací** označuje automatické vytváření identit uživatelů a rolí v cloudových aplikacích ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), ke kterým uživatelé potřebují přístup. Kromě vytváření identit uživatelů zahrnuje Automatické zřizování také údržbu a odebírání identit uživatelů při změně stavu nebo rolí. Mezi běžné scénáře patří zřizování uživatelů Azure AD v aplikacích, jako jsou [Dropbox](../articles/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../articles/active-directory/saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../articles/active-directory/saas-apps/servicenow-provisioning-tutorial.md)a další.

![Diagram přehledu zřizování](./media/active-directory-app-provisioning/provisioning-overview.png)

Tato funkce vám umožní:

- **Automatizace zřizování**: automatické vytváření nových účtů v pravém systému pro nové lidi při připojení k vašemu týmu nebo organizaci.
- **Automatické zrušení zřízení:** Automaticky deaktivovat účty ve správných systémech, když lidé odejdou z týmu nebo organizace.
- **Synchronizovat data mezi systémy:** Zajistěte, aby se identity ve vašich aplikacích a systémech aktualizovaly na základě změn v adresáři nebo v systému lidských zdrojů.
- **Zřídit skupiny:** Zřizování skupin pro aplikace, které je podporují.
- **Řídit přístup:** Monitorování a audit, který byl zřízen do vašich aplikací.
- **Bezproblémové nasazení ve scénářích hnědého pole:** Porovnává stávající identity mezi systémy a umožní snadnou integraci, i když uživatelé již existují v cílovém systému.
- **Použít bohatou úpravu:** Využijte přizpůsobitelných mapování atributů, která definují, jaká uživatelská data by se měla přesměrovat ze zdrojového systému do cílového systému.
- **Získat výstrahy pro kritické události:** Služba zřizování poskytuje výstrahy pro kritické události a umožňuje Log Analytics integraci, kde můžete definovat vlastní výstrahy pro potřeby vašich obchodních potřeb.

## <a name="benefits-of-automatic-provisioning"></a>Výhody automatického zřizování

Vzhledem k tomu, že počet aplikací používaných v moderních organizacích stále roste, správci IT mají na dosahu řízení přístupu ve velkém měřítku. Standardy, jako je například SAML (Security Assert Markup Language) nebo Open ID Connect (OIDC), umožňují správcům rychle nastavit jednotné přihlašování (SSO), ale přístup také vyžaduje, aby se do aplikace zřídili uživatelé. U mnoha správců zajišťuje zřizování ruční vytváření všech uživatelských účtů nebo nahrávání souborů CSV každý týden, ale tyto procesy jsou časově náročné, nákladné a náchylné k chybám. Byla přijata řešení, jako je JIT (just-in-time) SAML pro automatizaci zřizování, ale podniky také potřebují řešení pro zrušení zřízení uživatelů při opuštění organizace nebo už nevyžadují přístup k určitým aplikacím na základě změny role.

Mezi běžné motivace pro použití automatického zřizování patří:

- Maximalizace efektivity a přesnosti zřizování procesů.
- Úspora nákladů spojených s hostováním a udržováním vlastních řešení a skriptů pro zřizování s vlastním vývojem
- Zabezpečení organizace tím, že se okamžitě odeberou identity uživatelů z aplikací služby Key SaaS, když odejdou z organizace.
- Snadné importování velkého počtu uživatelů do konkrétní SaaS aplikace nebo systému.
- Pomocí jedné sady zásad určíte, kdo je zřízený a kdo se může přihlásit k aplikaci.

Zřizování uživatelů Azure AD může tyto výzvy vyřešit. Pokud chcete získat další informace o tom, jak zákazníci používají zřizování uživatelů Azure AD, můžete si přečíst [případovou studii Asos](https://aka.ms/asoscasestudy). Následující video poskytuje přehled zřizování uživatelů v Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Jaké aplikace a systémy je možné používat s automatickým zřizováním uživatelů Azure AD?

Azure AD nabízí předem integrovanou podporu pro spoustu oblíbených aplikací SaaS a systémů lidských zdrojů a obecnou podporu pro aplikace, které implementují určité části [standardu SCIM 2,0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Předem integrované aplikace (Galerie aplikací SaaS)**. Všechny aplikace, pro které Azure AD podporuje předem integrovaný zřizovací konektor, najdete v [seznamu kurzů aplikací pro zřizování uživatelů](../articles/active-directory/saas-apps/tutorial-list.md). Předem integrované aplikace uvedené v galerii obecně používají rozhraní API pro správu uživatelů na bázi SCIM 2,0 ke zřízení. 

   ![Logo Salesforce](./media/active-directory-app-provisioning/gallery-app-logos.png)

   Pokud chcete požádat o novou aplikaci pro zřizování, můžete [požádat o integraci aplikace do naší galerie aplikací](../articles/active-directory/develop/v2-howto-app-gallery-listing.md). Pro požadavek na zřízení uživatele vyžaduje aplikace, aby měl koncový bod kompatibilní s SCIM. Požádejte prosím, aby dodavatel aplikace následoval za standardu SCIM, abychom mohli rychle připojit aplikaci k naší platformě.

* **Aplikace, které podporují SCIM 2,0**. Informace o tom, jak obecně připojit aplikace, které implementují rozhraní API pro správu uživatelů na bázi SCIM 2,0, najdete v tématu [Vytvoření koncového bodu SCIM a konfigurace zřizování uživatelů](../articles/active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Co je systém pro správu identit mezi doménami (SCIM)?

Aby se usnadnilo automatizace zřizování a rušení, aplikace zveřejňují proprietární rozhraní API pro uživatele a skupiny. Kdokoli, kdo se snaží spravovat uživatele ve více než jedné aplikaci, vám ale oznámí, že se každá aplikace pokusí provést stejné jednoduché akce, třeba při vytváření nebo aktualizaci uživatelů, přidávání uživatelů do skupin nebo rušení zřizování uživatelů. Nicméně všechny tyto jednoduché akce jsou implementovány pouze trochu odlišně, s použitím různých cest koncových bodů, různých metod pro zadání informací o uživateli a jiného schématu, které představují jednotlivé prvky informací.

Specifikace SCIM poskytuje společné uživatelské schéma, které uživatelům umožňuje přesunout se do aplikací, mimo jiné a kolem nich. SCIM se stává jako de facto standard pro zřizování a při použití ve spojení s federačními standardy, jako je SAML nebo OpenID Connect, poskytuje správcům ucelené řešení založené na standardech pro správu přístupu.

Podrobné pokyny k vývoji SCIM koncového bodu pro automatizaci zřizování a rušení zřizování uživatelů a skupin do aplikace najdete v tématu [Vytvoření koncového bodu SCIM a konfigurace zřizování uživatelů](../articles/active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md). V případě předem integrovaných aplikací v galerii (časová rezerva, Azure Databricks, Snowflake atd.) můžete přeskočit dokumentaci pro vývojáře a použít [zde](../articles/active-directory/saas-apps/tutorial-list.md)uvedené kurzy.

## <a name="manual-vs-automatic-provisioning"></a>Ruční vs. automatické zřizování

Aplikace v galerii Azure AD podporují jeden ze dvou režimů zřizování:

* **Ruční** zřizování znamená, že zatím není pro aplikaci k dispozici žádný automatický konektor pro zřizování Azure AD. Uživatelské účty se musí vytvořit ručně, například přidáním uživatelů přímo do portálu pro správu aplikace nebo nahráním tabulky s podrobnostmi o uživatelském účtu. Projděte si dokumentaci poskytovanou aplikací nebo se obraťte na vývojáře aplikace a zjistěte, jaké mechanismy jsou k dispozici.

* **Automaticky** znamená, že se pro tuto aplikaci vyvinul konektor zřizování služby Azure AD. Měli byste postupovat podle kurzu nastavení, který je specifický pro nastavení zřizování pro aplikaci. Kurzy aplikací najdete v tématu [Seznam kurzů, jak integrovat aplikace SaaS s Azure Active Directory](../articles/active-directory/saas-apps/tutorial-list.md).

V galerii Azure AD jsou aplikace, které podporují Automatické zřizování, označeny ikonou **zřizování** . Přepněte na nové prostředí verze Preview, abyste viděli tyto ikony (v horní části **stránky přidat aplikaci** vyberete odkaz, který zobrazíte **kliknutím sem, abyste si vyzkoušeli novou a vylepšenou galerii aplikací**).

![Ikona zřizování v galerii aplikací](./media/active-directory-app-provisioning/browse-gallery.png)

Režim zřizování podporovaný aplikací je na kartě **zřizování** zobrazen také po přidání aplikace do **podnikových aplikací**.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Návody nastavit Automatické zřizování pro aplikaci?

V případě předem integrovaných aplikací uvedených v galerii jsou podrobné pokyny k dispozici pro nastavení automatického zřizování. Podívejte se na [Seznam kurzů pro integrované aplikace Galerie](../articles/active-directory/saas-apps/tutorial-list.md). Následující video ukazuje, jak nastavit Automatické zřizování uživatelů pro SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Pro jiné aplikace, které podporují SCIM 2,0, postupujte podle kroků v článku [Vytvoření koncového bodu SCIM a konfigurace zřizování uživatelů](../articles/active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md).