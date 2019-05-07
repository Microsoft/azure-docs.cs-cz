---
title: Identita a přístup osvědčené postupy zabezpečení Azure | Dokumentace Microsoftu
description: Tento článek poskytuje sadu osvědčených postupů pro správu identit a přístupu k řízení pomocí integrované možnosti Azure.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2019
ms.author: barclayn
ms.openlocfilehash: 2a669f5b46db4d5de7d1d6863b94e6c117667aee
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153245"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure správu identit a řízení přístupu osvědčené postupy zabezpečení
V tomto článku se podíváme na kolekci správy identit Azure a osvědčené postupy pro zabezpečení řízení přístupu. Tyto osvědčené postupy, které jsou odvozeny z našich zkušenostech s [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) a prostředí zákazníků, jako je sami.

Pro každý osvědčeným postupem je vám vysvětlíme:

* Jaký je doporučený postup
* Proč chcete povolit tento osvědčený postup
* Pokud chcete povolit osvědčený postup, co mohou být způsobeny
* Je to možné alternativy doporučené postupy
* Jak lze zjistíte, jak povolit osvědčený postup

Tento správy identit Azure a zabezpečení řízení přístupu, článek o osvědčených postupech je založena na názor shody a možnostech platformy Azure a sady funkcí, protože existují v době byl napsán v tomto článku. Názory a technologie v průběhu času měnit a tento článek bude aktualizován v pravidelných intervalech, aby tyto změny projevily.

Identita Azure správu nebo přístupová ovládací prvek osvědčené postupy zabezpečení popsané v tomto článku zahrnují:

* Nakládat jako primárního obvodu zabezpečení identit
* Centralizujte si správu identit
* Správa připojených klientů
* Povolit jednotné přihlašování
* Zapnout podmíněný přístup
* Povolení správy hesel
* Vynutit ověřování Multi-Factor Authentication pro uživatele
* Použití řízení přístupu na základě rolí
* Snižuje pravděpodobnost vyzrazení privilegované účty
* Řízení umístění, kde jsou umístěny prostředky
* Použití Azure AD pro ověření úložiště

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Nakládat jako primárního obvodu zabezpečení identit

Mnoho vezměte v úvahu identity jako primárního obvodu zabezpečení. Je to odklon od tradičního zaměřením na zabezpečení sítě. Sítě perimetry dále zobrazovat více porézní a že hraniční obrana nemůže být nejnižších před obrovské množství nových [BYOD](https://aka.ms/byodcg) zařízení a cloudové aplikace.

[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) je řešení Azure pro správu identit a přístupů. Azure AD je víceklientské, založené na cloudu adresáři a identitami služba pro správu od Microsoftu. Kombinuje základní adresářové služby, správu přístupu k aplikacím a ochranu identity v rámci jednoho řešení.

Osvědčené postupy pro zabezpečení přístupu a identit pomocí Azure AD v následujících částech.

## <a name="centralize-identity-management"></a>Centralizujte si správu identit

V [hybridní identita](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) scénář vám doporučujeme integraci místních a cloudových adresářích. Integrace umožňuje týmu IT ke správě účtů z jednoho místa, bez ohledu na to, kde se vytvoří účet. Integrace také pomáhá uživatelům se vyšší produktivitu poskytnutím společné identity pro přístup ke cloudovým i místním prostředkům.

**Osvědčený postup**: Vytvoření jedné instance služby Azure AD. Konzistence a jeden autoritativního zdroje se zvýšit přehlednost a snižování rizik zabezpečení z lidské chyby a složitost konfigurace.
**Podrobnosti o**: Určit jedné službě Azure AD directory jako autoritativního zdroje pro podnikové i organizační účty.

**Osvědčený postup**: Integrace místních adresářů s Azure AD.  
**Podrobnosti o**: Použití [Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md) k synchronizaci vašeho místního adresáře s adresářem cloudu.

> [!Note]
> Existují [faktorů ovlivňujících výkon služby Azure AD Connect](../active-directory/hybrid/plan-connect-performance-factors.md). Ujistěte se, že Azure AD Connect má dostatečnou kapacitu, abyste zachovat nevedou podle očekávání pro systémy od Včerejší zabezpečení a produktivitu. Velké a složité organizace (organizace zřizování více než 100 000 objektů) by měly dodržovat [doporučení](../active-directory/hybrid/whatis-hybrid-identity.md) optimalizovat jejich implementaci služby Azure AD Connect.

**Osvědčený postup**: Nesynchronizovat účty do služby Azure AD s vysokými oprávněními v existující instanci služby Active Directory.
**Podrobnosti o**: Neměnit výchozí [konfiguraci služby Azure AD Connect](../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) , který filtruje tyto účty. Tato konfigurace snižuje riziko nežádoucí osoby přesun z cloudu na místní prostředky (vytvořit hlavní incidentů).

**Osvědčený postup**: Zapnutí synchronizace hodnot hash hesel.  
**Podrobnosti o**: Synchronizace hodnot hash hesel je funkce používané k synchronizaci uživatelů hodnot hash hesel z místní instance Active Directory do Azure založené na cloudu instancí AD. Tato synchronizace pomáhá chránit před uniklými přihlašovacími údaji se znovu přehrát předchozí útoky.

I když se rozhodnete federace pomocí služby Active Directory Federation Services (AD FS) nebo u jiných poskytovatelů identit, můžete volitelně nastavit synchronizaci hodnot hash hesel jako záložní v případě místních serverů selhat nebo být dočasně nedostupné. Tato synchronizace umožňuje uživatelům přihlásit se ke službě s použitím stejné heslo, které používají pro přihlášení k jejich místní instancí Active Directory. Umožňuje také k detekci zneužití přihlašovacích údajů porovnáním hodnot hash hesel pro synchronizovaného s hesly známé došlo k narušení, pokud uživatel použil stejnou e-mailovou adresu a heslo na jiné služby, které nejsou připojené ke službě Azure AD Identity Protection.

Další informace najdete v tématu [implementace synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

**Osvědčený postup**: Pro nový vývoj aplikací pomocí Azure AD pro ověřování.
**Podrobnosti o**: Použití správné možnosti pro podporu ověřování:

  - Azure AD pro zaměstnance
  - [Azure AD s B2B](https://docs.microsoft.com/azure/active-directory/b2b/) pro hosty a externí partnery
  - [Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/) řídit, jak zákazníci zaregistrovat, přihlaste se a při používání vašich aplikací spravují své profily

Organizace, které není integrovat svoje místní identity s jejich cloudové identity můžete mít další režii při správě účtů. Tato režie se zvyšuje pravděpodobnost chyb a porušení zabezpečení.

> [!Note]
> Musíte zvolit adresáře, které důležité účty budou umístěné v a určuje, zda je pracovní stanice správce používá spravované nových cloudových služeb nebo existující zpracovává. Použití existující správy a procesy pro zřizování identity můžete snížit rizika, ale můžete vytvořit také riziko útočník tím bylo narušeno místní účet a přesun do cloudu. Můžete chtít použít jinou strategii pro různé role (například správce IT a obchodní jednotky admins). Máte dvě možnosti. První možností je vytvořit účty Azure AD, které nejsou synchronizované s vaší místní instancí Active Directory. Připojte se k vaší pracovní stanice správce ke službě Azure AD, která můžete spravovat a oprava pomocí Microsoft Intune. Druhou možností je používat existující účty správce synchronizací instanci aplikace místní služby Active Directory. Použijte stávající pracovní stanice pro správu a zabezpečení ve vaší doméně služby Active Directory.

## <a name="manage-connected-tenants"></a>Správa připojených klientů
Zabezpečení organizace potřebuje viditelnost se posoudí ohrožení a na zjištění, zda jsou dodržovány zásady vaší organizace a zákonné požadavky. Měli byste zajistit, že má vaše organizace zabezpečení dohled nad všemi předplatnými připojení pro vaše produkční prostředí a síť (prostřednictvím [Azure ExpressRoute](../expressroute/expressroute-introduction.md) nebo [site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). A [globální správce/správce společnosti](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) ve službě Azure AD, můžete zvýšit jejich přístup k [správce uživatelských přístupů](../role-based-access-control/built-in-roles.md#user-access-administrator) role a zobrazit všechna předplatná a spravované skupiny, které jsou připojené k vašemu prostředí.

Zobrazit [zvýšení úrovně přístupu ke správě všech předplatných Azure a skupiny pro správu](../role-based-access-control/elevate-access-global-admin.md) Ujistěte se, že vám a vaší skupiny zabezpečení můžete zobrazit všechna předplatná nebo skupiny pro správu připojené k vašemu prostředí. Po posouzení rizik, měli byste odebrat tento přístup se zvýšeným oprávněním.

## <a name="enable-single-sign-on"></a>Povolit jednotné přihlašování

Ve světě upřednostňujícím mobilní a cloud na prvním chcete povolit jednotné přihlašování (SSO) k zařízení, aplikacím a službám odkudkoli tak vaši uživatelé byli produktivní, kdykoli a kdekoli. Až budete mít více identit řešení pro správu, toto řešení pro správu problém není pouze pro IT, ale také pro uživatele, kteří musí pamatovat více hesel.

S využitím stejného řešení identit pro všechny aplikace a prostředky, můžete dosáhnout jednotného přihlašování. A vaši uživatelé můžete použít stejnou sadu pověření k přihlášení a přístup k prostředkům, které potřebují, zda jsou prostředky v místním nebo v cloudu.

**Osvědčený postup**: Povolení jednotného přihlašování.  
**Podrobnosti o**: Azure AD [rozšiřuje místní služby Active Directory](../active-directory/connect/active-directory-aadconnect.md) do cloudu. Uživatelé můžou používat svůj primární pracovní nebo školní účet pro svá zařízení připojená k doméně, prostředkům společnosti a všechny webové a aplikacím SaaS, které potřebují ke své práci. Uživatelé nebudou muset pamatovat více sad uživatelských jmen a hesel a jejich přístup k aplikaci může být automaticky zřídit (nebo zrušení) na základě jejich členství ve skupinách organizace a jejich stav jako zaměstnanec. Přístup můžete řídit u aplikací z galerie i u vlastních místních aplikací, které jste nasadili a publikovali přes [Proxy aplikací služby Azure AD](../active-directory/active-directory-application-proxy-get-started.md).

Používat jednotné přihlašování umožňuje uživatelům přístup k jejich [aplikací SaaS](../active-directory/active-directory-appssoaccess-whatis.md) podle svého pracovního nebo školního účtu ve službě Azure AD. To platí nejen pro aplikace SaaS společnosti Microsoft, ale také další aplikace, jako například [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) a [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). Můžete nakonfigurovat aplikace pro používání služby Azure AD jako [identity založené na SAML](../active-directory/fundamentals-identity.md) zprostředkovatele. Jako ovládací prvek zabezpečení Azure AD bez vyvolání token, který umožňuje uživatelům umožní přihlásit k aplikaci, není-li, kterým byl udělen přístup prostřednictvím služby Azure AD. Můžete udělit přístup přímo nebo prostřednictvím skupiny, že uživatelé jsou členy.

Organizace, které pro ně nevytvoříte společnou identitu pro navázání jednotného přihlašování pro svoje uživatele a aplikace jsou vystaveny další scénáře, kdy uživatelé mají více hesel. Tyto scénáře zvýšit pravděpodobnost, že uživatelé opětovné použití hesla nebo používání Slabá hesla.

## <a name="turn-on-conditional-access"></a>Zapnout podmíněný přístup

Uživatelé můžou přístup k prostředkům vaší organizace pomocí celé řady zařízení a aplikací z libovolného místa. Jako správce IT budete chtít Ujistěte se, že tato zařízení splňují vaše standardy zabezpečení a dodržování předpisů. Právě se zaměříte na to, kdo má přístup k prostředku dostatečná už není.

Chcete-li zůstatek, zabezpečení a produktivitu, rozmyslete si, jak prostředek přistupuje, než provedete rozhodnutí o řízení přístupu. Tento požadavek můžete vyřešit pomocí podmíněného přístupu služby Azure AD. S podmíněným přístupem můžete provádět automatizované rozhodovat o přístupu na základě podmínek pro přístup k vašim cloudovým aplikacím.

**Osvědčený postup**: Správa a řízení přístupu k firemním prostředkům.  
**Podrobnosti o**: Konfigurace služby Azure AD [podmíněného přístupu](../active-directory/active-directory-conditional-access-azure-portal.md) na základě skupin, umístění a "choulostivosti" aplikace pro aplikace SaaS a aplikace Azure AD – připojené.

**Osvědčený postup**: Blokování starších verzí ověřovacích protokolů.
**Podrobnosti o**: Útočníci zneužít slabá místa ve starším protokolům každý den, hlavně pro útoky heslo zařízení. Konfigurace podmíněného přístupu k blokování starších verzí protokolů. Podívejte se na video [Azure AD: Co Ano pro a proti](https://www.youtube.com/watch?v=wGk0J4z90GI) Další informace.

## <a name="enable-password-management"></a>Povolení správy hesel

Pokud máte více tenantů nebo chcete uživatelům umožnit [resetovat vlastní hesla](../active-directory/active-directory-passwords-update-your-own-password.md), je důležité použít vhodná bezpečnostní zásady zamezilo.

**Osvědčený postup**: Nastavení samoobslužného resetování hesla (SSPR) pro vaše uživatele.  
**Podrobnosti o**: Používání Azure AD [samoobslužné resetování hesla](../active-directory-b2c/active-directory-b2c-reference-sspr.md) funkce.

**Osvědčený postup**: Monitorování jak nebo jestli samoobslužné resetování HESLA je doopravdy používá.  
**Podrobnosti o**: Monitorování uživatelů, kteří jsou registraci pomocí služby Azure AD [sestava aktivit registrace resetování hesla](../active-directory/active-directory-passwords-get-insights.md). Funkci vytváření sestav, které poskytuje Azure AD pomáhá lze zodpovědět otázky pomocí předem připravených sestav. Pokud máte správně licenci, můžete také vytvořit vlastní dotazy.

**Osvědčený postup**: Rozšíření zásady pro hesla založené na cloudu na místní infrastrukturu.
**Podrobnosti o**: Vylepšení zásady pro hesla ve vaší organizaci tím, že provádí stejné kontroly pro změny hesla v místním, stejně jako pro změny hesla založené na cloudu. Nainstalujte [ochrana hesel Azure AD](../active-directory/authentication/concept-password-ban-bad.md) pro Windows Server Active Directory agentů místní rozšířit seznamy zakázaných hesel vaší stávající infrastruktuře. Uživatelé a správci, kteří změnit, nastavit nebo resetovat hesla, v místním se vyžadují k zajištění souladu se stejnými zásadami heslo jako uživatelů pouze cloudu.

## <a name="enforce-multi-factor-verification-for-users"></a>Vynutit ověřování Multi-Factor Authentication pro uživatele

Doporučujeme, abyste vyžadování dvoustupňového ověřování pro všechny vaše uživatele. To zahrnuje správce a ostatní uživatele ve vaší organizaci, který může mít významný dopad v případě ohrožení bezpečnosti účtu (například finanční vedoucí pracovníci pověření ochranou).

Vyžadováním dvoustupňového ověřování několika způsoby. Pro vás nejlepší možností závisí na vašich cílů, edice Azure AD, kterou používáte a licenční program. Zobrazit [vyžadování dvoustupňového ověřování pro uživatele](../active-directory/authentication/howto-mfa-userstates.md) určit pro vás nejlepší možností. Zobrazit [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) a [ověřování Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) ceny stránky pro další informace o licencích a cenách.

Níže jsou uvedeny možnosti a výhody pro povolení dvoustupňové ověřování:

**Možnost 1**: [Povolení služby Multi-Factor Authentication tak, že změníte stav uživatele](../active-directory/authentication/howto-mfa-userstates.md).   
**Výhoda**: Toto je tradiční metody pro vyžadování dvoustupňového ověření. Funguje s oběma [ověřování Azure Multi-Factor Authentication v cloudu a Azure Multi-Factor Authentication Server](../active-directory/authentication/concept-mfa-whichversion.md). Pomocí této metody vyžaduje, aby uživatelé provést dvoustupňové ověřování pokaždé, když se přihlásí a přepisuje zásady podmíněného přístupu.

Pokud chcete zjistit, kde musí být povoleno ověřování Multi-Factor Authentication, naleznete v tématu [kterou verzi Azure MFA je nejvhodnější pro moji organizaci?](../active-directory/authentication/concept-mfa-whichversion.md).

**Možnost 2**: [Povolení služby Multi-Factor Authentication se zásadami podmíněného přístupu](../active-directory/authentication/howto-mfa-getstarted.md).
**Výhoda**: Tato možnost umožňuje výzvu k zadání dvoustupňové ověření za určitých podmínek pomocí [podmíněného přístupu](../active-directory/active-directory-conditional-access-azure-portal.md). Přihlášení uživatele z různých míst, nedůvěryhodné zařízení nebo aplikace, které považujete za rizikové může být konkrétní podmínky. Definování zvláštní podmínky, kdy potřebujete dvoustupňové ověřování, lze předcházet konstanty s výzvou pro uživatele, které je možné rozluštit nepříjemnou uživatelské prostředí.

Toto je nejflexibilnější způsob, jak povolit dvoustupňové ověřování pro vaše uživatele. Povolení zásad podmíněného přístupu se dá použít jenom pro ověřování Azure Multi-Factor Authentication v cloudu a je Prémiová funkce služby Azure AD. Můžete najít další informace o této metodě v [nasazení cloudového ověřování Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md).

**Možnost 3**: Povolení služby Multi-Factor Authentication pomocí zásad podmíněného přístupu vyhodnocením riziko uživatele a přihlašování [Azure AD Identity Protection](../active-directory/authentication/tutorial-risk-based-sspr-mfa.md).   
**Výhoda**: Tato možnost vám umožní:

- Zjistit potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci.
- Nakonfigurujte automatické odpovědi na zjištěné podezřelé akce, které se vztahují na identity vaší organizace.
- Prozkoumat podezřelé incidenty a přijmout vhodná opatření k jejich řešení.

Tato metoda používá Azure AD Identity Protection rizika hodnocení k určení, zda bude vyžadovat dvoustupňové ověření na základě uživatele a riziko přihlášení pro všechny cloudové aplikace. Tato metoda vyžaduje licencování Azure Active Directory P2. Můžete najít další informace o této metodě v [Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md).

> [!Note]
> Možnost 1, povolování služby Multi-Factor Authentication tak, že změníte stav uživatele přepisuje zásady podmíněného přístupu. Protože možnosti 2 a 3 používají zásady podmíněného přístupu, možnost 1 nelze použít s nimi.

Organizace, které nepřidávají další vrstvy ochrany identit, jako je například dvoustupňové ověřování, budou náchylnější k útoku krádeží přihlašovacích údajů. Útoku krádeží přihlašovacích údajů může vést k ohrožení dat.

## <a name="use-role-based-access-control"></a>Použití řízení přístupu na základě rolí
Je důležité pro každou organizaci, který používá cloudovou správu přístupu k prostředkům cloudu. [Řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md) vám pomůže spravovat, kdo má přístup k prostředkům Azure, co můžete provádět pomocí těchto prostředků a oblasti, které mají přístup.

Určování skupin nebo k jednotlivým rolím za konkrétní funkce v Azure pomáhá zabránit nejasnostem, který může mít za následek lidské a automatizace chyb, které vytvořit bezpečnostní rizika. Omezení přístupu na základě [znát](https://en.wikipedia.org/wiki/Need_to_know) a [nejnižší možná oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) principů zabezpečení je nutné pro organizace, které chcete vynutit zásady zabezpečení pro přístup k datům.

Bezpečnostní tým potřebuje vhled do vašich prostředků Azure, aby bylo možné vyhodnotit a opravovat rizika. Pokud bezpečnostní tým má provozní odpovědnosti, které potřebují další oprávnění ke své práci.

Můžete použít [RBAC](../role-based-access-control/overview.md) přiřadit oprávnění pro uživatele, skupiny nebo aplikace v určitém rozsahu. Obor přiřazení role může být předplatné, skupinu prostředků nebo jediný prostředek.

**Osvědčený postup**: V rámci týmu oddělit a udělit pouze takovou úroveň přístupu pro uživatele, kteří potřebují k provádění svých úloh. Ne všichni poskytuje neomezená oprávnění v předplatném Azure nebo prostředky, povolit pouze určité akce v určitém rozsahu.
**Podrobnosti o**: Použití [předdefinované role RBAC](../role-based-access-control/built-in-roles.md) v Azure a přiřadit oprávnění pro uživatele.

> [!Note]
> Konkrétní oprávnění vytvořit zbytečné složitosti a předešli nedorozuměním kumulování do "starší" konfigurace, které se obtížně opravit bez obav z přerušení něco. Vyhněte se oprávnění specifických pro prostředky. Místo toho použijte skupiny pro správu pro oprávnění na úrovni podniku a skupiny prostředků pro oprávnění v rámci předplatných. Vyhněte se uživatelská oprávnění. Místo toho přiřadíte přístup do skupin ve službě Azure AD.

**Osvědčený postup**: Udělit týmu zabezpečení s přístupem k Azure odpovědnosti zobrazíte prostředky Azure, abyste mohli vyhodnotit a odstranit rizika.
**Podrobnosti o**: Udělit zabezpečení týmy RBAC [Čtenář zabezpečení](../role-based-access-control/built-in-roles.md#security-reader) role. Můžete použít skupinu root management nebo segment skupiny pro správu, v závislosti na rozsahu odpovědnosti:

- **Skupina serveru root management** pro týmy zodpovědné za všem podnikovým prostředkům
- **Skupina pro správu segmentu** pro týmy pracující s omezeným oborem (obvykle kvůli zákonné nebo jiné organizační hranice)

**Osvědčený postup**: Udělte příslušná oprávnění k zabezpečení týmy, které mají přímý odpovědnost.
**Podrobnosti o**: Projděte si předdefinované role RBAC pro příslušnou roli přiřazení. Pokud předdefinované role není splnili specifické požadavky vaší organizace, můžete vytvořit [vlastních rolí pro prostředky Azure](../role-based-access-control/custom-roles.md). Pomocí předdefinované role, můžete na uživatele, skupiny nebo instanční objekty na předplatné, skupinu prostředků a prostředků obory přiřazení vlastních rolí.

**Osvědčené postupy**: Grant Azure Security Center přístup k rolím zabezpečení, které potřebujete. Security Center umožňuje týmům rychle identifikovat a opravit rizika zabezpečení.
**Podrobnosti o**: Přidání týmů zabezpečení se tyto požadavky RBAC [správce zabezpečení](../role-based-access-control/built-in-roles.md#security-admin) roli, mohou zobrazit zásady zabezpečení, zobrazení zabezpečení stavy, upravit zásady zabezpečení, zobrazení výstrah a doporučení a zavřít upozornění a doporučení. Můžete to provést pomocí skupiny pro správu kořenového nebo segment skupiny pro správu, v závislosti na rozsahu odpovědnosti.

Organizace, které nevynucují díky funkcím, jako je RBAC může poskytuje více oprávnění než nezbytné pro své uživatele data řízení přístupu. To může vést k ohrožení dat tím, že uživatelé pro přístup k typy dat (například vysoký dopad na chod firmy), které by neměly mít.

## <a name="lower-exposure-of-privileged-accounts"></a>Snižuje pravděpodobnost vyzrazení privilegované účty

Zabezpečení privilegovaného přístupu je zásadním prvním krokem při ochraně podnikových prostředků. Minimalizace počtu uživatelů, kteří mají přístup k zabezpečené informace nebo materiály omezíte možnost uživatel se zlými úmysly získat přístup nebo autorizovaný uživatel neúmyslně by to ovlivnilo citlivých prostředků.

Privilegované účty jsou účty, které spravují a řídí systémy IT. Internetoví útočníci cílí na tyto účty k získání přístupu k datům a systémům v organizaci. K zabezpečení privilegovaného přístupu, měli izolovat účty a systémy před rizikem napadení se zlými úmysly.

Doporučujeme ji vyvíjet a postupujte podle plánu zabezpečení privilegovaného přístupu k proti kybernetických útoků. Informace o vytváření podrobný návod k zabezpečení identit a přístupu, které jsou spravované nebo hlášeny ve službě Azure AD, Microsoft Azure, Office 365 a jiných cloudových službách můžete zkontrolovat [k zabezpečení privilegovaného přístupu pro hybridními a cloudovými nasazeními v Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

Následující shrnuje osvědčené postupy, které jsou součástí [k zabezpečení privilegovaného přístupu pro hybridní a cloudové nasazení ve službě Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md):

**Osvědčený postup**: Spravovat, řídit a monitorovat přístup k privilegovaným účtům.   
**Podrobnosti o**: Zapnout [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md). Po zapnutí Privileged Identity Management, obdržíte oznámení e-mailové zprávy pro privilegovaný přístup změny role. Tato oznámení poskytovat včasné upozornění při přidání dalších uživatelů vysoce privilegované role ve vašem adresáři.

**Osvědčený postup**: Zkontrolujte všechny účty správců kritické jsou spravované účty Azure AD.
**Podrobnosti o**: Odeberte všechny účty uživatelů z rolí kritické správce (například účty Microsoft jako je hotmail.com, live.com nebo outlook.com).

**Osvědčený postup**: Zkontrolujte, že všechny role správce důležité mít samostatný účet pro úlohy správy předejdete tak útoků phishing a dalších útoků ohrozit oprávnění správce.
**Podrobnosti o**: Vytvoření samostatné účty správce, která má přiřazena oprávnění nutná k provádění úloh správy. Blokuje použití těchto účtů pro správu pro denní kancelářským nástrojům, jako je Microsoft Office 365 e-mailu nebo procházení libovolného webu.

**Osvědčený postup**: Identifikujte a kategorizovat účty, které jsou v vysoce privilegovaných rolích.   
**Podrobnosti o**: Po zapnutí Azure AD Privileged Identity Management, můžete zobrazte uživatele, kteří jsou v globální správce, správce privilegovaných rolí a dalších vysoce privilegované role. Odeberte všechny účty, které už nejsou potřeba v těchto rolích a kategorizaci zbývajících účtů, která jsou přiřazená rolím správce:

- Jednotlivě přiřazeny správcům a lze použít pro účely bez oprávnění správce (například osobní e-mailu)
- Jednotlivě přiřazené správcům a určené pouze pro účely správy
- Sdílet mezi více uživatelů
- Pro nouzový přístup scénáře
- Pro automatizované skripty
- Pro externí uživatele

**Osvědčený postup**: Implementace "just in time" (JIT) přístup k ještě víc snížit dobu expozice oprávnění a zvýší viditelnost do použití privilegovaných účtů.   
**Podrobnosti o**: Azure AD Privileged Identity Management vám umožní:

- Omezení uživatelů na pouze s ohledem na jejich oprávnění JIT.
- Přiřazení rolí zkrácený dobu bez obav, že jsou oprávnění automaticky odvolat.

**Osvědčený postup**: Definujte aspoň dva účty pro nouzový přístup.   
**Podrobnosti o**: Účty pro nouzový přístup pomáhají organizacím omezit privilegovaný přístup v existujícím prostředí Azure Active Directory. Tyto účty jsou vysoce privilegovaní a nejsou přiřazeny jednotlivým uživatelům. Účty pro nouzový přístup se omezuje na scénáře, kdy nejde použít normální účty pro správu. Organizace, musí omezit používání účtů nouzový s pouze nezbytné množství času.

Posouzení účtů, které jsou přiřazené nebo oprávněné pro roli globálního správce. Pokud nevidíte žádné účty jenom cloudu s využitím `*.onmicrosoft.com` domény (určený pro nouzový přístup), vytvořte je. Další informace najdete v tématu [Správa účtů pro nouzový přístup pro správu ve službě Azure AD](../active-directory/users-groups-roles/directory-emergency-access.md).

**Osvědčený postup**: Měli byste máte "pohotovostní" místo v případě nouze.
**Podrobnosti o**: Postupujte podle kroků v [k zabezpečení privilegovaného přístupu pro hybridní a cloudové nasazení ve službě Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

**Osvědčený postup**: Vyžadovat všechny kritické správce účtů, které se mají bez hesla (upřednostňováno) nebo vyžadovat Vícefaktorové ověřování.
**Podrobnosti o**: Použití [aplikaci Microsoft Authenticator](../active-directory/authentication/howto-authentication-phone-sign-in.md) se přihlásit k libovolnému účtu služby Azure AD bez použití hesla. Stejně jako [Windows Hello pro firmy](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator využívá ověřování pomocí klíče k zajištění přihlašovací údaje uživatele, který se váže na zařízení a používá biometrické ověřování nebo PIN kód.

Vyžadovat ověřování Azure Multi-Factor Authentication při přihlášení pro všechny jednotlivé uživatele, kteří jsou trvale přiřazené k jedné nebo více rolí správce Azure AD: Globální správce, správce privilegovaných rolí, správce Exchange Online a SharePoint Online správce. Povolit [ověřování službou Multi-Factor Authentication pro vaše účty správců](../active-directory/authentication/howto-mfa-userstates.md) a ujistěte se, že jste se zaregistrovali uživatele účtu správce.

**Osvědčený postup**: Pro účty správců kritické mají pracovní stanice správce kde produkční úlohy nejsou povolené (pro příklad, procházení a e-mailu). To bude chránit vaše účty správců před vektory útoku, které používají procházení a e-mailu a výrazně snížit riziko, že hlavní incidentu.
**Podrobnosti o**: Použijte pracovní stanice správce. Zvolte úroveň zabezpečení pracovní stanice:

- Produktivita vysoce zabezpečených zařízení poskytují pokročilé zabezpečení pro procházení a další úlohy produktivity.
- [Privilegovaný pracovní stanice s přístupem (Paw)](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) poskytují vyhrazený operační systém, které je chráněné před útoky z Internetu a jinými vektory hrozeb pro citlivé úlohy.

**Osvědčený postup**: Zrušení zřízení účtů správců, když zaměstnanci opustit organizaci.
**Podrobnosti o**: Měli byste máte místo, které zakazuje nebo odstraní účty správců, když zaměstnanci opustit organizaci.

**Osvědčený postup**: Pravidelně testovat účty správců pomocí aktuální technik útoku.
**Podrobnosti o**: Používejte simulátor útoku Office 365 nebo třetí strana nabídky ke spuštění útoku realistické scénáře ve vaší organizaci. To může pomoct najít ohrožených uživatelů, než dojde k skutečného útoku.

**Osvědčený postup**: Kroky pro zmírnění rizika u nejčastěji používaných technik napadené.  
**Podrobnosti o**: [Identifikujte účty Microsoft v rolích pro správu, které je potřeba přepnout na pracovní nebo školní účty](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Zajištění samostatné uživatelské účty a předávání pro účty globálních správců e-mailů](../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[Ujistěte se, že jste nedávno změnili hesla účtů pro správu](../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Zapnutí synchronizace hodnot hash hesel](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[Vyžadovat Vícefaktorové ověřování u uživatelů v všech privilegovaných rolích, jakož i odhalených uživatelů](../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Získat hodnotíte zabezpečení Office 365 (Pokud používáte Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[Projděte si doprovodné materiály pro zabezpečení a dodržování předpisů Office 365 (Pokud používáte Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Konfigurace monitorování aktivit Office 365 (Pokud používáte Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[Vytvořit incident/nouzovou odpovědi plán vlastníky](../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[Zabezpečené místní privilegované účty pro správu](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

Pokud není zabezpečení privilegovaného přístupu, můžete zjistit, že máte příliš mnoho uživatelů vysoce privilegované role a jsou zranitelnější vůči útokům. Útočníky, včetně internetoví útočníci často cílových účtů správce a další prvky privilegovaného přístupu k získání přístupu k citlivým datům a systémům pomocí krádeže přihlašovacích údajů.

## <a name="control-locations-where-resources-are-created"></a>Řízení umístění, kde jsou vytvořeny prostředky

Umožňuje operátorům cloudu k provádění úloh, a brání narušení smluv, které jsou potřeba ke správě prostředků vaší organizace je velmi důležité. Organizace, které chcete mít pod kontrolou umístění, kde se prostředky vytvoří by měl kód intenzivně tato místa.

Můžete použít [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) k vytvoření zásad zabezpečení, jejichž definice popisují akce nebo prostředky, které jsou výslovně odepřen. Můžete přiřadit tyto definice zásad v požadovaném oboru, jako je například předplatné, skupinu prostředků nebo samostatný prostředek.

> [!NOTE]
> Zásady zabezpečení nejsou stejná jako RBAC. Povolit uživatelům vytvářet prostředky, které skutečně používají RBAC.
>
>

Organizace, které nejsou řízení, jak vytvořit prostředky budou náchylnější uživatelům, kteří můžou zneužívání službu tak, že vytvoříte víc prostředků, než budou potřebovat. Posílení zabezpečení v procesu vytváření prostředků je důležitý krok k zabezpečení víceklientské scénář.

## <a name="actively-monitor-for-suspicious-activities"></a>Aktivně monitorujte pro podezřelé aktivity

Aktivní identitu monitorování systému můžete rychle odhalit podezřelé chování a aktivovat upozornění pro další zkoumání. V následující tabulce jsou uvedeny dvě možnosti Azure AD, které pomáhá organizacím sledovat svoji identitu:

**Osvědčený postup**: Měl odpovídající metodu k identifikaci:

- Pokusy o přihlášení [bez trasován](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md).
- [Útok hrubou silou](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) útoky na konkrétním účtu.
- Pokusy o přihlášení z více míst.
- Přihlášení z [zařízením infikovaným](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md).
- Podezřelé IP adresy.

**Podrobnosti o**: Pomocí Azure AD Premium [anomálií sestavy](../active-directory/active-directory-view-access-usage-reports.md). Mějte procesy a postupy pro správce IT spustit tyto sestavy, každý den nebo na vyžádání (obvykle ve scénáři reakce na incidenty).

**Osvědčený postup**: Mají aktivní monitorování systém, který upozorněním na rizika a můžete nastavit úroveň rizika (vysoká, střední nebo nízká) pro vaše obchodní požadavky.   
**Podrobnosti o**: Použití [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md), která označí aktuální rizika na svůj vlastní řídicí panel a odešle oznámení s denním souhrnu e-mailem. Pomáhá chránit identity vaší organizace, můžete nakonfigurovat zásady, které automaticky reagují na zjištěné problémy při dosažení úroveň zadané rizika.

Organizace, které není aktivně monitorovat jejich systémy identit hrozí správné přihlašovací údaje uživatele, dojde k ohrožení bezpečnosti. Bez vědomí, že podezřelé aktivity jsou probíhat přes tyto přihlašovací údaje organizace nelze zmírnění tohoto typu ohrožení.

## <a name="use-azure-ad-for-storage-authentication"></a>Použití Azure AD pro ověření úložiště
[Azure Storage](../storage/common/storage-auth-aad.md) podporuje ověřování a autorizace pomocí služby Azure AD pro úložiště objektů Blob a Queue storage. Pomocí ověřování Azure AD můžete použít řízení přístupu Azure na základě rolí udělit určitá oprávnění uživatelům, skupinám a aplikacím na rozsah kontejner jednotlivých objektů blob nebo fronty.

Doporučujeme, abyste použili [Azure AD pro ověřování přístupu k úložišti](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Další krok

Zobrazit [osvědčené postupy zabezpečení Azure a vzory](security-best-practices-and-patterns.md) pro další doporučené postupy zabezpečení, mají použít, když jste návrhu, nasazení a správa cloudových řešení pomocí služby Azure.
