---
title: Plánování konfigurace Azure Active Directory mých aplikací
description: Průvodce plánováním pro efektivní používání mých aplikací ve vaší organizaci.
services: active-directory
author: barbaraselden
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: 5184639d8c34be705aeeb691f1cf38486f850673
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543954"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Plánování konfigurace Azure Active Directory mých aplikací

> [!NOTE]
> Tento článek je určený pro odborníky v oblasti IT, kteří potřebují naplánovat konfiguraci portálu moje aplikace své organizace. 
>
> **Dokumentaci pro koncové uživatele najdete v tématu věnovaném [přihlášení a spouštění aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md)**.

Azure Active Directory (Azure AD) Moje aplikace jsou webový portál pro spouštění a správu aplikací. Stránka Moje aplikace poskytuje uživatelům jediné místo, kde můžete začít pracovat, a najít všechny aplikace, ke kterým mají přístup. Uživatelé mají přístup k mým aplikacím na adrese [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>Proč konfigurovat moje aplikace

Portál moje aplikace je ve výchozím nastavení dostupný pro uživatele a nedá se vypnout. Je důležité ho nakonfigurovat tak, aby měli nejlepší možnou činnost a aby portál zůstal užitečný. 

Jakákoli aplikace v seznamu Azure Active Directory podnikové aplikace se zobrazí, pokud jsou splněny obě následující podmínky:

* Vlastnost Visibility pro aplikaci je nastavena na hodnotu true. 

* Aplikace je přiřazena k libovolnému uživateli nebo skupině. Zobrazuje se pro přiřazené uživatele.

Při konfiguraci portálu se zajistí, že uživatelé můžou snadno najít správné aplikace.

 
### <a name="how-is-the-my-apps-portal-used"></a>Jak se používá portál moje aplikace?

Uživatelé přistupují k portálu moje aplikace na:

* Vyhledejte a získejte přístup ke všem aplikacím připojeným k Azure AD, ke kterým mají přístup.

   * Je vhodné zajistit, aby byly aplikace nakonfigurované pro jednotné přihlašování (SSO) a poskytovaly uživatelům nejlepší možnosti.

* Vyžádejte si přístup k novým aplikacím, které jsou nakonfigurované pro samoobslužné služby.

* Vytvářejte osobní kolekce aplikací.

* Správa přístupu k aplikacím jiným uživatelům, kteří mají přiřazenou roli vlastníka skupiny nebo delegovaného ovládacího prvku pro skupinu používanou pro udělení přístupu k aplikacím.

Správci můžou nakonfigurovat:

* [Vyjádření souhlasu](../manage-apps/configure-user-consent.md)  včetně podmínek služby.

* [Samoobslužné zjišťování aplikací a žádosti o přístup](../manage-apps/access-panel-manage-self-service-access.md).

* [Kolekce aplikací](../manage-apps/access-panel-collections.md).

* Přiřazení ikon k aplikacím

* Uživatelsky přívětivé názvy pro aplikace

* Branding společnosti zobrazený v mých aplikacích

 

## <a name="plan-consent-configuration"></a>Konfigurace souhlasu s plánem

### <a name="user-consent-for-applications"></a>Souhlas uživatele pro aplikace 

Předtím, než se uživatel může přihlásit k aplikaci a aplikace bude mít přístup k datům vaší organizace, musí oprávnění aplikace udělit uživatel nebo správce. Můžete nakonfigurovat, jestli je povolený souhlas uživatele, a za jakých podmínek. **Microsoft doporučuje, abyste uživatelům povolili jenom souhlas s aplikacemi od ověřených vydavatelů.**

Další informace najdete v tématu [Konfigurace způsobu souhlasu koncových uživatelů s aplikacemi](../manage-apps/configure-user-consent.md) .

### <a name="group-owner-consent-for-apps-accessing-data"></a>Souhlas vlastníka skupiny pro aplikace, které přistupují k datům

Vlastníci skupiny a týmu můžou autorizovat aplikace, jako jsou například aplikace publikované dodavateli třetích stran, pro přístup k datům vaší organizace, které jsou přidružené ke skupině. Další informace najdete v článku o [souhlasu týkajícím se konkrétního prostředku v Microsoft Teams](https://docs.microsoft.com/microsoftteams/resource-specific-consent) . 

Můžete nakonfigurovat, jestli chcete tuto funkci povolit nebo zakázat.

Další informace najdete v tématu [Konfigurace oprávnění k souhlasu skupin](../manage-apps/configure-user-consent-groups.md).

### <a name="plan-communications"></a>Plán komunikace

Komunikace je zásadní pro úspěch jakékoli nové služby. Proaktivně Informujte uživatele, jak a kdy se jejich prostředí změní a jak v případě potřeby získat podporu.

I když moje aplikace obvykle nevytvářejí problémy s uživatelem, je důležité připravit se. Před spuštěním vytvořte příručky a seznam všech prostředků pro pracovníky podpory.

#### <a name="communications-templates"></a>Šablony komunikace

Microsoft nabízí [přizpůsobitelné šablony pro e-maily a další komunikace](https://aka.ms/APTemplates) pro moje aplikace. Tyto prostředky můžete přizpůsobit pro použití v jiných komunikačních kanálech podle potřeby vaší podnikové jazykové verze.

 

## <a name="plan-your-sso-configuration"></a>Plánování konfigurace jednotného přihlašování

Je nejvhodnější, pokud je jednotné přihlašování povolené pro všechny aplikace na portálu moje aplikace, aby uživatelé měli bezproblémové prostředí, aniž by museli zadávat přihlašovací údaje.

Azure AD podporuje více možností jednotného přihlašování. 

* Další informace najdete v tématu [Možnosti jednotného přihlašování v Azure AD](sso-options.md).

* Další informace o používání služby Azure AD jako zprostředkovatele identity pro aplikaci najdete v části [rychlý start série při správě aplikací](../manage-apps/view-applications-portal.md).

### <a name="use-federated-sso-if-possible"></a>Pokud je to možné, používejte federované jednotné přihlašování.

Pro dosažení optimálního uživatelského prostředí se stránkou moje aplikace Začněte s integrací cloudových aplikací, které jsou k dispozici pro federované jednotné přihlašování (OpenID Connect nebo SAML). Federované jednotné přihlašování umožňuje uživatelům konzistentní možnosti jedním kliknutím na spouštěcích plochách aplikací a v rámci řízení konfigurace je v něm robustnější.

Další informace o tom, jak nakonfigurovat aplikace SaaS (software as a Service) pro jednotné přihlašování, najdete v článku [plán nasazení SaaS SSO].. /Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>Důležité důvody pro zvláštní případy jednotného přihlašování

> [!TIP]
> Pro lepší uživatelské prostředí používejte federované jednotné přihlašování s Azure AD (OpenID Connect/SAML), když je aplikace podporuje, namísto jednotného přihlašování založeného na heslech a ADFS.

Pokud se chcete přihlásit k aplikacím jednotného přihlašování pomocí hesla nebo k aplikacím, které jsou k dispozici v Azure Proxy aplikací služby AD, musí si uživatelé nainstalovat a používat zabezpečené přihlašovací rozšíření moje aplikace. Uživatelům se zobrazí výzva k instalaci rozšíření při prvním spuštění aplikace jednotného přihlašování nebo proxy aplikace založené na heslech. 

![Snímek obrazovky](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

Podrobné informace o rozšíření najdete v tématu [Instalace rozšíření prohlížeče moje aplikace](../user-help/my-apps-portal-end-user-access.md).

Pokud je nutné tyto aplikace integrovat, měli byste definovat mechanismus nasazení rozšíření ve velkém měřítku s využitím [podporovaných prohlížečů](../user-help/my-apps-portal-end-user-access.md). Vaše možnosti jsou:

* [Stažení a konfigurace na základě uživatele pro Chrome, Firefox, Microsoft Edge nebo IE](../user-help/my-apps-portal-end-user-access.md)

* [Configuration Manager pro Internet Explorer](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

Toto rozšíření umožňuje uživatelům spouštět libovolnou aplikaci z jejího panelu hledání, vyhledat přístup k nedávno používaným aplikacím a vytvořit odkaz na stránku Moje aplikace.

![Snímek obrazovky s vyhledáváním rozšíření moje aplikace](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>Plánování mobilního přístupu

Pro aplikace, které používají jednotné přihlašování založené na heslech nebo přistupované pomocí [Microsoft Azure AD proxy aplikací](../manage-apps/application-proxy.md), je nutné použít Microsoft Edge Mobile. Pro ostatní aplikace lze použít libovolný mobilní prohlížeč. 

### <a name="linked-sso"></a>Propojené jednotné přihlašování

Aplikace se dají přidat pomocí propojené možnosti jednotného přihlašování. Můžete nakonfigurovat dlaždici aplikace, která odkazuje na adresu URL vaší existující webové aplikace. Propojené jednotné přihlašování umožňuje zahájit přesměrování uživatelů na portál moje aplikace bez migrace všech aplikací do jednotného přihlašování služby Azure AD. Můžete postupně přecházet na aplikace konfigurované pro jednotné přihlašování služby Azure AD, aniž by došlo k přerušení činnosti uživatelů.

## <a name="plan-the-user-experience"></a>Plánování prostředí uživatele

Ve výchozím nastavení se všechny aplikace, ke kterým má uživatel přístup, a všechny aplikace nakonfigurované pro samoobslužné zjišťování zobrazují na panelu Moje aplikace uživatele. U mnoha organizací to může být velmi rozsáhlý seznam, který se může stát zatěžující, pokud není uspořádaný.

### <a name="plan-my-apps-collections"></a>Plánování kolekcí mých aplikací

Všechny aplikace Azure AD, ke kterým má uživatel přístup, se zobrazí v seznamu Moje aplikace v kolekci všechny aplikace. Pomocí kolekcí můžete seskupovat související aplikace a prezentovat je na samostatné kartě, což usnadňuje jejich vyhledání. Kolekce můžete například použít k vytvoření logických seskupení aplikací pro konkrétní role úlohy, úkoly, projekty a tak dále. 

Koncoví uživatelé mohou také přizpůsobit své prostředí nástrojem

* Vytváření vlastních kolekcí aplikací

* [Skrytí a změna pořadí kolekcí aplikací](access-panel-collections.md)

![Snímek obrazovky s konfigurací samoobslužné služby](./media/my-apps-deployment-plan/collections.png)

Máte možnost skrýt aplikace na portálu moje aplikace, ale pořád povolit přístup z jiných míst, jako je Microsoft 365 portál. Další informace: [skrytí aplikace z uživatelského prostředí v Azure Active Directory](hide-application-from-user-portal.md).

> [!IMPORTANT]
> Přes moje aplikace můžou přistupovat jenom 950 aplikace, ke kterým má uživatel přístup. To zahrnuje aplikace skryté buď uživatelem, nebo správcem. 

### <a name="plan-self-service-group-management-membership"></a>Plánování členství samoobslužné správy skupin

Uživatelům můžete povolit vytváření a správu vlastních skupin zabezpečení nebo skupin Microsoft 365 v Azure AD. Vlastník skupiny může schvalovat nebo zamítnout žádosti o členství a delegovat řízení členství ve skupině. Funkce Samoobslužné správy skupin nejsou k dispozici pro skupiny zabezpečení s povoleným e-mailem ani pro distribuční seznamy.

Pokud chcete naplánovat členství ve skupině samoobslužných služeb, určete, jestli chcete, aby všichni uživatelé ve vaší organizaci mohli vytvářet a spravovat skupiny nebo jenom podmnožiny uživatelů. Pokud povolujete podmnožinu uživatelů, budete muset nastavit skupinu, do které se tyto osoby přidávají. 

Podrobnosti o povolování těchto scénářů najdete [v tématu Nastavení samoobslužné správy skupin v Azure Active Directory](../enterprise-users/groups-self-service-management.md) .

### <a name="plan-self-service-application-access"></a>Plánování přístupu k aplikacím Samoobslužná služba

Uživatelům můžete povolit zjišťování a požadování přístupu k aplikacím přes panel Moje aplikace. Abyste to mohli udělat, musíte nejdřív 

* Povolit samoobslužnou správu skupin

* Povolit aplikaci pro jednotné přihlašování

* vytvořit skupinu pro přístup k aplikaci

![Snímek obrazovky s konfigurací samoobslužné služby Moje aplikace](./media/my-apps-deployment-plan/my-apps-self-service.png)

Když si uživatel vyžádá přístup, požádá o přístup k příslušné skupině a vlastníci skupiny můžou delegovat oprávnění ke správě členství ve skupině, a tím i přístupu k aplikacím. Pracovní postupy schvalování jsou k dispozici pro explicitní schválení přístupu k aplikacím. Uživatelé, kteří jsou schvalovatelé, budou dostávat oznámení na portálu moje aplikace, když se čeká na žádost o přístup k aplikaci.

## <a name="plan-reporting-and-auditing"></a>Plánování generování sestav a auditování

Azure AD poskytuje [sestavy, které nabízejí technickou a obchodní přehledy]. /reports-monitoring/overview-reports.md). Spolupracujte se svými vlastníky firemních a technických aplikací, abyste mohli převzít vlastnictví těchto sestav a pravidelně je spotřebovávat. Následující tabulka uvádí některé příklady typických scénářů generování sestav.

| Příklad| Řízení rizik| Zvýšení produktivity| Zásady správného řízení a dodržování předpisů |
| - | - | - | -|
| Typy sestav| Oprávnění a použití aplikace| Aktivita zřizování účtů| Kontrola přístupu k aplikacím |
| Potenciální akce| Přístup pro audit; odvolat oprávnění| Opravte všechny chyby zřizování.| Odvolat přístup |


Azure AD uchovává většinu dat auditování po dobu 30 dnů. Data jsou k dispozici prostřednictvím portálu pro správu Azure nebo rozhraní API, abyste je mohli stáhnout do svých analytických systémů.

#### <a name="auditing"></a>Auditování

Protokoly auditu pro přístup k aplikaci jsou k dispozici po dobu 30 dnů. Pokud vaše organizace vyžaduje delší dobu uchování, exportujte protokoly do nástroje Event Information and Management (SIEM), jako je například Splunk nebo ArcSight.

V rámci auditování, vytváření sestav a záloh zotavení po havárii si pozdokumentujte požadovanou četnost stahování, co je cílový systém a kdo je zodpovědný za správu jednotlivých záloh. Možná nebudete muset oddělit auditování a zálohy vytváření sestav. Záloha zotavení po havárii by měla být samostatnou entitou.

## <a name="validate-your-deployment"></a>Ověření nasazení

Ujistěte se, že vaše nasazení moje aplikace je důkladně testováno a že je nastaven plán vrácení zpět.

Proveďte následující testy jak u zařízení vlastněných společností, tak z osobních zařízení. Tyto testovací případy by se měly projevit i v případech použití vaší firmy. Následuje několik případů na základě typických technických scénářů. Přidejte jiné, které jsou specifické pro vaše potřeby.

#### <a name="application-sso-access-test-case-examples"></a>Příklady testovacího případu přístupu k aplikaci jednotného přihlašování:


| Obchodní případ| Očekávaný výsledek |
| - | - |
| Uživatel se přihlásí na portál moje aplikace.| Uživatel se může přihlásit a zobrazit své aplikace. |
| Uživatel spustí aplikaci federovaného jednotného přihlašování.| Uživatel se automaticky přihlásí k aplikaci. |
| Uživatel poprvé spustí aplikaci pro jednotné přihlašování pomocí hesla.| Uživatel musí nainstalovat rozšíření moje aplikace. |
| Uživatel spustí aplikaci pro jednotné přihlašování pomocí hesla později.| Uživatel se automaticky přihlásí k aplikaci. |
| Uživatel spustí aplikaci z portálu Microsoft 365.| Uživatel se automaticky přihlásí k aplikaci. |
| Uživatel spustí aplikaci z Managed Browser| Uživatel se automaticky přihlásí k aplikaci. |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Příklady testovacích případů aplikace samoobslužné funkce aplikací


| Obchodní případ| Očekávaný výsledek |
| - | - |
| Uživatel může spravovat členství v aplikaci.| Uživatel může přidat nebo odebrat členy, kteří mají přístup k aplikaci. |
| Uživatel může aplikaci upravit.| Uživatel může upravit popis aplikace a přihlašovací údaje pro aplikace jednotného přihlašování k heslům. |


### <a name="rollback-steps"></a>Kroky vrácení zpět

Je důležité, abyste naplánovali, co dělat, pokud nasazení neprojde jako plánované. Pokud během nasazení dojde k chybě v konfiguraci jednotného přihlašování, musíte pochopit, jak [řešit problémy s jednotným PŘIhlašováním](../hybrid/tshoot-connect-sso.md) a snížit dopad na uživatele. V extrémních případech možná budete muset [vrátit jednotné přihlašování](plan-sso-deployment.md).

## <a name="manage-your-implementation"></a>Správa implementace

K provedení požadované úlohy v Azure Active Directory použijte nejnižší privilegovanou roli. [Projděte si různé role, které jsou k dispozici](../roles/permissions-reference.md) , a podle toho, co potřebujete, můžete pro tuto aplikaci vyřešit vaše potřeby pro každého. Některé role může být nutné použít dočasně a odebrat po dokončení nasazení.

| Osoby| Role| Role Azure AD |
| - | - | - |
| Správce helpdesku| Podpora vrstvy 1| Žádné |
| Správce identit| Konfigurace a ladění v případě problémů ovlivňujících službu Azure AD| Globální správce |
| Správce aplikace| Ověření identity uživatele v aplikaci, konfigurace u uživatelů s oprávněním| Žádné |
| Správci infrastruktury| Vlastník změna certifikátu| Globální správce |
| Vlastník/účastník společnosti| Ověření identity uživatele v aplikaci, konfigurace u uživatelů s oprávněním| Žádné |


Pomocí [Privileged Identity Management](../privileged-identity-management/pim-configure.md) můžete spravovat své role, abyste měli k dispozici další auditování, řízení a kontrolu přístupu pro uživatele s oprávněními k adresáři.

## <a name="next-steps"></a>Další kroky

[Plánování nasazení Multi-Factor Authentication služby Azure AD](../authentication/howto-mfa-getstarted.md)

[Plánování nasazení proxy aplikací](application-proxy-deployment-plan.md)

