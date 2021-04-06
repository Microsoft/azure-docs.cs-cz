---
title: Ochrana Microsoft 365 z místních útoků
description: Pokyny k tomu, jak zajistit, aby místní útok neovlivnil Microsoft 365.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d548f4d792d8980e2aa5040b09530eaf7868c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609902"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Ochrana Microsoft 365 z místních útoků

Mnoho zákazníků připojí své soukromé podnikové sítě k Microsoft 365 využívat výhody jejich uživatelů, zařízení a aplikací. Tyto privátní sítě ale můžou ohrozit mnoho dobře dokumentovaných způsobů. Vzhledem k tomu, že Microsoft 365 slouží jako řazení nervového systému pro mnoho organizací, je důležité ho chránit před ohrožením místní infrastruktury.

V tomto článku se dozvíte, jak nakonfigurovat vaše systémy pro ochranu Microsoft 365 cloudového prostředí z místního zabezpečení. Primárně se zaměříme na: 

- Nastavení konfigurace tenanta Azure Active Directory (Azure AD).
- Způsob, jakým se můžou klienti Azure AD bezpečně připojit k místním systémům.
- Kompromisy potřebné k provozu systémů způsobem, který chrání vaše cloudové systémy před místním zabezpečením.

Důrazně doporučujeme, abyste implementovali tyto pokyny k zabezpečení Microsoft 365 cloudového prostředí.
> [!NOTE]
> Tento článek byl zpočátku publikován jako Blogový příspěvek. Byla přesunuta do aktuálního umístění pro longevity a údržbu.
>
> Pokud chcete vytvořit offline verzi tohoto článku, použijte funkci Tisk do PDF v prohlížeči. Pravidelně kontrolujte aktualizace.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Primární vektory hrozeb z ohrožených místních prostředí


Cloudové prostředí Microsoft 365 využívá rozsáhlou monitorovací a bezpečnostní infrastrukturu. Pomocí strojového učení a lidských inteligentních funkcí se Microsoft 365 prohlíží napříč celosvětovým provozem. Může rychle detekovat útoky a umožní vám překonfigurovat skoro v reálném čase. 

V hybridních nasazeních, která se připojují k místní infrastruktuře Microsoft 365, mnoho organizací deleguje důvěryhodnost místních komponent pro kritická ověřování a rozhodování o správě stavu objektů adresáře.
Pokud dojde k ohrožení bezpečnosti místního prostředí, stanou se tyto vztahy důvěryhodnosti příležitostmi útočníka při navýšení vašeho Microsoft 365ho prostředí.

Dva primární vektory hrozeb jsou *vztahy důvěryhodnosti federace* a *Synchronizace účtů.* Oba vektory můžou správci umožnit přístup k vašemu cloudu.

* K ověřování Microsoft 365 prostřednictvím místní infrastruktury identit se používají **federované vztahy důvěryhodnosti**, jako je ověřování SAML. Pokud dojde k ohrožení bezpečnosti podpisového certifikátu tokenu SAML, federace umožňuje všem, kdo tento certifikát vystupovat, zosobnit libovolného uživatele v cloudu. *Pokud je to možné, doporučujeme zakázat vztahy důvěryhodnosti federace pro ověřování a Microsoft 365.*

* **Synchronizace účtu** se dá použít k úpravě privilegovaných uživatelů (včetně jejich přihlašovacích údajů) nebo skupin, které mají oprávnění správce v Microsoft 365. *Doporučujeme, abyste zajistili, že synchronizované objekty nebudou mít žádná oprávnění nad rámec uživatele v Microsoft 365,* a to buď přímo, nebo prostřednictvím zahrnutí do důvěryhodných rolí nebo skupin. Ujistěte se, že tyto objekty nemají v důvěryhodných cloudových rolích nebo skupinách žádné přímé ani vnořené přiřazení.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Ochrana Microsoft 365 před místním zabezpečením


Chcete-li vyřešit výše uvedené vektory hrozeb, doporučujeme dodržovat principy znázorněné v následujícím diagramu:

![Referenční architektura pro ochranu Microsoft 365.](media/protect-m365/protect-m365-principles.png)

1. **Plně izolujte účty správců Microsoft 365.** Měly by být:

    * Hlavní ve službě Azure AD.

     * Ověřováno pomocí vícefaktorového ověřování.

     *  Zabezpečeno pomocí podmíněného přístupu Azure AD.

     *  Dostupné jenom pomocí pracovních stanic spravovaných Azure.

    Tyto účty správců jsou účty s omezeným použitím. *Žádné místní účty by neměly mít v Microsoft 365 oprávnění správce.* 

    Další informace najdete v tématu [Přehled rolí správce Microsoft 365](/microsoft-365/admin/add-users/about-admin-roles). Viz také [role Microsoft 365 ve službě Azure AD](../roles/m365-workload-docs.md).

1. **Správa zařízení z Microsoft 365.** Použijte službu Azure AD JOIN a cloudovou správu mobilních zařízení (MDM), abyste vyloučili závislosti na místní infrastruktuře správy zařízení. Tyto závislosti můžou ohrozit ovládací prvky zařízení a zabezpečení.

1. **Ujistěte se, že žádný místní účet nemá zvýšená oprávnění na Microsoft 365.**
    Některé účty přistupují k místním aplikacím, které vyžadují ověřování protokolem NTLM, LDAP nebo Kerberos. Tyto účty musí být v místní infrastruktuře identity organizace. Ujistěte se, že tyto účty, včetně účtů služeb, nejsou zahrnuté do privilegovaných cloudových rolí nebo skupin. Také zajistěte, aby změny těchto účtů neovlivnily integritu vašeho cloudového prostředí. Privilegovaný místní software nesmí umožňovat vliv Microsoft 365 privilegovaných účtů nebo rolí.

1. **Pomocí cloudového ověřování Azure AD** Eliminujte závislosti na místních přihlašovacích údajích. Vždy používejte silné ověřování, například Windows Hello, FIDO, Microsoft Authenticator nebo Azure AD vícefaktorového ověřování.

## <a name="specific-security-recommendations"></a>Konkrétní doporučení zabezpečení


V následujících částech najdete konkrétní pokyny k implementaci výše popsaných principů.

### <a name="isolate-privileged-identities"></a>Izolace privilegovaných identit


Ve službě Azure AD jsou uživatelé, kteří mají privilegované role, jako jsou správci, kořenem důvěryhodnosti pro sestavování a správu zbývajícího prostředí. K minimalizaci vlivu ohrožení zabezpečení implementujte následující postupy.

* Používejte jenom cloudové účty pro Azure AD a Microsoft 365 privilegované role.

* Nasazení [privilegovaných přístupových zařízení](/security/compass/privileged-access-devices#device-roles-and-profiles) pro privilegovaný přístup ke správě Microsoft 365 a Azure AD.

*  Nasaďte [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) pro přístup JIT (just-in-time) ke všem lidským účtům s privilegovanými rolemi. Pro aktivaci rolí vyžadovat silné ověřování.

* Poskytněte administrativní role, které umožňují [nejnižší oprávnění nezbytnou k provedení požadovaných úloh](../roles/delegate-by-task.md).

* Pokud chcete povolit možnosti přiřazení s bohatou rolí, která zahrnuje delegování a více rolí současně, zvažte použití skupin zabezpečení Azure AD nebo skupin Microsoft 365. Tyto skupiny se hromadně označují jako *cloudové skupiny*. [Povolit také řízení přístupu na základě role](../roles/groups-assign-role.md). [Jednotky pro správu](../roles/administrative-units.md) můžete použít k omezení rozsahu rolí na část organizace.

* Nasaďte [účty pro nouzový přístup](../roles/security-emergency-access.md). K ukládání přihlašovacích údajů *nepoužívejte místní* trezory hesel.

Další informace najdete v tématu [zabezpečení privilegovaného přístupu](/security/compass/overview). Podívejte [se také na postupy zabezpečeného přístupu pro správce ve službě Azure AD](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Použít cloudové ověřování 

Přihlašovací údaje představují primární vektor útoku. Implementací následujících postupů zajistíte bezpečnější zabezpečení těchto údajů:

* [Nasaďte ověřování neheslem](../authentication/howto-authentication-passwordless-deployment.md). Nasaďte přihlašovací údaje, abyste mohli co nejvíc omezit použití hesel. Tyto přihlašovací údaje se spravují a ověřují nativně v cloudu. Vyberte si z těchto metod ověřování:

   * [Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Aplikace Microsoft Authenticator](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 klíče zabezpečení](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Nasazení vícefaktorového ověřování](../authentication/howto-mfa-getstarted.md). Zřizování [více silných přihlašovacích údajů pomocí vícefaktorového ověřování Azure AD](../fundamentals/resilience-in-credentials.md). Přístup ke cloudovým prostředkům pak bude vyžadovat přihlašovací údaje, které jsou spravované v Azure AD, a navíc k místnímu heslu, na kterém je možné manipulovat. Další informace najdete v tématu [Vytvoření odolné strategie správy řízení přístupu pomocí Azure AD](./resilience-overview.md).

### <a name="limitations-and-tradeoffs"></a>Omezení a kompromisy

* Správa hesel pro hybridní účet vyžaduje hybridní komponenty, jako jsou agenti ochrany heslem a agenti zpětného zápisu hesla. Pokud dojde k ohrožení bezpečnosti vaší místní infrastruktury, můžou útočníci řídit počítače, na kterých se tito agenti nacházejí. Tato zranitelnost by neohrozila vaši cloudovou infrastrukturu. Vaše cloudové účty ale nechrání tyto součásti před místním zabezpečením.

*  Místní účty synchronizované ze služby Active Directory jsou označené tak, že ve službě Azure AD nikdy nevyprší platnost. Toto nastavení je obvykle omezeno místním nastavením hesla služby Active Directory. Pokud je ale vaše místní instance služby Active Directory ohrožená a synchronizace je zakázaná, musíte nastavit možnost [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md) pro vynucení změn hesel.

## <a name="provision-user-access-from-the-cloud"></a>Zřízení přístupu uživatele z cloudu

*Zřizování* se týká vytváření uživatelských účtů a skupin v aplikacích nebo zprostředkovatelích identity.

![Diagram architektury zřizování](media/protect-m365/protect-m365-provision.png)

Doporučujeme následující metody zřizování:

* **Zřízení z cloudových aplikací pro lidské nasazení do Azure AD**: Toto zřizování umožňuje izolaci místního zabezpečení, aniž by došlo k přerušení přejezdové cykly stěhovací aplikace z vašich aplikací cloudového HR do Azure AD.

* **Cloudové aplikace**: tam, kde je to možné, nasaďte [zřizování aplikací Azure AD](../app-provisioning/user-provisioning.md) , a to na rozdíl od místních řešení zřizování. Tato metoda chrání některé z vašich aplikací SaaS (software jako služba), které nemusejí mít vliv na škodlivé profily hackerů v místních podjednáních. 

* **Externí identity**: Využijte [spolupráci Azure AD B2B](../external-identities/what-is-b2b.md).
    Tato metoda snižuje závislost na místních účtech pro externí spolupráci s partnery, zákazníky a dodavateli. Pečlivě vyhodnoťte všechny přímé federace s jinými zprostředkovateli identity. Doporučujeme omezit účty hostů B2B následujícími způsoby:

   *  Omezte přístup hosta na skupiny procházení a další vlastnosti v adresáři. Pomocí nastavení externí spolupráce můžete omezit schopnost hostů číst skupiny, které nejsou členy. 

    *   Zablokuje přístup k Azure Portal. Je možné provádět vzácná nezbytná výjimka.  Vytvořte zásady podmíněného přístupu, které budou zahrnovat všechny hosty a externí uživatele. Potom [implementujte zásadu, která zablokuje přístup](../../role-based-access-control/conditional-access-azure-management.md). 

* **Odpojené doménové struktury**: použijte [zřizování cloudu Azure AD](../cloud-sync/what-is-cloud-sync.md). Tato metoda umožňuje připojit se k odpojeným doménovým strukturám a eliminovat nutnost navázat připojení mezi doménovými strukturami nebo vztahy důvěryhodnosti, což může rozšířit dopad na narušení místního prostředí. 
 
### <a name="limitations-and-tradeoffs"></a>Omezení a kompromisy

Při použití ke zřízení hybridních účtů systém Azure-AD-on-Cloud-HR spoléhá na místní synchronizaci, aby se dokončil tok dat ze služby Active Directory do Azure AD. Pokud je synchronizace přerušená, nové záznamy zaměstnanců nebudou ve službě Azure AD dostupné.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Použití cloudových skupin pro spolupráci a přístup

Cloudové skupiny umožňují oddělit vaše spolupráce a přístup z vaší místní infrastruktury.

* **Spolupráce**: použití skupin Microsoft 365 a Microsoft Teams pro moderní spolupráci. Vyřazení místních distribučních seznamů z provozu a [upgrade distribučních seznamů na Microsoft 365 skupiny v aplikaci Outlook](/office365/admin/manage/upgrade-distribution-lists).

* **Přístup**: k autorizaci přístupu k aplikacím ve službě Azure AD použijte skupiny zabezpečení Azure AD nebo skupiny Microsoft 365.
* **Licencování sady office 365**: k zřízení sady Office 365 pomocí pouze cloudových skupin použijte licencování na základě skupin. Tato metoda odděluje řízení členství ve skupině z místní infrastruktury.

Vlastníci skupin, které se používají pro přístup, by měly být považovány za privilegované identity, aby se zabránilo převzetí členství v místním ohrožení zabezpečení.
Převzetí by zahrnovalo přímou manipulaci s členstvím ve skupině v místním prostředí nebo manipulaci s místními atributy, které mohou ovlivnit členství v dynamické skupině v Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Správa zařízení z cloudu


Pro bezpečnou správu zařízení používejte funkce Azure AD.

-   **Použití pracovních stanic s Windows 10**: [Nasaďte zařízení připojená k Azure AD](../devices/azureadjoin-plan.md) pomocí zásad MDM. Povolením automatického [pilotního projektu Windows](/mem/autopilot/windows-autopilot) můžete plně automatizovat prostředí pro zřizování.

    -   Vyřadí se počítače, které používají Windows 8.1 a starší verze.

    -   Nesaďte počítače se serverovými operačními systémy jako pracovní stanice.

    -   Jako zdroj autority pro všechny úlohy správy zařízení použijte [Microsoft Intune](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/microsoft-intune) .

-   [**Nasazení privilegovaných přístupových zařízení**](/security/compass/privileged-access-devices#device-roles-and-profiles): pomocí privilegovaného přístupu můžete spravovat Microsoft 365 a Azure AD.

## <a name="workloads-applications-and-resources"></a>Úlohy, aplikace a prostředky 

-   **Místní systémy jednotného přihlašování (SSO)** 

    Vyřadíte všechny místní infrastruktury a infrastrukturu pro správu webového přístupu. Konfigurace aplikací pro použití Azure AD  

-   **SaaS a obchodní aplikace (LOB), které podporují protokoly moderního ověřování** 

    [Použijte Azure AD pro jednotné přihlašování](../manage-apps/what-is-single-sign-on.md). Víc aplikací, které nakonfigurujete pro použití Azure AD k ověřování, je menší riziko při místním ohrožení zabezpečení.


* **Starší verze aplikací** 

   * Můžete povolit ověřování, autorizaci a vzdálený přístup k starším aplikacím, které nepodporují moderní ověřování. Použijte [Azure proxy aplikací služby AD](../manage-apps/application-proxy.md). Můžete je také povolit prostřednictvím sítě nebo řešení pro doručování aplikací pomocí [Integrace partnerů pro zabezpečení hybridního přístupu](../manage-apps/secure-hybrid-access.md).   

   * Vyberte dodavatele sítě VPN, který podporuje moderní ověřování. Integrujte své ověřování s využitím Azure AD. V případě místního zabezpečení můžete pomocí Azure AD zakázat nebo blokovat přístup zakázáním sítě VPN.

*  **Aplikace a servery úloh**

   * Aplikace nebo prostředky, které vyžadují servery, se dají migrovat do infrastruktury Azure jako služby (IaaS). Pomocí [Azure AD Domain Services](../../active-directory-domain-services/overview.md) (Azure služba AD DS) můžete oddělit vztah důvěryhodnosti a závislosti na místních instancích služby Active Directory. Aby se zajistilo toto oddělení, ujistěte se, že virtuální sítě používané pro Azure služba AD DS nemají připojení k podnikovým sítím.

   * Postupujte podle pokynů pro [vrstvení přihlašovacích údajů](/security/compass/privileged-access-access-model#ADATM_BM). Aplikační servery se typicky považují za prostředky vrstvy 1.

## <a name="conditional-access-policies"></a>Zásady podmíněného přístupu

Podmíněný přístup Azure AD slouží k interpretaci signálů a jejich použití k rozhodování o ověřování. Další informace najdete v tématu [plán nasazení podmíněného přístupu](../conditional-access/plan-conditional-access.md).

* Pokud je to možné, používejte podmíněný přístup k [blokování starších protokolů ověřování](../conditional-access/howto-conditional-access-policy-block-legacy.md) . Kromě toho zakažte starší protokoly ověřování na úrovni aplikace pomocí konfigurace specifické pro aplikaci.

   Další informace najdete v tématu [protokoly pro starší verze ověřování](../fundamentals/auth-sync-overview.md). Nebo si přečtěte konkrétní podrobnosti pro [Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) a [SharePoint Online](/powershell/module/sharepoint-online/set-spotenant).

* Implementujte doporučené [Konfigurace identit a přístupu k zařízením](/microsoft-365/security/office-365-security/identity-access-policies).

* Pokud používáte verzi služby Azure AD, která neobsahuje podmíněný přístup, ujistěte se, že používáte [výchozí hodnoty zabezpečení Azure AD](../fundamentals/concept-fundamentals-security-defaults.md).

   Další informace o licencování funkcí služby Azure AD najdete v článku o [cenách služby Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitor"></a>Monitor 

Po nakonfigurování prostředí, aby chránila vaše Microsoft 365 před místním zabezpečením, [proaktivně monitorujte](../reports-monitoring/overview-monitoring.md) prostředí.
### <a name="scenarios-to-monitor"></a>Scénáře k monitorování

Kromě jakýchkoli scénářů, které jsou specifické pro vaši organizaci, můžete monitorovat následující klíčové scénáře. Například byste měli aktivně monitorovat přístup k důležitým firemním aplikacím a prostředkům.

* **Podezřelá aktivita** 

    Monitorujte všechny [rizikové události Azure AD](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) pro podezřelé aktivity. [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) je nativně integrována s Azure Security Center.

    Definujte síťová [umístění s názvem](../reports-monitoring/quickstart-configure-named-locations.md) , abyste se vyhnuli detekci šumu na signálech založených na poloze. 
*  **Výstrahy analýzy chování uživatelů a entit (UEBA)** 

    Využijte UEBA k získání přehledů o detekci anomálií.
    * Microsoft Cloud App Security (MCAS) poskytuje [UEBA v cloudu](/cloud-app-security/tutorial-ueba).

    * [Místní UEBA můžete integrovat z Azure Advanced Threat Protection (ATP)](/defender-for-identity/install-step2). MCAS čte signály z Azure AD Identity Protection. 

* **Aktivita účtů pro nouzový přístup** 

    Monitorujte veškerý přístup, který používá [účty pro nouzový přístup](../roles/security-emergency-access.md). Vytvořte výstrahy pro vyšetřování. Toto monitorování musí zahrnovat: 

   * Přihlášení. 

   * Správa přihlašovacích údajů. 

   * Jakékoli aktualizace členství ve skupinách. 

   * Přiřazení aplikací. 
* **Aktivita privilegované role**

    Nakonfigurujte a zkontrolujte [výstrahy zabezpečení generované Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts).
    Monitorování přímého přiřazení privilegovaných rolí mimo PIM tím, že generuje výstrahy vždy, když se uživatel přiřadí přímo.

* **Konfigurace pro tenanta Azure AD v úrovni klienta**

    Jakékoli změny v konfiguracích na úrovni klienta by měly generovat výstrahy v systému. Tyto změny zahrnují, ale nejsou omezené na:

  *  Aktualizace vlastních domén  

  * Azure AD B2B se změní na allowlists a adres.

  * Azure AD B2B se změní na povolené zprostředkovatele identity (zprostředkovatelé identity SAML prostřednictvím přímých nebo přesných přihlášení).  

  * Změní se podmíněný přístup nebo zásady rizik. 

* **Aplikační a instanční objekty**
    
   * Nové aplikace nebo instanční objekty, které mohou vyžadovat zásady podmíněného přístupu. 

   * Přihlašovací údaje přidané k instančním objektům
   * Aktivita souhlasu aplikace 

* **Vlastní role**
   * Aktualizuje definice vlastních rolí. 

   * Nově vytvořené vlastní role. 

### <a name="log-management"></a>Správa protokolů

Definujte protokol úložiště a strategii uchovávání protokolů, návrh a implementaci, abyste usnadnili konzistentní sadu nástrojů. Můžete například zvážit systémy zabezpečení a SIEM (Security Information and Event Management), jako je Azure Sentinel, Common dotazy a vyšetřování a forenzní playbooky.

* **Protokoly Azure AD**: ingestování generovaných protokolů a signálů pomocí konzistentně následujících osvědčených postupů pro nastavení, jako jsou diagnostika, uchovávání protokolů a ingestování Siem. 

    Strategie protokolu musí zahrnovat následující protokoly služby Azure AD:
   * Aktivita přihlášení 

   * Protokoly auditu 

   * Rizikové události 

    Azure AD poskytuje [integraci Azure monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md) pro protokol aktivit přihlašování a protokoly auditu. Rizikové události se dají ingestovat prostřednictvím [rozhraní Microsoft Graph API](/graph/api/resources/identityriskevent). [K Azure monitor protokolů můžete streamovat protokoly služby Azure AD](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Protokoly zabezpečení operačních systémů hybridní infrastruktury**: všechny protokoly hybridní infrastruktury identit by měly být archivovány a pečlivě monitorovány jako systém vrstvy 0 z důvodu vlivu na plochu. Zahrnout následující prvky: 

   *  Azure AD Connect . Aby bylo možné monitorovat synchronizaci identity, je nutné nasadit [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md) .

   *  Agenti proxy aplikací 


   * Agenti zpětného zápisu hesla 

   * Počítače brány ochrany heslem  

   * Servery zásad sítě (NPSs), které mají rozšíření RADIUS vícefaktorového ověřování Azure AD 

## <a name="next-steps"></a>Další kroky
* [Vytváření odolnosti proti správě identit a přístupu pomocí Azure AD](resilience-overview.md)

* [Zabezpečení externího přístupu k prostředkům](secure-external-access-resources.md) 
* [Integrace všech svých aplikací s Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)