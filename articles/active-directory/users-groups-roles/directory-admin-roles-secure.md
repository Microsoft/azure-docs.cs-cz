---
title: Doporučené postupy pro zabezpečený přístup správce – Azure AD | Dokumenty společnosti Microsoft
description: Ujistěte se, že jsou účty pro správu a správce vaší organizace zabezpečené. Pro systémové architekty a IT profesionály, kteří konfigurují Azure AD, Azure a Služby Microsoft Online Services.
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
ms.openlocfilehash: 1c46facb2d43137175730bf04fea0efec9c1ecbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266270"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD

Zabezpečení většiny nebo všech obchodních aktiv v moderní organizaci závisí na integritě privilegovaných účtů, které spravují a spravují it systémy. Zlomyslní aktéři, včetně kybernetických útočníků, se často zaměřují na účty správců a další prvky privilegovaného přístupu a snaží se rychle získat přístup k citlivým datům a systémům pomocí útoků na krádeže přihlašovacích údajů. U cloudových služeb jsou prevence a reakce společnou odpovědností poskytovatele cloudových služeb a zákazníka. Další informace o nejnovějších hrozbách pro koncové body a cloud naleznete v [microsoft security intelligence report](https://www.microsoft.com/security/operations/security-intelligence-report). Tento článek vám může pomoci vytvořit plán k odstranění mezer mezi aktuálními plány a pokyny popsanými zde.

> [!NOTE]
> Společnost Microsoft se zavázala k nejvyšší úrovni důvěryhodnosti, transparentnosti, shody standardů a dodržování předpisů. Další informace o tom, jak globální tým společnosti Microsoft pro reakci na incidenty zmírňuje dopady útoků na cloudové služby a jak je zabezpečení integrováno do obchodních produktů a cloudových služeb společnosti [Microsoft,](https://www.microsoft.com/trustcenter/security) najdete v centru zabezpečení společnosti Microsoft – cíle zabezpečení a dodržování předpisů společnosti Microsoft v Centru zabezpečení společnosti Microsoft [.](https://www.microsoft.com/trustcenter/compliance)

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
U většiny organizací závisí zabezpečení obchodních aktiv na integritě privilegovaných účtů, které spravují a spravují systémy IT. Počítačoví útočníci se zaměřují na privilegovaný přístup k infrastrukturním systémům (jako je služba Active Directory a služba Azure Active Directory), aby získali přístup k citlivým datům organizace. 

Tradiční přístupy, které se zaměřují na zabezpečení vstupních a výstupních bodů sítě jako primárního bezpečnostního obvodu, jsou méně účinné kvůli nárůstu používání aplikací SaaS a osobních zařízení na internetu. Přirozenou náhradou obvodu zabezpečení sítě ve složitém moderním podniku jsou ověřovací a autorizační kontrolní mechanizmy ve vrstvě identity organizace.

Privilegované účty pro správu mají fakticky kontrolu nad tímto novým "bezpečnostním perimetrem". Je důležité chránit privilegovaný přístup, bez ohledu na to, zda je prostředí místní, cloudové nebo hybridní místní a cloudové hostované služby. Ochrana přístupu správce před určenými protivníky vyžaduje, abyste k izolaci systémů vaší organizace před riziky zaujali úplný a promyšlený přístup. 

Zabezpečení privilegovaného přístupu vyžaduje změny

* Procesy, administrativní postupy a řízení znalostí
* Technické součásti, jako je obrana hostitele, ochrana účtů a správa identit

Tento dokument se zaměřuje především na vytvoření plánu pro zabezpečení identit a přístupu, které jsou spravované nebo hlášené ve službě Azure AD, Microsoft Azure, Office 365 a dalších cloudových službách. Organizace, které mají místní účty pro správu, najdete v pokynech pro místní a hybridní privilegovaný přístup spravovaný ze služby Active Directory na [webu Zabezpečení privilegovaného přístupu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Pokyny v tomto článku odkazuje především na funkce služby Azure Active Directory, které jsou součástí plánů Azure Active Directory Premium P1 a P2. Azure Active Directory Premium P2 je součástí sady EMS E5 a sady Microsoft 365 E5. Tyto pokyny předpokládají, že vaše organizace už má licence Azure AD Premium P2 zakoupené pro vaše uživatele. Pokud tyto licence nemáte, některé pokyny se nemusí vztahovat na vaši organizaci. V celém tomto článku je také termín globální správce (nebo globální správce) synonymem pro "správce společnosti" nebo "správce klienta".

## <a name="develop-a-roadmap"></a>Vypracovat plán 

Společnost Microsoft doporučuje, abyste vyvinuli a dodržovali plán zabezpečení privilegovaného přístupu proti kybernetickým útočníkům. Vždy můžete upravit plán tak, aby vyhovoval vašim stávajícím možnostem a specifickým požadavkům ve vaší organizaci. Každá fáze plánu by měla zvýšit náklady a obtížnost pro protivníky k útoku na privilegovaný přístup pro vaše místní, cloudové a hybridní prostředky. Společnost Microsoft doporučuje následující čtyři fáze plánu: Tento doporučený plán nejprve naplánuje nejúčinnější a nejrychlejší implementaci na základě zkušeností společnosti Microsoft s kybernetickými útoky a implementací reakce. Časové osy tohoto plánu jsou přibližné.

![Etapy plánu s časovými liniemi](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Fáze 1 (24-48 hodin): Kritické položky, které doporučujeme udělat hned

* Fáze 2 (2-4 týdny): Zmírnění nejčastěji používaných útočných technik

* Fáze 3 (1-3 měsíce): Vytvářejte viditelnost a vytvářejte plnou kontrolu nad aktivitou správce

* Fáze 4 (šest měsíců a dále): Pokračujte v budování obrany, abyste dále zpevnili bezpečnostní platformu

Tento plán rozhraní je navržen tak, aby maximalizoval využití technologií společnosti Microsoft, které jste již nasadili. Můžete také využít klíčové aktuální a nadcházející technologie zabezpečení a integrovat nástroje zabezpečení od jiných dodavatelů, které jste již nasadili nebo zvažují nasazení. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Fáze 1: Kritické položky, které doporučujeme udělat hned

![Fáze 1 Kritické položky, které je třeba provést jako první](./media/directory-admin-roles-secure/stage-one.png)

Fáze 1 plánu je zaměřena na kritické úkoly, které jsou rychlé a snadno implementovatelné. Doporučujeme provést těchto několik položek hned během prvních 24-48 hodin k zajištění základní úroveň zabezpečeného privilegovaného přístupu. Tato fáze plánu zabezpečeného privilegovaného přístupu zahrnuje následující akce:

### <a name="general-preparation"></a>Obecná příprava

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Zapnutí správy privilegovaných identit Azure AD

Pokud jste ještě nezapnuli Azure AD Privileged Identity Management (PIM), učiňte tak ve vašem produkčním tenantovi. Po zapnutí správy privilegovaných identit obdržíte e-mailové zprávy s oznámením o změnách rolí privilegovaného přístupu. Tato oznámení poskytují včasné upozornění, pokud jsou další uživatelé přidáni do vysoce privilegovaných rolí ve vašem adresáři.

Azure AD Privilegovaná správa identit je součástí Azure AD Premium P2 nebo EMS E5. Tato řešení vám pomohou chránit přístup k aplikacím a prostředkům v místním prostředí a do cloudu. Pokud ještě nemáte Azure AD Premium P2 nebo EMS E5 a chcete vyhodnotit další funkce uvedené v tomto plánu, zaregistrujte se k [90denní zkušební verzi Enterprise Mobility + Security free](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Pomocí těchto zkušebních verzí licencí vyzkoušejte Azure AD Privileged Identity Management a Azure AD Identity Protection, abyste sledovali aktivitu pomocí rozšířeného vytváření sestav zabezpečení Azure AD, auditování a výstrah.

Po zapnutí Azure AD Privilegované správy identit:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí účtu, který je globálním správcem vašeho produkčního tenanta.

2. Pokud chcete vybrat klienta, u kterého chcete používat správu privilegovaných identit, vyberte své uživatelské jméno v pravém horním rohu portálu Azure.

3. V nabídce Portál Azure vyberte **Všechny služby** a filtrujte seznam pro **správu privilegovaných identit Azure AD**.

4. Otevřete správu privilegovaných identit ze seznamu **Všechny služby** a připněte ji na řídicí panel.

První osobě, která používá Azure AD Privileged Identity Management ve vašem tenantovi je automaticky **přiřazena správce zabezpečení** a **privilegované** role správce rolí v tenantovi. Pouze privilegované role správci spravovat přiřazení rolí adresáře Azure AD uživatelů. Také po přidání Azure AD Privilegované Správy identit, zobrazí se průvodce zabezpečením, který vás provede počáteční zjišťování a přiřazení prostředí. Průvodce můžete ukončit bez dalších změn. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identifikace a kategorizace účtů ve vysoce privilegovaných rolích 

Po zapnutí správy privilegovaných identit Azure AD zobrazte uživatele, kteří jsou v rolích adresářů Globální správce, Správce privilegovaných rolí, správce Exchange Online a správce SharePointu Online. Pokud nemáte Azure AD PIM ve vašem tenantovi, můžete použít [rozhraní API prostředí PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Začněte s rolí globálního správce, protože tato role je obecná: uživatel, kterému je přiřazena tato role správce, má stejná oprávnění ve všech cloudových službách, pro které se vaše organizace přihlásila k odběru, bez ohledu na to, jestli mu byla tato role přiřazena v Microsoftu 365. centrum pro správu, portál Azure nebo pomocí modulu Azure AD pro Microsoft PowerShell. 

Odeberte všechny účty, které již nejsou v těchto rolích potřeba. Potom zařazujte do kategorií zbývající účty, které jsou přiřazeny k rolím správců:

* Individuálně přiřazené administrativním uživatelům a lze je také použít pro neadministrativní účely (například osobní e-mail)
* Individuálně přiděleni administrativním uživatelům a určeni pouze pro administrativní účely
* Sdíleno mezi více uživateli
* Pro scénáře nouzového přístupu k rozbití skla
* Pro automatické skripty
* Pro externí uživatele

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definování alespoň dvou účtů nouzového přístupu 

Ujistěte se, že se nedostanete do situace, kdy by mohly být nechtěně uzamčeny ze správy vašeho klienta Azure AD z důvodu neschopnosti přihlásit nebo aktivovat účet existujícího jednotlivého uživatele jako správce. Například pokud je organizace federována místnímu poskytovateli identity, může být tento poskytovatel identity nedostupný, takže se uživatelé nemohou přihlásit místně. Dopad náhodného nedostatku přístupu pro správu můžete zmírnit uložením dvou nebo více účtů nouzového přístupu do tenanta.

Účty nouzového přístupu pomáhají organizacím omezit privilegovaný přístup v rámci existujícího prostředí Služby Azure Active Directory. Tyto účty jsou vysoce privilegované a nejsou přiřazeny konkrétním osobám. Účty nouzového přístupu jsou omezeny na nouzové scénáře "break glass", kdy nelze použít běžné administrativní účty. Organizace musí zajistit cíl kontroly a omezení používání nouzového účtu pouze na dobu, pro kterou je to nezbytné.

Vyhodnoťte účty, které jsou přiřazeny nebo způsobilé pro roli globálního správce. Pokud jste neuviděli žádné účty pouze pro cloud pomocí domény *.onmicrosoft.com (určené pro nouzový přístup "break glass"),vytvořte je. Další informace najdete [v tématu Správa účtů pro správu nouzového přístupu ve službě Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Zapnutí vícefaktorového ověřování a registrace všech ostatních vysoce privilegovaných účtů nefederovaných správců pro jednoho uživatele

Vyžadovat azure multi-factor authentication (MFA) při přihlášení pro všechny jednotlivé uživatele, kteří jsou trvale přiřazeni k jedné nebo více rolí správce Azure AD: globální správce, správce privilegovaných rolí, správce Exchange Online a SharePoint Správce online. Pomocí příručky povolte [vícefaktorové ověřování (MFA) pro účty správce](../authentication/howto-mfa-userstates.md) a [https://aka.ms/mfasetup](https://aka.ms/mfasetup)ujistěte se, že všichni tito uživatelé se zaregistrovali na adrese . Další informace najdete v kroku 2 a kroku 3 průvodce [Ochrana přístupu k datům a službám v Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Fáze 2: Zmírnění nejčastěji používaných útočných technik

![Fáze 2 Zmírnění často používaných útoků](./media/directory-admin-roles-secure/stage-two.png)

Fáze 2 plánu se zaměřuje na zmírnění nejčastěji používaných útočných technik krádeže a zneužívání přihlašovacích údajů a může být implementována přibližně za 2-4 týdny. Tato fáze plánu zabezpečeného privilegovaného přístupu zahrnuje následující akce.

### <a name="general-preparation"></a>Obecná příprava

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Provedení soupisu služeb, vlastníků a správců

S nárůstem vbring-your-own-device (BYOD) a work-from-home politiky a růst bezdrátovépřipojení v podnicích, je důležité, abyste sledovali, kdo se připojuje k vaší síti. Efektivní audit zabezpečení často odhaluje zařízení, aplikace a programy spuštěné v síti, které nejsou podporovány it, a proto potenciálně nejsou zabezpečené. Další informace najdete v [tématu Azure security management and monitoring overview](../../security/fundamentals/management-monitoring-overview.md). Ujistěte se, že do procesu inventury zahrnete všechny následující úkoly. 

* Identifikujte uživatele, kteří mají role správy, a služby, kde mohou spravovat.
* Pomocí pim služby Azure AD zjistíte, kteří uživatelé ve vaší organizaci mají přístup správce k Azure AD, včetně dalších rolí nad rámec těch, které jsou uvedeny ve fázi 1.
* Kromě rolí definovaných ve službě Azure AD je Office 365 dodáván se sadou rolí správců, které můžete přiřadit uživatelům ve vaší organizaci. Každá role správce se mapuje na běžné obchodní funkce a uděluje lidem ve vaší organizaci oprávnění k provádění konkrétních úkolů v [Centru pro správu Microsoftu 365](https://admin.microsoft.com). Pomocí Centra pro správu Microsoftu 365 zjistíte, kteří uživatelé ve vaší organizaci mají přístup správce k Office 365, a to i prostřednictvím rolí, které nejsou spravované ve Službě Azure AD. Další informace najdete v [tématu O rolích správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) a [doporučených postupech zabezpečení pro Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Můžete provádět inventář v jiných službách, na kterých vaše organizace spoléhá, jako je Azure, Intune nebo Dynamics 365.
* Ujistěte se, že vaše účty správce (účty, které se používají pro účely správy, nejen pouze každodenní účty uživatelů) mají pracovní e-mailové adresy připojené k nim a registrované pro Azure MFA nebo používat vícefaktorové povolení místně.
* Požádejte uživatele o zdůvodnění firmy pro přístup správce.
* Odeberte přístup správce pro ty jednotlivce a služby, které ho nepotřebují.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifikace účtů Microsoft v rolích pro správu, které je třeba přepnout na pracovní nebo školní účty

V některých případě počáteční globální správci pro organizaci znovu použít své stávající přihlašovací údaje účtu Microsoft, když začali používat Azure AD. Tyto účty Microsoft by měly být nahrazeny jednotlivými cloudovými nebo synchronizované účty. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Zajištění samostatných uživatelských účtů a předávání pošty pro účty globálního správce 

Globální účty správců by neměly mít osobní e-mailové adresy, protože osobní e-mailové účty jsou pravidelně phished kybernetickými útočníky. Chcete-li pomoci oddělit rizika internetu (phishingové útoky, neúmyslné procházení webu) od oprávnění správce, vytvořte vyhrazené účty pro každého uživatele s oprávněními správce. 

Pokud jste tak ještě neučinili, vytvořte samostatné účty pro uživatele k provádění úloh globálního správce, abyste se ujistili, že neúmyslně neotevírají e-maily nebo nespouštějí programy přidružené k jejich účtům správce. Ujistěte se, že tyto účty mají své e-maily přeposlány do pracovní poštovní schránky.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Ujistěte se, že hesla účtů pro správu se nedávno změnila

Ujistěte se, že se všichni uživatelé přihlásili ke svým účtům pro správu a změnili svá hesla alespoň jednou za posledních 90 dní. Také se ujistěte, že všechny sdílené účty, ve kterých více uživatelů zná heslo, byly nedávno změněny.

#### <a name="turn-on-password-hash-synchronization"></a>Zapnutí synchronizace hodnot hash hesel

Synchronizace hodnot hash hesla je funkce používaná k synchronizaci hodnot hash hash hodnot hash uživatelských hesel z místní instance služby Active Directory do instance Azure AD založené na cloudu. I v případě, že se rozhodnete používat federaci se službou AD FS (AD FS) nebo jinými poskytovateli identit, můžete volitelně nastavit synchronizaci hodnot hash hesel jako zálohu v případě, že místní infrastruktura, jako jsou servery Služby AD nebo ADFS, selže nebo se stane dočasně nedostupná. To umožňuje uživatelům přihlásit se ke službě pomocí stejného hesla, které používají k přihlášení k jejich místní instanci služby AD. Také umožňuje identity protection odhalit ohrožené přihlašovací údaje porovnáním těchto hesel hash s hesly známo, že jsou ohroženy, pokud uživatel využil své stejné e-mailové adresy a hesla na jiné služby, které nejsou připojeny k Azure AD.  Další informace najdete [v tématu Implementace synchronizace hash hesel se synchronizací Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Vyžadovat vícefaktorové ověřování (MFA) pro uživatele ve všech privilegovaných rolích i exponované uživatele

Azure AD doporučuje, abyste pro všechny uživatele, včetně správců a všech ostatních uživatelů, kteří by měli významný dopad, pokud by došlo k ohrožení jejich účtu (například finanční úředníci), bylo vyžadováno vícefaktorové ověřování (MFA). Tím se snižuje riziko útoku z důvodu kompromitovanéheslo.

Zapnout:

* [Vícefaktorové zabezpečení pomocí zásad podmíněného přístupu](../authentication/howto-mfa-getstarted.md) pro všechny uživatele ve vaší organizaci.

Pokud používáte Windows Hello pro firmy, požadavek mfa lze splnit pomocí prostředí Windows Hello přihlášení. Další informace naleznete v tématu [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Konfigurace ochrany identity 

Azure AD Identity Protection je nástroj pro monitorování a vytváření sestav založený na algoritmu, který můžete použít ke zjištění potenciálních chyb zabezpečení ovlivňujících identitu vaší organizace. Můžete nakonfigurovat automatické odpovědi na zjištěné podezřelé aktivity a provést příslušná opatření k jejich vyřešení. Další informace najdete v článku [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Získání zabezpečeného skóre Office 365 (pokud používáte Office 365)

Zabezpečené skóre zjistí, jaké služby Office 365 používáte (například OneDrive, SharePoint a Exchange), pak se podívá na vaše nastavení a aktivity a porovná je se směrným plánem stanoveným společností Microsoft. Získáte skóre na základě toho, jak jste zarovnáni s osvědčenými postupy zabezpečení. Každý, kdo má oprávnění správce (globální správce nebo vlastní roli správce) pro předplatné Office [https://securescore.office.com](https://securescore.office.com/)365 Business Premium nebo Enterprise, má přístup k zabezpečenému skóre na adrese .

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Projděte si pokyny k zabezpečení a dodržování předpisů office 365 (pokud používáte Office 365)

[Plán zabezpečení a dodržování předpisů](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) nastiňuje přístup, jak by měl zákazník Office 365 konfigurovat Office 365 a využívat další funkce EMS. Potom zkontrolujte kroky 3-6 o tom, jak [chránit přístup k datům a službám v Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) a návod, jak [sledovat zabezpečení a dodržování předpisů v Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Konfigurace monitorování aktivit Office 365 (pokud používáte Office 365)

Můžete sledovat, jak lidé ve vaší organizaci používají služby Office 365, což vám umožní identifikovat uživatele, kteří mají účet pro správu a kteří nemusí potřebovat přístup k Office 365, protože se k těmto portálům nepřihlašují. Další informace najdete [v tématu Sestavy aktivit v Centru pro správu Microsoftu 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Zřídit vlastníky plánu reakce na incidenty/mimořádné události

Efektivní provádění reakce na incidenty je složitý podnik. Proto vytvoření úspěšné schopnosti reakce na incidenty vyžaduje značné plánování a zdroje. Je nezbytné, abyste neustále sledovali kybernetické útoky a stanovili postupy pro stanovení priorit řešení incidentů. Efektivní metody shromažďování, analýzy a vytváření sestav dat jsou nezbytné pro budování vztahů a navázání komunikace s dalšími interními skupinami a vlastníky plánu. Další informace naleznete v [centru Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Zabezpečení místních privilegovaných účtů pro správu, pokud to ještě nebylo provedeno

Pokud je váš tenant Služby Azure Active Directory synchronizován s místní službou Active Directory, postupujte podle pokynů v [plánu privilegovaného přístupu zabezpečení](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): Fáze 1. To zahrnuje vytvoření samostatných účtů pro správce pro uživatele, kteří potřebují provádět místní úlohy správy, nasazení pracovních stanic s privilegovaným přístupem pro správce služby Active Directory a vytvoření jedinečných hesel místních správců pro pracovní stanice a Servery.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Další kroky pro organizace spravující přístup k Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Dokončení soupisu předplatných

K identifikaci předplatných ve vaší organizaci, která hostují produkční aplikace, můžete použít portál Enterprise a portál Azure.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Odebrání účtů Microsoft z rolí správců

Účty Microsoft z jiných programů, jako je Xbox, Live a Outlook, by se neměly používat jako účty správce pro předplatná organizace. Odeberte stav správce ze všech účtů Microsoft a chris@contoso.comnahraďte je pracovními nebo školními účty Azure Active Directory (například) pracovními nebo školními účty.

#### <a name="monitor-azure-activity"></a>Sledování aktivity Azure

Protokol aktivit Azure poskytuje historii událostí na úrovni předplatného v Azure. Nabízí informace o tom, kdo vytvořil, aktualizoval a odstranil jaké prostředky a kdy k těmto událostem došlo. Další informace najdete [v tématu Auditování a přijímání oznámení o důležitých akcích v rámci předplatného Azure](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Další kroky pro organizace spravující přístup k dalším cloudovým aplikacím prostřednictvím Azure AD

#### <a name="configure-conditional-access-policies"></a>Konfigurace zásad podmíněného přístupu

Připravte zásady podmíněného přístupu pro místní a cloudové hostované aplikace. Pokud máte zařízení připojená k uživatelům na pracovišti, získejte další informace z [nastavení místního podmíněného přístupu pomocí registrace zařízení Azure Active Directory](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Fáze 3: Vytváření viditelnosti a převzetí plné kontroly nad aktivitou správce

![Fáze 3 převzít kontrolu nad administrátorskou aktivitou](./media/directory-admin-roles-secure/stage-three.png)

Fáze 3 navazuje na skutečnosti snižující závažnost rizika z fáze 2 a je navržena tak, aby byla implementována přibližně za 1-3 měsíce. Tato fáze plánu zabezpečeného privilegovaného přístupu obsahuje následující součásti.

### <a name="general-preparation"></a>Obecná příprava

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Dokončení kontroly přístupu uživatelů v rolích správce

Více podnikových uživatelů získává privilegovaný přístup prostřednictvím cloudových služeb, což může vést k rostoucí nespravované platformě. To zahrnuje uživatele, kteří se stávají globálními správci pro Office 365, správci předplatného Azure a uživatele, kteří mají přístup správce k virtuálním počítačům nebo prostřednictvím aplikací SaaS. Místo toho by organizace měly mít všechny zaměstnance, zejména správce, kteří by zpracovávali každodenní obchodní transakce jako neprivilegované uživatele a podle potřeby měli převzít pouze práva správce. Vzhledem k tomu, že počet uživatelů v rolích správců se od počátečního přijetí zvýšil, vyplňte kontroly přístupu, abyste identifikovali a potvrdili každého uživatele, který má nárok na aktivaci oprávnění správce. 

Udělejte toto:

* Určete, kteří uživatelé jsou správci Azure AD, povolte přístup správce na vyžádání, přístup správce just-in-time a ovládací prvky zabezpečení založené na rolích.
* Převést uživatele, kteří nemají žádné jasné odůvodnění pro správce privilegovaný přístup k jiné roli (pokud žádná způsobilá role, odeberte je).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Pokračovat v zavádění silnějšího ověřování pro všechny uživatele 

Vyžadovat, aby vedoucí pracovníci sady C, manažeři na vysoké úrovni, pracovníci it a zabezpečení a další vysoce exponovaní uživatelé měli moderní a silné ověřování, jako je Azure MFA nebo Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Použití vyhrazených pracovních stanic pro správu azure ad

Útočníci se mohou pokusit cílit na privilegované účty, aby získali přístup k datům a systémům organizace, aby mohli narušit integritu a pravost dat, a to prostřednictvím škodlivého kódu, který mění logiku programu nebo vyčmuchá správce zadávajícího pověření. Pracovní stanice s privilegovaným přístupem poskytují vyhrazený operační systém pro citlivé úlohy, který je chráněný před útoky z internetu a jinými vektory hrozeb. Separace citlivých úloh a účtů od pracovních stanic a zařízení pro běžné denní používání poskytuje velmi silnou ochranu před útoky phishing, bezpečnostními slabinami aplikací a operačního systému, různými útoky se zneužitím zosobnění a útoky založenými na krádeži přihlašovacích údajů, jako jsou protokolování stisknutí kláves a útoky Pass-the-Hash a Pass-The-Ticket. Nasazením pracovních stanic s privilegovaným přístupem můžete snížit riziko, že správci zadají pověření správce s výjimkou desktopového prostředí, které bylo posíleno. Další informace naleznete v [tématu Pracovní stanice s privilegovaným přístupem](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Projděte si doporučení Národního institutu pro standardy a technologie pro řešení incidentů 

National Institute of Standards and Technology (NIST) poskytuje pokyny pro řešení incidentů, zejména pro analýzu dat souvisejících s incidenty a určení vhodné reakce na každý incident. Další informace naleznete [v příručce (NIST) Computer Security Incident Handling Guide (SP 800-61, revize 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementovat správu privilegovaných identit (PIM) pro JIT do dalších administrativních rolí

Pro Azure Active Directory použijte funkci [Azure AD Privileged Identity Management.](../privileged-identity-management/pim-configure.md) Časově omezená aktivace privilegovaných rolí funguje tak, že umožňuje:

* Aktivace oprávnění správce k provedení určitého úkolu
* Vynucení vícefaktorové registrace během procesu aktivace
* Použití upozornění k informování správců o nerozpadlých změnách
* Umožněte uživatelům zachovat určitá oprávnění po předem nakonfigurovanou dobu
* Povolit správcům zabezpečení zjistit všechny privilegované identity, zobrazit zprávy o auditu a vytvářet kontroly přístupu k identifikaci každého uživatele, který je způsobilý k aktivaci oprávnění správce

Pokud už používáte Azure AD Privilegované Správy identit, upravte časové rámce pro časově vázaná oprávnění podle potřeby (například okna údržby).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Určení vystavení protokolům pro přihlášení založeným na heslech (pokud používáte Exchange Online)

V minulosti protokoly předpokládaly, že kombinace uživatelského jména a hesla byly vloženy do zařízení, e-mailových účtů, telefonů a tak dále. Ale teď s rizikem kybernetických útoků v cloudu doporučujeme identifikovat každého potenciálního uživatele, který, pokud by jejich přihlašovací údaje byly ohroženy, by mohl být pro organizaci katastrofický, a vyloučit je z možnosti přihlášení k e-mailu pomocí uživatelského jména nebo hesla implementací silných požadavků na ověřování a podmíněného přístupu. Starší verze ověřování můžete blokovat [pomocí podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication). Zkontrolujte podrobnosti o [tom, jak blokovat základní ověřování](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) prostřednictvím serveru Exchange online. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Dokončení hodnocení kontroly rolí pro role Office 365 (pokud používáte Office 365)

Vyhodnoťte, zda jsou všichni uživatelé správců ve správných rolích (odstraňte a znovu přiřaďte podle tohoto hodnocení).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Kontrola přístupu správy incidentů zabezpečení používaného v Office 365 a porovnání s vlastní organizací

Tuto sestavu si můžete stáhnout ze [služby Security Incident Management v systému Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Pokračovat v zabezpečení místních privilegovaných účtů pro správu

Pokud je služba Azure Active Directory připojena k místnímu službě Active Directory, postupujte podle pokynů v [plánu privilegovaného přístupu zabezpečení](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): Fáze 2. To zahrnuje nasazení pracovních stanic privilegovaného přístupu pro všechny správce, které vyžadují vícefaktorové informace, použití just enough admin pro údržbu řadiče domény, snížení prostoru útoku domén, nasazení ATA pro detekci útoků.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Další kroky pro organizace spravující přístup k Azure

#### <a name="establish-integrated-monitoring"></a>Zavést integrované monitorování

[Azure Security Center](../../security-center/security-center-intro.md) poskytuje integrované monitorování zabezpečení a správu zásad napříč vašimi předplatnými Azure, pomáhá odhalovat hrozby, které by jinak mohly zůstat bez povšimnutí, a spolupracuje s širokým ekosystémem řešení zabezpečení.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventarizace privilegovaných účtů v hostovaných virtuálních počítačích

Ve většině případů nemusíte uživatelům udělovat neomezená oprávnění ke všem vašim předplatným nebo prostředkům Azure. Role správce Azure AD můžete použít k oddělení povinností v rámci vaší organizace a udělit pouze množství přístupu uživatelům, kteří potřebují provádět určité úlohy. Například použijte role správce Azure AD, aby jeden správce spravovat pouze virtuální počítače v předplatném, zatímco jiný můžete spravovat databáze SQL v rámci stejného předplatného. Další informace najdete [v tématu Začínáme s řízením přístupu na základě rolí na webu Azure Portal](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementace rolí správce služby Azure AD pro Azure AD

Ke správě, řízení a monitorování přístupu k prostředkům Azure používejte správu privilegovaných identit s rolemi správce Azure AD. Používání PIM chrání privilegované účty před kybernetickými útoky tím, že snižuje dobu expozice oprávnění a zvyšuje vaši viditelnost jejich používání prostřednictvím sestav a výstrah. Další informace najdete [v tématu Správa přístupu RBAC k prostředkům Azure pomocí správy privilegovaných identit](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Použití integrace protokolů Azure k odesílání příslušných protokolů Azure do vašich systémů SIEM 

Integrace protokolů Azure umožňuje integrovat nezpracované protokoly z prostředků Azure do stávajících systémů správy informací o zabezpečení a událostí (SIEM) vaší organizace. [Integrace protokolu Azure](../../security/fundamentals/azure-log-integration-overview.md) shromažďuje události systému Windows z protokolů Prohlížeče událostí Windows a prostředky Azure z protokolů aktivit Azure, výstrah y Azure Security Center a protokolů Azure Diagnostic. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Další kroky pro organizace spravující přístup k dalším cloudovým aplikacím prostřednictvím Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementace zřizování uživatelů pro připojené aplikace

Azure AD umožňuje automatizovat vytváření, údržbu a odebírání uživatelských identit v cloudových (SaaS) aplikacích, jako je Dropbox, Salesforce, ServiceNow a tak dále. Další informace najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí Azure AD](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrace ochrany informací

MCAS umožňuje prozkoumat soubory a nastavit zásady založené na popiscích klasifikace Azure Information Protection, což umožňuje lepší viditelnost a kontrolu vašich dat v cloudu. Prohledává a klasifikuje soubory v cloudu a aplikuj popisky ochrany informací Azure. Další informace najdete v [tématu Integrace Azure Information Protection](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurace podmíněného přístupu

Konfigurace podmíněného přístupu na základě citlivosti skupiny, umístění a aplikací pro [aplikace SaaS](https://azure.microsoft.com/overview/what-is-saas/) a připojené aplikace Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Sledování aktivity v připojených cloudových aplikacích

Chcete-li zajistit, aby byl přístup uživatelů chráněn i v připojených aplikacích, doporučujeme využít [microsoft cloudapp security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Tím zabezpečujete podnikový přístup ke cloudovým aplikacím a zabezpečujete účty správců tím, že vám umožníte:

* Rozšíření viditelnosti a řízení na cloudové aplikace
* Vytvoření zásad pro přístup, aktivity a sdílení dat
* Automatická identifikace rizikových aktivit, abnormálního chování a hrozeb
* Zabránit úniku dat
* Minimalizujte rizika a automatizovanou prevenci hrozeb a prosazování zásad

Agent Cloud App Security SIEM integruje Cloud App Security s vaším serverem SIEM, aby umožnil centralizované monitorování výstrah a aktivit Office 365. Běží na vašem serveru a získává výstrahy a aktivity z Cloud App Security a streamuje je na server SIEM. Další informace naleznete v tématu [integrace SIEM](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Fáze 4: Pokračovat v budování obrany na proaktivnější bezpečnostní postoj

![Fáze 4 přijmout proaktivní bezpečnostní postoj](./media/directory-admin-roles-secure/stage-four.png)

Fáze 4 plánu vychází z viditelnosti z fáze 3 a je navržena tak, aby byla provedena za šest měsíců a dále. Dokončení plánu vám pomůže vytvořit silné privilegované ochrany přístupu před potenciálními útoky, které jsou v současné době známé a dostupné dnes. Bezpečnostní hrozby se bohužel neustále vyvíjejí a posouvají, takže doporučujeme, abyste bezpečnost považovali za probíhající proces zaměřený na zvyšování nákladů a snižování úspěšnosti protivníků zaměřených na vaše prostředí.

Zabezpečení privilegovaného přístupu je důležitým prvním krokem k vytvoření záruk zabezpečení obchodních aktiv v moderní organizaci, ale není to jediná část kompletního bezpečnostního programu, která by zahrnovala prvky, jako je zásada, operace, informace. zabezpečení, servery, aplikace, počítače, zařízení, cloudová struktura a další součásti poskytují průběžné záruky zabezpečení. 

Kromě správy privilegovaných přístupových účtů doporučujeme průběžně kontrolovat následující:

* Ujistěte se, že správci dělají svou každodenní práci jako neprivilegovaní uživatelé.
* V případě potřeby udělte pouze privilegovaný přístup a odeberte jej později (just-in-time).
* Uchovávejte a kontrolujte auditní činnost týkající se privilegovaných účtů.

Další informace o vytváření úplného plánu zabezpečení najdete v tématu [Prostředky architektury cloudových IT společnosti Microsoft](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Další informace o zapojení služeb společnosti Microsoft, které vám pomohou s některou z těchto témat, získáte od zástupce společnosti Microsoft nebo v [tématu Vytvoření kritické kybernetické obrany za účelem ochrany vašeho podniku](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Tato závěrečná fáze plánu zabezpečeného privilegovaného přístupu zahrnuje následující součásti.

### <a name="general-preparation"></a>Obecná příprava

#### <a name="review-admin-roles-in-azure-active-directory"></a>Kontrola rolí správců ve službě Azure Active Directory 

Zjistěte, jestli jsou aktuální role správce Azure AD stále aktuální, a ujistěte se, že uživatelé jsou jenom v rolích a delegacích, které potřebují pro odpovídající oprávnění. S Azure AD můžete určit samostatné správce, kteří budou obsluhovat různé funkce. Další informace naleznete v [tématu Přiřazení rolí správce ve službě Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Kontrola uživatelů, kteří mají správu zařízení připojená k Azure AD

Další informace najdete v tématu [Konfigurace hybridních zařízení spojených se službou Azure Active Directory](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>Kontrola členů [předdefinovaných rolí správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Pokud používáte Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Ověřit plán reakce na incidenty

Chcete-li zlepšit váš plán, společnost Microsoft doporučuje pravidelně ověřovat, zda váš plán funguje podle očekávání:

* Projděte si stávající cestovní mapu a podívejte se, co bylo vynecháno
* Na základě posmrtné analýzy revidovat stávající nebo definovat nové osvědčené postupy
* Zajistěte, aby byl aktualizovaný plán reakce na incidenty a osvědčené postupy distribuovány v celé organizaci.


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Další kroky pro organizace spravující přístup k Azure 

Zjistěte, jestli potřebujete [převést vlastnictví předplatného Azure na jiný účet](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Rozbít sklo": co dělat v případě nouze

![Účty pro nouzový přístup k rozbití skla](./media/directory-admin-roles-secure/emergency.jpeg)

1. Informujte klíčové manažery a bezpečnostní pracovníky s relevantními informacemi týkajícími se incidentu.

2. Zkontrolujte svůj scénář útoku. 

3. Přístup k uživatelskému jménu a heslu účtu "break glass" pro přihlášení do Azure AD. 

4. Získejte pomoc od [Microsoftu otevřením žádosti o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Podívejte se na [sestavy přihlášení Azure AD](../reports-monitoring/overview-reports.md). Může existovat prodleva mezi událostí, ke které dochází, a pokud je zahrnuta do sestavy.

6. V hybridních prostředích, pokud není federovaný a váš server Služby AD FS není k dispozici, bude pravděpodobně nutné dočasně přepnout z federovaného ověřování, abyste mohli používat synchronizaci hash hesel. Tím se federace domény vrátí zpět ke spravovanému ověřování, dokud nebude k dispozici server služby AD FS.

7. Sledujte e-maily pro privilegované účty.

8. Ujistěte se, že uložíte zálohy příslušných protokolů pro potenciální forenzní a právní vyšetřování.

Další informace o tom, jak Microsoft Office 365 zpracovává incidenty zabezpečení, naleznete [v tématu Správa incidentů zabezpečení v Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Časté dotazy: Běžné otázky, které dostáváme ohledně zabezpečení privilegovaného přístupu  

**Otázka:** Co mám dělat, když jsem ještě neimplementoval žádné součásti zabezpečeného přístupu?

**Odpověď:** Definujte alespoň dva nezlomové účty, přiřaďte vícefaktorové ověřování k privilegovaným účtům správce a oddělte uživatelské účty od globálních účtů správců.

**Otázka:** Jaký je po porušení problému, který je třeba řešit jako první?

**Odpověď:** Ujistěte se, že vyžadujete nejsilnější ověřování pro vysoce exponované osoby.

**Otázka:** Co se stane, když naši privilegovaní správci byli deaktivováni?

**Odpověď:** Vytvořte globální účet správce, který je vždy aktuální.

**Otázka:** Co se stane, pokud zbývá pouze jeden globální správce a není k dispozici? 

**Odpověď:** Použijte jeden z vašich break-glass účty získat okamžitý privilegovaný přístup.

**Otázka:** Jak mohu chránit správce v rámci organizace?

**Odpověď:** Mít adminy vždy dělat jejich každodenní podnikání jako standardní "neprivilegované" uživatele.

**Otázka:** Jaké jsou doporučené postupy pro vytváření účtů správce v rámci Azure AD?

**Odpověď:** Vyhradit privilegovaný přístup pro konkrétní úkoly správce.

**Otázka:** Jaké nástroje existují pro snížení trvalého přístupu správce?

**Odpověď:** Role správce privilegované identity (PIM) a Azure AD.

**Otázka:** Jaká je pozice Microsoftu při synchronizaci účtů správců do Azure AD?

**Odpověď:** Účty správce úrovně 0 (včetně účtů, skupin a dalších prostředků, které mají přímou nebo nepřímou správu doménové struktury služby AD, doménových disponoresí nebo řadičů domény a všech prostředků) se využívají pouze pro místní účty služby AD a nejsou obvykle synchronizovány pro Azure AD pro cloud.

**Otázka:** Jak zabráníme správcům v přiřazování náhodného přístupu správce na portál?

**Odpověď:** Používejte neprivilegované účty pro všechny uživatele a většinu správců. Začněte tím, že vyvíjí stopu organizace k určení, které málo účtů správce by měly být privilegované. A monitorujte nově vytvořené administrativní uživatele.

## <a name="next-steps"></a>Další kroky

* [Centrum zabezpečení microsoftu pro zabezpečení produktů](https://www.microsoft.com/trustcenter/security) – funkce zabezpečení cloudových produktů a služeb Microsoftu

* [Microsoft Trust Center – Dodržování předpisů](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – komplexní sada nabídek dodržování předpisů společnosti Microsoft pro cloudové služby

* [Pokyny k provádění hodnocení rizik](https://www.microsoft.com/trustcenter/guidance/risk-assessment) – Správa požadavků na zabezpečení a dodržování předpisů pro cloudové služby Microsoftu

### <a name="other-microsoft-online-services"></a>Další služby Microsoft Online Services

* [Microsoft Intune Security](https://www.microsoft.com/trustcenter/security/intune-security) – Intune poskytuje správu mobilních zařízení, správu mobilních aplikací a funkce správy počítače z cloudu.

* [Zabezpečení Microsoft Dynamics 365](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 je cloudové řešení společnosti Microsoft, které sjednocuje možnosti správy vztahů se zákazníky (CRM) a plánování podnikových zdrojů (ERP).
