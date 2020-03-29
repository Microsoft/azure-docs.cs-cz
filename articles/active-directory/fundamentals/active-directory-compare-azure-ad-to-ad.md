---
title: Porovnání služby Active Directory se službou Azure Active Directory
description: Tento dokument porovnává službu Active Directory Domain Services (ADDS) se službou Azure Active Directory (AD). Popisuje klíčové pojmy v obou řešení identity a vysvětluje, jak je to jiné nebo podobné.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 02/26/2020
ms.author: martinco
ms.openlocfilehash: 5075ae57df6a7306f0c860690931c846e52c2a89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926888"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>Porovnání služby Active Directory se službou Azure Active Directory

Azure Active Directory je další vývoj řešení správy identit a přístupu pro cloud. Společnost Microsoft zavedla službu Active Directory Domain Services v systému Windows 2000, aby organizacím poskytla možnost spravovat více součástí a systémů místní infrastruktury pomocí jedné identity na uživatele.

Azure AD posouvá tento přístup na další úroveň tím, že poskytuje organizacím řešení Identity as a Service (IDaaS) pro všechny jejich aplikace v cloudu i v místním prostředí.

Většina správců IT je obeznámena s koncepty služby Active Directory Domain Services. Následující tabulka popisuje rozdíly a podobnosti mezi koncepty služby Active Directory a službou Azure Active Directory.

|Koncept|Služba Active Directory (AD)|Azure Active Directory |
|:-|:-|:-|
|**Uživatelé**|||
|Zřizování: uživatelé | Organizace vytvořit interní uživatele ručně nebo použít interní nebo automatizovaný zřizovací systém, jako je microsoft identity manager, integrovat se systémem HR.|Stávající organizace služby AD používají [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) k synchronizaci identit do cloudu.</br> Azure AD přidává podporu pro automatické vytváření uživatelů z [cloudových hr systémů](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial). </br>Azure AD můžete zřídit identity v [aplikacích s podporou SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups) s podporou SaaS automaticky poskytovat aplikace s potřebné podrobnosti povolit přístup pro uživatele. |
|Zřizování: externí identity| Organizace vytvářejí externí uživatele ručně jako běžní uživatelé ve vyhrazené externí doménové struktuře služby AD, což vede k režii správy pro správu životního cyklu externích identit (uživatelé typu Host)| Azure AD poskytuje speciální třídu identity pro podporu externích identit. [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) bude spravovat odkaz na identitu externího uživatele a ujistěte se, že jsou platné. |
| Správa nároků a skupiny| Správci zhotovují uživatele členy skupin. Vlastníci aplikací a prostředků pak poskytují skupinám přístup k aplikacím nebo prostředkům.| [Skupiny](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) jsou také k dispozici ve službě Azure AD a správci můžete také použít skupiny udělit oprávnění k prostředkům. Ve službě Azure AD mohou správci přiřadit členství skupinám ručně nebo pomocí dotazu dynamicky zahrnout uživatele do skupiny. </br> Správci můžou pomocí [správy nároků](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) ve službě Azure AD poskytnout uživatelům přístup ke kolekci aplikací a prostředků pomocí pracovních postupů a v případě potřeby kritérií založených na čase. |
| Správa správce|Organizace budou používat kombinaci domén, organizačních jednotek a skupin ve službě AD k delegování práv správce ke správě adresáře a prostředků, které řídí.| Azure AD poskytuje [integrované role](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) s jeho systém řízení přístupu na základě rolí (RBAC) s omezenou podporou pro [vytváření vlastních rolí](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview) delegovat privilegovaný přístup k systému identit, aplikací a prostředků, které řídí.</br>Správa rolí může být vylepšena [pomocí správy privilegovaných identit (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) a poskytuje přístup k privilegovaným rolím za čase, časově omezený nebo založený na pracovním postupu. |
| Správa pověření| Pověření ve službě Active Directory jsou založena na heslech, ověřování certifikátů a ověřování čipových karet. Hesla jsou spravována pomocí zásad hesel, které jsou založeny na délce hesla, vypršení platnosti a složitosti.|Azure AD používá inteligentní [ochranu heslem](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) pro cloud a místní. Ochrana zahrnuje inteligentní uzamčení a blokování běžných a vlastních frází a substitucí hesel. </br>Azure AD výrazně zvyšuje zabezpečení [prostřednictvím vícefaktorového ověřování](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) a [bezhelných](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless) technologií, jako je FIDO2. </br>Azure AD snižuje náklady na podporu tím, že uživatelům poskytuje [samoobslužný systém pro resetování hesla.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) |
| **Aplikace**|||
| Aplikace pro infrastrukturu|Služba Active Directory je základem pro mnoho místních součástí infrastruktury, například DNS, DHCP, IPSec, WiFi, NPS a přístup k síti VPN.|V novém cloudovém světě, Azure AD, je nová rovina ovládacího prvku pro přístup k aplikacím versus spoléhání se na síťové ovládací prvky. Když se uživatelé ověřují[, podmíněný přístup (CA)](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)bude řídit, kteří uživatelé budou mít přístup ke kterým aplikacím za požadovaných podmínek.|
| Tradiční a starší aplikace| Většina místních aplikací používá k řízení přístupu uživatelům protokol LDAP, integrované ověřování systému Windows (NTLM a Kerberos) nebo ověřování na hlavičkách.| Azure AD můžete poskytnout přístup k těmto typům místních aplikací pomocí agentů [proxy aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) spuštěných místně. Pomocí této metody Azure AD můžete ověřit uživatele služby Active Directory místně pomocí protokolu Kerberos při migraci nebo potřebujete koexistovat se staršími aplikacemi. |
| Aplikace SaaS|Služba Active Directory nepodporuje aplikace SaaS nativně a vyžaduje federační systém, například službu AD FS.|SaaS aplikace podporující Ověřování OAuth2, SAML\* a WS lze integrovat do použití Azure AD pro ověřování. |
| Obchodní aplikace (LOB) s moderním ověřováním|Organizace mohou pomocí služby AD FS se službou Active Directory podporovat obchodní aplikace vyžadující moderní ověřování.| Obchodní aplikace vyžadující moderní ověřování lze nakonfigurovat tak, aby používaly Azure AD pro ověřování. |
| Služby střední úrovně/daemonu|Služby spuštěné v místních prostředích obvykle používají účty služeb služby AD nebo skupinové účty spravovaných služeb (gMSA) ke spuštění. Tyto aplikace pak zdědí oprávnění účtu služby.| Azure AD poskytuje [spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index) pro spouštění dalších úloh v cloudu. Životní cyklus těchto identit spravuje Azure AD a je vázaný na poskytovatele prostředků nelze použít pro jiné účely získat backdoor přístup.|
| **Zařízení**|||
| Mobilní zařízení|Služba Active Directory nepodporuje nativně mobilní zařízení bez řešení jiných výrobců.| Řešení microsoftu pro správu mobilních zařízení, Microsoft Intune, je integrované s Azure AD. Microsoft Intune poskytuje systému identit informace o stavu zařízení k vyhodnocení během ověřování. |
| Plochy Windows|Služba Active Directory umožňuje připojit zařízení se systémem Windows k doméně a spravovat je pomocí zásad skupiny, nástroje System Center Configuration Manager nebo jiných řešení jiných výrobců.|Zařízení s Windows se dá [připojit k Azure AD](https://docs.microsoft.com/azure/active-directory/devices/). Podmíněný přístup můžete zkontrolovat, pokud je zařízení Azure AD připojen jako součást procesu ověřování. Zařízení s Windows lze spravovat také pomocí [Služby Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune). V takovém případě podmíněný přístup před povolením přístupu k aplikacím zváží, zda se jedná o stížnost (například aktuální opravy zabezpečení a signatury virů).|
| Windows servery| Služba Active Directory poskytuje silné možnosti správy pro místní servery windows pomocí zásad skupiny nebo jiných řešení pro správu.| Virtuální počítače s Windows servery v Azure se dá spravovat pomocí [služby Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/). [Spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index) lze použít, když virtuální společnosti potřebují přístup k adresáři nebo prostředkům systému identit.|
| Linux/Unix pracovní zátěž|Služba Active Directory nativně nepodporuje jiné systémy než Windows bez řešení jiných výrobců, ačkoli počítače s Linuxem lze nakonfigurovat tak, aby se ověřovaly pomocí služby Active Directory jako sféry protokolu Kerberos.|Virtuální počítače Linux/Unix můžou používat [spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index) pro přístup k systému identit nebo prostředkům. Některé organizace migrují tyto úlohy do cloudových kontejnerových technologií, které můžou taky používat spravované identity.|

## <a name="next-steps"></a>Další kroky

- [Představení služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [Porovnání samospravovaných služeb Active Directory Domain Services, Služby Azure Active Directory a spravované služby Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)
- [Nejčastější dotazy týkající se služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-faq)
- [Co je nového ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/whats-new)
