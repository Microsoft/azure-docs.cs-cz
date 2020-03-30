---
title: Azure identity & doporučených postupů zabezpečení | Dokumenty společnosti Microsoft
description: Tento článek obsahuje sadu osvědčených postupů pro správu identit a řízení přístupu pomocí integrovaných funkcí Azure.
services: security
documentationcenter: na
author: barclayn
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: barclayn
ms.openlocfilehash: 52ef3a9b1df058d5d2e954b424094f9dbaeba15b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73053344"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Osvědčené postupy správy identit a zabezpečení řízení přístupu v Azure

V tomto článku se zabýváme kolekcí osvědčených postupů pro správu identit Azure a zabezpečení řízení přístupu. Tyto osvědčené postupy jsou odvozeny z našich zkušeností s [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) a zkušenosti zákazníků, jako jste vy.

Pro každý osvědčený postup vysvětlujeme:

* Jaký je osvědčený postup
* Proč chcete povolit tento osvědčený postup
* Jaký by mohl být výsledek, pokud nepovolíte osvědčené postupy
* Možné alternativy k osvědčeným postupům
* Jak se můžete naučit povolit osvědčené postupy

Tento článek osvědčených postupů pro správu identit Azure a řízení přístupu je založen na konsenzuálním stanovisku a možnostech platformy Azure a sadách funkcí, jak existují v době, kdy byl napsán tento článek.

Záměrem při psaní tohoto článku je poskytnout obecný plán na robustnější stav zabezpečení po nasazení vedeném našimi "[5 kroky k zabezpečení infrastruktury identit "](steps-secure-identity.md)kontrolní seznam, který vás provede některými z našich základních funkcí a služeb.

Názory a technologie se v průběhu času mění a tento článek bude pravidelně aktualizován tak, aby tyto změny odrážel.

Mezi osvědčené postupy pro správu identit Azure a řízení přístupu popsané v tomto článku patří:

* Považovat identitu za primární obvod zabezpečení
* Centralizovat správu identit
* Správa připojených klientů
* Povolení jednotného přihlašování
* Zapnutí podmíněného přístupu
* Plánování rutinních vylepšení zabezpečení
* Povolení správy hesel
* Vynucení vícefaktorového ověření pro uživatele
* Použití řízení přístupu na základě rolí
* Nižší expozice privilegovaných účtů
* Řízení umístění, kde jsou zdroje umístěny
* Použití Azure AD pro ověřování úložiště

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Považovat identitu za primární obvod zabezpečení

Mnozí považují identitu za primární perimetr pro zabezpečení. Jedná se o posun od tradičního zaměření na zabezpečení sítě. Obvody sítě jsou stále poréznější a že obvodová obrana nemůže být tak účinná, jako tomu bylo před explozí zařízení [BYOD](https://aka.ms/byodcg) a cloudových aplikací.

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) je řešení Azure pro správu identit a přístupu. Azure AD je víceklientská služba správy adresářů a identit od Microsoftu. Kombinuje základní adresářové služby, správu přístupu k aplikacím a ochranu identit do jednoho řešení.

V následujících částech jsou uvedeny osvědčené postupy pro zabezpečení identity a přístupu pomocí Azure AD.

**Osvědčený postup**: Centrum bezpečnostních ovládacích prvků a detekcí kolem identit uživatelů a služeb.
**Podrobnosti**: Použití Azure AD ke společnéumístění ovládacích prvků a identit.

## <a name="centralize-identity-management"></a>Centralizovat správu identit

Ve [scénáři hybridní identity](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) doporučujeme integrovat místní a cloudové adresáře. Integrace umožňuje it týmu spravovat účty z jednoho umístění bez ohledu na to, kde je účet vytvořen. Integrace také pomáhá uživatelům zvýšit produktivitu tím, že poskytuje společnou identitu pro přístup ke cloudovým i místním prostředkům.

**Osvědčený postup:** Vytvořte jednu instanci Azure AD. Konzistentnost a jediné autoritativní zdroje zvýší srozumitelnost a sníží bezpečnostní rizika plynoucích z lidských chyb a složitosti konfigurace.
**Podrobnosti**: Určete jeden adresář Azure AD jako autoritativní zdroj pro podnikové a organizační účty.

**Osvědčený postup:** Integrujte místní adresáře s Azure AD.  
**Podrobnosti**: Pomocí [služby Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnect) synchronizujte místní adresář s cloudovým adresářem.

> [!Note]
> Existují [faktory, které ovlivňují výkon Služby Azure AD Connect](../../active-directory/hybrid/plan-connect-performance-factors.md). Ujistěte se, že Azure AD Connect má dostatečnou kapacitu, aby nedostatečně výkonné systémy brání zabezpečení a produktivitě. Velké nebo složité organizace (organizace zřizující více než 100 000 objektů) by se měly řídit [doporučeními](../../active-directory/hybrid/whatis-hybrid-identity.md) k optimalizaci implementace Azure AD Connect.

**Osvědčený postup:** Nesynchronizujte účty do služby Azure AD, které mají vysoká oprávnění ve vaší existující instanci služby Active Directory.
**Podrobnosti**: Neměňte výchozí [konfiguraci Azure AD Connect,](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) která filtruje tyto účty. Tato konfigurace snižuje riziko, že se protivníci přetočí z cloudu na místní prostředky (což by mohlo způsobit závažný incident).

**Osvědčený postup**: Zapnutí synchronizace hodnot hash hesel.  
**Podrobnosti**: Synchronizace hodnot hash hesla je funkce používaná k synchronizaci hodnot hash uživatelských hesel z místní instance služby Active Directory s instancí Azure AD na bázi cloudu. Tato synchronizace pomáhá chránit před únikem přihlašovacích údajů přehrávaných z předchozích útoků.

I v případě, že se rozhodnete použít federaci se službou AD FS (AD FS) nebo jinými poskytovateli identit, můžete volitelně nastavit synchronizaci hodnot hash hesel jako zálohu v případě, že místní servery selžou nebo se stanou dočasně nedostupnými. Tato synchronizace umožňuje uživatelům přihlásit se ke službě pomocí stejného hesla, které používají k přihlášení k místní instanci služby Active Directory. Umožňuje také identity protection rozpoznat ohrožené přihlašovací údaje porovnáním synchronizované heslo hash s hesly známo, že jsou ohroženy, pokud uživatel použil stejnou e-mailovou adresu a heslo na jiné služby, které nejsou připojeny k Azure AD.

Další informace najdete [v tématu Implementace synchronizace hash hesel se synchronizací Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization).

**Osvědčený postup:** Pro vývoj nových aplikací použijte Azure AD pro ověřování.
**Podrobnosti**: Pro podporu ověřování použijte správné možnosti:

  - Azure AD pro zaměstnance
  - [Azure AD B2B](../../active-directory/b2b/index.yml) pro uživatele typu Host a externí partnery
  - [Azure AD B2C](../../active-directory-b2c/index.yml) řídit, jak zákazníci zaregistrovat, přihlásit se a spravovat své profily při používání aplikací

Organizace, které neintegrují svou místní identitu s jejich cloudovou identitou, můžou mít větší nároky na správu účtů. Tato režie zvyšuje pravděpodobnost chyb a narušení zabezpečení.

> [!Note]
> Musíte zvolit, které adresáře kritické účty budou umístěny v a zda je používaná pracovní stanice správce spravována novými cloudovými službami nebo existujícími procesy. Použití stávajících procesů správy a zřizování identit může snížit některá rizika, ale může také vytvořit riziko, že útočník kompromituje místní účet a přejde do cloudu. Můžete chtít použít jinou strategii pro různé role (například správci IT vs. správci organizační jednotky). Máte dvě možnosti. První možností je vytvoření účtů Azure AD, které nejsou synchronizovány s místní instancí služby Active Directory. Připojte se k pracovní stanici pro správu do Azure AD, kterou můžete spravovat a opravovat pomocí Microsoft Intune. Druhou možností je použití existujících účtů správce synchronizací s místní instancí služby Active Directory. Pro správu a zabezpečení používejte existující pracovní stanice v doméně služby Active Directory.

## <a name="manage-connected-tenants"></a>Správa připojených klientů
Vaše organizace zabezpečení potřebuje viditelnost k posouzení rizik a k určení, zda jsou dodržovány zásady vaší organizace a všechny regulační požadavky. Měli byste zajistit, aby vaše organizace zabezpečení měla přehled o všech předplatných připojených k produkčnímu prostředí a síti (prostřednictvím [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) nebo [sítě VPN site-to-site).](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) [Globální správce/správce společnosti](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) ve službě Azure AD může zvýšit jejich přístup k roli správce [přístupu uživatelů](../../role-based-access-control/built-in-roles.md#user-access-administrator) a zobrazit všechna předplatná a spravované skupiny připojené k vašemu prostředí.

Podívejte [se na zvýšení přístupu ke správě všech předplatných a skupin pro správu Azure,](../../role-based-access-control/elevate-access-global-admin.md) abyste zajistili, že vy a vaše skupina zabezpečení můžete zobrazit všechna předplatná nebo skupiny pro správu připojené k vašemu prostředí. Tento zvýšený přístup byste měli odebrat po posouzení rizik.

## <a name="enable-single-sign-on"></a>Povolení jednotného přihlašování

Ve světě, který je na prvním místě v mobilních zařízeních, chcete povolit jednotné přihlašování (SSO) k zařízením, aplikacím a službám odkudkoli, aby vaši uživatelé mohli být produktivní kdekoli a kdykoli. Pokud máte více řešení identit ke správě, stane se to problémem správy nejen pro IT, ale také pro uživatele, kteří si musí pamatovat více hesel.

Pomocí stejného řešení identity pro všechny vaše aplikace a prostředky, můžete dosáhnout sso. A vaši uživatelé mohou použít stejnou sadu přihlašovacích údajů k přihlášení a přístup k prostředkům, které potřebují, bez ohledu na to, zda jsou prostředky umístěné místně nebo v cloudu.

**Osvědčený postup**: Povolit spřijit.  
**Podrobnosti:** Azure AD [rozšiřuje místní službu Active Directory](/azure/active-directory/connect/active-directory-aadconnect) do cloudu. Uživatelé mohou používat svůj základní pracovní nebo školní účet pro zařízení připojená k doméně, firemní prostředky a všechny webové a SaaS aplikace, které potřebují k dokončení své práce. Uživatelé si nemusí pamatovat více sad uživatelských jmen a hesel a jejich přístup k aplikacím může být automaticky zřízen (nebo zrušen) na základě členství ve skupinách organizace a jejich stavu zaměstnance. Přístup můžete řídit u aplikací z galerie i u vlastních místních aplikací, které jste nasadili a publikovali přes [Proxy aplikací služby Azure AD](/azure/active-directory/active-directory-application-proxy-get-started).

Pomocí přihlašování k povolení uživatelům přístup k [jejich aplikacím SaaS](/azure/active-directory/active-directory-appssoaccess-whatis) na základě jejich práce nebo školní účet ve službě Azure AD. To platí nejen pro aplikace Microsoft SaaS, ale i pro další aplikace, jako jsou [Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) a [Salesforce](/azure/active-directory/active-directory-saas-salesforce-tutorial). Aplikaci můžete nakonfigurovat tak, aby používala Azure AD jako poskytovatele [identity založeného na SAML.](/azure/active-directory/fundamentals-identity) Jako ovládací prvek zabezpečení Azure AD nevydává token, který umožňuje uživatelům přihlásit se k aplikaci, pokud jim byl udělen přístup prostřednictvím Azure AD. Přístup můžete udělit přímo nebo prostřednictvím skupiny, jejíž jsou uživatelé jeho členem.

Organizace, které nevytvářejí společnou identitu k vytvoření automatického přihlašování pro své uživatele a aplikace, jsou více vystaveny scénářům, kde mají uživatelé více hesel. Tyto scénáře zvyšují pravděpodobnost, že uživatelé znovu použijí hesla nebo použijí slabá hesla.

## <a name="turn-on-conditional-access"></a>Zapnutí podmíněného přístupu

Uživatelé mohou přistupovat k prostředkům vaší organizace pomocí různých zařízení a aplikací odkudkoli. Jako správce IT se chcete ujistit, že tato zařízení splňují vaše standardy zabezpečení a dodržování předpisů. Jen se zaměřením na to, kdo má přístup k prostředku již nestačí.

Chcete-li vyvážit zabezpečení a produktivitu, musíte se před rozhodnutím o řízení přístupu zamyslet nad tím, jak se k prostředku přistupuje. S Podmíněný přístup Azure AD, můžete tento požadavek řešit. S podmíněným přístupem můžete provádět automatická rozhodnutí o řízení přístupu na základě podmínek pro přístup ke cloudovým aplikacím.

**Osvědčený postup**: Správa a řízení přístupu k podnikovým prostředkům.  
**Podrobnosti**: Konfigurace [podmíněného přístupu](/azure/active-directory/active-directory-conditional-access-azure-portal) Azure AD na základě citlivosti skupiny, umístění a aplikace pro aplikace SaaS a aplikace připojené k Azure AD.

**Osvědčený postup**: Blokovat starší ověřovací protokoly.
**Detail**: Útočníci zneužívají slabiny ve starších protokolech každý den, zejména při útocích na spreje heslem. Nakonfigurujte podmíněný přístup tak, aby blokoval starší protokoly. Další informace najdete ve videu [Azure AD: Co dělat a co nedělat.](https://www.youtube.com/watch?v=wGk0J4z90GI)

## <a name="plan-for-routine-security-improvements"></a>Plánování rutinních vylepšení zabezpečení

Zabezpečení se neustále vyvíjí a je důležité zabudovat do svého cloudu a architektury správy identit způsob, jak pravidelně zobrazovat růst a objevovat nové způsoby zabezpečení vašeho prostředí.

Skóre zabezpečení identity je sada doporučených ovládacích prvků zabezpečení, které společnost Microsoft publikuje a která vám poskytuje číselné skóre, které objektivně měří stav zabezpečení a pomáhá plánovat budoucí vylepšení zabezpečení. Můžete si také zobrazit své skóre ve srovnání s těmi v jiných odvětvích, stejně jako vaše vlastní trendy v průběhu času.

**Osvědčený postup**: Plánování rutinních kontrol zabezpečení a vylepšení na základě osvědčených postupů ve vašem odvětví.
**Podrobnosti**: Pomocí funkce Skóre zabezpečení identity můžete svá vylepšení v průběhu času ohodnotit.

## <a name="enable-password-management"></a>Povolení správy hesel

Pokud máte více klientů nebo chcete povolit uživatelům [resetovat vlastní hesla](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md), je důležité použít příslušné zásady zabezpečení, aby se zabránilo zneužití.

**Osvědčený postup:** Nastavte pro uživatele samoobslužné resetování hesla (SSPR).  
**Podrobnosti**: Použijte funkci [samoobslužné resetování hesla](/azure/active-directory-b2c/active-directory-b2c-reference-sspr) Azure AD.

**Osvědčený postup**: Sledujte, jak nebo zda je sspr skutečně používán.  
**Podrobnosti**: Sledujte uživatele, kteří se registrují pomocí [sestavy aktivity registrace obnovení hesla](/azure/active-directory/active-directory-passwords-get-insights)Azure AD . Funkce vytváření sestav, kterou azure ad poskytuje vám pomůže odpovědět na otázky pomocí předem sestavené sestavy. Pokud máte příslušnou licenci, můžete také vytvořit vlastní dotazy.

**Osvědčený postup:** Rozšíření zásad hesel na základě cloudu na místní infrastrukturu.
**Podrobnosti**: Vylepšete zásady hesel ve vaší organizaci provedením stejných kontrol místních změn hesel jako u změn hesel na základě cloudu. Nainstalujte [ochranu heslem Azure AD](/azure/active-directory/authentication/concept-password-ban-bad) pro agenty služby Windows Server Active Directory místně a rozšiřte seznamy zakázaných hesel na stávající infrastrukturu. Uživatelé a správci, kteří mění, nastavují nebo resetují hesla v místním prostředí, musí dodržovat stejné zásady hesel jako uživatelé pouze v cloudu.

## <a name="enforce-multi-factor-verification-for-users"></a>Vynucení vícefaktorového ověření pro uživatele

Doporučujeme, abyste pro všechny uživatele vyžadovali dvoustupňové ověření. To zahrnuje správce a další osoby ve vaší organizaci, kteří mohou mít významný dopad, pokud je jejich účet ohrožen (například finanční úředníci).

Existuje několik možností, jak vyžadovat dvoustupňové ověření. Nejlepší možnost pro vás závisí na vašich cílech, edici Azure AD, kterou používáte, a na vašem licenčním programu. Podívejte se [na how to require two-step verification for a user](/azure/active-directory/authentication/howto-mfa-userstates) to determine the best option for you. Další informace o licencích a cenách najdete na stránkách s cenami [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) a [Azure Multi-Factor Authentication.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Níže jsou uvedeny možnosti a výhody pro povolení dvoustupňového ověření:

**Možnost 1**: Povolit vícefaktorové povolení pro všechny uživatele a metody přihlášení s **výhodou výchozí**zabezpečení Azure AD : Tato možnost umožňuje snadno a rychle vynutit vícefaktorové povolení pro všechny uživatele ve vašem prostředí s přísnými zásadami:

* Zpochybnit účty správy a mechanismy pro přihlášení správce
* Vyžadovat výzvu vícefaktorové ověřování prostřednictvím služby Microsoft Authenticator pro všechny uživatele
* Omezte starší ověřovací protokoly.

Tato metoda je k dispozici pro všechny úrovně licencování, ale nelze je smíchat se stávajícími zásadami podmíněného přístupu. Další informace najdete ve výchozím nastavení zabezpečení Azure AD

**Možnost 2:** [Povolte vícefaktorové ověřování změnou stavu uživatele](../../active-directory/authentication/howto-mfa-userstates.md).   
**Výhoda**: Jedná se o tradiční metodu, která vyžaduje dvoustupňové ověření. Funguje s [vícefaktorovým ověřováním Azure v cloudu i s vícefaktorovým ověřovacím serverem Azure](/azure/active-directory/authentication/concept-mfa-whichversion). Pomocí této metody vyžaduje, aby uživatelé provádět dvoustupňové ověření při každém přihlášení a přepíše zásady podmíněného přístupu.

Pokud chcete zjistit, kde je potřeba povolit vícefaktorové ověřování, přečtěte [si část, která verze Azure MFA je pro mou organizaci vhodná?](/azure/active-directory/authentication/concept-mfa-whichversion).

**Možnost 3:** [Povolte vícefaktorové ověřování pomocí zásad podmíněného přístupu](/azure/active-directory/authentication/howto-mfa-getstarted).
**Výhoda**: Tato možnost umožňuje vyzvat k dvoustupňovému ověření za určitých podmínek pomocí [podmíněného přístupu](/azure/active-directory/active-directory-conditional-access-azure-portal). Konkrétní podmínky mohou být přihlášení uživatele z různých umístění, nedůvěryhodných zařízení nebo aplikací, které považujete za rizikové. Definování konkrétních podmínek, kdy vyžadujete dvoustupňové ověření, umožňuje vyhnout se neustálému dotazování uživatelů, což může být nepříjemné uživatelské prostředí.

Jedná se o nejflexibilnější způsob, jak uživatelům povolit dvoustupňové ověření. Povolení zásad podmíněného přístupu funguje jenom pro azure vícefaktorové ověřování v cloudu a je prémiovou funkcí Azure AD. Další informace o této metodě najdete v [nasadit cloudové azure vícefaktorové ověřování](/azure/active-directory/authentication/howto-mfa-getstarted).

**Možnost 4:** Povolte vícefaktorové ověřování pomocí zásad podmíněného přístupu vyhodnocením uživatelského a přihlašovacího rizika [ochrany identity Azure AD](/azure/active-directory/authentication/tutorial-risk-based-sspr-mfa).   
**Výhoda**: Tato možnost umožňuje:

* Zjištění potenciálních chyb zabezpečení, které ovlivňují identity vaší organizace.
* Nakonfigurujte automatické odpovědi na zjištěné podezřelé akce, které souvisejí s identitami vaší organizace.
* Vyšetřte podezřelé incidenty a vyřiďte příslušná opatření k jejich vyřešení.

Tato metoda používá hodnocení rizika Ochrany identity Azure AD k určení, pokud je vyžadováno dvoustupňové ověření na základě rizika přihlášení uživatele a přihlášení pro všechny cloudové aplikace. Tato metoda vyžaduje licencování Služby Azure Active Directory P2. Další informace o této metodě najdete v [azure active directory identity protection](/azure/active-directory/identity-protection/overview).

> [!Note]
> Možnost 1, povolení vícefaktorového ověřování změnou stavu uživatele, přepíše zásady podmíněného přístupu. Vzhledem k tomu, že možnosti 2 a 3 používají zásady podmíněného přístupu, nelze s nimi použít možnost 1.

Organizace, které nepřidávají další vrstvy ochrany identity, jako je například dvoustupňové ověření, jsou náchylnější k útoku na krádež přihlašovacích údajů. Útok krádeže přihlašovacích údajů může vést k ohrožení zabezpečení dat.

## <a name="use-role-based-access-control"></a>Použití řízení přístupu na základě rolí

Správa přístupu pro cloudové prostředky je důležitá pro každou organizaci, která používá cloud. [Řízení přístupu na základě rolí (RBAC)](/azure/role-based-access-control/overview)vám pomůže spravovat, kdo má přístup k prostředkům Azure, co můžou dělat s těmito prostředky a k jakým oblastem mají přístup.

Určení skupin nebo jednotlivých rolí odpovědných za konkrétní funkce v Azure pomáhá zabránit nejasnostem, které mohou vést k chybám člověka a automatizace, které vytvářejí bezpečnostní rizika. Omezení přístupu na základě [potřeby znát](https://en.wikipedia.org/wiki/Need_to_know) a [nejnižší oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) zásadzabezpečení je nezbytné pro organizace, které chtějí vynutit zásady zabezpečení pro přístup k datům.

Váš tým zabezpečení potřebuje přehled o vašich prostředcích Azure, aby mohl vyhodnotit a napravit rizika. Pokud má tým zabezpečení provozní odpovědnosti, potřebují další oprávnění k práci.

[RBAC](/azure/role-based-access-control/overview) můžete použít k přiřazení oprávnění uživatelům, skupinám a aplikacím v určitém oboru. Role se dají přidělovat na úrovni předplatného, skupiny prostředků nebo konkrétního prostředku.

**Osvědčený postup**: Segregujte povinnosti v rámci týmu a udělte uživatelům pouze tolik přístupu, který potřebují k výkonu své práce. Místo toho, aby všichni neomezená oprávnění ve vašem předplatném Azure nebo prostředky, povolit pouze určité akce v určitém oboru.
**Podrobnosti**: Pomocí [předdefinovaných rolí RBAC](/azure/role-based-access-control/built-in-roles) v Azure přiřaďte uživatelům oprávnění.

> [!Note]
> Konkrétní oprávnění vytvářejí nepotřebnou složitost a zmatek a hromadí se do "starší" konfigurace, kterou je obtížné opravit bez obav z porušení něčeho. Vyhněte se oprávněním pro konkrétní prostředky. Místo toho použijte skupiny pro správu pro oprávnění pro celou řadu organizace a skupiny prostředků pro oprávnění v rámci předplatných. Vyhněte se oprávněním specifickým pro uživatele. Místo toho přiřaďte přístup ke skupinám v Azure AD.

**Osvědčený postup**: Udělte týmům zabezpečení s azure odpovědnosti přístup k zobrazení prostředků Azure, aby mohli posoudit a napravit rizika.
**Podrobnosti**: Udělte týmům zabezpečení roli [Čtečky zabezpečení](/azure/role-based-access-control/built-in-roles#security-reader) RBAC. Kořenovou skupinu pro správu nebo skupinu pro správu segmentu můžete použít v závislosti na rozsahu odpovědností:

* **Kořenová skupina pro správu** týmů odpovědných za všechny zdroje organizace
* **Skupina pro správu segmentů** pro týmy s omezeným rozsahem (obvykle z důvodu regulačních nebo jiných organizačních hranic)

**Osvědčený postup**: Udělte příslušná oprávnění bezpečnostním týmům, které mají přímé provozní odpovědnosti.
**Podrobnosti**: Zkontrolujte předdefinované role RBAC pro přiřazení příslušné role. Pokud předdefinované role nesplňují specifické potřeby vaší organizace, můžete vytvořit [vlastní role pro prostředky Azure](/azure/role-based-access-control/custom-roles). Stejně jako u předdefinovaných rolí můžete přiřadit vlastní role uživatelům, skupinám a instančním objektům v oborech předplatného, skupiny prostředků a prostředků.

**Doporučené postupy**: Udělte Azure Security Center přístup k rolím zabezpečení, které ji potřebují. Security Center umožňuje bezpečnostním týmům rychle identifikovat a napravit rizika.
**Podrobnosti**: Přidání týmů zabezpečení s těmito potřebami do role [správce zabezpečení](/azure/role-based-access-control/built-in-roles#security-admin) RBAC, aby mohli zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazit výstrahy a doporučení a zavřít výstrahy a doporučení. Můžete to provést pomocí kořenové skupiny pro správu nebo skupiny pro správu segmentu v závislosti na rozsahu odpovědností.

Organizace, které nevynucují řízení přístupu k datům pomocí funkcí, jako je RBAC, mohou svým uživatelům udělit více oprávnění, než je nutné. To může vést k ohrožení zabezpečení dat tím, že uživatelům umožní přístup k typům dat (například velký dopad na podnikání), které by neměli mít.

## <a name="lower-exposure-of-privileged-accounts"></a>Nižší expozice privilegovaných účtů

Zabezpečení privilegovaného přístupu je důležitým prvním krokem k ochraně obchodních aktiv. Minimalizace počtu uživatelů, kteří mají přístup k zabezpečeným informacím nebo prostředkům, snižuje pravděpodobnost, že uživatel se zlými úmysly získá přístup nebo oprávněný uživatel neúmyslně ovlivní citlivý prostředek.

Privilegované účty jsou účty, které spravují a spravují it systémy. Počítačoví útočníci cílí na tyto účty, aby získali přístup k datům a systémům organizace. Chcete-li zabezpečit privilegovaný přístup, měli byste izolovat účty a systémy od rizika vystavení uživateli se zlými úmysly.

Doporučujeme, abyste vyvinuli a řídili se plánem pro zabezpečení privilegovaného přístupu proti kybernetickým útočníkům. Informace o vytvoření podrobného plánu pro zabezpečení identit a přístupu, které jsou spravované nebo vykazované ve službě Azure AD, Microsoft Azure, Office 365 a dalších cloudových službách, najděte v recenzi [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení ve službě Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure).

Následující shrnuje osvědčené postupy nalezené v [zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení ve službě Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure):

**Osvědčený postup:** Správa, řízení a monitorování přístupu k privilegovaným účtům.   
**Podrobnosti:** Zapněte [azure ad privilegované správy identit](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access). Po zapnutí správy privilegovaných identit obdržíte e-mailové zprávy s oznámením o změnách rolí privilegovaného přístupu. Tato oznámení poskytují včasné upozornění, pokud jsou další uživatelé přidáni do vysoce privilegovaných rolí ve vašem adresáři.

**Osvědčený postup:** Ujistěte se, že všechny důležité účty správce jsou spravované účty Azure AD.
**Podrobnosti**: Odeberte všechny spotřebitelské účty z kritických rolí správců (například účty Microsoft, jako jsou hotmail.com, live.com a outlook.com).

**Osvědčený postup:** Ujistěte se, že všechny kritické role správce mají samostatný účet pro úkoly správy, aby se zabránilo phishingu a dalším útokům, které by ohrozily oprávnění správce.
**Podrobnosti**: Vytvořte samostatný účet správce, kterému jsou přiřazena oprávnění potřebná k provádění úkolů správy. Zablokujte používání těchto účtů pro správu pro nástroje pro každodenní produktivitu, jako je e-mail Microsoft Office 365 nebo libovolné procházení webu.

**Osvědčený postup**: Identifikace a kategorizace účtů, které jsou ve vysoce privilegovaných rolích.   
**Podrobnosti**: Po zapnutí Správy privilegovaných identit Azure AD zobrazte uživatele, kteří jsou v globálním správci, správci privilegovaných rolí a dalších vysoce privilegovaných rolích. Odeberte všechny účty, které již nejsou v těchto rolích potřeba, a zařaďte zbývající účty, které jsou přiřazeny k rolím správců:

* Individuálně přiřazeno administrativním uživatelům a může být použito pro neadministrativní účely (například osobní e-mail)
* Individuálně přiděleni administrativním uživatelům a určeni pouze pro administrativní účely
* Sdíleno mezi více uživateli
* Pro scénáře nouzového přístupu
* Pro automatické skripty
* Pro externí uživatele

**Osvědčený postup**: Implementujte přístup "just in time" (JIT) k dalšímu snížení doby expozice oprávnění a zvýšit přehled o používání privilegovaných účtů.   
**Podrobnosti:** Správa privilegovaných identit Azure AD umožňuje:

* Omezte uživatele pouze na převzetí jejich oprávnění JIT.
* Přiřaďte role pro zkrácenou dobu trvání s jistotou, že oprávnění jsou automaticky odvolána.

**Osvědčený postup**: Definujte alespoň dva účty nouzového přístupu.   
**Podrobnosti**: Účty nouzového přístupu pomáhají organizacím omezit privilegovaný přístup v existujícím prostředí služby Azure Active Directory. Tyto účty jsou vysoce privilegované a nejsou přiřazeny konkrétním osobám. Účty nouzového přístupu jsou omezeny na scénáře, kde nelze použít běžné účty pro správu. Organizace musí omezit využití účtu tísňového volání pouze na potřebnou dobu.

Vyhodnoťte účty, které jsou přiřazeny nebo způsobilé pro roli globálního správce. Pokud nevidíte žádné účty pouze pro cloud `*.onmicrosoft.com` pomocí domény (určené pro nouzový přístup), vytvořte je. Další informace najdete [v tématu Správa účtů pro správu nouzového přístupu ve službě Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access).

**Osvědčený postup**: V případě nouze zaváděli proces "rozbití skla".
**Podrobnosti**: Postupujte podle kroků v [zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení ve službě Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure).

**Osvědčený postup**: Vyžadovat, aby všechny důležité účty správce byly bez hesla (upřednostňované) nebo vyžadovaly vícefaktorové ověřování.
**Podrobnosti**: Pomocí [aplikace Microsoft Authenticator](/azure/active-directory/authentication/howto-authentication-phone-sign-in) se přihlaste k libovolnému účtu Azure AD bez použití hesla. Podobně jako [Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-identity-verification)používá Microsoft Authenticator ověřování založené na klíči k povolení pověření uživatele, které je svázané se zařízením a používá biometrické ověřování nebo PIN kód.

Vyžadovat vícefaktorové ověřování Azure při přihlášení pro všechny jednotlivé uživatele, kteří jsou trvale přiřazeni k jedné nebo více rolím správce Azure AD: globální správce, správce privilegovaných rolí, správce Exchange Online a SharePoint Online Správce. Povolte [vícefaktorové ověřování pro své účty správců](/azure/active-directory/authentication/howto-mfa-userstates) a zajistěte, aby se uživatelé účtu správce zaregistrovali.

**Osvědčený postup**: Pro kritické účty správce, mít admin pracovní stanice, kde nejsou povoleny produkční úkoly (například procházení a e-mail). Tím ochráníte vaše účty správců před vektory útoku, které používají procházení a e-mail, a výrazně snížíte riziko závažného incidentu.
**Detail**: Použijte pracovní stanici správce. Zvolte úroveň zabezpečení pracovní stanice:

- Vysoce zabezpečená zařízení pro zvýšení produktivity poskytují pokročilé zabezpečení pro procházení a další úkoly s produktivitou.
- [Pracovní stanice s privilegovaným přístupem (PAWs)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) poskytují vyhrazený operační systém, který je chráněn před internetovými útoky a vektory hrozeb pro citlivé úkoly.

**Osvědčený postup:** Zrušení zřízení účtů správce, když zaměstnanci opustí vaši organizaci.
**Podrobnosti**: Mít na místě proces, který zakáže nebo odstraní účty správců, když zaměstnanci opustí vaši organizaci.

**Osvědčený postup**: Pravidelně testujte účty správců pomocí aktuálních technik útoku.
**Podrobnosti**: Ke spuštění realistických scénářů útoků ve vaší organizaci použijte Simulátor útoků Office 365 nebo nabídku třetí strany. To vám může pomoci najít zranitelné uživatele dříve, než dojde ke skutečnému útoku.

**Osvědčený postup**: Podnikneme kroky ke zmírnění nejčastěji používaných napadených technik.  
**Detail**: [Identifikace účtů Microsoft v administrativních rolích, které je třeba přepnout na pracovní nebo školní účty](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Zajištění samostatných uživatelských účtů a předávání pošty pro účty globálního správce](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[Ujistěte se, že se hesla účtů pro správu nedávno změnila](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Zapnutí synchronizace hodnot hash hesel](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[Vyžadovat vícefaktorové ověřování pro uživatele ve všech privilegovaných rolích i exponované uživatele](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Získání zabezpečeného skóre Office 365 (pokud používáte Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[Projděte si pokyny k zabezpečení a dodržování předpisů office 365 (pokud používáte Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Konfigurace monitorování aktivit Office 365 (pokud používáte Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

[Zřídit vlastníky plánu reakce na incidenty/mimořádné události](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#establish-incidentemergency-response-plan-owners)  

[Zabezpečení místních privilegovaných účtů pro správu](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)

Pokud nezabezpečíte privilegovaný přístup, můžete zjistit, že máte příliš mnoho uživatelů ve vysoce privilegovaných rolích a jste zranitelnější vůči útokům. Zlomyslní aktéři, včetně kybernetických útočníků, se často zaměřují na účty správců a další prvky privilegovaného přístupu, aby získali přístup k citlivým datům a systémům pomocí krádeže přihlašovacích údajů.

## <a name="control-locations-where-resources-are-created"></a>Řízení umístění, kde jsou vytvořeny prostředky

Povolení cloudových operátorů provádět úlohy a zároveň jim zabránit v porušení konvencí, které jsou potřebné ke správě prostředků vaší organizace, je velmi důležité. Organizace, které chtějí řídit umístění, kde jsou vytvořeny prostředky by měl pevný kód těchto umístění.

[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) můžete použít k vytvoření zásad zabezpečení, jejichž definice popisují akce nebo prostředky, které jsou výslovně odepřeny. Tyto definice zásad přiřadíte v požadovaném oboru, jako je například předplatné, skupina prostředků nebo jednotlivé prostředky.

> [!NOTE]
> Zásady zabezpečení nejsou stejné jako RBAC. Ve skutečnosti používají RBAC k autorizaci uživatelů k vytvoření těchto prostředků.
>
>

Organizace, které neřídí způsob vytváření prostředků, jsou náchylnější k uživatelům, kteří mohou službu zneužívat vytvořením více prostředků, než potřebují. Posílení procesu vytváření prostředků je důležitým krokem k zabezpečení scénáře s více tenanty.

## <a name="actively-monitor-for-suspicious-activities"></a>Aktivně sledovat podezřelé aktivity

Aktivní systém sledování identity může rychle zjistit podezřelé chování a spustit výstrahu pro další šetření. V následující tabulce jsou uvedeny dvě funkce Azure AD, které můžou organizacím pomoct sledovat jejich identity:

**Osvědčený postup**: Mít metodu pro identifikaci:

- Pokusí se přihlásit [bez vysledování](/azure/active-directory/active-directory-reporting-sign-ins-from-unknown-sources).
- [Hrubá síla](/azure/active-directory/active-directory-reporting-sign-ins-after-multiple-failures) útočí na konkrétní účet.
- Pokusí se přihlásit z více míst.
- Přihlášení z [infikovaných zařízení](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices).
- Podezřelé IP adresy.

**Podrobnosti:** Použijte [sestavy anomálií](/azure/active-directory/active-directory-view-access-usage-reports)Azure AD Premium . Mít procesy a postupy zavedeny pro správce IT pro spouštění těchto sestav na denní bázi nebo na vyžádání (obvykle ve scénáři reakce na incidenty).

**Osvědčené postupy**: Mít aktivní monitorovací systém, který vás upozorní na rizika a může upravit úroveň rizika (vysoká, střední nebo nízká) vašim obchodním požadavkům.   
**Podrobnosti**: Použijte [Azure AD Identity Protection](/azure/active-directory/active-directory-identityprotection), který označí aktuální rizika na vlastním řídicím panelu a odešle denní souhrnná oznámení prostřednictvím e-mailu. Chcete-li chránit identity vaší organizace, můžete nakonfigurovat zásady založené na rizicích, které automaticky reagují na zjištěné problémy při dosažení zadané úrovně rizika.

Organizacím, které aktivně nesledují své systémy identit, hrozí ohrožení zabezpečení přihlašovacích údajů uživatele. Bez znalosti, že podezřelé aktivity probíhají prostřednictvím těchto pověření, organizace nemohou zmírnit tento typ ohrožení.

## <a name="use-azure-ad-for-storage-authentication"></a>Použití Azure AD pro ověřování úložiště
[Azure Storage](/azure/storage/common/storage-auth-aad) podporuje ověřování a autorizaci pomocí Azure AD pro úložiště objektů blob a úložiště front. S ověřováním Azure AD můžete pomocí řízení přístupu založeného na rolích Azure udělit konkrétní oprávnění uživatelům, skupinám a aplikacím až do rozsahu jednotlivého kontejneru nebo fronty objektů blob.

Doporučujeme používat [Azure AD pro ověřování přístupu k úložišti](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Další krok

Podívejte se na [osvědčené postupy a vzory zabezpečení Azure, kde](best-practices-and-patterns.md) najdete další doporučené postupy zabezpečení, které se používají při navrhování, nasazování a správě cloudových řešení pomocí Azure.
