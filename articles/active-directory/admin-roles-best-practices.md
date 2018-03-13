---
title: "Osvědčené postupy pro zabezpečení přístup pro správu ve službě Azure AD | Microsoft Docs"
description: "Zajistěte, aby byly zabezpečené účty pro správu přístupu a správce vaší organizace. Pro systémové architekty a IT specialistů, kteří konfigurují služby Azure AD Azure a služeb Microsoft Online Services."
services: active-directory
keywords: 
author: curtand
ms.author: curtand
ms.date: 03/09/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: martincoetzer, MarkMorow
ms.openlocfilehash: 0231dc8336bb2442099984947897e5005767a8f5
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Zabezpečení privilegovaného přístupu pro nasazení hybridními a cloudovými ve službě Azure AD

Zabezpečení většině nebo všem obchodní prostředků v organizaci moderní závisí na integritě privilegované účty, které spravují a spravovat systémy IT. Nebezpečného actors včetně internetový útočníci často cílové účty správců a další prvky privilegovaného přístupu k pokusu o rychle získat přístup k citlivým datům a systémy, používat útoku krádeží přihlašovacích údajů. Pro cloudové služby, prevence a odpovědi jsou společné odpovědnosti poskytovatele cloudové služby a Zákazník. Další informace o nejnovějších hrozbách koncových bodů a cloudu najdete v tématu [sestavy Intelligence zabezpečení Microsoft](https://www.microsoft.com/security/sir/default.aspx). Tento článek vám může pomoct vytvořit plán vývoje směrem k zavření mezery mezi vaší aktuální plány a pokyny, které jsou zde popsané.

> [!NOTE] 
> Společnost Microsoft se zavazuje nejvyšší úrovně důvěryhodnosti, průhlednost, shoda standardů a dodržování legislativní předpisů. Další informace o tom, jak týmem Microsoft globální reakcí na incidenty důsledky útoky na cloudové služby a jak zabezpečení je součástí kancelářských aplikací společnosti Microsoft a cloudové služby na [Microsoft Trust Center – zabezpečení](https://www.microsoft.com/en-us/trustcenter/security)a cíle Microsoft dodržování předpisů v [Microsoft Trust Center – dodržování předpisů](https://www.microsoft.com/en-us/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
Pro většinu organizací zabezpečení prostředků obchodní závisí na integritu privilegované účty, které spravují a spravovat systémy IT. Internetový útočník soustředit na privilegovaný přístup k systémům infrastruktury (třeba služby Active Directory a Azure Active Directory) k získání přístupu k citlivým datům organizace. 

Tradiční přístupy, které soustředit na zabezpečení body vstupu a výstupu jako primární zabezpečení hraniční sítě jsou méně účinné kvůli rostoucím použití aplikace SaaS a osobní zařízení na Internetu. Fyzická náhradou zabezpečení hraniční sítě v komplexní moderní enterprise je ověřování a autorizace ovládacích prvků v vrstvu identit organizace. 

Privilegované účty pro správu jsou efektivní kontrolu nad tento nový "zabezpečení hraniční." Je důležité chránit privilegovaného přístupu, bez ohledu na to, jestli je prostředí na místě, cloudu nebo hybridních místní a Cloudová hostovaných služeb. Ochrana proti určené nežádoucí přístup pro správu vyžaduje, abyste pro dokončení a žádný jazyk postup k izolování systémy vaší organizace z rizika. 

Zabezpečení privilegovaného přístupu vyžaduje změny
* Procesy, administrativních postupů a Správa znalostní báze
* Technické komponenty, například hostitele obrany, účet ochranu a správu identit

Tento dokument je zaměřen především na vytváření plán k zabezpečení identity a přístup, který spravuje nebo hlášené v Azure AD, Microsoft Azure, Office 365 a jiné cloudové služby. Pro organizace, které mají místní účty pro správu, najdete pokyny pro místní a hybridní privilegovaný přístup spravované ze služby Active Directory v [zabezpečení privilegovaného přístupu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Pokyny v tomto článku se vztahuje zejména na funkce služby Azure Active Directory, které jsou zahrnuty v plánech Azure Active Directory Premium P1 a P2. Azure Active Directory Premium P2 je součástí sady EMS E5 a Microsoft 365 E5 suite. Tyto pokyny předpokládají, že už vaše organizace má Azure AD Premium P2 zakoupených pro vaše uživatele. Pokud jste tyto licence, některé z uvedených nemusí platit pro vaši organizaci. Také v rámci tohoto článku, termín globálního správce (nebo globální správce) je totožná s "Správce společnosti" nebo "správce klienta."

## <a name="develop-a-roadmap"></a>Vytvořit plán vývoje 

Společnost Microsoft doporučuje, jste sami vyvinuli a postupujte podle plán k zabezpečení privilegovaného přístupu proti internetový útočníkům. Vždy můžete upravit váš plán pro přizpůsobení vaší stávající funkce a specifické požadavky v rámci vaší organizace. Každá fáze plán by měl zvýšit náklady a potíže pro nežádoucí vůči útokům privilegovaného přístupu pro místní, cloud a hybridní prostředky. Společnost Microsoft doporučuje následující čtyři fáze plán: Toto doporučení plán plány nejúčinnějším a nejrychlejší implementace nejprve závislosti na prostředí společnosti Microsoft s implementací internetový útoku incidentů a odpovědi. Časové osy pro tento plán jsou jen přibližné.

![Fáze plán čas řádků](./media/admin-roles-best-practices/roadmap-timeline.png)

* Fáze 1 (24 48 hodin): kritické položky, které vám doporučujeme provést hned

* Fáze 2 (2 – 4 týdny): zmírnit útok nejčastěji používané techniky

* Fáze 3 (1 – 3 měsíců): sestavení viditelnost a vytvořit úplnou kontrolu nad aktivita správce

* Fáze 4 (šest měsíců a dále): pokračovat ve vytváření obrany k další posílení zabezpečení platformy pro zabezpečení

Tento plán je vytvořeno tak, aby maximalizovat využití technologiích společnosti Microsoft, které jste mohli jste už nasadit. Můžete také využít výhod technologií aktuálních a budoucích zabezpečení klíčů a integrovat nástroje zabezpečení od jiných dodavatelů už mají nasazenou nebo zvažují nasazení. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Fáze 1: Kritické položky, které vám doporučujeme provést hned

![Fáze 1](./media/admin-roles-best-practices/stage-one.png)

Fáze 1 plánovaná se zaměřuje na kritické úlohy, které jsou rychlé a snadno implementovat. Doporučujeme, abyste provedli tyto několik položek hned během prvních 24 48 hodin zajistit základní úroveň zabezpečení privilegovaného přístupu. Tato fáze plán zabezpečené privilegovaného přístupu zahrnuje následující akce:

### <a name="general-preparation"></a>Obecné přípravy

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Zapnout Azure AD Privileged Identity Management

Pokud již jste nezapnuli Azure AD Privileged Identity Management (PIM), můžete tak učinit ve vašem produkčním klientovi. Po Privileged Identity Management zapnete, dostanete oznámení e-mailové zprávy pro privilegovaný přístup změny role. Tato oznámení zadejte včasné upozornění při přidání dalších uživatelů vysoce privilegovaných rolí ve vašem adresáři.

Azure AD Privileged Identity Management je obsažena v Azure AD Premium P2 nebo EMS E5. Tato řešení vám umožní chránit přístup k aplikacím a prostředkům v místním prostředí a do cloudu. Pokud dosud nemáte Azure AD Premium P2 nebo EMS E5 a chcete vyhodnotit další možnosti, kterou odkazuje tento plán, si zaregistrovat [Enterprise Mobility + Security bezplatné zkušební verze-90denní](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Pomocí těchto licencí zkušební verze Azure AD Privileged Identity Management a Azure AD Identity Protection monitorování pomocí služby Azure AD Rozšířené sestavy zabezpečení, auditování a výstrahy.

Po jste zapnuli na Azure AD Privileged Identity Management:

1. Přihlaste se k [portál Azure](https://portal.azure.com/) pomocí účtu, který je globálním správcem klienta služby produkční.

2. Pokud chcete vybrat klienta, kde chcete použít Privileged Identity Management, vyberte své uživatelské jméno v pravém horním rohu portálu Azure.

3. Vyberte **všechny služby** a filtrovat seznam pro **Azure AD Privileged Identity Management**.

4. Otevřete Privileged Identity Management z **všechny služby** seznamu a připnout na řídicí panel.

První, kdo použít Azure AD Privileged Identity Management ve vašem klientovi se automaticky přiřadí **správce zabezpečení** a **správce privilegovaných rolí** rolí v klientovi. Pouze správci privilegované role můžete spravovat přiřazení rolí adresáře Azure AD uživatelů. Po přidání Azure AD Privileged Identity Management, navíc se zobrazí Průvodce zabezpečení, který vás provede počáteční zjišťování a přiřazení prostředí. Bez jakýchkoli dalších změn, v tuto chvíli můžete ukončit průvodce. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identifikovat a kategorizace účty, které jsou v vysoce privilegované role 

Po zapnutí Azure AD Privileged Identity Management, zobrazte uživatele, kteří jsou v globální správce adresáře role, správce privilegovaných rolí, správce Exchange Online a SharePoint Online správce. Pokud ve vašem klientovi Azure AD PIM nemáte, můžete použít [rozhraní API prostředí PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Spustit s rolí globálního správce, protože tato role je obecný: uživatel, kterému je přiřazena tato role správce má stejné oprávnění mezi všechny cloudové služby, pro které má vaše organizace předplatné, bez ohledu na to, jestli se přiřadil tuto roli na portálu Office 365 , portálu nebo pomocí modulu Azure AD pro Microsoft PowerShell Azure. 

Odeberte všechny účty, které již nejsou potřebné v těchto rolí a kategorizace zbývajících účtů, které jsou přiřazeny k rolí správce:

* Jednotlivě přiřazeny správcům a lze také použít pro účely bez oprávnění správce (například osobní e-mailu)
* Jednotlivě přiřazené správcům a určené pouze pro účely správy
* Sdílet více uživatelů
* Pro scénáře break pohotovostní nouzový přístup
* Pro automatizované skripty
* Pro externí uživatele

#### <a name="define-at-least-two-emergency-access-accounts"></a>Zadejte alespoň dva účty pro nouzový přístup 

Ujistěte se, že se nezobrazí do situace, kdy se může nechtěně uzamčení správy klienta služby Azure AD kvůli nemožností přihlášení nebo aktivovat účet existující konkrétního uživatele jako správce. Například pokud organizace federovaný pro zprostředkovatele identity na místě, tohoto zprostředkovatele identity pravděpodobně není k dispozici, uživatelé nemůžete se přihlásit místně. Uložením dvě nebo více účtů nouzový přístup ve vašem klientovi můžete zmírnit dopad náhodných nedostatečná přístup pro správu.

Účty pro nouzový přístup pomáhají organizacím omezit privilegovaný přístup v rámci stávajícího prostředí Azure Active Directory. Tyto účty jsou vysoce privilegované a nebyly přiřazeny do konkrétní osoby. Účty pro nouzový přístup je omezen na nouzový 'pohotovostní' scénáře, kdy nejde použít normální účty pro správu. Organizace musí zajistit za účelem řízení a snižuje využití nouzový účet pouze tento čas, pro které je nezbytné. 

Vyhodnoťte účty, které jsou přiřazené ani vhodné k roli globálního správce. Pokud nezobrazí žádné jen cloudové účty pomocí *. onmicrosoft.com domény (určený pro nouzový přístup "pohotovostní"), vytvořte je. Další informace najdete v tématu [Správa nouzový přístup pro správu účtů ve službě Azure AD](active-directory-admin-manage-emergency-access-accounts.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Zapnout vícefaktorové ověřování a zaregistrujte všechny ostatní účty vysoce privilegovaných jednoho uživatele nefederovaných správce 

Vyžadovat Azure Multi-Factor Authentication (MFA) při přihlášení pro všechny jednotlivé uživatele, kteří jsou trvale přiřazené na jeden nebo více rolí Správce služby Azure AD: Globální správce, správce privilegovaných rolí, správce Exchange Online a SharePoint Správce služby online. Pomocí průvodce povolit [Multi-Factor Authentication (MFA) pro účtů správce](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) a ujistěte se, že tito uživatelé zaregistrovali na [https://aka.ms/mfasetup](https://aka.ms/mfasetup). Další informace naleznete v části kroky 2 a 3 průvodce [chránit přístup k datům a službám Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Fáze 2: Zmírnit útok nejčastěji používané techniky

![Fáze 2](./media/admin-roles-best-practices/stage-two.png)

Fáze 2 průvodce se zaměřuje na zmírnění techniky nejčastěji používaných útoku krádeží přihlašovacích údajů a zneužití a slouží k implementaci do přibližně 2 – 4 týdny. Tato fáze plán zabezpečené privilegovaného přístupu zahrnuje následující akce.

### <a name="general-preparation"></a>Obecné přípravy

#### <a name="conduct-a-inventory-of-services-owners-and-admins"></a>Proveďte inventáře služeb, vlastníků a správci

S nárůstem bring-your-own-device (BYOD) a práci z Domů zásady a růst bezdrátového připojení v firmách je velmi důležité, můžete monitorovat, na který se připojuje k vaší síti. Efektivní zabezpečení auditu často zjistí zařízení, aplikace a programy spuštěné ve vaší síti, která nepodporuje IT a proto není potenciálně zabezpečení. Další informace najdete v tématu [monitorování přehled správy zabezpečení Azure a](../security/security-management-and-monitoring-overview.md). Ujistěte se, zahrnují všechny z následujících úloh v procesu inventáře. 

* Identifikujte uživatele, kteří mají pro správu rolí a služeb, kde můžete spravovat.
* Pomocí Azure AD PIM a zjistěte, kteří uživatelé ve vaší organizaci mají přístup správce ke službě Azure AD, včetně dalších rolí nad rámec těch, uvedené v fáze 1.
* Kromě role definované ve službě Azure AD Office 365 obsahuje sadu rolí správce, které můžete přidělovat uživatelům ve vaší organizaci. Každá role správce se mapuje na běžné podnikové funkce a poskytuje lidem ve vaší organizaci oprávnění ke všem konkrétní úlohy v Centru správy Office 365. Použijte Centrum pro správu Office a zjistěte, kteří uživatelé ve vaší organizaci mají přístup správce k Office 365, včetně prostřednictvím role ve službě Azure AD není spravovaný. Další informace najdete v tématu [role Správce služeb Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) a [osvědčené postupy zabezpečení pro Office 365](https://support.office.com/article/Security-best-practices-for-Office-365-9295e396-e53d-49b9-ae9b-0b5828cdedc3).
* Inventarizaci v jiných služeb, které vaše organizace využívá, například Azure, Intune nebo Dynamics 365.
* Zkontrolujte, že účtů správce (účty, které se používají pro účely správy, ne jenom každodenní účtů uživatelů) mít e-mailové adresy, které jsou připojené k jejich práci a zaregistrovali pro Azure MFA nebo pomocí MFA na místě.
* Požádejte uživatele pro jejich obchodního oprávnění pro přístup pro správu.
* U těchto jednotlivce a služby, které nepotřebujete ho odeberte přístup správce.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifikovat účty Microsoft v rolích pro správu, které je potřeba přepnout do pracovní nebo školní účty 

V některých případech znovu počáteční globální správci v organizaci, je použít své stávající přihlašovací údaje účtu Microsoft, při se začal pomocí služby Azure AD. Tyto účty Microsoft by měl být nahrazen jednotlivých účtů založené na cloudu nebo synchronizované. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Ujistěte se, samostatné uživatelské účty a e-mailu předávání pro účty globální správce 

Globální správce účtů by neměl mít osobní e-mailové adresy, jsou pravidelně phished útočníků internetový osobní e-mailové účty. Můžete oddělit rizika internet (útoky phishing neúmyslnému prohlížení Internetu) oprávnění správce, vytvořte vyhrazené účty pro každého uživatele s oprávněními správce. 

Pokud jste tak již neučinili, vytvořte samostatné účty pro uživatele k provádění úloh globálního správce, abyste měli jistotu, není nechtěně otevřít e-mailů nebo spouštět programy, které jsou přidružené k účtu správce. Ujistěte se, že tyto účty e-mailu předávaných do poštovní schránky práci.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Ujistěte se, že v nedávné době změnili hesla účtů pro správu

Ujistěte se, že mají všichni uživatelé přihlášení k jejich účty pro správu a změnit hesla nejméně jednou za posledních 90 dnů. Také zkontrolujte, zda byl, víte, že všechny sdílené účty, ve které více uživatelů heslo nedávno změnili hesla.

#### <a name="turn-on-password-synchronization"></a>Zapnutí synchronizace hesel

Synchronizace hesel je funkce používá k synchronizaci hodnoty hash hodnot hash hesla uživatele z místní instance služby Active Directory k Azure cloudové AD instance. I, že pokud se rozhodnete používat federační službou Active Directory Federation Services (AD FS) nebo jiných poskytovatelů identit, můžete volitelně nastavení synchronizace hesel jako záložní v případě vaší místní infrastruktury, jako je AD nebo servery služby AD FS selhat nebo se změní na dočasně nedostupná. To umožňuje uživatelům přihlásit ke službě pomocí stejné heslo, které používají pro přihlášení k místní instanci AD. Také je možné zjistit zneužití přihlašovacích údajů porovnáním hodnot hash těchto hesel s hesly známé došlo k narušení, pokud má uživatel využít jejich stejnou e-mailovou adresu a heslo na jiné služby, které není připojeno k Azure AD Identity Protection.  Další informace najdete v tématu [implementace synchronizace hesel s Azure AD Connect sync](./connect/active-directory-aadconnectsync-implement-password-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Vyžadovat vícefaktorové ověřování (MFA) pro uživatele ve všech privilegovaných rolí, jakož i zveřejněné uživatelů

Azure AD doporučuje vyžadovat vícefaktorové ověřování (MFA) pro všechny uživatele, včetně správců a ostatních uživatelů, kteří by mají významný vliv, pokud došlo k ohrožení jejich účtu (například finanční osoby). Tím se snižuje riziko útoků z důvodu ohroženými heslo.

Zapněte:

* [MFA pro účty vysoce ohrožení](../multi-factor-authentication/multi-factor-authentication-security-best-practices.md) například účty pro vedení osoby v organizaci 
* [MFA pro každý účet správce přidružený jednotlivého uživatele](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) pro jiné připojené aplikace SaaS 
* Vícefaktorové ověřování pro všechny správce pro aplikace Microsoft SaaS, včetně správců v rolích spravované v systému Exchange Online a portálu Office

Pokud používáte Windows Hello pro firmy, požadavek na vícefaktorové ověřování můžete splnit pomocí přihlášení Windows Hello v prostředí. Další informace najdete v tématu [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Konfigurovat Identity Protection 

Azure AD Identity Protection je monitorování na základě algoritmus a vytváření sestav nástroj, který můžete použít k detekci potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci. Můžete nakonfigurovat automatické odpovědi na tyto zjištěné podezřelé aktivity a proveďte příslušné akce jejich řešení. Další informace najdete v tématu [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Získat skóre zabezpečit vaše Office 365 (Pokud používáte Office 365)

Zabezpečte skóre obrázků na jaké služby Office 365 používáte (jako je OneDrive, SharePoint a Exchange) vypadá na vaše nastavení a aktivity a porovná je se směrný plán navázat společností Microsoft. Získáte skóre na základě toho, jak zarovnaný, které jste se s osvědčenými postupy zabezpečení. Každý, kdo má oprávnění správce (globální správce nebo roli správce vlastní) pro předplatné Office 365 Business Premium nebo Enterprise přístup zabezpečit skóre v [https://securescore.office.com](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office365"></a>Přečtěte si pokyny pro zabezpečení a dodržování předpisů Office 365 (Pokud používáte Office 365)

[Plánování zabezpečení a dodržování předpisů](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) popisuje způsob, jak by měla zákazníkem služby Office 365 konfigurace Office 365 a využít další možnosti EMS. Potom zkontrolujte kroky 3 až 6 jak [chránit přístup k datům a službám Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) a v příručce pro postup [monitorování zabezpečení a dodržování předpisů v Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).


#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Konfigurace monitorování aktivit Office 365 (Pokud používáte Office 365)

Můžete sledovat, jak uživatelé ve vaší organizaci používají služby Office 365, povolení k identifikaci uživatelů, kteří mají účtu správce a kdo nemusí potřebovat Office 365 přístup z důvodu není podepisování do těchto portálů. Fore další informatuon, najdete v části [aktivity sestavy v Centru pro správu Office 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Vytvořit incident nebo nutnosti odpovědi plán vlastníky

Efektivní provádění reakcí na incidenty je složitý proces. Vytvoření funkce úspěšné reakcí na incidenty vyžaduje proto významné plánování a prostředky. Je nutné, aby průběžně monitorovat internetovými útoky a vytvoří postupy pro zpracování incidentů stanovení priority. Efektivní metody shromažďování, analýze a vytváření sestav dat jsou potřeba k vytvoření relace a navázat komunikaci s další interní skupiny aplikací a naplánujte vlastníky. Další informace najdete v tématu [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Zabezpečené místní privilegované účty pro správu, pokud není neudělali

Pokud se klientovi služby Azure Active Directory synchronizována s místní služby Active Directory, postupujte podle pokynů v [Přehled zabezpečení privilegovaného přístupu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): fáze 1. To zahrnuje vytváření správce samostatné účty pro uživatele, kteří potřebují k provádění úloh správy místní nasazení Privileged Access pracovní stanice pro správce služby Active Directory a vytváření hesel jedinečný místního správce pro pracovní stanice a servery.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Další kroky pro organizace, Správa přístupu k Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Dokončení inventář odběrů

Slouží k identifikaci odběry ve vaší organizaci, které jsou hostiteli výrobní aplikace podnikovém portálu a portálu Azure. 

#### <a name="remove-microsoft-ccounts-from-admin-roles"></a>Odebrání rolí správce Microsoft ccounts

Účty Microsoft z jiných aplikací, jako jsou Xbox Live a Outlook nepoužívejte jako účty správců pro organizační odběry. Odeberte správce stav ze všech účtů Microsoft a nahraďte se službou Active Directory (například chris@contoso.com) pracovní nebo školní účty.

#### <a name="monitor-azure-activity"></a>Sledovat činnost Azure

Protokol činnosti Azure poskytuje historii událostí na úrovni předplatného v Azure. Nabízí informace o kdo vytvořen, aktualizovat a odstranit prostředky, a pokud tyto události došlo k chybě. Další informace najdete v tématu [auditu a přijímat oznámení o důležité akce ve vašem předplatném Azure](../monitoring-and-diagnostics/monitor-quick-audit-notify-action-in-subscription.md).


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Další kroky pro organizace, Správa přístupu k jiné cloudovým aplikacím přes Azure AD 

#### <a name="configure-conditional-access-policies"></a>Nakonfigurujte zásady podmíněného přístupu

Připravte zásady podmíněného přístupu pro místní a hostované cloudové aplikace. Pokud máte zařízení uživatelů připojených k síti na pracovišti, získat další informace z [nastavení místního podmíněného přístupu pomocí registrace zařízení služby Azure Active Directory](active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Fáze 3: Vytvoření viditelnost a proveďte úplnou kontrolu nad aktivita správce

![Fáze 3](./media/admin-roles-best-practices/stage-three.png)

Fáze 3 vychází jejich zmírnění z fáze 2 a slouží k implementaci do přibližně 1 – 3 měsíce. Tato fáze plán zabezpečené privilegovaného přístupu zahrnuje následující součásti.

### <a name="general-preparation"></a>Obecné přípravy

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Dokončit kontrolu přístupu uživatelé v rolích správce

Více podnikovým uživatelům jsou získání privilegovaného přístupu prostřednictvím cloudových služeb, které může vést roste nespravované platformu. Zahrnuti jsou uživatelé stal globální správci pro Office 365, správci předplatného Azure a uživatelé, kteří mají přístup správce ve virtuálních počítačích nebo prostřednictvím aplikací SaaS. Místo toho organizace, musí mít všechny zaměstnance, zejména admins zpracování každodenní obchodní transakce jako uživatelům bez oprávnění a trvat jenom na oprávnění správce, podle potřeby. Vzhledem k tomu, že od počátečního přijetí může roste počet uživatelů v rolí Správce služby, zkontroluje úplný přístup k identifikaci a potvrzení všech uživatelů, kteří vás opravňuje k aktivaci oprávnění správce. 

Udělejte toto:

* Určete uživatele, kteří jsou správci, povolit na vyžádání, přístup za běhu správce a ovládací prvky založené na rolích zabezpečení Azure AD.
* Převést uživatelé, kteří mají žádné zrušte oprávnění pro správce privilegovaný přístup k jiné roli (Pokud žádná vhodné role, odeberte je).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Pokračovat zavedení silnější ověřování pro všechny uživatele 

Vyžadovat C-suite vedení, vysoké úrovně manažerům, kritické IT a zabezpečení pracovníky a ostatní uživatelé vysoce zveřejněné moderní, silné ověřování, jako je například Azure MFA nebo Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Použití vyhrazených pracovních stanic pro správu pro Azure AD

Útočníci můžou snažit cíle privilegované účty pro přístup do firemní data a systémy, že může dojít k přerušení integrity a jejich pravost data prostřednictvím škodlivého kódu, který mění logice program nebo snoops zadání pověření správce. Pracovní stanice privilegovaného přístupu (PAWs) poskytuje vyhrazený operačního systému pro citlivé úlohy, které jsou chráněny před útoky Internet a z nečekaných směrů. Oddělení tyto úkoly citlivé a účty z denní použít pracovní stanice a zařízení poskytuje velmi silné ochranu před útoky phishing, aplikace a ohrožení zabezpečení operačního systému, různé zosobnění útokům a útokům krádeže přihlašovacích údajů například stisknutí klávesy protokolování, Pass-the-Hash a Pass-The-Ticket. Nasazení pracovní stanice privilegovaný přístup, můžete snížit riziko, že správci zadejte přihlašovací údaje správce, s výjimkou na prostředí plochy, který má zesílené zabezpečení. Další informace najdete v tématu [pracovní stanice privilegovaného přístupu](https://docs.microsoft.com/en-us/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Zkontrolujte doporučení Národního institutu standardů a technologie pro zpracování incidentů 

Národního institutu standardů a technologie (NIST) obsahuje pokyny pro zpracování incidentů, platí to hlavně o analýze dat souvisejících s incidentu a určení příslušné odpověď na každý incident. Další informatiin, najdete v části [The (NIST) počítače Security Incident Handling Guide (SP 800 61, revize 2)](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementaci Privileged Identity Management (PIM) pro JIT pro další role správců

Pro Azure Active Directory, použijte [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) schopností. Časově omezené aktivace privilegované role funguje tak, že umožňuje:

* Aktivovat správce oprávnění k provedení určitého úkolu
* Vynutit vícefaktorové ověřování během procesu aktivace
* Výstrahy můžete použít ke Správci informujte o změnách out-of-band
* Povolit uživatelům uchovat určitá privilegia, předem nakonfigurovaného množství času
* Povolit zabezpečení správci zjistit všechny privilegované identity, zobrazení sestavy auditu a vytvoření recenze přístup k identifikaci všech uživatelů, kteří vás opravňuje k aktivaci oprávnění správce

Pokud už používáte Azure AD Privileged Identity Management, upravte časové rámce pro časově vázaných oprávnění podle potřeby (například časová období údržby).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Určení vystavení založené na heslech přihlášení protokolů (Pokud používáte Exchange Online)

V minulosti protokoly předpokládá, že byly vložených kombinace uživatelského jména a hesla v zařízení, e-mailové účty, telefony a tak dále. Ale nyní s rizikem pro internetovými útoky v cloudu, doporučujeme identifikovat, všech potenciálních uživatelů, kteří, pokud byly dojde k ohrožení jejich přihlašovacích údajů, může být závažné k organizaci a vyloučit z se moct přihlásit k e-mailu pomocí uživatelského jména / heslo implementací požadavky na silné ověřování a podmíněného přístupu. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Dokončení vyhodnocení zkontrolujte role role služeb Office 365 (Pokud používáte Office 365)

Vyhodnocení, zda jsou všichni uživatelé admins ve správné role (odstranit a znovu přiřadíte podle této assessment).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Kontrola přístupu zabezpečení správy incidentů, používají v Office 365 a porovnání s vaší vlastní organizaci

Si můžete stáhnout z této sestavy [Správa incidentů zabezpečení v Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Pokračovat k zabezpečení místní privilegované účty pro správu

Pokud služby Azure Active Directory je připojený k místní službě Active Directory, postupujte podle pokynů v [Přehled zabezpečení privilegovaného přístupu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): fáze 2. To zahrnuje nasazení Privileged Access pracovní stanice pro všechny správce, vyžadování vícefaktorového ověřování, pomocí právě dostatečně správce pro řadič domény údržby, což snižuje prostor pro útoky domén, nasazení ATA pro detekce útoku.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Další kroky pro organizace, Správa přístupu k Azure

#### <a name="establish-integrated-monitoring"></a>Vytvoření integrované monitorování

[Azure Security Center](../security-center/security-center-intro.md) poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých může jinak nevšimli a spolupracuje s řadou zabezpečení řešení.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventáře privilegovaných účtů v rámci hostované virtuální počítače

Ve většině případů nemusíte poskytnout neomezená oprávnění uživatele k předplatná Azure nebo prostředky. Role správce Azure AD můžete oddělit povinností v rámci vaší organizace a poskytnout pouze takovou úroveň přístupu pro uživatele, kteří potřebují k provedení určité úlohy. Pokud chcete, aby jeden správce spravovat jenom virtuální počítače v předplatném, zatímco jiné můžete spravovat databází SQL v rámci stejného předplatného. použijte například role správce Azure AD. Další informace najdete v tématu [Začínáme s řízením přístupu na základě rolí na portálu Azure](role-based-access-control-what-is.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementace pro role správce Azure AD PIM

Pro správu, řízení a monitorování přístupu k prostředkům Azure pomocí role správce Azure AD Privileged identity Management. Pomocí PIM chrání privilegované účty před internetovými útoky snížení času ohrožení oprávnění a zvýšení vaší přehled o jejich používání prostřednictvím sestavy a výstrahy. Další informace najdete v tématu [přístupu RBAC spravovat prostředky Azure s Privileged Identity Management](pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Integrace protokolů Azure používat k odesílání relevantní protokolů Azure k vašim systémů SIEM 

Integrace protokolů Azure umožňuje integraci nezpracovaná protokoly z vašich prostředků Azure stávající informace o zabezpečení vaší organizace a systémy správy událostí (SIEM). [Integrace Azure protokolu](../security/security-azure-log-integration-overview.md) shromažďuje z protokoly Prohlížeče událostí systému Windows a prostředky Azure z protokolů aktivity Azure, Azure Security Center výstrahy a Azure diagnostické protokoly událostí systému Windows. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Další kroky pro organizace, Správa přístupu k jiné cloudovým aplikacím přes Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementace zřizování uživatelů pro připojené aplikace

Azure AD umožňuje automatizovat vytváření, údržbu a odebírání uživatelských identit v cloudových aplikacích (SaaS) jako je Dropbox, Salesforce, ServiceNow a tak dále. Další informace najdete v tématu [automatizace zřizování uživatelů a rušení zajištění pro aplikace SaaS ve službě Azure AD](active-directory-saas-app-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrace ochrany informací

MCAS můžete prozkoumat souborů a nastavení zásad založené na Azure Information Protection klasifikační štítky, povolení větší přehled a kontrolu nad daty v cloudu. Vyhledání a klasifikovat soubory v cloudu a použít Azure information protection popisky. Další informace najdete v tématu [integrace Azure Information Protection](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurace podmíněného přístupu

Konfigurace podmíněného přístupu na základě skupiny, umístění a aplikace velkých a malých písmen pro [aplikace SaaS](https://azure.microsoft.com/overview/what-is-saas/) a Azure AD připojené aplikace. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorování aktivity v připojených cloudových aplikací

Zajistit přístup uživatelů je chráněna v propojených aplikací také doporučujeme vám využít [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). To zabezpečuje enterprise přístupu do cloudových aplikací, kromě zabezpečení účtů správce, a to:

* Rozšíření viditelnost a kontrolu do cloudových aplikací
* Vytvořit zásady pro přístup, aktivity a sdílení dat
* Automaticky zjišťovat rizikové aktivit, neobvyklé chování a hrozeb
* Zabránit úniku dat
* Minimalizovat riziko a prevenci automatizované hrozeb a vynucení zásad

Cloud App Security SIEM agenta Cloud App Security integruje s server SIEM, abyste mohli povolit centralizované monitorování výstrah služeb Office 365 a aktivity. Pracuje na vašem serveru a vyžaduje výstrahy a aktivit od Cloud App Security a datové proudy je do serveru SIEM. Další informace najdete v tématu [SIEM integrace](https://docs.microsoft.com/cloud-app-security/siem).


## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Fáze 4: Pokračujte ve vytváření obrany k více proaktivní postavení zabezpečení


![Fáze 4](./media/admin-roles-best-practices/stage-four.png)

Fáze 4 plán vychází viditelnost z fáze 3 a slouží k implementaci v šest měsíců a později. Dokončení pomáhá plán, které vyvíjíte silné privilegovaného přístupu ochrany z možných útoků, které jsou aktuálně známé a dostupné ještě dnes. Bohužel se bezpečnostní hrozby neustále momentální a posunutí, proto doporučujeme zobrazení zabezpečení jako probíhající proces zaměřuje na vyvolání náklady a snižuje míru úspěšnosti nežádoucí cílení na vašem prostředí.

Zabezpečení privilegovaného přístupu je důležitým prvním krokem k navázání záruky zabezpečení pro firemní prostředky v organizaci, moderní, ale není pouze součást dokončení zabezpečení program, který bude zahrnovat prvky, jako jsou například zásady, operace, informace zabezpečení, servery, aplikací, počítačů, zařízení, cloudové infrastruktury a dalších součástí zaručí, že probíhající zabezpečení. 

Kromě správy privilegovaného přístupové účty, doporučujeme, že prostudujte si následující průběžně:

* Ujistěte se, že správci dělají své každodenní firmy jako uživatelům bez oprávnění.
* Pouze udělit privilegovaný přístup v případě potřeby a odebere ji potom (v běhu).
* Zachování a kontrolní aktivita auditu vztahující se k privilegovaným účtům.

Další informace o vytváření plán dokončení zabezpečení, najdete v části [Microsoft cloudové prostředky architektury IT](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Další informace o zapojení služby společnosti Microsoft, které pomáhají s žádným z těchto témat, obraťte se na zástupce společnosti Microsoft nebo v tématu [sestavení kritické internetový obrany k ochraně vašeho podniku](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Tato závěrečná fáze probíhající plán zabezpečené privilegovaného přístupu zahrnuje následující součásti.

### <a name="general-preparation"></a>Obecné přípravy

#### <a name="review-admin-roles-in-azure-active-directory"></a>Zkontrolujte rolí správce v Azure Active Directory 

Určí, jestli jsou stále aktuální role správce aktuální předdefinované Azure AD a ujistěte se, že uživatelé jsou pouze v role a delegování, které potřebují pro odpovídající oprávnění. S Azure AD můžete určit samostatné správcům poskytovat různé funkce. Další informace najdete v tématu [přiřazení rolí správce v Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Zařízení připojená k zkontrolujte uživatelé, kteří mají správy služby Azure AD

Další informace najdete v tématu [jak ke konfiguraci hybridního připojená k Azure Active Directory zařízení](device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Zkontrolujte členy [předdefinované role Správce služeb Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Pokud používáte Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Ověření plánu reakcí na incidenty

Pokud chcete zvýšit na plánu, společnost Microsoft doporučuje že pravidelně ověřit, že váš plán funguje podle očekávání:

* Projít vaše stávající silniční mapu chcete zobrazit, co byla provedena
* Na základě analýzy před porážkou post revizi stávající nebo definovat novou osvědčené postupy
* Ujistěte se, že plánu aktualizované reakcí na incidenty a osvědčené postupy jsou distribuovány v celé organizaci


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Další kroky pro organizace, Správa přístupu k Azure 

Pokud budete muset určit [přenos vlastnictví předplatného služby Azure na jiný účet](../billing/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Pohotovostní": co dělat v případě nouze

![Nouzové](./media/admin-roles-best-practices/emergency.jpeg)

1. Upozorněte správce klíčů a zabezpečení osob s příslušné informace o incidentu.

2. Zkontrolujte vaše playbook útoku. 

3. Přístup k kombinace uživatelského jména a hesla "pohotovostní" účet pro přihlášení k Azure AD. 

4. Získat pomoc od společnosti Microsoft nástrojem [otevřít žádost o podporu Azure](../azure-supportability/how-to-create-azure-support-request.md).

5. Podívejte se na [Azure AD přihlášení sestavy](active-directory-reporting-azure-portal.md). Může být prodleva mezi výskytu události, a když je zahrnutý v sestavě.

6. Pro hybridní prostředí, pokud federovaný a služby AD FS server není k dispozici, bude pravděpodobně nutné dočasně přepnutí z federovaného ověřování používat synchronizaci hodnoty hash hesla. To obnoví federaci domény zpět do spravovaných ověřování, dokud nebude k dispozici serveru služby AD FS.

7. Monitorování e-mailu k privilegovaným účtům.

8. Zajistěte, aby že ukládat zálohy relevantní protokoly pro potenciální forenzní a právní šetření.

Další informace o zpracování bezpečnostní incidenty v Microsoft Office 365 najdete v tématu [Správa incidentů zabezpečení v Microsoft Office 365](http://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Nejčastější dotazy: Časté otázky obdržíme v souvislosti zabezpečení privilegovaného přístupu  


**Otázka:** Co mám dělat, když I nebyly dosud implementována žádné součásti zabezpečeného přístupu?

**Odpověď:** definovat aspoň dva break pohotovostní účet, přiřadit MFA správce privilegovaných účtů a samostatné uživatelské účty z globálního správce účtů.


**Otázka:** po porušení zabezpečení, co je nejvyšší problém, který je potřeba vzít v úvahu nejprve?

**Odpověď:** Ujistěte se, jste nutnosti nejsilnější ověřování pro vysoce zveřejněné jednotlivce.


**Otázka:** co se stane, když bylo deaktivováno naše privilegované admins?

**Odpověď:** vytvořit účet globálního správce, který je pořád aktuální.


**Otázka:** co se stane, když je pouze jeden globální správce vlevo a nelze získat přístup? 

**Odpověď:** použijte jednu z break pohotovostní účty získat okamžitou oprávnění k přístupu.


**Otázka:** jak můžete chránit admins v rámci Moje organizace?

**Odpověď:** mít správci vždy provést své každodenní firmy jako standardní uživatelé "Neprivilegovaný".
 

**Otázka:** co jsou doporučené postupy pro vytváření účtů správce v rámci Azure AD?

**Odpověď:** rezervy privilegovaný přístup pro správu konkrétní úlohy.


**Otázka:** existují jaké nástroje pro omezení přístupu trvalé správce?

**Odpověď:** Privileged Identity Management (PIM) a Azure AD role správce.


**Otázka:** co je Microsoft pozici na synchronizace účtů správce ke službě Azure AD?

**Odpověď:** vrstvy 0 účty správců (včetně účty, skupiny a dalších prostředků, které mají přímý nebo nepřímý administrativní řízení doménové struktuře Active Directory, domény nebo řadiče domény a všechny prostředky) jsou použity pouze pro místní účty AD a jsou synchronizace není obvykle pro Azure AD pro cloud. 


**Otázka:** jak jsme zachovat admins v přiřazení přístup náhodných správce portálu?

**Odpověď:** používání účtů bez oprávnění pro všechny uživatele a většina správce. Začít vývoj nároků organizace, určete, který by měl být privilegovaný několik účty správců. A monitorování pro nově vytvořené administrativní uživatelé.


## <a name="next-steps"></a>Další postup

* [Microsoft Trust Center pro produkt zabezpečení](https://www.microsoft.com/trustcenter/security) – funkce zabezpečení systému Microsoft cloud produktů a služeb

* [Microsoft Trust Center – dodržování předpisů](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – komplexní sadu nabídky dodržování předpisů pro cloudové služby společnosti Microsoft

* [Pokyny o tom, jak provést vyhodnocení rizik](https://www.microsoft.com/trustcenter/guidance/risk-assessment) -Spravovat požadavky na zabezpečení a dodržování předpisů pro cloudové služby Microsoftu

### <a name="other-ms-online-services"></a>Další Online služby MS 

* [Microsoft Intune zabezpečení](https://www.microsoft.com/trustcenter/security/intune-security) – Intune zajišťuje správu mobilních zařízení, Správa mobilních aplikací a možnosti správy počítačů z cloudu.

* [Zabezpečení Microsoft Dynamics 365](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 je cloudové řešení společnosti Microsoft, který kombinuje řízení vztahů se zákazníky (CRM) a (ERP) možnosti plánování podnikových zdrojů.

 
