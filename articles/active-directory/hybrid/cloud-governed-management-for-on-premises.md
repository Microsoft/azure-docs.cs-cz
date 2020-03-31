---
title: Správa řízená Azure AD cloudem pro místní úlohy – Azure
description: Toto téma popisuje správu řízenou cloudem pro místní úlohy.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510a5562740260eb2946ded074a5c37804c55375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109522"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Jak Azure AD poskytuje správu cloudu pro místní úlohy

Azure Active Directory (Azure AD) je komplexní řešení identity jako služby (IDaaS), které používají miliony organizací, které pokrývají všechny aspekty identity, správy přístupu a zabezpečení. Azure AD obsahuje více než miliardu uživatelských identit a pomáhá uživatelům přihlásit se a bezpečně přistupovat k oběma:

* Externí prostředky, jako je Microsoft Office 365, portál Azure a tisíce dalších aplikací software jako služba (SaaS).
* Interní prostředky, jako jsou aplikace v podnikové síti a intranetu organizace, spolu se všemi cloudovými aplikacemi vyvinutými touto organizací.

Organizace můžou používat Azure AD, pokud jsou "čistý cloud" nebo jako "hybridní" nasazení, pokud mají místní úlohy. Hybridní nasazení Azure AD může být součástí strategie pro organizaci migrovat své IT prostředky do cloudu nebo pokračovat v integraci stávající místní infrastruktury spolu s novými cloudovými službami.

Historicky 'hybridní' organizace viděli Azure AD jako rozšíření jejich stávající místní infrastruktury. V těchto nasazeních jsou místní správa identit, služba Windows Server Active Directory nebo jiné interní adresářové systémy řídicími body a uživatelé a skupiny jsou synchronizovány z těchto systémů do cloudového adresáře, jako je azure ad. Jakmile jsou tyto identity v cloudu, můžou být dostupné pro Office 365, Azure a další aplikace.

![Životní cyklus identity](media/cloud-governed-management-for-on-premises//image1.png)

Vzhledem k tomu, že organizace přesouvají více své IT infrastruktury spolu se svými aplikacemi do cloudu, mnoho z nich hledá lepší zabezpečení a zjednodušené možnosti správy identit jako služby. Cloudové funkce IDaaS ve službě Azure AD urychlují přechod na správu řízenou cloudem tím, že poskytují řešení a funkce, které organizacím umožňují rychle přijmout a přesunout více správy identit z tradičního místního prostředí. systémy do Azure AD, zatímco nadále podporovat stávající i nové aplikace.

Tento dokument popisuje strategii společnosti Microsoft pro hybridní IDaaS a popisuje, jak organizace mohou používat Azure AD pro své stávající aplikace.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Přístup Azure AD ke správě identit řízené cloudem

Při přechodu organizací do cloudu potřebují záruky, že mají kontrolu nad svým kompletním prostředím – větší zabezpečení a větší přehled o aktivitách, podporované automatizací a proaktivní přehledy. "**Správa řízená cloudem**" popisuje, jak organizace spravují a řídí své uživatele, aplikace, skupiny a zařízení z cloudu.

V tomto moderním světě musí být organizace schopny efektivně řídit ve velkém měřítku, a to kvůli šíření aplikací SaaS a rostoucí roli spolupráce a externích identit. Nové prostředí rizik v cloudu znamená, že organizace musí být citlivější – škodlivý objekt actor, který ohrožuje uživatele cloudu, může ovlivnit cloudové a místní aplikace.

Hybridní organizace musí být zejména schopny delegovat a automatizovat úkoly, které historicky IT dělaly ručně. K automatizaci úkolů potřebují řešení API a procesy, které reagují životní cyklus různých prostředků souvisejících s identitou (uživatelů, skupin, aplikací, zařízení), aby mohli delegovat každodenní správu těchto prostředků na více osob mimo hlavní it pracovníky. Azure AD řeší tyto požadavky prostřednictvím správy uživatelských účtů a nativní ověřování pro uživatele bez nutnosti místní infrastruktury identit. Nevytváření místní infrastruktury může být přínosem pro organizace, které mají nové komunity uživatelů, jako jsou obchodní partneři, kteří nepocházejí z jejich místního adresáře, ale jejichž správa přístupu je důležitá pro dosažení obchodních výsledků.

Kromě toho není správa úplná bez správy věcí veřejných --- a správa věcí veřejných v tomto novém světě je integrovanou součástí systému identit, nikoli doplněk. Zásady správného řízení identit poskytují organizacím možnost spravovat životní cyklus identity a přístupu mezi zaměstnanci, obchodními partnery a dodavateli a službami a aplikacemi.

Začlenění zásad správného řízení identit usnadňuje organizaci přechod na správu řízenou cloudem, umožňuje it oddělením škálovat, řeší nové výzvy s hosty a poskytuje hlubší přehledy a automatizaci než zákazníci infrastruktury. Zásady správného řízení v tomto novém světě znamená schopnost organizace mít transparentnost, viditelnost a správné ovládací prvky přístupu k prostředkům v rámci organizace. S Azure AD, operace zabezpečení a týmy auditu mají přehled o tom, kdo má --- a kdo by měl mít – přístup k jaké prostředky v organizaci (na jakých zařízeních), co tito uživatelé dělají s tímto přístupem a zda organizace má a používá vhodné odstranit nebo omezit přístup v souladu se zásadami společnosti nebo regulačními zásadami.

Nový model správy přináší výhody organizacím s aplikacemi SaaS i obchodními aplikacemi, protože jsou snadněji schopné spravovat a zabezpečit přístup k těmto aplikacím. Díky integraci aplikací s Azure AD budou organizace moct používat a spravovat přístup napříč cloudovými i místními identitami, které jsou konzistentně. Správa životního cyklu aplikací se stává automatizovanější a Azure AD poskytuje bohaté přehledy o využití aplikací, které nebylo snadno dosažitelné v místní správě identit. Prostřednictvím Azure AD, skupin Office 365 a samoobslužných funkcí Teams můžou organizace snadno vytvářet skupiny pro správu přístupu a spolupráci a přidávat nebo odebírat uživatele v cloudu, aby umožnily požadavky na spolupráci a správu přístupu.

Výběr správných funkcí Azure AD pro správu řízenou cloudem závisí na aplikacích, které se mají používat, a na tom, jak budou tyto aplikace integrované s Azure AD. V následujících částech jsou popsány přístupy pro aplikace integrované se službou AD a aplikace, které používají federační protokoly (například SAML, OAuth nebo OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Správa integrovaná s Cloudem pro aplikace integrované s AD

Azure AD zlepšuje správu pro místní aplikace integrované službou Active Directory prostřednictvím zabezpečeného vzdáleného přístupu a podmíněného přístupu k těmto aplikacím. Kromě toho Azure AD také poskytuje správu životního cyklu účtu a správu přihlašovacích údajů pro stávající účty služby AD uživatele, včetně:

* **Zabezpečený vzdálený přístup a podmíněný přístup pro místní aplikace**

Pro mnoho organizací je prvním krokem při správě přístupu z cloudu pro místní webové a vzdálené desktopové aplikace integrované do služby AD nasazení [proxy aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) před těmito aplikacemi za účelem zajištění zabezpečeného vzdáleného přístupu.

Po jednotném přihlášení k Azure AD mají uživatelé přístup ke cloudovým i místním aplikacím prostřednictvím externí adresy URL nebo interního portálu aplikace. Například proxy aplikace poskytuje vzdálený přístup a jednotné přihlašování ke vzdálené ploše, sharepointu a také k aplikacím, jako jsou Tableau a Qlik, a aplikacím oboru podnikání (LOB). Zásady podmíněného přístupu mohou navíc zahrnovat zobrazení [podmínek použití](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) a zajištění toho, [aby s nimi uživatel souhlasil](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) před přístupem k aplikaci.

![Architektura proxy aplikace](media/cloud-governed-management-for-on-premises/image2.png)

* **Automatická správa životního cyklu pro účty služby Active Directory**

Zásady správného řízení identit pomáhá organizacím dosáhnout rovnováhy mezi *produktivitou* --- jak rychle může mít osoba přístup k prostředkům, které potřebuje, například když se připojí k organizaci? --- a *bezpečnosti* --- jak by se měl jejich přístup v průběhu času měnit, například když se změní zaměstnanecký status této osoby? Správa životního cyklu identitje základem pro zásady správného řízení identit a efektivní zásady správného řízení ve velkém měřítku vyžaduje modernizaci infrastruktury správy životního cyklu identit pro aplikace.

Pro mnoho organizací je životní cyklus identity pro zaměstnance vázán na reprezentaci tohoto uživatele v systému řízení lidského kapitálu (HCM). Pro organizace, které používají Workday jako svůj systém HCM, azure ad můžete zajistit uživatelské účty ve službě AD jsou [automaticky zřízena a zrušena pro pracovníky v Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial). To vede ke zvýšení produktivity uživatelů prostřednictvím automatizace účtů prvorozenství a řídí rizika tím, že zajišťuje, že přístup k aplikacím se automaticky aktualizuje, když uživatel změní role nebo opustí organizaci. [Plán nasazení](https://aka.ms/WorkdayDeploymentPlan) zřizování uživatelů řízený pracovníden je podrobný průvodce, který provede organizace doporučenými postupy implementace řešení Workday to Active Directory User Provisioning v pětikrocích.

Azure AD Premium také zahrnuje Microsoft Identity Manager, který může importovat záznamy z jiných místních systémů HCM, včetně SAP, Oracle eBusiness a Oracle PeopleSoft.

Spolupráce mezi podniky stále více vyžaduje udělení přístupu lidem mimo vaši organizaci. [Spolupráce Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) umožňuje organizacím bezpečně sdílet své aplikace a služby s uživateli typu Host a externími partnery při zachování kontroly nad vlastními podnikovými daty.

Azure AD můžete [automaticky vytvářet účty ve službě AD pro uživatele typu Host](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) podle potřeby, což umožňuje firemním hostům přístup k místním aplikacím integrovanému ve službě AD bez nutnosti dalšího hesla. Organizace mohou nastavit [vícefaktorové ověřování (MFA) zásady pro uživatele typu Host](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)s, takže kontroly MFA se provádějí během ověřování proxy aplikací. Také všechny [kontroly přístupu,](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) které se provádějí na uživatelů cloudu B2B platí pro místní uživatele. Například pokud je uživatel cloudu odstraněn prostřednictvím zásad správy životního cyklu, odstraní se také místní uživatel.

**Správa pověření pro účty služby Active Directory** Samoobslužné resetování hesla služby Azure AD umožňuje uživatelům, kteří zapomněli svá hesla, aby se znovu ověřili a resetovali hesla pomocí změněných hesel [zapsaných do místní služby Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback). Proces resetování hesla můžete také použít místní zásady hesla služby Active Directory: Když uživatel resetuje své heslo, je zaškrtnuto, zda splňuje místní zásady služby Active Directory před potvrzením do tohoto adresáře. Samoobslužný plán nasazení pro [resetování](https://aka.ms/deploymentplans/sspr) hesla popisuje osvědčené postupy pro zavedení samoobslužného resetování hesla uživatelům prostřednictvím webových možností a prostředí integrovaných systémem Windows.

![Architektura SSPR azure ad](media/cloud-governed-management-for-on-premises/image3.png)

A konečně pro organizace, které umožňují uživatelům měnit svá hesla ve službě AD, služba AD může být nakonfigurována tak, aby používala stejné zásady hesel, jakou organizace používá ve službě Azure AD prostřednictvím [funkce ochrany heslem Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), která je aktuálně ve verzi Public Preview.

Když je organizace připravená přesunout aplikaci integrovanou se službou AD do cloudu přesunutím operačního systému hostujícího aplikaci do Azure, azure [ad domain services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) poskytuje služby domény kompatibilní se službou AD (jako je připojení k doméně, zásady skupiny, LDAP a ověřování protokolu Kerberos/NTLM). Azure AD Domain Services se integruje s existujícím tenantem Azure AD organizace, takže uživatelé se mohou přihlásit pomocí svých podnikových přihlašovacích údajů. Kromě toho lze stávající skupiny a uživatelské účty použít k zabezpečení přístupu k prostředkům, což zajišťuje hladší "výtah a přesun" místních prostředků do služeb infrastruktury Azure.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Správa řízená cloudem pro místní aplikace založené na federacích

Pro organizaci, která už používá místního poskytovatele identit, přesunutí aplikací do Azure AD umožňuje bezpečnější přístup a snadnější administrativní prostředí pro správu federace. Azure AD umožňuje konfiguraci podrobné ovládací prvky přístupu pro aplikaci, včetně Azure Multi-Factor Authentication, pomocí Azure AD podmíněného přístupu. Azure AD podporuje další funkce, včetně certifikátů podpisu tokenů specifických pro aplikaci a konfigurovatelných dat vypršení platnosti certifikátů. Tyto funkce, nástroje a pokyny umožňují organizacím vyřadit své místní poskytovatele identit. Vlastní IT oddělení společnosti Microsoft například přesunulo 17 987 aplikací z interních služeb AD FS (AD FS) společnosti Microsoft do služby Azure AD.

![Vývoj Azure AD](media/cloud-governed-management-for-on-premises/image5.png)

Chcete-li zahájit migraci federovaných aplikací do služby https://aka.ms/migrateapps Azure AD jako zprostředkovatele identity, přečtěte si odkaz, který obsahuje odkazy na:

* Oficiální zpráva [migrace aplikací do služby Azure Active Directory](https://aka.ms/migrateapps/whitepaper), která představuje výhody migrace a popisuje, jak plánovat migraci ve čtyřech jasně popsaných fázích: zjišťování, klasifikace, migrace a průběžná správa. Budete vedeni tím, jak přemýšlet o procesu a rozdělit svůj projekt na snadno konzumovat kusy. V celém dokumentu jsou odkazy na důležité zdroje, které vám pomohou na cestě.

* Průvodce řešením [migrace ověřování aplikací ze služby AD Federační služby do služby Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide) podrobněji zkoumá stejné čtyři fáze plánování a provádění projektu migrace aplikací. V této příručce se dozvíte, jak použít tyto fáze na konkrétní cíl přesunutí aplikace ze služby AD FS (AD FS) do Služby Azure AD.

* [Skript připravenosti na migraci služby AD Služby Active Directory](https://aka.ms/migrateapps/adfstools) Fed(A) lze spustit na stávajících místních serverech služby AD FS (Active Directory Federation Services) a určit připravenost aplikací pro migraci do služby Azure AD.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Průběžná správa přístupu napříč cloudovými a místními aplikacemi

Organizace potřebují proces pro správu přístupu, který je škálovatelný. Uživatelé i nadále shromažďovat přístupová práva a skončit s nad rámec toho, co bylo původně zřízena pro ně. Kromě toho musí být podnikové organizace schopny efektivně škálovat, aby mohly průběžně vyvíjet a vynucovat zásady přístupu a ovládací prvky.

It obvykle deleguje rozhodnutí o schválení přístupu na osoby s rozhodovací pravomocí. Kromě toho může IT zapojit samotné uživatele. Například uživatelé, kteří mají přístup k důvěrným zákaznickým datům v marketingové aplikaci společnosti v Evropě, potřebují znát zásady společnosti. Uživatelé typu Host také nemusí vědět o požadavcích na zpracování dat v organizaci, do které byli pozváni.

Organizace mohou automatizovat proces životního cyklu přístupu prostřednictvím technologií, jako jsou [dynamické skupiny](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership), spolu s zřizováním uživatelů [do aplikací SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)nebo [aplikací integrovaných pomocí standardu SCIM (System for-Domain Identity Management).](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups) Organizace také mohou řídit, kteří [uživatelé typu Host mají přístup k místním aplikacím](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises). Tato přístupová práva pak můžete pravidelně kontrolovat pomocí opakovaných [kontrol přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

## <a name="future-directions"></a>Budoucí směry

V hybridních prostředích je strategií společnosti Microsoft povolit nasazení, kde **cloud je řídicí rovinou identity**, a místní adresáře a další systémy identit, jako je služba Active Directory a další místní aplikace, jsou cílem pro zřizování uživatelů s přístupem. Tato strategie bude i nadále zajišťovat práva, identity a přístup v těchto aplikacích a úlohách, které jsou na nich závislé. V tomto koncovém stavu budou organizace moci zvýšit produktivitu koncových uživatelů zcela z cloudu.

![Architektura Azure AD](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak začít na této cestě, najdete <https://aka.ms/deploymentplans> v tématu plány nasazení Azure AD, který se nachází na . Poskytují komplexní pokyny o tom, jak nasadit funkce Azure Active Directory (Azure AD). Každý plán vysvětluje obchodní hodnotu, aspekty plánování, návrh a provozní postupy potřebné k úspěšnému zavedení běžných funkcí Azure AD. Microsoft průběžně aktualizuje plány nasazení s osvědčenými postupy, které se učí z nasazení zákazníků a další zpětné vazby, když přidáme nové funkce pro správu z cloudu pomocí Azure AD.
