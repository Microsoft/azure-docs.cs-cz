---
title: Osvědčené postupy pro zabezpečení přístupu pro správu ve službě Azure AD | Dokumentace Microsoftu
description: Zajistěte, aby byly zabezpečené účty správců přístup a správce vaší organizace. Pro systémové architekty a IT profesionály, kteří konfigurují služby Azure AD Azure a Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.date: 06/25/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer, MarkMorow
ms.openlocfilehash: b52a447bf643510528e1bd8c6d473bf5d77731fe
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057188"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Zabezpečení privilegovaného přístupu pro hybridní a cloudové nasazení ve službě Azure AD

Zabezpečení většiny nebo veškerých podnikových prostředků v moderních organizacích závisí na integritě privilegovaných účtů, které spravují a řídí systémy IT. Útočníky včetně internetoví útočníci často cílí účtům pro správu a další prvky privilegovaného přístupu, pokusí se rychle získat přístup k citlivým datům a systémům pomocí útoků využívajících krádež přihlašovacích údajů. Pro cloud services, ochrany před únikem informací a odpovědi jsou společné odpovědnost poskytovatel cloudových služeb a zákazníka. Další informace o nejnovějších hrozbách do koncových bodů a cloud, najdete v článku [Microsoft Security Intelligence Report](https://www.microsoft.com/security/sir/default.aspx). Tento článek pomáhají při vývoji plánu směrem k uzavření mezery mezi vašich aktuálních plánů a pokyny popsané.

> [!NOTE] 
> Společnost Microsoft se zavazuje poskytovat nejvyšší úrovně důvěryhodnosti, transparentnosti, shoda se standardy a předpisy. Další informace o jak globální reakce na incidenty týmu Microsoftu snižuje důsledky útoků proti cloudové služby a jak zabezpečení je integrováno do produktů Microsoft business a cloudovým službám v [Microsoft Trust Center - zabezpečení](https://www.microsoft.com/trustcenter/security)a Microsoft dodržování předpisů cílí na [Microsoft Trust Center - dodržování předpisů](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
Pro většinu organizací závisí zabezpečení podnikových prostředků na integritě privilegovaných účtů, které spravují a řídí systémy IT. Internetoví útočníci se zaměřují na privilegovaný přístup k systémům infrastruktury (třeba Active Directory a Azure Active Directory) získat přístup k citlivým datům vaší organizace. 

Tradiční přístupy, které se soustředí na zabezpečení body vstupu a výstupu sítě jako primárního obvodu zabezpečení jsou míň efektivní z důvodu zvýšení využívání aplikací SaaS a osobní zařízení na Internetu. Přirozenou náhradou obvodu zabezpečení sítě ve složitém moderním podniku se ovládací prvky ověřování a autorizace ve vrstvě identity organizace. 

Privilegované účty správců jsou efektivní kontrolu nad tento nový "zabezpečovací obvod." Je velmi důležité pro ochranu privilegovaného přístupu, bez ohledu na to, jestli je prostředí místní, Cloudová nebo hybridní místní a cloudové služby hostované. Ochrana přístupu správců před odhodlanými vyžaduje, abyste se úplný a pečlivý přístup k izolování systémy vaší organizace před riziky. 

Zabezpečení privilegovaného přístupu vyžaduje změny
* Procesy a postupy pro správu, správy znalostní báze
* Technické komponenty jako obrana hostitelů, ochrana účtů a správu identit

Tento dokument se zaměřuje především na vytváření plánu zabezpečení identit a přístupu, která se spravují nebo nahlášené v Azure AD, Microsoft Azure, Office 365 a dalších cloudových služeb. Pro organizace, které mají místní účty správců, najdete pokyny pro místní a hybridní privilegovaný přístup spravovat z Active Directory na [zabezpečení privilegovaného přístupu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Pokyny v tomto článku označuje především funkce služby Azure Active Directory, které jsou součástí plánů Azure Active Directory Premium P1 a P2. Azure Active Directory Premium P2 je součástí sady EMS E5 a Microsoft 365 E5 suite. Tyto pokyny předpokládají, že vaše organizace už má zakoupené pro vaši uživatelé licence Azure AD Premium P2. Pokud tyto licence, některé z uvedených nemusí platit pro vaši organizaci. Také v celém tomto článku se termín globálního správce (nebo globální správce) je synonymem "Správce společnosti" nebo "správce klienta."

## <a name="develop-a-roadmap"></a>Vytvořit plán vývoje 

Společnost Microsoft doporučuje vyvíjet a postupujte podle plánu zabezpečení privilegovaného přístupu k proti kybernetických útoků. Vždy můžete upravit vás plán tak, aby vyhovovaly vašim stávajícím funkcím a konkrétní požadavky v rámci vaší organizace. Každá fáze plán by měl zvýšit náklady a ztížit pro nežádoucím osobám útoky na privilegované přístupy pro místní, Cloudová a hybridní prostředky. Společnost Microsoft doporučuje následující čtyři fáze plán: to doporučené plán plány nejúčinnějším a nejrychlejší implementace nejprve vybírána na základě zkušeností společnosti Microsoft s implementací internetového útoku incidentů a odpovědi. Časové osy pro tento plán jsou jen přibližné.

![Fáze plán pomocí časové osy](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Fáze 1 (24 nebo 48 hodin): kritické položky, které doporučujeme provést hned

* Fáze 2 (2 – 4 týdny): zmírnit u nejčastěji používaných technik útoků

* Fáze 3 (1-3 měsíců): přehled sestavení a sestavení úplná kontrola aktivity správců

* Fáze 4 (šest měsíců a novější): pokračování ve vytváření ochrany pro další posílení zabezpečení platformy pro zabezpečení

Tento plán framework je navrženo tak, aby maximalizoval využití technologií Microsoft, už nasazené. Můžete také využít výhod klíčových aktuálních a připravovaných bezpečnostních technologií a integrace zabezpečení nástrojů od jiných dodavatelů, že jste již nasadili nebo zvažuje nasazení. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Fáze 1: Kritické položky, které doporučujeme provést hned

![Fáze 1](./media/directory-admin-roles-secure/stage-one.png)

Fáze 1 plánu se zaměřuje na důležité úkoly, které jsou rychlé a snadno se implementuje. Doporučujeme, abyste provedli tyto několik položek hned během prvních 24 nebo 48 hodin k zajištění základní úroveň zabezpečení privilegovaného přístupu. Tato fáze se plánuje zabezpečené privilegovaného přístupu zahrnuje následující akce:

### <a name="general-preparation"></a>Obecné přípravy

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Zapnout Azure AD Privileged Identity Management

Pokud jste již v Azure AD Privileged Identity Management (PIM), to je v vašeho produkčního tenanta. Po zapnutí Privileged Identity Management, obdržíte oznámení e-mailové zprávy pro privilegovaný přístup změny role. Tato oznámení poskytovat včasné upozornění při přidání dalších uživatelů pro vysoce privilegované role ve vašem adresáři.

Azure AD Privileged Identity Management je součástí Azure AD Premium P2 nebo EMS E5. Tato řešení vám umožní chránit přístup k aplikacím a prostředkům napříč místním prostředím a do cloudu. Pokud dosud nemáte Azure AD Premium P2 nebo EMS E5 a chcete vyhodnotit, více možností odkazovat v rámci tohoto plánu, zaregistrovat [Enterprise Mobility + Security bezplatná 90denní zkušební verze](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Použijte tyto zkušební licence Azure AD Privileged Identity Management a Azure AD Identity Protection monitorování pomocí služby Azure AD pokročilé vytváření sestav zabezpečení, auditování a výstrahy.

Poté co jste zapnuli Azure AD Privileged Identity Management:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) pomocí účtu, který má oprávnění globálního správce vašeho produkčního tenanta.

2. Pokud chcete vybrat tenanta, ve které chcete používat Privileged Identity Management, vyberte své uživatelské jméno v pravém horním rohu webu Azure portal.

3. Vyberte **všechny služby** a filtrovat seznam pro **Azure AD Privileged Identity Management**.

4. Otevřete z Privileged Identity Management **všechny služby** seznamu a její Připnutí na řídicí panel.

První, kdo k použití ve vašem tenantovi Azure AD Privileged Identity Management se automaticky přiřadí **správce zabezpečení** a **správce privilegovaných rolí** role v tenantovi. Pouze správci privilegovaných rolí můžou Spravovat přiřazení rolí adresáře Azure AD uživatelů. Kromě toho po přidání Azure AD Privileged Identity Management, jsou uvedeny zabezpečení průvodce, který vás provede počátečním zjišťováním a přiřazováním prostředí. Ukončete průvodce bez jakýchkoliv dalších změn v tuto chvíli. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identifikujte a kategorizaci účty, které jsou ve vysoce privilegované role 

Po zapnutí Azure AD Privileged Identity Management, můžete zobrazte uživatele, kteří jsou v adresáři role Globální správce, správce privilegovaných rolí, správce Exchange Online a správce Sharepointu Online. Pokud nemáte Azure AD PIM ve vašem tenantovi, můžete použít [rozhraní API prostředí PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Spustit s rolí globálního správce, protože tato role je obecný: uživatel, který je přiřazena tato role správce má stejná oprávnění ve všech cloudových službách, pro které má vaše organizace předplacené, bez ohledu na to, zda se přiřadil tuto roli na portálu Office 365 , Azure portal, nebo pomocí modulu Azure AD pro Microsoft PowerShell. 

Odeberte všechny účty, které už nejsou potřeba v těchto rolích a kategorizaci zbývajících účtů, která jsou přiřazená rolím správce:

* Přiřazeny správcům a lze také použít pro účely bez oprávnění správce (například osobní e-mailu)
* Jednotlivě přiřazené správcům a určené pouze pro účely správy
* Sdílet mezi více uživatelů
* Pro scénáře skleněné nouzový přístup
* Pro automatizované skripty
* Pro externí uživatele

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definovat aspoň dva účty pro nouzový přístup 

Ujistěte se, že není dostat do situace, kdy se může být neúmyslně zablokována správu vašeho tenanta Azure AD z důvodu nemožnost přihlášení nebo aktivace účtu existující konkrétního uživatele jako správce. Například pokud organizace je Federovaná ke zprostředkovateli identity v místním, tohoto zprostředkovatele identity možná není k dispozici, uživatelům se přihlásit s místními. Zmírnit dopad náhodného chybějící přístup pro správu uložením dva nebo více účtů pro nouzový přístup ve vašem tenantovi.

Účty pro nouzový přístup pomáhají organizacím omezit privilegovaný přístup v rámci stávajícího prostředí Azure Active Directory. Tyto účty jsou vysoce privilegovaní a nejsou přiřazeny jednotlivým uživatelům. Účty pro nouzový přístup jsou omezené na nouzové "pohotovostní" scénářích, kdy nejde použít normální účty pro správu. Organizace musí zajistit za účelem řízení a snížení využití nouzový účtu jenom tento čas, pro které je nezbytné. 

Posouzení účtů, které jsou přiřazené nebo oprávněné pro roli globálního správce. Pokud jste nenalezli žádný účtů pomocí cloudového *. onmicrosoft.com domény (určený pro nouzový přístup "pohotovostní"), vytvořte je. Další informace najdete v tématu [Správa účtů pro nouzový přístup pro správu ve službě Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Zapnout ověřování službou Multi-Factor Authentication a všechny ostatní účty správců jiné než federované jednoho uživatele s vysokou úrovní oprávnění registrace 

Azure Multi-Factor Authentication (MFA) vyžadují při přihlášení pro všechny jednotlivé uživatele, kteří jsou trvale přiřazené na jeden nebo více rolí správce Azure AD: Globální správce, správce privilegovaných rolí, správce Exchange Online a SharePoint Správce služby online. Použití v příručce k povolení [Multi-Factor Authentication (MFA) pro vaše účty správců](../authentication/howto-mfa-userstates.md) a ujistěte se, že tito uživatelé zaregistrovali na [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup). Další informace najdete v části kroky 2 a 3 průvodce [chránit přístup k datům a službám Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Fáze 2: Zmírnit u nejčastěji používaných technik útoků

![Fáze 2](./media/directory-admin-roles-secure/stage-two.png)

Fáze 2 plánu je zaměřené na minimalizaci nejpravděpodobnějších u nejčastěji používaných technik útoků krádeže přihlašovacích údajů a zneužití a má implementovat přibližně během 2 – 4 týdnů. Tato fáze se plánuje zabezpečené privilegovaného přístupu zahrnuje následující akce.

### <a name="general-preparation"></a>Obecné přípravy

#### <a name="conduct-a-inventory-of-services-owners-and-admins"></a>Chování inventáře služeb, vlastníci a správci

S nárůstem přenést your-own-device (BYOD) a práci z domovské zásady a růstu bezdrátové připojení ve firmách je velmi důležité, je sledovat, na který se připojuje k vaší síti. Efektivní zabezpečení auditu často zjistí zařízení, aplikace a programy spuštěné ve vaší síti, která nepodporuje IT a proto potenciálně není zabezpečená. Další informace najdete v tématu [Přehled monitorování a správa Azure zabezpečení](../../security/security-management-and-monitoring-overview.md). Ujistěte se, že složku zahrnujete všechny z následujících úloh v procesu inventáře. 

* Identifikujte uživatele, kteří mají správní role a služby, kde můžete spravovat.
* Pomocí Azure AD PIM můžete zjistit, kteří uživatelé ve vaší organizaci mají přístup správce ke službě Azure AD, včetně dalších rolí nad rámec těch uvedené ve fázi 1.
* Nad rámec role definované ve službě Azure AD Office 365 obsahuje sadu rolí správců, které můžete přiřadit uživatelům ve vaší organizaci. Každá role správce se mapuje na běžné obchodní funkce a poskytuje lidem ve vaší organizaci oprávnění k provádění konkrétních úkolů v Centru pro správu Office 365. Pomocí centra pro správu Office a zjistěte, kteří uživatelé ve vaší organizaci mají přístup správce k Office 365, včetně rolí není spravovaná ve službě Azure AD. Další informace najdete v tématu [role správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) a [osvědčené postupy zabezpečení pro Office 365](https://support.office.com/article/Security-best-practices-for-Office-365-9295e396-e53d-49b9-ae9b-0b5828cdedc3).
* Inventarizaci v dalších službách, které vaše organizace využívá, jako je například Azure, Intune nebo Dynamics 365.
* Zkontrolujte, že vaše účty správců (počet účtů, které se používají pro účely správy, ne jenom každodenní účtů uživatelů) mají pracovní e-mailové adresy, které jsou připojené k nim a jste se zaregistrovali pro Azure MFA nebo používat MFA v místním.
* Požádejte uživatele pro jejich obchodní odůvodnění přístup pro správu.
* Odeberte přístup správce pro tyto osoby a služby, které ho nepotřebujete.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifikujte účty Microsoft v rolích pro správu, které je potřeba přepnout na pracovní nebo školní účty 

V některých případech počáteční globální správci pro organizaci použít své stávající přihlašovací údaje účtu Microsoft při začali používat Azure AD. Tyto účty Microsoft se nahrazuje samostatné účty založené na cloudu nebo synchronizovaná. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Zajištění samostatné uživatelské účty a předávání pro účty globálních správců e-mailů 

Účty globálních správců by neměl mít osobní e-mailové adresy, protože osobní e-mailové účty jsou pravidelně phished internetoví útočníci. Chcete pomoci oddělit Internetová rizika (útoky typu phishing, procházení neúmyslnému webu) od oprávnění správce, vytvořte vyhrazené účty pro každého uživatele s oprávněními správce. 

Pokud jste tak již neučinili, vytvořte samostatné účty pro uživatele k provádění úloh globálního správce, abyste měli jistotu, nikoli neúmyslně otevření e-mailů nebo spouštět programy přidružené k jejich účty správců. Ujistěte se, že tyto účty mají předané do poštovní schránky pracovní e-mailu.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Ujistěte se, že jste nedávno změnili hesla účtů pro správu

Ujistěte se, že všichni uživatelé mají přihlášení do svých účtů správců a změnit svá hesla nejméně jednou za posledních 90 dnů. Také se ujistěte, že své heslo nedávno změnili, některé sdílené účty kteří více víte, že heslo jste využili.

#### <a name="turn-on-password-hash-synchronization"></a>Zapnutí synchronizace hodnot hash hesel

Synchronizace hodnot hash hesel je funkce používá k synchronizaci hodnoty hash hodnot hash hesel uživatelů z místní instance Active Directory do Azure založené na cloudu instancí AD. I že pokud se rozhodnete federace pomocí služby Active Directory Federation Services (AD FS) nebo u jiných poskytovatelů identit, můžete volitelně nastavit synchronizaci hodnot hash hesel jako záložní v případě vaší místní infrastruktury, jako je AD nebo servery služby AD FS selhat nebo se stane dočasně nedostupné. To umožňuje uživatelům umožní přihlásit ke službě s použitím stejné heslo, které používají pro přihlášení k jejich místní AD instance. Také umožňuje k detekci zneužití přihlašovacích údajů porovnáním hodnoty hash těchto hesel s hesly známé došlo k narušení, pokud uživatel díky jejich stejnou e-mailovou adresu a heslo v jiné službě, není připojen ke službě Azure AD Identity Protection.  Další informace najdete v tématu [implementace synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect](./../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Vyžadovat vícefaktorové ověřování (MFA) u uživatelů v všech privilegovaných rolích, jakož i odhalených uživatelů

Azure AD doporučuje vyžadovat vícefaktorové ověřování (MFA) pro všechny uživatele, včetně správců a všichni uživatelé, kteří by mít zásadní vliv, pokud došlo k ohrožení jejich účet (třeba finanční vedoucí pracovníci pověření ochranou). Tím se snižuje riziko útoků kvůli ohrožení zabezpečení hesla.

Zapněte:

* [MFA pro účty vysoce vystavení](../authentication/multi-factor-authentication-security-best-practices.md) například účty pro vedoucí pracovníci pověření ochranou výkonný v organizaci 
* [Vícefaktorové ověřování pro každý účet správce přidružený k jednotlivého uživatele](../authentication/howto-mfa-userstates.md) pro druhým připojeným aplikacím SaaS 
* Vícefaktorové ověřování pro všechny správce pro aplikace SaaS společnosti Microsoft, včetně správců v rolích spravované v Exchangi Online a na portálu Office

Pokud používáte Windows Hello pro firmy, požadavek na vícefaktorové ověřování můžete splnit pomocí sign Windows Hello v prostředí. Další informace najdete v tématu [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Konfigurovat Identity Protection 

Služba Azure AD Identity Protection je monitorování na základě algoritmu a vytváření sestav nástroje, který můžete použít k detekci potenciálních ohroženích zabezpečení dopadem na identity vaší organizace. Můžete nakonfigurovat automatické odpovědi na tyto detekované podezřelé aktivity a přijmout vhodná opatření k jejich řešení. Další informace najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Získat hodnotíte zabezpečení Office 365 (Pokud používáte Office 365)

Zabezpečte skóre zjistí, jaké služby Office 365 používáte (jako je OneDrive, SharePoint a Exchange) zobrazuje vaše nastavení a aktivity a porovná je se základní Radou Microsoft. Zobrazí se skóre na základě toho, jak zarovnané jste se s osvědčenými postupy zabezpečení. Každý, kdo má oprávnění správce (globální správce nebo správce vlastní roli) pro předplatné Office 365 Business Premium nebo Enterprise přístup k zabezpečené skóre v [ https://securescore.office.com ](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Projděte si doprovodné materiály pro zabezpečení a dodržování předpisů Office 365 (Pokud používáte Office 365)

[Plánování zabezpečení a dodržování předpisů](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) nastiňuje metody pro zákazníky s Office 365 jak konfiguraci aplikací Office 365 a využívat další možnosti EMS. Pak zkontrolujte kroky 3 až 6 o tom, jak [chránit přístup k datům a službám Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) a příručky k tom, jak [monitorování zabezpečení a dodržování předpisů v Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).


#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Konfigurace monitorování aktivit Office 365 (Pokud používáte Office 365)

Můžete sledovat, jak lidé ve vaší organizaci používáte služby Office 365, můžete k identifikaci uživatelů, kteří mají účet správce a kdo nemusí potřebovat Office 365 přístup z důvodu není přihlásíte těchto portálů. Další informace najdete v tématu [sestavy aktivit v Centru pro správu Office 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Vytvořit incident/nouzovou odpovědi plán vlastníky

Efektivní provádění reakcí na incidenty je složitý proces. Proto vytváří úspěšné reakce na incidenty funkce vyžaduje podstatné plánování a prostředky. Je důležité, abyste neustále monitorovat kybernetických útoků a postupy pro stanovení priority zpracování incidentů. Efektivní metody shromažďování, analýzu a generování sestav dat jsou důležité vytvářet vztahy a navázat komunikaci s jinými skupinami interní a plánování vlastníky. Další informace najdete v tématu [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Zabezpečené místní privilegované účty pro správu, pokud ještě není Hotovo

Pokud váš tenant Azure Active Directory synchronizované s místní služby Active Directory, postupujte podle pokynů v [plánu zabezpečení privilegovaného přístupu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): fáze 1. To zahrnuje vytvoření správce samostatné účty pro uživatele, kteří potřebují k provádění úloh správy v místním nasazení pracovních stanic s privilegovaným přístupem pro správce Active Directory a vytváření jedinečná hesla místních správců pro pracovní stanice a servery.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Další kroky pro organizace spravovat přístup k Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Proveďte inventarizaci předplatná

Zjistěte, jaká předplatná ve vaší organizaci, které jsou hostiteli aplikace v produkčním prostředí pomocí webu Enterprise portal a na webu Azure portal. 

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Odebrat z role správce účtů Microsoft

Účty Microsoft z jiných aplikací, jako je Xbox Live a aplikace Outlook by neměl používat jako účty správce pro předplatná organizace. Odebrat správce stav ze všech účtů Microsoft a nahraďte služby Active Directory (například chris@contoso.com) pracovní nebo školní účty.

#### <a name="monitor-azure-activity"></a>Monitorování aktivit v Azure

Protokol aktivit Azure poskytuje historii událostí na úrovni předplatného v Azure. Nabízí informace o který vytvořili, aktualizovat a odstraňovat prostředky a kdy došlo k těmto událostem. Další informace najdete v tématu [Audit a přijímání oznámení o důležitých akcích ve vašem předplatném Azure](../../monitoring-and-diagnostics/monitor-quick-audit-notify-action-in-subscription.md).


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Další kroky pro organizace, správě přístupu k jiným cloudovým aplikacím pomocí služby Azure AD 

#### <a name="configure-conditional-access-policies"></a>Nakonfigurujte zásady podmíněného přístupu

Příprava zásad podmíněného přístupu pro místní a cloudové aplikace. Pokud máte zařízení připojená k pracovišti uživatelů, získejte další informace z [nastavení místního podmíněného přístupu pomocí registrace zařízení služby Azure Active Directory](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Fáze 3: Vytvoření viditelnost a úplnou kontrolu nad aktivita správce

![Fáze 3](./media/directory-admin-roles-secure/stage-three.png)

Fáze 3 navazuje na zmírnění rizik v fáze 2 a je určený má implementovat přibližně 1-3 měsíců. Tato fáze se plánuje zabezpečené privilegovaného přístupu zahrnuje následující součásti.

### <a name="general-preparation"></a>Obecné přípravy

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Dokončení kontroly přístupu uživatelů v rolích správce

Více firemní uživatelé získáváme privilegovaný přístup prostřednictvím cloudových služeb, což může vést k rostoucí nespravované platformy. Zahrnuti jsou uživatelé stále globální správci pro Office 365, správci předplatného Azure a uživatelé, kteří mají přístup správce k virtuálním počítačům nebo přes aplikace SaaS. Místo toho organizace musí mít všichni zaměstnanci, hlavně správcům, zpracování každodenních obchodních transakcí jako uživatelé bez oprávnění umožňovala zvlášť a trvat jenom na práva správce podle potřeby. Vzhledem k tomu, že počet uživatelé ve správcovských rolích mohou zvětšil od počátečního přijetí, zkontroluje úplný přístup k identifikaci a potvrzení každý uživatel, který opravňuje k aktivaci oprávnění správce. 

Udělejte toto:

* Určete, kteří jsou správci, povolit na vyžádání, přístup správce v čase a kontrolních mechanismů pro zabezpečení na základě rolí Azure AD.
* Převést uživatelé, kteří mají žádné vymazat odůvodnění pro správu privilegovaného přístupu k jinou roli (Pokud žádná oprávněné role, odeberte je).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Pokračovat zavedení silnějšího ověřování pro všechny uživatele 

Vyžadovat C-suite vedení, vedoucí vysoké úrovně, kritické IT a zabezpečení pracovníků a jiných vysoce vystavené uživatelům moderní, silné ověřování, jako je Azure MFA nebo Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Použít vyhrazené pracovní stanice pro správu Azure AD

Útočníci se můžou snažit k zacílení privilegovaných účtů pro získání přístupu k datům a systémům v organizaci, můžete narušit integritu a pravosti data prostřednictvím škodlivého kódu, který mění logiku programu nebo snoops zadávání přihlašovacích údajů správce. Pracovní stanice s privilegovaným přístupem (Paw) poskytují vyhrazený operační systém pro citlivé úlohy, které je chráněné před útoky z Internetu a jinými vektory hrozeb. Oddělení citlivých úloh a účtů z každodenní použití pracovních stanic a zařízení poskytuje velmi silnou ochranu před útoky typu phishing, aplikace a ohrožení zabezpečení operačního systému, různými útoky se zneužitím zosobnění a útoků využívajících krádež přihlašovacích údajů, jako je stisknutí kláves protokolování, Pass-the-Hash a Pass-The-Ticket. Nasazení pracovních stanic s privilegovaným přístupem, můžete snížit riziko, že správci zadání přihlašovacích údajů správce s výjimkou na desktopové prostředí, které byly Posílená. Další informace najdete v tématu [pracovních stanic s privilegovaným přístupem](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Projděte si doporučení Národního institutu standardů a technologie pro zpracování incidentů 

Národního institutu standardů a technologie (NIST) obsahuje pokyny pro zpracování incidentů, zejména pro analýzu dat týkajících se incidentu a určení odpovídající odpověď na každý incident. Další informace najdete v tématu [The (NIST) počítači Security Incident Handling Guide (SP 800-61, revize 2)](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementaci Privileged Identity Management (PIM) pro JIT pro další role správců

Azure Active Directory, použijte [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) funkce. Časově omezený aktivace privilegované role funguje tak, že umožňuje:

* Aktivovat správce oprávnění k provedení určitého úkolu
* Vynucení vícefaktorového ověřování během procesu aktivace
* Používání výstrah k informování o změnách out-of-band správci
* Povolit uživatelům určitá privilegia, uchovávat předem nakonfigurovaného množství času
* Povolit správce zabezpečení ke zjišťování všech privilegované identity, zobrazit sestavy auditování a vytvoření kontroly přístupu k identifikaci všech uživatelů, kteří vás opravňuje k aktivaci oprávnění správce

Pokud už používáte Azure AD Privileged Identity Management, upravte časové rámce pro časově vázaná oprávnění podle potřeby (například časová období údržby).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Určení vystavení založené na heslech přihlášení protokoly (Pokud se používá Exchange Online)

V minulosti protokoly předpokládá, že byly vloženy kombinace uživatelského jména a hesla v zařízení, e-mailové účty, telefony a tak dále. Ale nyní s riziky pro kybernetických útoků v cloudu, doporučujeme vám identifikovat každý potenciální uživatele, který, jejichž ohrožení zabezpečení přihlašovacích údajů se může mít katastrofální v organizaci a vyloučit tomu nebudou moct přihlásit k e-mailu pomocí uživatelského jména / heslo implementací požadavky na silné ověřování a podmíněného přístupu. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Proveďte posouzení revize rolí pro role služeb Office 365 (Pokud používáte Office 365)

Vyhodnoťte, jestli jsou všichni uživatelé admins ve správné role (odstranit a znovu přiřadíte podle tohoto vyhodnocení).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Zkontrolovat přístup správy incidentů zabezpečení používaný v Office 365 a porovnat s vaší vlastní organizaci

Můžete si stáhnout tuto sestavu z [Správa incidentů zabezpečení v Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Pokračovat k zabezpečení privilegovaného jako místní účty pro správu

Pokud služby Azure Active Directory je připojená k místní službě Active Directory, postupujte podle pokynů v [plánu zabezpečení privilegovaného přístupu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): fáze 2. To zahrnuje nasazení pracovních stanic s privilegovaným přístupem pro všechny správce, vyžaduje vícefaktorové ověřování, pomocí právě dostatečně správce pro údržbu řadičů domény, což snižuje prostor pro útoky domén, nasazení ATA pro detekce útoku.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Další kroky pro organizace spravovat přístup k Azure

#### <a name="establish-integrated-monitoring"></a>Vytvoření, integrované monitorování

[Azure Security Center](../../security-center/security-center-intro.md) poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, které může jinak nevšimli a spolupracuje s řadou zabezpečení řešení.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventarizovat privilegovaných účtů v rámci hostované virtuální počítače

Ve většině případů není nutné poskytnout neomezená oprávnění uživatele pro všechny vaše předplatná Azure nebo prostředky. Role správce Azure AD můžete oddělit úlohy v rámci vaší organizace a poskytnout pouze takovou úroveň přístupu pro uživatele, kteří potřebují k provádění konkrétních úloh. Například použijte role správce Azure AD, aby jeden správce spravovat pouze virtuální počítače v předplatném, zatímco jiné můžete spravovat databáze SQL v rámci stejného předplatného. Další informace najdete v tématu [Začínáme s řízením přístupu na základě rolí na portálu Azure portal](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementace PIM pro role správce Azure AD

Spravovat, řídit a monitorovat přístup k prostředkům Azure pomocí rolí správce Azure AD Privileged identity Management. Použití PIM chrání privilegované účty před kybernetickými útoky snížení doby expozice oprávnění a zvyšuje svůj vhled do jejich používání prostřednictvím sestavy a upozornění. Další informace najdete v tématu [RBAC spravovat přístup k prostředkům Azure s Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Integrace protokolů Azure používat k odesílání relevantní protokolů Azure do systémů SIEM 

Integrace protokolů Azure umožňuje integrovat nezpracovaných protokolů z vašich prostředků Azure do stávající informace o zabezpečení vaší organizace a systémů správy událostí (SIEM). [Integrace protokolů Azure](../../security/security-azure-log-integration-overview.md) shromažďuje události Windows z protokolů prohlížeče událostí Windows a prostředků Azure z protokolů aktivit Azure, Azure Security Center oznámení a protokoly diagnostiky Azure. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Další kroky pro organizace, správě přístupu k jiným cloudovým aplikacím pomocí služby Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementace zřizování uživatelů pro připojené aplikace

Azure AD umožňuje automatizovat vytváření, údržbu a odebírání uživatelských identit v cloudových (SaaS) aplikací, jako je například Dropbox, Salesforce, ServiceNow a tak dále. Další informace najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure AD](../active-directory-saas-app-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrace služby information protection

MCAS umožňuje prohledávat soubory a nastavovat zásady na základě popisků klasifikace Azure Information Protection, řešení nabízí větší viditelnost a kontrolu nad daty v cloudu. Vyhledání a klasifikovat soubory v cloudu a použít popisky Azure information protection. Další informace najdete v tématu [integrace služby Azure Information Protection](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurace podmíněného přístupu

Konfigurace podmíněného přístupu na základě skupin, umístění a "choulostivosti" aplikace pro [aplikace SaaS](https://azure.microsoft.com/overview/what-is-saas/) a Azure AD připojených aplikací. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorování aktivity v připojených cloudových aplikací

Zajistit přístup uživatelů je chráněný v připojených aplikacích, doporučujeme vám, že využijete [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). To zajišťuje zabezpečení organizace přístup ke cloudovým aplikacím, kromě zabezpečení účtů správce, umožňuje:

* Rozšířit viditelnost a ovládací prvek do cloudových aplikací
* Vytvoření zásad pro přístup, aktivity a sdílení dat
* Automaticky zjišťovat rizikové aktivit, neobvyklé chování a hrozby
* Zabránit úniku dat
* Minimalizovat riziko a automatizované ohrožení ochrany před únikem informací a vynucení zásad

Agent Cloud App Security SIEM integruje server SIEM umožňuje centralizované sledování upozornění Office 365 a aktivit Cloud App Security. Pracuje na vašem serveru a získává upozornění a aktivity z Cloud App Security a streamuje na server SIEM. Další informace najdete v tématu [integrace řešení SIEM](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Fáze 4: Pokračování ve vytváření ochrany proaktivnějšího otázkách zabezpečení


![Fáze 4](./media/directory-admin-roles-secure/stage-four.png)

Fáze 4 plánu navazuje na viditelnost v fázi 3 a slouží k implementaci za šest měsíců a nad rámec. Dokončení plánu pomůže při vývoji strong privileged access ochrany z potenciální útoky, které jsou aktuálně známé a dostupné ještě dnes. Ohrožení zabezpečení bohužel neustále vyvíjí a shift, proto doporučujeme, že je zabezpečení nahlíželi jako na nepřerušovaný proces, zaměřuje na zvyšování nákladů a snižování úspěšnosti na straně nežádoucích osob, které cílí na vaše prostředí.

Zabezpečení privilegovaného přístupu je zásadním prvním krokem k navázání záruky zabezpečení podnikových prostředků v moderních organizacích, ale není jediná součást kompletního programu zabezpečení, která bude zahrnovat prvky, jako je například zásady, operace, informace zabezpečení, serverů, aplikací, počítače, zařízení, cloudových prostředků infrastruktury a dalších součástí poskytují probíhající bezpečnostními zárukami. 

Kromě správy vašich účtů privilegovaný přístup, doporučujeme, abyste že si přečetli následující průběžně:

* Ujistěte se, že správci dělají jejich každodenní firmy jako neprivilegované uživatele.
* Pouze udělit oprávnění k přístupu, pokud je nepotřebujete a odebere ji později (just-in-time).
* Zachovat a kontrolní aktivita auditu vztahující se k privilegovaným účtům.

Další informace o vytvoření úplného plánu zabezpečení najdete v tématu [IT zdroje informací o architektuře cloudu Microsoft](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Další informace o zapojení služeb společnosti Microsoft pro účely pomoci s kterýmkoli z těchto témat vám poskytne zástupce Microsoftu nebo naleznete v tématu [sestavení kritické kybernetických ochrany k ochraně vaší organizace](https://www.microsoft.com/microsoftservices/campaigns/cybersecurity-protection.aspx).

Tento poslední probíhající fáze plánu zabezpečené privilegovaného přístupu zahrnuje následující součásti.

### <a name="general-preparation"></a>Obecné přípravy

#### <a name="review-admin-roles-in-azure-active-directory"></a>Kontrola rolí správce v Azure Active Directory 

Určí, jestli jsou stále aktuální role správců aktuální integrované služby Azure AD a ujistěte se, že uživatelé jsou pouze v rolích a delegování, které potřebují pro odpovídající oprávnění. S Azure AD můžete určit samostatné správcům slouží různým funkcím. Další informace najdete v tématu [přiřazení rolí správce v Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Zařízení připojená k revizi, kteří mají Správa služby Azure AD

Další informace najdete v tématu [jak nakonfigurovat hybridní služby Azure Active Directory zařízení připojená k](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Projděte si členové [předdefinované role správce Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Pokud používáte Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Ověření plánu reakcí na incidenty

Ke zlepšení při plánu, Microsoft doporučuje že pravidelně ověřit, že váš plán funguje podle očekávání:

* Projděte si existující silniční mapu zobrazit, co byla provedena
* Na základě analýzy postmortem revizi stávající nebo definovat nové doporučené postupy
* Zajišťují distribuci vašeho plánu reakcí na incidenty aktualizované a osvědčené postupy v rámci vaší organizace


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Další kroky pro organizace spravovat přístup k Azure 

Pokud je potřeba určit [přenos vlastnictví předplatného Azure na jiný účet](../../billing/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Pohotovostní": co dělat v případě nouze

![Nouzový](./media/directory-admin-roles-secure/emergency.jpeg)

1. Oznámí správci klíčů a vedoucí pracovníci pověření ochranou zabezpečení s relevantní informace týkající se incidentu.

2. Zkontrolujte vaše playbook útoku. 

3. Přístup k "pohotovostní" kombinace uživatelského jména a hesla účtu pro přihlášení k Azure AD. 

4. Získejte pomoc od společnosti Microsoft nástrojem [otevírání žádosti podporu Azure](../../azure-supportability/how-to-create-azure-support-request.md).

5. Podívejte se na [sestav Azure AD přihlášení](../reports-monitoring/overview-reports.md). Může existovat zpoždění mezi výskytu události, a když je zahrnutý do sestavy.

6. Pro hybridní prostředí, pokud federované a služby AD FS server není k dispozici, možná budete muset dočasně přepněte z federovaného ověřování používat synchronizace hodnot hash hesel. To obnoví federace domény zpět na spravované ověřování, dokud nebude k dispozici serveru služby AD FS.

7. Monitorování e-mailu pro privilegované účty.

8. Zajistěte, aby že ukládat zálohy protokolů relevantní pro potenciální právní a forenzní zkoumání.

Další informace o tom, jak Microsoft Office 365 zpracovává bezpečnostní incidenty v oblasti, naleznete v tématu [Správa incidentů zabezpečení v Microsoft Office 365](http://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Nejčastější dotazy: Časté otázky státem týkající se zabezpečení privilegovaného přístupu  


**Otázka:** Co mám dělat, když mám nebyly dosud implementován součásti žádné zabezpečený přístup?

**Odpověď:** definovat aspoň dva break pohotovostní účet, přiřaďte vícefaktorové ověřování pro správce privilegovaných účtů a samostatné uživatelské účty z globálního správce účtů.


**Otázka:** po porušení zabezpečení, co je nejdůležitější problémy, které je potřeba se zpracovávají jako první?

**Odpověď:** Ujistěte se, při vyžadování nejsilnější ověřování pro vysoce vystavený jednotlivce.


**Otázka:** co se stane, když byly deaktivovány naše privilegovaných správců?

**Odpověď:** vytvořte účet globálního správce, který je pořád aktuální.


**Otázka:** co se stane, pokud existuje pouze jeden globální správce vlevo a není dostupný? 

**Odpověď:** použijte jednu z break pohotovostní účty získat okamžitý oprávnění k přístupu.


**Otázka:** jak můžete chránit admins v rámci mé organizaci?

**Odpověď:** mají správci vždy proveďte jejich každodenní firmy jako standardní uživatelé "bez oprávnění umožňovala zvlášť".
 

**Otázka:** doporučené postupy pro vytváření účtů správce ve službě Azure AD?

**Odpověď:** rezervy privilegovaný přístup pro správu konkrétní úlohy.


**Otázka:** jaké nástroje existovat pro snížení trvalý přístup?

**Odpověď:** Privileged Identity Management (PIM) a Azure AD rolí správce.


**Otázka:** co je Microsoft pozice na synchronizaci účtů správce Azure AD?

**Odpověď:** vrstvy 0 účtům pro správu (včetně účty, skupiny a další prostředky, které mají přímou nebo nepřímou správní kontrolu nad doménové struktuře Active Directory, doménami nebo řadiči domény a všechny prostředky) se používá pouze pro místní účty služby AD a jsou obvykle nejsou synchronizovány pro službu Azure AD pro cloud. 


**Otázka:** jak nám zachovat správci v přiřazení náhodné správu přístupu na portálu?

**Odpověď:** použít neprivilegované účty pro všechny uživatele a většina správce. Začněte tím, že vývoj nároky organizace rozhodnout, který by měl být privilegovaného několik účtů správců. Sledování a pro nově vytvořený administrativní uživatelé.


## <a name="next-steps"></a>Další postup

* [Microsoft Trust Center pro produkt zabezpečení](https://www.microsoft.com/trustcenter/security) – zabezpečení funkce Microsoft cloudových produktů a služeb

* [Microsoft Trust Center - dodržování předpisů](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – komplexní sadu nabídek dodržování předpisů pro cloudové služby společnosti Microsoft

* [Pokyny o tom, jak provést vyhodnocení rizik](https://www.microsoft.com/trustcenter/guidance/risk-assessment) – Spravovat požadavky na zabezpečení a dodržování předpisů pro cloudové služby Microsoftu

### <a name="other-ms-online-services"></a>Další Online služby MS 

* [Microsoft Intune zabezpečení](https://www.microsoft.com/trustcenter/security/intune-security) – Intune poskytuje správu mobilních zařízení, správy mobilních aplikací a možnosti správy počítačů z cloudu.

* [Zabezpečení Microsoft Dynamics 365](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 je cloudové řešení Microsoftu, který spojuje řízení vztahů se zákazníky (CRM) a podnikových zdrojů (ERP) možnosti plánování.

 
