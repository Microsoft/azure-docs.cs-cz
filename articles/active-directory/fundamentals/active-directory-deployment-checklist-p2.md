---
title: Kontrolní seznam nasazení služby Azure AD
description: Kontrolní seznam nasazení funkce Azure Active Directory
services: active-directory
ms.service: active-directory
ms.component: ''
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: ''
ms.openlocfilehash: 1bfc97eb850cf81ff638f92bc628c856fa75681e
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54230658"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Příručka nasazení funkce Azure Active Directory

To se může zdát složitý k nasazení služby Azure Active Directory (Azure AD) pro vaši organizaci a zajistili jeho zabezpečení. Tento článek identifikuje běžné úkoly, že zákazníci považovat za užitečné pro dokončení ve fázích, v průběhu 30, 60, 90 dnů, nebo stav jejich zabezpečení vylepšit informace. Dokonce i organizace, kteří už mají nasazenou Azure AD můžete použít tato příručka k zajištění, že se, jak maximálně využít svou investici získají.

Infrastrukturu identit promyšlených a jsou prováděny usnadní cestu pro zabezpečený přístup k vaší produktivity úloh a dat pomocí známých uživatelů a zařízení pouze.

Kromě toho můžete zkontrolovat zákazníci jejich [identity zabezpečení skóre](identity-secure-score.md) zobrazíte jak zarovnané mají osvědčených postupů Microsoftu. Zkontrolujte vaše zabezpečené skóre před a po implementaci těchto doporučení zobrazíte jak dobře provádíte oproti ostatním uživatelům ve vašem oboru a jinými organizacemi velikost.

## <a name="prerequisites"></a>Požadavky

Mnohá z těchto doporučení v tomto průvodci je možné implementovat pomocí Azure AD Free, Basic nebo žádná licence vůbec. Kde se vyžadují licence jsme stavu, které licence se vyžaduje minimálně ke splnění úkolu.

Další informace o licencování najdete na následujících stránkách:

* [Licencování Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Licence k sadě Azure AD s B2B](../b2b/licensing-guidance.md)

## <a name="phase-1-foundation-of-security"></a>Fáze 1: Základy zabezpečení

Ve fázi 1 správci povolit standardních hodnot zabezpečení funkce pro vytváření základních zabezpečené a snadno se používá ve službě Azure AD předtím, než jsme naimportovat nebo vytvořit běžné uživatelské účty. Tato základní fáze zajišťuje jsou ve stavu zabezpečení od samého začátku a koncovým uživatelům, pouze že zavést do nových konceptů jednou.

| Úkol | Detail | Požadovaná licence |
| ---- | ------ | ---------------- |
| [Určení více než jeden globální správce](../users-groups-roles/directory-emergency-access.md) | Přiřadíte aspoň dva účty výhradně cloudového globálního správce je trvalý pro použití při nouze. Tyto účty nejsou používají denně a musí být dlouhá a složitá hesla. | Azure AD Free |
| [Použít platformou pro správu rolí, kde je to možné](../users-groups-roles/directory-assign-admin-roles.md) | Poskytují pouze přístup, které potřebují vaši správci do oblastí, které potřebují přístup k. Ne všichni správci musí být globální správce. | Azure AD Free |
| [Aktivaci Privileged Identity Management pro sledování využití role správce](../privileged-identity-management/pim-getting-started.md) | Povolte Privileged Identity Management ke spuštění, sledování využití pro správu role. | Azure AD Premium P2 |
| [Zavedení samoobslužného resetování hesla](../authentication/howto-sspr-deployment.md) | Omezit volání na helpdesk, k resetování hesla tím, že zaměstnanci k resetování hesla pomocí zásad můžete jako ovládací prvek správce. | Azure AD Basic |
| [Vytvořit seznam zakázaných hesel konkrétní vlastní organizaci](../authentication/howto-password-ban-bad-configure.md) | Zabraňte uživatelům ve vytváření hesel, které zahrnují obecná slova nebo fráze z vaší organizace nebo oblasti. | Azure AD Basic |
| [Povolit místní integrace s ochranou hesla Azure AD](../authentication/concept-password-ban-bad-on-premises.md) | Rozšířit seznam zakázaných hesel do místního adresáře, aby hesla sady místních jsou také v souladu s globální a specifickým pro tenanta zakázané seznamy heslo. | Azure AD Premium P1 |
| [Povolit pokyny heslo společnosti Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Zastavení vyžadující uživatelům změnit si heslo podle nastaveného plánu, zakažte požadavky na složitost a vaši uživatelé jsou více apt k pamatovat si hesla a zajistěte jejich něco, co je bezpečné. | Azure AD Free |
| [Zakažte obnovení periodické hesla pro cloudové uživatelské účty](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Resetování hesla pravidelné upozorněte své uživatele, zvýšit jejich stávající hesla. Postupujte podle pokynů v dokumentu pokyny hesla od Microsoftu a zrcadlí vaše místní zásady pro uživatele jenom pro cloud. | Azure AD Free |
| [Přizpůsobení inteligentním uzamčením Azure Active Directory](../authentication/howto-password-smart-lockout.md) | Zastavit uzamčení z cloudových uživatelů z replikují do místní služby Active Directory uživatelům | Azure AD Basic |
| [Povolit inteligentní uzamčení extranetu služby AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | Uzamčení extranetu služby AD FS chrání před útoky hrubou silou heslo opakovaně uhodnout útoky, přičemž umožníte platných uživatelů služby AD FS i nadále používat svoje účty. | |
| [Nasazení Azure Multi-Factor Authentication AD pomocí zásad podmíněného přístupu](../authentication/howto-mfa-getstarted.md) | Vyžadovat, aby uživatelé při přístupu k citlivým aplikacím pomocí zásad podmíněného přístupu, provedení dvoustupňového ověřování. | Azure AD Premium P1 |
| [Povolit Azure Active Directory Identity Protection](../identity-protection/enable.md) | Povolte sledování rizikových přihlášení a zneužití přihlašovacích údajů pro uživatele ve vaší organizaci. | Azure AD Premium P2 |
| [Rizikové události použít k aktivaci služby Multi-Factor authentication a změn hesel](../authentication/tutorial-risk-based-sspr-mfa.md) | Povolte automatizaci, která mohou aktivovat události, jako je ověřování službou Multi-Factor Authentication, resetování hesla a blokování přihlášení podle rizika. | Azure AD Premium P2 |
| [Povolit sblížené registrace pro samoobslužné resetování hesla a Vícefaktorové ověřování Azure AD (preview)](../authentication/concept-registration-mfa-sspr-converged.md) | Povolit uživatelům registrovat z jednoho společného prostředí pro ověřování Azure Multi-Factor Authentication a samoobslužné resetování hesla. | Azure AD Premium P1 |

## <a name="phase-2-users-synchronization-and-devices"></a>Fáze 2: Uživatelé, synchronizace a zařízení

Ve fázi 2, přidáme do foundation podle fáze 1 Import našich uživatelů a povolením synchronizace, plánování pro hosty a příprava pro podporu dalších funkcí.

| Úkol | Detail | Požadovaná licence |
| ---- | ------ | ---------------- |
| [Instalace služby Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md) | Připravte se na synchronizovat uživatele z existujícího místního adresáře do cloudu. | Azure AD Free |
| [Implementace synchronizace hodnot Hash hesel](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | Synchronizace hodnot hash hesel, aby změny hesla se musí replikovat, chybných zadání hesla a zjišťování a náprava, uniklé přihlašovací údaje vytváření sestav. | Azure AD Premium P1 |
| [Implementace zpětného zápisu hesla](../authentication/howto-sspr-writeback.md) | Povolení změn hesla v cloudu a možné zpětně zapsat do místních prostředí systému Windows Server Active Directory. | Azure AD Premium P1 |
| [Implementace služby Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md) | Povolte monitorování stavu klíče statistiky pro vaše servery Azure AD Connect, servery služby AD FS a řadiče domény. | Azure AD Premium P1 |
| [Přiřazení licencí pro uživatele na základě členství ve skupinách v Azure Active Directory](../users-groups-roles/licensing-groups-assign.md) | Šetřete čas a úsilí tak, že vytvoříte licenční skupiny, které povolí nebo zakáže funkce podle skupiny místo nastavení na uživatele. | |
| [Vytvořit plán pro přístupu uživatelů typu Host](../b2b/what-is-b2b.md) | Spolupracujte s uživatele typu Host tím, že je přihlašování k aplikacím a službám pomocí jejich vlastní pracovní, školní nebo sociálních identit. | [Licence k sadě Azure AD s B2B](../b2b/licensing-guidance.md) |
| [Při rozhodování o strategie správy zařízení](../devices/overview.md) | Rozhodněte se, co vaše organizace povoluje týkající se zařízení. Společnosti k dispozici registrace vs se zapojíte, vs přineste si vlastní zařízení. | |
| [Nasazení Windows Hello pro firmy ve vaší organizaci](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Příprava na ověření bez hesla pomocí Windows Hello | |

## <a name="phase-3-applications"></a>Fáze 3: Aplikace

Ve fázi 3 začněte správci trvalý proces pro přidání do aplikací.

| Úkol | Detail | Požadovaná licence |
| ---- | ------ | ---------------- |
| Identifikovat vaší aplikace | Identifikace aplikací ve vaší organizaci: místní, aplikací SaaS v cloudu a další – obchodní aplikace. Určení, zda tyto aplikace můžete a službou Azure AD se mají spravovat. | Vyžaduje se žádné licence |
| [Integrujte podporované aplikace SaaS v galerii](../manage-apps/add-application-portal.md) | Azure AD má galerie, která obsahuje tisíce předem integrovaných aplikací. Některé z aplikací, které vaše organizace používá jsou pravděpodobně v galerii dostupné přímo z webu Azure portal. | Azure AD Free |
| [Pomocí Proxy aplikací můžete integrovat místní aplikace](../manage-apps/application-proxy-add-on-premises-application.md) | Proxy aplikací umožňuje uživatelům přístup k místním aplikacím, když se přihlásíte pomocí svého účtu Azure AD. | Azure AD Basic |

## <a name="phase-4-privileged-identities-access-reviews-and-user-lifecycle"></a>Fáze 4: Privilegované identity, kontroly přístupu a životního cyklu uživatele

Fáze 4 vidí správci vynucování zásad nejnižší oprávnění pro správu, dokončení jejich první kontrolu přístupu a povolení automatizace běžné uživatelské úlohy životního cyklu.

| Úkol | Detail | Požadovaná licence |
| ---- | ------ | ---------------- |
| [Vynutit používání služby Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Odebrání role pro správu z normální každodenní uživatelské účty. Ujistěte se, administrativní uživatelé mají nárok na využití jejich role po úspěšné kontroly ověřování službou Multi-Factor Authentication, poskytuje obchodní odůvodnění nebo z určených schvalovatelů odesílání žádostí o schválení. | Azure AD Premium P2 |
| [Dokončení kontroly přístupu pro role adresáře Azure AD v PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Práce s vaší zabezpečení a vedoucí týmy k vytvoření zásad přístupu zkontrolujte zkontrolovat přístup pro správu na základě zásad vaší organizace. | Azure AD Premium P2 |
| [Zásady členství implementovat dynamické skupiny](../users-groups-roles/groups-dynamic-membership.md) | Umožňuje automaticky přiřadit uživatele do skupin na základě jejich atributů z personálního oddělení (nebo zdroji pravdivých informací), jako je například oddělení, název, oblast a další atributy dynamické skupiny. |  |
| [Implementace skupinové zřizování aplikací](../manage-apps/what-is-access-management.md) | Pomocí přístupu na základě skupiny správy zřizování, které umožňuje automaticky zřizovat uživatele pro aplikace SaaS. |  |
| [Automatizace a rušení zřizování uživatelů](../manage-apps/user-provisioning.md) | Vyžadováno provedení ručních kroků odebrání životní cyklus vašeho zaměstnance účet před neoprávněným přístupem. Synchronizaci identit mezi vaším zdrojem pravdivých informací (HR systému) do služby Azure AD. |  |

## <a name="next-steps"></a>Další postup

[Azure AD licencování a podrobnosti o cenách](https://azure.microsoft.com/pricing/details/active-directory/)

[Konfigurace přístupu k identity a zařízení](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Běžné doporučené zásady přístupu identity a zařízení](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
