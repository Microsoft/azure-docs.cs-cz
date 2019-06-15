---
title: Správa pro místní úlohy – Azure řídí cloudu služby Azure AD
description: Toto téma popisuje správu cloudu řídí pro místní úlohy.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109522"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Jak Azure AD poskytuje Cloud řídí správa pro místní úlohy

Azure Active Directory (Azure AD) je komplexní identity jako služby (IDaaS) řešení používají miliony organizací, které zahrnují všechny aspekty identity, správy přístupu a zabezpečení. Azure AD obsahuje víc než miliardu identity uživatelů a také pomůžou uživatelům přihlášení a bezpečný přístup k obojí:

* Externí prostředky, jako je například Microsoft Office 365, na webu Azure portal a tisícům dalších Software-as-a-Service (SaaS) aplikací.
* Interní prostředky, jako jsou aplikace v podnikové síti a intranetu, spolu s všechny cloudové aplikace, vypracovanou organizací cccppf dané organizace v organizaci.

Organizace můžou použít Azure AD, pokud jsou "čistě cloudových", nebo jako "hybridní nasazení, pokud mají místních úloh. Hybridní nasazení služby Azure AD může být součástí strategie služeb pro organizace, můžete migrovat své IT prostředky do cloudu nebo integrovat stávající místní infrastrukturu spolu s nové cloudové služby i nadále.

V minulosti "hybridní" organizace viděli Azure AD jako rozšíření své stávající místní infrastrukturu. V těchto nasazeních, správy zásad správného řízení identit v místním, Windows Server Active Directory nebo jiné systémy interní adresáře jsou kontrolní body a uživatelů a skupin jsou synchronizované z těchto systémů a cloudového adresáře, jako je Azure AD. Po těchto identit v cloudu, můžete provést k dispozici k Office 365, Azure a dalších aplikací.

![Životního cyklu identit](media/cloud-governed-management-for-on-premises//image1.png)

Jako organizace přesunout informace z jejich IT infrastrukturu spolu s jejich aplikací do cloudu, mnoho hledají lepší zabezpečení a jednodušší správu možnosti správy identit jako služby. Zrychlení přechodu na cloudové správy řídí tím, že poskytuje řešení a funkcí, které umožňují organizacím rychle přijmout a přesunout informace z jejich správy identit z tradičních místních cloudové funkce IDaaS ve službě Azure AD systémy tak, aby Azure AD, přičemž nadále podporovat existující, jakož i nové aplikace.

Tento dokument popisuje strategie Microsoftu pro hybridní IDaaS a popisuje, jak může organizace pomocí služby Azure AD pro svoje stávající aplikace.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Azure AD přístup ke cloudovým řízená identity management.

Jako organizace přechodu do cloudu, která potřebují záruky, ke kterým mají ovládací prvky prostřednictvím jejich kompletního prostředí – lepší zabezpečení a větší přehled o aktivitách, podporované služby automation a proaktivní insights. "**Cloudu řídí správu**" popisuje, jak organizacím spravovat a řídit jejich uživatelé, aplikace, skupiny a zařízení z cloudu.

V tomto světě moderní organizace potřebují mohli efektivně spravovat ve velkém měřítku, vzhledem k šíření aplikací SaaS a roli zvýšení spolupráce a externí identity. Nové prostředí riziko cloudu znamená, že organizace musí být pohotovější – škodlivý objekt actor, který ohrožuje uživatelů cloudu může mít vliv na cloudové a místní aplikace.

Konkrétně se hybridní organizace musí mít možnost delegování a automatizovat úlohy, které v minulosti IT nebyla ručně. K automatizaci úloh, které potřebují rozhraní API a procesy, které orchestrují životním cyklu různých souvisejícím s identitou prostředků (uživatelé, skupiny, aplikace, zařízení), takže delegují každodenní správu těchto prostředků do více osob mimo základní pracovníků IT. Azure AD řeší tyto požadavky prostřednictvím správy uživatelských účtů a nativní ověřování pro uživatele bez potřeby místní infrastruktury identit. Není vytváření na místní infrastrukturu využívat organizace, které mají nové komunity uživatelů, jako je například obchodní partnery, které nebyl pocházejí z jejich v místním adresáři, ale je velmi důležité k dosažení obchodních výsledků jejichž správu přístupu.

Kromě toho není kompletní bez zásad správného řízení---správy a zásad správného řízení v tomto novém světě je integrovaná součást systému identit, spíše než doplněk. Zásady správného řízení identit umožňuje organizacím spravovat identity a přístup životního cyklu napříč zaměstnanců, obchodními partnery a dodavateli a službami a aplikacemi.

Využívá zásady správného řízení identit usnadňuje povolení organizace k přechodu do cloudu řízení správy, umožňuje IT, pokud chcete škálovat, řeší nové výzvy s hosty a poskytuje podrobnější přehled a automatizace než zákazníci měli s v místní infrastruktuře. Zásady správného řízení v tomto novém světě znamená možnost pro organizaci transparentnosti, viditelnost a přiměřené ovládací prvky na přístup k prostředkům v rámci organizace. S Azure AD operace zabezpečení a audit týmy měli přehled o uživatele, kteří mají---a který by měl mít – které prostředky v organizaci (na které zařízení), co dělají uživatelé s přístupem a určuje, zda je v organizaci a používá odpovídající ovládací prvky pro odebrání nebo omezit přístup v souladu s firemní nebo zákonným zásadám.

Nový model správy výhody organizace s aplikacemi SaaS a -obchodní aplikace (LOB), jako je bylo možné snadněji spravovat a zabezpečit přístup k těmto aplikacím. Integrace aplikací s Azure AD, organizace budou moct používat a spravovat přístup v rámci obou cloudových a místních identit pochází konzistentně. Správa životního cyklu aplikací stane víc automatizovaná a Azure AD poskytuje podrobné informace o využití aplikace, který se snadno pouhé správy identit v místním. Prostřednictvím služby Azure AD, skupiny Office 365 a týmy samoobslužných funkcí organizace můžete snadno vytvořit skupiny pro správu přístupu a spolupráci a přidat nebo odebrat uživatele v cloudu a umožněte spolupráci a požadavky na řízení přístupu.

Výběr možnosti vpravo Azure AD pro cloud řízení správy, závisí na aplikace, které chcete použít, a jak tyto aplikace bude možné integrovat se službou Azure AD. Následující oddíly popisují přístupy pro aplikace integrované s Active Directory a aplikace, které používají protokoly federation (například SAML, OAuth nebo OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Správa cloudu řídí pro aplikace integrované s Active Directory

Azure AD zvyšuje vedení organizace místní integrace služby Active Directory aplikace prostřednictvím zabezpečeného vzdáleného přístupu a podmíněný přístup k těmto aplikacím. Kromě toho Azure AD také poskytuje správu životního cyklu účtů a správa přihlašovacích údajů pro uživatele AD účtů, včetně:

* **Zabezpečený vzdálený přístup a podmíněného přístupu pro místní aplikace**

Pro mnoho společností, prvním krokem při správě přístupu z cloudu pro integrované s Active Directory web místní a vzdálené desktopová aplikace je nasadit [proxy aplikací](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) před tyto aplikace k zajištění zabezpečení vzdálený přístup.

Po jednotné přihlašování do služby Azure AD mohou uživatelé přistupovat cloudové a místní aplikace prostřednictvím externí adresu URL nebo interní aplikace portálu. Například Proxy aplikací poskytuje vzdálený přístup a jednotné přihlašování k vzdálené ploše, SharePoint, jakož i aplikace, jako je Tableau, Qlik a obchodní (LOB) aplikace. Kromě toho zásady podmíněného přístupu může obsahovat zobrazení [podmínky použití](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) a [zajistit, že uživatel souhlasil s jejich](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) teprve pak ji bude mít přístup k aplikaci.

![Architektura Proxy aplikací](media/cloud-governed-management-for-on-premises/image2.png)

* **Správa životního cyklu automatické pro účty služby Active Directory**

Zásady správného řízení identit pomáhá organizacím dosáhnout rovnováhy mezi *produktivitu* ---jak může uživatel měli rychlý přístup k prostředkům potřebují, jako je například, když se připojte k organizaci? ---a *zabezpečení* ---jak by měl přístup v průběhu času měnit, například při změně stav zaměstnání tuto osobu? Správa životního cyklu identit je základem pro zásady správného řízení identit a efektivní škálovatelné zásady správného řízení vyžaduje modernizaci infrastruktura správy životního cyklu identit pro aplikace.

Pro mnoho společností životního cyklu identit pro zaměstnance vázán k reprezentaci tohoto uživatele v systému řízení lidského kapitálu (HCM). Pro organizace, které používají Workday jako jejich HCM systému, můžete zajistit služby Azure AD, jsou uživatelské účty ve službě AD [automaticky zřizovat a zrušit pro zaměstnance ve Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial). Ano zájemců Vylepšené uživatelské produktivity díky automatizaci birthright účty a spravovat rizika zajištěním aplikace přístup automaticky aktualizuje, když uživatel změní role nebo opustí organizaci. Zřizování uživatelů Workday řízené [plánu nasazení](https://aka.ms/WorkdayDeploymentPlan) podrobného průvodce, který vás provede organizace prostřednictvím osvědčené postupy pro implementaci Workday do Active Directory uživatele zřizování řešení v pěti krocích.

Azure AD Premium zahrnuje také Microsoft Identity Manager, který můžete importovat záznamy z jiných místních systémů HCM, včetně SAP, Oracle eBusiness, Oracle PeopleSoft a.

Spolupráce Business-to-business stále vyžaduje udělení přístupu uživateli mimo vaši organizaci. [Azure AD s B2B](https://docs.microsoft.com/azure/active-directory/b2b/) spolupráce umožňuje organizacím bezpečně sdílet své aplikace a služby s hosty a externí partnery zachováním potřebné kontroly nad své vlastní firemní data.

Azure AD můžete [automaticky vytvořit účty ve službě AD uživatelům typu Host](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) podle potřeby, povolení obchodní hosté pro přístup k místní aplikace integrované s Active Directory bez nutnosti jiné heslo. Můžete nastavit organizace [zásady ověřování službou Multi-Factor Authentication (MFA) pro uživatele typu Host](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)s tak MFA kontroluje dokončení během ověřování proxy aplikace. Kromě toho některé [kontrol přístupu](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) , které se provádějí v cloudu B2B uživatelé platí pro místní uživatele. Například pokud prostřednictvím zásad správy životního cyklu odstranění uživatele cloud, místní uživatel je také odstranit.

**Pověření správy pro účty služby Active Directory** uživatele Azure AD, samoobslužné resetování hesla umožňuje uživatelům, kteří jste zapomněli svoje hesla resetovat svá hesla, změny hesel a ověřit [zapsána do místní služby Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback). Procesu resetování hesla můžete také použít zásady hesel místní služby Active Directory: Když uživatel může resetovat hesla, zkontroluje se ujistěte, že jsou že splněné v místních zásadách služby Active Directory před potvrzením do tohoto adresáře. Je samoobslužné resetování hesla [plánu nasazení](https://aka.ms/deploymentplans/sspr) nastíní osvědčené postupy pro distribuci uživatelům prostřednictvím webových a Windows integrované prostředí pro resetování hesla pomocí samoobslužné služby.

![Architektura služby Azure AD SSPR](media/cloud-governed-management-for-on-premises/image3.png)

Nakonec pro organizace, které umožňují uživatelům změnit svá hesla ve službě AD, AD dá použít stejné zásady hesel jako organizace ve službě Azure AD prostřednictvím [funkce Ochrana hesel Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)aktuálně v verze Public preview.

Když organizace je připravené na přechod aplikace integrované s Active Directory do cloudu díky přesunu operačního systému, který je hostitelem aplikace do Azure, [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) poskytuje služby kompatibilní s AD domain services (například připojení k doméně Zásady skupiny, LDAP a protokolu Kerberos nebo NTLM authentication). Azure AD Domain Services se integruje s organizace existujícího tenanta Azure AD, takže uživatelům se přihlásit pomocí svých podnikových přihlašovacích údajů. Kromě toho stávajících skupin a uživatelských účtů slouží k zabezpečení přístupu k prostředkům, zajištění hladší "lift and shift" místních prostředků pro služby infrastruktury Azure.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Správa cloudu řídí pro místní federační aplikace

Pro organizaci, která se už používá zprostředkovatel identity v místním přesouvají aplikace do služby Azure AD umožňuje lepší zabezpečení přístupu a snadněji prostředí pro správu Management federačního. Azure AD umožňuje konfigurace ovládacích prvků granulární přístup k jednotlivým aplikacím, včetně Azure Multi-Factor Authentication pomocí podmíněného přístupu Azure AD. Azure AD podporuje další možnosti, včetně podpisové certifikáty tokenů specifické pro aplikaci a data vypršení platnosti certifikátů konfigurovatelné. Tyto možnosti, nástrojů a pokynů, umožňují organizacím vyřadit z provozu svých místních zprostředkovatelů identity. Vlastní IT, jedním z příkladů, přesunuta 17,987 aplikací od společnosti Microsoft interní Active Directory Federation Services (AD FS) do služby Azure AD.

![Vývoj služby Azure AD](media/cloud-governed-management-for-on-premises/image5.png)

Chcete-li začít migrovat federované aplikace do služby Azure AD jako zprostředkovatele identity, najdete https://aka.ms/migrateapps , který obsahuje odkazy na:

* Dokument white paper [migrace aplikací v Azure Active Directory](https://aka.ms/migrateapps/whitepaper), které představuje výhody migrace a popisuje, jak naplánovat migraci ve čtyřech fázích, jasně uvedených: zjišťování, klasifikaci, migrace, a Trvalá Správa. Vás provedeme vás jak uvažovat o procesu a rozdělit projekt do částí, snadno využívat. V celém dokumentu jsou odkazy na důležité prostředky, které vám pomůžou na cestě.

* V Průvodci řešením [migrace aplikace ověřování ze služby AD FS do Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide) vám umožní prozkoumat podrobněji stejné čtyři fáze plánování a provádění migrace projektu aplikace . V této příručce se dozvíte postupy platí tyto fáze pro specifické cílem přesun aplikace ze služby Active Directory Federation Services (AD FS) do služby Azure AD.

* [Active Directory Federation služby migrace připravenosti skript](https://aka.ms/migrateapps/adfstools) může běžet na existujících serverech v místním Active Directory Federation Services (AD FS) určit připravenost aplikací pro migraci do Azure AD.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Správa probíhající přístupu napříč cloudem a místním aplikacím

Organizace potřebují proces pro správu přístupu, která je škálovatelná. Uživatelé i nadále accumulate přístupová práva a nakonec nad rámec co byl zpočátku zřídit pro ně. Kromě toho organizace musí mít možnost k vývoji a vynucovat zásady přístupu a ovládacích prvků průběžně efektivní škálování.

Obvykle IT delegáti přístup pracovníci s rozhodovací pravomocí rozhodnutí o schválení. Kromě toho IT může zahrnovat samotných uživatelů. Například uživatelé, kteří přístup k důvěrným zákaznická data ve vaší společnosti marketingu aplikací v Evropě znát zásady vaší společnosti. Uživatelé typu Host také mohou je nezajímat se o požadavky na zpracování dat v organizaci, ke kterému jste byli pozváni.

Organizace můžete automatizovat proces životního cyklu přístup díky technologiím, jako například [dynamické skupiny](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership), svázaných s zřizování uživatelů pro [aplikací SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list), nebo [aplikací integrováno pomocí systému pro správu identit mezi doménami (SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)) standard. Organizace také můžete určit, které [uživatele typu Host mají přístup k místním aplikacím](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises). Tyto přístup k oprávnění může pak být pravidelně přezkoumávány pomocí opakovaného [kontroly přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

## <a name="future-directions"></a>Směr v budoucnosti

V hybridních prostředích je strategie Microsoftu umožnit nasazení kde **cloud je rovinu řízení identit**a místních adresářů a jinými systémy identit, jako je Active Directory a jiné místní aplikace, jsou cíl pro zřizování uživatelů s přístupem. Tato strategie nadále zajistit práva, identit a přístupu na tyto aplikace a úlohy, které spoléhají na ně. V tomto stavu ukončení organizace bude mít pro zvýšení produktivity koncového uživatele zcela v cloudu.

![Architektura Azure AD](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Další postup

Další informace o tom, jak začít s touto cestou podívejte na plány nasazení služby Azure AD, umístěného v <https://aka.ms/deploymentplans> . Poskytují začátku do konce pokyny o tom, jak nasadit funkce Azure Active Directory (Azure AD). Každý plán vysvětluje obchodní hodnotu, plánování aspekty návrhu a provozní postupy potřebné k úspěšné zavedení běžné možnosti Azure AD. Microsoft se průběžně aktualizuje plány nasazení osvědčené postupy vycházející z nasazení zákazníka a jinou zpětnou vazbu, když jsme přidejte nové funkce pro správu z cloudu pomocí služby Azure AD.
