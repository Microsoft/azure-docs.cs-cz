---
title: Identita a přístup osvědčené postupy zabezpečení Azure | Dokumentace Microsoftu
description: Tento článek poskytuje sadu osvědčených postupů pro správu identit a přístupu k řízení pomocí integrované možnosti Azure.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/17/2018
ms.author: barclayn
ms.openlocfilehash: f6640e7d179199fbfb5b0c2b0c384729b6f53bcf
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498245"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure správu identit a řízení přístupu osvědčené postupy zabezpečení

Mnoho vezměte v úvahu identity jako novou vrstvu hranici pro zabezpečení, trvá déle než tuto roli z hlediska tradiční zaměřené na síti. Tomuto vývoji primární pivot pro zabezpečení pozornost a investic pocházejí z skutečnost, že se stává stále porézní perimetry sítě a této hraniční obrana nemůže být co nejúčinnější, jednou byly před obrovské množství nových [modelu BYOD ](http://aka.ms/byodcg) zařízení a cloudové aplikace.

V tomto článku se podíváme na kolekci správy identit Azure a osvědčené postupy pro zabezpečení řízení přístupu. Tyto osvědčené postupy, které jsou odvozeny z našich zkušenostech s [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) a prostředí zákazníků, jako je sami.

Pro každý osvědčeným postupem je vám vysvětlíme:

* Jaký je doporučený postup
* Proč chcete povolit tento osvědčený postup
* Pokud chcete povolit osvědčený postup, co mohou být způsobeny
* Je to možné alternativy doporučené postupy
* Jak lze zjistíte, jak povolit osvědčený postup

Tento správy identit Azure a zabezpečení řízení přístupu, článek o osvědčených postupech je založena na názor shody a možnostech platformy Azure a sady funkcí, protože existují v době byl napsán v tomto článku. Názory a technologie v průběhu času měnit a tento článek bude aktualizován v pravidelných intervalech, aby tyto změny projevily.

Identita Azure správu nebo přístupová ovládací prvek osvědčené postupy zabezpečení popsané v tomto článku zahrnují:

* Nakládat jako primárního obvodu zabezpečení identit
* Centralizujte si správu identit
* Povolit jednotné přihlašování
* Zapnout podmíněný přístup
* Povolení správy hesel
* Vynutit ověřování Multi-Factor Authentication pro uživatele
* Použití řízení přístupu na základě rolí
* Snižuje pravděpodobnost vyzrazení privilegované účty
* Řízení umístění, kde jsou umístěny prostředky

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Nakládat jako primárního obvodu zabezpečení identit

Mnoho vezměte v úvahu identity jako primárního obvodu zabezpečení. Je to odklon od tradičního zaměřením na zabezpečení sítě. Sítě perimetry dále zobrazovat více porézní a že hraniční obrana nemůže být nejnižších před obrovské množství nových [BYOD](http://aka.ms/byodcg) zařízení a cloudové aplikace.
[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) je řešení Azure pro správu identit a přístupů. Azure AD je víceklientské, založené na cloudu adresáři a identitami služba pro správu od Microsoftu. Kombinuje základní adresářové služby, správu přístupu k aplikacím a ochranu identity v rámci jednoho řešení.

Osvědčené postupy pro zabezpečení přístupu a identit pomocí Azure AD v následujících částech.

## <a name="centralize-identity-management"></a>Centralizujte si správu identit

V [hybridní identita](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) scénář vám doporučujeme integraci místních a cloudových adresářích. Integrace umožňuje týmu IT spravovat účty z jednoho místa, bez ohledu na to, kde se vytvoří účet. Integrace také pomáhá svým uživatelům zajistit vyšší produktivitu poskytnutím společné identity pro přístup ke cloudovým i místním prostředkům.


**Osvědčený postup**: integrace místních adresářů s Azure AD.  
**Podrobnosti o**: použití [Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md) k synchronizaci vašeho místního adresáře s adresářem cloudu.

**Osvědčený postup**: zapnutí synchronizace hodnot hash hesel.  
**Podrobnosti o**: synchronizace hodnot hash hesel je funkce používá k synchronizaci hodnoty hash hodnot hash hesel uživatelů z místní instance Active Directory do Azure založené na cloudu instancí AD.

I když se rozhodnete federace pomocí služby Active Directory Federation Services (AD FS) nebo u jiných poskytovatelů identit, můžete volitelně nastavit synchronizaci hodnot hash hesel jako záložní v případě místních serverů selhat nebo být dočasně nedostupné. To umožňuje uživatelům umožní přihlásit ke službě s použitím stejné heslo, které používají pro přihlášení k jejich místní instancí Active Directory. Umožňuje také k detekci zneužití přihlašovacích údajů porovnáním hodnoty hash těchto hesel s hesly známé došlo k narušení, pokud uživatel použil jejich stejnou e-mailovou adresu a heslo v jiné službě, není připojen ke službě Azure AD Identity Protection.

Další informace najdete v tématu [implementace synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

Organizace, které není integrovat svoje místní identity s jejich cloudové identity můžete mít další režii při správě účtů. Tato režie se zvyšuje pravděpodobnost chyb a porušení zabezpečení.

## <a name="enable-single-sign-on"></a>Povolit jednotné přihlašování

Ve světě upřednostňujícím mobilní a cloud na prvním chcete povolit jednotné přihlašování (SSO) k zařízení, aplikacím a službám odkudkoli tak vaši uživatelé byli produktivní, kdykoli a kdekoli. Až budete mít více identit řešení pro správu, toto řešení pro správu problém není pouze pro IT, ale také pro uživatele, kteří musí pamatovat více hesel.

S využitím stejného řešení identit pro všechny aplikace a prostředky, můžete dosáhnout jednotného přihlašování. A vaši uživatelé můžete použít stejnou sadu pověření k přihlášení a přístup k prostředkům, které potřebují, zda jsou prostředky v místním nebo v cloudu.

**Osvědčený postup**: povolení jednotného přihlašování.  
**Podrobnosti o**: Azure AD [rozšiřuje místní služby Active Directory](../active-directory/connect/active-directory-aadconnect.md) do cloudu. Uživatelé můžou používat svůj primární pracovní nebo školní účet pro svá zařízení připojená k doméně, prostředkům společnosti a všechny webové a aplikacím SaaS, které potřebují ke své práci. Uživatelé nebudou muset pamatovat více sad uživatelských jmen a hesel a jejich přístup k aplikaci může být automaticky zřídit (nebo zrušení) na základě jejich členství ve skupinách organizace a jejich stav jako zaměstnanec. Přístup můžete řídit u aplikací z galerie i u vlastních místních aplikací, které jste nasadili a publikovali přes [Proxy aplikací služby Azure AD](../active-directory/active-directory-application-proxy-get-started.md).

Používat jednotné přihlašování umožňuje uživatelům přístup k jejich [aplikací SaaS](../active-directory/active-directory-appssoaccess-whatis.md) podle svého pracovního nebo školního účtu ve službě Azure AD. To platí nejen pro aplikace SaaS společnosti Microsoft, ale také další aplikace, jako například [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) a [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). Můžete nakonfigurovat aplikace pro používání služby Azure AD jako [identity založené na SAML](../active-directory/fundamentals-identity.md) zprostředkovatele. Jako ovládací prvek zabezpečení Azure AD bez vyvolání token, který umožňuje uživatelům umožní přihlásit k aplikaci, není-li, kterým byl udělen přístup prostřednictvím služby Azure AD. Můžete udělit přístup přímo nebo prostřednictvím skupiny, že uživatelé jsou členy.

Organizace, které pro ně nevytvoříte společnou identitu pro navázání jednotného přihlašování pro svoje uživatele a aplikace jsou vystaveny další scénáře, kdy uživatelé mají více hesel. Tyto scénáře zvýšit pravděpodobnost, že uživatelé opětovné použití hesla nebo používání Slabá hesla.

## <a name="turn-on-conditional-access"></a>Zapnout podmíněný přístup

Uživatelé můžou přístup k prostředkům vaší organizace pomocí celé řady zařízení a aplikací z libovolného místa. Jako správce IT budete chtít Ujistěte se, že tato zařízení splňují vaše standardy zabezpečení a dodržování předpisů. Právě se zaměříte na to, kdo má přístup k prostředku dostatečná už není.

Chcete-li zůstatek, zabezpečení a produktivitu, rozmyslete si, jak prostředek přistupuje, než provedete rozhodnutí o řízení přístupu. Tento požadavek můžete vyřešit pomocí podmíněného přístupu služby Azure AD. S podmíněným přístupem můžete provádět rozhodnutí o řízení automatizovaného přístupu pro přístup k vašim cloudovým aplikacím, které jsou založeny na podmínkách.

**Osvědčený postup**: Správa a řízení přístupu k firemním prostředkům.  
**Podrobnosti o**: Konfigurace služby Azure AD [podmíněného přístupu](../active-directory/active-directory-conditional-access-azure-portal.md) na základě skupin, umístění a "choulostivosti" aplikace pro aplikace SaaS a aplikace Azure AD – připojené.

## <a name="enable-password-management"></a>Povolení správy hesel

Pokud máte více tenantů nebo chcete uživatelům umožnit [resetovat vlastní hesla](../active-directory/active-directory-passwords-update-your-own-password.md), je důležité použít vhodná bezpečnostní zásady zamezilo.

**Osvědčený postup**: resetování nastavení hesla pomocí samoobslužné služby (SSPR) pro vaše uživatele.  
**Podrobnosti o**: používání Azure AD [samoobslužné resetování hesla](../active-directory-b2c/active-directory-b2c-reference-sspr.md) funkce.

**Osvědčený postup**: monitorování jak nebo jestli samoobslužné resetování HESLA je doopravdy používá.  
**Podrobnosti o**: sledování uživatelů, kteří jsou registraci pomocí služby Azure AD [sestava aktivit registrace resetování hesla](../active-directory/active-directory-passwords-get-insights.md). Funkci vytváření sestav, které poskytuje Azure AD pomáhá lze zodpovědět otázky pomocí předem připravených sestav. Pokud máte správně licenci, můžete také vytvořit vlastní dotazy.

## <a name="enforce-multi-factor-verification-for-users"></a>Vynutit ověřování Multi-Factor Authentication pro uživatele

Doporučujeme, abyste vyžadování dvoustupňového ověřování pro všechny vaše uživatele. To zahrnuje správce a ostatní uživatele ve vaší organizaci, který může mít významný dopad v případě ohrožení bezpečnosti účtu (například finanční vedoucí pracovníci pověření ochranou).

Vyžadováním dvoustupňového ověřování několika způsoby. Pro vás nejlepší možností závisí na vašich cílů, edice Azure AD, kterou používáte a licenční program. Zobrazit [vyžadování dvoustupňového ověřování pro uživatele](../active-directory/authentication/howto-mfa-userstates.md) určit pro vás nejlepší možností. Zobrazit [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) a [ověřování Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) ceny stránky pro další informace o licencích a cenách.

Níže jsou uvedeny možnosti a výhody pro povolení dvoustupňové ověřování:

**Možnost 1**: [povolit Vícefaktorové ověřování tak, že změníte stav uživatele](../active-directory/authentication/howto-mfa-userstates.md#enable-azure-mfa-by-changing-user-status).   
**Výhoda**: Toto je tradiční metody pro vyžadováním dvoustupňového ověřování. Funguje s oběma [ověřování Azure Multi-Factor Authentication v cloudu a Azure Multi-Factor Authentication Server](../active-directory/authentication/concept-mfa-whichversion.md). Pomocí této metody vyžaduje, aby uživatelé provést dvoustupňové ověřování pokaždé, když se přihlásí a přepisuje zásady podmíněného přístupu.

**Možnost 2**: [povolit Vícefaktorové ověřování se zásadami podmíněného přístupu](../active-directory/authentication/howto-mfa-getstarted.md#enable-multi-factor-authentication-with-conditional-access).   
**Výhoda**: Tato možnost umožňuje výzvu k zadání dvoustupňové ověření za určitých podmínek pomocí [podmíněného přístupu](../active-directory/active-directory-conditional-access-azure-portal.md). Přihlášení uživatele z různých míst, nedůvěryhodné zařízení nebo aplikace, které považujete za rizikové může být konkrétní podmínky. Definování zvláštní podmínky, kdy potřebujete dvoustupňové ověřování, lze předcházet konstanty s výzvou pro uživatele, které je možné rozluštit nepříjemnou uživatelské prostředí.

Toto je nejflexibilnější způsob, jak povolit dvoustupňové ověřování pro vaše uživatele. Povolení zásad podmíněného přístupu se dá použít jenom pro ověřování Azure Multi-Factor Authentication v cloudu a je Prémiová funkce služby Azure AD. Můžete najít další informace o této metodě v [nasazení cloudového ověřování Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md).

**Možnost 3**: povolení služby Multi-Factor Authentication se zásadami podmíněného přístupu vyhodnocením riziko uživatele a přihlašování [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md).   
**Výhoda**: Tato možnost vám umožní:

- Zjistit potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci.
- Nakonfigurujte automatické odpovědi na zjištěné podezřelé akce, které se vztahují na identity vaší organizace.
- Prozkoumat podezřelé incidenty a přijmout vhodná opatření k jejich řešení.

Tato metoda používá Azure AD Identity Protection rizika hodnocení k určení, zda bude vyžadovat dvoustupňové ověření na základě uživatele a riziko přihlášení pro všechny cloudové aplikace. Tato metoda vyžaduje licencování Azure Active Directory P2. Můžete najít další informace o této metodě v [Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md).

> [!Note]
> Možnost 1, povolování služby Multi-Factor Authentication tak, že změníte stav uživatele, přepíše podmíněné zásady. Protože možnosti 2 a 3 používají zásady podmíněného přístupu, možnost 1 nelze použít s nimi.

Organizace, které nepřidávají další vrstvy ochrany identit, jako je například dvoustupňové ověřování, budou náchylnější k útoku krádeží přihlašovacích údajů. Útoku krádeží přihlašovacích údajů může vést k ohrožení dat.

## <a name="use-role-based-access-control-rbac"></a>Použití řízení přístupu na základě rolí (RBAC)

Omezení přístupu na základě [znát](https://en.wikipedia.org/wiki/Need_to_know) a [nejnižší možná oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) principů zabezpečení je nutné pro organizace, které chcete vynutit zásady zabezpečení pro přístup k datům. Můžete použít [řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md) přiřadit oprávnění pro uživatele, skupiny nebo aplikace v určitém rozsahu. Obor přiřazení role může být předplatné, skupinu prostředků nebo jediný prostředek.

Můžete použít [integrované RBAC](../role-based-access-control/built-in-roles.md) role v Azure a přiřadit oprávnění pro uživatele. Organizace, které se nebudou vynucovat řízení přístupu dat pomocí funkcí, jako je RBAC může poskytuje více oprávnění než je nezbytné na svoje uživatele. To může vést k ohrožení dat tím, že přístup uživatelů k určité typy dat (například vysoký dopad na chod firmy), který by neměl mít.

## <a name="lower-exposure-of-privileged-accounts"></a>Snižuje pravděpodobnost vyzrazení privilegované účty

Zabezpečení privilegovaného přístupu je zásadním prvním krokem při ochraně podnikových prostředků. Minimalizace počtu uživatelů, kteří mají přístup k zabezpečené informace nebo materiály omezíte možnost uživatel se zlými úmysly získat přístup nebo autorizovaný uživatel neúmyslně by to ovlivnilo citlivých prostředků.

Privilegované účty jsou účty, které spravují a řídí systémy IT. Internetoví útočníci cílí na tyto účty k získání přístupu k datům a systémům v organizaci. K zabezpečení privilegovaného přístupu, měli izolovat účty a systémy před rizikem napadení se zlými úmysly.

Doporučujeme ji vyvíjet a postupujte podle plánu zabezpečení privilegovaného přístupu k proti kybernetických útoků. Informace o vytváření podrobný návod k zabezpečení identit a přístupu, které jsou spravované nebo hlášeny ve službě Azure AD, Microsoft Azure, Office 365 a jiných cloudových službách můžete zkontrolovat [k zabezpečení privilegovaného přístupu pro hybridními a cloudovými nasazeními v Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

Následující shrnuje osvědčené postupy, které jsou součástí [k zabezpečení privilegovaného přístupu pro hybridní a cloudové nasazení ve službě Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md):

**Osvědčený postup**: spravovat, řídit a monitorovat přístup k privilegovaným účtům.   
**Podrobnosti o**: zapněte [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md). Po zapnutí Privileged Identity Management, obdržíte oznámení e-mailové zprávy pro privilegovaný přístup změny role. Tato oznámení poskytovat včasné upozornění při přidání dalších uživatelů vysoce privilegované role ve vašem adresáři.

**Osvědčený postup**: identifikujte a kategorizaci účty, které jsou v vysoce privilegovaných rolích.   
**Podrobnosti o**: po zapnutí Azure AD Privileged Identity Management, můžete zobrazit uživatele, kteří jsou v globální správce, správce privilegovaných rolí a jiných vysoce privilegovaných rolí. Odeberte všechny účty, které už nejsou potřeba v těchto rolích a kategorizaci zbývajících účtů, která jsou přiřazená rolím správce:

- Jednotlivě přiřazeny správcům a lze použít pro účely bez oprávnění správce (například osobní e-mailu)
- Jednotlivě přiřazené správcům a určené pouze pro účely správy
- Sdílet mezi více uživatelů
- Pro nouzový přístup scénáře
- Pro automatizované skripty
- Pro externí uživatele

**Osvědčený postup**: implementace "just in time" (JIT) přístup k ještě víc snížit dobu expozice oprávnění a zvýší viditelnost do použití privilegovaných účtů.   
**Podrobnosti o**: Azure AD Privileged Identity Management vám umožní:

- Omezení uživatelů na pouze s ohledem na jejich oprávnění JIT.
- Přiřazení rolí zkrácený dobu bez obav, že jsou oprávnění automaticky odvolat.

**Osvědčený postup**: definování aspoň dva účty pro nouzový přístup.   
**Podrobnosti o**: účty pro přístup k mimořádné události pomáhají organizacím omezit privilegovaný přístup v existujícím prostředí Azure Active Directory. Tyto účty jsou vysoce privilegovaní a nejsou přiřazeny jednotlivým uživatelům. Účty pro nouzový přístup se omezuje na scénáře, kdy nejde použít normální účty pro správu. Organizace, musí omezit používání účtů nouzový s pouze nezbytné množství času.

Posouzení účtů, které jsou přiřazené nebo oprávněné pro roli globálního správce. Pokud nevidíte žádné účty jenom cloudu s využitím `*.onmicrosoft.com` domény (určený pro nouzový přístup), vytvořte je. Další informace najdete v tématu Správa účtů pro nouzový přístup pro správu ve službě Azure AD.

**Osvědčený postup**: zapnutí ověřování Multi-Factor Authentication a účty správců jiné než federované jednoho uživatele registrace všech ostatních vysoce privilegované.  
**Podrobnosti o**: vyžadovat ověřování Azure Multi-Factor Authentication při přihlášení pro všechny jednotlivé uživatele, kteří jsou trvale přiřazené na jeden nebo více rolí správce Azure AD: Globální správce, správce privilegovaných rolí, správce Exchange Online a Správce služby SharePoint Online. Použití v příručce k povolení [ověřování službou Multi-Factor Authentication pro vaše účty správců](../active-directory/authentication/howto-mfa-userstates.md) a zajistit, aby měli tito uživatelé [zaregistrovaný](https://aka.ms/mfasetup).

**Osvědčený postup**: proveďte kroky ke zmírnění nejčastěji používají napadené techniky.  
**Podrobnosti o**: [účty Microsoft identifikovat v rolích pro správu, které je potřeba přepnout na pracovní nebo školní účty](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Zajištění samostatné uživatelské účty a předávání pro účty globálních správců e-mailů](../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[Ujistěte se, že jste nedávno změnili hesla účtů pro správu](../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Zapnutí synchronizace hodnot hash hesel](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[Vyžadovat Vícefaktorové ověřování u uživatelů v všech privilegovaných rolích, jakož i odhalených uživatelů](../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Získat hodnotíte zabezpečení Office 365 (Pokud používáte Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[Projděte si doprovodné materiály pro zabezpečení a dodržování předpisů Office 365 (Pokud používáte Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Konfigurace monitorování aktivit Office 365 (Pokud používáte Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[Vytvořit incident/nouzovou odpovědi plán vlastníky](../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[Zabezpečené místní privilegované účty pro správu](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

Pokud není zabezpečení privilegovaného přístupu, můžete zjistit, že máte příliš mnoho uživatelů vysoce privilegované role a jsou zranitelnější vůči útokům. Útočníky, včetně internetoví útočníci často cílových účtů správce a další prvky privilegovaného přístupu k získání přístupu k citlivým datům a systémům pomocí krádeže přihlašovacích údajů.

## <a name="control-locations-where-resources-are-created"></a>Řízení umístění, kde jsou vytvořeny prostředky

Umožňuje operátorům cloudu k provádění úloh, a brání narušení smluv, které jsou potřeba ke správě prostředků vaší organizace je velmi důležité. Organizace, které chcete mít pod kontrolou umístění, kde se prostředky vytvoří by měl kód intenzivně tato místa.

Můžete použít [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) k vytvoření zásad zabezpečení, jejichž definice popisují akce nebo prostředky, které jsou výslovně odepřen. Můžete přiřadit tyto definice zásad v požadovaném oboru, jako je například předplatné, skupinu prostředků nebo samostatný prostředek.

> [!NOTE]
> Zásady zabezpečení nejsou stejná jako RBAC. Povolit uživatelům vytvářet prostředky, které skutečně používají RBAC.
>
>

Organizace, které nejsou řízení, jak vytvořit prostředky budou náchylnější uživatelům, kteří můžou zneužívání službu tak, že vytvoříte víc prostředků, než budou potřebovat. Posílení zabezpečení v procesu vytváření prostředků je důležitý krok k zabezpečení víceklientské scénář.

## <a name="actively-monitor-for-suspicious-activities"></a>Aktivně monitorujte pro podezřelé aktivity

Aktivní identitu monitorování systému můžete rychle odhalit podezřelé chování a aktivovat upozornění pro další zkoumání. V následující tabulce jsou uvedeny dvě možnosti Azure AD, které pomáhá organizacím sledovat svoji identitu:

**Osvědčený postup**: měl odpovídající metodu k identifikaci:

- Pokusy o přihlášení [bez trasován](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md).
- [Útok hrubou silou](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) útoky na konkrétním účtu.
- Pokusy o přihlášení z více míst.
- Přihlášení z [zařízením infikovaným](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md).
- Podezřelé IP adresy.

**Podrobnosti o**: použití Azure AD Premium [anomálií sestavy](../active-directory/active-directory-view-access-usage-reports.md). Mějte procesy a postupy pro správce IT spustit tyto sestavy, každý den nebo na vyžádání (obvykle ve scénáři reakce na incidenty).

**Osvědčený postup**: máte aktivní monitorování systému, který upozorněním na rizika a můžete nastavit úroveň rizika (vysoká, střední nebo nízká) pro vaše obchodní požadavky.   
**Podrobnosti o**: použití [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md), která označí aktuální rizika na svůj vlastní řídicí panel a odešle oznámení s denním souhrnu e-mailem. Pomáhá chránit identity vaší organizace, můžete nakonfigurovat zásady, které automaticky reagují na zjištěné problémy při dosažení úroveň zadané rizika.

Organizace, které není aktivně monitorovat jejich systémy identit hrozí správné přihlašovací údaje uživatele, dojde k ohrožení bezpečnosti. Bez vědomí, že podezřelé aktivity jsou probíhat přes tyto přihlašovací údaje organizace nelze zmírnění tohoto typu ohrožení.

## <a name="next-step"></a>Další krok

Zobrazit [osvědčené postupy zabezpečení Azure a vzory](security-best-practices-and-patterns.md) pro další doporučené postupy zabezpečení, mají použít, když jste návrhu, nasazení a správa cloudových řešení pomocí služby Azure.
