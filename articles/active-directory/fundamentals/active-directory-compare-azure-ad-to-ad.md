---
title: Porovnání Active Directory s Azure Active Directory
description: Tento dokument porovnává Active Directory Domain Services (přidá) do Azure Active Directory (AD). Popisuje klíčové koncepty v řešeních identit a vysvětluje, jak se liší nebo které je podobné.
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
ms.openlocfilehash: 64a8dabaedc3922ebd8d163b1ea162b7d1584de2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371915"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>Porovnání Active Directory s Azure Active Directory

Azure Active Directory je další vývoj řešení pro správu identit a přístupů pro Cloud. Společnost Microsoft představila Active Directory Domain Services ve Windows 2000, aby organizacím umožnila spravovat více místních komponent a systémů infrastruktury s použitím jediné identity na uživatele.

Azure AD tento přístup přináší na další úroveň tím, že poskytuje organizacím řešení identity jako služby (IDaaS) pro všechny své aplikace v cloudu i v místním prostředí.

Většina správců IT je obeznámená s Active Directory Domain Services koncepty. Následující tabulka popisuje rozdíly a podobnosti mezi koncepty a Azure Active Directory služby Active Directory.

|Koncepce|Služba Active Directory (AD)|Azure Active Directory |
|:-|:-|:-|
|**Uživatelé**|||
|Zřizování: uživatelé | Organizace vytváří interní uživatele ručně nebo používá integrovaný nebo automatizovaný systém zřizování, jako je například Microsoft Identity Manager, pro integraci se systémem HR.|Stávající organizace služby AD používají [Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md) k synchronizaci identit do cloudu.</br> Azure AD přidává podporu pro automatické vytváření uživatelů ze [systémů cloudového HR](../saas-apps/workday-tutorial.md). </br>Azure AD může zřídit identity v aplikacích SaaS s [povoleným SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) , aby automaticky poskytovaly aplikace s potřebnými podrobnostmi pro povolení přístupu pro uživatele. |
|Zřizování: externí identity| Organizace vytváří externí uživatele ručně jako běžné uživatele ve vyhrazené externí doménové struktuře služby AD, což má za následek režijní náklady na správu životního cyklu externích identit (uživatelů typu Host).| Azure AD poskytuje speciální třídu identity pro podporu externích identit. [Azure AD B2B](/azure/active-directory/b2b/) bude spravovat odkaz na identitu externího uživatele, aby se ujistili, že jsou platné. |
| Správa nároků a skupin| Správci provádějí uživatelské členy skupin. Vlastníci aplikací a prostředků pak poskytnou skupinám přístup k aplikacím nebo prostředkům.| [Skupiny](./active-directory-groups-create-azure-portal.md) jsou také dostupné ve službě Azure AD a správci můžou k udělení oprávnění k prostředkům použít taky skupiny. Ve službě Azure AD můžou správci přiřadit členství do skupin ručně nebo použít dotaz k dynamickému zahrnutí uživatelů do skupiny. </br> Správci můžou použít [správu nároků](../governance/entitlement-management-overview.md) ve službě Azure AD, aby měli uživatelé přístup ke kolekci aplikací a prostředků pomocí pracovních postupů a v případě potřeby i podle časových kritérií. |
| Správa správců|Organizace budou používat kombinaci domén, organizačních jednotek a skupin ve službě AD k delegování oprávnění správce ke správě adresáře a prostředků, které ovládá.| Azure AD poskytuje [předdefinované role](./active-directory-users-assign-role-azure-portal.md) se systémem řízení přístupu na základě role Azure AD (Azure AD RBAC) s omezeným přístupem k [vytváření vlastních rolí](../roles/custom-overview.md) pro delegování privilegovaného přístupu k systému identit, aplikacím a prostředkům, které ovládá.</br>Správa rolí se dá rozšířit pomocí [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) a poskytovat přístup k privilegovaným rolím za běhu, s omezeným časem nebo pomocí pracovního postupu. |
| Správa přihlašovacích údajů| Přihlašovací údaje ve službě Active Directory jsou založené na heslech, ověřování certifikátů a ověřování pomocí čipové karty. Hesla se spravují pomocí zásad hesel, které jsou založené na délce hesla, vypršení platnosti a složitosti.|Azure AD používá inteligentní [ochranu heslem](../authentication/concept-password-ban-bad.md) pro Cloud a místní prostředí. Ochrana zahrnuje chytré zamykání a blokuje společná a vlastní hesla a náhrady. </br>Azure AD významně zvyšuje zabezpečení [prostřednictvím služby Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) a technologií bez [hesla](../authentication/concept-authentication-passwordless.md) , jako je FIDO2. </br>Služba Azure AD snižuje náklady na podporu tím, že poskytuje uživatelům [Samoobslužný systém pro resetování hesla](../authentication/concept-sspr-howitworks.md) . |
| **Aplikace**|||
| Aplikace infrastruktury|Služba Active Directory je základem pro mnoho místních komponent infrastruktury, například DNS, DHCP, IPSec, Wi-Fi, NPS a VPN Access.|V novém cloudovém světě je Azure AD novou rovinou ovládacích prvků pro přístup k aplikacím, které se spoléhají na řízení sítě. Když se uživatelé ověřují[, podmíněný přístup (CA)](../conditional-access/overview.md)bude řídit, kteří uživatelé budou mít přístup k aplikacím, které jsou v požadovaných podmínkách.|
| Tradiční a starší aplikace| Většina místních aplikací používá protokol LDAP, Windows-Integrated ověřování (NTLM a Kerberos) nebo ověřování na základě hlaviček k řízení přístupu uživatelům.| Azure AD může poskytovat přístup k těmto typům místních aplikací pomocí agentů [proxy aplikací služby Azure AD](../manage-apps/application-proxy.md) místně běžících v místním prostředí. Pomocí této metody může Azure AD ověřovat uživatele Active Directory místně pomocí protokolu Kerberos při migraci nebo nutnosti koexistovat se staršími aplikacemi. |
| Aplikace SaaS|Služba Active Directory nepodporuje aplikace SaaS nativně a vyžaduje federační systém, například AD FS.|Aplikace SaaS podporující OAuth2, SAML a WS- \* Authentication lze integrovat pro ověřování pomocí služby Azure AD. |
| Obchodní aplikace (LOB) s moderním ověřováním|Organizace můžou použít AD FS se službou Active Directory k podpoře obchodních aplikací vyžadujících moderní ověřování.| Obchodní aplikace vyžadující moderní ověřování se dají nakonfigurovat tak, aby pro ověřování používaly službu Azure AD. |
| Služby střední vrstvy/démona|Služby běžící v místních prostředích obvykle používají účty služby AD nebo skupinové účty spravované služby (gMSA) ke spuštění. Tyto aplikace pak zdědí oprávnění k účtu služby.| Azure AD poskytuje [spravované identity](../managed-identities-azure-resources/index.yml) pro spouštění dalších úloh v cloudu. Životní cyklus těchto identit spravuje Azure AD a je vázaný na poskytovatele prostředků se nedá použít k získání přístupu zadní vrátka k jiným účelům.|
| **Zařízení**|||
| Mobilní|Služba Active Directory nativně nepodporuje mobilní zařízení bez řešení třetích stran.| Řešení správy mobilních zařízení od Microsoftu, Microsoft Intune, je integrováno se službou Azure AD. Microsoft Intune poskytuje systému identit informace o stavu zařízení k vyhodnocení během ověřování. |
| Stolní počítače se systémem Windows|Služba Active Directory umožňuje připojit zařízení s Windows k doméně a spravovat je pomocí Zásady skupiny, System Center Configuration Manager nebo jiných řešení třetích stran.|Zařízení s Windows je možné [připojit ke službě Azure AD](../devices/index.yml). Podmíněný přístup může v rámci procesu ověřování zjistit, jestli je zařízení připojené k Azure AD. Zařízení s Windows je taky možné spravovat pomocí [Microsoft Intune](/intune/what-is-intune). V takovém případě podmíněný přístup zvažte, jestli zařízení dodržuje předpisy (například aktualizované opravy zabezpečení a signatury virů) ještě před tím, než povolí přístup k aplikacím.|
| Windows servery| Služba Active Directory poskytuje silné možnosti správy místních serverů Windows pomocí Zásady skupiny nebo jiných řešení pro správu.| Virtuální počítače s Windows serverem v Azure je možné spravovat pomocí [Azure AD Domain Services](../../active-directory-domain-services/index.yml). [Spravované identity](../managed-identities-azure-resources/index.yml) se dají použít, když virtuální počítače potřebují přístup k prostředkům nebo adresářům systému identit.|
| Úlohy Linux/UNIX|Služba Active Directory není nativně podporována bez řešení jiných výrobců, i když počítače se systémem Linux lze konfigurovat pro ověřování pomocí služby Active Directory jako sféru protokolu Kerberos.|Virtuální počítače se systémem Linux/UNIX můžou k přístupu k systémům identit a prostředkům používat [spravované identity](../managed-identities-azure-resources/index.yml) . Některé organizace migrují tyto úlohy do cloudových kontejnerových technologií, které můžou používat i spravované identity.|

## <a name="next-steps"></a>Další kroky

- [Co je Azure Active Directory?](./active-directory-whatis.md)
- [Porovnání samoobslužně spravovaných Active Directory Domain Services, Azure Active Directory a spravovaných Azure Active Directory Domain Services](../../active-directory-domain-services/compare-identity-solutions.md)
- [Nejčastější dotazy týkající se Azure Active Directory](./active-directory-faq.md)
- [Co je nového v Azure Active Directory?](./whats-new.md)
