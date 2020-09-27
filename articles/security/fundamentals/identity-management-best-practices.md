---
title: Osvědčené postupy zabezpečení Azure identity & Accessu | Microsoft Docs
description: Tento článek poskytuje sadu osvědčených postupů pro správu identit a řízení přístupu pomocí integrovaných funkcí Azure.
services: security
documentationcenter: na
author: terrylanfear
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
ms.author: terrylan
ms.openlocfilehash: 69aac7dff80b7c85212602f1c03957a117628737
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400328"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Osvědčené postupy zabezpečení správy identit a řízení přístupu v Azure

V tomto článku se podíváme na shromažďování osvědčených postupů zabezpečení Azure Identity Management a Access Control. Tyto osvědčené postupy se odvozují z našich zkušeností s [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) a zkušenostmi se zákazníky, jako je sami.

Pro každý osvědčený postup je vysvětleno:

* Co je nejlepší postup
* Proč je vhodné Tento osvědčený postup povolit
* To může být způsobeno tím, že nepovolíte osvědčené postupy
* Možné alternativy k osvědčeným postupům
* Postup, jak se naučit, jak povolit osvědčený postup

Tento článek týkající se osvědčených postupů zabezpečení Azure Identity Management a Access Control je založený na konsensuch a funkcích platformy Azure a sadách funkcí, jak existují v době, kdy byl tento článek napsán.

Záměrem psaní tohoto článku je poskytnout obecný přehled o robustnějším stav zabezpečení po nasazení průvodcem, který provede naše "[5 kroků k zabezpečení vaší infrastruktury identity](steps-secure-identity.md)", který vás provede některými základními funkcemi a službami.

Názory a technologie se v průběhu času mění a tento článek se pravidelně aktualizuje, aby odrážel tyto změny.

Osvědčené postupy zabezpečení Azure Identity Management a Access Control jsou popsané v tomto článku:

* Považovat identitu za primární hraniční zabezpečení
* Centralizovaná správa identit
* Spravovat připojené klienty
* Povolit jednotné přihlašování
* Zapnutí podmíněného přístupu
* Plánování běžných vylepšení zabezpečení
* Povolení správy hesel
* Vymáhat vícefaktorové ověřování pro uživatele
* Použití řízení přístupu na základě rolí
* Nižší vystavení privilegovaných účtů
* Ovládací prvky umístění, kde jsou umístěny prostředky
* Použití Azure AD k ověřování úložiště

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Považovat identitu za primární hraniční zabezpečení

Celá řada považuje identitu za primární hraniční zabezpečení. Jedná se o posun od tradičního zaměření na zabezpečení sítě. Hraniční sítě udržují více Porous a tato ochrana hranice nemůže být tak efektivní, protože byla předtím, než dojde k rozpadu zařízení [BYOD](https://aka.ms/byodcg) a cloudových aplikací.

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) je řešení Azure pro správu identit a přístupu. Azure AD je víceklientské cloudová služba a služba pro správu identit od Microsoftu. Kombinuje základní adresářové služby, správu přístupu k aplikacím a ochranu identit do jednoho řešení.

V následujících oddílech jsou uvedeny osvědčené postupy pro zabezpečení identity a přístupu pomocí Azure AD.

**Osvědčený postup**: řízení zabezpečení centra a detekce identit uživatelů a služeb.
**Podrobnosti**: k společné umístění řízení a identit použijte Azure AD.

## <a name="centralize-identity-management"></a>Centralizovaná správa identit

Ve scénáři [hybridní identity](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) doporučujeme integrovat místní a cloudové adresáře. Integrace umožňuje vašemu IT týmu spravovat účty z jednoho místa bez ohledu na to, kde je účet vytvořený. Integrace taky pomáhá uživatelům zvýšit produktivitu tím, že poskytuje společnou identitu pro přístup k cloudovým i místním prostředkům.

**Osvědčený postup**: vytvoření jedné instance služby Azure AD. Konzistence a jeden autoritativní zdroj zvýší přehlednost a sníží rizika zabezpečení z lidských chyb a složitosti konfigurace.
**Podrobnosti**: Určete jeden adresář služby Azure AD jako autoritativní zdroj pro podnikové a organizační účty.

**Osvědčený postup**: Integrujte své místní adresáře se službou Azure AD.  
**Podrobnosti**: použijte [Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnect) k synchronizaci místního adresáře s adresářem v cloudu.

> [!Note]
> Existují [faktory, které mají vliv na výkon Azure AD Connect](../../active-directory/hybrid/plan-connect-performance-factors.md). Ujistěte se, že Azure AD Connect má dostatečnou kapacitu, aby bylo možné zabránit systémům v nedostatku zabezpečení a produktivity. Velká nebo složitá organizace (organizace, které zřizování více než 100 000 objektů) by měly dodržovat [doporučení](../../active-directory/hybrid/whatis-hybrid-identity.md) k optimalizaci Azure AD Connect implementace.

**Osvědčený postup**: Nesynchronizujte účty do služby Azure AD s vysokými oprávněními ve stávající instanci služby Active Directory.
**Podrobnosti**: neměňte výchozí [konfiguraci Azure AD Connect](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) , která filtruje tyto účty. Tato konfigurace snižuje riziko nežádoucí osobyho překlopení z cloudu na místní prostředky (což by mohlo způsobit velký incident).

**Osvědčený postup**: Zapněte synchronizaci hodnot hash hesel.  
**Podrobnosti**: synchronizace hodnot hash hesel je funkce, která se používá k synchronizaci hodnot hash hesel uživatele z místní instance služby Active Directory s instancí Azure AD založené na cloudu. Tato synchronizace pomáhá chránit proti nevráceným přihlašovacím údajům, které se přehrávají z předchozích útoků.

I když se rozhodnete použít federaci s Active Directory Federation Services (AD FS) (AD FS) nebo jinými zprostředkovateli identity, můžete volitelně nastavit synchronizaci hodnot hash hesel jako zálohu v případě, že vaše místní servery selžou nebo jsou dočasně nedostupné. Tato synchronizace umožňuje uživatelům přihlásit se ke službě pomocí stejného hesla, které používají pro přihlášení ke své místní instanci služby Active Directory. Umožňuje taky službě Identity Protection detekovat napadené přihlašovací údaje porovnáním synchronizovaných hodnot hash hesel pomocí hesel, která jsou známá k ohrožení, pokud uživatel použil stejnou e-mailovou adresu a heslo na jiných službách, které nejsou připojené ke službě Azure AD.

Další informace najdete v tématu [implementace synchronizace hodnot hash hesel pomocí Azure AD Connect synchronizace](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization).

**Osvědčený postup**: pro vývoj nových aplikací použijte Azure AD pro ověřování.
**Podrobnosti**: Používejte správné možnosti pro podporu ověřování:

  - Azure AD pro zaměstnance
  - [Azure AD B2B](../../active-directory/b2b/index.yml) pro uživatele typu Host a externí partneři
  - [Azure AD B2C](../../active-directory-b2c/index.yml) řídit, jak se uživatelé registrují, přihlašovat a spravovat svoje profily při používání vašich aplikací

Organizace, které neintegrují svou místní identitu s jejich cloudovou identitou, můžou mít větší režii při správě účtů. Tato režie zvyšuje pravděpodobnost chyb a porušení zabezpečení.

> [!Note]
> Musíte zvolit, které adresáře se budou nacházet v a jestli se používá pracovní stanice pro správu, kterou používají nové cloudové služby nebo stávající procesy. Používání stávajících procesů správy a zřizování identit může snížit některá rizika, ale může také vytvořit riziko narušení místního účtu a jeho překlopení do cloudu. Můžete chtít použít jinou strategii pro různé role (například správce IT vs. obchodní jednotky Admins). Máte dvě možnosti. První možností je vytvořit účty Azure AD, které nejsou synchronizované s vaší místní instancí Active Directory. Připojte se k pracovní stanici správce k Azure AD, kterou můžete spravovat a opravovat pomocí Microsoft Intune. Druhou možností je použít stávající účty správců synchronizací do místní instance služby Active Directory. Pro správu a zabezpečení použijte existující pracovní stanice v doméně služby Active Directory.

## <a name="manage-connected-tenants"></a>Spravovat připojené klienty
Vaše organizace zabezpečení potřebuje viditelnost, aby posoudila riziko a určila, jestli jsou dodržovány zásady vaší organizace a jakékoli zákonné požadavky. Měli byste zajistit, aby vaše organizace zabezpečení měla přehled o všech předplatných připojených k vašemu provoznímu prostředí a síti (prostřednictvím [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) nebo [VPN typu Site-to-site](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). [Globální správce nebo správce společnosti](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) ve službě Azure AD může zvýšit přístup k roli [Správce přístupu uživatele](../../role-based-access-control/built-in-roles.md#user-access-administrator) a zobrazit všechna předplatná a spravované skupiny připojené k vašemu prostředí.

Projděte si téma [zvýšení přístupu ke správě všech předplatných Azure a skupin pro správu](../../role-based-access-control/elevate-access-global-admin.md) , abyste měli jistotu, že máte a vaše skupina zabezpečení budou zobrazovat všechna předplatná nebo skupiny pro správu připojené k vašemu prostředí. Tento přístup se zvýšeným oprávněním byste měli po vyhodnocení rizik odebrat.

## <a name="enable-single-sign-on"></a>Povolit jednotné přihlašování

V cloudovém a prvním světě chcete povolit jednotné přihlašování (SSO) k zařízením, aplikacím a službám odkudkoli, takže uživatelé můžou být produktivní kdykoli a kdykoli. Pokud máte k dispozici více řešení pro správu identit, bude se jednat o problém správce, který není pro něj k dispozici, ale také uživatelům, kteří si musí pamatovat více hesel.

Pomocí stejného řešení identity pro všechny vaše aplikace a prostředky můžete dosáhnout jednotného přihlašování. A vaši uživatelé můžou použít stejnou sadu přihlašovacích údajů pro přihlášení a přístup k prostředkům, které potřebují, a to bez ohledu na to, jestli se prostředky nacházejí místně nebo v cloudu.

**Osvědčený postup**: povolení jednotného přihlašování.  
**Podrobnosti**: Azure AD [rozšiřuje místní službu Active Directory](/azure/active-directory/connect/active-directory-aadconnect) do cloudu. Uživatelé můžou použít svůj primární pracovní nebo školní účet pro svá zařízení připojená k doméně, prostředky společnosti a všechny webové a SaaS aplikace, které potřebují k tomu, aby získali své úlohy. Uživatelé si nemusí pamatovat několik sad uživatelských jmen a hesel a jejich přístup k aplikacím se dá automaticky zřídit (nebo zrušit zřízení) na základě členství ve skupinách organizace a jejich stavu jako zaměstnanec. Přístup můžete řídit u aplikací z galerie i u vlastních místních aplikací, které jste nasadili a publikovali přes [Proxy aplikací služby Azure AD](/azure/active-directory/active-directory-application-proxy-get-started).

Pomocí jednotného přihlašování můžete uživatelům povolit přístup k jejich [SaaS aplikacím](/azure/active-directory/active-directory-appssoaccess-whatis) na základě svého pracovního nebo školního účtu v Azure AD. To platí nejen pro aplikace Microsoft SaaS, ale i pro jiné aplikace, jako je [Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) nebo [Salesforce](/azure/active-directory/active-directory-saas-salesforce-tutorial). Aplikaci můžete nakonfigurovat tak, aby používala Azure AD jako zprostředkovatele [identity založeného na SAML](/azure/active-directory/fundamentals-identity) . V rámci řízení zabezpečení Azure AD nevydá token, který umožňuje uživatelům přihlásit se k aplikaci, pokud jim neudělí přístup prostřednictvím služby Azure AD. Přístup můžete udělit přímo nebo prostřednictvím skupiny, které jsou uživatelé členem.

Organizace, které nevytvářejí společnou identitu k navázání jednotného přihlašování pro svoje uživatele a aplikace, jsou k dispozici ve scénářích, kdy uživatelé mají více hesel. Tyto scénáře zvyšují pravděpodobnost, že uživatelé znovu používají hesla nebo používají slabá hesla.

## <a name="turn-on-conditional-access"></a>Zapnutí podmíněného přístupu

Uživatelé mají přístup k prostředkům vaší organizace pomocí nejrůznějších zařízení a aplikací odkudkoli. Jako správce IT chcete zajistit, aby tato zařízení splňovala vaše standardy pro zabezpečení a dodržování předpisů. Jenom se zaměřením na to, kdo má přístup k prostředku, už není dost.

Aby bylo možné zajistit rovnováhu mezi zabezpečením a produktivitou, je třeba zvážit, jak je prostředek přístupný, než můžete učinit rozhodnutí o řízení přístupu. Pomocí podmíněného přístupu Azure AD můžete tento požadavek vyřešit. S podmíněným přístupem můžete provádět automatizované rozhodování o řízení přístupu na základě podmínek pro přístup k vašim cloudovým aplikacím.

**Osvědčený postup**: Správa a řízení přístupu k podnikovým prostředkům.  
**Podrobnosti**: Konfigurace běžných [zásad podmíněného přístupu](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) Azure AD na základě skupin, umístění a citlivosti aplikací pro aplikace SaaS a aplikace připojené k Azure AD.

**Osvědčený postup**: blokování starších protokolů ověřování.
**Podrobnosti**: útočníci zneužije slabé stránky ve starších protokolech každý den, zejména pro útoky prostřednictvím spreje hesla. Nakonfigurujte podmíněný přístup k [blokování starších protokolů](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md).

## <a name="plan-for-routine-security-improvements"></a>Plánování běžných vylepšení zabezpečení

Zabezpečení je vždy vyvíjené a je důležité vytvořit do svého cloudu a architektury správy identit způsob, jak pravidelně zobrazovat růst a zjišťovat nové způsoby zabezpečení vašeho prostředí.

Identita Secure identity je sada doporučených kontrolních mechanismů zabezpečení, které společnost Microsoft zveřejňuje za účelem poskytnutí číselného skóre pro objektivní měření stav zabezpečení a snazšího plánování budoucích vylepšení zabezpečení. Skóre můžete zobrazit také v porovnání s ostatními odvětvími a také s vlastními trendy v průběhu času.

**Osvědčené postupy**: plánování rutin zabezpečení a vylepšení v závislosti na osvědčených postupech ve vašem odvětví.
**Podrobnosti**: k hodnocení vylepšení v průběhu času použijte funkci skóre Secure identity.

## <a name="enable-password-management"></a>Povolení správy hesel

Pokud máte více tenantů nebo chcete uživatelům umožnit, aby [obnovili vlastní hesla](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md), je důležité použít příslušné zásady zabezpečení, abyste zabránili zneužití.

**Osvědčený postup**: nastavte Samoobslužné resetování hesla (SSPR) pro vaše uživatele.  
**Podrobnosti**: použijte funkci [samoobslužného resetování hesla služby](/azure/active-directory-b2c/active-directory-b2c-reference-sspr) Azure AD.

**Osvědčený postup**: Sledujte, jak nebo jestli se SSPR skutečně používá.  
**Podrobnosti**: monitorování uživatelů, kteří se registrují, pomocí [sestavy aktivity registrace pro resetování hesel](/azure/active-directory/active-directory-passwords-get-insights)služby Azure AD. Funkce vytváření sestav, kterou poskytuje Azure AD, pomáhá odpovídat na otázky pomocí předem vytvořených sestav. Pokud máte patřičnou licenci, můžete také vytvořit vlastní dotazy.

**Osvědčený postup**: rozšíříte cloudové zásady hesel do místní infrastruktury.
**Podrobnosti**: Vylepšete zásady hesel ve vaší organizaci tak, že provedete stejné kontroly pro místní změny hesel, jako u cloudových změn hesel. Nainstalujte službu [Azure AD Password Protection](/azure/active-directory/authentication/concept-password-ban-bad) pro agenty Windows serveru Active Directory místně a rozšíříte seznamy zakázaných hesel na stávající infrastrukturu. Uživatelé a správci, kteří budou měnit, nastavovat nebo resetovat hesla v místním prostředí, se musí shodovat se stejnými zásadami hesel jako jenom uživatelé s pouze cloudy.

## <a name="enforce-multi-factor-verification-for-users"></a>Vymáhat vícefaktorové ověřování pro uživatele

Doporučujeme, abyste pro všechny uživatele vyžadovali dvoustupňové ověřování. To zahrnuje správce a jiné ve vaší organizaci, kteří můžou mít významný dopad na to, jestli je jejich účet ohrožený (například finanční důstojníci).

K dispozici je více možností pro Vyžadování dvoustupňového ověřování. Nejlepší možnost závisí na vašich cílech, edici Azure AD, kterou používáte, a na licenčním programu. Podívejte se, [jak vyžadovat dvoustupňové ověřování pro uživatele](/azure/active-directory/authentication/howto-mfa-userstates) , abyste mohli určit nejlepší možnost pro vás. Další informace o licencích a cenách najdete na stránce s cenami [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) a [Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) .

Níže jsou uvedené možnosti a výhody pro povolení dvoustupňové ověřování:

**Možnost 1**: Povolte vícefaktorové ověřování pro všechny uživatele a metody přihlašování s **využitím**služby Azure AD Security Defaults: Tato možnost umožňuje snadno a rychle vymáhat MFA pro všechny uživatele ve vašem prostředí s přísnou zásadou:

* Účty pro správu a mechanismy přihlášení pro správce
* Vyžadovat VÍCEFAKTOROVÉ ověřování pomocí Microsoft Authenticator pro všechny uživatele
* Omezte starší verze ověřovacích protokolů.

Tato metoda je dostupná pro všechny úrovně licencování, ale nemůže být smíšená se stávajícími zásadami podmíněného přístupu. Další informace najdete ve [výchozím nastavení zabezpečení Azure AD](/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) .

**Možnost 2**: [Povolení Multi-Factor Authentication změnou stavu uživatele](../../active-directory/authentication/howto-mfa-userstates.md).   
**Zvýhodnění**: Jedná se o tradiční metodu pro Vyžadování dvoustupňového ověřování. Funguje s [azure Multi-Factor Authentication v cloudu i v azure Multi-Factor Authentication Server](/azure/active-directory/authentication/concept-mfa-whichversion). Použití této metody vyžaduje, aby uživatelé prováděli dvoustupňové ověřování pokaždé, když se přihlásí, a přepíše zásady podmíněného přístupu.

Pokud chcete zjistit, kde Multi-Factor Authentication musí být povolená, podívejte [se, jakou verzi Azure MFA je pro moji organizaci nejvhodnější?](/azure/active-directory/authentication/concept-mfa-whichversion).

**Možnost 3**: [Povolení Multi-Factor Authentication s využitím zásad podmíněného přístupu](/azure/active-directory/authentication/howto-mfa-getstarted)
**Zvýhodnění**: Tato možnost vám umožní zobrazit dotaz na dvoustupňové ověřování za určitých podmínek pomocí [podmíněného přístupu](../../active-directory/conditional-access/concept-conditional-access-policy-common.md). Konkrétní podmínky se můžou přihlašovat uživateli z různých umístění, nedůvěryhodných zařízení nebo aplikací, které považujete za rizikové. Definování specifických podmínek, které vyžadují dvoustupňové ověřování, vám umožní vyhnout se tak neustálým dotazům pro uživatele, což může být nepříjemný zážitek uživatele.

Toto je nejpružnější způsob, jak povolit dvoustupňové ověřování pro vaše uživatele. Povolení zásad podmíněného přístupu funguje jenom pro Azure Multi-Factor Authentication v cloudu a je funkcí Premium služby Azure AD. Další informace o této metodě najdete v [nasazení cloudové Multi-Factor Authentication Azure](/azure/active-directory/authentication/howto-mfa-getstarted).

**Možnost 4**: povolení Multi-Factor Authentication se zásadami podmíněného přístupu vyhodnocením [zásad podmíněného přístupu na základě rizika](../../active-directory/conditional-access/howto-conditional-access-policy-risk.md).   
**Výhody**: Tato možnost umožňuje:

* Detekuje potenciální ohrožení zabezpečení, která ovlivňují identity vaší organizace.
* Konfigurace automatizovaných odpovědí na zjištěné podezřelé akce, které se vztahují k identitám vaší organizace.
* Prozkoumejte podezřelé incidenty a proveďte odpovídající opatření k jejich vyřešení.

Tato metoda používá Azure AD Identity Protection hodnocení rizik k určení, jestli se vyžaduje dvoustupňové ověřování na základě rizika uživatele a přihlašování pro všechny cloudové aplikace. Tato metoda vyžaduje licencování Azure Active Directory P2. Další informace o této metodě najdete v [Azure Active Directory Identity Protection](/azure/active-directory/identity-protection/overview).

> [!Note]
> Možnost 2, povolení Multi-Factor Authentication změnou stavu uživatele, Přepisuje zásady podmíněného přístupu. Protože možnosti 3 a 4 používají zásady podmíněného přístupu, nemůžete s nimi použít parametr 2.

Organizace, které neposkytují další vrstvy ochrany identity, jako je například dvoustupňové ověřování, jsou náchylnější k útoku prostřednictvím krádeže přihlašovacích údajů. Útok krádeže přihlašovacích údajů může vést k ohrožení bezpečnosti dat.

## <a name="use-role-based-access-control"></a>Použití řízení přístupu na základě rolí

Správa přístupu pro cloudové prostředky je kritická pro všechny organizace, které využívají cloud. [Řízení přístupu na základě role v Azure (Azure RBAC)](/azure/role-based-access-control/overview)pomáhá spravovat, kdo má přístup k prostředkům Azure, co s těmito prostředky může dělat a k jakým oblastem mají přístup.

Označení skupin nebo jednotlivých rolí zodpovědných za konkrétní funkce v Azure pomáhá zabránit nejasnostem, které mohou vést k chybám lidského a automatizace, které vytvářejí bezpečnostní rizika. Omezení přístupu na základě [potřebných znalostí](https://en.wikipedia.org/wiki/Need_to_know) a [minimálních](https://en.wikipedia.org/wiki/Principle_of_least_privilege) principů zabezpečení oprávnění je nezbytné pro organizace, které chtějí vynutilit zásady zabezpečení pro přístup k datům.

Aby mohl váš tým zabezpečení vyhodnocovat a napravovat riziko, musí mít přehled o vašich prostředcích Azure. Pokud má tým zabezpečení provozní zodpovědnost, potřebuje další oprávnění ke své práci.

Pomocí [RBAC](/azure/role-based-access-control/overview) můžete přiřadit oprávnění uživatelům, skupinám a aplikacím v určitém oboru. Role se dají přidělovat na úrovni předplatného, skupiny prostředků nebo konkrétního prostředku.

**Osvědčené postupy**: oddělení funkcí v rámci týmu a udělení přístupu pouze uživatelům, kteří potřebují k provádění svých úloh. Místo udělení všech neomezených oprávnění v rámci předplatného Azure nebo prostředků Povolte jenom určité akce v konkrétním oboru.
**Podrobnosti**: k přiřazení oprávnění uživatelům použijte [předdefinované role Azure](/azure/role-based-access-control/built-in-roles) v Azure.

> [!Note]
> Konkrétní oprávnění vytvářejí nepotřebnou složitost a nejasnost a nahromadění do konfigurace starší verze, která je obtížné opravit, aniž by došlo k obavám. Vyhněte se oprávněním pro konkrétní prostředky. Místo toho použijte pro oprávnění v rámci předplatných skupiny pro správu oprávnění a skupin prostředků pro podnikové úrovni. Vyhněte se konkrétním uživatelským oprávněním. Místo toho přiřaďte přístup ke skupinám v Azure AD.

**Osvědčený postup**: poskytnutím bezpečnostních týmů s použitím povinností Azure získáte přístup k prostředkům Azure, aby mohli vyhodnotit a opravit rizika.
**Podrobnosti**: udělení zabezpečení týmům roli [Čtenář zabezpečení](/azure/role-based-access-control/built-in-roles#security-reader) RBAC. Můžete použít kořenovou skupinu pro správu nebo skupinu pro správu segmentů v závislosti na rozsahu zodpovědnosti:

* **Kořenová skupina pro správu** pro týmy zodpovědné za všechny podnikové prostředky
* **Skupina pro správu segmentů** pro týmy s omezeným rozsahem (obvykle z důvodu zákonných nebo jiných organizačních hranic)

**Osvědčený postup**: Udělte patřičným oprávněním bezpečnostním týmům, které mají přímé provozní zodpovědnosti.
**Podrobnosti**: Projděte si předdefinované role RBAC pro příslušné přiřazení role. Pokud předdefinované role nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit [vlastní role Azure](/azure/role-based-access-control/custom-roles). Stejně jako u integrovaných rolí můžete uživatelům, skupinám a instančním objektům přiřadit vlastní role v předplatném, skupině prostředků a oborech prostředků.

**Osvědčené postupy**: Udělte Azure Security Center přístup k rolím zabezpečení, které ho potřebují. Security Center umožňuje týmům zabezpečení rychle identifikovat a opravit rizika.
**Podrobnosti**: přidejte bezpečnostní týmy s těmito požadavky do role [Správce zabezpečení](/azure/role-based-access-control/built-in-roles#security-admin) RBAC, aby mohli zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazit výstrahy a doporučení a zrušit výstrahy a doporučení. To můžete provést pomocí kořenové skupiny pro správu nebo skupiny pro správu segmentu v závislosti na rozsahu zodpovědnosti.

Organizace, které vynutily řízení přístupu k datům pomocí možností, jako je RBAC, můžou udělit větší oprávnění, než je nutné pro uživatele. To může vést k ohrožení dat tím, že uživatelům umožní získat přístup k typům dat (například vysoký dopad na firmu), které by neměli mít.

## <a name="lower-exposure-of-privileged-accounts"></a>Nižší vystavení privilegovaných účtů

Zabezpečení privilegovaného přístupu je důležitým prvním krokem k ochraně obchodních prostředků. Minimalizace počtu uživatelů, kteří mají přístup k zabezpečeným informacím nebo prostředkům, snižuje riziko, že uživatel se zlými úmysly získává přístup, nebo autorizovaný uživatel neúmyslně ovlivnil citlivý prostředek.

Privilegované účty jsou účty, které spravují a spravují systémy IT. Internetoví útočníci cílí na tyto účty, aby získali přístup k datům a systémům organizace. Aby bylo možné zabezpečit privilegovaný přístup, měli byste izolovat účty a systémy od rizika vystaveného uživateli se zlými úmysly.

Doporučujeme vyvinout a postupovat podle plánu zabezpečení privilegovaného přístupu proti internetovým útočníkům. Informace o vytvoření podrobného plánu pro zabezpečení identit a přístupu, které jsou spravované nebo nahlášené ve službě Azure AD, Microsoft Azure, Microsoft 365 a dalších cloudových službách, najdete [v popisu zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure).

Následující shrnuje osvědčené postupy, které najdete v článku [zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure):

**Osvědčený postup**: Správa, řízení a sledování přístupu k privilegovaným účtům.   
**Podrobnosti**: zapněte [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access). Po zapnutí Privileged Identity Management obdržíte e-mailové zprávy s oznámením o změnách role privilegovaného přístupu. Tato oznámení poskytují včasné upozornění, když se do vašeho adresáře přidají další uživatelé do vysoce privilegovaných rolí.

**Osvědčený postup**: Ujistěte se, že všechny kritické účty správců jsou spravované účty Azure AD.
**Podrobnosti**: Odeberte všechny uživatelské účty z klíčových rolí správce (například účty Microsoft, jako je hotmail.com, live.com a Outlook.com).

**Osvědčený postup**: Ujistěte se, že všechny důležité role správců mají samostatný účet pro úlohy správy, aby se zabránilo útokům phishing a dalším útokům na ohrožení oprávnění správce.
**Podrobnosti**: Vytvořte samostatný účet správce, kterému je přiřazeno oprávnění potřebná k provádění úloh správy. Zablokujte používání těchto účtů pro správu každodenních nástrojů pro zvýšení produktivity, jako je Microsoft 365 e-mailu nebo prohlížení libovolného webu.

**Osvědčený postup**: identifikace a kategorizace účtů, které jsou ve vysoce privilegovaných rolích.   
**Podrobnosti**: po zapnutí Azure AD Privileged Identity Management se podívejte na uživatele, kteří jsou v globálním správci, správce privilegovaných rolí a další role s vysokou úrovní oprávnění. Odeberte všechny účty, které už v těchto rolích nepotřebujete, a zařaďte do kategorií zbývající účty, které jsou přiřazené rolím Správce:

* Samostatně přiřazené administrativním uživatelům a lze je použít pro účely bez správy (například osobní e-mail).
* Přiřazeno individuálně správcům a určen pouze pro účely správy
* Sdíleno mezi více uživateli
* Pro scénáře nouzového přístupu
* Pro automatizované skripty
* Pro externí uživatele

**Osvědčený postup**: implementace "just in time" (JIT) k dalšímu snížení doby expozice oprávnění a zvýšení viditelnosti používání privilegovaných účtů.   
**Podrobnosti**: Azure AD Privileged Identity Management vám umožní:

* Omezte uživatelům, aby přebírali jenom na jejich oprávnění JIT.
* Přiřaďte role pro zkrácenou dobu trvání a jistotu, že se oprávnění odvolají automaticky.

**Osvědčený postup**: Definujte aspoň dva účty pro nouzový přístup.   
**Podrobnosti**: účty pro nouzový přístup umožňují organizacím omezit privilegovaný přístup v existujícím prostředí Azure Active Directory. Tyto účty jsou vysoce privilegované a nepřiřazují se konkrétním jednotlivcům. Účty pro nouzový přístup jsou omezeny na scénáře, ve kterých nelze použít běžné účty pro správu. Organizace musí omezit využívání účtu na nouzový účet jenom na nezbytné množství času.

Vyhodnoťte účty, které jsou přiřazeny nebo mají nárok na roli globálního správce. Pokud nevidíte žádné účty pouze cloudu, které používají `*.onmicrosoft.com` doménu (určené pro nouzový přístup), vytvořte je. Další informace najdete v tématu [Správa účtů pro správu pro nouzový přístup ve službě Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access).

**Osvědčený postup**: v případě nouze je potřeba mít na začátku proces "break sklo".
**Podrobnosti**: postupujte podle kroků v části [zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure).

**Osvědčený postup**: vyžadovat, aby všechny kritické účty správců byly bez hesla (preferované), nebo vyžadovat Multi-Factor Authentication.
**Podrobnosti**: pomocí [aplikace Microsoft Authenticator](/azure/active-directory/authentication/howto-authentication-phone-sign-in) se přihlaste k jakémukoli účtu Azure AD bez použití hesla. Stejně jako ve [Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-identity-verification)používá Microsoft Authenticator k povolení přihlašovacích údajů uživatele, které jsou svázané se zařízením, a používá biometrické ověřování nebo PIN kód.

Vyžadovat Multi-Factor Authentication Azure při přihlášení pro všechny uživatele, kteří jsou trvale přiřazeni k jedné nebo více rolím správce Azure AD: globální správce, správce privilegovaných rolí, správce Exchange Online a správce SharePointu Online. Povolte [Multi-Factor Authentication pro účty správců](/azure/active-directory/authentication/howto-mfa-userstates) a zajistěte, aby se zaregistrovali uživatelé účtu správce.

**Osvědčený postup**: u důležitých účtů správců máte pracovní stanici správce, kde nejsou provozní úlohy povolené (například procházení a e-mail). Tím se budou chránit účty správců před vektory útoku, které používají procházení a e-maily, a výrazně snižuje riziko závažného incidentu.
**Podrobnosti**: použijte pracovní stanici správce. Vyberte úroveň zabezpečení pracovní stanice:

- Vysoce zabezpečená zařízení pro zvýšení produktivity poskytují pokročilé zabezpečení pro procházení a další úlohy produktivity.
- [Pracovní stanice s privilegovaným přístupem (privilegovaným přístupem)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) poskytují vyhrazený operační systém chráněný před útoky z Internetu a vektory hrozeb pro citlivé úkoly.

**Osvědčený postup**: zrušení zřízení účtů správce, když zaměstnanci odejdou z vaší organizace.
**Podrobnosti**: má zavedený proces, který zakáže nebo odstraní účty správců, když zaměstnanci odejdou z vaší organizace.

**Osvědčený postup**: pravidelné testování účtů správců pomocí současných technik útoku.
**Podrobnosti**: Microsoft 365 simulátoru útoku nebo nabídky třetí strany ke spouštění reálných scénářů útoku ve vaší organizaci. To vám může pomáhat najít ohrožené uživatele předtím, než dojde k reálnému útoku.

**Osvědčený postup**: proveďte kroky pro zmírnění nejčastěji používaných technik útoku.  
**Podrobnosti**: [Identifikujte účty Microsoft v rolích pro správu, které je potřeba přepnout na pracovní nebo školní účty](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts) .  

[Zajistěte samostatné uživatelské účty a přesměrování pošty pro účty globálních správců.](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[Ujistěte se, že se nedávno změnila hesla účtů pro správu.](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Zapnout synchronizaci hodnot hash hesel](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[Vyžadovat Multi-Factor Authentication pro uživatele ve všech privilegovaných rolích a také vyexponovaných uživatelů](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Získat Microsoft 365 zabezpečeného skóre (Pokud používáte Microsoft 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[Přečtěte si pokyny pro zabezpečení Microsoft 365 (Pokud používáte Microsoft 365).](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Konfigurace monitorování aktivity Microsoft 365 (při použití Microsoft 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

[Navázání vlastníků plánů pro incidenty nebo naléhavé reakce](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#establish-incidentemergency-response-plan-owners)  

[Zabezpečení místních privilegovaných účtů pro správu](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)

Pokud jste nezabezpečili privilegovaný přístup, možná zjistíte, že máte příliš mnoho uživatelů v rolích s vysokou úrovní oprávnění a jsou zranitelnější vůči útokům. Škodlivé objekty actor, včetně nejčastějších útočníků, často cílí na účty správců a další prvky privilegovaného přístupu k získání přístupu k citlivým datům a systémům pomocí krádeže přihlašovacích údajů.

## <a name="control-locations-where-resources-are-created"></a>Ovládací prvky umístění, kde jsou vytvářeny prostředky

Povolením operátorů cloudu můžete provádět úlohy a zabránit tak jejich přerušení v přestupnosti, které jsou potřeba ke správě prostředků vaší organizace, jsou velmi důležité. Organizace, které chtějí řídit umístění, kde jsou prostředky vytvořeny, by měly zakódovat tato umístění.

Pomocí [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) můžete vytvořit zásady zabezpečení, jejichž definice popisují akce nebo prostředky, které jsou výslovně odepřeny. Tyto definice zásad přiřazujete požadovanému oboru, jako je například předplatné, skupina prostředků nebo jednotlivé prostředky.

> [!NOTE]
> Zásady zabezpečení nejsou stejné jako RBAC. Ve skutečnosti používají RBAC k autorizaci uživatelů k vytváření těchto prostředků.
>
>

Organizace, které neřídí způsob vytváření prostředků, jsou náchylnější uživatelům, kteří můžou službu zneužít, vytvořením více prostředků, než potřebují. Posílení zabezpečení procesu vytváření prostředků je důležitým krokem pro zabezpečení víceklientské scénáře.

## <a name="actively-monitor-for-suspicious-activities"></a>Aktivně monitorovat podezřelé aktivity

Aktivní systém monitorování identit může rychle detekovat podezřelé chování a aktivovat upozornění pro další šetření. V následující tabulce jsou uvedeny dvě možnosti služby Azure AD, které mohou organizacím monitorovat své identity:

**Osvědčený postup**: máte metodu pro identifikaci:

- Pokusy o přihlášení [bez trasování](/azure/active-directory/active-directory-reporting-sign-ins-from-unknown-sources).
- Útoky [hrubou silou](/azure/active-directory/active-directory-reporting-sign-ins-after-multiple-failures) na konkrétní účet.
- Pokusí se přihlásit z více míst.
- Přihlášení z [nakažených zařízení](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices).
- Podezřelé IP adresy.

**Podrobnosti**: použijte Azure AD Premium [sestavy anomálií](/azure/active-directory/active-directory-view-access-usage-reports). Všechny procesy a postupy pro správce IT můžou spouštět tyto sestavy každý den nebo na vyžádání (obvykle ve scénáři reakce na incidenty).

**Osvědčený postup**: máte aktivní monitorovací systém, který vás upozorní na rizika a může upravit úroveň rizika (vysoká, střední nebo nízká) na požadavky vaší firmy.   
**Podrobnosti**: použijte [Azure AD Identity Protection](/azure/active-directory/active-directory-identityprotection), které označí aktuální rizika na vlastním řídicím panelu a pošle denní souhrnná oznámení prostřednictvím e-mailu. Aby bylo možné chránit identity vaší organizace, můžete nakonfigurovat zásady založené na rizicích, které automaticky reagují na zjištěné problémy při dosažení zadané úrovně rizika.

Organizace, které aktivně nemonitorují své systémy identit, jsou ohrožené tím, že mají ohrožení zabezpečení uživatele. Bez vědomí, že tyto přihlašovací údaje probíhají podezřelé aktivity, nemůžou organizace zmírnit tento typ hrozby.

## <a name="use-azure-ad-for-storage-authentication"></a>Použití Azure AD k ověřování úložiště
[Azure Storage](/azure/storage/common/storage-auth-aad) podporuje ověřování a autorizaci se službou Azure AD pro úložiště objektů BLOB a úložiště front. Pomocí ověřování Azure AD můžete použít řízení přístupu na základě role Azure k udělení konkrétních oprávnění uživatelům, skupinám a aplikacím do rozsahu samostatného kontejneru objektů BLOB nebo fronty.

[Pro ověřování přístupu k úložišti doporučujeme použít službu Azure AD](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Další krok

V článku [osvědčené postupy a vzory zabezpečení Azure](best-practices-and-patterns.md) najdete v tématu Doporučené postupy zabezpečení pro použití při navrhování, nasazování a správě cloudových řešení pomocí Azure.
