---
title: Cloudová Správa Azure AD řídí správu místních úloh – Azure
description: Toto téma popisuje správu v cloudu, která se řídí správou pro místní úlohy.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b4d1041b9d330227fadf31f6afc1804174ea2ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96340845"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Jak Azure AD nabízí cloudovou správu pro místní úlohy

Azure Active Directory (Azure AD) je komplexní řešení identity jako služby (IDaaS) používané miliony organizací, které zahrnuje všechny aspekty identity, správy přístupu a zabezpečení. Azure AD uchovává více než miliarda uživatelských identit a pomáhá uživatelům se přihlásit a bezpečně přistupovat k oběma:

* Externí prostředky, například Microsoft 365, Azure Portal a tisíce dalších aplikací SaaS (software jako služba).
* Interní prostředky, jako jsou aplikace v podnikové síti a intranetu organizace, spolu s jakýmikoli cloudovou aplikací vyvinutou touto organizací.

Organizace můžou použít Azure AD, pokud jsou čistě Cloud, nebo jako hybridní nasazení, pokud mají místní úlohy. Hybridní nasazení služby Azure AD může být součástí strategie organizace, která migruje své IT prostředky do cloudu, nebo dál integrovat stávající místní infrastrukturu společně s novými Cloud Services.

"Hybrid" organizace viděli jako rozšíření své stávající místní infrastruktury službu Azure AD. V těchto nasazeních jsou řídicí body místní Správa identit, služba Windows Server Active Directory nebo jiné interní adresářové systémy, a uživatelé a skupiny se synchronizují z těchto systémů do cloudového adresáře, jako je například Azure AD. Jakmile jsou tyto identity v cloudu, dají se zpřístupnit pro Microsoft 365, Azure a další aplikace.

![Životní cyklus identity](media/cloud-governed-management-for-on-premises//image1.png)

Vzhledem k tomu, že organizace přesouvá více své IT infrastruktury spolu s aplikacemi do cloudu, mnoho z nich hledá vylepšené možnosti zabezpečení a zjednodušenou správu správy identit jako služby. Cloudové funkce IDaaS ve službě Azure AD urychlují přechod do cloudu, který spravuje správu, tím, že poskytuje řešení a možnosti, které organizacím umožňují rychle přijmout a přesunout další správu identit z tradičních místních systémů do služby Azure AD a přitom nadále podporovat stávající i nové aplikace.

Tento dokument popisuje strategii Microsoftu pro hybridní IDaaS a popisuje, jak můžou organizace používat Azure AD pro stávající aplikace.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Přístup Azure AD k řízení identit v cloudu, který řídí.

Protože organizace přecházejí do cloudu, potřebují ujištění, že mají kontrolu nad jejich kompletním prostředím – lepší zabezpečení a lepší přehled o aktivitách, které podporuje automatizace a proaktivní přehledy. "**Správa řízená v cloudu**" popisuje, jak organizace spravují a řídí své uživatele, aplikace, skupiny a zařízení z cloudu.

V tomto moderním světě je potřeba, aby organizace byly schopné efektivně spravovat ve velkém měřítku, a to z důvodu rozšiřování aplikací SaaS a rostoucí role spolupráce a externích identit. Nové riziko v cloudu znamená, že organizace musí být delší reagovat – škodlivý objekt actor, který by mohl ohrozit cloudový uživatel, by mohl ovlivnit cloudové a místní aplikace.

V některých případech je potřeba, aby hybridní organizace mohli delegovat a automatizovat úlohy, které historicky použily. K automatizaci úloh potřebují rozhraní API a procesy, které orchestrují životní cyklus různých prostředků souvisejících s identitou (uživatelé, skupiny, aplikace a zařízení), aby mohli delegovat každodenní správu těchto prostředků na více jednotlivců mimo hlavní pracovníky IT. Azure AD řeší tyto požadavky prostřednictvím správy uživatelských účtů a nativního ověřování pro uživatele bez nutnosti místní infrastruktury identity. Nevytvářejte místní infrastrukturu můžou těžit z organizací, které mají nové komunity uživatelů, jako jsou obchodní partneři, které nepocházejí z místního adresáře, ale jejichž Správa přístupu je pro dosažení obchodních výsledků velmi důležitá.

Kromě toho se Správa nedokončila bez zásad správného řízení---a zásad správného řízení v tomto novém světě je integrovaná součást systému identit, nikoli doplněk. Řízení identit poskytuje organizacím možnost spravovat životní cyklus identit a přístupu napříč zaměstnanci, obchodními partnery a dodavateli a službami a aplikacemi.

Začlenění zásad správného řízení identit usnadňuje organizacím přechod na správu na základě cloudu, umožňuje IT škálování, řeší nové výzvy s hosty a poskytuje hlubší přehledy a automatizaci než zákazníci s místní infrastrukturou. Řízení v tomto novém světě znamená, že organizace může mít transparentnost, viditelnost a správné ovládací prvky pro přístup k prostředkům v organizaci. V rámci Azure AD mají operace zabezpečení a týmy pro audit přehled o tom, kdo má---a kdo má mít přístup k jakým prostředkům v organizaci (na těchto zařízeních), co uživatelé s tímto přístupem dělají a jestli má organizace a používat vhodné ovládací prvky pro odebrání nebo omezení přístupu v souladu se zásadami společnosti nebo legislativních zásad.

Nový model správy přináší výhody organizacím s SaaS i obchodními aplikacemi, protože je snazší spravovat a zabezpečit přístup k těmto aplikacím. Díky integraci aplikací se službou Azure AD budou organizace moct používat a spravovat přístup v cloudových i místních pocházejících identitách konzistentně. Správa životního cyklu aplikací se vylepší automatizovaně a služba Azure AD poskytuje podrobné přehledy o využití aplikací, které není snadno dosažitelné v místní správě identit. Prostřednictvím samoobslužných funkcí služby Azure AD, Microsoft 365 skupin a týmů, můžou organizace snadno vytvářet skupiny pro správu a spolupráci přístupu a přidávat nebo odebírat uživatele v cloudu, aby bylo možné spravovat požadavky na spolupráci a řízení přístupu.

Výběr správných funkcí služby Azure AD pro správu v cloudu závisí na tom, jaké aplikace se mají použít, a na tom, jak budou tyto aplikace integrovány do Azure AD. V následujících částech najdete přehled přístupů k aplikacím integrovaným se službou Active Directory a aplikacích, které používají federační protokoly (například SAML, OAuth nebo OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Správa založená na cloudu pro aplikace integrované s AD

Azure AD vylepšuje správu místních aplikací integrovaných ve službě Active Directory v organizaci prostřednictvím zabezpečeného vzdáleného přístupu a podmíněného přístupu k těmto aplikacím. Kromě toho Azure AD poskytuje taky správu životního cyklu účtu a správu přihlašovacích údajů pro existující účty AD uživatele, včetně těchto:

* **Zabezpečený vzdálený přístup a podmíněný přístup pro místní aplikace**

V případě mnoha organizací je prvním krokem při správě přístupu z cloudu pro místní webové a vzdálené aplikace založené na službě AD nasazení [proxy aplikací](../manage-apps/application-proxy.md) před těmito aplikacemi za účelem zajištění zabezpečeného vzdáleného přístupu.

Po jednotném přihlášení ke službě Azure AD můžou uživatelé přistupovat ke cloudovým i místním aplikacím prostřednictvím externí adresy URL nebo interního portálu aplikací. Například proxy aplikací poskytuje vzdálený přístup a jednotné přihlašování ke vzdálené ploše, SharePointu a také aplikacím, jako je Tableau a Qlik, a obchodním aplikacím (LOB). Kromě toho můžou zásady podmíněného přístupu zahrnovat zobrazení [podmínek použití](../conditional-access/terms-of-use.md) a [zajištění jejich souhlasu s uživatelem](../conditional-access/require-tou.md) , aby bylo možné získat přístup k aplikaci.

![Architektura proxy aplikací](media/cloud-governed-management-for-on-premises/image2.png)

* **Automatická správa životního cyklu pro účty služby Active Directory**

Řízení identit pomáhá organizacím dosáhnout rovnováhy mezi *produktivitou* ---, jak rychle může osoba získat přístup k potřebným prostředkům, například když se připojí k organizaci? ---a *zabezpečení* ---jak se má v průběhu času měnit přístup, například když se změní stav zaměstnanosti této osoby? Správa životního cyklu identit je základem pro řízení identit a efektivní řízení škálování vyžaduje modernizaci infrastrukturu správy životního cyklu identit pro aplikace.

Pro mnoho organizací je životní cyklus identit zaměstnanců vázaný na zastoupení daného uživatele v systému HCM (Human kapitálu Management). Pro organizace, které používají Workday jako svůj HCM systém, může Azure AD zajistit, aby se uživatelské účty ve službě AD [automaticky zřídily a zrušily pro pracovní procesy v Workday](../saas-apps/workday-inbound-tutorial.md). To vede k lepší produktivitě uživatelů díky automatizaci účtů Birthright a spravuje rizika tím, že zajistí, že se přístup k aplikaci automaticky aktualizuje, když uživatel změní role nebo opustí organizaci. [Plán nasazení](https://aka.ms/WorkdayDeploymentPlan) zřizování uživatelů na základě pracovního dne je podrobný průvodce, který prochází organizace prostřednictvím implementace osvědčených postupů pro řešení zřizování uživatelů v rámci služby Active Directory v procesu s pěti kroky.

Azure AD Premium také zahrnuje Microsoft Identity Manager, které mohou importovat záznamy z dalších místních systémů HCM, včetně SAP, Oracle eBusiness a Oracle PeopleSoft spouštěných místně.

Spolupráce mezi společnostmi stále vyžaduje udělení přístupu lidem mimo vaši organizaci. Spolupráce [B2B Azure AD](/azure/active-directory/b2b/) umožňuje organizacím bezpečně sdílet své aplikace a služby s uživateli typu Host a externími partnery a přitom zachovat kontrolu nad svými vlastními podnikovými daty.

Služba Azure AD může [automaticky vytvářet účty v rámci služby AD pro uživatele typu Host](../external-identities/hybrid-cloud-to-on-premises.md) podle potřeby a umožnit tak obchodním hostům přístup k místním aplikacím integrovaným službou AD bez nutnosti dalšího hesla. Organizace můžou nastavit [zásady vícefaktorového ověřování (MFA) pro uživatele typu Host](../external-identities/conditional-access.md), takže kontroly MFA se provádějí při ověřování proxy aplikací. Také všechny kontroly [přístupu](../governance/manage-guest-access-with-access-reviews.md) , které jsou provedeny u cloudových uživatelů B2B, platí pro místní uživatele. Pokud se například uživatel cloudu odstraní prostřednictvím zásad správy životního cyklu, místní uživatel se odstraní také.

**Správa přihlašovacích údajů pro účty služby Active Directory** Samoobslužné resetování hesla služby Azure AD umožňuje uživatelům, kteří si zapomněli svá hesla, znovu ověřit a resetovat hesla, a to u změněných hesel [zapsaných do místní služby Active Directory](../authentication/concept-sspr-writeback.md). Proces resetování hesla může také používat místní zásady hesel služby Active Directory: když uživatel resetuje heslo, před jeho potvrzením do tohoto adresáře se ověří, jestli splňuje místní zásady služby Active Directory. [Plán nasazení](../authentication/howto-sspr-deployment.md) samoobslužného resetování hesla popisuje osvědčené postupy pro zavedení samoobslužného resetování hesla uživatelům přes prostředí integrované na webu a v systému Windows.

![Architektura Azure AD SSPR](media/cloud-governed-management-for-on-premises/image3.png)

V případě organizací, které uživatelům umožňují měnit hesla ve službě AD, může být služba AD nakonfigurovaná tak, aby používala stejné zásady hesel, jakou vaše organizace používá ve službě Azure AD prostřednictvím [funkce ochrany heslem služby Azure AD](../authentication/concept-password-ban-bad-on-premises.md), která je aktuálně ve verzi Public Preview.

Když je organizace připravena přesunout aplikaci integrovanou se službou Active Directory do cloudu přesunutím operačního systému, který hostuje aplikaci do Azure, [Azure AD Domain Services](../../active-directory-domain-services/overview.md) poskytne služby domény kompatibilní s AD (například připojení k doméně, zásady skupiny, LDAP a ověřování protokolu Kerberos/NTLM). Azure AD Domain Services se integruje s existujícím klientem Azure AD organizace, takže se uživatelé můžou přihlašovat pomocí svých podnikových přihlašovacích údajů. Existující skupiny a uživatelské účty se navíc dají použít k zabezpečení přístupu k prostředkům a zajištění plynulého navýšení místních prostředků na služby infrastruktury Azure.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Správa založená na cloudu pro místní federační aplikace

V organizaci, která už používá místního zprostředkovatele identity, umožňuje přesouvání aplikací do Azure AD bezpečnější přístup a jednodušší administrativní prostředí pro správu federace. Azure AD umožňuje konfigurovat podrobné řízení přístupu k jednotlivým aplikacím, včetně Azure AD Multi-Factor Authentication, pomocí podmíněného přístupu Azure AD. Azure AD podporuje další možnosti, včetně podpisových certifikátů tokenů specifických pro aplikace a konfigurovatelného data vypršení platnosti certifikátu. Tyto možnosti, nástroje a pokyny umožňují organizacím vyřadit své místní zprostředkovatele identity. Vlastní IT společnost od Microsoftu přesunula například 17 987 aplikací z interního Active Directory Federation Services (AD FS) (AD FS) Microsoftu do Azure AD.

![Vývoj pro Azure AD](media/cloud-governed-management-for-on-premises/image5.png)

Pokud chcete začít migrovat federované aplikace do služby Azure AD jako zprostředkovatele identity, Projděte si https://aka.ms/migrateapps téma, které obsahuje odkazy na:

* Dokument White Paper, který [migruje vaše aplikace do Azure Active Directory](https://aka.ms/migrateapps/whitepaper), který představuje výhody migrace a popisuje, jak naplánovat migraci ve čtyřech jasně stanovených fázích: zjišťování, klasifikace, migrace a průběžná správa. Provedete vás postupem, jak se zamyslet na proces a rozdělte projekt na snadno ovladatelné části. V celém dokumentu jsou odkazy na důležité prostředky, které vám pomůžou na cestě.

* Průvodce řešením [migruje ověřování aplikace z Active Directory Federation Services (AD FS) a Azure Active Directory](../manage-apps/migrate-adfs-apps-to-azure.md) se podrobněji prozkoumá stejné čtyři fáze plánování a provádění projektu migrace aplikace. V této příručce se dozvíte, jak tyto fáze použít na konkrétní cíl přesunutí aplikace z Active Directory Federation Services (AD FS) (AD FS) do služby Azure AD.

* [Skript připravenosti na migraci Active Directory Federation Services (AD FS)](https://aka.ms/migrateapps/adfstools) můžete spustit na stávajících serverech místního Active Directory Federation Services (AD FS) (AD FS) a určit připravenost aplikací pro migraci do Azure AD.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Průběžná správa přístupu napříč cloudem a místními aplikacemi

Organizace potřebují postup pro správu přístupu, který je škálovatelný. Uživatelé pokračují v hromadění přístupových práv a ukončí se nad rámec toho, co se pro ně poprvé zřídilo. Kromě toho musí být organizace v rozlehlých sítích schopné efektivně škálovat a průběžně vyvíjet a vysazovat zásady přístupu a ovládací prvky.

Obvykle IT deleguje rozhodnutí o schválení přístupu u tvůrců obchodních rozhodnutí. Kromě toho může zahrnovat samotné uživatele. Například uživatelé, kteří mají přístup k důvěrným zákaznickým datům v obchodní aplikaci společnosti v Evropě, potřebují znát zásady společnosti. Uživatelé typu Host můžou také vědět, že požadavky na zpracování dat v organizaci, na kterou byli pozvánky, jsou popsány.

Organizace můžou proces životního cyklu přístupu automatizovat prostřednictvím technologií, jako jsou [dynamické skupiny](../enterprise-users/groups-dynamic-membership.md), společně s zřizováním uživatelů pro [SaaS aplikace](../saas-apps/tutorial-list.md)nebo [aplikací, které jsou integrované pomocí systému pro standard SCIM (napříč doménovou správu identit](../app-provisioning/use-scim-to-provision-users-and-groups.md)). Organizace také mohou určovat, kteří [Uživatelé typu Host mají přístup k místním aplikacím](../external-identities/hybrid-cloud-to-on-premises.md). Tato přístupová práva se pak dají pravidelně kontrolovat pomocí opakujících se kontrol [přístupu Azure AD](../governance/access-reviews-overview.md).

## <a name="future-directions"></a>Budoucí pokyny

V hybridních prostředích má strategie Microsoftu možnost povolit nasazení, kde **Cloud je řídicí plocha pro identitu**, a místní adresáře a jiné systémy identit, jako je služba Active Directory a další místní aplikace, jsou cílem zřizování uživatelů s přístupem. Tato strategie bude nadále zajišťovat práva, identity a přístup v těchto aplikacích a úlohách, které je spoléhají na ně. V tomto konečném stavu budou organizace moci řídit produktivitu koncových uživatelů výhradně z cloudu.

![Architektura Azure AD](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak začít s touto cestou, najdete v tématu plány nasazení služby Azure AD, které se nacházejí v <https://aka.ms/deploymentplans> . Poskytují ucelené pokyny k nasazení možností služby Azure Active Directory (Azure AD). Každý plán vysvětluje obchodní hodnotu, požadavky na plánování, návrh a provozní postupy potřebné k úspěšnému zavedení běžných funkcí služby Azure AD. Microsoft průběžně aktualizuje plány nasazení s doporučenými postupy zjištěnými při zákaznických nasazeních a dalších názorech, když přidáváme nové funkce pro správu z cloudu pomocí Azure AD.