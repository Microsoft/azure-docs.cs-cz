---
title: Hybridní návrh identity – strategie přijetí v životním cyklu Azure | Microsoft Docs
description: Pomáhá definovat úlohy správy hybridních identit podle možností dostupných pro každou fázi životního cyklu.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf68406d4b0806e1d533e0bb8669a01939387989
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410653"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Určení strategie přijetí hybridního životního cyklu identit
V této úloze definujete strategii správy identit pro vaše řešení hybridní identity, aby splňovala podnikové požadavky, které jste definovali v části [určení úloh správy hybridních identit](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Pokud chcete definovat úlohy správy hybridních identit podle celého životního cyklu identity uvedeného výše v tomto kroku, budete muset vzít v úvahu možnosti dostupné pro každou fázi životního cyklu.

## <a name="access-management-and-provisioning"></a>Správa a zřizování přístupu
Díky kvalitnímu řešení pro správu přístupu k účtu může vaše organizace přesně sledovat, kdo má přístup k informacím v organizaci.

Řízení přístupu je kritická funkce centralizovaného zřizovacího systému s jedním bodem. Kromě ochrany citlivých informací řídí řízení přístupu stávající účty, které mají neschválené autorizace, nebo už nejsou potřeba. K řízení zastaralých účtů propojí systém zřizování informace o účtu s autoritativními informacemi o uživatelích, kteří vlastní účty. Informace o identitě autoritativního uživatele se obvykle uchovávají v databázích a adresářích lidských zdrojů.

Mezi účty v propracovaných IT firmách patří stovky parametrů, které definují autority, a tyto podrobnosti můžou být řízeny vaším systémem zřizování. Nové uživatele je možné identifikovat pomocí dat, která zadáte z autoritativního zdroje. Možnost schválení žádosti o přístup zahájí procesy, které schvalují (nebo zamítnou) zřizování prostředků pro ně.

| Fáze správy životního cyklu | Místně | Cloud | Hybridní |
| --- | --- | --- | --- |
| Správa účtů a zřizování |Pomocí role serveru AD DS (Active Directory® Domain Services) můžete vytvářet škálovatelnou, zabezpečenou a zvladatelnou infrastrukturu pro správu uživatelů a prostředků a poskytovat podporu pro aplikace pro práci s adresáři, jako je Microsoft® Exchange Server. <br><br> [Skupiny v služba AD DS můžete zřídit prostřednictvím Správce identit.](/previous-versions/mim/ff686261(v=ws.10)) <br>[Uživatele můžete zřídit v služba AD DS](/previous-versions/mim/ff686263(v=ws.10)) <br><br> Správci můžou pomocí řízení přístupu spravovat přístup uživatelů ke sdíleným prostředkům z bezpečnostních důvodů. Řízení přístupu ve službě Active Directory je spravováno na úrovni objektu nastavením různých úrovní přístupu nebo oprávnění k objektům, jako je úplné řízení, zápis, čtení nebo bez přístupu. Řízení přístupu ve službě Active Directory definuje, jak můžou různí uživatelé používat objekty služby Active Directory. Ve výchozím nastavení jsou oprávnění k objektům ve službě Active Directory nastavena na nejbezpečnější nastavení. |Musíte vytvořit účet pro každého uživatele, který bude mít přístup ke cloudové službě Microsoftu. Můžete také změnit uživatelské účty nebo je odstranit, pokud už je nepotřebujete. Ve výchozím nastavení nemají uživatelé oprávnění správce, ale můžete je volitelně přiřadit. <br><br> V rámci Azure Active Directory je jednou z hlavních funkcí schopnost spravovat přístup k prostředkům. Tyto prostředky mohou tvořit součást adresáře jako v případě oprávnění ke správě objektů prostřednictvím rolí v adresáři, nebo prostředků, které jsou pro adresář externí, jako jsou aplikace SaaS, služby Azure a sharepointové weby nebo místní prostředky. <br><br> Ve středu řešení pro správu přístupu Azure Active Directory je skupina zabezpečení. Vlastník prostředku (nebo správce adresáře) může přiřadit skupinu poskytující určité přístupové právo k prostředkům, které vlastní. Členové skupiny budou mít přístup a vlastník prostředku může delegovat právo na správu seznamu členů skupiny na někoho jiného, jako je například manažer oddělení nebo správce helpdesku.<br> <br> Část Správa skupin v Azure AD poskytuje další informace o správě přístupu prostřednictvím skupin. |Rozšíří identity služby Active Directory do cloudu prostřednictvím synchronizace a federace. |

## <a name="role-based-access-control"></a>Řízení přístupu na základě role
Řízení přístupu na základě role v Azure (Azure RBAC) využívá role a zásady zřizování k vyhodnocení, testování a prosazování vašich obchodních procesů a pravidel pro udělení přístupu uživatelům. Správci klíčů vytvářejí zásady zřizování a přiřazují uživatele k rolím a definují sady nároků na prostředky pro tyto role. Azure RBAC rozšiřuje řešení pro správu identit, aby používala softwarové procesy, a snížilo ruční interakci uživatelů během procesu zřizování.
Azure RBAC umožňuje společnosti omezit počet operací, které může jednotlivec provádět, jakmile budou mít přístup k Azure Portal. Když použijete službu Azure RBAC k řízení přístupu k portálu, správci IT můžou přístup delegovat pomocí následujících přístupů ke správě přístupu:

* **Přiřazení role na základě skupin** : můžete přiřadit přístup ke SKUPINÁM Azure AD, které se dají synchronizovat z vaší místní služby Active Directory. Díky tomu můžete využívat stávající investice, které vaše organizace provedla při správě skupin a jejich zpracování. Můžete také použít funkci delegovaná Správa skupin Azure AD Premium.
* **Využijte integrované role v Azure** : můžete použít tři role, vlastníka, přispěvatele a čtenáře, abyste zajistili, že uživatelé a skupiny mají oprávnění dělat jenom úkoly, které potřebují ke své práci.
* **Podrobný přístup k prostředkům** : role můžete přiřadit uživatelům a skupinám pro konkrétní předplatné, skupinu prostředků nebo jednotlivý prostředek Azure, jako je například web nebo databáze. Tímto způsobem můžete zajistit, že uživatelé budou mít přístup ke všem prostředkům, které potřebují, a nemají přístup k prostředkům, které nepotřebují spravovat.

## <a name="provisioning-and-other-customization-options"></a>Zřizování a další možnosti přizpůsobení
Váš tým může použít obchodní plány a požadavky k rozhodnutí, jak velký způsob přizpůsobení řešení identity. Velký podnik může například vyžadovat postupnou kumulativní plánování pro pracovní postupy a vlastní adaptéry, které jsou založené na časovém intervalu pro přírůstkové zřizování aplikací, které se běžně používají napříč geografickými oblastmi. Další plán vlastního nastavení může poskytovat možnost zřízení dvou nebo více aplikací v celé organizaci po úspěšném testování. Můžete přizpůsobit interakci s uživatelem aplikace a postupy pro zřizování prostředků se můžou změnit, aby se vešly Automatické zřizování.

Můžete zrušit zřízení služby nebo součásti. Například zrušení zřízení účtu znamená, že účet je odstraněn z prostředku.

Hybridní model zřizování prostředků kombinuje přístupy k žádostem a na základě rolí, které podporuje služba Azure AD. V případě podmnožiny zaměstnanců nebo spravovaných systémů může společnost chtít automatizovat přístup pomocí přiřazení na základě rolí. Společnost může také zpracovat všechny ostatní žádosti o přístup nebo výjimky prostřednictvím modelu založeného na požadavku. Některé firmy můžou začít s ručním přiřazením a vyvíjet se k hybridnímu modelu s cílem plně založeného nasazení na základě role v budoucnu.

Jiné společnosti mohou najít nepraktické pro obchodní důvody, aby bylo možné zajistit kompletní zřizování na základě rolí a cílit na hybridní přístup jako na požadovaný cíl. I nadále je možné, že ostatní společnosti splní jenom zřizování na základě požadavků a nechtějí investovat další úsilí k definování a správě automatizovaných zásad zřizování na základě rolí.

## <a name="license-management"></a>Správa licencí
Správa licencí na základě skupin ve službě Azure AD umožňuje správcům přiřazovat uživatele do skupiny zabezpečení a služba Azure AD automaticky přiřazuje licence všem členům skupiny. Pokud je uživatel následně přidán do skupiny nebo je z něj odebrán, licence bude podle potřeby automaticky přiřazena nebo odebrána.

Můžete použít skupiny, které synchronizujete z místní služby AD nebo spravovat ve službě Azure AD. Párování této služby se službou Azure AD Premium Self-Service Správa skupin vám umožní snadno delegovat přiřazení licencí na příslušné tvůrce rozhodnutí. Můžete si být jisti, že problémy, jako jsou konflikty licencí a chybějící data umístění, se automaticky odřadí.

## <a name="self-regulating-user-administration"></a>Správa uživatelů na základě vlastních regulačních prostředí
Když vaše organizace začne zřizovat prostředky ve všech vnitřních organizacích, implementujete možnost správy uživatelů samy na sebe. Můžete využít výhod a výhod zřizování uživatelů napříč hranicemi organizace. V tomto prostředí se změna stavu uživatele automaticky odráží v přístupových právech napříč hranicemi organizace a geografickými oblastmi. Můžete snížit náklady na zřizování a zjednodušit procesy přístupu a schvalování. Implementace si uvědomuje plný potenciál implementace řízení přístupu na základě rolí pro komplexní správu přístupu ve vaší organizaci. Pomocí automatizovaných postupů pro řízení zřizování uživatelů můžete snížit náklady na správu. Zabezpečení můžete zlepšit automatizací vynucování zásad zabezpečení a zjednodušením a centralizacim správy životního cyklu uživatelů a zřizováním prostředků pro velké uživatelské populace.

> [!NOTE]
> Další informace najdete v tématu Nastavení služby Azure AD pro samoobslužné řízení přístupu k aplikacím.
> 
> 

Služba Azure AD založená na licencích (založená na oprávněních) funguje na základě Aktivace předplatného v tenantovi adresáře nebo služby Azure AD. Po aktivním předplatném můžou být možnosti služby spravované správci adresářů a služeb a využíváni licencovanými uživateli. 

## <a name="integration-with-other-3rd-party-providers"></a>Integrace s dalšími poskytovateli třetích stran

Azure Active Directory poskytuje jednotné přihlašování a vylepšené zabezpečení přístupu k tisícům aplikací SaaS a místních webových aplikací. Další informace najdete v tématu [Integrace aplikací s Azure Active Directory](../develop/quickstart-register-app.md) .

## <a name="define-synchronization-management"></a>Definování správy synchronizace
Integrace místních adresářů se službou Azure AD zvyšuje produktivitu uživatelů tím, že jim poskytuje společnou identitu pro přístup ke cloudovým i místním prostředkům. Díky této integraci můžou uživatelé a organizace využít tyto výhody:

* Organizace můžou uživatelům poskytnout společnou hybridní identitu napříč místními nebo cloudovou službou, která využívá Windows Server Active Directory a připojuje se k Azure Active Directory.
* Správci můžou poskytnout podmíněný přístup na základě prostředků aplikace, identity zařízení a uživatele, síťového umístění a vícefaktorového ověřování.
* Uživatelé můžou využít své společné identity prostřednictvím účtů ve službě Azure AD, aby Microsoft 365, Intune, aplikace SaaS a aplikace třetích stran.
* Vývojáři můžou vytvářet aplikace, které využívají model Common identity a integrují aplikace do služby Active Directory v místním prostředí nebo Azure pro cloudové aplikace.

Následující obrázek obsahuje příklad vysokého zobrazení procesu synchronizace identit.

![Sync](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Proces Synchronizace identity

V následující tabulce najdete porovnání možností synchronizace:

| Možnost správy synchronizace | Výhody | Nevýhody |
| --- | --- | --- |
| Synchronizace založená na synchronizaci (přes DirSync nebo AADConnect) |Uživatelé a skupiny synchronizované z místního prostředí a cloudu <br>  **Řízení zásad** : zásady účtů je možné nastavit prostřednictvím služby Active Directory, která správci umožní spravovat zásady hesel, pracovní stanice, omezení, ovládací prvky pro zamčení a další, aniž by bylo potřeba provádět další úkoly v cloudu.  <br>  **Řízení přístupu** : může omezit přístup ke cloudové službě, aby k nim bylo možné přistupovat prostřednictvím podnikového prostředí, prostřednictvím online serverů nebo obou. <br>  Omezená volání podpory: Pokud uživatelé mají méně hesel, než se zapamatují, jsou jejich méně pravděpodobně zapomenout. <br>  Zabezpečení: identity a informace uživatelů jsou chráněné, protože všechny servery a služby používané při jednotném přihlašování jsou zaregistrované a řízené místně. <br>  Podpora pro silné ověřování: ke cloudové službě můžete použít silné ověřování (označované také jako dvojúrovňové ověřování). Pokud však používáte silné ověřování, je nutné použít jednotné přihlašování. | |
| Založené na federaci (prostřednictvím AD FS) |Povoluje služba tokenů zabezpečení (STS). Když nakonfigurujete službu STS tak, aby poskytovala přístup s jednotným přihlašováním přes cloudovou službu Microsoftu, budete vytvářet federované vztahy důvěryhodnosti mezi vaší místní službou STS a federované doménou, kterou jste zadali v tenantovi Azure AD. <br> Umožňuje koncovým uživatelům používat stejnou sadu přihlašovacích údajů pro získání přístupu k více prostředkům. <br>koncoví uživatelé nemusejí udržovat více sad přihlašovacích údajů. Uživatelé si zatím musí zadat své přihlašovací údaje do každého z nich podporovaných prostředků. jsou podporované scénáře B2B a B2C. |Vyžaduje specializované pracovníky pro nasazení a údržbu vyhrazených místních AD FS serverů. Pokud plánujete použít AD FS pro službu STS, existují omezení používání silného ověřování. Další informace najdete v tématu [Konfigurace pokročilých možností pro AD FS 2,0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh237448(v=ws.10)). |

> [!NOTE]
> Další informace najdete v tématu [Integrace místních identit pomocí Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Viz také
[Přehled otázek návrhu](plan-hybrid-identity-design-considerations-overview.md)