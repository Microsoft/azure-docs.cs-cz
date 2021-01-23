---
title: Ochrana Microsoft 365 z místních útoků
description: Pokyny k tomu, jak zajistit, aby místní útok neovlivnil Microsoft 365
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
ms.openlocfilehash: ecddb950c06c9f8e61f31e104051f5e3b3640ae5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725006"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Ochrana Microsoft 365 z místních útoků

Mnoho zákazníků připojí své soukromé podnikové sítě k Microsoft 365 využívat výhody jejich uživatelů, zařízení a aplikací. Existuje však mnoho dobře dokumentovaných způsobů, jak mohou být tyto privátní sítě ohroženy. Vzhledem k tomu, že Microsoft 365 slouží jako "nervový systém" pro mnoho organizací, je důležité ho chránit před ohrožením místní infrastruktury.

V tomto článku se dozvíte, jak nakonfigurovat vaše systémy pro ochranu Microsoft 365 cloudového prostředí z místního zabezpečení. Primárně se zaměřujeme na nastavení konfigurace tenanta Azure AD, způsoby, které můžou klienti Azure AD bezpečně připojit k místním systémům, a kompromisy potřebné k provozu vašich systémů způsobem, který chrání vaše cloudové systémy proti místnímu ohrožení zabezpečení.

Důrazně doporučujeme, abyste implementovali tyto pokyny k zabezpečení Microsoft 365 cloudového prostředí.
> [!NOTE]
> Tento článek byl zpočátku publikován jako Blogový příspěvek. Přesunuli jsme se sem pro longevity a údržbu. <br>
Pokud chcete vytvořit offline verzi tohoto článku, použijte funkci Tisk v prohlížeči do formátu PDF. Pravidelně kontrolujte aktualizace.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Primární vektory hrozeb z ohrožených místních prostředí


Cloudové prostředí Microsoft 365 využívá rozsáhlou monitorovací a bezpečnostní infrastrukturu. Pomocí strojového učení a lidských inteligentních nástrojů, které prohlíží napříč celosvětovým provozem, může rychle detekovat útoky a v reálném čase vám umožní překonfigurovat. V hybridních nasazeních, která se připojují k místní infrastruktuře Microsoft 365, mnoho organizací deleguje důvěryhodnost místních komponent pro kritická ověřování a rozhodování o správě stavu objektů adresáře.
Pokud dojde k ohrožení bezpečnosti místního prostředí, tyto vztahy důvěryhodnosti vedou k navýšení možností útočníků k navýšení Microsoft 365ho prostředí.

Dva primární vektory hrozeb jsou **vztahy důvěryhodnosti federace** a **Synchronizace účtů.** Oba vektory můžou správci umožnit přístup k vašemu cloudu.

* K ověřování Microsoft 365 prostřednictvím místní infrastruktury identit se používají **federované vztahy důvěryhodnosti**, jako je ověřování SAML. Pokud dojde k ohrožení bezpečnosti podpisového certifikátu tokenu SAML, federace umožní komukoli s tímto certifikátem zosobnit libovolného uživatele v cloudu. **Pokud je to možné, doporučujeme zakázat vztahy důvěryhodnosti federace pro ověřování a Microsoft 365.**

* **Synchronizace účtu** se dá použít k úpravě privilegovaných uživatelů (včetně jejich přihlašovacích údajů) nebo skupin, kterým jsou udělená oprávnění správce v Microsoft 365. **Doporučujeme, abyste zajistili, že synchronizované objekty neobsahují žádná oprávnění nad rámec uživatele v Microsoft 365,** a to buď přímo, nebo prostřednictvím zahrnutí do důvěryhodných rolí nebo skupin. Ujistěte se, že tyto objekty nemají v důvěryhodných cloudových rolích nebo skupinách žádné přímé ani vnořené přiřazení.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Ochrana Microsoft 365 před místním zabezpečením


Chcete-li vyřešit vektory hrozeb popsané výše, doporučujeme dodržovat níže popsané principy:

![Referenční architektura pro ochranu Microsoft 365 ](media/protect-m365/protect-m365-principles.png)

*  **Plně izolujte účty správců Microsoft 365.** Měly by být

    * Hlavní ve službě Azure AD.

     * Ověřeno službou Multi-Factor Authentication (MFA).

     *  Zabezpečeno pomocí podmíněného přístupu Azure AD.

     *  Dostupné jenom pomocí spravovaných pracovních stanic Azure.

Tyto účty jsou omezené na používání. **V Microsoft 365 by neměly být žádné místní účty s oprávněními správce.** Další informace najdete v tomto [přehledu Microsoft 365 rolí správce](/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).
Viz také [role Microsoft 365 v Azure Active Directory](../roles/m365-workload-docs.md).

*  **Správa zařízení z Microsoft 365.** Použijte službu Azure AD JOIN a cloudovou správu mobilních zařízení (MDM), abyste vyloučili závislosti na místní infrastruktuře správy zařízení, která může ohrozit ovládací prvky zařízení a zabezpečení.

* **Žádný místní účet nemá zvýšená oprávnění na Microsoft 365.**
    Účty, které přistupují k místním aplikacím, které vyžadují ověřování protokolem NTLM, LDAP nebo Kerberos, potřebují účet v místní infrastruktuře identity vaší organizace. Ujistěte se, že tyto účty, včetně účtů služeb, nejsou zahrnuté v privilegovaném cloudových rolích nebo skupinách a že změny těchto účtů nemůžou mít dopad na integritu vašeho cloudového prostředí. Privilegovaný místní software nesmí být schopný ovlivnit Microsoft 365 privilegované účty nebo role.

*  **Pomocí cloudového ověřování Azure AD** Eliminujte závislosti na místních přihlašovacích údajích. Vždy používejte silné ověřování, například Windows Hello, FIDO, Microsoft Authenticator nebo Azure AD MFA.

## <a name="specific-recommendations"></a>Konkrétní doporučení


V následujících částech najdete konkrétní pokyny k implementaci výše popsaných principů.

### <a name="isolate-privileged-identities"></a>Izolace privilegovaných identit


Uživatelé s privilegovanými rolemi, jako jsou správci, jsou ve službě Azure AD kořenem důvěryhodnosti pro sestavování a správu zbývajícího prostředí. Implementací následujících postupů minimalizujte dopad ohrožení bezpečnosti.

* Používejte jenom cloudové účty pro Azure AD a Microsoft 365 privilegované role. d

* Nasazení [privilegovaných přístupových zařízení](/security/compass/privileged-access-devices#device-roles-and-profiles) pro privilegovaný přístup ke správě Microsoft 365 a Azure AD.

*  Nasaďte [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) pro přístup JIT (just in time) ke všem lidským účtům s privilegovanými rolemi a při aktivaci rolí vyžadovat silné ověřování.

* Poskytněte administrativním rolím [nejnižší možné oprávnění k provádění svých úloh](../roles/delegate-by-task.md).

* Pokud chcete povolit bohatší možnosti přiřazování rolí, které zahrnují delegování a více rolí současně, zvažte použití skupin zabezpečení Azure AD nebo skupin Microsoft 365ch (souhrnně "skupiny cloudu") a [Povolení řízení přístupu na základě rolí](../roles/groups-assign-role.md). [Jednotky pro správu](../roles/administrative-units.md) můžete použít také k omezení rozsahu rolí na část organizace.

* Nasaďte [účty pro nouzový přístup](../roles/security-emergency-access.md) a nepoužívejte místní trezory hesel k ukládání přihlašovacích údajů.

Další informace najdete v tématu [zabezpečení privilegovaného přístupu](/security/compass/overview), který obsahuje podrobné pokyny k tomuto tématu. Podívejte se také na téma [postupy zabezpečeného přístupu pro správce ve službě Azure AD](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Použít cloudové ověřování 

Přihlašovací údaje představují primární vektor útoku. Implementací následujících postupů zajistíte bezpečnější zabezpečení přihlašovacích údajů.

* [Nasazení ověřování se nehesly](../authentication/howto-authentication-passwordless-deployment.md): Pokud chcete, aby se hesla co nejvíce omezila, nasadíte přihlašovací údaje, které se nepoužívají. Tyto přihlašovací údaje se spravují a ověřují nativně v cloudu. Vybírejte z těchto možností:

   * [Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Aplikace Authenticator](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 klíče zabezpečení](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Nasazení Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md): zřízení [více silných přihlašovacích údajů pomocí Azure AD MFA](../fundamentals/resilience-in-credentials.md). Přístup ke cloudovým prostředkům pak bude vyžadovat přihlašovací údaje, které jsou spravované v Azure AD, a navíc k místnímu heslu, na kterém je možné manipulovat.

   * Další informace najdete v tématu [vytvoření strategie odolného řízení přístupu pomocí Azure Active Directory](./resilience-overview.md).

**Omezení a kompromisy**

* Správa hesel pro hybridní účet vyžaduje hybridní komponenty, jako jsou agenti ochrany heslem a agenti zpětného zápisu hesla. Pokud dojde k ohrožení bezpečnosti vaší místní infrastruktury, můžou útočníci řídit počítače, na kterých se tito agenti nacházejí. I když nebude vaše cloudová infrastruktura ohrožená, nebudou vaše cloudové účty tyto součásti chránit před místním zabezpečením.

*  Místní účty synchronizované ze služby Active Directory jsou označeny tak, že ve službě Azure AD nikdy nevyprší platnost, a to na základě předpokladu, že tyto zásady pro místní hesla služby AD tuto skutečnost sníží. Pokud je vaše místní služba AD ohrožená a je potřeba zakázat synchronizaci se službou AD Connect, musíte nastavit možnost [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="provision-user-access-from-the-cloud"></a>Zřízení přístupu uživatele z cloudu

Zřizování se týká vytváření uživatelských účtů a skupin v aplikacích nebo zprostředkovatelích identity.

![Diagram architektury zřizování](media/protect-m365/protect-m365-provision.png)

* **Zřízení z aplikací cloudového HR do Azure AD:** To umožňuje izolaci místního zabezpečení, aniž by došlo k narušení chodu spojovacího cyklu z vašich aplikací cloudového HR do Azure AD.

* **Cloudové aplikace:** Pokud je to možné, nasaďte [aplikace Azure AD zřizování](../app-provisioning/user-provisioning.md) na rozdíl od místních řešení zřizování. Tím se ochrání některé aplikace SaaS před poškozením pomocí škodlivých uživatelských profilů v důsledku místních porušení. 

* **Externí identity:** Využijte [spolupráci Azure AD B2B](../external-identities/what-is-b2b.md).
    Tím se sníží závislost na místních účtech pro externí spolupráci s partnery, zákazníky a dodavateli. Pečlivě vyhodnoťte všechny přímé federace s jinými zprostředkovateli identity. Účty hostů B2B doporučujeme omezit následujícími způsoby.

   *  Omezte přístup hosta na skupiny procházení a další vlastnosti v adresáři. Pomocí nastavení externí spolupráce můžete omezit schopnost hosta číst skupiny, které nejsou členy. 

    *   Zablokuje přístup k Azure Portal. Je možné provádět vzácná nezbytná výjimka.  Vytvořte zásady podmíněného přístupu, které zahrnují všechny hosty a externí uživatele, a pak [implementujte zásadu pro blokování přístupu](../../role-based-access-control/conditional-access-azure-management.md). 

* **Odpojené doménové struktury:** Použijte [zřizování cloudu Azure AD](../cloud-provisioning/what-is-cloud-provisioning.md). To vám umožní připojit se k odpojeným doménovým strukturám a eliminovat nutnost navázat připojení mezi doménovými strukturami nebo vztahy důvěryhodnosti, což může rozšířit dopad místního porušení. * 
 
**Omezení a kompromisy:**

* Při použití ke zřízení hybridních účtů služba Azure AD z cloudových systémů HR spoléhá na místní synchronizaci, aby dokončila tok dat ze služby AD do Azure AD. V případě přerušení synchronizace nebudou v Azure AD k dispozici nové záznamy zaměstnanců.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Použití cloudových skupin pro spolupráci a přístup

Cloudové skupiny umožňují oddělit vaše spolupráce a přístup z vaší místní infrastruktury.

* **Spolupráce:** Používejte Microsoft 365 skupiny a Microsoft Teams pro moderní spolupráci. Vyřazení místních distribučních seznamů z provozu a [upgrade distribučních seznamů na Microsoft 365 skupiny v aplikaci Outlook](/office365/admin/manage/upgrade-distribution-lists?view=o365-worldwide).

* **Přístup:** K autorizaci přístupu k aplikacím v Azure AD použijte skupiny zabezpečení Azure AD nebo skupiny Microsoft 365.
* **Licencování Office 365:** Licencování na základě skupin můžete zřídit pro Office 365 s použitím pouze cloudových skupin. Tím se oddělí řízení členství ve skupině z místní infrastruktury.

Vlastníci skupin používaných pro přístup by měli být považováni za privilegované identity, aby nedocházelo k převzetí členství z místního zabezpečení.
Převzetí služeb při selhání zahrnuje přímou manipulaci s členstvím ve skupinách v místním prostředí nebo manipulaci s místními atributy, které mohou ovlivnit členství v dynamické skupině v Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Správa zařízení z cloudu


Pro bezpečnou správu zařízení používejte funkce Azure AD.

-   **Použití pracovních stanic s Windows 10:** [Nasaďte zařízení připojená k Azure AD](../devices/azureadjoin-plan.md) pomocí zásad MDM. Povolením automatického [pilotního projektu Windows](/mem/autopilot/windows-autopilot) můžete plně automatizovat prostředí pro zřizování.

    -   Vyřadí Windows 8.1 a starší počítače.

    -   Nesaďte počítače se serverovými operačními systémy jako pracovní stanice.

    -   Jako zdroj autority pro všechny úlohy správy zařízení použijte [Microsoft Intune](https://www.microsoft.com/en/microsoft-365/enterprise-mobility-security/microsoft-intune) .

-   [**Nasazení privilegovaných přístupových zařízení**](/security/compass/privileged-access-devices#device-roles-and-profiles) pro privilegovaný přístup ke správě Microsoft 365 a Azure AD.

 ## <a name="workloads-applications-and-resources"></a>Úlohy, aplikace a prostředky 

-   **Místní systémy jednotného přihlašování:** Vyřadíte všechny místní infrastruktury pro správu federace a Web Access a nakonfigurujete aplikace tak, aby používaly Azure AD.  

-   **SaaS a obchodní aplikace podporující moderní protokoly ověřování:** [k jednotnému přihlašování použijte Azure AD](../manage-apps/what-is-single-sign-on.md). Víc aplikací, které nakonfigurujete pro použití Azure AD k ověřování, je menší riziko v případě místního ohrožení zabezpečení.


* **Starší verze aplikací** 

   * Ověřování, autorizaci a vzdálený přístup k starším aplikacím, které nepodporují moderní ověřování, se dají povolit prostřednictvím služby [Azure proxy aplikací služby AD](../manage-apps/application-proxy.md). Dá se taky povolit prostřednictvím sítě nebo řešení pro doručování aplikací s využitím  [Integrace partnerů pro zabezpečení hybridního přístupu](../manage-apps/secure-hybrid-access.md).   

   * Vyberte dodavatele sítě VPN, který podporuje moderní ověřování, a integrujte jeho ověřování s Azure AD. V případě ohrožení bezpečnosti anon můžete pomocí Azure AD zakázat nebo zablokovat přístup zakázáním sítě VPN.

*  **Aplikace a servery úloh**

   * Aplikace nebo prostředky, které vyžadují servery, se dají migrovat do Azure IaaS a používat [Azure AD Domain Services](../../active-directory-domain-services/overview.md) (Azure služba AD DS) k odvýšení vztahu důvěryhodnosti a závislostí na místní službě AD. Aby bylo možné toto oddělení využít, virtuální sítě používané pro Azure služba AD DS by neměly mít připojení k podnikovým sítím.

   * Postupujte podle pokynů pro [vrstvení přihlašovacích údajů](/security/compass/privileged-access-access-model#ADATM_BM). Aplikační servery se typicky považují za prostředky vrstvy 1.

 ## <a name="conditional-access-policies"></a>Zásady podmíněného přístupu

Pomocí podmíněného přístupu Azure AD můžete interpretovat signály a na základě nich provádět rozhodnutí o ověřování. Další informace najdete v tématu [plán nasazení podmíněného přístupu.](../conditional-access/plan-conditional-access.md)

* [Starší protokoly ověřování](../fundamentals/auth-sync-overview.md): Pokud je to možné, používejte podmíněný přístup k [blokování starších protokolů ověřování](../conditional-access/howto-conditional-access-policy-block-legacy.md) . Kromě toho zakažte starší protokoly ověřování na úrovni aplikace pomocí konfigurace specifické pro aplikaci.

   * Podívejte se na konkrétní podrobnosti pro [Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) a [SharePoint Online](/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps).

* Implementujte doporučené [Konfigurace identit a přístupu k zařízením.](/microsoft-365/security/office-365-security/identity-access-policies?view=o365-worldwide)

* Pokud používáte verzi služby Azure AD, která nezahrnuje podmíněný přístup, ujistěte se, že používáte [výchozí nastavení zabezpečení Azure AD](../fundamentals/concept-fundamentals-security-defaults.md).

   * Další informace o licencování funkcí služby Azure AD najdete v tématu [Cenová příručka Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitoring"></a>Sledování 

Jakmile nakonfigurujete prostředí pro ochranu Microsoft 365 před místním zabezpečením, [proaktivně monitorujte](../reports-monitoring/overview-monitoring.md) prostředí.
### <a name="scenarios-to-monitor"></a>Scénáře, které se mají monitorovat

Kromě jakýchkoli scénářů, které jsou specifické pro vaši organizaci, můžete monitorovat následující klíčové scénáře. Například byste měli aktivně monitorovat přístup k důležitým firemním aplikacím a prostředkům.

* **Podezřelá aktivita**: všechny [rizikové události Azure AD](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) by se měly monitorovat na podezřelé aktivity. [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) je nativně integrována s Azure Security Center.

   * Definujte síťová [umístění s názvem](../reports-monitoring/quickstart-configure-named-locations.md) , abyste se vyhnuli detekci šumu na signálech založených na poloze. 
*  **Výstrahy analýzy chování entit uživatele (UEBA)** Využijte UEBA k získání přehledů o detekci anomálií.
   * MCAS (Microsoft Cloud App Discovery) poskytuje [UEBA v cloudu](/cloud-app-security/tutorial-ueba).

   * Můžete [integrovat místní UEBA z Azure ATP](/defender-for-identity/install-step2). MCAS čte signály z Azure AD Identity Protection. 

* **Činnost účtů pro nouzový přístup**: je třeba monitorovat jakýkoli přístup pomocí [účtů pro nouzový přístup](../roles/security-emergency-access.md) a vytvořit výstrahy pro šetření. Toto monitorování musí zahrnovat: 

   * Přihlášení. 

   * Správa přihlašovacích údajů. 

   * Jakékoli aktualizace členství ve skupinách. 

   *    Přiřazení aplikací. 
* **Aktivita privilegované role**: Nakonfigurujte a zkontrolujte výstrahy zabezpečení [generované službou Azure AD PIM](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts).
    Monitorování přímého přiřazení privilegovaných rolí mimo PIM tím, že generuje výstrahy vždy, když se uživatel přiřadí přímo.
* **Konfigurace tenanta Azure AD**: jakákoli změna v konfiguracích na úrovni tenanta by měla generovat výstrahy v systému. Mezi ně patří mimo jiné
  *  Aktualizace vlastních domén  

  * Azure AD B2B povolí nebo zablokuje změny seznamu.
  * Povolení zprostředkovatelů identity Azure AD B2B (SAML zprostředkovatelů identity prostřednictvím přímých federačních nebo sociálních přihlášení).  
  * Změny zásad podmíněného přístupu nebo zásad rizik 

* **Aplikační a instanční objekty**:
   * Nové aplikace nebo instanční objekty, které mohou vyžadovat zásady podmíněného přístupu. 

   * Další přihlašovací údaje přidané do instančních objektů.
   * Aktivita souhlasu aplikace 

* **Vlastní role**:
   * Aktualizuje definice vlastních rolí. 

   * Nové vlastní role se vytvořily. 

### <a name="log-management"></a>Správa protokolů

Definujte protokol úložiště a strategii uchovávání protokolů, návrh a implementaci, abyste usnadnili konzistentní sadu nástrojů, jako je například SIEM systémy, jako je Azure Sentinel, běžné dotazy a šetření a forenzní playbooky.

* **Protokoly Azure AD** Protokoly a signály ingestují tyto osvědčené postupy, včetně nastavení diagnostiky, uchovávání protokolů a ingestování SIEM. Strategie protokolu musí zahrnovat následující protokoly služby Azure AD:
   * Aktivita přihlášení 

   * Protokoly auditu 

   * Rizikové události 

Azure AD poskytuje [integraci Azure monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md) pro protokol aktivit přihlašování a protokoly auditu. Rizikové události se dají ingestovat prostřednictvím [rozhraní Microsoft Graph API](/graph/api/resources/identityriskevent). Protokoly [Azure AD můžete streamovat do protokolů Azure monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Protokoly zabezpečení operačních systémů hybridní infrastruktury** Všechny protokoly operačního systému infrastruktury hybridní identity by měly být archivovány a pečlivě monitorovány jako <br>Systém vrstvy 0, s ohledem na dopady oblasti Surface. Sem patří: 

   *  Azure AD Connect . Aby bylo možné monitorovat synchronizaci identity, je nutné nasadit [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md) .

   *  Agenti proxy aplikací 


   * Agenti se zpětným zápisem hesla 

   * Počítače brány ochrany heslem  

   * Server NPS s rozšířením RADIUS pro Azure MFA 

## <a name="next-steps"></a>Další kroky
* [Zajištění odolnosti správy přístupu a identit s využitím Azure AD](resilience-overview.md)

* [Zabezpečení externího přístupu k prostředkům](secure-external-access-resources.md) 
* [Integrace všech svých aplikací s Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)