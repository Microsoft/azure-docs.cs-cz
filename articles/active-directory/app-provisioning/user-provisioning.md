---
title: Automatické zřizování uživatelů aplikací SaaS ve službě Azure AD | Dokumenty společnosti Microsoft
description: Úvod o tom, jak můžete používat Azure AD automaticky zřazování, zrušení zřizování a průběžnou aktualizaci uživatelských účtů v několika aplikacích SaaS třetích stran.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e828fd9c2561007c332db67bfd0b20dda9b845f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454529"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Automatizace zřizování uživatelů a zrušení zřizování aplikací mandatorním i aktivním schránkou

Ve službě Azure Active Directory (Azure AD) termín **zřizování aplikací** odkazuje na automatické vytváření identit uživatelů a rolí v cloudu[(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)aplikace, které uživatelé potřebují přístup k. Kromě vytváření identit uživatelů zahrnuje automatické zřizování údržbu a odebrání identit uživatelů při změně stavu nebo rolí. Běžné scénáře zahrnují zřizování uživatele Azure AD do aplikací, jako [je Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)a další.

![Diagram přehledu zřizování](./media/user-provisioning/provisioning-overview.png)

Tato funkce umožňuje:

- **Automatizace zřizování**: Automaticky vytvářejte nové účty ve správných systémech pro nové lidi, když se připojí k vašemu týmu nebo organizaci.
- **Automatizace zrušení zřízení:** Automaticky deaktivujte účty ve správných systémech, když lidé opustí tým nebo organizaci.
- **Synchronizace dat mezi systémy:** Ujistěte se, že identity ve vašich aplikacích a systémech jsou průběžně aktuální na základě změn v adresáři nebo systému lidských zdrojů.
- **Zřizovací skupiny:** Zřiďte skupiny aplikacím, které je podporují.
- **Řídit přístup:** Sledujte a auditujte, kdo byl zřízen do vašich aplikací.
- **Bezproblémové nasazení ve scénářích hnědého pole:** Shodujte existující identity mezi systémy a povolte snadnou integraci, i když uživatelé již existují v cílovém systému.
- **Použijte bohaté přizpůsobení:** Využijte výhod přizpůsobitelných mapování atributů, která definují, jaká uživatelská data by měla proudit ze zdrojového systému do cílového systému.
- **Získejte upozornění na kritické události:** Služba zřizování poskytuje výstrahy pro kritické události a umožňuje integraci Log Analytics, kde můžete definovat vlastní výstrahy pro řešení vašich obchodních potřeb.

## <a name="benefits-of-automatic-provisioning"></a>Výhody automatického zřizování

Vzhledem k tomu, že počet aplikací používaných v moderních organizacích stále roste, správci IT mají za úkol správu přístupu ve velkém měřítku. Standardy, jako je například bezpečnostní kontrolní výrazy Značkovací jazyk (SAML) nebo Open ID Connect (OIDC) umožňují správcům rychle nastavit jednotné přihlašování (SSO), ale přístup také vyžaduje, aby uživatelé byli zřízeni do aplikace. Pro mnoho správců znamená zřizování ruční vytvoření každého uživatelského účtu nebo nahrávání souborů CSV každý týden, ale tyto procesy jsou časově náročné, nákladné a náchylné k chybám. Řešení, jako je SAML just-in-time (JIT) byly přijaty k automatizaci zřizování, ale podniky také potřebují řešení pro zrušení zřizování uživatelů, když opustí organizaci nebo již nevyžadují přístup k určitým aplikacím na základě změny role.

Některé běžné motivace pro použití automatickézřivací služby patří:

- Maximalizace efektivity a přesnosti procesů zajišťování.
- Úspora nákladů spojených s hostováním a údržbou vlastních zřizovacích řešení a skriptů.
- Zabezpečení vaší organizace okamžitým odebráním identit uživatelů z klíčových aplikací SaaS při odchodu z organizace.
- Snadný import velkého počtu uživatelů do konkrétní aplikace nebo systému SaaS.
- S jedinou sadou zásad k určení, kdo je zřízena a kdo se může přihlásit k aplikaci.

Zřizování uživatelů Azure AD může pomoci řešit tyto problémy. Další informace o tom, jak zákazníci používají zřizování uživatelů Azure AD, si můžete přečíst [případovou studii ASOS](https://aka.ms/asoscasestudy). Video níže poskytuje přehled zřizování uživatelů ve službě Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Jaké aplikace a systémy lze použít s automatickým zřizováním uživatelů Azure AD?

Azure AD obsahuje předem integrovanou podporu pro mnoho oblíbených aplikací SaaS a systémů lidských zdrojů a obecnou podporu pro aplikace, které implementují konkrétní části [standardu SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Předintegrované aplikace (galerie SaaS aplikace)**. Všechny aplikace, pro které Azure AD podporuje předem integrované zřizovací konektor v [seznamu aplikačních kurzů pro zřizování uživatelů](../saas-apps/tutorial-list.md). Předintegrované aplikace uvedené v galerii obecně používají rozhraní API pro správu uživatelů založené na SCIM 2.0 pro zřizování. 

   ![Logo Salesforce](./media/user-provisioning/gallery-app-logos.png)

   Pokud chcete požádat o novou aplikaci pro zřizování, můžete [požádat, aby vaše aplikace byla integrována s naší galerií aplikací](../develop/howto-app-gallery-listing.md). Pro požadavek zřizování uživatele vyžadujeme, aby aplikace měla koncový bod kompatibilní s SCIM. Požádejte dodavatele aplikace, aby se řídil standardem SCIM, abychom mohli aplikaci rychle připojit k naší platformě.

* **Aplikace, které podporují SCIM 2.0**. Informace o tom, jak obecně připojit aplikace, které implementují rozhraní API pro správu uživatelů založené na SCIM 2.0, naleznete [v tématu Vytvoření koncového bodu SCIM a konfigurace zřizování uživatelů](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Co je systém pro správu identit napříč doménami (SCIM)?

Chcete-li pomoci automatizovat zřizování a deprovisioning, aplikace vystavit proprietární rozhraní API pro uživatele a skupiny. Každý, kdo se pokusil spravovat uživatele ve více než jedné aplikaci, vám však řekne, že každá aplikace se pokusí provést stejné jednoduché akce, jako je vytváření nebo aktualizace uživatelů, přidávání uživatelů do skupin nebo zrušení zřízení uživatelů. Přesto všechny tyto jednoduché akce jsou implementovány jen trochu jinak, pomocí různých cest koncového bodu, různé metody pro určení informací o uživateli a různé schéma představují každý prvek informací.

K řešení těchto problémů specifikace SCIM poskytuje společné uživatelské schéma, které uživatelům pomáhá přesouvat se do aplikací, z nich a kolem nich. SCIM se stává de facto standardem pro zřizování a při použití ve spojení s federačními standardy, jako je SAML nebo OpenID Connect, poskytuje správcům komplexní řešení založené na standardech pro správu přístupu.

Podrobné pokyny k vývoji koncového bodu SCIM pro automatizaci zřizování a zrušení zřizování uživatelů a skupin do aplikace naleznete v [tématu Vytvoření koncového bodu SCIM a konfigurace zřizování uživatelů](use-scim-to-provision-users-and-groups.md). Pro předem integrované aplikace v galerii (Slack, Azure Databricks, Snowflake atd.) můžete přeskočit dokumentaci pro vývojáře a použít kurzy [zde](../saas-apps/tutorial-list.md)uvedené .

## <a name="manual-vs-automatic-provisioning"></a>Ruční vs. automatické zřizování

Aplikace v galerii Azure AD podporují jeden ze dvou režimů zřizování:

* **Ruční** zřizování znamená, že pro aplikaci ještě neexistuje žádný automatický konektor zřizování Azure AD. Uživatelské účty je nutné vytvořit ručně, například přidáním uživatelů přímo na portál pro správu aplikace nebo nahráním tabulky s podrobnostmi o uživatelském účtu. Prostudujte si dokumentaci poskytnutou aplikací nebo se obraťte na vývojáře aplikace a zjistěte, jaké mechanismy jsou k dispozici.

* **Automatické** znamená, že konektor zřizování Azure AD byl vyvinut pro tuto aplikaci. Měli byste postupovat podle kurzu nastavení specifické pro nastavení zřizování pro aplikaci. Kurzy aplikací najdete v [seznamu výukových programů o integraci aplikací SaaS s Azure Active Directory](../saas-apps/tutorial-list.md).

V galerii Azure AD aplikace, které podporují automatické zřizování jsou označeny **zřizování** ikonu. Přepněte do nové galerie náhled uvidíte tyto ikony (v banneru v horní části **stránky Přidat aplikaci**, vyberte odkaz, který říká **Klikněte zde vyzkoušet nové a vylepšené galerie aplikací**).

![Ikona zřizování v galerii aplikací](./media/user-provisioning/browse-gallery.png)

Režim zřizování podporovaný aplikací je také viditelný na kartě **Zřizování** po přidání aplikace do **podnikových aplikací**.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Jak nastavím automatické zřizování aplikace?

Pro předem integrované aplikace uvedené v galerii, podrobný návod je k dispozici pro nastavení automatickézřivací. Podívejte se na [seznam výukových programů pro integrované galerijní aplikace](../saas-apps/tutorial-list.md). Následující video ukazuje, jak nastavit automatické zřizování uživatelů pro SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Pro ostatní aplikace, které podporují SCIM 2.0, postupujte podle pokynů v článku [Vytvořit koncový bod SCIM a nakonfigurovat zřizování uživatelů](use-scim-to-provision-users-and-groups.md).


## <a name="related-articles"></a>Související články

- [Seznam výukových programů o tom, jak integrovat aplikace SaaS](../saas-apps/tutorial-list.md)
- [Přizpůsobení mapování atributů pro zřizování uživatelů](customize-application-attributes.md)
- [Psaní výrazů pro mapování atributů](../app-provisioning/functions-for-customizing-application-data.md)
- [Filtry oborů pro zřizování uživatelů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Vytvoření koncového bodu SCIM a konfigurace zřizování uživatelů](use-scim-to-provision-users-and-groups.md)
- [Přehled rozhraní API synchronizace Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
