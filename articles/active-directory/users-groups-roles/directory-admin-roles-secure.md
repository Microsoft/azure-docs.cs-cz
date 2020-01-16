---
title: Osvědčené postupy pro přístup zabezpečeným správcem – Azure AD | Microsoft Docs
description: Ujistěte se, že účty pro správu a přístup správce vaší organizace jsou zabezpečené. Pro systémové architekty a odborníky na IT, kteří konfigurují služby Azure AD, Azure a Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 11/13/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5f36e82c2ff95800a058f56f9cc6b80ddf02dbf
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75967713"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD

Zabezpečení většiny nebo všech podnikových prostředků v moderních organizacích závisí na integritě privilegovaných účtů, které spravují a spravují systémy IT. Škodlivé objekty actor, včetně počítačů, které jsou často cílené na účty správců, a další prvky privilegovaného přístupu, které se pokoušejí rychle získat přístup k citlivým datům a systémům pomocí útoků krádeže přihlašovacích údajů. Pro cloudové služby jsou prevence a reakce společné zodpovědnosti poskytovatele cloudové služby a zákazníka. Další informace o nejnovějších hrozbách koncových bodů a cloudu najdete v [sestavě Microsoft Security Intelligence](https://www.microsoft.com/security/operations/security-intelligence-report). Tento článek vám může pomoci při vývoji plánu pro uzavírání mezer mezi aktuálními plány a pokyny, které jsou zde popsané.

> [!NOTE]
> Společnost Microsoft se zavazuje k nejvyšší úrovni důvěryhodnosti, transparentnosti, dodržování standardů a dodržování předpisů. Přečtěte si další informace o tom, jak tým globálních odpovědí na incidenty od Microsoftu omezuje důsledky útoků na cloudové služby a jak je zabudované zabezpečení v obchodních produktech a cloudových službách Microsoftu v centru [zabezpečení Microsoftu –](https://www.microsoft.com/trustcenter/security) dodržování předpisů a cílů dodržování předpisů Microsoftu v [Centru zabezpečení Microsoftu – dodržování předpisů](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
Pro většinu organizací závisí zabezpečení obchodních prostředků na integritě privilegovaných účtů, které spravují a spravují systémy IT. Internetoví útočníci se zaměřují na privilegovaný přístup k systémům infrastruktury (například ke službě Active Directory a Azure Active Directory), aby získali přístup k citlivým datům organizace. 

Tradiční přístupy, které se zaměřují na zabezpečení počátečních a výstupních bodů sítě, protože primární hraniční zabezpečení je méně účinné kvůli nárůstu používání aplikací SaaS a osobních zařízení na internetu. Přirozenou náhradou obvodu zabezpečení sítě ve složitém moderním podniku jsou ověřovací a autorizační kontrolní mechanizmy ve vrstvě identity organizace.

Privilegované účty správců efektivně řídí toto nové "hraniční zabezpečení". Je důležité chránit privilegovaný přístup bez ohledu na to, jestli je prostředí místní, cloudové nebo hybridní místní a cloudové hostované služby. Ochrana přístupu pro správu proti určitému nežádoucí osoby vyžaduje, abyste si vybrali úplný a důkladné přístup k izolaci systémů vaší organizace před riziky. 

Zabezpečení privilegovaného přístupu vyžaduje změny

* Procesy, postupy správy a Správa znalostí
* Technické komponenty, jako jsou obrany hostitelů, ochrana účtů a Správa identit

Tento dokument se zaměřuje hlavně na vytvoření plánu zabezpečení identit a přístupu, které jsou spravované nebo nahlášené ve službě Azure AD, Microsoft Azure, Office 365 a dalších cloudových službách. Pro organizace, které mají místní účty pro správu, se podívejte na doprovodné materiály k místním a hybridnímu privilegovanému přístupu spravovanému ze služby Active Directory při [zajišťování privilegovaného přístupu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Pokyny v tomto článku se primárně vztahují k funkcím Azure Active Directory, které jsou zahrnuté v Azure Active Directory Premiumch plánech P1 a P2. Azure Active Directory Premium P2 je součástí EMS E5 Suite a Microsoft 365 E5 Suite. V těchto pokynech předpokládáme, že vaše organizace už má pro vaše uživatele zakoupené licence Azure AD Premium P2. Pokud tyto licence nemáte, nemusí se některé doprovodné materiály vztahovat na vaši organizaci. V celém tomto článku je také pojem globální správce (nebo globální správce) synonymem "Správce společnosti" nebo "správce tenanta".

## <a name="develop-a-roadmap"></a>Vývoj plánu 

Microsoft doporučuje vyvinout a postupovat podle plánu zabezpečení privilegovaného přístupu proti internetovým útočníkům. Plán můžete kdykoli upravit tak, aby vyhovoval vašim stávajícím funkcím a konkrétním požadavkům v rámci vaší organizace. Každá fáze plánu by měla zvýšit náklady a obtížnost nežádoucí osoby, aby mohl ohrozit privilegovaný přístup k místním, cloudovým a hybridním prostředkům. Microsoft doporučuje následující čtyři fáze plánu: Tento doporučený plán plánuje nejefektivnější a nejrychlejší implementaci na základě zkušeností Microsoftu s implementací incidentů a odpovědí na Internet. Časové osy pro tento plán jsou přibližné.

![Fáze plánu s časovými čárami](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Fáze 1 (24-48 hodin): kritické položky, které vám doporučujeme hned

* Fáze 2 (2-4 týdny): zmírnění nejčastěji používaných technik útoku

* Fáze 3 (1-3 měsíců): viditelnost sestavení a sestavení úplné řízení aktivity správce

* Fáze 4 (šest měsíců a mimo): pokračování v sestavování ochrany za účelem dalšího posílení zabezpečení platformy

Tento rámec plánu je navržený tak, aby maximalizoval používání technologií Microsoftu, které jste už možná nasadili. Můžete také využít výhod klíčových současných a nadcházejících technologií zabezpečení a integrovat nástroje zabezpečení od jiných dodavatelů, které jste již nasadili nebo uvažujete o jejich nasazení. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Fáze 1: kritické položky, které doporučujeme hned hned

![Fáze 1 nejdůležitějších položek, které se mají provést jako první](./media/directory-admin-roles-secure/stage-one.png)

Fáze 1 plánu se zaměřuje na kritické úkoly, které je možné rychle a snadno implementovat. Doporučujeme vám, abyste tyto položky provedli hned za prvních 24-48 hodin, abyste zajistili základní úroveň zabezpečeného privilegovaného přístupu. Tato fáze plánu zabezpečení privilegovaného přístupu zahrnuje tyto akce:

### <a name="general-preparation"></a>Obecná Příprava

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Zapnout Azure AD Privileged Identity Management

Pokud jste ještě nepovolili Azure AD Privileged Identity Management (PIM), udělejte to ve svém provozním tenantovi. Po zapnutí Privileged Identity Management obdržíte e-mailové zprávy s oznámením o změnách role privilegovaného přístupu. Tato oznámení poskytují včasné upozornění, když se do vašeho adresáře přidají další uživatelé do vysoce privilegovaných rolí.

Azure AD Privileged Identity Management je zahrnutý v Azure AD Premium P2 nebo EMS E5. Tato řešení vám pomůžou chránit přístup k aplikacím a prostředkům v místním prostředí i v cloudu. Pokud ještě nemáte Azure AD Premium P2 nebo EMS E5 a chcete vyhodnotit více funkcí, na které se odkazuje v tomto plánu, zaregistrujte se [Enterprise mobility + Security bezplatné zkušební verze 90](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Pomocí těchto zkušebních testů můžete Azure AD Privileged Identity Management a Azure AD Identity Protection vyzkoušet, abyste mohli sledovat aktivitu pomocí rozšířených sestav zabezpečení, auditování a výstrah v Azure AD.

Po zapnutí Azure AD Privileged Identity Management:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu, který je globálním správcem vašeho produkčního tenanta.

2. Chcete-li vybrat klienta, u kterého chcete použít Privileged Identity Management, vyberte své uživatelské jméno v pravém horním rohu Azure Portal.

3. V nabídce Azure Portal vyberte **všechny služby** a vyfiltrujte seznam pro **Azure AD Privileged Identity Management**.

4. V seznamu **všechny služby** otevřete Privileged Identity Management a připněte ho na řídicí panel.

První osoba, která se používá Azure AD Privileged Identity Management ve vašem tenantovi, má automaticky přiřazenou roli správce **zabezpečení** a **privilegované role** v tenantovi. Pouze správci privilegovaných rolí můžou spravovat přiřazení rolí uživatelů adresáře služby Azure AD. Po přidání Azure AD Privileged Identity Management se také zobrazí Průvodce zabezpečením, který vás provede počátečním zjišťováním a přiřazením. Průvodce můžete ukončit bez provedení jakýchkoli dalších změn. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identifikace a kategorizace účtů, které jsou ve vysoce privilegovaných rolích 

Po zapnutí Azure AD Privileged Identity Management se podívejte na uživatele, kteří jsou v adresáři role globálního správce, správce privilegovaných rolí, správce Exchange Online a správce SharePointu Online. Pokud ve vašem tenantovi nemáte PIM Azure AD, můžete použít [rozhraní PowerShell API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Začněte s rolí globálního správce, protože tato role je obecná: uživatel, který je přiřazený k této roli správce, má stejná oprávnění pro všechny cloudové služby, pro které má vaše organizace předplacené předplatné, bez ohledu na to, jestli jim byla tato role přiřazená v Microsoft 365. Centrum pro správu, Azure Portal nebo pomocí modulu Azure AD pro Microsoft PowerShell. 

Odeberte všechny účty, které už v těchto rolích nepotřebujete. Pak zařaďte do kategorií zbývající účty, které jsou přiřazené rolím Správce:

* Samostatně přiřazeno administrativním uživatelům a lze je také použít pro účely bez správy (například osobní e-mail).
* Přiřazeno individuálně správcům a určen pouze pro účely správy
* Sdíleno mezi více uživateli
* Pro scénáře nouzového přístupu k zábrusu
* Pro automatizované skripty
* Pro externí uživatele

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definice alespoň dvou účtů pro nouzový přístup 

Ujistěte se, že se nedostanete do situace, kdy by se nemusely nechtěně uzamknout ze správy vašeho tenanta Azure AD, protože se nepovedlo přihlásit nebo aktivovat existující účet individuálního uživatele jako správce. Pokud je například organizace federované pro místního zprostředkovatele identity, může být tento zprostředkovatel identity nedostupný, aby se uživatelé nemuseli přihlašovat místně. Uložením dvou nebo více účtů pro nouzový přístup ve vašem tenantovi můžete zmírnit dopad náhodného nedostatku oprávnění správce.

Účty pro nouzový přístup umožňují organizacím omezit privilegovaný přístup v rámci stávajícího Azure Active Directoryho prostředí. Tyto účty jsou vysoce privilegované a nepřiřazují se konkrétním jednotlivcům. Účty pro nouzový přístup jsou omezené na nouzový stav u scénářů "breakd", kde nelze použít běžné účty pro správu. Organizace musí zajistit, aby bylo možné řídit a snižovat využití účtu v nouzi jenom na dobu, kdy je to nezbytné.

Vyhodnoťte účty, které jsou přiřazeny nebo mají nárok na roli globálního správce. Pokud jste nezobrazili žádné účty jenom cloudu s použitím domény *. onmicrosoft.com (určené pro nouzový přístup "break sklo"), vytvořte je. Další informace najdete v tématu [Správa účtů pro správu pro nouzový přístup ve službě Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Zapnutí vícefaktorového ověřování a registrace všech dalších vysoce privilegovaných účtů pro jednoho uživatele, kteří nejsou federované

Vyžadovat Azure Multi-Factor Authentication (MFA) při přihlašování pro všechny jednotlivé uživatele, kteří jsou trvale přiřazeni k jedné nebo více rolím správce Azure AD: globální správce, Privileged role, správce Exchange Online a SharePoint Správce online. Pomocí průvodce povolte [vícefaktorové ověřování (MFA) pro účty správců](../authentication/howto-mfa-userstates.md) a zajistěte, aby všichni uživatelé zaregistrovali [https://aka.ms/mfasetup](https://aka.ms/mfasetup). Další informace najdete v části Krok 2 a 3. krok příručky průvodce [chránit přístup k datům a službám v Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Fáze 2: zmírnění nejčastěji používaných technik útoku

![Fáze 2 zmírňující často používané útoky](./media/directory-admin-roles-secure/stage-two.png)

Fáze 2 plánu se zaměřuje na zmírnění nejčastěji používaných technik útoků krádeže a zneužití přihlašovacích údajů a může být implementováno přibližně 2-4 týdnů. Tato fáze plánu zabezpečení privilegovaného přístupu zahrnuje následující akce.

### <a name="general-preparation"></a>Obecná Příprava

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Provádění inventáře služeb, vlastníků a správců

Díky nárůstu využití vlastního zařízení (BYOD) a zásad práce z domova a růstu bezdrátového připojení v podnicích je důležité monitorovat, kdo se k síti připojuje. Efektivní audit zabezpečení často odhaluje zařízení, aplikace a programy běžící v síti, které nepodporují, a proto potenciálně nezabezpečují. Další informace najdete v tématu [Přehled správy a monitorování zabezpečení Azure](../../security/fundamentals/management-monitoring-overview.md). Ujistěte se, že v procesu inventarizace zahrnete všechny následující úkoly. 

* Identifikujte uživatele, kteří mají administrativní role a služby, kde mohou spravovat.
* Pomocí Azure AD PIM zjistíte, kteří uživatelé ve vaší organizaci mají přístup správce ke službě Azure AD, včetně dalších rolí, které nejsou uvedené v kroku 1.
* Mimo role definované v Azure AD obsahuje sada Office 365 sadu rolí správce, které můžete přiřadit uživatelům ve vaší organizaci. Každá role správce je namapována na běžné obchodní funkce a poskytuje lidem ve vaší organizaci oprávnění provádět konkrétní úkoly v [centru pro správu Microsoft 365](https://admin.microsoft.com). Pomocí centra pro správu Microsoft 365 Zjistěte, kteří uživatelé ve vaší organizaci mají přístup správce k Office 365, včetně rolí, které nejsou spravované v Azure AD. Další informace najdete v tématech [o rolích správce systému Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) a [osvědčených postupech zabezpečení pro sadu Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Proveďte inventarizaci v jiných službách, na kterých vaše organizace spoléhá, jako je Azure, Intune nebo Dynamics 365.
* Ujistěte se, že účty správců (účty používané pro účely správy, ne jenom pro každodenní účty uživatelů) obsahují pracovní e-mailové adresy, které jsou k nim připojené a které jsou zaregistrované pro Azure MFA, nebo místní ověřování pomocí MFA.
* Požádejte uživatele o obchodní odůvodnění pro přístup pro správu.
* Odeberte přístup správce pro ty jednotlivce a služby, které ho nepotřebují.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifikujte účty Microsoft v rolích pro správu, které je potřeba přepnout na pracovní nebo školní účty.

V některých případech budou počáteční globální správci pro organizaci při zahájení používání Azure AD znovu používat svoje existující účet Microsoft přihlašovací údaje. Tyto účty Microsoft by měly být nahrazené jednotlivými cloudy nebo synchronizovanými účty. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Zajistěte samostatné uživatelské účty a přesměrování pošty pro účty globálních správců. 

Účty globálních správců by neměly mít osobní e-mailové adresy, protože osobní e-mailové účty jsou pravidelně podvodné prostřednictvím internetoví útočníků. Chcete-li pomoci oddělit Internetová rizika (útoky typu phishing, neúmyslné procházení webu) od oprávnění správce, vytvořte pro každého uživatele vyhrazené účty s oprávněními správce. 

Pokud jste to ještě neudělali, vytvořte samostatné účty pro uživatele, které provádějí úlohy globálního správce, abyste se ujistili, že nechtěně neotevřou e-maily nebo nespouštějí programy přidružené k účtům správců. Ujistěte se, že tyto účty mají e-maily předané pracovní schránce.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Zajistěte, aby se nedávno změnila hesla účtů pro správu.

Zajistěte, aby se všichni uživatelé přihlásili ke svým účtům pro správu a v posledních 90 dnech změnili hesla aspoň jednou. Také se ujistěte, že se hesla v poslední době změnila u všech sdílených účtů, ve kterých se heslo ví více uživatelů.

#### <a name="turn-on-password-hash-synchronization"></a>Zapnout synchronizaci hodnot hash hesel

Synchronizace hodnot hash hesel je funkce používaná k synchronizaci hodnot hash uživatelských hesel z místní instance služby Active Directory s instancí Azure AD založené na cloudu. I v případě, že se rozhodnete použít federaci s Active Directory Federation Services (AD FS) (AD FS) nebo jinými zprostředkovateli identity, můžete volitelně nastavit synchronizaci hodnot hash hesel jako zálohu v případě selhání místní infrastruktury, jako jsou servery AD nebo ADFS, nebo se může jednat o dočasně nedostupné. To uživatelům umožňuje přihlásit se ke službě pomocí stejného hesla, které používají k přihlášení do své místní instance služby AD. Umožňuje taky službě Identity Protection detekovat napadené přihlašovací údaje porovnáním hodnot hash hesel, u kterých se zjistilo ohrožení zabezpečení, pokud uživatel využil stejnou e-mailovou adresu a heslo v jiných službách, které nejsou připojené ke službě Azure AD.  Další informace najdete v tématu [implementace synchronizace hodnot hash hesel pomocí Azure AD Connect synchronizace](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Vyžadovat vícefaktorové ověřování (MFA) pro uživatele ve všech privilegovaných rolích a také vyexponovaných uživatelů

Azure AD doporučuje vyžadovat službu Multi-Factor Authentication (MFA) pro všechny uživatele, včetně správců a všech dalších uživatelů, kteří by měli výrazný dopad, pokud došlo k ohrožení jejich účtu (například finančních důstojníků). Tím se snižuje riziko útoku kvůli napadenému heslu.

Zapnout:

* [Vícefaktorové ověřování pomocí zásad podmíněného přístupu](../authentication/howto-mfa-getstarted.md) pro všechny uživatele ve vaší organizaci.

Pokud používáte Windows Hello pro firmy, požadavek MFA se dá splnit pomocí přihlašovacích zkušeností Windows Hello. Další informace najdete v tématu [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Konfigurace identity Protection 

Azure AD Identity Protection je nástroj pro monitorování a vytváření sestav založený na algoritmech, který můžete použít k detekci potenciálních ohrožení zabezpečení, které mají vliv na identity vaší organizace. Můžete nakonfigurovat automatizované odpovědi na ty zjištěné podezřelé aktivity a provést odpovídající opatření k jejich vyřešení. Další informace najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Získání zabezpečeného skóre Office 365 (při použití Office 365)

V rámci zabezpečeného skóre se dozvíte, jaké služby Office 365 používáte (jako je OneDrive, SharePoint a Exchange), a potom se podíváme na vaše nastavení a aktivity a porovnávají je se směrným plánem vytvořeným Microsoftem. Získáte skóre na základě toho, jak jste se s osvědčenými postupy zabezpečení dostali. Každý, kdo má oprávnění správce (globální správce nebo vlastní role správce) pro předplatné Office 365 Business Premium nebo Enterprise, má přístup k zabezpečenému skóre na [https://securescore.office.com](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Přečtěte si pokyny pro zabezpečení a dodržování předpisů Office 365 (Pokud používáte Office 365).

[Plán pro zabezpečení a dodržování předpisů](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) popisuje přístup k tomu, jak by zákazník Office 365 měl nakonfigurovat Office 365 a využívat další možnosti EMS. Pak si přečtěte kroky 3-6 o tom, jak [chránit přístup k datům a službám v sadě office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) a průvodce pro [monitorování zabezpečení a dodržování předpisů v sadě Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Konfigurace monitorování aktivit Office 365 (při použití Office 365)

Můžete sledovat, jak lidé ve vaší organizaci používají služby Office 365, což vám umožní identifikovat uživatele, kteří mají účet správce a kteří nemusí mít přístup k Office 365, protože se k těmto portálům přihlašuje. Další informace najdete v tématu [sestavy aktivit v centru pro správu Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Navázání vlastníků plánů pro incidenty nebo naléhavé reakce

Efektivní provádění reakce na incidenty je složitým podnikem. Proto je potřeba, abyste nahlásili úspěšné schopnosti reakce na incidenty, vyžadovaly zásadní plánování a prostředky. Je důležité, abyste nepřetržitě sledovali počítačové útoky a stanovili postupy pro stanovení priorit zpracování incidentů. Efektivní metody shromažďování, analýzy a vytváření sestav jsou důležité pro vytváření vztahů a navázání komunikace s dalšími interními skupinami a vlastníky plánů. Další informace najdete v tématu [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Zabezpečení místních privilegovaných účtů správců, pokud se ještě neudělaly

Pokud je váš tenant Azure Active Directory synchronizovaný s místní službou Active Directory, postupujte podle pokynů v části [Průvodce zabezpečením privilegovaného přístupu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): fáze 1. To zahrnuje vytvoření samostatných účtů správců pro uživatele, kteří potřebují provádět místní úlohy správy, nasazovat pracovní stanice s privilegovaným přístupem pro správce služby Active Directory a vytvářet jedinečná hesla místních správců pro pracovní stanice a servery.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Další kroky pro organizace, které spravují přístup k Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Dokončení inventarizace předplatných

Použijte portál Enterprise a Azure Portal k identifikaci předplatných ve vaší organizaci, které hostují produkční aplikace.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Odebrání účtů Microsoft z rolí správce

Účty Microsoft z jiných programů, například Xbox, Live a Outlook, by se neměly používat jako účty správců pro předplatná organizace. Odeberte stav správce ze všech účtů Microsoft a nahraďte je Azure Active Directory (například chris@contoso.com) pracovními nebo školními účty.

#### <a name="monitor-azure-activity"></a>Monitorování aktivity Azure

Protokol aktivit Azure poskytuje historii událostí na úrovni předplatného v Azure. Nabízí informace o tom, kdo vytvořil, aktualizoval a odstranil prostředky a kdy k těmto událostem došlo. Další informace najdete v tématu [auditování a příjem oznámení o důležitých akcích ve vašem předplatném Azure](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Další kroky pro organizace, které spravují přístup k jiným cloudovým aplikacím přes Azure AD

#### <a name="configure-conditional-access-policies"></a>Konfigurace zásad podmíněného přístupu

Připravte zásady podmíněného přístupu pro místní a cloudové aplikace hostované v cloudu. Pokud máte zařízení připojená k síti na pracovišti, získáte další informace z [nastavení místního podmíněného přístupu pomocí registrace zařízení Azure Active Directory](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Fáze 3: sestavovat viditelnost a převzít úplnou kontrolu nad aktivitou správy

![Fáze 3 převzít kontrolu nad aktivitou správy](./media/directory-admin-roles-secure/stage-three.png)

Fáze 3 navazuje na zmírnění rizik z fáze 2 a je navržená tak, aby se implementovala přibližně 1-3 měsíců. Tato fáze plánu zabezpečení privilegovaného přístupu zahrnuje následující součásti.

### <a name="general-preparation"></a>Obecná Příprava

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Dokončení kontroly přístupu uživatelů v rolích správce

Více firemních uživatelů získává privilegovaný přístup prostřednictvím cloudových služeb, což může vést ke zvýšení nespravované platformy. Zahrnuje to i uživatele globální správci pro Office 365, Správce předplatného Azure a uživatele, kteří mají přístup správce k virtuálním počítačům nebo prostřednictvím aplikací SaaS. Místo toho by organizace měli mít všechny zaměstnance, zejména správci, zpracovávat každodenní obchodní transakce jako neprivilegovaných uživatelů a provádět v případě potřeby pouze práva správce. Vzhledem k tomu, že počet uživatelů v rolích správce se mohl od počátečního přijetí vypěstovat, dokončete kontroly přístupu a ověřte a potvrďte každého uživatele, který má nárok na aktivaci oprávnění správce. 

Udělejte toto:

* Určete, kteří uživatelé jsou správci Azure AD, umožněte na vyžádání přístup správce za běhu a řízení zabezpečení na základě rolí.
* Převeďte uživatele, kteří nemají žádné jasné zdůvodnění přístupu správce k jiné roli (Pokud žádnou z oprávněných rolí neodeberete).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Pokračovat v zavedení silnějšího ověřování pro všechny uživatele 

Vyžadují vedoucí pracovníky pro správu, vysoké úrovně, kritické pracovníky IT a zabezpečení a další vysoce vyexponované uživatelé, kteří mají moderní, silné ověřování, jako je Azure MFA nebo Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Použití vyhrazených pracovních stanic pro správu Azure AD

Útočníci se můžou pokusit cílit na privilegované účty a získat tak přístup k datům a systémům organizace, aby mohli narušit integritu a pravost dat, a to prostřednictvím škodlivého kódu, který mění logiku programu nebo snoopuje správce zadání přihlašovacích údajů. Pracovní stanice s privilegovaným přístupem poskytují vyhrazený operační systém pro citlivé úlohy, který je chráněný před útoky z internetu a jinými vektory hrozeb. Oddělení těchto citlivých úloh a účtů od každodenních pracovních stanic a zařízení zajišťuje velmi silnou ochranu před útoky typu phishing, ohrožení zabezpečení aplikací a operačních systémů, různých útoků na zosobnění a útoků krádeže přihlašovacích údajů, jako je třeba klávesová zkratka. Logging, pass-the-hash a Pass-The-Ticket. Nasazením přístupových pracovních stanic s privilegovaným přístupem můžete snížit riziko, že správci zadávají přihlašovací údaje správce, s výjimkou prostředí, které bylo posílené. Další informace najdete v tématu [pracovní stanice s privilegovaným přístupem](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Kontrola Národního institutu standardů a technologických doporučení pro zpracování incidentů 

National Institute of Standards and Technology (NIST) poskytuje pokyny pro zpracování incidentů, zejména pro analýzu dat týkajících se incidentů a určení vhodné reakce na jednotlivé incidenty. Další informace najdete v tématu [Příručka pro zpracování incidentů zabezpečení počítače (NIST) (SP 800-61, revize 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementace Privileged Identity Management (PIM) pro JIT v dalších rolích pro správu

Pro Azure Active Directory použijte možnost [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) . Časově omezená aktivace privilegovaných rolí funguje tak, že vám umožní:

* Aktivace oprávnění správce k provedení konkrétního úkolu
* Vynutil MFA během procesu aktivace
* Informování správců o změnách v nedostatku pásma pomocí výstrah
* Umožnění uživatelům zachovat určitá oprávnění pro předem nakonfigurované množství času
* Umožněte správcům zabezpečení zjistit všechny privilegované identity, zobrazit sestavy auditu a vytvořit kontroly přístupu k identifikaci každého uživatele, který má nárok na aktivaci oprávnění správce.

Pokud již používáte Azure AD Privileged Identity Management, upravte časové rámce pro oprávnění s časovou vazbou podle potřeby (například časová období údržby).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Určení vystavení přihlašovacích protokolů založených na heslech (Pokud používáte Exchange Online)

V minulosti protokoly předpokládaly, že kombinace uživatelského jména a hesla byly vloženy do zařízení, e-mailových účtů, telefonů atd. Ale teď s rizikem pro počítačové útoky v cloudu doporučujeme identifikovat každého potenciálního uživatele, který, pokud by jejich přihlašovací údaje byly ohroženy, může být pro organizaci nenáročné a vyloučit je z možností přihlášení k e-mailu pomocí uživatelského jména a hesla implementací požadavků silného ověřování a podmíněného přístupu. [Pomocí podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication)můžete zablokovat starší ověřování. Podrobnosti o [tom, jak blokovat základní ověřování](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) prostřednictvím Exchange Online, najdete v podrobnostech. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Dokončení vyhodnocení přezkoumání rolí u rolí Office 365 (Pokud používáte Office 365)

Vyhodnoťte, jestli všichni správci jsou ve správných rolích (odstraňte a znovu přiřadíte podle tohoto posouzení).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Projděte si přístup ke správě incidentů zabezpečení, který se používá v Office 365 a porovnejte s vaší vlastní organizací.

Tuto sestavu můžete stáhnout ze [správy incidentů zabezpečení v systém Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Pokračovat v zabezpečení místních privilegovaných účtů pro správu

Pokud je váš Azure Active Directory připojený k místní službě Active Directory, postupujte podle pokynů v části [plán zabezpečení privilegovaného přístupu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): fáze 2. To zahrnuje nasazení přístupových stanic privilegovaného přístupu pro všechny správce, vyžadování MFA, použití jenom dostatečného správce pro údržbu řadiče domény, snížení prostoru pro útoky na útoky a nasazení ATA pro detekci útoků.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Další kroky pro organizace, které spravují přístup k Azure

#### <a name="establish-integrated-monitoring"></a>Zavedení integrovaného monitorování

[Azure Security Center](../../security-center/security-center-intro.md) poskytuje integrované monitorování zabezpečení a správu zásad napříč předplatnými Azure, pomáhá detekovat hrozby, které by jinak neinformovaly a pracují s širokou ekosystémem řešení zabezpečení.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventarizaci privilegovaných účtů v rámci hostovaných Virtual Machines

Ve většině případů nemusíte uživatelům poskytovat neomezená oprávnění k vašim předplatným nebo prostředkům Azure. Role správce Azure AD můžete použít k oddělení povinností v rámci vaší organizace a udělení přístupu jenom uživatelům, kteří potřebují provádět určité úlohy. Například použijte role správce Azure AD, aby jeden správce mohl spravovat jenom virtuální počítače v rámci předplatného, zatímco jiný může spravovat databáze SQL v rámci stejného předplatného. Další informace najdete v tématu [Začínáme s Access Control na základě rolí v Azure Portal](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementace rolí PIM pro správce Azure AD

Využijte Privileged Identity Management s rolemi správce Azure AD ke správě, řízení a monitorování přístupu k prostředkům Azure. Použití PIM chrání privilegované účty před internetovými útoky tím, že snižuje dobu expozice oprávnění a zvyšuje viditelnost jejich používání prostřednictvím sestav a výstrah. Další informace najdete v tématu [Správa přístupu RBAC k prostředkům Azure pomocí Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Použití integrace protokolů Azure k posílání relevantních protokolů Azure do systémů SIEM 

Integrace protokolů Azure umožňuje integrovat nezpracované protokoly z vašich prostředků Azure do stávajících systémů zabezpečení a SIEM (Security Information and Event Management) vaší organizace. [Integrace protokolu Azure](../../security/fundamentals/azure-log-integration-overview.md) shromažďuje události Windows z protokolů Prohlížeč událostí Windows a prostředků Azure z protokolů aktivit Azure, výstrah Azure Security Center a diagnostických protokolů Azure. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Další kroky pro organizace, které spravují přístup k jiným cloudovým aplikacím přes Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementace zřizování uživatelů pro připojené aplikace

Azure AD umožňuje automatizovat vytváření, údržbu a odebírání identit uživatelů v cloudových aplikacích (SaaS), jako jsou Dropbox, Salesforce, ServiceNow a tak dále. Další informace najdete v tématu [Automatizace zřizování a rušení uživatelů při SaaS aplikací pomocí Azure AD](../manage-apps/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrace ochrany informací

MCAS umožňuje prozkoumat soubory a nastavovat zásady na základě popisků klasifikace Azure Information Protection a umožnit tak větší viditelnost a kontrolu nad daty v cloudu. Prohledávání a klasifikace souborů v cloudu a použití popisků služby Azure Information Protection. Další informace najdete v tématu [integrace služby Azure Information Protection](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurace podmíněného přístupu

Nakonfigurujte podmíněný přístup na základě skupiny, umístění a citlivosti aplikace pro aplikace [SaaS](https://azure.microsoft.com/overview/what-is-saas/) a aplikace připojené k Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorování aktivity v připojených cloudových aplikacích

Abyste měli jistotu, že je přístup uživatelů chráněný i v připojených aplikacích, doporučujeme využít [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Tím se zabezpečení přístupu ke cloudovým aplikacím zabezpečuje kromě zabezpečení účtů správců tím, že vám umožní:

* Rozšiřování viditelnosti a řízení pro cloudové aplikace
* Vytvoření zásad pro přístup, aktivity a sdílení dat
* Automaticky identifikovat rizikové aktivity, neobvyklé chování a hrozby
* Zabránit úniku dat
* Minimalizace rizik a automatizované ochrany před hrozbami a vynucení zásad

Agent Cloud App Security SIEM integruje Cloud App Security se serverem SIEM, aby umožnil centralizované monitorování výstrah a aktivit Office 365. Spouští se na vašem serveru a vybírá výstrahy a aktivity z Cloud App Security a streamuje je na server SIEM. Další informace najdete v tématu [integrace řešení SIEM](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Fáze 4: pokračování v budování ochrany pro aktivnější stav zabezpečení

![Fáze 4 – přijetí proaktivní stav zabezpečení](./media/directory-admin-roles-secure/stage-four.png)

Fáze 4 plánu sestaví na viditelnosti ze fáze 3 a je navržena tak, aby se implementovala po dobu šesti měsíců a později. Dokončení plánu vám pomůže vyvíjet silnou ochranu privilegovaného přístupu od potenciálních útoků, které jsou aktuálně známé a dostupné dnes. Bezpečnostní hrozby se ale neustále víjejí a posunují, takže doporučujeme zobrazit zabezpečení jako probíhající proces zaměřený na zvýšení nákladů a snížení míry úspěšnosti nežádoucí osoby, která cílí na vaše prostředí.

Zabezpečení privilegovaného přístupu je důležitým prvním krokem při vytváření bezpečnostních ujištění pro obchodní prostředky v moderní organizaci, ale není to jedinou součástí kompletního programu zabezpečení, který by zahrnoval prvky, jako jsou například zásady, operace, informace zabezpečení, servery, aplikace, počítače, zařízení, cloudové prostředky a další komponenty poskytují průběžné záruky zabezpečení. 

Kromě správy privilegovaných účtů pro přístup doporučujeme, abyste si průběžně provedli následující kroky:

* Zajistěte, aby správci prováděli své každodenní obchody jako Neprivilegovaní uživatelé.
* Udělte přístup k privilegovanému přístupu v případě potřeby a později ho odeberte (za běhu).
* Zachování a kontrola aktivit auditu týkajících se privilegovaných účtů.

Další informace o vytvoření kompletního plánu zabezpečení najdete v tématu [prostředky architektury Cloud IT v Microsoftu](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Další informace o zapojení služeb společnosti Microsoft, které vám pomůžou s některým z těchto témat, vám poskytne zástupce Microsoftu nebo si přečtěte téma [Vytvoření kritické obrany pro Internet, která chrání vaše podnikání](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Tato poslední nepřetržitá fáze plánu zabezpečení privilegovaného přístupu zahrnuje následující součásti.

### <a name="general-preparation"></a>Obecná Příprava

#### <a name="review-admin-roles-in-azure-active-directory"></a>Kontrola rolí správců v Azure Active Directory 

Zjistěte, jestli jsou aktuální předdefinované role správce Azure AD pořád aktuální, a zajistěte, aby se uživatelé nastavili jenom v rolích a delegováních, které potřebují k odpovídajícím oprávněním. Pomocí Azure AD můžete určit samostatné správce pro poskytování různých funkcí. Další informace najdete v tématu [přiřazení rolí správce v Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Kontrola uživatelů, kteří mají správu zařízení připojených k Azure AD

Další informace najdete v tématu [jak nakonfigurovat zařízení připojená k hybridním Azure Active Directory](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Kontrola členů [předdefinovaných rolí správce sady Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Pokud používáte Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Ověřit plán reakce na incidenty

Pro zlepšení plánu vám Microsoft doporučuje pravidelně ověřovat, jestli váš plán funguje podle očekávání:

* Projděte si stávající mapu cest a podívejte se, co se vynechalo.
* V závislosti na analýze Postmortem si provedete revizi stávajících nebo definujte nové osvědčené postupy.
* Ujistěte se, že aktualizovaný plán odpovědí na incidenty a osvědčené postupy jsou distribuované napříč vaší organizací.


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Další kroky pro organizace, které spravují přístup k Azure 

Určete, jestli potřebujete [přenášet vlastnictví předplatného Azure na jiný účet](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Break sklo": co dělat v nouzi

![Účty pro přístup do nouzového skla](./media/directory-admin-roles-secure/emergency.jpeg)

1. Informujte Správce klíčů a bezpečnostní pracovníky o relevantních informacích týkajících se incidentu.

2. Prohlédněte si PlayBook útoku. 

3. Přihlaste se ke svojí kombinaci uživatelského jména a hesla účtu "break sklo", abyste se mohli přihlásit ke službě Azure AD. 

4. Získejte pomoc od Microsoftu [otevřením žádosti o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Podívejte se na [sestavy přihlášení ke službě Azure AD](../reports-monitoring/overview-reports.md). Může existovat prodleva mezi výskytem události a jejich zahrnutím do sestavy.

6. V hybridních prostředích, pokud není k dispozici federovaný a váš AD FS Server, možná budete muset dočasně přepnout z federovaného ověřování na použití synchronizace hodnot hash hesel. Tím se vrátí doménová federace zpátky do spravovaného ověřování, dokud nebude server AD FS k dispozici.

7. Monitorujte e-mail pro privilegované účty.

8. Nezapomeňte uložit zálohy příslušných protokolů pro případné forenzní a soudní šetření.

Další informace o tom, jak systém Microsoft Office 365 zpracovává incidenty zabezpečení, najdete [v tématu Správa incidentů zabezpečení v systém Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Nejčastější dotazy: nejčastější dotazy týkající se zabezpečení privilegovaného přístupu  

**Otázka:** Jak mám postupovat, pokud jsem ještě neimplementoval nějaké součásti zabezpečeného přístupu?

**Odpověď:** Definujte aspoň dva účty pro přerušení, přiřaďte MFA účtům privilegovaného správce a oddělte uživatelské účty z účtů globálních správců.

**Otázka:** Jaký je hlavní problém, který je třeba vyřešit jako první, co je v rozporu?

**Odpověď:** Ujistěte se, že požadujete nejsilnější ověřování pro vysoce exponované uživatele.

**Otázka:** Co se stane, když jsme deaktivovali naše privilegované správce?

**Odpověď:** Vytvořte účet globálního správce, který je vždy aktuální.

**Otázka:** Co se stane, když je nalevo jenom jeden globální správce a nedají se k němu získat přístup? 

**Odpověď:** K získání okamžitého privilegovaného přístupu použijte jeden z vašich účtů se systémem Break.

**Otázka:** Jak můžu chránit správce v mojí organizaci?

**Odpověď:** Správci mají vždycky v běžném podnikání jako standardní "Neprivilegovaný" uživatel.

**Otázka:** Jaké jsou osvědčené postupy pro vytváření účtů správců v rámci služby Azure AD?

**Odpověď:** Rezervujte privilegovaný přístup pro konkrétní úlohy správce.

**Otázka:** Jaké nástroje existují pro omezení trvalého přístupu správce?

**Odpověď:** Privileged Identity Management (PIM) a role správce Azure AD.

**Otázka:** Jaká je Microsoft pozice při synchronizaci účtů správců se službou Azure AD?

**Odpověď:** Účty správců vrstvy 0 (včetně účtů, skupin a dalších prostředků, které mají přímou nebo nepřímou administrativní kontrolu nad doménovou strukturou služby AD, doménami nebo řadiči domény a všemi prostředky), jsou využívány pouze pro místní účty služby AD a nejsou obvykle synchronizovány pro službu Azure AD pro Cloud.

**Otázka:** Jak správcům bráníme v přiřazení náhodného přístupu správce na portálu?

**Odpověď:** Používejte neprivilegované účty pro všechny uživatele a většinu správců. Začněte tím, že vyvíjíte řadu organizací, abyste zjistili, který z nich by měl mít oprávnění k privilegovanému účtu správce. A monitorujte nově vytvořené administrativní uživatele.

## <a name="next-steps"></a>Další kroky

* [Microsoft Trust Center pro zabezpečení produktů](https://www.microsoft.com/trustcenter/security) – funkce zabezpečení cloudových produktů a služeb Microsoftu

* [Centrum zabezpečení Microsoftu – dodržování předpisů](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – komplexní sada nabídek dodržování předpisů od Microsoftu pro cloudové služby

* [Pokyny k provedení posouzení rizik](https://www.microsoft.com/trustcenter/guidance/risk-assessment) – Správa požadavků na zabezpečení a dodržování předpisů pro cloudové služby Microsoftu

### <a name="other-microsoft-online-services"></a>Další online služby Microsoftu

* [Microsoft Intune zabezpečení](https://www.microsoft.com/trustcenter/security/intune-security) – Intune poskytuje možnosti správy mobilních zařízení, správy mobilních aplikací a počítačů z cloudu.

* [Microsoft Dynamics 365 Security](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 je cloudové řešení Microsoftu, které sjednocuje možnosti pro správu vztahů se zákazníky (CRM) a plánování podnikových zdrojů (ERP).
