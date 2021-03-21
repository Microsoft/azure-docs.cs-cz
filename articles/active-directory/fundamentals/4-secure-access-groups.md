---
title: Zabezpečení externího přístupu pomocí skupin v Azure Active Directory a Microsoft 365
description: Skupiny Azure Active Directory a Microsoft 365 lze použít ke zvýšení zabezpečení při přístupu externích uživatelů k vašim prostředkům.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d9d63c7a703987d7b17e6e03d8b5596d5f1dfa5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560612"
---
# <a name="securing-external-access-with-groups"></a>Zabezpečení externího přístupu pomocí skupin 

Skupiny jsou důležitou součástí jakékoli strategie řízení přístupu. Skupiny zabezpečení Azure Active Directory (Azure AD) a skupiny Microsoft 365 (M365) se dají použít jako základ pro zabezpečení přístupu k prostředkům.

Skupiny jsou nejlepší možností, jak použít jako základ pro následující mechanismy řízení přístupu:

* Zásady podmíněného přístupu

* Balíčky pro přístup pro správu nároků 

* Přístup k prostředkům M365, Microsoft teams a SharePointovým webům

Skupiny mají následující role:

* Vlastníci – vlastníci skupiny spravují nastavení skupiny a její členství.

* Členové – členové, kteří dědí oprávnění a přístup přiřazený ke skupině.

* Hosté – hosté jsou členy mimo vaši organizaci. 

## <a name="determine-your-group-strategy"></a>Určení strategie skupiny

Při vývoji strategie skupiny pro zabezpečení externího přístupu k vašim prostředkům zvažte, jestli [máte požadované stav zabezpečení](1-secure-access-posture.md) a určete následující možnosti.

* **Kdo by měl být schopný vytvářet skupiny?** Chcete vytvořit skupiny jenom správců, nebo chcete, aby se tyto skupiny vytvořily i zaměstnanci a externími uživateli.

   * *Ve výchozím nastavení může každý člen tenanta vytvořit skupiny zabezpečení Azure AD*. 

      * [Přístup k portálu můžete omezit na portál bez oprávnění správce](../develop/howto-restrict-your-app-to-a-set-of-users.md) a zakázat možnost vytváření skupin v [PowerShellu.](../enterprise-users/groups-troubleshooting.md) 

      * Můžete také [nastavit samoobslužnou správu skupin v Azure Active Directory](../enterprise-users/groups-self-service-management.md). 

   * *Ve výchozím nastavení mohou všichni uživatelé vytvářet skupiny M365 a skupiny jsou otevřeny pro všechny (interní a externí) uživatele ve vašem tenantovi, aby se mohli připojit*. 

      * [Vytvoření skupiny Microsoft 365 můžete omezit](/microsoft-365/solutions/manage-creation-of-groups) na členy určité skupiny zabezpečení. Ke konfiguraci tohoto nastavení použijte Windows PowerShell. 

* **Kdo by měl být schopný pozvat lidi do skupin?** Mohou všichni členové skupiny přidávat další členy nebo mohou pouze vlastníci skupiny přidat členy?

* **Koho se dá pozvat do skupin?** Ve výchozím nastavení je možné přidat externí uživatele do skupin. 

### <a name="assign-users-to-groups"></a>Přiřazení uživatelů ke skupinám

Uživatele je možné přiřadit do skupin ručně na základě atributů uživatele v jejich uživatelském objektu nebo u jiných kritérií. Uživatelům lze přiřadit pouze skupiny dynamicky na základě jejich atributů.

Můžete například přiřadit uživatele do skupin na základě jejich:

* konkrétní název nebo oddělení úlohy

* partnerská organizace, ke které patří (ručně nebo prostřednictvím připojených organizací)

* Typ uživatele (člen nebo host)

* účast v konkrétním projektu (ručně)

* location

Dynamické skupiny můžou obsahovat buď uživatele, nebo zařízení, ale ne obojí. Můžete přidat dotazy založené na uživatelských atributech a přiřadit uživatele do dynamické skupiny. Následující příklad ukazuje dotazy, které přidávají uživatele do skupiny, pokud jsou členy (ne hosté) a ve finančním oddělení.

![Snímek obrazovky s konfigurací dynamických pravidel členství](media/secure-external-access/4-dynamic-membership-rules.png)

Další informace o dynamických skupinách najdete v tématu [Vytvoření nebo aktualizace dynamické skupiny v Azure Active Directory.](../enterprise-users/groups-create-rule.md)

### <a name="do-not-use-groups-for-multiple-purposes"></a>Nepoužívejte skupiny pro více účelů

Pokud používáte skupiny pro účely zabezpečení nebo přístupu k prostředkům, je důležité, aby měly jednu funkci. Pokud se skupina používá pro udělení přístupu k prostředkům, neměla by se používat k žádným jiným účelům. Pokud se skupina používá pro obecné účely, jako je například definování umístění nebo členství v týmu, neměli byste ji také používat k zabezpečení přístupu. 

Pro skupiny zabezpečení doporučujeme konvence pojmenování, které tento účel znemožňuje vymazat. Například:

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>Typy skupin

Skupiny zabezpečení Azure AD i skupiny Microsoft 365 můžete vytvořit z portálu Azure AD nebo na portálu pro správu M365. Oba typy se dají použít jako základ pro zabezpečení externího přístupu:

|Požadavky | Skupiny zabezpečení Azure AD (ruční a dynamické)| Skupiny Microsoft 365 |
| - | - | - |
| Co může skupina obsahovat?| Uživatelé<br>Skupiny<br>Instanční objekty<br>Zařízení| Jenom uživatelé |
| Kde se skupina vytvořila?| Portál Azure AD<br>Portál M365 (Pokud má být povolený e-mail)<br>PowerShell<br>Microsoft Graph<br>Portál pro koncové uživatele| Portál M365<br>Portál Azure AD<br>PowerShell<br>Microsoft Graph<br>V Microsoft 365ch aplikacích |
| Kdo se ve výchozím nastavení vytváří?| Administrators <br>Koncoví uživatelé| Administrators<br>Koncoví uživatelé |
| Kdo se dá ve výchozím nastavení přidat?| Interní uživatelé (členové)| Členové tenanta a hosté z jakékoli organizace |
| K čemu udělí přístup?| Pouze prostředky, ke kterým je přiřazena.| Všechny prostředky související se skupinami:<br>(Skupinová poštovní schránka, web, tým, chaty a další zahrnuté prostředky M365)<br>Všechny ostatní prostředky, ke kterým se přidá skupina |
| Dá se použít s| Podmíněný přístup<br>Správa nároků<br>Licencování skupin| Podmíněný přístup<br>Správa nároků<br>Popisky citlivosti |



Pomocí Microsoft 365 skupin můžete vytvořit a spravovat sadu prostředků Microsoft 365, jako je například tým a jeho přidružené weby a obsah. Jsou skvělou volbou pro úsilí založené na projektech. 

 

## <a name="azure-ad-security-groups"></a>Skupiny zabezpečení služby Azure AD 

[Skupiny zabezpečení Azure AD](./active-directory-manage-groups.md) můžou obsahovat uživatele nebo zařízení a dají se použít ke správě přístupu k 

* Prostředky Azure, jako jsou aplikace Microsoft 365, vlastní aplikace a aplikace SaaS (software jako služba), jako je ServiceNow Dropboxu.

* Data a předplatná Azure.

* Služby Azure.

Skupiny zabezpečení Azure AD se taky dají použít k těmto akcím:

* Přiřaďte licence pro služby, jako je M365, Dynamics 365 a Enterprise mobility and Security. Další informace najdete v tématu [licencování na základě skupin](./active-directory-licensing-whatis-azure-portal.md).

* Přiřaďte zvýšená oprávnění. Další informace najdete v tématu [použití skupin cloudu ke správě přiřazení rolí (Preview](../roles/groups-concept.md)). 

Pokud chcete vytvořit skupinu [v Azure Portal](./active-directory-groups-create-azure-portal.md) přejděte na Azure Active Directory a pak na skupiny. Můžete také vytvořit skupiny zabezpečení Azure AD pomocí [rutin PowerShellu](../enterprise-users/groups-settings-v2-cmdlets.md). 

> [!NOTE]
> Skupinu zabezpečení je možné použít pro přiřazení až 1500 aplikací, ale ne víc. 

![Snímek obrazovky s vytvořením skupiny zabezpečení](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> Pokud **chcete vytvořit skupinu zabezpečení s povolenými e-maily, otevřete [Centrum pro správu Microsoft 365](https://admin.microsoft.com/)**. Nemůžete ho vytvořit na portálu Azure AD. 
<br>V okamžiku vytvoření e-mailu musíte povolit skupinu zabezpečení. Nemůžete ho později povolit.

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>Hybridní organizace a skupiny zabezpečení Azure AD

Hybridní organizace mají místní infrastrukturu i cloudovou infrastrukturu Azure AD. Řada hybridních organizací, které používají službu Active Directory, vytváří své skupiny zabezpečení místně a synchronizuje je do cloudu. Pomocí této metody lze do skupin zabezpečení přidat pouze uživatele v místním prostředí.

**Chraňte svoji místní infrastrukturu před ohrožením, protože k získání přístupu k vašemu Microsoft 365 tenantovi se dá využít místně porušování**. Pokyny najdete v tématu [ochrana Microsoft 365 z místních útoků](./protect-m365-from-on-premises-attacks.md) .

## <a name="microsoft-365-groups"></a>Skupiny Microsoft 365

[Microsoft 365 skupiny](/microsoft-365/admin/create-groups/office-365-groups) představují základní službu pro členství, která se řídí všemi přístupy přes M365. Je možné je vytvořit z [Azure Portal](https://portal.azure.com/)nebo z [portálu M365](https://admin.microsoft.com/). Při vytvoření skupiny M365 udělíte přístup ke skupině prostředků, které se používají ke spolupráci. Úplný seznam těchto prostředků najdete v tématu [Přehled skupin Microsoft 365 pro správce](/microsoft-365/admin/create-groups/office-365-groups) .

M365 skupiny mají pro své role následující drobné odlišnosti.

* **Vlastníci** – vlastníci skupiny můžou přidávat nebo odebírat členy a mít jedinečná oprávnění, jako je možnost odstraňovat konverzace ze sdílené doručené pošty nebo měnit nastavení skupiny. Vlastníci skupiny můžou přejmenovat skupinu, aktualizovat popis nebo obrázek a další.

* **Členové** – členové mají přístup ke všem ve skupině, ale nemůžou měnit nastavení skupiny. Ve výchozím nastavení mohou členové skupiny pozvat hostům, aby se připojili ke skupině, i když [Toto nastavení můžete řídit](/microsoft-365/admin/create-groups/manage-guest-access-in-groups).

* **Hosté** – členové skupiny jsou členy, kteří jsou mimo vaši organizaci. Hosté mají ve výchozím nastavení určitá omezení funkcí v týmech.

 

### <a name="m365-group-settings"></a>Nastavení skupiny M365

Vyberte e-mailový alias, ochranu osobních údajů a to, jestli chcete skupinu pro týmy povolit v době nastavování. 

![Snímek obrazovky s úpravami nastavení Microsoft 365 skupiny](media/secure-external-access/4-edit-group-settings.png)

Po dokončení instalace můžete přidat členy a nakonfigurovat nastavení pro použití e-mailů atd.

### <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích o zabezpečení externího přístupu k prostředkům. Doporučujeme, abyste provedli akce v uvedeném pořadí.

1. [Určení požadovaného stav zabezpečení pro externí přístup](1-secure-access-posture.md)

2. [Zjistit aktuální stav](2-secure-access-current-state.md)

3. [Vytvoření plánu zásad správného řízení](3-secure-access-plan.md)

4. [Použijte skupiny pro zabezpečení](4-secure-access-groups.md) (jste tady.)

5. [Přechod do Azure AD B2B](5-secure-access-b2b.md)

6. [Zabezpečený přístup se správou oprávnění](6-secure-access-entitlement-managment.md)

7. [Zabezpečený přístup pomocí zásad podmíněného přístupu](7-secure-access-conditional-access.md)

8. [Zabezpečený přístup s popisky citlivosti](8-secure-access-sensitivity-labels.md)

9. [Zabezpečený přístup k Microsoft teams, OneDrivu a SharePointu](9-secure-access-teams-sharepoint.md)